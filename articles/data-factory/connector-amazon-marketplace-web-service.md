---
title: 从 AWS Marketplace 复制数据
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何通过在 Azure 数据工厂管道中使用复制活动，将数据从 Amazon Marketplace Web 服务复制到支持的接收器数据存储。
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.author: jianleishen
author: jianleishen
ms.custom: synapse
ms.date: 08/30/2021
ms.openlocfilehash: 7899827351529ef4a3d6235245403a25c3e80074
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123307191"
---
# <a name="copy-data-from-amazon-marketplace-web-service-using-azure-data-factory"></a>使用 Azure 数据工厂从 Amazon Marketplace Web 服务复制数据

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述了如何使用 Azure 数据工厂中的复制活动从 Amazon Marketplace Web 服务复制数据。 它是基于概述复制活动总体的[复制活动概述](copy-activity-overview.md)一文。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 Amazon Marketplace Web Service 连接器：

- 带有[支持的源或接收器矩阵](copy-activity-overview.md)的[复制活动](copy-activity-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)

可以将数据从 Amazon Marketplace Web 服务复制到任何支持的接收器数据存储。 有关复制活动支持作为源/接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

Azure 数据工厂提供内置的驱动程序用于启用连接，因此无需使用此连接器手动安装任何驱动程序。

## <a name="getting-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-amazon-marketplace-web-service-using-ui"></a>使用 UI 创建到 Amazon Marketplace Web Service 的链接服务

使用以下步骤在 Azure 门户 UI 中创建到 Amazon Marketplace Web Service 的链接服务。

1. 浏览到 Azure 数据工厂或 Synapse 工作区中的“管理”选项卡并选择“链接服务”，然后单击“新建”：

    # <a name="azure-data-factory"></a>[Azure 数据工厂](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="使用 Azure 数据工厂 UI 创建新链接服务的屏幕截图。":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="使用 Azure Synapse UI 创建新链接服务的屏幕截图。":::

2. 搜索 Amazon，然后选择 Amazon Marketplace Web Service 连接器。

   :::image type="content" source="media/connector-amazon-marketplace-web-service/amazon-marketplace-web-service-connector.png" alt-text="Amazon Marketplace Web Service 连接器的屏幕截图。":::    


1. 配置服务详细信息、测试连接并创建新的链接服务。

   :::image type="content" source="media/connector-amazon-marketplace-web-service/configure-amazon-marketplace-web-service-linked-service.png" alt-text="Amazon Marketplace Web Service 的链接服务配置的屏幕截图。":::

## <a name="connector-configuration-details"></a>连接器配置详细信息

对于特定于 Amazon Marketplace Web 服务连接器的数据工厂实体，以下部分提供了有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Amazon Marketplace Web 服务链接服务支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | type 属性必须设置为：AmazonMWS  | 是 |
| endpoint | Amazon MWS 服务器的终结点（即，mws.amazonservices.com）  | 是 |
| marketplaceID | 要从中检索数据的 Amazon Marketplace ID。 要从多个市场 ID 检索数据，请将其用逗号 (`,`) 分隔。 （即，A2EUQ1WTGCTBG2）  | 是 |
| sellerID | Amazon 卖方 ID。  | 是 |
| mwsAuthToken | Amazon MWS 身份验证令牌。 将此字段标记为 SecureString 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | 是 |
| accessKeyId | 用于访问数据的访问密钥 ID。  | 是 |
| secretKey | 用于访问数据的密钥。 将此字段标记为 SecureString 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | 是 |
| useEncryptedEndpoints | 指定是否使用 HTTPS 加密数据源终结点。 默认值为 true。  | 否 |
| useHostVerification | 指定通过 TLS 进行连接时是否要求服务器证书中的主机名与服务器的主机名匹配。 默认值为 true。  | 否 |
| usePeerVerification | 指定通过 TLS 进行连接时是否要验证服务器的标识。 默认值为 true。  | 否 |

**示例：**

```json
{
    "name": "AmazonMWSLinkedService",
    "properties": {
        "type": "AmazonMWS",
        "typeProperties": {
            "endpoint" : "mws.amazonservices.com",
            "marketplaceID" : "A2EUQ1WTGCTBG2",
            "sellerID" : "<sellerID>",
            "mwsAuthToken": {
                 "type": "SecureString",
                 "value": "<mwsAuthToken>"
            },
            "accessKeyId" : "<accessKeyId>",
            "secretKey": {
                 "type": "SecureString",
                 "value": "<secretKey>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供 Amazon Marketplace Web 服务数据集支持的属性列表。

要从 Amazon Marketplace Web 服务复制数据，请将数据集的 type 属性设置为“AmazonMWSObject”  。 支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为：AmazonMWSObject  | 是 |
| tableName | 表的名称。 | 否（如果指定了活动源中的“query”） |

**示例**

```json
{
    "name": "AmazonMWSDataset",
    "properties": {
        "type": "AmazonMWSObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<AmazonMWS linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}

```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 Amazon Marketplace Web 服务数据源支持的属性列表。

### <a name="amazon-mws-as-source"></a>Amazon MWS 作为源

要从 Amazon Marketplace Web 服务复制数据，请将复制活动中的源类型设置为“AmazonMWSSource”  。 复制活动 **source** 部分支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 复制活动 source 的 type 属性必须设置为：AmazonMWSSource  | 是 |
| query | 使用自定义 SQL 查询读取数据。 例如：`"SELECT * FROM Orders where  Amazon_Order_Id = 'xx'"`。 | 否（如果指定了数据集中的“tableName”） |

**示例：**

```json
"activities":[
    {
        "name": "CopyFromAmazonMWS",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<AmazonMWS input dataset name>",
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
                "type": "AmazonMWSSource",
                "query": "SELECT * FROM Orders where Amazon_Order_Id = 'xx'"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>查找活动属性

若要了解有关属性的详细信息，请查看 [Lookup 活动](control-flow-lookup-activity.md)。

## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂中复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
