---
title: Bicep 模块
description: 介绍如何定义和使用模块及如何使用模块作用域。
ms.topic: conceptual
ms.date: 03/30/2021
ms.openlocfilehash: 6c325bbbe265e13241119761373985ca4552b158
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967874"
---
# <a name="use-bicep-modules-preview"></a>使用 Bicep 模块（预览）

Bicep 可以将复杂的解决方案分解为模块。 Bicep 模块是要一起部署的一个或多个资源的集合。 模块会抽象出原始资源声明的复杂细节，从而提高可读性。 这些模块可以重复使用，并与他人共享。 模块与[模板规格](./template-specs.md)结合使用，为模块化和代码重用创建了方法。 Bicep 模块被转译为包含用于部署的[嵌套模板](./linked-templates.md#nested-template)的一个 ARM 模板。 在 Bicep 中，自动处理 [dependsOn](./template-syntax.md#resources)。

如需教程，请参阅[教程：添加 Bicep 模块](./bicep-tutorial-add-modules.md)。

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
- **模块文件**：在此示例中，模块的路径使用相对路径 (./storage.bicep) 来指定。 必须使用正斜杠 (/) 目录分隔符来指定 Bicep 中的所有路径，以确保跨平台编译时的一致性。 不支持 Windows 反斜杠 (\\) 字符。
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

若要从模块中获取输出值，请使用类似如下的语法检索属性值：`stgModule.outputs.storageEndpoint` 其中 `stgModule` 是模块的标识符。

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

当模块目标作用域和父目标作用域相同时，可以省略 _作用域_ 属性。 如果未提供作用域属性，则模块将部署在父目标作用域。

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

## <a name="next-steps"></a>后续步骤

- 若要完成教程，请参阅[教程：添加 Bicep 模块](./bicep-tutorial-add-modules.md)。
