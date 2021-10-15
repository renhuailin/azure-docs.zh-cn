---
title: Azure 自定义角色 - Azure RBAC
description: 了解如何使用 Azure 基于角色的访问控制 (Azure RBAC) 创建 Azure 自定义角色，以便对 Azure 资源进行精细的访问权限管理。
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: conceptual
ms.workload: identity
ms.date: 08/27/2021
ms.author: rolyon
ms.openlocfilehash: 31da46a7eb6a021a14641d13f9c76ab7406f9b94
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129363411"
---
# <a name="azure-custom-roles"></a>Azure 自定义角色

> [!IMPORTANT]
> 将管理组添加到 `AssignableScopes` 的功能目前为预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

如果 [Azure 内置角色](built-in-roles.md)不满足组织的特定需求，你可以创建自己的自定义角色。 与内置角色一样，可将自定义角色分配到管理组（仅以预览版提供）、订阅和资源组范围内的用户、组与服务主体。

自定义角色可在信任同一 Azure AD 目录的订阅之间共享。 每个目录都有 **5,000** 个自定义角色的限制。 （对于 Azure 德国和 Azure 中国世纪互联，限制为 2,000 个自定义角色。）可以使用 Azure 门户、Azure PowerShell、Azure CLI 或 REST API 创建自定义角色。

## <a name="steps-to-create-a-custom-role"></a>创建自定义角色的步骤

下面是创建自定义角色的基本步骤。

