---
title: TechnicalProfiles
titleSuffix: Azure AD B2C
description: 在 Azure Active Directory B2C 中指定自定义策略的 TechnicalProfiles 元素。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bbb0c5617696347b566ba09a481afae4f52379aa
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102096031"
---
# <a name="technicalprofiles"></a>TechnicalProfiles

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

技术配置文件提供了一个框架，该框架具有用于与不同类型的参与方进行通信的内置机制。 技术配置文件用于与 Azure AD B2C 租户通信、创建用户或读取用户配置文件。 技术配置文件可以自断言以实现与用户的交互。 例如，收集用户的凭据进行登录，然后呈现注册页面或密码重置页面。

## <a name="type-of-technical-profiles"></a>技术配置文件的类型

技术配置文件支持以下类型的方案：

- [Application Insights](analytics-with-application-insights.md) - 将事件数据发送到 [Application Insights](../azure-monitor/app/app-insights-overview.md)。
- [Azure Active Directory](active-directory-technical-profile.md) - 为 Azure Active Directory B2C 用户管理提供支持。
- [Azure AD 多重身份验证](multi-factor-auth-technical-profile.md) - 使用 Azure AD 多重身份验证 (MFA) 为验证电话号码提供支持。 
- [声明转换](claims-transformation-technical-profile.md) -调用输出声明转换来处理声明值、验证声明或设置一组输出声明的默认值。
- [ID 令牌提示](id-token-hint.md) -验证 `id_token_hint` JWT 令牌签名、颁发者名称和令牌受众，并从入站令牌中提取声明。
- [JWT 令牌颁发者](jwt-issuer-technical-profile.md) - 发出返回给信赖方应用程序的 JWT 令牌。
- [OAuth1](oauth1-technical-profile.md) -与任何 OAuth 1.0 协议标识提供程序联合。
- [OAuth2](oauth2-technical-profile.md) -与任何 OAuth 2.0 协议标识提供程序联合。
- [一次性密码](one-time-password-technical-profile.md) - 为管理一次性密码的生成和验证提供支持。
- [OpenID Connect](openid-connect-technical-profile.md) - 与任何 OpenID Connect 协议标识提供者联合。
- [电话因素](phone-factor-technical-profile.md) - 支持注册和验证电话号码。
- [RESTful 提供程序](restful-technical-profile.md) - 调用 REST API 服务，例如验证用户输入、扩充用户数据或与业务线应用程序集成。
- [SAML 标识提供者](identity-provider-generic-saml.md) - 与任何 SAML 协议标识提供者联合。
- [SAML 令牌颁发者](saml-service-provider.md) - 发出返回给信赖方应用程序的 SAML 令牌。
- [自断言](self-asserted-technical-profile.md) - 与用户交互。 例如，收集用户的凭据进行登录、呈现注册页或密码重置。
- [会话管理](custom-policy-reference-sso.md) - 处理不同类型的会话。

## <a name="technical-profile-flow"></a>技术配置文件流

所有类型的技术配置文件都具有相同的概念。 首先，请阅读输入声明，运行声明转换。 然后与已配置的参与方进行通信，例如标识提供者、REST API 或 Azure AD 目录服务。 在该过程完成后，技术配置文件返回输出声明并可能运行输出声明转换。 下图显示了如何处理技术配置文件中引用的转换和映射。 执行声明转换后，输出声明会立即存储在声明包中。 与技术配置文件交互的参与方无关。

![说明技术配置文件流的示意图](./media/technical-profiles/technical-profile-flow.png)

1. **单一登录 (SSO) 会话管理**-使用 [SSO 会话管理](custom-policy-reference-sso.md)还原技术配置文件的会话状态。
1. **输入声明转换** - 在启动技术配置文件之前，Azure AD B2C 运行输入 [声明转换](claimstransformations.md)。
1. **输入声明** - 从用于技术配置文件的声明包中提取声明。
1. 技术配置文件执行 - 技术配置文件与已配置的参与方交换声明。 例如：
    - 将用户重定向到标识提供者以完成登录。 成功登录后，用户返回并继续执行技术配置文件。
    - 在将参数作为 InputClaims 发送并将信息作为 OutputClaims 返回时调用 REST API。
    - 创建或更新用户帐户。
    - 发送并验证 MFA 文本信息。
