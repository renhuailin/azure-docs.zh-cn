---
title: 部署资源的多个实例
description: 在 Azure 资源管理器模板（ARM 模板）中使用复制操作和数组多次部署资源类型。
ms.topic: conceptual
ms.date: 05/07/2021
ms.openlocfilehash: 84276811f1a809e66427584ad6d59a2805b2f79e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128589998"
---
# <a name="resource-iteration-in-arm-templates"></a>ARM 模板中的资源迭代

本文介绍如何在 Azure 资源管理器模板（ARM 模板）中创建一个资源的多个实例。 通过将 copy 循环添加到模板的 resources 节，可以动态设置要部署的资源数。 还可以避免重复模板语法。

还可以将 copy 循环用于 [properties](copy-properties.md)、[variables](copy-variables.md) 和 [outputs](copy-outputs.md)。

如需指定究竟是否部署资源，请参阅 [condition 元素](conditional-resource-deployment.md)。

## <a name="syntax"></a>语法

将 `copy` 元素添加到模板的 resources 节可部署资源的多个实例。 `copy` 元素采用以下常规格式：

```json
"copy": {
  "name": "<name-of-loop>",
  "count": <number-of-iterations>,
  "mode": "serial" <or> "parallel",
  "batchSize": <number-to-deploy-serially>
}
```

`name` 属性是标识循环的任何值。 `count` 属性指定要对该资源类型进行的迭代次数。

使用 `mode` 和 `batchSize` 属性指定是并行还是按顺序部署资源。 [串行或并行](#serial-or-parallel)中介绍了这些属性。

## <a name="copy-limits"></a>复制限制

count 不能超过 800。

count 不能为负数。 如果使用最新版本的 Azure CLI、PowerShell 或 REST API 部署模板，则它可以为零。 具体而言，必须使用：

- Azure PowerShell 2.6 或更高版本
- Azure CLI 2.0.74 或更高版本
- REST API 版本 2019-05-10 或更高版本
- [链接的部署](linked-templates.md)必须将 API 版本 2019-05-10 或更高版本用于部署资源类型

更早版本的 PowerShell、CLI 和 REST API 不支持将 count 设为零。

将[完整模式部署](deployment-modes.md)与 copy 循环一起使用时要小心。 如果以完整模式重新部署到资源组，则在解析复制循环后会删除模板中未指定的任何资源。

## <a name="resource-iteration"></a>资源迭代

以下示例创建在 `storageCount` 参数中指定的存储帐户数目。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageCount": {
      "type": "int",
      "defaultValue": 2
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": "[parameters('storageCount')]"
      }
    }
  ]
}
```

请注意，每个资源的名称都包括 `copyIndex()` 函数，用于返回循环中的当前迭代。 `copyIndex()` 从零开始。 因此，以下示例：

```json
"name": "[concat('storage', copyIndex())]",
```

将创建以下名称：

- storage0
- storage1
- storage2

若要偏移索引值，可以在 `copyIndex()` 函数中传递一个值。 迭代次数仍在 copy 元素中指定，但 `copyIndex` 的值会偏移一个指定的值。 因此，以下示例：

```json
"name": "[concat('storage', copyIndex(1))]",
```

将创建以下名称：

- storage1
- storage2
- storage3

处理数组时可以使用复制操作，因为可对数组中的每个元素执行迭代操作。 可以对数组使用 `length` 函数来指定迭代计数，并使用 `copyIndex` 来检索数组中的当前索引。

以下示例为参数中提供的每个名称创建一个存储帐户。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageNames": {
          "type": "array",
          "defaultValue": [
            "contoso",
            "fabrikam",
            "coho"
          ]
      }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(parameters('storageNames')[copyIndex()], uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": "[length(parameters('storageNames'))]"
      }
    }
  ],
  "outputs": {}
}
```

如果要从已部署的资源返回值，可以使用[在输出部分中复制](copy-outputs.md)。

## <a name="serial-or-parallel"></a>串行或并行

