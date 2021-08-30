---
title: 使用 Kafka Connect for Azure Cosmos DB 读写数据
description: Kafka Connect for Azure Cosmos DB 是一种连接器，用于从 Azure Cosmos DB 读写数据。 Kafka Connect 是一项工具，用于在 Apache Kafka 和其他系统之间以可缩放且可靠的方式流式传输数据
author: kushagrathapar
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 06/28/2021
ms.author: kuthapar
ms.openlocfilehash: 56cdedda37e51685a21701c00657cb337d836467
ms.sourcegitcommit: d2738669a74cda866fd8647cb9c0735602642939
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2021
ms.locfileid: "113657050"
---
# <a name="kafka-connect-for-azure-cosmos-db"></a>Kafka Connect for Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

[Kafka Connect](http://kafka.apache.org/documentation.html#connect) 是一项工具，用于在 Apache Kafka 和其他系统之间以可缩放且可靠的方式流式传输数据。 使用 Kafka Connect 可以定义将大型数据集移入和移出 Kafka 的连接器。 Kafka Connect for Azure Cosmos DB 是一种连接器，用于从 Azure Cosmos DB 读写数据。

## <a name="source--sink-connectors-semantics"></a>源连接器和接收器连接器语义

* 源连接器 - 目前，对于多任务，此连接器支持“至少一次”语义；对于单任务，支持“恰好一次”语义。

* 接收器连接器 - 此连接器完全支持“恰好一次”语义。

## <a name="supported-data-formats"></a>受支持的数据格式

源连接器和接收器连接器可以配置为支持以下数据格式：

| 格式 | 说明 |
| :----------- | :---------- |
| 纯 JSON  | 没有任何附加架构的 JSON 记录结构。 |
| 包含架构的 JSON | 包含显式架构信息的 JSON 记录结构，以确保数据与预期格式匹配。 |
| AVRO | 在 Apache 的 Hadoop 项目中开发的面向行的远程过程调用和数据序列化框架。 它使用 JSON 来定义数据类型、协议，并采用紧凑的二进制格式来序列化数据。

可以在 Kafka 中单独配置键和值设置，包括格式和序列化。 因此，可以分别对键和值使用不同的数据格式。 为了适应不同的数据格式，`key.converter` 和 `value.converter` 都提供了转换器配置。

## <a name="converter-configuration-examples"></a>转换器配置示例

### <a name="plain-json"></a><a id="json-plain"></a>纯 JSON

如果需要使用不带架构注册表的 JSON 来连接数据，请使用 Kafka 支持的 `JsonConverter`。 以下示例显示了添加到配置的 `JsonConverter` 键和值属性：

  ```java
  key.converter=org.apache.kafka.connect.json.JsonConverter
  key.converter.schemas.enable=false
  value.converter=org.apache.kafka.connect.json.JsonConverter
  value.converter.schemas.enable=false
  ```

### <a name="json-with-schema"></a><a id="json-with-schema"></a>包含架构的 JSON

将属性 `key.converter.schemas.enable` 和 `value.converter.schemas.enable` 设置为 true，以便将键或值视为同时包含内部架构和数据的复合 JSON 对象。 如果没有这些属性，键或值将被视为纯 JSON。 

  ```java
  key.converter=org.apache.kafka.connect.json.JsonConverter
  key.converter.schemas.enable=true
  value.converter=org.apache.kafka.connect.json.JsonConverter
  value.converter.schemas.enable=true
  ```

发送到 Kafka 的结果消息如以下示例所示，其中架构和有效负载是 JSON 中的顶层元素：

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
      ],
      "optional": false,
      "name": "ksql.users"
    },
    "payload": {
      "userid": 123,
      "name": "user's name"
    }
  }
  ```

> [!NOTE]
> 写入 Azure Cosmos DB 的消息由架构和有效负载组成。 请注意消息的大小，以及它所包含的有效负载与架构的比例。 写入到 Kafka 的每条消息中都会重复此架构。 在此类场景中，可能需要使用 JSON 架构或 AVRO 等序列化格式，其中架构单独进行存储，消息仅保存有效负载。

### <a name="avro"></a><a id="avro"></a>AVRO

Kafka 连接器支持 AVRO 数据格式。 若要使用 AVRO 格式，请配置 `AvroConverter`，以便 Kafka Connect 了解如何处理 AVRO 数据。 Azure Cosmos DB Kafka Connect 已在 Apache 2.0 许可下使用 Confluent 提供的 [AvroConverter](https://www.confluent.io/hub/confluentinc/kafka-connect-avro-converter) 进行了测试。 你也可以根据需要使用不同的自定义转换器。

Kafka 单独处理键和值。 在辅助角色配置中根据需要指定 `key.converter` 和 `value.converter` 属性。 使用 `AvroConverter` 时，添加一个额外的转换器属性，该属性提供架构注册表的 URL。 以下示例显示了添加到配置的 AvroConverter 键和值属性：

  ```java
  key.converter=io.confluent.connect.avro.AvroConverter
  key.converter.schema.registry.url=http://schema-registry:8081
  value.converter=io.confluent.connect.avro.AvroConverter
  value.converter.schema.registry.url=http://schema-registry:8081
  ```

## <a name="choose-a-conversion-format"></a>选择转换格式

下面是有关如何选择转换格式的一些注意事项：

* 在配置源连接器时：

  * 如果希望 Kafka Connect 在其写入 Kafka 的消息中包含纯 JSON，请设置[纯 JSON](#json-plain) 配置。

  * 如果希望 Kafka Connect 在其写入 Kafka 的消息中包含架构，请设置[包含架构的 JSON](#json-with-schema) 配置。

  * 如果希望 Kafka Connect 在其写入 Kafka 的消息中包含 AVRO 格式，请设置 [AVRO](#avro) 配置。

* 如果要将 Kafka 主题中的 JSON 数据用于接收器连接器，请了解在将 JSON 写入 Kafka 主题时如何对该 JSON 进行序列化：

  * 如果它是使用 JSON 序列化程序编写的，请将 Kafka Connect 设置为使用 JSON 转换器 `(org.apache.kafka.connect.json.JsonConverter)`。

    * 如果 JSON 数据是以普通字符串形式编写的，请确定该数据是否包含嵌套的架构或有效负载。 如果是，请设置[包含架构的 JSON](#json-with-schema) 配置。
    * 但是，如果使用的 JSON 数据没有架构或有效负载构造，则必须通过按照[纯 JSON](#json-plain) 配置设置 `schemas.enable=false` 来告知 Kafka Connect 不要查找架构。

  * 如果它是使用 AVRO 序列化程序编写的，请按照 [AVRO](#avro) 配置将 Kafka Connect 设置为使用 AVRO 转换器 `(io.confluent.connect.avro.AvroConverter)`。

## <a name="configuration"></a>配置

### <a name="common-configuration-properties"></a>常见配置属性

源连接器和接收器连接器共享以下常见配置属性：

| 名称 | 类型 | 说明 | 必需/可选 |
| :--- | :--- | :--- | :--- |
| connect.cosmos.connection.endpoint | uri | Cosmos 终结点 URI 字符串 | 必需 |
| connect.cosmos.master.key | string | 接收器在连接时使用的 Azure Cosmos DB 主密钥。 | 必需 |
| connect.cosmos.databasename | string | 接收器写入到的 Azure Cosmos 数据库的名称。 | 必需 |
| connect.cosmos.containers.topicmap | string | Kafka 主题与 Azure Cosmos DB 容器之间的映射。 其格式使用 CSV 设置为 `topic#container,topic2#container2` | 必需 |

