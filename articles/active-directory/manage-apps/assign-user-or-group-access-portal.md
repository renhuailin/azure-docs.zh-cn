---
title: 将用户和组分配到企业应用程序
titleSuffix: Azure AD
description: 了解如何为使用 Azure Active Directory 进行标识管理的应用分配和取消分配用户和组。
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 08/27/2021
ms.author: davidmu
ms.reviewer: alamaral
ms.openlocfilehash: bb7def388096d29e7ab1f41a745f9fbc192230ba
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123431939"
---
# <a name="assign-users-and-groups-to-an-enterprise-application"></a>将用户和组分配到企业应用程序

本文介绍如何使用 PowerShell 将用户和组分配到 Azure Active Directory (Azure AD) 中的企业应用程序。 当你将用户分配到某个应用程序时，该应用程序会显示在该用户的“我的应用”门户中以方便访问。 如果应用程序公开角色，则你还可以将特定的角色分配给用户。

## <a name="prerequisites"></a>先决条件

若要使用 PowerShell 将用户分配到应用，需要具备以下各项：

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 以下角色之一：全局管理员、云应用程序管理员、应用程序管理员或服务主体的所有者。
- 设置 Azure AD PowerShell。 请参阅 [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/)
- 可选：适用于基于组的分配的 Azure Active Directory Premium P1 或 P2 版。 有关本文中讨论的功能的其他许可要求，请参阅 [Azure Active Directory 定价页](https://azure.microsoft.com/pricing/details/active-directory)。
- 可选：完成[配置应用](add-application-portal-configure.md)。

## <a name="assign-users-and-groups-to-an-app-using-powershell"></a>使用 PowerShell 将用户和组分配到应用

1. 以提升的权限打开 Windows PowerShell 命令提示符。
   > [!NOTE]
   > 需要安装 AzureAD 模块（使用命令 `Install-Module -Name AzureAD`）。 出现安装 NuGet 模块或新的 Azure Active Directory V2 PowerShell 模块的提示时，请键入 Y，然后按 ENTER。
2. 运行 `Connect-AzureAD` 并使用全局管理员用户帐户登录。
3. 使用以下脚本将用户和角色分配到应用程序：

    ```powershell
    # Assign the values to the variables
    $username = "<Your user's UPN>"
    $app_name = "<Your App's display name>"
    $app_role_name = "<App role display name>"

    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }

    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id

To assign a group to an enterprise app, you must replace `Get-AzureADUser` with `Get-AzureADGroup` and replace `New-AzureADUserAppRoleAssignment` with `New-AzureADGroupAppRoleAssignment`.

For more information about how to assign a group to an application role, see the documentation for [New-AzureADGroupAppRoleAssignment](/powershell/module/azuread/new-azureadgroupapproleassignment).

### Example

This example assigns the user Britta Simon to the Microsoft Workplace Analytics application using PowerShell.

1. In PowerShell, assign the corresponding values to the variables $username, $app_name and $app_role_name.

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"

2. In this example, we don't know what is the exact name of the application role we want to assign to Britta Simon. Run the following commands to get the user ($user) and the service principal ($sp) using the user UPN and the service principal display names.

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"

3. Run the command `$sp.AppRoles` to display the roles available for the Workplace Analytics application. In this example, we want to assign Britta Simon the Analyst (Limited access) Role.
   ![Shows the roles available to a user using Workplace Analytics Role](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)
4. Assign the role name to the `$app_role_name` variable.

    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }

5. Run the following command to assign the user to the app role:

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="unassign-users-and-groups-from-an-app-using-powershell"></a>使用 PowerShell 从应用中取消分配用户和组

1. 以提升的权限打开 Windows PowerShell 命令提示符。
   > [!NOTE]
   > 需要安装 AzureAD 模块（使用命令 `Install-Module -Name AzureAD`）。 出现安装 NuGet 模块或新的 Azure Active Directory V2 PowerShell 模块的提示时，请键入 Y，然后按 ENTER。
2. 运行 `Connect-AzureAD` 并使用全局管理员用户帐户登录。
3. 使用以下脚本将用户和角色从应用程序中删除：

    ```powershell
    # Store the proper parameters
    $user = get-azureaduser -ObjectId <objectId>
    $spo = Get-AzureADServicePrincipal -ObjectId <objectId>

    #Get the ID of role assignment 
    $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId | Where {$_.PrincipalDisplayName -eq $user.DisplayName}

    #if you run the following, it will show you what is assigned what
    $assignments | Select *

    #To remove the App role assignment run the following command.
    Remove-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId -AppRoleAssignmentId $assignments[assignment #].ObjectId
    ```

## <a name="related-articles"></a>相关文章

- [使用 Azure 门户分配或取消分配用户或组](add-application-portal-assign-users.md)
- [使用图形 API 为应用分配或取消分配用户和组](/graph/api/resources/approleassignment)
- [管理对应用的访问权限](what-is-access-management.md)

## <a name="next-steps"></a>后续步骤

详细了解如何使用以下资源来管理用户分配和访问权限

- [删除用户对应用的访问权限的方法](methods-for-removing-user-access.md)
- [对用户隐藏应用](hide-application-from-user-portal.md)
- [禁用企业应用的用户登录](disable-user-sign-in-portal.md)
