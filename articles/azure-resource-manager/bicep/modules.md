---
title: Bicep 模块
description: 介绍如何定义和使用模块，以及如何使用模块范围。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 07/15/2021
ms.openlocfilehash: 5e092a0b7f27379cf9fdc488c7a56a295ce17d25
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121752250"
---
# <a name="use-bicep-modules"></a>使用 Bicep 模块

使用 Bicep 可将复杂的解决方案分解为模块。 Bicep 模块是要一起部署的一个或多个资源的集合。 模块会抽象出原始资源声明的复杂细节，从而提高可读性。 这些模块可以重复使用，并与他人共享。 Bicep 模块被转译为包含用于部署的[嵌套模板](../templates/linked-templates.md#nested-template)的一个 ARM 模板。

有关教程，请参阅[使用 Bicep 模板部署 Azure 资源](/learn/modules/deploy-azure-resources-by-using-bicep-templates/)。

## <a name="define-modules"></a>定义模块

每个 Bicep 文件都可以作为模块使用。 模块仅将参数和输出作为协定公开给其他 Bicep 文件。 参数和输出都是可选的。

以下 Bicep 文件可以直接部署，以创建存储帐户或将作为模块使用。  下一节将演示如何使用模块：

```bicep
@minLength(3)
@maxLength(11)
param storagePrefix string

@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_RAGRS'
  'Standard_ZRS'
  'Premium_LRS'
  'Premium_ZRS'
  'Standard_GZRS'
  'Standard_RAGZRS'
])
param storageSKU string = 'Standard_LRS'
param location string

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

output storageEndpoint object = stg.properties.primaryEndpoints
```

输出用来将值传递给父 Bicep 文件。

## <a name="consume-modules"></a>使用模块

应用 _模块_ 关键字来使用模块。 以下 Bicep 文件部署在引用的模块文件中定义的资源：

```bicep
@minLength(3)
@maxLength(11)
param namePrefix string
param location string = resourceGroup().location

module stgModule './storageAccount.bicep' = {
  name: 'storageDeploy'
  params: {
    storagePrefix: namePrefix
    location: location
  }
}

output storageEndpoint object = stgModule.outputs.storageEndpoint
```

- **模块**：关键字。
- **符号名称** (stgModule)：模块的标识符。
- **模块文件**：必须使用相对路径引用模块文件。 必须使用正斜杠 (/) 目录分隔符来指定 Bicep 中的所有路径，以确保跨平台编译时的一致性。 不支持 Windows 反斜杠 (\\) 字符。 路径可以包含空格。
- 使用模块时，需要 **_名称_** 属性 (storageDeploy)。 当 Bicep 生成模板 IL 时，此字段将用做为模块生成的嵌套部署资源的名称：

    ```json
    ...
    ...
    "resources": [
      {
        "type": "Microsoft.Resources/deployments",
        "apiVersion": "2020-10-01",
        "name": "storageDeploy",
        "properties": {
          ...
        }
      }
    ]
    ...
    ```
- **_params_** 属性包含要传递给模块文件的任何参数。 这些参数与 Bicep 文件中定义的参数匹配。

与资源一样，模块会并行部署，除非它们依赖于其他模块或资源部署。 若要了解有关依赖项的详细信息，请参阅[设置资源依赖项](resource-declaration.md#set-resource-dependencies)。

若要从模块中获取输出值，请使用类似如下的语法检索属性值：`stgModule.outputs.storageEndpoint` 其中 `stgModule` 是模块的标识符。

可以有条件地部署模块。 使用与[有条件部署资源](conditional-resource-deployment.md)时相同的 if 语法。

```bicep
param deployZone bool

module dnsZone 'dnszones.bicep' = if (deployZone) {
  name: 'myZoneModule'
}
```

## <a name="configure-module-scopes"></a>配置模块作用域

声明模块时，可以提供 _作用域_ 属性来设置部署模块的作用域：

```bicep
module stgModule './storageAccount.bicep' = {
  name: 'storageDeploy'
  scope: resourceGroup('someOtherRg') // pass in a scope to a different resourceGroup
  params: {
    storagePrefix: namePrefix
    location: location
  }
}
```

当模块目标作用域和父目标作用域相同时，可以省略 _作用域_ 属性。 未提供 scope 属性时，将在父级的目标范围部署模块。

以下 Bicep 文件显示了如何创建资源组，及如何将模块部署到资源组：

```bicep
// set the target scope for this file
targetScope = 'subscription'

@minLength(3)
@maxLength(11)
param namePrefix string

param location string = deployment().location

var resourceGroupName = '${namePrefix}rg'
resource myResourceGroup 'Microsoft.Resources/resourceGroups@2020-01-01' = {
  name: resourceGroupName
  location: location
  scope: subscription()
}

module stgModule './storageAccount.bicep' = {
  name: 'storageDeploy'
  scope: myResourceGroup
  params: {
    storagePrefix: namePrefix
    location: location
  }
}

output storageEndpoint object = stgModule.outputs.storageEndpoint
```

scope 属性必须设置为有效的范围对象。 如果 Bicep 文件部署资源组、订阅或管理组，可以将模块的范围设置为该资源的符号名称。 上一个示例演示了此方法，其中创建了资源组，并用于模块的范围。

或者，可使用 scope 函数获取有效的范围。 这些函数包括：

- [resourceGroup](bicep-functions-scope.md#resourcegroup)
- [subscription](bicep-functions-scope.md#subscription)
- [managementGroup](bicep-functions-scope.md#managementgroup)
- [tenant](bicep-functions-scope.md#tenant)

## <a name="next-steps"></a>后续步骤

- 若要浏览教程，请参阅[使用 Bicep 模板部署 Azure 资源](/learn/modules/deploy-azure-resources-by-using-bicep-templates/)。
