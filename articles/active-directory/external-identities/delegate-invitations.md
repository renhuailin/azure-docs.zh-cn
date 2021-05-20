---
title: 启用 B2B 外部协作设置 - Azure AD
description: 了解如何启用 Active Directory B2B 外部协作，并管理谁可以邀请来宾用户。 使用“来宾邀请者”角色来委托邀请。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 747fa3005930414832878757664f4787157302d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "101645817"
---
# <a name="enable-b2b-external-collaboration-and-manage-who-can-invite-guests"></a>启用 B2B 外部协作并管理谁可以邀请来宾

本文介绍如何启用 Azure Active Directory (Azure AD) B2B 协作、指定谁可以邀请来宾，并确定来宾用户在你的 Azure AD 中所具有的权限。 

默认情况下，目录中的所有用户和来宾都可以邀请来宾，即使未为他们分配管理员角色。 使用外部协作设置可为组织中不同类型的用户启用或禁用来宾邀请功能。 还可以将邀请委托给个人用户，只需向他们分配有权邀请来宾的角色即可。

Azure AD 允许你限制外部来宾用户可以在你的 Azure AD 目录中所看到的内容。 默认情况下，来宾用户设置为受限权限级别，该权限级别阻止来宾用户枚举用户、组或其他目录资源，但允许他们查看非隐藏组的成员身份。 新的预览设置可让你进一步限制来宾访问权限，使来宾只能查看其自己的个人资料信息。 有关详细信息，请参阅[限制来宾访问权限（预览版）](../enterprise-users/users-restrict-guest-permissions.md)。

## <a name="configure-b2b-external-collaboration-settings"></a>配置 B2B 外部协作设置

使用 Azure AD B2B 协作，租户管理员可以设置以下邀请策略：

- 关闭邀请
- 只有管理员和具有“来宾邀请者”角色的用户可以邀请
- 管理员、“来宾邀请者”角色和成员可以邀请
- 所有用户（包括来宾）都可以邀请

默认情况下，所有用户（包括来宾）都可以邀请来宾用户。

### <a name="to-configure-external-collaboration-settings"></a>若要配置外部协作设置，请执行以下操作：

1. 以租户管理员身份登录到 [Azure 门户](https://portal.azure.com)。
2. 选择“Azure Active Directory” 。
3. 选择“外部标识” > “外部协作设置”。

4. 在“来宾用户访问限制(预览)”下，选择希望来宾用户具有的访问级别：
  
   - **来宾用户具有与成员相同的访问权限（包括首选项）** ：此选项使来宾与成员用户具有相同的对 Azure AD 资源和目录数据的访问权限。

   - **来宾用户对目录对象的属性和成员身份具有有限的访问权限**：（默认值）此设置阻止来宾执行某些目录任务，例如枚举用户、组或其他目录资源。 来宾可以看到所有非隐藏组的成员身份。

   - **来宾用户访问权限仅限于自己的目录对象的属性和成员身份（最严格）** ：使用此设置，来宾只能访问自己的配置文件。 来宾不能看到其他用户的个人资料、组或组成员身份。


5. 在“来宾邀请设置”下，选择适当的设置：

    ![来宾邀请设置](./media/delegate-invitations/guest-invite-settings.png)

   - **管理员和具有“来宾邀请者”角色的用户可以邀请**：若要允许充当“来宾邀请者”角色的管理员和用户邀请来宾，请将此策略设置为“是”。

   - **成员可以邀请**：若要允许目录的非管理员成员邀请来宾，请将此策略设置为“是”。

   - **来宾可以邀请**：若要允许来宾邀请其他来宾，请将此策略设置为“是”。

   > [!NOTE]
   > 如果“成员可以邀请”设为“否”，而“来宾邀请者角色中的管理员和用户可以邀请”设为“是”，则“来宾邀请者”角色中的用户仍将能够邀请来宾。

6. 在“针对来宾的电子邮件一次性密码”下，选择适当的设置（有关详细信息，请参阅[电子邮件一次性密码身份验证](one-time-passcode.md)）：

   - **将于 2021 年 10 月为来宾自动启用电子邮件一次性密码**。 （默认）如果尚未为租户启用电子邮件一次性密码功能，该功能将于 2021 年 10 月自动启用。 如果届时要启用该功能，则无需执行进一步操作。 如果已启用或禁用该功能，则此选项将不可用。

   - 立即为来宾启用电子邮件一次性密码。 为租户启用电子邮件一次性密码功能。

   - 为来宾禁用电子邮件一次性密码。 关闭租户的电子邮件一次性密码功能，并阻止该功能在 2021 年 10 月启用。

   > [!NOTE]
   > 如果已启用或禁用此功能，或者以前选择了预览，则会看到以下切换，而不会看到以上选项：
   >
   >![启用已选择加入的电子邮件一次性密码](media/delegate-invitations/enable-email-otp-opted-in.png)

7. 如果想要创建允许用户注册应用的用户流，可以在“通过用户流启用来宾自助注册”下，选择“是” 。 有关此设置的详细信息，请参阅[向应用添加自助注册用户流](self-service-sign-up-user-flow.md)。

    ![通过用户流设置进行自助注册](./media/delegate-invitations/self-service-sign-up-setting.png)

7. 在“协作限制”下，可以选择是允许还是拒绝指定的域的邀请，并在文本框中输入特定的域名。 若要阻止多个域，请分行输入每个域。 有关详细信息，请参阅[允许或阻止向特定组织中的 B2B 用户发送邀请](allow-deny-list.md)。

    ![协作限制设置](./media/delegate-invitations/collaboration-restrictions.png)
## <a name="assign-the-guest-inviter-role-to-a-user"></a>将“来宾邀请者”角色分配给用户

“来宾邀请者”角色可让个人用户邀请来宾，无需向他们分配全局管理员角色或其他管理员角色。 将“来宾邀请者”角色分配给个人。 然后，确保将“管理员和具有‘来宾邀请者’角色的用户可以邀请”设置为“是” 。

下面是一个示例，它展示了如何使用 PowerShell 将用户添加到“来宾邀请者”角色：

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="next-steps"></a>后续步骤

请参阅以下有关 Azure AD B2B 协作的文章：

- [什么是 Azure AD B2B 协作？](what-is-b2b.md)
- [在没有邀请的情况下添加 B2B 协作来宾用户](add-user-without-invite.md)
- [将 B2B 协作用户添加到角色](add-guest-to-role.md)

