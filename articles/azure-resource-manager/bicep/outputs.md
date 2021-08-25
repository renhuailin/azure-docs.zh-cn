---
title: Bicep 中的输出
description: 介绍如何在 Bicep 中定义输出
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 1468f1a8a42f51dec1d195d39fe9096746c8193f
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2021
ms.locfileid: "122635137"
---
# <a name="outputs-in-bicep"></a>Bicep 中的输出

本文介绍如何在 Bicep 文件中定义输出值。 需要从部署的资源返回值时，可以使用输出。

每个输出值的格式必须能够解析为这些[数据类型](data-types.md)中的一种。

## <a name="define-output-values"></a>定义输出值

下面的示例演示如何使用 `output` 关键字从已部署的资源返回属性。

在下例中，`publicIP` 是在 Bicep 文件中部署的公共 IP 地址的标识符（符号名称）。 输出值获取公共 IP 地址的完全限定的域名。

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

如果你需要输出在名称中包含连字符的属性，请在该名称两侧使用方括号，而不要使用点表示法。 例如，请使用 `['property-name']` 而非 `.property-name`。

```bicep
var user = {
  'user-name': 'Test Person'
}

output stringOutput string = user['user-name']
```

## <a name="conditional-output"></a>条件输出

可以有条件地返回值。 通常，如果你[有条件地部署](conditional-resource-deployment.md)了某个资源，则可以使用条件输出。 以下示例展示了如何根据是否部署了新的公共 IP 地址，有条件地返回公共 IP 地址的资源 ID：

若要在 Bicep 中指定条件输出，请使用 `?` 运算符。 下面的示例根据条件返回终结点 URL 或空字符串。

```bicep
param deployStorage bool = true
param storageName string
param location string = resourceGroup().location

resource myStorageAccount 'Microsoft.Storage/storageAccounts@2019-06-01' = if (deployStorage) {
  name: storageName
  location: location
  kind: 'StorageV2'
  sku:{
    name:'Standard_LRS'
    tier: 'Standard'
  }
  properties: {
    accessTier: 'Hot'
  }
}

output endpoint string = deployStorage ? myStorageAccount.properties.primaryEndpoints.blob : ''
```

## <a name="dynamic-number-of-outputs"></a>动态输出数量

在某些情况下，创建模板时你不知道需要返回的值的实例数量。 你可以使用迭代输出返回可变数量的值。

在 Bicep 中，添加定义动态输出条件的 `for` 表达式。 下面的示例对数组进行循环访问。

```bicep
param nsgLocation string = resourceGroup().location
param nsgNames array = [
  'nsg1'
  'nsg2'
  'nsg3'
]

resource nsg 'Microsoft.Network/networkSecurityGroups@2020-06-01' = [for name in nsgNames: {
  name: name
  location: nsgLocation
}]

output nsgs array = [for (name, i) in nsgNames: {
  name: nsg[i].name
  resourceId: nsg[i].id
}]
```

还可以对一系列整数进行循环访问。 有关详细信息，请参阅 [Bicep 中的输出迭代](loop-outputs.md)。

## <a name="modules"></a>模块

可以使用模块部署相关模板。 若要从某个模块检索输出值，请使用以下语法：

```bicep
<module-name>.outputs.<property-name>
```

以下示例展示了如何通过从某个模块检索值，在负载均衡器上设置 IP 地址。 该模块的名称是 `publicIP`。

```bicep
publicIPAddress: {
  id: publicIP.outputs.resourceID
}
```

## <a name="example-template"></a>示例模板

以下模板不部署任何资源。 它显示了返回不同类型的输出的一些方式。

Bicep 当前不支持循环。

:::code language="bicep" source="~/azure-docs-bicep-samples/bicep/output.bicep":::

## <a name="get-output-values"></a>获取输出值

部署成功时，将在部署结果中自动返回输出值。

若要从部署历史记录中获取输出值，可以使用 Azure CLI 或 Azure PowerShell 脚本。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
(Get-AzResourceGroupDeployment `
  -ResourceGroupName <resource-group-name> `
  -Name <deployment-name>).Outputs.resourceID.value
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az deployment group show \
  -g <resource-group-name> \
  -n <deployment-name> \
  --query properties.outputs.resourceID.value
```

---

## <a name="next-steps"></a>后续步骤

* 若要了解输出的可用属性，请参阅[了解 Bicep 的结构和语法](./file.md)。
