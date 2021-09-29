---
title: 设置使用 Salesforce 帐户进行注册和登录
titleSuffix: Azure AD B2C
description: 为客户提供使用 Azure Active Directory B2C 在应用程序中通过 Salesforce 帐户进行注册与登录的设置。
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
ms.openlocfilehash: c1c9ed19d55850258c043a2a1e223474e13d2d21
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128574294"
---
# <a name="set-up-sign-up-and-sign-in-with-a-salesforce-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 设置 Salesforce 帐户的注册与登录

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>先决条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="create-a-salesforce-application"></a>创建一个 Salesforce 应用程序

要使用户能够在 Azure Active Directory B2C (Azure AD B2C) 中使用 Salesforce 帐户登录，需要在 Salesforce [应用管理器](https://login.salesforce.com/)中创建一个应用程序。 有关详细信息，请参阅[配置基本的连接应用设置](https://help.salesforce.com/articleView?id=connected_app_create_basics.htm)和[启用 OAuth 设置以实现 API 集成](https://help.salesforce.com/articleView?id=connected_app_create_api_integration.htm)

1. [登录到 Salesforce](https://login.salesforce.com/)。
1. 在菜单中选择“Setup”（设置）。
1.  展开“Apps”（应用），然后选择“App Manager”（应用管理器）。 
1. 选择“New Connected App”（新建连接的应用）。
1. 在“Basic Information”（基本信息）下，输入：
    1. **Connected App Name**（连接的应用名称）- 连接的应用名称显示在“App Manager”（应用管理器）以及自身的“App Launcher”（应用启动器）磁贴上。 该名称必须在整个组织中保持唯一。 
    1. **API Name**（API 名称） 
    1. **Contact Email**（联系人电子邮件地址）- Salesforce 的联系人电子邮件地址
1. 在“API (Enable OAuth Settings)”（API (启用 OAuth 设置)）下，选择“Enable OAuth Settings”（启用 OAuth 设置）。 
    1. 对于“回调 URL”，输入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`。 如果使用[自定义域](custom-domain.md)，请输入 `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp`。 将 `your-tenant-name` 替换为租户的名称，将 `your-domain-name` 替换为你的自定义域。 输入租户名称时，必须全部使用小写字母，即使租户是使用大写字母在 Azure AD B2C 中定义的，也是如此。
    1. 在“Selected OAuth Scopes”（选定的 OAuth 范围）中，选择“Access your basic information (id, profile, email, address, phone)”（访问你的基本信息(ID、个人资料、电子邮件、地址、电话)）和“Allow access to your unique identifier (openid)”（允许访问你的唯一标识符(openid)）。  
    1. 选择“Require Secret for Web Server Flow”（需要 Web 服务器流的机密）。
1. 选择“Configure ID Token”（配置 ID 令牌） 
    1. 将“Token Valid for”（令牌有效期）设置为 5 分钟。
    1. 选择“Include Standard Claims”（包括标准声明）。
1. 单击“保存”  。
1. 复制“Consumer Key”（使用者密钥）和“Consumer Secret”（使用者机密）的值。  将 Salesforce 配置为租户中的标识提供者时需要这两个凭据。 **客户端密码** 是一个重要的安全凭据。

::: zone pivot="b2c-user-flow"

## <a name="configure-salesforce-as-an-identity-provider"></a>将 Salesforce 配置为标识提供者

1. 确保你正在使用包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C” 。
1. 选择“标识提供程序”，然后选择“新建 OpenID Connect 提供程序” 。
1. 输入“名称”。 例如，输入 *Salesforce*。
1. 对于“元数据 URL”，请输入 [Salesforce OpenID Connect 配置文档](https://help.salesforce.com/articleView?id=remoteaccess_using_openid_discovery_endpoint.htm)的 URL。 在沙盒中，login.salesforce.com 将替换为 test.salesforce.com。 在社区版中，login.salesforce.com 将替换为社区 URL，例如 username.force.com/.well-known/openid-configuration。 该 URL 必须包含 HTTPS。

    ```
    https://login.salesforce.com/.well-known/openid-configuration
    ```

1. 对于“客户端 ID”，输入之前记录的应用程序 ID。
1. 对于“客户端机密”，请输入之前记录的客户端机密。
1. 对于“范围”，请输入 `openid id profile email`。
1. 对于“响应类型”和“响应模式” ，请保留默认值。
1. （可选）对于“域提示”，请输入 `contoso.com`。 有关详细信息，请参阅[使用 Azure Active Directory B2C 设置直接登录](direct-signin.md#redirect-sign-in-to-a-social-provider)。
1. 在“标识提供者声明映射”下，选择以下声明：

    - **用户 ID**：*sub*
    - 显示名称：name
    - 给定名称：given_name
    - 姓氏：family_name
    - **电子邮件**：*email*

1. 选择“保存”。

## <a name="add-salesforce-identity-provider-to-a-user-flow"></a>将 Salesforce 标识提供者添加到用户流 

此时，Salesforce 标识提供者已设置，但还不能在任何登录页面中使用。 将 Salesforce 标识提供者添加到用户流：

1. 在 Azure AD B2C 租户中，选择“用户流”  。
1. 单击要将 Salesforce 标识提供者添加到的用户流。
1. 在“社交标识提供者”下，选择“Salesforce” 。
1. 选择“保存”。
1. 若要测试策略，请选择“运行用户流”。
1. 对于“应用程序”，请选择前面已注册的名为“testapp1”的 Web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 选择“运行用户流”按钮。
1. 在“注册或登录”页上，选择“Salesforce”以使用 Salesforce 帐户登录。

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
1. 输入策略密钥的 **名称**。 例如，`SalesforceSecret`。 前缀 `B2C_1A_` 会自动添加到密钥名称。
1. 在“机密”中，输入前面记录的应用程序机密。
1. 在“密钥用法”处选择 `Signature`。
1. 单击“创建”。

## <a name="configure-salesforce-as-an-identity-provider"></a>将 Salesforce 配置为标识提供者

要使用户能够使用 Salesforce 帐户登录，需将该帐户定义为 Azure AD B2C 可通过终结点与之通信的声明提供程序。 该终结点将提供一组声明，Azure AD B2C 使用这些声明来验证特定的用户是否已完成身份验证。

可以通过在策略的扩展文件中将 Salesforce 帐户添加到 **ClaimsProvider** 元素，将该帐户定义为声明提供程序。

1. 打开 *TrustFrameworkExtensions.xml*。
2. 找到 **ClaimsProviders** 元素。 如果该元素不存在，请在根元素下添加它。
3. 如下所示添加新的 **ClaimsProvider**：

    ```xml
    <ClaimsProvider>
      <Domain>salesforce.com</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Salesforce-OpenIdConnect">
          <DisplayName>Salesforce</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="METADATA">https://login.salesforce.com/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="scope">openid id profile email</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">Your Salesforce application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_SalesforceSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="salesforce.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
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

4. “元数据”设置为 [Salesforce OpenID Connect 配置文档](https://help.salesforce.com/articleView?id=remoteaccess_using_openid_discovery_endpoint.htm)的 URL。 在沙盒中，login.salesforce.com 将替换为 test.salesforce.com。 在社区版中，login.salesforce.com 将替换为社区 URL，例如 username.force.com/.well-known/openid-configuration。 该 URL 必须包含 HTTPS。
5. 将 **client_id** 设置为应用程序注册中的应用程序 ID。
6. 保存文件。

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="SalesforceExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="SalesforceExchange" TechnicalProfileReferenceId="Salesforce-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>测试自定义策略

1. 选择信赖方策略，例如 `B2C_1A_signup_signin`。
1. 对于“应用程序”，请选择[前面注册](tutorial-register-applications.md)的 Web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 选择“立即运行”按钮。
1. 在注册或登录页上，选择“Salesforce”以使用 Salesforce 帐户登录。

如果登录过程是成功的，则你的浏览器会被重定向到 `https://jwt.ms`，其中显示 Azure AD B2C 返回的令牌内容。


::: zone-end

## <a name="next-steps"></a>后续步骤

了解如何[将 Salesforce 令牌传递给应用程序](idp-pass-through-user-flow.md)。
