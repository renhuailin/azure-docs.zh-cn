---
title: 在 Azure AD 基于角色的访问控制中创建自定义角色 | Microsoft Docs
description: 通过 Azure Active Directory 资源的资源范围创建和分配自定义 Azure AD 角色。
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
ms.openlocfilehash: 3d7ece742e211715c27a4bb81b67c51a7910552f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121746077"
---
# <a name="create-and-assign-a-custom-role-in-azure-active-directory"></a>在 Azure Active Directory 中创建和分配自定义角色

本文介绍如何在 Azure Active Directory (Azure AD) 中创建新的自定义角色。 有关自定义角色的基础知识，请参阅[自定义角色概述](custom-overview.md)。 只能在目录级范围或应用注册资源范围中分配角色。

自定义角色可以在 Azure AD 概述页上的[角色和管理员](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators)选项卡中创建。

## <a name="prerequisites"></a>先决条件

- Azure AD Premium P1 或 P2 许可证
- 特权角色管理员或全局管理员
- 使用 PowerShell 时需要 AzureADPreview 模块
- 将 Graph 浏览器用于 Microsoft Graph API 时需要管理员同意

有关详细信息，请参阅[使用 PowerShell 或 Graph 浏览器的先决条件](prerequisites.md)。

## <a name="create-a-role-in-the-azure-portal"></a>在 Azure 门户中创建角色

### <a name="create-a-new-custom-role-to-grant-access-to-manage-app-registrations"></a>创建新的自定义角色，授予管理应用注册所需的访问权限

1. 登录到 [Azure 门户](https://portal.azure.com)或 [Azure AD 管理中心](https://aad.portal.azure.com)。

1. 选择“Azure Active Directory” > “角色和管理员” > “新建自定义角色”。

   ![从“角色和管理员”页创建或编辑角色](./media/custom-create/new-custom-role.png)

1. 在“基本信息”选项卡中提供角色的名称和说明，然后单击“下一步”。 

   ![在“基本信息”选项卡中提供自定义角色的名称和说明](./media/custom-create/basics-tab.png)

1. 在“权限”选项卡中，选择管理应用注册的基本属性和凭据属性所需的权限。 如需每个权限的详细说明，请参阅 [Azure Active Directory 中的应用程序注册子类型和权限](custom-available-permissions.md)。
   1. 首先在搜索栏中输入“credentials”，然后选择 `microsoft.directory/applications/credentials/update` 权限。

      ![在“权限”选项卡上选择自定义角色的权限](./media/custom-create/permissions-tab.png)

   1. 接下来，在搜索栏中输入“basic”，选择 `microsoft.directory/applications/basic/update` 权限，然后单击“下一步”。
1. 在“查看 + 创建”选项卡上查看权限，然后选择“创建” 。

你的自定义角色将显示在要分配的可用角色列表中。

## <a name="create-a-role-using-powershell"></a>使用 PowerShell 创建角色

### <a name="connect-to-azure"></a>连接到 Azure

若要连接到 Azure Active Directory，请使用以下命令：

``` PowerShell
Connect-AzureAD
```

### <a name="create-the-custom-role"></a>创建自定义角色

使用以下 PowerShell 脚本创建新角色：

``` PowerShell
# Basic role information
$displayName = "Application Support Administrator"
$description = "Can manage basic aspects of application registrations."
$templateId = (New-Guid).Guid
 
# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/basic/update",
    "microsoft.directory/applications/credentials/update"
)
$rolePermissions = @{'allowedResourceActions'= $allowedResourceAction}
 
# Create new custom admin role
$customAdmin = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role-using-powershell"></a>使用 PowerShell 分配自定义角色

使用以下 PowerShell 脚本分配角色：

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

## <a name="create-a-role-with-the-microsoft-graph-api"></a>使用 Microsoft Graph API 创建角色

1. 创建角色定义。

    用于创建自定义角色定义的 HTTP 请求。

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
    ```

    正文

    ``` HTTP
    {
       "description": "Can manage basic aspects of application registrations.",
       "displayName": "Application Support Administrator",
       "isEnabled": true,
       "templateId": "<GUID>",
       "rolePermissions": [
           {
               "allowedResourceActions": [
                   "microsoft.directory/applications/basic/update",
                   "microsoft.directory/applications/credentials/update"
               ]
           }
       ]
    }
    ```

    > [!Note]
    > `"templateId": "GUID"` 是根据需要在正文中发送的可选参数。 如果需要使用公共参数创建多个不同的自定义角色，最好创建一个模板并定义 `templateId` 值。 可以事先通过使用 PowerShell cmdlet `(New-Guid).Guid` 来生成 `templateId` 值。 

1. 创建角色分配。

    用于创建自定义角色定义的 HTTP 请求。

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
    ```

    正文

    ``` HTTP
   {
       "principalId":"<GUID OF USER>",
       "roleDefinitionId":"<GUID OF ROLE DEFINITION>",
       "resourceScope":"/<GUID OF APPLICATION REGISTRATION>"
   }
    ```

## <a name="assign-a-custom-role-scoped-to-a-resource"></a>分配资源范围的自定义角色

与内置角色一样，默认情况下，在默认的组织范围内分配自定义角色，以授予对组织中所有应用注册的访问权限。 此外，还可以在单个 Azure AD 资源的范围中分配自定义角色和一些相关的内置角色（具体取决于 Azure AD 资源的类型）。 这样你就可以被用户分配权限，使之可以更新单个应用的凭据和基本属性，不需创建另一个自定义角色。

1. 使用应用程序开发人员权限登录到 [Azure 门户](https://portal.azure.com)或 [Azure AD 管理中心](https://aad.portal.azure.com)。

1. 选择“Azure Active Directory” > “应用注册”。 

1. 选择要授予其管理权限的应用注册。 可能必须选择“所有应用程序”，以便在 Azure AD 组织中查看应用注册的完整列表。

    ![选择应用注册作为角色分配的资源范围](./media/custom-create/appreg-all-apps.png)

1. 在应用注册中，选择“角色和管理员”。 如果尚未创建，请按[上一过程](#create-a-new-custom-role-to-grant-access-to-manage-app-registrations)中的说明操作。

1. 选择角色以打开“分配”页。

1. 选择“添加分配”以添加用户。 用户将仅被授予对所选应用注册的任何权限。

## <a name="next-steps"></a>后续步骤

- 欢迎在 [Azure AD 管理角色论坛](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)上与我们分享知识。
- 有关角色权限的详细信息，请参阅 [Azure AD 内置角色](permissions-reference.md)。
- 有关默认用户权限，请参阅[默认来宾和成员用户权限的比较](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2froles%2fcontext%2fugr-context)。
