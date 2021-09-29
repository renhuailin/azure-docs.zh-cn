---
title: 使用 SAML 协议设置通过 Salesforce SAML 提供者进行的登录
titleSuffix: Azure AD B2C
description: 通过在 Azure Active Directory B2C 中使用 SAML 协议来设置通过 Salesforce SAML 提供者进行的登录。
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
ms.openlocfilehash: 4501d28699b2bf9056cfd814bc89b5e9c5672ab9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128571948"
---
# <a name="set-up-sign-in-with-a-salesforce-saml-provider-by-using-saml-protocol-in-azure-active-directory-b2c"></a>通过在 Azure Active Directory B2C 中使用 SAML 协议来设置使用 Salesforce SAML 提供程序的登录

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"
[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文展示了如何在 Azure Active Directory B2C (Azure AD B2C) 中使用[自定义策略](custom-policy-overview.md)为来自 Salesforce 组织的用户实现登录。 可通过将 [SAML 标识提供者](identity-provider-generic-saml.md)添加到自定义策略来实现登录。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]
- 如果尚未注册，请注册一个[免费的 Developer Edition 帐户](https://developer.salesforce.com/signup)。 本文使用 [Salesforce Lightning Experience](https://developer.salesforce.com/page/Lightning_Experience_FAQ)。
- 为 Salesforce 组织[设置“我的域”](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0)。

## <a name="set-up-salesforce-as-an-identity-provider"></a>将 Salesforce 设置为标识提供者

1. [登录到 Salesforce](https://login.salesforce.com/)。
2. 在左侧菜单中的“设置”下面，展开“标识”，然后选择“标识提供者”。
3. 选择“启用标识提供者”。
4. 在“选择证书”下，选择希望 Salesforce 用于与 Azure AD B2C 进行通信的证书。 可以使用默认证书。
5. 单击“ **保存**”。

### <a name="create-a-connected-app-in-salesforce"></a>在 Salesforce 中创建连接的应用

1. 在“标识提供者”页面上，选择“现在通过已连接应用创建服务提供程序”。请单击此处”。
2. 在“基本信息”下，为连接的应用输入所需值。
3. 在“Web 应用设置”下，选中“启用 SAML”复选框。
4. 在“实体 ID”字段中，输入以下 URL。 确保将 `your-tenant` 的值替换为你的 Azure AD B2C 租户的名称。

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```

      使用[自定义域](custom-domain.md)时，请使用以下格式：

      ```
      https://your-domain-name/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```

6. 在“ACS URL”字段中，输入以下 URL。 确保将 `your-tenant` 的值替换为你的 Azure AD B2C 租户的名称。

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```

      使用[自定义域](custom-domain.md)时，请使用以下格式：

      ```
      https://your-domain-name/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```

7. 滚动到列表底部，然后单击“保存”。

### <a name="get-the-metadata-url"></a>获取元数据 URL

1. 在连接的应用的概述页上，单击“管理”。
2. 复制“元数据发现终结点”的值，然后保存它。 在本文后面部分将使用它。

### <a name="set-up-salesforce-users-to-federate"></a>设置 Salesforce 用户以进行联合

1. 在连接的应用的“管理”页上，单击“管理配置文件”。
2. 选择要与 Azure AD B2C 联合的配置文件（或用户组）。 以系统管理员身份，选中“系统管理员”复选框，以便可以使用 Salesforce 帐户进行联合。

## <a name="create-a-self-signed-certificate"></a>创建自签名证书

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

## <a name="create-a-policy-key"></a>创建策略密钥

你需要将已创建的证书存储在 Azure AD B2C 租户中。

1. 登录 [Azure 门户](https://portal.azure.com/)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C” 。
1. 在“概述”页上选择“标识体验框架”。
1. 选择“策略密钥”，然后选择“添加”。
1. 对于“选项”，请选择 `Upload`。
1. 输入策略的 **名称**。 例如，SAMLSigningCert。 前缀 `B2C_1A_` 将自动添加到你的密钥的名称中。
1. 浏览找到并选择你创建的 B2CSigningCert.pfx 证书。
1. 输入证书的密码。
1. 单击“创建”。

## <a name="add-a-claims-provider"></a>添加声明提供程序

如果希望用户使用 Salesforce 帐户登录，需将该帐户定义为 Azure AD B2C 可通过终结点与其进行通信的声明提供程序。 该终结点将提供一组声明，Azure AD B2C 使用这些声明来验证特定的用户是否已完成身份验证。

可以通过在策略的扩展文件中将 Salesforce 帐户添加到 **ClaimsProvider** 元素，将该帐户定义为声明提供程序。 有关详细信息，请参阅[定义 SAML 标识提供者](identity-provider-generic-saml.md)。

1. 打开 *TrustFrameworkExtensions.xml*。
1. 找到 **ClaimsProviders** 元素。 如果该元素不存在，请在根元素下添加它。
1. 如下所示添加新的 **ClaimsProvider**：

    ```xml
    <ClaimsProvider>
      <Domain>salesforce.com</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Salesforce-SAML2">
          <DisplayName>Salesforce</DisplayName>
          <Description>Login with your Salesforce account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="WantsSignedAssertions">false</Item>
            <Item Key="PartnerEntity">https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userId"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="username"/>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="salesforce.com" />
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

1. 将 **PartnerEntity** 的值更新为前面复制的 Salesforce 元数据 URL。
1. 将 **StorageReferenceId** 的两个实例的值更新为你的签名证书的密钥。 例如，B2C_1A_SAMLSigningCert。
1. 找到 `<ClaimsProviders>` 部分并添加以下 XML 片段。 如果策略已包含 `SM-Saml-idp` 技术配置文件，请跳到下一步。 有关详细信息，请参阅[单一登录会话管理](custom-policy-reference-sso.md)。

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
1. 保存文件。

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
    <ClaimsExchange Id="SalesforceExchange" TechnicalProfileReferenceId="Salesforce-SAML2" />
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