1. **验证技术配置文件** - [自断言技术配置文件](self-asserted-technical-profile.md)可以调用 [验证技术配置文件](validation-technical-profile.md)来验证用户所描述的数据。
1. **输出声明** - 声明将返回到声明包中。 可以在下一个业务流程步骤或输出声明转换中使用这些声明。
1. **输出声明转换** - 技术配置文件完成后，Azure AD B2C 运行输出 [声明转换](claimstransformations.md)。 
1. **单一登录 (SSO) 会话管理** - 使用 [SSO 会话管理](custom-policy-reference-sso.md)将技术配置文件的数据持久保留在会话中。

**TechnicalProfiles** 元素包含声明提供程序支持的一组技术配置文件。 每个声明提供程序都必须至少有一个技术配置文件。 技术配置文件确定终结点以及与声明提供程序通信所需的协议。 一个声明提供程序可以包含多个技术配置文件。

```xml
<ClaimsProvider>
  <DisplayName>Display name</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Technical profile identifier">
      <DisplayName>Display name of technical profile</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        ...
      </Metadata>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

**TechnicalProfile** 元素包含以下属性：

| 属性 | 必选 | 说明 |
|---------|---------|---------|
| ID | 是 | 技术配置文件的唯一标识符。 可以使用此标识符从策略文件中的其他元素引用技术配置文件。 例如，**OrchestrationSteps** 和 **ValidationTechnicalProfile**。 |

**TechnicalProfile** 包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| 域 | 0:1 | 技术配置文件的域名。 例如，如果技术配置文件指定 Facebook 标识提供者，则域名是 Facebook.com。 |
| DisplayName | 1:1 | 技术配置文件的显示名称。 |
| 说明 | 0:1 | 技术配置文件的说明。 |
| 协议 | 1:1 | 用来与另一方通信的协议。 |
| 元数据 | 0:1 | 控制技术配置文件行为的键/值的集合。 |
| InputTokenFormat | 0:1 | 输入令牌的格式。 可能的值：`JSON`、`JWT`、`SAML11` 或 `SAML2`。 `JWT` 值表示符合 IETF 规范的 JSON Web 令牌。 `SAML11` 值表示符合 OASIS 规范的 SAML 1.1 安全令牌。  `SAML2` 值表示符合 OASIS 规范的 SAML 2.0 安全令牌。 |
| OutputTokenFormat | 0:1 | 输出令牌的格式。 可能的值：`JSON`、`JWT`、`SAML11` 或 `SAML2`。 |
| CryptographicKeys | 0:1 | 技术配置文件中使用的加密密钥列表。 |
| InputClaimsTransformations | 0:1 | 事先定义的、对在将任何声明发送到声明提供程序或信赖方之前应执行的声明转换的引用列表。 |
| InputClaims | 0:1 | 事先定义的、对在技术配置文件中用作输入的声明类型的引用列表。 |
| PersistedClaims | 0:1 | 之前定义的引用的列表，这些引用将由技术配置文件保留。 |
| DisplayClaims | 0:1 | [自断言技术配置文件](self-asserted-technical-profile.md)所提供的声明类型的以前定义的引用的列表。 DisplayClaims 功能目前处于 **预览** 状态。 |
| OutputClaims | 0:1 | 事先定义的、对在技术配置文件中用作输出的声明类型的引用列表。 |
| OutputClaimsTransformations | 0:1 | 事先定义的、对在从声明提供程序收到声明之后应执行的声明转换的引用列表。 |
| ValidationTechnicalProfiles | 0:n | 对技术配置文件用来进行验证的其他技术配置文件的引用列表。 有关详细信息，请参阅[验证技术配置文件](validation-technical-profile.md)|
| SubjectNamingInfo | 0:1 | 控制令牌中使用者名称的生成，其中的使用者名称与声明分开指定。 例如 OAuth 或 SAML。  |
| IncludeInSso | 0:1 |  使用此技术配置文件时是应当对会话应用单一登录 (SSO) 行为，还是需要显式交互。 此元素仅在验证技术配置文件内使用的 SelfAsserted 配置文件中有效。 可能的值为 `true`（默认）或 `false`。 |
| IncludeClaimsFromTechnicalProfile | 0:1 | 要将其中的所有输入和输出声明添加到此技术配置文件的技术配置文件的标识符。 必须在同一个策略文件中定义被引用的技术配置文件。 |
| IncludeTechnicalProfile |0:1 | 要将其中的所有数据添加到此技术配置文件的技术配置文件的标识符。 |
| UseTechnicalProfileForSessionManagement | 0:1 | 用于会话管理的另一个技术配置文件。 |
|EnabledForUserJourneys| 0:1 |控制是否在用户旅程中执行技术配置文件。  |

## <a name="protocol"></a>协议

协议指定用于与另一方通信的协议。 Protocol  元素包含以下属性：

| Attribute | 必选 | 说明 |
| --------- | -------- | ----------- |
| 名称 | 是 | Azure AD B2C 支持的有效协议的名称，用作技术配置文件的一部分。 可能的值：`OAuth1`、`OAuth2`、`SAML2`、`OpenIdConnect`、`Proprietary` 或 `None`。 |
| Handler | 否 | 如果 "协议名称" 设置为 `Proprietary` ，则指定 Azure AD B2C 用来确定协议处理程序的程序集的名称。 |

## <a name="metadata"></a>元数据

Metadata 元素包含特定协议的相关配置选项。 相应的[技术配置文件](#type-of-technical-profiles)规范中介绍了支持的元数据列表。 Metadata 元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| 项目 | 0:n | 与技术配置文件相关的元数据。 每种类型的技术配置文件具有一组不同的元数据项。 有关详细信息，请参阅技术配置文件类型部分。  |

### <a name="item"></a>Item

Metadata 元素的 Item 元素包含以下属性 ：

| 属性 | 必选 | 说明 |
| --------- | -------- | ----------- |
| 密钥 | 是 | 元数据密钥。 请参阅每种[技术配置文件类型](#type-of-technical-profiles)，了解元数据项的列表。 |

下面的示例说明如何使用与 [OAuth2 技术配置文件](oauth2-technical-profile.md#metadata)相关的元数据。

```xml
<TechnicalProfile Id="Facebook-OAUTH">
  ...
  <Metadata>
    <Item Key="ProviderName">facebook</Item>
    <Item Key="authorization_endpoint">https://www.facebook.com/dialog/oauth</Item>
    <Item Key="AccessTokenEndpoint">https://graph.facebook.com/oauth/access_token</Item>
    <Item Key="HttpBinding">GET</Item>
    <Item Key="UsePolicyInRedirectUri">0</Item>
    ...
  </Metadata>
  ...
