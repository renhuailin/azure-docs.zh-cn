---
title: 复制和转换 Azure Database for PostgreSQL 中的数据
description: 了解如何使用 Azure 数据工厂复制和转换 Azure Database for PostgreSQL 中的数据。
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/08/2020
ms.openlocfilehash: 2537167783f3e68c52c665dafa9378193852acb4
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2020
ms.locfileid: "96930375"
---
# <a name="copy-and-transform-data-in-azure-database-for-postgresql-by-using-azure-data-factory"></a>使用 Azure 数据工厂复制和转换 Azure Database for PostgreSQL 中的数据

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何使用 Azure 数据工厂中的复制活动将数据从和复制到 Azure Database for PostgreSQL，并使用数据流转换 Azure Database for PostgreSQL 中的数据。 若要了解 Azure 数据工厂，请阅读[介绍性文章](introduction.md)。

此连接器专用于 [Azure Database for PostgreSQL 服务](../postgresql/overview.md)。 若要从位于本地或云中的通用 PostgreSQL 数据库复制数据，请使用 [PostgreSQL 连接器](connector-postgresql.md)。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 Azure Database for PostgreSQL 连接器：

- 带有[支持的源或接收器矩阵](copy-activity-overview.md)的[复制活动](copy-activity-overview.md)
- [映射数据流](concepts-data-flow-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)

## <a name="getting-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于 Azure Database for PostgreSQL 连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Azure Database for PostgreSQL 链接服务支持以下属性：

| Property | 说明 | 必选 |
|:--- |:--- |:--- |
| type | Type 属性必须设置为： **AzurePostgreSql**。 | 是 |
| connectionString | 用于连接到 Azure Database for PostgreSQL 的 ODBC 连接字符串。<br/>还可以将密码放在 Azure 密钥保管库中，并从连接字符串中拉取 `password` 配置。 有关更多详细信息，请参阅以下示例和[在 Azure 密钥保管库中存储凭据](store-credentials-in-key-vault.md)。 | 是 |
| connectVia | 此属性表示用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果数据存储位于专用网络，则可以使用 Azure Integration Runtime 或自承载集成运行时。 如果未指定，则使用默认 Azure Integration Runtime。 |否 |

典型的连接字符串为 `Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>`。 以下是你可以根据具体情况设置的更多属性：

| Property | 说明 | 选项 | 必选 |
|:--- |:--- |:--- |:--- |
| EncryptionMethod (EM)| 驱动程序用于加密在驱动程序和数据库服务器之间发送的数据的方法。 例如  `EncryptionMethod=<0/1/6>;`| 0 (No Encryption) **(Default)** / 1 (SSL) / 6 (RequestSSL) | 否 |
| ValidateServerCertificate (VSC) | 启用 SSL 加密后，确定驱动程序是否验证数据库服务器发送的证书（加密方法=1）。 例如  `ValidateServerCertificate=<0/1>;`| 0 (Disabled) **(Default)** / 1 (Enabled) | 否 |

**示例**：

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>"
        }
    }
}
```

**示例**：

**_将密码存储在 Azure Key Vault_* _

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        }
    }
}
```

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各个部分和属性的完整列表，请参阅 [Azure 数据工厂中的数据集](concepts-datasets-linked-services.md)。 本部分提供数据集中 Azure Database for PostgreSQL 支持的属性列表。

若要从 Azure Database for PostgreSQL 复制数据，请将数据集的 type 属性设置为 _ * AzurePostgreSqlTable * *。 支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为 AzurePostgreSqlTable | 是 |
| tableName | 表名称 | 否（如果指定了活动源中的“query”） |

**示例**：

