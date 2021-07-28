---
title: 重置来宾用户的兑换状态 - Azure AD
description: 了解如何在 Azure AD 外部标识中重置 Azure Active Directory B2B 来宾用户的邀请兑换状态。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 04/06/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5bfce7ef2621cbe3bbbfdd95bf9a75e427c8cbd
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531876"
---
# <a name="reset-redemption-status-for-a-guest-user-preview"></a>重置来宾用户的兑换状态（预览）

在来宾用户兑换你的 B2B 协作邀请后，有时候，你可能需要更新其登录信息，例如在以下情况下：

- 用户需要使用其他电子邮件和标识提供者进行登录
- 用户在主租户中的帐户已被删除或重新创建
- 用户已转移到其他公司，但他们仍然需要对你的资源具有相同的访问权限
- 用户的职责已转移给其他用户

以前，若要管理这些方案，你必须从你的目录中手动删除来宾用户的帐户，然后重新邀请用户。 现在，你可以使用 PowerShell 或 Microsoft Graph 邀请 API 来重置用户的兑换状态并重新邀请用户，并保留用户的对象 ID、组成员身份和应用分配。 当用户兑换新邀请时，用户的 UPN 不会更改，但用户的登录名将更改为新电子邮件地址。 然后，用户可以使用新电子邮件地址或你添加到用户对象的 `otherMails` 属性的电子邮件地址进行登录。

## <a name="reset-the-email-address-used-for-sign-in"></a>重置用于登录的电子邮件地址

用户想要使用其他电子邮件登录时：

1. 请确保将新的电子邮件地址添加到用户对象的 `mail` 或 `otherMails` 属性。 
2.  将 `InvitedUserEmailAddress` 属性中的电子邮件地址替换为新的电子邮件地址。
3. 使用以下方法之一重置用户的兑换状态。

> [!NOTE]
>对于公共预览版，我们有以下两点建议：
>- 如果你要将用户的电子邮件地址重置为一个新地址，建议设置 `mail` 属性。 这样，除使用邀请中的兑换链接之外，用户还可以登录到你的目录来兑换邀请。
>- 如果你要重置 B2B 来宾用户的状态，请确保在用户上下文下执行此操作。 目前不支持仅应用调用。
>
## <a name="use-powershell-to-reset-redemption-status"></a>使用 PowerShell 重置兑换状态

安装最新的 AzureADPreview PowerShell 模块，创建一个新邀请并将 `InvitedUserEmailAddress` 设置为新的电子邮件地址，将 `ResetRedemption` 设置为 `true`。

```powershell  
Uninstall-Module AzureADPreview 
Install-Module AzureADPreview 
Connect-AzureAD 
$ADGraphUser = Get-AzureADUser -objectID "UPN of User to Reset"  
$msGraphUser = New-Object Microsoft.Open.MSGraph.Model.User -ArgumentList $ADGraphUser.ObjectId 
New-AzureADMSInvitation -InvitedUserEmailAddress <<external email>> -SendInvitationMessage $True -InviteRedirectUrl "http://myapps.microsoft.com" -InvitedUser $msGraphUser -ResetRedemption $True 
```

## <a name="use-microsoft-graph-api-to-reset-redemption-status"></a>使用 Microsoft Graph API 重置兑换状态

使用 [Microsoft Graph 邀请 API](/graph/api/resources/invitation?view=graph-rest-beta&preserve-view=true)，将 `resetRedemption` 属性设置为 `true` 并在 `invitedUserEmailAddress` 属性中指定新的电子邮件地址。

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
