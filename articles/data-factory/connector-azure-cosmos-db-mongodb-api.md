---
title: 从 Azure Cosmos DB 的 API for MongoDB 复制数据
description: 了解如何使用 Azure 数据工厂或 Synapse Analytics 管道将数据从受支持的源数据存储复制到受支持的接收器存储或从 Azure Cosmos DB 的用于 MongoDB 的 API 复制到受支持的接收器存储。
titleSuffix: Azure Data Factory & Azure Synapse
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 6720bcfdd4e0ce804bfd15803e1ed186d94e5181
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124762073"
---
# <a name="copy-data-to-or-from-azure-cosmos-dbs-api-for-mongodb-using-azure-data-factory-or-synapse-analytics"></a>使用 Azure 数据工厂或 Synapse Analytics 向/从 Azure Cosmos DB 的用于 MongoDB 的 API 复制数据

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何使用 Azure 数据工厂和 Synapse Analytics 管道中的复制活动从/向 Azure Cosmos DB 的用于 MongoDB 的 API 复制数据。 本文是基于概括性介绍复制活动的[复制活动](copy-activity-overview.md)一文编写的。

>[!NOTE]
>此连接器仅支持向/从 Azure Cosmos DB 的用于 MongoDB 的 API 复制数据。 有关 SQL API，请参阅 [Cosmos DB SQL API 连接器](connector-azure-cosmos-db.md)。 目前不支持其他 API 类型。

## <a name="supported-capabilities"></a>支持的功能

