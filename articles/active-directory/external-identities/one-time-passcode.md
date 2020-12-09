---
title: 适用于 B2B 来宾用户的一次性密码身份验证 - Azure AD
description: 本文介绍如何在不使用 Microsoft 帐户的情况下，使用电子邮件一次性密码对 B2B 来宾用户进行身份验证。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 11/30/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9bc7d94cff46b54b81615cf064ed63927b3ec60
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2020
ms.locfileid: "96929287"
---
# <a name="email-one-time-passcode-authentication-preview"></a>电子邮件一次性密码身份验证 (预览) 

本文介绍如何为 B2B 来宾用户启用电子邮件一次性密码身份验证。 电子邮件一次性密码功能在无法通过其他方式（例如 Azure AD、Microsoft 帐户 (MSA) 或 Google federation）进行身份验证时对 B2B 来宾用户进行身份验证。 使用一次性密码身份验证时，无需创建 Microsoft 帐户。 当来宾用户兑换邀请或访问共享资源时，他们可以请求临时代码，该代码会发送到他们的电子邮件地址。 他们输入此代码后，可以继续登录。

![电子邮件一次性密码概述示意图](media/one-time-passcode/email-otp.png)

> [!IMPORTANT]
> **从2021年3月开始**，将为所有现有租户启用电子邮件一次性密码功能，并在默认情况下为新租户启用此功能。 如果不想允许此功能自动打开，可以禁用它。 请参阅下面 [的禁用电子邮件一次性密码](#disable-email-one-time-passcode) 。

> [!NOTE]
> 一次性密码用户必须使用包含租户上下文的链接登录（例如，`https://myapps.microsoft.com/?tenantid=<tenant id>` 或 `https://portal.azure.com/<tenant id>`，或者经过验证的域 `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com`）。 也可以使用应用程序和资源的直接链接，只要这些链接包含租户上下文即可。 来宾用户目前无法使用不包含租户上下文的终结点登录。 例如，使用 `https://myapps.microsoft.com` ， `https://portal.azure.com` 将导致错误。

## <a name="user-experience-for-one-time-passcode-guest-users"></a>一次性密码来宾用户的用户体验

启用电子邮件一次性密码功能后， [满足特定条件](#when-does-a-guest-user-get-a-one-time-passcode) 的新邀请用户将使用一次性密码身份验证。 如果来宾用户在电子邮件一次性密码启用之前兑换邀请，将继续使用相同的身份验证方法。

借助一次性密码身份验证，来宾用户可以通过单击直接链接或使用邀请电子邮件来兑换邀请。 在任一情况下，浏览器中的消息都指示代码将发送到来宾用户的电子邮件地址。 来宾用户选择“发送代码”：

   ![显示“发送代码”按钮的屏幕截图](media/one-time-passcode/otp-send-code.png)

密码将发送到用户的电子邮件地址。 用户从电子邮件检索密码，并在浏览器窗口中输入该密码：

   ![显示“输入代码”页的屏幕截图](media/one-time-passcode/otp-enter-code.png)

来宾用户现在已通过身份验证，他们可以查看共享资源或继续登录。

> [!NOTE]
> 一次性密码在 30 分钟内有效。 30 分钟后，该特定的一次性密码不再有效，用户必须申请新密码。 用户会话在 24 小时后过期。 届时，来宾用户在访问资源时会收到新密码。 会话过期可提高安全性，尤其是当来宾用户离开公司或不再需要访问权限时。

## <a name="when-does-a-guest-user-get-a-one-time-passcode"></a>来宾用户何时收到一次性密码？

当来宾用户兑换邀请或使用指向已与其共享的资源的链接时，将收到一次性密码，前提是符合以下条件：

- 他们没有 Azure AD 帐户
- 他们没有 Microsoft 帐户
- 邀请的租户未针对 @gmail.com 和 @googlemail.com 用户设置 Google 联合身份验证

在邀请时，没有迹象表明你邀请的用户将使用一次性密码身份验证。 但是当来宾用户登录时，如果不能使用其他身份验证方法，则可以使用一次性密码身份验证作为应变方法。

查看用户的详细信息中的 " **源** " 属性，可以查看 guest 用户是否通过一次性密码进行身份验证。 在 Azure 门户中，请参阅 " **Azure Active Directory**  >  **用户**"，然后选择用户以打开详细信息页。

![显示具有 OTP 源值的一次性密码用户的屏幕截图](media/one-time-passcode/guest-user-properties.png)

> [!NOTE]
> 在用户兑换一次性密码并稍后获得 MSA、Azure AD 帐户或其他联合帐户的情况下，他们将继续使用一次性密码进行身份验证。 如果要更新用户的身份验证方法，可以删除其来宾用户帐户并重新邀请用户。

### <a name="example"></a>示例

邀请来宾用户 teri@gmail.com 加入 Fabrikam，后者未设置 Google 联合身份验证。 Teri 没有 Microsoft 帐户。 他将收到用于身份验证的一次性密码。

## <a name="disable-email-one-time-passcode"></a>禁用电子邮件一次性密码

从2021年3月开始，将为所有现有租户启用电子邮件一次性密码功能，并在默认情况下为新租户启用此功能。 此时，Microsoft 将不再支持通过创建非托管 ( "病毒" 或 "实时" ) Azure AD 用于 B2B 协作方案的帐户和租户来兑换邀请。 我们正在启用电子邮件一次性密码功能，因为它为你的来宾用户提供了无缝的回退身份验证方法。 但是，如果您选择不使用此功能，则可以禁用此功能。

> [!NOTE]
>
> 如果已在租户中启用电子邮件一次性密码功能，并将其关闭，则已兑换一次性密码的任何来宾用户将无法登录。 你可以删除来宾用户并将其 reinvite，以便他们可以使用另一种身份验证方法再次登录。

### <a name="to-disable-the-email-one-time-passcode-feature"></a>禁用电子邮件一次性密码功能

1. 以 Azure AD 全局管理员身份登录到 [Azure 门户](https://portal.azure.com/)。

2. 在导航窗格中选择“Azure Active Directory”。

3. 依次选择“外部标识” > “外部协作设置”。

4. 在 " **来宾的电子邮件一次性密码**" 下，选择 " **禁用来宾的电子邮件一次性密码**"。

    ![电子邮件一次性密码设置](media/one-time-passcode/otp-admin-settings.png)

   > [!NOTE]
   > 如果看到以下切换，而不是上面显示的选项，这意味着你之前已启用、禁用或选择加入功能预览。 选择 " **否** " 以禁用该功能。
   >
   >![启用选择的电子邮件一次性密码](media/delegate-invitations/enable-email-otp-opted-in.png)

5. 选择“保存”。

## <a name="note-for-public-preview-customers"></a>公共预览版客户注意事项

如果你之前已选择使用电子邮件一次性密码公共预览版，则自动功能支持的3月2021日期不适用于你，因此你的相关业务流程将不会受到影响。 此外，在 Azure 门户中，在 " **来宾的电子邮件一次性密码** " 属性下，你不会看到在 **3 月2021中自动为来宾启用电子邮件一次性密码** 的选项。 相反，你将看到以下 **"是" 或 "** **否** " 切换：

![启用选择的电子邮件一次性密码](media/delegate-invitations/enable-email-otp-opted-in.png)

但是，如果想要选择退出功能，并允许在2021年3月自动启用此功能，则可以使用 Microsoft Graph API [电子邮件身份验证方法配置资源类型](https://aka.ms/exid-graphemailauth)，恢复到默认设置。 恢复到默认设置后，以下选项将在 " **来宾的电子邮件一次性密码**" 下提供：

- **2021 年3月自动为来宾启用电子邮件一次性密码**。  (默认值) 如果尚未为你的租户启用电子邮件一次性密码功能，则会在3月2021自动启用。 如果要在此时启用此功能，则无需执行任何其他操作。 如果已启用或禁用该功能，则此选项将不可用。

- **立即为来宾启用电子邮件一次性密码**。 为租户启用 "一次性密码" 电子邮件功能。

- **对来宾禁用电子邮件一次性密码**。 关闭租户的电子邮件一次性密码功能，并在2021年3月阻止该功能开启。
