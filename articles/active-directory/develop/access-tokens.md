---
title: Microsoft 标识平台访问令牌 | Azure
titleSuffix: Microsoft identity platform
description: 了解 Azure AD v1.0 和 Microsoft 标识平台 (v2.0) 终结点发出的访问令牌。
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 06/25/2021
ms.author: hirsin
ms.reviewer: marsma
ms.custom: aaddev, identityplatformtop40, fasttrack-edit
ms.openlocfilehash: d633f52d739552a02999295ec083a965e0fa45fd
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114447021"
---
# <a name="microsoft-identity-platform-access-tokens"></a>Microsoft 标识平台访问令牌

访问令牌使客户端可以安全地调用受保护的 Web API，并可由 Web API 用来执行身份验证和授权。 根据 OAuth 规范，访问令牌是没有固定格式的不透明字符串 - 某些标识提供者 (IDP) 使用 GUID，另一些标识提供者使用加密的 blob。 Microsoft 标识平台使用各种访问令牌格式，具体取决于接受令牌的 API 的配置。 在 Microsoft 标识平台上[由开发人员注册的自定义 API](quickstart-configure-app-expose-web-apis.md) 可以从两种不同格式的 JSON Web 令牌 (JWT)（称为“v1”和“v2”）中进行选择，Microsoft 开发的 API（例如 Microsoft Graph）或 Azure 中的 API 具有另外的专有令牌格式。 这些专有格式可能是加密的令牌、JWT 或不会进行验证的特殊 JWT 样式令牌。

