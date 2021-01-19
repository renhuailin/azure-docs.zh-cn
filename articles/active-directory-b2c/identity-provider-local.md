---
title: 设置 Azure AD B2C 本地帐户标识提供者
titleSuffix: Azure AD B2C
description: 定义使用的标识类型可用于注册或登录 (Azure Active Directory B2C 租户中的电子邮件、用户名和电话号码) 。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/19/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: e6b520a32755379fce5326e25b90f135b0d67755
ms.sourcegitcommit: 65cef6e5d7c2827cf1194451c8f26a3458bc310a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2021
ms.locfileid: "98574298"
---
# <a name="set-up-the-local-account-identity-provider"></a>设置本地帐户标识提供者

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure AD B2C 提供了不同的方式来验证用户的身份。 用户可以使用用户名和密码登录到本地帐户，电话验证 (也称为 "密码不太验证") 或社交标识提供者。 默认情况下，在本地帐户标识提供者设置中启用电子邮件注册。 

本文介绍用户如何在此 Azure AD B2C 租户的本地创建帐户。 对于社交或企业标识，其中用户的标识由 Facebook 和 Google 等联合标识提供者进行管理，请参阅 [添加标识提供者](add-identity-provider.md)。

## <a name="email-sign-in"></a>电子邮件登录

通过电子邮件选项，用户可以通过其电子邮件地址和密码进行登录：

- **登录** 时，系统将提示用户提供其电子邮件和密码。
- **注册** 时，系统将提示用户提供电子邮件地址，该地址将在注册 (可选) 时进行验证，并成为其登录 ID。 然后，用户输入注册页上请求的任何其他信息，例如显示名称、名字和姓氏。 然后选择 "继续" 以创建帐户。
- **密码重置**，用户必须输入并验证其电子邮件，之后用户可以重置密码

![电子邮件注册或登录体验](./media/identity-provider-local/local-account-email-experience.png)

## <a name="username-sign-in"></a>用户名登录

使用 user 选项，用户可以使用用户名和密码登录/签入：

- **登录**：系统会提示用户提供其用户名和密码。
- **注册**：系统会提示用户输入用户名，该用户名将成为其登录 ID。 系统还会提示用户提供电子邮件地址，该地址将在注册时进行验证。 电子邮件地址将在密码重置流期间使用。 用户输入注册页上请求的任何其他信息，例如显示名称、名字和姓氏。 然后，用户选择 "继续" 以创建帐户。
- **密码重置**：用户必须输入其用户名和关联的电子邮件地址。 电子邮件地址必须经过验证，之后用户可以重置密码。

![用户名注册或登录体验](./media/identity-provider-local/local-account-username-experience.png)

## <a name="phone-sign-in-preview"></a> (预览版的手机登录) 

无密码 authentication 是一种身份验证类型，用户无需使用其密码进行登录。 通过电话注册和登录，用户可以使用电话号码作为主要登录标识符来注册应用。 用户在注册和登录过程中将具有以下体验：

- **登录**：如果用户有一个使用电话号码作为标识符的现有帐户，则用户将输入其电话号码，然后选择 *"登录*"。 它们通过选择 " *继续*" 确认国家/地区和电话号码，并将一次性验证代码发送到其手机。 用户输入验证码，并选择 " *继续* " 以登录。
- **注册**：如果用户还没有应用程序的帐户，则可以通过单击 " *立即注册* " 链接创建一个帐户。 
    1. 此时将显示注册页面，用户可在其中选择 *国家/地区*，输入电话号码，然后选择 " *发送代码*"。 
    1. 一次性验证码会发送到用户的电话号码。 用户在注册页上输入 *验证代码* ，然后选择 " *验证代码*"。  (如果用户无法检索代码，则他们可以选择 " *发送新代码* ") 。 
    1. 用户输入注册页上请求的任何其他信息，例如显示名称、名字和姓氏。 然后选择“继续”。 
    1. 接下来，要求用户提供一个 **恢复电子邮件**。 用户输入其电子邮件地址，然后选择 " *发送验证码*"。 代码将发送到用户的电子邮件收件箱，用户可以在 "验证码" 框中检索并输入该收件箱。 然后，用户选择 "验证代码"。
    1. 验证代码后，用户可以选择 " *创建* " 来创建帐户。 

![电话注册或登录体验](./media/identity-provider-local/local-account-phone-experience.png)

### <a name="pricing"></a>定价

