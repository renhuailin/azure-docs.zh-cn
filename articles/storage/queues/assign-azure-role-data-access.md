---
title: 分配 Azure 角色以访问队列数据
titleSuffix: Azure Storage
description: 了解如何通过 Azure 基于角色的访问控制 (Azure RBAC) 向 Azure Active Directory 安全主体分配队列数据的权限。 Azure 存储支持通过 Azure AD 使用内置角色和 Azure 自定义角色进行身份验证和授权。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/07/2021
ms.author: tamram
ms.reviewer: sohamnc
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c10386f6a81fabed61efd63aa4dffe61fe9e5cd7
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111982066"
---
# <a name="assign-an-azure-role-for-access-to-queue-data"></a>分配 Azure 角色以访问队列数据

Azure Active Directory (Azure AD) 通过 [Azure 基于角色的访问控制 (Azure RBAC)](../../role-based-access-control/overview.md) 授予对受保护资源的访问权限。 Azure 存储定义了一组 Azure 内置角色，它们包含用于访问队列的通用权限集。

将 Azure 角色分配到 Azure AD 安全主体后，Azure 会向该安全主体授予对这些资源的访问权限。 可以将访问权限限定于订阅、资源组、存储帐户或单个队列级别。 Azure AD 安全主体可以是用户、组、应用程序服务主体，也可以是 [Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。

本文介绍如何分配 Azure 角色以访问队列数据。

## <a name="azure-roles-for-queues"></a>用于队列的 Azure 角色

[!INCLUDE [storage-auth-rbac-roles-queue-include](../../../includes/storage-auth-rbac-roles-queue-include.md)]

## <a name="determine-resource-scope"></a>确定资源范围

[!INCLUDE [storage-auth-resource-scope-queue-include](../../../includes/storage-auth-resource-scope-queue-include.md)]

## <a name="assign-an-azure-role"></a>分配 Azure 角色

可以使用 Azure 门户、PowerShell 或 Azure CLI 分配角色以访问数据。

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

若要使用 Azure AD 凭据访问 Azure 门户中的队列数据，用户必须具有以下角色分配：

- 数据访问角色，如“存储队列数据参与者”
- Azure 资源管理器“读者”角色

若要了解如何将这些角色分配给用户，请按照 [使用 Azure 门户分配 Azure 角色](../../role-based-access-control/role-assignments-portal.md)中提供的说明进行操作。

“读取者”角色是一个 Azure 资源管理器角色，可让用户查看存储帐户资源，但不允许修改这些资源。  该角色不提供对 Azure 存储中的数据的读取权限，而只提供对帐户管理资源的读取权限。 “读者”角色是必需的，以便用户导航到 Azure 门户中的队列和消息。

例如，如果在名为 sample-queue 的队列级别将“存储队列数据参与者”角色分配给用户 Mary，则会向 Mary 授予对该队列的读取、写入和删除访问权限 。 但是，如果 Mary 想要查看 Azure 门户中的队列，则“存储队列数据参与者”角色本身不会提供通过门户导航到队列并进行查看的足够权限。 必须拥有其他权限才能在门户中导航和查看门户中显示的其他资源。

必须向用户分配“读者”角色，才能结合使用 Azure 门户与 Azure AD 凭据。 但是，如果已为用户分配具有“Microsoft.Storage/storageAccounts/listKeys/action”权限的角色，则用户可以通过共享密钥授权结合使用门户与存储帐户密钥。 若要使用存储帐户密钥，必须允许存储帐户访问共享密钥。 有关允许或禁止共享密钥访问的详细信息，请参阅[阻止对 Azure 存储帐户进行共享密钥授权](../common/shared-key-authorization-prevent.md)。

你还可以分配 Azure 资源管理器角色，该角色提供比“读者”角色更多的权限。 建议的最佳安全做法是分配尽可能少的权限。 有关详细信息，请参阅 [Azure RBAC 的最佳做法](../../role-based-access-control/best-practices.md)。

> [!NOTE]
> 在为自己分配数据访问角色之前，可以通过 Azure 门户访问存储帐户中的数据，因为 Azure 门户还可以使用帐户密钥进行数据访问。 有关详细信息，请参阅[选择如何在 Azure 门户中授予对队列数据的访问权限](../queues/authorize-data-operations-portal.md)。
>
> Azure 门户中存储资源管理器的预览版不支持使用 Azure AD 凭据来查看和修改队列数据。 Azure 门户中的存储资源管理器始终使用帐户密钥来访问数据。 若要在 Azure 门户中使用存储资源管理器，你必须被分配一个包含 **Microsoft.Storage/storageAccounts/listkeys/action** 的角色。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要将 Azure 角色分配给安全主体，请调用 [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) 命令。 命令的格式因分配范围而异。 若要运行该命令，必须具有一个角色，该角色包含在相应范围或更大范围内向你分配的 Microsoft.Authorization/roleAssignments/write 权限。

若要分配队列范围的角色，请为 `--scope` 参数指定一个包含队列范围的字符串。 队列的范围采用以下格式：

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue-name>
```

以下示例为用户分配 **存储队列数据参与者** 角色，其范围为名为 *sample-queue* 的队列。 请务必将括号中的示例值和占位符值替换为你自己的值： 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/sample-queue"
```

有关使用 PowerShell 在订阅、资源组或存储帐户范围内分配角色的信息，请参阅[使用 Azure PowerShell 分配 Azure 角色](../../role-based-access-control/role-assignments-powershell.md)。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要向安全主体分配 Azure 角色，请使用 [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) 命令。 命令的格式因分配范围而异。 命令的格式因分配范围而异。 若要运行该命令，必须具有一个角色，该角色包含在相应范围或更大范围内向你分配的 Microsoft.Authorization/roleAssignments/write 权限。

若要分配队列范围的角色，请为 `--scope` 参数指定一个包含队列范围的字符串。 队列的范围采用以下格式：

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue-name>
```

以下示例为用户分配 **存储队列数据参与者** 角色，其范围为队列级别。 请务必将括号中的示例值和占位符值替换为你自己的值：

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue>"
```

有关使用 PowerShell 在订阅、资源组或存储帐户范围内分配角色的信息，请参阅[使用 Azure CLI 分配 Azure 角色](../../role-based-access-control/role-assignments-cli.md)。

---

请记住以下有关 Azure 存储中 Azure 角色分配的要点：

- 创建 Azure 存储帐户时，系统不会自动向你分配通过 Azure AD 访问数据的权限。 你必须为自己显式分配一个用于 Azure 存储的 Azure 角色。 可以在订阅、资源组、存储帐户或队列级别分配它。
- 如果存储帐户被 Azure 资源管理器只读锁锁定，那么该锁将阻止将范围限定为存储帐户或队列的 Azure 角色分配。

## <a name="next-steps"></a>后续步骤

- [什么是 Azure 基于角色的访问控制 (Azure RBAC)？](../../role-based-access-control/overview.md)
- [Azure RBAC 最佳做法](../../role-based-access-control/best-practices.md)