---
title: 从 MongoDB 复制数据或将数据复制到其中
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何通过在 Azure 数据工厂或 Synapse Analytics 管道中使用复制活动，将数据从 MongoDB 复制到支持的接收器数据存储，或者从支持的源数据存储复制到 MongoDB。
author: jianleishen
ms.author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 5642577cf8b8e1edf741c09bf4e1968d5cd69770
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124831720"
---
# <a name="copy-data-from-or-to-mongodb-using-azure-data-factory-or-synapse-analytics"></a>使用 Azure 数据工厂或 Synapse Analytics 从/向 MongoDB 复制数据

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何使用 Azure 数据工厂或 Synapse Analytics 管道中的复制活动从/向 MongoDB 数据库复制数据。 它是基于概述复制活动总体的[复制活动概述](copy-activity-overview.md)一文。

>[!IMPORTANT]
>新的 MongoDB 连接器提供改进的本机 MongoDB 支持。 如果在解决方案中使用了旧的 MongoDB 连接器，且该连接器仅“按原样”支持后向兼容性，请参阅 [MongoDB 连接器（旧版）](connector-mongodb-legacy.md)一文。


## <a name="supported-capabilities"></a>支持的功能

可以从 MongoDB 数据库将数据复制到任何受支持的接收器数据存储，或从任何受支持的源数据存储将数据复制到 MongoDB 数据库。 有关复制活动支持作为源/接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

具体而言，此 MongoDB 连接器最高支持版本 4.2。


## <a name="prerequisites"></a>先决条件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](includes/data-factory-v2-integration-runtime-requirements.md)]


## <a name="getting-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-mongodb-using-ui"></a>使用 UI 创建一个到 MongoDB 的链接服务

使用以下步骤在 Azure 门户 UI 中创建一个到 MongoDB 的链接服务。

1. 浏览到 Azure 数据工厂或 Synapse 工作区中的“管理”选项卡并选择“链接服务”，然后单击“新建”：

    # <a name="azure-data-factory"></a>[Azure 数据工厂](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="使用 Azure 数据工厂 UI 创建新的链接服务。":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="使用 Azure Synapse UI 创建新的链接服务。":::

2. 搜索 MongoDB 并选择 MongoDB 连接器。

    :::image type="content" source="media/connector-mongodb/mongodb-connector.png" alt-text="选择 MongoDB 连接器。":::    

1. 配置服务详细信息，测试连接，然后创建新的链接服务。

    :::image type="content" source="media/connector-mongodb/configure-mongodb-linked-service.png" alt-text="配置到 MongoDB 的链接服务。":::

## <a name="connector-configuration-details"></a>连接器配置详细信息

对于特定于 MongoDB 连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。


## <a name="linked-service-properties"></a>链接服务属性

