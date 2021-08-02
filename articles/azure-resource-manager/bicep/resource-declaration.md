---
title: 在 Bicep 中声明资源
description: 介绍如何在 Bicep 中声明资源以进行部署。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: f62c790f1cb4f0613e17d2bbb3e4fc13e39d8e39
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111963460"
---
# <a name="resource-declaration-in-bicep"></a>Bicep 中的资源声明

若要通过 Bicep 文件部署资源，请使用关键字 `resource` 添加资源声明。

## <a name="set-resource-type-and-version"></a>设置资源类型和版本

将资源添加到 Bicep 文件时，请首先设置资源类型和 API 版本。 这些值决定了可用于资源的其他属性。

下面的示例演示了如何为存储帐户设置资源类型和 API 版本。 示例不显示完整的资源声明。

```bicep
resource myStorageAccount 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  ...
}
```

请设置资源的符号名称。 在前面的示例中，符号名称为 `myStorageAccount`。 可以使用任何值作为符号名称，但它不能与 Bicep 文件中的其他资源、参数或变量相同。 符号名称与资源名称不同。 使用符号名称可以轻松地引用 Bicep 文件其他部分中的资源。

Bicep 不支持 `apiProfile`，在 [Azure 资源管理器模板 (ARM 模板) JSON](../templates/syntax.md) 中提供。

## <a name="set-resource-name"></a>设置资源名称

每个资源都有一个名称。 设置资源名称时，请注意[资源名称的规则和限制](../management/resource-name-rules.md)。

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: 'examplestorage'
  ...
}
```

通常，你可以将名称设置为参数，以便在部署过程中传入不同的值。

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

通常，你可以将位置设置为参数，以便将其部署到不同的位置。

```bicep
param location string = resourceGroup().location

resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: 'examplestorage'
  location: location
  ...
}
```

不同位置支持的资源类型不一样。 若要获取 Azure 服务的支持位置，请参阅[按区域提供的产品](https://azure.microsoft.com/global-infrastructure/services/)。  若要获取资源类型支持的位置，请使用 Azure PowerShell 或 Azure CLI。

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

部署资源时，可能需要确保某些资源在其他资源之前存在。 例如，在部署数据库之前，需要逻辑 SQL Server。 可通过将一个资源标记为依赖于其他资源来建立此关系。 可以通过两种方式该改变资源部署的顺序：[隐式依赖项](#implicit-dependency)和[显式依赖项](#explicit-dependency)

Azure 资源管理器将评估资源之间的依赖关系，并根据其依赖顺序进行部署。 如果资源互不依赖，资源管理器将以并行方式部署资源。 只需为在同一 Bicep 文件中部署的资源定义依赖关系。

### <a name="implicit-dependency"></a>隐式依赖项

当一个资源声明引用表达式中的另一个资源声明的标识符时，将创建隐式依赖项。 例如，以下示例中的第二个资源定义引用了 dnsZone：

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

嵌套资源对其包含资源也具有隐式依赖关系。

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

有关详细信息，请参阅[在 Bicep 中设置子资源的名称和类型](./child-resource-name-type.md)。

### <a name="explicit-dependency"></a>显式依赖项

显式依赖项是在资源声明中通过 `dependsOn` 属性声明的。 属性接受资源标识符的一个数组。 下面是一个 DNS 区域的示例，具体取决于另一个显示依赖项：

```bicep
resource dnsZone 'Microsoft.Network/dnszones@2018-05-01' = {
  name: 'myZone'
  location: 'global'
}

resource otherZone 'Microsoft.Network/dnszones@2018-05-01' = {
  name: 'myZone'
  location: 'global'
  dependsOn: [
    dnsZone
  ]
}
```

尽管你可能倾向于使用 `dependsOn` 来映射资源之间的关系，但请务必了解这么做的理由。 例如，若要记录资源的互连方式，使用 `dependsOn` 并不是合适的方法。 部署之后，你无法查询 `dependsOn` 元素中定义了哪些资源。 设置不必要的依赖关系会减慢部署速度，因为资源管理器无法并行部署这些资源。

尽管有时需要显式依赖项，但很少需要它们。 在大多数情况下，你可以使用符号参考来表示资源之间的依赖关系。 如果你发现自己使用的是 dependsOn，则应考虑是否有办法删除它。

### <a name="visualize-dependencies"></a>可视化依赖项

Visual Studio Code 提供了一个用于可视化依赖项的工具。 在 Visual Studio Code 中打开一个 Bicep 文件，然后选择左上角的可视化工具按钮。  以下屏幕截图显示了在 Bicep 文件中定义的视觉对象的依赖关系。

:::image type="content" source="./media/resource-declaration/bicep-resource-visualizer.png" alt-text="Visual Studio Code Bicep 资源可视化工具的屏幕截图":::

## <a name="reference-existing-resources"></a>参考现有资源

你可以通过在资源声明中使用关键字 `existing`，从当前文件之外的资源中添加参考和访问运行时属性。 这等效于使用 ARM 模板[引用 () 函数](../templates/template-functions-resource.md#reference)。

使用关键字 `existing` 时，你必须提供资源的 `name`，还可以选择将 `scope` 属性设置为在不同范围内访问资源。 有关使用范围属性的详细信息，请参阅[资源范围](./deploy-to-resource-group.md)。

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' existing = {
  name: 'exampleStorage'
}

output blobEndpoint string = stg.properties.primaryEndpoints.blob
```

上述示例不部署存储帐户，但声明提供对现有资源属性的访问权限。 借助“stg”符号名称，你可以访问存储帐户的属性。

以下示例演示如何指定 `scope` 属性：

resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' existing = { name: 'exampleStorage' scope: resourceGroup(mySub, myRg) }

## <a name="next-steps"></a>后续步骤

- 若要有条件地部署资源，请参阅 [Bicep 中的条件部署](./conditional-resource-deployment.md)。