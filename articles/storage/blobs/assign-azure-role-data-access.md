---
title: 分配 Azure 角色以访问 blob 数据
titleSuffix: Azure Storage
description: 了解如何通过 Azure 基于角色的访问控制 (Azure RBAC) 向 Azure Active Directory 安全主体分配 blob 数据的权限。 Azure 存储支持通过 Azure AD 使用内置和 Azure 自定义角色进行身份验证和授权。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/13/2021
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b30bb21369e75b76c5aba299d38b2ccfc3259803
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128651591"
---
# <a name="assign-an-azure-role-for-access-to-blob-data"></a>分配 Azure 角色以访问 blob 数据

Azure Active Directory (Azure AD) 通过 [Azure 基于角色的访问控制 (Azure RBAC)](../../role-based-access-control/overview.md) 授予对受保护资源的访问权限。 Azure 存储定义了一组 Azure 内置角色，其中包含用于访问 blob 数据的常用权限集。

将 Azure 角色分配到 Azure AD 安全主体后，Azure 会向该安全主体授予对这些资源的访问权限。 Azure AD 安全主体可以是用户、组、应用程序服务主体，也可以是 [Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。

若要详细了解如何使用 Azure AD 来授予对 blob 数据的访问权限，请参阅[使用 Azure Active Directory 授予对 blob 的访问权限](authorize-access-azure-active-directory.md)。

> [!NOTE]
> 本文演示如何分配 Azure 角色以访问存储帐户中的 blob 数据。 若要了解如何为 Azure 存储中的管理操作分配角色，请参阅[使用 Azure 存储资源提供程序访问管理资源](../common/authorization-resource-provider.md)。

## <a name="assign-an-azure-role"></a>分配 Azure 角色

可以使用 Azure 门户、PowerShell、Azure CLI 或 Azure 资源管理器模板为数据访问分配角色。

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

要使用 Azure AD 凭据访问 Azure 门户中的 blob 数据，用户必须具有以下角色分配：

- 数据访问角色，如“存储 Blob 数据参与者”
- Azure 资源管理器“读者”角色

要了解如何将这些角色分配给用户，请按照 [使用 Azure 门户分配 Azure 角色](../../role-based-access-control/role-assignments-portal.md)中提供的说明进行操作。

“读取者”角色是一个 Azure 资源管理器角色，可让用户查看存储帐户资源，但不允许修改这些资源。  该角色不提供对 Azure 存储中的数据的读取权限，而只提供对帐户管理资源的读取权限。 “读者”角色是必需的，以便用户可以导航到 Azure 门户中的 blob 容器。

例如，如果在名为 **sample-container** 的容器级别向用户 Mary 分配“存储 Blob 数据参与者”角色，则会向 Mary 授予对该容器中所有 Blob 的读取、写入和删除访问权限。 但是，如果 Mary 希望在 Azure 门户中查看某个 Blob，“存储 Blob 数据参与者”角色本身无法提供足够的权限用于在门户中导航，因此 Mary 无法查看该 Blob。  必须拥有其他权限，才能在门户中导航和查看门户中显示的其他资源。

必须为用户分配“读者”角色，才能将 Azure 门户与 Azure AD 凭据结合使用。 但是，如果已为用户分配具有 Microsoft.Storage/storageAccounts/listKeys/action 权限的角色，则用户可以通过共享密钥授权，将该门户与存储帐户密钥结合使用。 要使用存储帐户密钥，必须允许存储帐户访问共享密钥。 有关允许或禁止共享密钥访问的详细信息，请参阅[阻止对 Azure 存储帐户进行共享密钥授权](../common/shared-key-authorization-prevent.md)。

还可以分配 Azure 资源管理器角色，提供比“读者”角色更多的权限。 建议将分配尽可能少的权限作为一种安全最佳做法。 有关详细信息，请参阅 [Azure RBAC 的最佳做法](../../role-based-access-control/best-practices.md)。

> [!NOTE]
> 在为自己分配数据访问角色之前，可以通过 Azure 门户访问存储帐户中的数据，因为 Azure 门户还可以使用帐户密钥进行数据访问。 有关详细信息，请参阅[选择如何在 Azure 门户中授予对 blob 数据的访问权限](../blobs/authorize-data-operations-portal.md)。
>
> Azure 门户中存储资源管理器的预览版不支持使用 Azure AD 凭据来查看和修改 blob 数据。 Azure 门户中的存储资源管理器始终使用帐户密钥来访问数据。 若要在 Azure 门户中使用存储资源管理器，你必须被分配一个包含 **Microsoft.Storage/storageAccounts/listkeys/action** 的角色。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 为安全主体分配 Azure 角色，请调用 [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) 命令。 命令的格式因分配范围而异。 要运行该命令，必须在相应的范围或更高的范围内拥有已分配 Microsoft.Authorization/roleAssignments/write 权限的角色。

若要分配容器范围的角色，请为 `--scope` 参数指定一个包含容器范围的字符串。 容器的范围采用以下格式：

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
```

以下示例为用户分配 **存储 Blob 数据参与者** 角色，其范围为名为 *sample-container* 的容器。 请务必将括号中的示例值和占位符值替换为你自己的值：

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/sample-container"
```

有关在订阅、资源组或存储帐户范围内使用 PowerShell 分配角色的信息，请参阅[使用 Azure PowerShell 分配 Azure 角色](../../role-based-access-control/role-assignments-powershell.md)。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 为安全主体分配 Azure 角色，请使用 [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) 命令。 命令的格式因分配范围而异。 命令的格式因分配范围而异。 要运行该命令，必须在相应的范围或更高的范围内拥有已分配 Microsoft.Authorization/roleAssignments/write 权限的角色。

若要分配容器范围的角色，请为 `--scope` 参数指定一个包含容器范围的字符串。 容器的范围采用以下格式：

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
```

以下示例为用户分配 **存储 Blob 数据参与者** 角色，其范围为容器级别。 请务必将括号中的示例值和占位符值替换为你自己的值：

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container>"
```

有关在订阅、资源组或存储帐户范围内使用 PowerShell 分配角色的信息，请参阅[使用 Azure CLI 分配 Azure 角色](../../role-based-access-control/role-assignments-cli.md)。

# <a name="template"></a>[模板](#tab/template)

若要了解如何使用 Azure 资源管理器模板分配 Azure 角色，请参阅[使用 Azure 资源管理器模板分配 Azure 角色](../../role-based-access-control/role-assignments-template.md)。

---

请记住以下有关 Azure 存储中 Azure 角色分配的要点：

- 创建 Azure 存储帐户时，系统不会自动向你分配通过 Azure AD 访问数据的权限。 你必须为自己显式分配一个用于 Azure 存储的 Azure 角色。 可以在订阅、资源组、存储帐户、容器级别分配该角色。
- 如果使用 Azure 资源管理器只读锁锁定存储帐户，则该锁会阻止将范围限定为存储帐户或容器的 Azure 角色分配。
- 如果设置了适当的权限以允许通过 Azure AD 访问数据，却无法访问数据，你会收到“AuthorizationPermissionMismatch”错误。 请务必留出足够的时间来复制在 Azure AD 中所做的权限更改，并确保没有任何阻止访问的拒绝分配，请参阅[了解 Azure 拒绝分配](../../role-based-access-control/deny-assignments.md)。

## <a name="next-steps"></a>后续步骤

- [什么是 Azure 基于角色的访问控制 (Azure RBAC)？](../../role-based-access-control/overview.md)
- [Azure RBAC 最佳做法](../../role-based-access-control/best-practices.md)
