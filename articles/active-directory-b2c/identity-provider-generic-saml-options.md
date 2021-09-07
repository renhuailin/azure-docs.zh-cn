---
title: 使用 SAML 标识提供者选项设置登录
titleSuffix: Azure Active Directory B2C
description: 在 Azure Active Directory B2C 中配置登录 SAML 标识提供者 (IdP) 选项。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/25/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: c245fef005be5937887a3b8af1a5264e6520a6e8
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2021
ms.locfileid: "122967778"
---
# <a name="configure-saml-identity-provider-options-with-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 配置 SAML 标识提供者选项

Azure Active Directory B2C (Azure AD B2C) 支持使用 SAML 2.0 标识提供者进行联合身份验证。 本文介绍了在使用 SAML 标识提供者登录时可用的配置选项。

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="claims-mapping"></a>声明映射

OutputClaims 元素包含 SAML 标识提供者返回的声明列表。 需要将策略中定义的声明名称映射到标识提供者中定义的名称。 检查标识提供者以获取声明（断言）的列表。 还可以检查标识提供者返回的 SAML 响应的内容。 有关详细信息，请参阅[调试 SAML 消息](#debug-saml-protocol)。 若要添加声明，请先[定义声明](claimsschema.md)，然后将声明添加到输出声明集合。

如果设置了 `DefaultValue` 属性，则还可以包含标识提供者不会返回的声明。 默认值可以是静态值，也可以是动态值，并且使用[上下文声明](#enable-use-of-context-claims)。

输出声明元素包含以下属性：

- ClaimTypeReferenceId 是对声明类型的引用。 
- PartnerClaimType 是 SAML 断言中显示的属性的名称。 
- DefaultValue 是预定义的默认值。 如果声明为空，则将使用默认值。 还可以使用具有上下文值（例如相关 ID 或用户 IP 地址）的[声明解析程序](claim-resolver-overview.md)。

### <a name="subject-name"></a>使用者名称

若要将 Subject 中的 SAML 断言 NameId 读取为规范化声明，请将声明 PartnerClaimType 设置为 `SPNameQualifier` 属性的值  。 如果未提供 `SPNameQualifier` 属性，请将声明 PartnerClaimType 设置为 `NameQualifier` 属性的值。

SAML 断言：

```xml
<saml:Subject>
  <saml:NameID SPNameQualifier="http://your-idp.com/unique-identifier" Format="urn:oasis:names:tc:SAML:2.0:nameid-format:transient">david@contoso.com</saml:NameID>
  <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
    <SubjectConfirmationData InResponseTo="_cd37c3f2-6875-4308-a9db-ce2cf187f4d1" NotOnOrAfter="2020-02-15T16:23:23.137Z" Recipient="https://<your-tenant>.b2clogin.com/<your-tenant>.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer" />
    </SubjectConfirmation>
  </saml:SubjectConfirmation>
</saml:Subject>
```

输出声明：

```xml
<OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="http://your-idp.com/unique-identifier" />
```

如果 `SPNameQualifier` 和 `NameQualifier` 属性都未在 SAML 断言中出现，请将声明 PartnerClaimType 设置为 `assertionSubjectName`。 确保 **NameId** 是断言 XML 中的第一个值。 定义多个断言时，Azure AD B2C 会选取上一个声明中的主题值。


## <a name="configure-saml-protocol-bindings"></a>配置 SAML 协议绑定

SAML 请求将发送到标识提供者的元数据 `SingleSignOnService` 元素中指定的标识提供者。 大多数标识提供者的授权请求都直接包含在 HTTP GET 请求的 URL 查询字符串中（因为消息相对较短）。 有关如何为这两个 SAML 请求配置绑定的信息，请参阅标识提供者文档。

下面是具有两个绑定的 Azure AD 元数据单一登录服务的示例。 `HTTP-Redirect` 优先于 `HTTP-POST`，因为它首先出现在 SAML 标识提供者元数据中。

```xml
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
  ...
  <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/saml2"/>
  <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/saml2"/>
</IDPSSODescriptor>
```

### <a name="assertion-consumer-service"></a>断言使用者服务

Azure AD B2C 在断言使用者服务（简称 ACS）中发送和接收标识提供者 SAML 响应。 SAML 响应通过 HTTP POST 绑定传输到 Azure AD B2C。 ACS 位置指向信赖方的基本策略。 例如，如果信赖策略是 B2C_1A_signup_signin，则 ACS 是 B2C_1A_signup_signin 的基本策略（如 B2C_1A_TrustFrameworkBase）。

下面是 Azure AD B2C 策略元数据断言使用者服务元素的示例。 

```xml
<SPSSODescriptor AuthnRequestsSigned="true" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
  ...
  <AssertionConsumerService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://your-tenant.b2clogin.com/your-tenant/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer" index="0" isDefault="true"/>
</SPSSODescriptor>
```

## <a name="configure-the-saml-request-signature"></a>配置 SAML 请求签名

Azure AD B2C 使用 SamlMessageSigning 加密密钥对所有传出身份验证请求进行签名。 若要禁用 SAML 请求签名，请将 WantsSignedRequests 设置为 `false`。 如果元数据设置为 `false`，则请求中将省略 SigAlg 和 Signature 参数（查询字符串或 post 参数） 。

此元数据还控制 AuthnRequestsSigned 属性，该属性包含在与标识提供者共享的 Azure AD B2C 技术配置文件的元数据中。 如果技术配置文件元数据中的 **WantsSignedRequests** 的值设置为 `false` 且标识提供者元数据 **WantAuthnRequestsSigned** 设置为 `false` 或未指定，则 Azure AD B2C 不会对请求签名。

以下示例从 SAML 请求中删除签名。

```xml
<Metadata>
  ...
  <Item Key="WantsSignedRequests">false</Item>
</Metadata>
```

### <a name="signature-algorithm"></a>签名算法

Azure AD B2C 使用 `Sha1` 对 SAML 请求进行签名。 使用 XmlSignatureAlgorithm 元数据来配置要使用的算法。 可能的值为 `Sha256`、`Sha384`、`Sha512` 或 `Sha1`（默认）。 此元数据控制 SAML 请求中 SigAlg 参数（查询字符串或 post 参数）的值。 确保在两端配置具有相同值的签名算法。 仅使用证书支持的算法。

### <a name="include-key-info"></a>包含关键信息

当标识提供者指示 Azure AD B2C 绑定设置为 `HTTP-POST` 时，Azure AD B2C 将在 SAML 请求正文中包含签名和算法。 还可以将 Azure AD 配置为在绑定设置为 `HTTP-POST` 时包含证书的公钥。 将 IncludeKeyInfo 元数据用于 `true` 或 `false`。 在以下示例中，Azure AD 将不包括证书的公钥。

```xml
<Metadata>
  ...
  <Item Key="IncludeKeyInfo">false</Item>
</Metadata>
```

## <a name="configure-saml-request-name-id"></a>配置 SAML 请求名称 ID

SAML 授权请求 `<NameID>` 元素指示 SAML 名称标识符格式。 本部分介绍默认配置以及自定义名称 ID 元素的方法。

## <a name="preferred-username"></a>首选用户名

在登录用户旅程中，信赖方应用程序可以针对特定用户。 Azure AD B2C 允许将首选用户名发送到 SAML 标识提供者。 InputClaims 元素用于发送 SAML 授权请求的 Subject 中的 NameId  。

若要在授权请求中包含使用者名称 ID，请在 `<CryptographicKeys>` 后面添加以下 `<InputClaims>` 元素。 PartnerClaimType 必须设置为 `subject`。

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="subject" />
</InputClaims>
```

在此示例中，Azure AD B2C 发送 SAML 授权请求的 Subject 中具有 NameId 的 signInName 声明的值  。

```xml
<samlp:AuthnRequest ... >
  ...
  <saml:Subject>
    <saml:NameID>sam@contoso.com</saml:NameID>
  </saml:Subject>
</samlp:AuthnRequest>
```

可以使用[上下文声明](claim-resolver-overview.md)（例如 `{OIDC:LoginHint}`）来填充声明值。

```xml
<Metadata>
  ...
  <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
</Metadata>
  ...
<InputClaims>
  <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="subject" DefaultValue="{OIDC:LoginHint}" AlwaysUseDefaultValue="true" />
</InputClaims>
```

### <a name="name-id-policy-format"></a>名称 ID 策略格式

默认情况下，SAML 授权请求指定 `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` 策略。 这表明可使用请求的主题对应的标识提供者支持的任何类型的标识符。

若要更改此行为，请参阅标识提供者的文档，了解哪些名称 ID 策略受支持。 然后，以相应的策略格式添加 `NameIdPolicyFormat` 元数据。 例如：

```xml
<Metadata>
  ...
  <Item Key="NameIdPolicyFormat">urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress</Item>
</Metadata>
```

以下 SAML 授权请求包含名称 ID 策略。

```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:NameIDPolicy Format="urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress" />
</samlp:AuthnRequest>
```

### <a name="allow-creating-new-accounts"></a>允许创建新帐户

如果指定[名称 ID 策略格式](#name-id-policy-format)，则还可以指定 NameIDPolicy 的 `AllowCreate` 属性，以指示是否允许标识提供者在登录流程中创建新帐户。 有关指导，请参阅标识提供者的文档。

默认情况下，Azure AD B2C 会省略 `AllowCreate` 属性。 可以使用 `NameIdPolicyAllowCreate` 元数据更改此行为。 此元数据的值为 `true` 或 `false`。

下面的示例演示如何将 `NameIDPolicy` 的 `AllowCreate` 属性设置为 `true`。

```xml
<Metadata>
  ...
  <Item Key="NameIdPolicyFormat">urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress</Item>
  <Item Key="NameIdPolicyAllowCreate">true</Item>
</Metadata>
```


下面的示例演示其中包含 NameIDPolicy 元素的 AllowCreate 的授权请求 。


```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:NameIDPolicy 
      Format="urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress" 
      AllowCreate="true" />
</samlp:AuthnRequest>
```

### <a name="force-authentication"></a>强制身份验证

可以通过在 SAML 身份验证请求中传递 `ForceAuthN` 属性来强制外部 SAML IDP 提示用户进行身份验证。 标识提供者也必须支持此属性。

`ForceAuthN` 属性是布尔值 `true` 或 `false`。 默认情况下，Azure AD B2C 将 ForceAuthN 值设置为 `false`。 如果随后重置会话（例如，通过在 OIDC 中使用 `prompt=login` 来这样做），则 ForceAuthN 值将设置为 `true`。 如下所示设置元数据项会强制设置向外部 IDP 发出的所有请求的值。

以下示例显示了设置为 `true` 的 `ForceAuthN` 属性：

```xml
<Metadata>
  ...
  <Item Key="ForceAuthN">true</Item>
  ...
</Metadata>
```

以下示例显示了授权请求中的 `ForceAuthN` 属性：


```xml
<samlp:AuthnRequest AssertionConsumerServiceURL="https://..."  ...
                    ForceAuthN="true">
  ...
</samlp:AuthnRequest>
```

### <a name="include-authentication-context-class-references"></a>包含身份验证上下文类引用

SAML 授权请求可能包含 AuthnContext 元素，该元素指定授权请求的上下文。 元素可以包含身份验证上下文类引用，该引用告知 SAML 标识提供者向用户提供何种身份验证机制。

若要配置身份验证上下文类引用，请添加 IncludeAuthnContextClassReferences 元数据。 在值中指定一个或多个可标识身份验证上下文类的 URI 引用。 以逗号分隔列表的形式指定多个 URI。 有关受支持的 AuthnContextClassRef URI，请参阅标识提供者的文档。

以下示例允许用户使用用户名和密码登录，并允许使用用户名和密码通过受保护的会话 (SSL/TLS) 登录。

```xml
<Metadata>
  ...
  <Item Key="IncludeAuthnContextClassReferences">urn:oasis:names:tc:SAML:2.0:ac:classes:Password,urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport</Item>
</Metadata>
```

以下 SAML 授权请求包含身份验证上下文类引用。

```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:RequestedAuthnContext>
    <saml:AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:Password</saml:AuthnContextClassRef>
    <saml:AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport</saml:AuthnContextClassRef>
  </samlp:RequestedAuthnContext>
</samlp:AuthnRequest>
```

## <a name="include-custom-data-in-the-authorization-request"></a>在授权请求中包含自定义数据

可以选择包含 Azure AD BC 和标识提供者都同意的协议消息扩展元素。 此扩展以 XML 格式呈现。 可以通过将 XML 数据添加到 CDATA 元素 `<![CDATA[Your Custom XML]]>` 中来包含扩展元素。 检查标识提供者的文档，看扩展元素是否受支持。

以下示例说明了扩展数据的用法：

```xml
<Metadata>
  ...
  <Item Key="AuthenticationRequestExtensions"><![CDATA[
            <ext:MyCustom xmlns:ext="urn:ext:custom">
              <ext:AssuranceLevel>1</ext:AssuranceLevel>
              <ext:AssuranceDescription>Identity verified to level 1.</ext:AssuranceDescription>
            </ext:MyCustom>]]></Item>
</Metadata>
```

> [!NOTE]
> 根据 SAML 规范，扩展数据必须是命名空间限定的 XML（例如，上面示例所示的“urn:ext:custom”），并且不能是特定于 SAML 的命名空间之一。

使用 SAML 协议消息扩展时，SAML 响应将如以下示例所示：

```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:Extensions>
    <ext:MyCustom xmlns:ext="urn:ext:custom">
      <ext:AssuranceLevel>1</ext:AssuranceLevel>
      <ext:AssuranceDescription>Identity verified to level 1.</ext:AssuranceDescription>
    </ext:MyCustom>
  </samlp:Extensions>
</samlp:AuthnRequest>
```

## <a name="require-signed-saml-responses"></a>需要签名的 SAML 响应

Azure AD B2C 要求对所有传入断言进行签名。 可以通过将 WantsSignedAssertions 设置为 `false` 来移除此要求。 在此情况下，标识提供者不应对断言进行签名，但即使这样做，Azure AD B2C 也不会验证签名。

WantsSignedAssertions 元数据控制 SAML 元数据标记 WantAssertionsSigned，该标记包含在与标识提供者共享的 Azure AD B2C 技术配置文件的元数据中 。

```xml
<SPSSODescriptor AuthnRequestsSigned="true" WantAssertionsSigned="true" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
```

如果禁用断言验证，则可能还需要禁用响应消息签名验证。 将 ResponsesSigned 元数据设置为 `false`。 在此情况下，标识提供者不应对 SAML 响应消息进行签名，但即使这样做，Azure AD B2C 也不会验证签名。

以下示例同时删除了消息和断言签名：

```xml
<Metadata>
  ...
  <Item Key="WantsSignedAssertions">false</Item>
  <Item Key="ResponsesSigned">false</Item>
</Metadata>
```

## <a name="require-encrypted-saml-responses"></a>需要加密的 SAML 响应

若要要求加密所有传入断言，请设置 WantsEncryptedAssertions 元数据。 要求加密时，标识提供者将使用 Azure AD B2C 技术配置文件中加密证书的公钥。 Azure AD B2C 使用加密证书的私有部分来解密响应断言。

如果启用断言加密，则还可能需要禁用响应签名验证（有关详细信息，请参阅[需要签名的 SAML 响应](#require-signed-saml-responses)）。

当 WantsEncryptedAssertions 元数据设置为 `true` 时，Azure AD B2C 技术配置文件的元数据将包含“加密”部分 。 身份提供程序读取元数据并使用 Azure AD B2C 技术配置文件的元数据中提供的公钥加密 SAML 响应断言。

以下示例显示了用于加密的 SAML 元数据的“密钥描述符”部分：

```xml
<SPSSODescriptor AuthnRequestsSigned="true"  protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
  <KeyDescriptor use="encryption">
    <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
      <X509Data>
        <X509Certificate>valid certificate</X509Certificate>
      </X509Data>
    </KeyInfo>
  </KeyDescriptor>
  ...
</SPSSODescriptor>
```

若要加密 SAML 响应断言：

1. 使用唯一标识符[创建策略密钥](identity-provider-generic-saml.md#create-a-policy-key)。 例如，`B2C_1A_SAMLEncryptionCert`。
2. 在 SAML 技术配置文件 CryptographicKeys 集合中。 将 StorageReferenceId 设置为在第一步中创建的策略密钥的名称。 `SamlAssertionDecryption` ID 指示使用加密密钥来加密和解密 SAML 响应的断言。

    ```xml
    <CryptographicKeys>
            ...
      <Key Id="SamlAssertionDecryption" StorageReferenceId="B2C_1A_SAMLEncryptionCert"/>
    </CryptographicKeys>
    ```

3. 将技术配置文件元数据 WantsEncryptedAssertions 设为 `true`。
    
    ```xml
    <Metadata>
      ...
      <Item Key="WantsEncryptedAssertions">true</Item>
    </Metadata>
    ```

4. 使用新的 Azure AD B2C 技术配置文件元数据更新标识提供者。 你应该会看到 KeyDescriptor，其中的“使用”属性设置为包含你的证书的公钥的 `encryption`。

## <a name="enable-use-of-context-claims"></a>允许使用上下文声明

在输入和输出声明集合中，只要设置了 `DefaultValue` 属性，就可以包含标识提供者未返回的声明。 还可以使用要包含在技术配置文件中的[上下文声明](claim-resolver-overview.md)。 若要使用上下文声明：

1. 将声明类型添加到 [BuildingBlocks](buildingblocks.md) 中的 [ClaimsSchema](claimsschema.md) 元素。
2. 将输出声明添加到输入或输出集合。 在以下示例中，第一个声明设置标识提供者的值。 第二个声明使用用户 IP 地址[上下文声明](claim-resolver-overview.md)。
    
    ```xml
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" AlwaysUseDefaultValue="true" />
      <OutputClaim ClaimTypeReferenceId="IpAddress" DefaultValue="{Context:IPAddress}" AlwaysUseDefaultValue="true" />
    </OutputClaims>
    ```

## <a name="disable-single-logout"></a>禁用单一注销

在请求注销应用程序时，Azure AD B2C 会尝试从 SAML 标识提供者注销。 有关详细信息，请参阅 [Azure AD B2C 会话注销](session-behavior.md#sign-out)。若要禁用单一注销行为，请将 SingleLogoutEnabled 元数据设置为 `false`。

```xml
<Metadata>
  ...
  <Item Key="SingleLogoutEnabled">false</Item>
</Metadata>
```

## <a name="debug-saml-protocol"></a>调试 SAML 协议

若要帮助配置和调试使用 SAML 标识提供者的联合身份验证，可以使用 SAML 协议的浏览器扩展，例如 Chrome 的 [SAML DevTools 扩展](https://chrome.google.com/webstore/detail/saml-devtools-extension/jndllhgbinhiiddokbeoeepbppdnhhio)、FireFox 的 [SAML 跟踪程序](https://addons.mozilla.org/es/firefox/addon/saml-tracer/)或 [Microsoft Edge 或 IE 开发人员工具](https://techcommunity.microsoft.com/t5/microsoft-sharepoint-blog/gathering-a-saml-token-using-edge-or-ie-developer-tools/ba-p/320957)。

使用这些工具，可以检查 Azure AD B2C 与 SAML 标识提供者之间的集成。 例如：

* 检查 SAML 请求是否包含签名，并确定用于登录授权请求的算法。
* 获取 `AttributeStatement` 部分下的声明（断言）。
* 检查标识提供者是否返回错误消息。
* 检查断言部分是否已加密。

## <a name="next-steps"></a>后续步骤

- 了解如何使用 [Application Insights](troubleshoot-with-application-insights.md) 诊断自定义策略的问题。 

::: zone-end
