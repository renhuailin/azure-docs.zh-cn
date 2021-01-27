---
title: 模板中的变量
description: 介绍如何在 Azure 资源管理器模板 (ARM 模板) 中定义变量。
ms.topic: conceptual
ms.date: 01/26/2021
ms.openlocfilehash: feecc4b5df77e6a3bf51294cb12aabf44899dde5
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/27/2021
ms.locfileid: "98874428"
---
# <a name="variables-in-arm-template"></a>ARM 模板中的变量

本文介绍如何在 Azure 资源管理器模板（ARM 模板）中定义和使用变量。 可以使用变量来简化模板。 可以定义一个包含复杂表达式的变量，而不必在整个模板中重复使用复杂表达式。 然后，可以在整个模板中根据需要引用该变量。

资源管理器会在启动部署操作之前解析变量。 只要在模板中使用变量，资源管理器就会将其替换为解析的值。

## <a name="define-variable"></a>定义变量

定义变量时，请提供可解析为 [数据类型](template-syntax.md#data-types)的值或模板表达式。 构造变量时，可以使用参数或其他变量中的值。

您可以在变量声明中使用 [模板函数](template-functions.md) ，但不能使用 [reference](template-functions-resource.md#reference) 函数或任何 [列表](template-functions-resource.md#list) 函数。 在解析变量时，这些函数获取资源的运行时状态，不能在部署之前执行。

以下示例介绍了变量定义。 它为存储帐户名称创建字符串值。 它使用多个模板函数来获取参数值，并将其连接到唯一字符串。

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

## <a name="use-variable"></a>使用变量

在模板中，可以使用 [variables](template-functions-deployment.md#variables) 函数引用参数值。 以下示例演示如何使用资源属性的变量。

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageName')]",
    ...
  }
]
```

## <a name="example-template"></a>示例模板

以下模板不部署任何资源。 它只是演示声明变量的一些方法。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variables.json":::

## <a name="configuration-variables"></a>配置变量

可以定义变量来保存配置环境所需的相关值。 可以将变量定义为一个包含值的对象。 下面的示例演示了一个对象，该对象包含两个环境的值： " **测试** " 和 " **生产**"。在部署过程中，您可以传入其中一个值。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variablesconfigurations.json":::

## <a name="next-steps"></a>后续步骤

* 若要了解变量的可用属性，请参阅[了解 ARM 模板的结构和语法](template-syntax.md)。
* 有关创建变量的建议，请参阅[最佳做法 - 变量](template-best-practices.md#variables)。
* 有关将安全规则分配到网络安全组的示例模板，请参阅 [网络安全规则](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) 和 [参数文件](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json)。
