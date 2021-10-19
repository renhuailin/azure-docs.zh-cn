---
title: 在 Azure AD 权利管理中查看、添加和删除访问包的分配 - Azure Active Directory
description: 了解如何在 Azure Active Directory 权利管理中查看、添加和删除访问包的分配。
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 10/05/2021
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: f944caecae6d35e680f5c5beb1a6e23fc422e698
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129618076"
---
# <a name="view-add-and-remove-assignments-for-an-access-package-in-azure-ad-entitlement-management"></a>在 Azure AD 权利管理中查看、添加和删除访问包的分配

在 Azure AD 权利管理中，可以看到哪一用户已分配到访问包，这些包的策略以及状态。 如果访问包具有相应的策略，也可以直接将用户分配到访问包。 本文介绍如何查看、添加和删除访问包的分配。

## <a name="prerequisites"></a>必备条件

若要使用 Azure AD 权利管理并将用户分配到访问包，必须具有以下许可证之一：

- Azure AD Premium P2
- 企业移动性 + 安全性 (EMS) E5 许可证

## <a name="view-who-has-an-assignment"></a>查看谁具有分配

**必备角色**：全局管理员、标识治理管理员、用户管理员、目录所有者、访问包管理员或访问包分配管理员

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。  

1. 在左侧菜单中单击“访问包”，然后打开访问包。

1. 单击“分配”查看活动分配的列表。

    ![访问包分配情况的列表](./media/entitlement-management-access-package-assignments/assignments-list.png)

1. 单击特定的分配可查看更多详细信息。

1. 若要查看并非所有资源角色都已未正确预配的分配列表，请单击筛选器状态，然后选择“正在传递”。

    在“请求”页上找到用户的对应请求，可以查看有关传递错误的更多详细信息。

1. 若要查看已过期的分配，请单击筛选器状态，然后选择“已过期”。

1. 若要下载筛选列表的 CSV 文件，请单击“下载”。

## <a name="view-assignments-programmatically"></a>以编程方式查看分配
### <a name="view-assignments-with-microsoft-graph"></a>使用 Microsoft Graph 查看分配
你也可以使用 Microsoft Graph 在访问包中检索分配。  借助具有委托的 `EntitlementManagement.Read.All` 或 `EntitlementManagement.ReadWrite.All` 权限的应用程序，相应角色中的用户可以调用 API 来[列出 accessPackageAssignments](/graph/api/accesspackageassignment-list?view=graph-rest-beta&preserve-view=true)。 当标识治理管理员可以从多个目录中检索访问包时，如果仅将用户分配给特定于目录的委托管理角色，则请求必须提供筛选器以指示特定访问包，如：`$filter=accessPackage/id eq 'a914b616-e04e-476b-aa37-91038f0b165b'`。 具有应用程序权限 `EntitlementManagement.Read.All` 或 `EntitlementManagement.ReadWrite.All` 的应用程序也可以使用此 API。

### <a name="view-assignments-with-powershell"></a>使用 PowerShell 查看分配

