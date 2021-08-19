---
title: 了解资源锁定
description: 了解用于在分配蓝图时保护资源的 Azure 蓝图中的锁定选项。
ms.date: 08/17/2021
ms.topic: conceptual
ms.openlocfilehash: 1f209cb1e350739bfecdf86e125b28dcd4866501
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122324306"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>了解 Azure 蓝图中的资源锁定

仅当存在一个可以维护该一致性的机制时，大规模创建一致的环境才会真正有价值。 本文介绍 Azure 蓝图中的资源锁定的工作原理。 若要查看资源锁定的示例以及“拒绝分配”的应用，请参阅[保护新资源](../tutorials/protect-new-resources.md)教程。

> [!NOTE]
> 由 Azure 蓝图部署的资源锁仅适用于蓝图分配部署的[非扩展资源](../../../azure-resource-manager/templates/scope-extension-resources.md)。 现有资源（如资源组中已存在的资源）不会添加锁定。

## <a name="locking-modes-and-states"></a>锁定模式和状态

锁定模式适用于蓝图分配，具有三个选项：“不锁定”、“只读”或“不删除”。   在蓝图分配过程中的项目部署过程中配置锁定模式。 可以通过更新蓝图分配来设置不同的锁定模式。
但是，不能在 Azure 蓝图外部更改锁定模式。

由蓝图分配中的项目创建的资源有四种状态：“未锁定”、“只读”、“无法编辑/删除”或“无法删除”。 每种项目类型都可以处于“未锁定”状态。 下表可以用于确定资源的状态：

|Mode|项目资源类型|状态|说明|
|-|-|-|-|
|不锁定|*|未锁定|资源不受 Azure 蓝图保护。 此状态也用于从蓝图分配外部添加到“只读”或“不要删除”资源组项目的资源。|
|只读|资源组|无法编辑/删除|资源组是只读的，资源组上的标记无法修改。 可以从此资源组添加、移动、更改或删除“未锁定”资源。|
|只读|非资源组|只读|以任何方式都无法更改资源。 无更改且无法删除。|
|不要删除|*|无法删除|资源可以更改，但无法删除。 可以从此资源组添加、移动、更改或删除“未锁定”资源。|

## <a name="overriding-locking-states"></a>重写锁定状态

通常可以允许在订阅上具有合适的 [Azure 基于角色的访问控制 (Azure RBAC)](../../../role-based-access-control/overview.md) 的某人（例如“所有者”角色）更改或删除任何资源。 当 Azure 蓝图在已部署的分配中应用了锁定时，无法进行此访问。 如果使用“只读”或“不要删除”选项设置了分配，则即使订阅所有者也无法对受保护资源执行阻止的操作。

此安全措施可以保护已定义的蓝图与设计用于通过意外或以编程方式删除或更改创建的环境之间的一致性。

### <a name="assign-at-management-group"></a>在管理组中分配

阻止订阅所有者删除蓝图分配的唯一选项是将蓝图分配到管理组。 在此方案中，只有管理组的 **所有者** 具有删除蓝图分配所需的权限。

若要将蓝图分配到管理组而不是订阅，REST API 调用会更改为如下所示：

```http
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{assignmentMG}/providers/Microsoft.Blueprint/blueprintAssignments/{assignmentName}?api-version=2018-11-01-preview
```

`{assignmentMG}` 定义的管理组必须位于管理组层次结构中，或者是保存蓝图定义的同一管理组。

蓝图分配的请求正文如下所示：

```json
{
    "identity": {
        "type": "SystemAssigned"
    },
    "location": "eastus",
    "properties": {
        "description": "enforce pre-defined simpleBlueprint to this XXXXXXXX subscription.",
        "blueprintId": "/providers/Microsoft.Management/managementGroups/{blueprintMG}/providers/Microsoft.Blueprint/blueprints/simpleBlueprint",
        "scope": "/subscriptions/{targetSubscriptionId}",
        "parameters": {
            "storageAccountType": {
                "value": "Standard_LRS"
            },
            "costCenter": {
                "value": "Contoso/Online/Shopping/Production"
            },
            "owners": {
                "value": [
                    "johnDoe@contoso.com",
                    "johnsteam@contoso.com"
                ]
            }
        },
        "resourceGroups": {
            "storageRG": {
                "name": "defaultRG",
                "location": "eastus"
            }
        }
    }
}
```

此请求正文和分配给订阅的主要差异是 `properties.scope` 属性。 此需要的属性必须设置为蓝图分配适用的订阅。 订阅必须是存储蓝图分配的管理组层次结构的直接子级。

> [!NOTE]
> 分配到管理组范围的蓝图仍作为订阅级别蓝图分配运行。 唯一的区别是，存储蓝图分配的位置可防止订阅所有者删除分配和关联的锁定。

## <a name="removing-locking-states"></a>删除锁定状态

如果需要修改或删除受分配保护的资源，则可通过两种方法来实现。

- 将蓝图分配更新为“不锁定”锁定模式
- 删除蓝图分配

