---
title: 控制对 Azure 文件共享的访问 - 本地 AD DS 身份验证
description: 了解如何为某个表示你的存储帐户的 Active Directory 域服务标识分配权限。 这样你就可以使用基于标识的身份验证来控制访问权限。
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 07/13/2021
ms.author: rogarana
ms.custom: devx-track-azurepowershell, subject-rbac-steps
ms.openlocfilehash: 4c69a8bcd3acb559de3674dd7012220f4c7868e4
ms.sourcegitcommit: 6f4378f2afa31eddab91d84f7b33a58e3e7e78c1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2021
ms.locfileid: "113687148"
---
# <a name="part-two-assign-share-level-permissions-to-an-identity"></a>第二部分：向标识分配共享级权限

在开始阅读本文之前，请确保已完成上一篇文章：[为你的帐户启用 AD DS 身份验证](storage-files-identity-ad-ds-enable.md)。

在存储帐户上启用 Active Directory 域服务 (AD DS) 身份验证后，必须配置共享级权限才能访问文件共享。 可通过两种方式分配共享级别权限。 可将这些权限分配给特定的 Azure AD 用户/用户组，还可以将其作为默认共享级别权限分配给所有已经过身份验证的标识。

> [!IMPORTANT]
> 对文件共享的完全管理控制权限（包括文件所有权权限）需要使用存储帐户密钥。 Azure AD 凭据不支持管理控制。

## <a name="applies-to"></a>适用于
| 文件共享类型 | SMB | NFS |
|-|:-:|:-:|
| 标准文件共享 (GPv2)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 标准文件共享 (GPv2)、GRS/GZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 高级文件共享 (FileStorage)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |

## <a name="which-configuration-should-you-use"></a>应使用哪种配置

大多数用户应将共享级别权限分配给特定的 Azure AD 用户或组，然后使用 Windows ACL 在目录和文件级别进行精细访问控制。 这是最严格且最安全的配置。

但对于以下三种情况，我们建议使用分配给所有已经过身份验证的标识的默认共享级别权限：

- 如果你无法将本地 AD DS 同步到 Azure AD，则可以改用默认共享级别权限。 通过分配默认共享级别权限可以解决同步要求，因为无需在 Azure AD 中为标识指定权限。 然后，可以使用 Windows ACL 对文件和目录强制实施精细权限。
- 使用的本地 AD DS 不会同步到部署了文件共享的 Azure AD，而是同步到另一个 Azure AD。
    - 管理多租户环境时，这种情况很常见。 使用默认共享级别权限可以绕过 Azure AD 混合标识相关的要求。 仍可对文件和目录使用 Windows ACL，以强制实施精细权限。
- 你倾向于仅在文件和目录级别使用 Windows ACL 强制实施身份验证。 

## <a name="share-level-permissions"></a>共享级权限

下表列出了共享级别权限及其如何与内置 RBAC 角色保持一致：

