---
title: 通过最新的 API 以编程方式创建 Azure 企业协议订阅
description: 了解如何使用 REST API、Azure CLI、Azure PowerShell 和 Azure 资源管理器模板的最新版本以编程方式创建 Azure 企业协议订阅。
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 06/22/2021
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: b30856b5fe84f8c66e4029714e4bf39fca0470a9
ms.sourcegitcommit: 5fabdc2ee2eb0bd5b588411f922ec58bc0d45962
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2021
ms.locfileid: "112541295"
---
# <a name="programmatically-create-azure-enterprise-agreement-subscriptions-with-the-latest-apis"></a>通过最新的 API 以编程方式创建 Azure 企业协议订阅

本文帮助你使用最新的 API 版本以编程方式为 EA 计费帐户创建 Azure 企业协议 (EA) 订阅。 如果你仍在使用较旧的预览版本，请参阅[通过旧 API 以编程方式创建 Azure 订阅](programmatically-create-subscription-preview.md)。 

本文介绍如何使用 Azure 资源管理器以编程方式创建订阅。

以编程方式创建 Azure 订阅时，该订阅受你从 Microsoft 或授权经销商处获取 Azure 服务时所签署协议的约束。 有关详细信息，请参阅 [Microsoft Azure 法律信息](https://azure.microsoft.com/support/legal/)。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>先决条件

用户必须在注册帐户上具有所有者角色才能创建订阅。 可通过两种方式获取角色：

* 注册的企业管理员可以[将你设为帐户所有者](https://ea.azure.com/helpdocs/addNewAccount)（需要登录），这使得你成为注册帐户的所有者。
* 注册帐户的现有所有者可以[向你授予访问权限](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put)。 

若要使用服务主体 (SPN) 来创建 EA 订阅，则注册帐户的所有者必须[向该服务主体授予创建订阅的权限](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put)。 使用 SPN 来创建订阅时，请使用 [Azure Active Directory PowerShell](/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0&preserve-view=true ) 或 [Azure CLI](/cli/azure/ad/sp?view=azure-cli-latest&preserve-view=true#az_ad_sp_list) 将 Azure AD 应用程序注册的 ObjectId 用作服务主体 ObjectId。 有关 EA 角色分配 API 请求的详细信息，请参阅[将角色分配给 Azure 企业协议服务主体名称](assign-roles-azure-service-principals.md)。 本文包括可以分配给 SPN 的角色（和角色定义 ID）的列表。

  > [!NOTE]
  > - 确保使用正确的 API 版本为注册帐户授予所有者权限。 对于本文以及其中所述的 API，请使用 [2019-10-01-preview](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put) API。 
  > - 如果要进行迁移以使用较新的 API，以前使用 [2015-07-01 版本](grant-access-to-create-subscription.md)进行的配置不会自动转换为使用较新的 API。

## <a name="find-accounts-you-have-access-to"></a>查找有访问权限的帐户

将你添加到与帐户所有者关联的注册帐户后，Azure 通过帐户到注册关系确定订阅收费对象。 在该帐户下创建的所有订阅均向该帐户所在的 EA 注册收费。 若要创建订阅，必须传入拥有订阅所需的合约帐户和用户主体的相关值。

若要运行以下命令，必须登录到帐户所有者的主目录（默认在该目录中创建订阅）。

### <a name="rest"></a>[REST](#tab/rest)

请求列出你有权访问的所有注册帐户：

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingaccounts/?api-version=2020-05-01
```

API 响应列出你有权访问的所有注册帐户：

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/1234567",
      "name": "1234567",
      "properties": {
        "accountStatus": "Unknown",
        "accountType": "Enterprise",
        "agreementType": "EnterpriseAgreement",
        "soldTo": {
          "companyName": "Contoso",
          "country": "US "
        },
        "billingProfiles": {
          "hasMoreResults": false
        },
        "displayName": "Contoso",
        "enrollmentAccounts": [
          {
            "id": "/providers/Microsoft.Billing/billingAccounts/1234567/enrollmentAccounts/7654321",
            "name": "7654321",
            "type": "Microsoft.Billing/enrollmentAccounts",
            "properties": {
              "accountName": "Contoso",
              "accountOwnerEmail": "kenny@contoso.onmicrosoft.com",
              "costCenter": "Test",
              "isDevTest": false
            }
          }
        ],
        "hasReadAccess": false
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```

计费范围的值和 `id` 是相同的。 注册帐户的 `id` 是在发起订阅请求的计费范围。 了解该 ID 很重要，因为它是稍后要在本文中创建订阅时使用的必需参数。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

请使用 Azure CLI 或 REST API 来获取此值。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

请求列出你有权访问的所有注册帐户：

```azurecli-interactive
> az billing account list
```

响应列出你有权访问的所有注册帐户

```json
[
  {
    "accountStatus": "Unknown",
    "accountType": "Enterprise",
    "agreementType": "EnterpriseAgreement",
    "billingProfiles": {
      "hasMoreResults": false,
      "value": null
    },
    "departments": null,
    "displayName": "Contoso",
    "enrollmentAccounts": [
      {
        "accountName": "Contoso",
        "accountOwner": "",
        "costCenter": "Test",
        "department": null,
        "endDate": null,
        "id": "/providers/Microsoft.Billing/billingAccounts/1234567/enrollmentAccounts/7654321",
        "name": "7654321",
        "startDate": null,
        "status": null,
        "type": "Microsoft.Billing/enrollmentAccounts"
      }
    ],
    "enrollmentDetails": null,
    "hasReadAccess": false,
    "id": "/providers/Microsoft.Billing/billingAccounts/1234567",
    "name": "1234567",
    "soldTo": {
      "addressLine1": null,
      "addressLine2": null,
      "addressLine3": null,
      "city": null,
      "companyName": "Contoso",
      "country": "US ",
      "district": null,
      "email": null,
      "firstName": null,
      "lastName": null,
      "phoneNumber": null,
      "postalCode": null,
      "region": null
    },
    "type": "Microsoft.Billing/billingAccounts"
  },
```

计费范围的值和 `id` 是相同的。 注册帐户的 `id` 是在发起订阅请求的计费范围。 了解该 ID 很重要，因为它是稍后要在本文中创建订阅时使用的必需参数。

---

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>在特定注册帐户下创建订阅

以下示例在上一步选择的注册帐户中创建名为 Dev Team Subscription 的订阅。 

### <a name="rest"></a>[REST](#tab/rest)

调用 PUT API 来创建订阅创建请求/别名。

```json
PUT  https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01 
```

在请求正文中，作为 `billingScope` 提供其中一个 `enrollmentAccounts` 的 `id`。

```json 
{
  "properties": {
        "billingScope": "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321",
        "DisplayName": "Dev Team Subscription", //Subscription Display Name
        "Workload": "Production"
  }
}
```

`Workload` 的允许值为 `Production` 和 `DevTest`。

#### <a name="response"></a>响应

```json
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "type": "Microsoft.Subscription/aliases",
  "properties": {
    "subscriptionId": "b5bab918-e8a9-4c34-a2e2-ebc1b75b9d74",
    "provisioningState": "Accepted"
  }
}
```

可以对同一 URL 执行 GET 操作，以获取请求的状态。

### <a name="request"></a>请求

```json
GET https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01
```

### <a name="response"></a>响应

```json
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "type": "Microsoft.Subscription/aliases",
  "properties": {
    "subscriptionId": "b5bab918-e8a9-4c34-a2e2-ebc1b75b9d74",
    "provisioningState": "Succeeded"
  }
}
```

正在进行的状态在 `provisioningState` 下作为 `Accepted` 状态返回。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要安装包含 `New-AzSubscriptionAlias` cmdlet 的模块的最新版本，请运行 `Install-Module Az.Subscription`。 若要安装最新版本的 PowerShellGet，请参阅[获取 PowerShellGet 模块](/powershell/scripting/gallery/installing-psget)。

使用计费范围 `"/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321"` 运行以下 [New-AzSubscriptionAlias](/powershell/module/az.subscription/get-azsubscriptionalias) 命令。 

```azurepowershell-interactive
New-AzSubscriptionAlias -AliasName "sampleAlias" -SubscriptionName "Dev Team Subscription" -BillingScope "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321" -Workload "Production"
```

可以从命令中获取作为响应的一部分的 subscriptionId。

```azurepowershell
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "type": "Microsoft.Subscription/aliases",
  "properties": {
    "provisioningState": "Succeeded",
    "subscriptionId": "4921139b-ef1e-4370-a331-dd2229f4f510"
  }
}
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

首先，通过运行 `az extension add --name account` 和 `az extension add --name alias` 安装扩展。

运行以下 [az account alias create](/cli/azure/account/alias#az_account_alias_create) 命令，并提供其中一个 `enrollmentAccounts` 的 `billing-scope` 和 `id`。 

```azurecli-interactive
az account alias create --name "sampleAlias" --billing-scope "/providers/Microsoft.Billing/billingAccounts/1234567/enrollmentAccounts/654321" --display-name "Dev Team Subscription" --workload "Production"
```

可以从命令中获取作为响应的一部分的 subscriptionId。

```azurecli
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "properties": {
    "provisioningState": "Succeeded",
    "subscriptionId": "4921139b-ef1e-4370-a331-dd2229f4f510"
  },
  "type": "Microsoft.Subscription/aliases"
}
```

---

## <a name="use-arm-template"></a>使用 ARM 模板

上一部分介绍了如何使用 PowerShell、CLI 或 REST API 创建订阅。 如果需要自动创建订阅，请考虑使用 Azure 资源管理器模板（ARM 模板）。

以下模板可用于创建订阅。 对于 `billingScope`，请提供注册帐户 ID。 订阅已在根管理组中创建。 创建订阅后，可以将其移到另一个管理组。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "subscriptionAliasName": {
            "type": "string",
            "metadata": {
                "description": "Provide a name for the alias. This name will also be the display name of the subscription."
            }
        },
        "billingScope": {
            "type": "string",
            "metadata": {
                "description": "Provide the full resource ID of billing scope to use for subscription creation."
            }
        }
    },
    "resources": [
        {
            "scope": "/", 
            "name": "[parameters('subscriptionAliasName')]",
            "type": "Microsoft.Subscription/aliases",
            "apiVersion": "2020-09-01",
            "properties": {
                "workLoad": "Production",
                "displayName": "[parameters('subscriptionAliasName')]",
                "billingScope": "[parameters('billingScope')]"
            }
        }
    ],
    "outputs": {}
}
```

在[管理组级别](../../azure-resource-manager/templates/deploy-to-management-group.md)部署模板。

### <a name="rest"></a>[REST](#tab/rest)

```json
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/mg1/providers/Microsoft.Resources/deployments/exampledeployment?api-version=2020-06-01
```

包含请求正文：

```json
{
  "location": "eastus",
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json"
    },
    "parameters": {
      "subscriptionAliasName": {
        "value": "sampleAlias"
      },
      "billingScope": {
        "value": "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321"
      }
    },
    "mode": "Incremental"
  }
}
```

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -Name exampledeployment `
  -Location eastus `
  -ManagementGroupId mg1 `
  -TemplateFile azuredeploy.json `
  -subscriptionAliasName sampleAlias `
  -billingScope "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321"
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az deployment mg create \
  --name exampledeployment \
  --location eastus \
  --management-group-id mg1 \
  --template-file azuredeploy.json \
  --parameters subscriptionAliasName='sampleAlias' billingScope='/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321'
```

---

若要将订阅移动到新的管理组，请使用以下模板。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "targetMgId": {
            "type": "string",
            "metadata": {
                "description": "Provide the ID of the management group that you want to move the subscription to."
            }
        },
        "subscriptionId": {
            "type": "string",
            "metadata": {
                "description": "Provide the ID of the existing subscription to move."
            }
        }
    },
    "resources": [
        {
            "scope": "/",
            "type": "Microsoft.Management/managementGroups/subscriptions",
            "apiVersion": "2020-05-01",
            "name": "[concat(parameters('targetMgId'), '/', parameters('subscriptionId'))]",
            "properties": {
            }
        }
    ],
    "outputs": {}
}
```

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>对创建 Azure Enterprise 订阅的 API 限制

- 仅 Azure Enterprise 订阅使用此 API 进行创建。
- 每个注册帐户的订阅限制为 2000 个。 超过此限制后，只能在 Azure 门户中为该帐户创建更多订阅。 若要通过 API 创建更多订阅，请创建另一个注册帐户。 已取消、已删除和已传输的订阅数的上限为 2000。
- 不是帐户所有者但通过 Azure RBAC 添加到注册帐户的用户不能在 Azure 门户中创建订阅。
- 不能选择要在其中创建订阅的租户。 订阅始终在帐户所有者的主租户中进行创建。 若要将订阅移到不同的租户，请参阅[更改订阅租户](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)。


## <a name="next-steps"></a>后续步骤

* 创建订阅以后，即可将该权限授予其他用户和服务主体。 有关详细信息，请参阅[授予创建 Azure Enterprise 订阅（预览版）所需的访问权限](grant-access-to-create-subscription.md)。
* 要详细了解如何使用管理组管理大量订阅，请参阅[使用 Azure 管理组整理资源](../../governance/management-groups/overview.md)。
* 若要更改订阅的管理组，请参阅[移动订阅](../../governance/management-groups/manage.md#move-subscriptions)。
