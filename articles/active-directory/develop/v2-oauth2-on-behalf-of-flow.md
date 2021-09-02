---
title: Microsoft 标识平台和 OAuth2.0 代理流 | Azure
titleSuffix: Microsoft identity platform
description: 本文介绍如何使用 OAuth2.0 代理流通过 HTTP 消息实现服务到服务身份验证。
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 07/16/2021
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: ae74589dbbde2402d3acd916f2c5c1f58a7a5c7c
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114464124"
---
# <a name="microsoft-identity-platform-and-oauth-20-on-behalf-of-flow"></a>Microsoft 标识平台和 OAuth 2.0 代理流

OAuth 2.0 代理流 (OBO) 适用于这样的用例：应用程序调用某个服务/Web API，而后者又需要调用另一个服务/Web API。 思路是通过请求链传播委托用户标识和权限。 要使中间层服务向下游服务发出身份验证请求，该服务需要代表用户保护 Microsoft 标识平台提供的访问令牌。

OBO 流目前仅适用于用户主体。 服务主体无法请求仅限应用的令牌，无法将其发送到 API，也无法使该 API 与表示原始服务主体的另一个令牌交换。 此外，OBO 流侧重于代表另一方进行操作（称为委托场景），这意味着它只使用委托范围，而不使用应用程序角色进行权限推理。 角色仍然依附于流中的主体（用户），而不是代表用户操作的应用程序。

本文介绍如何在应用程序中直接针对协议进行编程。 如果可能，建议改为使用受支持的 Microsoft 身份验证库 (MSAL) 来[获取令牌并调用受保护的 Web API](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)。  另请参阅[使用 MSAL 的示例应用](sample-v2-code.md)。

