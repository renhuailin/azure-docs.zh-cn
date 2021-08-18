---
title: Microsoft 标识平台证书凭据
titleSuffix: Microsoft identity platform
description: 本文讨论注册和使用证书凭据进行应用程序身份验证。
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 06/23/2021
ms.author: hirsin
ms.reviewer: nacanuma, jmprieur
ms.custom: contperf-fy21q4, aaddev
ms.openlocfilehash: ed3495bb7267c54f9b95f7fc3465d76ddde2faaa
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2021
ms.locfileid: "112581882"
---
# <a name="microsoft-identity-platform-application-authentication-certificate-credentials"></a>Microsoft 标识平台应用程序身份验证证书凭据

Microsoft 标识平台允许应用程序在任何可以使用客户端机密的地方使用其自己的凭据进行身份验证，例如，在 OAuth 2.0 [客户端凭据授权](v2-oauth2-client-creds-grant-flow.md)流和[代理](v2-oauth2-on-behalf-of-flow.md) (OBO) 流中。

应用程序可用于身份验证的一种凭据形式是使用应用程序拥有的证书签名的 [JSON Web 令牌](./security-tokens.md#json-web-tokens-and-claims) (JWT) 断言。

## <a name="assertion-format"></a>断言格式

若要计算断言，可以使用所选语言的多个 JWT 库之一 - [MSAL 使用 `.WithCertificate()`](msal-net-client-assertions.md) 对此提供支持。 令牌在其标头、声明和签名中携带相关信息。

### <a name="header"></a>标头

| 参数 |  备注 |
| --- | --- |
| `alg` | 应为 **RS256** |
| `typ` | 应为 **JWT** |
| `x5t` | 编码为 Base64url 字符串值的 x.509 证书哈希（也称为证书的 SHA-1 指纹）的十六进制表示形式。 例如，如果 x.509 证书哈希为 `84E05C1D98BCE3A5421D225B140B36E86A3D5534`（十六进制），`x5t` 声明就会是 `hOBcHZi846VCHSJbFAs26Go9VTQ=` (Base64url)。 |

### <a name="claims-payload"></a>声明（有效负载）

声明类型 | 值 | 说明
---------- | ---------- | ----------
aud | `https://login.microsoftonline.com/{tenantId}/v2.0` | “aud”（受众）声明标识 JWT 预期的收件人（在这里为 Azure AD）。请参阅 [RFC 7519 的 4.1.3 节](https://tools.ietf.org/html/rfc7519#section-4.1.3)。  在本例中，该收件人为登录服务器 (login.microsoftonline.com)。
exp | 1601519414 | “exp”（过期时间）声明指定只能在哪个时间（含）之前接受 JWT 的处理。 请参阅 [RFC 7519 的 4.1.4 节](https://tools.ietf.org/html/rfc7519#section-4.1.4)。  这样就可以在这之前一直使用断言，所以时间要短 - 最多在 `nbf` 之后 5 - 10 分钟。  Azure AD 当前未对 `exp` 时间设置限制。 
iss | {ClientID} | “iss”（颁发者）声明标识颁发了 JWT 的主体，在本例中是你的客户端应用程序。  使用 GUID 应用程序 ID。
jti | （一个 GUID） | “jti”(JWT ID) 声明为 JWT 提供唯一标识符。 分配标识符值时，所用方式必须确保几乎不可能将同一值意外分配给不同的数据对象；如果应用程序使用多个颁发者，还必须防止在不同的颁发者生成的值之间发生冲突。 “jti”值是一个区分大小写的字符串。 [RFC 7519 的 4.1.7 节](https://tools.ietf.org/html/rfc7519#section-4.1.7)
nbf | 1601519114 | “nbf”（不早于）声明指定只能在哪个时间之后接受 JWT 的处理。 [RFC 7519 的 4.1.5 节](https://tools.ietf.org/html/rfc7519#section-4.1.5)。  使用当前时间是合适的。 
sub | {ClientID} | “sub”（使用者）声明标识 JWT 的使用者，在本例中也是你的应用程序。 使用与 `iss` 相同的值。 

### <a name="signature"></a>签名

签名是通过应用证书计算出来的，如 [JSON Web 令牌 RFC7519 规范](https://tools.ietf.org/html/rfc7519)所述。

## <a name="example-of-a-decoded-jwt-assertion"></a>已解码的 JWT 断言示例

```JSON
{
  "alg": "RS256",
  "typ": "JWT",
  "x5t": "gx8tGysyjcRqKjFPnd7RFwvwZI0"
}
.
{
  "aud": "https: //login.microsoftonline.com/contoso.onmicrosoft.com/oauth2/token",
  "exp": 1484593341,
  "iss": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05",
  "jti": "22b3bb26-e046-42df-9c96-65dbd72c1c81",
  "nbf": 1484592741,
  "sub": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05"
}
.
"Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="example-of-an-encoded-jwt-assertion"></a>已编码的 JWT 断言示例

以下字符串是已编码的断言的示例。 如果你仔细查看，会注意到由句点 (`.`) 分隔的三个部分：

* 第一部分对标头编码
* 第二部分对声明（有效负载）编码
* 最后一部分是使用前两部分内容中的证书计算出来的签名

```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="register-your-certificate-with-microsoft-identity-platform"></a>向 Microsoft 标识平台注册证书

可以使用以下任意方法通过 Azure 门户将证书凭据与 Microsoft 标识平台中的客户端应用程序相关联：

### <a name="uploading-the-certificate-file"></a>上传证书文件

在客户端应用程序的 Azure 应用注册中：
1. 选择“证书和机密”。
2. 单击“上传证书”，然后选择要上传的证书文件。
3. 单击“添加”。
  上传证书后，将显示指纹、开始日期和到期日期值。

### <a name="updating-the-application-manifest"></a>更新应用程序清单

获取证书后，计算以下值：

- `$base64Thumbprint` - 证书哈希的 Base64 编码值
- `$base64Value` - 证书原始数据的 Base64 编码值

请提供 GUID 以标识应用程序清单中的密钥 (`$keyId`)。

在客户端应用程序的 Azure 应用注册中：
1. 选择“清单”以打开应用程序清单。
2. 使用以下架构将 *keyCredentials* 属性替换为新的证书信息。

   ```JSON
   "keyCredentials": [
       {
           "customKeyIdentifier": "$base64Thumbprint",
           "keyId": "$keyid",
           "type": "AsymmetricX509Cert",
           "usage": "Verify",
           "value":  "$base64Value"
       }
   ]
   ```
3. 将所做的编辑保存到应用程序清单，然后将清单上传到 Microsoft 标识平台。

   `keyCredentials` 属性具有多个值，因此可上传多个证书实现更丰富的密钥管理。
   
## <a name="using-a-client-assertion"></a>使用客户端断言

客户端断言可以在任何使用客户端机密的地方使用。  例如，在[授权代码流](v2-oauth2-auth-code-flow.md)中，你可以传入一个 `client_secret` 来证明请求来自你的应用。 可以用 `client_assertion` 和 `client_assertion_type` 参数替换它。 

| 参数 | 值 | 说明|
|-----------|-------|------------|
|`client_assertion_type`|`urn:ietf:params:oauth:client-assertion-type:jwt-bearer`| 这是一个固定值，表示你正在使用证书凭据。 |
|`client_assertion`| JWT |这是上面创建的 JWT。 |

## <a name="next-steps"></a>后续步骤

[MSAL.NET 库用单行代码处理这种情况](msal-net-client-assertions.md)。

GitHub 上的[使用 Microsoft 标识平台的 .NET Core 守护程序控制台应用程序](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2)代码示例展示了应用程序如何使用自己的凭据进行身份验证。 它还展示了如何使用 `New-SelfSignedCertificate` PowerShell cmdlet [创建自签名证书](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/1-Call-MSGraph#optional-use-the-automation-script)。 你还可以使用示例存储库中的[应用创建脚本](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/AppCreationScripts-withCert/AppCreationScripts.md)来创建证书、计算指纹，以及进行其他操作。
