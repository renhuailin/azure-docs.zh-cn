---
title: 在 Bicep 中定义变量的多个实例
description: 创建变量时使用 Bicep 变量循环进行迭代。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 08/30/2021
ms.openlocfilehash: bf182379c9cc10db11e451f908df552a16520b45
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123225197"
---
# <a name="variable-iteration-in-bicep"></a>Bicep 中的变量迭代

本文展示了如何为 Bicep 文件中的变量创建多个值。 可以在 `variables` 声明中添加循环，并动态设置变量的项数。 避免在 Bicep 文件中重复使用语法。

还可以将复制与 [modules](loop-modules.md)、[resources](loop-resources.md)、[资源中的 properties](loop-properties.md) 和 [outputs](loop-outputs.md) 配合使用。

## <a name="syntax"></a>语法

可通过以下方式使用循环来声明多个变量：

- 使用循环索引。

  ```bicep
  var <variable-name> = [for <index> in range(<start>, <stop>): {
    <properties>
  }]
  ```

  有关详细信息，请参阅[循环索引](#loop-index)。

- 循环访问数组。

  ```bicep
  var <variable-name> = [for <item> in <collection>: {
    <properties>
  }]

  ```

  有关详细信息，请参阅[循环数组](#loop-array)。

- 循环访问数组和索引。

  ```bicep
  var <variable-name> = [for <item>, <index> in <collection>: {
    <properties>
  }]
  ```

## <a name="loop-limits"></a>循环限制

Bicep 文件的循环迭代不能为负数，也不能超过 800 次。 

## <a name="loop-index"></a>循环索引

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

## <a name="loop-array"></a>循环数组

以下示例循环访问以参数形式传入的数组。 变量根据参数以所需格式构造对象。

```bicep
@description('An array that contains objects with properties for the security rules.')
param securityRules array = [
  {
    name: 'RDPAllow'
    description: 'allow RDP connections'
    direction: 'Inbound'
    priority: 100
    sourceAddressPrefix: '*'
    destinationAddressPrefix: '10.0.0.0/24'
    sourcePortRange: '*'
    destinationPortRange: '3389'
    access: 'Allow'
    protocol: 'Tcp'
  }
  {
    name: 'HTTPAllow'
    description: 'allow HTTP connections'
    direction: 'Inbound'
    priority: 200
    sourceAddressPrefix: '*'
    destinationAddressPrefix: '10.0.1.0/24'
    sourcePortRange: '*'
    destinationPortRange: '80'
    access: 'Allow'
    protocol: 'Tcp'
  }
]


var securityRulesVar = [for rule in securityRules: {
  name: rule.name
  properties: {
    description: rule.description
    priority: rule.priority
    protocol: rule.protocol
    sourcePortRange: rule.sourcePortRange
    destinationPortRange: rule.destinationPortRange
    sourceAddressPrefix: rule.sourceAddressPrefix
    destinationAddressPrefix: rule.destinationAddressPrefix
    access: rule.access
    direction: rule.direction
  }
}]

resource netSG 'Microsoft.Network/networkSecurityGroups@2020-11-01' = {
  name: 'NSG1'
  location: resourceGroup().location
  properties: {
    securityRules: securityRulesVar
  }
}
```

## <a name="next-steps"></a>后续步骤

- 有关循环的其他用法，请参阅：
  - [Bicep 中的资源迭代](loop-resources.md)
  - [Bicep 中的模块迭代](loop-modules.md)
  - [Bicep 中的属性迭代](loop-properties.md)
  - [Bicep 中的输出迭代](loop-outputs.md)
- 若要设置对循环中创建的资源的依赖关系，请参阅[设置资源依赖关系](./resource-declaration.md#set-resource-dependencies)。
