---
title: 复制和转换 Azure Database for MySQL 中的数据
description: 了解如何使用 Azure 数据工厂或 Synapse Analytics 管道来复制和转换 Azure Database for MySQL 中的数据。
titleSuffix: Azure Data Factory & Azure Synapse
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: b9f5f5046e3c03ec0ee7057553b49ca26f18061c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124761861"
---
# <a name="copy-and-transform-data-in-azure-database-for-mysql-using-azure-data-factory-or-synapse-analytics"></a>使用 Azure 数据工厂或 Synapse Analytics 来复制和转换 Azure Database for MySQL 中的数据

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述了如何使用 Azure 数据工厂或 Synapse Analytics 管道中的复制活动从/向 Azure Database for MySQL 复制数据，以及如何使用数据流转换 Azure Database for MySQL 中的数据。 有关详细信息，请阅读 [Azure 数据工厂](introduction.md)和 [Synapse Analytics](../synapse-analytics/overview-what-is.md) 的简介文章。

此连接器专用于 [Azure Database for MySQL 服务](../mysql/overview.md)。 若要从位于本地或云中的通用 MySQL 数据库复制数据，请使用 [MySQL 连接器](connector-mysql.md)。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 Azure Database for MySQL 连接器：

- 带有[支持的源或接收器矩阵](copy-activity-overview.md)的[复制活动](copy-activity-overview.md)
- [映射数据流](concepts-data-flow-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)

## <a name="getting-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-azure-database-for-mysql-using-ui"></a>使用 UI 创建到 Azure Database for MySQL 的链接服务

使用以下步骤在 Azure 门户 UI 中创建一个到 Azure Database for MySQL 的链接服务。

1. 浏览到 Azure 数据工厂或 Synapse 工作区中的“管理”选项卡并选择“链接服务”，然后单击“新建”：

    # <a name="azure-data-factory"></a>[Azure 数据工厂](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="使用 Azure 数据工厂 UI 创建新的链接服务。":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="使用 Azure Synapse UI 创建新的链接服务。":::

2. 搜索“MySQL”，然后选择“Azure Database for MySQL 连接器”。

   :::image type="content" source="media/connector-azure-database-for-mysql/azure-database-for-mysql-connector.png" alt-text="选择“Azure Database for MySQL 连接器”。":::    


1. 配置服务详细信息、测试连接并创建新的链接服务。

   :::image type="content" source="media/connector-azure-database-for-mysql/configure-azure-database-for-mysql-linked-service.png" alt-text="配置到 Azure Database for MySQL 的链接服务。":::

## <a name="connector-configuration-details"></a>连接器配置详细信息

对于特定于 Azure Database for MySQL 连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Azure Database for MySQL 链接服务支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | type 属性必须设置为：**AzureMySql** | 是 |
| connectionString | 指定连接到 Azure Database for MySQL 实例所需的连接信息。 <br/> 还可以将密码放在 Azure 密钥保管库中，并从连接字符串中拉取 `password` 配置。 有关更多详细信息，请参阅以下示例和[在 Azure 密钥保管库中存储凭据](store-credentials-in-key-vault.md)一文。 | 是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果数据存储位于专用网络，则可以使用 Azure Integration Runtime 或自承载集成运行时。 如果未指定，则使用默认 Azure Integration Runtime。 |否 |

典型的连接字符串为 `Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;PWD=<password>`。 你可以根据自己的情况设置更多属性：

| 属性 | 说明 | 选项 | 必选 |
|:--- |:--- |:--- |:--- |
| SSLMode | 此选项指定驱动程序在连接到 MySQL 时是否使用 TLS 加密和验证。 例如 `SSLMode=<0/1/2/3/4>`| DISABLED (0) / PREFERRED (1) **(Default)** / REQUIRED (2) / VERIFY_CA (3) / VERIFY_IDENTITY (4) | 否 |
| UseSystemTrustStore | 此选项指定是使用系统信任存储中的 CA 证书还是使用指定 PEM 文件中的 CA 证书。 例如 `UseSystemTrustStore=<0/1>;`| Enabled (1) / Disabled (0) **(Default)** | 否 |

**示例：**

