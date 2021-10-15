---
title: 了解 Azure 角色定义 - Azure RBAC
description: 了解 Azure 基于角色的访问控制 (Azure RBAC) 中的 Azure 角色定义，以便对 Azure 资源进行精细的访问管理。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: conceptual
ms.workload: identity
ms.date: 09/28/2021
ms.author: rolyon
ms.custom: ''
ms.openlocfilehash: c68849dcb3c0c5683bfc160a0b4a1cb03ae0e13a
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129356059"
---
# <a name="understand-azure-role-definitions"></a>了解 Azure 角色定义

如果想要了解 Azure 角色是如何工作的，或者要创建自己的 [Azure 自定义角色](custom-roles.md)，那么了解角色的定义方法会很有帮助。 本文介绍角色定义的详细信息，并提供了一些示例。

## <a name="role-definition"></a>角色定义

角色定义是权限的集合。 它有时简称为“角色”。 角色定义列出可执行的操作，例如读取、写入和删除。 它还可以列出已从允许的操作中排除的操作，或者与基础数据相关的操作。

以下示例展示了在 Azure PowerShell 中显示的角色定义属性：

```
Name
Id
IsCustom
Description
Actions []
NotActions []
DataActions []
NotDataActions []
AssignableScopes []
```

以下示例展示了在 Azure 门户、Azure CLI 或 REST API 中显示的角色定义属性：

```
roleName
name
type
description
actions []
notActions []
dataActions []
notDataActions []
assignableScopes []
```

下表说明了角色属性的含义。

| 属性 | 说明 |
| --- | --- |
| `Name`</br>`roleName` | 角色的显示名称。 |
| `Id`</br>`name` | 角色的唯一 ID。 内置角色在云之间具有相同的角色 ID。 |
| `IsCustom`</br>`roleType` | 指示此角色是否为自定义角色。 对于自定义角色，设置为 `true` 或 `CustomRole`。 对于内置角色，设置为 `false` 或 `BuiltInRole`。 |
| `Description`</br>`description` | 角色的说明。 |
| `Actions`</br>`actions` | 一个字符串数组，指定该角色允许执行的控制平面操作。 |
| `NotActions`</br>`notActions` | 一个字符串数组，指定已从允许的 `Actions` 中排除的控制平面操作。 |
| `DataActions`</br>`dataActions` | 一个字符串数组，指定该角色允许对该对象中的数据执行的数据平面操作。 |
| `NotDataActions`</br>`notDataActions` | 一个字符串数组，指定已从允许的 `DataActions` 中排除的数据平面操作。 |
| `AssignableScopes`</br>`assignableScopes` | 一个字符串数组，指定角色可用于分配的范围。 |

### <a name="actions-format"></a>操作格式

使用以下格式的字符串指定操作：

- `{Company}.{ProviderName}/{resourceType}/{action}`

操作字符串的 `{action}` 部分指定可以对某个资源类型执行的操作类型。 例如，将在 `{action}` 中看到以下子字符串：

| 操作子字符串    | 说明         |
| ------------------- | ------------------- |
| `*` | 通配符授予对与字符串匹配的所有操作的访问权限。 |
| `read` | 允许读取操作 (GET)。 |
| `write` | 允许写入操作（PUT 或 PATCH）。 |
| `action` | 允许自定义操作，如重启虚拟机 (POST)。 |
| `delete` | 允许删除操作 (DELETE)。 |

### <a name="role-definition-example"></a>角色定义示例

