---
title: 删除对委派的访问权限
description: 了解如何删除对委派给 Azure Lighthouse 服务提供商的资源的访问权限。
ms.date: 09/08/2021
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 16d92c58e08e06832781ff7d5095039cedbb344f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124736591"
---
# <a name="remove-access-to-a-delegation"></a>删除对委派的访问权限

在将客户的订阅或资源组委派给 [Azure Lighthouse](../overview.md) 的服务提供商后，可以根据需要删除委派。 删除委派后，先前向服务提供商租户中的用户授予的 [Azure 委托资源管理](../concepts/architecture.md)访问权限将不再适用。

只要用户具有适当的权限，客户租户或服务提供商租户中的用户就可以删除委派。

> [!TIP]
> 尽管本主题中只是提到了服务提供商和客户，但[管理多个租户的企业](../concepts/enterprise.md)也可以使用相同的过程。

## <a name="customers"></a>客户

客户租户中拥有具有 `Microsoft.Authorization/roleAssignments/write` 权限（如[所有者](../../role-based-access-control/built-in-roles.md#owner)）的角色的用户可以删除服务提供商对该订阅（或对该订阅中的资源组）的访问权限。 为此，用户可以访问 Azure 门户的[“服务提供商”页面](view-manage-service-providers.md#remove-service-provider-offers)，在“服务提供商产品/服务”屏幕上找到产品/服务，然后选择该产品/服务所在行的回收站图标。

确认删除后，服务提供商租户中的任何用户将无法访问之前委派的资源。

## <a name="service-providers"></a>服务提供商

如果管理租户中的用户被授予适用于客户资源的[托管服务注册分配删除角色](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)，则用户可以删除对委派资源的访问权限。 如果未将此角色分配给任何服务提供商用户，则只能由客户租户中的用户删除委派。

下面的示例显示了授予可包含在参数文件中的 [载入进程](onboard-customer.md) 期间的“托管服务注册分配删除角色”的分配：

```json
    "authorizations": [ 
        { 
            "principalId": "cfa7496e-a619-4a14-a740-85c5ad2063bb", 
            "principalIdDisplayName": "MSP Operators", 
            "roleDefinitionId": "91c1777a-f3dc-4fae-b103-61d183457e46" 
        } 
    ] 
```

[创建托管服务产品/服务](../../marketplace/plan-managed-service-offer.md)以发布到 Azure 市场时，还可以在“授权”中选择此角色。

具有此权限的用户可以通过以下的一种方法删除委托。

### <a name="azure-portal"></a>Azure 门户

1. 导航到[“我的客户”页面](view-manage-customers.md)。
2. 选择“委派”。
3. 找到要删除的委派，然后选择显示在其行中的回收站图标。

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory 

Login-AzAccount

# Select the subscription that is delegated - or contains the delegated resource group(s)

Select-AzSubscription -SubscriptionName "<subscriptionName>"

# Get the registration assignment

Get-AzManagedServicesAssignment -Scope "/subscriptions/{delegatedSubscriptionId}"

# Delete the registration assignment

Remove-AzManagedServicesAssignment -ResourceId "/subscriptions/{delegatedSubscriptionId}/providers/Microsoft.ManagedServices/registrationAssignments/{assignmentGuid}"
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory

az login

# Select the subscription that is delegated – or contains the delegated resource group(s)

az account set -s <subscriptionId/name>

# List registration assignments

az managedservices assignment list

# Delete the registration assignment

az managedservices assignment delete --assignment <id or full resourceId>
```

## <a name="next-steps"></a>后续步骤

- 了解 [Azure Lighthouse 体系结构](../concepts/architecture.md)。
- 在 Microsoft Azure 门户中转到“我的客户”，以[查看和管理客户](view-manage-customers.md)。
- 了解如何[更新先前的委托](update-delegation.md)。
