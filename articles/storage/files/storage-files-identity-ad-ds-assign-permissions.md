---
title: 控制对 Azure 文件共享的访问 - 本地 AD DS 身份验证
description: 了解如何为某个表示你的存储帐户的 Active Directory 域服务标识分配权限。 这样你就可以使用基于标识的身份验证来控制访问权限。
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 06/22/2020
ms.author: rogarana
ms.openlocfilehash: af88f0b3403fb80acbb7dacebe293ac583e35799
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "91716033"
---
# <a name="part-two-assign-share-level-permissions-to-an-identity"></a>第二部分：向标识分配共享级权限

在开始阅读本文之前，请确保已完成上一篇文章：[为你的帐户启用 AD DS 身份验证](storage-files-identity-ad-ds-enable.md)。

在存储帐户上启用 Active Directory 域服务 (AD DS) 身份验证后，必须配置共享级权限才能访问文件共享。 你需要的用于访问 Azure 文件共享资源的标识必须是同时存在于 AD DS 和 Azure AD 中的混合标识。 例如，假设你的 AD DS 中有一个用户是 user1@onprem.contoso.com，并且你已使用 Azure AD Connect 同步将其作为 user1@contoso.com 同步到 Azure AD。若要允许此用户访问 Azure 文件存储，必须向 user1@contoso.com 分配共享级权限。 同一理念也适用于组或服务主体。 因此，你必须使用 Azure AD Connect 同步将用户和组从 AD DS 同步到 Azure AD。 

必须在 AD DS 中为表示同一用户或组的 Azure AD 标识分配共享级权限，以支持向 Azure文件共享进行 AD DS 身份验证的功能。 AD DS 身份验证不支持对仅存在于 Azure AD 中的标识（例如 Azure 托管标识 (MSI)）进行身份验证和授权。 本文演示了如何将文件共享的共享级权限分配给某个标识。


## <a name="share-level-permissions"></a>共享级权限

通常，我们建议使用共享级权限对代表一组用户和标识的 AD 组进行高级别访问管理，然后利用 Windows ACL 在目录/文件级别进行精细访问控制。 

有三个用于向用户授予共享级权限的 Azure 内置角色：

- “存储文件数据 SMB 共享读取者”可以通过 SMB 在 Azure 存储文件共享中进行读取访问。
- “存储文件数据 SMB 共享参与者”可以通过 SMB 在 Azure 存储文件共享中进行读取、写入和删除访问。
- “存储文件数据 SMB 共享特权提升参与者”可以通过 SMB 在 Azure 存储文件共享中读取、写入、删除和修改 Windows ACL。

> [!IMPORTANT]
> 对文件共享的完全管理控制权限（包括文件所有权权限）需要使用存储帐户密钥。 Azure AD 凭据不支持管理控制。

你可以使用 Azure 门户、Azure PowerShell 或 Azure CLI 将内置角色分配给用户的 Azure AD 标识，以便授予共享级别权限。

## <a name="assign-an-azure-role"></a>分配 Azure 角色

### <a name="azure-portal"></a>Azure 门户

若要使用 [Azure 门户](https://portal.azure.com)将 Azure 角色分配给 Azure AD 标识，请执行以下步骤：

1. 在 Azure 门户中，转到文件共享，或[创建文件共享](storage-how-to-create-file-share.md)。
1. 选择“访问控制 (IAM)”。
1. 选择“添加角色分配”
1. 在“添加角色分配”边栏选项卡中，从“角色”列表中选择适当的内置角色（“存储文件数据 SMB 共享读取者”和“存储文件数据 SMB 共享参与者”） 。 让“分配访问权限至”保留默认设置“Azure AD 用户、组或服务主体” 。 按名称或电子邮件地址选择目标 Azure AD 标识。 所选 Azure AD 标识必须是混合标识，不能是纯云标识。 这意味着同一标识也会在 AD DS 中进行表示。
1. 选择“保存”以完成角色分配操作。

### <a name="powershell"></a>PowerShell

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

### <a name="cli"></a>CLI
  
以下 CLI 2.0 命令根据登录名将 Azure 角色分配给 Azure AD 标识。 若要详细了解如何使用 Azure CLI 分配 Azure 角色，请参阅[使用 Azure CLI 添加或删除 Azure 角色分配](../../role-based-access-control/role-assignments-cli.md)。 

运行以下示例脚本之前，请务必将占位符值（包括括号）替换为自己的值。

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```

## <a name="next-steps"></a>后续步骤

现在，你已分配共享级权限，接下来必须配置目录和文件级权限。 请继续阅读下一篇文章。

[第三部分：通过 SMB 配置目录和文件级别权限](storage-files-identity-ad-ds-configure-permissions.md)
