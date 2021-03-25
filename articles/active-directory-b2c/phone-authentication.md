---
title: 通过自定义策略进行手机注册和登录
titleSuffix: Azure AD B2C
description: 通过 Azure Active Directory B2C 中的自定义策略，通过短信将一次性密码 (OTP) 发送到应用程序用户的手机。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2600ea3488c643bcf215b058425de42cd439dcff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "98660261"
---
# <a name="set-up-phone-sign-up-and-sign-in-with-custom-policies-in-azure-ad-b2c"></a>在 Azure AD B2C 中通过自定义策略设置手机注册和登录

通过 Azure Active Directory B2C (Azure AD B2C) 中的手机注册和登录，用户可以使用以短信形式发送到其手机的一次性密码 (OTP) 来注册并登录到你的应用程序。 一次性密码有助于最大程度地降低用户忘记或泄露密码的风险。

请按照本文中的步骤操作，使用自定义策略使客户能够使用发送到他们手机的一次性密码来注册和登录到应用程序。

## <a name="pricing"></a>定价

通过使用 SMS 短信将一次性密码发送给用户，并且每发送一条消息，你都需要付费。 有关定价信息，请参阅 [Azure Active Directory B2C 定价](https://azure.microsoft.com/pricing/details/active-directory-b2c/) 的“单独费用”部分。

## <a name="user-experience-for-phone-sign-up-and-sign-in"></a>手机注册和登录的用户体验

通过手机注册和登录，用户可以使用电话号码作为其主要标识符来注册应用。 最终用户在注册和登录期间的体验如下所述。

> [!NOTE]
> 我们强烈建议你在注册和登录体验中包括同意信息，类似于以下示例文本。 本示例文本仅供参考。 请参考 [ CTIA 网站](https://www.ctia.org/programs)上的《短代码监视手册》，并咨询你自己的合法或合规性专家，获取有关最终文本和功能配置的指南，以满足合规性需求：
>
> 通过提供你的电话号码，即表示同意接收通过短信发送的一次性密码，帮助你登录到&lt;插入：你的应用程序名称&gt;*。* 标准消息和数据费率可能适用。
>
> &lt;插入：指向隐私声明的链接&gt;<br/>&lt;插入：指向服务条款的链接&gt;

若要添加你自己的同意信息，请自定义以下示例。 将其包含在带有显示控件（[手机注册和登录入门包][starter-pack-phone]中的 Phone_Email_Base.xml 文件）的自断言页面所使用的 ContentDefinition 的 `LocalizedResources` 中：

```xml
<LocalizedResources Id="phoneSignUp.en">        
    <LocalizedStrings>
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_msg_intro">By providing your phone number, you consent to receiving a one-time passcode sent by text message to help you sign into {insert your application name}. Standard message and data rates may apply.</LocalizedString>          
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_link_1_text">Privacy Statement</LocalizedString>                
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_link_1_url">{insert your privacy statement URL}</LocalizedString>          
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_link_2_text">Terms and Conditions</LocalizedString>             
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_link_2_url">{insert your terms and conditions URL}</LocalizedString>          
    <LocalizedString ElementType="UxElement" StringId="initial_intro">Please verify your country code and phone number</LocalizedString>        
    </LocalizedStrings>      
</LocalizedResources>
   ```

### <a name="phone-sign-up-experience"></a>手机注册体验

如果用户还没有应用程序的帐户，可通过选择“立即注册”链接来创建一个帐户。 此时将显示注册页面，用户可在其中选择其“国家/地区”，输入其电话号码，并选择“发送代码” 。

![用户开始手机注册](media/phone-authentication/phone-signup-start.png)

一次性验证码会发送到用户的电话号码。 用户在注册页面上输入验证码，然后选择“验证代码” 。 （如果用户无法检索代码，则可以选择“发送新代码”。）

![用户在手机注册期间验证代码](media/phone-authentication/phone-signup-verify-code.png)

用户输入注册页面上要求的任何其他信息。 例如，“显示名称”、“名字”和“姓氏”（国家/地区和电话号码保持已填充）。 如果用户想使用其他电话号码，则可以选择“更改号码”重新开始注册。 完成后，用户选择“继续”。

![用户提供其他信息](media/phone-authentication/phone-signup-additional-info.png)

接下来，要求用户提供恢复电子邮件。 用户输入其电子邮件地址，然后选择“发送验证码”。 代码将发送到用户的电子邮件收件箱，用户可以检索该代码并在“验证码”中输入它。 然后，用户选择“验证代码”。 

验证代码后，用户选择“创建”以创建帐户。 或者，如果用户想使用其他电子邮件地址，则可以选择“更改电子邮件”。

![用户创建帐户](media/phone-authentication/email-verification.png)

### <a name="phone-sign-in-experience"></a>手机登录体验

如果用户拥有一个以电话号码作为其标识符的现有帐户，则用户输入其电话号码并选择“继续”。 通过选择“继续”，用户可确认国家/地区和电话号码，随即会向其手机发送一次性验证码。 用户输入验证码，然后选择“继续”以登录。

![手机登录用户体验](media/phone-authentication/phone-signin-screens.png)

## <a name="deleting-a-user-account"></a>删除用户帐户

在某些情况下，你可能需要从 Azure AD B2C 目录中删除用户和关联的数据。 有关如何通过 Azure 门户删除用户帐户的详细信息，请参阅[这些说明](/microsoft-365/compliance/gdpr-dsr-azure#step-5-delete)。 

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]



## <a name="prerequisites"></a>先决条件

设置 OTP 之前，需要准备以下资源。

* [Azure AD B2C 租户](tutorial-create-tenant.md)
* 在租户中[注册的 Web 应用程序](tutorial-register-applications.md)
* 已上传到租户的[自定义策略](custom-policy-get-started.md)

## <a name="get-the-phone-sign-up--sign-in-starter-pack"></a>获取手机注册和登录入门包

首先更新手机注册和登录自定义策略文件以与 Azure AD B2C 租户一起使用。

1. 在入门包存储库的本地克隆中找到[手机注册和登录自定义策略文件][starter-pack-phone]，或直接下载它们。 XML 策略文件位于以下目录中：

    `active-directory-b2c-custom-policy-starterpack/scenarios/`**`phone-number-passwordless`**

1. 在每个文件中，将字符串 `yourtenant` 替换为 Azure AD B2C 租户的名称。 例如，如果 B2C 租户的名称为 contosob2c，则 `yourtenant.onmicrosoft.com` 的所有实例都将变为 `contosob2c.onmicrosoft.com`。

1. 完成 [Azure Active Directory B2C 中的自定义策略入门](custom-policy-get-started.md)的[将应用程序 ID 添加到自定义策略](custom-policy-get-started.md#add-application-ids-to-the-custom-policy)部分中的步骤。 在本例中，请使用你在完成先决条件时注册的两个应用程序的应用程序（客户端）ID（IdentityExperienceFramework 和 ProxyIdentityExperienceFramework）更新 `/phone-number-passwordless/`**`Phone_Email_Base.xml`**  。

## <a name="upload-the-policy-files"></a>上传策略文件

1. 登录到 [Azure 门户](https://portal.azure.com)，并导航到你的 Azure AD B2C 租户。
1. 在“策略”下，选择“Identity Experience Framework”。 
1. 选择“上传自定义策略”。
1. 按以下顺序上传策略文件：
    1. Phone_Email_Base.xml
    1. SignUpOrSignInWithPhone.xml
    1. SignUpOrSignInWithPhoneOrEmail.xml
    1. ProfileEditPhoneOnly.xml
    1. ProfileEditPhoneEmail.xml
    1. ChangePhoneNumber.xml
    1. PasswordResetEmail.xml

上载每个文件时，Azure 会添加前缀 `B2C_1A_`。

## <a name="test-the-custom-policy"></a>测试自定义策略

1. 在“自定义策略”下，选择“B2C_1A_SignUpOrSignInWithPhone” 。
1. 在“选择应用程序”下，选择完成先决条件后注册的 webapp1 应用程序。
1. 对于“选择回复 URL”，请选择 `https://jwt.ms`。
1. 选择“立即运行”，然后使用电子邮件地址或电话号码进行注册。
1. 再次选择“立即运行”并使用相同的帐户登录，以确认配置正确。

## <a name="get-user-account-by-phone-number"></a>通过电话号码获取用户帐户

使用电话号码注册但未提供恢复电子邮件地址的用户会被记录在你的 Azure AD B2C 目录中，其电话号码将作为他们的登录名。 若要更改电话号码，你的支持人员或支持团队必须首先找到其帐户，然后更新其电话号码。

可以使用 [Microsoft Graph](microsoft-graph-operations.md) 通过用户的电话号码（登录名）找到用户：

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+{phone number}' and c/issuer eq '{tenant name}.onmicrosoft.com')
```

例如：

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+450334567890' and c/issuer eq 'contosob2c.onmicrosoft.com')
```

## <a name="next-steps"></a>后续步骤

可以在 GitHub 上找到手机注册和登录自定义策略入门包（和其他入门包）：[Azure-Samples / active-directory-b2c-custom-policy-starterpack / scenarios / phone-number-passwordless][starter-pack-phone] 入门包策略文件使用多重身份验证技术配置文件和电话号码声明转换：
* [定义 Azure AD 多重身份验证技术配置文件](multi-factor-auth-technical-profile.md)
* [定义电话号码声明转换](phone-number-claims-transformations.md)

<!-- LINKS - External -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
[starter-pack-phone]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless
