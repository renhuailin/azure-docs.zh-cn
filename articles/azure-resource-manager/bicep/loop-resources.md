---
title: 在 Bicep 中部署多个资源实例
description: 在 Bicep 文件中使用循环和数组来部署多个资源实例。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 07/19/2021
ms.openlocfilehash: b510d2601c7f1d2724e104017707345e05fb47c7
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114453334"
---
# <a name="resource-iteration-in-bicep"></a>Bicep 中的资源迭代

本文介绍如何在 Bicep 文件中创建多个资源实例。 可以将循环添加到文件的 `resource` 部分，并动态设置要部署的资源数。 还可避免在 Bicep 文件中重复语法。

还可以将循环用于 [properties](loop-properties.md)、[variables](loop-variables.md) 和 [outputs](loop-outputs.md)。

如需指定究竟是否部署资源，请参阅 [condition 元素](conditional-resource-deployment.md)。

## <a name="syntax"></a>语法

可以通过以下方式使用循环来声明多个属性：

- 循环访问数组。

  ```bicep
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for <item> in <collection>: {
    <resource-properties>
  }]
  ```

- 循环访问数组的元素。

  ```bicep
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for (<item>, <index>) in <collection>: {
    <resource-properties>
  }]
  ```

- 使用循环索引。

  ```bicep
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for <index> in range(<start>, <stop>): {
    <resource-properties>
  }]
  ```

## <a name="loop-limits"></a>循环限制

Bicep 文件的循环迭代不能为负数，也不能超过 800 次。 若要部署 Bicep 文件，请安装最新版本的 [Bicep 工具](install.md)。

## <a name="resource-iteration"></a>资源迭代

以下示例创建在 `storageCount` 参数中指定的存储帐户数目。

```bicep
param rgLocation string = resourceGroup().location
param storageCount int = 2

resource storageAcct 'Microsoft.Storage/storageAccounts@2021-02-01' = [for i in range(0, storageCount): {
  name: '${i}storage${uniqueString(resourceGroup().id)}'
  location: rgLocation
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
}]
```

请注意，创建存储帐户资源名称时将使用索引 `i`。

以下示例为 `storageNames` 参数中提供的每个名称创建一个存储帐户。

```bicep
param rgLocation string = resourceGroup().location
param storageNames array = [
  'contoso'
  'fabrikam'
  'coho'
]

resource storageAcct 'Microsoft.Storage/storageAccounts@2021-02-01' = [for name in storageNames: {
  name: '${name}${uniqueString(resourceGroup().id)}'
  location: rgLocation
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
}]
```

如果要从已部署的资源返回值，可以在[输出部分](loop-outputs.md)中使用循环。

## <a name="resource-iteration-with-condition"></a>具有条件的资源迭代

下面的示例演示与筛选的资源循环组合在一起的嵌套循环。 筛选器必须是计算结果为布尔值的表达式。

```bicep
resource parentResources 'Microsoft.Example/examples@2020-06-06' = [for parent in parents: if(parent.enabled) {
  name: parent.name
  properties: {
    children: [for child in parent.children: {
      name: child.name
      setting: child.settingValue
    }]
  }
}]
```

筛选器还必须受模块循环支持。

## <a name="deploy-in-batches"></a>批量部署

默认情况下，资源管理器并行创建资源。 使用循环创建多个资源类型实例时，将同时部署这些实例。 不会保证它们的创建顺序。 除了 Bicep 文件中的资源总数为 800 这一限制外，并行部署的资源数量没有限制。

你可能不希望同时更新资源类型的所有实例。 例如，在更新生产环境时，可能需要错开更新，使任何一次仅更新一定数量。 可指定同时批处理和部署其中一部分实例。 其他实例等待该批处理完成。

若要串行部署资源的实例，请添加 [batchSize 修饰器](./file.md#resource-and-module-decorators)。 将其值设置为一次要部署的实例数。 在循环中创建先前实例的依赖关系，使其在上一个批处理完成后才启动批处理。

```bicep
param rgLocation string = resourceGroup().location

@batchSize(2)
resource storageAcct 'Microsoft.Storage/storageAccounts@2021-02-01' = [for i in range(0, 4): {
  name: '${i}storage${uniqueString(resourceGroup().id)}'
  location: rgLocation
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
}]
```

## <a name="iteration-for-a-child-resource"></a>子资源的迭代

不能对嵌套子资源使用循环。 若要创建子资源的多个实例，请将子资源更改为顶级资源。

例如，假设通常将文件服务和文件共享定义为存储帐户的嵌套资源。

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2021-02-01' = {
  name: 'examplestorage'
  location: resourceGroup().location
  kind: 'StorageV2'
  sku: {
    name: 'Standard_LRS'
  }
  resource service 'fileServices' = {
    name: 'default'
    resource share 'shares' = {
      name: 'exampleshare'
    }
  }
}
```

若要创建多个文件共享，请将其移到存储帐户之外。 可通过 `parent` 属性定义与父资源的关系。

以下示例演示如何创建存储帐户、文件服务和多个文件共享：

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2021-02-01' = {
  name: 'examplestorage'
  location: resourceGroup().location
  kind: 'StorageV2'
  sku: {
    name: 'Standard_LRS'
  }
}

resource service 'Microsoft.Storage/storageAccounts/fileServices@2021-02-01' = {
  name: 'default'
  parent: stg
}

resource share 'Microsoft.Storage/storageAccounts/fileServices/shares@2021-02-01' = [for i in range(0, 3): {
  name: 'exampleshare${i}'
  parent: service
}]
```

## <a name="example-templates"></a>示例模板

以下示例展示了创建资源或属性的多个实例的常见方案。

|模板  |说明  |
|---------|---------|
|[循环存储](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/loopstorage.bicep) |部署名称中带索引号的多个存储帐户。 |
|[串行循环存储](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/loopserialstorage.bicep) |一次部署多个存储帐户。 名称包含索引号。 |
|[使用数组的循环存储](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/loopstoragewitharray.bicep) |部署多个存储帐户。 名称包含数组中的值。 |

## <a name="next-steps"></a>后续步骤

- 有关循环的其他用法，请参阅：
  - [Bicep 文件中的属性迭代](loop-properties.md)
  - [Bicep 文件中的变量迭代](loop-variables.md)
  - [Bicep 文件中的输出迭代](loop-outputs.md)
- 若要了解 Bicep 文件的各个部分，请参阅[了解 Bicep 文件的结构和语法](file.md)。
- 若要了解如何部署多个资源，请参阅[使用 Bicep 模块](modules.md)。
- 若要设置对循环中创建的资源的依赖关系，请参阅[设置资源依赖关系](./resource-declaration.md#set-resource-dependencies)。
- 若要了解如何使用 PowerShell 进行部署，请参阅[使用 Bicep 和 Azure PowerShell 部署资源](deploy-powershell.md)。
- 若要了解如何使用 Azure CLI 进行部署，请参阅[使用 Bicep 和 Azure CLI 部署资源](deploy-cli.md)。