MongoDB 链接的服务支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type |type 属性必须设置为：MongoDbV2  |是 |
| connectionString |指定 MongoDB 连接字符串，例如 `mongodb://[username:password@]host[:port][/[database][?options]]`。 请参阅 [MongoDB 连接字符串手册](https://docs.mongodb.com/manual/reference/connection-string/)获取详细信息。 <br/><br /> 还可以将连接字符串置于 Azure Key Vault 中。 有关更多详细信息，请参阅[在 Azure Key Vault 中存储凭据](store-credentials-in-key-vault.md)。 |是 |
| database | 要访问的数据库的名称。 | 是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 在[先决条件](#prerequisites)部分了解更多信息。 如果未指定，则使用默认 Azure Integration Runtime。 |否 |

**示例：**

```json
{
    "name": "MongoDBLinkedService",
    "properties": {
        "type": "MongoDbV2",
        "typeProperties": {
            "connectionString": "mongodb://[username:password@]host[:port][/[database][?options]]",
            "database": "myDatabase"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集和链接服务](concepts-datasets-linked-services.md)。 MongoDB 数据集支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为：MongoDbV2Collection  | 是 |
| collectionName |MongoDB 数据库中集合的名称。 |是 |

**示例：**

```json
{
    "name": "MongoDbDataset",
    "properties": {
        "type": "MongoDbV2Collection",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<MongoDB linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```


## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供了 MongoDB 源和接收器支持的属性的列表。

### <a name="mongodb-as-source"></a>以 MongoDB 作为源

复制活动 **source** 部分支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 复制活动 source 的 type 属性必须设置为：MongoDbV2Source  | 是 |
| filter | 使用查询运算符指定选择筛选器。 若要返回集合中的所有文档，请省略此参数或传递空文档 ({})。 | 否 |
| cursorMethods.project | 指定要在文档中返回用于投影的字段。 若要返回匹配文档中的所有字段，请省略此参数。 | 否 |
| cursorMethods.sort | 指定查询返回匹配文档的顺序。 请参阅 [cursor.sort()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort)。 | 否 |
| cursorMethods.limit | 指定服务器返回的文档的最大数量。 请参阅 [cursor.limit()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit)。  | 否 |
| cursorMethods.skip | 指定要跳过的文档数量以及 MongoDB 开始返回结果的位置。 请参阅 [cursor.skip()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip)。 | 否 |
| batchSize | 指定从 MongoDB 实例的每批响应中返回的文档数量。 大多数情况下，修改批大小不会影响用户或应用程序。 Cosmos DB 限制每个批不能超过 40 MB（这是文档大小的 batchSize 数量的总和），因此如果文档很大，请减小此值。 | 否<br/>（默认值为 **100**） |

>[!TIP]
>服务支持在严格模式下使用 BSON 文档。 请确保筛选器查询处于严格模式，而不是 Shell 模式。 有关详细说明，请参阅 [MongoDB 手册](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html)。

**示例：**

```json
"activities":[
    {
        "name": "CopyFromMongoDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MongoDB input dataset name>",
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
                "type": "MongoDbV2Source",
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

### <a name="mongodb-as-sink"></a>MongoDB 作为接收器

复制活动 **sink** 节支持以下属性：

| 属性 | 描述 | 必需 |
|:--- |:--- |:--- |
| type | 复制活动接收器的“type”属性必须设置为“MongoDbV2Sink” 。 |是 |
| writeBehavior |介绍如何将数据写入 MongoDB。 允许的值为 **insert** 和 **upsert**。<br/><br/>**upsert** 的行为是，如果已存在具有相同 `_id` 的文档，则替换该文档；否则将插入该文档。<br /><br />注意：如果未在原始文档中指定 `_id`，或未通过列映射指定 `_id`，则服务会自动为文档生成 _id。 这表示必须先确保文档有 ID，才能让 **upsert** 按预期工作。 |否<br />（默认值为 **insert**） |
| writeBatchSize | **writeBatchSize** 属性控制每个批中可写入的文档大小。 可尝试增大 **writeBatchSize** 的值以提高性能，并在文档大小较大时减小该值。 |否<br />（默认值为 **10,000**） |
| writeBatchTimeout | 超时前等待批插入操作完成的时间。允许的值为 timespan。 | 否<br/>（默认值为 **00:30:00** - 30 分钟） |

>[!TIP]
>若要按原样导入 JSON 文档，请参阅[导入或导出 JSON 文档](#import-and-export-json-documents)部分；若要从表格形数据复制，请参阅[架构映射](#schema-mapping)。

**示例**

```json
"activities":[
    {
        "name": "CopyToMongoDB",
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
                "type": "MongoDbV2Sink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

## <a name="import-and-export-json-documents"></a>导入和导出 JSON 文档

可以使用此 MongoDB 连接器轻松完成以下操作：

* 在两个 MongoDB 集合之间按原样复制文档。
* 将各种源（包括 Azure Cosmos DB、Azure Blob 存储、Azure Data Lake Store 和其他受支持的基于文件的存储）中的 JSON 文档导入 MongoDB。
* 将 JSON 文档从 MongoDB 集合导出到各种基于文件的存储。

若要实现这种架构不可知的复制，请跳过数据集中的“结构”（也称为“架构”）节和复制活动中的架构映射  。


## <a name="schema-mapping"></a>架构映射

若要将数据从 MongoDB 复制到表格接收器或进行反向复制，请参阅[架构映射](copy-activity-schema-and-type-mapping.md#schema-mapping)。


## <a name="next-steps"></a>后续步骤
有关复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
