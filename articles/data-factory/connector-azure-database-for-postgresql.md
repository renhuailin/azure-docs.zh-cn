---
title: 复制和转换 Azure Database for PostgreSQL 中的数据
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何使用 Azure 数据工厂和 Synapse Analytics 来复制和转换 Azure Database for PostgreSQL 中的数据。
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 2f17e5a90eac6941786a08044132867dadcb9ca0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128592429"
---
# <a name="copy-and-transform-data-in-azure-database-for-postgresql-using-azure-data-factory-or-synapse-analytics"></a>使用 Azure 数据工厂或 Synapse Analytics 来复制和转换 Azure Database for PostgreSQL 中的数据

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述了如何使用 Azure 数据工厂和 Synapse Analytics 管道中的复制活动从/向 Azure Database for PostgreSQL 复制数据，以及如何使用数据流转换 Azure Database for PostgreSQL 中的数据。 有关详细信息，请阅读 [Azure 数据工厂](introduction.md)和 [Synapse Analytics](../synapse-analytics/overview-what-is.md) 的简介文章。

此连接器专用于 [Azure Database for PostgreSQL 服务](../postgresql/overview.md)。 若要从位于本地或云中的通用 PostgreSQL 数据库复制数据，请使用 [PostgreSQL 连接器](connector-postgresql.md)。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 Azure Database for PostgreSQL 连接器：

- 带有[支持的源或接收器矩阵](copy-activity-overview.md)的[复制活动](copy-activity-overview.md)
- [映射数据流](concepts-data-flow-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)

目前，数据流支持 Azure Database for PostgreSQL 单一服务器，但不支持灵活服务器或超大规模 (Citus)；Azure Synapse Analytics 中的数据流支持所有 PostgreSQL 风格。

## <a name="getting-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-azure-database-for-postgresql-using-ui"></a>使用 UI 创建一个到 Azure Database for PostgreSQL 的链接服务

使用以下步骤在 Azure 门户 UI 中创建一个到 Azure Database for PostgreSQL 的链接服务。

1. 浏览到 Azure 数据工厂或 Synapse 工作区中的“管理”选项卡并选择“链接服务”，然后单击“新建”：

    # <a name="azure-data-factory"></a>[Azure 数据工厂](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="使用 Azure 数据工厂 UI 创建新的链接服务。":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="使用 Azure Synapse UI 创建新的链接服务。":::

2. 搜索 PostgreSQL 并选择 Azure Database for PostgreSQL 连接器。

    :::image type="content" source="media/connector-azure-database-for-postgresql/azure-database-for-postgresql-connector.png" alt-text="选择 Azure Database for PostgreSQL 连接器。":::    

1. 配置服务详细信息，测试连接，然后创建新的链接服务。

    :::image type="content" source="media/connector-azure-database-for-postgresql/configure-azure-database-for-postgresql-linked-service.png" alt-text="配置到 Azure Database for PostgreSQL 的链接服务。":::

## <a name="connector-configuration-details"></a>连接器配置详细信息

对于特定于 Azure Database for PostgreSQL 连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Azure Database for PostgreSQL 链接服务支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | type 属性必须设置为：**AzurePostgreSql**。 | 是 |
| connectionString | 用于连接到 Azure Database for PostgreSQL 的 ODBC 连接字符串。<br/>还可以将密码放在 Azure 密钥保管库中，并从连接字符串中拉取 `password` 配置。 有关更多详细信息，请参阅以下示例和[在 Azure 密钥保管库中存储凭据](store-credentials-in-key-vault.md)。 | 是 |
| connectVia | 此属性表示用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果数据存储位于专用网络，则可以使用 Azure Integration Runtime 或自承载集成运行时。 如果未指定，则使用默认 Azure Integration Runtime。 |否 |

典型的连接字符串为 `Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>`。 以下是你可以根据具体情况设置的更多属性：

| 属性 | 说明 | 选项 | 必须 |
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

***在 Azure 密钥保管库中存储密码***

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

有关可用于定义数据集的各个部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)。 本部分提供数据集中 Azure Database for PostgreSQL 支持的属性列表。

要从 Azure Database for PostgreSQL 复制数据，请将数据集的 type 属性设置为 **AzurePostgreSqlTable**。 支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为 AzurePostgreSqlTable  | 是 |
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

