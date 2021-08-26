---
title: 在 Bicep 中定义变量的多个实例
description: 创建变量时使用 Bicep 变量循环进行迭代。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 429a15c222e47bab29b314b0d11f7e077281b635
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2021
ms.locfileid: "122635000"
---
# <a name="variable-iteration-in-bicep"></a>Bicep 中的变量迭代

本文展示了如何为 Bicep 文件中的变量创建多个值。 可向 `variables` 部分添加一个循环，并在部署过程中动态设置变量的项数。 还可避免在 Bicep 文件中重复语法。

还可以将 copy 用于[资源](loop-resources.md)、[资源中的属性](loop-properties.md)，以及[输出](loop-outputs.md)。

## <a name="syntax"></a>语法

可通过以下方式使用循环来声明多个变量：

- 循环访问数组。

  ```bicep
  var <variable-name> = [for <item> in <collection>: {
    <properties>
  }]

  ```

- 循环访问数组的元素。

  ```bicep
  var <variable-name> = [for <item>, <index> in <collection>: {
    <properties>
  }]
  ```

- 使用循环索引。

  ```bicep
  var <variable-name> = [for <index> in range(<start>, <stop>): {
    <properties>
  }]
  ```

## <a name="loop-limits"></a>循环限制

Bicep 文件的循环迭代不能为负数，也不能超过 800 次。 若要部署 Bicep 文件，请安装最新版本的 [Bicep 工具](install.md)。

## <a name="variable-iteration"></a>变量迭代

以下示例展示了如何创建字符串值的数组：

```bicep
param itemCount int = 5

var stringArray = [for i in range(0, itemCount): 'item${(i + 1)}']

output arrayResult array = stringArray
```

输出返回具有以下值的数组：

```json
[
  "item1",
  "item2",
  "item3",
  "item4",
  "item5"
]
```

下一个示例演示如何创建具有三个属性（`name`、`diskSizeGB` 和 `diskIndex`）的对象的数组。

```bicep
param itemCount int = 5

var objectArray = [for i in range(0, itemCount): {
  name: 'myDataDisk${(i + 1)}'
  diskSizeGB: '1'
  diskIndex: i
}]

output arrayResult array = objectArray
```

输出返回具有以下值的数组：

```json
[
  {
    "name": "myDataDisk1",
    "diskSizeGB": "1",
    "diskIndex": 0
  },
  {
    "name": "myDataDisk2",
    "diskSizeGB": "1",
    "diskIndex": 1
  },
  {
    "name": "myDataDisk3",
    "diskSizeGB": "1",
    "diskIndex": 2
  },
  {
    "name": "myDataDisk4",
    "diskSizeGB": "1",
    "diskIndex": 3
  },
  {
    "name": "myDataDisk5",
    "diskSizeGB": "1",
    "diskIndex": 4
  }
]
```

## <a name="example-templates"></a>示例模板

以下示例显示了为一个变量创建多个值的常见方案。

|模板  |说明  |
|---------|---------|
|[循环变量](https://github.com/Azure/azure-docs-bicep-samples/blob/main/bicep/multiple-instance/loopvariables.bicep) | 演示如何循环访问变量。 |
|[多项安全规则](https://github.com/Azure/azure-docs-bicep-samples/blob/main/bicep/multiple-instance/multiplesecurityrules.bicep) |将多个安全规则部署到网络安全组。 它从参数构造安全规则。 有关参数，请参阅[多个 NSG 参数文件](https://github.com/Azure/azure-docs-bicep-samples/blob/main/bicep/multiple-instance/multiplesecurityrules.parameters.json)。 |

## <a name="next-steps"></a>后续步骤

- 有关循环的其他用法，请参阅：
  - [Bicep 文件中的资源迭代](loop-resources.md)
  - [Bicep 文件中的属性迭代](loop-properties.md)
  - [Bicep 文件中的输出迭代](loop-outputs.md)
- 若要了解 Bicep 文件的各个部分，请参阅[了解 Bicep 文件的结构和语法](file.md)。
- 若要了解如何部署多个资源，请参阅[使用 Bicep 模块](modules.md)。
- 若要设置对循环中创建的资源的依赖关系，请参阅[设置资源依赖关系](./resource-declaration.md#set-resource-dependencies)。
- 若要了解如何使用 PowerShell 进行部署，请参阅[使用 Bicep 和 Azure PowerShell 部署资源](deploy-powershell.md)。
- 若要了解如何使用 Azure CLI 进行部署，请参阅[使用 Bicep 和 Azure CLI 部署资源](deploy-cli.md)。
