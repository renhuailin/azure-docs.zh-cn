---
title: 使用管理单元范围分配和列出角色 - Azure Active Directory | Microsoft Docs
description: 在 Azure Active Directory 中使用管理单元来限制角色分配的范围。
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 05/14/2021
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b34eafac248bc0fd06076550e784a061573a712
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734499"
---
# <a name="assign-scoped-roles-to-an-administrative-unit"></a>向管理单元分配限定范围的角色

若要在 Azure Active Directory (Azure AD) 中实现更精细的管理控制，可向用户分配范围限定为一个或多个管理单元的 Azure AD 角色。

## <a name="prerequisites"></a>先决条件

- 每个管理单元管理员都有 Azure AD Premium P1 或 P2 许可证
- 管理单元成员有 Azure AD 免费许可证
- 特权角色管理员或全局管理员
- 使用 PowerShell 时需要 AzureAD 模块
- 将 Graph 浏览器用于 Microsoft Graph API 时需要管理员同意

有关详细信息，请参阅[使用 PowerShell 或 Graph 浏览器的先决条件](prerequisites.md)。


## <a name="available-roles"></a>可用的角色

角色  |  描述
----- |  -----------
身份验证管理员  |  其访问权限仅限于查看、设置和重置所分配的管理单元中任何非管理员用户的身份验证方法信息。
组管理员  |  只能在所分配的管理单元中管理组和组设置的所有方面，如命名策略和过期策略。
支持管理员  |  只能重置所分配的管理单元中非管理员和支持管理员的密码。
许可证管理员  |  只能在管理单元内分配、删除和更新许可证分配。
密码管理员  |  只能在所分配的管理单元内重置非管理员和密码管理员的密码。
用户管理员  |  只能在所分配的管理单元内管理用户和组的所有方面，包括重置受限管理员的密码。

## <a name="security-principals-that-can-be-assigned-to-a-scoped-role"></a>可分配给范围角色的安全主体

可以将以下安全主体分配给具有管理单元范围的角色：

* 用户
* 可分配角色的 Azure AD 组
* 服务主体名称 (SPN)

## <a name="assign-a-scoped-role"></a>分配限定范围的角色

可使用 Azure 门户、PowerShell 或 Microsoft Graph 分配限定范围的角色。

### <a name="azure-portal"></a>Azure 门户

1. 登录到 [Azure 门户](https://portal.azure.com)或 [Azure AD 管理中心](https://aad.portal.azure.com)。

1. 选择“Azure Active Directory” > “管理单元”，然后选择要向其分配用户角色范围的管理单元 。 

1. 在左侧窗格中，选择“角色和管理员”以列出所有可用的角色。

   ![“角色和管理员”窗格的屏幕截图，用于选择要分配角色范围的管理单元。](./media/admin-units-assign-roles/select-role-to-scope.png)

1. 选择要分配的角色，然后选择“添加分配”。 

1. 在“添加分配”窗格中，选择要分配给该角色的一个或多个用户。

   ![选择要限定范围的角色，然后选择“添加分配”](./media/admin-units-assign-roles/select-add-assignment.png)

> [!Note]
> 若要使用 Azure AD Privileged Identity Management (PIM) 在管理单元上分配角色，请参阅[在 PIM 中分配 Azure AD 角色](../privileged-identity-management/pim-how-to-add-role-to-user.md?tabs=new#assign-a-role-with-restricted-scope)。

### <a name="powershell"></a>PowerShell

```powershell
$adminUser = Get-AzureADUser -ObjectId "Use the user's UPN, who would be an admin on this unit"
$role = Get-AzureADDirectoryRole | Where-Object -Property DisplayName -EQ -Value "User Administrator"
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
$roleMember = New-Object -TypeName Microsoft.Open.MSGraph.Model.MsRoleMemberInfo
$roleMember.Id = $adminUser.ObjectId
Add-AzureADMSScopedRoleMembership -Id $adminUnitObj.Id -RoleId $role.ObjectId -RoleMemberInfo $roleMember
```

可以根据特定环境的需要更改突出显示的部分。

### <a name="microsoft-graph-api"></a>Microsoft Graph API

请求

```http
POST /directory/administrativeUnits/{admin-unit-id}/scopedRoleMembers
```
    
正文

```http
{
  "roleId": "roleId-value",
  "roleMemberInfo": {
    "id": "id-value"
  }
}
```

## <a name="view-a-list-of-the-scoped-admins-in-an-administrative-unit"></a>查看管理单元中限定范围的管理员列表

可使用 Azure 门户、PowerShell 或 Microsoft Graph 查看限定范围的管理员列表。

### <a name="azure-portal"></a>Azure 门户

可在 [Azure AD 的“管理单元”部分](https://ms.portal.azure.com/?microsoft_aad_iam_adminunitprivatepreview=true&microsoft_aad_iam_rbacv2=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AdminUnit)中查看使用管理单元范围创建的所有角色分配。 

1. 登录到 [Azure 门户](https://portal.azure.com)或 [Azure AD 管理中心](https://aad.portal.azure.com)。

1. 选择“Azure Active Directory” > “管理单元”，然后选择要查看的角色分配列表的管理单元 。 

1. 选择“角色和管理员”，然后打开一个角色查看该管理单元中的分配。

### <a name="powershell"></a>PowerShell

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
Get-AzureADMSScopedRoleMembership -Id $adminUnitObj.Id | fl *
```

可以根据特定环境的需要更改突出显示的部分。

### <a name="microsoft-graph-api"></a>Microsoft Graph API

请求

```http
GET /directory/administrativeUnits/{admin-unit-id}/scopedRoleMembers
```

正文

```http
{}
```

## <a name="next-steps"></a>后续步骤

- [使用 Azure AD 组来管理角色分配](groups-concept.md)
- [排查分配给组的 Azure AD 角色的问题](groups-faq-troubleshooting.yml)
