---
title: 在不同的作用域分配 Azure AD 角色 - Azure Active Directory
description: 了解如何在 Azure Active Directory 中的不同作用域分配角色
services: active-directory
author: abhijeetsinha
manager: vincesm
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 09/13/2021
ms.author: absinh
ms.reviewer: rolyon
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 951431ca79bfeb182eb5d1252851edb767120039
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128658234"
---
# <a name="assign-azure-ad-roles-at-different-scopes"></a>在不同的作用域分配 Azure AD 角色

在 Azure Active Directory (Azure AD) 中，通常分配 Azure AD 角色，以便它们应用于整个租户。 但是，还可以为不同的资源（例如管理单元或应用程序注册）分配 Azure AD 角色。 例如，可以分配支持管理员角色，使之仅应用于特定的管理单元，而不是整个租户。 角色分配应用到的资源也称为范围。 本文介绍如何在租户范围、管理单元范围和应用程序注册范围分配 Azure AD 角色。 有关范围的详细信息，请参阅 [Azure AD 中的 RBAC 概述](custom-overview.md#scope)。

## <a name="prerequisites"></a>先决条件

- 特权角色管理员或全局管理员。
- 使用 PowerShell 时需要 AzureADPreview 模块。
- 将 Graph 浏览器用于 Microsoft Graph API 时需要管理员同意。

有关详细信息，请参阅[使用 PowerShell 或 Graph 浏览器的先决条件](prerequisites.md)。

## <a name="assign-roles-scoped-to-the-tenant"></a>分配以租户为作用域的角色

本部分介绍如何在租户作用域分配角色。

### <a name="azure-portal"></a>Azure 门户

1. 登录到 [Azure 门户](https://portal.azure.com)或 [Azure AD 管理中心](https://aad.portal.azure.com)。

1. 选择“Azure Active Directory” > “角色和管理员”以查看所有可用角色列表 。

    ![Azure Active Directory 中的角色和管理员页面。](./media/manage-roles-portal/roles-and-administrators.png)

1. 选择一个角色，查看其分配情况。 为帮助查找所需角色，请使用“添加筛选器”来筛选角色。

1. 选择“添加分配”，然后选择要分配给此角色的用户。

    ![为所选角色添加分配窗格。](./media/manage-roles-portal/add-assignments.png)

1. 选择“添加”以分配角色。

### <a name="powershell"></a>PowerShell

执行以下步骤以使用 PowerShell 分配 Azure AD 角色。

1. 打开 PowerShell 窗口并使用 [Import-Module](/powershell/module/microsoft.powershell.core/import-module) 导入 AzureADPreview 模块。 有关详细信息，请参阅[使用 PowerShell 或 Graph 浏览器的先决条件](prerequisites.md)。

    ```powershell
    Import-Module -Name AzureADPreview -Force
    ```

1. 在 PowerShell 窗口中，使用 [Connect-AzureAD](/powershell/module/azuread/connect-azuread) 登录租户。

    ```powershell
    Connect-AzureAD
    ```

1. 使用 [Get-AzureADUser](/powershell/module/azuread/get-azureaduser) 获取用户。

    ```powershell
    $user = Get-AzureADUser -Filter "userPrincipalName eq 'alice@contoso.com'"
    ```

1. 使用 [Get-AzureADMSRoleDefinition](/powershell/module/azuread/get-azureadmsroledefinition) 获取要分配的角色。

    ```powershell
    $roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Billing Administrator'"
    ```

1. 将租户设置为角色分配的作用域。

    ```powershell
    $directoryScope = '/'
    ```

1. 使用 [New-AzureADMSRoleAssignment](/powershell/module/azuread/new-azureadmsroleassignment) 分配角色。

    ```powershell
    $roleAssignment = New-AzureADMSRoleAssignment -DirectoryScopeId $directoryScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
    ```
 
### <a name="microsoft-graph-api"></a>Microsoft Graph API

按照这些说明使用 [Graph 浏览器](https://aka.ms/ge)中的 Microsoft Graph API 分配角色。

1. 登录到 [Graph 浏览器](https://aka.ms/ge)。

1. 使用[列出用户](/graph/api/user-list) API 获取用户。

    ```HTTP
    GET https://graph.microsoft.com/beta/users?$filter=userPrincipalName eq 'alice@contoso.com'
    ```
    
1. 使用[列出 roleDefinitions](/graph/api/rbacapplication-list-roledefinitions) API 获取想要分配的角色。

    ```HTTP
    GET https://graph.microsoft.com/beta/rolemanagement/directory/roleDefinitions?$filter=displayName eq 'Billing Administrator'
    ```
    
1. 使用[创建 roleAssignments](/graph/api/rbacapplication-post-roleassignments) API 分配角色。

    ```HTTP
    POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
    {
        "principalId": "<provide objectId of the user obtained above>",
        "roleDefinitionId": "<provide templateId of the role obtained above>",
        "directoryScopeId": "/"
    }
    ```

## <a name="assign-roles-scoped-to-an-administrative-unit"></a>分配以管理单元为作用域的角色

本部分介绍如何在[管理单元](administrative-units.md)范围分配角色。

### <a name="azure-portal"></a>Azure 门户

1. 登录到 [Azure 门户](https://portal.azure.com)或 [Azure AD 管理中心](https://aad.portal.azure.com)。

1. 选择“Azure Active Directory”>“管理单元”查看所有管理单元的列表。

1. 选择管理单元。

    ![Azure Active Directory 中的管理单元。](./media/assign-roles-different-scopes/admin-units.png)

1. 从左侧导航菜单中选择“角色和管理员”，查看可用于通过管理单元分配的所有角色的列表。

    ![Azure Active Directory 中管理单元下的“角色和管理员”菜单。](./media/assign-roles-different-scopes/admin-units-roles.png)

1. 选择所需的角色。

1. 选择“添加分配”，然后选择要向其分配此角色的用户或组。

1. 选择“添加”分配以管理单元为作用域的角色。

>[!Note] 
>你不会在此处看到 Azure AD 内置或自定义角色的完整列表。 这是正常情况。 我们将显示具有与管理单元内支持的对象相关的权限的角色。 请参阅[此文档](administrative-units.md)，查看管理单元中支持的对象列表。

### <a name="powershell"></a>PowerShell

执行以下步骤，使用 PowerShell 在管理单元作用域分配 Azure AD 角色。

1. 打开 PowerShell 窗口并使用 [Import-Module](/powershell/module/microsoft.powershell.core/import-module) 导入 AzureADPreview 模块。 有关详细信息，请参阅[使用 PowerShell 或 Graph 浏览器的先决条件](prerequisites.md)。

    ```powershell
    Import-Module -Name AzureADPreview -Force
    ```

1. 在 PowerShell 窗口中，使用 [Connect-AzureAD](/powershell/module/azuread/connect-azuread) 登录租户。

    ```powershell
    Connect-AzureAD
    ```

1. 使用 [Get-AzureADUser](/powershell/module/azuread/get-azureaduser) 获取用户。

    ```powershell
    $user = Get-AzureADUser -Filter "userPrincipalName eq 'alice@contoso.com'"
    ```

1. 使用 [Get-AzureADMSRoleDefinition](/powershell/module/azuread/get-azureadmsroledefinition) 获取要分配的角色。

    ```powershell
    $roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'User Administrator'"
    ```

1. 使用 [Get-AzureADMSAdministrativeUnit](/powershell/module/azuread/get-azureadmsadministrativeunit) 获取要将角色分配的作用域限定到的管理单元。

    ```powershell
    $adminUnit = Get-AzureADMSAdministrativeUnit -Filter "displayName eq 'Seattle Admin Unit'"
    $directoryScope = '/administrativeUnits/' + $adminUnit.Id
    ```

1. 使用 [New-AzureADMSRoleAssignment](/powershell/module/azuread/new-azureadmsroleassignment) 分配角色。

    ```powershell
    $roleAssignment = New-AzureADMSRoleAssignment -DirectoryScopeId $directoryScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
    ```

### <a name="microsoft-graph-api"></a>Microsoft Graph API

按照这些说明使用 [Graph 浏览器](https://aka.ms/ge)中的 Microsoft Graph API 在管理单元作用域分配角色。

1. 登录到 [Graph 浏览器](https://aka.ms/ge)。

1. 使用[列出用户](/graph/api/user-list) API 获取用户。

    ```HTTP
    GET https://graph.microsoft.com/beta/users?$filter=userPrincipalName eq 'alice@contoso.com'
    ```
    
1. 使用[列出 roleDefinitions](/graph/api/rbacapplication-list-roledefinitions) API 获取想要分配的角色。

    ```HTTP
    GET https://graph.microsoft.com/beta/rolemanagement/directory/roleDefinitions?$filter=displayName eq 'User Administrator'
    ```
    
1. 使用[列出 administrativeUnits](/graph/api/administrativeunit-list) API 获取要将角色分配的作用域限定到的管理单元。

    ```HTTP
    GET https://graph.microsoft.com/beta/administrativeUnits?$filter=displayName eq 'Seattle Admin Unit'
    ```

1. 使用[创建 roleAssignments](/graph/api/rbacapplication-post-roleassignments) API 分配角色。

    ```HTTP
    POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
    {
        "principalId": "<provide objectId of the user obtained above>",
        "roleDefinitionId": "<provide templateId of the role obtained above>",
        "directoryScopeId": "/administrativeUnits/<provide objectId of the admin unit obtained above>"
    }
    ```
    
>[!Note] 
>此处 directoryScopeId 被指定为 /administrativeUnits/foo，而不是 /foo。 这是默认设置。 作用域 /administrativeUnits/foo 表示主体可以管理管理单元的成员（基于分配给她的角色），而不是管理单元本身。 /foo 作用域表示主体可以管理该 Azure AD 对象本身。 在后续部分中，你将看到作用域为 /foo，因为以应用注册为作用域的角色授予管理对象本身的特权。

## <a name="assign-roles-scoped-to-an-app-registration"></a>分配以应用注册为作用域的角色

本部分介绍如何在应用程序注册范围分配角色。

### <a name="azure-portal"></a>Azure 门户

1. 登录到 [Azure 门户](https://portal.azure.com)或 [Azure AD 管理中心](https://aad.portal.azure.com)。

1. 选择“Azure Active Directory”>“应用注册”查看所有应用注册的列表。

1. 选择应用程序。 可以使用“搜索”框查找所需的应用。

    ![Azure Active Directory 中的应用注册。](./media/assign-roles-different-scopes/app-reg.png)

1. 从左侧导航菜单中选择“角色和管理员”，查看可用于通过应用注册分配的所有角色的列表。

    ![Azure Active Directory 中的应用注册的角色。](./media/assign-roles-different-scopes/app-reg-roles.png)

1. 选择所需的角色。

1. 选择“添加分配”，然后选择要向其分配此角色的用户或组。

    ![在 Azure Active Directory 中添加以应用注册为作用域的角色分配。](./media/assign-roles-different-scopes/app-reg-add-assignment.png)

1. 选择“添加”分配以应用注册为作用域的角色。 

    ![在 Azure Active Directory 中成功添加了以应用注册为作用域的角色分配。](./media/assign-roles-different-scopes/app-reg-assignment.png)
    
    ![向 Azure Active Directory 中以应用注册为作用域的用户分配的角色。](./media/assign-roles-different-scopes/app-reg-scoped-assignment.png)
    

>[!Note] 
>你不会在此处看到 Azure AD 内置或自定义角色的完整列表。 这是正常情况。 我们将显示仅具有与管理应用注册相关的权限的角色。 


### <a name="powershell"></a>PowerShell

执行以下步骤，使用 PowerShell 在应用程序作用域分配 Azure AD 角色。

1. 打开 PowerShell 窗口并使用 [Import-Module](/powershell/module/microsoft.powershell.core/import-module) 导入 AzureADPreview 模块。 有关详细信息，请参阅[使用 PowerShell 或 Graph 浏览器的先决条件](prerequisites.md)。

    ```powershell
    Import-Module -Name AzureADPreview -Force
    ```

1. 在 PowerShell 窗口中，使用 [Connect-AzureAD](/powershell/module/azuread/connect-azuread) 登录租户。

    ```powershell
    Connect-AzureAD
    ```

1. 使用 [Get-AzureADUser](/powershell/module/azuread/get-azureaduser) 获取用户。

    ```powershell
    $user = Get-AzureADUser -Filter "userPrincipalName eq 'alice@contoso.com'"
    ```

1. 使用 [Get-AzureADMSRoleDefinition](/powershell/module/azuread/get-azureadmsroledefinition) 获取要分配的角色。

    ```powershell
    $roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Administrator'"
    ```

1. 使用 [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) 获取要将角色分配的作用域限定到的应用注册。

    ```powershell
    $appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
    $directoryScope = '/' + $appRegistration.objectId
    ```

1. 使用 [New-AzureADMSRoleAssignment](/powershell/module/azuread/new-azureadmsroleassignment) 分配角色。

    ```powershell
    $roleAssignment = New-AzureADMSRoleAssignment -DirectoryScopeId $directoryScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
    ```

### <a name="microsoft-graph-api"></a>Microsoft Graph API

按照这些说明使用 [Graph 浏览器](https://aka.ms/ge)中的 Microsoft Graph API 在应用程序作用域分配角色。

1. 登录到 [Graph 浏览器](https://aka.ms/ge)。

1. 使用[列出用户](/graph/api/user-list) API 获取用户。

    ```HTTP
    GET https://graph.microsoft.com/beta/users?$filter=userPrincipalName eq 'alice@contoso.com'
    ```
    
1. 使用[列出 roleDefinitions](/graph/api/rbacapplication-list-roledefinitions) API 获取想要分配的角色。

    ```HTTP
    GET https://graph.microsoft.com/beta/rolemanagement/directory/roleDefinitions?$filter=displayName eq 'Application Administrator'
    ```
    
1. 使用[列出应用程序](/graph/api/application-list) API 获取要将角色分配的作用域限定到的管理单元。

    ```HTTP
    GET https://graph.microsoft.com/beta/applications?$filter=displayName eq 'f/128 Filter Photos'
    ```

1. 使用[创建 roleAssignments](/graph/api/rbacapplication-post-roleassignments) API 分配角色。

    ```HTTP
    POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
    {
        "principalId": "<provide objectId of the user obtained above>",
        "roleDefinitionId": "<provide templateId of the role obtained above>",
        "directoryScopeId": "/<provide objectId of the app registration obtained above>"
    }
    ```

>[!Note] 
>此处的 directoryScopeId 被指定为 /foo，与上面的部分不同。 这是默认设置。 /foo 作用域表示主体可以管理该 Azure AD 对象。 作用域 /administrativeUnits/foo 表示主体可以管理管理单元的成员（基于分配给她的角色），而不是管理单元本身。


## <a name="next-steps"></a>后续步骤

* [列出 Azure AD 角色分配](view-assignments.md)。
* [向用户分配 Azure AD 角色](manage-roles-portal.md)。
* [向组分配 Azure AD 角色](groups-assign-role.md)