---
title: 分配和列出具有管理单元范围的角色-Azure Active Directory |Microsoft Docs
description: 使用管理单元在 Azure Active Directory 中限制角色分配的范围。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 11/04/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73ed2741c7dd754127a57642703b650a70637c63
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2020
ms.locfileid: "93393432"
---
# <a name="assign-scoped-roles-to-an-administrative-unit"></a>向管理单元分配限定范围的角色

在 Azure Active Directory (Azure AD) 中，为获得更精细的管理控制，可以将用户分配到一个范围限制为一个或多个管理单元的 Azure AD 角色。

若要准备使用 PowerShell 和 Microsoft Graph 管理单元管理，请参阅 [开始](admin-units-manage.md#get-started)使用。

## <a name="available-roles"></a>可用的角色

角色  |  描述
----- |  -----------
身份验证管理员  |  其访问权限仅限于查看、设置和重置所分配的管理单元中任何非管理员用户的身份验证方法信息。
组管理员  |  可以在分配的管理单元中管理组和组设置的所有方面，如命名和过期策略。
支持管理员  |  只能重置所分配的管理单元中非管理员和支持管理员的密码。
许可证管理员  |  只能在管理单元内分配、删除和更新许可证分配。
密码管理员  |  只能在所分配的管理单元内重置非管理员和密码管理员的密码。
用户管理员  |  只能在所分配的管理单元内管理用户和组的所有方面，包括重置受限管理员的密码。

## <a name="security-principals-that-can-be-assigned-to-a-scoped-role"></a>可分配给作用域角色的安全主体

可以将以下安全主体分配给具有管理单元范围的角色：

* 用户
* 角色可分配的云组 (预览版) 
* 服务主体名称 (SPN)

## <a name="assign-a-scoped-role"></a>分配限定范围的角色

可以使用 Azure 门户、PowerShell 或 Microsoft Graph 来分配作用域内角色。

### <a name="use-the-azure-portal"></a>使用 Azure 门户

1. 在 Azure 门户中，请参阅 **Azure AD** 。

1. 选择 " **管理单元** "，然后选择要向其分配用户角色作用域的管理单元。 

1. 在左侧窗格中，选择 " **角色和管理员** " 列出所有可用的角色。

   !["角色和管理员" 窗格的屏幕截图，用于选择要分配角色作用域的管理单元。](./media/admin-units-assign-roles/select-role-to-scope.png)

1. 选择要分配的角色，然后选择 " **添加分配** "。 

1. 在 " **添加分配** " 窗格中，选择要分配给该角色的一个或多个用户。

   ![选择要限定范围的角色，然后选择“添加分配”](./media/admin-units-assign-roles/select-add-assignment.png)

> [!Note]
> 若要通过使用 Azure AD Privileged Identity Management (PIM) 为管理单元分配角色，请参阅 [在 pim 中分配 Azure AD 角色](../privileged-identity-management/pim-how-to-add-role-to-user.md?tabs=new#assign-a-role-with-restricted-scope)。

### <a name="use-powershell"></a>使用 PowerShell

```powershell
$AdminUser = Get-AzureADUser -ObjectId "Use the user's UPN, who would be an admin on this unit"
$Role = Get-AzureADDirectoryRole | Where-Object -Property DisplayName -EQ -Value "User Account Administrator"
$administrativeUnit = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
$RoleMember = New-Object -TypeName Microsoft.Open.AzureAD.Model.RoleMemberInfo
$RoleMember.ObjectId = $AdminUser.ObjectId
Add-AzureADMSScopedRoleMembership -ObjectId $administrativeUnit.ObjectId -RoleObjectId $Role.ObjectId -RoleMemberInfo $RoleMember
```

你可以根据特定环境的需要更改突出显示的部分。

### <a name="use-microsoft-graph"></a>使用 Microsoft Graph

```http
Http request
POST /directory/administrativeUnits/{id}/scopedRoleMembers
    
Request body
{
  "roleId": "roleId-value",
  "roleMemberInfo": {
    "id": "id-value"
  }
}
```

## <a name="view-a-list-of-the-scoped-admins-in-an-administrative-unit"></a>在管理单元中查看作用域内管理员的列表

您可以使用 Azure 门户、PowerShell 或 Microsoft Graph 来查看作用域管理员的列表。

### <a name="use-the-azure-portal"></a>使用 Azure 门户

可以在 Azure AD 的 "管理单元" [部分](https://ms.portal.azure.com/?microsoft_aad_iam_adminunitprivatepreview=true&microsoft_aad_iam_rbacv2=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AdminUnit)中查看使用管理单元范围创建的所有角色分配。 

1. 在 Azure 门户中，请参阅 **Azure AD** 。

1. 在左窗格中，选择 " **管理单元** "，然后选择要查看的角色分配列表的管理单元。 

1. 选择 " **角色" 和 "管理员** "，然后打开角色以在管理单元中查看分配。

### <a name="use-powershell"></a>使用 PowerShell

```powershell
$administrativeUnit = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
Get-AzureADMSScopedRoleMembership -ObjectId $administrativeUnit.ObjectId | fl *
```

你可以根据特定环境的需要更改突出显示的部分。

### <a name="use-microsoft-graph"></a>使用 Microsoft Graph

```http
Http request
GET /directory/administrativeUnits/{id}/scopedRoleMembers
Request body
{}
```

## <a name="next-steps"></a>后续步骤

- [使用云组来管理角色分配](groups-concept.md)
- [排查分配给云组的角色问题](groups-faq-troubleshooting.md)
