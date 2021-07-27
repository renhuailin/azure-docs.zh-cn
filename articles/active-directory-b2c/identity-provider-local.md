---
title: 设置 Azure AD B2C 本地帐户标识提供者
titleSuffix: Azure AD B2C
description: 定义用户可用于在 Azure Active Directory B2C 租户中注册或登录的标识类型（电子邮件、用户名和电话号码）。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/22/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 48d6f6fc983de3f9a98b81011db1a8843f678939
ms.sourcegitcommit: 19dcad80aa7df4d288d40dc28cb0a5157b401ac4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2021
ms.locfileid: "107896254"
---
# <a name="set-up-the-local-account-identity-provider"></a>设置本地帐户标识提供者

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure AD B2C 提供了用户可以用于对用户进行身份验证的各种方式。 用户可以使用用户名和密码、电话验证（也称为无密码身份验证）或社交标识提供者登录本地帐户。 在默认情况下，本地帐户标识提供者设置中会启用电子邮件注册。 

本文介绍用户如何对此 Azure AD B2C 租户创建本地帐户。 对于社交或企业标识（其中用户的标识由 Facebook 和 Google 等联合标识提供者进行管理），请参阅[添加标识提供者](add-identity-provider.md)。

## <a name="email-sign-in"></a>电子邮件登录

借助电子邮件选项，用户可以使用其电子邮件地址和密码进行登录/注册：

- 登录，系统会提示用户提供其电子邮件和密码。
- 注册，系统会提示用户输入电子邮件地址，此地址将在注册时进行验证（可选）并成为登录 ID。 用户随后在注册页面上输入任何其他所需的信息，例如“显示名称”、“名字”和“姓氏”。 随后选择“继续”以创建帐户。
- 密码重置，用户必须输入并验证其电子邮件，之后用户可以重置密码

![电子邮件注册或登录体验](./media/identity-provider-local/local-account-email-experience.png)

## <a name="username-sign-in"></a>用户名登录

借助用户选项，用户可以使用用户名和密码进行登录/注册：

- 登录：系统会提示用户提供其用户名和密码。
- 注册：系统会提示用户输入用户名，该用户名会成为其登录 ID。 系统还会提示用户输入在注册时进行验证的电子邮件地址。 该电子邮件地址会在密码重置流中使用。 用户在注册页面上输入任何其他所需的信息，例如“显示名称”、“名字”和“姓氏”。 用户随后选择“继续”以创建帐户。
- 密码重置：用户必须输入其用户名和关联电子邮件地址。 电子邮件地址必须进行验证，之后用户可以重置密码。

![用户名注册或登录体验](./media/identity-provider-local/local-account-username-experience.png)

## <a name="phone-sign-in"></a>手机登录

无密码身份验证是用户无需使用其密码进行登录的身份验证类型。 借助手机注册和登录，用户可以使用电话号码作为其主要登录标识符来注册应用。 用户在注册和登录过程中会具有以下体验：

- 登录：如果用户拥有一个以电话号码作为其标识符的现有帐户，则用户输入其电话号码并选择“登录”。 通过选择“继续”，用户可确认国家/地区和电话号码，随即会向其手机发送一次性验证码。 用户输入验证码，然后选择“继续”以登录。
- 注册：如果用户还没有应用程序的帐户，可通过单击“立即注册”链接来创建一个帐户。 
    1. 此时将显示注册页面，用户可在其中选择其“国家/地区”，输入其电话号码，并选择“发送代码” 。 
    1. 一次性验证码会发送到用户的电话号码。 用户在注册页面上输入验证码，然后选择“验证代码” 。 （如果用户无法检索代码，则可以选择“发送新代码”）。 
    1. 用户在注册页面上输入任何其他所需的信息，例如“显示名称”、“名字”和“姓氏”。 然后选择“继续”。
    1. 接下来，要求用户提供恢复电子邮件。 用户输入其电子邮件地址，然后选择“发送验证码”。 代码将发送到用户的电子邮件收件箱，用户可以检索该代码并在“验证码”中输入它。 然后，用户选择“验证代码”。
    1. 验证代码后，用户选择“创建”以创建帐户。 

![手机注册或登录体验](./media/identity-provider-local/local-account-phone-experience.png)

### <a name="pricing"></a>定价

