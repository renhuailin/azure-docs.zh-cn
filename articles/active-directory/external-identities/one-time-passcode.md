---
title: 适用于 B2B 来宾用户的一次性密码身份验证 - Azure AD
description: 本文介绍如何在不使用 Microsoft 帐户的情况下，使用电子邮件一次性密码对 B2B 来宾用户进行身份验证。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 07/26/2021
ms.author: mimart
author: msmimart
manager: CelesteDG
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019, contperf-fy21q4-portal
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7b12d0611f6488a9ab5475ec6488328a764434f
ms.sourcegitcommit: bb1c13bdec18079aec868c3a5e8b33ef73200592
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2021
ms.locfileid: "114721072"
---
# <a name="email-one-time-passcode-authentication"></a>电子邮件一次性密码身份验证

当 B2B 协作用户无法通过 Azure AD、Microsoft 帐户 (MSA) 或社交标识提供者等其他方式进行身份验证时，电子邮件一次性密码功能可以对他们进行身份验证。 当 B2B 来宾用户试图履行你的邀请或登录你的共享资源时，他们可以请求向其电子邮件地址发送临时密码。 他们输入此密码后，可以继续登录。

通过在租户的外部标识设置下配置电子邮件一次性密码标识提供者，你可以随时在 Azure 门户中启用该功能。 你可以选择启用、禁用该功能或者等到 2021 年 10 月自动启用。

![电子邮件一次性密码功能概览图](media/one-time-passcode/email-otp.png)

