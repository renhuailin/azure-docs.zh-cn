---
title: 在自定义策略中定义 SAML 技术配置文件
titleSuffix: Azure AD B2C
description: 定义采用 Azure Active Directory B2C 的自定义策略的 SAML 技术配置文件。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/20/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e0376e13c0519920a30229eafdc8fc66fb7c242f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128585551"
---
# <a name="define-a-saml-identity-provider-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>在 Azure Active Directory B2C 自定义策略中定义 SAML 标识提供者技术配置文件

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) 为 SAML 2.0 标识提供者提供支持。 本文介绍了与支持此标准化协议的声明提供程序进行交互的技术配置文件的详细信息。 使用 SAML 技术配置文件，可以与基于 SAML 的标识提供者（例如 [ADFS](./identity-provider-adfs.md) 和 [Salesforce](identity-provider-salesforce-saml.md)）联合， 这样用户就可以使用其现有的社交或企业标识登录。

## <a name="metadata-exchange"></a>元数据交换

元数据是 SAML 协议中用于公开 SAML 方（例如服务提供程序或身份提供程序）的配置的信息。 元数据定义服务的位置，例如登录和注销、证书、登录方法和其他信息。 身份提供程序使用元数据来了解如何与 Azure AD B2C 进行通信。 元数据以 XML 格式配置，并且可以用数字签名签名，以便另一方可以验证元数据的完整性。 当 Azure AD B2C 与 SAML 身份提供程序联合时，它将充当发起 SAML 请求并等待 SAML 响应的服务提供程序。 并且，在某些情况下，会接受未经请求的 SAML 身份验证，也称为标识提供者发起的身份验证。

元数据可以在双方中配置为“静态元数据”或“动态元数据”。 在静态模式下，你从一方复制整个元数据并将其设置在另一方中。 在动态模式下，你将 URL 设置为元数据，而另一方则动态读取配置。 原理是相同的，在身份提供程序中设置 Azure AD B2C 技术配置文件的元数据，并在 Azure AD B2C 中设置身份提供程序的元数据。

每个 SAML 身份提供程序都有不同的步骤来公开和设置服务提供程序，在本例的 Azure AD B2C 中，在身份提供程序中设置 Azure AD B2C 元数据。 请查看你的身份提供程序的文档，以获取有关如何执行此操作的指导。

以下示例显示 Azure AD B2C 技术配置文件的 SAML 元数据的 URL 地址：

```
https://your-tenant-name.b2clogin.com/your-tenant-name/your-policy/samlp/metadata?idptp=your-technical-profile
```

请替换以下值：

- 将 **your-tenant-name** 替换为你的租户名称，例如 fabrikam.b2clogin.com。
- 将 your-policy 替换为你的策略名称。 使用配置 SAML 提供者技术配置文件的策略或从该策略继承的策略。
- 将 **your-technical-profile** 替换为 SAML 标识提供者技术配置文件名称。

## <a name="digital-signing-certificates-exchange"></a>数字签名证书交换

若要在 Azure AD B2C 与 SAML 身份提供程序之间建立信任，你需要提供带有私钥的有效 X509 证书。 将带私钥的证书（.pfx 文件）上传到 Azure AD B2C 策略密钥存储库。 Azure AD B2C 使用你提供的证书对 SAML 登录请求进行数字签名。

可通过以下方式使用证书：

- Azure AD B2C 使用证书的 Azure AD B2C 私钥生成 SAML 请求并对其进行签名。 SAML 请求将发送到身份提供程序，该提供程序使用证书的 Azure AD B2C 公钥验证请求。 通过技术配置文件元数据可访问 Azure AD B2C 公用证书。 或者，可以将 .cer 文件手动上传到 SAML 身份提供程序。
- 身份提供程序使用身份提供程序的证书私钥对发送到 Azure AD B2C 的数据进行签名。 Azure AD B2C 会使用身份提供程序的公共证书验证数据。 每个标识提供者都有不同的设置步骤，请查看标识提供者的文档，以获取有关如何执行此操作的指导。 在 Azure AD B2C 中，你的策略需要使用身份提供程序的元数据访问证书公钥。

在大多数情况下，自签名证书都是可接受的。 对于生产环境，建议使用由证书颁发机构颁发的 X509 证书。 此外，如本文档后面所述，对于非生产环境，你可以禁用两端的 SAML 签名。

下图显示了元数据和证书交换：

