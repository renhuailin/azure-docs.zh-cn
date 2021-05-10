---
title: 关于 Azure AD B2C 中的 API 连接器
description: 使用 Azure Active Directory (Azure AD) API 连接器，通过使用 REST API 自定义和扩展用户流。
services: active-directory-b2c
ms.service: active-directory
ms.subservice: B2C
ms.topic: how-to
ms.date: 04/27/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: dcda93e2001e982ff2fb470f0e7a4bc9c366a8d7
ms.sourcegitcommit: 2e123f00b9bbfebe1a3f6e42196f328b50233fc5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2021
ms.locfileid: "108070060"
---
# <a name="use-api-connectors-to-customize-and-extend-sign-up-user-flows"></a>使用 API 连接器来自定义并扩展注册用户流

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

> [!IMPORTANT]
> 用于注册的 API 连接器是 Azure AD B2C 的一项公共预览版功能。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="overview"></a>概述 

作为开发者或 IT 管理员，你可以使用 API 连接器将注册用户流与 REST API 集成，以自定义注册体验，并与外部系统集成。 例如，使用 API 连接器，可以：

- 验证用户输入数据。 针对格式错误或无效的用户数据进行验证。 例如，可以根据外部数据存储或允许值列表中的现有数据验证用户提供的数据。 如果无效，你可以要求用户提供有效数据或阻止用户继续注册流。
- 与自定义批准工作流集成。 连接到用于管理和限制帐户创建的自定义审批系统。
- 覆盖用户属性。 重新格式化或为从用户收集的属性赋值。 例如，如果用户使用全小写或全大写字母输入了名字，则你可以设置该名字的格式，只将第一个字母大写。 
- **验证用户标识**。 使用身份验证服务为帐户创建决策添加额外的安全级别。
- 运行自定义业务逻辑。 可以在云系统中触发下游事件，以发送推送通知、更新企业数据库、管理权限、审核数据库以及执行其他自定义操作。

API 连接器通过定义 API 调用的 HTTP 终结点 URL 和身份验证，为 Azure AD B2C 提供调用 API 终结点所需的信息。 配置 API 连接器后，可以为用户流中的特定步骤启用它。 当用户到达注册流中的那一步时，API 连接器被调用，并作为 HTTP POST 请求具体化到你的 API 中，将用户信息（“声明”）作为 JSON 正文中的键值对。 API 响应可能会影响用户流的执行。 例如，API 响应可以阻止用户进行注册，要求用户重新输入信息，或覆盖并追加用户属性。

## <a name="where-you-can-enable-an-api-connector-in-a-user-flow"></a>可在其中启用用户流中的 API 连接器

用户流中有两个位置可用于启用 API 连接器：

- 使用标识提供者登录之后
- 创建用户之前

> [!IMPORTANT]
> 在这两种情况下，将在用户“注册”而不是登录过程中调用 API 连接器。

### <a name="after-signing-in-with-an-identity-provider"></a>使用标识提供者登录之后

在注册过程的此步骤中，在用户使用标识提供者（例如 Google、Facebook 和 Azure AD）进行身份验证之后，会立即调用 API 连接器。 此步骤优先于特性收集页，后者是向用户显示的用于收集用户特性的表单。 如果用户正在使用本地帐户注册，则不会调用此步骤。 以下是可能在此步骤中启用的 API 连接器方案的示例：

- 使用用户提供的电子邮件或联合标识在现有系统中查找声明。 从现有系统返回这些声明，预先填充属性集合页，并使它们可在令牌中返回。
- 根据社交标识实现允许或阻止列表。

### <a name="before-creating-the-user"></a>创建用户之前

在显示属性集合页之后，将调用注册过程中的此步骤的 API 连接器（如果已包含一个连接器）。 在创建用户帐户之前始终会调用此步骤。 下面是你在注册过程中此时可能会启用的方案示例：

- 验证用户输入数据并要求用户重新提交数据。
- 阻止用户根据用户输入的数据进行注册。
- 验证用户标识。
- 在外部系统中查询有关用户的现有数据，以将其返回到应用程序令牌中或将其存储在 Azure AD 中。

::: zone-end

::: zone pivot="b2c-custom-policy"

构成 Azure Active Directory B2C (Azure AD B2C) 的基础的 Identity Experience Framework 可在用户旅程中与 RESTful API 相集成。 本文介绍如何使用 [RESTful 技术配置文件](restful-technical-profile.md)创建与 RESTful 服务交互的用户旅程。

使用 Azure AD B2C 可以通过调用 RESTful 服务，将自己的业务逻辑添加到用户旅程中。 Identity Experience Framework 可以在 RESTful 服务中发送和接收数据，以交换声明。 例如，可以：

