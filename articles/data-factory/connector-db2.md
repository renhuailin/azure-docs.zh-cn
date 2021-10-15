---
title: 从 DB2 复制数据
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何通过在 Azure 数据工厂或 Synapse Analytics 管道中使用复制活动，将数据从 DB2 复制到支持的接收器数据存储。
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.author: jianleishen
ms.openlocfilehash: def376920d111f915edfa7f367fcbedd4bc370f4
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124778051"
---
# <a name="copy-data-from-db2-using-azure-data-factory-or-synapse-analytics"></a>使用 Azure 数据工厂或 Synapse Analytics 从 DB2 复制数据
> [!div class="op_single_selector" title1="选择所使用的数据工厂服务版本："]
> * [版本 1](v1/data-factory-onprem-db2-connector.md)
> * [当前版本](connector-db2.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何使用 Azure 数据工厂和 Synapse Analytics 管道中的复制活动从 DB2 数据库复制数据。 它是基于概述复制活动总体的[复制活动概述](copy-activity-overview.md)一文。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 DB2 数据库连接器：

- 带有[支持的源或接收器矩阵](copy-activity-overview.md)的[复制活动](copy-activity-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)

可以将数据从 DB2 数据库复制到任何支持的接收器数据存储。 有关复制活动支持作为源/接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

具体而言，此 DB2 连接器支持以下 IBM DB2 平台和版本，以及分布式关系数据库结构 (DRDA) SQL 访问管理器 (SQLAM) 版本 9、版本 10 和版本 11。  它采用 DDM/DRDA 协议。

* IBM DB2 for z/OS 12.1
* IBM DB2 for z/OS 11.1
* IBM DB2 for z/OS 10.1
* IBM DB2 for i 7.3
* IBM DB2 for i 7.2
* IBM DB2 for i 7.1
* IBM DB2 for LUW 11
* IBM DB2 for LUW 10.5
* IBM DB2 for LUW 10.1

>[!TIP]
>DB2 连接器构建在 DB2 的 Microsoft OLE DB 提供程序之上。 若要排查 DB2 连接器错误，请参阅[数据提供程序错误代码](/host-integration-server/db2oledbv/data-provider-error-codes#drda-protocol-errors)。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](includes/data-factory-v2-integration-runtime-requirements.md)]

集成运行时提供内置 DB2 驱动程序，因此从 DB2 复制数据时，无需手动安装任何驱动程序。

## <a name="getting-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-db2-using-ui"></a>使用 UI 创建到 DB2 的链接服务

使用以下步骤在 Azure 门户 UI 中创建一个到 DB2 的链接服务。

1. 浏览到 Azure 数据工厂或 Synapse 工作区中的“管理”选项卡并选择“链接服务”，然后单击“新建”：

    # <a name="azure-data-factory"></a>[Azure 数据工厂](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="屏幕截图，显示如何使用 Azure 数据工厂 UI 创建新的链接服务。":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="屏幕截图，显示如何使用 Azure Synapse UI 创建新的链接服务。":::

2. 搜索“DB2”并选择“DB2 连接器”。

    :::image type="content" source="media/connector-db2/db2-connector.png" alt-text="DB2 连接器的屏幕截图。":::    

1. 配置服务详细信息、测试连接并创建新的链接服务。

    :::image type="content" source="media/connector-db2/configure-db2-linked-service.png" alt-text="DB2 的链接服务配置的屏幕截图。":::

## <a name="connector-configuration-details"></a>连接器配置详细信息

对于特定于 DB2 连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

DB2 链接服务支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | type 属性必须设置为：**Db2** | 是 |
| connectionString | 指定连接到 DB2 实例所需的信息。<br/> 还可以将密码放在 Azure 密钥保管库中，并从连接字符串中拉取 `password` 配置。 有关更多详细信息，请参阅以下示例和[在 Azure 密钥保管库中存储凭据](store-credentials-in-key-vault.md)一文。 | 是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 在[先决条件](#prerequisites)部分了解更多信息。 如果未指定，则使用默认 Azure Integration Runtime。 |否 |

连接字符串中的典型属性：

| 属性 | 说明 | 必须 |
|:--- |:--- |:--- |
| server |DB2 服务器的名称。 可以在冒号分隔的服务器名称后面指定端口号，例如 `server:port`。<br>DB2 连接器采用 DDM/DRDA 协议，如果未指定，则默认使用端口 50000。 特定 DB2 数据库使用的端口可能会因版本和设置而有所不同，例如，对于 DB2 LUW，默认端口为 50000，对于 AS400，默认端口为 446 或 448（如果启用 TLS）。 请参阅以下 DB2 文档，了解端口的典型配置方式：[DB2 z/OS](https://www.ibm.com/support/knowledgecenter/SSEPGG_11.5.0/com.ibm.db2.luw.qb.dbconn.doc/doc/t0008229.html)、[DB2 iSeries](https://www.ibm.com/support/knowledgecenter/ssw_ibm_i_74/ddp/rbal1ports.htm) 和 [DB2 LUW](https://www.ibm.com/support/knowledgecenter/en/SSEKCU_1.1.3.0/com.ibm.psc.doc/install/psc_t_install_typical_db2_port.html)。 |是 |
| database |DB2 数据库的名称。 |是 |
| authenticationType |用于连接 DB2 数据库的身份验证类型。<br/>允许的值为：**基本**。 |是 |
| username |指定用于连接到 DB2 数据库的用户名。 |是 |
| password |指定为用户名指定的用户帐户的密码。 将此字段标记为 SecureString 以安全地存储它，或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 |是 |
| packageCollection    | 指定在查询数据库时此服务自动创建所需包的位置。 如果未设置此值，此服务会使用 {username} 作为默认值。 | 否 |
| certificateCommonName | 使用安全套接字层 (SSL) 或传输层安全性 (TLS) 加密时，必须为“证书公用名称”输入值。 | 否 |

> [!TIP]
> 如果收到一条提示 `The package corresponding to an SQL statement execution request was not found. SQLSTATE=51002 SQLCODE=-805` 的错误消息，则原因是未为用户创建所需的包。 默认情况下，此服务会尝试在集合下创建一个以用于连接到 DB2 的用户命名的包。 指定包集合属性，以指示在查询数据库时希望此服务在哪里创建所需的包。

**示例：**

```json
{
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "connectionString": "server=<server:port>;database=<database>;authenticationType=Basic;username=<username>;password=<password>;packageCollection=<packagecollection>;certificateCommonName=<certname>;"
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
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "connectionString": "server=<server:port>;database=<database>;authenticationType=Basic;username=<username>;packageCollection=<packagecollection>;certificateCommonName=<certname>;",
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

如果使用的是具有以下有效负载的 DB2 链接服务，它仍然按原样受支持，但建议使用新的版本。

**先前的有效负载：**

```json
{
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "server": "<servername:port>",
            "database": "<dbname>",
            "authenticationType": "Basic",
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

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供 DB2 数据集支持的属性列表。

若要从 DB2 复制数据，需要支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为：**Db2Table** | 是 |
| schema | 架构的名称。 |否（如果指定了活动源中的“query”）  |
| 表 | 表的名称。 |否（如果指定了活动源中的“query”）  |
| tableName | 具有架构的表的名称。 支持此属性是为了向后兼容。 对于新的工作负荷，请使用 `schema` 和 `table`。 | 否（如果指定了活动源中的“query”） |

**示例**

```json
{
    "name": "DB2Dataset",
    "properties":
    {
        "type": "Db2Table",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<DB2 linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

如果使用 `RelationalTable` 类型数据集，该数据集仍按原样受支持，但我们建议今后使用新数据集。

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 DB2 源支持的属性列表。

### <a name="db2-as-source"></a>DB2 作为源

若要从 DB2 复制数据，复制活动的 **source** 节需要支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 复制活动 source 的 type 属性必须设置为：**Db2Source** | 是 |
| query | 使用自定义 SQL 查询读取数据。 例如：`"query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""`。 | 否（如果指定了数据集中的“tableName”） |

**示例：**

```json
"activities":[
    {
        "name": "CopyFromDB2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<DB2 input dataset name>",
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
                "type": "Db2Source",
                "query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

如果使用 `RelationalSource` 类型源，该源仍按原样受支持，但我们建议今后使用新源。

## <a name="data-type-mapping-for-db2"></a>DB2 的数据类型映射

从 DB2 复制数据时，将使用以下映射从 DB2 数据类型映射到在服务内部使用的临时数据类型。 若要了解复制活动如何将源架构和数据类型映射到接收器，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

| DB2 数据库类型 | 临时服务数据类型 |
|:--- |:--- |
| BigInt |Int64 |
| 二进制 |Byte[] |
| Blob |Byte[] |
| Char |字符串 |
| Clob |字符串 |
| Date |datetime |
| DB2DynArray |String |
| DbClob |字符串 |
| 小数 |Decimal |
| DecimalFloat |Decimal |
| Double |Double |
| Float |Double |
| Graphic |String |
| Integer |Int32 |
| LongVarBinary |Byte[] |
| LongVarChar |字符串 |
| LongVarGraphic |String |
| Numeric |小数 |
| Real |Single |
| SmallInt |Int16 |
| 时间 |TimeSpan |
| 时间戳 |DateTime |
| VarBinary |Byte[] |
| VarChar |String |
| VarGraphic |String |
| Xml |Byte[] |

## <a name="lookup-activity-properties"></a>Lookup 活动属性

若要了解有关属性的详细信息，请查看 [Lookup 活动](control-flow-lookup-activity.md)。

## <a name="next-steps"></a>后续步骤
有关复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。