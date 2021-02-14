---
title: 重置来宾用户的兑换状态-Azure AD
description: 了解如何重置 Azure AD 外部标识中 Azure Active Directory B2B 来宾用户的邀请兑换状态。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 02/03/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: d843755847d074e00aec9ed2830cb873b6bb3382
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100365406"
---
# <a name="reset-redemption-status-for-a-guest-user"></a>重置来宾用户的兑换状态

在来宾用户兑换你的 B2B 协作邀请后，有时你可能需要更新其登录信息，例如：

- 用户要使用其他电子邮件和标识提供程序登录
- 已删除并重新创建用户在其 home 租户中的帐户
- 用户已移动到其他公司，但仍需要对你的资源具有相同的访问权限
- 用户的责任已传递给其他用户

若要在之前管理这些方案，你必须从你的目录中手动删除来宾用户的帐户，并 reinvite 用户。 现在，你可以使用 PowerShell 或 Microsoft Graph 邀请 API 来重置用户的兑换状态并 reinvite 用户，同时保留用户的对象 ID、组成员身份和应用分配。 当用户兑换新邀请时，用户的 UPN 将不会更改，但用户的登录名称将更改为新的电子邮件。 然后，用户可以使用新电子邮件或已添加到该用户对象的属性的电子邮件登录 `otherMails` 。

## <a name="use-powershell-to-reset-redemption-status"></a>使用 PowerShell 重置兑换状态

安装最新的 AzureADPreview PowerShell 模块，并创建新的邀请 `InvitedUserEMailAddress` ，并将其设置为新的电子邮件地址，并 `ResetRedemption` 将设置为 `true` 。

```powershell  
Uninstall-Module AzureADPreview 
Install-Module AzureADPreview 
Connect-AzureAD 
$ADGraphUser = Get-AzureADUser -objectID "UPN of User to Reset"  
$msGraphUser = New-Object Microsoft.Open.MSGraph.Model.User -ArgumentList $ADGraphUser.ObjectId 
New-AzureADMSInvitation -InvitedUserEmailAddress <<external email>> -SendInvitationMessage $True -InviteRedirectUrl "http://myapps.microsoft.com" -InvitedUser $msGraphUser -ResetRedemption $True 
```

## <a name="use-microsoft-graph-api-to-reset-redemption-status"></a>使用 Microsoft Graph API 重置兑换状态

使用 [Microsoft Graph 邀请 API](/graph/api/resources/invitation?view=graph-rest-1.0)，将属性设置 `resetRedemption` 为， `true` 并在属性中指定新的电子邮件地址 `invitedUserEmailAddress` 。

```json
POST https://graph.microsoft.com/beta/invitations  
Authorization: Bearer eyJ0eX...  
ContentType: application/json  
{  
   "invitedUserEmailAddress": "<<external email>>",  
   "sendInvitationMessage": true,  
   "invitedUserMessageInfo": {  
      "messageLanguage": "en-US",  
      "ccRecipients": [  
         {  
            "emailAddress": {  
               "name": null,  
               "address": "<<optional additional notification email>>"  
            }  
         } 
      ],  
      "customizedMessageBody": "<<custom message>>"  
},  
"inviteRedirectUrl": "https://myapps.microsoft.com?tenantId=",  
"invitedUser": {  
   "id": "<<ID for the user you want to reset>>"  
}, 
"resetRedemption": true 
}
```

## <a name="next-steps"></a>后续步骤

- [使用 PowerShell 添加 Azure Active Directory B2B 协作用户](customize-invitation-api.md#powershell)
- [Azure AD B2B 来宾用户的属性](user-properties.md)
