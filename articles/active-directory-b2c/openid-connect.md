---
title: 使用 OpenID Connect 进行 Web 登录 - Azure Active Directory B2C
description: 使用 Azure Active Directory B2C 中的 OpenID Connect 身份验证协议生成 Web 应用程序。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 32154904a78062f5e3afdb6217351f39151b36b8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729400"
---
# <a name="web-sign-in-with-openid-connect-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用 OpenID Connect 进行 Web 登录

OpenID Connect 是构建在 OAuth 2.0 基础之上的身份验证协议，可用于将用户安全登录到 Web 应用程序。 通过使用 OpenID Connect 的 Azure Active Directory B2C (Azure AD B2C) 实现，可以将 Web 应用程序中的注册、登录和其他标识管理体验转移到 Azure Active Directory (Azure AD) 中。 本指南演示如何使用与语言无关的方式执行此操作。 介绍在不使用我们的任何开放源代码库的情况下，如何发送和接收 HTTP 消息。

> [!NOTE]
> 大多数开源身份验证库会为应用程序获取并验证 JWT 令牌。 我们建议浏览这些选项，而不是实现自己的代码。 有关详细信息，请参阅 [Microsoft 身份验证库 (MSAL) 概述](../active-directory/develop/msal-overview.md)和 [Microsoft 标识 Web 身份验证库](../active-directory/develop/microsoft-identity-web.md)。

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) 扩展了 OAuth 2.0 *授权* 协议，将其用作 *身份验证* 协议。 使用此身份验证协议可以执行单一登录。 它引入了 ID 令牌的概念，可让客户端验证用户的标识，并获取有关用户的基本配置文件信息。

因为 OpenID Connect 扩展了 OAuth 2.0，因此，它还能使应用程序安全地获取访问令牌。 可以使用 access_token 访问由[授权服务器](protocols-overview.md)保护的资源。 如果要生成的 Web 应用程序托管在服务器中并通过浏览器访问，我们建议使用 OpenID Connect。 有关令牌的详细信息，请参阅 [Azure Active Directory B2C 中的令牌概述](tokens-overview.md)

Azure AD B2C 扩展了标准 OpenID Connect 协议，使其功能远远超出了简单的身份验证和授权。 它引入了[用户流参数](user-flow-overview.md)，可让你使用 OpenID Connect 向应用程序添加注册、登录和配置文件管理等用户体验。

## <a name="send-authentication-requests"></a>发送身份验证请求

当 Web 应用程序需要对用户进行身份验证并运行用户流时，它可以将用户定向到 `/authorize` 终结点。 用户可以根据用户流执行操作。

在此请求中，客户端指示需要在 `scope` 参数中从用户获取的权限，并指定要运行的用户流。 若要了解该请求的工作原理，请尝试将该请求粘贴到浏览器中并运行它。 将 `{tenant}` 替换为租户的名称。 将 `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` 替换为之前在租户中注册的应用程序的应用程序 ID。 此外，将策略名称 (`{policy}`) 更改为租户中的策略名称，例如 `b2c_1_sign_in`。

```http
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
```

