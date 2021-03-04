---
title: 模板中的输出
description: 介绍如何在 Azure 资源管理器模板中定义输出值 (ARM 模板) 和 Bicep 文件。
ms.topic: conceptual
ms.date: 02/19/2021
ms.openlocfilehash: 2b6a6afa127bf43102103baadae576233843f00d
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102123405"
---
# <a name="outputs-in-arm-templates"></a>ARM 模板中的输出

本文介绍如何在 Azure 资源管理器模板中定义输出值 (ARM 模板) 和 Bicep 文件。 需要从部署的资源返回值时，可以使用输出。

每个输出值的格式必须解析为其中一种 [数据类型](data-types.md)。

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="define-output-values"></a>定义输出值

下面的示例演示如何从已部署的资源返回一个属性。

# <a name="json"></a>[JSON](#tab/json)

对于 JSON，请将 `outputs` 节添加到模板。 输出值获取公共 IP 地址的完全限定域名。

```json
"outputs": {
  "hostname": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))).dnsSettings.fqdn]"
    },
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

对于 Bicep，请使用 `output` 关键字。

在下面的示例中， `publicIP` 是 Bicep 文件中部署的公共 IP 地址的标识符。 输出值获取公共 IP 地址的完全限定域名。

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

---

如果需要输出在名称中包含连字符的属性，请在该名称两侧使用方括号，而不要使用点表示法。 例如，使用  `['property-name']` 而不是 `.property-name` 。

# <a name="json"></a>[JSON](#tab/json)

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "user": {
            "user-name": "Test Person"
        }
    },
    "resources": [
    ],
    "outputs": {
        "nameResult": {
            "type": "string",
            "value": "[variables('user')['user-name']]"
        }
    }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var user = {
  'user-name': 'Test Person'
}

output stringOutput string = user['user-name']
```

---

## <a name="conditional-output"></a>条件输出

您可以有条件地返回值。 通常，在有 [条件地部署](conditional-resource-deployment.md) 资源时使用条件输出。 以下示例展示了如何根据是否部署了新的公共 IP 地址，有条件地返回公共 IP 地址的资源 ID：

# <a name="json"></a>[JSON](#tab/json)

在 JSON 中，添加 `condition` 元素以定义是否返回输出。

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

若要在 Bicep 中指定条件输出，请使用 `?` 运算符。 下面的示例根据条件返回终结点 URL 或空字符串。

```bicep
param deployStorage bool = true
param storageName string
param location string = resourceGroup().location

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = if (deployStorage) {
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

output endpoint string = deployStorage ? sa.properties.primaryEndpoints.blob : ''
```

---

有关条件输出的简单示例，请参阅[条件输出模板](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json)。

## <a name="dynamic-number-of-outputs"></a>动态输出数量

在某些情况下，创建模板时你不知道需要返回的值的实例数量。 您可以通过使用迭代输出来返回可变数目的值。

# <a name="json"></a>[JSON](#tab/json)

在 JSON 中，添加 `copy` 元素以循环访问输出。

```json
"outputs": {
  "storageEndpoints": {
    "type": "array",
    "copy": {
      "count": "[parameters('storageCount')]",
      "input": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

迭代输出当前不适用于 Bicep。

---

有关详细信息，请参阅 [ARM 模板中的输出迭代](copy-outputs.md)。

## <a name="linked-templates"></a>链接模板

在 JSON 模板中，可以使用 [链接模板](linked-templates.md)部署相关模板。 若要从链接模板中检索输出值，请在父模板中使用 [reference](template-functions-resource.md#reference) 函数。 父模板中的语法为：

```json
"[reference('<deploymentName>').outputs.<propertyName>.value]"
```

以下示例演示如何通过从链接模板检索值，在负载均衡器上设置 IP 地址。

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

如果属性名称包含连字符，请在名称两侧使用方括号，而不要使用点表示法。

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs['resource-ID'].value]"
}
```

不能在[嵌套模板](linked-templates.md#nested-template)的 outputs 节中使用 `reference` 函数。 若要返回嵌套模板中部署的资源的值，请将嵌套模板转换为链接模板。

[公共 ip 地址模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json)创建公共 ip 地址并输出资源 ID。 [负载均衡器模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json)链接到前面的模板。 它在创建负载均衡器时使用输出中的资源 ID。

## <a name="modules"></a>模块

在 Bicep 文件中，可以使用模块部署相关模板。 若要检索模块的输出值，请使用以下语法：

```bicep
<module-name>.outputs.<property-name>
```

下面的示例演示如何通过从模块中检索值来设置负载均衡器上的 IP 地址。 模块的名称为 `publicIP` 。

```bicep
publicIPAddress: {
  id: publicIP.outputs.resourceID
}
```

## <a name="example-template"></a>示例模板

以下模板不部署任何资源。 其中显示了返回不同类型的输出的一些方法。

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/outputs.json":::

# <a name="bicep"></a>[Bicep](#tab/bicep)

Bicep 当前不支持循环。

:::code language="bicep" source="~/resourcemanager-templates/azure-resource-manager/outputs.bicep":::

---

## <a name="get-output-values"></a>获取输出值

部署成功时，将在部署结果中自动返回输出值。

若要从部署历史记录中获取输出值，可以使用脚本。

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

* 若要了解输出的可用属性，请参阅[了解 ARM 模板的结构和语法](template-syntax.md)。
