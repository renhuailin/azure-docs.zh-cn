---
title: 适用于 PIM 中 Azure AD 角色的 PowerShell - Azure AD | Microsoft Docs
description: 使用 Azure AD Privileged Identity Management (PIM) 中的 PowerShell cmdlet 管理 Azure AD 角色。
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/07/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b25ae76e2af971c32b027245a852c29f088a145
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129669219"
---
# <a name="powershell-for-azure-ad-roles-in-privileged-identity-management"></a>适用于 Privileged Identity Management 中 Azure AD 角色的 PowerShell

本文说明如何使用 Azure Active Directory (Azure AD) PowerShell cmdlet 管理 Privileged Identity Management (PIM) 中的 Azure AD 角色。 此外，本文还介绍如何安装 Azure AD PowerShell 模块。

## <a name="installation-and-setup"></a>安装和设置

1. 安装 Azure AD 预览版模块

    ```powershell
    Install-module AzureADPreview
    ```

1. 在继续之前，请确保你拥有所需的角色权限。 若要尝试执行管理任务（例如分配角色或更新角色设置），请确保具有“全局管理员”或“特权角色管理员”角色。 如果你只是尝试激活自己的分配，则除了默认的用户权限以外，不需要其他任何权限。

1. 连接到 Azure AD。

    ```powershell
    $AzureAdCred = Get-Credential  
    Connect-AzureAD -Credential $AzureAdCred
    ```

1. 转到“Azure Active Directory” > “属性” > “目录 ID”，找到你的 Azure AD 组织的租户 ID。   在“cmdlet”部分，每当需要提供 resourceId 时，都需要使用此 ID。

    ![在 Azure AD 组织的属性中查找组织 ID](./media/powershell-for-azure-ad-roles/tenant-id-for-Azure-ad-org.png)