```json
{
    "name": "AzurePostgreSqlDataset",
    "properties": {
        "type": "AzurePostgreSqlTable",
        "linkedServiceName": {
            "referenceName": "<AzurePostgreSql linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅 [Azure 数据工厂中的管道和活动](concepts-pipelines-activities.md)。 本部分提供 Azure Database for PostgreSQL 源支持的属性列表。

### <a name="azure-database-for-postgresql-as-source"></a>用于 PostgreSql 的 Azure 数据库作为源

要从 Azure Database for PostgreSQL 复制数据，请将复制活动中的源类型设置为 **AzurePostgreSqlSource**。 复制活动 **source** 部分支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为 **AzurePostgreSqlSource** | 是 |
| query | 使用自定义 SQL 查询读取数据。 例如 `SELECT * FROM mytable` 或 `SELECT * FROM "MyTable"`。 请注意，在 PostgreSQL 中，如果未加引号，则将实体名称视为不区分大小写。 | 否（如果指定了数据集中的 tableName 属性） |

**示例**：

```json
"activities":[
    {
        "name": "CopyFromAzurePostgreSql",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<AzurePostgreSql input dataset name>",
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
                "type": "AzurePostgreSqlSource",
                "query": "<custom query e.g. SELECT * FROM mytable>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-database-for-postgresql-as-sink"></a>Azure Database for PostgreSQL 作为接收器

将数据复制到 Azure Database for PostgreSQL 时，复制活动的 **sink** 节支持以下属性：

| Property | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动接收器的 type 属性必须设置为 **AzurePostgreSQLSink**。 | 是 |
| preCopyScript | 每次运行时将数据写入 Azure Database for PostgreSQL 之前，为要执行的复制活动指定 SQL 查询。 可以使用此属性清除预加载的数据。 | 否 |
| writeBatchSize | 当缓冲区大小达到 writeBatchSize 时，会将数据插入 Azure Database for PostgreSQL 表。<br>允许的值是表示行数的整数。 | 否（默认值为 10,000） |
| writeBatchTimeout | 超时之前等待批插入操作完成时的等待时间。<br>允许的值为 Timespan 字符串。 示例为 00:30:00（30 分钟）。 | 否（默认值为 00:00:30） |

**示例**：

```json
"activities":[
    {
        "name": "CopyToAzureDatabaseForPostgreSQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure PostgreSQL output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzurePostgreSQLSink",
                "preCopyScript": "<custom SQL script>",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="mapping-data-flow-properties"></a>映射数据流属性

在映射数据流中转换数据时，可以从 Azure Database for PostgreSQL 读取和写入表。 有关详细信息，请参阅映射数据流中的[源转换](data-flow-source.md)和[接收器转换](data-flow-sink.md)。 您可以选择使用 Azure Database for PostgreSQL 数据集或 [内联数据集](data-flow-source.md#inline-datasets) 作为源和接收器类型。

### <a name="source-transformation"></a>源转换

下表列出了 Azure Database for PostgreSQL 源支持的属性。 可以在 " **源选项** " 选项卡中编辑这些属性。

| 名称 | 说明 | 必选 | 允许的值 | 数据流脚本属性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 表 | 如果选择 "表" 作为输入，数据流将从数据集中指定的表中获取所有数据。 | 否 | - |*仅限内联数据集的 ()*<br>tableName |
| 查询 | 如果选择 "查询作为输入"，请指定一个 SQL 查询，以便从源中提取数据，这将覆盖在 dataset 中指定的任何表。 使用查询是减少测试或查找行的好办法。<br><br>不支持 **Order by** 子句，但可以设置完整的 SELECT FROM 语句。 还可以使用用户定义的表函数。 **select * From udfGetData ( # B1** 是 SQL 中的一个 UDF，它返回可以在数据流中使用的表。<br>查询示例： `select * from mytable where customerId > 1000 and customerId < 2000` 或 `select * from "MyTable"` 。 请注意，在 PostgreSQL 中，如果未加引号，则将实体名称视为不区分大小写。| 否 | 字符串 | query |
| 批大小 | 指定批大小，将大数据拆分为批处理。 | 否 | Integer | batchSize |
| 隔离级别 | 选择以下隔离级别之一：<br>-已提交读<br>-未提交 (默认) <br>-可重复读<br>-可序列化<br>-None (忽略隔离级别)  | 否 | <small>READ_COMMITTED<br/>READ_UNCOMMITTED<br/>REPEATABLE_READ<br/>SERIALIZABLE<br/>内容</small> |isolationLevel |

#### <a name="azure-database-for-postgresql-source-script-example"></a>Azure Database for PostgreSQL 源脚本示例

使用 Azure Database for PostgreSQL 作为源类型时，关联的数据流脚本为：

```
source(allowSchemaDrift: true,
    validateSchema: false,
    isolationLevel: 'READ_UNCOMMITTED',
    query: 'select * from mytable',
    format: 'query') ~> AzurePostgreSQLSource
```

### <a name="sink-transformation"></a>接收器转换

下表列出 Azure Database for PostgreSQL 接收器支持的属性。 可以在 " **接收器选项** " 选项卡中编辑这些属性。

| 名称 | 说明 | 必选 | 允许的值 | 数据流脚本属性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Update 方法 | 指定对数据库目标允许哪些操作。 默认设置为仅允许插入。<br>若要更新、upsert 或删除行，需要 [更改行转换](data-flow-alter-row.md) 以标记这些操作的行。 | 是 | `true` 或 `false` | 删除 <br/>可插入 <br/>更新 <br/>upsertable |
| 键列 | 对于更新，upsert 和 delete，键列 (s) 必须设置为确定要更改的行。<br>选取为密钥的列名称将用作后续更新 upsert （删除）的一部分。 因此，你必须选择存在于接收器映射中的列。 | 否 | Array | 密钥 |
| 跳过写入键列 | 如果您不希望将该值写入键列，请选择 "跳过写入键列"。 | 否 | `true` 或 `false` | skipKeyWrites |
| 表操作 |确定在写入之前是否在目标表中重新创建或删除所有行。<br>- **None**：不会对表执行任何操作。<br>- **重新创建**：该表将被删除并重新创建。 如果以动态方式创建表，则是必需的。<br>- **截断**：目标表中的所有行都将被删除。 | 否 | `true` 或 `false` | 重新创建<br/>truncate |
| 批大小 | 指定每个批次中写入的行数。 较大的批大小可提高压缩比并改进内存优化，但在缓存数据时可能会导致内存不足异常。 | 否 | Integer | batchSize |
| Pre 和 Post SQL 脚本 | 指定将在 (预处理) 之前和 (在将后处理) 数据写入接收器数据库之前执行的多行 SQL 脚本。 | 否 | 字符串 | preSQLs<br>postSQLs |

#### <a name="azure-database-for-postgresql-sink-script-example"></a>Azure Database for PostgreSQL 接收器脚本示例

将 Azure Database for PostgreSQL 用作接收器类型时，关联的数据流脚本为：

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
    skipDuplicateMapOutputs: true) ~> AzurePostgreSQLSink
```

## <a name="lookup-activity-properties"></a>Lookup 活动属性

有关属性的详细信息，请参阅 [Azure 数据工厂中的 Lookup 活动](control-flow-lookup-activity.md)。

## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂中复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
