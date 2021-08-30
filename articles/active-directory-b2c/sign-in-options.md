---
title: Azure AD B2C 支持的登录选项
titleSuffix: Azure AD B2C
description: 了解可以在 Azure Active Directory B2C 中使用的注册和登录选项，包括用户名和密码、电子邮件、手机，或者与社交标识提供者或外部标识提供者的联合。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/10/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: efb526f9a7418c0d7d2316ec91f355a7c4431404
ms.sourcegitcommit: e1874bb73cb669ce1e5203ec0a3777024c23a486
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/16/2021
ms.locfileid: "112204728"
---
# <a name="sign-in-options-in-azure-ad-b2c"></a>Azure AD B2C 中的登录选项

Azure AD B2C 为应用程序的用户提供多种注册和登录方法。 当用户注册你的应用程序时，你需要确定他们是将使用用户名、电子邮件地址还是电话号码在 Azure AD B2C 租户中创建本地帐户。 还可与社交标识提供者（如 Facebook、LinkedIn 和 Twitter）和标准标识协议（如 OAuth 2.0、OpenID Connect 等）相联合。

本文提供 Azure AD B2C 登录选项的概述。

## <a name="email-sign-in"></a>电子邮件登录

在默认情况下，本地帐户标识提供者设置中会启用电子邮件注册。 借助电子邮件选项，用户可以使用其电子邮件地址和密码进行登录和注册。

- 登录：系统会提示用户提供其电子邮件和密码。
- 注册：系统会提示用户输入电子邮件地址，此地址将在注册时进行验证（可选）并成为用户的登录 ID。 用户随后在注册页面上输入任何其他所请求的信息，例如显示名称、名字和姓氏。 然后，用户选择“继续”以创建帐户。
- 密码重置：用户输入并验证其电子邮件，然后可以重置密码

![电子邮件注册或登录体验](./media/sign-in-options/local-account-email-experience.png)

了解如何在本地帐户标识提供者中配置电子邮件登录。
## <a name="username-sign-in"></a>用户名登录

本地帐户标识提供者包含“用户名”选项，用户可以通过该选项使用用户名和密码来注册和登录你的应用程序。

- 登录：系统会提示用户提供其用户名和密码。
- 注册：系统会提示用户输入用户名，该用户名会成为其登录 ID。 系统还会提示用户输入在注册时进行验证的电子邮件地址。 该电子邮件地址会在密码重置流中使用。 用户在注册页面上输入任何其他所需的信息，例如“显示名称”、“名字”和“姓氏”。 用户随后选择“继续”以创建帐户。
- 密码重置：用户必须输入其用户名和关联的电子邮件地址。 电子邮件地址必须进行验证，之后用户可以重置密码。

![用户名注册或登录体验](./media/sign-in-options/local-account-username-experience.png)

## <a name="phone-sign-in"></a>手机登录

手机登录是本地帐户标识提供者设置中的一个无密码选项。 此方法可让用户使用电话号码作为主标识符来注册你的应用。 一次性密码将通过短信发送给用户。 用户在注册和登录过程中的体验如下：

- 登录：如果用户拥有一个以电话号码作为其标识符的现有帐户，则用户输入其电话号码并选择“登录”。 通过选择“继续”，用户可确认国家/地区和电话号码，随即会向其手机发送一次性验证码。 用户输入验证码，然后选择“继续”以登录。
- 注册：如果用户还没有应用程序的帐户，可通过单击“立即注册”链接来创建一个帐户。
    1. 此时将显示注册页面，用户可在其中选择其“国家/地区”，输入其电话号码，并选择“发送代码” 。 
    1. 一次性验证码会发送到用户的电话号码。 用户在注册页面上输入验证码，然后选择“验证代码” 。 （如果用户无法检索代码，则可以选择“发送新代码”）。
    1. 用户在注册页面上输入任何其他所需的信息，例如“显示名称”、“名字”和“姓氏”。 然后选择“继续”。
    1. 接下来，要求用户提供恢复电子邮件。 用户输入其电子邮件地址，然后选择“发送验证码”。 代码将发送到用户的电子邮件收件箱，用户可以检索该代码并在“验证码”中输入它。 然后，用户选择“验证代码”。
    1. 验证代码后，用户选择“创建”以创建帐户。

![手机注册或登录体验](./media/sign-in-options/local-account-phone-experience.png)

### <a name="pricing-for-phone-sign-in"></a>手机登录的定价

使用短信将一次性密码发送给用户。 根据移动网络运营商，可能需要为发送的每条消息付费。 有关定价信息，请参阅 [Azure Active Directory B2C 定价](https://azure.microsoft.com/pricing/details/active-directory-b2c/) 的“单独费用”部分。

> [!NOTE]
> 在为用户流配置电话注册时，默认情况下会禁用多重身份验证 (MFA)。 你可以在具有电话注册的用户流中启用 MFA，但是，由于使用电话号码作为主标识符，因此，对于第二个身份验证因素，只提供电子邮件一次性密码这一个选项。

### <a name="phone-recovery"></a>手机恢复

在为用户流启用电话注册和登录时，最好启用恢复电子邮件功能。 使用此功能，用户可以提供电子邮件地址，该地址可用于在没有电话的情况下恢复用户帐户。 此电子邮件地址只用于帐户恢复。 它不能用于登录。

- 在恢复电子邮件提示处于打开状态时，系统会提示首次注册的用户验证备份电子邮件。 对于以前未提供恢复电子邮件的用户，系统会在他们下次登录的过程中要求他们验证备份电子邮件。

- 在恢复电子邮件处于关闭状态时，注册或登录的用户不会看到恢复电子邮件提示。

以下屏幕截图演示了手机恢复流：

![手机恢复用户流](./media/sign-in-options/local-account-change-phone-flow.png)


## <a name="phone-or-email-sign-in"></a>手机或电子邮件登录

可以选择在本地帐户标识提供者设置中组合使用[手机登录](#phone-sign-in)和[电子邮件登录](#email-sign-in)。 在注册或登录页上，用户可以键入电话号码或电子邮件地址。 根据用户输入，Azure AD B2C 会将用户转到相应的流。

![手机或电子邮件注册或登录体验](./media/sign-in-options/local-account-phone-and-email-experience.png)

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure Active Directory B2C 中的用户流](user-flow-overview.md)提供的内置策略。
- [配置本地帐户标识提供者](identity-provider-local.md)。