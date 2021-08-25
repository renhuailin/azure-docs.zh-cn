---
title: 使用 Azure 数据工厂从 Xero 复制数据
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何通过在 Azure 数据工厂管道中使用复制活动，将数据从 Xero 复制到支持的接收器数据存储。
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 01/26/2021
ms.author: jianleishen
ms.openlocfilehash: 16722f7b9047ef5bf4e3d0f823a9fad2a2bcba08
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122637930"
---
# <a name="copy-data-from-xero-using-azure-data-factory"></a>使用 Azure 数据工厂从 Xero 复制数据

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述了如何使用 Azure 数据工厂中的复制活动从 Xero 复制数据。 它是基于概述复制活动总体的[复制活动概述](copy-activity-overview.md)一文。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 Xero 连接器：

- 带有[支持的源或接收器矩阵](copy-activity-overview.md)的[复制活动](copy-activity-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)

可以将数据从 Xero 复制到任何支持的接收器数据存储。 有关复制活动支持作为源/接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

具体而言，此 Xero 连接器支持：

- OAuth 2.0 和 OAuth 1.0 身份验证。 对于 OAuth 1.0，连接器支持 Xero [专用应用程序](https://developer.xero.com/documentation/getting-started/getting-started-guide)而非公共应用程序。
- 除“报告”外的所有 Xero 表（API 终结点）。

## <a name="getting-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

对于特定于 Xero 连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Xero 链接服务支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | type 属性必须设置为：**Xero** | 是 |
| connectionProperties | 定义如何连接到 Xero 的一组属性。 | 是 |
| 在 `connectionProperties` 下： | | |
| host | Xero 服务器的终结点 (`api.xero.com`)。  | 是 |
| authenticationType | 允许的值为 `OAuth_2.0` 和 `OAuth_1.0`。 | 是 |
| consumerKey | 对于 OAuth 2.0，请指定 Xero 应用程序的客户端 ID。<br>对于 OAuth 1.0，请指定与 Xero 应用程序关联的使用者密钥。<br>将此字段标记为 SecureString 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | 是 |
| privateKey | 对于 OAuth 2.0，请指定 Xero 应用程序的客户端密码。<br>对于 OAuth 1.0，若要指定为 Xero 专用应用程序生成的 .pem 文件中的私钥，请参阅[创建公钥/私钥对](https://developer.xero.com/documentation/auth-and-limits/create-publicprivate-key)。 注意：使用 `openssl genrsa -out privatekey.pem 512` 可生成数位为 512 的 privatekey.pem，不支持生成 1024 数位。 包括 .pem 文件中的所有文本，包括 Unix 行尾(\n)，请参见下面的示例。<br/><br>将此字段标记为 SecureString 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | 是 |
| tenantId | 与 Xero 应用程序关联的租户 ID。 适用于 OAuth 2.0 身份验证。<br>请参阅[查看你有权访问的租户](https://developer.xero.com/documentation/oauth2/auth-flow)部分，了解如何获取租户 ID。 | 是，适用于 OAuth 2.0 身份验证 |
| refreshToken | 适用于 OAuth 2.0 身份验证。<br/>OAuth 2.0 刷新令牌与  Xero 应用程序关联，用于刷新访问令牌；访问令牌在 30 分钟后过期。 了解 Xero 授权流的工作原理，并了解如何从[此文](https://developer.xero.com/documentation/oauth2/auth-flow)获取刷新令牌。 若要获取刷新令牌，必须请求 [offline_access 范围](https://developer.xero.com/documentation/oauth2/scopes)。 <br/>**已知限制**：请注意，在将刷新令牌用于访问令牌刷新后，Xero 会将其重置。 对于操作化工作负荷，在每个复制活动运行之前，需要设置有效的刷新令牌供 ADF 使用。<br/>将此字段标记为 SecureString 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | 是，适用于 OAuth 2.0 身份验证 |
| useEncryptedEndpoints | 指定是否使用 HTTPS 加密数据源终结点。 默认值为 true。  | 否 |
| useHostVerification | 指定通过 TLS 进行连接时是否要求服务器证书中的主机名与服务器的主机名匹配。 默认值为 true。  | 否 |
| usePeerVerification | 指定通过 TLS 进行连接时是否要验证服务器的标识。 默认值为 true。  | 否 |

示例：OAuth 2.0 身份验证

```json
{
    "name": "XeroLinkedService",
    "properties": {
        "type": "Xero",
        "typeProperties": {
            "connectionProperties": { 
                "host": "api.xero.com",
                "authenticationType":"OAuth_2.0", 
                "consumerKey": {
                    "type": "SecureString",
                    "value": "<client ID>"
                },
                "privateKey": {
                    "type": "SecureString",
                    "value": "<client secret>"
                },
                "tenantId": "<tenant ID>", 
                "refreshToken": {
                    "type": "SecureString",
                    "value": "<refresh token>"
                }, 
                "useEncryptedEndpoints": true, 
                "useHostVerification": true, 
                "usePeerVerification": true
            }            
        }
    }
}
```

示例：OAuth 1.0 身份验证

```json
{
    "name": "XeroLinkedService",
    "properties": {
        "type": "Xero",
        "typeProperties": {
            "connectionProperties": {
                "host": "api.xero.com", 
                "authenticationType":"OAuth_1.0", 
                "consumerKey": {
                    "type": "SecureString",
                    "value": "<consumer key>"
                },
                "privateKey": {
                    "type": "SecureString",
                    "value": "<private key>"
                }, 
                "useEncryptedEndpoints": true,
                "useHostVerification": true,
                "usePeerVerification": true
            }
        }
    }
}
```

**示例私钥值：**

包括 .pem 文件中的所有文本，包括 Unix 行尾(\n)。

```
"-----BEGIN RSA PRIVATE KEY-----\nMII***************************************************P\nbu****************************************************s\nU/****************************************************B\nA*****************************************************W\njH****************************************************e\nsx*****************************************************l\nq******************************************************X\nh*****************************************************i\nd*****************************************************s\nA*****************************************************dsfb\nN*****************************************************M\np*****************************************************Ly\nK*****************************************************Y=\n-----END RSA PRIVATE KEY-----"
```

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供 Xero 数据集支持的属性列表。

要从 Xero 复制数据，请将数据集的 type 属性设置为“XeroObject”  。 支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为：**XeroObject** | 是 |
| tableName | 表的名称。 | 否（如果指定了活动源中的“query”） |

**示例**

```json
{
    "name": "XeroDataset",
    "properties": {
        "type": "XeroObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Xero linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 Xero 数据源支持的属性列表。

### <a name="xero-as-source"></a>以 Xero 作为源

要从 Xero 复制数据，请将复制活动中的源类型设置为“XeroSource”  。 复制活动 **source** 部分支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 复制活动 source 的 type 属性必须设置为：**XeroSource** | 是 |
| query | 使用自定义 SQL 查询读取数据。 例如：`"SELECT * FROM Contacts"`。 | 否（如果指定了数据集中的“tableName”） |

**示例：**

```json
"activities":[
    {
        "name": "CopyFromXero",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Xero input dataset name>",
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
                "type": "XeroSource",
                "query": "SELECT * FROM Contacts"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

指定 Xero 查询时，请注意以下事项：

- 包含复杂项的表将拆分为多个表。 例如，银行事务具有复杂数据结构“LineItems”，因此银行事务数据将映射到表 `Bank_Transaction` 和 `Bank_Transaction_Line_Items`，并使用 `Bank_Transaction_ID` 作为外键将这些表链接在一起。

- 可通过两个架构使用 Xero 数据：`Minimal`（默认值）和 `Complete`。 完整架构包含先决条件调用表，这些表在进行所需的查询之前需要其他数据（例如 ID 列）。

以下表的最小架构和完整架构具有相同信息。 若要减少 API 调用数，请使用最小架构（默认值）。

- Bank_Transactions
- Contact_Groups 
- 联系人 
- Contacts_Sales_Tracking_Categories 
- Contacts_Phones 
- Contacts_Addresses 
- Contacts_Purchases_Tracking_Categories 
- Credit_Notes 
- Credit_Notes_Allocations 
- Expense_Claims 
- Expense_Claim_Validation_Errors
- 发票 
- Invoices_Credit_Notes
- Invoices_Prepayments 
- Invoices_Overpayments 
- Manual_Journals 
- Overpayments 
- Overpayments_Allocations 
- Prepayments 
- Prepayments_Allocations 
- Receipts 
- Receipt_Validation_Errors 
- Tracking_Categories

以下表只能使用完整架构查询：

- Complete.Bank_Transaction_Line_Items 
- Complete.Bank_Transaction_Line_Item_Tracking 
- Complete.Contact_Group_Contacts 
- Complete.Contacts_Contact_Persons 
- Complete.Credit_Note_Line_Items 
- Complete.Credit_Notes_Line_Items_Tracking 
- Complete.Expense_Claim_Payments 
- Complete.Expense_Claim_Receipts 
- Complete.Invoice_Line_Items 
- Complete.Invoices_Line_Items_Tracking
- Complete.Manual_Journal_Lines 
- Complete.Manual_Journal_Line_Tracking 
- Complete.Overpayment_Line_Items 
- Complete.Overpayment_Line_Items_Tracking 
- Complete.Prepayment_Line_Items 
- Complete.Prepayment_Line_Item_Tracking 
- Complete.Receipt_Line_Items 
- Complete.Receipt_Line_Item_Tracking 
- Complete.Tracking_Category_Options

## <a name="lookup-activity-properties"></a>Lookup 活动属性

若要了解有关属性的详细信息，请查看 [Lookup 活动](control-flow-lookup-activity.md)。


## <a name="next-steps"></a>后续步骤
有关复制活动支持的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
