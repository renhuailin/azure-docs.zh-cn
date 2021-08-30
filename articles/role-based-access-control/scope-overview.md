---
title: 了解 Azure RBAC 的范围
description: 了解 Azure 基于角色的访问控制 (Azure RBAC) 的范围，以及如何确定资源的范围。
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 08/09/2021
ms.author: rolyon
ms.openlocfilehash: 3fc3d614ef26235325e0b9a9e8fee68d2bf919a5
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121862581"
---
# <a name="understand-scope-for-azure-rbac"></a>了解 Azure RBAC 的范围

范围是访问权限应用到的资源集。 在分配角色时，请了解范围，以便为安全主体授予它真正需要的最低访问权限，这一点很重要。 通过限制范围，可以限制在安全主体受到入侵的情况下会有哪些资源面临风险。

## <a name="scope-levels"></a>范围级别

在 Azure 中，可以在四个级别指定范围：[管理组](../governance/management-groups/overview.md)、订阅、[资源组](../azure-resource-manager/management/overview.md#resource-groups)和资源。 范围采用父子关系结构。 层次结构的每个级别都会使范围更具针对性。 可以在其中任何一个范围级别分配角色。 所选级别决定了角色的应用广泛程度。 较低级别继承较高级别的角色权限。 

![角色分配的范围](./media/scope-overview/rbac-scope-no-label.png)

管理组是高于订阅的范围级别，但管理组支持更复杂的层次结构。 下图显示了可以定义的管理组和订阅的层次结构的示例。 有关管理组的详细信息，请参阅[什么是 Azure 管理组？](../governance/management-groups/overview.md)。

![管理组和订阅层次结构](./media/scope-overview/rbac-scope-management-groups.png)

## <a name="scope-format"></a>范围格式

如果使用命令行分配角色，则需要指定范围。 对于命令行工具，范围是一个可能会很长的字符串，用于标识角色分配的确切范围。 在 Azure 门户中，此范围通常作为 *资源 ID* 列出。

范围由一系列标识符组成，标识符之间用斜杠 (/) 字符分隔。 可以将此字符串视为表示以下层次结构，其中没有占位符的文本 (`{}`) 是固定标识符：

```
/subscriptions
    /{subscriptionId}
        /resourcegroups
            /{resourceGroupName}
                /providers
                    /{providerName}
                        /{resourceType}
                            /{resourceSubType1}
                                /{resourceSubType2}
                                    /{resourceName}
```

- `{subscriptionId}` 是要使用的订阅的 ID (GUID)。
- `{resourcesGroupName}` 是包含资源组的名称。
- `{providerName}` 是处理资源的[资源提供程序](../azure-resource-manager/management/azure-services-resource-providers.md)的名称，`{resourceType}` 和 `{resourceSubType*}` 标识该资源提供程序内更多的级别。
- `{resourceName}` 是标识特定资源的字符串的最后一部分。

管理组是高于订阅的级别，并且具有最广泛（针对性最低）的范围。 此级别的角色分配会应用于该管理组内的订阅。 管理组的范围具有以下格式：

```
/providers
    /Microsoft.Management
        /managementGroups
            /{managmentGroupName}
```

## <a name="scope-examples"></a>范围示例

> [!div class="mx-tableFixed"]
> | 范围 | 示例 |
> | --- | --- |
> | 管理组 | `/providers/Microsoft.Management/managementGroups/marketing-group` |
> | 订阅 | `/subscriptions/00000000-0000-0000-0000-000000000000` |
> | 资源组 | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg` |
> |  | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales` |
> | 资源 | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01` |
> |  | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVirtualNetwork12345` |

## <a name="how-to-determine-the-scope-for-a-resource"></a>如何确定资源的范围

确定管理组、订阅或资源组的范围是相当简单的。 只需知道名称和订阅 ID。 不过，确定资源范围时所需完成的工作会稍多一些。 以下是几种确定资源范围的方法。

- 在 Azure 门户中，打开资源，然后查看属性。 该资源应该会列出可用于确定范围的 **资源 ID**。 例如，以下是某个存储帐户的资源 ID。

    ![Azure 门户中存储帐户的资源 ID](./media/scope-overview/scope-resource-id.png)

- 另一种方法是，使用 Azure 门户在资源范围临时分配一个角色，然后使用 [Azure PowerShell](role-assignments-list-powershell.md) 或 [Azure CLI](role-assignments-list-cli.md) 来列出角色分配。 在输出中，该范围将会作为属性列出。

    ```azurepowershell
    RoleAssignmentId   : /subscriptions/<subscriptionId>/resourceGroups/test-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01/pro
                         viders/Microsoft.Authorization/roleAssignments/<roleAssignmentId>
    Scope              : /subscriptions/<subscriptionId>/resourceGroups/test-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01
    DisplayName        : User
    SignInName         : user@contoso.com
    RoleDefinitionName : Storage Blob Data Reader
    RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
    ObjectId           : <principalId>
    ObjectType         : User
    CanDelegate        : False
    Description        :
    ConditionVersion   :
    Condition          :
    ```

    ```azurecli
    {
        "canDelegate": null,
        "condition": null,
        "conditionVersion": null,
        "description": null,
        "id": "/subscriptions/{subscriptionId}/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
        "name": "{roleAssignmentId}",
        "principalId": "{principalId}",
        "principalName": "user@contoso.com",
        "principalType": "User",
        "resourceGroup": "test-rg",
        "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
        "roleDefinitionName": "Storage Blob Data Reader",
        "scope": "/subscriptions/{subscriptionId}/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01",
        "type": "Microsoft.Authorization/roleAssignments"
      }
    ```

## <a name="scope-and-arm-templates"></a>范围和 ARM 模板

角色分配是 Azure 资源管理器中的一种特殊类型，称为扩展资源。 扩展资源是扩展另一资源的功能的资源。 它们始终作为另一资源的扩展（如子资源）存在。 例如，订阅范围的角色分配是订阅的扩展资源。 角色分配的名称始终是要扩展的资源的名称加上 `/Microsoft.Authorization/roleAssignments/{roleAssignmentId}`。 使用 Azure 资源管理器模板（ARM 模板）分配角色时，通常不需要提供范围。 原因在于范围字段最后始终为要扩展的资源的 ID。 范围可以从角色分配本身的 ID 确定。 下表显示了角色分配 ID 和相应范围的示例：

> [!div class="mx-tableFixed"]
> | 角色分配 ID | 范围 |
> | --- | --- |
> | `/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}` | `/subscriptions/{subscriptionId}` |
> | `/subscriptions/{subscriptionId}/resourceGroups/Example-Storage-rg/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}` | `/subscriptions/{subscriptionId}/resourceGroups/Example-Storage-rg` |

有关范围和 ARM 模板详细信息，请参阅[使用 Azure 资源管理器模板分配角色](role-assignments-template.md)。 有关扩展资源类型的完整列表，请参阅[用于扩展其他资源的功能的资源类型](../azure-resource-manager/management/extension-resource-types.md)。

## <a name="next-steps"></a>后续步骤

- [分配 Azure 角色的步骤](role-assignments-steps.md)
- [Azure 服务的资源提供程序](../azure-resource-manager/management/azure-services-resource-providers.md)
- [什么是 Azure 管理组？](../governance/management-groups/overview.md)
