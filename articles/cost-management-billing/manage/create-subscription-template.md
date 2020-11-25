---
title: 使用 Azure 资源管理器模板以编程方式创建 Azure 订阅
description: 了解如何使用 Azure 资源管理器模板以编程方式创建 Azure 订阅。
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 11/17/2020
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 8cc27f5870d5289867b9e1e1760425a2015c35dc
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2020
ms.locfileid: "94886543"
---
# <a name="programmatically-create-azure-subscriptions-with-an-azure-resource-manager-template"></a>使用 Azure 资源管理器模板以编程方式创建 Azure 订阅

本文将帮助你使用 Azure 资源管理器模板（ARM 模板）以编程方式创建 Azure 订阅。 具有以下协议类型的计费帐户的 Azure 客户可以使用 ARM 模板创建订阅：

- 企业协议 (EA)
    - 有关详细信息，请参阅 [Azure 企业 REST API](ea-portal-rest-apis.md)
- Microsoft 客户协议 (MCA)
    - [Azure 计费 REST API](/rest/api/billing)
    - [Azure 订阅 API](/rest/api/subscription)
- Microsoft 合作伙伴协议 (MPA)
    - [Azure 计费 REST API](/rest/api/billing)
    - [Azure 订阅 API](/rest/api/subscription)

