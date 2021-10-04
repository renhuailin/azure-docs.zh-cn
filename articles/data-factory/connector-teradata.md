---
title: 从 Teradata Vantage 复制数据
description: 使用 Azure 数据工厂和 Synapse Analytics 中的 Teradata 连接器，你可以将数据从 Teradata Vantage 复制到支持的接收器数据存储。
titleSuffix: Azure Data Factory & Azure Synapse
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.author: jianleishen
ms.openlocfilehash: ab5fc46d558a68503c018fb35e53801f9e6ee282
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124771681"
---
# <a name="copy-data-from-teradata-vantage-using-azure-data-factory-and-synapse-analytics"></a>使用 Azure 数据工厂和 Synapse Analytics 从 Teradata Vantage 复制数据

> [!div class="op_single_selector" title1="选择所使用的数据工厂服务版本："]
>
> * [版本 1](v1/data-factory-onprem-teradata-connector.md)
> * [当前版本](connector-teradata.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何使用 Azure 数据工厂和 Synapse Analytics 管道中的复制活动从 Teradata Vantage 复制数据。 本文是在[复制活动概述](copy-activity-overview.md)的基础上编写的。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 Teradata 连接器：

- 带有[支持的源或接收器矩阵](copy-activity-overview.md)的[复制活动](copy-activity-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)

可以将数据从 Teradata Vantage 复制到任何支持的接收器数据存储。 有关复制活动支持作为源/接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

具体而言，此 Teradata 连接器支持：

- Teradata **版本 14.10、15.0、15.10、16.0、16.10 和 16.20**。
- 使用“基本”、“Windows”或“LDAP”身份验证复制数据。
- 从 Teradata 源进行并行复制。 有关详细信息，请参阅[从 Teradata 进行并行复制](#parallel-copy-from-teradata)部分。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](includes/data-factory-v2-integration-runtime-requirements.md)]

如果使用自承载集成运行时，请注意，它从 3.18 版开始提供内置的 Teradata 驱动程序。 无需手动安装任何驱动程序。 驱动程序要求在自承载集成运行时计算机上安装“Visual C++ Redistributable 2012 Update 4”。 如果尚未安装，请在[此处](https://www.microsoft.com/en-sg/download/details.aspx?id=30679)下载。

## <a name="getting-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-teradata-using-ui"></a>使用 UI 创建到 Teradata 的链接服务

使用以下步骤在 Azure 门户 UI 中创建一个到 Teradata 的链接服务。

1. 浏览到 Azure 数据工厂或 Synapse 工作区中的“管理”选项卡并选择“链接服务”，然后单击“新建”：

    # <a name="azure-data-factory"></a>[Azure 数据工厂](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="使用 Azure 数据工厂 UI 创建新的链接服务。":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="使用 Azure Synapse UI 创建新的链接服务。":::

2. 搜索 Teradata 并选择 Teradata 连接器。

    :::image type="content" source="media/connector-teradata/teradata-connector.png" alt-text="选择 Teradata 连接器。":::    

1. 配置服务详细信息、测试连接并创建新的链接服务。

    :::image type="content" source="media/connector-teradata/configure-teradata-linked-service.png" alt-text="配置到 Teradata 的链接服务。":::

## <a name="connector-configuration-details"></a>连接器配置详细信息

对于特定于 Teradata 连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Teradata 链接服务支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | type 属性必须设置为 **Teradata**。 | 是 |
| connectionString | 指定连接到 Teradata 实例所需的信息。 请参阅以下示例。<br/>还可以将密码放在 Azure Key Vault 中，并从连接字符串中拉取 `password` 配置。 有关更多详细信息，请参阅[在 Azure Key Vault 中存储凭据](store-credentials-in-key-vault.md)。 | 是 |
| username | 指定用于连接到 Teradata 的用户名。 使用 Windows 身份验证时适用。 | 否 |
| password | 指定为用户名指定的用户帐户的密码。 此外，还可以选择[引用 Azure Key Vault 中存储的机密](store-credentials-in-key-vault.md)。 <br>使用 Windows 身份验证时，或引用 Key Vault 中用于基本身份验证的密码时适用。 | 否 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 在[先决条件](#prerequisites)部分了解更多信息。 如果未指定，则使用默认 Azure Integration Runtime。 |否 |

可以根据自己的情况在连接字符串中设置更多连接属性：

| 属性 | 说明 | 默认值 |
|:--- |:--- |:--- |
| TdmstPortNumber | 用于访问 Teradata 数据库的端口号。<br>除非技术支持指示，否则请勿更改此值。 | 1025 |
| UseDataEncryption | 指定是否对 Teradata 数据库的所有通信进行加密。 允许的值为 0 或 1。<br><br/>- **0（已禁用，为默认值）** ：仅加密身份验证信息。<br/>- **1（已启用）** ：对驱动程序和数据库之间传递的所有数据进行加密。 | `0` |
| CharacterSet | 要用于会话的字符集。 例如，`CharacterSet=UTF16`。<br><br/>此值可以是用户定义的字符集，也可以是以下预定义的字符集之一： <br/>- ASCII<br/>- UTF8<br/>- UTF16<br/>- LATIN1252_0A<br/>- LATIN9_0A<br/>- LATIN1_0A<br/>- Shift-JIS（Windows、兼容 DOS、KANJISJIS_0S）<br/>- EUC（兼容 Unix、KANJIEC_0U）<br/>- IBM Mainframe (KANJIEBCDIC5035_0I)<br/>- KANJI932_1S0<br/>- BIG5 (TCHBIG5_1R0)<br/>- GB (SCHGB2312_1T0)<br/>- SCHINESE936_6R0<br/>- TCHINESE950_8R0<br/>- NetworkKorean (HANGULKSC5601_2R4)<br/>- HANGUL949_7R0<br/>- ARABIC1256_6A0<br/>- CYRILLIC1251_2A0<br/>- HEBREW1255_5A0<br/>- LATIN1250_1A0<br/>- LATIN1254_7A0<br/>- LATIN1258_8A0<br/>- THAI874_4A0 | `ASCII` |
| MaxRespSize |SQL 请求的响应缓冲区的最大大小，以千字节 (KB) 为单位。 例如，`MaxRespSize=‭10485760‬`。<br/><br/>对于 Teradata 数据库版本 16.00 或更高版本，最大值为 7361536。 对于使用较早版本的连接，最大值为 1048576。 | `65536` |
| MechanismName | 若要使用 LDAP 协议对连接进行身份验证，请指定 `MechanismName=LDAP`。 | 不适用 |

**示例：使用基本身份验证**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>;Uid=<username>;Pwd=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**示例：使用 Windows 身份验证**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>",
            "username": "<username>",
            "password": "<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**示例：使用 LDAP 身份验证**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>;MechanismName=LDAP;Uid=<username>;Pwd=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

> [!NOTE]
>
> 仍支持以下有效负载。 不过，今后应使用新的有效负载。

**先前的有效负载：**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<Basic/Windows>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
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

本部分提供 Teradata 数据集支持的属性列表。 有关可用于定义数据集的各个部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)。

从 Teradata 复制数据时，支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为 `TeradataTable`。 | 是 |
| database | Teradata 实例的名称。 | 否（如果指定了活动源中的“query”） |
| 表 | Teradata 实例中的表名。 | 否（如果指定了活动源中的“query”） |

**示例：**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "TeradataTable",
        "typeProperties": {},
        "schema": [],        
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

> [!NOTE]
>
> 仍支持 `RelationalTable` 类型数据集。 不过，我们建议使用新的数据集。

**先前的有效负载：**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

本部分提供 Teradata 源支持的属性列表。 有关可用于定义活动的各个部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)。 

### <a name="teradata-as-source"></a>以 Teradata 作为源

>[!TIP]
>若要详细了解如何使用数据分区从 Teradata 有效加载数据，请参阅[从 Teradata 进行并行复制](#parallel-copy-from-teradata)部分。

从 Teradata 复制数据时，复制活动的 **source** 节支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 复制活动 source 的 type 属性必须设置为 `TeradataSource`。 | 是 |
| query | 使用自定义 SQL 查询读取数据。 例如 `"SELECT * FROM MyTable"`。<br>启用分区加载时，需要在查询中挂接任何相应的内置分区参数。 有关示例，请参阅[从 Teradata 进行并行复制](#parallel-copy-from-teradata)部分。 | 否（如果指定了数据集中的表） |
| partitionOptions | 指定用于从 Teradata 加载数据的数据分区选项。 <br>允许的值为：**None**（默认值）、**Hash** 和 **DynamicRange**。<br>启用分区选项（即，该选项不为 `None`）时，用于从 Teradata 并行加载数据的并行度由复制活动上的 [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 设置控制。 | 否 |
| partitionSettings | 指定数据分区的设置组。 <br>当分区选项不是 `None` 时适用。 | 否 |
| partitionColumnName | 指定用于并行复制的，由范围分区或哈希分区使用的源列的名称。 如果未指定，系统会自动检测表的主索引并将其用作分区列。 <br>当分区选项是 `Hash` 或 `DynamicRange` 时适用。 如果使用查询来检索源数据，请在 WHERE 子句中挂接 `?AdfHashPartitionCondition` 或 `?AdfRangePartitionColumnName`。 请参阅[从 Teradata 进行并行复制](#parallel-copy-from-teradata)部分中的示例。 | 否 |
| partitionUpperBound | 要从中复制数据的分区列的最大值。 <br>当分区选项是 `DynamicRange` 时适用。 如果使用查询来检索源数据，请在 WHERE 子句中挂接 `?AdfRangePartitionUpbound`。 有关示例，请参阅[从 Teradata 进行并行复制](#parallel-copy-from-teradata)部分。 | 否 |
| partitionLowerBound | 要从中复制数据的分区列的最小值。 <br>当分区选项是 `DynamicRange` 时适用。 如果使用查询来检索源数据，请在 WHERE 子句中挂接 `?AdfRangePartitionLowbound`。 有关示例，请参阅[从 Teradata 进行并行复制](#parallel-copy-from-teradata)部分。 | 否 |

> [!NOTE]
>
> 仍支持 `RelationalSource` 类型复制源，但它不支持从 Teradata 进行并行加载（分区选项）的新内置功能。 不过，我们建议使用新的数据集。

**示例：使用基本查询但不使用分区复制数据**

```json
"activities":[
    {
        "name": "CopyFromTeradata",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Teradata input dataset name>",
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
                "type": "TeradataSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-teradata"></a>从 Teradata 进行并行复制

Teradata 连接器提供内置的数据分区，用于从 Teradata 并行复制数据。 可以在复制活动的“源”表中找到数据分区选项。 

:::image type="content" source="./media/connector-teradata/connector-teradata-partition-options.png" alt-text="分区选项的屏幕截图":::

启用分区复制后，服务将对 Teradata 源运行并行查询，以便按分区加载数据。 可通过复制活动中的 [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 设置控制并行度。 例如，如果将 `parallelCopies` 设置为 4，则该服务会根据指定的分区选项和设置并行生成并运行 4 个查询，每个查询从 Teradata 检索一部分数据。

建议同时启用并行复制和数据分区，尤其是从 Teradata 加载大量数据时。 下面是适用于不同方案的建议配置。 将数据复制到基于文件的数据存储中时，建议将数据作为多个文件写入文件夹（仅指定文件夹名称），在这种情况下，性能优于写入单个文件。

| 方案                                                     | 建议的设置                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 从大型表进行完整加载。                                   | **分区选项**：哈希。 <br><br/>在执行期间，服务会自动检测主索引列，对其应用哈希，然后按分区复制数据。 |
| 使用自定义查询加载大量数据。                 | **分区选项**：哈希。<br>**查询**：`SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>`。<br>**分区列**：指定用于应用哈希分区的列。 如果未指定，服务会自动检测 Teradata 数据集中指定的表的 PK 列。<br><br>在执行期间，服务会将 `?AdfHashPartitionCondition` 替换为哈希分区逻辑，并将其发送到 Teradata。 |
| 使用自定义查询加载大量数据，某个整数列包含均匀分布的范围分区值。 | **分区选项**：动态范围分区。<br>**查询**：`SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`。<br>**分区列**：指定用于对数据进行分区的列。 可以针对整数数据类型的列进行分区。<br>**分区上限** 和 **分区下限**：指定是否要对分区列进行筛选，以便仅检索介于下限和上限之间的数据。<br><br>在执行期间，服务会将 `?AdfRangePartitionColumnName`、`?AdfRangePartitionUpbound` 和 `?AdfRangePartitionLowbound` 替换为每个分区的实际列名称和值范围，并将其发送到 Teradata。 <br>例如，如果为分区列“ID”设置了下限 1、上限 80，并将并行复制设置为 4，则服务会按 4 个分区检索数据。 其 ID 分别介于 [1, 20]、[21, 40]、[41, 60] 和 [61, 80] 之间。 |

**示例：使用哈希分区进行查询**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "Hash",
    "partitionSettings": {
        "partitionColumnName": "<hash_partition_column_name>"
    }
}
```

**示例：使用动态范围分区进行查询**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="data-type-mapping-for-teradata"></a>Teradata 的数据类型映射

从 Teradata 复制数据时，将应用下列从 Teradata 的数据类型到服务使用的内部数据类型的映射。 若要了解复制活动如何将源架构和数据类型映射到接收器，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

| Teradata 数据类型 | 临时服务数据类型 |
|:--- |:--- |
| BigInt |Int64 |
| Blob |Byte[] |
| Byte |Byte[] |
| ByteInt |Int16 |
| Char |字符串 |
| Clob |字符串 |
| Date |DateTime |
| Decimal |小数 |
| Double |Double |
| Graphic |不支持。 在源查询中应用显式强制转换。 |
| Integer |Int32 |
| Interval Day |不支持。 在源查询中应用显式强制转换。 |
| Interval Day To Hour |不支持。 在源查询中应用显式强制转换。 |
| Interval Day To Minute |不支持。 在源查询中应用显式强制转换。 |
| Interval Day To Second |不支持。 在源查询中应用显式强制转换。 |
| Interval Hour |不支持。 在源查询中应用显式强制转换。 |
| Interval Hour To Minute |不支持。 在源查询中应用显式强制转换。 |
| Interval Hour To Second |不支持。 在源查询中应用显式强制转换。 |
| Interval Minute |不支持。 在源查询中应用显式强制转换。 |
| Interval Minute To Second |不支持。 在源查询中应用显式强制转换。 |
| Interval Month |不支持。 在源查询中应用显式强制转换。 |
| Interval Second |不支持。 在源查询中应用显式强制转换。 |
| Interval Year |不支持。 在源查询中应用显式强制转换。 |
| Interval Year To Month |不支持。 在源查询中应用显式强制转换。 |
| Number |Double |
| 期间（日期） |不支持。 在源查询中应用显式强制转换。 |
| 期间（时间） |不支持。 在源查询中应用显式强制转换。 |
| 期间（带时区的时间） |不支持。 在源查询中应用显式强制转换。 |
| 期间（时间戳） |不支持。 在源查询中应用显式强制转换。 |
| 期间（带时区的时间戳） |不支持。 在源查询中应用显式强制转换。 |
| SmallInt |Int16 |
| 时间 |TimeSpan |
| Time With Time Zone |TimeSpan |
| 时间戳 |DateTime |
| Timestamp With Time Zone |DateTime |
| VarByte |Byte[] |
| VarChar |字符串 |
| VarGraphic |不支持。 在源查询中应用显式强制转换。 |
| Xml |不支持。 在源查询中应用显式强制转换。 |


## <a name="lookup-activity-properties"></a>查找活动属性

若要了解有关属性的详细信息，请查看 [Lookup 活动](control-flow-lookup-activity.md)。


## <a name="next-steps"></a>后续步骤
有关复制活动支持作为源和接收器的数据存储的列表，请参阅[受支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
