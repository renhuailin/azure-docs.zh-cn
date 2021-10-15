---
title: 使用隐式流的单页登录
titleSuffix: Azure AD B2C
description: 了解如何通过 Azure Active Directory B2C 添加使用 OAuth 2.0 隐式流的单页登录。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/19/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 31dd0096140544db9c1265999b8c0c709def9cda
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129350066"
---
# <a name="single-page-sign-in-using-the-oauth-20-implicit-flow-in-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 中的 OAuth 2.0 隐式流的单页登录

许多新式应用程序都有一个单页应用 (SPA) 前端（主要以 JavaScript 编写）。 通常，该应用可使用 React、Angular 或 Vue.js 等框架进行编写。 主要在浏览器中运行的 SPA 和其他 JavaScript 应用在身份验证时还面临一些其他挑战：

- 这些应用程序的安全特征与传统的基于服务器的 Web 应用程序不同。
- 许多授权服务器与标识提供者不支持跨源资源共享 (CORS) 请求。
- 重定向离开应用的完整网页浏览器可能会对用户体验具有侵略性。

支持 SPA 的推荐方法是 [OAuth 2.0 授权代码流（使用 PKCE）](./authorization-code-flow.md)。

某些框架（如 [MSAL.js 1.x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core)）仅支持隐式授权流。 在这些情况下，Azure Active Directory B2C (Azure AD B2C) 支持 OAuth 2.0 授权隐式授权流。 有关该流的说明，请参阅 [OAuth 2.0 规范第 4.2 节](https://tools.ietf.org/html/rfc6749)。 在隐式流中，应用直接从 Azure Active Directory (Azure AD) 授权终结点接收令牌，无需任何服务器到服务器的交换。 所有身份验证逻辑和会话处理全部在 JavaScript 客户端中通过页面重定向或弹框执行。

Azure AD B2C 扩展了标准 OAuth 2.0 隐式流，使其功能远远超出了简单的身份验证和授权。 Azure AD B2C 引入了[策略参数](user-flow-overview.md)。 通过该策略参数，可以使用 OAuth 2.0 向应用添加策略，例如注册、登录和配置文件管理用户流。 在本文的示例 HTTP 请求中，使用了 {tenant}.onmicrosoft.com 作为示例。 如果你有一个租户并且还创建了用户流，请将 `{tenant}` 替换为该租户的名称。

隐式登录流看起来类似于下图。 本文后面将详细说明每个步骤。

![显示 OpenID Connect 隐式流的泳道样式示意图](./media/implicit-flow-single-page-application/convergence_scenarios_implicit.png)

## <a name="send-authentication-requests"></a>发送身份验证请求

当 Web 应用程序需要对用户进行身份验证并运行用户流时，它可以将用户定向到 `/authorize` 终结点。 用户可以根据用户流执行操作。

在此请求中，客户端在 `scope` 参数中指明需要从用户获取的权限以及要运行的用户流。 若要了解该请求的工作原理，请尝试将该请求粘贴到浏览器中并运行它。 将 `{tenant}` 替换为 Azure AD B2C 租户的名称。 将 `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` 替换为之前在租户中注册的应用程序的应用程序 ID。 将 `{policy}` 替换为在租户中创建的策略的名称，例如 `b2c_1_sign_in`。

```http
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
```

| 参数 | 必需 | 说明 |
| --------- | -------- | ----------- |
|{tenant}| 是 | Azure AD B2C 租户的名称|
|{policy}| 是| 要运行的用户流。 指定在 Azure AD B2C 租户中创建的用户流的名称。 例如：`b2c_1_sign_in`、`b2c_1_sign_up` 或 `b2c_1_edit_profile`。 |
| client_id | 是 | [Azure 门户](https://portal.azure.com/)分配给应用程序的应用程序 ID。 |
| response_type | 是 | 必须包含 OpenID Connect 登录的 `id_token` 。 也可以包含响应类型 `token`。 如果使用 `token`，则应用能够立即从授权终结点接收访问令牌，而无需向授权终结点发出第二次请求。  如果使用 `token` 响应类型，`scope` 参数必须包含一个范围，以指出要对哪个资源发出令牌。 |
| redirect_uri | 否 | 应用的重定向 URI，应用可在其中发送和接收身份验证响应。 它必须完全匹配在门户中注册的其中一个重定向 URI，但必须经 URL 编码。 |
| response_mode | 否 | 指定用于将生成的令发送回应用的方法。  对于隐式流，使用 `fragment`。 |
| scope | 是 | 范围的空格分隔列表。 一个范围值，该值向 Azure AD 指示正在请求的两个权限。 `openid` 作用域表示允许使用 ID 令牌的形式使用户登录并获取有关用户的数据。 `offline_access` 作用域对 Web 应用是可选的。 它表示你的应用需要刷新令牌才能长期访问资源。 |
| state | 否 | 同时随令牌响应返回的请求中所包含的值。 它可以是你想要使用的任何内容的字符串。 随机生成的唯一值通常用于防止跨站点请求伪造攻击。 该状态也用于在身份验证请求出现之前，在应用中编码用户的状态信息，例如用户之前所在的页面。 |
| nonce | 是 | 由应用生成且包含在请求中的值，以声明方式包含在生成的 ID 令牌中。 然后，应用可以验证此值，以减少令牌重播攻击。 此值通常是随机产生的唯一字符串，可用于识别请求的来源。 |
| prompt | 否 | 需要的用户交互类型。 目前唯一有效的值是 `login`。 此参数会强制用户在该请求上输入其凭据。 单一登录不会生效。 |

此时，要求用户完成策略的工作流。 用户可能必须输入其用户名和密码、用社交标识登录、注册目录或者执行任何其他数目的步骤。 用户操作取决于用户流是如何定义的。

在用户完成用户流后，Azure AD B2C 会在你用于 `redirect_uri` 的值处将响应返回到应用。 它使用在 `response_mode` 参数中指定的方法。 对于每种用户操作情况，响应完全相同，与执行的用户流无关。

### <a name="successful-response"></a>成功的响应
使用 `response_mode=fragment` 和 `response_type=id_token+token` 的成功响应如下所示（包含换行符以便阅读）：

```http
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
```

| 参数 | 说明 |
| --------- | ----------- |
| access_token | 应用请求的访问令牌。 |
| token_type | 令牌类型值。 Azure AD 唯一支持的类型是 Bearer。 |
| expires_in | 访问令牌有效的时间长度（以秒为单位）。 |
| scope | 令牌的有效范围。 还可使用范围来缓存令牌，以供以后使用。 |
| id_token | 应用请求的 ID 令牌。 可以使用 ID 令牌验证用户的标识，开始与用户建立会话。 有关 ID 令牌及其内容的详细信息，请参阅 [Azure AD B2C 令牌参考](tokens-overview.md)。 |
| state | 如果请求中包含 `state` 参数，响应中就应该出现相同的值。 应用需验证请求和响应中的 `state` 值是否相同。 |

### <a name="error-response"></a>错误响应
错误响应也可能发送到重定向 URI，让应用能够对其进行适当处理：

```http
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| 参数 | 说明 |
| --------- | ----------- |
| error | 一个代码，用于对发生的错误类型进行分类。 |
| error_description | 帮助识别身份验证错误根本原因的特定错误消息。 |
| state | 如果请求中包含 `state` 参数，响应中就应该出现相同的值。 应用需验证请求和响应中的 `state` 值是否相同。|

## <a name="validate-the-id-token"></a>验证 ID 令牌

收到 ID 令牌并不表示可以对用户进行身份验证。 根据应用的要求验证 ID 令牌的签名和令牌中的声明。 Azure AD B2C 使用 [JSON Web 令牌 (JWT)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) 和公钥加密对令牌进行签名并验证其是否有效。

许多开放源代码库都可用于验证 JWT，具体取决于首选使用的语言。 请考虑浏览可用的开放源代码库，而不是实现自己的验证逻辑。 可以使用本文中的信息了解如何正确使用这些库。

Azure AD B2C 具有 OpenID Connect 元数据终结点。 应用可以使用终结点在运行时提取 Azure AD B2C 的相关信息。 此信息包括终结点、令牌内容和令牌签名密钥。 Azure AD B2C 租户中的每个用户流都有一个 JSON 元数据文档。 例如，fabrikamb2c.onmicrosoft.com 租户中的 b2c_1_sign_in 用户流的元数据文档位于以下位置：

```http
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/v2.0/.well-known/openid-configuration
```

此配置文档的其中一个属性是 `jwks_uri`。 相同用户流的值是：

```http
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/discovery/v2.0/keys
```

若要确定对 ID 令牌签名所用的用户流（以及提取元数据的位置），可以采取两种做法：
-  将用户流名称包含在 `id_token` 的 `acr` 声明中。 有关如何从 ID 令牌中分析声明的信息，请参阅 [Azure AD B2C 令牌参考](tokens-overview.md)。 
- 发出请求时在 `state` 参数的值中对用户流进行编码。 然后对 `state` 参数进行解码以确定所使用的用户流。 任意一种方法均有效。

从 OpenID Connect 元数据终结点获取元数据文档后，可以使用 RSA-256 公钥（位于此终结点上）来验证 ID 令牌的签名。 在任何给定的时间，此终结点上可能列出多个密钥，每个密钥使用 `kid` 进行标识。 `id_token` 的标头还包含 `kid` 声明。 它指示这些键中的哪一个用于对 ID 令牌进行签名。 有关详细信息（包括了解[验证令牌](tokens-overview.md)），请参阅 [Azure AD B2C 令牌参考](tokens-overview.md)。
<!--TODO: Improve the information on this-->

在验证 ID 令牌的签名后，需要验证几个声明。 例如：

* 验证 `nonce` 声明以防止令牌重放攻击。 其值应为在登录请求中指定的内容。
* 验证 `aud` 声明以确保已为应用颁发 ID 令牌。 其值应为应用的应用程序 ID。
* 验证 `iat` 和 `exp` 声明以确保 ID 令牌未过期。

其他一些需要执行的验证在 [OpenID Connect 核心规范](https://openid.net/specs/openid-connect-core-1_0.html)中有详细说明。根据情况，可能还希望验证其他声明。 一些常见的验证包括：

* 确保用户或组织已注册应用。
* 确保用户拥有正确的授权和权限。
* 确保通过某种方式（例如，通过 Azure AD 多重身份验证）执行了一定强度的身份验证。

有关 ID 令牌中声明的详细信息，请参阅 [Azure AD B2C 令牌参考](tokens-overview.md)。

验证 ID 令牌后，可以开始与用户的会话。 在应用中，使用 ID 令牌中的声明来获取用户的相关信息。 此信息可以用于显示、记录和授权等等。

## <a name="get-access-tokens"></a>获取访问令牌
如果 Web 应用唯一需要做的是执行用户流，则可以跳过下面几节。 下面几节中的信息仅适用于需要对 Web API 进行验证调用，并且受 Azure AD B2C 保护的 Web 应用。

将用户登录到 SPA 后，可以获取访问令牌以调用受 Azure AD 保护的 Web API。 即使已使用 `token` 响应类型收到令牌，也仍可以使用此方法获取其他资源的令牌，而无需再次将用户重定向到登录页。

在典型的 Web 应用流中，你将对 `/token` 终结点发出请求。 但是，该终结点不支持 CORS 请求，因此进行 AJAX 调用以获取和刷新令牌并不可取。 相反，可以在隐藏的 HTML iframe 元素中使用隐式流，以获取其他 Web API 的新令牌。 下面是一个示例（带换行符以便阅读）：

```http
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
&response_mode=fragment
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&prompt=none
```

| 参数 | 必需？ | 说明 |
| --- | --- | --- |
|{tenant}| 必需 | Azure AD B2C 租户的名称|
{policy}| 必须| 要运行的用户流。 指定在 Azure AD B2C 租户中创建的用户流的名称。 例如：`b2c_1_sign_in`、`b2c_1_sign_up` 或 `b2c_1_edit_profile`。 |
| client_id |必需 |在 [Azure 门户](https://portal.azure.com)中分配给应用的应用程序 ID。 |
| response_type |必须 |必须包含 OpenID Connect 登录的 `id_token` 。  也可能包含响应类型 `token`。 如果在此处使用 `token`，应用能够立即从授权终结点接收访问令牌，而无需向授权终结点发出第二次请求。 如果使用 `token` 响应类型，`scope` 参数必须包含一个范围，以指出要对哪个资源发出令牌。 |
| redirect_uri |建议 |应用的重定向 URI，应用可在其中发送和接收身份验证响应。 它必须与门户中注册的其中一个重定向 URI 完全匹配，否则必须经过 URL 编码。 |
| scope |必须 |范围的空格分隔列表。  若要获取令牌，请包含相应资源所需的所有范围。 |
| response_mode |建议 |指定用于将生成的令牌送回到应用的方法。 对于隐式流，请使用 `fragment`。 可以指定其他两种模式（`query` 和 `form_post`），但在隐式流中不起作用。 |
| state |建议 |随令牌响应返回的请求中所包含的值。  它可以是你想要使用的任何内容的字符串。  随机生成的唯一值通常用于防止跨站点请求伪造攻击。  它还可用于在身份验证请求发生前，对有关用户在应用中的状态信息进行编码。 例如，用户之前所在的页面或视图。 |
| nonce |必需 |由应用生成且包含在请求中的值，以声明方式包含在生成的 ID 令牌 中。  然后，应用可以验证此值，以减少令牌重播攻击。 此值通常是随机产生的唯一字符串，可识别请求的来源。 |
| prompt |必需 |若要刷新并获取隐藏的 iframe 中的令牌，请使用 `prompt=none` 以确保 iframe 会立即返回，而不会停滞在登录页面上。 |
| login_hint |必需 |若要刷新并获取隐藏的 iframe 中的令牌，请在此提示中加入用户的用户名，以便区分用户在给定时间内可能具有的多个会话。 可以使用 `preferred_username` 声明从以前的登录名中提取用户名（需要使用 `profile` 范围来接收 `preferred_username` 声明）。 |
| domain_hint |必需 |可以是 `consumers` 或 `organizations`。  若要刷新并获取隐藏的 iframe 中的令牌，请在请求中包含 `domain_hint` 值。  从以前登录名的 ID 令牌中提取 `tid` 声明，以确定要使用的值（需要使用 `profile` 范围来接收 `tid` 声明）。 如果 `tid` 声明值是 `9188040d-6c67-4c5b-b112-36a304b66dad`，请使用 `domain_hint=consumers`。  否则使用 `domain_hint=organizations`。 |

通过设置 `prompt=none` 参数，此请求会立即成功或立即失败，并返回到应用程序。  成功的响应会通过 `response_mode` 参数中指定的方法，发送到位于所指示的重定向 URI 的应用。

### <a name="successful-response"></a>成功的响应
使用 `response_mode=fragment` 的成功响应如以下示例所示：

```http
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
```

| 参数 | 说明 |
| --- | --- |
| access_token |应用请求的令牌。 |
| token_type |令牌类型始终是“持有者”。 |
| state |如果请求中包含 `state` 参数，响应中就应该出现相同的值。 应用需验证请求和响应中的 `state` 值是否相同。 |
| expires_in |访问令牌有效的时间长度（以秒为单位）。 |
| scope |访问令牌有效的范围。 |

### <a name="error-response"></a>错误响应
错误响应也可能发送到重定向 URI，使应用可以对其进行适当处理。  对于 `prompt=none`，错误应如以下示例所示：

```http
GET https://aadb2cplayground.azurewebsites.net/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| 参数 | 说明 |
| --- | --- |
| error |错误代码字符串，可用于对发生的错误类型进行分类。 还可使用该字符串对错误作出响应。 |
| error_description |帮助识别身份验证错误根本原因的特定错误消息。 |

如果在 iframe 请求中收到此错误，用户必须再次以交互方式登录以检索新令牌。

## <a name="refresh-tokens"></a>刷新令牌
ID 令牌和访问令牌在较短时间后都会过期。 应用必须准备好定期刷新这些令牌。  若要刷新任一类型的令牌，请使用 `prompt=none` 参数控制 Azure AD 步骤，执行我们在先前示例中使用的同一隐藏的 iframe 请求。  若要接收新的 `id_token` 值，请务必使用 `response_type=id_token` 和 `scope=openid`，以及 `nonce` 参数。

## <a name="send-a-sign-out-request"></a>发送注销请求
如果想要从应用中注销用户，请将用户重定向到 Azure AD 进行注销。如果不对用户进行重定向，用户可能不需要输入其凭据就能重新通过应用的身份验证，因为他们与 Azure AD 之间仍然存在有效的单一登录会话。

只需将用户重定向到[验证 ID 令牌](#validate-the-id-token)中所述的相同 OpenID Connect 元数据文档中列出的 `end_session_endpoint`。 例如：

```http
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/logout?post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| 参数 | 必需 | 说明 |
| --------- | -------- | ----------- |
| {tenant} | 是 | Azure AD B2C 租户的名称 |
| {policy} | 是 | 想要用于从应用程序中注销用户的用户流。 |
| post_logout_redirect_uri | 否 | 用户在成功注销后应重定向到的 URL。如果未包含此参数，Azure AD B2C 会向用户显示一条常规消息。 |
| state | 否 | 如果请求中包含 `state` 参数，响应中就应该出现相同的值。 应用程序需验证请求和响应中的 `state` 值是否相同。 |


> [!NOTE]
> 将用户定向到 `end_session_endpoint` 会清除用户的某些 Azure AD B2C 单一登录状态。 但是，不会从用户的社交标识提供者会话中注销该用户。 如果用户在后续登录中选择相同的标识提供者，用户将重新进行身份验证，且无需输入其凭据。 例如，如果用户要注销 Azure AD B2C 应用程序，并不表示他们想要完全注销其 Facebook 帐户。 但是，如果是本地帐户，则会以正确的方式结束用户的会话。
>

## <a name="next-steps"></a>后续步骤

参阅代码示例：[在 JavaScript SPA 中使用 Azure AD B2C 登录](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/msal-core-samples/VanillaJSTestApp/app/b2c)。