有关接收器连接器特定的配置，请参阅[接收器连接器文档](kafka-connector-sink.md)

有关源连接器特定的配置，请参阅[源连接器文档](kafka-connector-source.md)

## <a name="common-configuration-errors"></a>常见配置错误

如果 Kafka Connect 中的转换器配置有误，则可能会导致错误。 这些错误将显示在 Kafka 连接器接收器上，因为你将尝试反序列化已存储在 Kafka 中的消息。 源连接器中通常不会发生转换器问题，因为序列化在源连接器中进行设置。

有关详细信息，请参阅[常见配置错误](https://www.confluent.io/blog/kafka-connect-deep-dive-converters-serialization-explained/#common-errors)文档。

## <a name="project-setup"></a>项目设置

有关初始设置说明，请参阅[开发人员演练和项目设置](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/Developer_Walkthrough.md)。

## <a name="performance-testing"></a>性能测试

有关为接收器连接器和源连接器运行的性能测试的详细信息，请参阅[性能测试文档](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/Performance_Testing.md)。

有关为连接器部署性能测试环境的确切步骤，请参阅[性能环境设置](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/src/perf/README.md)。

## <a name="resources"></a>资源

* [Kafka Connect](http://kafka.apache.org/documentation.html#connect)
* [Kafka Connect 深入探讨 - 转换器和序列化介绍](https://www.confluent.io/blog/kafka-connect-deep-dive-converters-serialization-explained/)

## <a name="next-steps"></a>后续步骤

* Kafka Connect for Azure Cosmos DB [源连接器](kafka-connector-source.md)
* Kafka Connect for Azure Cosmos DB [接收器连接器](kafka-connector-sink.md)