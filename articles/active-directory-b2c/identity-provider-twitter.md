---
title: 设置使用 Twitter 帐户进行注册和登录
titleSuffix: Azure AD B2C
description: 使用 Azure Active Directory B2C，为 应用程序中的客户提供通过 Twitter 帐户注册与登录的功能。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/16/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: fb345a170f32cdf33ef069d4944a0d8d28aad631
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128571854"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 设置通过 Twitter 帐户注册与登录

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]
::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>先决条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-application"></a>创建应用程序

要使用户能够在 Azure AD B2C 中使用 Twitter 帐户登录，需要创建一个 Twitter 应用程序。 如果没有 Twitter 帐户，可以在 [https://twitter.com/signup](https://twitter.com/signup) 上注册。 此外，需要[申请一个开发人员帐户](https://developer.twitter.com/en/apply/user.html)。 有关详细信息，请参阅[申请访问权限](https://developer.twitter.com/en/apply-for-access)。

1. 使用 Twitter 帐户凭据登录到 [Twitter 开发人员门户](https://developer.twitter.com/portal/projects-and-apps)。
1. 在“Standalone Apps”（独立应用）下，选择“+Create App”（+ 创建应用）。 
1. 输入一个 **应用名称**，然后选择“Complete”（完成）。
1. 复制“App key”（应用密钥）和“API key secret”（API 密钥机密）的值。   稍后需要使用这两个值将 Twitter 配置为租户中的标识提供者。 
1. 在“Setup your App”（设置应用）下，选择“App settings”（应用设置）。 
1. 在“Authentication settings”（身份验证设置）下，选择“Edit”（编辑） 
    1. 选中“Enable 3-legged OAuth”（启用三重 OAuth）复选框。
    1. 选中“Request email address from users”（请求用户提供电子邮件地址）复选框。
    1. 对于“Callback URLs”（回调 URL），请输入 `https://your-tenant.b2clogin.com/your-tenant-name.onmicrosoft.com/your-user-flow-Id/oauth1/authresp`。  如果使用[自定义域](custom-domain.md)，请输入 `https://your-domain-name/your-tenant-name.onmicrosoft.com/your-user-flow-Id/oauth1/authresp`。 输入租户名称和用户流 ID 时，请使用全小写字母，即使它们在 Azure AD B2C 中是使用大写字母定义也不例外。 将：
        - `your-tenant-name` 替换为租户的名称。
        - `your-domain-name` 替换为自定义域。
        - `your-user-flow-Id` 替换为用户流的标识符。 例如，`b2c_1a_signup_signin_twitter`。 
    
    1. 对于“Website URL”（网站 URL），请输入 `https://your-tenant.b2clogin.com`。 将 `your-tenant` 替换为租户的名称。 例如，`https://contosob2c.b2clogin.com`。 如果使用[自定义域](custom-domain.md)，请输入 `https://your-domain-name`。
    1. 为“Terms of service”（服务条款）输入一个 URL，例如 `http://www.contoso.com/tos`。 策略 URL 是为了提供应用程序使用条款和条件而保留的页面。
    1. 为“Privacy policy”（隐私策略）输入一个 URL，例如 `http://www.contoso.com/privacy`。 策略 URL 是继续提供应用程序的隐私信息的页面。
    1. 选择“保存”。

::: zone pivot="b2c-user-flow"

## <a name="configure-twitter-as-an-identity-provider"></a>将 Twitter 配置为标识提供者

1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 选择 Azure 门户左上角的“所有服务”，搜索并选择 **Azure AD B2C**。
1. 依次选择“标识提供者”、“Twitter”。 
1. 输入“名称”。 例如 *Twitter*。
1. 对于“客户端 ID”，请输入前面创建的 Twitter 应用程序的“API 密钥”。
1. 对于“客户端机密”，请输入记下的“API 密钥机密”。
1. 选择“保存”。

## <a name="add-twitter-identity-provider-to-a-user-flow"></a>将 Twitter 标识提供者添加到用户流 

此时，Twitter 标识提供者已设置，但还不能在任何登录页中使用。 将 Twitter 标识提供者添加到用户流：

1. 在 Azure AD B2C 租户中，选择“用户流”  。
1. 单击要将 Twitter 标识提供者添加到的用户流。
1. 在“社交标识提供者”下，选择“Twitter”。
1. 选择“保存”。
1. 若要测试策略，请选择“运行用户流”。
1. 对于“应用程序”，请选择前面已注册的名为“testapp1”的 Web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 选择“运行用户流”按钮。
1. 在注册或登录页中，选择“Twitter”以使用 Twitter 帐户登录。

如果登录过程成功，则浏览器将重定向到 `https://jwt.ms`，其中显示了 Azure AD B2C 返回的令牌内容。

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>创建策略密钥

你需要存储前面在 Azure AD B2C 租户中记录的机密密钥。

1. 登录 [Azure 门户](https://portal.azure.com/)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C” 。
1. 在“概述”页上选择“标识体验框架”。
1. 选择“策略密钥”，然后选择“添加”。
1. 对于“选项”，请选择 `Manual`。
1. 输入策略密钥的 **名称**。 例如，`TwitterSecret`。 前缀 `B2C_1A_` 会自动添加到密钥名称。
1. 在“机密”中，输入前面记录的应用程序机密。
1. 在“密钥用法”处选择 `Encryption`。
1. 单击“创建”。

## <a name="configure-twitter-as-an-identity-provider"></a>将 Twitter 配置为标识提供者

要使用户能够使用 Twitter 帐户登录，需将该帐户定义为 Azure AD B2C 可通过终结点与之通信的声明提供程序。 该终结点将提供一组声明，Azure AD B2C 使用这些声明来验证特定的用户是否已完成身份验证。

可以通过在策略的扩展文件中将 Twitter 帐户添加到 **ClaimsProvider** 元素，将该帐户定义为声明提供程序。

1. 打开 *TrustFrameworkExtensions.xml*。
2. 找到 **ClaimsProviders** 元素。 如果该元素不存在，请在根元素下添加它。
3. 如下所示添加新的 **ClaimsProvider**：

    ```xml
    <ClaimsProvider>
      <Domain>twitter.com</Domain>
      <DisplayName>Twitter</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Twitter-OAuth1">
          <DisplayName>Twitter</DisplayName>
          <Protocol Name="OAuth1" />
          <Metadata>
            <Item Key="ProviderName">Twitter</Item>
            <Item Key="authorization_endpoint">https://api.twitter.com/oauth/authenticate</Item>
            <Item Key="access_token_endpoint">https://api.twitter.com/oauth/access_token</Item>
            <Item Key="request_token_endpoint">https://api.twitter.com/oauth/request_token</Item>
            <Item Key="ClaimsEndpoint">https://api.twitter.com/1.1/account/verify_credentials.json?include_email=true</Item>
            <Item Key="ClaimsResponseFormat">json</Item>
            <Item Key="client_id">Your Twitter application API key</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_TwitterSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. 请将 **client_id** 的值替换为前面记下的“API 密钥机密”。
5. 保存文件。

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAuth1" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>测试自定义策略

1. 选择信赖方策略，例如 `B2C_1A_signup_signin`。
1. 对于“应用程序”，请选择[前面注册](tutorial-register-applications.md)的 Web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 选择“立即运行”按钮。
1. 在注册或登录页中，选择“Twitter”以使用 Twitter 帐户登录。

如果登录过程成功，则浏览器将重定向到 `https://jwt.ms`，其中显示了 Azure AD B2C 返回的令牌内容。

::: zone-end