> [!IMPORTANT]
> - 从 2021 年 10 月开始，将为所有现有租户启用电子邮件一次性密码功能，并在默认情况下为新租户启用此功能。 如果不希望此功能自动启用，则可以禁用它。 请参阅下面的[禁用电子邮件一次性密码](#disable-email-one-time-passcode)。
> - 电子邮件一次性密码设置已经在 Azure 门户中从“外部协作设置”移动到“所有标识提供者” 。

> [!NOTE]
> 一次性密码用户必须使用包含租户上下文的链接登录（例如，`https://myapps.microsoft.com/?tenantid=<tenant id>` 或 `https://portal.azure.com/<tenant id>`，或者经过验证的域 `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com`）。 也可以使用应用程序和资源的直接链接，只要这些链接包含租户上下文即可。 来宾用户目前无法使用不包含租户上下文的终结点登录。 例如，使用 `https://myapps.microsoft.com` 和 `https://portal.azure.com` 将会导致错误。

## <a name="user-experience-for-one-time-passcode-guest-users"></a>一次性密码来宾用户的用户体验

启用电子邮件一次性密码功能后，[满足特定条件](#when-does-a-guest-user-get-a-one-time-passcode)的新邀请的用户将使用一次性密码身份验证。 在启用一次性密码之前已兑换邀请的来宾用户将继续使用同一身份验证方法。

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
- 发出邀请的租户没有与社交标识提供者（如 [Google](google-federation.md)）或其他标识提供者建立联合身份验证。

在邀请时，没有迹象表明你邀请的用户将使用一次性密码身份验证。 但是当来宾用户登录时，如果不能使用其他身份验证方法，则可以使用一次性密码身份验证作为应变方法。


> [!NOTE]
> 在用户兑换一次性密码并稍后获得 MSA、Azure AD 帐户或其他联合帐户的情况下，他们将继续使用一次性密码进行身份验证。 如果要更新用户的身份验证方法，可以[重置其兑换状态](reset-redemption-status.md)。

### <a name="example"></a>示例

邀请来宾用户 teri@gmail.com 加入 Fabrikam，后者未设置 Google 联合身份验证。 Teri 没有 Microsoft 帐户。 他将收到用于身份验证的一次性密码。

## <a name="enable-email-one-time-passcode"></a>启用电子邮件一次性密码

1. 以 Azure AD 全局管理员身份登录到 [Azure 门户](https://portal.azure.com/)。

2. 在导航窗格中选择“Azure Active Directory”。

3. 选择“外部标识” > “所有标识提供者”。

4. 选择“电子邮件一次性密码”以打开“配置”窗格。

5. 在“来宾的电子邮件一次性密码”下，选择以下选项之一：

   - 如果你不想立即启用该功能，并希望等到 2021 年 10 月自动启用日期，从 2021年 10 月开始为来宾自动启用电子邮件一次性密码。
   - 立即为来宾启用电子邮件一次性密码可立即启用该功能。
   - 如果你看到“是/否”开关，选择“是”以立即启用该功能（如果该功能之前被禁用，则会出现此开关）。

   ![电子邮件一次性密码开关已启用](media/one-time-passcode/enable-email-otp-options.png)

5. 选择“保存”。

## <a name="disable-email-one-time-passcode"></a>禁用电子邮件一次性密码

从 2021 年 10 月开始，将为所有现有租户启用电子邮件一次性密码功能，并在默认情况下为新租户启用此功能。 届时，Microsoft 将不再支持兑换通过创建用于 B2B 协作方案的非托管（“病毒性”或“即时”）Azure AD 帐户和租户进行的邀请。 我们将启用电子邮件一次性密码功能，因为它为你的来宾用户提供了一种无缝的回退身份验证方法。 但如果你选择不使用此功能，则可以禁用此功能。

> [!NOTE]
>
> 如果电子邮件一次性密码功能已在租户中启用，而你将其关闭，则已兑换一次性密码的任何来宾用户将无法登录。 可以[重置其兑换状态](reset-redemption-status.md)，以便他们可以使用另一种身份验证方法再次登录。

### <a name="to-disable-the-email-one-time-passcode-feature"></a>若要禁用电子邮件一次性密码功能

1. 以 Azure AD 全局管理员身份登录到 [Azure 门户](https://portal.azure.com/)。

2. 在导航窗格中选择“Azure Active Directory”。

3. 选择“外部标识” > “所有标识提供者”。

4. 选择“电子邮件一次性密码”，然后在“来宾的电子邮件一次性密码”下，选择“禁用来宾的电子邮件一次性密码”（或者如果该功能以前已启用、已禁用或在预览期间已选择加入，则选择“否”）   。

   ![禁用的电子邮件一次性密码切换](media/one-time-passcode/disable-email-otp-options.png)

   > [!NOTE]
   > 电子邮件一次性密码设置已经在 Azure 门户中从“外部协作设置”移动到“所有标识提供者” 。
   > 如果看到切换而不是电子邮件一次性密码选项，这意味着你之前已启用、禁用或选择进行功能预览。 选择“否”以禁用该功能。

5. 选择“保存”。

## <a name="note-for-public-preview-customers"></a>公共预览版客户注意事项

如果之前已选择加入电子邮件一次性密码公共预览版，则在 2021 年 10 月不会为你自动启用功能，因此你的相关业务流程不会受影响。 此外，在 Azure 门户中的“来宾电子邮件一次性密码”属性下，将不会显示“从 2021 年 10 月开始自动为来宾启用电子邮件一次性密码”选项 ， 而会显示以下“是”或“否”的切换 ：

![已选择加入电子邮件一次性密码](media/one-time-passcode/enable-email-otp-opted-in.png)

但如果倾向于选择退出该功能，并允许在 2021 年 10 月自动启用，则可以使用 Microsoft Graph API [电子邮件身份验证方法配置类型](/graph/api/resources/emailauthenticationmethodconfiguration)，还原到默认设置。 还原到默认设置后，“来宾电子邮件一次性密码”下将提供以下选项：

![启用“已选择加入电子邮件一次性密码”](media/one-time-passcode/email-otp-options.png)

- 自 2021 年 10 月起，自动为来宾启用电子邮件一次性密码。 （默认）如果尚未为租户启用电子邮件一次性密码功能，则会从 2021 年 10 开始自动启用该功能。 如果届时要启用该功能，则无需执行进一步操作。 如果已启用或禁用该功能，则此选项将不可用。

- 立即为来宾启用电子邮件一次性密码。 为租户启用电子邮件一次性密码功能。

- 为来宾禁用电子邮件一次性密码。 为租户关闭电子邮件一次性密码功能，并阻止该功能在 2021 年 10 月启用。

## <a name="note-for-azure-us-government-customers"></a>Azure 美国政府客户注意事项

在 Azure 美国政府云中，默认禁用电子邮件一次性密码功能。 若不启用此功能，你的合作伙伴将无法登录。 与 Azure 公有云不同，Azure 美国政府云不支持自助服务 Azure Active Directory 帐户的兑换邀请。

 ![已禁用电子邮件一次性密码](media/one-time-passcode/enable-email-otp-disabled.png)

要在 Azure 美国政府云中启用电子邮件一次性密码功能：

1. 以 Azure AD 全局管理员身份登录到 [Azure 门户](https://portal.azure.com)。
2. 在导航窗格中选择“Azure Active Directory”。
3. 选择“组织关系” >“所有标识提供者” **** 。

   > [!NOTE]
   > - 如果未看到“组织关系”，请在顶部的搜索栏中搜索“外部标识”。

4. 选择“电子邮件一次性密码”，然后选择“是” 。
5. 选择“保存”。

有关当前限制的详细信息，请参阅 [Azure 美国政府云](current-limitations.md#azure-us-government-clouds)。
