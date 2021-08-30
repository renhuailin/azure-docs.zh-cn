---
title: 使用 Bicep 将资源部署到订阅
description: 介绍如何创建 Bicep 文件，以将资源部署到 Azure 订阅范围。 它展示了如何创建资源组。
ms.topic: conceptual
ms.date: 07/19/2021
ms.openlocfilehash: a683e0c46547be3ecb48a20a88f9a95a5b6c8ef8
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114453419"
---
# <a name="subscription-deployments-with-bicep-files"></a>采用 Bicep 文件的订阅部署

本文介绍如何在部署到订阅时使用 Bicep 设置范围。

若要简化资源管理，可在 Azure 订阅级别部署资源。 例如，可以将[策略](../../governance/policy/overview.md)和 [Azure 基于角色的访问控制 (Azure RBAC)](../../role-based-access-control/overview.md) 部署到你的订阅中，从而将它们应用于整个订阅。 还可以在订阅中创建资源组，然后将资源部署到订阅中的资源组。

> [!NOTE]
> 可在订阅级别部署中部署到 800 个不同的资源组。

## <a name="supported-resources"></a>支持的资源

并非所有资源类型都可以部署到订阅级别。 本部分列出了支持的资源类型。

对于 Azure 蓝图，请使用：

* [项目](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [蓝图](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAssignments](/azure/templates/microsoft.blueprint/blueprintassignments)
* [版本（蓝图）](/azure/templates/microsoft.blueprint/blueprints/versions)

对于 Azure 策略，请使用：

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [remediations](/azure/templates/microsoft.policyinsights/remediations)

对于 Azure 基于角色的访问控制 (Azure RBAC)，请使用：

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

对于部署到资源组的嵌套模板，请使用：

* [部署](/azure/templates/microsoft.resources/deployments)

若要创建新的资源组，请使用：

* [resourceGroups](/azure/templates/microsoft.resources/resourcegroups)

若要管理订阅，请使用：

* [顾问配置](/azure/templates/microsoft.advisor/configurations)
* [预算](/azure/templates/microsoft.consumption/budgets)
* [更改分析配置文件](/azure/templates/microsoft.changeanalysis/profile)
* [supportPlanTypes](/azure/templates/microsoft.addons/supportproviders/supportplantypes)
* [标记](/azure/templates/microsoft.resources/tags)

其他支持的类型包括：

* [scopeAssignments](/azure/templates/microsoft.managednetwork/scopeassignments)
* [eventSubscriptions](/azure/templates/microsoft.eventgrid/eventsubscriptions)
* [peerAsns](/azure/templates/microsoft.peering/2019-09-01-preview/peerasns)

## <a name="set-scope"></a>集作用域

若要将范围设置为订阅，请使用以下命令：

```bicep
targetScope = 'subscription'
```

## <a name="deployment-commands"></a>部署命令

若要部署到订阅，请使用订阅级别部署命名。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

对于 Azure CLI，请使用 [az deployment sub create](/cli/azure/deployment/sub#az_deployment_sub_create)。 以下示例会部署一个模板来创建资源组：

```azurecli-interactive
az deployment sub create \
  --name demoSubDeployment \
  --location centralus \
  --template-file main.bicep \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

对于 PowerShell 部署命令，请使用 [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) 或其别名 `New-AzSubscriptionDeployment`。 以下示例会部署一个模板来创建资源组：

```azurepowershell-interactive
New-AzSubscriptionDeployment `
  -Name demoSubDeployment `
  -Location centralus `
  -TemplateFile main.bicep `
  -rgName demoResourceGroup `
  -rgLocation centralus
```

---

有关部署命令和部署 ARM 模板的选项的更多详细信息，请参阅：

* [使用 ARM 模板和 Azure CLI 部署资源](deploy-cli.md)
* [使用 ARM 模板和 Azure PowerShell 部署资源](deploy-powershell.md)
* [从 Cloud Shell 部署 ARM 模板](deploy-cloud-shell.md)

## <a name="deployment-location-and-name"></a>部署位置和名称

对于订阅级别部署，必须为部署提供位置。 部署位置独立于部署的资源的位置。 部署位置指定何处存储部署数据。 [管理组](deploy-to-management-group.md)和[租户](deploy-to-tenant.md)部署也需要位置。 对于[资源组](deploy-to-resource-group.md)部署，资源组的位置用于存储部署数据。

可以为部署提供一个名称，也可以使用默认部署名称。 默认名称是模板文件的名称。 例如，部署名为“main.json”的模板会创建默认部署名称 main。

每个部署名称的位置不可变。 当某个位置中已有某个部署时，无法在另一位置创建同名的部署。 例如，如果在 **centralus** 中创建名为 **“deployment1”** 的订阅部署，则以后不能创建另一个名为 **“deployment1”** 但位置为 **“westus”** 的部署。 如果出现错误代码 `InvalidDeploymentLocation`，请使用其他名称或使用与该名称的以前部署相同的位置。

## <a name="deployment-scopes"></a>部署范围

部署到订阅时，可以将资源部署到：

* 操作中的目标订阅
* 租户中的任何订阅
* 该订阅或其他订阅中的资源组
* 订阅的租户

可以将[扩展资源](scope-extension-resources.md)的范围设置为与部署目标不同的范围。

部署模板的用户必须有权访问指定的作用域。

### <a name="scope-to-subscription"></a>订阅的范围

若要将资源部署到目标订阅，请使用关键字 `resource` 添加这些资源。

```bicep
targetScope = 'subscription'

// resource group created in target subscription
resource exampleResource 'Microsoft.Resources/resourceGroups@2020-10-01' = {
  ...
}
```

有关部署到订阅的示例，请参阅[创建资源组](#create-resource-groups)和[分配策略定义](#assign-policy-definition)。

若要将资源部署到与操作中的订阅不同的订阅，请添加一个[模块](modules.md)。 使用 [subscription 函数](bicep-functions-scope.md#subscription)设置 `scope` 属性。 将 `subscriptionId` 属性设置为要部署到的订阅的 ID。

```bicep
targetScope = 'subscription'

param otherSubscriptionID string

// module deployed at subscription level but in a different subscription
module exampleModule 'module.bicep' = {
  name: 'deployToDifferentSub'
  scope: subscription(otherSubscriptionID)
}
```

### <a name="scope-to-resource-group"></a>将范围限定于资源组

若要将资源部署到订阅中的资源组，请添加一个模块并设置其 `scope` 属性。 如果资源组已存在，请使用 [resourceGroup 函数](bicep-functions-scope.md#resourcegroup)设置范围值。 提供资源组名称。

```bicep
targetScope = 'subscription'

param resourceGroupName string

module exampleModule 'module.bicep' = {
  name: 'exampleModule'
  scope: resourceGroup(resourceGroupName)
}
```

如果资源组是在同一 Bicep 文件中创建的，请使用资源组的符号名称设置范围值。 有关将范围设置为符号名称的示例，请参阅[创建资源组和资源](#create-resource-group-and-resources)。

### <a name="scope-to-tenant"></a>将范围设定为租户

要在租户中创建资源，请添加一个模块。 使用 [tenant 函数](bicep-functions-scope.md#tenant)设置其 `scope` 属性。

部署模板的用户必须具有[在租户中进行部署所需的访问权限](deploy-to-tenant.md#required-access)。

下面的示例包括一个部署到租户的模块。

```bicep
targetScope = 'subscription'

// module deployed at tenant level
module exampleModule 'module.bicep' = {
  name: 'deployToTenant'
  scope: tenant()
}
```

可将范围限定为某些资源类型的 `tenant()`，而不是使用模块。 下面的示例在租户中部署管理组。

```bicep
targetScope = 'subscription'

param mgName string = 'mg-${uniqueString(newGuid())}'

// management group created at tenant
resource managementGroup 'Microsoft.Management/managementGroups@2020-05-01' = {
  scope: tenant()
  name: mgName
  properties: {}
}

output output string = mgName
```

有关详细信息，请参阅[管理组](deploy-to-management-group.md#management-group)。

## <a name="resource-groups"></a>资源组

### <a name="create-resource-groups"></a>创建资源组

若要创建资源组，请为该资源组定义具有名称和位置的 [Microsoft.Resources/resourceGroups](/azure/templates/microsoft.resources/allversions) 资源。

以下示例创建空的资源组。

```bicep
targetScope='subscription'

param resourceGroupName string
param resourceGroupLocation string

resource newRG 'Microsoft.Resources/resourceGroups@2021-01-01' = {
  name: resourceGroupName
  location: resourceGroupLocation
}
```

### <a name="create-resource-group-and-resources"></a>创建资源组和资源

若要创建资源组并向其部署资源，请添加一个模块。 该模块包含要部署到资源组的资源。 将该模块的范围设置为你创建的资源组的符号名称。 最多可部署到 800 个资源组。

以下示例将创建一个资源组，并向该资源组部署存储帐户。 请注意，模块的 `scope` 属性设置为 `newRG`，这是正在创建的资源组的符号名称。

```bicep
targetScope='subscription'

param resourceGroupName string
param resourceGroupLocation string
param storageName string
param storageLocation string

resource newRG 'Microsoft.Resources/resourceGroups@2021-01-01' = {
  name: resourceGroupName
  location: resourceGroupLocation
}

module storageAcct 'storage.bicep' = {
  name: 'storageModule'
  scope: newRG
  params: {
    storageLocation: storageLocation
    storageName: storageName
  }
}
```

模块使用名为 storage.bicep 的 Bicep 文件，其中包含以下内容：

```bicep
param storageLocation string
param storageName string

resource storageAcct 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageName
  location: storageLocation
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}
```

## <a name="azure-policy"></a>Azure Policy

### <a name="assign-policy-definition"></a>分配策略定义

以下示例将现有的策略定义分配到订阅。 如果策略定义使用参数，请将参数作为对象提供。 如果策略定义不使用参数，请使用默认的空对象。

```bicep
targetScope = 'subscription'

param policyDefinitionID string
param policyName string
param policyParameters object = {}

resource policyAssign 'Microsoft.Authorization/policyAssignments@2020-09-01' = {
  name: policyName
  properties: {
    policyDefinitionId: policyDefinitionID
    parameters: policyParameters
  }
}
```

### <a name="create-and-assign-policy-definitions"></a>创建和分配策略定义

可以在同一 Bicep 文件中[定义](../../governance/policy/concepts/definition-structure.md)和分配策略定义。

```bicep
targetScope = 'subscription'

resource locationPolicy 'Microsoft.Authorization/policyDefinitions@2020-09-01' = {
  name: 'locationpolicy'
  properties: {
    policyType: 'Custom'
    parameters: {}
    policyRule: {
      if: {
        field: 'location'
        equals: 'northeurope'
      }
      then: {
        effect: 'deny'
      }
    }
  }
}

resource locationRestrict 'Microsoft.Authorization/policyAssignments@2020-09-01' = {
  name: 'allowedLocation'
  properties: {
    policyDefinitionId: locationPolicy.id
  }
}
```

## <a name="access-control"></a>访问控制

若要了解如何分配角色，请参阅[使用 Azure 资源管理器模板添加 Azure 角色分配](../../role-based-access-control/role-assignments-template.md)。

以下示例创建一个资源组，对其应用锁定，并为主体分配一个角色。

```bicep
targetScope = 'subscription'

@description('Name of the resourceGroup to create')
param resourceGroupName string

@description('Location for the resourceGroup')
param resourceGroupLocation string

@description('principalId of the user that will be given contributor access to the resourceGroup')
param principalId string

@description('roleDefinition to apply to the resourceGroup - default is contributor')
param roleDefinitionId string = 'b24988ac-6180-42a0-ab88-20f7382dd24c'

@description('Unique name for the roleAssignment in the format of a guid')
param roleAssignmentName string = guid(principalId, roleDefinitionId, resourceGroupName)

var roleID = '/subscriptions/${subscription().subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/${roleDefinitionId}'

resource newResourceGroup 'Microsoft.Resources/resourceGroups@2019-10-01' = {
  name: resourceGroupName
  location: resourceGroupLocation
  properties: {}
}

module applyLock 'lock.bicep' = {
  name: 'applyLock'
  scope: newResourceGroup
}

module assignRole 'role.bicep' = {
  name: 'assignRBACRole'
  scope: newResourceGroup
  params: {
    principalId: principalId
    roleNameGuid: roleAssignmentName
    roleDefinitionId: roleID
  }
}
```

以下示例显示了要应用锁的模块：

```bicep
resource createRgLock 'Microsoft.Authorization/locks@2016-09-01' = {
  name: 'rgLock'
  properties: {
    level: 'CanNotDelete'
    notes: 'Resource group should not be deleted.'
  }
}
```

以下示例显示了要分配角色的模块：

```bicep
@description('The principal to assign the role to')
param principalId string

@description('A GUID used to identify the role assignment')
param roleNameGuid string = newGuid()

param roleDefinitionId string

resource roleNameGuid_resource 'Microsoft.Authorization/roleAssignments@2020-04-01-preview' = {
  name: roleNameGuid
  properties: {
    roleDefinitionId: roleDefinitionId
    principalId: principalId
  }
}
```

## <a name="next-steps"></a>后续步骤

若要了解其他范围，请参阅：

* [资源组部署](deploy-to-resource-group.md)
* [管理组部署](deploy-to-management-group.md)
* [租户部署](deploy-to-tenant.md)