使用短信将一次性密码发送给用户。 根据移动网络运营商，可能需要为发送的每条消息付费。 有关定价信息，请参阅 [Azure Active Directory B2C 定价](https://azure.microsoft.com/pricing/details/active-directory-b2c/) 的“单独费用”部分。

> [!NOTE]
> 在为用户流配置电话注册时，默认情况下会禁用多重身份验证 (MFA)。 你可以在具有电话注册的用户流中启用 MFA，但是，由于使用电话号码作为主标识符，因此，对于第二个身份验证因素，只提供电子邮件一次性密码这一个选项。

### <a name="phone-recovery"></a>手机恢复

在为用户流启用电话注册和登录时，最好启用恢复电子邮件功能。 使用此功能，用户可以提供电子邮件地址，该地址可用于在没有电话的情况下恢复用户帐户。 此电子邮件地址只用于帐户恢复。 它不能用于登录。

- 在恢复电子邮件提示处于打开状态时，系统会提示首次注册的用户验证备份电子邮件。 对于以前未提供恢复电子邮件的用户，系统会在他们下次登录的过程中要求他们验证备份电子邮件。

- 在恢复电子邮件处于关闭状态时，注册或登录的用户不会看到恢复电子邮件提示。
 
以下屏幕截图演示了手机恢复流：

![手机恢复用户流](./media/identity-provider-local/local-account-change-phone-flow.png)


## <a name="phone-or-email-sign-in"></a>手机或电子邮件登录

可以选择将[手机登录](#phone-sign-in)与[电子邮件登录](#email-sign-in)合并。 在注册或登录页上，用户可以键入电话号码或电子邮件地址。 根据用户输入，Azure AD B2C 会将用户转到相应的流。 

![手机或电子邮件注册或登录体验](./media/identity-provider-local/local-account-phone-and-email-experience.png)

::: zone pivot="b2c-user-flow"

## <a name="configure-local-account-identity-provider-settings"></a>配置本地帐户标识提供者设置

可以通过启用或禁用提供者（电子邮件、用户名或电话号码）来配置可在用户流中使用的本地标识提供者。  可以在租户级别启用多个本地标识提供者。

用户流在任何时候都只能配置为使用一个本地帐户标识提供者。 如果在租户级别启用了多个用户，则每个用户流都可以设置不同的本地帐户标识提供者。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 请确保使用包含 Azure AD B2C 租户的目录，方法是：选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含 Azure AD 租户的目录。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C” 。
1. 在“管理”下，选择“标识提供者” 。
1. 在标识提供者列表中，选择“本地帐户”。
1. 在“配置本地 IDP”页中，已选择至少一个允许的标识类型（使用者可用于在你的 Azure AD B2C 租户中创建其本地帐户）。
1. 选择“保存”。

## <a name="configure-your-user-flow"></a>配置用户流

1. 在 Azure 门户的左侧菜单中，选择“Azure AD B2C”。
1. 在“策略”下，选择“用户流(策略)”。 
1. 选择要为其配置注册和登录体验的用户流。
1. 选择“标识提供者”
1. 在“本地帐户”下，选择以下项之一：“电子邮件注册”、“用户 ID 注册”、“手机注册”、“手机/电子邮件注册”或“无”     。

### <a name="enable-the-recovery-email-prompt"></a>启用恢复电子邮件提示

如果选择“手机注册”、“手机/电子邮件注册”选项，请启用恢复电子邮件提示 。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在门户工具栏中选择“目录 + 订阅”图标，然后选择包含 Azure AD B2C 租户的目录。
1. 在 Azure 门户中，搜索并选择“Azure AD B2C”。
1. 在 Azure AD B2C 中的“策略”下，选择“用户流” 。
1. 从列表中选择用户。
1. 在“设置”下，选择“属性”   。
1. 在“启用电话号码注册和登录的恢复电子邮件地址提示(预览)”旁边，选择：
   - “打开”，以在注册和登录过程中都显示恢复电子邮件提示。
   - “关闭”，以隐藏恢复电子邮件提示。
1. 选择“保存”。

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="get-the-starter-pack"></a>获取新手包

自定义策略是上传到 Azure AD B2C 租户的一组 XML 文件，用于定义用户旅程。 我们提供了包含多个预建策略的新手包。 下载相关新手包： 

- [电子邮件登录](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccounts)
- [用户名登录](https://github.com/azure-ad-b2c/samples/tree/master/policies/username-signup-or-signin)
- [手机登录](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless)。 选择 [SignUpOrSignInWithPhone.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/scenarios/phone-number-passwordless/SignUpOrSignInWithPhone.xml) 信赖方策略。 
- [手机或电子邮件登录](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless)。 选择 [SignUpOrSignInWithPhone.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/scenarios/phone-number-passwordless/SignUpOrSignInWithPhoneOrEmail.xml) 信赖方策略。

下载新手包后。

1. 在每个文件中，将字符串 `yourtenant` 替换为 Azure AD B2C 租户的名称。 例如，如果 B2C 租户的名称为 contosob2c，则 `yourtenant.onmicrosoft.com` 的所有实例都将变为 `contosob2c.onmicrosoft.com`。

1. 完成 [Azure Active Directory B2C 中的自定义策略入门](tutorial-create-user-flows.md?pivots=b2c-custom-policy)的[将应用程序 ID 添加到自定义策略](tutorial-create-user-flows.md?pivots=b2c-custom-policy#add-application-ids-to-the-custom-policy)部分中的步骤。 例如，请使用你在完成先决条件时注册的两个应用程序的应用程序（客户端）ID（IdentityExperienceFramework 和 ProxyIdentityExperienceFramework）更新 `/phone-number-passwordless/``Phone_Email_Base.xml`  。
1. 上传策略文件

::: zone-end

## <a name="next-steps"></a>后续步骤

- [添加外部标识提供者](add-identity-provider.md)
- [创建用户流](tutorial-create-user-flows.md)
