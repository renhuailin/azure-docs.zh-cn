---
title: Azure 应用配置 REST API - 标头
description: 与 Azure 应用配置 REST API 配合使用的标头的参考页
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 80b20b0b55219766872166685c0b1257f3c39c55
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932568"
---
# <a name="headers"></a>标头

本文提供了与 Azure 应用配置 REST API 配合使用的标头的参考页链接。

## <a name="request-headers"></a>请求标头

下表介绍了 Azure 应用配置中使用的常见请求标头。

| 标头 | 说明 | 示例 |
|--|--|--|
| **授权** | 用于对服务请求进行[身份验证](./rest-api-authentication-index.md)。 请参阅[第 14.8 节](https://tools.ietf.org/html/rfc2616#section-14.8) | `Authorization: HMAC-SHA256 Credential=<Credential>&SignedHeaders=Host;x-ms-date;x-ms-content-sha256&Signature=<Signature>` |
| **接受** | 向服务器通知客户端将在 HTTP 响应中接受哪一媒体类型。 请参阅[第 14.1 节](https://tools.ietf.org/html/rfc2616#section-14.1) | `Accept: application/vnd.microsoft.appconfig.kv+json;` |
| **Accept-Datetime** | 请求服务器将其内容作为其之前状态的表示形式返回。 此标头的值是该状态的请求日期/时间。 请参阅 [RFC 7089](https://tools.ietf.org/html/rfc7089#section-2.1.1) | `Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT` |
| **Content-Type** | 包含 HTTP 请求正文中内容的媒体类型。 请参阅[第 14.17 节](https://tools.ietf.org/html/rfc2616#section-14.17) | `Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8;` |
| **日期** | 发出 HTTP 请求的日期/时间。 此标头用于 [HMAC 身份验证](./rest-api-authentication-hmac.md)。 请参阅[第 14.18 节](https://tools.ietf.org/html/rfc2616#section-14.18) | `Date: Fri, 11 May 2018 18:48:36 GMT` |
| **主机** | 指定已为其发出请求的租户。 此标头用于 [HMAC 身份验证](./rest-api-authentication-hmac.md)。 请参阅[第 14.23 节](https://tools.ietf.org/html/rfc2616#section-14.23) | `Host: contoso.azconfig.io` |
| **If-Match** | 用于使 HTTP 请求成为有条件的请求。 仅当目标资源的 ETag 与此标头的值匹配时，该请求才会成功。 “*”值匹配任何 ETag。 请参阅[第 14.24 节](https://tools.ietf.org/html/rfc2616#section-14.24) | `If-Match: "4f6dd610dd5e4deebc7fbaef685fb903"` |
| **If-None-Match** | 用于使 HTTP 请求成为有条件的请求。 仅当目标资源的 ETag 与此标头的值不匹配时，该请求才会成功。 “*”值匹配任何 ETag。 请参阅[第 14.26 节](https://tools.ietf.org/html/rfc2616#section-14.26) | `If-None-Match: "4f6dd610dd5e4deebc7fbaef685fb903"` |
| **Sync-Token** | 用于在处理一系列的请求期间实现实时一致性。 | `Sync-Token: jtqGc1I4=MDoyOA==;sn=28` |
| **x-ms-client-request-id** | 客户端提供的唯一 ID，用于跟踪请求的往返。 | `x-ms-client-request-id: 00000000-0000-0000-0000-000000000000` |
| **x-ms-content-sha256** | HTTP 请求正文的 sha256 摘要。 此标头用于 [HMAC 身份验证](./rest-api-authentication-hmac.md)。 | `x-ms-content-sha256: 47DEQpj8HBSa+/TImW+5JCeuQeRkm5NMpJWZG3hSuFU=` |
| **x-ms-date** | 如果 Date 标头不可供访问，则可以设置此 x-ms-date 标头，并将其用于替代 `Date` 标头。 此标头用于 [HMAC 身份验证](./rest-api-authentication-hmac.md)。 | `x-ms-date: Fri, 11 May 2018 18:48:36 GMT` |
| **x-ms-return-client-request-id** | 与 `x-ms-client-request-id` 标头结合使用。 如果此标头的值为“true”，则服务器将被指示返回 `x-ms-client-request-id` 请求标头的值。 | `x-ms-return-client-request-id: true` |

## <a name="response-headers"></a>响应标头

服务器可能会将以下 HTTP 标头包含在其响应中。

| 标头 | 说明 | 示例 |
|--|--|--|
| **Content-Type** | 包含 HTTP 响应正文中内容的媒体类型。 请参阅[第 14.17 节](https://tools.ietf.org/html/rfc2616#section-14.17) | `Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8;` |
| **ETag** | 一个不透明的标记，表示给定资源的状态。 可用于条件运算。 请参阅[第 14.19 节](https://tools.ietf.org/html/rfc2616#section-14.19) | `ETag: "4f6dd610dd5e4deebc7fbaef685fb903"` |
| **Last-Modified** | 描述上次修改所请求资源的时间。 已格式化为 [HTTP-Date](https://tools.ietf.org/html/rfc2616#section-3.3.1)。 请参阅[第 14.29 节](https://tools.ietf.org/html/rfc2616#section-14.29) | `Last-Modified: Tue, 05 Dec 2017 02:41:26 GMT` |
| **链接** | 提供与响应相关的资源的链接。 此标头用于使用“next”链接进行分页。 请参阅 [RFC 5988](https://tools.ietf.org/html/rfc5988) | `Link: </kv?after={token}>; rel="next"` |
| **Memento-Datetime** | 指示响应中包含的内容表示以前的状态。 此标头的值是该状态的日期/时间。 请参阅 [RFC 7089](https://tools.ietf.org/html/rfc7089#section-2.1.1) | `Memento-Datetime: Sat, 12 May 2018 02:10:00 GMT` |
| **retry-after-ms** | 提供了建议该客户端在重试失败的请求前等待的时间段（以毫秒为单位）。 | `retry-after-ms: 10` |
| **x-ms-request-id** | 服务器生成的唯一 ID，用于在服务中跟踪请求。 | `x-ms-request-id: 00000000-0000-0000-0000-000000000000` |
| **WWW-Authenticate** | 用于质询客户端以进行身份验证，并提供有关身份验证尝试失败的原因。 请参阅[第 14.47 节](https://tools.ietf.org/html/rfc2616#section-14.47) | `WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="Invalid Signature"` |
