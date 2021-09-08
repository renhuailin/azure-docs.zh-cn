---
title: 使用 Bicep 将资源部署到资源组
description: 介绍如何通过 Bicep 文件部署资源。 它介绍如何将多个资源组作为目标。
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 57e6814a8d7acd5c35995e037c2fd0c1c6804819
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123426933"
---
# <a name="resource-group-deployments-with-bicep-files"></a>使用 Bicep 文件部署资源组

本文介绍如何在部署到资源组时使用 Bicep 设置范围。

## <a name="supported-resources"></a>支持的资源

可以将大多数资源部署到资源组。 有关可用资源的列表，请参阅 [ARM 模板参考](/azure/templates)。

## <a name="set-scope"></a>集作用域

默认情况下，Bicep 文件的范围限定为资源组。 如果希望显式设置范围，请使用以下内容：

```bicep
targetScope = 'resourceGroup'
```

但是，不需要将目标范围设置为资源组，因为默认使用该范围。

## <a name="deployment-commands"></a>部署命令

若要部署到资源组，请使用资源组部署命令。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

对于 Azure CLI，请使用 [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create)。 以下示例会部署一个模板来创建资源组：

```azurecli-interactive
az deployment group create \
  --name demoRGDeployment \
  --resource-group ExampleGroup \
  --template-file main.bicep \
  --parameters storageAccountType=Standard_GRS
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

对于 PowerShell 部署命令，请使用 [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)。 以下示例会部署一个模板来创建资源组：

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Name demoRGDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateFile main.bicep `
  -storageAccountType Standard_GRS `
```

---

有关部署命令和部署 ARM 模板的选项的更多详细信息，请参阅：

* [使用 ARM 模板和 Azure CLI 部署资源](deploy-cli.md)
* [使用 ARM 模板和 Azure PowerShell 部署资源](deploy-powershell.md)
* [从 Cloud Shell 部署 ARM 模板](deploy-cloud-shell.md)

## <a name="deployment-scopes"></a>部署范围

部署到资源组时，可以将资源部署到：

* 部署操作的目标资源组
* 同一订阅或其他订阅中的其他资源组
* 租户中的任何订阅
* 资源组的租户

可以将[扩展资源](scope-extension-resources.md)的范围设置为与部署目标不同的范围。

部署模板的用户必须有权访问指定的作用域。

本部分演示如何指定不同范围。 可以在单个模板中组合这些不同范围。

### <a name="scope-to-target-resource-group"></a>目标资源组的范围

若要将资源部署到目标资源组，请将这些资源添加到 Bicep 文件。

```bicep
// resource deployed to target resource group
resource exampleResource 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  ...
}
```

