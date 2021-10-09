---
title: 将 Azure Active Directory B2C 作为 SAML IdP 配置到应用程序
title-suffix: Azure Active Directory B2C
description: 了解如何配置 Azure Active Directory B2C 以向应用程序（服务提供程序）提供 SAML 协议断言。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/20/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 673835a3e3112bf433faeba815e65c6203dd9ce8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128603802"
---
# <a name="register-a-saml-application-in-azure-ad-b2c"></a>在 Azure AD B2C 中注册 SAML 应用程序

本文介绍如何将安全断言标记语言 (SAML) 应用程序（服务提供程序）连接到 Azure Active Directory B2C (Azure AD B2C) 以进行身份验证。

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="overview"></a>概述

使用 Azure AD B2C 作为客户标识并访问管理解决方案的组织可能需要与使用 SAML 协议进行身份验证的应用程序集成。 下图显示了如何将 Azure AD B2C 用作标识提供者 (IdP)，在基于 SAML 的应用程序中实现单一登录 (SSO)。

![示意图的左侧显示 Azure Active Directory B2C 用作标识提供者，右侧显示 B2C 用作服务提供程序。](media/saml-service-provider/saml-service-provider-integration.png)

1. 应用程序创建一个要发送到 Azure AD B2C SAML 登录终结点的 SAML AuthN 请求。
2. 用户可以使用 Azure AD B2C 本地帐户或任何其他联合标识提供者（如果已配置）进行身份验证。
3. 如果用户使用联合标识提供者登录，令牌响应会发送到 Azure AD B2C。
4. Azure AD B2C 生成 SAML 断言并将其发送到应用程序。

观看此视频，了解如何将 SAML 应用程序与 Azure AD B2C 集成。 