</TechnicalProfile>
```

下面的示例说明如何使用与 [REST API 技术配置文件](restful-technical-profile.md#metadata)相关的元数据。

```xml
<TechnicalProfile Id="REST-Validate-Email">
  ...
  <Metadata>
    <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
    ...
  </Metadata>
  ...
</TechnicalProfile>
```

## <a name="cryptographic-keys"></a>加密密钥

为了与集成的服务建立信任，Azure AD B2C 以 [策略密钥](policy-keys-overview.md)的形式存储机密和证书。 在执行技术配置文件的过程中，Azure AD B2C 从 Azure AD B2C 策略密钥中检索加密密钥。 然后，使用密钥建立信任、加密或签名令牌。 这些信任包括：

- 与 [OAuth1](oauth1-technical-profile.md#cryptographic-keys)、 [OAuth2](oauth2-technical-profile.md#cryptographic-keys)和 [SAML](identity-provider-generic-saml.md) 标识提供者联合
- 使用 [REST API 服务](secure-rest-api.md)保护连接
- 对 [JWT](jwt-issuer-technical-profile.md#cryptographic-keys) 和 [SAML](saml-service-provider.md) 令牌进行签名和加密

**CryptographicKeys** 元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| 密钥 | 1:n | 此技术配置文件中使用的加密密钥。 |

### <a name="key"></a>密钥

**Key** 元素包含以下属性：

| Attribute | 必选 | 说明 |
| --------- | -------- | ----------- |
| ID | 否 | 从策略文件中的其他元素引用的特定密钥对的唯一标识符。 |
| StorageReferenceId | 是 | 从策略文件中的其他元素引用的存储密钥容器的标识符。 |

## <a name="input-claims-transformations"></a>输入声明转换

InputClaimsTransformations 元素可以包含一组输入声明转换元素，这些元素用于修改输入声明或生成新的声明。 

声明转换集合中以前的声明转换的输出声明可以是后续输入声明转换的输入声明，这样您就可以根据彼此进行声明转换序列。

**InputClaimsTransformations** 元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| InputClaimsTransformation | 1:n | 在将任何声明发送到声明提供程序或信赖方之前应执行的声明转换的标识符。 声明转换可用于修改现有的或生成新的 ClaimsSchema 声明。 |

### <a name="inputclaimstransformation"></a>InputClaimsTransformation

**InputClaimsTransformation** 元素包含以下属性：

| Attribute | 必选 | 说明 |
| --------- | -------- | ----------- |
| ReferenceId | 是 | 已在策略文件或父策略文件中定义的声明转换的标识符。 |

以下技术配置文件引用了 CreateOtherMailsFromEmail 声明转换。 声明转换将 `email` 声明的值添加到 `otherMails` 集合中，然后再将数据永久保存到目录中。

```xml
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  ...
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="CreateOtherMailsFromEmail" />
  </InputClaimsTransformations>
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <PersistedClaim ClaimTypeReferenceId="userPrincipalName" />
    <PersistedClaim ClaimTypeReferenceId="mailNickName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="otherMails" />
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
  ...
