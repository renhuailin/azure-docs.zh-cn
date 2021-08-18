---
title: 在 Bicep 中定义属性的多个实例
description: 使用 Bicep 属性循环在创建资源属性时进行迭代。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 59ca13e4f6ab29a71c543fcbe779ef616beba43b
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/02/2021
ms.locfileid: "111025828"
---
# <a name="property-iteration-in-bicep"></a>Bicep 中的属性迭代

本文介绍如何在 Bicep 文件中创建一个属性的多个实例。 可向资源的 `properties` 部分添加一个循环，并在部署期间动态设置属性的项数。 还可避免在 Bicep 文件中重复语法。

你只能对顶级资源使用循环，即使在将循环应用到属性时也是如此。 若要了解如何将子资源更改为顶级资源，请参阅[子资源的迭代](loop-resources.md#iteration-for-a-child-resource)。

还可将循环用于 [resources](loop-resources.md)、[variables](loop-variables.md) 和 [outputs](loop-outputs.md)。

## <a name="syntax"></a>语法

可通过以下方式使用循环声明多个属性：

- 循环访问数组。

  ```bicep
  <property-name>: [for <item> in <collection>: {
    <properties>
  }]
  ```

- 循环访问数组的元素。

  ```bicep
  <property-name>: [for (<item>, <index>) in <collection>: {
    <properties>
  }]
  ```

- 使用循环索引。

  ```bicep
  <property-name>: [for <index> in range(<start>, <stop>): {
    <properties>
  }]
  ```

## <a name="loop-limits"></a>循环限制

Bicep 文件的循环迭代不能为负数，也不能超过 800 次。 若要部署 Bicep 文件，请安装最新版本的 [Bicep 工具](install.md)。

## <a name="property-iteration"></a>属性迭代

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
  - [Bicep 文件中的资源迭代](loop-resources.md)
  - [Bicep 文件中的变量迭代](loop-variables.md)
  - [Bicep 文件中的输出迭代](loop-outputs.md)
- 若要了解 Bicep 文件的各个部分，请参阅[了解 Bicep 文件的结构和语法](file.md)。
- 若要了解如何部署多个资源，请参阅[使用 Bicep 模块](modules.md)。
- 若要设置对循环中创建的资源的依赖关系，请参阅[设置资源依赖关系](./resource-declaration.md#set-resource-dependencies)。
- 若要了解如何使用 PowerShell 进行部署，请参阅[使用 Bicep 和 Azure PowerShell 部署资源](deploy-powershell.md)。
- 若要了解如何使用 Azure CLI 进行部署，请参阅[使用 Bicep 和 Azure CLI 部署资源](deploy-cli.md)。
