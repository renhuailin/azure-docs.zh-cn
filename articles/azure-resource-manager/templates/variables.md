---
title: 模板中的变量
description: 介绍如何在 Azure 资源管理器模板（ARM 模板）中定义变量。
ms.topic: conceptual
ms.date: 06/24/2021
ms.openlocfilehash: 0a9b49c36f25295ab06e724773e1494c10eb1f08
ms.sourcegitcommit: 5be51a11c63f21e8d9a4d70663303104253ef19a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2021
ms.locfileid: "112893107"
---
# <a name="variables-in-arm-templates"></a>ARM 模板中的变量

本文介绍如何在 Azure 资源管理器模板（ARM 模板）中定义和使用变量。 可以使用变量来简化模板。 可以定义一个包含复杂表达式的变量，而不必在整个模板中重复使用复杂表达式。 然后，可以在整个模板中根据需要使用该变量。

资源管理器会在启动部署操作之前解析变量。 只要在模板中使用变量，资源管理器就会将其替换为解析的值。

## <a name="define-variable"></a>定义变量

定义变量时，你不指定变量的[数据类型](data-types.md)。 而是提供一个值或模板表达式。 变量类型是从解析的值推断的。 以下示例将一个变量设置为字符串。

```json
"variables": {
  "stringVar": "example value"
},
```

若要构造此变量，可以使用参数或其他变量的值。

```json
"parameters": {
  "inputValue": {
    "defaultValue": "deployment parameter",
    "type": "string"
  }
},
"variables": {
  "stringVar": "myVariable",
  "concatToVar": "[concat(variables('stringVar'), '-addtovar') ]",
  "concatToParam": "[concat(parameters('inputValue'), '-addtoparam')]"
}
```

你可以使用[模板函数](template-functions.md)来构造变量值。

以下示例创建一个字符串值作为存储帐户名称。 它使用多个模板函数来获取参数值，并将其连接到唯一字符串。

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

不能在变量声明中使用 [reference](template-functions-resource.md#reference) 函数或任何 [list](template-functions-resource.md#list) 函数。 在解析变量时，这些函数获取资源的运行时状态，不能在部署之前执行。

## <a name="use-variable"></a>使用变量

以下示例演示如何使用资源属性的变量。

若要引用变量的值，请使用 [variables](template-functions-deployment.md#variables) 函数。

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageName')]",
    ...
  }
]
```

## <a name="example-template"></a>示例模板

以下模板不部署任何资源。 它显示了声明各种类型的变量的一些方法。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variables.json":::

## <a name="configuration-variables"></a>配置变量

可以定义变量来保存配置环境所需的相关值。 可以将变量定义为一个包含值的对象。 以下示例演示的对象包含的值适用于两个环境 - **test** 和 **prod**。在部署过程中传入这些值之一。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variablesconfigurations.json":::

## <a name="next-steps"></a>后续步骤

* 若要了解变量的可用属性，请参阅[了解 ARM 模板的结构和语法](./syntax.md)。
* 有关创建变量的建议，请参阅[最佳做法 - 变量](./best-practices.md#variables)。
* 有关用于将安全规则分配到网络安全组的示例模板，请参阅[网络安全规则](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json)和[参数文件](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json)。