</TechnicalProfile>
```

## <a name="input-claims"></a>输入声明

InputClaims 从声明包中提取声明，并将其用于技术配置文件。 例如，[自断言技术配置文件](self-asserted-technical-profile.md)使用输入声明来预填充用户提供的输出声明。 REST API 技术配置文件使用输入声明将输入参数发送到 REST API 终结点。 Azure Active Directory 使用输入声明作为读取、更新或删除帐户的唯一标识符。

InputClaims  元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| InputClaim | 1:n | 预期的输入声明类型。 |

### <a name="inputclaim"></a>InputClaim

InputClaim  元素包含以下属性：

| 属性 | 必选 | 说明 |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | 是 | 声明类型的标识符。 声明已在策略文件或父策略文件的声明架构部分中定义。 |
| DefaultValue | 否 | 当 ClaimTypeReferenceId 指示的声明不存在时用来创建声明的默认值。技术配置文件可将生成的声明用作 InputClaim。 |
| PartnerClaimType | 否 | 指定的策略声明类型映射到的外部合作伙伴声明类型的标识符。 如果未指定 PartnerClaimType 属性，则指定的策略声明类型将映射到同名的合作伙伴声明类型。 当声明类型名称不同于另一方时，请使用此属性。 例如，第一个声明名称为“givenName”，而合作伙伴使用名为“first_name”的声明。 |

## <a name="display-claims"></a>显示声明

**DisplayClaims** 元素包含要在屏幕上呈现的声明的列表，该列表用于从用户收集数据。 在显示声明集合中，可以包含对已创建的[声明类型](claimsschema.md)或 [DisplayControl](display-controls.md) 的引用。 

- 声明类型是对要在屏幕上呈现的声明的引用。 
  - 若要强制用户提供特定声明的值，请将“DisplayClaim”元素的“Required”属性设置为 `true`。
  - 若要预填充显示声明的值，请使用前面介绍的输入声明。 另外，此元素还可能包含默认值。
  - “DisplayClaims”集合中的“ClaimType”元素需要将“UserInputType”元素设置为 Azure AD B2C 支持的任意用户输入类型    。 例如，`TextBox` 或 `DropdownSingleSelect`。
- 显示控件是一个具有特殊功能的用户界面元素，可以与 Azure AD B2C 后端服务进行交互。 它允许用户在页面上执行某些操作，这些操作在后端调用验证技术配置文件。 例如，验证电子邮件地址、电话号码或客户会员号。

“DisplayClaims”中的元素顺序指定 Azure AD B2C 在屏幕上呈现声明的顺序。 

DisplayClaims  元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| DisplayClaim | 1:n | 预期的输入声明类型。 |

### <a name="displayclaim"></a>DisplayClaim

DisplayClaim  元素包含以下属性：

| Attribute | 必选 | 说明 |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | 否 | 已在策略文件或父策略文件的 ClaimsSchema 节中定义的声明类型的标识符。 |
| DisplayControlReferenceId | 否 | 已在策略文件或父策略文件的 ClaimsSchema 节中定义的[显示控件](display-controls.md)的标识符。 |
| 必选 | 否 | 指示是否需要显示声明。 |

下面的示例说明如何在自断言技术配置文件中使用显示声明和显示控件。

![带有显示声明的自断言技术配置文件](./media/technical-profiles/display-claims.png)

在以下技术配置文件中：

- 第一个显示声明引用收集和验证电子邮件地址的 `emailVerificationControl` 显示控件。
- 第五个显示声明引用收集和验证电话号码的 `phoneVerificationControl` 显示控件。
- 其他显示声明为 ClaimTypes，要从用户处收集。

```xml
<TechnicalProfile Id="Id">
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
    <DisplayClaim DisplayControlReferenceId="phoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
  </DisplayClaims>
