---
title: 将 Azure Active Directory B2C 配置为应用程序的 SAML IdP
title-suffix: Azure Active Directory B2C
description: 如何配置 Azure Active Directory B2C 以便向应用程序提供 SAML 协议断言 (服务提供商) 。 Azure AD B2C 将充当 SAML 应用程序的单个标识提供者 (IdP)。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/03/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 1035f43642f3884e7cc0f6ab47e9c9afd1f29170
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102107289"
---
# <a name="register-a-saml-application-in-azure-ad-b2c"></a>在 Azure AD B2C 中注册 SAML 应用程序

本文介绍如何将安全断言标记语言 (SAML) 应用程序 (服务提供商) Azure Active Directory B2C (Azure AD B2C) 进行身份验证。

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="overview"></a>概述

使用 Azure AD B2C 作为其客户标识和访问管理解决方案的组织可能需要与使用 SAML 协议进行身份验证的应用程序集成。 下图显示了 Azure AD B2C 如何作为 *标识提供者* (IdP) ，以实现基于 SAML 的应用程序的单一登录 (SSO) 。

![在左侧将 B2C 作为标识提供者的关系图作为右侧的服务提供商。](media/saml-service-provider/saml-service-provider-integration.png)

1. 应用程序创建一个 SAML 身份验证请求，该请求将发送到 Azure AD B2c SAML 登录终结点。
2. 如果配置) 进行身份验证，则用户可以使用 Azure AD B2C 本地帐户或任何其他联合身份提供程序 (。
3. 如果用户使用联合标识提供程序登录，则会将令牌响应发送到 Azure AD B2C。
4. Azure AD B2C 生成 SAML 断言并将其发送到应用程序。

## <a name="prerequisites"></a>先决条件

* 完成 [Azure AD B2C 中的自定义策略](custom-policy-get-started.md)中的步骤。 你需要本文讨论的自定义策略入门包中的 SocialAndLocalAccounts 自定义策略。
* 基本了解 SAML 协议，并熟悉应用程序的 SAML 实现。
* 配置为 SAML 应用程序的 web 应用程序。 对于本教程，你可以使用我们提供的 [SAML 测试应用程序][samltest]。

## <a name="components"></a>组件

此方案需要三个主要组件：

* 一种 SAML **应用程序** ，能够发送 saml 身份验证请求，以及接收、解码和验证 AZURE AD B2C 的 saml 响应。 SAML 应用程序也称为信赖方应用程序或服务提供商。
* SAML 应用程序的公开可用 SAML **元数据终结点** 或 XML 文档。
* [Azure AD B2C 租户](tutorial-create-tenant.md)

如果还没有 SAML 应用程序和关联的元数据终结点，则可以使用我们提供的用于测试的示例 SAML 应用程序：

[SAML 测试应用程序][samltest]

## <a name="set-up-certificates"></a>设置证书

若要在应用程序和 Azure AD B2C 之间建立信任关系，两个服务都必须能够创建和验证彼此的签名。 在 Azure AD B2C 和应用程序中配置配置 X509 证书。

**应用程序证书**

| 使用情况 | 必选 | 说明 |
| --------- | -------- | ----------- |
| SAML 请求签名  | 否 | 一个证书，其中包含存储在 web 应用中的私钥，由应用程序用来对发送到 Azure AD B2C 的 SAML 请求进行签名。 Web 应用必须通过其 SAML 元数据终结点公开公钥。 Azure AD B2C 使用应用程序元数据中的公钥来验证 SAML 请求签名。|
| SAML 断言加密  | 否 | 具有存储在 web 应用中的私钥的证书。 Web 应用必须通过其 SAML 元数据终结点公开公钥。 Azure AD B2C 可以使用公钥将断言加密到应用程序。 应用程序使用私钥来解密断言。|

**Azure AD B2C 证书**

| 使用情况 | 必选 | 说明 |
| --------- | -------- | ----------- |
| SAML 响应签名 | 是 | 具有 Azure AD B2C 中存储的私钥的证书。 Azure AD B2C 使用此证书对发送到应用程序的 SAML 响应进行签名。 应用程序读取 Azure AD B2C 的元数据公钥以验证 SAML 响应的签名。 |

在生产环境中，我们建议使用由公共证书颁发机构颁发的证书。 不过，也可以通过自签名证书完成此过程。

### <a name="prepare-a-self-signed-certificate-for-saml-response-signing"></a>为 SAML 响应签名准备自签名证书

您必须创建 SAML 响应签名证书，以便您的应用程序可以信任 Azure AD B2C 的断言。

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

## <a name="enable-your-policy-to-connect-with-a-saml-application"></a>启用策略以与 SAML 应用程序连接

若要连接到 SAML 应用程序，Azure AD B2C 必须能够创建 SAML 响应。

打开自定义策略新手包中的 `SocialAndLocalAccounts\`**`TrustFrameworkExtensions.xml`** 。

找到 `<ClaimsProviders>` 部分，然后添加以下 XML 代码片段以实现 SAML 响应生成器。

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
      </CryptographicKeys>
      <InputClaims/>
      <OutputClaims/>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer"/>
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
    </TechnicalProfile>

  </TechnicalProfiles>
</ClaimsProvider>
```

#### <a name="configure-the-issueruri-of-the-saml-response"></a>配置 SAML 响应的 IssuerUri

可以 `IssuerUri` 在 SAML 令牌颁发者技术配置文件中更改元数据项的值。 此更改将反映在 Azure AD B2C 的 `issuerUri` SAML 响应中返回的属性中。 应用程序应配置为 `issuerUri` 在 SAML 响应验证期间接受相同的配置。

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

#### <a name="sign-the-azure-ad-b2c-idp-saml-metadata-optional"></a>将 Azure AD B2C IdP SAML 元数据签名 (可选) 

如果应用程序需要，你可以指示 Azure AD B2C 对其 SAML IdP 元数据文档进行签名。 为此，请生成并上传 SAML IdP 元数据签名策略密钥，如为 [SAML 响应签名准备自签名证书](#prepare-a-self-signed-certificate-for-saml-response-signing)中所示。 然后配置 `MetadataSigning` SAML 令牌颁发者技术配置文件中的元数据项。 `StorageReferenceId`必须引用策略密钥名称。

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
        ...
      <CryptographicKeys>
        <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlMetadataCert"/>
        ...
      </CryptographicKeys>
    ...
    </TechnicalProfile>
```

#### <a name="sign-the-azure-ad-b2c-idp-saml-response-element-optional"></a>为 Azure AD B2C IdP SAML response 元素签名 (可选) 

可以指定用于对 SAML 消息进行签名的证书。 消息是 `<samlp:Response>` 发送到应用程序的 SAML 响应中的元素。

若要指定证书，请按为 [SAML 响应签名准备自签名证书](#prepare-a-self-signed-certificate-for-saml-response-signing)中所示，生成和上传策略密钥。 然后配置 `SamlMessageSigning` SAML 令牌颁发者技术配置文件中的元数据项。 `StorageReferenceId`必须引用策略密钥名称。

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
        ...
      <CryptographicKeys>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlMessageCert"/>
        ...
      </CryptographicKeys>
    ...
    </TechnicalProfile>
```
## <a name="configure-your-policy-to-issue-a-saml-response"></a>配置策略以发出 SAML 响应

现在，你的策略可以创建 SAML 响应，你必须将策略配置为发出 SAML 响应，而不是应用程序的默认 JWT 响应。

### <a name="create-a-sign-up-or-sign-in-policy-configured-for-saml"></a>创建为 SAML 配置的注册或登录策略

1. 在新手包工作目录中创建 SignUpOrSignin.xml 文件的副本，并使用新名称保存该副本。 例如，SignUpOrSigninSAML.xml。 此文件是信赖方策略文件，默认情况下它被配置为发出 JWT 响应。

1. 在首选编辑器中打开“SignUpOrSigninSAML.xml”文件。

1. 如下所示，将策略的 `PolicyId` 和 `PublicPolicyUri` 更改为 B2C_1A_signup_signin_saml 和 `http://<tenant-name>.onmicrosoft.com/B2C_1A_signup_signin_saml`。

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

1. 用户旅程结束时，Azure AD B2C 包含一个 `SendClaims` 步骤。 此步骤引用令牌颁发者技术配置文件。 若要发出 SAML 响应而不是默认的 JWT 响应，请修改 `SendClaims` 步骤以引用新的 SAML 令牌颁发者技术配置文件 `Saml2AssertionIssuer` 。

在元素之前添加以下 XML 代码段 `<RelyingParty>` 。 此 XML 将覆盖 _signuporsignin.xml_ 用户旅程中的业务流程步骤7。 如果从 starter pack 的不同文件夹中启动，或通过添加或删除业务流程步骤自定义用户旅程，请确保元素中的数字对应于 " `order` 令牌颁发者的用户旅程" 步骤中指定的数字。 例如，在其他初学者包文件夹中，对应的步骤号为4，对于) 为6，对于，则为 `LocalAccounts` `SocialAccounts` 9 `SocialAndLocalAccountsWithMfa` 。

```xml
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>
      <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys>
```

信赖方元素确定应用程序使用的协议。 默认值为 `OpenId`。 `Protocol`必须将元素更改为 `SAML` 。 输出声明将创建指向 SAML 断言的声明映射。

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

最终的信赖方策略文件应如下 XML 代码所示：

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
> 可以按照此相同的过程来实现其他类型的用户流 (例如登录、密码重置或配置文件编辑流) 。

### <a name="upload-your-policy"></a>上传策略

保存所做的更改并将新 **TrustFrameworkExtensions.xml** 和 **SignUpOrSigninSAML.xml** 策略文件上传到 Azure 门户中。

### <a name="test-the-azure-ad-b2c-idp-saml-metadata"></a>测试 Azure AD B2C IdP SAML 元数据

上传策略文件后，Azure AD B2C 将使用配置信息来生成要由应用程序使用的标识提供者的 SAML 元数据文档。 SAML 元数据文档包含服务的位置，例如登录和注销方法、证书等。

以下 URL 提供了 Azure AD B2C 策略元数据：

`https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/samlp/metadata`

将替换为 Azure AD B2C 租户的名称，将替换为 `<tenant-name>` `<policy-name>` 该策略的名称 (ID) ，例如：

`https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1A_signup_signin_saml/samlp/metadata`

## <a name="register-your-saml-application-in-azure-ad-b2c"></a>在 Azure AD B2C 中注册 SAML 应用程序

为了 Azure AD B2C 信任你的应用程序，你需要创建一个 Azure AD B2C 应用程序注册，其中包含配置信息，例如应用程序的元数据终结点。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在顶部菜单中选择“目录 + 订阅”筛选器，然后选择包含Azure AD B2C 租户的目录。
1. 在左侧菜单中，选择“Azure AD B2C”。 或者，选择“所有服务”并搜索并选择“Azure AD B2C”。
1. 选择“应用注册”，然后选择“新建注册” 。
1. 输入应用程序的“名称”。 例如，SAMLApp1。
1. 在“支持的帐户类型”下，选择“仅此组织目录中的帐户” 。
1. 在“重定向 URI”下，选择“Web”，然后输入 `https://localhost`。 稍后会在应用程序注册的清单中修改此值。
1. 选择“注册”  。

### <a name="configure-your-application-in-azure-ad-b2c"></a>在 Azure AD B2C 中配置应用程序

对于 SAML 应用，需要在应用程序注册的清单中配置几个属性。

1. 在 [Azure 门户](https://portal.azure.com)中，导航到在上一节中创建的应用程序注册。
1. 在 " **管理**" 下，选择 " **清单** " 打开清单编辑器，然后修改以下部分中描述的属性。

#### <a name="add-the-identifier"></a>添加标识符

当 SAML 应用程序发出 Azure AD B2C 请求时，SAML 身份验证请求包括一个 `Issuer` 属性，该属性通常与应用程序的元数据相同 `entityID` 。 Azure AD B2C 使用此值在目录中查找应用程序注册并读取配置。 若要成功进行此查找， `identifierUri` 必须使用与属性匹配的值填充应用程序注册中的 `Issuer` 。

在注册清单中，找到 `identifierURIs` 参数并添加相应的值。 此值将与在应用程序的 EntityId 的 SAML 身份验证请求中配置的值相同，并在 `entityID` 应用程序的元数据中配置值。

下面的示例演示了 `entityID` SAML 元数据中的：

```xml
<EntityDescriptor ID="id123456789" entityID="https://samltestapp2.azurewebsites.net" validUntil="2099-12-31T23:59:59Z" xmlns="urn:oasis:names:tc:SAML:2.0:metadata">
```

`identifierUris`属性将只接受域上的 url `tenant-name.onmicrosoft.com` 。

```json
"identifierUris":"https://samltestapp2.azurewebsites.net",
```

#### <a name="share-the-applications-metadata-with-azure-ad-b2c"></a>与 Azure AD B2C 共享应用程序的元数据

在加载应用程序注册后 `identifierUri` ，Azure AD B2C 将使用应用程序的元数据来验证 SAML 身份验证请求，并确定如何做出响应。

建议应用程序公开可公开访问的元数据终结点。

如果在 SAML 元数据 URL 和应用程序注册的清单中 *同时* 指定了属性，则会 *合并* 它们。 会优先处理元数据 URL 中指定的属性，其优先级更高。

使用 SAML 测试应用程序作为示例，你可以 `samlMetadataUrl` 在应用程序清单中使用以下值：

```json
"samlMetadataUrl":"https://samltestapp2.azurewebsites.net/Metadata",
```

#### <a name="override-or-set-the-assertion-consumer-url-optional"></a>重写或设置断言使用者 URL (可选) 

你可以配置 Azure AD B2C 向其发送 SAML 响应的回复 URL。 可以在应用程序清单中配置回复 Url。 当应用程序不公开可公开访问的元数据终结点时，此配置非常有用。

SAML 应用程序的 "回复 URL" 是应用程序预期接收 SAML 响应的终结点。 应用程序通常会在元数据文档中的属性下提供此 URL `AssertionConsumerServiceUrl` ，如下所示：

```xml
<SPSSODescriptor AuthnRequestsSigned="false" WantAssertionsSigned="false" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
    ...
    <AssertionConsumerService index="0" isDefault="true" Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://samltestapp2.azurewebsites.net/SP/AssertionConsumer" />        
</SPSSODescriptor>
```

如果要重写属性中提供的元数据 `AssertionConsumerServiceUrl` ，或者该 url 不在元数据文档中，则可以在属性中的属性下配置 url `replyUrlsWithType` 。 `BindingType`将设置为 `HTTP POST` 。

使用 SAML 测试应用程序作为示例，你可以将的 `url` 属性设置 `replyUrlsWithType` 为以下 JSON 代码段中所示的值。

```json
"replyUrlsWithType":[
  {
    "url":"https://samltestapp2.azurewebsites.net/SP/AssertionConsumer",
    "type":"Web"
  }
],
```

#### <a name="override-or-set-the-logout-url-optional"></a>重写或设置注销 URL (可选) 

可以配置注销请求后 Azure AD B2C 将发送给用户的注销 URL。 可以在应用程序清单中配置回复 Url。

如果要重写属性中提供的元数据 `SingleLogoutService` ，或者该 URL 不在元数据文档中，则可以在属性下的清单中配置它 `Logout` 。 `BindingType`将设置为 `Http-Redirect` 。

应用程序通常会在元数据文档中的属性下提供此 URL `AssertionConsumerServiceUrl` ，如下所示：

```xml
<IDPSSODescriptor WantAuthnRequestsSigned="false" WantAssertionsSigned="false" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://samltestapp2.azurewebsites.net/logout" ResponseLocation="https://samltestapp2.azurewebsites.net/logout" />

</IDPSSODescriptor>
```

使用 SAML 测试应用程序作为示例，你应该将 `logoutUrl` 设置为 `https://samltestapp2.azurewebsites.net/logout` ：

```json
"logoutUrl": "https://samltestapp2.azurewebsites.net/logout",
```

> [!NOTE]
> 如果选择在应用程序清单中配置回复 URL 和注销 URL，而不通过属性填充应用程序的元数据终结点 `samlMetadataUrl` ，Azure AD B2C 将不会验证 saml 请求签名，也不会加密 saml 响应。

## <a name="configure-azure-ad-b2c-as-a-saml-idp-in-your-saml-application"></a>在 SAML 应用程序中将 Azure AD B2C 配置为 SAML IdP

最后一步是在 SAML 应用程序中启用作为 SAML IdP 的 Azure AD B2C。 每个应用程序都不同，步骤各不相同。 有关详细信息，请参阅应用的文档。

可以在应用程序中将元数据配置为 *静态元数据* 或 *动态元数据*。 在静态模式下，从 Azure AD B2C 的策略元数据复制全部或部分元数据。 在动态模式下，为元数据提供 URL 并允许应用程序动态读取元数据。

通常需要以下部分或全部内容：

* **Metadata**：使用格式 `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/Samlp/metadata` 。
* **颁发者**：SAML 请求 `issuer` 值必须与在应用程序注册清单的 `identifierUris` 元素中配置的 URI 之一匹配。 如果 SAML 请求 `issuer` 名称不存在于 `identifierUris` 元素中，请[将其添加到应用程序注册清单中](#add-the-identifier)。 例如，`https://contoso.onmicrosoft.com/app-name`。 
* **登录 url/saml 终结点/Saml url**：检查 XML 元素 Azure AD B2C SAML 策略元数据文件中的值 `<SingleSignOnService>` 。
* **证书**：此证书已 *B2C_1A_SamlIdpCert*，但没有私钥。 若要获取证书的公钥：

    1. 请转到上面指定的元数据 URL。
    1. 复制 `<X509Certificate>` 元素中的值。
    1. 将其粘贴到文本文件中。
    1. 将该文本文件另存为 .cer 文件。

### <a name="test-with-the-saml-test-app"></a>测试 SAML 测试应用

可以使用 [SAML 测试应用程序][samltest] 测试配置：

* 更新租户名称。
* 更新策略名称，例如 *B2C_1A_signup_signin_saml*。
* 指定此颁发者 URI。 使用在应用程序注册清单的 `identifierUris` 元素中找到的 URI 之一，例如 `https://contoso.onmicrosoft.com/app-name`。

选择“登录”，然后会显示用户登录屏幕。 登录时，SAML 响应会发回给示例应用程序。

## <a name="supported-and-unsupported-saml-modalities"></a>受支持的和不支持的 SAML 形式

通过你自己的元数据终结点支持以下 SAML 应用程序方案：

* 应用程序/服务主体对象中注销 URL 的多个注销 Url 或发布绑定。
* 指定签名密钥以验证应用程序/服务主体对象中 (RP) 请求的信赖方。
* 在应用程序/服务主体对象中指定令牌加密密钥。
* 标识提供者发起的登录，Azure AD B2C 标识提供者。

## <a name="next-steps"></a>后续步骤

- 从 [Azure AD B2C GitHub 社区存储库](https://github.com/azure-ad-b2c/saml-sp-tester)获取 SAML 测试 Web 应用。
- 请参阅 [在 Azure AD B2C 中注册 SAML 应用程序的选项](saml-service-provider-options.md)

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp

::: zone-end
