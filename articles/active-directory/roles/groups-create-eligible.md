---
title: 在 Azure Active Directory 中创建组以分配角色 | Microsoft Docs
description: 了解如何在 Azure AD 中创建可分配角色的组。 在 Azure 门户、PowerShell 或图形 API 中管理 Azure 角色。
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: article
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d69662ca24ab135db549ee108e93f9276e154e58
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2021
ms.locfileid: "106106902"
---
# <a name="create-a-role-assignable-group-in-azure-active-directory"></a>在 Azure Active Directory 中创建可分配角色的组

只能将角色分配到在创建时“isAssignableToRole”属性设置为 True 的组，或是在 Azure AD 门户中创建时打开了“Azure AD 角色可以分配到组”的组。 具有此组属性，组才会成为你可以在 Azure Active Directory (Azure AD) 中为其分配角色的组。 本文介绍如何创建这种特殊类型的组。 注意：isAssignableToRole 属性设置为 true 的组不能是动态成员身份类型。 有关详细信息，请参阅[使用组来管理 Azure AD 角色分配](groups-concept.md)。

## <a name="using-azure-ad-admin-center"></a>使用 Azure AD 管理中心

1. 使用 Azure AD 组织中的特权角色管理员或全局管理员权限登录到 [Azure AD 管理中心](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)。
1. 选择“组” > “所有组” > “新建组”  。

    [![打开 Azure Active Directory 并创建新组。](./media/groups-create-eligible/new-group.png "打开 Azure Active Directory 并创建新组。")](./media/groups-create-eligible/new-group.png#<lightbox>)

1. 在“新建组”选项卡上，提供组的类型、名称和说明。
1. 打开“Azure AD 角色可以分配到组”。 只有特权角色管理员和全局管理员才能看到此开关，因为只有这两个角色可以设置该开关。

    [![使新组符合角色分配条件](./media/groups-create-eligible/eligible-switch.png "使新组符合角色分配条件")](./media/groups-create-eligible/eligible-switch.png#<lightbox>)

1. 选择该组的成员和所有者。 也可以选择将角色分配到组，但并不需要在此处分配角色。

    [![将成员添加到可分配角色的组并分配角色。](./media/groups-create-eligible/specify-members.png "将成员添加到可分配角色的组并分配角色。")](./media/groups-create-eligible/specify-members.png#<lightbox>)

1. 在指定了成员和所有者后，选择“创建”。

    [![“创建”按钮位于该页面的底部。](./media/groups-create-eligible/create-button.png "“创建”按钮位于该页面的底部。")](./media/groups-create-eligible/create-button.png#<lightbox>)

该组创建完成，具有你可能已经分配给它的角色。

## <a name="using-powershell"></a>使用 PowerShell

### <a name="install-the-azure-ad-preview-module"></a>安装 Azure AD 预览版模块

```powershell
Install-Module -Name AzureADPreview
Import-Module -Name AzureADPreview
```

若要验证该模块是否已可供使用，请发出以下命令：

```powershell
Get-Module -Name AzureADPreview
```

### <a name="create-a-group-that-can-be-assigned-to-role"></a>创建可分配给角色的组

```powershell
$group = New-AzureADMSGroup -DisplayName "Contoso_Helpdesk_Administrators" -Description "This group is assigned to Helpdesk Administrator built-in role in Azure AD." -MailEnabled $true -SecurityEnabled $true -MailNickName "contosohelpdeskadministrators" -IsAssignableToRole $true
```

对于这种类型的组，`isPublic` 将始终为 false，`isSecurityEnabled` 将始终为 true。

### <a name="copy-one-groups-users-and-service-principals-into-a-role-assignable-group"></a>将一个组的用户和服务主体复制到可分配角色的组中

```powershell
#Basic set up
Install-Module -Name AzureADPreview
Import-Module -Name AzureADPreview
Get-Module -Name AzureADPreview

#Connect to Azure AD. Sign in as Privileged Role Administrator or Global Administrator. Only these two roles can create a role-assignable group.
Connect-AzureAD

#Input variabled: Existing group
$idOfExistingGroup = "14044411-d170-4cb0-99db-263ca3740a0c"

#Input variables: New role-assignable group
$groupName = "Contoso_Bellevue_Admins"
$groupDescription = "This group is assigned to Helpdesk Administrator built-in role in Azure AD."
$mailNickname = "contosobellevueadmins"

#Create new security group which is a role assignable group. For creating a Microsoft 365 group, set GroupTypes="Unified" and MailEnabled=$true
$roleAssignablegroup = New-AzureADMSGroup -DisplayName $groupName -Description $groupDescription -MailEnabled $false -MailNickname $mailNickname -SecurityEnabled $true -IsAssignableToRole $true

#Get details of existing group
$existingGroup = Get-AzureADMSGroup -Id $idOfExistingGroup
$membersOfExistingGroup = Get-AzureADGroupMember -ObjectId $existingGroup.Id

#Copy users and service principals from existing group to new group
foreach($member in $membersOfExistingGroup){
if($member.ObjectType -eq 'User' -or $member.ObjectType -eq 'ServicePrincipal'){
Add-AzureADGroupMember -ObjectId $roleAssignablegroup.Id -RefObjectId $member.ObjectId
}
}
```

## <a name="using-microsoft-graph-api"></a>使用 Microsoft Graph API

### <a name="create-a-role-assignable-group-in-azure-ad"></a>在 Azure AD 中创建可分配角色的组

```http
POST https://graph.microsoft.com/beta/groups
{
  "description": "This group is assigned to Helpdesk Administrator built-in role of Azure AD.",
  "displayName": "Contoso_Helpdesk_Administrators",
  "groupTypes": [
    "Unified"
  ],
  "isAssignableToRole": true,
  "mailEnabled": true,
  "securityEnabled": true,
  "mailNickname": "contosohelpdeskadministrators",
  "visibility" : "Private"
}
```

对于这种类型的组，`isPublic` 将始终为 false，`isSecurityEnabled` 将始终为 true。

## <a name="next-steps"></a>后续步骤

- [向云组分配角色](groups-assign-role.md)
- [使用云组来管理角色分配](groups-concept.md)
- [分配给云组的角色疑难解答](groups-faq-troubleshooting.md)