默认情况下，资源管理器并行创建资源。 除了模板中 800 个资源的总限制外，它对并行部署的资源数量没有限制。 不会保证它们的创建顺序。

但是，你可能希望将资源指定为按顺序部署。 例如，在更新生产环境时，可能需要错开更新，使任何一次仅更新一定数量。

若要按顺序部署多个资源实例，请将 `mode` 设置为“串行”，并将 `batchSize` 设置为一次要部署的实例数量。 在串行模式下，资源管理器会在循环中创建早前实例的依赖项，以便在前一个批处理完成之前它不会启动一个批处理。

`batchSize` 的值不能超过 copy 元素中 `count` 的值。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "copy": {
        "name": "storagecopy",
        "count": 4,
        "mode": "serial",
        "batchSize": 2
      },
      "properties": {}
    }
  ],
  "outputs": {}
}
```

`mode` 属性也接受“parallel”（默认值）。

## <a name="iteration-for-a-child-resource"></a>子资源的迭代

不能对子资源使用 copy 循环。 要创建通常定义为嵌套在另一个资源中的资源的多个实例，必须将该资源创建为顶级资源。 可以通过 type 和 name 属性定义与父资源的关系。

例如，假设用户通常会将某个数据集定义为数据工厂中的子资源。

```json
"resources": [
{
  "type": "Microsoft.DataFactory/factories",
  "name": "exampleDataFactory",
  ...
  "resources": [
    {
      "type": "datasets",
      "name": "exampleDataSet",
      "dependsOn": [
        "exampleDataFactory"
      ],
      ...
    }
  ]
```

若要创建多个数据集，请将其移出数据工厂。 数据集必须与数据工厂处于同一级别，但它仍是数据工厂的子资源。 可以通过 type 和 name 属性保留数据集和数据工厂之间的关系。 由于类型不再可以从其在模板中的位置推断，因此必须按以下格式提供完全限定的类型： `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`。

若要与数据工厂的实例建立父/子关系，提供的数据集的名称应包含父资源名称。 使用以下格式： `{parent-resource-name}/{child-resource-name}`。

以下示例演示实现过程。

```json
"resources": [
{
  "type": "Microsoft.DataFactory/factories",
  "name": "exampleDataFactory",
  ...
},
{
  "type": "Microsoft.DataFactory/factories/datasets",
  "name": "[concat('exampleDataFactory', '/', 'exampleDataSet', copyIndex())]",
  "dependsOn": [
    "exampleDataFactory"
  ],
  "copy": {
    "name": "datasetcopy",
    "count": "3"
  },
  ...
}]
```

## <a name="example-templates"></a>示例模板

以下示例展示了创建资源或属性的多个实例的常见方案。

|模板  |说明  |
|---------|---------|
|[复制存储](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystorage.json) |部署名称中带索引号的多个存储帐户。 |
|[串行复制存储](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/serialcopystorage.json) |一次部署多个存储帐户。 名称包含索引号。 |
|[使用数组的复制存储](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewitharray.json) |部署多个存储帐户。 名称包含数组中的值。 |
| [复制资源组](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyrg.json) | 部署多个资源组。 |

## <a name="next-steps"></a>后续步骤

- 若要设置在复制循环中创建的资源的依赖项，请参阅[在 ARM 模板中定义部署资源的顺序](./resource-dependency.md)。
- 若要完成教程，请参阅[教程：使用 ARM 模板创建多个资源实例](template-tutorial-create-multiple-instances.md)。
- 有关介绍资源副本的 Microsoft Learn 模块，请参阅[使用高级 ARM 模板功能管理复杂云部署](/learn/modules/manage-deployments-advanced-arm-template-features/)。
- 有关 copy 循环的其他用法，请参阅：
  - [ARM 模板中的属性迭代](copy-properties.md)
  - [ARM 模板中的变量迭代](copy-variables.md)
  - [ARM 模板中的输出迭代](copy-outputs.md)
- 有关将副本与嵌套的模板配合使用的信息，请参阅[使用副本](linked-templates.md#using-copy)。
