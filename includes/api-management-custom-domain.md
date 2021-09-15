---
author: vladvino
ms.service: api-management
ms.topic: include
ms.date: 08/23/2021
ms.author: vlvinogr
ms.openlocfilehash: 4f75dc57d6f94983b390d45de657029dd157cf64
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2021
ms.locfileid: "123484835"
---
## <a name="how-api-management-proxy-server-responds-with-ssl-certificates-in-the-tls-handshake"></a>API 管理代理服务器在 TLS 握手中如何通过 SSL 证书进行响应

### <a name="clients-calling-with-server-name-indication-sni-header"></a>调用时使用服务器名称指示 (SNI) 标头的客户端
如果为代理（网关）配置了一个或多个自定义域，则 API 管理可以响应来自以下两者的 HTTPS 请求：
* 自定义域（例如 `contoso.com`）
* 默认域（例如 `apim-service-name.azure-api.net`）。 

根据 SNI 标头中的信息，API 管理会使用相应的服务器证书进行响应。

### <a name="clients-calling-without-sni-header"></a>调用时不使用 SNI 标头的客户端
如果使用不发送 [SNI](https://tools.ietf.org/html/rfc6066#section-3) 标头的客户端，则 API 管理会根据以下逻辑创建响应：

* 如果服务仅为代理配置了一个自定义域，则默认证书是已颁发给代理自定义域的证书。
* 如果服务已为代理配置了多个自定义域（在开发人员和高级层中受支持），则可以通过将 [defaultSslBinding](/rest/api/apimanagement/2020-12-01/api-management-service/create-or-update#hostnameconfiguration) 属性设置 true ("defaultSslBinding":"true") 来指定默认证书 。 如果未设置该属性，则默认证书是颁发给 `*.azure-api.net` 上托管的默认代理域的证书。

## <a name="support-for-putpost-request-with-large-payload"></a>对包含大型有效负载的 PUT/POST 请求的支持

当在 HTTPS 中使用客户端证书时，API 管理代理服务器支持包含大型有效负载 (> 40 KB) 的请求。 若要防止服务器的请求被冻结，可以在代理主机名上设置属性 ["negotiateClientCertificate": "true"](/rest/api/apimanagement/2020-12-01/api-management-service/create-or-update#hostnameconfiguration)。 

如果该属性设置为 true，则在进行 SSL/TLS 连接时将在交换任何 HTTP 请求之前请求证书。 由于该设置是在 **代理主机名** 级别应用的，因此，所有连接请求都会请求客户端证书。 可以避开此限制并为代理配置最多 20 个自定义域（只有高级层才支持）。