>[!Video https://www.youtube.com/embed/r2TIVBCm7v4]

## <a name="prerequisites"></a>先决条件

对于本文中的应用场景，需具备以下项：

* 自定义策略初学者包中的 SocialAndLocalAccounts 自定义策略。 完成 [Azure AD B2C 中的自定义策略](tutorial-create-user-flows.md?pivots=b2c-custom-policy)中的步骤。 
* 基本了解 SAML 协议，并熟悉应用程序的 SAML 实现。
* 一个已配置为 SAML 应用程序的应用程序。 它必须能够发送 SAML AuthN 请求，以及接收、解码和验证来自 Azure AD B2C 的 SAML 响应。 SAML 应用程序也称为信赖方应用程序或服务提供程序。 
* SAML 应用程序的公开可用 SAML 元数据终结点或 XML 文档。
* [Azure AD B2C 租户](tutorial-create-tenant.md)。

如果你没有 SAML 应用程序和关联的元数据终结点，可以使用我们提供的用于测试的 [SAML 测试应用程序][samltest]。

[!INCLUDE [active-directory-b2c-https-cipher-tls-requirements](../../includes/active-directory-b2c-https-cipher-tls-requirements.md)]

## <a name="set-up-certificates"></a>设置证书

若要在应用程序和 Azure AD B2C 之间建立信任关系，这两个服务必须能够创建和验证彼此的签名。 在应用程序和 Azure AD B2C 中配置 X509 证书。

**应用程序证书**

| 使用情况 | 必需 | 说明 |
| --------- | -------- | ----------- |
| SAML 请求签名  | 否 | 一个证书，其私钥存储在你的 Web 应用中。 应用程序使用该证书对发送到 Azure AD B2C 的 SAML 请求进行签名。 Web 应用必须通过其 SAML 元数据终结点公开公钥。 Azure AD B2C 使用应用程序元数据中的公钥来验证 SAML 请求签名。|
| SAML 断言加密  | 否 | 一个证书，其私钥存储在你的 Web 应用中。 Web 应用必须通过其 SAML 元数据终结点公开公钥。 Azure AD B2C 可以使用公钥在应用程序中加密断言。 应用程序使用私钥来解密断言。|

**Azure AD B2C 证书**

| 使用情况 | 必需 | 说明 |
| --------- | -------- | ----------- |
| SAML 响应签名 | 是  | 一个证书，其私钥存储在 Azure AD B2C 中。 Azure AD B2C 使用此证书对发送到应用程序的 SAML 响应进行签名。 应用程序读取 Azure AD B2C 中的元数据公钥以验证 SAML 响应的签名。 |
| SAML 断言签名 | 是 | 一个证书，其私钥存储在 Azure AD B2C 中。 Azure AD B2C 使用此证书对 SAML 响应的 `<saml:Assertion>` 部分进行签名。  |

在生产环境中，我们建议使用公共证书颁发机构颁发的证书。 不过，你也可以使用自签名证书完成此过程。

### <a name="create-a-policy-key"></a>创建策略密钥

若要在应用程序和 Azure AD B2C 之间建立信任关系，请创建 SAML 响应的签名证书。 Azure AD B2C 使用此证书对发送到应用程序的 SAML 响应进行签名。 应用程序读取 Azure AD B2C 的元数据公钥以验证 SAML 响应的签名。 

> [!TIP]
> 可以使用此策略密钥实现其他目的，例如对 [SAML 断言](saml-service-provider-options.md#check-the-saml-assertion-signature)进行签名。 

### <a name="obtain-a-certificate"></a>获得证书

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

### <a name="upload-the-certificate"></a>上传证书

需要将你的证书存储在 Azure AD B2C 租户中。

1. 登录 [Azure 门户](https://portal.azure.com/)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C”。
1. 在“概述”页上，选择“Identity Experience Framework”。
1. 选择“策略密钥”，然后选择“添加” 。
1. 对于“选项”，请选择“上传”。
1. 对于“名称”，输入策略密钥的名称。 例如，输入“SamlIdpCert”。 前缀“B2C_1A_”会自动添加到密钥名称中。
1. 浏览并选择带有私钥的证书 .pfx 文件。
1. 选择“创建”。

## <a name="enable-your-policy-to-connect-with-a-saml-application"></a>启用策略以连接 SAML 应用程序

若要连接到 SAML 应用程序，Azure AD B2C 必须能够创建 SAML 响应。

在自定义策略初学者包中打开 SocialAndLocalAccounts\TrustFrameworkExtensions.xml。

找到 `<ClaimsProviders>` 一节，并添加以下 XML 代码片段来实现 SAML 响应生成器：

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>

    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="IssuerUri">https://issuerUriMyAppExpects</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
      </CryptographicKeys>
      <InputClaims/>
      <OutputClaims/>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer"/>
    </TechnicalProfile>

    <!-- Session management technical profile for SAML-based tokens -->
    <TechnicalProfile Id="SM-Saml-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
    </TechnicalProfile>

  </TechnicalProfiles>
</ClaimsProvider>
```

#### <a name="configure-the-issuer-uri-of-the-saml-response"></a>配置 SAML 响应的颁发者 URI

可以在 SAML 令牌颁发者技术配置文件中更改 `IssuerUri` 元数据项的值。 此项更改将反映在 Azure AD B2C 做出的 SAML 响应中返回的 `issuerUri` 特性内。 配置应用程序以在 SAML 响应验证期间接受相同的 `IssuerUri` 值。

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="IssuerUri">https://issuerUriMyAppExpects</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="configure-your-policy-to-issue-a-saml-response"></a>配置策略以发出 SAML 响应

你的策略现在可以创建 SAML 响应，必须将策略配置为向应用程序发出 SAML 响应，而不是默认的 JWT 响应。

### <a name="create-a-sign-up-or-sign-in-policy-configured-for-saml"></a>创建为 SAML 配置的注册或登录策略

1. 在初学者包的工作目录中创建 SignUpOrSignin.xml 文件的副本，并使用新名称保存该副本。 本文以“SignUpOrSigninSAML.xml”为例。 此文件是你对信赖方的策略文件。 它默认配置为发出 JWT 响应。

1. 在首选编辑器中打开“SignUpOrSigninSAML.xml”文件。

1. 将策略的 `PolicyId` 和 `PublicPolicyUri` 值更改为 `_B2C_1A_signup_signin_saml_` 和 `http://<tenant-name>.onmicrosoft.com/B2C_1A_signup_signin_saml`。

    ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="tenant-name.onmicrosoft.com"
    PolicyId="B2C_1A_signup_signin_saml"
    PublicPolicyUri="http://<tenant-name>.onmicrosoft.com/B2C_1A_signup_signin_saml">
    ```

1. 用户旅程结束时，Azure AD B2C 将包含 `SendClaims` 步骤。 此步骤引用令牌颁发者技术配置文件。 若要发出 SAML 响应而不是默认的 JWT 响应，请修改 `SendClaims` 步骤，以引用新的 SAML 令牌颁发者技术配置文件 `Saml2AssertionIssuer`。

紧靠在 `<RelyingParty>` 元素的前面添加以下 XML 代码片段。 此 XML 将覆盖 SignUpOrSignIn 用户旅程中的业务流程步骤 7。 

如果已从初学者包中的不同文件夹启动，或者通过添加或删除业务流程步骤自定义了用户旅程，请确保 `order` 元素中的编号对应于用户旅程中为令牌颁发者步骤指定的编号。 例如，在其他初学者包文件夹中，`LocalAccounts`、`SocialAccounts` 和 `SocialAndLocalAccountsWithMfa` 的对应步骤编号分别为 4、6、9。

```xml
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>
      <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys>
```

信赖方元素确定应用程序使用的协议。 默认为 `OpenId`。 必须将 `Protocol` 元素更改为 `SAML`。 输出声明将创建到 SAML 断言的声明映射。

将 `<RelyingParty>` 元素中的整个 `<TechnicalProfile>` 元素替换为以下技术配置文件 XML。 将 `tenant-name` 更新为 Azure AD B2C 租户的名称。

```xml
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="objectId"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="objectId" ExcludeAsClaim="true"/>
    </TechnicalProfile>
```

信赖方的最终策略文件应如以下 XML 代码所示：

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="contoso.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin_saml"
  PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_signup_signin_saml">

  <BasePolicy>
    <TenantId>contoso.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>

  <UserJourneys>
    <UserJourney Id="SignUpOrSignIn">
      <OrchestrationSteps>
        <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
      </OrchestrationSteps>
    </UserJourney>
  </UserJourneys>

  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="objectId"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="objectId" ExcludeAsClaim="true"/>
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

> [!NOTE]
> 可以遵循上述相同过程来实现其他类型的用户流（例如登录、密码重置或配置文件编辑流）。

### <a name="upload-your-policy"></a>上传策略

保存更改，并将新的 TrustFrameworkExtensions.xml 和 SignUpOrSigninSAML.xml 策略文件上传到 Azure 门户 。

### <a name="test-the-azure-ad-b2c-idp-saml-metadata"></a>测试 Azure AD B2C IdP SAML 元数据

上传策略文件后，Azure AD B2C 将使用配置信息来生成应用程序使用的标识提供者 SAML 元数据文档。 SAML 元数据文档包含服务的位置，例如登录方法、注销方法和证书。

以下 URL 提供了 Azure AD B2C 策略元数据：

`https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/samlp/metadata`

将 `<tenant-name>` 替换为 Azure AD B2C 租户的名称。 将 `<policy-name>` 替换为策略的名称 (ID)。 下面是一个示例：

`https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1A_signup_signin_saml/samlp/metadata`

## <a name="register-your-saml-application-in-azure-ad-b2c"></a>在 Azure AD B2C 中注册 SAML 应用程序

要使 Azure AD B2C 信任你的应用程序，你需要创建一个 Azure AD B2C 应用程序注册。 注册包含配置信息，如应用程序的元数据终结点。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 在左侧菜单中，选择“Azure AD B2C”。 或者，选择“所有服务”，然后搜索并选择“Azure AD B2C”。
1. 选择“应用注册”，然后选择“新建注册” 。
1. 输入应用程序的“名称”。 例如，输入“SAMLApp1”。
1. 在“支持的帐户类型”下，选择“仅此组织目录中的帐户” 。
1. 在“重定向 URI”下，选择“Web”，然后输入 `https://localhost`。 稍后你将在应用程序注册的清单中修改此值。
1. 选择“注册”  。

### <a name="configure-your-application-in-azure-ad-b2c"></a>在 Azure AD B2C 中配置应用程序

对于 SAML 应用，需要在应用程序注册的清单中配置几个属性。

1. 在 [Azure 门户](https://portal.azure.com)中，转到在上一节中创建的应用程序注册。
1. 在“管理”下，选择“清单”以打开清单编辑器。 然后修改以下各节中介绍的属性。

#### <a name="add-the-identifier"></a>添加标识符

当 SAML 应用程序向 Azure AD B2C 发出请求时，SAML AuthN 请求将包含一个 `Issuer` 特性。 此特性的值通常与应用程序的元数据 `entityID` 值相同。 Azure AD B2C 使用此值在目录中查找应用程序注册并读取配置。 要使此查找操作成功，必须使用与 `Issuer` 特性匹配的值来填充应用程序注册中的 `identifierUri`。

在注册清单中，找到 `identifierURIs` 参数并添加适当的值。 此值将与在应用程序的 SAML AuthN 请求中为 `EntityId` 配置的值，以及应用程序元数据中的 `entityID` 值相同。

以下示例展示了 SAML 元数据中的 `entityID` 值：

```xml
<EntityDescriptor ID="id123456789" entityID="https://samltestapp2.azurewebsites.net" validUntil="2099-12-31T23:59:59Z" xmlns="urn:oasis:names:tc:SAML:2.0:metadata">
```

`identifierUris` 属性仅接受域 `tenant-name.onmicrosoft.com` 上的 URL。

```json
"identifierUris":"https://samltestapp2.azurewebsites.net",
```

#### <a name="share-the-applications-metadata-with-azure-ad-b2c"></a>与 Azure AD B2C 共享应用程序的元数据

在应用程序注册由其 `identifierUri` 值加载后，Azure AD B2C 将使用应用程序的元数据来验证 SAML AuthN 请求，并确定如何做出响应。

我们建议让应用程序公开一个可公开访问的元数据终结点。

如果在 SAML 元数据 URL 和应用程序注册的清单中都指定了一些属性，则这些属性将合并。 会优先处理元数据 URL 中指定的属性，其优先级更高。

以 SAML 测试应用程序为例，可以在应用程序清单中为 `samlMetadataUrl` 使用以下值：

```json
"samlMetadataUrl":"https://samltestapp2.azurewebsites.net/Metadata",
```

#### <a name="override-or-set-the-assertion-consumer-url-optional"></a>替代或设置断言使用者 URL（可选）

可以配置 Azure AD B2C 要向其发送 SAML 响应的回复 URL。 可以在应用程序清单中配置回复 URL。 当应用程序未公开一个可公开访问的元数据终结点时，此配置非常有用。

SAML 应用程序的回复 URL 是应用程序预期在其上接收 SAML 响应的终结点。 应用程序通常在元数据文档中的 `AssertionConsumerServiceUrl` 特性下提供此 URL，如以下示例中所示：

```xml
<SPSSODescriptor AuthnRequestsSigned="false" WantAssertionsSigned="false" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
    ...
    <AssertionConsumerService index="0" isDefault="true" Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://samltestapp2.azurewebsites.net/SP/AssertionConsumer" />        
</SPSSODescriptor>
```

如果你要替代 `AssertionConsumerServiceUrl` 特性中提供的元数据，或者该 URL 不存在于元数据文档中，则你可以在清单中的 `replyUrlsWithType` 属性下配置 URL。 `BindingType` 值将设置为 `HTTP POST`。

以 SAML 测试应用程序为例，可将 `replyUrlsWithType` 的 `url` 属性设置为以下 JSON 代码片段中显示的值：

```json
"replyUrlsWithType":[
  {
    "url":"https://samltestapp2.azurewebsites.net/SP/AssertionConsumer",
    "type":"Web"
  }
],
```

#### <a name="override-or-set-the-logout-url-optional"></a>替代或设置注销 URL（可选）

可以配置 Azure AD B2C 在收到注销请求后要将用户定向到的注销 URL。 可以在应用程序清单中配置回复 URL。

如果你要替代 `SingleLogoutService` 特性中提供的元数据，或者该 URL 不存在于元数据文档中，则你可以在清单中的 `Logout` 属性下配置该 URL。 `BindingType` 值将设置为 `Http-Redirect`。

应用程序通常在元数据文档中的 `AssertionConsumerServiceUrl` 特性下提供此 URL，如以下示例中所示：

```xml
<IDPSSODescriptor WantAuthnRequestsSigned="false" WantAssertionsSigned="false" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://samltestapp2.azurewebsites.net/logout" ResponseLocation="https://samltestapp2.azurewebsites.net/logout" />

</IDPSSODescriptor>
```

以 SAML 测试应用程序为例，你会将 `logoutUrl` 设置为 `https://samltestapp2.azurewebsites.net/logout`：

```json
"logoutUrl": "https://samltestapp2.azurewebsites.net/logout",
```

> [!NOTE]
> 如果你选择在应用程序清单中配置回复 URL 和注销 URL，而不通过 `samlMetadataUrl` 属性填充应用程序的元数据终结点，Azure AD B2C 将不会验证 SAML 请求签名。 也不会加密 SAML 响应。

## <a name="configure-azure-ad-b2c-as-a-saml-idp-in-your-saml-application"></a>在 SAML 应用程序中将 Azure AD B2C 配置为 SAML IdP

最后一步是在 SAML 应用程序中将 Azure AD B2C 启用为 SAML IdP。 应用程序各不相同，因此步骤也不相同。 有关详细信息，请参阅应用的文档。

可以在应用程序中将元数据配置为静态元数据或动态元数据 。 在静态模式下，复制 Azure AD B2C 策略元数据中的所有或一部分元数据。 在动态模式下，提供元数据的 URL 并允许应用程序动态读取元数据。

通常需要以下部分或全部内容：

* 元数据：使用格式 `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/Samlp/metadata`。
* 颁发者：SAML 请求的 `issuer` 值必须与在应用注册清单的 `identifierUris` 元素中配置的 URI 之一匹配。 如果 SAML 请求的 `issuer` 名称不存在于 `identifierUris` 元素中，则[将它添加到应用程序注册清单中](#add-the-identifier)。 例如：`https://contoso.onmicrosoft.com/app-name`。 
* 登录 URL、SAML 终结点、SAML URL：检查 Azure AD B2C SAML 策略元数据文件中 `<SingleSignOnService>` XML 元素的值。
* 证书：此证书为 B2C_1A_SamlIdpCert，但不包含私钥。 若要获取证书的公钥：

    1. 转到之前指定的元数据 URL。
    1. 复制 `<X509Certificate>` 元素中的值。
    1. 将其粘贴到文本文件中。
    1. 将该文本文件另存为 .cer 文件。

### <a name="test-with-the-saml-test-app"></a>使用 SAML 测试应用进行测试

可以使用我们的 [SAML 测试应用程序][samltest]来测试你的配置：

* 更新租户名称。
* 更新策略名称。 例如，使用 B2C_1A_signup_signin_saml。
* 指定颁发者 URI。 使用在应用程序注册清单的 `identifierUris` 元素中找到的 URI 之一。 例如，使用 `https://contoso.onmicrosoft.com/app-name`。

选择“登录”，应会显示用户登录屏幕。 登录后，SAML 响应将发回示例应用程序。

## <a name="supported-and-unsupported-saml-modalities"></a>受支持的和不支持的 SAML 形式

可通过你自己的元数据终结点支持以下 SAML 应用程序方案：

* 指定应用程序或服务主体对象中的多个注销 URL 或注销 URL 的 POST 绑定。
* 指定签名密钥，以验证应用程序或服务主体对象中的信赖方请求。
* 在应用程序或服务主体对象中指定令牌加密密钥。
* 指定 IdP 发起的登录，其中的标识提供者是 Azure AD B2C。

## <a name="next-steps"></a>后续步骤

- 从 [Azure AD B2C GitHub 社区存储库](https://github.com/azure-ad-b2c/saml-sp-tester)获取 SAML 测试 Web 应用。
- 请参阅[用于在 Azure AD B2C 中注册 SAML 应用程序的选项](saml-service-provider-options.md)。

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp

::: zone-end