> [!Note]
> 以下部分提供了简单的示例来帮助你开始操作。 有关以下 cmdlet 的更详细文档，请参阅 [/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#privileged_role_management](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#privileged_role_management)。 但是，必须将 providerID 参数中的“azureResources”替换为“aadRoles”。 此外，还需要记得使用 Azure AD 组织的租户 ID 作为 resourceId 参数。

## <a name="retrieving-role-definitions"></a>检索角色定义

使用以下 cmdlet 获取 Azure AD 组织中的所有内置和自定义 Azure AD 角色。 此重要步骤在角色名称与 roleDefinitionId 之间提供映射。 所有这些 cmdlet 中都会使用 roleDefinitionId 来引用特定的角色。

roleDefinitionId 特定于 Azure AD 组织，不同于角色管理 API 返回的 roleDefinitionId。

```powershell
Get-AzureADMSPrivilegedRoleDefinition -ProviderId aadRoles -ResourceId 926d99e7-117c-4a6a-8031-0cc481e9da26
```

结果：

![获取 Azure AD 组织的所有角色](./media/powershell-for-azure-ad-roles/get-all-roles-result.png)

## <a name="retrieving-role-assignments"></a>检索角色分配

使用以下 cmdlet 检索 Azure AD 组织中的所有角色分配。

```powershell
Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26"
```

使用以下 cmdlet 检索特定用户的所有角色分配。 此列表在 Azure AD 门户中也称作“我的角色”。 唯一的差别在于，为使用者 ID 添加了一个筛选器。 此上下文中的使用者 ID 是用户 ID 或组 ID。

```powershell
Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "subjectId eq 'f7d1887c-7777-4ba3-ba3d-974488524a9d'" 
```

使用以下 cmdlet 检索特定角色的所有角色分配。 此处的 roleDefinitionId 是上一个 cmdlet 返回的 ID。

```powershell
Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "roleDefinitionId eq '0bb54a22-a3df-4592-9dc7-9e1418f0f61c'"
```

这些 cmdlet 将生成如下所示的角色分配对象列表。 使用者 ID 是角色分配到的用户的用户 ID。 分配状态可以是 active 或 eligible。 如果用户处于 active 状态，并且 LinkedEligibleRoleAssignmentId 字段中有一个 ID，则表示该角色当前已激活。

结果：

![检索 Azure AD 组织的所有角色分配](./media/powershell-for-azure-ad-roles/get-all-role-assignments-result.png)

## <a name="assign-a-role"></a>分配角色

使用以下 cmdlet 创建符合条件的分配。

```powershell
Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'ff690580-d1c6-42b1-8272-c029ded94dec' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'adminAdd' -AssignmentState 'Eligible' -schedule $schedule -reason "dsasdsas" 
```

用于定义分配开始时间和结束时间的计划是可按如下示例所示创建的对象：

```powershell
$schedule = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedSchedule
$schedule.Type = "Once"
$schedule.StartDateTime = (Get-Date).ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ss.fffZ")
$schedule.endDateTime = "2020-07-25T20:49:11.770Z"
```
> [!Note]
> 如果 endDateTime 的值设置为 null，则表示永久分配。

## <a name="activate-a-role-assignment"></a>激活角色分配

使用以下 cmdlet 在普通用户的上下文中激活符合条件的分配：

```powershell
Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'f55a9a68-f424-41b7-8bee-cee6a442d418' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'UserAdd' -AssignmentState 'Active' -Schedule $schedule -Reason "Business Justification for the role assignment"
``` 

如果需要以管理员角色激活符合条件的分配，请为 `Type` 参数指定 `adminAdd`：

```powershell
Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'f55a9a68-f424-41b7-8bee-cee6a442d418' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'adminAdd' -AssignmentState 'Active' -Schedule $schedule -Reason "Business Justification for the role assignment"
``` 

此 cmdlet 与用于创建角色分配的 cmdlet 几乎完全相同。 这些 cmdlet 的主要差别在于，对于 -Type 参数，激活类型为“userAdd”而不是“adminAdd”。 另一个差别是，-AssignmentState 参数是“Active”而不是“Eligible”。

> [!Note]
> 通过 PowerShell 激活角色会出现两种限制性的情况。
> 1. 如果角色设置中需要票证系统/票证编号，则无法提供这些值作为参数。 因此，只能通过 Azure 门户激活角色。 在未来的几个月，此项功能即会在 PowerShell 中推出。
> 1. 如果需要使用多重身份验证来激活角色，当用户激活其角色时，PowerShell 目前无法质询用户。 在此情况下，用户在连接到 Azure AD 时，需要根据我们的某位工程师撰写的[此篇博客文章](http://www.anujchaudhary.com/2020/02/connect-to-azure-ad-powershell-with-mfa.html)触发 MFA 质询。 如果你正在开发 PIM 相关的应用，一种可行的实施方案是质询用户，并在他们收到“MfaRule”错误后将其重新连接到模块。

## <a name="retrieving-and-updating-role-settings"></a>检索和更新角色设置

使用以下 cmdlet 获取 Azure AD 组织中的所有角色设置。

```powershell
Get-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Filter "ResourceId eq '926d99e7-117c-4a6a-8031-0cc481e9da26'"
```

设置中有四个主要对象。 PIM 目前只使用其中的三个对象。 UserMemberSettings 是激活设置，AdminEligibleSettings 是符合条件的分配的分配设置，AdminmemberSettings 是活动分配的分配设置。

[![获取和更新角色设置。](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png)](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png#lightbox)

若要更新角色设置，必须获取特定角色的现有设置对象并对其进行更改：

```powershell
Get-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Filter "ResourceId eq 'tenant id' and RoleDefinitionId eq 'role id'"
$settinga = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedRuleSetting
$settinga.RuleIdentifier = "JustificationRule"
$settinga.Setting = '{"required":false}'
```

然后，可以继续将设置应用到特定角色的某个对象，如下所示。 此处的 ID 是可以从“列出角色设置”cmdlet 的结果中检索到的角色设置 ID。

```powershell
Set-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Id 'ff518d09-47f5-45a9-bb32-71916d9aeadf' -ResourceId '3f5887ed-dd6e-4821-8bde-c813ec508cf9' -RoleDefinitionId '2387ced3-4e95-4c36-a915-73d803f93702' -UserMemberSettings $settinga 
```

## <a name="next-steps"></a>后续步骤

- [Azure AD 中的角色定义](../roles/permissions-reference.md)