删除分配后，将删除由 Azure 蓝图创建的锁定。 但是，资源会留在原地，需要通过正常方式删除。

## <a name="how-blueprint-locks-work"></a>蓝图锁定的工作原理

如果蓝图分配选择了“只读”或“不要删除”选项，则会在分配期间将 Azure RBAC [拒绝分配](../../../role-based-access-control/deny-assignments.md)拒绝操作应用于项目资源。 该拒绝操作由蓝图分配的托管标识添加，并且只能通过同一托管标识从项目资源中删除。 此安全措施将强制实施锁定机制，并防止在 Azure 蓝图外部删除蓝图锁定。

:::image type="content" source="../media/resource-locking/blueprint-deny-assignment.png" alt-text="资源组的访问控制 (I A M) 页和拒绝分配选项卡的屏幕截图。" border="false":::

每个模式的[拒绝分配属性](../../../role-based-access-control/deny-assignments.md#deny-assignment-properties)如下所示：

|Mode |Permissions.Actions |Permissions.NotActions |Principals[i].Type |ExcludePrincipals[i].Id | DoNotApplyToChildScopes |
|-|-|-|-|-|-|
|只读 |**\** _ |_ *\*/read **<br />** Microsoft.Authorization/locks/delete **<br />** Microsoft.Network/virtualNetwork/subnets/join/action** |SystemDefined (Everyone) |“excludedPrincipals”中的蓝图分配和用户定义 |资源组 - “true”；资源 - “false”  |
|不要删除 |**\*/delete** | **Microsoft.Authorization/locks/delete**<br />**Microsoft.Network/virtualNetwork/subnets/join/action** |SystemDefined (Everyone) |“excludedPrincipals”中的蓝图分配和用户定义 |资源组 - “true”；资源 - “false”  |

> [!IMPORTANT]
> Azure 资源管理器可以将角色分配详细信息缓存最多 30 分钟。 因此，蓝图资源上的拒绝分配拒绝操作可能不会立即完全生效。 在此时间段内，可能无法删除将由蓝图锁保护的资源。

## <a name="exclude-a-principal-from-a-deny-assignment"></a>从拒绝分配中排除主体

在某些设计或安全方案中，可能需要将主体从蓝图分配创建的[拒绝分配](../../../role-based-access-control/deny-assignments.md)中排除。 此步骤在 REST API 中完成，方法是在[创建分配](/rest/api/blueprints/assignments/createorupdate)时，将最多 5 个值添加到“锁定”属性中的“excludedPrincipals”数组。  下面的分配定义是包含“excludedPrincipals”的请求正文示例：

```json
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "eastus",
  "properties": {
    "description": "enforce pre-defined simpleBlueprint to this XXXXXXXX subscription.",
    "blueprintId": "/providers/Microsoft.Management/managementGroups/{mgId}/providers/Microsoft.Blueprint/blueprints/simpleBlueprint",
    "locks": {
        "mode": "AllResourcesDoNotDelete",
        "excludedPrincipals": [
            "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
            "38833b56-194d-420b-90ce-cff578296714"
        ]
    },
    "parameters": {
      "storageAccountType": {
        "value": "Standard_LRS"
      },
      "costCenter": {
        "value": "Contoso/Online/Shopping/Production"
      },
      "owners": {
        "value": [
          "johnDoe@contoso.com",
          "johnsteam@contoso.com"
        ]
      }
    },
    "resourceGroups": {
      "storageRG": {
        "name": "defaultRG",
        "location": "eastus"
      }
    }
  }
}
```

## <a name="exclude-an-action-from-a-deny-assignment"></a>从拒绝分配中排除操作

与在蓝图分配中的[拒绝分配](../../../role-based-access-control/deny-assignments.md)上[排除主体](#exclude-a-principal-from-a-deny-assignment)类似，可以排除特定的 [Azure 资源提供程序操作](../../../role-based-access-control/resource-provider-operations.md)。 在“properties.locks”块中，可以在“excludedPrincipals”所在的同一位置添加“excludedActions”：  

```json
"locks": {
    "mode": "AllResourcesDoNotDelete",
    "excludedPrincipals": [
        "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
        "38833b56-194d-420b-90ce-cff578296714"
    ],
    "excludedActions": [
        "Microsoft.ContainerRegistry/registries/push/write",
        "Microsoft.Authorization/*/read"
    ]
},
```

尽管“excludedPrincipals”必须是显式的，但“excludedActions”条目可将 `*` 用于通配符匹配资源提供程序操作。 

## <a name="next-steps"></a>后续步骤

- 遵循[保护新资源](../tutorials/protect-new-resources.md)教程操作。
- 了解[蓝图生命周期](./lifecycle.md)。
- 了解如何使用[静态和动态参数](./parameters.md)。
- 了解如何自定义[蓝图排序顺序](./sequencing-order.md)。
- 了解如何[更新现有分配](../how-to/update-existing-assignments.md)。
- 使用[一般故障排除](../troubleshoot/general.md)在蓝图的分配期间解决问题。
