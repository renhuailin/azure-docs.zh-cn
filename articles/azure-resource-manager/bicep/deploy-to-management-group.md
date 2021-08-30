---
title: 使用 Bicep 将资源部署到管理组
description: 介绍如何创建一个在管理组范围内部署资源的 Bicep 文件。
ms.topic: conceptual
ms.date: 07/19/2021
ms.openlocfilehash: afa4a0f266eb7720a569df123c9828fd151d21e0
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114453593"
---
# <a name="management-group-deployments-with-bicep-files"></a>使用 Bicep 文件进行管理组部署

本文介绍如何在部署到管理组时使用 Bicep 来设置范围。

随着组织的不断发展，可以部署 Bicep 文件来创建管理组级别的资源。 例如，你可能需要为管理组定义和分配[策略](../../governance/policy/overview.md)或 [Azure 基于角色的访问控制 (Azure RBAC)](../../role-based-access-control/overview.md)。 使用管理组级别的模板，可以声明方式在管理组级别应用策略和分配角色。

## <a name="supported-resources"></a>支持的资源

并非所有资源类型都可以部署到管理组级别。 本部分列出了支持的资源类型。

对于 Azure 蓝图，请使用：

* [项目](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [蓝图](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAssignments](/azure/templates/microsoft.blueprint/blueprintassignments)
* [versions](/azure/templates/microsoft.blueprint/blueprints/versions)

对于 Azure Policy，请使用：

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [remediations](/azure/templates/microsoft.policyinsights/remediations)

对于 Azure 基于角色的访问控制 (Azure RBAC)，请使用：

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

对于部署到订阅或资源组的嵌套模板，请使用：

* [部署](/azure/templates/microsoft.resources/deployments)

若要管理资源，请使用：

* [标记](/azure/templates/microsoft.resources/tags)

管理组是租户级资源。 但你可以将新管理组的范围设置为租户，从而在管理组部署中创建管理组。 请参阅[管理组](#management-group)。

## <a name="set-scope"></a>集作用域

若要将范围设置为管理组，请使用：

```bicep
targetScope = 'managementGroup'
```

## <a name="deployment-commands"></a>部署命令

若要部署到管理组，请使用管理组部署命令。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

对于 Azure CLI，请使用 [az deployment mg create](/cli/azure/deployment/mg#az_deployment_mg_create)：

```azurecli-interactive
az deployment mg create \
  --name demoMGDeployment \
  --location WestUS \
  --management-group-id myMG \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

对于 Azure PowerShell，请使用 [New-AzManagementGroupDeployment](/powershell/module/az.resources/new-azmanagementgroupdeployment)。

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -Name demoMGDeployment `
  -Location "West US" `
  -ManagementGroupId "myMG" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

---

有关部署命令和部署 ARM 模板的选项的更多详细信息，请参阅：

* [使用 ARM 模板和 Azure CLI 部署资源](deploy-cli.md)
* [使用 ARM 模板和 Azure PowerShell 部署资源](deploy-powershell.md)
* [从 Cloud Shell 部署 ARM 模板](deploy-cloud-shell.md)

## <a name="deployment-location-and-name"></a>部署位置和名称

对于管理组级别部署，必须为部署提供位置。 部署位置独立于部署的资源的位置。 部署位置指定何处存储部署数据。 [订阅](deploy-to-subscription.md)和[租户](deploy-to-tenant.md)部署也需要位置。 对于[资源组](deploy-to-resource-group.md)部署，资源组的位置用于存储部署数据。

可以为部署提供一个名称，也可以使用默认部署名称。 默认名称是模板文件的名称。 例如，部署名为“main.bicep”的模板会创建默认部署名称 main。

每个部署名称的位置不可变。 当某个位置中已有某个部署时，无法在另一位置创建同名的部署。 例如，如果在 centralus 中创建名为“deployment1”的管理组部署，则以后不能创建另一个名为“deployment1”但位置为“westus”的部署   。 如果出现错误代码 `InvalidDeploymentLocation`，请使用其他名称或使用与该名称的以前部署相同的位置。

## <a name="deployment-scopes"></a>部署范围

部署到管理组时，可以将资源部署到：

* 操作的目标管理组
* 租户中的另一个管理组
* 管理组中的订阅
* 管理组中的资源组
* 资源组的租户

可以将[扩展资源](scope-extension-resources.md)的范围设置为与部署目标不同的范围。

部署模板的用户必须有权访问指定的作用域。

### <a name="scope-to-management-group"></a>将范围设定为管理组

若要将资源部署到目标管理组，请添加那些具有关键字 `resource` 的资源。

```bicep
targetScope = 'managementGroup'

// policy definition created in the management group
resource policyDefinition 'Microsoft.Authorization/policyDefinitions@2019-09-01' = {
  ...
}
```

若要以其他管理组作为目标，请添加[模块](modules.md)。 使用 [managementGroup 函数](bicep-functions-scope.md#managementgroup)设置 `scope` 属性。 提供管理组名称。

```bicep
targetScope = 'managementGroup'

param otherManagementGroupName string

// module deployed at management group level but in a different management group
module exampleModule 'module.bicep' = {
  name: 'deployToDifferentMG'
  scope: managementGroup(otherManagementGroupName)
}
```

### <a name="scope-to-subscription"></a>订阅的范围

还可以将管理组中的订阅作为目标。 部署模板的用户必须有权访问指定的作用域。

若要以管理组中的订阅为目标，请添加模块。 使用 [subscription 函数](bicep-functions-scope.md#subscription)设置 `scope` 属性。 提供订阅 ID。

```bicep
targetScope = 'managementGroup'

param subscriptionID string

// module deployed to subscription in the management group
module exampleModule 'module.bicep' = {
  name: 'deployToSub'
  scope: subscription(subscriptionID)
}
```

### <a name="scope-to-resource-group"></a>将范围限定于资源组

还可以将管理组中的资源组作为目标。 部署模板的用户必须有权访问指定的作用域。

若要以管理组中的资源组为目标，请添加模块。 使用 [resourceGroup 函数](bicep-functions-scope.md#resourcegroup)设置 `scope` 属性。  提供订阅 ID 和资源组名称。

```bicep
targetScope = 'managementGroup'

param subscriptionID string
param resourceGroupName string

// module deployed to resource group in the management group
module exampleModule 'module.bicep' = {
  name: 'deployToRG'
  scope: resourceGroup(subscriptionID, resourceGroupName)
}
```

### <a name="scope-to-tenant"></a>将范围设定为租户

要在租户中创建资源，请添加一个模块。 使用 [tenant 函数](bicep-functions-scope.md#tenant)设置其 `scope` 属性。 部署模板的用户必须具有[在租户中进行部署所需的访问权限](deploy-to-tenant.md#required-access)。

```bicep
targetScope = 'managementGroup'

// module deployed at tenant level
module exampleModule 'module.bicep' = {
  name: 'deployToTenant'
  scope: tenant()
}
```

或者，可将某些资源类型（如管理组）的范围设置为 `/`。 下一部分将介绍如何创建新的管理组。

## <a name="management-group"></a>管理组

若要在管理组部署中创建管理组，则必须将管理组的范围设置为 `/`。

下面的示例在根管理组中创建了一个新的管理组。

```bicep
targetScope = 'managementGroup'

param mgName string = 'mg-${uniqueString(newGuid())}'

resource newMG 'Microsoft.Management/managementGroups@2020-05-01' = {
  scope: tenant()
  name: mgName
  properties: {}
}

output newManagementGroup string = mgName
```

下一个示例将在指定为父级的管理组中创建一个新管理组。

```bicep
targetScope = 'managementGroup'

param mgName string = 'mg-${uniqueString(newGuid())}'
param parentMGName string

resource newMG 'Microsoft.Management/managementGroups@2020-05-01' = {
  scope: tenant()
  name: mgName
  properties: {
    details: {
      parent: {
        id: parentMG.id
      }
    }
  }
}

resource parentMG 'Microsoft.Management/managementGroups@2020-05-01' existing = {
  name: parentMGName
  scope: tenant()
}

output newManagementGroup string = mgName
```

## <a name="subscriptions"></a>订阅

若要使用 ARM 模板在管理组中创建新的 Azure 订阅，请参阅：

* [以编程方式创建 Azure 企业协议订阅](../../cost-management-billing/manage/programmatically-create-subscription-enterprise-agreement.md)
* [以编程方式为 Microsoft 客户协议创建 Azure 订阅](../../cost-management-billing/manage/programmatically-create-subscription-microsoft-customer-agreement.md)
* [以编程方式为 Microsoft 合作伙伴协议创建 Azure 订阅](../../cost-management-billing/manage/programmatically-create-subscription-microsoft-partner-agreement.md)

若要部署一个会将现有 Azure 订阅移至新管理组的模板，请参阅[在 ARM 模板中移动订阅](../../governance/management-groups/manage.md#move-subscriptions-in-arm-template)

## <a name="azure-policy"></a>Azure Policy

部署到管理组的自定义策略定义是管理组的扩展。 若要获取自定义策略定义的 ID，请使用 [extensionResourceId()](./bicep-functions-resource.md#extensionresourceid) 函数。 内置策略定义是租户级别资源。 若要获取内置策略定义的 ID，请使用 [tenantResourceId](./bicep-functions-resource.md#tenantresourceid) 函数。

下面的示例演示如何[定义](../../governance/policy/concepts/definition-structure.md)管理组级别策略，并对其进行分配。

```bicep
targetScope = 'managementGroup'

@description('An array of the allowed locations, all other locations will be denied by the created policy.')
param allowedLocations array = [
  'australiaeast'
  'australiasoutheast'
  'australiacentral'
]

resource policyDefinition 'Microsoft.Authorization/policyDefinitions@2019-09-01' = {
  name: 'locationRestriction'
  properties: {
    policyType: 'Custom'
    mode: 'All'
    parameters: {}
    policyRule: {
      if: {
        not: {
          field: 'location'
          in: allowedLocations
        }
      }
      then: {
        effect: 'deny'
      }
    }
  }
}

resource policyAssignment 'Microsoft.Authorization/policyAssignments@2019-09-01' = {
  name: 'locationAssignment'
  properties: {
    policyDefinitionId: policyDefinition.id
  }
}
```

## <a name="next-steps"></a>后续步骤

若要了解其他范围，请参阅：

* [资源组部署](deploy-to-resource-group.md)
* [订阅部署](deploy-to-subscription.md)
* [租户部署](deploy-to-tenant.md)
