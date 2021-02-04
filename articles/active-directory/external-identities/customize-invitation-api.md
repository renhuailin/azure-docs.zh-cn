---
title: B2B 协作 API 和自定义 - Azure Active Directory
description: Azure Active Directory B2B 协作可让业务合作伙伴有选择性地访问本方的企业应用程序，为跨公司合作关系提供支持。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 02/03/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8160859bb782ee8ffc4fef5ee03b61b6f54be1bb
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2021
ms.locfileid: "99548655"
---
# <a name="azure-active-directory-b2b-collaboration-api-and-customization"></a>Azure Active Directory B2B 协作 API 和自定义

已经有许多客户和我们说他们想要以最适合其组织的方式自定义邀请过程。 使用我们的 API，便可以实现该想法。 [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](/graph/api/resources/invitation)

## <a name="capabilities-of-the-invitation-api"></a>邀请 API 的功能

该 API 提供以下功能：

1. 使用 *任何* 电子邮件地址邀请外部用户。

    ```
    "invitedUserDisplayName": "Sam"
    "invitedUserEmailAddress": "gsamoogle@gmail.com"
    ```

2. 自定义希望用户在接受其邀请后登陆的位置。

    ```
    "inviteRedirectUrl": "https://myapps.microsoft.com/"
    ```

3. 选择通过我们将包含可自定义消息的标准邀请邮件

    ```
    "sendInvitationMessage": true
    ```

   发送给收件人

    ```
    "customizedMessageBody": "Hello Sam, let's collaborate!"
    ```

4. 并选择你想要让其了解你邀请了此协作者的抄送人员。

5. 或者完全自定义邀请，并通过选择不通过 Azure AD 发送通知加入工作流。

    ```
    "sendInvitationMessage": false
    ```

   在这种情况下，将通过可嵌入电子邮件模板的 API、即时消息或所选择的其他分发方法收到兑换 URL。

6. 最后，如果是管理员，可以选择以成员身份邀请用户。

    ```
    "invitedUserType": "Member"
    ```

## <a name="determine-if-a-user-was-already-invited-to-your-directory"></a>确定用户是否已被邀请到你的目录

可以使用邀请 API 确定资源租户中是否已存在用户。 当开发使用邀请 API 邀请用户的应用时，这会很有用。 如果用户已在资源目录中存在，则不会收到邀请，因此你可以首先运行查询来确定已有电子邮件是否作为 UPN 或其他登录属性存在。

1. 请确保用户的电子邮件域不是资源租户的已验证域的一部分。
2. 在资源租户中，使用以下 get 用户查询，其中 {0} 是你邀请的电子邮件地址：

   ```
   “userPrincipalName eq '{0}' or mail eq '{0}' or proxyAddresses/any(x:x eq 'SMTP:{0}') or signInNames/any(x:x eq '{0}') or otherMails/any(x:x eq '{0}')"
   ```

## <a name="authorization-model"></a>授权模型

可以在以下授权模式下运行 API：

### <a name="app--user-mode"></a>“应用 + 用户”模式

在此模式下，任何使用 API 的用户都需要有创建 B2B 邀请的权限。

### <a name="app-only-mode"></a>“仅应用”模式

在仅应用上下文中，应用需要 User.Invite.All 作用域才能使邀请成功。

有关详细信息，请参阅 https://developer.microsoft.com/graph/docs/authorization/permission_scopes


## <a name="powershell"></a>PowerShell

可使用 PowerShell 轻松地将外部用户添加并邀请到组织。 使用 cmdlet 创建邀请：

```powershell
New-AzureADMSInvitation
```

您可以使用下列选项：

* -InvitedUserDisplayName
* -InvitedUserEmailAddress
* -SendInvitationMessage
* -InvitedUserMessageInfo

### <a name="invitation-status"></a>邀请状态

向外部用户发送邀请后，可使用 Get-AzureADUser cmdlet 查看是否用户已接受该邀请  。 向外部用户发送邀请时，将填充 Get-AzureADUser 的以下属性：

* **UserState** 指示邀请是“PendingAcceptance”还是“Accepted”   。
* **UserStateChangedOn** 显示 UserState 属性最新更改的时间戳  。

可使用“筛选器”选项按 UserState 筛选结果   。 以下示例显示了如何筛选结果以仅显示具有待处理邀请的用户。 该示例还显示了 Format-List 选项，借助该选项可以指定要显示的属性  。 
 

```powershell
Get-AzureADUser -Filter "UserState eq 'PendingAcceptance'" | Format-List -Property DisplayName,UserPrincipalName,UserState,UserStateChangedOn
```

> [!NOTE]
> 确保拥有最新版本的 AzureAD PowerShell 模块或 AzureADPreview PowerShell 模块。 

## <a name="see-also"></a>另请参阅

在 [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](/graph/api/resources/invitation) 中查看邀请 API 参考。

## <a name="next-steps"></a>后续步骤

- [什么是 Azure AD B2B 协作？](what-is-b2b.md)
- [B2B 协作邀请电子邮件的元素](invitation-email-elements.md)
- [B2B 协作邀请兑换](redemption-experience.md)
- [在没有邀请的情况下添加 B2B 协作用户](add-user-without-invite.md)