```json
{
    "name": "AzureDatabaseForMySQLLinkedService",
    "properties": {
        "type": "AzureMySql",
        "typeProperties": {
            "connectionString": "Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;PWD=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**示例：在 Azure 密钥保管库中存储密码**

```json
{
    "name": "AzureDatabaseForMySQLLinkedService",
    "properties": {
        "type": "AzureMySql",
        "typeProperties": {
            "connectionString": "Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供 Azure Database for MySQL 数据集支持的属性列表。

要从 Azure Database for MySQL 复制数据，请将数据集的 type 属性设置为 **AzureMySqlTable**。 支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为：**AzureMySqlTable** | 是 |
| tableName | MySQL 数据库中的表名。 | 否（如果指定了活动源中的“query”） |

**示例**

```json
{
    "name": "AzureMySQLDataset",
    "properties": {
        "type": "AzureMySqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure MySQL linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 Azure Database for MySQL 源和接收器支持的属性列表。

### <a name="azure-database-for-mysql-as-source"></a>Azure Database for MySQL 用作源

若要从 Azure Database for MySQL 复制数据，复制活动的 **source** 节需要支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 复制活动 source 的 type 属性必须设置为：**AzureMySqlSource** | 是 |
| query | 使用自定义 SQL 查询读取数据。 例如：`"SELECT * FROM MyTable"`。 | 否（如果指定了数据集中的“tableName”） |
| queryCommandTimeout | 查询请求超时前的等待时间。默认值为 120 分钟 (02:00:00) | 否 |

**示例：**

```json
"activities":[
    {
        "name": "CopyFromAzureDatabaseForMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure MySQL input dataset name>",
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
                "type": "AzureMySqlSource",
                "query": "<custom query e.g. SELECT * FROM MyTable>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-database-for-mysql-as-sink"></a>Azure Database for MySQL 作为接收器

将数据复制到 Azure Database for MySQL 时，复制活动的 **sink** 节支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 复制活动接收器的 type 属性必须设置为：**AzureMySqlSink** | 是 |
| preCopyScript | 每次运行时将数据写入 Azure Database for MySQL 之前，为要执行的复制活动指定 SQL 查询。 可以使用此属性清除预加载的数据。 | 否 |
| writeBatchSize | 当缓冲区大小达到 writeBatchSize 时，会将数据插入 Azure Database for MySQL 表。<br>允许的值为 integer（表示行数）。 | 否（默认值为 10,000） |
| writeBatchTimeout | 超时之前等待批插入操作完成时的等待时间。<br>允许的值为 Timespan。 示例为 00:30:00（30 分钟）。 | 否（默认值为 00:00:30） |

**示例：**

```json
"activities":[
    {
        "name": "CopyToAzureDatabaseForMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure MySQL output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureMySqlSink",
                "preCopyScript": "<custom SQL script>",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="mapping-data-flow-properties"></a>映射数据流属性

在转换映射数据流中的数据时，可以从 Azure Database for MySQL 读取表，并且可以将数据写入表。 有关详细信息，请参阅映射数据流中的[源转换](data-flow-source.md)和[接收器转换](data-flow-sink.md)。 可以选择使用 Azure Database for MySQL 数据集或[内联数据集](data-flow-source.md#inline-datasets)作为源和接收器类型。

### <a name="source-transformation"></a>源转换

下表列出了 Azure Database for MySQL 源支持的属性。 你可以在“源选项”选项卡中编辑这些属性。

| 名称 | 说明 | 必需 | 允许的值 | 数据流脚本属性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 表 | 如果你选择“表”作为输入，则数据流会从数据集中指定的表提取所有数据。 | 否 | - |（仅适用于内联数据集）<br>tableName |
| 查询 | 如果你选择“查询”作为输入，请指定一个用来从源提取数据的 SQL 查询，这将替代在数据集中指定的任何表。 使用查询是一个好方法，它可以减少用于测试或查找的行数。<br><br>不支持 Order By 子句，但你可以设置完整的 SELECT FROM 语句。 还可以使用用户定义的表函数。 select * from udfGetData() 是 SQL 中的一个 UDF，它返回你可以在数据流中使用的表。<br>查询示例：`select * from mytable where customerId > 1000 and customerId < 2000` 或 `select * from "MyTable"`。| 否 | 字符串 | query |
| 批大小 | 指定批大小，以将大型数据分成多个批。 | 否 | Integer | batchSize |
| 隔离级别 | 选择下列隔离级别之一：<br>- 读取已提交的内容<br>- 读取未提交的内容（默认）<br>- 可重复的读取<br>- 可序列化<br>- 无（忽略隔离级别） | 否 | <small>READ_COMMITTED<br/>READ_UNCOMMITTED<br/>REPEATABLE_READ<br/>SERIALIZABLE<br/>NONE</small> |isolationLevel |

#### <a name="azure-database-for-mysql-source-script-example"></a>Azure Database for MySQL 源脚本示例

在使用 Azure Database for MySQL 作为源类型时，关联的数据流脚本为：

```
source(allowSchemaDrift: true,
    validateSchema: false,
    isolationLevel: 'READ_UNCOMMITTED',
    query: 'select * from mytable',
    format: 'query') ~> AzureMySQLSource
```

### <a name="sink-transformation"></a>接收器转换

下表列出了 Azure Database for MySQL 接收器支持的属性。 可以在“接收器选项”选项卡中编辑这些属性。

| 名称 | 说明 | 必需 | 允许的值 | 数据流脚本属性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Update 方法 | 指定数据库目标上允许哪些操作。 默认设置为仅允许插入。<br>若要更新、更新插入或删除行，需要进行[“更改行”转换](data-flow-alter-row.md)才能标记这些操作的行。 | 是 | `true` 或 `false` | deletable <br/>insertable <br/>updateable <br/>upsertable |
| 键列 | 对于更新、更新插入和删除操作，必须设置键列来确定要更改的行。<br>后续的更新、更新插入和删除将使用你选取为密钥的列名称。 因此，你必须选取存在于接收器映射中的列。 | 否 | Array | 密钥 |
| 跳过写入键列 | 如果你不希望将值写入到键列，请选择“跳过写入键列”。 | 否 | `true` 或 `false` | skipKeyWrites |
| 表操作 |确定在写入之前是否从目标表重新创建或删除所有行。<br>- **无**：不会对表进行任何操作。<br>- 重新创建：将删除表并重新创建表。 如果以动态方式创建表，则是必需的。<br>- 截断：将删除目标表中的所有行。 | 否 | `true` 或 `false` | recreate<br/>truncate |
| 批大小 | 指定每批中写入的行数。 较大的批大小可提高压缩比并改进内存优化，但在缓存数据时可能会导致内存不足异常。 | 否 | Integer | batchSize |
| 预处理和后处理 SQL 脚本 | 指定在将数据写入接收器数据库之前（预处理）和之后（后处理）会执行的多行 SQL 脚本。 | 否 | 字符串 | preSQLs<br>postSQLs |

#### <a name="azure-database-for-mysql-sink-script-example"></a>Azure Database for MySQL 接收器脚本示例

在使用 Azure Database for MySQL 作为接收器类型时，关联的数据流脚本为：

```
IncomingStream sink(allowSchemaDrift: true,
    validateSchema: false,
    deletable:false,
    insertable:true,
    updateable:true,
    upsertable:true,
    keys:['keyColumn'],
    format: 'table',
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> AzureMySQLSink
```

## <a name="lookup-activity-properties"></a>查找活动属性

若要了解有关属性的详细信息，请查看 [Lookup 活动](control-flow-lookup-activity.md)。

## <a name="data-type-mapping-for-azure-database-for-mysql"></a>Azure Database for MySQL 的数据类型映射

从 Azure Database for MySQL 复制数据时，以下映射用于从 MySQL 数据类型映射到服务内部使用的临时数据类型。 若要了解复制活动如何将源架构和数据类型映射到接收器，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

| Azure Database for MySQL 数据类型 | 临时服务数据类型 |
|:--- |:--- |
| `bigint` |`Int64` |
| `bigint unsigned` |`Decimal` |
| `bit` |`Boolean` |
| `bit(M), M>1`|`Byte[]`|
| `blob` |`Byte[]` |
| `bool` |`Int16` |
| `char` |`String` |
| `date` |`Datetime` |
| `datetime` |`Datetime` |
| `decimal` |`Decimal, String` |
| `double` |`Double` |
| `double precision` |`Double` |
| `enum` |`String` |
| `float` |`Single` |
| `int` |`Int32` |
| `int unsigned` |`Int64`|
| `integer` |`Int32` |
| `integer unsigned` |`Int64` |
| `long varbinary` |`Byte[]` |
| `long varchar` |`String` |
| `longblob` |`Byte[]` |
| `longtext` |`String` |
| `mediumblob` |`Byte[]` |
| `mediumint` |`Int32` |
| `mediumint unsigned` |`Int64` |
| `mediumtext` |`String` |
| `numeric` |`Decimal` |
| `real` |`Double` |
| `set` |`String` |
| `smallint` |`Int16` |
| `smallint unsigned` |`Int32` |
| `text` |`String` |
| `time` |`TimeSpan` |
| `timestamp` |`Datetime` |
| `tinyblob` |`Byte[]` |
| `tinyint` |`Int16` |
| `tinyint unsigned` |`Int16` |
| `tinytext` |`String` |
| `varchar` |`String` |
| `year` |`Int32` |

## <a name="next-steps"></a>后续步骤
有关复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
