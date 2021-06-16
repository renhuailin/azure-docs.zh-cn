---
title: 比较 Azure 资源管理器模板的 JSON 和 Bicep 语法
description: 比较通过 JSON 和 Bicep 开发的 Azure 资源管理器模板，并展示如何在这两种语言之间进行转换。
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 225e52e9534a77a01502b762f043a4f34df19caa
ms.sourcegitcommit: 5c136a01bddfccb2cc9f7e7e7741e2cf2651ddbe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/03/2021
ms.locfileid: "111353282"
---
# <a name="comparing-json-and-bicep-for-templates"></a>比较模板的 JSON 和 Bicep

本文将 Azure 资源管理器模板（ARM 模板）的 Bicep 语法与 JSON 语法进行了比较。 在大多数情况下，Bicep 提供的语法比 JSON 中的等效语法更简单。

如果你知道如何使用 JSON 开发 ARM 模板，请使用以下示例来了解 Bicep 的等效语法。

## <a name="expressions"></a>表达式

若要创作表达式，请运行以下命令：

```bicep
func()
```

```json
"[func()]"
```

## <a name="parameters"></a>参数

若要使用默认值声明参数，请运行以下命令：

```bicep
param demoParam string = 'Contoso'
```

```json
"parameters": {
  "demoParam": {
    "type": "string",
    "defaultValue": "Contoso"
  }
}
```

若要获取参数值，请运行以下命令：

```bicep
demoParam
```

```json
[parameters('demoParam'))]
```

## <a name="variables"></a>变量

若要声明变量，请运行以下命令：

```bicep
var demoVar = 'example value'
```

```json
"variables": {
  "demoVar": "example value"
},
```

若要获取变量值，请运行以下命令：

```bicep
demoVar
```

```json
[variables('demoVar'))]
```

## <a name="strings"></a>字符串

若要连接字符串，请运行以下命令：

```bicep
'${namePrefix}-vm'
```

```json
[concat(parameters('namePrefix'), '-vm')]
```

## <a name="logical-operators"></a>逻辑运算符

若要返回逻辑 **AND**，请运行以下命令：

```bicep
isMonday && isNovember
```

```json
[and(parameter('isMonday'), parameter('isNovember'))]
```

若要按条件设置值，请运行以下命令：

```bicep
isMonday ? 'valueIfTrue' : 'valueIfFalse'
```

```json
[if(parameters('isMonday'), 'valueIfTrue', 'valueIfFalse')]
```

## <a name="deployment-scope"></a>部署范围

若要设置部署的目标范围，请运行以下命令：

```bicep
targetScope = 'subscription'
```

```json
"$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#"
```

## <a name="resources"></a>资源

若要声明资源，请运行以下命令：

```bicep
resource vm 'Microsoft.Compute/virtualMachines@2020-06-01' = {
  ...
}
```

```json
"resources": [ 
  { 
    "type": "Microsoft.Compute/virtualMachines", 
    "apiVersion": "2020-06-01", 
    ... 
  } 
]
```

若要按条件部署资源，请运行以下命令：

```bicep
resource vm 'Microsoft.Compute/virtualMachines@2020-06-01' = if(deployVM) {
  ...
}
```

```json
"resources": [ 
  {
    "condition": "[parameters('deployVM')]",
    "type": "Microsoft.Compute/virtualMachines", 
    "apiVersion": "2020-06-01", 
    ... 
  } 
]
```

若要设置资源属性，请运行以下命令：

```bicep
sku: '2016-Datacenter'
```

```json
"sku": "2016-Datacenter",
```

若要获取模板中资源的资源 ID，请运行以下命令：

```bicep
nic1.id
```

```json
[resourceId('Microsoft.Network/networkInterfaces', variables('nic1Name'))]
```

## <a name="loops"></a>循环

若要循环访问数组或计数中的项，请运行以下命令：

```bicep
[for storageName in storageAccounts: {
  ...
}]
```

```json
"copy": {
  "name": "storagecopy",
  "count": "[length(parameters('storageAccounts'))]"
},
...
```

## <a name="resource-dependencies"></a>资源依赖关系

若要设置资源之间的依赖关系，请运行以下命令：

对于 Bicep，请依赖于依赖关系的自动检测，或手动设置依赖关系。

```bicep
dependsOn: [ stg ]
```

```json
"dependsOn": ["[resourceId('Microsoft.Storage/storageAccounts', 'parameters('storageAccountName'))]"]
```

## <a name="reference-resources"></a>引用资源

若要从模板中的资源获取属性，请运行以下命令：

```bicep
diagsAccount.properties.primaryEndpoints.blob
```

```json
[reference(resourceId('Microsoft.Storage/storageAccounts', variables('diagStorageAccountName'))).primaryEndpoints.blob]
```

若要从未部署在模板中的现有资源获取属性，请运行以下命令：

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' existing = {
  name: storageAccountName
}

// use later in template as often as needed
stg.properties.primaryEndpoints.blob
```

```json
// required every time the property is needed
"[reference(resourceId('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2019-06-01').primaryEndpoints.blob]"
```

## <a name="outputs"></a>输出

若要从模板中的资源输出属性，请运行以下命令：

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

```json
"outputs": {
  "hostname": {
    "type": "string",
    "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))).dnsSettings.fqdn]"
  },
}
```

## <a name="code-reuse"></a>代码重用

若要将解决方案隔离到多个文件，请运行以下命令：

* 对于 Bicep，请使用[模块](bicep-tutorial-add-modules.md)。
* 对于 JSON，请使用[链接的模板](linked-templates.md)。

## <a name="recommendations"></a>建议

* 如果可能，请避免在 Bicep 文件中使用 [reference](template-functions-resource.md#reference) 和 [resourceId](template-functions-resource.md#resourceid) 函数。 引用同一 Bicep 部署中的资源时，请改用资源标识符。 例如，如果你在 Bicep 文件中部署了一个资源并使用 `stg` 作为资源标识符，请使用类似于 `stg.id` 或 `stg.properties.primaryEndpoints.blob` 的语法来获取属性值。 通过使用资源标识符，可以在资源之间创建隐式依赖关系。 不需要使用 dependsOn 属性显式设置依赖关系。
* 如果资源未在 Bicep 文件中部署，你仍可以使用 **现有** 关键字获取对该资源的符号引用。
* 请为标识符使用一致的大小写。 如果你不确定要使用哪种类型的大小写，请尝试使用驼峰式大小写。 例如，`param myCamelCasedParameter string`。
* 仅当需要使用说明向用户提供基本信息时，才向参数中添加说明。 对于某些信息，你可以使用 `//` 注释。

## <a name="next-steps"></a>后续步骤

* 有关 Bicep 的信息，请参阅 [Bicep 教程](./bicep-tutorial-create-first-bicep.md)。
* 若要了解如何在语言之间转换模板，请参阅[在 JSON 和 Bicep 之间转换 ARM 模板](bicep-decompile.md)。
