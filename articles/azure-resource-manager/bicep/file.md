---
title: Bicep 文件结构和语法
description: 使用声明性语法介绍 Bicep 文件的结构和属性。
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: a9dcb83b54a665a71c8f1095a682c9dabcafd039
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/02/2021
ms.locfileid: "111025679"
---
# <a name="understand-the-structure-and-syntax-of-bicep-files"></a>了解 Bicep 文件的结构和语法

本文介绍 Bicep 文件的结构。 本文演示了文件的不同部分，以及可在相应部分使用的属性。

本文适用对象为对 Bicep 文件有一定了解的用户。 文中详细介绍了 Bicep 文件的结构。 有关指导你完成 Bicep 文件创建过程的分步教程，请参阅[快速入门：使用 Visual Studio Code 创建 Bicep 文件](./quickstart-create-bicep-use-visual-studio-code.md)。

## <a name="bicep-format"></a>Bicep 格式

Bicep 文件具有以下元素。 Bicep 是一种声明性语言，这意味着元素可以按任何顺序显示。  元素的顺序不会影响处理部署的方式，这与命令性语言不同。

```bicep
targetScope = '<scope>'

@<decorator>(<argument>)
param <parameter-name> <parameter-data-type> = <default-value>

var <variable-name> = <variable-value>

resource <resource-symbolic-name> '<resource-type>@<api-version>' = {
  <resource-properties>
}

// conditional deployment
resource <resource-symbolic-name> '<resource-type>@<api-version>' = if (<condition-to-deploy>) {
  <resource-properties>
}

// iterative deployment
@<decorator>(<argument>)
resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for <item> in <collection>: {
  <resource-properties>
}]

module <module-symbolic-name> '<path-to-file>' = {
  name: '<linked-deployment-name>'
  params: {
    <parameter-names-and-values>
  }
}

// conditional deployment
module <module-symbolic-name> '<path-to-file>' = if (<condition-to-deploy>) {
  name: '<linked-deployment-name>'
  params: {
    <parameter-names-and-values>
  }
}

// iterative deployment
module <module-symbolic-name> '<path-to-file>' = [for <item> in <collection>: {
  name: '<linked-deployment-name>'
  params: {
    <parameter-names-and-values>
  }
}]

// deploy to different scope
module <module-symbolic-name> '<path-to-file>' = {
  name: '<linked-deployment-name>'
  scope: <scope-object>
  params: {
    <parameter-names-and-values>
  }
}

output <output-name> <output-data-type> = <output-value>

// iterative output
output <output-name> array = [for <item> in <collection>: {
  <output-properties>
}]
```

以下示例演示了这些元素的实现。

```bicep
@minLength(3)
@maxLength(11)
param storagePrefix string

param storageSKU string = 'Standard_LRS'
param location string = resourceGroup().location

var uniqueStorageName = '${storagePrefix}${uniqueString(resourceGroup().id)}'

resource stg 'Microsoft.Storage/storageAccounts@2019-04-01' = {
  name: uniqueStorageName
  location: location
  sku: {
    name: storageSKU
  }
  kind: 'StorageV2'
  properties: {
    supportsHttpsTrafficOnly: true
  }
}

module webModule './webApp.bicep' = {
  name: 'webDeploy'
  params: {
    skuName: 'S1'
    location: location
  }
}

output storageEndpoint object = stg.properties.primaryEndpoints
```

## <a name="target-scope"></a>目标作用域

默认情况下，目标作用域设置为 `resourceGroup`。 如果要在资源组级别进行部署，则无需在 Bicep 文件中设置目标作用域。

允许的值为：

* resourceGroup - 用于[资源组部署](deploy-to-resource-group.md)的默认值。
* subscription - 用于[订阅部署](deploy-to-subscription.md)。
* managementGroup - 用于[管理组部署](deploy-to-management-group.md)。
* tenant - 用于[租户部署](deploy-to-tenant.md)。

