---
title: 从/向 SAP Cloud for Customer 复制数据
description: 了解如何使用 Azure 数据工厂或 Synapse Analytics 管道将数据从 SAP Cloud for Customer 复制到支持的接收器数据存储，（或）从支持的源数据存储复制到 SAP Cloud for Customer。
titleSuffix: Azure Data Factory & Azure Synapse
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: e3a09400b3ebf8fb0f49a46677f7d0dc815d4937
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124836035"
---
# <a name="copy-data-from-sap-cloud-for-customer-c4c-using-azure-data-factory-or-synapse-analytics"></a>使用 Azure 数据工厂或 Synapse Analytics 从 SAP Cloud for Customer (C4C) 复制数据

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何使用 Azure 数据工厂或 Synapse Analytics 管道中的复制活动从/向 SAP Cloud for Customer (C4C) 复制数据。 它是基于概述复制活动总体的[复制活动概述](copy-activity-overview.md)一文。

>[!TIP]
>若要了解该服务对 SAP 数据集成方案的总体支持，请参阅[使用 Azure 数据工厂进行 SAP 数据集成白皮书](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf)，其中包含有关每个 SAP 连接器的详细介绍、比较和指导。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 SAP Cloud for Customer 连接器：

- 包含[支持的源/接收器矩阵](copy-activity-overview.md)的 [Copy 活动](copy-activity-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)

可将数据从 SAP Cloud for Customer 复制到任何受支持的接收器数据存储，或者将数据从任何受支持的源数据存储复制到 SAP Cloud for Customer。 有关复制活动支持作为源/接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

具体而言，此连接器使该服务能够将数据从/向 SAP Cloud for Customer（包括 SAP Cloud for Sales、SAP Cloud for Service 和 SAP Cloud for Social Engagement 解决方案）复制数据。

## <a name="getting-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-sap-cloud-for-customer-using-ui"></a>使用 UI 创建到 SAP Cloud for Customer 的链接服务

使用以下步骤在 Azure 门户 UI 中创建一个到 SAP Cloud for Customer 的链接服务。

1. 浏览到 Azure 数据工厂或 Synapse 工作区中的“管理”选项卡并选择“链接服务”，然后单击“新建”：

    # <a name="azure-data-factory"></a>[Azure 数据工厂](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="使用 Azure 数据工厂 UI 创建新的链接服务。":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="使用 Azure Synapse UI 创建新的链接服务。":::

2. 搜索“SAP”并选择 SAP Cloud for Customer 连接器。

   :::image type="content" source="media/connector-sap-cloud-for-customer/sap-cloud-for-customer-connector.png" alt-text="选择 SAP Cloud for Customer 连接器。":::    


1. 配置服务详细信息，测试连接，然后创建新的链接服务。

   :::image type="content" source="media/connector-sap-cloud-for-customer/configure-sap-cloud-for-customer-linked-service.png" alt-text="配置到 SAP Cloud for Customer 的链接服务。":::

## <a name="connector-configuration-details"></a>连接器配置详细信息

对于特定于 SAP Cloud for Customer 连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

SAP Cloud for Customer 链接服务支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | type 属性必须设置为：**SapCloudForCustomer**。 | 是 |
| url | SAP C4C OData 服务的 URL。 | 是 |
| username | 指定用于连接到 SAP C4C 的用户名。 | 是 |
| password | 指定为 username 指定的用户帐户的密码。 将此字段标记为 SecureString 以安全地存储它，或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | 是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果未指定，则使用默认 Azure Integration Runtime。 | 否 |

**示例：**

