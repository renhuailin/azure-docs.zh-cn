---
title: 配置 SAML 服务提供程序选项
title-suffix: Azure Active Directory B2C
description: 如何配置 Azure Active Directory B2C SAML 服务提供程序选项
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/04/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: adfe5318949ffa624ebe3548944b558bd0dda9e1
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102198466"
---
# <a name="options-for-registering-a-saml-application-in-azure-ad-b2c"></a>用于在 Azure AD B2C 中注册 SAML 应用程序的选项

本文介绍将 Azure Active Directory (Azure AD B2C) 与 SAML 应用程序连接时可用的配置选项。

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="encrypted-saml-assertions"></a>加密 SAML 断言

当应用程序要求 SAML 断言采用加密格式时，需要确保在 Azure AD B2C 策略中启用加密。

Azure AD B2C 使用服务提供商的公钥证书来加密 SAML 断言。 公钥必须存在于 SAML 应用程序的元数据终结点中，并将 KeyDescriptor "use" 设置为 "Encryption"，如以下示例中所示：

```xml
<KeyDescriptor use="encryption">
  <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
    <X509Data>
      <X509Certificate>valid certificate</X509Certificate>
    </X509Data>
  </KeyInfo>
</KeyDescriptor>
```

若要使 Azure AD B2C 发送加密的断言，请在[信赖方技术配置文件](relyingparty.md#technicalprofile)中将 WantsEncryptedAssertion 元数据项设置为 `true`。 你也可以配置用于加密 SAML 断言的算法。

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

若要配置用于加密 SAML 断言数据的加密方法，请在 `DataEncryptionMethod` 依赖方内设置元数据密钥。 可能的值是 `Aes256` (默认值) 、 `Aes192` 、 `Sha512` 或 `Aes128` 。 此元数据控制 SAML 响应中 `<EncryptedData>` 元素的值。

若要配置用于加密 SAML 断言数据的密钥副本所使用的加密方法，请在 `KeyEncryptionMethod` 依赖方内设置元数据密钥。 可能的值为 `Rsa15` (默认) -Rsa 公钥加密标准 (PKCS) 版本1.5 算法，以及 `RsaOaep` -Rsa 最佳非对称加密填充 (OAEP) 加密算法。  此元数据控制 SAML 响应中 `<EncryptedKey>` 元素的值。

下面的示例演示 `EncryptedAssertion` SAML 断言的部分。 加密的数据方法为 `Aes128` ，并且加密密钥方法为 `Rsa15` 。

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

您可以更改加密断言的格式。 若要配置加密格式，请在 `UseDetachedKeys` 信赖方中设置元数据密钥。 可能的值：`true` 或 `false`（默认值）。 如果将值设置为 `true` ，则已分离的键会将加密断言添加为的子级， `EncrytedAssertion` 而不是 `EncryptedData` 。

配置加密方法和格式，使用 [信赖方技术配置文件](relyingparty.md#technicalprofile)中的元数据密钥：

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

## <a name="identity-provider-initiated-flow"></a>标识提供程序启动的流

如果你的应用程序需要在不首先向标识提供程序发送 SAML 身份验证请求的情况下接收 SAML 断言，则必须为标识提供程序启动的流配置 Azure AD B2C。

在标识提供程序启动的流中，标识提供程序 (Azure AD B2C) 启动登录过程，该过程会将未经请求的 SAML 响应发送给信赖方应用) 程序的服务提供程序 (。

目前尚不支持初始标识提供者是与 Azure AD B2C （例如 [AD FS](identity-provider-adfs.md)或 [Salesforce](identity-provider-salesforce-saml.md)）联合的外部标识提供者的方案。 仅支持 Azure AD B2C 本地帐户身份验证。

若要启用标识提供者启动的流，请将 " **IdpInitiatedProfileEnabled** metadata" 项设置为 `true` " [信赖方技术配置文件](relyingparty.md#technicalprofile)"。

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

若要登录或通过标识提供者启动的流注册用户，请使用以下 URL：

```
https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/generic/login?EntityId=app-identifier-uri 
```

请替换以下值：

* 将 tenant-name 替换为你的租户名称
* 将 policy-name 替换为你的 SAML 信赖方策略名称
* 将 app-identifier-uri 替换为元数据文件中的 `identifierUris`，例如 `https://contoso.onmicrosoft.com/app-name`

### <a name="sample-policy"></a>示例策略

我们提供了一个可用于测试 SAML 测试应用的完整示例策略。

1. 下载 [SAML SP 启动的登录示例策略](https://github.com/azure-ad-b2c/saml-sp/tree/master/policy/SAML-SP-Initiated)。
1. 更新 `TenantId` 以匹配租户名称，例如 *contoso.b2clogin.com*。
1. 保留策略名称 *B2C_1A_signup_signin_saml*。

## <a name="saml-response-signature-algorithm"></a>SAML 响应签名算法

你可以配置用于对 SAML 断言进行签名的签名算法。 可能的值为 `Sha256`、`Sha384`、`Sha512` 或 `Sha1`。 请确保技术配置文件和应用程序使用相同的签名算法。 仅使用证书支持的算法。

使用 `XmlSignatureAlgorithm` 信赖方 metadata 元素中的元数据密钥配置签名算法。

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

## <a name="saml-response-lifetime"></a>SAML 响应生存期

你可以配置 SAML 响应保持有效的时间长度。 使用 `TokenLifeTimeInSeconds` SAML 令牌颁发者技术配置文件中的元数据项设置生存期。 此值是 `NotBefore` 在令牌颁发时间计算的时间戳可以经过的秒数。 默认生存期为300秒 (5 分钟) 。

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

## <a name="saml-response-valid-from-skew"></a>SAML 响应对于偏斜有效

可以配置应用于 SAML 响应时间戳的时间偏差 `NotBefore` 。 此配置可确保在两个平台之间的时间不同步时，SAML 断言在此时间偏差内仍将被视为有效。

使用 `TokenNotBeforeSkewInSeconds` SAML 令牌颁发者技术配置文件中的元数据项设置时间倾斜。 以秒为单位给出了倾斜值，默认值为0。 最大值为 3600（1 小时）。

例如，如果 `TokenNotBeforeSkewInSeconds` 设置为 `120` seconds：

- 令牌在 13:05:10 UTC 发出
- 该令牌的有效时间为 13:03:10 UTC

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

## <a name="remove-milliseconds-from-date-and-time"></a>删除日期和时间的毫秒

您可以指定是否要在 SAML 响应中从 datetime 值中删除毫秒， (包括 IssueInstant、NotBefore、NotOnOrAfter 和 AuthnInstant) 。 若要删除毫秒，请在 `RemoveMillisecondsFromDateTime
` 依赖方内设置元数据密钥。 可能的值：`false`（默认值）或 `true`。

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

## <a name="azure-ad-b2c-issuer-id"></a>Azure AD B2C 颁发者 ID

如果有多个依赖不同值的 SAML 应用程序 `entityID` ，则可以覆盖 `issueruri` 信赖方文件中的值。 若要替代颁发者 URI，请将技术配置文件的 "Saml2AssertionIssuer" ID 从基本文件复制，并覆盖 `issueruri` 值。

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

## <a name="session-management"></a>会话管理

你可以使用 `UseTechnicalProfileForSessionManagement` 元素和 [SamlSSOSessionProvider](custom-policy-reference-sso.md#samlssosessionprovider)管理 Azure AD B2C 与 SAML 信赖方应用程序之间的会话。

## <a name="debug-the-saml-protocol"></a>调试 SAML 协议

若要帮助配置和调试与你的服务提供商的集成，你可以为 SAML 协议使用浏览器扩展，例如，用于 Chrome 的 [Saml DevTools 扩展](https://chrome.google.com/webstore/detail/saml-devtools-extension/jndllhgbinhiiddokbeoeepbppdnhhio) 、用于 FireFox 的 [saml 跟踪](https://addons.mozilla.org/es/firefox/addon/saml-tracer/) 程序 [工具或 Edge 或 IE 开发人员工具](https://techcommunity.microsoft.com/t5/microsoft-sharepoint-blog/gathering-a-saml-token-using-edge-or-ie-developer-tools/ba-p/320957)。

使用这些工具，可以检查应用程序和 Azure AD B2C 之间的集成。 例如：

* 检查 SAML 请求是否包含签名，并确定用于登录授权请求的算法。
* 检查 Azure AD B2C 是否返回错误消息。
* 检查断言部分是否已加密。

## <a name="next-steps"></a>后续步骤

- [在 OASIS 网站上查找有关 SAML 协议](https://www.oasis-open.org/)的详细信息。
- 从 [Azure AD B2C GitHub 社区](https://github.com/azure-ad-b2c/saml-sp-tester)存储库获取 SAML 测试 web 应用。

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp

::: zone-end
