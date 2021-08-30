---
title: 向用户分配 Azure AD 角色 - Azure Active Directory
description: 了解如何通过分配 Azure AD 角色向 Azure Active Directory 中的用户授予访问权限。
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 07/15/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 311478f52de547a99354a6effad376145166704b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121724818"
---
# <a name="assign-azure-ad-roles-to-users"></a>向用户分配 Azure AD 角色

若要为 Azure Active Directory (Azure AD) 中的用户授予访问权限，请分配 Azure AD 角色。 角色是权限的集合。 本文介绍如何使用 Azure 门户和 PowerShell 分配 Azure AD 角色。

## <a name="prerequisites"></a>先决条件

- 特权角色管理员或全局管理员
- 使用 Privileged Identity Management (PIM) 时，需要 Azure AD Premium P2 许可证
- 使用 PowerShell 时需要 AzureADPreview 模块
- 将 Graph 浏览器用于 Microsoft Graph API 时需要管理员同意

有关详细信息，请参阅[使用 PowerShell 或 Graph 浏览器的先决条件](prerequisites.md)。

## <a name="azure-portal"></a>Azure 门户

执行以下步骤以使用 Azure 门户分配 Azure AD 角色。 根据是否启用了 [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md)，体验会有所不同。

### <a name="assign-a-role"></a>分配角色

