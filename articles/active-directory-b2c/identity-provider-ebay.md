---
title: 使用 eBay 帐户设置注册和登录
titleSuffix: Azure AD B2C
description: 使用 Azure Active Directory B2C，为应用程序中的客户提供通过 eBay 帐户注册与登录的功能。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/16/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: ed60e8dc519bc4a23ef3814a76d729e1b875e694
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128574982"
---
# <a name="set-up-sign-up-and-sign-in-with-an-ebay-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 设置通过 eBay 帐户注册与登录

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="prerequisites"></a>先决条件

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="create-an-ebay-application"></a>创建 eBay 应用程序

要使用户能够在 Azure Active Directory B2C (Azure AD B2C) 中使用 eBay 帐户登录，需在 [eBay 开发人员控制台](https://developer.ebay.com)中创建一个应用程序。 有关详细信息，请参阅[创建开发人员帐户](https://developer.ebay.com/api-docs/static/creating-edp-account.html)。 如果没有 eBay 开发人员帐户，可以在 [https://developer.ebay.com/signin](https://developer.ebay.com/signin?tab=register) 上注册。

若要创建 eBay 应用程序，请按照以下步骤操作：

1. 用 eBay 开发人员帐户凭据登录到 eBay 开发人员控制台的[应用程序密钥](https://developer.ebay.com/my/keys)。
1. 输入应用程序标题。
1. 在“生产”下，选择“创建键集” 。 
1. 在“确认此帐户的主要联系人”页上，提供你的帐户详细信息。 若要完成注册过程，请选择“继续以创建密钥”。
1. 复制“应用 ID (客户端 ID)”和“应用 ID (客户端 ID)”的值 。 将标识提供者添加到租户时需要这两个值。
1. 选择“用户令牌”，然后选择“通过应用程序从 eBay 获取令牌” 。
1. 选择“添加 eBay 重定向 URL”。
    1. 输入你的隐私策略 URL 的有效 URL，例如 `https://www.contoso.com/privacy`。 策略 URL 是继续提供应用程序的隐私信息的页面。
    1. 在你的身份验证接受的 URL 中，输入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`。 如果使用[自定义域](custom-domain.md)，请输入 `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp`。 将 `your-tenant-name` 替换为租户的名称，将 `your-domain-name` 替换为你的自定义域。
1. 选择“保存”。

## <a name="create-a-policy-key"></a>创建策略密钥

你需要存储前面在 Azure AD B2C 租户中记录的客户端机密。

1. 登录 [Azure 门户](https://portal.azure.com/)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C” 。
1. 在“概述”页上选择“标识体验框架”。
1. 选择“策略密钥”，然后选择“添加”。
1. 对于“选项”，请选择 `Manual`。
1. 输入策略密钥的 **名称**。 例如，`eBaySecret`。 前缀 `B2C_1A_` 会自动添加到密钥名称。
1. 在“机密”中，输入前面记录的应用程序机密。
1. 在“密钥用法”处选择 `Signature`。
1. 选择“创建”。

## <a name="configure-ebay-as-an-identity-provider"></a>将 eBay 配置为标识提供者

要使用户能够使用 eBay 帐户登录，需将该帐户定义为 Azure AD B2C 可通过终结点与之通信的声明提供程序。 该终结点将提供一组声明，Azure AD B2C 使用这些声明来验证特定的用户是否已完成身份验证。

可通过在策略的扩展文件中将 eBay 帐户添加到 ClaimsProvider 元素，将该帐户定义为声明提供程序。

1. 打开 *TrustFrameworkExtensions.xml*。
2. 找到 **ClaimsProviders** 元素。 如果该元素不存在，请在根元素下添加它。
3. 如下所示添加新的 **ClaimsProvider**：

    ```xml
    <!--
    <ClaimsProviders> -->
      <ClaimsProvider>
        <Domain>ebay.com</Domain>
        <DisplayName>eBay</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="eBay-OAUTH2">
            <DisplayName>eBay</DisplayName>
            <Protocol Name="OAuth2" />
            <Metadata>
              <Item Key="ProviderName">ebay.com</Item>
              <Item Key="authorization_endpoint">https://auth.ebay.com/oauth2/authorize</Item>
              <Item Key="AccessTokenEndpoint">https://api.ebay.com/identity/v1/oauth2/token</Item>
              <Item Key="ClaimsEndpoint">https://apiz.ebay.com/commerce/identity/v1/user/</Item>
              <Item Key="HttpBinding">POST</Item>
              <Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
              <Item Key="token_endpoint_auth_method">client_secret_basic</Item>
              <Item Key="scope">https://api.ebay.com/oauth/api_scope/commerce.identity.readonly</Item>
              <Item Key="UsePolicyInRedirectUri">0</Item>
              <!-- Update the Client ID below to the Application ID -->
              <Item Key="client_id">Your eBay app ID</Item>
            </Metadata>
            <CryptographicKeys>
              <Key Id="client_secret" StorageReferenceId="eBaySecret"/>
            </CryptographicKeys>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userId"/>
              <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="individualAccount.firstName"/>
              <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="individualAccount.lastName"/>
              <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="username"/>
              <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
              <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="ebay.com" AlwaysUseDefaultValue="true" />
              <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            </OutputClaims>
            <OutputClaimsTransformations>
              <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
              <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
              <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            </OutputClaimsTransformations>
            <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    <!--
    </ClaimsProviders> -->
    ```

4. 将 **client_id** 设置为应用程序注册中的应用程序 ID。
5. 保存文件。


[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="eBayExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="eBayExchange" TechnicalProfileReferenceId="eBay-OAUTH2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>测试自定义策略

1. 选择信赖方策略，例如 `B2C_1A_signup_signin`。
1. 对于“应用程序”，请选择[前面注册](tutorial-register-applications.md)的 Web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 选择“立即运行”按钮。
1. 在注册或登录页面上，选择“eBay”以使用 eBay 帐户登录。

如果登录过程是成功的，则你的浏览器会被重定向到 `https://jwt.ms`，其中显示 Azure AD B2C 返回的令牌内容。


## <a name="next-steps"></a>后续步骤

了解如何[将 Facebook 令牌传递给你的应用程序](idp-pass-through-user-flow.md)。

::: zone-end