使用模板以编程方式创建 Azure 订阅时，该订阅受你从 Microsoft 或授权经销商处获取 Azure 服务时所签署协议的约束。 有关详细信息，请参阅 [Microsoft Azure 法律信息](https://azure.microsoft.com/support/legal/)。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="create-subscriptions-using-arm-templates"></a>使用 ARM 模板创建订阅

可使用 Azure 资源管理器模板（ARM 模板）创建订阅，这样你就能够自动执行生产/测试部署过程。 在以下示例中，使用 ARM 模板创建 Azure 订阅和 Azure 资源组。

## <a name="prerequisites"></a>先决条件

若要创建订阅，必须具有以下角色之一： 

- 发票科目上的 Azure 订阅所有者
- 发票科目上的 Azure 订阅参与者
- 发票科目上的 Azure 订阅创建者角色
- 计费对象信息或计费帐户上的 Azure 订阅所有者
- 计费对象信息或计费帐户上的 Azure 订阅参与者角色

有关详细信息，请参阅[订阅计费角色和任务](understand-mca-roles.md#subscription-billing-roles-and-tasks)。

此外，由于你要执行 ARM 模板部署，因此需要具有对根对象的写入权限。 若要在管理组下创建 ARM 部署，需要具有对管理组的写入权限。 此操作纯粹用于创建 ARM 部署。 如果创建了订阅，只能将其创建在 ARM 模板中指定的管理组中。

下面的示例使用 REST API。 目前，PowerShell 和 Azure CLI 不受支持。

## <a name="find-billing-accounts-that-you-have-access-to"></a>查找你有权访问的计费帐户

发出以下请求，以列出所有计费帐户。

### <a name="rest"></a>[REST](#tab/rest-getBillingAccounts)

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingaccounts/?api-version=2020-05-01
```

API 响应列出你有权访问的计费帐户。

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "billingProfiles": {
          "hasMoreResults": false
        },
        "displayName": "Contoso",
        "hasReadAccess": false
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}
```

使用 `displayName` 属性来标识要为其创建订阅的计费帐户。 确保帐户的 agreementType 为 MicrosoftCustomerAgreement。 复制帐户的 `name`。 例如，若要为 `Contoso` 计费帐户创建订阅，请复制 `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`。 将该值粘贴到某个位置，以便在下一步中使用它。

<!--
### [PowerShell](#tab/azure-powershell-getBillingAccounts)

we're still working on enabling PowerShell SDK for billing APIs. Check back soon.
-->

<!--
### [Azure CLI](#tab/azure-cli-getBillingAccounts)

we're still working on enabling CLI SDK for billing APIs. Check back soon.
-->

---

## <a name="find-billing-profiles--invoice-sections-to-create-subscriptions"></a>查找计费对象信息和发票科目以创建订阅

订阅费用在计费对象信息发票的某个科目中显示。 使用以下 API 获取你有权在其上创建 Azure 订阅的计费对象信息和发票科目的列表。

首先，获取有权访问的计费帐户下的计费对象信息的列表。

### <a name="rest"></a>[REST](#tab/rest-getBillingProfiles)

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingaccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingprofiles/?api-version=2020-05-01
```

API 响应列出你有权在其上创建订阅的所有计费对象信息：

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx",
      "name": "AW4F-xxxx-xxx-xxx",
      "properties": {
        "billingRelationshipType": "Direct",
        "billTo": {
          "addressLine1": "One Microsoft Way",
          "city": "Redmond",
          "companyName": "Contoso",
          "country": "US",
          "email": "kenny@contoso.com",
          "phoneNumber": "425xxxxxxx",
          "postalCode": "98052",
          "region": "WA"
        },
        "currency": "USD",
        "displayName": "Contoso Billing Profile",
        "enabledAzurePlans": [
          {
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
          },
          {
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
          }
        ],
        "hasReadAccess": true,
        "invoiceDay": 5,
        "invoiceEmailOptIn": false,
        "invoiceSections": {
          "hasMoreResults": false
        },
        "poNumber": "001",
        "spendingLimit": "Off",
        "status": "Active",
        "systemId": "AW4F-xxxx-xxx-xxx",
        "targetClouds": []
      },
      "type": "Microsoft.Billing/billingAccounts/billingProfiles"
    }
  ]
}
```

 复制 `id`，然后在计费对象信息下标识发票科目。 例如，复制 `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx` 并调用以下 API。

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoicesections?api-version=2020-05-01
```

### <a name="response"></a>响应

```json
{
  "totalCount": 1,
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx",
      "name": "SH3V-xxxx-xxx-xxx",
      "properties": {
        "displayName": "Development",
        "state": "Active",
        "systemId": "SH3V-xxxx-xxx-xxx"
      },
      "type": "Microsoft.Billing/billingAccounts/billingProfiles/invoiceSections"
    }
  ]
}
```

使用 `id` 属性来标识要为其创建订阅的发票科目。 复制整个字符串。 例如，`/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx`。 

<!--
### [PowerShell](#tab/azure-powershell-getBillingProfiles)

we're still working on enabling PowerShell SDK for billing APIs. Check back soon.

### [Azure CLI](#tab/azure-cli-getBillingProfiles)

we're still working on enabling CLI SDK for billing APIs. Check back soon.
-->

---

## <a name="create-a-subscription-and-resource-group-with-a-template"></a>使用模板创建订阅和资源组

以下 ARM 模板为 Development 发票科目创建名为 Dev Team subscription 的订阅 。 订阅将向 Contoso Billing Profile 计费对象信息收费，并在发票的 Development 科目显示 。 使用上一步中复制的计费范围：`/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx`。 

### <a name="request"></a>请求

```rest
PUT https://management.azure.com/providers/Microsoft.Resources/deployments/sampleTemplate?api-version=2019-10-01
```

### <a name="request-body"></a>请求正文

```json
{
  "properties":
    {
        "location": "westus",
        "properties": {
            "template": {
                "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                "contentVersion": "1.0.0.0",
                "parameters": {},
                "variables": {
                    "uniqueAliasName": "sampleAlias"
                },
                "resources": [
                    {
                        "type": "Microsoft.Resources/deployments",
                        "apiVersion": "2019-10-01",
                        "name": "sampleTemplate",
                        "location": "westus",
                        "properties": {
                            "expressionEvaluationOptions": {
                                "scope": "inner"
                            },
                            "mode": "Incremental",
                            "template": {
                                  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                                "contentVersion": "1.0.0.0",
                                "variables": {
                                    "uniqueAliasName": "sampleAlias"
                                },
                                "resources": [
                                    {
                                        "name": "[variables('uniqueAliasName')]",
                                        "type": "Microsoft.Subscription/aliases",
                                        "apiVersion": "2020-09-01",
                                        "properties": {
                                            "workLoad": "Production",
                                            "displayName": "Dev Team subscription",
                                            "billingScope": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx"
                                        },
                                        "dependsOn": [],
                                        "tags": {}
                                    }
                                ],
                                "outputs": {
                                    "subscriptionId": {
                                        "type": "string",
                                        "value": "[replace(reference(variables('uniqueAliasName')).subscriptionId, 'invalidrandom/', '')]"
                                    }
                                }
                            }
                        }
                    },
                    {
                        "name": "sampleOuterResource",
                        "type": "Microsoft.Resources/deployments",
                        "apiVersion": "2019-10-01",
                        "location": "westus",
                        "properties": {
                            "expressionEvaluationOptions": {
                                "scope": "inner"
                            },
                            "mode": "Incremental",
                            "parameters": {
                                "subscriptionId": {
                                    "value": "[reference('sampleTemplate').outputs.subscriptionId.value]"
                                }
                            },
                            "template": {
                                "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                            "contentVersion": "1.0.0.0",
                                "parameters": {
                                    "subscriptionId": {
                                        "type": "string"
                                    }
                                },
                                "variables": {},
                                "resources": [
                                    {
                                        "name": "sampleInnerResource",
                                        "type": "Microsoft.Resources/deployments",
                                        "subscriptionId": "[parameters('subscriptionId')]",
                                        "apiVersion": "2019-10-01",
                                        "location": "westus",
                                        "properties": {
                                            "expressionEvaluationOptions": {
                                                "scope": "inner"
                                            },
                                            "mode": "Incremental",
                                            "parameters": {},
                                            "template": {
                                                "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                                                "contentVersion": "1.0.0.0",
                                                "parameters": {},
                                                "variables": {},
                                                "resources": [
                                                    {
                                                        "type": "Microsoft.Resources/resourceGroups",
                                                        "apiVersion": "2020-05-01",
                                                        "location": "[deployment().location]",
                                                        "name": "sampleRG",
                                                        "properties": {},
                                                        "tags": {}
                                                    }
                                                ],
                                                "outputs": {}
                                            }
                                        }
                                    }
                                ],
                                "outputs": {}
                            }
                        }
                    }
                ],
                "outputs": {
                    "messageFromLinkedTemplate": {
                        "type": "string",
                        "value": "[reference('sampleTemplate').outputs.subscriptionId.value]"
                    }
                }
            },
            "mode": "Incremental"
        }
    }
}
```

### <a name="response"></a>响应

```json
{
  "id": "/providers/Microsoft.Resources/deployments/sampleTemplate",
  "name": "sampleTemplate",
  "type": "Microsoft.Resources/deployments",
  "location": "westus",
  "properties": {
    "templateHash": "16005880870587497948",
    "parameters": {},
    "mode": "Incremental",
    "provisioningState": "Accepted",
    "timestamp": "2020-10-07T19:06:34.110811Z",
    "duration": "PT0.1345459S",
    "correlationId": "2b57ddf6-7e27-42cb-90b4-90eeccd11a28",
    "providers": [
      {
        "namespace": "Microsoft.Resources",
        "resourceTypes": [
          {
            "resourceType": "deployments",
            "locations": [
              "westus"
            ]
          }
        ]
      }
    ],
    "dependencies": [
      {
        "dependsOn": [
          {
            "id": "/providers/Microsoft.Resources/deployments/sampleTemplate",
            "resourceType": "Microsoft.Resources/deployments",
            "resourceName": "anuragTemplate1"
          }
        ],
        "id": "/providers/Microsoft.Resources/deployments/sampleOuterResource",
        "resourceType": "Microsoft.Resources/deployments",
        "resourceName": "sampleOuterResource"
      }
    ]
  }
}
```

你可以获取部署状态以监视进度。

```json
GET https://management.azure.com/providers/Microsoft.Resources/deployments/sampleTemplate?api-version=2019-10-01
```

### <a name="response"></a>响应

```json
{
  "id": "/providers/Microsoft.Resources/deployments/sampleDeployment5",
  "name": "sampleDeployment5",
  "type": "Microsoft.Resources/deployments",
  "location": "westus",
  "properties": {
    "templateHash": "16005880870587497948",
    "parameters": {},
    "mode": "Incremental",
    "provisioningState": "Succeeded",
    "timestamp": "2020-10-07T19:07:20.8007311Z",
    "duration": "PT46.824466S",
    "correlationId": "2b57ddf6-7e27-42cb-90b4-90eeccd11a28",
    "providers": [
      {
        "namespace": "Microsoft.Resources",
        "resourceTypes": [
          {
            "resourceType": "deployments",
            "locations": [
              "westus"
            ]
          }
        ]
      }
    ],
    "dependencies": [
      {
        "dependsOn": [
          {
            "id": "/providers/Microsoft.Resources/deployments/sampleTemplate",
            "resourceType": "Microsoft.Resources/deployments",
            "resourceName": "sampleTemplate"
          }
        ],
        "id": "/providers/Microsoft.Resources/deployments/sampleOuterResource",
        "resourceType": "Microsoft.Resources/deployments",
        "resourceName": "sampleOuterResource"
      }
    ],
    "outputs": {
      "messageFromLinkedTemplate": {
        "type": "String",
        "value": "16edf959-11fd-48bb-9a46-85190963ead9"
      }
    },
    "outputResources": [
      {
        "id": "/providers/Microsoft.Subscription/aliases/sampleAlias"
      },
      {
        "id": "/subscriptions/16edf959-11fd-48bb-9a46-85190963ead9/resourceGroups/sampleRG"
      }
    ]
  }
}
```

在上一个示例中，你可以看到创建的订阅为 `16edf959-11fd-48bb-9a46-85190963ead9`，创建的 RG 为 `sampleRG`。

## <a name="next-steps"></a>后续步骤

* 创建订阅以后，即可将该权限授予其他用户和服务主体。 有关详细信息，请参阅[授予创建 Azure Enterprise 订阅（预览版）所需的访问权限](grant-access-to-create-subscription.md)。
* 要详细了解如何使用管理组管理大量订阅，请参阅[使用 Azure 管理组整理资源](../../governance/management-groups/overview.md)。