![元数据和证书交换](media/saml-technical-profile/technical-profile-idp-saml-metadata.png)

## <a name="digital-encryption"></a>数字加密

若要加密 SAML 响应断言，身份提供程序应始终在 Azure AD B2C 技术配置文件中使用加密证书的公钥。 当 Azure AD B2C 需要对数据进行解密时，它使用加密证书的专用部分。

若要加密 SAML 响应断言：

1. 将带私钥的有效 X509 证书（.pfx 文件）上传到 Azure AD B2C 策略密钥存储库。
2. 将标识符为 `SamlAssertionDecryption` 的 CryptographicKey 元素添加到技术配置文件 CryptographicKeys 集合。 将 StorageReferenceId 设为在步骤 1 中创建的策略密钥的名称。
3. 将技术配置文件元数据 WantsEncryptedAssertions 设为 `true`。
4. 使用新的 Azure AD B2C 技术配置文件元数据更新身份提供程序。 你应该会看到 KeyDescriptor，其中的“使用”属性设置为包含你的证书的公钥的 `encryption`。

以下示例显示了用于加密的 SAML 元数据的“密钥描述符”部分：

```xml
<KeyDescriptor use="encryption">
  <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
    <X509Data>
      <X509Certificate>valid certificate</X509Certificate>
    </X509Data>
  </KeyInfo>
</KeyDescriptor>
```

## <a name="protocol"></a>协议

“协议”元素的“名称”属性必须设置为 `SAML2`。

## <a name="input-claims"></a>输入声明

InputClaims 元素用于在 SAML AuthN 请求的 Subject 内发送 NameId。 为此，请添加一个输入声明并将 PartnerClaimType 设为 `subject`，如下所示。

```xml
<InputClaims>
    <InputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="subject" />
</InputClaims>
```

## <a name="output-claims"></a>输出声明

**OutputClaims** 元素在 `AttributeStatement` 节下包含 SAML 标识提供者返回的声明列表。 可能需要将策略中定义的声明名称映射到标识提供者中定义的名称。 只要设置了 `DefaultValue` 属性，就还可以包含标识提供者不会返回的声明。

### <a name="subject-name-output-claim"></a>使用者名称输出声明

若要将 Subject 中的 SAML 断言 NameId 读取为规范化声明，请将声明 PartnerClaimType 设置为 `SPNameQualifier` 属性的值。 如果未提供 `SPNameQualifier` 属性，请将声明 PartnerClaimType 设置为 `NameQualifier` 属性的值。 


SAML 断言： 

```xml
<saml:Subject>
  <saml:NameID SPNameQualifier="http://your-idp.com/unique-identifier" Format="urn:oasis:names:tc:SAML:2.0:nameid-format:transient">david@contoso.com</saml:NameID>
  <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
    <SubjectConfirmationData InResponseTo="_cd37c3f2-6875-4308-a9db-ce2cf187f4d1" NotOnOrAfter="2020-02-15T16:23:23.137Z" Recipient="https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer" />
    </SubjectConfirmation>
  </saml:SubjectConfirmation>
</saml:Subject>
```

输出声明：

```xml
<OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="http://your-idp.com/unique-identifier" />
```

如果 `SPNameQualifier` 和 `NameQualifier` 属性都未在 SAML 断言中出现，请将声明 PartnerClaimType 设置为 `assertionSubjectName`。 确保 **NameId** 是断言 XML 中的第一个值。 定义多个断言时，Azure AD B2C 会选取上一个声明中的主题值。

以下示例演示 SAML 标识提供者返回的声明：

- issuerUserId 声明映射到 assertionSubjectName 声明。
- **first_name** 声明已映射到 **givenName** 声明。
- **last_name** 声明已映射到 **surname** 声明。
- displayName 声明已映射到 name 声明。
- 没有名称映射的 **email** 声明。

技术配置文件还会返回标识提供者不返回的声明：

- **identityProvider** 声明，其中包含标识提供者的名称。
- **authenticationSource** 声明，其默认值为 **socialIdpAuthentication**。

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="assertionSubjectName" />
  <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
  <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email"  />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

**OutputClaimsTransformations** 元素可能包含用于修改输出声明或生成新输出声明的 **OutputClaimsTransformation** 元素集合。

## <a name="metadata"></a>元数据

