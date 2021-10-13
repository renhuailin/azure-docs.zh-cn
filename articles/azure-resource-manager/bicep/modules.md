---
title: Bicep 模块
description: 介绍如何定义和使用模块，以及如何使用模块范围。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 10/05/2021
ms.openlocfilehash: bd5069db6a2ad9cb14f5f0b3bc28612afa519727
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129619588"
---
# <a name="use-bicep-modules"></a>使用 Bicep 模块

使用 Bicep 可将复杂的解决方案分解为模块。 Bicep 模块只是从另一个 Bicep 文件部署的 Bicep 文件。 可以将资源声明的复杂详细信息封装在一个模块中，从而提高使用该模块的文件的可读性。 这些模块可以重复使用，并与他人共享。 Bicep 模块被转换为单个 Azure 资源管理器模板，其中包含用于部署的[嵌套模板](../templates/linked-templates.md#nested-template)。

本文介绍如何定义和使用模块。

有关教程，请参阅[使用 Bicep 模板部署 Azure 资源](/learn/modules/deploy-azure-resources-by-using-bicep-templates/)。

## <a name="define-modules"></a>定义模块

每个 Bicep 文件都可以用作模块。 模块仅将参数和输出作为协定公开给其他 Bicep 文件。 参数和输出都是可选的。

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

module stgModule 'storageAccount.bicep' = {
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

可以使用循环多次部署模块。 有关详细信息，请参阅 [Bicep 中的模块迭代](loop-modules.md)。

## <a name="configure-module-scopes"></a>配置模块作用域

声明模块时，可以为模块设置一个与 Bicep 包含文件的范围不同的范围。 使用 `scope` 属性设置模块的范围。 未提供 scope 属性时，将在父级的目标范围部署模块。

以下 Bicep 文件显示了如何创建资源组，及如何将模块部署到资源组：

```bicep
// set the target scope for this file
targetScope = 'subscription'

@minLength(3)
@maxLength(11)
param namePrefix string

param location string = deployment().location

var resourceGroupName = '${namePrefix}rg'
resource myResourceGroup 'Microsoft.Resources/resourceGroups@2021-04-01' = {
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

下一个示例部署到现有资源组。

```bicep
targetScope = 'subscription'

resource firstRG 'Microsoft.Resources/resourceGroups@2021-04-01' existing = {
  name: 'demogroup1'
}

resource secondRG 'Microsoft.Resources/resourceGroups@2021-04-01' existing = {
  name: 'demogroup2'
}

module storage1 'storageAccount.bicep' = {
  name: 'westusdeploy'
  scope: firstRG
  params: {
    storagePrefix: 'stg1'
    location: 'westus'
  }
}

module storage2 'storageAccount.bicep' = {
  name: 'eastusdeploy'
  scope: secondRG
  params: {
    storagePrefix: 'stg2'
    location: 'eastus'
  }
}
```

scope 属性必须设置为有效的范围对象。 如果 Bicep 文件部署资源组、订阅或管理组，可以将模块的范围设置为该资源的符号名称。 或者，可使用 scope 函数获取有效的范围。

这些函数包括：

- [resourceGroup](bicep-functions-scope.md#resourcegroup)
- [subscription](bicep-functions-scope.md#subscription)
- [managementGroup](bicep-functions-scope.md#managementgroup)
- [tenant](bicep-functions-scope.md#tenant)

下面的示例使用 `managementGroup` 函数来设置范围。

```bicep
param managementGroupName string

module  'module.bicep' = {
  name: 'deployToMG'
  scope: managementGroup(managementGroupName)
}
```

## <a name="next-steps"></a>后续步骤

- 若要将敏感值传递给模块，请使用 [getSecret](bicep-functions-resource.md#getsecret) 函数。
- 可以使用循环多次部署模块。 有关详细信息，请参阅 [Bicep 中的模块迭代](loop-modules.md)。
