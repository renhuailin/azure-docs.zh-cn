---
title: 颁发者服务通信示例（预览版）- Azure Active Directory 可验证凭据
description: 标识提供者和颁发者服务之间的通信的详细信息
author: barclayn
manager: davba
ms.service: identity
ms.subservice: verifiable-credentials
ms.workload: identity
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 6aa502e1ed0e49192220174d5a8573690035a4a3
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739127"
---
# <a name="issuer-service-communication-examples-preview"></a>颁发者服务通信示例（预览版）

Azure AD 可验证凭据服务可以通过从组织的 OpenID 合规标识提供者生成的 ID 令牌中检索声明，来颁发可验证凭据。 本文将会说明如何设置标识提供者，使 Authenticator 能够与它通信，并检索要传递给颁发服务的正确 ID 令牌。 

> [!IMPORTANT]
> Azure Active Directory 可验证凭据目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。


若要颁发可验的凭据，可指示 Authenticator 下载协定，以从用户收集输入并将该信息发送到颁发服务。 如果需要使用 ID 令牌，必须将标识提供者设置为允许 Authenticator 使用 OpenID Connect 协议将用户登录。 生成的 ID 令牌中的声明用于填充可验证凭据的内容。 Authenticator 使用 OpenID Connect 授权代码流对用户进行身份验证。 OpenID 提供程序必须支持以下 OpenID Connect 功能： 

| 功能 | 说明 |
| ------- | ----------- |
| 授权类型 | 必须支持授权代码授权类型。 |
| 令牌格式 | 必须生成不加密的精简 JWT。 |
| 签名算法 | 必须生成使用 RS 256 签名的 JWT。 |
| 配置文档 | 必须支持 OpenID Connect 配置文档和 `jwks_uri`。 | 
| 客户端注册 | 必须支持使用 `redirect_uri` 值为 `vcclient://openid/` 的公共客户端注册。 | 
| PKCE | 出于安全原因建议使用，但并非必须使用。 |

下面提供了发送到标识提供者的 HTTP 请求示例。 标识提供者必须根据 OpenID Connect 身份验证标准接受并响应这些请求。

## <a name="client-registration"></a>客户端注册

若要接收可验证凭据，你的用户需要从 Microsoft Authenticator 应用登录到你的 IDP。 

若要启用这种交换，请将应用程序注册到标识提供者。 如果使用 Azure AD，可在[此处](../develop/quickstart-register-app.md)找到相关说明。 注册时请使用以下值。

| 设置 | 值 |
| ------- | ----- |
| 应用程序名称 | `<Issuer Name> Verifiable Credential Service` |
| 重定向 URI | `vcclient://openid/ ` |


将应用程序注册到标识提供者之后，请记下其客户端 ID。 在后面的部分中将要用到它。 此外，还需要记下 OIDC 兼容标识提供者的已知终结点的 URL。 Authenticator 发送 ID 令牌后，颁发服务将使用此终结点下载所需的公钥用于验证该令牌。

配置的重定向 URI 将由 Authenticator 使用，以便知道登录何时已完成，并可以检索 ID 令牌。 

## <a name="authorization-request"></a>授权请求

发送到标识提供者的授权请求使用以下格式。

```HTTP
GET /authorize?client_id=<client-id>&redirect_uri=portableidentity%3A%2F%2Fverify&response_mode=query&response_type=code&scope=openid&state=12345&nonce=12345 HTTP/1.1
Host: www.contoso.com
Connection: Keep-Alive
```

| 参数 | 值 |
| ------- | ----------- |
| `client_id` | 在应用程序注册过程中获取的客户端 ID。 |
| `redirect_uri` | 必须使用 `vcclient://openid/`。 |
| `response_mode` | 必须支持 `query`。 |
| `response_type` | 必须支持 `code`。 |
| `scope` | 必须支持 `openid`。 |
| `state` | 必须根据 OpenID Connect 标准返回到客户端。 |
| `nonce` | 必须根据 OpenID Connect 标准以声明形式在 ID 令牌中返回。 |