| 属性 | 必需 | 说明 |
| --------- | -------- | ----------- |
| PartnerEntity | 是 | SAML 身份提供程序的元数据的 URL。 或者复制身份提供程序元数据并将其嵌入到 CDATA 元素 `<![CDATA[Your IDP metadata]]>`。 不建议嵌入标识提供者元数据。 标识提供者可以更改设置或更新证书。 如果标识提供者元数据已更改，请获取新元数据，然后使用新元数据更新策略。 |
| WantsSignedRequests | 否 | 指示技术配置文件是否要求对所有传出身份验证请求进行签名。 可能的值：`true` 或 `false`。 默认值为 `true`。 当该值设置为 `true` 时，需要指定 SamlMessageSigning 加密密钥，并对所有传出的身份验证请求进行签名。 如果该值设置为 `false`，则请求中将省略 SigAlg 和 Signature 参数（查询字符串或 post 参数）。 此元数据还控制元数据的 AuthnRequestsSigned 属性，该属性在与身份提供程序共享的 Azure AD B2C 技术配置文件的元数据中输出。 如果技术配置文件元数据中的 **WantsSignedRequests** 的值设置为 `false` 且标识提供者元数据 **WantAuthnRequestsSigned** 设置为 `false` 或未指定，则 Azure AD B2C 不会对请求签名。 |
| XmlSignatureAlgorithm | 否 | Azure AD B2C 用于对 SAML 请求进行签名的方法。 此元数据控制 SAML 请求中 SigAlg 参数（查询字符串或 post 参数）的值。 可能的值：`Sha256`、`Sha384`、`Sha512` 或 `Sha1`（默认值）。 确保在两端配置具有相同值的签名算法。 仅使用证书支持的算法。 |
| WantsSignedAssertions | 否 | 指示技术配置文件是否要求对所有传入断言进行签名。 可能的值：`true` 或 `false`。 默认值为 `true`。 如果该值设置为 `true`，则身份提供程序发送到 Azure AD B2C 的所有断言部分 `saml:Assertion` 必须进行签名。 如果该值设置为 `false`，则身份提供程序不应对断言进行签名，但即使这样做，Azure AD B2C 也不会验证签名。 此元数据还控制元数据标记的 WantsAssertionsSigned 属性，该属性在与身份提供程序共享的 Azure AD B2C 技术配置文件的元数据中输出。 如果禁用断言验证，则还可能想要禁用响应签名验证（有关详细信息，请参阅 ResponsesSigned）。 |
| ResponsesSigned | 否 | 可能的值：`true` 或 `false`。 默认值为 `true`。 如果该值设置为 `false`，则身份提供程序不应对 SAML 响应进行签名，但即使这样做，Azure AD B2C 也不会验证签名。 如果该值设置为 `true`，则身份提供程序发送到 Azure AD B2C 的 SAML 响应已签名，且必须进行验证。 如果禁用 SAML 响应验证，则还可能想要禁用断言签名验证（有关详细信息，请参阅 WantsSignedAssertions）。 |
| WantsEncryptedAssertions | 否 | 指示技术配置文件是否要求对所有传入断言进行加密。 可能的值：`true` 或 `false`。 默认值为 `false`。 如果该值设置为 `true`，则身份提供程序发送到 Azure AD B2C 的断言必须进行签名，并且需要指定 SamlAssertionDecryption 加密密钥。 如果该值设置为 `true`，则 Azure AD B2C 技术配置文件的元数据要包括“加密”部分。 身份提供程序读取元数据并使用 Azure AD B2C 技术配置文件的元数据中提供的公钥加密 SAML 响应断言。 如果启用断言加密，则还可能需要禁用响应签名验证（有关详细信息，请参阅 ResponsesSigned）。 |
| NameIdPolicyFormat | 否 | 指定要使用的名称标识符上的约束，使之代表请求的主题。 如果省略此项，则可使用请求的主题对应的标识提供者支持的任何类型的标识符。 例如，`urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`。 **NameIdPolicyFormat** 可以与 **NameIdPolicyAllowCreate** 配合使用。 查看标识提供者的文档，了解哪些名称 ID 策略受支持。 |
| NameIdPolicyAllowCreate | 否 | 使用 **NameIdPolicyFormat** 时，也可指定 **NameIDPolicy** 的 `AllowCreate` 属性。 此元数据的值为 `true` 或 `false`，表示是否允许标识提供者在登录流程中创建新帐户。 请查看你的身份提供程序的文档，以获取有关如何执行此操作的指导。 |
| AuthenticationRequestExtensions | 否 | Azure AD BC 和标识提供者认可的可选协议消息扩展元素。 此扩展以 XML 格式呈现。 将 XML 数据添加到 CDATA 元素 `<![CDATA[Your IDP metadata]]>` 中。 检查标识提供者的文档，看扩展元素是否受支持。 |
| IncludeAuthnContextClassReferences | 否 | 指定一个或多个可标识身份验证上下文类的 URI 引用。 例如，如果只允许用户使用用户名和密码登录，请将值设置为 `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`。 若要允许用户在受保护会话 (SSL/TLS) 期间通过用户名和密码登录，请指定 `PasswordProtectedTransport`。 查看标识提供者的文档，了解受支持的 **AuthnContextClassRef** URI。 以逗号分隔列表的形式指定多个 URI。 |
| IncludeKeyInfo | 否 | 指定在将绑定设置为 `HTTP-POST` 时，SAML 身份验证请求是否包含证书的公钥。 可能的值：`true` 或 `false`。 |
| IncludeClaimResolvingInClaimsHandling  | 否 | 对于输入和输出声明，指定[声明解析](claim-resolver-overview.md)是否包含在技术配置文件中。 可能的值：`true` 或 `false`（默认值）。 若要使用技术配置文件中的声明解析程序，请将此项设为 `true`。 |
|SingleLogoutEnabled| 否| 指示在登录过程中技术配置文件是否尝试从联合标识提供者注销。 有关详细信息，请参阅 [Azure AD B2C 会话注销](session-behavior.md#sign-out)。可能的值为 `true`（默认）或 `false`。|
|ForceAuthN| 否| 在 SAML 身份验证请求中传递 ForceAuthN 值，以确定是否会强制外部 SAML IDP 提示用户进行身份验证。 默认情况下，Azure AD B2C 在初始登录时将 ForceAuthN 值设置为 false。 如果随后重置会话（例如，通过在 OIDC 中使用 `prompt=login` 来这样做），则 ForceAuthN 值将设置为 `true`。 如下所示设置元数据项会强制设置向外部 IDP 发出的所有请求的值。  可能的值：`true` 或 `false`。|


## <a name="cryptographic-keys"></a>加密密钥

<**CryptographicKeys**> 元素包含以下属性：

| 属性 |必需 | 说明 |
| --------- | ----------- | ----------- |
| SamlMessageSigning |是 | X509 证书（RSA 密钥集），用于对 SAML 消息进行签名。 Azure AD B2C 使用此密钥对请求进行签名并将其发送给身份提供程序。 |
| SamlAssertionDecryption |否 | X509 证书（RSA 密钥集）。 SAML 标识提供程序使用证书的公共部分来加密 SAML 响应的断言。 Azure AD B2C 使用证书的专用部分来解密断言。 |
| MetadataSigning |否 | X509 证书（RSA 密钥集），用于对 SAML 元数据进行签名。 Azure AD B2C 使用此密钥对元数据进行签名。  |

## <a name="saml-entityid-customization"></a>SAML entityID 自定义

如果有多个依赖于不同 entityID 值的 SAML 应用程序，可以重写信赖方文件中的 `issueruri` 值。 为此，请从基础文件复制 ID 为“Saml2AssertionIssuer”的技术配置文件，重写 `issueruri` 值。

> [!TIP]
> 从基础文件中复制 `<ClaimsProviders>` 节，并在声明提供程序中保留这些元素：`<DisplayName>Token Issuer</DisplayName>`、`<TechnicalProfile Id="Saml2AssertionIssuer">` 和 `<DisplayName>Token Issuer</DisplayName>`。
 
示例：

```xml
   <ClaimsProviders>   
    <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Saml2AssertionIssuer">
          <DisplayName>Token Issuer</DisplayName>
          <Metadata>
            <Item Key="IssuerUri">customURI</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
  </ClaimsProviders>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpInSAML" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2" />
      <Metadata>
     …
```

## <a name="next-steps"></a>后续步骤

请参阅以下文章，了解在 Azure AD B2C 中使用 SAML 标识提供者的示例：

- [使用自定义策略将 ADFS 添加为 SAML 身份提供程序](identity-provider-adfs.md)
- [通过 SAML 使用 Salesforce 帐户登录](identity-provider-salesforce-saml.md)
