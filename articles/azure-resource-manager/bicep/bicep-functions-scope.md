---
title: Bicep 函数 - 范围
description: 介绍要在 Bicep 文件中用于检索部署范围相关值的函数。
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 2bf09aef4e3b59f6718a96c6ce2650d508872106
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/02/2021
ms.locfileid: "111025963"
---
# <a name="scope-functions-for-bicep"></a>Bicep 的范围函数

资源管理器提供以下用于在 Bicep 文件中获取范围值的函数：

* [managementGroup](#managementgroup)
* [resourceGroup](#resourcegroup)
* [subscription](#subscription)
* [tenant](#tenant)

## <a name="managementgroup"></a>managementGroup

`managementGroup()`

`managementGroup(name)`

返回一个用于将范围设置为管理组的对象。

### <a name="remarks"></a>备注

`managementGroup()` 只能对[管理组部署](deploy-to-management-group.md)使用。 它返回部署操作的当前管理组。

`managementGroup(name)` 可用于任何部署范围。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| name |否 |字符串 |要部署到的管理组的唯一标识符。 请不要使用管理组的显示名称。 如果未提供值，则返回当前管理组。 |

### <a name="return-value"></a>返回值

一个用于在[模块](modules.md#configure-module-scopes)或[扩展资源类型](scope-extension-resources.md)上设置 `scope` 属性的对象。

### <a name="management-group-example"></a>管理组示例

以下示例将模块的范围设置为管理组。

```bicep
param managementGroupName string

module  'module.bicep' = {
  name: 'deployToMG'
  scope: managementGroup(managementGroupName)
}
``` 

## <a name="resourcegroup"></a>resourceGroup

`resourceGroup()`

`resourceGroup(resourceGroupName)`

`resourceGroup(subscriptionId, resourceGroupName)`

返回一个用于将范围设置为资源组的对象。

或

返回表示当前资源组的对象。

### <a name="remarks"></a>备注

resourceGroup 函数具有两种不同的用法。 一种用法是在[模块](modules.md#configure-module-scopes)或[扩展资源类型](scope-extension-resources.md)上设置范围。 另一种用法是获取有关当前资源组的详细信息。 该函数的调用方式确定了其用法。 用于设置 `scope` 属性时，它将返回范围对象。

`resourceGroup()` 可用于设置范围，或获取有关资源组的详细信息。

`resourceGroup(resourceGroupName)` 和 `resourceGroup(subscriptionId, resourceGroupName)` 只可用于设置范围。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| resourceGroupName |否 |字符串 | 要部署到的资源组的名称。 如果未提供值，则返回当前资源组。 |
| subscriptionId |否 |字符串 |要部署到的订阅的唯一标识符。 如果未提供值，则返回当前订阅。 |

### <a name="return-value"></a>返回值

用于设置范围时，该函数将返回一个对于模块或扩展资源类型的 `scope` 属性有效的对象。

用于获取有关资源组的详细信息时，该函数将返回以下格式：

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "type":"Microsoft.Resources/resourceGroups",
  "location": "{resourceGroupLocation}",
  "managedBy": "{identifier-of-managing-resource}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

仅对包含由其他服务托管的资源的资源组返回 managedBy 属性。 对于托管应用程序、Databricks 和 AKS，该属性的值为管理资源的资源 ID。

### <a name="resource-group-example"></a>资源组示例

以下示例将模块的范围设置为资源组。

```bicep
param resourceGroupName string

module exampleModule 'module.bicep' = {
  name: 'exampleModule'
  scope: resourceGroup(resourceGroupName)
}
```

下一个示例返回资源组的属性。

```bicep
output resourceGroupOutput object = resourceGroup()
```

它返回采用以下格式的对象：

```json
{
  "id": "/subscriptions/{subscription-id}/resourceGroups/examplegroup",
  "name": "examplegroup",
  "type":"Microsoft.Resources/resourceGroups",
  "location": "southcentralus",
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

resourceGroup 函数的一个常见用途是在与资源组相同的位置中创建资源。 以下示例使用资源组位置作为默认参数值。

```bicep
param location string = resourceGroup().location
```

还可以使用 resourceGroup 函数将资源组中的标记应用于资源。 有关详细信息，请参阅[应用资源组中的标记](../management/tag-resources.md#apply-tags-from-resource-group)。

## <a name="subscription"></a>订阅

`subscription()`

`subscription(subscriptionId)`

返回一个用于将范围设置为订阅的对象。

或

返回有关当前部署的订阅的详细信息。

### <a name="remarks"></a>备注

subscription 函数具有两种不同的用法。 一种用法是在[模块](modules.md#configure-module-scopes)或[扩展资源类型](scope-extension-resources.md)上设置范围。 另一种用法是获取有关当前订阅的详细信息。 该函数的调用方式确定了其用法。 用于设置 `scope` 属性时，它将返回范围对象。

`subscription(subscriptionId)` 只可用于设置范围。

`subscription()` 可用于设置范围，或获取有关订阅的详细信息。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| subscriptionId |否 |字符串 |要部署到的订阅的唯一标识符。 如果未提供值，则返回当前订阅。 |

### <a name="return-value"></a>返回值

用于设置范围时，该函数将返回一个对于模块或扩展资源类型的 `scope` 属性有效的对象。

用于获取有关订阅的详细信息时，该函数将返回以下格式：

```json
{
  "id": "/subscriptions/{subscription-id}",
  "subscriptionId": "{subscription-id}",
  "tenantId": "{tenant-id}",
  "displayName": "{name-of-subscription}"
}
```

### <a name="subscription-example"></a>订阅示例

以下示例将模块的范围设置为订阅。

```bicep
module exampleModule 'module.bicep' = {
  name: 'deployToSub'
  scope: subscription()
}
```

下一个示例返回订阅的详细信息。

```bicep
output subscriptionOutput object = subscription()
```

## <a name="tenant"></a>tenant

`tenant()`

返回一个用于将范围设置为租户的对象。

### <a name="remarks"></a>备注

`tenant()` 可与任何部署范围配合使用。 它始终返回当前租户。

### <a name="return-value"></a>返回值

一个用于在[模块](modules.md#configure-module-scopes)或[扩展资源类型](scope-extension-resources.md)上设置 `scope` 属性的对象。

### <a name="tenant-example"></a>租户示例

以下示例演示一个已部署到租户的模块。

```bicep
module exampleModule 'module.bicep' = {
  name: 'deployToTenant'
  scope: tenant()
}
```

## <a name="next-steps"></a>后续步骤

若要详细了解部署范围，请参阅：

* [资源组部署](deploy-to-resource-group.md)
* [订阅部署](deploy-to-subscription.md)
* [管理组部署](deploy-to-management-group.md)
* [租户部署](deploy-to-tenant.md)