下面是 Azure PowerShell 和 Azure CLI 中显示的[参与者](built-in-roles.md#contributor)角色定义。 `Actions` 下的通配符 (`*`) 操作表示分配给此角色的主体可以执行所有操作，换句话说，它可以管理所有内容。 这包括将来定义的操作，因为 Azure 会添加新的资源类型。 `NotActions` 下的操作会从 `Actions` 中减去。 就[参与者](built-in-roles.md#contributor)角色而言，`NotActions` 去除了此角色管理资源访问权限以及管理 Azure 蓝图分配的能力。

Azure PowerShell 中显示的参与者角色：

```json
{
  "Name": "Contributor",
  "Id": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "IsCustom": false,
  "Description": "Lets you manage everything except access to resources.",
  "Actions": [
    "*"
  ],
  "NotActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action",
    "Microsoft.Blueprint/blueprintAssignments/write",
    "Microsoft.Blueprint/blueprintAssignments/delete"
  ],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

Azure CLI 中显示的参与者角色：

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything except access to resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
  "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [
        "Microsoft.Authorization/*/Delete",
        "Microsoft.Authorization/*/Write",
        "Microsoft.Authorization/elevateAccess/Action",
        "Microsoft.Blueprint/blueprintAssignments/write",
        "Microsoft.Blueprint/blueprintAssignments/delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="control-and-data-actions"></a>控制和数据操作

控制平面操作的基于角色的访问控制在角色定义的 `Actions` 和 `NotActions` 属性中指定。 下面是 Azure 中控制平面操作的一些示例：

- 管理存储帐户的访问权限
- 创建、更新或删除 blob 容器
- 删除资源组及其所有资源

如果容器身份验证方法设置为“Azure AD 用户帐户”而不是“访问密钥”，则控制平面访问权限不会继承到数据平面。 此分隔可防止带通配符 (`*`) 的角色无限制地访问数据。 例如，如果用户对订阅具有[读取者](built-in-roles.md#reader)角色，则他们可以查看存储帐户，但他们默认无法查看基础数据。

以前，基于角色的访问控制不用于数据操作。 数据操作的授权因资源提供程序而异。 用于控制平面操作的同一基于角色的访问控制授权模型已扩展到数据平面操作。

为了支持数据平面操作，已将新的数据属性添加到角色定义。 数据平面操作在 `DataActions` 和 `NotDataActions` 属性中指定。 通过添加这些数据属性，可在控制平面与数据平面之间保持隔离。 这可以防止包含通配符 (`*`) 的当前角色分配突然访问数据。 下面是可在 `DataActions` 和 `NotDataActions` 中指定的一些数据平面操作：

- 读取容器中的 Blob 列表
- 在容器中写入存储 Blob
- 删除队列中的消息

下面是[存储 Blob 数据读取者](built-in-roles.md#storage-blob-data-reader)角色定义，其中包含 `Actions` 和 `DataActions` 属性中的操作。 使用此角色可以读取 Blob 容器以及基础 Blob 数据。

Azure PowerShell 中显示的存储 Blob 数据读取者角色：

```json
{
  "Name": "Storage Blob Data Reader",
  "Id": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "IsCustom": false,
  "Description": "Allows for read access to Azure Storage blob containers and data",
  "Actions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/read",
    "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
  ],
  "NotActions": [],
  "DataActions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
  ],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

Azure CLI 中显示的存储 Blob 数据读取者角色：

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "name": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

只有数据平面操作才能添加到 `DataActions` 和 `NotDataActions` 属性。 资源提供程序通过将 `isDataAction` 属性设置为 `true`，来标识哪些操作是数据操作。 若要查看 `isDataAction` 为 `true` 的操作列表，请参阅[资源提供程序操作](resource-provider-operations.md)。 没有数据操作的角色不需要在角色定义中包含 `DataActions` 和 `NotDataActions` 属性。

所有控制平面操作 API 调用的授权由 Azure 资源管理器处理。 数据平面 API 调用的授权由资源提供程序或 Azure 资源管理器处理。

### <a name="data-actions-example"></a>数据操作示例

