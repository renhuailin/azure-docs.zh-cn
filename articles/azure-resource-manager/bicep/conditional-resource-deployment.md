---
title: 使用 Bicep 进行条件部署
description: 介绍如何在 Bicep 中有条件地部署资源。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 07/30/2021
ms.openlocfilehash: be7e52cb093eb9920be5b6e8e9971b7f882f7f06
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124793828"
---
# <a name="conditional-deployment-in-bicep"></a>Bicep 中的条件部署

有时，需要有选择地在 Bicep 中部署资源或模块。 使用 `if` 关键字指定是否部署资源或模块。 条件的值解析为 true 或 false。 如果值为 true，则创建了该资源。 如果值为 false，则未创建该资源。 值只能应用到整个资源或模块。

> [!NOTE]
> 条件部署不会级联到[子资源](child-resource-name-type.md)。 如果要有条件地部署资源及其子资源，需要对每种资源类型应用相同的条件。

### <a name="microsoft-learn"></a>Microsoft Learn

若要详细了解条件并获得实际操作的指导，请参阅“Microsoft Learn”上的[使用条件和循环构建灵活的 Bicep 模板](/learn/modules/build-flexible-bicep-templates-conditions-loops/)。

## <a name="deploy-condition"></a>部署条件

你可以传入一个指示是否部署资源的参数值。 以下示例按条件部署 DNS 区域。

```bicep
param deployZone bool

resource dnsZone 'Microsoft.Network/dnszones@2018-05-01' = if (deployZone) {
  name: 'myZone'
  location: 'global'
}
```

下一个示例有条件地部署模块。

```bicep
param deployZone bool

module dnsZone 'dnszones.bicep' = if (deployZone) {
  name: 'myZoneModule'
}
```

条件可以与依赖项声明一起使用。 对于[显式依赖项](resource-declaration.md#set-resource-dependencies)，Azure 资源管理器会在资源不部署时，自动将其从必需依赖项中删除。 对于隐式依赖项，允许引用条件资源的属性，但这样可能会导致部署错误。

## <a name="new-or-existing-resource"></a>新资源或现有资源

可以使用条件部署来创建新资源或使用现有资源。 以下示例演示如何部署新的存储帐户或使用现有存储帐户。

```bicep
param storageAccountName string
param location string = resourceGroup().location

@allowed([
  'new'
  'existing'
])
param newOrExisting string = 'new'

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = if (newOrExisting == 'new') {
  name: storageAccountName
  location: location
  sku: {
    name: 'Standard_LRS'
    tier: 'Standard'
  }
  kind: 'StorageV2'
  properties: {
    accessTier: 'Hot'
  }
}
```

当参数 `newOrExisting` 设置为 **new** 时，条件的计算结果为 true。 将部署存储帐户。 但是，当 `newOrExisting` 设置为 **existing** 时，条件的计算结果为 false，并且不部署存储帐户。

## <a name="runtime-functions"></a>运行时函数

如果对条件性部署的资源使用 [reference](./bicep-functions-resource.md#reference) 或 [list](./bicep-functions-resource.md#list) 函数，则会对该函数进行评估，即使资源尚未部署。 如果该函数引用某个不存在的资源，则会出现错误。

使用[条件表达式 ?:](./operators-logical.md#conditional-expression--) 运算符可确保仅在部署资源时才针对条件来计算函数。 以下示例模板演示了如何将此函数与仅在特定条件下有效的表达式配合使用。

```bicep
param vmName string
param location string
param logAnalytics string = ''

resource vmName_omsOnboarding 'Microsoft.Compute/virtualMachines/extensions@2017-03-30' = if (!empty(logAnalytics)) {
  name: '${vmName}/omsOnboarding'
  location: location
  properties: {
    publisher: 'Microsoft.EnterpriseCloud.Monitoring'
    type: 'MicrosoftMonitoringAgent'
    typeHandlerVersion: '1.0'
    autoUpgradeMinorVersion: true
    settings: {
      workspaceId: ((!empty(logAnalytics)) ? reference(logAnalytics, '2015-11-01-preview').customerId : json('null'))
    }
    protectedSettings: {
      workspaceKey: ((!empty(logAnalytics)) ? listKeys(logAnalytics, '2015-11-01-preview').primarySharedKey : json('null'))
    }
  }
}

output mgmtStatus string = ((!empty(logAnalytics)) ? 'Enabled monitoring for VM!' : 'Nothing to enable')
```

## <a name="next-steps"></a>后续步骤

* 如果需要有关条件和循环的 Microsoft Learn 模块，请参阅[通过使用条件和循环来生成灵活的 Bicep 模板](/learn/modules/build-flexible-bicep-templates-conditions-loops/)。
* 如需了解有关创建 Bicep 文件的建议，请参阅 [Bicep 最佳做法](best-practices.md)。
* 若要创建资源的多个实例，请参阅 [Bicep 中的资源迭代](loop-resources.md)。
