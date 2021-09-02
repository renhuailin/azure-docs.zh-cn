---
title: Kafka Connect for Azure Cosmos DB - 接收器连接器
description: 使用 Azure Cosmos DB 接收器连接器可将 Apache Kafka 主题中的数据导出到 Azure Cosmos DB 数据库。 该连接器基于主题订阅从 Kafka 中轮询数据，以将其写入到数据库中的容器。
author: kushagrathapar
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 06/28/2021
ms.author: kuthapar
ms.openlocfilehash: fa0fc899453c11858d518ed5f224b6eb94e218f4
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123113636"
---
# <a name="kafka-connect-for-azure-cosmos-db---sink-connector"></a>Kafka Connect for Azure Cosmos DB - 接收器连接器
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Kafka Connect for Azure Cosmos DB 是用于从/向 Azure Cosmos DB 读取/写入数据的连接器。 使用 Azure Cosmos DB 接收器连接器可将 Apache Kafka 主题中的数据导出到 Azure Cosmos DB 数据库。 该连接器基于主题订阅从 Kafka 中轮询数据，以将其写入到数据库中的容器。

## <a name="prerequisites"></a>先决条件

* 请从 [Confluent 平台设置](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/Confluent_Platform_Setup.md)开始，因为完成此过程可以提供一个可用的完整环境。 如果你不想要使用 Confluent 平台，则需要自行安装并配置 Zookeeper、Apache Kafka 和 Kafka Connect。 还需要手动安装并配置 Azure Cosmos DB 连接器。
* 按照容器[设置指南](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/CosmosDB_Setup.md)创建一个 Azure Cosmos DB 帐户
* 已使用 WSL2 在 GitHub Codespaces、Mac、Ubuntu 和 Windows 上测试过的 Bash shell。 此 shell 在 Cloud Shell 或 WSL1 中无法正常工作。
* 下载 [Java 11+](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html)
* 下载 [Maven](https://maven.apache.org/download.cgi)

## <a name="install-sink-connector"></a>安装接收器连接器

如果使用的是建议的 [Confluent 平台设置](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/Confluent_Platform_Setup.md)，则 Azure Cosmos DB 接收器连接器已包含在安装中，因此可以跳过此步骤。  

否则，可以从最新[版本](https://github.com/microsoft/kafka-connect-cosmosdb/releases)下载 JAR 文件，或者打包此存储库以创建新的 JAR 文件。 若要使用 JAR 文件手动安装连接器，请参阅这些[说明](https://docs.confluent.io/current/connect/managing/install.html#install-connector-manually)。 还可以从源代码打包新的 JAR 文件。

```bash
# clone the kafka-connect-cosmosdb repo if you haven't done so already
git clone https://github.com/microsoft/kafka-connect-cosmosdb.git
cd kafka-connect-cosmosdb

# package the source code into a JAR file
mvn clean package

# include the following JAR file in Kafka Connect installation
ls target/*dependencies.jar
```

## <a name="create-a-kafka-topic-and-write-data"></a>创建 Kafka 主题并写入数据

如果使用的是 Confluent 平台，则创建 Kafka 主题的最简单方法是使用提供的控制中心 UX。 否则，可以使用以下语法手动创建 Kafka 主题：

```bash
./kafka-topics.sh --create --zookeeper <ZOOKEEPER_URL:PORT> --replication-factor <NO_OF_REPLICATIONS> --partitions <NO_OF_PARTITIONS> --topic <TOPIC_NAME>
```

对于此方案，我们将创建一个名为“hotels”的 Kafka 主题，并将非架构嵌入式 JSON 数据写入该主题。 若要在控制中心创建主题，请参阅 [Confluent 指南](https://docs.confluent.io/platform/current/quickstart/ce-docker-quickstart.html#step-2-create-ak-topics)。

接下来，启动 Kafka 控制台生成器以将一些记录写入“hotels”主题。

```powershell
# Option 1: If using Codespaces, use the built-in CLI utility
kafka-console-producer --broker-list localhost:9092 --topic hotels

# Option 2: Using this repo's Confluent Platform setup, first exec into the broker container
docker exec -it broker /bin/bash
kafka-console-producer --broker-list localhost:9092 --topic hotels

# Option 3: Using your Confluent Platform setup and CLI install
<path-to-confluent>/bin/kafka-console-producer --broker-list <kafka broker hostname> --topic hotels
```

在控制台生成器中，输入：

```json
{"id": "h1", "HotelName": "Marriott", "Description": "Marriott description"}
{"id": "h2", "HotelName": "HolidayInn", "Description": "HolidayInn description"}
{"id": "h3", "HotelName": "Motel8", "Description": "Motel8 description"}
```

输入的三条记录将以 JSON 格式发布到“hotels”Kafka 主题。

## <a name="create-the-sink-connector"></a>创建接收器连接器

在 Kafka Connect 中创建 Azure Cosmos DB 接收器连接器。 以下 JSON 正文定义接收器连接器的配置。 确保替换 `connect.cosmos.connection.endpoint` 和 `connect.cosmos.master.key`（在学习先决条件部分所述的 Azure Cosmos DB 设置指南时应已保存这些属性）的值。

有关其中每个配置属性的详细信息，请参阅[接收器属性](#sink-configuration-properties)部分。

```json
{
  "name": "cosmosdb-sink-connector",
  "config": {
    "connector.class": "com.azure.cosmos.kafka.connect.sink.CosmosDBSinkConnector",
    "tasks.max": "1",
    "topics": [
      "hotels"
    ],
    "value.converter": "org.apache.kafka.connect.json.JsonConverter",
    "value.converter.schemas.enable": "false",
    "key.converter": "org.apache.kafka.connect.json.JsonConverter",
    "key.converter.schemas.enable": "false",
    "connect.cosmos.connection.endpoint": "https://<cosmosinstance-name>.documents.azure.com:443/",
    "connect.cosmos.master.key": "<cosmosdbprimarykey>",
    "connect.cosmos.databasename": "kafkaconnect",
    "connect.cosmos.containers.topicmap": "hotels#kafka"
  }
}
```

填写所有值后，将 JSON 文件保存在本地的某个位置。 可以使用此文件通过 REST API 创建连接器。

### <a name="create-connector-using-control-center"></a>使用控制中心创建连接器

在控制中心网页上可以轻松创建连接器。 请按照此[安装指南](https://docs.confluent.io/platform/current/quickstart/ce-docker-quickstart.html#step-3-install-a-ak-connector-and-generate-sample-data)通过控制中心创建连接器。 请不要使用 `DatagenConnector` 选项，而应改用 `CosmosDBSinkConnector` 磁贴。 配置接收器连接器时，请填写已在 JSON 文件中填充的值。

或者，也可以在“连接器”页中，使用“上传连接器配置文件”选项上传前面创建的 JSON 文件。

:::image type="content" source="./media/kafka-connector-sink/upload-connector-config.png" alt-text="上传连接器配置。":::

### <a name="create-connector-using-rest-api"></a>使用 REST API 创建连接器

使用“连接”REST API 创建接收器连接器：

```bash
# Curl to Kafka connect service
curl -H "Content-Type: application/json" -X POST -d @<path-to-JSON-config-file> http://localhost:8083/connectors

```

## <a name="confirm-data-written-to-cosmos-db"></a>确认数据已写入到 Cosmos DB

登录到 [Azure 门户](https://portal.azure.com/learn.docs.microsoft.com)并导航到你的 Azure Cosmos DB 帐户。 检查该帐户中是否创建了来自“hotels”主题的三条记录。

## <a name="cleanup"></a>清理

若要在控制中心删除连接器，请导航到创建的接收器连接器，然后单击“删除”图标。

:::image type="content" source="./media/kafka-connector-sink/delete-connector.png" alt-text="删除连接器。":::

或者，可以使用“连接”REST API 删除连接器：

```bash
# Curl to Kafka connect service
curl -X DELETE http://localhost:8083/connectors/cosmosdb-sink-connector
```

若要使用 Azure CLI 删除创建的 Azure Cosmos DB 服务及其资源组，请参阅这些[步骤](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/CosmosDB_Setup.md#cleanup)。

## <a name="sink-configuration-properties"></a><a id="sink-configuration-properties"></a>接收器配置属性

以下设置用于配置 Cosmos DB Kafka 接收器连接器。 这些配置值决定了要使用哪些 Kafka 主题数据、哪个 Azure Cosmos DB 容器的数据将写入到其中，以及用于序列化数据的格式。 有关包含默认值的示例配置文件，请参阅[此配置]( https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/src/docker/resources/sink.example.json)。

| 名称 | 类型 | 说明 | 必需/可选 |
| :--- | :--- | :--- | :--- |
| 主题 | list | 要监视的 Kafka 主题列表。 | 必需 |
| connector.class | string | Azure Cosmos DB 接收器的类名。 它应设置为 `com.azure.cosmos.kafka.connect.sink.CosmosDBSinkConnector`。 | 必需 |
| connect.cosmos.connection.endpoint | uri | Azure Cosmos 终结点 URI 字符串。 | 必需 |
| connect.cosmos.master.key | string | 接收器在连接时使用的 Azure Cosmos DB 主密钥。 | 必需 |
| connect.cosmos.databasename | string | 接收器写入到的 Azure Cosmos 数据库的名称。 | 必需 |
| connect.cosmos.containers.topicmap | string | Kafka 主题与 Azure Cosmos DB 容器之间的映射，使用如下所示的 CSV 格式：`topic#container,topic2#container2`。 | 必需 |
| key.converter | string | 写入到 Kafka 主题的键数据的序列化格式。 | 必需 |
| value.converter | string | 写入到 Kafka 主题的值数据的序列化格式。 | 必需 |
| key.converter.schemas.enable | string | 如果键数据采用嵌入式架构，则此属性设置为“true”。 | 可选 |
| value.converter.schemas.enable | string | 如果键数据采用嵌入式架构，则此属性设置为“true”。 | 可选 |
| tasks.max | int | 连接器接收器任务的最大数目。 默认为 `1` | 可选 |

数据始终以不采用任何架构的 JSON 格式写入到 Azure Cosmos DB。

## <a name="supported-data-types"></a>支持的数据类型

Azure Cosmos DB 接收器连接器将接收器记录转换为支持以下架构类型的 JSON 文档：

| 架构类型 | JSON 数据类型 |
| :--- | :--- |
| Array | Array |
| 布尔 | 布尔 |
| Float32 | Number |
| Float64 | Number |
| Int8 | Number |
| Int16 | Number |
| Int32 | Number |
| Int64 | Number|
| 映射 | 对象 (JSON)|
| 字符串 | 字符串<br> Null |
| 结构 | 对象 (JSON) |

接收器连接器还支持以下 AVRO 逻辑类型：

| 架构类型 | JSON 数据类型 |
| :--- | :--- |
| Date | Number |
| 时间 | Number |
| 时间戳 | Number |

> [!NOTE]
> Azure Cosmos DB 接收器连接器目前不支持字节反序列化。

## <a name="single-message-transformssmt"></a>单一消息转换 (SMT)

除了接收器连接器设置以外，还可以指定使用单一消息转换 (SMT) 来修改流经 Kafka Connect 平台的消息。 有关详细信息，请参阅 [Confluent SMT 文档](https://docs.confluent.io/platform/current/connect/transforms/overview.html)。

### <a name="using-the-insertuuid-smt"></a>使用 InsertUUID SMT

可以使用 InsertUUID SMT 来自动添加项 ID。 使用自定义 `InsertUUID` SMT，可以在每条消息写入到 Azure Cosmos DB 之前，为其插入包含随机 UUID 值的 `id` 字段。

> [!WARNING]
> 仅当消息不包含 `id` 字段时，才使用此 SMT。 否则将覆盖 `id` 值，并且数据库中最终可能会出现重复项。 使用 UUID 作为消息 ID 可能既快速又简单，但 UUID 不是要在 Azure Cosmos DB 中使用的[理想分区键](https://stackoverflow.com/questions/49031461/would-using-a-substring-of-a-guid-in-cosmosdb-as-partitionkey-be-a-bad-idea)。

### <a name="install-the-smt"></a>安装 SMT

在可以使用 `InsertUUID` SMT 之前，需要在 Confluent 平台安装程序中安装此转换。 如果使用的是此存储库中的 Confluent 平台安装程序，则此转换已包含在安装中，因此可以跳过此步骤。

或者，可以打包 [InsertUUID 源](https://github.com/confluentinc/kafka-connect-insert-uuid)以创建新的 JAR 文件。 若要使用 JAR 文件手动安装连接器，请参阅这些[说明](https://docs.confluent.io/current/connect/managing/install.html#install-connector-manually)。

```bash
# clone the kafka-connect-insert-uuid repo
https://github.com/confluentinc/kafka-connect-insert-uuid.git
cd kafka-connect-insert-uuid

# package the source code into a JAR file
mvn clean package

# include the following JAR file in Confluent Platform installation
ls target/*.jar
```

### <a name="configure-the-smt"></a>配置 SMT

在接收器连接器配置中，添加以下属性以设置 `id`。

```json
"transforms": "insertID",
"transforms.insertID.type": "com.github.cjmatta.kafka.connect.smt.InsertUuid$Value",
"transforms.insertID.uuid.field.name": "id"
```

有关使用此 SMT 的详细信息，请参阅 [InsertUUID 存储库](https://github.com/confluentinc/kafka-connect-insert-uuid)。

### <a name="using-smts-to-configure-time-to-live-ttl"></a>使用 SMT 配置生存时间 (TTL)

使用 `InsertField` 和 `Cast` SMT 可对 Azure Cosmos DB 中创建的每个项配置 TTL。 在项级别启用 TTL 之前，请对容器启用 TTL。 有关详细信息，请参阅[生存时间](how-to-time-to-live.md#enable-time-to-live-on-a-container-using-azure-portal)文档。

在接收器连接器配置中，添加以下属性以设置 TTL（为秒为单位）。 在以下示例中，TTL 设置为 100 秒。 如果消息已包含 `TTL` 字段，则 `TTL` 值将由这些 SMT 覆盖。

```json
"transforms": "insertTTL,castTTLInt",
"transforms.insertTTL.type": "org.apache.kafka.connect.transforms.InsertField$Value",
"transforms.insertTTL.static.field": "ttl",
"transforms.insertTTL.static.value": "100",
"transforms.castTTLInt.type": "org.apache.kafka.connect.transforms.Cast$Value",
"transforms.castTTLInt.spec": "ttl:int32"
```

有关使用这些 SMT 的详细信息，请参阅 [InsertField](https://docs.confluent.io/platform/current/connect/transforms/insertfield.html) 和[强制转换](https://docs.confluent.io/platform/current/connect/transforms/cast.html)文档。

## <a name="troubleshooting-common-issues"></a>排查常见问题

下面是使用 Kafka 接收器连接器时可能会遇到的一些常见问题的解决方法。

### <a name="read-non-json-data-with-jsonconverter"></a>使用 JsonConverter 读取非 JSON 数据

如果 Kafka 中的源主题包含非 JSON 数据，而你尝试使用 `JsonConverter` 读取这些数据，则会出现以下异常：

```console
org.apache.kafka.connect.errors.DataException: Converting byte[] to Kafka Connect data failed due to serialization error:
…
org.apache.kafka.common.errors.SerializationException: java.io.CharConversionException: Invalid UTF-32 character 0x1cfa7e2 (above 0x0010ffff) at char #1, byte #7)

```

发生此错误的原因可能是，源主题中的数据是以 Avro 或其他格式（例如 CSV 字符串）序列化的。

解决方法：如果主题数据采用 AVRO 格式，请将 Kafka Connect 接收器连接器更改为使用 `AvroConverter`，如下所示。

```json
"value.converter": "io.confluent.connect.avro.AvroConverter",
"value.converter.schema.registry.url": "http://schema-registry:8081",
```

### <a name="read-non-avro-data-with-avroconverter"></a>使用 AvroConverter 读取非 Avro 数据

此方案适用于使用 Avro 转换器从主题中读取非 Avro 格式的数据的情况。 这些数据包括除 Confluent 架构注册表的 Avro 序列化程序（具有其自己的传输格式）以外的 Avro 序列化程序写入的数据。

```console
org.apache.kafka.connect.errors.DataException: my-topic-name
at io.confluent.connect.avro.AvroConverter.toConnectData(AvroConverter.java:97)
…
org.apache.kafka.common.errors.SerializationException: Error deserializing Avro message for id -1
org.apache.kafka.common.errors.SerializationException: Unknown magic byte!

```

解决方案：检查源主题的序列化格式。 然后，将 Kafka Connect 的接收器连接器切换以使用适当的转换器，或者将上游格式切换为 Avro。

### <a name="read-a-json-message-without-the-expected-schemapayload-structure"></a>读取不带有预期架构/有效负载结构的 JSON 消息

Kafka Connect 支持包含有效负载和架构的特殊 JSON 消息结构，如下所示。

 ```json
{
  "schema": {
    "type": "struct",
    "fields": [
      {
        "type": "int32",
        "optional": false,
        "field": "userid"
      },
      {
        "type": "string",
        "optional": false,
        "field": "name"
      }
    ]
  },
  "payload": {
    "userid": 123,
    "name": "Sam"
  }
}
```

如果你尝试读取不包含采用此结构的数据的 JSON 数据，将收到以下错误：

```none
org.apache.kafka.connect.errors.DataException: JsonConverter with schemas.enable requires "schema" and "payload" fields and may not contain additional fields. If you are trying to deserialize plain JSON data, set schemas.enable=false in your converter configuration.
```

确切地说，对于 `schemas.enable=true`，唯一有效的 JSON 结构包含架构和有效负载字段作为顶级元素，如上所示。 如错误消息所述，如果你只有普通的 JSON 数据，则应将连接器的配置更改为：

```json
"value.converter": "org.apache.kafka.connect.json.JsonConverter",
"value.converter.schemas.enable": "false",
```

## <a name="limitations"></a>限制

* 不支持在 Azure Cosmos DB 中自动创建数据库和容器。 数据库和容器必须已存在，并且必须已正确配置。

## <a name="next-steps"></a>后续步骤

可通过以下文档来详细了解 Azure Cosmo DB 中的更改源：

* [更改源简介](https://azurecosmosdb.github.io/labs/dotnet/labs/08-change_feed_with_azure_functions.html)
* [从更改源中读取](read-change-feed.md)