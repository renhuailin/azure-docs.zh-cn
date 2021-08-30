---
title: Kafka Connect for Azure Cosmos DB - 源连接器
description: Azure Cosmos DB 源连接器提供从 Azure Cosmos DB 更改源读取数据并将这些数据发布到 Kafka 主题的功能。 Kafka Connect for Azure Cosmos DB 是用于从/向 Azure Cosmos DB 读取/写入数据的连接器。
author: kushagrathapar
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 06/28/2021
ms.author: kuthapar
ms.openlocfilehash: b28a9c9cf59df3647c5f2bc8f278f9eb00c2edbb
ms.sourcegitcommit: d2738669a74cda866fd8647cb9c0735602642939
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2021
ms.locfileid: "113657059"
---
# <a name="kafka-connect-for-azure-cosmos-db---source-connector"></a>Kafka Connect for Azure Cosmos DB - 源连接器
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Kafka Connect for Azure Cosmos DB 是用于从/向 Azure Cosmos DB 读取/写入数据的连接器。 Azure Cosmos DB 源连接器提供从 Azure Cosmos DB 更改源读取数据并将这些数据发布到 Kafka 主题的功能。

## <a name="prerequisites"></a>先决条件

* 请从 [Confluent 平台设置](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/Confluent_Platform_Setup.md)开始，因为完成此过程可以提供一个可用的完整环境。 如果你不想要使用 Confluent 平台，则需要自行安装并配置 Zookeeper、Apache Kafka 和 Kafka Connect。 还需要手动安装并配置 Azure Cosmos DB 连接器。
* 按照容器[设置指南](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/CosmosDB_Setup.md)创建一个 Azure Cosmos DB 帐户
* 已使用 WSL2 在 GitHub Codespaces、Mac、Ubuntu 和 Windows 上测试过的 Bash shell。 此 shell 在 Cloud Shell 或 WSL1 中无法正常工作。
* 下载 [Java 11+](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html)
* 下载 [Maven](https://maven.apache.org/download.cgi)

## <a name="install-the-source-connector"></a>安装源连接器

如果使用的是建议的 [Confluent 平台设置](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/Confluent_Platform_Setup.md)，则 Azure Cosmos DB 源连接器已包含在安装中，因此可以跳过此步骤。

否则，可以使用最新[版本](https://github.com/microsoft/kafka-connect-cosmosdb/releases)中的 JAR 文件，并手动安装连接器。 有关详细信息，请参阅这些[说明](https://docs.confluent.io/current/connect/managing/install.html#install-connector-manually)。 还可以从源代码打包新的 JAR 文件：

```bash
# clone the kafka-connect-cosmosdb repo if you haven't done so already
git clone https://github.com/microsoft/kafka-connect-cosmosdb.git
cd kafka-connect-cosmosdb

# package the source code into a JAR file
mvn clean package

# include the following JAR file in Confluent Platform installation
ls target/*dependencies.jar
```

## <a name="create-a-kafka-topic"></a>创建 Kafka 主题

使用 Confluent 控制中心创建 Kafka 主题。 对于此方案，我们将创建一个名为“apparels”的 Kafka 主题，并将非架构嵌入式 JSON 数据写入该主题。 若要在控制中心内创建主题，请参阅[创建 Kafka 主题文档](https://docs.confluent.io/platform/current/quickstart/ce-docker-quickstart.html#step-2-create-ak-topics)。

## <a name="create-the-source-connector"></a>创建源连接器

### <a name="create-the-source-connector-in-kafka-connect"></a>在 Kafka Connect 中创建源连接器

若要在 Kafka Connect 中创建 Azure Cosmos DB 源连接器，请使用以下 JSON 配置。确保替换 `connect.cosmos.connection.endpoint` 和 `connect.cosmos.master.key` 属性的占位符值（在学习先决条件部分所述的 Azure Cosmos DB 设置指南时应已保存所需的值）。

```json
{
  "name": "cosmosdb-source-connector",
  "config": {
    "connector.class": "com.azure.cosmos.kafka.connect.source.CosmosDBSourceConnector",
    "tasks.max": "1",
    "key.converter": "org.apache.kafka.connect.json.JsonConverter",
    "value.converter": "org.apache.kafka.connect.json.JsonConverter",
    "connect.cosmos.task.poll.interval": "100",
    "connect.cosmos.connection.endpoint": "https://<cosmosinstance-name>.documents.azure.com:443/",
    "connect.cosmos.master.key": "<cosmosdbprimarykey>",
    "connect.cosmos.databasename": "kafkaconnect",
    "connect.cosmos.containers.topicmap": "apparels#kafka",
    "connect.cosmos.offset.useLatest": false,
    "value.converter.schemas.enable": "false",
    "key.converter.schemas.enable": "false"
  }
}
```

有关上述每个配置属性的详细信息，请参阅[源属性](#source-configuration-properties)部分。 填写所有值后，将 JSON 文件保存在本地的某个位置。 可以使用此文件通过 REST API 创建连接器。

#### <a name="create-connector-using-control-center"></a>使用控制中心创建连接器

创建连接器的简单方法是使用 Confluent 控制中心门户。 请按照 [Confluent 设置指南](https://docs.confluent.io/platform/current/quickstart/ce-docker-quickstart.html#step-3-install-a-ak-connector-and-generate-sample-data)从控制中心创建连接器。 设置时，请不要使用 `DatagenConnector` 选项，而应改用 `CosmosDBSourceConnector` 磁贴。 配置源连接器时，请填写在 JSON 文件中填充的值。

或者，也可以在“连接器”页中，使用“上传连接器配置文件”选项上传在前一部分生成的 JSON 文件。

:::image type="content" source="./media/kafka-connector-source/upload-connector-config.png" alt-text="上传连接器配置。":::

#### <a name="create-connector-using-rest-api"></a>使用 REST API 创建连接器

使用“连接”REST API 创建源连接器

```bash
# Curl to Kafka connect service
curl -H "Content-Type: application/json" -X POST -d @<path-to-JSON-config-file> http://localhost:8083/connectors
```

## <a name="insert-document-into-azure-cosmos-db"></a>将文档插入 Azure Cosmos DB

1. 登录到 [Azure 门户](https://portal.azure.com/learn.docs.microsoft.com)并导航到你的 Azure Cosmos DB 帐户。
1. 打开“数据资源管理器”选项卡并选择“数据库” 
1. 打开前面创建的“kafkaconnect”数据库和“kafka”容器。
1. 若要创建新的 JSON 文档，请在“SQL API”窗格中展开“kafka”容器，选择“项”，然后在工具栏中选择“新建项” 。
1. 现在，将文档添加到采用以下结构的容器。 将以下示例 JSON 块粘贴到“项”选项卡中，并覆盖当前内容：

   ``` json
 
   {
     "id": "2",
     "productId": "33218897",
     "category": "Women's Outerwear",
     "manufacturer": "Contoso",
     "description": "Black wool pea-coat",
     "price": "49.99",
     "shipping": {
       "weight": 2,
       "dimensions": {
         "width": 8,
         "height": 11,
         "depth": 3
       }
     }
   }
 
   ```

1. 选择“保存”  。
1. 查看左侧菜单中的“项”，确认文档已保存。

### <a name="confirm-data-written-to-kafka-topic"></a>确认数据已写入 Kafka 主题

1. 通过 `<http://localhost:9000>` 打开 Kafka 主题 UI。
1. 选择创建的 Kafka“apparels”主题。
1. 检查前面已插入到 Azure Cosmos DB 的文档是否显示在该 Kafka 主题中。

### <a name="cleanup"></a>清理

若要在 Confluent 控制中心删除连接器，请导航到创建的源连接器，然后选择“删除”图标。

:::image type="content" source="./media/kafka-connector-source/delete-source-connector.png" alt-text="从 Confluent 中心删除连接器":::

或者，使用连接器的 REST API：

```bash
# Curl to Kafka connect service
curl -X DELETE http://localhost:8083/connectors/cosmosdb-source-connector
```

若要使用 Azure CLI 删除创建的 Azure Cosmos DB 服务及其资源组，请参阅这些[步骤](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/CosmosDB_Setup.md#cleanup)。

## <a name="source-configuration-properties"></a>源配置属性

以下设置用于配置 Kafka 源连接器。 这些配置值确定了要使用哪个 Azure Cosmos DB 容器、要从哪些 Kafka 主题写入数据，以及用于序列化数据的格式。 有关使用默认值的示例，请参阅此[配置文件](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/src/docker/resources/source.example.json)。

| 名称 | 类型 | 说明 | 必需/可选 |
| :--- | :--- | :--- | :--- |
| connector.class | 字符串 | Azure Cosmos DB 源的类名。 它应设置为 `com.azure.cosmos.kafka.connect.source.CosmosDBSourceConnector` | 必需 |
| connect.cosmos.databasename | 字符串 | 要从中读取数据的数据库的名称。 | 必需 |
| connect.cosmos.master.key | 字符串 | Azure Cosmos DB 主密钥。 | 必需 |
| connect.cosmos.connection.endpoint | URI | 帐户终结点。 | 必需 |
| connect.cosmos.containers.topicmap | 字符串 | 主题到容器的映射的逗号分隔列表。 例如，topic1#coll1, topic2#coll2 | 必需 |
| connect.cosmos.messagekey.enabled | 布尔 | 此值表示是否应设置 Kafka 消息键。 默认值为 `true` | 必需 |
| connect.cosmos.messagekey.field | 字符串 | 使用文档中的字段值作为消息键。 默认值为 `id`。 | 必需 |
| connect.cosmos.offset.useLatest | 布尔 |  设置为 `true` 会使用最近的源偏移量。 设置为 `false` 会使用最早记录的偏移量。 默认值是 `false`。 | 必需 |
| connect.cosmos.task.poll.interval | int | 在更改源容器中轮询更改的间隔。 | 必需 |
| key.converter | 字符串 | 写入到 Kafka 主题的键数据的序列化格式。 | 必需 |
| value.converter | 字符串 | 写入到 Kafka 主题的值数据的序列化格式。 | 必需 |
| key.converter.schemas.enable | 字符串 | 如果键数据采用嵌入式架构，则设置为 `true`。 | 可选 |
| value.converter.schemas.enable | 字符串 | 如果键数据采用嵌入式架构，则设置为 `true`。 | 可选 |
| tasks.max | int | 连接器源任务的最大数目。 默认值是 `1`。 | 可选 |

## <a name="supported-data-types"></a>支持的数据类型

Azure Cosmos DB 源连接器将 JSON 文档转换为架构，支持以下 JSON 数据类型：

| JSON 数据类型 | 架构类型 |
| :--- | :--- |
| Array | Array |
| 布尔 | 布尔 | 
| 数字 | Float32<br>Float64<br>Int8<br>Int16<br>Int32<br>Int64|
| null | 字符串 |
| 对象 (JSON)| 结构|
| 字符串 | String |

## <a name="next-steps"></a>后续步骤

* Kafka Connect for Azure Cosmos DB [接收器连接器](kafka-connector-sink.md)