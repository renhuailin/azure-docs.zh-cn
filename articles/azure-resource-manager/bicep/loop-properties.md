---
title: 在 Bicep 中定义属性的多个实例
description: 使用 Bicep 属性循环在创建资源属性时进行迭代。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 08/30/2021
ms.openlocfilehash: 6e9d41136401b28cf330bc828947d35a67c69a43
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123225966"
---
# <a name="property-iteration-in-bicep"></a>Bicep 中的属性迭代

本文介绍如何在 Bicep 文件中创建一个属性的多个实例。 可以向资源的 `properties` 节添加循环，并动态设置属性的项数。 避免在 Bicep 文件中重复使用语法。

你只能对顶级资源使用循环，即使在将循环应用到属性时也是如此。 若要了解如何将子资源更改为顶级资源，请参阅[子资源的迭代](loop-resources.md#iteration-for-a-child-resource)。

还可以将循环用于 [modules](loop-modules.md)、[resources](loop-resources.md)、[variables](loop-variables.md) 和 [outputs](loop-outputs.md)。

## <a name="syntax"></a>语法

可通过以下方式使用循环声明多个属性：

- 使用循环索引。

  ```bicep
  <property-name>: [for <index> in range(<start>, <stop>): {
    <properties>
  }]
  ```

- 循环访问数组。

  ```bicep
  <property-name>: [for <item> in <collection>: {
    <properties>
  }]
  ```

  有关详细信息，请参阅[循环数组](#loop-array)。

- 循环访问数组和索引。

  ```bicep
  <property-name>: [for (<item>, <index>) in <collection>: {
    <properties>
  }]
  ```

## <a name="loop-limits"></a>循环限制

Bicep 文件的循环迭代不能为负数，也不能超过 800 次。 

## <a name="loop-array"></a>循环数组

此示例将循环访问 `subnets` 的数组，以在一个虚拟网络中创建两个子网。

```bicep
param rgLocation string = resourceGroup().location

var subnets = [
  {
    name: 'api'
    subnetPrefix: '10.144.0.0/24'
  }
  {
    name: 'worker'
    subnetPrefix: '10.144.1.0/24'
  }
]

resource vnet 'Microsoft.Network/virtualNetworks@2020-07-01' = {
  name: 'vnet'
  location: rgLocation
  properties: {
    addressSpace: {
      addressPrefixes: [
        '10.144.0.0/20'
      ]
    }
    subnets: [for subnet in subnets: {
      name: subnet.name
      properties: {
        addressPrefix: subnet.subnetPrefix
      }
    }]
  }
}
```

## <a name="next-steps"></a>后续步骤

- 有关循环的其他用法，请参阅：
  - [Bicep 中的资源迭代](loop-resources.md)
  - [Bicep 中的模块迭代](loop-modules.md)
  - [Bicep 中的变量迭代](loop-variables.md)
  - [Bicep 中的输出迭代](loop-outputs.md)
- 若要设置对循环中创建的资源的依赖关系，请参阅[设置资源依赖关系](./resource-declaration.md#set-resource-dependencies)。
