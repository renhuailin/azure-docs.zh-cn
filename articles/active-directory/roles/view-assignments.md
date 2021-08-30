---
title: 列出 Azure AD 角色分配
description: 现在，你可以在 Azure Active Directory 管理中心查看和管理 Azure Active Directory 管理员角色的成员。
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
ms.openlocfilehash: 947e2b83c409f7e592fd112e20f64b70cc7db070
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121744011"
---
# <a name="list-azure-ad-role-assignments"></a>列出 Azure AD 角色分配

本文介绍如何在 Azure Active Directory (Azure AD) 中列出已分配的角色。 在 Azure Active Directory (Azure AD) 中，角色可以在组织范围内分配，也可以在单应用程序范围内分配。

- 组织范围的角色分配会添加到单应用程序角色分配列表中，并可在其中查看它们。
- 单应用程序范围的角色分配不会添加到组织范围的分配列表中，也不能在其中查看它们。

## <a name="prerequisites"></a>先决条件

- 使用 PowerShell 时需要 AzureADPreview 模块
- 将 Graph 浏览器用于 Microsoft Graph API 时需要管理员同意

有关详细信息，请参阅[使用 PowerShell 或 Graph 浏览器的先决条件](prerequisites.md)。

## <a name="azure-portal"></a>Azure 门户

此过程介绍如何列出组织范围的角色分配。

1. 登录到 [Azure 门户](https://portal.azure.com)或 [Azure AD 管理中心](https://aad.portal.azure.com)。

1. 依次选择“Azure Active Directory” > “角色和管理员”，然后选择一个角色，将其打开并查看其属性。

1. 选择“分配”以列出角色分配。

    ![从列表中打开角色时，列出角色分配和权限](./media/view-assignments/role-assignments.png)

### <a name="list-my-role-assignments"></a>列出我的角色分配

你也可轻松列出自己的权限。 在“角色和管理员”页上选择“你的角色”，可查看当前分配给你的角色。

### <a name="download-role-assignments"></a>下载角色分配

若要下载特定角色的所有分配，请在“角色和管理员”页上选择一个角色，然后选择“下载角色分配”。 将会下载一个 CSV 文件，其中列出了该角色的所有范围内的分配。

![下载角色的所有分配](./media/view-assignments/download-role-assignments.png)

### <a name="list-role-assignments-with-single-application-scope"></a>列出单一应用程序范围的角色分配

此部分介绍如何列出单一应用程序范围的角色分配。 此功能目前处于公开预览状态。

1. 登录到 [Azure 门户](https://portal.azure.com)或 [Azure AD 管理中心](https://aad.portal.azure.com)。

1. 选择“Azure Active Directory” > “应用注册”，然后选择要查看其属性的应用注册。 可能必须选择“所有应用程序”，以便在 Azure AD 组织中查看应用注册的完整列表。

    ![在“应用注册”页中创建或编辑应用注册](./media/view-assignments/app-reg-all-apps.png)

1. 在应用注册中，选择“角色和管理员”，然后选择一个角色，以查看其属性。

    ![通过“应用注册”页列出应用注册角色分配](./media/view-assignments/app-reg-assignments.png)

1. 选择“分配”以列出角色分配。 在“应用注册”中打开分配页会显示局限于此 Azure AD 资源的角色分配。

    ![通过应用注册的属性列出应用注册角色分配](./media/view-assignments/app-reg-assignments-2.png)


## <a name="powershell"></a>PowerShell

此部分介绍如何查看组织范围的角色分配。 本文使用 [Azure Active Directory PowerShell 版本 2](/powershell/module/azuread/#directory_roles) 模块。 若要使用 PowerShell 查看单应用程序范围的分配，可以使用[通过 PowerShell 分配自定义角色](custom-assign-powershell.md)中的 cmdlet。

列出角色分配的示例。

``` PowerShell
# Fetch list of all directory roles with object ID
Get-AzureADDirectoryRole

# Fetch a specific directory role by ID
$role = Get-AzureADDirectoryRole -ObjectId "5b3fe201-fa8b-4144-b6f1-875829ff7543"

# Fetch role membership for a role
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADUser
```

## <a name="microsoft-graph-api"></a>Microsoft Graph API

此部分介绍如何列出组织范围的角色分配。  若要使用 Graph API 列出单一应用程序范围的角色分配，可以使用[通过 Graph API 分配自定义角色](custom-assign-graph.md)中的操作。

用于为给定角色定义获取角色分配的 HTTP 请求。

GET

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments&$filter=roleDefinitionId eq ‘<object-id-or-template-id-of-role-definition>’
```

响应

``` HTTP
HTTP/1.1 200 OK
{
    "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1",
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
    "resourceScopes":["/"]
}
```

## <a name="next-steps"></a>后续步骤

* 欢迎在 [Azure AD 管理角色论坛](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)上与我们分享知识。
* 有关角色权限的详细信息，请参阅 [Azure AD 内置角色](permissions-reference.md)。
* 有关默认用户权限，请参阅[默认来宾和成员用户权限的比较](../fundamentals/users-default-permissions.md)。