1. 确定所需的权限。

    创建自定义角色时，需要清楚可以执行的操作以定义权限。 通常，我们会从一个现有的内置角色着手，并根据需要对其进行修改。 将操作添加到[角色定义](role-definitions.md)的 `Actions` 或 `NotActions` 属性。 如果有数据操作，请将这些操作添加到 `DataActions` 或 `NotDataActions` 属性。

    有关详细信息，请参阅下一部分：[如何确定所需的权限](#how-to-determine-the-permissions-you-need)。

1. 确定如何创建自定义角色。

    可以使用 [Azure 门户](custom-roles-portal.md)、[Azure PowerShell](custom-roles-powershell.md)、[Azure CLI](custom-roles-cli.md) 或 [REST API](custom-roles-rest.md) 创建自定义角色。

1. 创建自定义角色。

    最简单的方法是使用 Azure 门户。 要查看使用 Azure 门户创建自定义角色的步骤，请参阅[使用 Azure 门户创建或更新 Azure 自定义角色](custom-roles-portal.md)。

1. 测试自定义角色。

    创建自定义角色后，必须对其进行测试，以验证它是否按预期工作。 如果以后需要进行调整，可以更新自定义角色。

## <a name="how-to-determine-the-permissions-you-need"></a>如何确定所需的权限

Azure 具有数千个权限，你可将这些权限包含在自定义角色中。 可通过一些方法来确定要添加到自定义角色的权限：

- 查看现有的[内置角色](built-in-roles.md)。

    你可能需要修改现有角色或组合多个角色中使用的权限。

- 列出要授予对其的访问权限的 Azure 服务。

- 确定[映射到 Azure 服务的资源提供程序](../azure-resource-manager/management/azure-services-resource-providers.md)。

    Azure 服务通过[资源提供程序](../azure-resource-manager/management/overview.md)公开其功能和权限。 例如，Microsoft.Compute 资源提供程序提供虚拟机资源，而 Microsoft.Billing 资源提供程序提供订阅和计费资源。 了解资源提供程序可以帮助你缩小和确定自定义角色所需的权限。

    使用 Azure 门户创建自定义角色时，还可以通过搜索关键字来确定资源提供程序。 [使用 Azure 门户创建或更新 Azure 自定义角色](custom-roles-portal.md#step-4-permissions)中介绍了此搜索功能。

    ![包含资源提供程序的“添加权限”窗格](./media/shared/add-permissions-provider.png)

- 搜索[可用权限](resource-provider-operations.md)，找到要包括的权限。

    使用 Azure 门户创建自定义角色时，可以按关键字搜索权限。 例如，可以搜索“虚拟机”或“计费”权限。  你还可以将所有权限下载为 CSV 文件，然后搜索此文件。 [使用 Azure 门户创建或更新 Azure 自定义角色](custom-roles-portal.md#step-4-permissions)中介绍了此搜索功能。

    ![“添加权限”列表](./media/shared/add-permissions-list.png)

## <a name="custom-role-example"></a>自定义角色示例

下面展示了使用 Azure PowerShell 以 JSON 格式显示的自定义角色。 自定义角色可以用于监视和重新启动虚拟机。

```json
{
  "Name": "Virtual Machine Operator",
  "Id": "88888888-8888-8888-8888-888888888888",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/subscriptions/{subscriptionId1}",
    "/subscriptions/{subscriptionId2}",
    "/providers/Microsoft.Management/managementGroups/{groupId1}"
  ]
}
```

下面展示了使用 Azure CLI 显示的相同自定义角色。

```json
[
  {
    "assignableScopes": [
      "/subscriptions/{subscriptionId1}",
      "/subscriptions/{subscriptionId2}",
      "/providers/Microsoft.Management/managementGroups/{groupId1}"
    ],
    "description": "Can monitor and restart virtual machines.",
    "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/88888888-8888-8888-8888-888888888888",
    "name": "88888888-8888-8888-8888-888888888888",
    "permissions": [
      {
        "actions": [
          "Microsoft.Storage/*/read",
          "Microsoft.Network/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action",
          "Microsoft.Authorization/*/read",
          "Microsoft.ResourceHealth/availabilityStatuses/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Insights/diagnosticSettings/*",
          "Microsoft.Support/*"
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "Virtual Machine Operator",
    "roleType": "CustomRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

## <a name="custom-role-properties"></a>自定义角色属性

下表说明了自定义角色属性的含义。

| 属性 | 必须 | 类型 | 说明 |
| --- | --- | --- | --- |
| `Name`</br>`roleName` | 是 | String | 自定义角色的显示名称。 虽然角色定义是管理组或订阅级资源，但角色定义可以在共享同一 Azure AD 目录的多个订阅中使用。 此显示名称在 Azure AD 目录范围内必须是唯一的。 可以包含字母、数字、空格和特殊字符。 最多包含 128 个字符。 |
| `Id`</br>`name` | 是 | String | 自定义角色的唯一 ID。 如果使用 Azure PowerShell 和 Azure CLI，在创建新角色时会自动生成此 ID。 |
| `IsCustom`</br>`roleType` | 是 | String | 指示此角色是否为自定义角色。 对于自定义角色，设置为 `true` 或 `CustomRole`。 对于内置角色，设置为 `false` 或 `BuiltInRole`。 |
| `Description`</br>`description` | 是 | String | 自定义角色的说明。 可以包含字母、数字、空格和特殊字符。 最多包含 1024 个字符。 |
| `Actions`</br>`actions` | 是 | String[] | 一个字符串数组，指定该角色允许执行的控制平面操作。 有关详细信息，请参阅 [Actions](role-definitions.md#actions)。 |
| `NotActions`</br>`notActions` | 否 | String[] | 一个字符串数组，指定已从允许的 `Actions` 中排除的控制平面操作。 有关详细信息，请参阅 [NotActions](role-definitions.md#notactions)。 |
| `DataActions`</br>`dataActions` | 否 | String[] | 一个字符串数组，指定该角色允许对该对象中的数据执行的数据平面操作。 如果使用 `DataActions` 来创建自定义角色，则无法在管理组范围内分配该角色。 有关详细信息，请参阅 [DataActions](role-definitions.md#dataactions)。 |
| `NotDataActions`</br>`notDataActions` | 否 | String[] | 一个字符串数组，指定已从允许的 `DataActions` 中排除的数据平面操作。 有关详细信息，请参阅 [NotDataActions](role-definitions.md#notdataactions)。 |
| `AssignableScopes`</br>`assignableScopes` | 是 | String[] | 一个字符串数组，指定自定义角色的可分配范围。 只能在自定义角色的 `AssignableScopes` 中定义一个管理组。 将管理组添加到 `AssignableScopes` 的功能目前处于预览状态。 有关详细信息，请参阅 [AssignableScopes](role-definitions.md#assignablescopes)。 |

权限字符串不区分大小写。 创建自定义角色时，约定是匹配可以在其中找到 [Azure 资源提供程序操作](resource-provider-operations.md)中所述权限的大小写。

## <a name="wildcard-permissions"></a>通配符权限

`Actions`、`NotActions`、`DataActions` 和 `NotDataActions` 支持使用通配符 (`*`) 来定义权限。 通配符 (`*`) 将权限扩展到与所提供的操作字符串匹配的所有内容。 例如，假设你要添加与 Azure 成本管理和导出相关的所有权限。 可以添加所有这些操作字符串：

```
Microsoft.CostManagement/exports/action
Microsoft.CostManagement/exports/read
Microsoft.CostManagement/exports/write
Microsoft.CostManagement/exports/delete
Microsoft.CostManagement/exports/run/action
```

也可以不添加所有这些字符串，而只需添加一个通配符字符串。 例如，以下通配符字符串等效于上面的五个字符串。 此通配符权限还包括将来可能要添加的任何导出权限。

```
Microsoft.CostManagement/exports/*
```

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>谁可以创建、删除、更新或查看自定义角色

与在内置角色中一样，`AssignableScopes` 属性指定角色的可配置范围。 自定义角色的 `AssignableScopes` 属性还控制谁可以创建、删除、更新或查看自定义角色。

| 任务 | 操作 | 说明 |
| --- | --- | --- |
| 创建/删除自定义角色 | `Microsoft.Authorization/ roleDefinitions/write` | 在自定义角色的所有 `AssignableScopes` 上被允许此操作的用户可以创建（或删除）用于这些范围的自定义角色。 例如，管理组、订阅和资源组的[所有者](built-in-roles.md#owner)和[用户访问管理员](built-in-roles.md#user-access-administrator)。 |
| 更新自定义角色 | `Microsoft.Authorization/ roleDefinitions/write` | 被授权在自定义角色的所有 `AssignableScopes` 上执行此操作的用户可以更新这些范围中的自定义角色。 例如，管理组、订阅和资源组的[所有者](built-in-roles.md#owner)和[用户访问管理员](built-in-roles.md#user-access-administrator)。 |
| 查看自定义角色 | `Microsoft.Authorization/ roleDefinitions/read` | 在某个范围内被允许此操作的用户可以查看可在该范围内分配的自定义角色。 所有内置角色都允许自定义角色可用于分配。 |

## <a name="custom-role-limits"></a>自定义角色限制

以下列表描述了对自定义角色的限制。

- 每个目录最多可以有 **5000** 个自定义角色。
- Azure 德国和 Azure 中国世纪互联的每个目录最多可以有 2000 个自定义角色。
- 不能将 `AssignableScopes` 设置为根范围 (`"/"`)。
- 不能在 `AssignableScopes` 中使用通配符 (`*`)。 此通配符限制有助于确保用户不可能通过更新角色定义来获取对某个范围的访问权限。
- 只能在自定义角色的 `AssignableScopes` 中定义一个管理组。 将管理组添加到 `AssignableScopes` 的功能目前为预览版。
- 操作字符串中只能有一个通配符。
- 无法在管理组范围内分配具有 `DataActions` 的自定义角色。
- Azure 资源管理器不验证管理组是否存在于角色定义的可分配范围中。

有关自定义角色和管理组的详细信息，请参阅[什么是 Azure 管理组？](../governance/management-groups/overview.md#azure-custom-role-definition-and-assignment)。

## <a name="input-and-output-formats"></a>输入和输出格式

要使用命令行创建自定义角色，通常使用 JSON 来指定自定义角色的属性。 根据所使用的工具，输入和输出格式看起来会稍有不同。 本部分列出了不同工具的输入和输出格式。

### <a name="azure-powershell"></a>Azure PowerShell

要使用 Azure PowerShell 创建自定义角色，必须提供以下输入。

```json
{
  "Name": "",
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

要使用 Azure PowerShell 更新自定义角色，必须提供以下输入。 请注意，已添加 `Id` 属性。 

```json
{
  "Name": "",
  "Id": "",
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

下面显示了使用 Azure PowerShell 和 [ConvertTo-Json](/powershell/module/microsoft.powershell.utility/convertto-json) 命令列出自定义角色时的输出示例。 

```json
{
  "Name": "",
  "Id": "",
  "IsCustom": true,
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

### <a name="azure-cli"></a>Azure CLI

要使用 Azure CLI 创建或更新自定义角色，必须提供以下输入。 此格式与使用 Azure PowerShell 创建自定义角色时生成的格式相同。

```json
{
  "Name": "",
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

下面显示了使用 Azure CLI 列出自定义角色时的输出示例。

```json
[
  {
    "assignableScopes": [],
    "description": "",
    "id": "",
    "name": "",
    "permissions": [
      {
        "actions": [],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "",
    "roleType": "CustomRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

### <a name="rest-api"></a>REST API

要使用 REST API 创建或更新自定义角色，必须提供以下输入。 此格式与使用 Azure 门户创建自定义角色时生成的格式相同。

```json
{
  "properties": {
    "roleName": "",
    "description": "",
    "assignableScopes": [],
    "permissions": [
      {
        "actions": [],
        "notActions": [],
        "dataActions": [],
        "notDataActions": []
      }
    ]
  }
}
```

下面显示了使用 REST API 列出自定义角色时的输出示例。

```json
{
    "properties": {
        "roleName": "",
        "type": "CustomRole",
        "description": "",
        "assignableScopes": [],
        "permissions": [
            {
                "actions": [],
                "notActions": [],
                "dataActions": [],
                "notDataActions": []
            }
        ],
        "createdOn": "",
        "updatedOn": "",
        "createdBy": "",
        "updatedBy": ""
    },
    "id": "",
    "type": "Microsoft.Authorization/roleDefinitions",
    "name": ""
}
```

## <a name="next-steps"></a>后续步骤

- [教程：使用 Azure PowerShell 创建 Azure 自定义角色](tutorial-custom-role-powershell.md)
- [教程：使用 Azure CLI 创建 Azure 自定义角色](tutorial-custom-role-cli.md)
- [了解 Azure 角色定义](role-definitions.md)
- [排查 Azure RBAC 问题](troubleshooting.md)