</TechnicalProfile>
```

### <a name="persisted-claims"></a>持久化声明

PersistedClaims 元素包含 [Azure AD 技术配置文件](active-directory-technical-profile.md)应持久保存的所有值，以及策略的 [ClaimsSchema](claimsschema.md) 节中已定义的声明类型与 Azure AD 属性名称之间可能的映射信息。

除非指定了包含 Azure AD 属性名称的 PartnerClaimType 属性，否则声明名称是 [Azure AD 属性](user-profile-attributes.md)的名称。

**PersistedClaims** 元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| PersistedClaim | 1:n | 要保存的声明类型。 |

### <a name="persistedclaim"></a>PersistedClaim

**PersistedClaim** 元素包含以下属性：

| Attribute | 必选 | 说明 |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | 是 | 已在策略文件或父策略文件的 ClaimsSchema 节中定义的声明类型的标识符。 |
| DefaultValue | 否 | 要用于创建声明的默认值（如果声明不存在）。 |
| PartnerClaimType | 否 | 指定的策略声明类型映射到的外部合作伙伴声明类型的标识符。 如果未指定 PartnerClaimType 属性，则指定的策略声明类型将映射到同名的合作伙伴声明类型。 当声明类型名称不同于另一方时，请使用此属性。 例如，第一个声明名称为“givenName”，而合作伙伴使用名为“first_name”的声明。 |

在下面的示例中，AAD-UserWriteUsingLogonEmail 技术配置文件或[初学者包](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccounts)创建新本地帐户并保存以下声明：

```xml
<PersistedClaims>
  <PersistedClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" />
  <PersistedClaim ClaimTypeReferenceId="newPassword" PartnerClaimType="password"/>
  <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
  <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration" />
  <PersistedClaim ClaimTypeReferenceId="givenName" />
  <PersistedClaim ClaimTypeReferenceId="surname" />
</PersistedClaims>
```

## <a name="output-claims"></a>输出声明

OutputClaims 是在技术配置文件完成后返回到声明包的声明的集合。 可以在下一个业务流程步骤或输出声明转换中使用这些声明。 OutputClaims  元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| OutputClaim | 1:n | 预期的输出声明类型。 |

### <a name="outputclaim"></a>OutputClaim

OutputClaim 元素包含以下属性：

| 属性 | 必选 | 说明 |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | 是 | 已在策略文件或父策略文件的 ClaimsSchema 节中定义的声明类型的标识符。 |
| DefaultValue | 否 | 要用于创建声明的默认值（如果声明不存在）。 |
|AlwaysUseDefaultValue |否 |强制使用默认值。  |
| PartnerClaimType | 否 | 指定的策略声明类型映射到的外部合作伙伴声明类型的标识符。 如果未指定 "合作伙伴声明类型" 属性，则将指定的策略声明类型映射到具有相同名称的合作伙伴声明类型。 当声明类型名称不同于另一方时，请使用此属性。 例如，第一个声明名称为“givenName”，而合作伙伴使用名为“first_name”的声明。 |

## <a name="output-claims-transformations"></a>输出声明转换

**OutputClaimsTransformations** 元素可包含 **OutputClaimsTransformation** 元素的集合。 输出声明转换用于修改输出声明或生成新声明。 执行后，输出声明将被放回到声明包中。 可以在下一个业务流程步骤中使用这些声明。

声明转换集合中先前声明转换的输出声明可以是后续输入声明转换的输入声明，从而使你可以进行一系列相互依赖的声明转换。

**OutputClaimsTransformations** 元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| OutputClaimsTransformation | 1:n | 在将任何声明发送到声明提供程序或信赖方之前应执行的声明转换的标识符。 声明转换可用于修改现有的或生成新的 ClaimsSchema 声明。 |

### <a name="outputclaimstransformation"></a>OutputClaimsTransformation

**OutputClaimsTransformation** 元素包含以下属性：

| Attribute | 必选 | 说明 |
| --------- | -------- | ----------- |
| ReferenceId | 是 | 已在策略文件或父策略文件中定义的声明转换的标识符。 |

下面的技术配置文件引用 AssertAccountEnabledIsTrue 声明转换，以在从目录中读取 `accountEnabled` 声明之后评估该帐户是否已启用。    

```xml
<TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
  ...
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="accountEnabled" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
  </OutputClaims>
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertAccountEnabledIsTrue" />
  </OutputClaimsTransformations>
  ...
