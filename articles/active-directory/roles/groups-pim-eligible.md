---
title: 在 Azure AD 中使用 Privileged Identity Management 将角色分配给组 | Microsoft Docs
description: 了解如何使用 Azure AD Privileged Identity Management (PIM) 将 Azure Active Directory (Azure AD) 角色分配给组。
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: article
ms.date: 05/14/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9d1d5e1b9a159d8084c3bf93d00611af72f2b21
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110085862"
---
# <a name="assign-a-role-to-a-group-using-privileged-identity-management"></a>使用 Privileged Identity Management 将角色分配给组

本文介绍如何使用 Azure AD Privileged Identity Management (PIM) 将 Azure Active Directory (Azure AD) 角色分配给组。

> [!NOTE]
> 必须使用 Privileged Identity Management 的更新版本才能使用 PIM 将组分配给 Azure AD 角色。 如果你的 Azure AD 组织使用的是 Privileged Identity Management API，则你可能使用的是旧版本的 PIM。 如果是这样，请联系别名 pim_preview@microsoft.com 来移动你的组织并更新你的 API。 有关详细信息，请参阅 [PIM 中的 Azure AD 角色和功能](../privileged-identity-management/azure-ad-roles-features.md)。

## <a name="prerequisites"></a>先决条件

- Azure AD Premium P2 许可证
- 特权角色管理员或全局管理员
- 使用 PowerShell 时需要 AzureADPreview 模块
- 将 Graph 浏览器用于 Microsoft Graph API 时需要管理员同意

有关详细信息，请参阅[使用 PowerShell 或 Graph 浏览器的先决条件](prerequisites.md)。

## <a name="azure-portal"></a>Azure 门户

1. 登录到 [Azure AD Privileged Identity Management](https://ms.portal.azure.com/?Microsoft_AAD_IAM_GroupRoles=true&Microsoft_AAD_IAM_userRolesV2=true&Microsoft_AAD_IAM_enablePimIntegration=true#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart)。

1. 选择“Privileged Identity Management” > “Azure AD 角色” > “角色” > “添加分配”   

1. 选择一个角色，然后选择一个组。 仅显示符合角色分配条件的组（可分配角色的组），而不是所有组。

    ![屏幕截图，其中显示了“添加分配”页，页面上突出显示了“选择角色”和“选择成员”部分。](./media/groups-pim-eligible/select-member.png)

1. 选择所需的成员资格设置。 对于需要激活的角色，请选择“符合条件的”。 默认情况下，用户将是永久符合条件的，但你也可以为用户的资格设置开始和结束时间。 完成后，单击“保存”并“添加”以完成角色分配。

    ![选择要分配到该角色的用户](./media/groups-pim-eligible/set-assignment-settings.png)

## <a name="powershell"></a>PowerShell

### <a name="assign-a-group-as-an-eligible-member-of-a-role"></a>将组分配为角色的符合条件的成员

```powershell
$schedule = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedSchedule
$schedule.Type = "Once"
$schedule.StartDateTime = "2019-04-26T20:49:11.770Z"
$schedule.endDateTime = "2019-07-25T20:49:11.770Z"
Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId aadRoles -Schedule $schedule -ResourceId "[YOUR TENANT ID]" -RoleDefinitionId "9f8c1837-f885-4dfd-9a75-990f9222b21d" -SubjectId "[YOUR GROUP ID]" -AssignmentState "Eligible" -Type "AdminAdd"
```

## <a name="microsoft-graph-api"></a>Microsoft Graph API

```http
POST
https://graph.microsoft.com/beta/privilegedAccess/aadroles/roleAssignmentRequests
{
 "roleDefinitionId": {roleDefinitionId},
 "resourceId": {tenantId},
 "subjectId": {GroupId},
 "assignmentState": "Eligible",
 "type": "AdminAdd",
 "reason": "reason string",
 "schedule": {
   "startDateTime": {DateTime},
   "endDateTime": {DateTime},
   "type": "Once"
 }
}
```

## <a name="next-steps"></a>后续步骤

- [使用云组来管理角色分配](groups-concept.md)
- [分配给云组的角色疑难解答](groups-faq-troubleshooting.md)
- [在 Privileged Identity Management 中配置 Azure AD 管理员角色设置](../privileged-identity-management/pim-how-to-change-default-settings.md)
- [在 Privileged Identity Management 中分配 Azure 资源角色](../privileged-identity-management/pim-resource-roles-assign-roles.md)
