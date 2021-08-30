---
title: 列出 Azure AD 角色定义 - Azure AD
description: 了解如何列出 Azure 内置角色和自定义角色。
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 07/23/2021
ms.author: rolyon
ms.reviewer: absinh
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c5131cb56ff65b6c559186cf491c4367ecbc7d5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748958"
---
# <a name="list-azure-ad-role-definitions"></a>列出 Azure AD 角色定义

角色定义是可执行特权的集合，例如读取、写入和删除。 它通常直接称为“角色”。 Azure Active Directory 有 60 多种内置角色，你也可以创建自己的自定义角色。 如果你曾经有过“这些角色究竟有什么用？”这样的疑问，可查看每个角色的详细权限列表。

本文介绍如何列出 Azure AD 内置角色和自定义角色及其权限。

## <a name="prerequisites"></a>先决条件

- 使用 PowerShell 时需要 AzureADPreview 模块
- 将 Graph 浏览器用于 Microsoft Graph API 时需要管理员同意

有关详细信息，请参阅[使用 PowerShell 或 Graph 浏览器的先决条件](prerequisites.md)。

## <a name="azure-portal"></a>Azure 门户

1. 登录到 [Azure 门户](https://portal.azure.com)或 [Azure AD 管理中心](https://aad.portal.azure.com)。

1. 选择“Azure Active Directory” > “角色和管理员”以查看所有可用角色列表 。

    ![Azure 门户中的角色列表](./media/role-definitions-list/view-roles-in-azure-active-directory.png)

1. 在右侧选择省略号，然后选择“说明”来查看角色权限的完整列表。

    该页包含相关文档的链接，引导你对角色进行管理。

    ![显示“全局管理员 - 说明”页面的屏幕截图。](./media/role-definitions-list/role-description-updated.png)

## <a name="powershell"></a>PowerShell

执行以下步骤，使用 PowerShell 列出 Azure AD 角色。

1. 打开 PowerShell 窗口并使用 [Import-Module](/powershell/module/microsoft.powershell.core/import-module) 导入 AzureADPreview 模块。 有关详细信息，请参阅[使用 PowerShell 或 Graph 浏览器的先决条件](prerequisites.md)。

    ```powershell
    Import-Module -Name AzureADPreview -Force
    ```

2. 在 PowerShell 窗口中，使用 [Connect-AzureAD](/powershell/module/azuread/connect-azuread) 登录租户。

    ```powershell
    Connect-AzureAD
    ```
3. 使用 [Get-AzureADMSRoleDefinition](/powershell/module/azuread/get-azureadmsroledefinition) 获取所有角色。

    ```powershell
    Get-AzureADMSRoleDefinition
    ```

4. 要查看角色的权限列表，请使用以下 cmdlet。
    
    ```powershell
    # Do this avoid truncation of the list of permissions
    $FormatEnumerationLimit = -1
    
    (Get-AzureADMSRoleDefinition -Filter "displayName eq 'Conditional Access Administrator'").RolePermissions | Format-list
    ```

## <a name="microsoft-graph-api"></a>Microsoft Graph API

按照以下说明操作，使用 [Graph 浏览器](https://aka.ms/ge)中的 Microsoft Graph API 列出 Azure AD 角色。

1. 登录到 [Graph 浏览器](https://aka.ms/ge)。
2. 从下拉列表中选择 GET 作为 HTTP 方法。 
3. 选择的 API 版本为“beta 版本”。
4. 添加以下查询以使用[列出 roleDefinitions ](/graph/api/rbacapplication-list-roledefinitions) API。

   ```HTTP
   GET https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
   ```

5. 选择“运行查询”以列出角色。
6. 要查看角色的权限，请使用以下 API。

   ```HTTP
   GET https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions?$filter=DisplayName eq 'Conditional Access Administrator'&$select=rolePermissions
   ```

## <a name="next-steps"></a>后续步骤

* [列出 Azure AD 角色分配](view-assignments.md)。
* [向用户分配 Azure AD 角色](manage-roles-portal.md)。
* [Azure AD 内置角色](permissions-reference.md)。