</TechnicalProfile>
```

## <a name="validation-technical-profiles"></a>验证技术配置文件

验证技术配置文件用于验证 [自断言技术配置文件](self-asserted-technical-profile.md#validation-technical-profiles)中的输出声明。 验证技术配置文件是来自任何协议（如 [Azure Active Directory](active-directory-technical-profile.md) 或 [REST API](restful-technical-profile.md)）的普通技术配置文件。 验证技术配置文件返回输出声明，或返回错误代码。 错误消息在屏幕上呈现给用户，允许用户重试。

下图说明了 Azure AD B2C 如何使用验证技术配置文件来验证用户凭据

![验证技术配置文件流示意图](./media/technical-profiles/validation-technical-profile.png) 

**ValidationTechnicalProfiles** 元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1:n | 用于验证引用技术配置文件的部分或所有输出声明的技术配置文件的标识符。 被引用技术配置文件的所有输入声明必须出现在引用技术配置文件的输出声明中。 |

### <a name="validationtechnicalprofile"></a>ValidationTechnicalProfile

**ValidationTechnicalProfile** 元素包含以下属性：

| Attribute | 必选 | 说明 |
| --------- | -------- | ----------- |
| ReferenceId | 是 | 已在策略文件或父策略文件中定义的技术配置文件的标识符。 |

## <a name="subjectnaminginfo"></a>SubjectNamingInfo

SubjectNamingInfo 定义[依赖方策略](relyingparty.md#subjectnaminginfo)中的令牌中使用的使用者名称。 **SubjectNamingInfo** 包含以下属性：

| Attribute | 必选 | 说明 |
| --------- | -------- | ----------- |
| ClaimType | 是 | 已在策略文件的 ClaimsSchema 节中定义的声明类型的标识符。 |

## <a name="include-technical-profile"></a>包括技术配置文件

技术配置文件可以包括另一个技术配置文件，用以更改设置或添加新功能。 IncludeTechnicalProfile 元素是对通用技术配置文件的引用，可从中派生技术配置文件。 若要降低策略元素的冗余性和复杂性，当你具有共享核心元素的多个技术配置文件时，请使用包含。 结合使用通用技术配置文件与一组通用配置，以及包含通用技术配置文件的特定任务技术配置文件。 

假设你有一个 [REST API 的技术配置文件](restful-technical-profile.md) ，其中包含一个终结点，你需要针对不同的方案发送不同的声明集。 使用共享功能创建通用技术配置文件，如 REST API 终结点 URI、元数据、身份验证类型和加密密钥。 创建包含通用技术配置文件的特定任务技术配置文件。 然后添加输入声明、输出声明，或覆盖与该技术配置文件相关的 REST API 终结点 URI。

**IncludeTechnicalProfile** 元素包含以下属性：

| Attribute | 必选 | 说明 |
| --------- | -------- | ----------- |
| ReferenceId | 是 | 已在策略文件或父策略文件中定义的技术配置文件的标识符。 |


以下示例说明了包含的用法：

- REST-API-Common - 具有基本配置的通用技术配置文件。
- *Rest-ValidateProfile* -包括 *rest API 常用* 技术配置文件，并指定输入和输出声明。
- *Rest-UpdateProfile* -包括 *rest API 常用* 技术配置文件，指定输入声明，并覆盖 `ServiceUrl` 元数据。

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-API-Common">
      <DisplayName>Base REST API configuration</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity</Item>
        <Item Key="AuthenticationType">Basic</Item>
        <Item Key="SendClaimsIn">Body</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
      </CryptographicKeys>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>

    <TechnicalProfile Id="REST-ValidateProfile">
      <DisplayName>Validate the account and return promo code</DisplayName>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="promoCode" />
      </OutputClaims>
      <IncludeTechnicalProfile ReferenceId="REST-API-Common" />
    </TechnicalProfile>

    <TechnicalProfile Id="REST-UpdateProfile">
       <Metadata>
        <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/update</Item>
      </Metadata>
      <DisplayName>Update the user profile</DisplayName>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="email" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="REST-API-Common" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

### <a name="multi-level-inclusion"></a>多层级包含 

技术配置文件可以包含单个技术配置文件。 包含的层级数没有限制。 例如，AAD-UserReadUsingAlternativeSecurityId-NoError 技术配置文件包括 AAD-UserReadUsingAlternativeSecurityId。 此技术配置文件将 `RaiseErrorIfClaimsPrincipalDoesNotExist` 元数据项设置为 `true`，并且如果目录中不存在社交帐户，则会引发错误。 **AAD-UserReadUsingAlternativeSecurityId-NoError** 将覆盖此行为并禁用错误消息。

```xml
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">false</Item>
  </Metadata>
  <IncludeTechnicalProfile ReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