| 参数 | 必需 | 说明 |
| --------- | -------- | ----------- |
| {tenant} | 是 | Azure AD B2C 租户的名称 |
| {policy} | 是 | 要运行的用户流。 指定在 Azure AD B2C 租户中创建的用户流的名称。 例如：`b2c_1_sign_in`、`b2c_1_sign_up` 或 `b2c_1_edit_profile`。 |
| client_id | 是 | [Azure 门户](https://portal.azure.com/)分配给应用程序的应用程序 ID。 |
| nonce | 是 | 由应用程序生成且包含在请求中的值，以声明方式包含在生成的 ID 令牌中。 应用程序接着便可确认此值，以减少令牌重新执行攻击。 此值通常是随机的唯一字符串，可用以识别请求的来源。 |
| response_type | 是 | 必须包含 OpenID Connect 的 ID 令牌。 如果 Web 应用程序还需要使用令牌来调用 Web API，则可以使用 `code+id_token`。 |
| scope | 是 | 范围的空格分隔列表。 `openid` 作用域表示允许使用 ID 令牌的形式使用户登录并获取有关用户的数据。 `offline_access` 范围对 Web 应用程序是可选的。 它表示应用程序需要使用刷新令牌来长期访问资源。 `https://{tenant-name}/{app-id-uri}/{scope}` 指示对受保护资源（例如 Web API）的权限。 有关详细信息，请参阅[请求访问令牌](access-tokens.md#scopes)。 |
| prompt | 否 | 需要的用户交互类型。 此时唯一有效的值为 `login`，这会强制用户在该请求上输入其凭据。 |
| redirect_uri | 否 | 应用程序的 `redirect_uri` 参数，应用程序可在此发送及接收身份验证响应。 它必须完全匹配在 Azure 门户中注册的其中一个 `redirect_uri` 参数，但必须经过 URL 编码。 |
| response_mode | 否 | 将生成的授权代码发回到应用程序所用的方法。 这可以是 `query`、`form_post` 或 `fragment`。  建议使用 `form_post` 响应模式以获得最佳安全性。 |
| state | 否 | 同时随令牌响应返回的请求中所包含的值。 可以是所需的任何内容的字符串。 随机生成的唯一值通常用于防止跨站点请求伪造攻击。 该状态也用于在身份验证请求出现之前，在应用程序中编码用户的状态信息，例如用户之前所在的页面。 |
| login_hint | 否| 可用于预先填充登录页面的“登录名”字段。 有关详细信息，请参阅[预填充登录名](direct-signin.md#prepopulate-the-sign-in-name)。  |
| domain_hint | 否| 向 Azure AD B2C 提供有关应该用于登录的社交标识提供者的提示。 如果包含了有效的值，用户将直接转到标识提供者登录页面。  有关详细信息，请参阅[将登录重定向到社交服务提供商](direct-signin.md#redirect-sign-in-to-a-social-provider)。 |
| 自定义参数 | 否| 可用于[自定义策略](custom-policy-overview.md)的自定义参数。 例如，[动态自定义页面内容 URI](customize-ui-with-html.md?pivots=b2c-custom-policy#configure-dynamic-custom-page-content-uri) 或[键值声明解析程序](claim-resolver-overview.md#oauth2-key-value-parameters)。 |

此时，要求用户完成工作流。 用户可能需要输入其用户名和密码、用社交标识登录，或注册目录。 可能还可任何其他若干步骤，具体取决于如何定义用户流。

用户完成用户流后，会使用 `response_mode` 参数中指定的方法，将响应返回到 `redirect_uri` 参数中指定的应用程序。 对于上述每种情况，响应均相同，而与用户流无关。

使用 `response_mode=fragment` 的成功的响应如下所示：

```http
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| 参数 | 说明 |
| --------- | ----------- |
| id_token | 应用程序请求的 ID 令牌。 可以使用 ID 令牌验证用户的标识，开始与用户建立会话。 |
| code | 如果使用了 `response_type=code+id_token`，则为应用程序请求的授权代码。 应用程序可以使用该授权代码请求目标资源的访问令牌。 授权代码通常在约 10 分钟后即会过期。 |
| state | 如果请求中包含 `state` 参数，响应中就应该出现相同的值。 应用程序需验证请求和响应中的 `state` 值是否相同。 |

错误响应也可能会被发送到 `redirect_uri` 参数，以便应用程序对它们进行恰当的处理：

```http
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| 参数 | 说明 |
| --------- | ----------- |
| error | 一个代码，可用于对发生的错误类型进行分类。 |
| error_description | 帮助识别身份验证错误根本原因的特定错误消息。 |
| state | 如果请求中包含 `state` 参数，响应中就应该出现相同的值。 应用程序需验证请求和响应中的 `state` 值是否相同。 |

## <a name="validate-the-id-token"></a>验证 ID 令牌

仅收到一个 ID 令牌并不表示可以对用户进行身份验证。 根据应用程序的要求验证 ID 令牌的签名和令牌中的声明。 Azure AD B2C 使用 [JSON Web 令牌 (JWT)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) 和公钥加密对令牌进行签名并验证其是否有效。 

> [!NOTE]
> 大多数开源身份验证库会为应用程序验证 JWT 令牌。 我们建议浏览这些选项，而不是实现自己的验证逻辑。 有关详细信息，请参阅 [Microsoft 身份验证库 (MSAL) 概述](../active-directory/develop/msal-overview.md)和 [Microsoft 标识 Web 身份验证库](../active-directory/develop/microsoft-identity-web.md)。

Azure AD B2C 具有 OpenID Connect 元数据终结点，允许应用程序在运行时获取有关 Azure AD B2C 的信息。 此信息包括终结点、令牌内容和令牌签名密钥。 B2C 租户中的每个用户流都有一个 JSON 元数据文档。 例如，`fabrikamb2c.onmicrosoft.com` 中 `b2c_1_sign_in` 用户流的元数据文档位于：

```http
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/v2.0/.well-known/openid-configuration
```

此配置文档的一个属性为 `jwks_uri`，对于相同用户流，该属性的值为：

```http
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/discovery/v2.0/keys
```

若要确定对 ID 令牌进行签名所使用的用户流（以及从何处获取元数据），可以使用两种方法。 第一种方法，用户流名称包含在 ID 令牌的 `acr` 声明中。 另一个方法是在发出请求时在 `state` 参数的值中对用户流进行编码，然后对其进行解码以确定使用的用户流。 任意一种方法均有效。

从 OpenID Connect 元数据终结点获取元数据文档后，可以使用 RSA-256 公钥来验证 ID 令牌的签名。 此终结点上可能列出多个密钥，每个密钥使用 `kid` 声明进行标识。 ID 令牌的标头还包含 `kid` 声明，该声明指示哪个密钥用于对 ID 令牌进行签名。

若要从 Azure AD B2C 验证令牌，需要使用指数 (e) 和模数 (n) 生成公钥。 需要确定如何在相应的编程语言中执行此操作。 可在以下网页中找到有关使用 RSA 协议生成公钥的官方文档： https://tools.ietf.org/html/rfc3447#section-3.1

验证 ID 令牌的签名后，还有几项声明需要验证。 例如：

- 验证 `nonce` 声明以防止令牌重放攻击。 其值应为在登录请求中指定的内容。
- 验证 `aud` 声明以确保已为应用程序颁发 ID 令牌。 其值应为应用程序的 ID。
- 验证 `iat` 和 `exp` 声明以确保 ID 令牌未过期。

此外，还需要执行更多的一些验证。 [OpenID Connect 核心规范](https://openid.net/specs/openid-connect-core-1_0.html)中详细介绍了验证。根据情况，可能还希望验证其他声明。 一些常见的验证包括：

- 确保用户/组织已注册应用程序。
- 确保用户拥有正确的授权/权限。
- 确保执行了一定强度的身份验证，例如 Azure AD 多重身份验证。

验证 ID 令牌后，便可以开始与用户的会话。 在应用程序中，可以使用 ID 令牌中的声明来获取用户的相关信息。 此信息的用途包括显示、记录和授权。

## <a name="get-a-token"></a>获取令牌

如果仅需要 Web 应用程序运行用户流，则可以跳过下面几个部分。 这些部分仅适用于需要对 Web API 进行验证的调用，以及受到 Azure AD B2C 保护的 Web 应用程序。

通过将 `POST` 请求发送到 `/token` 终结点，可以将获取的授权代码（通过 `response_type=code+id_token` 获取）兑换为所需资源的令牌。 在 Azure AD B2C 中，可以像往常一样通过在请求中指定其他 API 的范围来[为这些 API 请求访问令牌](access-tokens.md#request-a-token)。

还可以按照将应用的客户端 ID 用作所请求范围（这将导致具有该客户端 ID 的访问令牌作为“受众”）的约定，为应用自己的后端 Web API 请求访问令牌：

```http
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| 参数 | 必需 | 说明 |
| --------- | -------- | ----------- |
| {tenant} | 是 | Azure AD B2C 租户的名称 |
| {policy} | 是 | 用于获取授权代码的用户流。 无法在此请求中使用不同的用户流。 将此参数添加到查询字符串中，而不是添加到 POST 正文中。 |
| client_id | 是 | [Azure 门户](https://portal.azure.com/)分配给应用程序的应用程序 ID。 |
| client_secret | 是，在 Web 应用中 | 在 [Azure 门户](https://portal.azure.com/)中生成的应用程序机密。 客户端密码在此流中用于 Web 应用场景，在其中客户端可以安全地存储客户端密码。 对于本机应用（公共客户端）场景，客户端密码不能安全地存储，因此不能在此流上使用。 如果使用客户端密码，请定期更改。 |
| code | 是 | 在用户流的开头获取的授权代码。 |
| grant_type | 是 | 授予类型，该类型必须是授权代码流的 `authorization_code`。 |
| redirect_uri | 是 | 在其中收到授权代码的应用程序的 `redirect_uri` 参数。 |
| scope | 否 | 范围的空格分隔列表。 `openid` 作用域表示允许使用 id_token 参数的形式使用户登录并获取有关用户的数据。 它可以用于为应用程序的后端 Web API 获取令牌，该令牌使用和客户端相同的应用程序 ID 表示。 `offline_access` 范围表示应用程序需要使用刷新令牌来长期访问资源。 |

成功的令牌响应如下所示：

```json
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```

| 参数 | 说明 |
| --------- | ----------- |
| not_before | epoch 时间中令牌被视为有效的时间。 |
| token_type | 令牌类型值。 `Bearer` 是唯一支持的类型。 |
| access_token | 请求的已签名的 JWT 令牌。 |
| scope | 令牌的有效范围。 |
| expires_in | 访问令牌有效的时间长度（以秒为单位）。 |
| refresh_token | OAuth 2.0 刷新令牌。 应用程序可以使用此令牌，在当前令牌过期之后获取其他令牌。 刷新令牌可用于延长保留资源访问权限的时间。 必须在授权和令牌请求中使用范围 `offline_access`，才能接收刷新令牌。 |

错误响应如下所示：

```json
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| 参数 | 说明 |
| --------- | ----------- |
| error | 一个代码，可用于对发生的错误类型进行分类。 |
| error_description | 帮助识别身份验证错误根本原因的消息。 |

## <a name="use-the-token"></a>使用令牌

现在你已成功获取访问令牌，可通过在 `Authorization` 标头中加入令牌的方式，在后端 Web API 请求中使用该令牌：

```http
GET /tasks
Host: mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>刷新令牌

ID 令牌在短时间内即会过期。 在它们过期后，请刷新令牌以便能够继续访问资源。 可以通过向 `/token` 终结点提交另一个 `POST` 请求来刷新令牌。 此时，提供 `refresh_token` 参数而不是 `code` 参数：

```http
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| 参数 | 必需 | 说明 |
| --------- | -------- | ----------- |
| {tenant} | 是 | Azure AD B2C 租户的名称 |
| {policy} | 是 | 用于获取原始刷新令牌的用户流。 无法在此请求中使用不同的用户流。 将此参数添加到查询字符串中，而不是添加到 POST 正文中。 |
| client_id | 是 | [Azure 门户](https://portal.azure.com/)分配给应用程序的应用程序 ID。 |
| client_secret | 是，在 Web 应用中 | 在 [Azure 门户](https://portal.azure.com/)中生成的应用程序机密。 客户端密码在此流中用于 Web 应用场景，在其中客户端可以安全地存储客户端密码。 对于本机应用（公共客户端）场景，客户端密码不能安全地存储，因此不能用于此调用。 如果使用客户端密码，请定期更改。 |
| grant_type | 是 | 授予类型，该类型必须是这一部分授权代码流的 `refresh_token`。 |
| refresh_token | 是 | 在流的第二部分获取的原始刷新令牌。 必须在授权和令牌请求中使用范围 `offline_access`，才能接收刷新令牌。 |
| redirect_uri | 否 | 在其中收到授权代码的应用程序的 `redirect_uri` 参数。 |
| scope | 否 | 范围的空格分隔列表。 `openid` 作用域表示允许使用 ID 令牌的形式使用户登录并获取有关用户的数据。 它可以用于向应用程序的后端 Web API 发送令牌，该令牌使用和客户端相同的应用程序 ID 表示。 `offline_access` 范围表示应用程序需要使用刷新令牌来长期访问资源。 |

成功的令牌响应如下所示：

```json
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```

| 参数 | 说明 |
| --------- | ----------- |
| not_before | epoch 时间中令牌被视为有效的时间。 |
| token_type | 令牌类型值。 `Bearer` 是唯一支持的类型。 |
| access_token | 请求的已签名 JWT 令牌。 |
| scope | 令牌的有效范围。 |
| expires_in | 访问令牌有效的时间长度（以秒为单位）。 |
| refresh_token | OAuth 2.0 刷新令牌。 应用程序可以使用此令牌，在当前令牌过期之后获取其他令牌。 刷新令牌可用于延长保留资源访问权限的时间。 |

错误响应如下所示：

```json
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| 参数 | 说明 |
| --------- | ----------- |
| error | 一个代码，可用于对发生的错误类型进行分类。 |
| error_description | 帮助识别身份验证错误根本原因的消息。 |

## <a name="send-a-sign-out-request"></a>发送注销请求

如果想要从应用程序中注销用户，只是清除应用程序的 Cookie 或者结束与用户的会话是不够的。 需将用户重定向到 Azure AD B2C 进行注销。如果没有这么做，那么用户可能可以在应用程序中重新进行身份验证，且无需再次输入其凭据。 有关详细信息，请参阅 [Azure AD B2C 会话](session-behavior.md)。

若要将用户注销，请将用户重定向到前面所述的 OpenID Connect 元数据文档中列出的 `end_session` 终结点：

```http
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/logout?post_logout_redirect_uri=https%3A%2F%2Fjwt.ms%2F
```

| 参数 | 必需 | 说明 |
| --------- | -------- | ----------- |
| {tenant} | 是 | Azure AD B2C 租户的名称 |
| {policy} | 是 | 想要用于从应用程序中注销用户的用户流。 |
| id_token_hint| 否 | 以前颁发的 ID 令牌，该令牌将作为有关最终用户当前与客户端建立的身份验证会话的提示传递给注销终结点。 `id_token_hint` 确保 `post_logout_redirect_uri` 是 Azure AD B2C 应用程序设置中的已注册回复 URL。 有关详细信息，请参阅[保护注销重定向](#secure-your-logout-redirect)。 |
| client_id | 否* | [Azure 门户](https://portal.azure.com/)分配给应用程序的应用程序 ID。<br><br>\*使用 `Application` 隔离 SSO 配置并且注销请求中的所需 ID 令牌设置为 `No` 时，这是必需的。 |
| post_logout_redirect_uri | 否 | 用户在成功注销后应重定向到的 URL。如果未包含此参数，Azure AD B2C 会向用户显示一条常规消息。 除非提供 `id_token_hint`，否则不应在 Azure AD B2C 应用程序设置中将此 URL 注册为回复 URL。 |
| state | 否 | 如果请求中包含 `state` 参数，响应中就应该出现相同的值。 应用程序需验证请求和响应中的 `state` 值是否相同。 |

### <a name="secure-your-logout-redirect"></a>保护注销重定向

注销后，用户将重定向到 `post_logout_redirect_uri` 参数中指定的 URI，而不管为应用程序指定的回复 URL 为何。 但是，如果传递了有效的 `id_token_hint` 并启用了“注销请求中需要 ID 令牌”，则在执行重定向之前，Azure AD B2C 将验证 `post_logout_redirect_uri` 的值是否与应用程序的某个已配置重定向 URI 相匹配。 如果没有为应用程序配置匹配的回复 URL，则会显示一条错误消息，而用户不会重定向。

若要在注销请求中设置所需的 ID 令牌，请参阅[在 Azure Active Directory B2C 中配置会话行为](session-behavior.md#secure-your-logout-redirect)。

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure AD B2C 会话](session-behavior.md)。