使用短信将一次性密码发送到用户。 根据你的移动网络操作员，你可能需要为发送的每条消息付费。 有关定价信息，请参阅 [Azure Active Directory B2C 定价](https://azure.microsoft.com/pricing/details/active-directory-b2c/)的 **单独费用** 部分。

> [!NOTE]
> 多重身份验证 (MFA) 默认情况下在使用电话注册配置用户流时禁用。 你可以通过电话注册在用户流中启用 MFA，但由于使用电话号码作为主标识符，因此第二个身份验证因素只提供电子邮件一次性密码。

### <a name="phone-recovery"></a>电话恢复

当你为用户流启用电话注册和登录时，最好启用恢复电子邮件功能。 使用此功能，用户可以提供一个电子邮件地址，该地址可用于在没有手机的情况下恢复帐户。 此电子邮件地址仅用于帐户恢复。 它不能用于登录。

- 当恢复电子邮件提示符处于 **打开** 状态时，系统将提示用户首次注册，以验证备份电子邮件。 在下一次登录期间，系统要求验证备份电子邮件之前尚未提供恢复电子邮件的用户。

- 当恢复电子邮件 **关闭** 时，用户注册或登录的用户不会显示 "恢复电子邮件" 提示。
 
以下屏幕截图演示了手机恢复流程：

![手机恢复用户流](./media/identity-provider-local/local-account-change-phone-flow.png)


## <a name="phone-or-email-sign-in-preview"></a>电话或电子邮件登录 (预览) 

你可以选择合并 [手机登录](#phone-sign-in-preview)和 [电子邮件登录](#email-sign-in)。 在注册或登录页上，用户可以键入电话号码或电子邮件地址。 根据用户输入，Azure AD B2C 会将用户转到相应的流。 

![电话或电子邮件注册或登录体验](./media/identity-provider-local/local-account-phone-and-email-experience.png)

::: zone pivot="b2c-user-flow"

## <a name="configure-local-account-identity-provider-settings"></a>配置本地帐户标识提供程序设置

可以通过启用或禁用提供程序 (电子邮件、用户名或电话号码) 来配置可在用户流中使用的本地标识提供程序。  可以在租户级别启用多个本地标识提供者。

一次只能将一个用户流配置为使用其中一个本地帐户标识提供者。 如果在租户级别启用了多个用户，则每个用户流都可以具有不同的本地帐户标识提供程序集。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在顶部菜单中选择 " **目录 + 订阅** " 筛选器并选择包含 Azure AD 租户的目录，确保使用的是包含 Azure AD B2C 租户的目录。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C” 。
1. 在“管理”下，选择“标识提供者” 。
1. 在标识提供者列表中，选择“本地帐户”。
1. 在 " **配置本地 IDP** " 页中，选择了至少一个允许使用的标识类型，使用者可以在 Azure AD B2C 租户中创建其本地帐户。
1. 选择“保存”。 

## <a name="configure-your-user-flow"></a>配置用户流

1. 在 Azure 门户的左侧菜单中，选择 " **Azure AD B2C**"。
1. 在“策略”下，选择“用户流(策略)”。 
1. 选择要为其配置注册和登录体验的用户流。
1. 选择 **标识提供者**
1. 在 " **本地帐户**" 下，选择下列项之一： " **电子邮件注册**"、"  **用户 ID 注册**"、" **电话注册**"、" **电话/电子邮件注册**" 或 " **无**"。

### <a name="enable-the-recovery-email-prompt"></a>启用恢复电子邮件提示

如果选择 " **电话注册**"、" **电话/电子邮件注册** " 选项，请启用 "恢复电子邮件" 提示。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在门户工具栏中选择“目录 + 订阅”图标，然后选择包含 Azure AD B2C 租户的目录。
1. 在 Azure 门户中，搜索并选择“Azure AD B2C”  。
1. 在 Azure AD B2C 的 " **策略**" 下，选择 " **用户流**"。
1. 从列表中选择用户流。
1. 在“设置”下，选择“属性”   。
1. 在 " **为电话号码注册启用恢复电子邮件提示" 和 "登录 (预览")** 中，选择：
   - 如果为，则 **在** 注册和登录过程中显示恢复电子邮件提示。
   - **关闭** 以隐藏恢复电子邮件提示。
1. 选择“保存”。 

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="get-the-starter-pack"></a>获取新手包

自定义策略是上传到 Azure AD B2C 租户的一组 XML 文件，用于定义用户旅程。 我们提供了包含若干预先构建的策略的初学者包。 下载相关的初学者包： 

- [电子邮件登录](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccounts)
- [用户名登录](https://github.com/azure-ad-b2c/samples/tree/master/policies/username-signup-or-signin)
- [电话登录](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless)。 选择 [SignUpOrSignInWithPhone.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/scenarios/phone-number-passwordless/SignUpOrSignInWithPhone.xml) 信赖方策略 "。 
- [电话或电子邮件登录](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless)。 选择 [SignUpOrSignInWithPhone.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/scenarios/phone-number-passwordless/SignUpOrSignInWithPhoneOrEmail.xml) 信赖方策略 "。

下载 starter pack 后。

1. 在每个文件中，将字符串替换 `yourtenant` 为 Azure AD B2C 租户的名称。 例如，如果 B2C 租户的名称为 *contosob2c*，则的所有实例都将 `yourtenant.onmicrosoft.com` 变为 `contosob2c.onmicrosoft.com` 。

1. 完成[Azure Active Directory B2C 中的自定义策略入门](custom-policy-get-started.md)中的[将应用程序 id 添加到自定义策略](custom-policy-get-started.md#add-application-ids-to-the-custom-policy)部分中的步骤。 例如，在 `/phone-number-passwordless/` **`Phone_Email_Base.xml`** 完成必备组件 *IdentityExperienceFramework* 和 *ProxyIdentityExperienceFramework* 时，通过 **应用程序 (客户端) id** 注册两个应用程序。
1. 上传策略文件

::: zone-end

## <a name="next-steps"></a>后续步骤

- [添加外部标识提供者](tutorial-add-identity-providers.md)
- [创建用户流](tutorial-create-user-flows.md)
