---
title: 设置使用 Google 帐户进行注册和登录
titleSuffix: Azure AD B2C
description: 使用 Azure Active Directory B2C 提供在应用程序中通过 Google 帐户注册与登录到客户的设置。
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
ms.openlocfilehash: fb8dfdac7d9cc2d3d1529056651c15f673f0b768
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128574548"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 设置通过 Google 帐户注册与登录

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

> [!IMPORTANT]
> 从 2021 年 9 月 30 日开始，Google 将[弃用 Web 视图登录支持](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html)。 如果应用使用嵌入式 web 视图对用户进行身份验证，而你将 Google 联合身份验证与 Azure AD B2C 配合使用，则 Google Gmail 用户将无法进行身份验证。 [了解详细信息](../active-directory/external-identities/google-federation.md#deprecation-of-web-view-sign-in-support)。


::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>先决条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="create-a-google-application"></a>创建 Google 应用程序

若要使用户能够在 Azure Active Directory B2C (Azure AD B2C) 中使用 Google 帐户登录，需要在 [Google 开发人员控制台](https://console.developers.google.com/)中创建一个应用程序。 有关详细信息，请参阅[设置 OAuth 2.0](https://support.google.com/googleapi/answer/6158849)。 如果没有 Google 帐户，可以在 [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp) 上注册。

1. 使用 Google 帐户凭据登录 [Google 开发人员控制台](https://console.developers.google.com/)。
1. 在页面左上角选择项目列表，然后选择“New Project”（新建项目）。
1. 输入 **项目名称**，然后选择“Create”（创建）。
1. 通过选择屏幕左上角的项目下拉列表来确保使用的是新项目。 根据名称选择你的项目，然后选择“Open”（打开）。
1. 在左侧菜单中选择“OAuth consent screen”（OAuth 同意屏幕），然后依次选择“External”（外部）、“Create”（创建）。  
输入应用程序的 **名称**。 在“Authorized domains”（已授权的域）部分中输入 *b2clogin.com*，然后选择“Save”（保存）。 
1. 在左侧菜单中选择“凭据”，然后选择“创建凭据” > “Oauth 客户端 ID”。
1. 在“应用程序类型”下，选择“Web 应用程序”。
    1. 输入应用程序的 **名称**。
    1. 对于“授权的 JavaScript 源”，请输入 `https://your-tenant-name.b2clogin.com`。 如果使用[自定义域](custom-domain.md)，请输入 `https://your-domain-name`。
    1. 对于“授权的重定向 URI”，请输入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`。 如果使用[自定义域](custom-domain.md)，请输入 `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp`。 将 `your-domain-name` 替换为你的自定义域，将 `your-tenant-name` 替换为租户的名称。 输入租户名称时，全部使用小写字母，即使租户是使用大写字母在 Azure AD B2C 中定义的，也是如此。
1. 单击“创建”。
1. 复制“客户端 ID”和“客户端密码”的值。 将 Google 配置为租户中的标识提供者时需要这两项。 **客户端密码** 是一个重要的安全凭据。

::: zone pivot="b2c-user-flow"

## <a name="configure-google-as-an-identity-provider"></a>将 Google 配置为标识提供者

1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 选择 Azure 门户左上角的“所有服务”，搜索并选择 **Azure AD B2C**。
1. 依次选择“标识提供者”、“Google”。 
1. 输入“名称”。 例如 *Google*。
1. 对于“客户端 ID”，请输入前面创建的 Google 应用程序的客户端 ID。
1. 对于“客户端密码”，输入已记录的客户端密码。
1. 选择“保存”。

## <a name="add-google-identity-provider-to-a-user-flow"></a>将 Google 标识提供者添加到用户流 

此时，Google 标识提供者已设置，但还不能在任何登录页中使用。 将 Google 标识提供者添加到用户流：


1. 在 Azure AD B2C 租户中，选择“用户流”  。
1. 单击要将 Google 标识提供者添加到的用户流。
1. 在“社交标识提供者”下，选择“Google”。
1. 选择“保存”。
1. 若要测试策略，请选择“运行用户流”。
1. 对于“应用程序”，请选择前面已注册的名为“testapp1”的 Web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 选择“运行用户流”按钮。
1. 在注册或登录页面中，选择“Google”以使用 Google 帐户登录。

如果登录过程是成功的，则你的浏览器会被重定向到 `https://jwt.ms`，其中显示 Azure AD B2C 返回的令牌内容。

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>创建策略密钥

你需要存储前面在 Azure AD B2C 租户中记录的客户端机密。

1. 登录 [Azure 门户](https://portal.azure.com/)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C” 。
1. 在“概述”页上选择“标识体验框架”。
1. 选择“策略密钥”，然后选择“添加”。
1. 对于“选项”，请选择 `Manual`。
1. 输入策略密钥的 **名称**。 例如，`GoogleSecret`。 前缀 `B2C_1A_` 会自动添加到密钥名称。
1. 在“机密”中，输入前面记录的应用程序机密。
1. 在“密钥用法”处选择 `Signature`。
1. 单击“创建”。

## <a name="configure-google-as-an-identity-provider"></a>将 Google 配置为标识提供者

要使用户能够使用 Google 帐户登录，需将该帐户定义为 Azure AD B2C 可通过终结点与之通信的声明提供程序。 该终结点将提供一组声明，Azure AD B2C 使用这些声明来验证特定的用户是否已完成身份验证。

可以通过在策略的扩展文件中将 Google 帐户添加到 **ClaimsProviders** 元素，将该帐户定义为声明提供程序。

1. 打开 *TrustFrameworkExtensions.xml*。
2. 找到 **ClaimsProviders** 元素。 如果该元素不存在，请在根元素下添加它。
3. 如下所示添加新的 **ClaimsProvider**：

    ```xml
    <ClaimsProvider>
      <Domain>google.com</Domain>
      <DisplayName>Google</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Google-OAuth2">
          <DisplayName>Google</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">google</Item>
            <Item Key="authorization_endpoint">https://accounts.google.com/o/oauth2/auth</Item>
            <Item Key="AccessTokenEndpoint">https://accounts.google.com/o/oauth2/token</Item>
            <Item Key="ClaimsEndpoint">https://www.googleapis.com/oauth2/v1/userinfo</Item>
            <Item Key="scope">email profile</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="client_id">Your Google application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_GoogleSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="google.com" />
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

4. 将 **client_id** 设置为应用程序注册中的应用程序 ID。
5. 保存文件。

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAuth2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>测试自定义策略

1. 选择信赖方策略，例如 `B2C_1A_signup_signin`。
1. 对于“应用程序”，请选择[前面注册](tutorial-register-applications.md)的 Web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 选择“立即运行”按钮。
1. 在注册或登录页面中，选择“Google”以使用 Google 帐户登录。

如果登录过程是成功的，则你的浏览器会被重定向到 `https://jwt.ms`，其中显示 Azure AD B2C 返回的令牌内容。

::: zone-end

## <a name="next-steps"></a>后续步骤

了解如何[将 Google 令牌传递给应用程序](idp-pass-through-user-flow.md)。