---
title: 使用 SAML 标识提供者设置注册和登录
titleSuffix: Azure Active Directory B2C
description: 在 Azure Active Directory B2C 中使用任何 SAML 标识提供者 (IdP) 设置注册和登录。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/30/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 046911fef537f91e1b1d6c5f6c1ca351d2cc171f
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2021
ms.locfileid: "121777783"
---
# <a name="set-up-sign-up-and-sign-in-with-saml-identity-provider-using-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用 SAML 标识提供者设置注册和登录

Azure Active Directory B2C (Azure AD B2C) 支持使用 SAML 2.0 标识提供者进行联合身份验证。 本文介绍如何使用 SAML 标识提供者用户帐户登录，从而允许用户使用其现有的社交或企业标识（例如 [ADFS](./identity-provider-adfs.md) 和 [Salesforce](identity-provider-salesforce-saml.md)）登录。

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="scenario-overview"></a>方案概述

可以配置 Azure AD B2C，以允许用户使用外部社交或企业 SAML 标识提供者 (IdP) 提供的凭据登录到你的应用程序。 当 Azure AD B2C 使用 SAML 标识提供者进行联合身份验证时，它将充当向 SAML 标识提供者发起 SAML 请求并等待 SAML 响应的服务提供商。 在下图中：

1. 应用程序向 Azure AD B2C 发起授权请求。 应用程序可以是 [OAuth 2.0](protocols-overview.md) 或 [OpenId Connect](openid-connect.md) 应用程序，也可以是 [SAML 服务提供者](saml-service-provider.md)。 
1. 在 Azure AD B2C 登录页上，用户选择使用 SAML 标识提供者帐户（例如 Contoso）登录。 Azure AD B2C 发起 SAML 授权请求，并将用户转到 SAML 标识提供者以完成登录。
1. SAML 标识提供者返回 SAML 响应。
1. Azure AD B2C 验证 SAML 令牌、提取声明，颁发其自己的令牌，并使用户返回到应用程序。  

![使用 SAML 标识提供者流登录](./media/identity-provider-generic-saml/sign-in-with-saml-identity-provider-flow.png)

## <a name="prerequisites"></a>先决条件

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="components-of-the-solution"></a>解决方案组件

此方案需要以下组件：

* SAML 标识提供者能够接收、解码和响应来自 Azure AD B2C 的 SAML 请求。
* 标识提供者公开可用的 SAML 元数据终结点。
* [Azure AD B2C 租户](tutorial-create-tenant.md)。

[!INCLUDE [active-directory-b2c-https-cipher-tls-requirements](../../includes/active-directory-b2c-https-cipher-tls-requirements.md)]

## <a name="create-a-policy-key"></a>创建策略密钥

若要在 Azure AD B2C 与 SAML 标识提供者之间建立信任，你需要提供带有私钥的有效 X509 证书。 Azure AD B2C 使用证书的私钥对 SAML 请求进行签名。 标识提供者使用证书的公钥验证请求。 通过技术配置文件元数据可访问公钥。 或者，可以将 .cer 文件手动上传到 SAML 身份提供程序。

在大多数情况下，自签名证书都是可接受的。 对于生产环境，建议使用由证书颁发机构颁发的 X509 证书。 此外，如本文档后面所述，对于非生产环境，你可以禁用两端的 SAML 签名。

### <a name="obtain-a-certificate"></a>获得证书

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

### <a name="upload-the-certificate"></a>上传证书

需要将你的证书存储在 Azure AD B2C 租户中。

