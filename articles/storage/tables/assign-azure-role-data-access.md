---
title: 分配用于访问表数据（预览版）的 Azure 角色
titleSuffix: Azure Storage
description: 了解如何通过 Azure 基于角色的访问控制 (Azure RBAC) 向 Azure Active Directory 安全主体分配表数据（预览版）的权限。 Azure 存储支持通过 Azure AD 使用内置和 Azure 自定义角色进行身份验证和授权。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/13/2021
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 19c377c1b6d0b5cad7515ca199133f8d10c8742b
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113760600"
---
# <a name="assign-an-azure-role-for-access-to-table-data-preview"></a>分配用于访问表数据（预览版）的 Azure 角色

Azure Active Directory (Azure AD) 通过 [Azure 基于角色的访问控制 (Azure RBAC)](../../role-based-access-control/overview.md) 授权访问受保护的资源。 Azure 存储定义一组 Azure 内置角色，它们包含用于访问 Azure 存储（预览版）中的表数据的通用权限集。

将 Azure 角色分配到 Azure AD 安全主体后，Azure 会向该安全主体授予对这些资源的访问权限。 Azure AD 安全主体可以是用户、组、应用程序服务主体，也可以是 [Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。

若要详细了解如何使用 Azure AD 来授予对表数据的访问权限，请参阅[使用 Azure Active Directory 授予对表的访问权限](authorize-access-azure-active-directory.md)。

> [!IMPORTANT]
> 使用 Azure AD 授权访问表的功能当前处于“预览”状态。 有关 beta 版本、预览版或尚未正式发布的版本的 Azure 功能所适用的法律条款，请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="assign-an-azure-role"></a>分配 Azure 角色

可以使用 PowerShell、Azure CLI 或 Azure 资源管理器模板为数据访问分配角色。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

要将 Azure 角色分配给安全主体，请调用 [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) 命令。 命令的格式因分配范围而异。 要运行该命令，必须在相应的范围或更高的范围内拥有已分配 Microsoft.Authorization/roleAssignments/write 权限的角色。

若要分配表范围的角色，请为 `--scope` 参数指定一个包含表范围的字符串。 表的范围采用以下格式：

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/tableServices/default/tables/<table-name>
```

以下示例为用户分配“存储表数据参与者”角色（其范围为表）。 请务必将括号中的示例值和占位符值替换为你自己的值：

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Table Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/tableServices/default/tables/<table>"
```

有关在订阅、资源组或存储帐户范围内使用 PowerShell 分配角色的信息，请参阅[使用 Azure PowerShell 分配 Azure 角色](../../role-based-access-control/role-assignments-powershell.md)。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要向安全主体分配 Azure 角色，请使用 [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) 命令。 命令的格式因分配范围而异。 命令的格式因分配范围而异。 要运行该命令，必须在相应的范围或更高的范围内拥有已分配 Microsoft.Authorization/roleAssignments/write 权限的角色。

若要分配表范围的角色，请为 `--scope` 参数指定一个包含表范围的字符串。 表的范围采用以下格式：

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/tableServices/default/tables/<table-name>
```

以下示例为用户分配“存储表数据参与者”角色（其范围为表级别）。 请务必将括号中的示例值和占位符值替换为你自己的值：

```azurecli-interactive
az role assignment create \
    --role "Storage Table Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/tableServices/default/tables/<table>"
```

有关在订阅、资源组或存储帐户范围内使用 PowerShell 分配角色的信息，请参阅[使用 Azure CLI 分配 Azure 角色](../../role-based-access-control/role-assignments-cli.md)。

# <a name="template"></a>[模板](#tab/template)

若要了解如何使用 Azure 资源管理器模板分配 Azure 角色，请参阅[使用 Azure 资源管理器模板分配 Azure 角色](../../role-based-access-control/role-assignments-template.md)。

---

请记住以下有关 Azure 存储中 Azure 角色分配的要点：

- 创建 Azure 存储帐户时，系统不会自动向你分配通过 Azure AD 访问数据的权限。 你必须为自己显式分配一个用于 Azure 存储的 Azure 角色。 可以在订阅、资源组、存储帐户或表的级别分配该角色。
- 如果使用 Azure 资源管理器只读锁锁定存储帐户，则该锁会阻止其范围限定为存储帐户或表的 Azure 角色的分配。
- Azure 门户中的存储资源管理器的预览版不支持使用 Azure AD 凭据来查看和修改表数据。 Azure 门户中的存储资源管理器始终使用帐户密钥来访问数据。 若要在 Azure 门户中使用存储资源管理器，你必须被分配一个包含 **Microsoft.Storage/storageAccounts/listkeys/action** 的角色。

## <a name="next-steps"></a>后续步骤

- [什么是 Azure 基于角色的访问控制 (Azure RBAC)？](../../role-based-access-control/overview.md)
- [Azure RBAC 最佳做法](../../role-based-access-control/best-practices.md)
