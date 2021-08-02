---
title: 声明质询、声明请求和客户端功能
titleSuffix: Microsoft identity platform
description: 解释 Microsoft 标识平台中的声明质询、声明请求和客户端功能。
services: active-directory
author: knicholasa
manager: martinco
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 05/11/2021
ms.author: nichola
ms.reviewer: kkrishna, kylemar
ms.openlocfilehash: abce87c8d5c5d88c9edd1303f0a585aa773d2ec8
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110471374"
---
# <a name="claims-challenges-claims-requests-and-client-capabilities"></a>声明质询、声明请求和客户端功能

声明质询是从 API 发送的响应，指示客户端应用程序发送的访问令牌中的声明不足。 声明不足的原因可能是该令牌不满足为该 API 设置的条件访问策略，或者该访问令牌已撤销。

声明请求由客户端应用程序发出，目的是将用户重定向回到标识提供者以检索到一个新令牌，该令牌中的声明满足先前尚不满足的附加要求。

使用增强的安全功能（例如[连续访问评估 (CAE)](../conditional-access/concept-continuous-access-evaluation.md) 和[条件访问身份验证上下文](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/granular-conditional-access-for-sensitive-data-and-actions/ba-p/1751775)）的应用程序必须经过准备才能处理声明质询。

仅当应用程序在对服务发出的调用中声明了其[客户端功能](#client-capabilities)时，才会从 [Microsoft Graph](/graph/overview) 等热门服务收到声明质询。

## <a name="claims-challenge-header-format"></a>声明质询的头格式

声明质询是一个指令，其形式是当提供给 API 的[访问令牌](access-tokens.md)未获授权，因而需要改为提供具有适当功能的新访问令牌时，由该 API 返回的 `www-authenticate` 头。 声明质询由多个部分组成：响应的 HTTP 状态代码和 `www-authenticate` 头，后者本身包含多个组成部分，并且必须包含一个声明指令。

下面是一个示例：

```https
HTTP 401; Unauthorized

www-authenticate =Bearer realm="", authorization_uri="https://login.microsoftonline.com/common/oauth2/authorize", error="insufficient_claims", claims="eyJhY2Nlc3NfdG9rZW4iOnsiYWNycyI6eyJlc3NlbnRpYWwiOnRydWUsInZhbHVlIjoiYzEifX19"
```

 HTTP 状态代码：必须是“401 Unauthorized” 。

包含以下内容的 www-authenticate 响应头：

| 参数    | 必需/可选 | 说明 |
|--------------|-------------|--------------|
| 身份验证类型 | 必须 | 必须是“Bearer”。|
| 领域 | 可选 | 正在访问的租户 ID 或租户域名（例如 microsoft.com）。 如果身份验证要通过 [common 终结点](howto-convert-app-to-be-multi-tenant.md#update-your-code-to-send-requests-to-common)进行，则该项必须是空字符串。 |
| `authorization_uri` | 必须 | 可在其中根据需要执行交互式身份验证的 authorize 终结点的 URI。 如果在 realm 中指定了租户信息，则租户信息必须包含在 authorization_uri 中。 如果 realm 是空字符串，则 authorization_uri 必须是针对 [common 终结点](howto-convert-app-to-be-multi-tenant.md#update-your-code-to-send-requests-to-common)提供的。 |
| `error` | 必须 | 如果应生成声明质询，则此项必须是“insufficient_claims”。 | 
| `claims` | 当 error 为“insufficient_claims”时，此项是必需的。 | 带引号的字符串，其中包含 base 64 编码的[声明请求](https://openid.net/specs/openid-connect-core-1_0.html#ClaimsParameter)。 声明请求应在 JSON 对象的最高级别请求“access_token”的声明。 （声明请求的）值与上下文相关，稍后将在本文档中详细阐明。 由于大小的原因，信赖方应用程序在进行 base 64 编码之前应该减少 JSON 代码。 以上示例的原始 JSON 为 {"access_token":{"acrs":{"essential":true,"value":"cp1"}}}。 |

401 响应可以包含多个 `www-authenticate` 头。 上表中的所有字段必须包含在同一个 `www-authenticate` 头中。 包含声明质询的 `www-authenticate` 头可以包含其他字段。 头中的字段是无序的。 根据 RFC 7235，每个参数名称在每个身份验证方案质询中只能出现一次。

## <a name="claims-request"></a>声明请求

当应用程序收到声明质询时，会指示不再将前一个访问令牌视为有效。 在这种情况下，应用程序应该清除任何本地缓存或用户会话中的令牌。 然后，它应将已登录的用户重定向回到 Azure Active Directory (Azure AD)，通过将 [OAuth 2.0 授权代码流](v2-oauth2-auth-code-flow.md)与一个满足先前尚不满足的附加要求的 claims 参数配合使用来检索新的令牌。

下面是一个示例：

```https
GET https://login.microsoftonline.com/14c2f153-90a7-4689-9db7-9543bf084dad/oauth2/v2.0/authorize
?client_id=2810aca2-a927-4d26-8bca-5b32c1ef5ea9
&redirect_uri=https%3A%2F%contoso.com%3A44321%2Fsignin-oidc
&response_type=code
&scope=openid%20profile%20offline_access%20user.read%20Sites.Read.All
&response_mode=form_post
&login_hint=kalyan%ccontoso.onmicrosoft.com
&domain_hint=organizations
claims=%7B%22access_token%22%3A%7B%22acrs%22%3A%7B%22essential%22%3Atrue%2C%22value%22%3A%22c1%22%7D%7D%7D
```

应将声明质询作为对 Azure AD 的 [/authorize](v2-oauth2-auth-code-flow.md#request-an-authorization-code) 终结点发出的所有调用的一部分进行传递，直到成功检索到令牌，此后便不再需要该声明质询。

若要填充 claims 参数，开发人员必须：

1. 将前面收到的 base64 字符串解码。
2. 对该字符串进行 URL 编码，然后再次将其添加到 claims 参数。

完成此流后，应用程序将收到一个访问令牌，其中包含的附加声明可以证明用户满足所需的条件。

## <a name="client-capabilities"></a>客户端功能

客户端功能可帮助 Web API 等资源提供程序检测调用方客户端应用程序是否理解声明质询，然后可以相应地自定义其响应。 当并非所有 API 客户端都能处理声明质询，而某些早期版本仍然需要不同的响应时，此功能可能很有用。

某些热门的应用程序（例如 [Microsoft Graph](/graph/overview)）只会在调用方客户端应用声明它能够使用客户端功能处理声明质询时，才发送声明质询。

为了避免产生额外的流量或者对用户体验造成影响，Azure AD 不假设应用能够处理声明质询，除非你显式选择了能够处理声明质询。 除非应用程序声明它已准备好使用“cp1”功能处理声明质询，否则它不会接收声明质询（并且无法使用 CAE 令牌等相关功能）。

### <a name="how-to-communicate-client-capabilities-to-azure-ad"></a>如何将客户端功能传达给 Azure AD

以下示例 claims 参数演示客户端应用程序如何在 [OAuth 2.0 授权代码流](v2-oauth2-auth-code-flow.md)中将其功能传达给 Azure AD。

```json
Claims: {"access_token":{"xms_cc":{"values":["cp1"]}}}
```

使用 MSAL 库的应用程序将使用以下代码：

```c#
_clientApp = PublicClientApplicationBuilder.Create(App.ClientId)
 .WithDefaultRedirectUri()
 .WithAuthority(authority)
 .WithClientCapabilities(new [] {"cp1"})
 .Build();*
```

使用 Microsoft.Identity.Web 的应用程序可将以下代码添加到配置文件：

```c#
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    // the remaining settings
    // ... 
    "ClientCapabilities": [ "cp1" ]
},
```

下面是演示如何向 Azure AD 发出请求的示例：

```https
GET https://login.microsoftonline.com/14c2f153-90a7-4689-9db7-9543bf084dad/oauth2/v2.0/authorize
?client_id=2810aca2-a927-4d26-8bca-5b32c1ef5ea9
&redirect_uri=https%3A%2F%contoso.com%3A44321%2Fsignin-oidc
&response_type=code
&scope=openid%20profile%20offline_access%20user.read%20Sites.Read.All
&response_mode=form_post
&login_hint=kalyan%ccontoso.onmicrosoft.com
&domain_hint=organizations
&claims=%7B%22access_token%22%3A%7B%22xms_cc%22%3A%7B%22values%22%3A%5B%22cp1%22%5D%7D%7D%7D
```

如果你已有一个现有的 claims 参数有效负载，可将其添加到现有的集。

例如，如果在执行条件访问身份验证上下文操作后已收到以下响应

```json
{"access_token":{"acrs":{"essential":true,"value":"c25"}}}
```

需要在现有的 claims 有效负载中预先附加客户端功能。

```json
{"access_token":{"xms_cc":{"values":["cp1"]},"acrs":{"essential":true,"value":"c25"}}}
```

## <a name="receiving-xms_cc-claim-in-an-access-token"></a>在访问令牌中接收 xms_cc 声明

若要接收有关客户端应用程序是否可以处理声明质询的信息，API 实现器必须在其应用程序清单中以可选声明的形式请求 xms_cc。

访问令牌中值为“cp1”的 xms_cc 声明是确定客户端应用程序能否处理声明质询的权威方式。 xms_cc 是一个可选声明，它并非始终都在访问令牌中颁发，即使客户端发送包含“xms_cc”的声明请求，也是如此。 要使访问令牌包含 xms_cc 声明，资源应用程序（即 API 实现器）必须在其应用程序清单中以[可选声明](active-directory-optional-claims.md)的形式请求 xms_cc。 如果以可选声明的形式请求 xms_cc，仅当客户端应用程序在声明请求中发送了 xms_cc 时，才会将它添加到访问令牌 。 xms_cc 声明请求的值将作为 xms_cc 声明的值包含在访问令牌中（如果它是已知值） 。 当前唯一已知的值为 cp1。

值不区分大小写且无序。 如果在 xms_cc 声明请求中指定多个值，这些值将是多值集合，并用作 xms_cc 声明的值 。

以下请求：

```json
{ "access_token": { "xms_cc":{"values":["cp1","foo", "bar"] } }}
```

将在访问令牌中生成

```json
"xms_cc": ["cp1", "foo", "bar"]
```

声明（如果 cp1、foo 和 bar 是已知功能  ）。

这是在请求 xms_cc [可选声明](active-directory-optional-claims.md)后应用清单的外观

```c#
"optionalClaims":
{
    "accessToken": [
    {
        "additionalProperties": [],
        "essential": false,
        "name": "xms_cc",
        "source": null
    }],
    "idToken": [],
    "saml2Token": []
}
```

然后，API 可以根据客户端能否处理声明质询来自定义其响应。

C# 示例

```c#
Claim ccClaim = context.User.FindAll(clientCapabilitiesClaim).FirstOrDefault(x => x.Type == "xms_cc");
if (ccClaim != null && ccClaim.Value == "cp1")
{
    // Return formatted claims challenge as this client understands this
}
else
{
    // Throw generic exception
    throw new UnauthorizedAccessException("The caller does not meet the authentication bar to carry our this operation. The service cannot allow this operation");
}
```

## <a name="next-steps"></a>后续步骤

- [Microsoft 标识平台和 OAuth 2.0 授权代码流](v2-oauth2-auth-code-flow.md#request-an-authorization-code)
- [如何在应用程序中使用启用了连续访问评估的 API](app-resilience-continuous-access-evaluation.md)
- [对敏感数据和操作进行精细条件访问](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/granular-conditional-access-for-sensitive-data-and-actions/ba-p/1751775)