客户端必须将访问令牌视为不透明的字符串，因为令牌的内容仅适用于资源 (API)。 开发人员可以使用 [jwt.ms](https://jwt.ms) 之类的站点来解码 JWT，但仅限进行验证和调试。 但请注意，你为 Microsoft API 接收的令牌可能并非始终为 JWT，你无法始终对其进行解码。

要获得有关访问令牌内部内容的详细信息，客户端应使用随访问令牌一起返回给客户端的令牌响应数据。 当客户端请求访问令牌时，Microsoft 标识平台还会返回一些有关访问令牌的元数据供应用使用。 此信息包含访问令牌的过期时间及其有效范围。 此数据可让应用执行访问令牌的智能缓存，而无需分析访问令牌本身。

请参阅以下部分，了解你的 API 可以如何验证和使用访问令牌中的声明。  

> [!NOTE]
> 除非另行说明，否则本页上的所有文档仅适用于为已注册 API 颁发的令牌。  它不适用于为 Microsoft 拥有的 API 颁发的令牌，不能使用这些令牌来验证 Microsoft 标识平台将如何为你创建的 API 颁发令牌。  

## <a name="token-formats-and-ownership"></a>令牌格式和所有权

### <a name="v10-and-v20"></a>v1.0 和 v2.0 

Microsoft 标识平台提供了两个版本的访问令牌：v1.0 和 v2.0。  这些版本控制令牌中有哪些声明，确保 Web API 可以控制其令牌的样式。 Web API 在注册过程中选择了其中一个版本作为默认值 - v1.0 用于仅限 Azure AD 的应用，v2.0 用于支持使用者帐户的应用。  这可由应用程序使用[应用清单](reference-app-manifest.md#manifest-reference)中的 `accessTokenAcceptedVersion` 设置进行控制，其中的 `null` 和 `1` 会生成 v1.0 令牌，`2` 会生成 v2.0 令牌。

### <a name="what-app-is-a-token-for"></a>令牌用于什么应用？

访问令牌请求中涉及两个参与方：请求令牌的客户端，和调用 API 时接受令牌的资源 (API)。 令牌中的 `aud` 声明指示令牌适用的资源（其受众）。 客户端使用令牌，但不应理解或尝试分析它。 资源接受令牌。  

Microsoft 标识平台支持从任何版本的终结点颁发任何令牌版本 - 它们不相关。 因此，当资源将 `accessTokenAcceptedVersion` 设置为 `2` 时，客户端调用 v1.0 终结点来获取该 API 的令牌会收到 v2.0 访问令牌。  资源始终拥有其令牌（带 `aud` 声明的令牌），是唯一可以更改其令牌详细信息的应用程序。 因此，更改客户端的访问令牌[可选声明](active-directory-optional-claims.md)不会更改为 `user.read` 请求令牌时收到的访问令牌，该令牌归 Microsoft Graph 资源所有。

### <a name="sample-tokens"></a>示例令牌

v1.0 和 v2.0 令牌很相似，都包含许多相同的声明。 此处提供了每种令牌的示例。 但是，这些示例令牌不会进行[验证](#validating-tokens)，因为密钥在发布之前已经轮换，个人信息已从其中删除。

#### <a name="v10"></a>v1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0.D3H6pMUtQnoJAGq6AHd
```

在 [JWT.ms](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0.D3H6pMUtQnoJAGq6AHd) 中查看此 v1.0 令牌。

#### <a name="v20"></a>v2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt
```

在 [JWT.ms](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt) 中查看此 v2.0 令牌。

## <a name="claims-in-access-tokens"></a>访问令牌中的声明

JWT（JSON Web 令牌）拆分成三个部分：

* **标头** - 提供有关如何 [验证令牌](#validating-tokens)的信息，包括有关令牌的类型及其签名方式的信息。
* **有效负载** - 包含有关正在尝试调用你的服务的用户或应用的所有重要数据。
* **签名** - 用于验证令牌的原始材料。

每个部分由句点 (`.`) 分隔，并分别采用 Base64 编码。

仅当存在可以填充声明的值时，才提供声明。 你的应用不应依赖于提供的声明。 示例包括 `pwd_exp`（并非每个租户都要求密码过期）和 `family_name`（[客户端凭据](v2-oauth2-client-creds-grant-flow.md)流代表无名称的应用程序）。 始终会提供用于验证访问令牌的声明。

某些声明用于帮助 Azure AD 保护重复使用的令牌。 在说明中，这些声明标记为“不透明”，表示不可公开使用。 这些声明不一定会显示在令牌中，我们可能在不发出通告的情况下添加新的声明。

### <a name="header-claims"></a>标头声明

|声明 | 格式 | 说明 |
|--------|--------|-------------|
| `typ` | 字符串 - 始终为“JWT” | 指示令牌是 JWT。|
| `alg` | 字符串 | 指示用于对令牌进行签名的算法，例如“RS256” |
| `kid` | 字符串 | 指定可用于验证此令牌签名的公钥的指纹。 在 v1.0 和 v2.0 访问令牌中已发出。 |
| `x5t` | 字符串 | 功能与 `kid` 相同（在用法和值方面）。 `x5t` 是在 v1.0 访问令牌中仅出于兼容目的而发出的旧式声明。 |

### <a name="payload-claims"></a>有效负载声明

| 声明 | 格式 | 说明 |
|-----|--------|-------------|
| `aud` | 字符串，一个应用 ID URI 或 GUID | 标识令牌的目标接收方 - 其受众。  你的 API 必须验证此值，如果值不匹配，则必须拒绝该令牌。 在 v2.0 令牌中，它始终是 API 的客户端 ID，而在 v1.0 令牌中，它可以是请求中使用的客户端 ID 或资源 URI，具体取决于客户端请求令牌的方式。|
| `iss` | 字符串，STS URI | 标识构造并返回令牌的安全令牌服务 (STS)，以及对用户进行身份验证的 Azure AD 租户。 如果颁发的令牌是 v2.0 令牌（请参阅 `ver` 声明），则 URI 将以 `/v2.0` 结尾。 表示用户是来自 Microsoft 帐户的使用者用户的 GUID 为 `9188040d-6c67-4c5b-b112-36a304b66dad`。 应用可使用声明的 GUID 部分限制可登录应用的租户集（如果适用）。 |
|`idp`| 字符串，通常是 STS URI | 记录对令牌使用者进行身份验证的标识提供者。 除非用户帐户与颁发者不在同一租户中，否则此值与颁发者声明的值相同 - 例如，来宾。 如果声明不存在，则意味着可以改用 `iss` 的值。  对于在组织上下文中使用的个人帐户（例如，受邀加入 Azure AD 租户的个人帐户），`idp` 声明可能是“live.com”或包含 Microsoft 帐户租户 `9188040d-6c67-4c5b-b112-36a304b66dad` 的 STS URI。 |
| `iat` | int，UNIX 时间戳 | “Issued At”表示针对此令牌进行身份验证的时间。 |
| `nbf` | int，UNIX 时间戳 | “nbf”（不早于）声明指定只能在哪个时间之后接受 JWT 的处理。 |
| `exp` | int，UNIX 时间戳 | “exp”（过期时间）声明指定只能在哪个时间（含）之前接受 JWT 的处理。 请务必注意，资源也可以在此时间之前拒绝令牌，例如，需要对身份验证进行更改，或者检测到令牌吊销时。 |
| `aio` | 不透明字符串 | 一个内部声明，Azure AD 用它来记录有关重复使用令牌的数据。 资源不应使用此声明。 |
| `acr` | 字符串，“0”或“1” | 仅在 v1.0 令牌中提供。 “身份验证上下文类”声明。 值为“0”表示最终用户身份验证不符合 ISO/IEC 29115 要求。 |
| `amr` | 字符串的 JSON 数组 | 仅在 v1.0 令牌中提供。 标识对令牌使用者进行身份验证的方式。 有关更多详细信息，请参阅 [amr 声明部分](#the-amr-claim)。 |
| `appid` | 字符串，GUID | 仅在 v1.0 令牌中提供。 使用令牌的客户端的应用程序 ID。 该应用程序可以自身名义或者代表用户进行操作。 应用程序 ID 通常表示应用程序对象，但它还可以表示 Azure AD 中的服务主体对象。 |
| `azp` | 字符串，GUID | 仅在 v2.0 令牌中存在，取代了 `appid`。 使用令牌的客户端的应用程序 ID。 该应用程序可以自身名义或者代表用户进行操作。 应用程序 ID 通常表示应用程序对象，但它还可以表示 Azure AD 中的服务主体对象。 |
| `appidacr` | “0”、“1”或“2” | 仅在 v1.0 令牌中提供。 表示对客户端进行身份验证的方式。 对于公共客户端，值为“0”。 如果使用客户端 ID 和客户端机密，则值为“1”。 如果使用客户端证书进行身份验证，则值为“2”。 |
| `azpacr` | “0”、“1”或“2” | 仅在 v2.0 令牌中存在，取代了 `appidacr`。 表示对客户端进行身份验证的方式。 对于公共客户端，值为“0”。 如果使用客户端 ID 和客户端机密，则值为“1”。 如果使用客户端证书进行身份验证，则值为“2”。 |
| `preferred_username` | 字符串 | 表示用户的主用户名。 可以是电子邮件地址、电话号码或未指定格式的一般用户名。 其值是可变的，可能随时改变。 由于此值是可变的，因此它不能用于做出授权决定。  它可以用于用户名提示，但在人工可读的 UI 中用作用户名。 需要 `profile` 范围才能接收此声明。 仅在 v2.0 令牌中提供。 |
| `name` | String | 提供一个用户可读值，用于标识令牌使用者。 该值不一定唯一，而且可变，只能用于显示目的。 需要 `profile` 范围才能接收此声明。 |
| `scp` | 字符串，范围的空格分隔列表 | 应用程序公开的、客户端应用程序已请求（和接收）其许可的范围集。 应用应该验证这些范围是否为应用公开的有效范围，并根据这些范围的值做出授权决策。 仅为[用户令牌](#user-and-application-tokens)包含此值。 |
| `roles` | 字符串数组、权限列表 | 应用程序公开的、请求方应用程序或用户有权调用的权限集。 对于[应用程序令牌](#user-and-application-tokens)，在执行客户端凭据流（[v1.0](../azuread-dev/v1-oauth2-client-creds-grant-flow.md)、[v2.0](v2-oauth2-client-creds-grant-flow.md)）期间使用此声明代替用户范围。  对于[用户令牌](#user-and-application-tokens)，使用用户在目标应用程序上分配的角色进行填充。 |
| `wids` | [RoleTemplateID](../roles/permissions-reference.md#all-roles) GUID 的数组 | 表示从 [Azure AD 内置角色](../roles/permissions-reference.md#all-roles)中的角色部分分配给此用户的租户范围角色。  此声明是通过[应用程序清单](reference-app-manifest.md)的 `groupMembershipClaims` 属性按应用程序定义的。  必须将其设置为“All”或“DirectoryRole”。  由于令牌长度方面的原因，它在通过隐式流获取的令牌中可能不存在。 |
| `groups` | GUID 的 JSON 数组 | 指定表示使用者的组成员身份的对象 ID。 这些值具有唯一性（请参阅对象 ID），可安全地用于管理访问，例如强制要求授权访问资源。 组声明中包含的组通过[应用程序清单](reference-app-manifest.md)的 `groupMembershipClaims` 属性基于每个应用程序进行配置。 值为 null 将排除所有组；值为“SecurityGroup”将只包括“Active Directory 安全组”成员身份；值为“All”将包括安全组和 Microsoft 365 通讯组列表。 <br><br>有关将 `groups` 声明与隐式授权一起使用的详细信息，请参阅下文中的 `hasgroups` 声明。 <br>对于其他流，如果用户所在的组数超出了某个限制（对于 SAML，为 150，对于 JWT，为 200），则会将超额声明添加到指向包含该用户的组列表的 Microsoft Graph 终结点的声明源。 |
| `hasgroups` | Boolean | 如果存在，始终为 `true`，表示用户至少在一个组中。 如果完整组声明将导致 URI 片段超出 URL 长度限制（当前为 6 个或更多组），则在隐式授权流中用来替代 JWT 的 `groups` 声明。 指示客户端应当使用 Microsoft Graph API 来确定用户的组 (`https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects`)。 |
| `groups:src1` | JSON 对象 | 对于长度不受限制（参阅上文中的 `hasgroups`）但对于令牌而言仍然太大的令牌请求，将包括指向用户的完整组列表的链接。 对于 JWT，作为分布式声明；对于 SAML，作为新声明替代 `groups` 声明。 <br><br>JWT 值示例： <br> `"groups":"src1"` <br> `"_claim_sources`: `"src1" : { "endpoint" : "https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects" }` |
| `sub` | String | 令牌针对其断言信息的主体，例如应用的用户。 此值固定不变，无法重新分配或重复使用。 可使用它安全地执行授权检查（例如，使用令牌访问资源时），并可将它用作数据库表中的键。 由于使用者始终会在 Azure AD 颁发的令牌中存在，我们建议在通用授权系统中使用此值。 但是，使用者是成对标识符 - 它对特定应用程序 ID 是唯一的。 因此，如果单个用户使用两个不同的客户端 ID 登录到两个不同的应用，这些应用将收到两个不同的使用者声明值。 这不一定是所需的，具体取决于体系结构和隐私要求。 另请参阅 `oid` 声明（在租户中的应用之间确实会保持相同）。 |
| `oid` | 字符串，GUID | 请求的“主体”（身份已验证的用户或服务主体）的不可变标识符。  在 ID 令牌和应用+用户令牌中，此项是用户的对象 ID。  在仅限应用的令牌中，此项是调用服务主体的对象 ID。 还可以使用它安全地执行授权检查，并将它用作数据库表中的键。 此 ID 唯一标识跨应用程序的主体 - 将同一用户登录的两个不同的应用程序会在 `oid` 声明中收到相同的值。 因此，对 Microsoft 联机服务（例如 Microsoft Graph）发出查询时可以使用 `oid`。 Microsoft Graph 将返回此 ID 作为给定[用户帐户](/graph/api/resources/user)的 `id` 属性。 因为 `oid` 允许多个应用关联主体，所以需要 `profile` 范围才能为用户接收此声明。 请注意，如果单个用户存在于多个租户中，该用户将包含每个租户中的不同对象 ID - 它们将视为不同帐户，即使用户使用相同的凭据登录到每个帐户，也是如此。 |
|`tid` | 字符串，GUID | 表示用户登录到的租户。 对于工作和学校帐户，该 GUID 就是用户登录到的组织的不可变租户 ID。 对于登录个人 Microsoft 帐户租户（Xbox、Teams for Life 或 Outlook 等服务）的情况，值为 `9188040d-6c67-4c5b-b112-36a304b66dad`。 若要接收此声明，你的应用必须请求 `profile` 范围。 |
| `unique_name` | 字符串 | 仅在 v1.0 令牌中提供。 提供一个用户可读值，用于标识令牌使用者。 不保证此值在租户中唯一，只应该用于显示目的。 |
| `uti` | 不透明字符串 | Azure 用来重新验证令牌的内部声明。 资源不应使用此声明。 |
| `rh` | 不透明字符串 | Azure 用来重新验证令牌的内部声明。 资源不应使用此声明。 |
| `ver` | 字符串，`1.0` 或 `2.0` | 指示访问令牌的版本。 |

**组超额声明**

为了确保令牌大小不超过 HTTP 标头大小限制，Azure AD 对它包含在组声明中的对象 ID 数进行了限制。 如果用户所属的组数超过超额限制（SAML 令牌的为 150 个，JWT 令牌的为 200 个，通过隐式流颁发的令牌的仅为 6 个），则 Azure AD 不会在令牌中发出组声明。 但是，它会在令牌中包含超额声明，该声明指示应用程序查询 Microsoft Graph API 以检索用户的组成员身份。

```JSON
{
  ...
  "_claim_names": {
   "groups": "src1"
    },
    {
  "_claim_sources": {
    "src1": {
        "endpoint":"[Url to get this user's group membership from]"
        }
       }
     }
  ...
}
```

可使用[应用创建脚本](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-2-Groups/AppCreationScripts)文件夹中提供的 `BulkCreateGroups.ps1` 来帮助测试超额方案。

#### <a name="v10-basic-claims"></a>v1.0 基本声明

以下声明将包含在 v1.0 令牌中（如果适用），默认不会包含在 v2.0 令牌中。 如果使用 v2.0 并需要其中的某个声明，请使用[可选声明](active-directory-optional-claims.md)来请求它们。

| 声明 | 格式 | 说明 |
|-----|--------|-------------|
| `ipaddr`| 字符串 | 进行身份验证的用户的来源 IP 地址。 |
| `onprem_sid`| 字符串，采用 [SID 格式](/windows/desktop/SecAuthZ/sid-components) | 如果用户使用了本地身份验证，则此声明会提供其 SID。 可在旧版应用程序中将 `onprem_sid` 用于授权。|
| `pwd_exp`| int，UNIX 时间戳 | 指示用户的密码何时过期。 |
| `pwd_url`| 字符串 | 可向用户发送的，以重置其密码的 URL。 |
| `in_corp`| boolean | 表示客户端是否从企业网络登录。 如果不是，则不包括该声明。 |
| `nickname`| 字符串 | 用户的附加名称，不同于名字或姓氏。|
| `family_name` | 字符串 | 根据用户对象中的定义提供用户的姓氏。 |
| `given_name` | 字符串 | 根据用户对象中的设置提供用户的名字。 |
| `upn` | 字符串 | 用户的用户名。 可以是电话号码、电子邮件地址或无格式的字符串。 只应该用于显示目的，在重新身份验证方案中提供用户名提示。 |

#### <a name="the-amr-claim"></a>`amr` 声明

Microsoft 标识可以通过与应用程序相关的不同方式进行身份验证。 `amr` 声明是可以包含多个项（例如 `["mfa", "rsa", "pwd"]`）的数组，适用于使用密码和 Authenticator 应用的身份验证。

| 值 | 说明 |
|-----|-------------|
| `pwd` | 密码身份验证，用户的 Microsoft 密码或应用的客户端机密。 |
| `rsa` | 身份验证基于 RSA 密钥的证明，例如，使用 [Microsoft Authenticator 应用](https://aka.ms/AA2kvvu)。 这包括，身份验证是否是使用服务拥有的 X509 证书通过自签名的 JWT 执行的。 |
| `otp` | 使用电子邮件或短信的一次性密码。 |
| `fed` | 使用了联合身份验证断言（例如 JWT 或 SAML）。 |
| `wia` | Windows 集成身份验证 |
| `mfa` | 使用了[多重身份验证](../authentication/concept-mfa-howitworks.md)。 如果存在这种情况，则也会包含其他身份验证方法。 |
| `ngcmfa` | 等效于 `mfa`，用于预配某些高级凭据类型。 |
| `wiaormfa`| 用户已使用 Windows 或 MFA 凭据进行身份验证。 |
| `none` | 未执行任何身份验证。 |

## <a name="access-token-lifetime"></a>访问令牌生存期

访问令牌的默认生存期会有所不同，具体取决于请求令牌的客户端应用程序。 例如，对于支持协商 CAE 感知会话客户端的连续访问评估 (CAE)，令牌生存期时间将会很长（最多 28 小时）。  访问令牌过期后，客户端必须使用刷新令牌（通常以无提示方式）获取新的刷新令牌和访问令牌。

可以调整访问令牌的生存期，控制客户端应用程序使应用程序会话过期的频率，以及它要求用户重新进行身份验证（以无提示方式或交互方式）的频率。 有关详细信息，请参阅[可配置的令牌生存期](active-directory-configurable-token-lifetimes.md)。

## <a name="validating-tokens"></a>验证令牌

并非所有应用都应当验证令牌。 只有在特定的场景中，应用才应当验证令牌：

* [Web API](quickstart-configure-app-expose-web-apis.md) 必须验证由客户端发送给它们的访问令牌。  它们只能接受包含其 `aud` 声明的令牌。
* 机密 Web 应用（例如 ASP.NET Core）必须通过混合流中的用户浏览器验证发送给它们的 ID 令牌，然后才允许访问用户的数据或建立会话。

如果上述方案均不适用，则对令牌进行验证对你的应用程序无益，而如果根据令牌的有效性做出决策，则可能会带来安全和可靠性方面的风险。  对令牌进行验证对公共客户端（例如原生应用或 SPA）无益 - 应用会直接与 IDP 进行通信，因此 SSL 保护可确保令牌有效。

API 和 Web 应用只能验证其中的 `aud` 声明与其应用程序匹配的令牌；其他资源可能有自定义令牌验证规则。 例如，Microsoft Graph 的令牌不会根据这些规则进行验证，因为它们采用了专有格式。 验证和接受用于另一个资源的令牌是[混淆代理](https://cwe.mitre.org/data/definitions/441.html)问题的一个示例。

如果应用程序需要根据以上说明验证 id_token 或 access_token，则应用应首先根据 OpenID 发现文档中的值来验证令牌的签名和颁发者。 例如，文档的租户独立版本位于 [https://login.microsoftonline.com/common/.well-known/openid-configuration](https://login.microsoftonline.com/common/.well-known/openid-configuration)。

我们为那些希望了解基础过程的用户提供了以下信息。 Azure AD 中间件具有验证访问令牌的内置功能，可以浏览我们的[示例](sample-v2-code.md)，以所选语言进行查找。 另外还有多个第三方开源库可用于 JWT 验证 - 几乎每个平台和语言都至少有一个选项。 有关 Azure AD 身份验证库和代码示例的详细信息，请参阅[身份验证库](reference-v2-libraries.md)。

### <a name="validating-the-signature"></a>验证签名

JWT 包含三个段（以 `.` 字符分隔）。 第一个段称为标头，第二个称为主体，第三个称为签名。   签名段可用于验证令牌的真实性，使令牌可获得应用的信任。

Azure AD 颁发的令牌已使用行业标准非对称式加密算法（例如 RS256）进行签名。 JWT 的标头包含用于签名令牌的密钥和加密方法的相关信息：

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk",
  "kid": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk"
}
```

`alg` 声明表示用于对令牌进行签名的算法，而 `kid` 声明表示用于验证令牌的特定公钥。

在任何给定时间点，Azure AD 可以使用特定公钥 - 私钥对中的任何一组对 id_token 进行签名。 Azure AD 会定期滚动更新一组可能的密钥，因此应将应用编写成自动处理这些密钥更改。 对 Azure AD 所用公钥的更新进行检查的合理频率为每 24 小时一次。

可以使用位于以下位置的 [OpenID Connect 元数据文档](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document)来获取验证签名所需的签名密钥数据：

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [!TIP]
> 在浏览器中尝试打开此 [URL](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration)！

此元数据文档：

* 是一个 JSON 对象，其中包含一些有用的信息，例如执行 OpenID Connect 身份验证所需的各种终结点的位置。
* 包含 `jwks_uri`，提供公钥集的位置，这些公钥对应于用于对令牌进行签名的私钥。 位于 `jwks_uri` 的 JSON Web 密钥 (JWK) 包含在该特定时间点使用的所有公钥信息。 [RFC 7517](https://tools.ietf.org/html/rfc7517) 中对 JWK 格式进行了说明。 应用可以使用 JWT 标头中的 `kid` 声明从本文档中选择公钥，该公钥对应于用于对特定令牌进行签名的私钥。 然后可以使用正确的公钥和指定的算法来执行签名验证。

> [!NOTE]
> 我们建议使用 `kid` 声明来验证令牌。 虽然 v1.0 令牌同时包含 `x5t` 和 `kid` 声明，但 v2.0 令牌仅包含 `kid` 声明。

执行签名验证超出了本文档的范围 - 有许多开放源代码库可帮助这么做（如有必要）。  但是，Microsoft 标识平台有一个针对标准的令牌签名扩展 - 自定义签名密钥。

如果应用因使用[声明映射](active-directory-claims-mapping.md)功能而具有自定义签名密钥，则必须追加包含应用 ID 的 `appid` 查询参数，以获取指向应用的签名密钥信息的 `jwks_uri`，该信息应该用于验证。 例如：`https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` 包含 `https://login.microsoftonline.com/{tenant}/discovery/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e` 的 `jwks_uri`。

### <a name="claims-based-authorization"></a>基于声明的授权

应用程序的业务逻辑将会强制规定此步骤，下面列出了一些常见的授权方法。

* 使用 `aud` 声明来确保该用户打算调用你的应用程序。  如果资源的标识符不在 `aud` 声明内，请拒绝它。
* 使用 `scp` 声明来验证该用户是否已向调用应用授予了调用你的 API 的权限。
* 使用 `roles` 和 `wids` 声明来验证用户本身是否获得了调用你的 API 的授权。  例如，管理员可能有权写入到你的 API，但普通用户不行。
* 确保允许调用方客户端使用 `appid` 声明调用你的 API。
* 检查 `tid` 是否与允许调用该 API 的租户相匹配。
* 使用 `amr` 声明验证已执行 MFA 的用户。 应使用[条件访问](../conditional-access/overview.md)强制实施此步骤。
* 如果在访问令牌中请求了 `roles` 或 `groups` 声明，请验证用户是否在允许执行此操作的组中。
  * 对于使用隐式流检索的令牌，可能需要在 [Microsoft Graph](https://developer.microsoft.com/graph/) 中查询此数据，因为该数据通常很庞大，无法放到令牌中。

## <a name="user-and-application-tokens"></a>用户和应用程序令牌

应用程序可以为用户（经常讨论的流）或直接从应用程序（通过[客户端凭据流](../azuread-dev/v1-oauth2-client-creds-grant-flow.md)）接收令牌。 这些仅限应用的令牌表示这种调用来自应用程序，而没有支持它的用户。 这些令牌的处理方式大致相同：

* 使用 `roles` 查看已授予令牌使用者的权限。
* 使用 `oid` 或 `sub` 来验证调用服务主体是否是预期的服务主体。

如果应用需要区分仅限应用的访问令牌和用户的访问令牌，请使用 `idtyp` [可选声明](active-directory-optional-claims.md)。  通过将 `idtyp` 声明添加到 `accessToken` 字段，并检查值 `app`，可以检测仅限应用的访问令牌。  用户的 ID 令牌和访问令牌不包含 `idtyp` 声明。

## <a name="token-revocation"></a>令牌吊销

刷新令牌可能由于不同的原因而随时失效或吊销。 这些原因主要分为两个类别：超时和吊销。

### <a name="token-timeouts"></a>令牌超时

使用[令牌生存期配置](active-directory-configurable-token-lifetimes.md)，可以更改刷新令牌的生存期。  一些令牌未被使用（例如，用户在 3 个月内未打开应用）就过期了，这是正常现象，也在预料之中。  应用将遇到这样的情况：登录服务器会因时间问题而拒绝刷新令牌。

* MaxInactiveTime：如果在 MaxInactiveTime 指定的时间内未使用刷新令牌，刷新令牌将不再有效。
* MaxSessionAge：如果 MaxAgeSessionMultiFactor 或 MaxAgeSessionSingleFactor 已设置为其默认值（“直到吊销”）以外的值，则在经过 MaxAgeSession* 中设置的时间后，需要重新进行身份验证。
* 示例：
  * 租户的 MaxInactiveTime 为 5 天，用户去度假一周，因此 Azure AD 在 7 天内未看到用户发出的新令牌请求。 下次用户请求新令牌时，他们将看到其刷新令牌已被吊销，他们必须重新输入其凭据。
  * 敏感应用程序的 MaxAgeSessionSingleFactor 为 1 天。 如果用户在星期一登录，则在星期二（25 个小时后），他们需要重新进行身份验证。

### <a name="revocation"></a>撤销

服务器可能会由于以下原因而撤销刷新令牌：凭据发生更改，或者用户或管理员执行了相关操作。  刷新令牌分为两类：颁发给机密客户端的刷新令牌（最右边的列），和颁发给公共客户端的刷新令牌（所有其他列）。

| 更改 | 基于密码的 Cookie | 基于密码的令牌 | 不基于密码的 Cookie | 不基于密码的令牌 | 机密客户端令牌 |
|---|-----------------------|----------------------|---------------------------|--------------------------|---------------------------|
| 密码过期 | 一直有效 | 一直有效 | 一直有效 | 一直有效 | 一直有效 |
| 用户更改了密码 | 已撤销 | 已撤销 | 一直有效 | 一直有效 | 一直有效 |
| 用户执行 SSPR | 已撤销 | 已撤销 | 一直有效 | 一直有效 | 一直有效 |
| 管理员重置密码 | 已撤销 | 已撤销 | 一直有效 | 一直有效 | 一直有效 |
| 用户[通过 PowerShell](/powershell/module/azuread/revoke-azureadsignedinuserallrefreshtoken) 撤销刷新令牌 | 已撤销 | 已撤销 | 已撤销 | 已撤销 | 已撤销 |
| 管理员[通过 PowerShell](/powershell/module/azuread/revoke-azureaduserallrefreshtoken) 撤销用户的所有刷新令牌 | 已撤销 | 已撤销 |已撤销 | 已撤销 | 已撤销 |
| Web 上的单一注销（[v1.0](../azuread-dev/v1-protocols-openid-connect-code.md#single-sign-out)、[v2.0](v2-protocols-oidc.md#single-sign-out)） | 已撤销 | 一直有效 | 已撤销 | 一直有效 | 一直有效 |

#### <a name="non-password-based"></a>不基于密码

“不基于密码”登录是指用户在未键入密码的情况下登录。 “不基于密码”登录的示例包括：

- 在 Windows Hello 中使用人脸
- FIDO2 密钥
- SMS
- 语音
- PIN

请查看[主刷新令牌](../devices/concept-primary-refresh-token.md)，了解有关主刷新令牌的更多详细信息。

## <a name="next-steps"></a>后续步骤

* 了解 [Azure AD 中的 `id_tokens`](id-tokens.md)。
* 了解权限和同意（[v1.0](../azuread-dev/v1-permissions-consent.md)、[v2.0](v2-permissions-and-consent.md)）。
