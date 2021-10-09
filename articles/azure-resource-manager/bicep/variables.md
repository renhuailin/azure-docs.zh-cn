---
title: Bicep 中的变量
description: 说明如何在 Bicep 中定义变量
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 09/10/2021
ms.openlocfilehash: 040e40d20fe81bb72493f087c9d0583a911b1ee7
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124733338"
---
# <a name="variables-in-bicep"></a>Bicep 中的变量

本文介绍如何在 Bicep 文件中定义和使用变量。 变量用于简化 Bicep 文件开发。 请定义包含复杂表达式的变量，而不必在整个 Bicep 文件中重复使用复杂的表达式。 然后，在整个 Bicep 文件中根据需要使用该变量。

资源管理器会在启动部署操作之前解析变量。 在 Bicep 文件中所有使用该变量的位置，资源管理器都会将其替换为解析的值。

## <a name="define-variable"></a>定义变量

定义变量时，你不指定变量的[数据类型](data-types.md)。 而是提供一个值或模板表达式。 变量类型是从解析的值推断的。 以下示例将一个变量设置为字符串。

```bicep
var stringVar = 'example value'
```

构造变量时，你可以使用参数或其他变量中的值。

```bicep
param inputValue string = 'deployment Parameter'

var stringVar = 'myVariable'

var concatToVar =  '${stringVar}AddToVar'
var concatToParam = '${inputValue}AddToParam'
```

可以使用 [Bicep 函数](bicep-functions.md)来构造变量值。 [reference](bicep-functions-resource.md#reference) 和 [list](bicep-functions-resource.md#list) 函数在声明变量时有效。

以下示例创建一个字符串值作为存储帐户名称。 它使用多个 Bicep 函数来获取参数值，并将其连接到唯一字符串。

```bicep
var storageName = '${toLower(storageNamePrefix)}${uniqueString(resourceGroup().id)}'
```

以下示例不部署任何资源。 它演示如何声明不同类型的变量。

:::code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/variables/variables.bicep":::

可以使用循环来声明具有动态元素数的数组变量。 有关详细信息，请参阅 [Bicep 中的变量迭代](loop-variables.md)。

## <a name="use-variable"></a>使用变量

以下示例演示如何使用资源属性的变量。 通过提供变量的名称 `storageName` 来引用变量的值。

```bicep
param rgLocation string = resourceGroup().location
param storageNamePrefix string = 'STG'

var storageName = '${toLower(storageNamePrefix)}${uniqueString(resourceGroup().id)}'

resource demoAccount 'Microsoft.Storage/storageAccounts@2021-02-01' = {
  name: storageName
  location: rgLocation
  kind: 'Storage'
  sku: {
    name: 'Standard_LRS'
    tier: 'Standard'
  }
}

output stgOutput string = storageName
```

由于存储帐户名称必须使用小写字母，因此 `storageName` 变量使用 `toLower` 函数来使 `storageNamePrefix` 值成为小写。 `uniqueString` 函数从资源组 ID 创建唯一值。 这些值会连接到某个字符串。

## <a name="configuration-variables"></a>配置变量

可以定义变量来保存配置环境所需的相关值。 可以将变量定义为一个包含值的对象。 以下示例演示的对象包含的值适用于两个环境 - **test** 和 **prod**。在部署过程中传入这些值之一。

:::code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/variables/variablesconfigurations.bicep":::

## <a name="next-steps"></a>后续步骤

- 若要了解变量的可用属性，请参阅[了解 Bicep 文件的结构和语法](file.md)。
- 若要了解如何在变量声明中使用循环，请参阅 [Bicep 中的变量迭代](loop-variables.md)。
