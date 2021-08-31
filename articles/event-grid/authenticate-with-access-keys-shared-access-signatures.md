---
title: 使用访问密钥或共享访问签名对 Azure 事件网格发布客户端进行身份验证
description: 本文介绍如何使用访问密钥和共享访问签名对 Azure 事件网格发布客户端进行身份验证。
ms.topic: conceptual
ms.date: 08/10/2021
ms.openlocfilehash: 5a271372afcc36df97d0a7afa37ee0b065a5a5e6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778350"
---
# <a name="authenticate-azure-event-grid-publishing-clients-using-access-keys-or-shared-access-signatures"></a>使用访问密钥或共享访问签名对 Azure 事件网格发布客户端进行身份验证
本文介绍使用访问密钥或共享访问签名 (SAS) 令牌对那些将事件发布到 Azure 事件网格主题、域、合作伙伴命名空间的客户端进行身份验证。 

> [!IMPORTANT]
> 与基于密钥的共享访问签名 (SAS) 身份验证方式相比，使用 Azure AD 标识对用户或应用程序进行身份验证和授权提供了更高的安全性和易用性。 使用 Azure AD，不需要在代码中存储用于身份验证的机密，也不需要冒出现潜在安全漏洞的风险。 强烈建议将 Azure AD 与 Azure 事件网格事件发布应用程序配合使用。 有关详细信息，请参阅[使用 Azure Active Directory 对发布客户端进行身份验证](authenticate-with-active-directory.md)。

## <a name="authenticate-using-access-key"></a>使用访问密钥进行身份验证
访问密钥身份验证是最简单的身份验证形式。 可以将访问密钥作为 HTTP 标头或 URL 查询参数传递。 

### <a name="access-key-in-an-http-header"></a>HTTP 标头中的访问密钥
将访问密钥作为 HTTP 标头的值传递：`aeg-sas-key`。

```
aeg-sas-key: XXXXXXXXXXXXXXXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="access-key-as-a-query-parameter"></a>用作查询参数的访问密钥
还可以将 `aeg-sas-key` 指定为查询参数。 

```
https://<yourtopic>.<region>.eventgrid.azure.net/api/events?aeg-sas-key=XXXXXXXX53249XX8XXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

有关如何获取主题或域的访问密钥的说明，请参阅[获取访问密钥](get-access-keys.md)。

## <a name="authenticate-using-sas"></a>使用 SAS 进行身份验证 
事件网格资源的 SAS 令牌包括资源、过期时间和签名。 SAS 令牌的格式是：`r={resource}&e={expiration}&s={signature}`。

资源是要将事件发送到的事件网格主题的路径。 例如，有效的资源路径是 `https://<yourtopic>.<region>.eventgrid.azure.net/api/events`。 若要查看所有受支持的 API 版本，请参阅 [Microsoft.EventGrid 资源类型](/azure/templates/microsoft.eventgrid/allversions)。 

首先，以编程方式生成 SAS 令牌，然后使用 `aeg-sas-token` 标头或 `Authorization SharedAccessSignature` 标头向事件网格进行身份验证。 

### <a name="generate-sas-token-programmatically"></a>以编程方式生成 SAS 令牌
以下示例会创建用于事件网格的 SAS 令牌：

```cs
static string BuildSharedAccessSignature(string resource, DateTime expirationUtc, string key)
{
    const char Resource = 'r';
    const char Expiration = 'e';
    const char Signature = 's';

    string encodedResource = HttpUtility.UrlEncode(resource);
    var culture = CultureInfo.CreateSpecificCulture("en-US");
    var encodedExpirationUtc = HttpUtility.UrlEncode(expirationUtc.ToString(culture));

    string unsignedSas = $"{Resource}={encodedResource}&{Expiration}={encodedExpirationUtc}";
    using (var hmac = new HMACSHA256(Convert.FromBase64String(key)))
    {
        string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(unsignedSas)));
        string encodedSignature = HttpUtility.UrlEncode(signature);
        string signedSas = $"{unsignedSas}&{Signature}={encodedSignature}";

        return signedSas;
    }
}
```

```python
def generate_sas_token(uri, key, expiry=3600):
    ttl = datetime.datetime.utcnow() + datetime.timedelta(seconds=expiry)
    encoded_resource = urllib.parse.quote_plus(uri)
    encoded_expiration_utc = urllib.parse.quote_plus(ttl.isoformat())

    unsigned_sas = f'r={encoded_resource}&e={encoded_expiration_utc}'
    signature = b64encode(HMAC(b64decode(key), unsigned_sas.encode('utf-8'), sha256).digest())
    encoded_signature = urllib.parse.quote_plus(signature)
    
    token = f'r={encoded_resource}&e={encoded_expiration_utc}&s={encoded_signature}'

    return token
```

### <a name="using-aeg-sas-token-header"></a>使用 aeg-sas-token 标头
以下是将 SAS 令牌作为 `aeg-sas-token` 标头的值传递的示例。 

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2fapi%2fevents&e=6%2f15%2f2017+6%3a20%3a15+PM&s=XXXXXXXXXXXXX%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

### <a name="using-authorization-header"></a>使用 Authorization 标头
以下是将 SAS 令牌作为 `Authorization` 标头的值传递的示例。 

```http
Authorization: SharedAccessSignature r=https%3a%2f%2fmytopic.eventgrid.azure.net%2fapi%2fevents&e=6%2f15%2f2017+6%3a20%3a15+PM&s=XXXXXXXXXXXXX%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

## <a name="next-steps"></a>后续步骤
请参阅[事件传递身份验证](security-authentication.md)，了解如何使用事件处理程序进行身份验证以传递事件。 
