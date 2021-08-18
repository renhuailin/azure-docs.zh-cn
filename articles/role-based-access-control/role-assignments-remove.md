---
title: 删除 Azure 角色分配 - Azure RBAC
description: 了解如何使用 Azure 门户、Azure PowerShell、Azure CLI 或 REST API 来删除用户、组、服务主体或托管标识对 Azure 资源的访问权限。
services: active-directory
author: rolyon
manager: daveba
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 02/15/2021
ms.author: rolyon
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 48f7cc738b55ef02de1d631d9ed7a41a9bc8a0b0
ms.sourcegitcommit: 1c12bbaba1842214c6578d914fa758f521d7d485
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2021
ms.locfileid: "112988811"
---
# <a name="remove-azure-role-assignments"></a>删除 Azure 角色分配

[Azure 基于角色的访问控制 (Azure RBAC)](../../articles/role-based-access-control/overview.md) 是用于管理 Azure 资源访问权限的授权系统。 若要从 Azure 资源删除访问权限，请删除角色分配。 本文介绍如何使用 Azure 门户、Azure PowerShell、Azure CLI 和 REST API 来删除角色分配。

## <a name="prerequisites"></a>先决条件

若要删除角色分配，你必须具有：

- `Microsoft.Authorization/roleAssignments/delete` 权限，例如[用户访问管理员](../../articles/role-based-access-control/built-in-roles.md#user-access-administrator)或[所有者](../../articles/role-based-access-control/built-in-roles.md#owner)

## <a name="azure-portal"></a>Azure 门户

1. 在要删除访问权限的范围（例如管理组、订阅、资源组或资源）内打开“访问控制(IAM)”。

1. 单击“角色分配”选项卡以查看在此范围内的所有角色分配。

1. 在角色分配列表中，在需删除其角色分配的安全主体旁边添加复选标记。

   ![已选中要删除的角色分配](./media/role-assignments-remove/rg-role-assignments-select.png)

1. 单击 **“删除”** 。

   ![“删除角色分配”消息](./media/shared/remove-role-assignment.png)

1. 在显示的“删除角色分配”消息中，单击“是”。

    此时会显示一条消息，指出无法删除继承的角色分配，而你正在尝试删除子范围的角色分配。 应在角色的分配范围打开“访问控制(IAM)”，然后重试。 在正确的范围打开“访问控制(IAM)”的快捷方法是查看“范围”列，然后单击“(继承)”旁边的链接。

   ![继承的角色分配的“删除角色分配”消息](./media/role-assignments-remove/remove-role-assignment-inherited.png)

## <a name="azure-powershell"></a>Azure PowerShell

在 Azure PowerShell 中，通过使用 [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) 来删除角色分配。

以下示例在 *pharma-sales* 资源组上从 *patlong\@contoso.com* 用户删除“虚拟机参与者”角色分配：

```azurepowershell
PS C:\> Remove-AzRoleAssignment -SignInName patlong@contoso.com `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceGroupName pharma-sales
```

在订阅范围内将[读者](built-in-roles.md#reader)角色从 ID 为 22222222-2222-2222-2222-222222222222 的“Ann Mack 团队”组中删除。

```azurepowershell
PS C:\> Remove-AzRoleAssignment -ObjectId 22222222-2222-2222-2222-222222222222 `
-RoleDefinitionName "Reader" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000"
```

在管理组范围内将[账单读者](built-in-roles.md#billing-reader)角色从 alain\@example.com 用户中删除。

```azurepowershell
PS C:\> Remove-AzRoleAssignment -SignInName alain@example.com `
-RoleDefinitionName "Billing Reader" `
-Scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

如果收到错误消息：“提供的信息未映射到角色分配”，请确保还指定了 `-Scope` 或 `-ResourceGroupName` 参数。 有关详细信息，请参阅 [Azure RBAC 疑难解答](troubleshooting.md#role-assignments-with-identity-not-found)。

## <a name="azure-cli"></a>Azure CLI

在 Azure CLI 中，通过使用 [az role assignment delete](/cli/azure/role/assignment#az_role_assignment_delete) 来删除角色分配。

以下示例在 *pharma-sales* 资源组上从 *patlong\@contoso.com* 用户删除“虚拟机参与者”角色分配：

```azurecli
az role assignment delete --assignee "patlong@contoso.com" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

在订阅范围内将[读者](built-in-roles.md#reader)角色从 ID 为 22222222-2222-2222-2222-222222222222 的“Ann Mack 团队”组中删除。

```azurecli
az role assignment delete --assignee "22222222-2222-2222-2222-222222222222" \
--role "Reader" \
--subscription "00000000-0000-0000-0000-000000000000"
```

在管理组范围内将[账单读者](built-in-roles.md#billing-reader)角色从 alain\@example.com 用户中删除。

```azurecli
az role assignment delete --assignee "alain@example.com" \
--role "Billing Reader" \
--scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

## <a name="rest-api"></a>REST API

在 REST API 中，通过使用[角色分配 - 删除](/rest/api/authorization/roleassignments/delete)来删除角色分配。

1. 获取角色分配标识符 (GUID)。 首次创建角色分配时将返回此标识符，也可以通过列出角色分配来获取它。

1. 从下面的请求开始：

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}?api-version=2015-07-01
    ```

1. 在 URI 内，将“{scope}”替换为删除角色分配的范围。

    > [!div class="mx-tableFixed"]
    > | 作用域 | 类型 |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | 管理组 |
    > | `subscriptions/{subscriptionId1}` | 订阅 |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | 资源组 |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | 资源 |

1. 将“{roleAssignmentId}”替换为角色分配的 GUID 标识符。

以下请求在订阅范围内删除指定的角色分配：

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId1}/providers/microsoft.authorization/roleassignments/{roleAssignmentId1}?api-version=2015-07-01
```

下面显示了输出示例：

```json
{
    "properties": {
        "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
        "principalId": "{objectId1}",
        "scope": "/subscriptions/{subscriptionId1}",
        "createdOn": "2020-05-06T23:55:24.5379478Z",
        "updatedOn": "2020-05-06T23:55:24.5379478Z",
        "createdBy": "{createdByObjectId1}",
        "updatedBy": "{updatedByObjectId1}"
    },
    "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId1}",
    "type": "Microsoft.Authorization/roleAssignments",
    "name": "{roleAssignmentId1}"
}
```

## <a name="arm-template"></a>ARM 模板

无法使用 Azure 资源管理器模板（ARM 模板）来删除角色分配。 若要删除角色分配，必须使用其他工具，如 Azure 门户、Azure PowerShell、Azure CLI 或 REST API。

## <a name="next-steps"></a>后续步骤

- [使用 Azure 门户列出 Azure 角色分配](role-assignments-list-portal.md)
- [使用 Azure PowerShell 列出 Azure 角色分配](role-assignments-list-powershell.md)
- [排查 Azure RBAC 问题](troubleshooting.md)