收到授权请求时，标识提供者应该对用户进行身份验证，并执行完成登录所需的任何步骤（例如多重身份验证）。

可以根据需要自定义登录过程。 可以要求用户提供更多信息、接受服务条款、支付凭据费用，等等。 完成所有步骤后，通过重定向到重定向 URI 来响应授权请求，如下所示。 

```HTTP
vcclient://openid/?code=nbafhjbh1ub1yhbj1h4jr1&state=12345
```

| 参数 | 值 |
| ------- | ----------- |
| `code` |  标识提供者返回的授权代码。 |
| `state` | 必须根据 OpenID Connect 标准返回到客户端。 |

## <a name="token-request"></a>令牌请求

发送到标识提供者的令牌请求采用以下格式。

```HTTP
POST /token HTTP/1.1
Host: www.contoso.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 291

client_id=<client-id>&redirect_uri=vcclient%3A%2F%2Fopenid%2F&grant_type=authorization_code&code=nbafhjbh1ub1yhbj1h4jr1&scope=openid
```

| 参数 | 值 |
| ------- | ----------- |
| `client_id` | 在应用程序注册过程中获取的客户端 ID。 |
| `redirect_uri` | 必须使用 `vcclient://openid/`。 |
| `scope` | 必须支持 `openid`。 |
| `grant_type` | 必须支持 `authorization_code`。 |
| `code` | 标识提供者返回的授权代码。 |

收到令牌请求后，标识提供者应使用 ID 令牌做出响应。

```HTTP
HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: no-store
Pragma: no-cache

{
"id_token": "eyJhbGciOiJSUzI1NiIsImtpZCI6IjFlOWdkazcifQ.ewogImlzc
    yI6ICJodHRwOi8vc2VydmVyLmV4YW1wbGUuY29tIiwKICJzdWIiOiAiMjQ4Mjg5
    NzYxMDAxIiwKICJhdWQiOiAiczZCaGRSa3F0MyIsCiAibm9uY2UiOiAibi0wUzZ
    fV3pBMk1qIiwKICJleHAiOiAxMzExMjgxOTcwLAogImlhdCI6IDEzMTEyODA5Nz
    AKfQ.ggW8hZ1EuVLuxNuuIJKX_V8a_OMXzR0EHR9R6jgdqrOOF4daGU96Sr_P6q
    Jp6IcmD3HP99Obi1PRs-cwh3LO-p146waJ8IhehcwL7F09JdijmBqkvPeB2T9CJ
    NqeGpe-gccMg4vfKjkM8FcGvnzZUN4_KSP0aAp1tOJ1zZwgjxqGByKHiOtX7Tpd
    QyHE5lcMiKPXfEIQILVq0pc_E2DzL7emopWoaoZTF_m0_N0YzFC6g6EJbOEoRoS
    K5hoDalrcvRYLSrQAZZKflyuVCyixEoV9GfNQC3_osjzw2PAithfubEEBLuVVk4
    XUVrWOLrLl0nx7RkKU8NXNHq-rvKMzqg"
}
```

ID 令牌必须使用 JWT 精简序列化格式，且不得加密。 ID 令牌应包含以下声明。

| 声明 | 值 |
| ------- | ----------- |
| `kid` | 用于对 ID 令牌进行签名的密钥的密钥标识符，对应于 OpenID 提供程序中的 `jwks_uri` 中的条目。 |
| `aud` | 在应用程序注册过程中获取的客户端 ID。 |
| `iss` | 必须是 OpenID Connect 配置文档中的 `issuer` 值。 |
| `exp` | 必须包含 ID 令牌的过期时间。 |
| `iat` | 必须包含 ID 令牌的颁发时间。 |
| `nonce` | 授权请求中包含的值。 |
| 其他声明 | ID 令牌应包含任何附加声明，这些声明的值将包含在要颁发的可验证凭据中。 应在此部分包含有关用户的任何特性，例如用户的姓名。 |

## <a name="next-steps"></a>后续步骤

- [如何自定义 Azure Active Directory 可验证凭据](credential-design.md)