1. 登录到 [Azure 门户](https://portal.azure.com)或 [Azure AD 管理中心](https://aad.portal.azure.com)。

1. 选择“Azure Active Directory” > “角色和管理员”以查看所有可用角色列表 。

    ![Azure Active Directory 中的角色和管理员页面。](./media/manage-roles-portal/roles-and-administrators.png)

1. 选择一个角色，查看其分配情况。

    为帮助查找所需角色，请使用“添加筛选器”来筛选角色。

1. 选择“添加分配”，然后选择要分配给此角色的用户。

    如果显示的内容与下图不同，则可能启用了 PIM。 请参阅下一部分。

    ![为所选角色添加分配窗格。](./media/manage-roles-portal/add-assignments.png)

1. 选择“添加”以分配角色。

### <a name="assign-a-role-using-pim"></a>使用 PIM 分配角色

如果启用了 [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md)，则你可使用额外的角色分配功能。 例如，可以使用户符合角色的条件或设置持续时间。 启用 PIM 后，可以通过两种方式使用 Azure 门户分配角色。 可以使用”角色和管理员”页面或 PIM 体验。 这两种方式均使用相同的 PIM 服务。

按照以下步骤通过[角色和管理员](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators)页面分配角色。 如果要使用 [Privileged Identity Management](https://portal.azure.com/#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) 页面分配角色，请参阅[在 Privileged Identity Management 中分配 Azure AD 角色](../privileged-identity-management/pim-how-to-add-role-to-user.md)。

1. 登录到 [Azure 门户](https://portal.azure.com)或 [Azure AD 管理中心](https://aad.portal.azure.com)。

1. 选择“Azure Active Directory” > “角色和管理员”以查看所有可用角色列表 。

    ![启用 PIM 时 Azure Active Directory 中的“角色和管理员”页面。](./media/manage-roles-portal/roles-and-administrators.png)

1. 选择一个角色，查看其符合条件的、有效的和过期的角色分配。

    为帮助查找所需角色，请使用“添加筛选器”来筛选角色。

1. 选择“添加分配”。

1. 选择“未选中成员”，然后选择要分配给此角色的用户。

    ![启用了 PIM 的“添加分配”页面和“选择成员”窗格。](./media/manage-roles-portal/add-assignments-pim.png)

1. 选择“**下一步**”。

1. 在“设置”选项卡上，选择要将此角色分配设置为“符合条件”还是“有效”  。

    符合条件的角色分配意味着用户必须执行一项或多项操作才能使用该角色。 活动角色分配意味着用户无需执行任何操作即可使用该角色。 若要详细了解这些设置的含义，请参阅 [PIM 术语](../privileged-identity-management/pim-configure.md#terminology)。

    ![启用了 PIM 的“添加分配”页面和“设置”选项卡。](./media/manage-roles-portal/add-assignments-pim-setting.png)

1. 使用其余选项设置分配的持续时间。

1. 选择“分配”以分配角色。

## <a name="powershell"></a>PowerShell

执行以下步骤以使用 PowerShell 分配 Azure AD 角色。

### <a name="setup"></a>设置

1. 打开 PowerShell 窗口并使用 [Import-Module](/powershell/module/microsoft.powershell.core/import-module) 导入 AzureADPreview 模块。 有关详细信息，请参阅[使用 PowerShell 或 Graph 浏览器的先决条件](prerequisites.md)。

    ```powershell
    Import-Module -Name AzureADPreview -Force
    ```

1. 在 PowerShell 窗口中，使用 [Connect-AzureAD](/powershell/module/azuread/connect-azuread) 登录租户。

    ```powershell
    Connect-AzureAD
    ```

1. 使用 [Get-AzureADUser](/powershell/module/azuread/get-azureaduser) 获取要为其分配角色的用户。

    ```powershell
    $user = Get-AzureADUser -Filter "userPrincipalName eq 'user@contoso.com'"
    ```

### <a name="assign-a-role"></a>分配角色

1. 使用 [Get-AzureADMSRoleDefinition](/powershell/module/azuread/get-azureadmsroledefinition) 获取要分配的角色。

    ```powershell
    $roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Billing Administrator'"
    ```

1. 使用 [New-AzureADMSRoleAssignment](/powershell/module/azuread/new-azureadmsroleassignment) 分配角色。

    ```powershell
    $roleAssignment = New-AzureADMSRoleAssignment -DirectoryScopeId '/' -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
    ```

### <a name="assign-a-role-as-eligible-using-pim"></a>使用 PIM 将角色分配为符合条件

如果启用 PIM，你将拥有其他功能，例如使用户有资格分配角色或定义角色分配的开始和结束时间。 这些功能使用一组不同的 PowerShell 命令。 若要详细了解如何使用 PowerShell 和 PIM，请参阅[适用于 Privileged Identity Management 中的 Azure AD 角色的 PowerShell](../privileged-identity-management/powershell-for-azure-ad-roles.md)。


1. 使用 [Get-AzureADMSRoleDefinition](/powershell/module/azuread/get-azureadmsroledefinition) 获取要分配的角色。

    ```powershell
    $roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Billing Administrator'"
    ```

1. 使用 [Get-AzureADMSPrivilegedResource](/powershell/module/azuread/get-azureadmsprivilegedresource) 获取特权资源。 在本例中即为你的租户。

    ```powershell
    $aadTenant = Get-AzureADMSPrivilegedResource -ProviderId aadRoles
    ```

1. 使用 [New-Object](/powershell/module/microsoft.powershell.utility/new-object) 新建 `AzureADMSPrivilegedSchedule` 对象来定义角色分配的开始和结束时间。

    ```powershell
    $schedule = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedSchedule
    $schedule.Type = "Once"
    $schedule.StartDateTime = (Get-Date).ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ss.fffZ")
    $schedule.EndDateTime = "2021-07-25T20:00:00.000Z"
    ```

1. 使用 [Open-AzureADMSPrivilegedRoleAssignmentRequest](/powershell/module/azuread/open-azureadmsprivilegedroleassignmentrequest) 将角色分配为符合条件。

    ```powershell
    $roleAssignmentEligible = Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId $aadTenant.Id -RoleDefinitionId $roleDefinition.Id -SubjectId $user.objectId -Type 'AdminAdd' -AssignmentState 'Eligible' -schedule $schedule -reason "Review billing info"
    ```

## <a name="microsoft-graph-api"></a>Microsoft Graph API

按照这些说明使用 [Graph 浏览器](https://aka.ms/ge)中的 Microsoft Graph API 分配角色。

### <a name="assign-a-role"></a>分配角色

在此示例中，在租户范围内为 objectID 为 `f8ca5a85-489a-49a0-b555-0a6d81e56f0d` 的安全主体分配计费管理员角色（角色定义 ID `b0f54661-2d74-4c50-afa3-1ec803f12efe`）。 若要查看所有内置角色的不可变角色模板 ID 列表，请参阅 [Azure AD 内置角色](permissions-reference.md)。

1. 登录到 [Graph 浏览器](https://aka.ms/ge)。
2. 从下拉列表中选择“POST”作为 HTTP 方法。 
3. 选择的 API 版本为“beta 版本”。
4. 使用 [roleAssignments](/graph/api/rbacapplication-post-roleassignments) API 分配角色。 将以下详细信息添加到 URL 和请求正文，然后选择“运行查询”。

```HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
Content-type: application/json

{ 
    "@odata.type": "#microsoft.graph.unifiedRoleAssignment",
    "roleDefinitionId": "b0f54661-2d74-4c50-afa3-1ec803f12efe",
    "principalId": "f8ca5a85-489a-49a0-b555-0a6d81e56f0d",
    "directoryScopeId": "/"
}
```

### <a name="assign-a-role-using-pim"></a>使用 PIM 分配角色

在此示例中，将为一个 objectID 为 `f8ca5a85-489a-49a0-b555-0a6d81e56f0d` 的安全主体分配一个限时有效的计费管理员角色（角色定义 ID `b0f54661-2d74-4c50-afa3-1ec803f12efe`），有效期为 180 天。

1. 登录到 [Graph 浏览器](https://aka.ms/ge)。
2. 从下拉列表中选择“POST”作为 HTTP 方法。 
3. 选择的 API 版本为“beta 版本”。
4. 将以下详细信息添加到 URL 和请求正文，然后选择“运行查询”。

```HTTP
POST https://graph.microsoft.com/beta/rolemanagement/directory/roleEligibilityScheduleRequests

Content-type: application/json

{
    "action": "AdminAssign",
    "justification": "for managing admin tasks",
    "directoryScopeId": "/",
    "principalId": "f8ca5a85-489a-49a0-b555-0a6d81e56f0d",
    "roleDefinitionId": "b0f54661-2d74-4c50-afa3-1ec803f12efe",
    "scheduleInfo": {
        "startDateTime": "2021-07-15T19:15:08.941Z",
        "expiration": {
            "type": "AfterDuration",
            "duration": "PT180D"
        }
    }
}

```

在下面的示例中，为安全主体分配了一个永久有效的计费管理员角色。

```HTTP
POST https://graph.microsoft.com/beta/rolemanagement/directory/roleEligibilityScheduleRequests

Content-type: application/json

{
    "action": "AdminAssign",
    "justification": "for managing admin tasks",
    "directoryScopeId": "/",
    "principalId": "f8ca5a85-489a-49a0-b555-0a6d81e56f0d",
    "roleDefinitionId": "b0f54661-2d74-4c50-afa3-1ec803f12efe",
    "scheduleInfo": {
        "startDateTime": "2021-07-15T19:15:08.941Z",
        "expiration": {
            "type": "NoExpiration"
        }
    }
}

```

若要激活角色分配，请使用以下 API。

```HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignmentScheduleRequests

Content-type: application/json

{
    "action": "SelfActivate",
    "justification": "activating role assignment for admin privileges",
    "roleDefinitionId": "b0f54661-2d74-4c50-afa3-1ec803f12efe",
    "directoryScopeId": "/",
    "principalId": "f8ca5a85-489a-49a0-b555-0a6d81e56f0d"
}

```

## <a name="next-steps"></a>后续步骤

- [列出 Azure AD 角色分配](view-assignments.md)
- [使用 PowerShell 分配具有资源范围的自定义角色](custom-assign-powershell.md)
- [Azure AD 内置角色](permissions-reference.md)
