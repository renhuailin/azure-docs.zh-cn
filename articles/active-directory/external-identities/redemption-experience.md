---
title: B2B 协作中的邀请兑换 - Azure AD
description: 介绍最终用户的 Azure AD B2B 协作邀请兑换体验，包括对隐私条款的同意。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 07/13/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 766228eb291776c0ba4162f78238d6336d9194ae
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121746148"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Azure Active Directory B2B 协作邀请兑换

本文介绍来宾用户可以访问资源以及所遇到的同意过程的方式。 如果向来宾发送邀请电子邮件，邀请会附有一个链接，来宾可以兑换该链接来访问应用或门户。 邀请电子邮件只是来宾可以访问资源的其中一种方式。 或者，可以将来宾添加到目录并为其提供指向要共享的门户或应用的直接链接。 无论他们使用哪种方法，都会指导来宾完成首次同意过程。 此过程可确保来宾同意隐私条款，并接受已设置的任何[使用条款](../conditional-access/terms-of-use.md)。

将来宾用户添加到目录时，来宾用户帐户的同意状态（可在 PowerShell 中查看）最初设置为“PendingAcceptance”。 在来宾接受邀请并同意隐私策略和使用条款之前，此设置一直保留。 此后，同意状态会更改为“已接受”，且不再向来宾显示同意页。

   > [!IMPORTANT]
   >
   > - 从 2021 年 7 月 12 日开始，如果 Azure AD B2B 客户设置了新的 Google 集成，将其用于自定义应用程序或业务线应用程序的自助注册，则在身份验证转移到系统 Web 视图之前，无法使用 Google 标识进行身份验证。 [了解详细信息](google-federation.md#deprecation-of-web-view-sign-in-support)。
   > - 从 2021 年 9 月 30 日开始，Google 将[弃用嵌入式 Web 视图登录支持](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html)。 如果你的应用使用嵌入式 Web 视图对用户进行身份验证，而你将 Google 联合身份验证与 [Azure AD B2C](../../active-directory-b2c/identity-provider-google.md) 或 Azure AD B2B 配合使用来进行[外部用户邀请](google-federation.md)或[自助注册](identity-providers.md)，则 Google Gmail 用户将无法进行身份验证。 [了解详细信息](google-federation.md#deprecation-of-web-view-sign-in-support)。
   > - 从 2021 年 10 月起，Microsoft 将不再支持兑换通过创建用于 B2B 协作方案的非托管 Azure AD 帐户和租户进行的邀请。 在准备期间，我们鼓励客户选择加入现已正式发布的[电子邮件一次性密码身份验证](one-time-passcode.md)。

## <a name="redemption-and-sign-in-through-a-common-endpoint"></a>通过常用终结点进行的兑换和登录

来宾用户现可通过 `https://myapps.microsoft.com` 等常用终结点 (URL) 登录到多租户或 Microsoft 第一方应用。 以前，常用 URL 会将来宾用户重定向到其主租户（而不是资源租户）进行身份验证，因此需要特定于租户的链接（例如 `https://myapps.microsoft.com/?tenantid=<tenant id>`）。 现在，来宾用户可以访问应用程序的常用 URL，选择“登录选项”，然后选择“登录组织” 。 然后，用户需要键入组织的名称。

![常用终结点登录](media/redemption-experience/common-endpoint-flow-small.png)

然后，用户重定向到租户终结点，用户可在其中使用其电子邮件地址登录或选择已配置的标识提供者。

## <a name="redemption-through-a-direct-link"></a>通过直接链接兑换

作为邀请电子邮件或应用程序的常用 URL 的替代方法，你可以为来宾提供指向你的应用或门户的直接链接。 首先需要通过 [Azure 门户](./b2b-quickstart-add-guest-users-portal.md)或 [PowerShell](./b2b-quickstart-invite-powershell.md) 将来宾用户添加到目录。 然后，可以使用[将应用程序部署到用户的可自定义方式](../manage-apps/end-user-experiences.md)（包括直接登录链接）。 当来宾使用直接链接而不是邀请电子邮件时，仍将指导他们完成首次同意体验。

> [!NOTE]
> 直接链接特定于租户。 换句话说，它包含租户 ID 或已验证的域，以便可以在共享应用所在的租户中对来宾进行身份验证。 下面是包含租户上下文的直接链接的一些示例：
 > - 应用访问面板：`https://myapps.microsoft.com/?tenantid=<tenant id>`
 > - 已验证域的应用访问面板：`https://myapps.microsoft.com/<;verified domain>`
 > - Azure 门户：`https://portal.azure.com/<tenant id>`
 > - 单个应用：了解如何使用[直接登录链接](../manage-apps/end-user-experiences.md#direct-sign-on-links)

在某些情况下，建议通过直接链接发送邀请电子邮件。 如果这些特殊情况对贵组织而言很重要，我们建议使用仍会发送邀请电子邮件的方法来邀请用户：
 - 用户在联合组织中没有 Azure AD 帐户、MSA 或电子邮件帐户。 除非你使用一次性密码功能，否则来宾需要兑换邀请电子邮件才能指导完成创建 MSA 的步骤。
 - 有时，由于与联系人对象（例如，Outlook 联系人对象）存在冲突，受邀用户对象可能会没有电子邮件地址。 在这种情况下，用户必须单击邀请电子邮件中的兑换 URL。
 - 用户可使用受邀电子邮件地址的别名登录。 （别名指与电子邮件帐户关联的其他电子邮件地址。）在这种情况下，用户必须单击邀请电子邮件中的兑换 URL。

## <a name="redemption-through-the-invitation-email"></a>通过邀请电子邮件兑换

[使用 Azure 门户](./b2b-quickstart-add-guest-users-portal.md)将来宾用户添加到目录时，会在该过程中向来宾发送邀请电子邮件。 你还可以选择在[使用 PowerShell](./b2b-quickstart-invite-powershell.md) 将来宾用户添加到目录时发送邀请电子邮件。 下面是来宾在兑换电子邮件中的链接时的体验说明。

1. 来宾收到从 Microsoft 邀请发送的[邀请电子邮件](./invitation-email-elements.md)。
2. 来宾选择电子邮件中的“接受邀请”。
3. 来宾将使用其自己的凭据登录到目录。 如果来宾没有可联合到目录的帐户，且未启用[电子邮件一次性密码 (OTP)](./one-time-passcode.md) 功能；系统将提示来宾创建个人 [MSA](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create) 或 [Azure AD 自助服务帐户](../enterprise-users/directory-self-service-signup.md)。 有关详细信息，请参阅[邀请兑换流](#invitation-redemption-flow)。
4. 将指导来宾完成下面所述的[同意体验](#consent-experience-for-the-guest)。

## <a name="redemption-limitation-with-conflicting-contact-object"></a>冲突的 Contact 对象的兑换限制
有时，受邀的外部来宾用户的电子邮件可能会与现有的 [Contact 对象](/graph/api/resources/contact?view=graph-rest-1.0&preserve-view=true)冲突，从而导致创建来宾用户时没有 proxyAddress。 这是一个已知限制，可防止来宾用户： 
- 使用 [SAML/WS-Fed IdP](./direct-federation.md)、[Microsoft 帐户](./microsoft-account.md)、[Google 联合身份验证](./google-federation.md)或[电子邮件一次性密码](./one-time-passcode.md)帐户通过直接链接兑换邀请。 
- 使用 [SAML/WS-Fed IdP](./direct-federation.md) 和[电子邮件一次性密码](./one-time-passcode.md)帐户通过邀请电子邮件兑换链接兑换邀请。
- 使用 [SAML/WS-Fed IdP](./direct-federation.md) 和 [Google 联合身份验证](./google-federation.md)帐户兑换后重新登录应用程序。

若要取消阻止因 [Contact 对象](/graph/api/resources/contact?view=graph-rest-1.0&preserve-view=true)冲突而无法兑换邀请的用户，请执行以下步骤：
1. 删除冲突的 Contact 对象。
2. 删除 Azure 门户中的来宾用户（用户的“已接受邀请”属性应处于挂起状态）。
3. 重新邀请来宾用户。
4. 等待用户兑换邀请。
5. 将用户的联系人电子邮件重新添加到 Exchange 以及他们应属于的任何 DL 中。

## <a name="invitation-redemption-flow"></a>邀请兑换流

当用户单击[邀请电子邮件](invitation-email-elements.md)中的“接受邀请”链接时，Azure AD 会根据兑换流自动兑换邀请，如下所示：

![显示兑换流关系图的屏幕截图](media/redemption-experience/invitation-redemption-flow.png)

如果用户的用户主体名称 (UPN) 与现有 Azure AD 和个人 MSA 帐户相匹配，系统会提示用户选择兑换所用的帐户。*

1. Azure AD 执行基于用户的发现，以确定用户是否存在于[现有 Azure AD 租户](./what-is-b2b.md#easily-invite-guest-users-from-the-azure-ad-portal)中。

2. 如果管理员已启用 [SAML/WS-Fed IdP 联合身份验证](./direct-federation.md)，Azure AD 会检查用户的域后缀是否与已配置的 SAML/WS-Fed 标识提供程序的域相匹配，并将用户重定向到预配置的标识提供程序。

3. 如果管理员已启用 [Google 联合](./google-federation.md)，Azure AD 会检查用户的域后缀是 gmail.com 还是 googlemail.com，并将用户重定向到 Google。

4. 兑换过程将检查用户是否具有适用于即时 (JIT) 兑换（而不是适用于邀请电子邮件链接兑换）的现有个人 [Microsoft 帐户 (MSA)](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create)。 如果用户已有现有 MSA，则用户将使用其现有的 MSA 登录。

5. 标识用户的主目录后，用户将发送到相应的标识提供程序进行登录。  

6. 如果步骤 1 到 4 找不到受邀用户的主目录，Azure AD 会确定邀请租户是否已为来宾启用[电子邮件一次性密码 (OTP)](./one-time-passcode.md) 功能。

7. 如果 [已为来宾启用电子邮件一次性密码](./one-time-passcode.md#when-does-a-guest-user-get-a-one-time-passcode)，密码将通过邀请的电子邮件发送到用户。 用户将在 Azure AD 登录页面中检索并输入此密码。

8. 如果已为来宾禁用电子邮件一次性密码，Azure AD 会检查域后缀，以确定它是否属于使用者帐户。 如果是这样，系统会提示用户创建个人 [Microsoft 帐户](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create)。 否则，系统会提示用户创建 [Azure AD 自助服务帐户](../enterprise-users/directory-self-service-signup.md)。

9. Azure AD 尝试通过验证对电子邮件的访问权限来创建 [Azure AD 自助服务帐户](../enterprise-users/directory-self-service-signup.md)。 通过向电子邮件发送代码，让用户检索该代码并将其提交到 Azure AD 来验证帐户。 但是，如果受邀用户的租户是联合的，或者如果在受邀用户的租户中将 AllowEmailVerifiedUsers 字段设置为 false，则用户无法完成兑换，并且流会导致错误。 有关详细信息，请参阅 [Azure Active Directory B2B 协作疑难解答](./troubleshoot.md#the-user-that-i-invited-is-receiving-an-error-during-redemption)。

10. 系统会提示用户创建个人 [Microsoft 帐户 (MSA)](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create)。

11. 对正确的标识提供程序进行身份验证后，用户将重定向到 Azure AD 以完成[同意体验](#consent-experience-for-the-guest)。  

对于实时 (JIT) 兑换，通过租户应用程序链接兑换时，步骤 8 到步骤 10 不可用。 如果用户到达步骤 6 且未启用电子邮件一次性密码功能，则用户会收到一条错误消息，并且无法兑换邀请。 若要防止此错误，管理员应[启用电子邮件一次性密码](./one-time-passcode.md#when-does-a-guest-user-get-a-one-time-passcode)或确保用户单击邀请链接。

## <a name="consent-experience-for-the-guest"></a>来宾的同意体验

来宾在首次登录以访问合作伙伴组织中的资源后，会指导他们完成以下页面。 

1. 来宾查看描述邀请组织的隐私声明的“查看权限”页。 用户必须“接受”根据邀请组织的隐私策略使用其信息才能继续。

   ![显示“查看权限”页的屏幕截图](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > 有关如何以租户管理员身份链接到组织隐私声明的信息，请参阅[如何：在 Azure Active Directory 中添加组织的隐私信息](../fundamentals/active-directory-properties-area.md)。

2. 如果已配置使用条款，来宾将打开并查看使用条款，然后选择“接受”。 

   ![显示新使用条款的屏幕截图](media/redemption-experience/terms-of-use-accept.png) 

   可以在“外部标识” > “使用条款”中配置[使用条款](../conditional-access/terms-of-use.md)。

3. 除非另行指定，否则来宾将重定向到应用访问面板，其中列出了来宾可以访问的应用程序。

   ![显示应用访问面板的屏幕截图](media/redemption-experience/myapps.png) 

> [!NOTE]
> 仅在用户登录后才会出现同意体验，而不会在此之前出现。 在某些情况下，不会向用户显示同意体验，例如：
> - 用户已接受同意体验
> - 管理员[向应用程序授予租户范围的管理员同意](../manage-apps/grant-admin-consent.md)

在目录中，来宾的“已接受邀请”值更改为“是” 。 如果创建了 MSA，则来宾的“源”将显示“Microsoft 帐户”。 有关来宾用户帐户属性的详细信息，请参阅 [Azure AD B2B 协作用户的属性](user-properties.md)。 如果在访问应用程序时遇到需要管理员同意的错误，请参阅[如何向应用授予管理员同意](../develop/v2-admin-consent.md)。

## <a name="next-steps"></a>后续步骤

- [什么是 Azure AD B2B 协作？](what-is-b2b.md)
- [在 Azure 门户中添加 Azure Active Directory B2B 协作用户](add-users-administrator.md)
- [信息工作者如何将 B2B 协作用户添加到 Azure Active Directory？](add-users-information-worker.md)
- [使用 PowerShell 添加 Azure Active Directory B2B 协作用户](customize-invitation-api.md#powershell)
- [以来宾用户身份离开组织](leave-the-organization.md)