1. 登录 [Azure 门户](https://portal.azure.com/)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含租户的目录。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C” 。
1. 在“概述”页上选择“标识体验框架”。
1. 选择“策略密钥”，然后选择“添加”。
1. 对于“选项”，请选择 `Upload`。
1. 输入策略密钥的 **名称**。 例如，`SAMLSigningCert` 。 前缀 `B2C_1A_` 会自动添加到密钥名称。
1. 浏览并选择带有私钥的证书 .pfx 文件。
1. 单击“创建”。

## <a name="configure-the-saml-technical-profile"></a>配置 SAML 技术配置文件

通过在策略的扩展文件中将 SAML 标识提供者添加到 ClaimsProviders 元素来定义该标识提供者。 声明提供程序包含用于确定与 SAML 标识提供者通信所需的终结点和协议的 SAML 技术配置文件。 若要添加包含 SAML 技术配置文件的声明提供程序，请执行以下操作：

1. 打开 *TrustFrameworkExtensions.xml*。
1. 找到 **ClaimsProviders** 元素。 如果该元素不存在，请在根元素下添加它。
1. 如下所示添加新的 **ClaimsProvider**：

    ```xml
    <ClaimsProvider>
      <Domain>Contoso.com</Domain>
      <DisplayName>Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso</DisplayName>
          <Description>Login with your SAML identity provider account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="PartnerEntity">https://your-AD-FS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="assertionSubjectName" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="http://schemas.microsoft.com/identity/claims/displayname" />
            <OutputClaim ClaimTypeReferenceId="email"  />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-idp"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

使用以下相关值更新以下 XML 元素：

|XML 元素  |“值”  |
|---------|---------|
|ClaimsProvider\Domain  | 用于[直接登录](direct-signin.md?pivots=b2c-custom-policy#redirect-sign-in-to-a-social-provider)的域名。 输入要在直接登录中使用的域名。 例如，Contoso.com。 |
|TechnicalProfile\DisplayName|此值会显示在登录屏幕中的登录按钮上。 例如，Contoso。 |
|Metadata\PartnerEntity|SAML 身份提供程序的元数据的 URL。 或者可以复制标识提供者元数据并将其添加到 CDATA 元素 `<![CDATA[Your IDP metadata]]>`。|

## <a name="map-the-claims"></a>映射声明

OutputClaims 元素包含 SAML 标识提供者返回的声明列表。 将策略中定义的声明名称映射到标识提供者中定义的断言名称。 检查标识提供者以获取声明（断言）的列表。 有关详细信息，请参阅[声明映射](identity-provider-generic-saml-options.md#claims-mapping)。

在上面的示例中，Contoso-SAML2 包括 SAML 标识提供者返回的声明：

* issuerUserId 声明映射到 assertionSubjectName 声明。
* **first_name** 声明已映射到 **givenName** 声明。
* **last_name** 声明已映射到 **surname** 声明。
* displayName 声明已映射到 `http://schemas.microsoft.com/identity/claims/displayname` 声明。
* 没有名称映射的 **email** 声明。

技术配置文件还会返回标识提供者不返回的声明：

* **identityProvider** 声明，其中包含标识提供者的名称。
* **authenticationSource** 声明，其默认值为 **socialIdpAuthentication**。
 
### <a name="add-the-saml-session-technical-profile"></a>添加 SAML 会话技术配置文件

如果还没有 `SM-Saml-idp` SAML 会话技术配置文件，请在扩展策略中添加一个。 找到 `<ClaimsProviders>` 部分并添加以下 XML 片段。 如果策略已包含 `SM-Saml-idp` 技术配置文件，请跳到下一步。 有关详细信息，请参阅[单一登录会话管理](custom-policy-reference-sso.md)。

```xml
<ClaimsProvider>
  <DisplayName>Session Management</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SM-Saml-idp">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="IncludeSessionIndex">false</Item>
        <Item Key="RegisterServiceProviders">false</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-create-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="configure-your-saml-identity-provider"></a>配置 SAML 标识提供者

配置策略后，需要使用 Azure AD B2C SAML 元数据配置 SAML 标识提供者。 SAML 元数据是 SAML 协议中用于公开策略（例如服务提供商）的配置的信息。 它定义服务的位置，例如登录和注销、证书、登录方法和其他信息。

每个 SAML 标识提供者都有不同的步骤来设置服务提供商。 一些 SAML 标识提供者请求 Azure AD B2C 元数据，而另一些则要求你手动浏览元数据文件并提供信息。 有关指导，请参阅标识提供者的文档。

以下示例显示 Azure AD B2C 技术配置文件的 SAML 元数据的 URL 地址：

```
https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-policy>/samlp/metadata?idptp=<your-technical-profile>
```

使用[自定义域](custom-domain.md)时，请使用以下格式：

```
https://your-domain-name/<your-tenant-name>.onmicrosoft.com/<your-policy>/samlp/metadata?idptp=<your-technical-profile>
```

请替换以下值：

- 将 your-tenant-name 替换为租户的名称，例如 your-tenant.onmicrosoft.com。
- 将 your-domain-name 替换为你的自定义域名，例如 login.contoso.com。
- 将 your-policy 替换为你的策略名称。 例如，B2C_1A_signup_signin_adfs。
- 将 your-technical-profile 替换为 SAML 标识提供者技术配置文件的名称。 例如，Contoso-SAML2。

打开浏览器并导航到此 URL。 确保键入正确的 URL 并且你有权访问 XML 元数据文件。

## <a name="test-your-custom-policy"></a>测试自定义策略

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在门户工具栏中选择“目录 + 订阅”图标，然后选择包含 Azure AD B2C 租户的目录。
1. 在 Azure 门户中，搜索并选择“Azure AD B2C”  。
1. 在“策略”下，选择“Identity Experience Framework”
1. 选择信赖方策略，例如 `B2C_1A_signup_signin`。
1. 对于“应用程序”，请选择[前面注册](tutorial-register-applications.md)的 Web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 选择“立即运行”按钮。
1. 在注册或登录页面上，选择“Contoso”以使用 Contoso 帐户登录。

如果登录过程是成功的，则你的浏览器会被重定向到 `https://jwt.ms`，其中显示 Azure AD B2C 返回的令牌内容。

## <a name="next-steps"></a>后续步骤

- [使用 Azure Active Directory B2C 配置 SAML 标识提供者选项](identity-provider-generic-saml-options.md)

::: zone-end