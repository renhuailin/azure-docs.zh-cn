---
title: 不再支持双向短信-Azure Active Directory
description: 说明如何为仍使用双向短信的用户启用其他方法。
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/02/2021
ms.author: justinha
author: rhicock
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: d25ed1e46823ec6d820addf3944c96c97fcabcb8
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101689022"
---
# <a name="two-way-sms-unsupported"></a>不支持双向短信

2018中最初弃用了用于 Azure AD 多重身份验证的双向短信 (MFA) 服务器，在2021年2月24日后不再受支持。 管理员应为仍使用双向短信的用户启用其他方法。

2020年12月8日和年1月 2021 28 日向受影响的管理员发送了电子邮件通知和 Azure 门户服务运行状况通知 (门户 toast) 。 警报将发送到与订阅关联的所有者、共同所有者、管理员和服务管理员 RBAC 角色。 如果你已经完成了以下步骤，则无需执行任何操作。

## <a name="required-actions"></a>必需的操作

1. 为用户启用移动应用（如果尚未这样做）。 有关详细信息，请参阅 [使用 MFA 服务器启用移动应用身份验证](howto-mfaserver-deploy-mobileapp.md)。
1. 通知最终用户访问你的 MFA 服务器 [用户门户](howto-mfaserver-deploy-userportal.md) 以激活移动应用。 [Microsoft Authenticator 应用](https://www.microsoft.com/en-us/account/authenticator)是推荐的验证选项，因为它比双向短信更安全。 有关详细信息，请参阅 [如何在电话传输上挂起身份验证](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/it-s-time-to-hang-up-on-phone-transports-for-authentication/ba-p/1751752)。
1. 将用户设置从双向文本消息更改为默认方法。

## <a name="faq"></a>常见问题解答

### <a name="what-if-i-dont-change-the-default-method-from-two-way-sms-to-the-mobile-app"></a>如果不将双向短信中的默认方法更改为移动应用，该怎么办？
2021年2月24日之后，双向短信失败。 用户尝试登录并传递 MFA 时，将会看到错误。

### <a name="how-do-i-change-the-user-settings-from-two-way-text-message-to-mobile-app"></a>如何实现将用户设置从双向文本消息更改为移动应用？

你应按照以下步骤更改用户设置：

1. 在 "MFA 服务器" 中，筛选 "双向文本" 消息的 "用户" 列表。
1. 选择 "所有用户"。
1. 打开 "编辑用户" 对话框。
1. 将用户从短信改为移动应用。

   ![最终用户的屏幕截图](media/how-to-authentication-two-way-sms-unsupported/end-users.png)

### <a name="do-my-users-need-to-take-any-action-if-yes-how"></a>我的用户是否需要采取任何措施？ 如果是，如何操作？
是的。 最终用户需要访问特定的 MFA 服务器用户门户，以激活移动应用（如果尚未这样做）。 完成步骤3后，未访问用户门户以设置移动应用程序的任何用户将无法登录，直到他们访问用户门户重新注册。

### <a name="what-if-my-users-cant-install-the-mobile-app-what-other-options-do-they-have"></a>如果用户无法安装移动应用，该怎么办？ 它们有哪些其他选项？
双向短信或移动应用的替代方法是电话呼叫。 不过，Microsoft Authenticator 应用是推荐的验证方法。

### <a name="will-one-way-sms-be-deprecated-as-well"></a>是否也不推荐使用单向短信？
不会，只会弃用双向短信。 对于 MFA 服务器，单向短信适用于一小部分方案：

- AD FS 适配器
- IIS 身份验证 (需要用户门户和配置) 
- RADIUS (要求 RADIUS 客户端支持访问质询，并使用 PAP 协议) 

使用单向短信时有一些限制，因为它不需要验证码提示，所以使移动应用成为更好的备选方案。
如果仍要在某些情况下使用单向短信，则可以将它们保留为选中状态，但更改 "**公司设置**" 部分、"**常规**" 选项卡 "**用户默认将文本消息** 改 **为单向，** 而不 **是双向"。** 最后，如果使用默认为 SMS 的目录同步，则需要将其更改为 One-Way 而不是双向的。

### <a name="how-can-i-check-which-users-are-still-using-two-way-sms"></a>如何检查哪些用户仍在使用双向短信？
若要列出这些用户，请启动 " **MFA 服务器**"，选择 " **用户** " 部分，单击 " **筛选用户列表**"，并按双向筛选 **文本消息**。

### <a name="how-do-we-hide-two-way-sms-as-an-option-in-the-mfa-portal-to-prevent-users-from-selecting-it-in-the-future"></a>如何将双向短信隐藏为 MFA 门户中的一个选项，以防止用户在将来选择它？
在 MFA 服务器用户门户中，单击 " **设置**"，可以清除 **文本消息** ，使其不可用。 如果使用的是用户注册 AD FS，则 **AD FS** 部分也是如此。

