---
title: 比较 Azure 资源管理器模板的 JSON 和 Bicep 语法
description: 比较通过 JSON 和 Bicep 开发的 Azure 资源管理器模板，并展示如何在这两种语言之间进行转换。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 07/30/2021
ms.openlocfilehash: 414e073fe6940cc9a223a25515902b3af3ca0a54
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121744906"
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

对于 Bicep，可以设置显式依赖项，但不建议使用此方法。 而应依赖于隐式依赖项。 当一个资源声明引用另一个资源的标识符时，将创建隐式依赖项。

下面显示了一个隐式依赖于网络安全组的网络接口。 它使用 `nsg.id` 引用网络安全组。

```bicep
resource nsg 'Microsoft.Network/networkSecurityGroups@2020-06-01' = {
  ...
}

resource nic1 'Microsoft.Network/networkInterfaces@2020-06-01' = {
  name: nic1Name
  location: location
  properties: {
    ...
    networkSecurityGroup: {
      id: nsg.id
    }
  }
}
```

如果必须设置显式依赖项，请使用：

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

* 对于 Bicep，请使用[模块](modules.md)。
* 对于 ARM 模板，请使用[链接的模板](../templates/linked-templates.md)。

## <a name="next-steps"></a>后续步骤

* 有关 Bicep 的信息，请参阅 [Bicep 快速入门](./quickstart-create-bicep-use-visual-studio-code.md)。
* 若要了解如何在语言之间转换模板，请参阅[在 JSON 和 Bicep 之间转换 ARM 模板](decompile.md)。