- 验证用户输入数据。 例如，可以验证用户提供的电子邮件地址是否在客户数据库中存在，如果不存在，则显示错误消息。
- 处理声明。 如果用户使用全小写或全大写字母输入了其名字，REST API 可以设置该名字的格式，只将第一个字母，然后将此名字返回到 Azure AD B2C。
- 通过进一步与企业业务线应用程序集成来丰富用户数据。 RESTful 服务可以接收用户的电子邮件地址、查询客户的数据库，并向 Azure AD B2C 返回用户的会员号。 返回声明可以存储在用户 Azure AD 帐户中、在后续的业务流程步骤中进行评估，或包含在访问令牌中。
- 运行自定义业务逻辑。 可以发送推送通知、更新企业数据库、运行用户迁移过程、管理权限、审核数据库，以及执行任何其他工作流。

![RESTful 服务声明交换示意图](media/api-connectors-overview/restful-service-claims-exchange.png)

> [!NOTE]
> 如果 RESTful 服务对 Azure AD B2C 的响应速度缓慢或没有响应，则超时为 30 秒，重试计数为 2 次（这意味着总共有 3 次尝试）。 超时和重试计数设置当前不可配置。

## <a name="calling-a-restful-service"></a>调用 RESTful 服务

交互包括 REST API 声明与 Azure AD B2C 之间的声明信息交换。 可通过以下方式来设计与 RESTful 服务的集成：

- 验证技术配置文件。 对 RESTful 服务的调用在指定的[自我断言技术配置文件](self-asserted-technical-profile.md)的[验证技术配置文件](validation-technical-profile.md)中发生，或者在[显示控制](display-controls.md)的[验证显示控制](display-control-verification.md)中发生。 在用户旅程推进之前，验证技术配置文件会验证用户提供的数据。 使用验证技术配置文件，可以：

  - 将声明发送到 REST API。
  - 验证声明，并引发向用户显示的自定义错误消息。
  - 将 REST API 中的声明发回给下一个业务流程步骤。

- 声明交换。 可以通过从[用户旅程](userjourneys.md)的业务流程步骤直接调用 REST API 技术配置文件，来配置直接声明交换。 此定义仅限于：

  - 将声明发送到 REST API。
  - 验证声明，并引发返回给应用程序的自定义错误消息。
  - 将 REST API 中的声明发回给下一个业务流程步骤。

可在自定义策略定义的用户旅程中的任意步骤中添加 REST API 调用。 例如，可在以下时机调用 REST API：

- 登录期间在 Azure AD B2C 验证凭据之前的那一刻。
- 登录后立即调用。
- Azure AD B2C 在目录中创建新帐户之前。
- Azure AD B2C 在目录中创建新帐户之后。
- Azure AD B2C 颁发访问令牌之前。

![验证技术配置文件集合](media/api-connectors-overview/validation-technical-profile.png)

## <a name="sending-data"></a>发送数据

在 [RESTful 技术配置文件](restful-technical-profile.md)中，`InputClaims` 元素包含要发送到 RESTful 服务的声明列表。 可将声明的名称映射到 RESTful 服务中定义的名称，设置默认值，然后使用[声明解析程序](claim-resolver-overview.md)。

可以使用 SendClaimsIn 特性来配置如何将输入声明发送到 RESTful 声明提供程序。 可能的值包括：

- Body，以 JSON 格式在 HTTP POST 请求正文中发送。
- Form，以“&”符号分隔的键值格式在 HTTP POST 请求正文中发送。
- Header，在 HTTP GET 请求标头中发送。
- QueryString，在 HTTP GET 请求查询字符串中发送。

