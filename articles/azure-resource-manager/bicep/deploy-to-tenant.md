---
title: 使用 Bicep 将资源部署到租户
description: 介绍如何在 Bicep 文件中的租户范围内部署资源。
ms.topic: conceptual
ms.date: 07/19/2021
ms.openlocfilehash: fe5b8a1a17af47570bb2399172866a1e720b0bab
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124793714"
---
# <a name="tenant-deployments-with-bicep-file"></a>使用 Bicep 文件的租户部署

随着组织的成熟，你可能需要在 Azure AD 租户中定义和分配[策略](../../governance/policy/overview.md)或 [Azure 基于角色的访问控制 (Azure RBAC)](../../role-based-access-control/overview.md)。 通过租户级模板，可以声明的方式在全局级别应用策略和分配角色。

### <a name="microsoft-learn"></a>Microsoft Learn

若要详细了解部署范围并获得实际操作的指导，请参阅“Microsoft Learn”上的[使用 Bicep 将资源部署到订阅、管理组和租户](/learn/modules/deploy-resources-scopes-bicep/)。

## <a name="supported-resources"></a>支持的资源

并非所有资源类型都可以部署到租户级别。 本部分列出了支持的资源类型。

对于 Azure 基于角色的访问控制 (Azure RBAC)，请使用：

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)

对于部署到管理组、订阅或资源组的嵌套模板，请使用：

* [部署](/azure/templates/microsoft.resources/deployments)

对于创建管理组，请使用：

* [managementGroups](/azure/templates/microsoft.management/managementgroups)

若要创建订阅，请使用：

* [aliases](/azure/templates/microsoft.subscription/aliases)

对于管理成本，请使用：

* [billingProfiles](/azure/templates/microsoft.billing/billingaccounts/billingprofiles)
* [instructions](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/instructions)
* [invoiceSections](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/invoicesections)

若要配置门户，请使用：

* [tenantConfigurations](/azure/templates/microsoft.portal/tenantconfigurations)