自 2018 年 5 月起，派生 `id_token` 的某些隐式流不能用于 OBO 流。 单页应用 (SPA) 应改为将 **访问** 令牌传递给中间层机密客户端，才能执行 OBO 流。 有关哪些客户端可以执行 OBO 调用的详细信息，请参阅[限制](#client-limitations)。

[!INCLUDE [try-in-postman-link](includes/try-in-postman-link.md)]

## <a name="protocol-diagram"></a>协议图

假设已在应用程序中使用 [OAuth 2.0 授权代码授权流](v2-oauth2-auth-code-flow.md)或其他登录流对用户进行身份验证。 此时，应用程序已获得 API A 的访问令牌（令牌 A），其中包含用户对访问中间层 Web API (API A) 的声明和许可。 现在，API A 需要向下游 Web API (API B) 发出身份验证请求。

所遵循的步骤构成 OBO 流，并借助以下关系图进行说明。

![显示 OAuth2.0 代理流](./media/v2-oauth2-on-behalf-of-flow/protocols-oauth-on-behalf-of-flow.png)

1. 客户端应用程序使用令牌 A（其中包含 API A 的 `aud` 声明）向 API A 发出请求。
1. API A 向 Microsoft 标识平台令牌颁发终结点进行身份验证并请求访问 API B 的令牌。
1. Microsoft 标识平台令牌颁发终结点使用令牌 A 验证 API A 的凭据，并颁发供 API B（令牌 B）访问 API A 的访问令牌。
1. 令牌 B 由 API A 在向 API B 发出的请求的 authorization 标头中设置。
1. 受保护资源中的数据将通过 API B 返回到 API A，然后返回到客户端。

在此方案中，中间层服务无需用户干预，即可获取用户对访问下游 API 的同意。 因此，在身份验证过程的同意步骤中会提前显示授权访问下游 API 的选项。 若要了解如何为应用设置此选项，请参阅[为中间层应用程序获得同意](#gaining-consent-for-the-middle-tier-application)。

## <a name="middle-tier-access-token-request"></a>中间层访问令牌请求

若要请求访问令牌，请使用以下参数向特定于租户的 Microsoft 标识平台令牌终结点发出 HTTP POST。

```
https://login.microsoftonline.com/<tenant>/oauth2/v2.0/token
```

有两种情况，具体取决于客户端应用程序选择由共享密钥还是由证书保护。

### <a name="first-case-access-token-request-with-a-shared-secret"></a>第一种情况：使用共享机密访问令牌请求

使用共享密钥时，服务到服务访问令牌请求包含以下参数：

| 参数 | 类型 | 说明 |
| --- | --- | --- |
| `grant_type` | 必须 | 令牌请求的类型。 对于使用 JWT 的请求，该值必须为 `urn:ietf:params:oauth:grant-type:jwt-bearer`。 |
| `client_id` | 必须 | [Azure 门户 - 应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)页分配给应用的应用程序（客户端）ID。 |
| `client_secret` | 必须 | 在“Azure 门户 - 应用注册”页中为应用生成的客户端机密。 |
| `assertion` | 必须 | 已发送到中间层 API 的访问令牌。  此令牌必须包含发出此 OBO 请求的应用（由 `client-id` 字段表示的应用）的受众 (`aud`) 声明。 应用程序无法兑换其他应用的令牌（例如，如果客户端向 API 发送用于 MS Graph 的令牌，则该 API 无法使用 OBO 兑换该令牌。  它应该改为拒绝该令牌）。  |
| `scope` | 必选 | 空格分隔的令牌请求作用域的列表。 有关详细信息，请参阅[作用域](v2-permissions-and-consent.md)。 |
| `requested_token_use` | 必须 | 指定应如何处理请求。 在 OBO 流中，该值必须设置为 `on_behalf_of`。 |

#### <a name="example"></a>示例

以下 HTTP POST 通过 `user.read` 作用域请求 https://graph.microsoft.com Web API 的访问令牌和刷新令牌。

```HTTP
//line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com/<tenant>
Content-Type: application/x-www-form-urlencoded

grant_type=urn:ietf:params:oauth:grant-type:jwt-bearer
client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&client_secret=sampleCredentia1s
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiIyO{a lot of characters here}
&scope=https://graph.microsoft.com/user.read+offline_access
&requested_token_use=on_behalf_of
```

### <a name="second-case-access-token-request-with-a-certificate"></a>第二种情况：使用证书访问令牌请求

使用证书的服务到服务访问令牌请求包含以下参数：

| 参数 | 类型 | 说明 |
| --- | --- | --- |
| `grant_type` | 必须 | 令牌请求的类型。 对于使用 JWT 的请求，该值必须为 `urn:ietf:params:oauth:grant-type:jwt-bearer`。 |
| `client_id` | 必须 |  [Azure 门户 - 应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)页分配给应用的应用程序（客户端）ID。 |
| `client_assertion_type` | 必须 | 值必须是 `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`。 |
| `client_assertion` | 必须 | 断言（JSON Web 令牌），需使用作为凭据向应用程序注册的证书进行创建和签名。 若要了解如何注册证书以及断言的格式，请参阅[证书凭据](active-directory-certificate-credentials.md)。 |
| `assertion` | 必须 |  已发送到中间层 API 的访问令牌。  此令牌必须包含发出此 OBO 请求的应用（由 `client-id` 字段表示的应用）的受众 (`aud`) 声明。 应用程序无法兑换其他应用的令牌（例如，如果客户端向 API 发送用于 MS Graph 的令牌，则该 API 无法使用 OBO 兑换该令牌。  它应该改为拒绝该令牌）。  |
| `requested_token_use` | 必选 | 指定应如何处理请求。 在 OBO 流中，该值必须设置为 `on_behalf_of`。 |
| `scope` | 必须 | 空格分隔的令牌请求范围的列表。 有关详细信息，请参阅[作用域](v2-permissions-and-consent.md)。|

请注意，这些参数与共享密钥请求的参数几乎相同，只不过 `client_secret` 参数替换为以下两个参数：`client_assertion_type` 和 `client_assertion`。

#### <a name="example"></a>示例

以下 HTTP POST 通过 `user.read` 作用域请求具有证书的 https://graph.microsoft.com Web API 的访问令牌。

```HTTP
// line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com/<tenant>
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiO{a lot of characters here}
&requested_token_use=on_behalf_of
&scope=https://graph.microsoft.com/user.read+offline_access
```

## <a name="middle-tier-access-token-response"></a>中间层访问令牌响应

成功响应是具有以下参数的 JSON OAuth 2.0 响应。

| 参数 | 说明 |
| --- | --- |
| `token_type` | 指示令牌类型值。 Microsoft 标识平台支持的唯一类型是 `Bearer`。 有关持有者令牌的详细信息，请参阅 [OAuth 2.0 授权框架：持有者令牌用法 (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt)。 |
| `scope` | 令牌中授予的访问权限的范围。 |
| `expires_in` | 访问令牌有效的时间长度（以秒为单位）。 |
| `access_token` | 请求的访问令牌。 调用方服务可以使用此令牌向接收方服务进行身份验证。 |
| `refresh_token` | 所请求的访问令牌的刷新令牌。 当前访问令牌过期后，调用方服务可以使用此令牌请求另一个访问令牌。 仅当已请求 `offline_access` 作用域时提供刷新令牌。 |

### <a name="success-response-example"></a>成功响应示例

以下示例演示对 https://graph.microsoft.com Web API 的访问令牌请求的成功响应。

```json
{
  "token_type": "Bearer",
  "scope": "https://graph.microsoft.com/user.read",
  "expires_in": 3269,
  "ext_expires_in": 0,
  "access_token": "eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkQ0NDYy0tY0hGa18wZE50MVEtc2loVzRMd2RwQVZISGpnTVdQZ0tQeVJIaGlDbUN2NkdyMEpmYmRfY1RmMUFxU21TcFJkVXVydVJqX3Nqd0JoN211eHlBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMzA1LCJuYmYiOjE0OTM5MzAzMDUsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQU9KYnFFWlRNTnEyZFcxYXpKN1RZMDlYeDdOT29EMkJEUlRWMXJ3b2ZRc1k9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJWR1ItdmtEZlBFQ2M1dWFDaENRSkFBIiwidmVyIjoiMS4wIn0.cubh1L2VtruiiwF8ut1m9uNBmnUJeYx4x0G30F7CqSpzHj1Sv5DCgNZXyUz3pEiz77G8IfOF0_U5A_02k-xzwdYvtJUYGH3bFISzdqymiEGmdfCIRKl9KMeoo2llGv0ScCniIhr2U1yxTIkIpp092xcdaDt-2_2q_ql1Ha_HtjvTV1f9XR3t7_Id9bR5BqwVX5zPO7JMYDVhUZRx08eqZcC-F3wi0xd_5ND_mavMuxe2wrpF-EZviO3yg0QVRr59tE3AoWl8lSGpVc97vvRCnp4WVRk26jJhYXFPsdk4yWqOKZqzr3IFGyD08WizD_vPSrXcCPbZP3XWaoTUKZSNJg",
  "refresh_token": "OAQABAAAAAABnfiG-mA6NTae7CdWW7QfdAALzDWjw6qSn4GUDfxWzJDZ6lk9qRw4An{a lot of characters here}"
}
```

上述访问令牌是 v1.0 格式的 Microsoft Graph 令牌。 这是因为令牌格式基于所访问的资源，而与请求它时使用的终结点无关。 Microsoft Graph 设置为接受 v1.0 令牌，因此当客户端请求 Microsoft Graph 的令牌时，Microsoft 标识平台会生成 v1.0 访问令牌。 其他应用可能指示它们需要 v2.0 格式的令牌、1.0 格式的令牌甚至专用或加密格式的令牌。  v1.0 和 v2.0 终结点都可以发出任意一种令牌格式，这样资源就可以始终获得正确的令牌格式，而不管客户端如何或在何处请求令牌。 

[!INCLUDE [remind-not-to-validate-access-tokens](includes/remind-not-to-validate-access-tokens.md)]

### <a name="error-response-example"></a>错误响应示例

如果已对下游 API 设置条件访问策略（如[多重身份验证](../authentication/concept-mfa-howitworks.md)），则在尝试获取下游 API 的访问令牌时，令牌终结点会返回错误响应。 中间层服务应向客户端应用程序显示此错误，以便客户端应用程序可以提供用户交互，以满足条件访问策略。

```json
{
    "error":"interaction_required",
    "error_description":"AADSTS50079: Due to a configuration change made by your administrator, or because you moved to a new location, you must enroll in multifactor authentication to access 'bf8d80f9-9098-4972-b203-500f535113b1'.\r\nTrace ID: b72a68c3-0926-4b8e-bc35-3150069c2800\r\nCorrelation ID: 73d656cf-54b1-4eb2-b429-26d8165a52d7\r\nTimestamp: 2017-05-01 22:43:20Z",
    "error_codes":[50079],
    "timestamp":"2017-05-01 22:43:20Z",
    "trace_id":"b72a68c3-0926-4b8e-bc35-3150069c2800",
    "correlation_id":"73d656cf-54b1-4eb2-b429-26d8165a52d7",
    "claims":"{\"access_token\":{\"polids\":{\"essential\":true,\"values\":[\"9ab03e19-ed42-4168-b6b7-7001fb3e933a\"]}}}"
}
```

## <a name="use-the-access-token-to-access-the-secured-resource"></a>使用访问令牌访问受保护资源

现在，中间层服务可以通过在 `Authorization` 标头中设置令牌，使用上面获取的令牌向下游 Web API 发出身份验证请求。

### <a name="example"></a>示例

```HTTP
GET /v1.0/me HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiO ... 0X2tnSQLEANnSPHY0gKcgw
```

## <a name="saml-assertions-obtained-with-an-oauth20-obo-flow"></a>使用 OAuth2.0 OBO 流获得的 SAML 断言

某些基于 OAuth 的 Web 服务需要访问在非交互式流中接受 SAML 断言的其他 Web 服务 API。 Azure Active Directory 可以提供 SAML 断言，以响应将基于 SAML 的 Web 服务用作目标资源的代理流。

这是非标准的 OAuth 2.0 代理流扩展，它允许基于 OAuth2 的应用程序访问使用 SAML 令牌的 Web 服务 API 终结点。

> [!TIP]
> 当从前端 Web 应用程序调用受 SAML 保护的 Web 服务时，只需调用 API 并使用用户的现有会话启动正常的交互式身份验证流。 当服务到服务调用需要 SAML 令牌来提供用户上下文时，只需使用 OBO 流。
 
 ### <a name="obtain-a-saml-token-by-using-an-obo-request-with-a-shared-secret"></a>使用带共享密钥的 OBO 请求获取 SAML 令牌

SAML 断言的服务到服务请求包含以下参数：

| 参数 | 类型 | 说明 |
| --- | --- | --- |
| grant_type |必需 | 令牌请求的类型。 对于使用 JWT 的请求，该值必须是 urn:ietf:params:oauth:grant-type:jwt-bearer。 |
| assertion |必需 | 请求中使用的访问令牌值。|
| client_id |必需 | 在注册到 Azure AD 期间分配给调用服务的应用 ID。 要在 Azure 门户中查找应用 ID，请选择“Active Directory”，选择目录，然后选择应用程序名称。 |
| client_secret |必需 | 在 Azure AD 中为调用服务注册的密钥。 注册时应已记下此值。 |
| scope |必需 | 空格分隔的令牌请求范围的列表。 有关详细信息，请参阅[作用域](v2-permissions-and-consent.md)。 例如，“https://testapp.contoso.com/user_impersonation openid” |
| requested_token_use |必需 | 指定应如何处理请求。 在代理流中，该值必须是 **on_behalf_of**。 |
| requested_token_type | 必需 | 指定请求令牌的类型。 值可以是 urn:ietf:params:oauth:token-type:saml2 或 urn:ietf:params:oauth:token-type:saml1，具体取决于访问资源的要求 。 |

响应包含以 UTF8 和 Base64url 编码的 SAML 令牌。

- **源自 OBO 调用的 SAML 断言的 SubjectConfirmationData**：如果目标应用程序需要 **SubjectConfirmationData** 中的接收方值，则该值必须是资源应用程序配置中的非通配符回复 URL。
- **SubjectConfirmationData 节点**：此节点不能包含 **InResponseTo** 属性，因为它不是 SAML 响应的一部分。 接收 SAML 令牌的应用程序必须能够在没有 InResponseTo 属性的情况下接受 SAML 断言。

- **许可**：必须授予许可，才能接收包含 OAuth 流上用户数据的 SAML 令牌。 有关权限和获取管理员许可的信息，请参阅 [Azure Active Directory v1.0 终结点中的权限和许可](../azuread-dev/v1-permissions-consent.md)。

### <a name="response-with-saml-assertion"></a>使用 SAML 断言进行响应

| 参数 | 说明 |
| --- | --- |
| token_type |指示令牌类型值。 Azure AD 唯一支持的类型是 Bearer 。 有关持有者令牌的详细信息，请参阅 [OAuth 2.0 授权框架：持有者令牌用法 (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt)。 |
| scope |令牌中授予的访问权限的范围。 |
| expires_in |访问令牌有效的时间长度（以秒为单位）。 |
| expires_on |访问令牌的过期时间。 该日期表示为自 1970-01-01T0:0:0Z UTC 至过期时间的秒数。 此值用于确定缓存令牌的生存期。 |
| resource |接收服务（受保护资源）的应用 ID URI。 |
| access_token |返回 SAML 断言的参数。 |
| refresh_token |刷新令牌。 当前 SAML 断言过期后，调用方服务可以使用此令牌请求另一个访问令牌。 |

- token_type：持有者
- expires_in：3296
- ext_expires_in：0
- expires_on：1529627844
- 资源：`https://api.contoso.com`
- access_token：\<SAML assertion\>
- issued_token_type: urn:ietf:params:oauth:token-type:saml2
- refresh_token：\<Refresh token\>


## <a name="gaining-consent-for-the-middle-tier-application"></a>为中间层应用程序获得同意

根据应用程序的体系结构或使用情况，可以考虑使用不同的策略来确保 OBO 流的成功。 在所有情况下，最终目标都是确保获得相应许可，以便客户端应用可以调用中间层应用，并且中间层应用有权调用后端资源。

> [!NOTE]
> 以前，Microsoft 帐户系统（个人帐户）不支持“已知客户端应用程序”字段，也无法显示组合同意。  添加此项后，Microsoft 标识平台中的所有应用都可以使用已知客户端应用程序方法获取 OBO 调用的许可。

### <a name="default-and-combined-consent"></a>/.default 和组合同意

中间层应用程序将客户端添加到其清单中的已知客户端应用程序列表中，然后，客户端可以为自身和中间层应用程序触发组合同意流。 在 Microsoft 标识平台中，此功能是使用 [`/.default` 范围](v2-permissions-and-consent.md#the-default-scope)实现的。 当使用已知的客户端应用程序和 `/.default` 触发同意屏幕时，同意屏幕将显示客户端到中间层 API 的权限，同时还会请求中间层 API 所需的任何权限。 用户同意这两个应用程序，接着 OBO 流便开始工作。

### <a name="pre-authorized-applications"></a>预授权应用程序

资源可以指示给定应用程序始终具有接收某些范围的权限。 这主要用于使前端客户端和后端资源之间的连接更顺畅。 一个资源可以声明多个预授权应用程序 - 任何此类应用程序都可以在 OBO 流中请求这些权限，并在未经用户同意的情况下接收这些权限。

### <a name="admin-consent"></a>管理员同意

租户管理员可以通过为中间层应用程序提供管理员同意，保证应用程序有权调用其所需的 API。 为此，管理员可以在其租户中找到中间层应用程序，打开“所需的权限”页面，然后选择为应用授予权限。 若要详细了解管理员同意功能，请参阅[同意和权限文档](v2-permissions-and-consent.md)。

### <a name="use-of-a-single-application"></a>使用单一应用程序

在某些情况下，可能只有一对中间层和前端客户端。 在这种情况下，你可能会发现将其作为单一应用程序更轻松，完全无需使用中间层应用程序。 若要在前端和 Web API 之间进行身份验证，可以使用 cookie、id_token 或为应用程序本身请求的访问令牌。 然后，从此单一应用程序请求同意后端资源。

## <a name="client-limitations"></a>客户端限制

如果客户端使用隐式流来获取 id_token，且该客户端在回复 URL 中也具有通配符，则 id_token 不能用于 OBO 流。  但是，即使发起客户端已注册通配符回复 URL，通过隐式授予流获取的访问令牌仍可由机密客户端兑换。

## <a name="next-steps"></a>后续步骤

详细了解 OAuth 2.0 协议和使用客户端凭据执行服务到服务身份验证的其他方法。

* [Microsoft 标识平台中的 OAuth 2.0 客户端凭据授予](v2-oauth2-client-creds-grant-flow.md)
* [Microsoft 标识平台中的 OAuth 2.0 代码流](v2-oauth2-auth-code-flow.md)
* [使用 `/.default` 范围](v2-permissions-and-consent.md#the-default-scope)