配置 Body 选项时，REST API 技术配置文件允许将复杂的 JSON 有效负载发送到终结点。 有关详细信息，请参阅[发送 JSON 有效负载](restful-technical-profile.md#send-a-json-payload)。

## <a name="receiving-data"></a>接收数据

[RESTful 技术配置文件](restful-technical-profile.md)的 `OutputClaims` 元素包含 REST API 返回的声明列表。 可能需要将策略中定义的声明名称映射到 REST API 中定义的名称。 只要设置了 DefaultValue 特性，就还可以包含 REST API 标识提供者不会返回的声明。

RESTful 声明提供程序分析的输出声明始终预期分析平面 JSON 正文响应，例如：

```json
{
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "loyaltyNumber":  1234
}
```

输出声明应类似于以下 xml 代码片段：

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" />
</OutputClaims>
```

若要分析嵌套的 JSON 正文响应，请将 ResolveJsonPathsInJsonTokens 的元数据设置为 true。 在输出声明中，将 PartnerClaimType 设置为要输出的 JSON 路径元素。

```json
"contacts": [
  {
    "id": "MAINCONTACT_1",
    "person": {
      "name": "Emily Smith",
      "loyaltyNumber":  1234,
      "emails": [
        {
          "id": "EMAIL_1",
          "type": "WORK",
          "email": "email@domain.com"
        }
      ]
    }
  }
],
```


输出声明应类似于以下 xml 代码片段：

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="contacts[0].person.name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="contacts[0].person.emails[0].email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="contacts[0].person.loyaltyNumber" />
</OutputClaims>
```

## <a name="localize-the-rest-api"></a>本地化 REST API

在 RESTful 技术配置文件中，你可能想要发送当前会话的语言/区域设置，并在必要时引发本地化的错误消息。 使用[声明解析程序](claim-resolver-overview.md)可以发送上下文声明，例如用户语言。 以下示例中的 RESTful 技术配置文件演示了此方案。

```xml
<TechnicalProfile Id="REST-ValidateUserData">
  <DisplayName>Validate user input data</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app.azurewebsites.net/api/identity</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userLanguage" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress" />
  </InputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

## <a name="handling-error-messages"></a>处理错误消息

REST API 可能需要返回错误消息，例如“在 CRM 系统中未找到该用户”。 如果发生错误，REST API 应返回 HTTP 409 错误消息（冲突响应状态代码）。 有关详细信息，请参阅 [RESTful 技术配置文件](restful-technical-profile.md#returning-validation-error-message)。

此行为只能通过从验证技术配置文件调用 REST API 技术配置文件来实现。 这使用户可以更正页面上的数据，并在提交页面时再次运行验证。

如果直接从用户旅程引用 REST API 技术配置文件，用户将通过相关的错误消息重定向回到信赖方应用程序。

::: zone-end

## <a name="security-considerations"></a>安全注意事项

应保护 REST API 终结点，以便只有经过身份验证的客户端才能与它通信。 REST API 必须使用 HTTPS 终结点。 将身份验证类型设置为以下身份验证方法之一。

### <a name="api-key"></a>API 密钥

API 密钥是用于对要访问 REST API 终结点的用户进行身份验证的唯一标识符。 例如，[Azure Functions HTTP 触发器](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys)将 `code` 作为查询参数包含在终结点 URL 中。

::: zone pivot="b2c-user-flow"

```http
https://contoso.azurewebsites.net/api/endpoint?code=0123456789 
```

不应在生产中单独使用 API 密钥身份验证。 因此，始终需要基本身份验证或证书身份验证的配置。 如果你不想要出于开发目的实现任何身份验证方法（不建议），可以选择基本身份验证，并为 `username` 和 `password` 使用临时值，在 API 中实现授权时，API 可以忽视这些值。

::: zone-end

::: zone pivot="b2c-custom-policy"

可以向 API 密钥发送自定义 HTTP 标头。 例如，[Azure Functions HTTP 触发器](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys)使用 `x-functions-key` HTTP 标头来标识请求者。  

::: zone-end

### <a name="client-certificate"></a>客户端证书

客户端证书身份验证是一种基于证书的相互身份验证方法，其中，客户端向服务器提供客户端证书来证明自己的身份。 在这种情况下，Azure AD B2C 将使用你上传的证书作为 API 连接器配置的一部分。  此行为在 SSL 握手期间发生。 

然后 API 服务可限制只有拥有适当证书的服务才能访问。 客户端证书是一种 PKCS12 (PFX) X.509 数字证书。 在生产环境中，必须由证书颁发机构为此证书签名。

### <a name="http-basic-authentication"></a>HTTP 基本身份验证

HTTP 基本身份验证在 [RFC 2617](https://tools.ietf.org/html/rfc2617) 中进行定义。 Azure AD B2C 使用 `Authorization` 头中的客户端凭据（`username` 和 `password`）发送 HTTP 请求。 凭据的格式是 base64 编码的字符串 `username:password`。 然后，API 将检查这些值，以确定是否要拒绝 API 调用。

::: zone pivot="b2c-custom-policy"

### <a name="bearer-token"></a>持有者令牌

持有者令牌身份验证在 [OAuth2.0 授权框架：持有者令牌用法 (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt) 中进行定义。 在持有者令牌身份验证中，Azure AD B2C 使用授权标头中的令牌发送 HTTP 请求。

```http
Authorization: Bearer <token>
```

持有者令牌是一种不透明字符串。 它可以是 JWT 访问令牌，也可以是 REST API 要求 Azure AD B2C 在授权标头中发送的任何字符串。 
 
::: zone-end

## <a name="rest-api-platform"></a>REST API 平台

REST API 可以基于任何平台并以任何编程语言进行编写，但前提需保证它是安全的，并且可以发送和接收 JSON 格式的声明。

对 REST API 服务的请求来自 Azure AD B2C 服务器。 必须将 REST API 服务发布到可公开访问的 HTTPS 终结点。 REST API 调用将从 Azure 数据中心 IP 地址抵达。

请对 REST API 服务及其底层组件（例如数据库和文件系统）采用高可用性设计。


## <a name="next-steps"></a>后续步骤



::: zone pivot="b2c-user-flow"

- 了解如何[向用户流添加 API 连接器](add-api-connector.md)
- 开始使用我们的[示例](code-samples.md#api-connectors)。

::: zone-end

::: zone pivot="b2c-custom-policy"

有关使用 RESTful 技术配置文件的示例，请参阅以下文章：

- [演练：在 Azure AD B2C 用户旅程中以用户输入验证的形式集成 REST API 声明交换](custom-policy-rest-api-claims-validation.md)
- [演练：在 Azure Active Directory B2C 中将 REST API 声明交换添加到自定义策略](custom-policy-rest-api-claims-validation.md)
- [保护 REST API 服务](secure-rest-api.md)
- [参考：RESTful 技术配置文件](restful-technical-profile.md)

::: zone-end