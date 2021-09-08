---
title: 在 Bicep 中部署多个模块实例
description: 在 Bicep 文件中使用循环和数组来部署多个模块实例。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 08/27/2021
ms.openlocfilehash: 44e5a4356cac0f252b344a7acf8b68a2f51d1ef1
ms.sourcegitcommit: f53f0b98031cd936b2cd509e2322b9ee1acba5d6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123215236"
---
# <a name="module-iteration-in-bicep"></a>Bicep 中的模块迭代

本文展示了如何在 Bicep 文件中部署多个[模块](modules.md)实例。 可以在 `module` 声明中添加循环，并动态设置部署该模块的次数。 避免在 Bicep 文件中重复语法。

还可以将循环用于[资源](loop-resources.md)、[属性](loop-properties.md)、[变量](loop-variables.md)和[输出](loop-outputs.md)。

## <a name="syntax"></a>语法

可通过以下方式使用循环来声明多个模块：

- 使用循环索引。

  ```bicep
  module <module-symbolic-name> '<module-file>' = [for <index> in range(<start>, <stop>): {
    <module-properties>
  }]
  ```

  有关详细信息，请参阅[循环索引](#loop-index)。

- 循环访问数组。

  ```bicep
  module <module-symbolic-name> '<module-file>' = [for <item> in <collection>: {
    <module-properties>
  }]
  ```

  有关详细信息，请参阅[循环数组](#loop-array)。

- 循环访问数组和索引：

  ```bicep
  module <module-symbolic-name> 'module-file' = [for (<item>, <index>) in <collection>: {
    <module-properties>
  }]
  ```

## <a name="loop-limits"></a>循环限制

Bicep 文件的循环迭代不能为负数，也不能超过 800 次。

## <a name="loop-index"></a>循环索引

以下示例按照 `storageCount` 参数中指定的次数部署模块。 该模块的每个实例都创建一个存储帐户。

```bicep
param location string = resourceGroup().location
param storageCount int = 2

var baseName = 'store${uniqueString(resourceGroup().id)}'

module stgModule './storageAccount.bicep' = [for i in range(0, storageCount): {
  name: '${i}storage${baseName}'
  params: {
    storageName: '${i}${baseName}'
    location: location
  }
}]
```

请注意，创建存储帐户资源名称时将使用索引 `i`。 存储帐户作为参数值传递给模块。

## <a name="loop-array"></a>循环数组

以下示例针对 `storageNames` 参数中提供的每个名称部署模块。 该模块会创建一个存储帐户

```bicep
param rgLocation string = resourceGroup().location
param storageNames array = [
  'contoso'
  'fabrikam'
  'coho'
]

module stgModule './storageAccount.bicep' = [for name in storageNames: {
  name: '${name}${uniqueString(resourceGroup().id)}'
  params: {
    storageName: name
    location: location
  }
}]

```

输出循环中不支持引用模块集合。 若要从集合中的模块输出结果，请将数组索引器应用于表达式。 有关详细信息，请参阅[输出迭代](loop-outputs.md)。

## <a name="module-iteration-with-condition"></a>具有条件的模块迭代

以下示例展示带有经过筛选的模块循环的 Bicep 文件。 筛选器必须是计算结果为布尔值的表达式。

```bicep
param location string = resourceGroup().location
param storageCount int = 2
param createNewStorage bool = true

var baseName = 'store${uniqueString(resourceGroup().id)}'

module stgModule './storageAccount.bicep' = [for i in range(0, storageCount): if(createNewStorage) {
  name: '${i}storage${baseName}'
  params: {
    storageName: '${i}${baseName}'
    location: location
  }
}]
```

在前面的示例中，仅当布尔值为 `true` 时才会调用该模块。

筛选器还必须受[资源循环](loop-resources.md)支持。

## <a name="deploy-in-batches"></a>批量部署

默认情况下，资源管理器并行创建资源。 使用循环创建多个资源类型实例时，将同时部署这些实例。 不会保证它们的创建顺序。 除了 Bicep 文件中的资源总数为 800 这一限制外，并行部署的资源数量没有限制。

你可能不希望同时更新资源类型的所有实例。 例如，在更新生产环境时，可能需要错开更新，使任何一次仅更新一定数量。 可指定同时批处理和部署其中一部分实例。 其他实例等待该批处理完成。

若要连续部署模块的实例，请添加 [batchSize 修饰器](./file.md#resource-and-module-decorators)。 将其值设置为要并发部署的实例数。 在循环中创建先前实例的依赖关系，使其在上一个批处理完成后才启动批处理。

```bicep
param location string = resourceGroup().location

@batchSize(2)
module stgModule './storageAccount.bicep' = [for i in range(0, 4): {
  name: '${i}storage${uniqueString(resourceGroup().id)}'
  params: {
    storageName: '${i}${baseName}'
    location: location
  }
}]
```

对于纯顺序部署，请将批大小设置为 1。

## <a name="next-steps"></a>后续步骤

- 有关循环的其他用法，请参阅：
  - [Bicep 中的资源迭代](loop-resources.md)
  - [Bicep 中的属性迭代](loop-properties.md)
  - [Bicep 中的变量迭代](loop-variables.md)
  - [Bicep 中的输出迭代](loop-outputs.md)
- 若要详细了解模块，请参阅[使用 Bicep 模块](modules.md)。
- 若要设置对循环中创建的资源的依赖关系，请参阅[设置资源依赖关系](./resource-declaration.md#set-resource-dependencies)。
