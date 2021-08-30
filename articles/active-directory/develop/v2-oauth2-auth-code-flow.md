---
title: Microsoft 标识平台和 OAuth 2.0 授权代码流 | Azure
titleSuffix: Microsoft identity platform
description: 使用 OAuth 2.0 身份验证协议的 Microsoft 标识平台实现来生成 Web 应用程序。
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
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 0042d12941107c4704364dc261f95d3521b8208f
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114464141"
---
# <a name="microsoft-identity-platform-and-oauth-20-authorization-code-flow"></a>Microsoft 标识平台和 OAuth 2.0 授权代码流

OAuth 2.0 授权代码授予可用于设备上所安装的应用，以访问受保护的资源，例如 Web API。 使用 OAuth 2.0 的 Microsoft 标识平台实现，可以将登录名及 API 访问添加到移动应用和桌面应用。

本文介绍如何使用任何语言直接针对应用程序中的协议编程。  如果可能，建议改为使用受支持的 Microsoft 身份验证库 (MSAL) 来[获取令牌并调用受保护的 Web API](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)。  另请参阅[使用 MSAL 的示例应用](sample-v2-code.md)。

[OAuth 2.0 规范第 4.1 部分](https://tools.ietf.org/html/rfc6749)描述了 OAuth 2.0 授权代码流。 它用于在大部分的应用类型（包括[单页应用](v2-app-types.md#single-page-apps-javascript)、[Web 应用](v2-app-types.md#web-apps)和[本机安装的应用](v2-app-types.md#mobile-and-native-apps)）中执行身份验证与授权。 通过此流，应用能安全地获取 access_token、刷新令牌和登录用户的 ID 令牌，其中 access_token 可用于访问受 Microsoft 标识平台保护的资源，而刷新令牌用于获取其他 access_token。

[!INCLUDE [try-in-postman-link](includes/try-in-postman-link.md)]

## <a name="protocol-diagram"></a>协议图

概括而言，应用程序的整个身份验证流看起来有点类似于：

![OAuth 授权代码流](./media/v2-oauth2-auth-code-flow/convergence-scenarios-native.svg)

## <a name="redirect-uri-setup-required-for-single-page-apps"></a>单页应用所需的重定向 URI 设置

单页应用程序的授权代码流需要一些其他设置。  按照[创建单页应用程序](scenario-spa-app-registration.md#redirect-uri-msaljs-20-with-auth-code-flow)的说明将重定向 URI 正确地标记为已为 CORS 启用。 若要更新现有的重定向 URI 以启用 CORS，请打开清单编辑器，然后在 `replyUrlsWithType` 部分中将重定向 URI 的 `type` 字段设置为 `spa`。 还可以在“身份验证”选项卡的“Web”部分单击重定向 URI，然后使用授权代码流选择要迁移到的 URI。

`spa` 重定向类型与隐式流向后兼容。 当前使用隐式流来获取令牌的应用可以移动到 `spa` 重定向 URI 类型，而不会出现问题，并会继续使用隐式流。

如果尝试使用授权代码流并查看此错误：

`access to XMLHttpRequest at 'https://login.microsoftonline.com/common/v2.0/oauth2/token' from origin 'yourApp.com' has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.`

然后访问应用注册，并更新应用的重定向 URI 以键入 `spa`。

应用程序不能使用具有本机应用程序或客户端凭据流等非 SPA 流的 `spa` 重定向 URI。 为确保安全性和最佳做法，如果尝试使用不含 `Origin` 标头的 `spa` 重定向 URI，Microsoft 标识平台将返回错误。 同样，Microsoft 标识平台还会阻止在存在 `Origin` 标头的情况下使用客户端凭据（在 OBO 流、客户端凭据流和身份验证代码流中），以确保不会在浏览器中使用机密。 

## <a name="request-an-authorization-code"></a>请求授权代码

授权代码流始于客户端将用户定向到 `/authorize` 终结点。 在此请求中，客户端向用户请求 `openid`、`offline_access` 和 `https://graph.microsoft.com/mail.read ` 权限。  某些权限受管理员限制，例如，使用 `Directory.ReadWrite.All` 将数据写入组织的目录。 如果应用程序向组织用户请求访问以下权限之一，则用户会收到错误消息，显示他们未经授权，无法同意应用的权限。 若要请求访问受管理员限制的范围，应该直接向全局管理员请求。  有关详细信息，请参阅[管理员限制的权限](v2-permissions-and-consent.md#admin-restricted-permissions)。

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read%20api%3A%2F%2F
&state=12345
&code_challenge=YTFjNjI1OWYzMzA3MTI4ZDY2Njg5M2RkNmVjNDE5YmEyZGRhOGYyM2IzNjdmZWFhMTQ1ODg3NDcxY2Nl
&code_challenge_method=S256
```

> [!TIP]
> 单击下面的链接以执行此请求！ 登录之后，浏览器应重定向至地址栏中具有 `code` 的 `http://localhost/myapp/`。
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| 参数    | 必需/可选 | 说明 |
|--------------|-------------|--------------|
| `tenant`    | 必需    | 请求路径中的 `{tenant}` 值可用于控制哪些用户可以登录应用程序。 可以使用的值包括 `common`、`organizations`、`consumers` 和租户标识符。 有关详细信息，请参阅[协议基础知识](active-directory-v2-protocols.md#endpoints)。 至关重要的是，对于用户从一个租户登录到另一个租户的来宾场景，必须提供租户标识符才能让其正确登录到资源租户。|
| `client_id`   | 必填    | [Azure 门户 – 应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)体验分配给你的应用的应用程序（客户端）ID。  |
| `response_type` | 必填    | 必须包括授权代码流的 `code` 。 如果使用[混合流](#request-an-id-token-as-well-hybrid-flow)，则还可以包括 `id_token` 或 `token`。 |
| `redirect_uri`  | 必需 | 应用的 redirect_uri，应用可向其发送及从其接收身份验证响应。 它必须完全符合在门户中注册的其中一个 redirect_uris，否则必须是编码的 url。 对于本机应用和移动应用，应使用推荐值之一 - `https://login.microsoftonline.com/common/oauth2/nativeclient`（适用于使用嵌入式浏览器的应用）或 `http://localhost`（适用于使用系统浏览器的应用）。 |
| `scope`  | 必需    | 希望用户同意的[范围](v2-permissions-and-consent.md)的空格分隔列表。  对于请求的 `/authorize` 段，这可以涵盖多个资源，从而允许应用获得你要调用的多个 Web API 的同意。 |
| `response_mode`   | 建议 | 指定将生成的令牌送回到应用程序时应该使用的方法。 可以是以下值之一：<br/><br/>- `query`<br/>- `fragment`<br/>- `form_post`<br/><br/>`query` 在重定向 URI 上提供代码作为查询字符串参数。 如果要使用隐式流请求 ID 令牌，则不能使用 [OpenID 规范](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations)中指定的 `query`。如果只是请求代码，则可以使用 `query`、`fragment` 或 `form_post`。 `form_post` 对重定向 URI 执行包含代码的 POST。 |
| `state`                 | 建议 | 同样随令牌响应返回的请求中所包含的值。 可以是想要的任何内容的字符串。 随机生成的唯一值通常用于 [防止跨站点请求伪造攻击](https://tools.ietf.org/html/rfc6749#section-10.12)。 在发出身份验证请求出现之前，此值对有关用户在应用中的状态的信息（例如前面所在的页面或视图）进行编码。 |
| `prompt`  | 可选    | 表示需要的用户交互类型。 此时唯一有效的值为 `login`、`none`、`consent` 和 `select_account`。<br/><br/>- `prompt=login` 将强制用户在该请求上输入其凭据，从而使单一登录无效。<br/>- `prompt=none` 则相反 - 它确保不对用户显示任何交互式提示。 如果无法在无提示的情况下通过单一登录完成请求，Microsoft 标识平台将返回 `interaction_required` 错误。<br/>- `prompt=consent` 在用户登录后将触发 OAuth 同意对话框，要求用户向应用授予权限。<br/>- `prompt=select_account` 将中断单一登录，提供帐户选择体验（列出所有帐户（会话中的帐户或任何记住的帐户），或提供用于选择使用其他帐户的选项）。<br/> |
| `login_hint` | 可选 | 如果事先知道用户名，可以使用此参数预先填充用户登录页的用户名/电子邮件地址字段。 通常，应用在已从上次登录提取 `login_hint` [可选声明](active-directory-optional-claims.md)后，会在重新身份验证时使用此参数。 |
| `domain_hint`  | 可选    | 如果已包含在内，它将跳过用户在登录页面上经历的基于电子邮件的发现过程，从而实现更加流畅的用户体验，例如，将其发送给联合标识提供者。 通常，应用将在重新身份验证期间使用此参数，方法是从上次登录提取 `tid`。  |
| `code_challenge`  | 建议/必需 | 用于通过 Proof Key for Code Exchange (PKCE) 保护授权代码授权。 如果包含 `code_challenge_method`，则需要。 有关详细信息，请参阅 [PKCE RFC](https://tools.ietf.org/html/rfc7636)。 现在建议将此参数用于所有应用程序类型（公共客户端和机密客户端），并且 Microsoft 标识平台需要将此参数用于[使用授权代码流的单页应用](reference-third-party-cookies-spas.md)。 |
| `code_challenge_method` | 建议/必需 | 用于为 `code_challenge` 参数编码 `code_verifier` 的方法。 它应该为 `S256`，但是如果客户端出于某种原因不能支持 SHA256，则该规范允许使用 `plain`。 <br/><br/>如果已排除在外，且包含了 `code_challenge`，则假定 `code_challenge` 为纯文本。 Microsoft 标识平台支持 `plain` 和 `S256`。 有关详细信息，请参阅 [PKCE RFC](https://tools.ietf.org/html/rfc7636)。 这是[使用授权代码流的单页应用](reference-third-party-cookies-spas.md)所必需的。|


此时，将请求用户输入凭据并完成身份验证。 Microsoft 标识平台还将确保用户已许可 `scope` 查询参数中指定的权限。 如果用户未曾同意这些权限的任何一项，则请求用户同意请求的权限。 [此处提供了权限、许可与多租户应用](v2-permissions-and-consent.md)的详细信息。

用户经过身份验证并授予许可后，Microsoft 标识平台会使用 `response_mode` 参数中指定的方法，将响应返回到位于所指示的 `redirect_uri` 的应用中。

#### <a name="successful-response"></a>成功的响应

使用 `response_mode=query` 的成功响应如下所示：

```HTTP
GET http://localhost?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| 参数 | 说明  |
|-----------|--------------|
| `code` | 应用请求的 authorization_code。 应用可以使用授权代码请求目标资源的访问令牌。 Authorization_codes 的生存期较短，通常在约 10 分钟后即过期。 |
| `state` | 如果请求中包含 state 参数，响应中就应该出现相同的值。 应用应该验证请求和响应中的 state 值是否完全相同。 |

如果你请求 ID 令牌并在应用程序注册中启用了隐式授权，则还会收到 ID 令牌。  这有时称为[“混合流”](#request-an-id-token-as-well-hybrid-flow)，由 ASP.NET 之类的框架使用。

#### <a name="error-response"></a>错误响应

错误响应可能也发送到 `redirect_uri` ，让应用可以适当地处理：

```HTTP
GET http://localhost?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| 参数 | 说明  |
|----------|------------------|
| `error`  | 可用于分类发生的错误类型与响应错误的错误码字符串。 |
| `error_description` | 帮助开发人员识别身份验证错误根本原因的特定错误消息。 |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>授权终结点错误的错误代码

下表描述了可在错误响应的 `error` 参数中返回的各个错误代码。

| 错误代码  | 说明    | 客户端操作   |
|-------------|----------------|-----------------|
| `invalid_request` | 协议错误，例如，缺少必需的参数。 | 修复并重新提交请求。 这通常是在初始测试期间捕获的开发错误。 |
| `unauthorized_client` | 不允许客户端应用程序请求授权代码。 | 客户端应用程序未注册到 Azure AD 中或者未添加到用户的 Azure AD 租户时，通常会出现此错误。 应用程序可以提示用户，并说明如何安装应用程序并将其添加到 Azure AD。 |
| `access_denied`  | 资源所有者拒绝了许可  | 客户端应用程序可以通知用户，除非用户许可，否则无法继续。 |
| `unsupported_response_type` | 授权服务器不支持请求中的响应类型。 | 修复并重新提交请求。 这通常是在初始测试期间捕获的开发错误。 在[混合流](#request-an-id-token-as-well-hybrid-flow)中出现此错误表明你必须在客户端应用注册上启用 ID 令牌隐式授权设置。 |
| `server_error`  | 服务器遇到意外的错误。| 重试请求。 这些错误可能是临时状况导致的。 客户端应用程序可能向用户说明，其响应由于临时错误而延迟。 |
| `temporarily_unavailable`   | 服务器暂时繁忙，无法处理请求。 | 重试请求。 客户端应用程序可向用户说明，其响应由于临时状况而延迟。 |
| `invalid_resource`  | 目标资源无效，原因是它不存在，Azure AD 找不到它，或者未正确配置。 | 此错误表示未在租户中配置该资源（如果存在）。 应用程序可以提示用户，并说明如何安装应用程序并将其添加到 Azure AD。 |
| `login_required` | 找到的用户太多或未找到任何用户 | 客户端请求了静默身份验证 (`prompt=none`)，但找不到单个用户。 这可能表示会话中有多个处于活动状态的用户或无用户。 此时会考虑所选的租户（例如，如果有 2 个处于活动状态的 AD 帐户和一个 Microsoft 帐户，并且已选择 `consumers`，则会执行静默身份验证）。 |
| `interaction_required` | 请求需要用户交互。 | 需要额外的身份验证步骤或许可。 请在没有 `prompt=none` 的情况下重试请求。 |

### <a name="request-an-id-token-as-well-hybrid-flow"></a>同时请求一个 ID 令牌（混合流）

为了在兑换授权代码之前了解用户是谁，应用程序在请求授权代码时通常也会请求 ID 令牌。 这称为混合流，因为它混合使用隐式授权与授权代码流。 混合流通常用于希望在不阻止代码兑换的情况下为用户呈现页面的 Web 应用，特别是 [ASP.NET](quickstart-v2-aspnet-core-webapp.md)。 在此模型中，单页应用和传统 Web 应用都会受益于延迟降低。

混合流与前面所述的授权代码流相同，但有以下三个添加项，这三个添加项都是请求 ID 令牌所需的：新的作用域、新的 response_type 和新的 `nonce` 查询参数。

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code%20id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=fragment
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&state=12345
&nonce=abcde
&code_challenge=YTFjNjI1OWYzMzA3MTI4ZDY2Njg5M2RkNmVjNDE5YmEyZGRhOGYyM2IzNjdmZWFhMTQ1ODg3NDcxY2Nl
&code_challenge_method=S256
```

| 更新后的参数 | 必需/可选 | 说明 |
|---------------|-------------|--------------|
|`response_type`| 必须 | 添加 `id_token` 是向服务器表明：应用程序希望 `/authorize` 终结点的响应中有一个 ID 令牌。  |
|`scope`| 必须 | 对于 ID 令牌，必须对其进行更新以包括 ID 令牌作用域 - `openid`，还可以包括 `profile` 和 `email`。 |
|`nonce`| 必须|     包含在请求中的值，由应用生成，这些值将作为声明包含在生成的 id_token 中。 然后，应用可以验证此值，以减少令牌重播攻击。 该值通常是随机的唯一字符串，可用于标识请求的来源。 |
|`response_mode`| 建议 | 指定将生成的令牌送回到应用程序时应该使用的方法。 默认为 `query` 的目的是仅发送授权代码，但如果请求包括 id_token `response_type`，则为 `fragment`。  但是，建议应用使用 `form_post`，尤其是使用 `http://localhost` 作为重定向 URI 时。 |

使用 `fragment` 作为响应模式可能会导致从重定向读取代码的 Web 应用出现问题，因为浏览器不会将片段传递给 Web 服务器。  在这些情况下，应用应该使用 `form_post` 响应模式，以确保将所有数据发送到服务器。 

#### <a name="successful-response"></a>成功的响应

使用 `response_mode=fragment` 的成功响应如下所示：

```HTTP
GET https://login.microsoftonline.com/common/oauth2/nativeclient#
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&id_token=eYj...
&state=12345
```

| 参数 | 说明  |
|-----------|--------------|
| `code` | 应用程序请求的授权代码。 应用可以使用授权代码请求目标资源的访问令牌。 授权代码的生存期较短，通常在约 10 分钟后过期。 |
| `id_token` | 用户的 ID 令牌，通过隐式授权颁发。 包含一个特殊的 `c_hash` 声明，该声明是同一请求中 `code` 的哈希。 |
| `state` | 如果请求中包含 state 参数，响应中就应该出现相同的值。 应用应验证请求和响应中的状态值是否相同。 |

## <a name="redeem-a-code-for-an-access-token"></a>兑换访问令牌的代码

所有机密客户端都可以选择使用客户端机密（由 Microsoft 标识平台生成的对称共享机密）和[证书凭据](active-directory-certificate-credentials.md)（开发人员上传的非对称密钥）。  为了获得最佳安全性，我们建议使用证书凭据。 兑换授权代码时，公共客户端（本机应用程序和单页应用）不得使用机密或证书。请始终确保重定向 URI 正确指示应用程序的类型，并且[是唯一的](reply-url.md#localhost-exceptions)。 

### <a name="request-an-access-token-with-a-client_secret"></a>使用 client_secret 请求访问令牌

现在已获取 authorization_code 并获得用户授权，可兑换 `code` 以获取所需资源的 `access_token`。 通过向 `/token` 终结点发送 `POST` 请求来完成此操作：

```HTTP
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&code_verifier=ThisIsntRandomButItNeedsToBe43CharactersLong 
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps. This secret needs to be URL-Encoded.
```

| 参数  | 必需/可选 | 说明     |
|------------|-------------------|----------------|
| `tenant`   | 必需   | 请求路径中的 `{tenant}` 值可用于控制哪些用户可以登录应用程序。 可以使用的值包括 `common`、`organizations`、`consumers` 和租户标识符。 有关详细信息，请参阅[协议基础知识](active-directory-v2-protocols.md#endpoints)。  |
| `client_id` | 必填  | [Azure 门户 – 应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)页分配给你的应用的应用程序（客户端）ID。 |
| `scope`      | 可选   | 范围的空格分隔列表。 范围必须全部来自单个资源，以及 OIDC范围（`profile`、`openid`、`email`）。 有关范围更加详细的说明，请参阅[权限、许可和范围](v2-permissions-and-consent.md)。 这是授权代码流的 Microsoft 扩展，旨在允许应用在令牌兑换期间声明其需要令牌的资源。|
| `code`          | 必填  | 在流的第一个阶段中获取的 authorization_code。 |
| `redirect_uri`  | 必需  | 用于获取 authorization_code 的相同 redirect_uri 值。 |
| `grant_type` | 必需   | 必须是授权代码流的 `authorization_code` 。   |
| `code_verifier` | 建议  | 即用于获取 authorization_code 的 code_verifier。 如果在授权码授权请求中使用 PKCE，则需要。 有关详细信息，请参阅 [PKCE RFC](https://tools.ietf.org/html/rfc7636)。 |
| `client_secret` | 机密 Web 应用所需 | 在应用注册门户中为应用创建的应用程序机密。 不应在本机应用或单页应用中使用应用程序密钥，因为 client_secrets 无法可靠地存储在设备或网页上。 Web 应用和 Web API 都需要应用程序密钥，它能够将 client_secret 安全地存储在服务器端。  与此处讨论的所有参数一样，客户端机密在发送之前必须进行 URL 编码，这通常是由 SDK 执行的步骤。 有关 URI 编码的详细信息，请参阅 [URI 常规语法规范](https://tools.ietf.org/html/rfc3986#page-12)。 |

### <a name="request-an-access-token-with-a-certificate-credential"></a>使用证书凭据请求访问令牌

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&code_verifier=ThisIsntRandomButItNeedsToBe43CharactersLong
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
```

| 参数  | 必需/可选 | 说明     |
|------------|-------------------|----------------|
| `tenant`   | 必需   | 请求路径中的 `{tenant}` 值可用于控制哪些用户可以登录应用程序。 可以使用的值包括 `common`、`organizations`、`consumers` 和租户标识符。 有关详细信息，请参阅[协议基础知识](active-directory-v2-protocols.md#endpoints)。  |
| `client_id` | 必填  | [Azure 门户 – 应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)页分配给你的应用的应用程序（客户端）ID。 |
| `scope`      | 可选   | 范围的空格分隔列表。 范围必须全部来自单个资源，以及 OIDC范围（`profile`、`openid`、`email`）。 有关范围更加详细的说明，请参阅[权限、许可和范围](v2-permissions-and-consent.md)。 这是授权代码流的 Microsoft 扩展，旨在允许应用在令牌兑换期间声明其需要令牌的资源。|
| `code`          | 必填  | 在流的第一个阶段中获取的 authorization_code。 |
| `redirect_uri`  | 必需  | 用于获取 authorization_code 的相同 redirect_uri 值。 |
| `grant_type` | 必需   | 必须是授权代码流的 `authorization_code` 。   |
| `code_verifier` | 建议  | 即用于获取 authorization_code 的 code_verifier。 如果在授权码授权请求中使用 PKCE，则需要。 有关详细信息，请参阅 [PKCE RFC](https://tools.ietf.org/html/rfc7636)。 |
| `client_assertion_type` | 机密 Web 应用所需 | 必须将值设置为 `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` 才能使用证书凭据。 |
| `client_assertion` | 机密 Web 应用所需  | 断言（JSON Web 令牌），需使用作为凭据向应用程序注册的证书进行创建和签名。 有关如何注册证书以及断言的格式，请阅读[证书凭据](active-directory-certificate-credentials.md)的相关信息。|

请注意，这些参数与共享机密请求的参数相同，只不过 `client_secret` 参数替换为以下两个参数：`client_assertion_type` 和 `client_assertion`。  

### <a name="successful-response"></a>成功的响应

成功的令牌响应如下：

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```

| 参数     | 说明   |
|---------------|------------------------------|
| `access_token`  | 请求的访问令牌。 应用可以使用此令牌对受保护的资源（如 Web API）进行身份验证。 |
| `token_type`    | 指示令牌类型值。 Azure AD 唯一支持的类型是 Bearer |
| `expires_in`    | 访问令牌的有效期（以秒为单位）。 |
| `scope`         | access_token 的有效范围。 可选 - 这是非标准的，如果省略，令牌将用于流的初始阶段中所请求的范围。 |
| `refresh_token` | OAuth 2.0 刷新令牌。 应用可以使用此令牌，在当前的访问令牌过期之后获取其他访问令牌。 Refresh_tokens 的生存期很长，而且可以用于延长保留资源访问权限的时间。 有关刷新访问令牌的详细信息，请参阅[以下部分](#refresh-the-access-token)。 <br> **注意：** 仅当已请求 `offline_access` 作用域时提供。 |
| `id_token`      | JSON Web 令牌 (JWT)。 应用可以解码此令牌的段，以请求已登录用户的相关信息。 应用可以缓存并显示值，机密客户端可以使用这些值进行授权。 有关 id_tokens 的详细信息，请参阅 [`id_token reference`](id-tokens.md)。 <br> **注意：** 仅当已请求 `openid` 作用域时提供。 |

### <a name="error-response"></a>错误响应

错误响应如下所示：

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| 参数         | 说明    |
|-------------------|----------------|
| `error`       | 可用于分类发生的错误类型与响应错误的错误码字符串。 |
| `error_description` | 帮助开发人员识别身份验证错误根本原因的特定错误消息。 |
| `error_codes` | 可帮助诊断的 STS 特定错误代码列表。  |
| `timestamp`   | 发生错误的时间。 |
| `trace_id`    | 可帮助诊断的请求唯一标识符。 |
| `correlation_id` | 可帮助跨组件诊断的请求唯一标识符。 |

### <a name="error-codes-for-token-endpoint-errors"></a>令牌终结点错误的错误代码

| 错误代码         | 说明        | 客户端操作    |
|--------------------|--------------------|------------------|
| `invalid_request`  | 协议错误，例如，缺少必需的参数。 | 修复请求或应用注册，然后重新提交请求   |
| `invalid_grant`    | 授权代码或 PKCE 代码验证程序无效或已过期。 | 尝试向 `/authorize` 终结点发送新请求，并验证 code_verifier 参数是否正确。  |
| `unauthorized_client` | 经过身份验证的客户端无权使用此权限授予类型。 | 客户端应用程序未注册到 Azure AD 中或者未添加到用户的 Azure AD 租户时，通常会出现这种情况。 应用程序可以提示用户，并说明如何安装应用程序并将其添加到 Azure AD。 |
| `invalid_client` | 客户端身份验证失败。  | 客户端凭据无效。 若要修复，应用程序管理员应更新凭据。   |
| `unsupported_grant_type` | 授权服务器不支持权限授予类型。 | 更改请求中的授权类型。 这种类型的错误应该只在开发过程中发生，并且应该在初始测试过程中检测到。 |
| `invalid_resource` | 目标资源无效，原因是它不存在，Azure AD 找不到它，或者未正确配置。 | 这表示未在租户中配置该资源（如果存在）。 应用程序可以提示用户，并说明如何安装应用程序并将其添加到 Azure AD。  |
| `interaction_required` | 非标准，因为 OIDC 规范仅在 `/authorize` 终结点上调用它。 请求需要用户交互。 例如，需要额外的身份验证步骤。 | 使用同一范围重试 `/authorize` 请求。 |
| `temporarily_unavailable` | 服务器暂时繁忙，无法处理请求。 | 请在短暂延迟后重试该请求。 客户端应用程序可能向用户说明，其响应由于临时状况而延迟。 |
|`consent_required` | 请求需要用户同意。 此错误是非标准错误，因为根据 OIDC 规范，它通常仅在 `/authorize` 终结点上返回。 在客户端应用无权请求的代码兑换流上使用 `scope` 参数时返回。  | 客户端应将用户发送回具有正确范围的 `/authorize` 终结点，以便触发同意。 |
|`invalid_scope` | 应用请求的范围无效。  | 将身份验证请求中的 scope 参数值更新为有效值。 |

> [!NOTE]
> 单页应用可能会收到 `invalid_request` 错误，指明仅“单页应用程序”客户端类型允许进行跨源令牌兑换。  这表明用于请求令牌的重定向 URI 未标记为 `spa` 重定向 URI。  有关如何启用此流的信息，请查看[应用程序注册步骤](#redirect-uri-setup-required-for-single-page-apps)。

## <a name="use-the-access-token"></a>使用访问令牌

现已成功获取 `access_token`，可以通过在 `Authorization` 标头中包含令牌，在 Web API 的请求中使用令牌：

```HTTP
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-access-token"></a>刷新访问令牌

Access_token 生存期很短，必须在其过期后刷新，才能继续访问资源。 为此，可以向 `/token` 终结点提交另一个 `POST` 请求，但这次要提供 `refresh_token` 而不是 `code`。  刷新令牌对客户端已获得同意的所有权限有效 - 因此，对 `scope=mail.read` 请求发出的刷新令牌可用于请求 `scope=api://contoso.com/api/UseResource` 的新访问令牌。

Web 应用和本机应用的刷新令牌没有指定的生存期。 通常，刷新令牌的生存期相对较长。 但是，在某些情况下，刷新令牌会过期、被吊销，或缺少执行所需操作的足够权限。 应用程序需要正确预期和处理[令牌颁发终结点返回的错误](#error-codes-for-token-endpoint-errors)。 但是，单页应用会获得一个生存期为 24 小时的令牌，并且每天需要新的身份验证。  当第三方 cookie 处于启用状态时，可以在 iframe 中静默完成此操作，但必须在没有第三方 cookie 的浏览器（如 Safari）的顶级框架中完成。

尽管刷新令牌在用于获取新的访问令牌时不会被撤销，但应放弃旧的刷新令牌。 [OAuth 2.0 规范](https://tools.ietf.org/html/rfc6749#section-6)指出：“授权服务器可能会发出新的刷新令牌，在这种情况下，客户端必须丢弃旧的刷新令牌，并将其替换为新的刷新令牌。 向客户端颁发新的刷新令牌后，授权服务器可能会撤销旧的刷新令牌。”

>[!IMPORTANT]
> 对于发送到已注册为 `spa` 的重定向 URI 的刷新令牌，刷新令牌将在 24 小时后过期。 使用初始刷新令牌获取的其他刷新令牌将超过该过期时间，因此应用必须准备好使用交互式身份验证重新运行授权代码流，以每隔 24 小时获取一个新的刷新令牌。 用户无需输入其凭据，通常也不会看到任何 UX，只需重新加载应用程序，但浏览器必须访问顶级框架中的登录页，才能看到登录会话。  这是由[阻止第三方 cookie 的浏览器中的隐私功能](reference-third-party-cookies-spas.md)造成的。

```HTTP

// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&client_secret=sampleCredentia1s    // NOTE: Only required for web apps. This secret needs to be URL-Encoded
```

| 参数     | 类型           | 说明        |
|---------------|----------------|--------------------|
| `tenant`        | 必需     | 请求路径中的 `{tenant}` 值可用于控制哪些用户可以登录应用程序。 可以使用的值包括 `common`、`organizations`、`consumers` 和租户标识符。 有关详细信息，请参阅[协议基础知识](active-directory-v2-protocols.md#endpoints)。   |
| `client_id`     | 必填    | [Azure 门户 – 应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)体验分配给你的应用的应用程序（客户端）ID。 |
| `grant_type`    | 必填    | 必须是授权代码流的此阶段的 `refresh_token` 。 |
| `scope`         | 可选    | 范围的空格分隔列表。 在此阶段请求的范围必须等效于原始 authorization_code 请求阶段中所请求的范围，或者为该范围的子集。 如果此请求中指定的范围涵盖多个资源服务器，Microsoft 标识平台将返回第一个范围中指定的资源的令牌。 有关范围更加详细的说明，请参阅[权限、许可和范围](v2-permissions-and-consent.md)。 |
| `refresh_token` | 必需    | 在流的第二个阶段获取的 refresh_token。 |
| `client_secret` | 必填（对于 Web 应用） | 在应用注册门户中为应用创建的应用程序机密。 它不应用于本机应用，因为设备无法可靠地存储 client_secrets。 Web 应用和 Web API 都需要应用程序密钥，它能够将 client_secret 安全地存储在服务器端。 此机密需要进行 URL 编码。 有关详细信息，请参阅 [URI 一般语法规范](https://tools.ietf.org/html/rfc3986#page-12)。 |

#### <a name="successful-response"></a>成功的响应

成功的令牌响应如下：

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```

| 参数     | 说明         |
|---------------|-------------------------------------------------------------|
| `access_token`  | 请求的访问令牌。 应用可以使用此令牌验证受保护的资源，例如 Web API。 |
| `token_type`    | 指示令牌类型值。 Azure AD 唯一支持的类型是 Bearer |
| `expires_in`    | 访问令牌的有效期（以秒为单位）。   |
| `scope`         | access_token 的有效范围。    |
| `refresh_token` | 新的 OAuth 2.0 刷新令牌。 应该将旧刷新令牌替换为新获取的这个刷新令牌，以确保刷新令牌的有效期尽可能地长。 <br> **注意：** 仅当已请求 `offline_access` 作用域时提供。|
| `id_token`      | 无符号 JSON Web 令牌 (JWT)。 应用可以解码此令牌的段，以请求已登录用户的相关信息。 应用可以缓存并显示值，但不应依赖于这些值获取任何授权或安全边界。 有关 id_tokens 的详细信息，请参阅 [`id_token reference`](id-tokens.md)。 <br> **注意：** 仅当已请求 `openid` 作用域时提供。 |

[!INCLUDE [remind-not-to-validate-access-tokens](includes/remind-not-to-validate-access-tokens.md)]

#### <a name="error-response"></a>错误响应

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| 参数         | 说明                                                                                        |
|-------------------|----------------------------------------------------------------------------------------------------|
| `error`           | 可用于分类发生的错误类型与响应错误的错误码字符串。 |
| `error_description` | 帮助开发人员识别身份验证错误根本原因的特定错误消息。           |
| `error_codes` |可帮助诊断的 STS 特定错误代码列表。 |
| `timestamp` | 发生错误的时间。 |
| `trace_id` | 可帮助诊断的请求唯一标识符。 |
| `correlation_id` | 可帮助跨组件诊断的请求唯一标识符。 |

有关错误代码的描述和建议的客户端操作，请参阅 [令牌终结点错误的错误代码](#error-codes-for-token-endpoint-errors)。