内置策略定义是租户级别的资源，但你无法在租户中部署自定义策略定义。 有关为资源分配内置策略定义的示例，请参阅 [tenantResourceId 示例](./bicep-functions-resource.md#tenantresourceid)。

## <a name="set-scope"></a>集作用域

若要将范围设置为租户，请使用：

```bicep
targetScope = 'tenant'
```

## <a name="required-access"></a>所需访问权限

部署模板的主体必须具有在租户范围中创建资源的权限。 该主体必须有权执行部署操作 (`Microsoft.Resources/deployments/*`) 和创建模板中定义的资源。 例如，若要创建管理组，主体必须在租户范围内具有参与者权限。 若要创建角色分配，主体则必须具有所有者权限。

Azure Active Directory 的全局管理员不自动拥有分配角色的权限。 若要在租户范围内实现模板部署，全局管理员必须执行以下步骤：

1. 提升帐户访问权限，使其自身可分配角色。 有关详细信息，请参阅[提升访问权限以管理所有 Azure 订阅和管理组](../../role-based-access-control/elevate-access-global-admin.md)。

1. 向需要部署模板的主体分配所有者或参与者角色。

   ```azurepowershell-interactive
   New-AzRoleAssignment -SignInName "[userId]" -Scope "/" -RoleDefinitionName "Owner"
   ```

   ```azurecli-interactive
   az role assignment create --assignee "[userId]" --scope "/" --role "Owner"
   ```

主体现已具有部署模板所需的权限。

## <a name="deployment-commands"></a>部署命令

用于租户部署的命令与资源组部署使用的命令不同。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

对于 Azure CLI，请使用 [az deployment tenant create](/cli/azure/deployment/tenant#az_deployment_tenant_create)：

```azurecli-interactive
az deployment tenant create \
  --name demoTenantDeployment \
  --location WestUS \
  --template-file main.bicep
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

对于 Azure PowerShell，请使用 [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment)。

```azurepowershell-interactive
New-AzTenantDeployment `
  -Name demoTenantDeployment `
  -Location "West US" `
  -TemplateFile main.bicep
```

---

有关部署命令和部署 ARM 模板的选项的更多详细信息，请参阅：

* [使用 ARM 模板和 Azure CLI 部署资源](deploy-cli.md)
* [使用 ARM 模板和 Azure PowerShell 部署资源](deploy-powershell.md)
* [从 Cloud Shell 部署 ARM 模板](deploy-cloud-shell.md)

## <a name="deployment-location-and-name"></a>部署位置和名称

对于租户级别的部署，必须提供部署位置。 部署位置独立于部署的资源的位置。 部署位置指定何处存储部署数据。 [订阅](deploy-to-subscription.md)和[管理组](deploy-to-management-group.md)部署也需要位置。 对于[资源组](deploy-to-resource-group.md)部署，资源组的位置用于存储部署数据。

可以为部署提供一个名称，也可以使用默认部署名称。 默认名称是模板文件的名称。 例如，部署一个名为 main.bicep 的文件会创建默认部署名称 main。

每个部署名称的位置不可变。 当某个位置中已有某个部署时，无法在另一位置创建同名的部署。 例如，如果在 centralus 中创建名为“deployment1”的租户部署，则以后不能创建另一个名为“deployment1”但位置为“westus”的部署   。 如果出现错误代码 `InvalidDeploymentLocation`，请使用其他名称或使用与该名称的以前部署相同的位置。

## <a name="deployment-scopes"></a>部署范围

部署到租户时，可以将资源部署到：

* 租户
* 租户中的管理组
* subscriptions
* 资源组

可以将[扩展资源](scope-extension-resources.md)的范围设置为与部署目标不同的范围。

部署模板的用户必须有权访问指定的作用域。

本部分演示如何指定不同范围。 可以在单个模板中组合这些不同范围。

### <a name="scope-to-tenant"></a>将范围设定为租户

在 Bicep 文件中定义的资源将应用于租户。

```bicep
targetScope = 'tenant'

// create resource at tenant
resource mgName_resource 'Microsoft.Management/managementGroups@2020-02-01' = {
  ...
}
```

### <a name="scope-to-management-group"></a>将范围设定为管理组

若要以租户中的管理组为目标，请添加[模块](modules.md)。 使用 [managementGroup 函数](bicep-functions-scope.md#managementgroup)设置其 `scope` 属性。 提供管理组名称。

```bicep
targetScope = 'tenant'

param managementGroupName string

// create resources at management group level
module  'module.bicep' = {
  name: 'deployToMG'
  scope: managementGroup(managementGroupName)
}
```

### <a name="scope-to-subscription"></a>订阅的范围

若要以租户中的订阅为目标，请添加一个模块。 使用 [subscription 函数](bicep-functions-scope.md#subscription)设置其 `scope` 属性。 提供订阅 ID。

```bicep
targetScope = 'tenant'

param subscriptionID string

// create resources at subscription level
module  'module.bicep' = {
  name: 'deployToSub'
  scope: subscription(subscriptionID)
}
```

### <a name="scope-to-resource-group"></a>将范围限定于资源组

若要以租户内的资源组为目标，请添加一个模块。 使用 [resourceGroup 函数](bicep-functions-scope.md#resourcegroup)设置其 `scope` 属性。 提供订阅 ID 和资源组名称。

```bicep
targetScope = 'tenant'

param resourceGroupName string
param subscriptionID string

// create resources at resource group level
module  'module.bicep' = {
  name: 'deployToRG'
  scope: resourceGroup(subscriptionID, resourceGroupName)
}
```

## <a name="create-management-group"></a>创建管理组

以下模板用于创建管理组。

```bicep
targetScope = 'tenant'
param mgName string = 'mg-${uniqueString(newGuid())}'

resource mgName_resource 'Microsoft.Management/managementGroups@2020-02-01' = {
  name: mgName
  properties: {}
}
```

如果帐户没有部署到租户的权限，仍然可以通过部署到另一个范围来创建管理组。 有关详细信息，请参阅[管理组](deploy-to-management-group.md#management-group)。

## <a name="assign-role"></a>分配角色

以下模板用于在租户范围内分配角色。

```bicep
targetScope = 'tenant'

@description('principalId if the user that will be given contributor access to the resourceGroup')
param principalId string

@description('roleDefinition for the assignment - default is owner')
param roleDefinitionId string = '8e3af657-a8ff-443c-a75c-2fe8c4bcb635'

var roleAssignmentName = guid(principalId, roleDefinitionId)

resource roleAssignment 'Microsoft.Authorization/roleAssignments@2020-03-01-preview' = {
  name: roleAssignmentName
  properties: {
    roleDefinitionId: tenantResourceId('Microsoft.Authorization/roleDefinitions', roleDefinitionId)
    principalId: principalId
  }
}
```

## <a name="next-steps"></a>后续步骤

若要了解其他范围，请参阅：

* [资源组部署](deploy-to-resource-group.md)
* [订阅部署](deploy-to-subscription.md)
* [管理组部署](deploy-to-management-group.md)