</TechnicalProfile>
```

AAD-UserReadUsingAlternativeSecurityId 包括 `AAD-Common` 技术配置文件。

```xml
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">Read</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">User does not exist. Please sign up before you can sign in.</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId" PartnerClaimType="alternativeSecurityId" Required="true" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

**AAD-UserReadUsingAlternativeSecurityId-NoError** 和 **AAD-UserReadUsingAlternativeSecurityId** 未指定所需的 **Protocol** 元素，因为在 **AAD-Common** 技术配置文件中指定了该元素。

```xml
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
</TechnicalProfile>
```

## <a name="use-technical-profile-for-session-management"></a>使用技术配置文件进行会话管理

UseTechnicalProfileForSessionManagement 元素引用了[单一登录会话技术配置文件](custom-policy-reference-sso.md)。 **UseTechnicalProfileForSessionManagement** 元素包含以下属性：

| Attribute | 必选 | 说明 |
| --------- | -------- | ----------- |
| ReferenceId | 是 | 已在策略文件或父策略文件中定义的技术配置文件的标识符。 |

## <a name="enabled-for-user-journeys"></a>为用户旅程启用

用户旅程中的 [ClaimsProviderSelections](userjourneys.md#claimsproviderselection) 定义声明提供程序选项的列表及其顺序。 使用 **EnabledForUserJourneys** 元素可以筛选用户可用的声明提供程序。 **EnabledForUserJourneys** 元素包含以下值之一：

- **Always**：执行技术配置文件。
- **Never**：跳过技术配置文件。
- **OnClaimsExistence**：仅当技术配置文件中指定的特定声明存在时才执行技术配置文件。
- **OnItemExistenceInStringCollectionClaim**：仅当字符串集合声明中存在某个项时才执行技术配置文件。
- **OnItemAbsenceInStringCollectionClaim**：仅当字符串集合声明中不存在某个项时才执行技术配置文件。

使用 **OnClaimsExistence**、 **OnItemExistenceInStringCollectionClaim** 或 **OnItemAbsenceInStringCollectionClaim** 时，需要提供以下元数据： 

- **ClaimTypeOnWhichToEnable** -指定要评估的声明的类型。
- **ClaimValueOnWhichToEnable** -指定要比较的值。

仅当 **identityProviders** 字符串集合包含 `facebook.com` 值时，才执行以下技术配置文件：

```xml
<TechnicalProfile Id="UnLink-Facebook-OAUTH">
  <DisplayName>Unlink Facebook</DisplayName>
...
    <Metadata>
      <Item Key="ClaimTypeOnWhichToEnable">identityProviders</Item>
      <Item Key="ClaimValueOnWhichToEnable">facebook.com</Item>
    </Metadata>
...
  <EnabledForUserJourneys>OnItemExistenceInStringCollectionClaim</EnabledForUserJourneys>
</TechnicalProfile>
```
