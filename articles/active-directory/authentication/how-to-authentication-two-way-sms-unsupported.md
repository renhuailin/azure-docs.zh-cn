---
title: 不再支持双向短信 - Azure Active Directory
description: 说明如何为仍在使用双向短信的用户启用其他方法。
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/19/2021
ms.author: justinha
author: rhicock
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: c92095449132b790c80cf6396a3608f802d7e9a7
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114458497"
---
# <a name="two-way-sms-unsupported"></a>不支持双向短信

Azure AD 多重身份验证 (MFA) 服务器的双向短信服务于 2018 年开始弃用，在 2021 年 2 月 24 日之后不再受到支持，但 2021 年 8 月 2 日前收到支持扩展的组织除外。 管理员应为仍然使用双向短信的用户启用另一种方法。

电子邮件通知和 Azure 门户服务运行状况通知（门户 toast）已于 2020 年 12 月 8 日和 2021 年 1 月 28 日发送给受影响的管理员。 警报已发送到与订阅绑定的所有者、共同所有者、管理员和服务管理员 RBAC 角色。 如果已完成以下步骤，则无需执行任何操作。

## <a name="required-actions"></a>必需的操作

1. 为用户启用移动应用（如果尚未这样做）。 有关详细信息，请参阅[使用 MFA 服务器启用移动应用身份验证](howto-mfaserver-deploy-mobileapp.md)。
1. 通知最终用户访问你的 MFA 服务器[用户门户](howto-mfaserver-deploy-userportal.md)以激活移动应用。 推荐使用 [Microsoft Authenticator 应用](https://www.microsoft.com/en-us/account/authenticator)进行验证，因为它比双向短信更安全。 有关详细信息，请参阅 [It's Time to Hang Up on Phone Transports for Authentication](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/it-s-time-to-hang-up-on-phone-transports-for-authentication/ba-p/1751752)（是时候放弃电话传输进行身份验证了）。
1. 将用户设置从双向短信更改为移动应用作为默认方法。

## <a name="faq"></a>常见问题解答

### <a name="what-if-i-dont-change-the-default-method-from-two-way-sms-to-the-mobile-app"></a>如果不将默认方法从双向短信更改为移动应用，会发生什么？
在 2021 年 2 月24 日之后，双向短信会失败。 用户尝试登录并通过 MFA 时，会出现错误。

### <a name="how-do-i-change-the-user-settings-from-two-way-text-message-to-mobile-app"></a>如何将用户设置从双向短信更改为移动应用？

应按照以下步骤更改用户设置：

1. 在 MFA 服务器中，筛选使用双向短信的用户列表。
1. 选择“所有用户”。
1. 打开“编辑用户”对话框。
1. 将用户从“短信”更改为“移动应用”。

   ![最终用户的屏幕截图](media/how-to-authentication-two-way-sms-unsupported/end-users.png)

### <a name="do-my-users-need-to-take-any-action-if-yes-how"></a>我的用户需要执行什么操作吗？ 如果需要，如何操作？
是的。 最终用户需要访问特定的 MFA 服务器用户门户，以激活移动应用（如果尚未这样做）。 完成步骤 3 后，未访问用户门户来设置移动应用的用户都将开始无法登录，直到他们访问用户门户重新注册为止。

### <a name="what-if-my-users-cant-install-the-mobile-app-what-other-options-do-they-have"></a>如果用户无法安装移动应用，该怎么办？ 还能选择哪些选项？
双向短信或移动应用的替代方法是电话呼叫。 不过，推荐使用 Microsoft Authenticator 应用进行验证。

### <a name="will-one-way-sms-be-deprecated-as-well"></a>单向短信也会被弃用吗？
否，只有双向短信即将弃用。 对于 MFA 服务器来说，单向短信适用于一部分方案：

- AD FS 适配器
- IIS 身份验证（需要用户门户和配置）
- RADIUS（要求 RADIUS 客户端支持访问质询，并使用 PAP 协议）

单向短信的使用时间是有限制的，这使得移动应用成为更好的选择，因为它不需要验证码提示。
如果仍要在某些方案中使用单向短信，可以将它们保留为选中状态，但需要将“公司设置”部分的“常规”选项卡中的“用户默认短信”从“双向”改为“单向”    。 最后，如果使用默认为短信的目录同步，则需要将其更改为“单向”而不是“双向”。

### <a name="how-can-i-check-which-users-are-still-using-two-way-sms"></a>如何检查哪些用户仍在使用双向短信？
若要列出这些用户，请启动 MFA 服务器，选择“用户”部分，单击“筛选用户列表”，然后筛选出“短信双向”   。

### <a name="how-do-we-hide-two-way-sms-as-an-option-in-the-mfa-portal-to-prevent-users-from-selecting-it-in-the-future"></a>如何在 MFA 门户中隐藏双向短信选项，防止用户在将来选择它？
在 MFA 服务器用户门户中，单击“设置”，可以清除“短信”，使其不可用 。 如果使用 AD FS 进行用户注册，则该方法也适用于 AD FS 部分。

