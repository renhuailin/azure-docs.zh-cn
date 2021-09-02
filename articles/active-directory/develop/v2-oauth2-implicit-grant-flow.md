---
title: OAuth 2.0 隐式授权流 - Microsoft 标识平台 | Azure
description: 使用 Microsoft 标识平台隐式流保护单页应用。
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 07/19/2021
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 8c8c6fd5662637161f98f062234686ec77b92c76
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123105661"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Microsoft 标识平台和隐式授权流

Microsoft 标识平台支持 [OAuth 2.0 规范](https://tools.ietf.org/html/rfc6749#section-4.2)中所述的 OAuth 2.0 隐式授权流。 隐式授权的定义特征是令牌（ID 令牌或访问令牌）直接从 /authorize 终结点返回，而不是从 /token 终结点返回。 这通常在所谓的“混合流”中用作[授权代码流](v2-oauth2-auth-code-flow.md)的一部分 - 在 /authorize 请求中检索 ID 令牌以及授权代码。

[!INCLUDE [suggest-msal-from-protocols](includes/suggest-msal-from-protocols.md)]

[!INCLUDE [try-in-postman-link](includes/try-in-postman-link.md)]

## <a name="prefer-the-auth-code-flow"></a>首选授权代码流

由于计划[从浏览器中删除第三方 Cookie](reference-third-party-cookies-spas.md)，因此，隐式授权流不再是合适的身份验证方法。  如果没有第三方 Cookie，隐式流的[无提示 SSO 功能](#getting-access-tokens-silently-in-the-background)将不起作用，导致应用程序在尝试获取新令牌时中断。 强烈建议你让所有新的应用程序都使用[授权代码流](v2-oauth2-auth-code-flow.md)（现在替代隐式流来支持单页应用），并让[现有的单页应用也开始迁移到授权代码流](migrate-spa-implicit-to-auth-code.md)。

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>OAuth2 隐式授权的适用方案

只有对于登录流的初始交互式部分（其中缺少[第三方 Cookie](reference-third-party-cookies-spas.md) 不会影响你的应用程序），隐式授权才是可靠的。 此限制意味着，你只应将其用作混合流的一部分，在混合流中，你的应用程序从授权终结点请求代码和令牌。 这可确保应用程序收到可兑换为刷新令牌的代码，从而确保应用的登录会话在一段时间内保持有效。

## <a name="protocol-diagram"></a>协议图

下图显示了整个隐式登录流的样子，后续各部分更详细地介绍了每个步骤。

![显示隐式登录流的关系图](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>发送登录请求

最初将用户登录到应用时，可以发送 [OpenID Connect](v2-protocols-oidc.md) 身份验证请求，并从 Microsoft 标识平台获取 `id_token`。

> [!IMPORTANT]
> 若要成功请求 ID 令牌和/或访问令牌，必须通过在“隐式授权和混合流”部分中选择“ID 令牌”和“访问令牌”，为 [Azure 门户 - 应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)页中的应用注册启用相应的隐式授权流  。 如果未启用，将返回 `unsupported_response` 错误：`The provided value for the input parameter 'response_type' is not allowed for this client. Expected value is 'code'`

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid
&response_mode=fragment
&state=12345
&nonce=678910
```

> [!TIP]
> 若要使用隐式流测试登录，请单击 <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize..</a>在登录之后，浏览器应重定向到 `https://localhost/myapp/` ，并且地址栏中有一个 `id_token` 。
>

| 参数 | 类型 | 说明 |
| --- | --- | --- |
| `tenant` | 必填 |请求路径中的 `{tenant}` 值可用于控制哪些用户可以登录应用程序。 可以使用的值包括 `common`、`organizations`、`consumers` 和租户标识符。 有关详细信息，请参阅[协议基本信息](active-directory-v2-protocols.md#endpoints)。至关重要的是，对于从一个租户登录到另一个租户的来宾方案，必须提供租户标识符才能让他们正确登录到资源租户。|
| `client_id` | 必填 | [Azure 门户 - 应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)页分配给应用的应用程序（客户端）ID。 |
| `response_type` | 必填 |必须包含 OpenID Connect 登录的 `id_token` 。 也可以包含 response_type `token`。 此处使用 `token` ，让应用能够立即从授权终结点接收访问令牌，而无需向授权终结点发出第二次请求。 如果使用 `token` response_type，`scope` 参数必须包含范围，以指出要对哪个资源（例如，Microsoft Graph 上的 user.read）发出令牌。 它还可以包含可提供授权代码的 `code`（取代 `token`），该授权代码在[授权代码流](v2-oauth2-auth-code-flow.md)中使用。 此“id_token+代码”响应有时称为混合流。  |
| `redirect_uri` | 建议 |应用的 redirect_uri，应用可向其发送及从其接收身份验证响应。 它必须完全符合在门户中注册的其中一个 redirect_uris，否则必须是编码的 url。 |
| `scope` | 必填 |[范围](v2-permissions-and-consent.md)的空格分隔列表。 对于 OpenID Connect (id_token)，它必须包含范围 `openid`，该范围在许可 UI 中会转换为“将你登录”权限。 或者，也可以包含 `email` 和 `profile` 范围，以获取对其他用户数据的访问权限。 也可以在此请求中包含其他范围，以请求对各种资源的许可（如果请求了访问令牌）。 |
| `response_mode` | 可选 |指定将生成的令牌送回到应用程序时应该使用的方法。 默认为仅查询访问令牌，但如果请求包括 id_token，则会进行分段。 |
| `state` | 建议 |同样随令牌响应返回的请求中所包含的值。 可以是想要的任何内容的字符串。 随机生成的唯一值通常用于 [防止跨站点请求伪造攻击](https://tools.ietf.org/html/rfc6749#section-10.12)。 该 state 也用于在身份验证请求出现之前，于应用中编码用户的状态信息，例如之前所在的网页或视图。 |
| `nonce` | 必填 |包含在请求中的值，由应用生成，这些值将作为声明包含在生成的 id_token 中。 然后，应用可以验证此值，以减少令牌重播攻击。 该值通常是随机的唯一字符串，可用于标识请求的来源。 只有请求 id_token 时才是必需的。 |
| `prompt` | 可选 |表示需要的用户交互类型。 目前的有效值为“login”、“none”、“select_account”和“consent”。 `prompt=login` 将强制用户在该请求上输入凭据，取消单一登录。 `prompt=none` 则相反 - 它确保不对用户显示任何交互式提示。 如果请求无法通过单一登录静默完成，则 Microsoft 标识平台将返回错误。 `prompt=select_account` 将用户发送到一个帐户选取器，其中将显示在会话中记住的所有帐户。 `prompt=consent` 会在用户登录之后触发 OAuth 同意对话框，要求用户向应用授予权限。 |
| `login_hint` | 可选 | 如果事先知道用户名，可以使用此参数预先填充用户登录页的用户名/电子邮件地址字段。 通常，应用在已从上次登录提取 `login_hint` [可选声明](active-directory-optional-claims.md)后，会在重新身份验证时使用此参数。 |
| `domain_hint` | 可选 |如果包含，它跳过用户在登录页上经历的基于电子邮件的发现过程，导致稍微更加流畅的用户体验。 此参数通常用于在单个租户中运行的业务线应用，它们会提供给定租户中的域名，将用户转发给该租户的联合身份验证提供程序。  请注意，此提示会阻止来宾登录到此应用程序，并限制 FIDO 之类的云凭据的使用。  |

此时，将请求用户输入凭据并完成身份验证。 Microsoft 标识平台还会确保用户已许可 `scope` 查询参数中指定的权限。 如果用户未曾同意这些权限的任何一项，就请求用户同意请求的权限。 有关详细信息，请参阅[权限、同意和多租户应用](v2-permissions-and-consent.md)。

用户经过身份验证并授予许可后，Microsoft 标识平台会使用 `response_mode` 参数中指定的方法，将响应返回到位于所指示的 `redirect_uri` 的应用。

#### <a name="successful-response"></a>成功的响应

使用 `response_mode=fragment` 和 `response_type=id_token+code` 的成功响应如下所示（为方便阅读，包含了换行符）：

```HTTP
GET https://localhost/myapp/#
code=0.AgAAktYV-sfpYESnQynylW_UKZmH-C9y_G1A
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| 参数 | 说明 |
| --- | --- |
| `code` | 如果 `response_type` 包含 `code`，则包含该参数。 这是适合在[授权代码流](v2-oauth2-auth-code-flow.md)中使用的授权代码。  |
| `access_token` |如果 `response_type` 包含 `token`，则包含该参数。 应用请求的访问令牌。 访问令牌不得进行解码或检查，应当作为不透明字符串对待。 |
| `token_type` |如果 `response_type` 包含 `token`，则包含该参数。 始终为 `Bearer`。 |
| `expires_in`|如果 `response_type` 包含 `token`，则包含该参数。 表示令牌有效的秒数（针对缓存目的）。 |
| `scope` |如果 `response_type` 包含 `token`，则包含该参数。 表示 access_token 的有效范围。 如果某些范围不适用于用户，则可能未包括所请求的所有范围（例如，当使用个人帐户登录时，将仅包括所请求的仅限 Azure AD 的范围）。 |
| `id_token` | 有符号 JSON Web 令牌 (JWT)。 应用可以解码此令牌的段，以请求已登录用户的相关信息。 应用可以缓存并显示值，但不应依赖于这些值来获取任何授权或安全边界。 有关 id_tokens 的详细信息，请参阅 [`id_token reference`](id-tokens.md)。 <br> **注意：** 仅当请求了 `openid` 作用域且 `response_type` 包括 `id_tokens` 时才提供。 |
| `state` |如果请求中包含 state 参数，响应中就应该出现相同的值。 应用应该验证请求和响应中的 state 值是否完全相同。 |

[!INCLUDE [remind-not-to-validate-access-tokens](includes/remind-not-to-validate-access-tokens.md)]

#### <a name="error-response"></a>错误响应

错误响应可能也发送到 `redirect_uri` ，让应用可以适当地处理：

```HTTP
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| 参数 | 说明 |
| --- | --- |
| `error` |可用于分类发生的错误类型与响应错误的错误码字符串。 |
| `error_description` |帮助开发人员识别身份验证错误根本原因的特定错误消息。 |

## <a name="getting-access-tokens-silently-in-the-background"></a>在后台以无提示方式获取访问令牌

> [!Important]
> 隐式流的这一部分不太可能适用于你的应用程序，因为[默认情况下删除了第三方 Cookie](reference-third-party-cookies-spas.md)，所以它用于各种浏览器中。  虽然目前这仍然适用于不在 Incognito 中的基于 Chromium 的浏览器，但开发人员应该重新考虑这部分流的使用。 在不支持第三方 Cookie 的浏览器中，你会收到一条错误消息，指出没有用户登录，因为浏览器删除了登录页面的会话 Cookie。 

已经将用户登录到单页应用，现在可以通过无提示方式获取访问令牌以调用受到 Microsoft 标识平台保护的 Web API，例如 [Microsoft Graph](https://developer.microsoft.com/graph)。 即使已使用 `token` response_type 收到令牌，仍然可以使用此方法获取其他资源的令牌，而无需再次将用户重定向到登录页。

在正常的 OpenID Connect/OAuth 流中，可以通过对 Microsoft 标识平台 `/token` 终结点进行请求来实现此目的。 你可以在隐藏的 iframe 中进行请求，以获取其他 Web API 的新令牌：

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&response_mode=fragment
&state=12345
&nonce=678910
&prompt=none
&login_hint=myuser@mycompany.com
```

有关 URL 中的查询参数的详细信息，请参阅[发送登录请求](#send-the-sign-in-request)。

> [!TIP]
> 请尝试将以下请求复制并粘贴到浏览器选项卡中！ （不要忘记使用适用于用户的正确值替换 `login_hint` 值）
>
>`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&login_hint={your-username}`
>
> 请注意，即使是在不支持第三方 Cookie 的浏览器中，也可以这样做，因为你要将其直接输入到浏览器栏中，而不是在 iframe 中打开它。 

借助 `prompt=none` 参数，此请求立即成功或立即失败，并返回应用程序。 响应会通过 `response_mode` 参数中指定的方法，发送到位于所指示的 `redirect_uri` 的应用。

#### <a name="successful-response"></a>成功的响应

使用 `response_mode=fragment` 的成功响应如下所示：

```HTTP
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fdirectory.read
```

| 参数 | 说明 |
| --- | --- |
| `access_token` |如果 `response_type` 包含 `token`，则包含该参数。 应用请求的访问令牌，在本例中为 Microsoft Graph 的访问令牌。 访问令牌不得进行解码或检查，应当作为不透明字符串对待。 |
| `token_type` | 始终为 `Bearer`。 |
| `expires_in` | 表示令牌有效的秒数（针对缓存目的）。 |
| `scope` | 表示 access_token 的有效范围。 如果某些范围不适用于用户，则可能未包括所请求的所有范围（例如，当使用个人帐户登录时，将仅包括所请求的仅限 Azure AD 的范围）。 |
| `id_token` | 有符号 JSON Web 令牌 (JWT)。 如果 `response_type` 包含 `id_token`，则包含该参数。 应用可以解码此令牌的段，以请求已登录用户的相关信息。 应用可以缓存并显示值，但不应依赖于这些值来获取任何授权或安全边界。 有关 id_tokens 的详细信息，请参阅[`id_token`参考](id-tokens.md)。 <br> **注意：** 仅当已请求 `openid` 作用域时提供。 |
| `state` |如果请求中包含 state 参数，响应中就应该出现相同的值。 应用应该验证请求和响应中的 state 值是否完全相同。 |

#### <a name="error-response"></a>错误响应

错误响应还可能发送到 `redirect_uri` ，因此应用可以适当地对其进行处理。 如果是 `prompt=none`，预期的错误为：

```HTTP
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| 参数 | 说明 |
| --- | --- |
| `error` |可用于分类发生的错误类型与响应错误的错误码字符串。 |
| `error_description` |帮助开发人员识别身份验证错误根本原因的特定错误消息。 |

如果在 iframe 请求中收到此错误，用户必须再次以交互方式登录以检索新令牌。 可以选择对应用程序合理的任何方式处理这种情况。

## <a name="refreshing-tokens"></a>刷新令牌

隐式授权不提供刷新令牌。 `id_token` 和 `access_token` 很快就会过期，因此应用必须准备好定期刷新这些令牌。 若要刷新任一类型的令牌，可以通过使用 `prompt=none` 参数控制 Microsoft 标识平台的行为，来执行上述同一隐藏的 iframe 请求。 若要接收新的 `id_token`，请务必使用 `response_type` 和 `scope=openid` 中的 `id_token`，以及 `nonce` 参数。

在不支持第三方 Cookie 的浏览器中，这会导致错误，指出没有用户登录。 

## <a name="send-a-sign-out-request"></a>发送注销请求

OpenID Connect `end_session_endpoint` 允许应用向 Microsoft 标识平台发送请求以结束用户的会话，并清除 Microsoft 标识平台设置的 Cookie。 要完全将用户从 Web 应用程序注销，应用应结束自己与用户的会话（通常通过清除令牌缓存或删除 Cookie 实现），然后将浏览器重定向到：

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| 参数 | 类型 | 说明 |
| --- | --- | --- |
| `tenant` |必填 |请求路径中的 `{tenant}` 值可用于控制哪些用户可以登录应用程序。 可以使用的值包括 `common`、`organizations`、`consumers` 和租户标识符。 有关详细信息，请参阅[协议基础知识](active-directory-v2-protocols.md#endpoints)。 |
| `post_logout_redirect_uri` | 建议 | 注销完成后用户应返回到的 URL。 此值必须与为应用程序注册的重定向 URI 之一匹配。 如果未包含，Microsoft 标识平台会向用户显示一条常规消息。 |

## <a name="next-steps"></a>后续步骤

* 重温 [MSAL JS 示例](sample-v2-code.md)以开始编码。
* 复习[授权代码流](v2-oauth2-auth-code-flow.md)这种较新的、可取代隐式授权的授权方式。 
