---
title: 在 Bicep 中声明资源
description: 介绍如何在 Bicep 中声明要部署的资源。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 10/07/2021
ms.openlocfilehash: 4b3b355016057af00c361a118aed2728948768dd
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129659614"
---
# <a name="resource-declaration-in-bicep"></a>Bicep 中的资源声明

若要通过 Bicep 文件部署资源，请使用 `resource` 关键字添加资源声明。

## <a name="set-resource-type-and-version"></a>设置资源类型和版本

将资源添加到 Bicep 文件时，首先需设置资源类型和 API 版本。 这些值决定了可用于资源的其他属性。

下面的示例演示了如何为存储帐户设置资源类型和 API 版本。 示例不显示完整的资源声明。

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  ...
}
```

设置资源的符号名称。 在前面的示例中，符号名称为 `stg`。  符号名称与资源名称不同。 使用符号名称可以引用 Bicep 文件其他部分中的资源。 符号名称区分大小写。  符号名称可以包含字母、数字和 _；但不能以数字开头。

Bicep 不支持在 [Azure 资源管理器模板（ARM 模板）JSON](../templates/syntax.md) 中可用的 `apiProfile`。

## <a name="set-resource-name"></a>设置资源名称

每个资源都有一个名称。 设置资源名称时，请注意[资源名称的规则和限制](../management/resource-name-rules.md)。

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: 'examplestorage'
  ...
}
```

通常，你会将名称设置为参数，以便可以在部署过程中传入不同的值。

```bicep
@minLength(3)
@maxLength(24)
param storageAccountName string

resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageAccountName
  ...
}
```

## <a name="set-location"></a>设置位置

许多资源需要一个位置。 可以通过 intellisense 或[模板引用](/azure/templates/)确定资源是否需要位置。 以下示例添加用于存储帐户的位置参数。

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: 'examplestorage'
  location: 'eastus'
  ...
}
```

通常，你会将位置设置为参数，以便可以部署到不同的位置。

```bicep
param location string = resourceGroup().location

resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: 'examplestorage'
  location: location
  ...
}
```

不同位置支持的资源类型不一样。 若要获取 Azure 服务支持的位置，请参阅[可用产品（按区域）](https://azure.microsoft.com/global-infrastructure/services/)。  若要获取资源类型支持的位置，请使用 Azure PowerShell 或 Azure CLI。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes `
  | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az provider show \
  --namespace Microsoft.Batch \
  --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" \
  --out table
