---
title: 模板中的输出
description: 介绍如何在 Azure 资源管理器模板（ARM 模板）中定义输出值。
ms.topic: conceptual
ms.date: 05/18/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d81499ca7a85f8652ee353cde2e8e1cee48d4bf4
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111960341"
---
# <a name="outputs-in-arm-templates"></a>ARM 模板中的输出

本文介绍如何在 Azure 资源管理器模板（ARM 模板）中定义输出值。 需要从部署的资源返回值时，可以使用输出。

每个输出值的格式必须能够解析为这些[数据类型](data-types.md)中的一种。

## <a name="define-output-values"></a>定义输出值

下面的示例展示了如何从已部署的资源返回属性。

向模板添加 `outputs` 节。 输出值获取公共 IP 地址的完全限定的域名。

```json
"outputs": {
  "hostname": {
    "type": "string",
    "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))).dnsSettings.fqdn]"
  },
}
```

如果你需要输出在名称中包含连字符的属性，请在该名称两侧使用方括号，而不要使用点表示法。 例如，请使用 `['property-name']` 而非 `.property-name`。

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

## <a name="conditional-output"></a>条件输出

可以使用 `condition` 元素有条件地返回值。 通常，如果你[有条件地部署](conditional-resource-deployment.md)了某个资源，则可以使用条件输出。 以下示例展示了如何根据是否部署了新的公共 IP 地址，有条件地返回公共 IP 地址的资源 ID：

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

有关条件输出的简单示例，请参阅[条件输出模板](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json)。

## <a name="dynamic-number-of-outputs"></a>动态输出数量

在某些情况下，创建模板时你不知道需要返回的值的实例数量。 你可以使用迭代输出返回可变数量的值。 添加 `copy` 元素以循环访问输出。

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

有关详细信息，请参阅 [ARM 模板中的输出迭代](copy-outputs.md)。

## <a name="linked-templates"></a>链接模板

可以使用[链接模板](linked-templates.md)来部署相关模板。 若要从链接模板中检索输出值，请在父模板中使用 [reference](template-functions-resource.md#reference) 函数。 父模板中的语法为：

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

[公共 IP 地址模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json)创建一个公共 IP 地址并输出资源 ID。 [负载均衡器模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json)链接到前面所述的模板。 创建负载平衡器时，它使用输出中的资源 ID。

## <a name="example-template"></a>示例模板

以下模板不部署任何资源。 它显示了返回不同类型的输出的一些方式。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/outputs.json":::

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

* 若要了解输出的可用属性，请参阅[了解 ARM 模板的结构和语法](./syntax.md)。