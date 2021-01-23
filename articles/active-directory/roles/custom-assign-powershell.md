---
title: 使用 Azure AD PowerShell Azure AD 分配自定义角色 |Microsoft Docs
description: 使用 Azure AD PowerShell 管理 Azure AD 管理员自定义角色的成员。
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7828313844b8f95b2bac5bff37022a822686ab33
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98740238"
---
# <a name="assign-custom-roles-with-resource-scope-using-powershell-in-azure-active-directory"></a>在 Azure Active Directory 中使用 PowerShell 分配具有资源范围的自定义角色

本文介绍如何在 Azure Active Directory (Azure AD) 中创建组织范围的角色分配。 在组织范围分配角色会跨 Azure AD 组织授予访问权限。 若要创建具有单个 Azure AD 资源范围的角色分配，请参阅 [如何创建自定义角色并在资源范围内分配该角色](custom-create.md)。 本文使用 [Azure Active Directory PowerShell 版本 2](/powershell/module/azuread/#directory_roles) 模块。

有关 Azure AD 管理员角色的详细信息，请参阅[在 Azure Active Directory 中分配管理员角色](permissions-reference.md)。

## <a name="required-permissions"></a>所需的权限

连接到 Azure AD 组织，使用全局管理员帐户分配或删除角色。

## <a name="prepare-powershell"></a>准备 PowerShell

安装 [PowerShell 库](https://www.powershellgallery.com/packages/AzureADPreview)中的 Azure AD PowerShell 模块。 然后使用以下命令导入 Azure AD PowerShell 预览版模块：

``` PowerShell
Import-Module AzureADPreview
```

若要验证该模块是否可供使用，请将以下命令返回的版本与此处列出的版本之一进行匹配：

``` PowerShell
Get-Module AzureADPreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADMSAdministrati...}
```

现在可以开始使用模块中的 cmdlet 了。 有关 AzureAD 模块中 cmdlet 的完整说明，请参阅 [Azure AD 预览版模块](https://www.powershellgallery.com/packages/AzureADPreview)的联机参考文档。

## <a name="assign-a-directory-role-to-a-user-or-service-principal-with-resource-scope"></a>使用资源范围将目录角色分配给用户或服务主体

1. 加载 Azure AD PowerShell (Preview) 模块。
1. 通过执行 `Connect-AzureAD` 命令登录。
1. 使用以下 PowerShell 脚本创建新角色。

``` PowerShell
## Assign a role to a user or service principal with resource scope
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

若要将角色分配给服务主体而不是用户，请使用 [AzureADMSServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) cmdlet。

## <a name="role-definitions"></a>角色定义

角色定义对象包含内置角色或自定义角色的定义，以及通过该角色分配授予的权限。 此资源显示自定义角色定义和内置目录角色 (以 roleDefinition 等效形式) 显示。 目前，Azure AD 组织最多可定义30个唯一自定义角色定义。

### <a name="create-a-role-definition"></a>创建角色定义

``` PowerShell
# Basic information
$description = "Can manage credentials of application registrations"
$displayName = "Application Registration Credential Administrator"
$templateId = (New-Guid).Guid

# Set of actions to include
$rolePermissions = @{
    "allowedResourceActions" = @(
        "microsoft.directory/applications/standard/read",
        "microsoft.directory/applications/credentials/update"
    )
}

# Create new custom directory role
$customAdmin = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="read-and-list-role-definitions"></a>读取和列出角色定义

``` PowerShell
# Get all role definitions
Get-AzureADMSRoleDefinitions

# Get single role definition by ID
Get-AzureADMSRoleDefinition -Id 86593cfc-114b-4a15-9954-97c3494ef49b

# Get single role definition by templateId
Get-AzureADMSRoleDefinition -Filter "templateId eq 'c4e39bd9-1100-46d3-8c65-fb160da0071f'"
```

### <a name="update-a-role-definition"></a>更新角色定义

``` PowerShell
# Update role definition
# This works for any writable property on role definition. You can replace display name with other
# valid properties.
Set-AzureADMSRoleDefinition -Id c4e39bd9-1100-46d3-8c65-fb160da0071f -DisplayName "Updated DisplayName"
```

### <a name="delete-a-role-definition"></a>删除角色定义

``` PowerShell
# Delete role definition
Remove-AzureADMSRoleDefinitions -Id c4e39bd9-1100-46d3-8c65-fb160da0071f
```

## <a name="role-assignments"></a>角色分配

角色分配包含将给定安全主体（用户或应用程序服务主体）链接到角色定义的信息。 必要时，可以为分配的权限添加单一 Azure AD 资源范围。  内置和自定义角色支持限制角色分配的作用域。

### <a name="create-a-role-assignment"></a>创建角色分配

``` PowerShell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

### <a name="read-and-list-role-assignments"></a>读取和列出角色分配

``` PowerShell
# Get role assignments for a given principal
Get-AzureADMSRoleAssignment -Filter "principalId eq '27c8ca78-ab1c-40ae-bd1b-eaeebd6f68ac'"

# Get role assignments for a given role definition 
Get-AzureADMSRoleAssignment -Filter "roleDefinitionId eq '355aed8a-864b-4e2b-b225-ea95482e7570'"
```

### <a name="delete-a-role-assignment"></a>删除角色分配

``` PowerShell
# Delete role assignment
Remove-AzureADMSRoleAssignment -Id 'qiho4WOb9UKKgng_LbPV7tvKaKRCD61PkJeKMh7Y458-1'
```

## <a name="next-steps"></a>后续步骤

- 在 [Azure AD 管理角色论坛](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)上与我们分享你的观点
- 有关角色和 Azure AD 管理员角色分配的详细信息，请参阅 [分配管理员角色](permissions-reference.md)
- 有关默认用户权限，请参阅[默认来宾和成员用户权限的比较](../fundamentals/users-default-permissions.md)
