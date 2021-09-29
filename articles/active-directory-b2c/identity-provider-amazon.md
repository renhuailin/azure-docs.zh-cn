---
title: 使用 Amazon 帐户设置注册和登录
titleSuffix: Azure AD B2C
description: 使用 Azure Active Directory B2C 提供在应用程序中通过 Amazon 帐户注册与登录到客户的设置。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.custom: project-no-code
ms.date: 09/16/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 58b4738784683aa1ce9271e7cf3a022d70496e1a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128572096"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 设置通过 Amazon 帐户注册与登录

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>先决条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-app-in-the-amazon-developer-console"></a>在 Amazon 开发人员控制台中创建应用

若要在 Azure Active Directory B2C (Azure AD B2C) 中为使用 Amazon 帐户的用户启用登录，则需要在 [Amazon 开发人员服务和技术](https://developer.amazon.com)中创建应用程序。 有关详细信息，请参阅[注册使用 Amazon 登录](https://developer.amazon.com/docs/login-with-amazon/register-web.html)。 如果还没有 Amazon 帐户，可在 [https://www.amazon.com/](https://www.amazon.com/) 上注册一个。

1. 使用 Amazon 帐户凭据登录 [Amazon 开发人员控制台](https://developer.amazon.com/dashboard)。
1. 如果未曾登录过，请选择“注册”，按照开发人员注册步骤操作，然后接受策略。
1. 在仪表板中，选择“Login with Amazon”。
1. 选择“创建新的安全配置文件”。
1. 输入 **安全配置文件名称**、**安全配置文件说明** 和 **同意隐私声明 URL**，例如 `https://www.contoso.com/privacy` 隐私声明 URL 是你管理的、向用户提供隐私信息的页面。 然后单击“保存”  。
1. 在“Login with Amazon 配置”部分中，选择你创建的安全配置文件名称，选择“管理”图标，然后选择“Web 设置”。
1. 在“Web 设置”部分中，复制“客户端 ID”的值。 选择“显示机密”来获取客户端机密，然后复制它。 将 Amazon 帐户配置为租户中的标识提供者时需要这两个值。 “客户端密钥”是一个很重要的安全凭据。
1. 在“Web 设置”部分中，选择“编辑” 。 
    1. 在“允许的源”中，输入 `https://your-tenant-name.b2clogin.com`。 将 `your-tenant-name` 替换为租户的名称。 如果使用[自定义域](custom-domain.md)，请输入 `https://your-domain-name`。
    1.  对于“允许的返回 URL”，请输入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`。  如果使用[自定义域](custom-domain.md)，请输入 `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp`。  将 `your-tenant-name` 替换为租户的名称，将 `your-domain-name` 替换为你的自定义域。
1. 选择“保存”。

::: zone pivot="b2c-user-flow"

## <a name="configure-amazon-as-an-identity-provider"></a>将 Amazon 配置为标识提供者

1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 选择 Azure 门户左上角的“所有服务”，搜索并选择 **Azure AD B2C**。
1. 依次选择“标识提供者”和“Amazon”。
1. 输入“名称”。 例如，Amazon。
1. 对于“客户端 ID”，输入之前创建的 Amazon 应用程序的“客户端 ID”。
1. 对于“客户端密码”，输入已记录的客户端密码。
1. 选择“保存”。

## <a name="add-amazon-identity-provider-to-a-user-flow"></a>将 Amazon 标识提供者添加到用户流 

此时，Amazon 标识提供者已设置，但还不能在任何登录页中使用。 若要将 Amazon 标识提供者添加到用户流，请执行以下操作：

1. 在 Azure AD B2C 租户中，选择“用户流”  。
1. 单击要添加 Amazon 标识提供者的用户流。
1. 在“社交标识提供者”下，选择“Amazon”。
1. 选择“保存”。
1. 若要测试策略，请选择“运行用户流”。
1. 对于“应用程序”，请选择前面已注册的名为“testapp1”的 Web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 选择“运行用户流”按钮。
1. 在注册或登录页面上，选择“Amazon”以使用 Amazon 帐户登录。

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
1. 输入策略密钥的 **名称**。 例如，`AmazonSecret`。 前缀 `B2C_1A_` 会自动添加到密钥名称。
1. 在“机密”中，输入前面记录的应用程序机密。
1. 在“密钥用法”处选择 `Signature`。
1. 单击“创建”。

## <a name="configure-amazon-as-an-identity-provider"></a>将 Amazon 配置为标识提供者

若要使用户能够使用 Amazon 帐户登录，则需将该帐户定义为声明提供程序。 Azure AD B2C 可以通过终结点与之进行通信。 该终结点将提供一组声明，Azure AD B2C 使用这些声明来验证特定的用户是否已完成身份验证。

可以通过在策略的扩展文件中将 Amazon 帐户添加到 **ClaimsProviders** 元素，将该帐户定义为声明提供程序。


1. 打开 *TrustFrameworkExtensions.xml*。
2. 找到 **ClaimsProviders** 元素。 如果该元素不存在，请在根元素下添加它。
3. 如下所示添加新的 **ClaimsProvider**：

    ```xml
    <ClaimsProvider>
      <Domain>amazon.com</Domain>
      <DisplayName>Amazon</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Amazon-OAuth2">
        <DisplayName>Amazon</DisplayName>
        <Protocol Name="OAuth2" />
        <Metadata>
          <Item Key="ProviderName">amazon</Item>
          <Item Key="authorization_endpoint">https://www.amazon.com/ap/oa</Item>
          <Item Key="AccessTokenEndpoint">https://api.amazon.com/auth/o2/token</Item>
          <Item Key="ClaimsEndpoint">https://api.amazon.com/user/profile</Item>
          <Item Key="scope">profile</Item>
          <Item Key="HttpBinding">POST</Item>
          <Item Key="UsePolicyInRedirectUri">false</Item>
          <Item Key="client_id">Your Amazon application client ID</Item>
        </Metadata>
        <CryptographicKeys>
          <Key Id="client_secret" StorageReferenceId="B2C_1A_AmazonSecret" />
        </CryptographicKeys>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
          <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
          <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
          <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="amazon.com" />
          <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
        </OutputClaims>
          <OutputClaimsTransformations>
          <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
          <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
          <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
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
    <ClaimsProviderSelection TargetClaimsExchangeId="AmazonExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AmazonExchange" TechnicalProfileReferenceId="Amazon-OAuth2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>测试自定义策略

1. 选择信赖方策略，例如 `B2C_1A_signup_signin`。
1. 对于“应用程序”，请选择[前面注册](tutorial-register-applications.md)的 Web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 选择“立即运行”按钮。
1. 在注册或登录页面上，选择“Amazon”以使用 Amazon 帐户登录。

如果登录过程是成功的，则你的浏览器会被重定向到 `https://jwt.ms`，其中显示 Azure AD B2C 返回的令牌内容。

::: zone-end