为了更好地了解控制平面和数据平面操作的工作原理，让我们考虑一个具体的示例。 在订阅范围为 Alice 分配了[所有者](built-in-roles.md#owner)角色。 在存储帐户范围为 Bob 分配了[存储 Blob 数据参与者](built-in-roles.md#storage-blob-data-contributor)角色。 下图演示了此示例。

![基于角色的访问控制已扩展，可支持控制平面和数据平面操作](./media/role-definitions/rbac-data-plane.png)

Alice 的[所有者](built-in-roles.md#owner)角色和 Bob 的[存储 Blob 数据参与者](built-in-roles.md#storage-blob-data-contributor)角色具有以下操作：

所有者

&nbsp;&nbsp;&nbsp;&nbsp;操作<br>
&nbsp;&nbsp;&nbsp;&nbsp;`*`

存储 Blob 数据参与者

&nbsp;&nbsp;&nbsp;&nbsp;操作<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/write`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action`<br>
&nbsp;&nbsp;&nbsp;&nbsp;DataActions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`

由于 Alice 具有订阅范围的通配符 (`*`) 操作，其权限将向下继承，使其可以执行所有控制平面操作。 Alice 可以读取、写入和删除容器。 但是，Alice 在不采取其他步骤的情况下无法执行数据平面操作。 例如，默认情况下，Alice 无法读取容器内的 blob。 若要读取 blob，Alice 必须检索存储访问密钥并使用它们来访问 blob。

Bob 的权限限制为[存储 Blob 数据参与者](built-in-roles.md#storage-blob-data-contributor)角色中指定的 `Actions` 和 `DataActions`。 Bob 可以基于角色执行控制平面和数据平面操作。 例如，Bob 可以读取、写入和删除指定存储帐户中的容器，并可以读取、写入和删除 Blob。

有关存储的控制平面和数据平面安全性的详细信息，请参阅 [Azure 存储安全指南](../storage/blobs/security-recommendations.md)。

### <a name="what-tools-support-using-azure-roles-for-data-actions"></a>哪些工具支持使用 Azure 角色进行数据操作？

若要查看和处理数据操作，必须安装正确版本的工具或 SDK：

| 工具  | 版本  |
|---------|---------|
| [Azure PowerShell](/powershell/azure/install-az-ps) | 1.1.0 或更高版本 |
| [Azure CLI](/cli/azure/install-azure-cli) | 2.0.30 或更高版本 |
| [Azure for .NET](/dotnet/azure/) | 2.8.0-preview 或更高版本 |
| [Azure SDK for Go](/azure/go/azure-sdk-go-install) | 15.0.0 或更高版本 |
| [Azure for Java](/java/azure/) | 1.9.0 或更高版本 |
| [Azure for Python](/azure/python/) | 0.40.0 或更高版本 |
| [用于 Ruby 的 Azure SDK](https://rubygems.org/gems/azure_sdk) | 0.17.1 或更高版本 |

若要在 REST API 中查看和使用数据操作，必须将 api-version 参数设置为以下版本或更高版本：

- 2018-07-01

## <a name="actions"></a>操作

`Actions` 权限指定该角色允许执行的控制平面操作。 它是用于标识 Azure 资源提供程序安全对象操作的字符串的集合。 下面是可在 `Actions` 中使用的控制平面操作的一些示例。

> [!div class="mx-tableFixed"]
> | 操作字符串    | 说明         |
> | ------------------- | ------------------- |
> | `*/read` | 向所有 Azure 资源提供程序的所有资源类型的读取操作授予访问权限。|
> | `Microsoft.Compute/*` | 向 Microsoft.Compute 资源提供程序中的所有资源类型的所有操作授予访问权限。|
> | `Microsoft.Network/*/read` | 向 Microsoft.Network 资源提供程序中的所有资源类型的读取操作授予访问权限。|
> | `Microsoft.Compute/virtualMachines/*` | 向虚拟机及其子资源类型的所有操作授予访问权限。|
> | `microsoft.web/sites/restart/Action` | 授予重启 Web 应用的访问权限。|

## <a name="notactions"></a>NotActions

`NotActions` 权限指定从允许的包含通配符 (`*`) 的 `Actions` 中减去或排除的控制平面操作。 如果通过从包含通配符 (`*`) 的 `Actions` 中减去操作可更方便地定义要允许的操作集，请使用 `NotActions` 权限。 通过从 `Actions` 操作中减去 `NotActions` 操作可以计算出角色授予的访问权限（有效权限）。

`Actions - NotActions = Effective control plane permissions`

下表显示了 [Microsoft.CostManagement](resource-provider-operations.md#microsoftcostmanagement) 通配符操作的有效控制平面权限的两个示例：

> [!div class="mx-tableFixed"]
> | Actions | NotActions | 有效控制平面权限 |
> | --- | --- | --- |
> | `Microsoft.CostManagement/exports/*` | *无* | `Microsoft.CostManagement/exports/action`</br>`Microsoft.CostManagement/exports/read`</br>`Microsoft.CostManagement/exports/write`</br>`Microsoft.CostManagement/exports/delete`</br>`Microsoft.CostManagement/exports/run/action` |
> | `Microsoft.CostManagement/exports/*` | `Microsoft.CostManagement/exports/delete` | `Microsoft.CostManagement/exports/action`</br>`Microsoft.CostManagement/exports/read`</br>`Microsoft.CostManagement/exports/write`</br>`Microsoft.CostManagement/exports/run/action` |

> [!NOTE]
> 如果用户分配到的一个角色排除了 `NotActions` 中的一个操作，而分配到的第二个角色向同一操作授予访问权限，则用户可以执行该操作。 `NotActions` 不是拒绝规则 - 它只是一个简便方法，可在需要排除特定操作时创建一组允许的操作。
>

### <a name="differences-between-notactions-and-deny-assignments"></a>NotActions 和拒绝分配之间的区别

`NotActions` 和拒绝分配并不相同，并且具有不同的用途。 `NotActions` 是从通配符 (`*`) 操作中减去特定操作的一种简便方法。

即使角色分配向用户授予了访问权限，拒绝分配也会阻止用户执行特定的操作。 有关详细信息，请参阅[了解 Azure 拒绝分配](deny-assignments.md)。

## <a name="dataactions"></a>DataActions

`DataActions` 权限指定此角色允许对该对象中的数据执行的数据平面操作。 例如，如果某个用户对某个存储帐户拥有读取 Blob 数据的访问权限，则该用户可以读取该存储帐户中的 Blob。 下面是可在 `DataActions` 中使用的一些数据操作的示例。

> [!div class="mx-tableFixed"]
> | 数据操作字符串    | 说明         |
> | ------------------- | ------------------- |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` | 返回 Blob 或 Blob 列表。 |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` | 返回写入 Blob 的结果。 |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/read` | 返回消息。 |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/*` | 返回消息，或返回写入或删除消息的结果。 |

## <a name="notdataactions"></a>NotDataActions

`NotDataActions` 权限指定从允许的包含通配符 (`*`) 的 `DataActions` 中减去或排除的数据平面操作。 如果通过从包含通配符 (`*`) 的 `DataActions` 中减去操作可更方便地定义要允许的操作集，请使用 `NotDataActions` 权限。 通过从 `DataActions` 操作中减去 `NotDataActions` 操作可以计算出角色授予的访问权限（有效权限）。 每个资源提供程序提供相应的一组 API 用于实现数据操作。

`DataActions - NotDataActions = Effective data plane permissions`

下表显示了 [Microsoft.Storage](resource-provider-operations.md#microsoftstorage) 通配符操作的有效数据平面权限的两个示例：

> [!div class="mx-tableFixed"]
> | DataActions | NotDataActions | 有效数据平面权限 |
> | --- | --- | --- |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/*` | *无* | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/read`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/write`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action` |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/*` | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete`</br> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/read`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/write`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action` |

> [!NOTE]
> 如果用户分配到的一个角色排除了 `NotDataActions` 中的某个数据操作，而分配到的第二个角色向同一数据操作授予访问权限，则该用户可以执行该数据操作。 `NotDataActions` 不是拒绝规则 - 它只是一个简便方法，可在需要排除特定数据操作时创建一组允许的数据操作。
>

## <a name="assignablescopes"></a>AssignableScopes

`AssignableScopes` 属性指定可分配此角色定义的范围（管理组、订阅或资源组）。 只能在需要此角色的管理组、订阅或资源组中分配此角色。 必须使用至少一个管理组、订阅或资源。

内置角色已将 `AssignableScopes` 设置为根范围 (`"/"`)。 根范围指示角色可供在所有范围中进行分配。 有效的可分配范围的示例包括：

> [!div class="mx-tableFixed"]
> | 角色可供分配 | 示例 |
> |----------|---------|
> | 一个订阅 | `"/subscriptions/{subscriptionId1}"` |
> | 两个订阅 | `"/subscriptions/{subscriptionId1}", "/subscriptions/{subscriptionId2}"` |
> | 网络资源组 | `"/subscriptions/{subscriptionId1}/resourceGroups/Network"` |
> | 一个管理组 | `"/providers/Microsoft.Management/managementGroups/{groupId1}"` |
> | 管理组和订阅 | `"/providers/Microsoft.Management/managementGroups/{groupId1}", /subscriptions/{subscriptionId1}",` |
> | 所有范围（仅适用于内置角色） | `"/"` |

要了解自定义角色的 `AssignableScopes`，请参阅 [Azure 自定义角色](custom-roles.md)。

## <a name="next-steps"></a>后续步骤

* [Azure 内置角色](built-in-roles.md)
* [Azure 自定义角色](custom-roles.md)
* [Azure 资源提供程序操作](resource-provider-operations.md)