可将 Azure Cosmos DB 的用于 MongoDB 的 API 中的数据复制到任一受支持的接收器数据存储，或将数据从任一受支持的源数据存储复制到 Azure Cosmos DB 的用于 MongoDB 的 API。 有关复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储和格式](copy-activity-overview.md#supported-data-stores-and-formats)。

可以将 Azure Cosmos DB 的用于 MongoDB 的 API 连接器用于：

- 将数据复制到 [ Azure Cosmos DB 的用于 MongoDB 的 API](../cosmos-db/mongodb-introduction.md)。
- 以 **insert** 或 **upsert** 的形式写入 Azure Cosmos DB。
- 按原样导入和导出 JSON 文档，或在表格数据集中复制或粘贴数据。 示例包括 SQL 数据库和 CSV 文件。 若要在 JSON 文件或另一个 Azure Cosmos DB 集合中按原样复制或粘贴文档，请参阅“导入或导出 JSON 文档”。

## <a name="get-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-azure-cosmos-dbs-api-for-mongodb-using-ui"></a>使用 UI 创建到 Azure Cosmos DB API for MongoDB 的链接服务

使用以下步骤在 Azure 门户 UI 中创建到 Azure Cosmos DB API for MongoDB 的链接服务。

1. 浏览到 Azure 数据工厂或 Synapse 工作区中的“管理”选项卡并选择“链接服务”，然后单击“新建”：

    # <a name="azure-data-factory"></a>[Azure 数据工厂](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="使用 Azure 数据工厂 UI 创建新的链接服务。":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="使用 Azure Synapse UI 创建新的链接服务。":::

2. 搜索“Cosmos”并选择“Azure Cosmos DB API for MongoDB 连接器”。

    :::image type="content" source="media/connector-azure-cosmos-db-mongodb-api/azure-cosmos-db-mongodb-api-connector.png" alt-text="选择“Azure Cosmos DB API for MongoDB 连接器”。":::    

1. 配置服务详细信息、测试连接并创建新的链接服务。

    :::image type="content" source="media/connector-azure-cosmos-db-mongodb-api/configure-azure-cosmos-db-mongodb-api-linked-service.png" alt-text="配置到 Azure Cosmos DB API for MongoDB 的链接服务。":::

## <a name="connector-configuration-details"></a>连接器配置详细信息

对于特定于 Azure Cosmos DB 的用于 MongoDB 的 API 的数据工厂实体，以下部分提供了有关可用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Azure Cosmos DB 的用于 MongoDB 的 API 链接服务支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | **type** 属性必须设置为 **CosmosDbMongoDbApi**。 | 是 |
| connectionString |指定 Azure Cosmos DB 的用于 MongoDB 的 API 的连接字符串。 可以在 Azure 门户 -> Cosmos DB 边栏选项卡 -> 主要或辅助连接字符串中找到该字符串。 <br/>对于 3.2 服务器版本，字符串模式为 `mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb`。 <br/>对于 3.6+ 服务器版本，字符串模式为 `mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.mongo.cosmos.azure.com:10255/?ssl=true&replicaSet=globaldb&retrywrites=false&maxIdleTimeMS=120000&appName=@<cosmosdb-name>@`。<br/><br />还可以将密码放在 Azure 密钥保管库中，并从连接字符串中拉取 `password` 配置。 有关更多详细信息，请参阅[在 Azure Key Vault 中存储凭据](store-credentials-in-key-vault.md)。|是 |
| database | 要访问的数据库的名称。 | 是 |
| isServerVersionAbove32 | 指定服务器版本是否高于 3.2。 允许的值为“true”和“false”（默认值）。 这将确定要在服务中使用的驱动程序。 | 是 |
| connectVia | 用于连接到数据存储的 [ Integration Runtime](concepts-integration-runtime.md)。 可使用 Azure Integration Runtime 或自承载集成运行时（如果数据存储位于专用网络）。 如果未指定此属性，则使用默认的 Azure Integration Runtime。 |否 |

**示例**

```json
{
    "name": "CosmosDbMongoDBAPILinkedService",
    "properties": {
        "type": "CosmosDbMongoDbApi",
        "typeProperties": {
            "connectionString": "mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb",
            "database": "myDatabase",
            "isServerVersionAbove32": "false"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集和链接服务](concepts-datasets-linked-services.md)。 Azure Cosmos DB 的用于 MongoDB 的 API 数据集支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 数据集的 **type** 属性必须设置为 **CosmosDbMongoDbApiCollection**。 |是 |
| collectionName |Azure Cosmos DB 集合的名称。 |是 |

**示例**

```json
{
    "name": "CosmosDbMongoDBAPIDataset",
    "properties": {
        "type": "CosmosDbMongoDbApiCollection",
        "typeProperties": {
            "collectionName": "<collection name>"
        },
        "schema": [],
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB's API for MongoDB linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

本部分列出 Azure Cosmos DB 的用于 MongoDB 的 API 源和接收器支持的属性。

有关可用于定义活动的各个部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)。

### <a name="azure-cosmos-dbs-api-for-mongodb-as-source"></a>Azure Cosmos DB 的用于 MongoDB 的 API 作为源

复制活动 **source** 节支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 复制活动源的 **type** 属性必须设置为 **CosmosDbMongoDbApiSource**。 |是 |
| filter | 使用查询运算符指定选择筛选器。 若要返回集合中的所有文档，请省略此参数或传递空文档 ({})。 | 否 |
| cursorMethods.project | 指定要在文档中返回用于投影的字段。 若要返回匹配文档中的所有字段，请省略此参数。 | 否 |
| cursorMethods.sort | 指定查询返回匹配文档的顺序。 请参阅 [cursor.sort()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort)。 | 否 |
| cursorMethods.limit |    指定服务器返回的文档的最大数量。 请参阅 [cursor.limit()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit)。  | 否 | 
| cursorMethods.skip | 指定要跳过的文档数量以及 MongoDB 开始返回结果的位置。 请参阅 [cursor.skip()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip)。 | 否 |
| batchSize | 指定从 MongoDB 实例的每批响应中返回的文档数量。 大多数情况下，修改批大小不会影响用户或应用程序。 Cosmos DB 限制每个批不能超过 40 MB（这是文档大小的 batchSize 数量的总和），因此如果文档很大，请减小此值。 | 否<br/>（默认值为 **100**） |

>[!TIP]
>ADF 支持在 **严格模式** 下使用 BSON 文档。 请确保筛选器查询处于严格模式，而不是 Shell 模式。 有关详细说明，请参阅 [MongoDB 手册](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html)。

**示例**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBMongoDBAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Cosmos DB's API for MongoDB input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "CosmosDbMongoDbApiSource",
                "filter": "{datetimeData: {$gte: ISODate(\"2018-12-11T00:00:00.000Z\"),$lt: ISODate(\"2018-12-12T00:00:00.000Z\")}, _id: ObjectId(\"5acd7c3d0000000000000000\") }",
                "cursorMethods": {
                    "project": "{ _id : 1, name : 1, age: 1, datetimeData: 1 }",
                    "sort": "{ age : 1 }",
                    "skip": 3,
                    "limit": 3
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-cosmos-dbs-api-for-mongodb-as-sink"></a>Azure Cosmos DB 的用于 MongoDB 的 API 作为链接

复制活动 **sink** 节支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 复制活动接收器的 **type** 属性必须设置为 **CosmosDbMongoDbApiSink**。 |是 |
| writeBehavior |描述如何将数据写入 Azure Cosmos DB。 允许的值为 **insert** 和 **upsert**。<br/><br/>**upsert** 的行为是，如果已存在具有相同 `_id` 的文档，则替换该文档；否则将插入该文档。<br /><br />注意：如果未在原始文档中指定或未通过列映射指定 `_id`，则服务会自动为文档生成 `_id`。 这表示必须先确保文档有 ID，才能让 **upsert** 按预期工作。 |否<br />（默认值为 **insert**） |
| writeBatchSize | **writeBatchSize** 属性控制每个批中可写入的文档大小。 可尝试增大 **writeBatchSize** 的值以提高性能，并在文档大小较大时减小该值。 |否<br />（默认值为 **10,000**） |
| writeBatchTimeout | 超时前等待批插入操作完成的时间。允许的值为 timespan。 | 否<br/>（默认值为 **00:30:00** - 30 分钟） |

>[!TIP]
>若要按原样导入 JSON 文档，请参阅[导入或导出 JSON 文档](#import-and-export-json-documents)部分；若要从表格形数据复制，请参阅[架构映射](#schema-mapping)。

**示例**

```json
"activities":[
    {
        "name": "CopyToCosmosDBMongoDBAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Document DB output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "CosmosDbMongoDbApiSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

## <a name="import-and-export-json-documents"></a>导入和导出 JSON 文档

使用此 Azure Cosmos DB 连接器，可以轻松地：

* 在两个 Azure Cosmos DB 集合之间按原样复制文档。
* 将各种源（包括 MongoDB、Azure Blob 存储、Azure Data Lake Store 和服务所支持的其他基于文件的存储）中的 JSON 文档导入 Azure Cosmos DB。
* 将 JSON 文档从 Azure Cosmos DB 集合导出到各种基于文件的存储。

若要实现“架构不可知”复制，请执行以下操作：

* 使用复制数据工具时，选择“原样导出到 JSON 文件或 Cosmos DB 集合”选项。
* 使用活动创作时，请为源或接收器选择 JSON 格式以及相应的文件存储。

## <a name="schema-mapping"></a>架构映射

要将数据从 Azure Cosmos DB 的用于 MongoDB 的 API 复制到表格接收器或进行反向复制，请参阅[架构映射](copy-activity-schema-and-type-mapping.md#schema-mapping)。

具体而言，在写入到 Cosmos DB 时，为了确保使用源数据中的正确对象 ID 填充 Cosmos DB（例如，SQL 数据库表中包含一个“id”列，你想要使用该列的值作为 MongoDB 中的文档 ID 以完成插入/更新插入操作），需要根据 MongoDB 严格模式定义 (`_id.$oid`) 设置适当的架构映射，如下所示：

:::image type="content" source="./media/connector-azure-cosmos-db-mongodb-api/map-id-in-mongodb-sink.png" alt-text="MongoDB 接收器中的映射 ID":::

完成复制活动的执行后，接收器中将生成以下 BSON ObjectId：

```json
{
    "_id": ObjectId("592e07800000000000000000")
}
``` 

## <a name="next-steps"></a>后续步骤

有关复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
