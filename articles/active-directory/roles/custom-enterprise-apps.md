---
title: 创建自定义角色以在 Azure Active Directory 中管理企业应用
description: 在 Azure Active Directory 中为企业应用访问创建和分配自定义 Azure AD 角色
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 05/14/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf49cfd4d1e0e9b3e65354c9f6c89dd7efd71970
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121739953"
---
# <a name="create-custom-roles-to-manage-enterprise-apps-in-azure-active-directory"></a>创建自定义角色以在 Azure Active Directory 中管理企业应用

本文介绍如何创建一个在 Azure Active Directory (Azure AD) 中有权限管理用户和组的企业应用分配的自定义角色。 有关角色分配的元素，以及子类型、权限和属性集等术语的含义，请参阅[自定义角色概述](custom-overview.md)。

## <a name="prerequisites"></a>先决条件

- Azure AD Premium P1 或 P2 许可证
- 特权角色管理员或全局管理员
- 使用 PowerShell 时需要 AzureADPreview 模块
- 将 Graph 浏览器用于 Microsoft Graph API 时需要管理员同意

有关详细信息，请参阅[使用 PowerShell 或 Graph 浏览器的先决条件](prerequisites.md)。

## <a name="enterprise-app-role-permissions"></a>企业应用角色权限

本文中讨论了两种企业应用权限。 所有示例都使用 update 权限。

* 若要读取范围内的用户和组分配，请授予 `microsoft.directory/servicePrincipals/appRoleAssignedTo/read` 权限
* 若要管理范围内的用户和组分配，请授予 `microsoft.directory/servicePrincipals/appRoleAssignedTo/update` 权限

授予 update 权限后，被分派人能够管理向企业应用的用户和组分配。 可以针对单个应用程序或针对所有应用程序授予用户和/或组分配的范围。 如果在组织范围级别授予，则被分派人可以管理所有应用程序的分配。 如果在应用程序级别授予，则被分派人只能管理指定应用程序的分配。

授予 update 权限的操作分为两步：

1. 使用权限 `microsoft.directory/servicePrincipals/appRoleAssignedTo/update` 创建自定义角色
1. 向用户或组授予权限，以管理向企业应用的用户和组分配。 这时，你可以将范围设置为组织范围级别或单个应用程序。

## <a name="azure-portal"></a>Azure 门户

### <a name="create-a-new-custom-role"></a>创建新的自定义角色

>[!NOTE]
> 自定义角色是在组织范围级别创建和管理的，只在组织的“概述”页中提供。

1. 登录到 [Azure 门户](https://portal.azure.com)或 [Azure AD 管理中心](https://aad.portal.azure.com)。

1. 依次选择“Azure Active Directory” > “角色和管理员”和“新建自定义角色”  。

    ![在 Azure AD 中，从角色列表添加新的自定义角色](./media/custom-enterprise-apps/new-custom-role.png)

1. 在“基础”选项卡上，提供“管理用户和组分配”作为角色的名称，提供“授予权限以管理用户和组分配”作为角色说明，然后选择“下一步” 。

    ![提供自定义角色的名称及描述](./media/custom-enterprise-apps/role-name-and-description.png)

1. 在“权限”选项卡上的搜索框中输入“microsoft.directory/servicePrincipals/appRoleAssignedTo/update”，选中所需权限旁边的复选框，然后选择“下一步” 。

    ![向自定义角色添加权限](./media/custom-enterprise-apps/role-custom-permissions.png)

1. 在“查看 + 创建”选项卡上查看权限，然后选择“创建” 。

    ![现在可以创建自定义角色](./media/custom-enterprise-apps/role-custom-create.png)

### <a name="assign-the-role-to-a-user-using-the-azure-portal"></a>使用 Azure 门户将角色分配给用户

1. 登录到 [Azure 门户](https://portal.azure.com)或 [Azure AD 管理中心](https://aad.portal.azure.com)。

1. 选择“Azure Active Directory” > “角色和管理员”。 。

1. 选择“授予权限以管理用户和组分配”角色。

    ![打开“角色和管理员”并搜索自定义角色](./media/custom-enterprise-apps/select-custom-role.png)

1. 选择“添加分配”，选择所需的用户，然后单击“选择”以向用户添加角色分配 。

    ![向用户添加自定义角色的分配](./media/custom-enterprise-apps/assign-user-to-role.png)

#### <a name="assignment-tips"></a>分配提示

* 若要向被分派人授予权限以管理组织范围内所有企业应用的用户和组访问权限，请从组织的 Azure AD“概述”页上组织范围内的“角色和管理员”列表开始操作 。
* 若要向被分派人授予权限以管理特定企业应用的用户和组访问权限，请在 Azure AD 中转到该应用，并打开该应用的“角色和管理员”列表。 选择新的自定义角色并完成用户或组分配。 被分派人只能管理特定应用的用户和组访问权限。
* 若要测试自定义角色分配，请以被分派人身份登录，并打开应用程序的“用户和组”页，验证是否启用了“添加用户”选项 。

    ![验证用户权限](./media/custom-enterprise-apps/verify-user-permissions.png)

## <a name="powershell"></a>PowerShell

有关更多详细信息，请参阅[创建和分配自定义角色](custom-create.md)和[使用 PowerShell 分配具有资源范围的自定义角色](custom-assign-powershell.md)。

### <a name="create-a-custom-role"></a>创建自定义角色

使用以下 PowerShell 脚本创建新角色：

```PowerShell
# Basic role information
$description = "Manage user and group assignments"
$displayName = "Can manage user and group assignments for Applications"
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction = @("microsoft.directory/servicePrincipals/appRoleAssignedTo/update")
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customRole = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role"></a>分配自定义角色

使用此 PowerShell 脚本分配角色。

```powershell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'chandra@example.com'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Manage user and group assignments'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'My Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

## <a name="microsoft-graph-api"></a>Microsoft Graph API

在 Microsoft Graph API 中使用提供的示例创建自定义角色。 有关更多详细信息，请参阅[创建和分配自定义角色](custom-create.md)以及[使用 Microsoft Graph API 分配自定义管理员角色](custom-assign-graph.md)。

用于创建自定义角色的 HTTP 请求。

```HTTP
POST
https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitionsIsEnabled $true
{
    "description":"Can manage user and group assignments for Applications.",
    "displayName":" Manage user and group assignments",
    "isEnabled":true,
    "rolePermissions":
    [
        {
            "resourceActions":
            {
                "allowedResourceActions":
                [
                    "microsoft.directory/servicePrincipals/appRoleAssignedTo/update"
                ]
            },
            "condition":null
        }
    ],
    "templateId":"<PROVIDE NEW GUID HERE>",
    "version":"1"
}
```

### <a name="assign-the-custom-role-using-the-microsoft-graph-api"></a>使用 Microsoft Graph API 分配自定义角色

角色分配会将安全主体 ID（可以是用户或服务主体）、角色定义 ID 和 Azure AD 资源范围合并。 有关角色分配元素的详细信息，请参阅[自定义角色概述](custom-overview.md)

用于分配自定义角色的 HTTP 请求。

```HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments

{
    "principalId":"<PROVIDE OBJECTID OF USER TO ASSIGN HERE>",
    "roleDefinitionId":"<PROVIDE OBJECTID OF ROLE DEFINITION HERE>",
    "resourceScopes":["/"]
}
```

## <a name="next-steps"></a>后续步骤

* [了解适用于企业应用的可用自定义角色权限](custom-enterprise-app-permissions.md)