在模块中，指定的范围可以不同于 Bicep 文件其余部分的范围。 有关详细信息，请参阅[配置模块范围](modules.md#configure-module-scopes)

## <a name="parameters"></a>参数

将参数用于需要随不同部署而变化的值。 如果在部署过程中未提供任何值，则可以为所使用的参数定义默认值。

例如，可以添加 SKU 参数以指定资源的不同大小。 可以使用 Bicep 函数来创建默认值，例如获取资源组位置。

```bicep
param storageSKU string = 'Standard_LRS'
param location string = resourceGroup().location
```

有关可用的数据类型，请参阅 [Bicep 中的数据类型](data-types.md)。

参数不能与变量、模块或资源同名。

有关详细信息，请参阅 [Bicep 中的参数](./parameters.md)。

## <a name="parameter-decorators"></a>参数修饰器

可以为每个参数添加一个或多个修饰器。 这些修饰器定义参数所允许的值。 以下示例指定可以通过 Bicep 文件部署的 SKU。

```bicep
@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
param storageSKU string = 'Standard_LRS'
```

下表介绍了可用的修饰器及其使用方式。

| 修饰器 | 应用于 | 参数 | 说明 |
| --------- | ---- | ----------- | ------- |
| 允许 | all | array | 参数允许的值。 使用此修饰器以确保用户提供正确的值。 |
| description | all | string | 解释如何使用参数的文本。 通过门户向用户显示该说明。 |
| maxLength | 数组、字符串 | int | 字符串和数组参数的最大长度。 最大值包含在内。 |
| maxValue | int | int | 整数参数的最大值。 最大值包含在内。 |
| metadata | all | object | 应用于参数的自定义属性。 可以包含与说明修饰器等效的说明属性。 |
| minLength | 数组、字符串 | int | 字符串和数组参数的最小长度。 最小值包含在内。 |
| minValue | int | int | 整数参数的最小值。 最小值包含在内。 |
| secure | 字符串、对象 | 无 | 将参数标记为安全。 安全参数的值不会保存到部署历史记录中，也不会被记录下来。 有关详细信息，请参阅[保护字符串和对象](data-types.md#secure-strings-and-objects)。 |

## <a name="variables"></a>变量

将变量用于在 Bicep 文件中重复的复杂表达式。 例如，可以为资源名称添加一个变量，该名称通过将多个值连接在一起构造而成。

```bicep
var uniqueStorageName = '${storagePrefix}${uniqueString(resourceGroup().id)}'
```

不能为变量指定[数据类型](data-types.md)。 而是从值中推断数据类型。

变量不能与参数、模块或资源同名。

有关详细信息，请参阅 [Bicep 中的变量](./variables.md)。

## <a name="resource"></a>资源

使用关键字 `resource` 定义要部署的资源。 资源声明中包含资源的符号名称。 如果需要从资源中获取值，请在 Bicep 文件的其他部分中使用此符号名称。

资源声明中还包含资源类型和 API 版本。

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: uniqueStorageName
  location: location
  sku: {
    name: storageSKU
  }
  kind: 'StorageV2'
  properties: {
    supportsHttpsTrafficOnly: true
  }
}
```

在资源声明中，需加入资源类型的属性。 这些属性对于每个资源类型而言皆具有唯一性。

有关详细信息，请参阅 [Bicep 中的资源声明](resource-declaration.md)。

若要[有条件地部署资源](conditional-resource-deployment.md)，请添加 `if` 表达式。

```bicep
resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = if (newOrExisting == 'new') {
  name: uniqueStorageName
  location: location
  sku: {
    name: storageSKU
  }
  kind: 'StorageV2'
  properties: {
    supportsHttpsTrafficOnly: true
  }
}
```

若要部署一个资源类型的[多个实例](https://github.com/Azure/bicep/blob/main/docs/spec/loops.md)，请添加 `for` 表达式。 此表达式可以循环访问数组的成员。

```bicep
resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = [for storageName in storageAccounts: {
  name: storageName
  location: location
  sku: {
    name: storageSKU
  }
  kind: 'StorageV2'
  properties: {
    supportsHttpsTrafficOnly: true
  }
}]
```

资源不能与参数、变量或模块同名。

## <a name="modules"></a>模块

如果你想要重用某些代码，请使用模块链接到包含这些代码的其他 Bicep 文件。 该模块包含一个或多个要部署的资源。 这些资源与 Bicep 文件中的所有其他资源一起部署。

```bicep
module webModule './webApp.bicep' = {
  name: 'webDeploy'
  params: {
    skuName: 'S1'
    location: location
  }
}
```

借助符号名称，你可以从文件中的其他位置引用模块。 例如，可以通过使用符号名称和输出值的名称来获取模块的输出值。

模块不能与参数、变量或资源同名。

与资源类似，可以有条件地或以迭代方式部署模块。 模块与资源拥有相同的语法。

有关详细信息，请参阅[使用 Bicep 模块](./modules.md)。

## <a name="resource-and-module-decorators"></a>资源和模块修饰器

可以向资源或模块定义中添加修饰器。 唯一受支持的修饰器是 `batchSize(int)`。 只能将该修饰器应用于使用 `for` 表达式的资源或模块定义。

默认情况下，将并行部署资源。 你并不知道它们的完成顺序。 添加 `batchSize` 修饰器时，将串行部署实例。 使用整数参数指定要并行部署的实例数。

```bicep
@batchSize(3)
resource storageAccountResources 'Microsoft.Storage/storageAccounts@2019-06-01' = [for storageName in storageAccounts: {
  ...
}]
```

有关详细信息，请参阅[批量部署](loop-resources.md#deploy-in-batches)。

## <a name="outputs"></a>输出

使用输出，以从部署中返回值。 通常，当需要将某值重新用于其他操作时，可以从已部署的资源中返回该值。

```bicep
output storageEndpoint object = stg.properties.primaryEndpoints
```

指定输出值的[数据类型](data-types.md)。

输出可以与参数、变量、模块或资源同名。

有关详细信息，请参阅 [Bicep 中的输出](./outputs.md)。

## <a name="comments"></a>注释

将 `//` 用于单行注释或将 `/* ... */` 用于多行注释

以下示例演示单行注释。

```bicep
// This is your primary NIC.
resource nic1 'Microsoft.Network/networkInterfaces@2020-06-01' = {
   ...
}
```

以下示例演示多行注释。

```bicep
/*
  This Bicep file assumes the key vault already exists and
  is in same subscription and resource group as the deployment.
*/
param existingKeyVaultName string
```

## <a name="multi-line-strings"></a>多行字符串

可将一个字符串分成多个行。 使用三个单引号字符 `'''` 来开始和结束多行字符串。

多行字符串中的字符按原样处理。 不需要转义字符。 多行字符串中不能包含 `'''`。 当前不支持字符串内插。

可以在开头的 `'''` 之后立即开始字符串，也可以加入新行。 无论哪种情况，生成的字符串都不包含新行。 根据 Bicep 文件中的行尾，会将新行解释为 `\r\n` 或 `\n`。

以下示例演示多行字符串。

```bicep
var stringVar = '''
this is multi-line
  string with formatting
  preserved.
'''
```

前面的示例与以下 JSON 等效。

```json
"variables": {
  "stringVar": "this is multi-line\r\n  string with formatting\r\n  preserved.\r\n"
}
```

## <a name="next-steps"></a>后续步骤

有关 Bicep 的简介，请参阅 [Bicep 是什么？](./overview.md)。 有关 Bicep 数据类型，请参阅[数据类型](./data-types.md)。