```json
{
    "name": "SAPC4CLinkedService",
    "properties": {
        "type": "SapCloudForCustomer",
        "typeProperties": {
            "url": "https://<tenantname>.crm.ondemand.com/sap/c4c/odata/v1/c4codata/" ,
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

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供 SAP Cloud for Customer 数据集支持的属性列表。

若要从 SAP Cloud for Customer 复制数据，请将数据集的 type 属性设置为 **SapCloudForCustomerResource**。 支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为：**SapCloudForCustomerResource** |是 |
| path | 指定 SAP C4C OData 实体的路径。 |是 |

**示例：**

```json
{
    "name": "SAPC4CDataset",
    "properties": {
        "type": "SapCloudForCustomerResource",
        "typeProperties": {
            "path": "<path e.g. LeadCollection>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP C4C linked service>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 SAP Cloud for Customer 源支持的属性列表。

### <a name="sap-c4c-as-source"></a>以 SAP C4C 作为源

若要从 SAP Cloud for Customer 复制数据，请将复制活动中的源类型设置为 **SapCloudForCustomerSource**。 复制活动 **source** 部分支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | type 属性必须设置为：**SapCloudForCustomerSource**  | 是 |
| query | 指定用于读取数据的自定义 OData 查询。 | 否 |
| httpRequestTimeout | 用于获取响应的 HTTP 请求的超时 （TimeSpan 值）  。 该值是获取响应而不是读取响应数据的超时。 如果未指定，默认值为“00:30:00”（30 分钟）。 | 否 |

用于获取特定天数据的示例查询：`"query": "$filter=CreatedOn ge datetimeoffset'2017-07-31T10:02:06.4202620Z' and CreatedOn le datetimeoffset'2017-08-01T10:02:06.4202620Z'"`

**示例：**

```json
"activities":[
    {
        "name": "CopyFromSAPC4C",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP C4C input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SapCloudForCustomerSource",
                "query": "<custom query e.g. $top=10>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sap-c4c-as-sink"></a>以 SAP C4C 作为接收器

若要将数据复制到 SAP Cloud for Customer，请将复制活动中的接收器类型设置为 **SapCloudForCustomerSink**。 复制活动接收器部分中支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | type 属性必须设置为：**SapCloudForCustomerSink**  | 是 |
| writeBehavior | 操作的写入行为。 可以是“Insert”、“Update”。 | 不是。 默认值为“Insert”。 |
| writeBatchSize | 写入操作的批大小。 可获得最佳性能的批大小可能会因不同表或服务器而异。 | 不是。 默认值为 10。 |
| maxConcurrentConnections |活动运行期间与数据存储建立的并发连接的上限。 仅在要限制并发连接时指定一个值。| 否 |

**示例：**

```json
"activities":[
    {
        "name": "CopyToSapC4c",
        "type": "Copy",
        "inputs": [{
            "type": "DatasetReference",
            "referenceName": "<dataset type>"
        }],
        "outputs": [{
            "type": "DatasetReference",
            "referenceName": "SapC4cDataset"
        }],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SapCloudForCustomerSink",
                "writeBehavior": "Insert",
                "writeBatchSize": 30
            },
            "parallelCopies": 10,
            "dataIntegrationUnits": 4,
            "enableSkipIncompatibleRow": true,
            "redirectIncompatibleRowSettings": {
                "linkedServiceName": {
                    "referenceName": "ErrorLogBlobLinkedService",
                    "type": "LinkedServiceReference"
                },
                "path": "incompatiblerows"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-cloud-for-customer"></a>SAP Cloud for Customer 的数据类型映射

从 SAP Cloud for Customer 复制数据时，以下映射用于从 SAP Cloud for Customer 数据类型映射到服务内部使用的临时数据类型。 若要了解复制活动如何将源架构和数据类型映射到接收器，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

| SAP C4C OData 数据类型 | 临时服务数据类型 |
|:--- |:--- |
| Edm.Binary | Byte[] |
| Edm.Boolean | Bool |
| Edm.Byte | Byte[] |
| Edm.DateTime | DateTime |
| Edm.Decimal | 小数 |
| Edm.Double | Double |
| Edm.Single | Single |
| Edm.Guid | Guid |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | 字符串 |
| Edm.Time | TimeSpan |
| Edm.DateTimeOffset | DateTimeOffset |


## <a name="lookup-activity-properties"></a>Lookup 活动属性

若要了解有关属性的详细信息，请查看 [Lookup 活动](control-flow-lookup-activity.md)。

## <a name="next-steps"></a>后续步骤
有关复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