|支持的内置角色  |说明  |
|---------|---------|
|[存储文件数据 SMB 共享读取者](../../role-based-access-control/built-in-roles.md#storage-file-data-smb-share-reader)     |允许对 Azure 文件共享中的文件和目录进行读取访问。 此角色类似于 Windows 文件服务器上的文件共享读取 ACL。 [了解详细信息](storage-files-identity-auth-active-directory-enable.md)。         |
|[存储文件数据 SMB 共享参与者](../../role-based-access-control/built-in-roles.md#storage-file-data-smb-share-contributor)     |允许对 Azure 文件共享中的文件和目录进行读取、写入和删除访问。 [了解详细信息](storage-files-identity-auth-active-directory-enable.md)。         |
|[存储文件数据 SMB 共享提升参与者](../../role-based-access-control/built-in-roles.md#storage-file-data-smb-share-elevated-contributor)     |允许读取、写入、删除和修改针对 Azure 文件共享中文件和目录的 ACL。 此角色类似于 Windows 文件服务器上的文件共享更改 ACL。 [了解详细信息](storage-files-identity-auth-active-directory-enable.md)。         |


## <a name="share-level-permissions-for-specific-azure-ad-users-or-groups"></a>特定 Azure AD 用户或组的共享级别权限

如果你打算使用特定的 Azure AD 用户或组来访问 Azure 文件共享资源，该标识必须是存在于本地 AD DS 和 Azure AD 中的混合标识。 例如，假设你在 AD 中有一个用户 user1@onprem.contoso.com，并且你已使用 Azure AD Connect 同步以 user1@contoso.com 身份同步到 Azure AD。要使此用户能够访问 Azure 文件存储，必须向 user1@contoso.com 分配共享级别权限。 同一理念也适用于组或服务主体。 因此，必须使用 Azure AD Connect 同步将 AD 中的用户和组同步到 Azure AD。 

必须在 AD DS 中为表示同一用户或组的 Azure AD 标识分配共享级权限，以支持向 Azure文件共享进行 AD DS 身份验证的功能。 AD DS 身份验证不支持对仅存在于 Azure AD 中的标识（例如 Azure 托管标识 (MSI)）进行身份验证和授权。

可以使用 Azure 门户、Azure PowerShell 模块或 Azure CLI 将内置角色分配给用户的 Azure AD 标识，以便授予共享级别权限。

# <a name="portal"></a>[Portal](#tab/azure-portal)

若要使用 [Azure 门户](https://portal.azure.com)将 Azure 角色分配给 Azure AD 标识，请执行以下步骤：

1. 在 Azure 门户中，转到文件共享，或[创建文件共享](storage-how-to-create-file-share.md)。
1. 选择“访问控制 (IAM)”。
1. 选择“添加角色分配”
1. 在“添加角色分配”边栏选项卡中，从“角色”列表中选择[相应的内置角色](#share-level-permissions) 。
    1. 存储文件数据 SMB 共享读取者
    1. 存储文件数据 SMB 共享参与者
    1. 存储文件数据 SMB 共享特权参与者
1.  让“分配访问权限至”保留默认设置“Azure AD 用户、组或服务主体” 。 按名称或电子邮件地址选择目标 Azure AD 标识。 所选 Azure AD 标识必须是混合标识，不能是纯云标识。 这意味着同一标识也会在 AD DS 中进行表示。
1. 选择“保存”以完成角色分配操作。

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

以下 PowerShell 示例显示如何根据登录名将 Azure 角色分配给 Azure AD 标识。 若要详细了解如何使用 PowerShell 分配 Azure 角色，请参阅[使用 Azure PowerShell 模块添加或删除 Azure 角色分配](../../role-based-access-control/role-assignments-powershell.md)。

运行以下示例脚本之前，请将占位符值（包括括号）替换为你的值。

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
  
以下 CLI 2.0 命令根据登录名将 Azure 角色分配给 Azure AD 标识。 若要详细了解如何使用 Azure CLI 分配 Azure 角色，请参阅[使用 Azure CLI 添加或删除 Azure 角色分配](../../role-based-access-control/role-assignments-cli.md)。 

运行以下示例脚本之前，请务必将占位符值（包括括号）替换为自己的值。

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```
---

## <a name="share-level-permissions-for-all-authenticated-identities"></a>所有已经过身份验证的标识的共享级别权限

可以在存储帐户上添加默认共享级别权限，而不是为 Azure AD 用户或组配置共享级别权限。 分配给存储帐户的默认共享级别权限将应用于存储帐户中包含的所有文件共享。 

设置默认共享级别权限时，所有已经过身份验证的用户和组将获得相同的权限。 已经过身份验证的用户或组会被识别，因为该标识可以针对与存储帐户关联的本地 AD DS 进行身份验证。 默认共享级别权限在初始化时设置为“无”，这意味着不允许访问 Azure 文件共享中的文件和目录。

# <a name="portal"></a>[Portal](#tab/azure-portal)

目前无法使用 Azure 门户向存储帐户分配权限。 请改用 Azure PowerShell 模块或 Azure CLI。

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

可使用以下脚本在存储帐户上配置默认共享级别权限。 只能在与目录服务（用于文件身份验证）关联的存储帐户上启用默认共享级别权限。 

在运行以下脚本之前，请确保 Az.Storage 模块的版本为 3.7.0 或更高版本。

```azurepowershell
$defaultPermission = "None|StorageFileDataSmbShareContributor|StorageFileDataSmbShareReader|StorageFileDataSmbShareElevatedContributor" # Set the default permission of your choice

$account = Set-AzStorageAccount -ResourceGroupName "<resource-group-name-here>" -AccountName "<storage-account-name-here>" -DefaultSharePermission $defaultPermission

$account.AzureFilesIdentityBasedAuth
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

可使用以下脚本在存储帐户上配置默认共享级别权限。 只能在与目录服务（用于文件身份验证）关联的存储帐户上启用默认共享级别权限。 

在运行以下脚本之前，请确保 Azure CLI 的版本为 2.24.1 或更高版本。

```azurecli
# Declare variables
storageAccountName="YourStorageAccountName"
resourceGroupName="YourResourceGroupName"
defaultPermission="None|StorageFileDataSmbShareContributor|StorageFileDataSmbShareReader|StorageFileDataSmbShareElevatedContributor" # Set the default permission of your choice


az storage account update --name $storageAccountName --resource-group $resourceGroupName --default-share-permission $defaultPermission
```
---

## <a name="what-happens-if-you-use-both-configurations"></a>同时使用这两种配置会发生什么情况

还可以将权限分配给所有已经过身份验证的 Azure AD 用户和特定的 Azure AD 用户/组。 使用此配置时，特定的用户或组将获得默认共享级别权限和 RBAC 分配中允许的权限超集。 为了帮助你了解此配置的工作原理，下面提供了一个示例：假设你向某个用户授予了目标文件共享上的“存储文件数据 SMB 读取者”角色。 此外，你还向所有已经过身份验证的用户授予了默认共享级别权限“存储文件数据 SMB 共享权限提升的参与者”角色。 使用此配置时，该特定用户将对文件共享拥有“存储文件数据 SMB 共享权限提升的参与者”级别的访问权限。 较高级别的权限始终优先。

## <a name="next-steps"></a>后续步骤

现在，你已分配共享级权限，接下来必须配置目录和文件级权限。 请继续阅读下一篇文章。

[第三部分：通过 SMB 配置目录和文件级别权限](storage-files-identity-ad-ds-configure-permissions.md)
