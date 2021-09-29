---
title: 列出用户的 Azure AD 角色分配 - Azure Active Directory
description: 了解如何列出用户的 Azure AD 角色分配
services: active-directory
author: abhijeetsinha
manager: vincesm
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 08/12/2021
ms.author: absinh
ms.reviewer: rolyon
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58647d37da29066c14457ae6624763c800e6f123
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124776410"
---
# <a name="list-azure-ad-role-assignments-for-a-user"></a>列出用户的 Azure AD 角色分配

可以直接将某个角色分配给用户，也可以通过组间接分配。 本文介绍如何列出分配给用户的 Azure AD 角色。 有关为组分配角色的信息，请参阅[使用 Azure AD 组管理角色分配](groups-concept.md)。

## <a name="prerequisites"></a>先决条件

- 使用 PowerShell 时需要 AzureADPreview 模块
- 使用 PowerShell 时需要 Microsoft.Graph 模块
- 将 Graph 浏览器用于 Microsoft Graph API 时需要管理员同意

有关详细信息，请参阅[使用 PowerShell 或 Graph 浏览器的先决条件](prerequisites.md)。

## <a name="azure-portal"></a>Azure 门户
执行以下步骤以使用 Azure 门户列出用户的 Azure AD 角色。 根据是否启用了 [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md)，体验会有所不同。

1. 登录到 [Azure 门户](https://portal.azure.com)或 [Azure AD 管理中心](https://aad.portal.azure.com)。

2. 选择“Azure Active Directory” > “用户” > “用户名” > “分配的角色”。

    可以在不同范围内查看分配给用户的角色的列表。 此外，还可以查看角色是直接分配的，还是通过组分配的。
    
    ![Azure 门户中分配给用户的角色列表](./media/list-role-assignments-users/list-role-definition.png)

    如果有 Premium P2 许可证，将看到 PIM 体验，其中包含合格、活动和过期的角色分配详细信息。

    ![PIM 中分配给用户的角色列表](./media/list-role-assignments-users/list-role-definition-pim.png)

## <a name="powershell"></a>PowerShell

执行以下步骤以使用 PowerShell 列出分配给用户的 Azure AD 角色。

1. 使用 [Install-module](/powershell/azure/active-directory/install-adv2) 安装 AzureADPreview 和 Microsoft.Graph 模块。
  
    ```powershell
    Install-module -name AzureADPreview
    Install-module -name Microsoft.Graph
    ```
  
2. 打开 PowerShell 窗口并使用 [Import-Module](/powershell/module/microsoft.powershell.core/import-module) 导入 AzureADPreview 模块。 有关详细信息，请参阅[使用 PowerShell 或 Graph 浏览器的先决条件](prerequisites.md)。

    ```powershell
    Import-Module -Name AzureADPreview -Force
    ```

3. 在 PowerShell 窗口中，使用 [Connect-AzureAD](/powershell/module/azuread/connect-azuread) 登录租户。

    ```powershell
    Connect-AzureAD
    ```
4. 使用 [Get-AzureADMSRoleAssignment](/powershell/module/azuread/get-azureadmsroleassignment) 获取直接分配给用户的角色。

    ```powershell
    #Get the user
    $userId = (Get-AzureADUser -Filter "userPrincipalName eq 'alice@contoso.com'").ObjectId

    #Get direct role assignments to the user
    $directRoles = (Get-AzureADMSRoleAssignment -Filter "principalId eq '$userId'").RoleDefinitionId
    ```

5. 若要获取分配给用户的可传递角色，请使用以下 cmdlet。
  
    a. 使用 [Get-AzureADMSGroup](/powershell/module/azuread/get-azureadmsgroup) 获取所有角色可分配组的列表。  
  
      ```powershell
      $roleAssignableGroups = (Get-AzureADMsGroup -All $true | Where-Object IsAssignableToRole -EQ 'True').Id
      ```

    b. 使用 [Connect-MgGraph](/graph/powershell/get-started) 登录并使用 Microsoft Graph PowerShell cmdlet。
  
      ```powershell
      Connect-MgGraph -Scopes "User.Read.All”
      ```
  
    c. 使用 [checkMemberObjects](/graph/api/user-checkmemberobjects) API 确定用户是哪个角色可分配组的成员。 
    
      ```powershell
      $uri = "https://graph.microsoft.com/beta/directoryObjects/$userId/microsoft.graph.checkMemberObjects"

      $userRoleAssignableGroups = (Invoke-MgGraphRequest -Method POST -Uri $uri -Body @{"ids"= $roleAssignableGroups}).value
      ```
  
    d. 使用 [Get-AzureADMSRoleAssignment](/powershell/module/azuread/get-azureadmsroleassignment) 循环访问组并获取分配给组的角色。
  
      ```powershell
      $transitiveRoles=@()
      foreach($item in $userRoleAssignableGroups){
          $transitiveRoles += (Get-AzureADMSRoleAssignment -Filter "principalId eq '$item'").RoleDefinitionId
      }
      ```

6. 合并用户的直接和可传递角色分配。
  
    ```powershell
    $allRoles = $directRoles + $transitiveRoles
    ```
  
## <a name="microsoft-graph-api"></a>Microsoft Graph API

按照以下步骤，使用 [Graph 浏览器](https://aka.ms/ge)中的 Microsoft Graph API 列出分配给用户的 Azure AD 角色。

1. 登录到 [Graph 浏览器](https://aka.ms/ge)。

1. 使用 [List roleAssignments](/graph/api/rbacapplication-list-roleassignments) API 列出直接分配给用户的角色。 将以下查询添加到 URL 并选择“运行查询”。

   ```HTTP
   GET https://graph.microsoft.com/beta/rolemanagement/directory/roleAssignments?$filter=principalId eq '55c07278-7109-4a46-ae60-4b644bc83a31'
   ```
  
3. 若要获取分配给用户的可传递角色，请执行以下步骤。

    a. 使用[列出组](/graph/api/group-list)获取所有角色可分配组的列表。
  
      ```HTTP
      GET https://graph.microsoft.com/beta/groups?$filter=isAssignableToRole eq true 
      ```
  
    b. 将此列表传递给 [checkMemberObjects](/graph/api/user-checkmemberobjects) API 确定用户是哪个角色可分配组的成员。 
    
      ```HTTP
      POST https://graph.microsoft.com/beta/users/55c07278-7109-4a46-ae60-4b644bc83a31/checkMemberObjects
      {
          "ids": [
              "936aec09-47d5-4a77-a708-db2ff1dae6f2",
              "5425a4a0-8998-45ca-b42c-4e00920a6382",
              "ca9631ad-2d2a-4a7c-88b7-e542bd8a7e12",
              "ea3cee12-360e-411d-b0ba-2173181daa76",
              "c3c263bb-b796-48ee-b4d2-3fbc5be5f944"
          ]
      }
      ```
  
    c. 使用 [List roleAssignments](/graph/api/rbacapplication-list-roleassignments) API 循环访问组并获取分配给组的角色。
  
      ```HTTP
      GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments?$filter=principalId eq '5425a4a0-8998-45ca-b42c-4e00920a6382' 
      ```

## <a name="next-steps"></a>后续步骤

* [列出 Azure AD 角色分配](view-assignments.md)。
* [向用户分配 Azure AD 角色](manage-roles-portal.md)。
* [向组分配 Azure AD 角色](groups-assign-role.md)