有关可用于定义活动的各个部分和属性的完整列表，请参阅[管道和活动](concepts-pipelines-activities.md)。 本部分提供 Azure Database for PostgreSQL 源支持的属性列表。

### <a name="azure-database-for-postgresql-as-source"></a>用于 PostgreSql 的 Azure 数据库作为源

要从 Azure Database for PostgreSQL 复制数据，请将复制活动中的源类型设置为 **AzurePostgreSqlSource**。 复制活动 **source** 部分支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为 **AzurePostgreSqlSource** | 是 |
| query | 使用自定义 SQL 查询读取数据。 例如 `SELECT * FROM mytable` 或 `SELECT * FROM "MyTable"`。 请注意，在 PostgreSQL 中，如果未加引号，则实体名称不区分大小写。 | 否（如果指定了数据集中的 tableName 属性） |
| partitionOptions | 指定用于从 Azure SQL 数据库加载数据的数据分区选项。 <br>允许值包括：None（默认值）、PhysicalPartitionsOfTable 和 DynamicRange  。<br>启用分区选项（即，该选项不为 `None`）时，用于从 Azure SQL 数据库并行加载数据的并行度由复制活动上的 [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 设置控制。 | 否 |
| partitionSettings | 指定数据分区的设置组。 <br>当分区选项不是 `None` 时适用。 | 否 |
| 在 `partitionSettings` 下： | | |
| partitionColumnName | 以整数类型、日期类型或日期/时间类型（`int`、`smallint`、`bigint`、`date`、`smalldatetime`、`datetime`、`datetime2` 或 `datetimeoffset`）指定源列的名称，范围分区将使用它进行并行复制。 如果未指定，系统会自动检测表的索引或主键并将其用作分区列。<br>当分区选项是 `DynamicRange` 时适用。 如果使用查询来检索源数据，请在 WHERE 子句中挂接 `?AdfDynamicRangePartitionCondition `。 有关示例，请参阅[从 Azure Database for PostgreSQL 进行并行复制](#parallel-copy-from-azure-database-for-postgresql)一节。 | 否 |
| partitionUpperBound | 分区范围拆分的分区列的最大值。 此值用于决定分区步幅，不用于筛选表中的行。 将对表或查询结果中的所有行进行分区和复制。 如果未指定，复制活动会自动检测该值。  <br>当分区选项是 `DynamicRange` 时适用。 有关示例，请参阅[从 Azure Database for PostgreSQL 进行并行复制](#parallel-copy-from-azure-database-for-postgresql)一节。 | 否 |
| partitionLowerBound | 分区范围拆分的分区列的最小值。 此值用于决定分区步幅，不用于筛选表中的行。 将对表或查询结果中的所有行进行分区和复制。 如果未指定，复制活动会自动检测该值。<br>当分区选项是 `DynamicRange` 时适用。 有关示例，请参阅[从 Azure Database for PostgreSQL 进行并行复制](#parallel-copy-from-azure-database-for-postgresql)一节。 | 否 |

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

| Property | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 复制活动接收器的 type 属性必须设置为 **AzurePostgreSQLSink**。 | 是 |
| preCopyScript | 每次运行时将数据写入 Azure Database for PostgreSQL 之前，为要执行的复制活动指定 SQL 查询。 可以使用此属性清除预加载的数据。 | 否 |
| writeMethod | 用于将数据写入 Azure Database for PostgreSQL 的方法。<br>允许的值为：CopyCommand（默认值，性能较佳）和 BulkInsert 。 | 否 |
| writeBatchSize | 每批加载到 Azure Database for PostgreSQL 中的行数。<br>允许的值是表示行数的整数。 | 否（默认值为 1000000） |
| writeBatchTimeout | 超时之前等待批插入操作完成时的等待时间。<br>允许的值为 Timespan 字符串。 示例为 00:30:00（30 分钟）。 | 否（默认值为 00:30:00） |

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
                "writeMethod": "CopyCommand",
                "writeBatchSize": 1000000
            }
        }
    }
]
```

## <a name="parallel-copy-from-azure-database-for-postgresql"></a>从 Azure Database for PostgreSQL 进行并行复制

复制活动中的 Azure Database for PostgreSQL 连接器提供内置的数据分区，用于并行复制数据。 可以在复制活动的“源”表中找到数据分区选项。 

![分区选项的屏幕截图](.\media\connector-azure-database-for-postgresql/connector-postgresql-partition-options.png)

启用分区复制时，复制活动将对 Azure Database for PostgreSQL 源运行并行查询，以按分区加载数据。 可通过复制活动中的 [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 设置控制并行度。 例如，如果将 `parallelCopies` 设置为 4，则该服务会根据指定的分区选项和设置并行生成并运行 4 个查询，每个查询从 Azure Database for PostgreSQL 检索一部分数据。

建议同时启用并行复制和数据分区，尤其是从 Azure Database for PostgreSQL 加载大量数据时。 下面是适用于不同方案的建议配置。 将数据复制到基于文件的数据存储中时，建议将数据作为多个文件写入文件夹（仅指定文件夹名称），在这种情况下，性能优于写入单个文件。

| 方案                                                     | 建议的设置                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 从包含物理分区的大型表进行完整加载。        | **分区选项**：表的物理分区。 <br><br/>在执行期间，该服务将自动检测物理分区并按分区复制数据。 |
| 从不包含物理分区但包含用于数据分区的整数或日期时间列的大型表进行完整加载。 | **分区选项**：动态范围分区。<br>**分区列**（可选）：指定用于对数据进行分区的列。 如果未指定，将使用索引或主键列。<br/>分区上限和分区下限（可选） ：指定是否要确定分区步幅。 这不适用于筛选表中的行，表中的所有行都将进行分区和复制。 如果未指定，复制活动会自动检测这些值。<br><br>例如，如果分区列“ID”的值范围为 1 至 100，其下限设置为 20、上限设置为 80，并行复制设置为 4，则该服务会按 4 个分区检索数据，ID 范围分别为 <=20、[21, 50]、[51, 80] 和 >=81。 |
| 使用自定义查询从不包含物理分区但包含用于数据分区的整数或日期/日期时间列的表加载大量数据。 | **分区选项**：动态范围分区。<br>**查询**：`SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`。<br>**分区列**：指定用于对数据进行分区的列。<br>分区上限和分区下限（可选） ：指定是否要确定分区步幅。 这不适用于筛选表中的行，查询结果中的所有行都将进行分区和复制。 如果未指定，复制活动会自动检测该值。<br><br>在执行期间，该服务会将 `?AdfRangePartitionColumnName` 替换为每个分区的实际列名称和值范围，并发送到 Azure Database for PostgreSQL。 <br>例如，如果分区列“ID”的值范围为 1 至 100，其下限设置为 20、上限设置为 80，并行复制设置为 4，则该服务会按 4 个分区检索数据，ID 范围分别为 <=20、[21, 50]、[51, 80] 和 >=81。 <br><br>下面是针对不同场景的更多示例查询：<br> 1.查询整个表： <br>`SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition`<br> 2.使用列选择和附加的 where 子句筛选器从表中查询： <br>`SELECT <column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 3.使用子查询进行查询： <br>`SELECT <column_list> FROM (<your_sub_query>) AS T WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 4.在子查询中使用分区查询： <br>`SELECT <column_list> FROM (SELECT <your_sub_query_column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition) AS T`
|

使用分区选项加载数据的最佳做法：

1. 选择独特的列作为分区列（如主键或唯一键），以避免数据倾斜。 
2. 如果表具有内置分区，请使用名为“表的物理分区”分区选项来提升性能。    
3. 如果使用 Azure Integration Runtime 复制数据，则可设置较大的“[数据集成单元 (DIU)](copy-activity-performance-features.md#data-integration-units)”(>4) 以利用更多计算资源。 检查此处适用的方案。
4. “[复制并行度](copy-activity-performance-features.md#parallel-copy)”可控制分区数量，将此数字设置得太大有时会损害性能，建议将此数字设置按以下公式计算的值：（DIU 或自承载 IR 节点数）*（2 到 4）。

**示例：从包含物理分区的大型表进行完整加载**

```json
"source": {
    "type": "AzurePostgreSqlSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**示例：使用动态范围分区进行查询**

```json
"source": {
    "type": "AzurePostgreSqlSource",
    "query": "SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column (optional) to decide the partition stride, not as data filter>",
        "partitionLowerBound": "<lower_value_of_partition_column (optional) to decide the partition stride, not as data filter>"
    }
}
```

## <a name="mapping-data-flow-properties"></a>映射数据流属性

在映射数据流中转换数据时，可以从 Azure Database for PostgreSQL 读取表以及将数据写入表。 有关详细信息，请参阅映射数据流中的[源转换](data-flow-source.md)和[接收器转换](data-flow-sink.md)。 你可以选择使用 Azure Database for PostgreSQL 数据集或[内联数据集](data-flow-source.md#inline-datasets)作为源和接收器类型。

### <a name="source-transformation"></a>源转换

下表列出了 Azure Database for PostgreSQL 源支持的属性。 你可以在“源选项”选项卡中编辑这些属性。

| 名称 | 说明 | 必需 | 允许的值 | 数据流脚本属性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 表 | 如果你选择“表”作为输入，则数据流会从数据集中指定的表提取所有数据。 | 否 | - |（仅适用于内联数据集）<br>tableName |
| 查询 | 如果你选择“查询”作为输入，请指定一个用来从源提取数据的 SQL 查询，这将替代在数据集中指定的任何表。 使用查询是一个好方法，它可以减少用于测试或查找的行数。<br><br>不支持 Order By 子句，但你可以设置完整的 SELECT FROM 语句。 还可以使用用户定义的表函数。 select * from udfGetData() 是 SQL 中的一个 UDF，它返回你可以在数据流中使用的表。<br>查询示例：`select * from mytable where customerId > 1000 and customerId < 2000` 或 `select * from "MyTable"`。 请注意，在 PostgreSQL 中，如果未加引号，则实体名称不区分大小写。| 否 | 字符串 | query |
| 批大小 | 指定批大小，以将大型数据分成多个批。 | 否 | Integer | batchSize |
| 隔离级别 | 选择下列隔离级别之一：<br>- 读取已提交的内容<br>- 读取未提交的内容（默认）<br>- 可重复的读取<br>- 可序列化<br>- 无（忽略隔离级别） | 否 | <small>READ_COMMITTED<br/>READ_UNCOMMITTED<br/>REPEATABLE_READ<br/>SERIALIZABLE<br/>NONE</small> |isolationLevel |

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

下表列出了 Azure Database for PostgreSQL 接收器支持的属性。 可以在“接收器选项”选项卡中编辑这些属性。

| 名称 | 说明 | 必需 | 允许的值 | 数据流脚本属性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Update 方法 | 指定数据库目标上允许哪些操作。 默认设置为仅允许插入。<br>若要更新、更新插入或删除行，需要进行[“更改行”转换](data-flow-alter-row.md)才能标记这些操作的行。 | 是 | `true` 或 `false` | deletable <br/>insertable <br/>updateable <br/>upsertable |
| 键列 | 对于更新、更新插入和删除操作，必须设置键列来确定要更改的行。<br>后续的更新、更新插入和删除将使用你选取为密钥的列名称。 因此，你必须选取存在于接收器映射中的列。 | 否 | Array | 密钥 |
| 跳过写入键列 | 如果你不希望将值写入到键列，请选择“跳过写入键列”。 | 否 | `true` 或 `false` | skipKeyWrites |
| 表操作 |确定在写入之前是否从目标表重新创建或删除所有行。<br>- **无**：不会对表进行任何操作。<br>- 重新创建：将删除表并重新创建表。 如果以动态方式创建表，则是必需的。<br>- 截断：将删除目标表中的所有行。 | 否 | `true` 或 `false` | recreate<br/>truncate |
| 批大小 | 指定每批中写入的行数。 较大的批大小可提高压缩比并改进内存优化，但在缓存数据时可能会导致内存不足异常。 | 否 | Integer | batchSize |
| 预处理和后处理 SQL 脚本 | 指定在将数据写入接收器数据库之前（预处理）和之后（后处理）会执行的多行 SQL 脚本。 | 否 | 字符串 | preSQLs<br>postSQLs |

#### <a name="azure-database-for-postgresql-sink-script-example"></a>Azure Database for PostgreSQL 接收器脚本示例

使用 Azure Database for PostgreSQL 作为接收器类型时，关联的数据流脚本为：

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

## <a name="lookup-activity-properties"></a>查找活动属性

有关属性的详细信息，请参阅[查找活动](control-flow-lookup-activity.md)。

## <a name="next-steps"></a>后续步骤
有关复制活动支持作为源和接收器的数据存储的列表，请参阅[受支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
