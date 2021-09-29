---
title: 从/向 Salesforce 复制数据
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何通过在 Azure 数据工厂或 Azure Synapse Analytics 管道中使用复制活动，将数据从 Salesforce 复制到支持的接收器数据存储，或者从支持的源数据存储复制到 Salesforce。
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/03/2021
ms.openlocfilehash: 0a7bca44ccee4e836fd5aa8e0ef44412e1fc6985
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124836212"
---
# <a name="copy-data-from-and-to-salesforce-using-azure-data-factory-or-azure-synapse-analytics"></a>使用 Azure 数据工厂或 Azure Synapse Analytics 从/向 Salesforce 复制数据

> [!div class="op_single_selector" title1="选择所使用的数据工厂服务版本："]
> * [版本 1](v1/data-factory-salesforce-connector.md)
> * [当前版本](connector-salesforce.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何使用 Azure 数据工厂和 Azure Synapse 管道中的复制活动从/向 Salesforce 复制数据。 本文基于总体概述复制活动的[复制活动概述](copy-activity-overview.md)一文。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 Salesforce 连接器：

- 带有[支持的源或接收器矩阵](copy-activity-overview.md)的[复制活动](copy-activity-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)

可以将数据从 Salesforce 复制到任何支持的接收器数据存储。 还可以将数据从任何支持的源数据存储复制到 Salesforce。 有关复制活动支持作为源或接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

具体而言，Salesforce 连接器支持：

- Salesforce 开发人员版、专业版、企业版或不受限制版。
- 从/向 Salesforce 生产、沙盒和自定义域复制数据。

Salesforce 连接器在 Salesforce REST/Bulk API 之上构建。 从 Salesforce 复制数据时，连接器会根据数据大小在 REST 和批量 API 之间自动选择。当结果集较大时，使用批量 API 来提高性能。你可通过链接服务中的 [`apiVersion` 属性](#linked-service-properties)显式设置用于读取/写入数据的 API 版本。

## <a name="prerequisites"></a>先决条件

在 Salesforce 中，必须启用 API 权限。

## <a name="salesforce-request-limits"></a>Salesforce 请求限制

Salesforce 对 API 请求总数和并发 API 请求均有限制。 请注意以下几点：

- 如果并发请求数超过限制，则将进行限制并且会看到随机失败。
- 如果请求总数超过限制，会阻止 Salesforce 帐户 24 小时。

在这两种情况下，还可能会收到“REQUEST_LIMIT_EXCEEDED”错误消息。 有关详细信息，请参阅 [Salesforce 开发人员限制](https://developer.salesforce.com/docs/atlas.en-us.218.0.salesforce_app_limits_cheatsheet.meta/salesforce_app_limits_cheatsheet/salesforce_app_limits_platform_api.htm)中的“API 请求限制”部分。

## <a name="get-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-salesforce-using-ui"></a>使用 UI 创建到 Salesforce 的链接服务

使用以下步骤在 Azure 门户 UI 中创建一个到 Salesforce 的链接服务。

1. 浏览到 Azure 数据工厂或 Synapse 工作区中的“管理”选项卡并选择“链接服务”，然后单击“新建”：

    # <a name="azure-data-factory"></a>[Azure 数据工厂](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="屏幕截图，显示如何使用 Azure 数据工厂 UI 创建新的链接服务。":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="屏幕截图，显示如何使用 Azure Synapse UI 创建新的链接服务。":::

2. 搜索“Salesforce”并选择 Salesforce 连接器。

    :::image type="content" source="media/connector-salesforce/salesforce-connector.png" alt-text="Salesforce 连接器的屏幕截图。":::    

1. 配置服务详细信息、测试连接并创建新的链接服务。

    :::image type="content" source="media/connector-salesforce/configure-salesforce-linked-service.png" alt-text="Salesforce 的链接服务配置的屏幕截图。":::

## <a name="connector-configuration-details"></a>连接器配置详细信息

对于特定于 Salesforce 连接器的实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Salesforce 链接服务支持以下属性。

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type |类型属性必须设置为 **Salesforce**。 |是 |
| environmentUrl | 指定 Salesforce 实例的 URL。 <br> - 默认为 `"https://login.salesforce.com"`。 <br> - 要从沙盒复制数据，请指定 `"https://test.salesforce.com"`。 <br> - 要从自定义域复制数据，请指定 `"https://[domain].my.salesforce.com"`（以此为例）。 |否 |
| username |为用户帐户指定用户名。 |是 |
| password |指定用户帐户的密码。<br/><br/>将此字段标记为 SecureString 以安全地存储它，或[引用 Azure Key Vault 中存储的机密](store-credentials-in-key-vault.md)。 |是 |
| securityToken |为用户帐户指定安全令牌。 <br/><br/>若要了解有关安全令牌的一般信息，请参阅 [Security and the API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm)（安全性和 API）。 仅当将 Integration Runtime 的 IP 添加到 Salesforce 上的[受信任 IP 地址列表](https://developer.salesforce.com/docs/atlas.en-us.securityImplGuide.meta/securityImplGuide/security_networkaccess.htm)时，才能跳过安全令牌。 使用 Azure IR 时，请参阅 [Azure Integration Runtime IP 地址](azure-integration-runtime-ip-addresses.md)。<br/><br/>有关如何获取和重置安全令牌的说明，请参阅[获取安全令牌](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm)。 将此字段标记为 SecureString 以安全地存储它，或[引用 Azure Key Vault 中存储的机密](store-credentials-in-key-vault.md)。 |否 |
| apiVersion | 指定要使用的 Salesforce REST/Bulk API 版本，例如 `52.0`。 | 否 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果未指定，则使用默认 Azure Integration Runtime。 | 否 |

**示例：存储凭据**

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            },
            "securityToken": {
                "type": "SecureString",
                "value": "<security token>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**示例：在密钥保管库中存储凭据**

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of password in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            },
            "securityToken": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of security token in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
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

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供 Salesforce 数据集支持的属性列表。

要从/向 Salesforce 复制数据，请将数据集的 type 属性设置为 **SalesforceObject**。 支持以下属性。

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | type 属性必须设置为 **SalesforceObject**。  | 是 |
| objectApiName | 要从中检索数据的 Salesforce 对象名称。 | 对于源为“No”，对于接收器为“Yes” |

> [!IMPORTANT]
> 任何自定义对象均需要 **API 名称** 的“__c”部分。

:::image type="content" source="media/copy-data-from-salesforce/data-factory-salesforce-api-name.png" alt-text="Salesforce 连接 API 名称":::

**示例：**

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "type": "SalesforceObject",
        "typeProperties": {
            "objectApiName": "MyTable__c"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Salesforce linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

>[!NOTE]
>为了向后兼容：从 Salesforce 复制数据时，如果使用以前的“RelationalTable”类型数据集，它会在你看到切换到新的“SalesforceObject”类型的建议时继续工作。

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为 **RelationalTable**。 | 是 |
| tableName | 在 Salesforce 中表的名称。 | 否（如果指定了活动源中的“query”） |

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 Salesforce 源和接收器支持的属性列表。

### <a name="salesforce-as-a-source-type"></a>将 Salesforce 用作源类型

要从 Salesforce 复制数据，请将复制活动中的源类型设置为“SalesforceSource”。 复制活动的 **source** 节支持以下属性。

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为 **SalesforceSource**。 | 是 |
| 查询 |使用自定义查询读取数据。 可以使用 [Salesforce 对象查询语言 (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) 查询或 SQL-92 查询。 请在[查询提示](#query-tips)部分中查看更多提示。 如果未指定查询，将检索在数据集的“objectApiName”中指定的 Salesforce 对象的所有数据。 | 否（如果指定了数据集中的“objectApiName”） |
| readBehavior | 指示是查询现有记录，还是查询包括已删除记录在内的所有记录。 如果未指定，默认行为是前者。 <br>允许的值：**query**（默认值）、**queryAll**。  | 否 |

> [!IMPORTANT]
> 任何自定义对象均需要 **API 名称** 的“__c”部分。

:::image type="content" source="media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png" alt-text="Salesforce 连接 API 名称列表":::

**示例：**

```json
"activities":[
    {
        "name": "CopyFromSalesforce",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Salesforce input dataset name>",
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
                "type": "SalesforceSource",
                "query": "SELECT Col_Currency__c, Col_Date__c, Col_Email__c FROM AllDataType__c"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

>[!NOTE]
>为了向后兼容：从 Salesforce 复制数据时，如果使用以前的“RelationalSource”类型副本，则当你看到切换到新的“SalesforceSource”类型的建议时，该源会继续工作。

### <a name="salesforce-as-a-sink-type"></a>将 Salesforce 用作接收器类型

要向 Salesforce 复制数据，请将复制活动中的接收器类型设置为“SalesforceSink”。 复制活动 **sink** 节支持以下属性。

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 复制活动接收器的 type 属性必须设置为 **SalesforceSink**。 | 是 |
| writeBehavior | 操作写入行为。<br/>允许的值为 **Insert** 和 **Upsert**。 | 否（默认值为 Insert） |
| externalIdFieldName | 更新插入操作的外部的 ID 字段名称。 指定的字段必须在 Salesforce 对象中定义为“外部 ID 字段”。 它相应的输入数据中不能有 NULL 值。 | 对于“Upsert”是必需的 |
| writeBatchSize | 每批中写入到 Salesforce 的数据行计数。 | 否（默认值为5,000） |
| ignoreNullValues | 指示是否忽略 NULL 值从输入数据期间写入操作。<br/>允许的值为 **true** 和 **false**。<br>- **True**：执行更新插入或更新操作时，保持目标对象中的数据不变。 插入在执行插入操作时定义的默认值。<br/>- **False**：执行更新插入或更新操作时，将目标对象中的数据更新为 NULL。 执行插入操作时插入 NULL 值。 | 否（默认值为 false） |
| maxConcurrentConnections |活动运行期间与数据存储建立的并发连接的上限。 仅在要限制并发连接时指定一个值。| 否 |

**示例：复制活动中的 Salesforce 接收器**

```json
"activities":[
    {
        "name": "CopyToSalesforce",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Salesforce output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SalesforceSink",
                "writeBehavior": "Upsert",
                "externalIdFieldName": "CustomerId__c",
                "writeBatchSize": 10000,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="query-tips"></a>查询提示

### <a name="retrieve-data-from-a-salesforce-report"></a>从 Salesforce 报表检索数据

可通过将查询指定为 `{call "<report name>"}` 从 Salesforce 报表检索数据。 例如 `"query": "{call \"TestReport\"}"`。

### <a name="retrieve-deleted-records-from-the-salesforce-recycle-bin"></a>从 Salesforce 回收站中检索删除的记录

若要从 Salesforce 回收站中查询软删除的记录，需要将 `readBehavior` 指定为 `queryAll`。 

### <a name="difference-between-soql-and-sql-query-syntax"></a>SOQL 与 SQL 查询语法之间的差异

从 Salesforce 中复制数据时，可以使用 SOQL 查询或 SQL 查询。 请注意，这两者具有不同的语法和功能支持，不要混用。 建议使用 Salesforce 原本就支持的 SOQL 查询。 下表列出了主要差异：

| 语法 | SOQL 模式 | SQL 模式 |
|:--- |:--- |:--- |
| 列选择 | 需要枚举要在查询中复制的字段，例如 `SELECT field1, filed2 FROM objectname` | 除了列选择之外，还支持 `SELECT *`。 |
| 引号 | 字段/对象名称不能用引号引起来。 | 字段/对象名称可以用引号引起来，例如 `SELECT "id" FROM "Account"` |
| 日期时间格式 |  请参考[此处的](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql_select_dateformats.htm)详细信息和下一部分中的示例。 | 请参考[此处的](/sql/odbc/reference/develop-app/date-time-and-timestamp-literals)详细信息和下一部分中的示例。 |
| 布尔值 | 表示为 `False` 和 `True`，例如 `SELECT … WHERE IsDeleted=True`。 | 表示为 0 或 1，例如 `SELECT … WHERE IsDeleted=1`。 |
| 列重命名 | 不支持。 | 支持，例如：`SELECT a AS b FROM …`。 |
| 关系 | 支持，例如 `Account_vod__r.nvs_Country__c`。 | 不支持。 |

### <a name="retrieve-data-by-using-a-where-clause-on-the-datetime-column"></a>使用 DateTime 列上的 where 子句检索数据

当指定 SOQL 或 SQL 查询时，请注意 DateTime 的格式差异。 例如：

* **SOQL 示例**：`SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= @{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')} AND LastModifiedDate < @{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}`
* **SQL 示例**：`SELECT * FROM Account WHERE LastModifiedDate >= {ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}'} AND LastModifiedDate < {ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'}`

### <a name="error-of-malformed_query-truncated"></a>MALFORMED_QUERY:Truncated 错误

如果遇到“MALFORMED_QUERY:Truncated”错误，通常是因为在数据中存在 JunctionIdList 类型列，而 Salesforce 在支持此类具有大量行的数据方面存在限制。 若要缓解这种情况，请尝试排除 JunctionIdList 列或限制要复制的行数（可以将其划分为多个复制活动运行）。

## <a name="data-type-mapping-for-salesforce"></a>Salesforce 的数据类型映射

从 Salesforce 复制数据时，使用以下映射从 Salesforce 数据类型内部映射到服务中的临时数据类型。 若要了解复制活动如何将源架构和数据类型映射到接收器，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

| Salesforce 数据类型 | 服务临时数据类型 |
|:--- |:--- |
| 自动编号 |String |
| 复选框 |布尔 |
| 货币 |小数 |
| Date |DateTime |
| 日期/时间 |DateTime |
| Email |String |
| ID |String |
| 查找关系 |String |
| 多选择列表 |String |
| Number |小数 |
| 百分比 |小数 |
| 电话 |String |
| 选择列表 |String |
| 文本 |String |
| 文本区域 |String |
| 文本区域（长型值） |String |
| 文本区域（丰富） |String |
| 文本（加密） |String |
| URL |String |

## <a name="lookup-activity-properties"></a>查找活动属性

若要了解有关属性的详细信息，请查看 [Lookup 活动](control-flow-lookup-activity.md)。


## <a name="next-steps"></a>后续步骤
有关复制活动支持作为源和接收器的数据存储的列表，请参阅[受支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。