有关示例模板，请参阅[部署到目标资源组](#deploy-to-target-resource-group)。

### <a name="scope-to-different-resource-group"></a>将范围限定为其他资源组

要将资源部署到不是目标资源组的资源组，请添加[模块](modules.md)。 使用 [resourceGroup 函数](bicep-functions-scope.md#resourcegroup)设置该模块的 `scope` 属性。

如果资源组位于其他订阅中，请提供该订阅 ID 和资源组名称。 如果资源组与当前部署位于同一订阅，请仅提供资源组名称。 如果未在 [resourceGroup 函数](bicep-functions-scope.md#resourcegroup)中指定订阅，则使用当前订阅。

下面的示例显示了一个以其他订阅中的资源组为目标的模块。

```bicep
param otherResourceGroup string
param otherSubscriptionID string

// module deployed to different subscription and resource group
module exampleModule 'module.bicep' = {
  name: 'otherSubAndRG'
  scope: resourceGroup(otherSubscriptionID, otherResourceGroup)
}
```

下一示例显示了一个以同一订阅中的资源组为目标的模块。

```bicep
param otherResourceGroup string

// module deployed to resource group in the same subscription
module exampleModule 'module.bicep' = {
  name: 'otherRG'
  scope: resourceGroup(otherResourceGroup)
}
```

有关示例模板，请参阅[部署到多个资源组](#deploy-to-multiple-resource-groups)。

### <a name="scope-to-subscription"></a>订阅的范围

要将资源部署到订阅，请添加一个模块。 使用 [subscription 函数](bicep-functions-scope.md#subscription)设置其 `scope` 属性。

要部署到当前订阅，请使用不带参数的 subscription 函数。

```bicep

// module deployed at subscription level
module exampleModule 'module.bicep' = {
  name: 'deployToSub'
  scope: subscription()
}
```

要部署到其他订阅，请在 subscription 函数中指定该订阅 ID 作为参数。

```bicep
param otherSubscriptionID string

// module deployed at subscription level but in a different subscription
module exampleModule 'module.bicep' = {
  name: 'deployToSub'
  scope: subscription(otherSubscriptionID)
}
```

有关示例模板，请参阅[创建资源组](#create-resource-group)。

### <a name="scope-to-tenant"></a>将范围设定为租户

要在租户中创建资源，请添加一个模块。 使用 [tenant 函数](bicep-functions-scope.md#tenant)设置其 `scope` 属性。

部署模板的用户必须具有[在租户中进行部署所需的访问权限](deploy-to-tenant.md#required-access)。

下面的示例包括一个部署到租户的模块。

```bicep
// module deployed at tenant level
module exampleModule 'module.bicep' = {
  name: 'deployToTenant'
  scope: tenant()
}
```

可将范围限定为某些资源类型的 `tenant()`，而不是使用模块。 下面的示例在租户中部署管理组。

```bicep
param mgName string = 'mg-${uniqueString(newGuid())}'

// ManagementGroup deployed at tenant
resource managementGroup 'Microsoft.Management/managementGroups@2020-05-01' = {
  scope: tenant()
  name: mgName
  properties: {}
}

output output string = mgName
```

有关详细信息，请参阅[管理组](deploy-to-management-group.md#management-group)。

## <a name="deploy-to-target-resource-group"></a>部署到目标资源组

若要在目标资源组部署资源，请在模板的“资源”部分定义这些资源。 以下模板会在部署操作中指定的资源组中创建一个存储帐户。

:::code language="bicep" source="~/azure-docs-bicep-samples/samples/create-storage-account/azuredeploy.bicep":::

## <a name="deploy-to-multiple-resource-groups"></a>部署到多个资源组

可通过单个 Bicep 文件部署到多个资源组。

> [!NOTE]
> 在单个部署中可以部署到 800 个资源组。 通常情况下，此限制意味着，在嵌套或链接的部署中可以部署到为父模板指定的一个资源组和最多 799 个资源组。 但是，如果父模板仅包含嵌套或链接的模板，并且本身不部署任何资源，则在嵌套或链接的部署中最多可包含 800 个资源组。

以下示例部署两个存储帐户。 第一个存储帐户部署到在部署操作中指定的资源组。 第二个存储帐户部署到在 `secondResourceGroup` 和 `secondSubscriptionID` 参数中指定的资源组：

```bicep
@maxLength(11)
param storagePrefix string

param firstStorageLocation string = resourceGroup().location

param secondResourceGroup string
param secondSubscriptionID string = ''
param secondStorageLocation string

var firstStorageName = '${storagePrefix}${uniqueString(resourceGroup().id)}'
var secondStorageName = '${storagePrefix}${uniqueString(secondSubscriptionID, secondResourceGroup)}'

module firstStorageAcct 'storage.bicep' = {
  name: 'storageModule1'
  params: {
    storageLocation: firstStorageLocation
    storageName: firstStorageName
  }
}

module secondStorageAcct 'storage.bicep' = {
  name: 'storageModule2'
  scope: resourceGroup(secondSubscriptionID, secondResourceGroup)
  params: {
    storageLocation: secondStorageLocation
    storageName: secondStorageName
  }
}
```

这两个模块都使用名为 storage.bicep 的同一 Bicep 文件。

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

## <a name="create-resource-group"></a>创建资源组

可从资源组部署中切换到订阅级别并创建一个资源组。 以下模板会向目标资源组部署一个存储帐户，并在指定的订阅中创建一个新的资源组。

```bicep
@maxLength(11)
param storagePrefix string

param firstStorageLocation string = resourceGroup().location

param secondResourceGroup string
param secondSubscriptionID string = ''
param secondLocation string

var firstStorageName = '${storagePrefix}${uniqueString(resourceGroup().id)}'

// resource deployed to target resource group
module firstStorageAcct 'storage2.bicep' = {
  name: 'storageModule1'
  params: {
    storageLocation: firstStorageLocation
    storageName: firstStorageName
  }
}

// module deployed to subscription
module newRG 'resourceGroup.bicep' = {
  name: 'newResourceGroup'
  scope: subscription(secondSubscriptionID)
  params: {
    resourceGroupName: secondResourceGroup
    resourceGroupLocation: secondLocation
  }
}
```

上面的示例对创建新资源组的模块使用以下 Bicep 文件。

```bicep
targetScope='subscription'

param resourceGroupName string
param resourceGroupLocation string

resource newRG 'Microsoft.Resources/resourceGroups@2021-01-01' = {
  name: resourceGroupName
  location: resourceGroupLocation
}
```

## <a name="next-steps"></a>后续步骤

若要了解其他范围，请参阅：

* [订阅部署](deploy-to-subscription.md)
* [管理组部署](deploy-to-management-group.md)
* [租户部署](deploy-to-tenant.md)
