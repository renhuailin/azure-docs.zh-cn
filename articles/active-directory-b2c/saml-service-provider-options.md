---
title: 配置 SAML 服务提供者选项
title-suffix: Azure Active Directory B2C
description: 了解如何配置 Azure Active Directory B2C SAML 服务提供程序选项。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/05/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 51672c7757e5c747bf2b243e32506159a468f2d5
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123222164"
---
# <a name="options-for-registering-a-saml-application-in-azure-ad-b2c"></a>用于在 Azure AD B2C 中注册 SAML 应用程序的选项

本文介绍将 Azure Active Directory B2C (Azure AD B2C) 与安全断言标记语言 (SAML) 应用程序连接时可用的配置选项。

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"


## <a name="specify-a-saml-response-signature"></a>指定 SAML 响应签名

可以指定用于对 SAML 消息进行签名的证书。 消息是发送到应用程序的 SAML 响应中的 `<samlp:Response>` 元素。

如果你没有策略密钥，请[创建一个](saml-service-provider.md#create-a-policy-key)。 然后在 SAML 令牌颁发者技术配置文件中配置 `SamlMessageSigning` 元数据项。 `StorageReferenceId` 必须引用策略密钥名称。

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

### <a name="signature-algorithm"></a>签名算法

可以配置用于对 SAML 断言进行签名的签名算法。 可能的值为 `Sha256`、`Sha384`、`Sha512` 或 `Sha1`。 请确保技术配置文件和应用程序使用相同的签名算法。 仅使用证书支持的算法。

使用信赖方 `Metadata` 元素中的 `XmlSignatureAlgorithm` 元数据密钥配置签名算法。

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="XmlSignatureAlgorithm">Sha256</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

## <a name="check-the-saml-assertion-signature"></a>检查 SAML 断言签名

当应用程序要求对 SAML 断言部分签名时，请确保 SAML 服务提供程序将 `WantAssertionsSigned` 设置为 `true`。 如果设置为 `false` 或不存在，则不会对断言部分进行签名。 

以下示例演示了 `WantAssertionsSigned` 设置为 `true` 的 SAML 服务提供程序的元数据。

```xml
<EntityDescriptor ID="id123456789" entityID="https://samltestapp2.azurewebsites.net" validUntil="2099-12-31T23:59:59Z" xmlns="urn:oasis:names:tc:SAML:2.0:metadata">
  <SPSSODescriptor  WantAssertionsSigned="true" AuthnRequestsSigned="false" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
  ...
  </SPSSODescriptor>
</EntityDescriptor>
```  

### <a name="signature-certificate"></a>签名证书

策略必须指定一个用于对 SAML 响应的 SAML 断言部分进行签名的证书。 如果你没有策略密钥，请[创建一个](saml-service-provider.md#create-a-policy-key)。 然后在 SAML 令牌颁发者技术配置文件中配置 `SamlAssertionSigning` 元数据项。 `StorageReferenceId` 必须引用策略密钥名称。

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
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SamlMessageCert"/>
        ...
      </CryptographicKeys>
    ...
    </TechnicalProfile>
```

## <a name="enable-encryption-in-saml-assertions"></a>在 SAML 断言中启用加密

当应用程序要求 SAML 断言采用加密格式时，确保在 Azure AD B2C 策略中启用加密。

Azure AD B2C 使用服务提供程序的公钥证书来加密 SAML 断言。 公钥必须存在于 SAML 应用程序的元数据终结点中，并将 `KeyDescriptor` `use` 值设置为 `Encryption`，如以下示例中所示：

```xml
<KeyDescriptor use="encryption">
  <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
    <X509Data>
      <X509Certificate>valid certificate</X509Certificate>
    </X509Data>
  </KeyInfo>
</KeyDescriptor>
```

要使 Azure AD B2C 能够发送加密断言，请在[信赖方技术配置文件](relyingparty.md#technicalprofile)中将 `WantsEncryptedAssertion` 元数据项设置为 `true`。 你也可以配置用于加密 SAML 断言的算法。

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="WantsEncryptedAssertions">true</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

### <a name="encryption-method"></a>加密方法

若要配置用于加密 SAML 断言数据的加密方法，请在信赖方中设置 `DataEncryptionMethod` 元数据密钥。 可能的值为 `Aes256` （默认）、`Aes192`、`Sha512` 或 `Aes128`。 此元数据控制 SAML 响应中 `<EncryptedData>` 元素的值。

要配置加密方法来加密用于加密 SAML 断言数据的密钥副本，请在信赖方中设置 `KeyEncryptionMethod` 元数据密钥。 可能的值包括：

- `Rsa15`（默认值）： RSA 公钥加密标准 (PKCS) 版本 1.5 算法。
- `RsaOaep`：RSA 最佳非对称加密填充 (OAEP) 加密算法。  

此元数据控制 SAML 响应中 `<EncryptedKey>` 元素的值。

下面的示例演示 SAML 断言的 `EncryptedAssertion` 部分。 加密数据方法为 `Aes128`，加密密钥方法为 `Rsa15`。

```xml
<saml:EncryptedAssertion>
  <xenc:EncryptedData xmlns:xenc="http://www.w3.org/2001/04/xmlenc#"
    xmlns:dsig="http://www.w3.org/2000/09/xmldsig#" Type="http://www.w3.org/2001/04/xmlenc#Element">
    <xenc:EncryptionMethod Algorithm="http://www.w3.org/2001/04/xmlenc#aes128-cbc" />
    <dsig:KeyInfo>
      <xenc:EncryptedKey>
        <xenc:EncryptionMethod Algorithm="http://www.w3.org/2001/04/xmlenc#rsa-1_5" />
        <xenc:CipherData>
          <xenc:CipherValue>...</xenc:CipherValue>
        </xenc:CipherData>
      </xenc:EncryptedKey>
    </dsig:KeyInfo>
    <xenc:CipherData>
      <xenc:CipherValue>...</xenc:CipherValue>
    </xenc:CipherData>
  </xenc:EncryptedData>
</saml:EncryptedAssertion>
```

可以更改加密断言的格式。 若要配置加密格式，请在信赖方中设置 `UseDetachedKeys` 元数据密钥。 可能的值：`true` 或 `false`（默认值）。 如果将值设置为 `true`，则已分离的键会将加密断言添加为 `EncryptedAssertion`（而不是 `EncryptedData`）的子级。

通过使用[信赖方技术配置文件](relyingparty.md#technicalprofile)的元数据密钥配置加密方法和格式：

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="DataEncryptionMethod">Aes128</Item>
      <Item Key="KeyEncryptionMethod">Rsa15</Item>
      <Item Key="UseDetachedKeys">false</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

## <a name="configure-idp-initiated-flow"></a>配置 IdP 发起的流

如果应用程序需要在不首先向标识提供程序 (IdP) 发送 SAML AuthN 请求的情况下接收 SAML 断言，则必须为 IdP 发起的流配置 Azure AD B2C。

在 IdP 发起的流中，标识提供程序 (Azure AD B2C) 启动登录过程。 标识提供程序将未经请求的 SAML 响应发送到服务提供程序（你的信赖方应用程序）。

目前尚不支持发起标识提供程序是使用 Azure AD B2C 进行联合身份验证的外部标识提供程序的场景，例如 [Active Directory 联合身份验证服务](identity-provider-adfs.md)或 [Salesforce](identity-provider-salesforce-saml.md)。 IdP 发起的流仅支持在 Azure AD B2C 中进行本地帐户身份验证。

要启用 IdP 发起的流，请在[信赖方技术配置文件](relyingparty.md#technicalprofile)中将 `IdpInitiatedProfileEnabled` 元数据项设置为 `true`。

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="IdpInitiatedProfileEnabled">true</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

要通过 IdP 发起的流使用户登录或将用户注册，请使用以下 URL：

```
https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/generic/login?EntityId=app-identifier-uri 
```

请替换以下值：

* 将 `<tenant-name>` 替换为你的租户名称。
* 将 `<policy-name>` 替换为 SAML 信赖方策略的名称。
* 在元数据文件中，将 `app-identifier-uri` 替换为 `identifierUris`，例如 `https://contoso.onmicrosoft.com/app-name`。

### <a name="sample-policy"></a>示例策略

你可以使用一个完整示例策略，通过 SAML 测试应用进行测试：

1. 下载 [SAML-SP 启动的登录示例策略](https://github.com/azure-ad-b2c/saml-sp/tree/master/policy/SAML-SP-Initiated)。
1. 更新 `TenantId` 以匹配你的租户名称。 本文使用示例 contoso.b2clogin.com。
1. 保留策略名称 B2C_1A_signup_signin_saml。

## <a name="configure-the-saml-response-lifetime"></a>配置 SAML 响应生存期

可以配置 SAML 响应保持有效的时长。 使用 SAML 令牌颁发者技术配置文件中的 `TokenLifeTimeInSeconds` 元数据项设置生存期。 此值是在令牌颁发时计算的 `NotBefore` 时间戳经过的秒数。 默认生存期是 300 秒（5 分钟）。

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="TokenLifeTimeInSeconds">400</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="configure-the-time-skew-of-a-saml-response"></a>配置 SAML 响应的时间偏差

可以配置应用于 SAML 响应 `NotBefore` 时间戳的时间偏差。 此配置可确保在两个平台之间的时间不同步时，SAML 断言在此时间偏差内仍将被视为有效。

使用 SAML 令牌颁发者技术配置文件中的 `TokenNotBeforeSkewInSeconds` 元数据项设置时间偏差。 偏差值以秒为单位，默认值为 0。 最大值为 3600（1 小时）。

例如，如果 `TokenNotBeforeSkewInSeconds` 设置为 `120` 秒：

- 令牌在 13:05:10 UTC 颁发。
- 令牌从 13:03:10 UTC 生效。

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="TokenNotBeforeSkewInSeconds">120</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="remove-milliseconds-from-the-date-and-time"></a>从日期和时间中删除毫秒

可以指定是否将从 SAML 响应的日期和时间值中删除毫秒值。 （这些值包括 `IssueInstant`、`NotBefore`、`NotOnOrAfter` 和 `AuthnInstant`）。若要删除毫秒，请在信赖方中设置 `RemoveMillisecondsFromDateTime` 元数据密钥。 可能的值：`false`（默认值）或 `true`。

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="RemoveMillisecondsFromDateTime">true</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="use-an-issuer-id-to-override-an-issuer-uri"></a>使用颁发者 ID 替代颁发者 URI

如果有多个依赖于不同 `entityID` 值的 SAML 应用程序，可以重写信赖方文件中的 `IssuerUri` 值。 如需替代证书颁发者 URI，请从基础文件复制 ID 为 `Saml2AssertionIssuer` 的技术配置文件，并重写 `IssuerUri` 值。

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

## <a name="manage-a-session"></a>管理会话

可以使用 `UseTechnicalProfileForSessionManagement` 元素和 [SamlSSOSessionProvider](custom-policy-reference-sso.md#samlssosessionprovider) 管理 Azure AD B2C 和 SAML 信赖方应用之间的会话。

## <a name="force-users-to-reauthenticate"></a>强制用户重新进行身份验证 

若要强制用户重新进行身份验证，应用程序可以在 SAML 身份验证请求中包含 `ForceAuthn` 特性。 `ForceAuthn` 属性是布尔值。 如果设置为 `true`，则用户的会话将在 Azure AD B2C 中失效，并强制用户重新进行身份验证。 

以下 SAML 身份验证请求演示了如何将 `ForceAuthn` 属性设置为 `true`。 

```xml
<samlp:AuthnRequest 
       Destination="https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1A_SAML2_signup_signin/samlp/sso/login"
       ForceAuthn="true" ...>
    ...
</samlp:AuthnRequest>
```

## <a name="sign-the-azure-ad-b2c-idp-saml-metadata"></a>对 Azure AD B2C IdP SAML 元数据进行签名

如果应用程序需要，可以指示 Azure AD B2C 为 SAML 标识提供程序签署其元数据文档。 如果你没有策略密钥，请[创建一个](saml-service-provider.md#create-a-policy-key)。 然后在 SAML 令牌颁发者技术配置文件中配置 `MetadataSigning` 元数据项。 `StorageReferenceId` 必须引用策略密钥名称。

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

## <a name="debug-the-saml-protocol"></a>调试 SAML 协议

若要帮助配置和调试与服务提供程序的集成，可以使用 SAML 协议的浏览器扩展。 浏览器扩展包括 [Chrome 的 SAML DevTools 扩展](https://chrome.google.com/webstore/detail/saml-devtools-extension/jndllhgbinhiiddokbeoeepbppdnhhio)、[FireFox 的 SAML 跟踪程序](https://addons.mozilla.org/es/firefox/addon/saml-tracer/)或 [Edge 或 Internet 的开发人员工具](https://techcommunity.microsoft.com/t5/microsoft-sharepoint-blog/gathering-a-saml-token-using-edge-or-ie-developer-tools/ba-p/320957)。

使用这些工具，可以检查应用程序和 Azure AD B2C 之间的集成。 例如：

* 检查 SAML 请求是否包含签名，并确定用于登录授权请求的算法。
* 检查 Azure AD B2C 是否返回错误消息。
* 检查断言部分是否已加密。

## <a name="next-steps"></a>后续步骤

- 有关 SAML 协议的详细信息，请参阅 [OASIS 网站](https://www.oasis-open.org/)。
- 从 [Azure AD B2C GitHub 社区存储库](https://github.com/azure-ad-b2c/saml-sp-tester)获取 SAML 测试 Web 应用。

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp

::: zone-end