可以使用[适用于 Identity Governance 的 Microsoft Graph PowerShell cmdlet](https://www.powershellgallery.com/packages/Microsoft.Graph.Identity.Governance/) 模块 1.6.0 或更高版本中的 `Get-MgEntitlementManagementAccessPackageAssignment` cmdlet 在 PowerShell 中执行此查询。 此 cmdlet 将访问包 ID 作为参数包含在 `Get-MgEntitlementManagementAccessPackage` cmdlet 的响应中。

```powershell
Connect-MgGraph -Scopes "EntitlementManagement.Read.All"
Select-MgProfile -Name "beta"
$accesspackage = Get-MgEntitlementManagementAccessPackage -DisplayNameEq "Marketing Campaign"
$assignments = Get-MgEntitlementManagementAccessPackageAssignment -AccessPackageId $accesspackage.Id -ExpandProperty target -All -ErrorAction Stop
$assignments | ft Id,AssignmentState,TargetId,{$_.Target.DisplayName}
```

## <a name="directly-assign-a-user"></a>直接分配用户 

在某些情况下，你可能希望直接将特定的用户分配到访问包，使其不必要完成请求访问包的过程。 若要直接分配用户，访问包必须有一个允许管理员直接分配的策略。

**必备角色：** 全局管理员、用户管理员、目录所有者、访问包管理员或访问包分配管理员

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。  

1. 在左侧菜单中单击“访问包”，然后打开访问包。

1. 在左侧菜单中单击“分配”。

1. 单击“新建分配”打开“将用户添加到访问包”。

    ![分配 - 将用户添加到访问包](./media/entitlement-management-access-package-assignments/assignments-add-user.png)

1.  在“选择策略”列表中，选择将会控制和跟踪用户的未来请求和生命周期的策略。 如果希望所选用户具有不同的策略设置，可单击“创建新策略”来添加新策略。

1.  选择策略后，你将能够在所选策略下添加用户以选择要将此访问包分配到的用户。

    > [!NOTE]
    > 如果选择有问题的策略，则一次只能分配一个用户。

1. 设置所选用户的分配开始与结束日期和时间。 如果未提供结束日期，将使用该策略的生命周期设置。

1.  （可选）为直接分配提供理由，以保留记录。

1.  如果所选策略包含其他请求者信息，请单击“查看问题”以代表用户回答这些问题，然后单击“保存” 。  

     ![分配 - 单击查看问题](./media/entitlement-management-access-package-assignments/assignments-view-questions.png)

    ![分配 - 问题窗格](./media/entitlement-management-access-package-assignments/assignments-questions-pane.png)

1. 单击“添加”将所选用户直接分配到访问包。

    片刻之后，单击“刷新”查看“分配”列表中的用户。
    
> [!NOTE]
> 将用户分配到访问包时，管理员需要根据现有策略要求验证用户是否有资格获得该访问包。 否则，用户将无法成功分配到该访问包。 如果访问包包含要求批准用户请求的策略，则未经指定审批者的必要批准，无法将用户直接分配到该包。

## <a name="directly-assign-any-user-preview"></a>直接分配任何用户（预览版）
Azure AD 权利管理还允许将外部用户直接分配到访问包，以便更轻松地与合作伙伴协作。 为此，访问包必须具有一个策略，该策略允许尚不在你目录中的用户请求访问权限。

**必备角色：** 全局管理员、用户管理员、目录所有者、访问包管理员或访问包分配管理员

1.  在 Azure 门户中，依次选择“Azure Active Directory”和“标识治理” 。

1.  在左侧菜单中单击“访问包”，然后打开要向其添加用户的访问包。

1.  在左侧菜单中单击“分配”。

1.  选择“新建分配”打开“将用户添加到访问包”。 

1.  在“选择策略”列表中，选择一个策略，该策略允许设置为“不在你目录中的用户”。

1. 选择“任何用户”。 你可以指定要分配到此访问包的用户。
    ![分配 - 将任何用户添加到访问包](./media/entitlement-management-access-package-assignments/assignments-add-any-user.png)

1. 输入用户的“名称”（可选）和用户的“电子邮件地址”（必填）。 

    > [!NOTE]
    > - 要添加的用户必须在策略范围内。 例如，如果策略设置为“特定连接组织”，则用户的电子邮件地址必须来自所选组织的域。 如果尝试添加的用户的电子邮件地址为 jen@foo.com，但所选组织的域为 bar.com，则无法将该用户添加到访问包。
    > - 同样，如果将策略设置为包括“所有配置连接组织”，则用户的电子邮件地址必须来自其中一个配置连接组织。 否则，无法将用户添加到访问包。
    > - 如果要将任何用户添加到访问包，需要确保在配置策略时选择“所有用户(所有连接组织 + 任何外部用户)”。

1.  设置所选用户的分配开始与结束日期和时间。 如果未提供结束日期，将使用该策略的生命周期设置。
1.  单击“添加”将所选用户直接分配到访问包。
1.  片刻之后，单击“刷新”查看“分配”列表中的用户。

## <a name="directly-assigning-users-programmatically"></a>以编程方式直接分配用户
### <a name="assign-a-user-to-an-access-package-with-microsoft-graph"></a>使用 Microsoft Graph 将用户分配到访问包
你也可以使用 Microsoft Graph 直接将用户分配到访问包。  通过具有委托的 `EntitlementManagement.ReadWrite.All` 权限的应用程序，或通过具有该应用程序权限的应用程序，相应角色中的用户可以调用 API 来[创建 accessPackageAssignmentRequest](/graph/api/accesspackageassignmentrequest-post?view=graph-rest-beta&preserve-view=true)。 在此请求中，`requestType` 属性的值应为 `AdminAdd`，`accessPackageAssignment` 属性是一个结构，包含所分配用户的 `targetId`。

### <a name="assign-a-user-to-an-access-package-with-powershell"></a>使用 PowerShell 将用户分配到访问包

可以使用[适用于 Identity Governance 的 Microsoft Graph PowerShell cmdlet](https://www.powershellgallery.com/packages/Microsoft.Graph.Identity.Governance/) 模块 1.6.0 或更高版本中的 `New-MgEntitlementManagementAccessPackageAssignmentRequest` cmdlet 在 PowerShell 中将用户分配到访问包。 该 cmdlet 作为多种参数
* 访问包 ID，包含在 `Get-MgEntitlementManagementAccessPackage` cmdlet 的响应中，
* 访问包分配策略 ID，包含在 `Get-MgEntitlementManagementAccessPackageAssignmentPolicy` cmdlet 的响应中，
* 目标用户的对象 ID。

```powershell
Connect-MgGraph -Scopes "EntitlementManagement.ReadWrite.All"
Select-MgProfile -Name "beta"
$accesspackage = Get-MgEntitlementManagementAccessPackage -DisplayNameEq "Marketing Campaign" -ExpandProperty "accessPackageAssignmentPolicies"
$policy = $accesspackage.AccessPackageAssignmentPolicies[0]
$req = New-MgEntitlementManagementAccessPackageAssignmentRequest -AccessPackageId $accesspackage.Id -AssignmentPolicyId $policy.Id -TargetId "a43ee6df-3cc5-491a-ad9d-ea964ef8e464"
```

还可使用 [Microsoft Graph PowerShell cmdlet for Identity Governance](https://www.powershellgallery.com/packages/Microsoft.Graph.Identity.Governance/) 模块 1.6.1 或更高版本中的 `New-MgEntitlementManagementAccessPackageAssignment` cmdlet 在 PowerShell 中将多个用户分配到访问包。 该 cmdlet 作为多种参数
* 访问包 ID，包含在 `Get-MgEntitlementManagementAccessPackage` cmdlet 的响应中，
* 访问包分配策略 ID，包含在 `Get-MgEntitlementManagementAccessPackageAssignmentPolicy` cmdlet 的响应中，
* 目标用户的对象 ID，可以是字符串数组，或是从 `Get-MgGroupMember` cmdlet 返回的用户成员列表。

例如，如果要确保当前为组成员的所有用户也具有访问包的分配，可以使用此 cmdlet 为当前没有分配的用户创建请求。  请注意，此 cmdlet 仅会创建分配，不会删除分配。

```powershell
Connect-MgGraph -Scopes "EntitlementManagement.ReadWrite.All,Directory.Read.All"
Select-MgProfile -Name "beta"
$members = Get-MgGroupMember -GroupId "a34abd69-6bf8-4abd-ab6b-78218b77dc15"
$accesspackage = Get-MgEntitlementManagementAccessPackage -DisplayNameEq "Marketing Campaign" -ExpandProperty "accessPackageAssignmentPolicies"
$policy = $accesspackage.AccessPackageAssignmentPolicies[0]
$req = New-MgEntitlementManagementAccessPackageAssignment -AccessPackageId $accesspackage.Id -AssignmentPolicyId $policy.Id -RequiredGroupMember $members
```

## <a name="remove-an-assignment"></a>删除分配

**必备角色：** 全局管理员、用户管理员、目录所有者、访问包管理员或访问包分配管理员

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。  

1. 在左侧菜单中单击“访问包”，然后打开访问包。

1. 在左侧菜单中单击“分配”。
 
1. 单击要从访问包中删除其分配的用户旁边的复选框。 

1. 单击左侧窗格顶部附近的“删除”按钮。 
 
    ![分配 - 从访问包中删除用户](./media/entitlement-management-access-package-assignments/remove-assignment-select-remove-assignment.png)

    将会显示通知，告知该分配已被删除。 

## <a name="remove-an-assignment-programmatically"></a>以编程方式删除分配
### <a name="remove-an-assignment-with-microsoft-graph"></a>使用 Microsoft Graph 删除分配
你也可以使用 Microsoft Graph 删除用户到访问包的分配。  通过具有委托的 `EntitlementManagement.ReadWrite.All` 权限的应用程序，或通过具有该应用程序权限的应用程序，相应角色中的用户可以调用 API 来[创建 accessPackageAssignmentRequest](/graph/api/accesspackageassignmentrequest-post?view=graph-rest-beta&preserve-view=true)。  在此请求中，`requestType` 属性的值应为 `AdminRemove`，`accessPackageAssignment` 属性是一个结构，包含确定 `accessPackageAssignment` 是否被删除的 `id` 属性。

### <a name="remove-an-assignment-with-powershell"></a>使用 PowerShell 删除分配

可以使用[适用于 Identity Governance 的 Microsoft Graph PowerShell cmdlet](https://www.powershellgallery.com/packages/Microsoft.Graph.Identity.Governance/) 模块 1.6.0 或更高版本中的 `New-MgEntitlementManagementAccessPackageAssignmentRequest` cmdlet 在 PowerShell 中删除用户的分配。

```powershell
Connect-MgGraph -Scopes "EntitlementManagement.ReadWrite.All"
Select-MgProfile -Name "beta"
$assignments = Get-MgEntitlementManagementAccessPackageAssignment -Filter "accessPackageId eq '9f573551-f8e2-48f4-bf48-06efbb37c7b8' and assignmentState eq 'Delivered'" -All -ErrorAction Stop
$toRemove = $assignments | Where-Object {$_.targetId -eq '76fd6e6a-c390-42f0-879e-93ca093321e7'}
$req = New-MgEntitlementManagementAccessPackageAssignmentRequest -AccessPackageAssignmentId $toRemove.Id -RequestType "AdminRemove"
```

## <a name="next-steps"></a>后续步骤

- [更改访问包的请求和设置](entitlement-management-access-package-request-policy.md)
- [查看报表和日志](entitlement-management-reports.md)
