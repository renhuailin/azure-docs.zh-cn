---
title: 扩展资源类型的范围 (Bicep)
description: 介绍如何在使用 Bicep 部署扩展资源类型时使用 scope 属性。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 59b6576dfb1bd5e0ac4f56e6a59b6ea4d5c4b5f4
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/02/2021
ms.locfileid: "111026000"
---
# <a name="set-scope-for-extension-resources-in-bicep"></a>在 Bicep 中设置扩展资源的范围

扩展资源是用于修改其他资源的资源。 例如，可以为资源分配角色。 角色分配是扩展资源类型。

有关扩展资源类型的完整列表，请参阅[用于扩展其他资源的功能的资源类型](../management/extension-resource-types.md)。

本文介绍如何在使用 Bicep 文件进行部署时设置扩展资源类型的范围。 它介绍了在应用到资源时可用于扩展资源的 scope 属性。

> [!NOTE]
> scope 属性仅适用于扩展资源类型。 若要为非扩展类型的资源类型指定不同的范围，请使用[模块](modules.md)。

## <a name="apply-at-deployment-scope"></a>在部署范围内应用

若要在目标部署范围内应用扩展资源类型，请将该资源添加到模板中，就像应用任何资源类型一样。 可用的范围是[资源组](deploy-to-resource-group.md)、[订阅](deploy-to-subscription.md)、[管理组](deploy-to-management-group.md)和[租户](deploy-to-tenant.md)。 部署范围必须支持该资源类型。

以下模板会部署一个锁。

```bicep
resource createRgLock 'Microsoft.Authorization/locks@2016-09-01' = {
  name: 'rgLock'
  properties: {
    level: 'CanNotDelete'
    notes: 'Resource group should not be deleted.'
  }
}
```

下一个示例会分配角色。

```bicep
targetScope = 'subscription'

@description('The principal to assign the role to')
param principalId string

@allowed([
  'Owner'
  'Contributor'
  'Reader'
])
@description('Built-in role to assign')
param builtInRoleType string

@description('A new GUID used to identify the role assignment')
param roleNameGuid string = newGuid()

var role = {
  Owner: '/subscriptions/${subscription().subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635'
  Contributor: '/subscriptions/${subscription().subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c'
  Reader: '/subscriptions/${subscription().subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7'
}

resource roleAssignSub 'Microsoft.Authorization/roleAssignments@2020-04-01-preview' = {
  name: roleNameGuid
  properties: {
    roleDefinitionId: role[builtInRoleType]
    principalId: principalId
  }
}
```

## <a name="apply-to-resource"></a>应用于资源

若要将扩展资源应用于资源，请使用 `scope` 属性。 将 scope 属性设置为要将扩展添加到其中的资源的名称。 scope 属性是扩展资源类型的根属性。

下面的示例创建一个存储帐户并对其应用角色。

```bicep
@description('The principal to assign the role to')
param principalId string

@allowed([
  'Owner'
  'Contributor'
  'Reader'
])
@description('Built-in role to assign')
param builtInRoleType string

@description('A new GUID used to identify the role assignment')
param roleNameGuid string = newGuid()
param location string = resourceGroup().location

var role = {
  Owner: '/subscriptions/${subscription().subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635'
  Contributor: '/subscriptions/${subscription().subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c'
  Reader: '/subscriptions/${subscription().subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7'
}
var uniqueStorageName = 'storage${uniqueString(resourceGroup().id)}'

resource storageName 'Microsoft.Storage/storageAccounts@2019-04-01' = {
  name: uniqueStorageName
  location: location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}

resource roleAssignStorage 'Microsoft.Authorization/roleAssignments@2020-04-01-preview' = {
  name: roleNameGuid
  properties: {
    roleDefinitionId: role[builtInRoleType]
    principalId: principalId
  }
  scope: storageName
  dependsOn: [
    storageName
  ]
}
```

## <a name="next-steps"></a>后续步骤

若要了解如何部署到范围，请参阅：

* [资源组部署](deploy-to-resource-group.md)
* [订阅部署](deploy-to-subscription.md)
* [管理组部署](deploy-to-management-group.md)
* [租户部署](deploy-to-tenant.md)