```

---

## <a name="set-tags"></a>设置标记

可以在部署期间对资源应用标记。 可以通过标记对部署的资源进行逻辑组织。 有关指定标记的不同方法的示例，请参阅 [ARM 模板标记](../management/tag-resources.md#arm-templates)。

## <a name="set-managed-identities-for-azure-resources"></a>为 Azure 资源设置托管标识

某些资源支持 [Azure 资源托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。 这些资源在资源声明的根级别具有标识对象。

可以使用系统分配的或用户分配的标识。

以下示例演示如何为 Azure Kubernetes 服务群集配置系统分配的标识。

```bicep
resource aks 'Microsoft.ContainerService/managedClusters@2020-09-01' = {
  name: clusterName
  location: location
  tags: tags
  identity: {
    type: 'SystemAssigned'
  }
```

下一个示例显示如何为虚拟机配置用户分配的身份。

```bicep
param userAssignedIdentity string

resource vm 'Microsoft.Compute/virtualMachines@2020-06-01' = {
  name: vmName
  location: location
  identity: {
    type: 'UserAssigned'
    userAssignedIdentities: {
      '${userAssignedIdentity}': {}
    }
  }
```

## <a name="set-resource-specific-properties"></a>设置特定于资源的属性

上述属性对于大多数资源类型都是通用的。 设置这些值后，需要设置特定于所部署的资源类型的属性。

使用 intellisense 或[模板参考](/azure/templates/)确定哪些属性可用以及哪些属性是必需的。 下面的示例将为存储帐户设置其余属性。

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: 'examplestorage'
  location: 'eastus'
  sku: {
    name: 'Standard_LRS'
    tier: 'Standard'
  }
  kind: 'StorageV2'
  properties: {
    accessTier: 'Hot'
  }
}
```

## <a name="set-resource-dependencies"></a>设置资源依赖项

部署资源时，可能需要确保某些资源在其他资源之前存在。 例如，在部署数据库之前，需要逻辑 SQL Server。 可通过将一个资源标记为依赖于其他资源来建立此关系。 资源部署顺序可能会受到两方面的影响：[隐式依赖关系](#implicit-dependency)和[显式依赖关系](#explicit-dependency)

Azure 资源管理器将评估资源之间的依赖关系，并根据其依赖顺序进行部署。 如果资源互不依赖，资源管理器将以并行方式部署资源。 只需为同一 Bicep 文件中部署的资源定义依赖关系。

### <a name="implicit-dependency"></a>隐式依赖项

当一个资源声明引用同一部署中的另一个资源时，会创建隐式依赖项。 例如，以下示例中的第二个资源定义引用了 dnsZone：

```bicep
resource dnsZone 'Microsoft.Network/dnszones@2018-05-01' = {
  name: 'myZone'
  location: 'global'
}

resource otherResource 'Microsoft.Example/examples@2020-06-01' = {
  name: 'exampleResource'
  properties: {
    // get read-only DNS zone property
    nameServers: dnsZone.properties.nameServers
  }
}
```

嵌套资源也隐式依赖于其包含资源。

```bicep
resource myParent 'My.Rp/parentType@2020-01-01' = {
  name: 'myParent'
  location: 'West US'

  // depends on 'myParent' implicitly
  resource myChild 'childType' = {
    name: 'myChild'
  }
}
```

存在隐式依赖项时，请勿添加显式依赖项。

有关嵌套资源的详细信息，请参阅[在 Bicep 中设置子资源的名称和类型](./child-resource-name-type.md)。

### <a name="explicit-dependency"></a>显式依赖关系

使用 `dependsOn` 属性声明显式依赖项。 该属性接受一组资源标识符，因此你可以指定多个依赖项。

以下示例显示了名为 `otherZone` 的 DNS 区域，该区域依赖于名为 `dnsZone` 的 DNS 区域：

```bicep
resource dnsZone 'Microsoft.Network/dnszones@2018-05-01' = {
  name: 'demoeZone1'
  location: 'global'
}

resource otherZone 'Microsoft.Network/dnszones@2018-05-01' = {
  name: 'demoZone2'
  location: 'global'
  dependsOn: [
    dnsZone
  ]
}
```

尽管你可能倾向于使用 `dependsOn` 来映射资源之间的关系，但请务必了解这么做的理由。 例如，若要记录资源的互连方式，使用 `dependsOn` 并不是合适的方法。 部署之后，你无法查询 `dependsOn` 元素中定义了哪些资源。 设置不必要的依赖关系会减慢部署速度，因为资源管理器无法并行部署这些资源。

尽管有时需要显式依赖关系，但这种情况非常罕见。 在大多数情况下，你可以使用一个符号名称来隐式表示资源之间的依赖关系。 如果发现自己设置了显式依赖项，应考虑是否可以将其删除。

### <a name="visualize-dependencies"></a>可视化依赖项

Visual Studio Code 提供了一个用于可视化依赖关系的工具。 在 Visual Studio Code 中打开 Bicep 文件，然后选择左上角的可视化工具按钮。  以下屏幕截图显示了虚拟机的依赖项。

:::image type="content" source="./media/resource-declaration/bicep-resource-visualizer.png" alt-text="Visual Studio Code Bicep 资源可视化工具的屏幕截图":::

## <a name="reference-existing-resources"></a>引用现有资源

若要引用当前 Bicep 文件之外的资源，请在资源声明中使用 `existing` 关键字。

使用 `existing` 关键字时，请提供资源的 `name`。 以下示例获取当前部署的资源组中的现有存储帐户。

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' existing = {
  name: 'examplestorage'
}

output blobEndpoint string = stg.properties.primaryEndpoints.blob
```

根据需要，可以设置 `scope` 属性以访问不同范围内的资源。 以下示例引用不同资源组中的现有存储帐户。

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' existing = {
  name: 'examplestorage'
  scope: resourceGroup(exampleRG)
}

output blobEndpoint string = stg.properties.primaryEndpoints.blob
```

有关设置范围的详细信息，请参阅 [Bicep 的范围函数](bicep-functions-scope.md)。

上述示例不部署存储帐户。 相反，你可以使用符号名称访问现有资源上的属性。

## <a name="next-steps"></a>后续步骤

- 若要按条件部署资源，请参阅 [Bicep 中的条件部署](./conditional-resource-deployment.md)。
