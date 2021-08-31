---
title: Azure Front Door 中 HTTP 标头的协议支持 | Microsoft Docs
description: 本文介绍 Front Door 支持的 HTTP 标头协议。
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/10/2021
ms.author: duau
ms.openlocfilehash: 807138187e37deef6f23121ce085e62f520ad335
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121736665"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door"></a>Azure Front Door 中 HTTP 标头的协议支持
本文概述了 Front Door 支持部分调用路径（见下图）所用的协议。 以下各节提供了有关 Front Door 支持的 HTTP 标头的详细信息。

:::image type="content" source="./media/front-door-http-headers-protocol/front-door-protocol-summary.png" alt-text="Azure Front Door HTTP 标头协议":::

>[!IMPORTANT]
>Front Door 不认证此处未记录的任何 HTTP 标头。

## <a name="client-to-front-door"></a>客户端到 Front Door
Front Door 接受传入请求的大多数标头而不进行修改。 传入请求发送后，某些保留标头将从传入请求中删除，包括带有 X-FD-* 前缀的标头。

## <a name="front-door-to-backend"></a>Front Door 到后端

Front Door 包含传入请求的标头，除非由于限制而被删除。 Front Door 还添加以下标头：

| Header  | 示例和说明 |
| ------------- | ------------- |
| Via |  *Via：1.1 Azure* </br> Front Door 添加客户端的 HTTP 版本，后跟“Azure”作为 Via 标头的值。 此标头指示客户端的 HTTP 版本，并表示 Front Door 是客户端与后端之间的请求的中间接收方。  |
| X-Azure-ClientIP | *X-Azure-ClientIP：127.0.0.1* </br> 表示与正在处理的请求关联的客户端 IP 地址。 例如，来自代理的请求可能会添加 X-Forwarded-For 标头，以指示原始调用方的 IP 地址。 |
| X-Azure-SocketIP |  *X-Azure-SocketIP：127.0.0.1* </br> 表示与当前请求的源 TCP 连接相关联的套接字 IP 地址。 请求的客户端 IP 地址可能不等于其套接字 IP 地址，因为用户可以随意覆盖客户端 IP。|
| X-Azure-Ref | *X-Azure-Ref：0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> 这是唯一的引用字符串，用于识别 Front Door 处理的请求。 字符串用于搜索访问日志，是故障排除的关键。|
| X-Azure-RequestChain | *X-RequestChain：跃点 = 1* </br> Front Door 用于检测请求循环的标头，用户不应该依赖它。 |
| X-Azure-FDID | *X-Azure-FDID：55ce4ed1-4b06-4bf1-b40e-4638452104da* <br/> 识别来自特定 Front Door 资源的请求的引用字符串。 可以在 Azure 门户中查看此值，也可以使用管理 API 检索该值。 可以将此标头与 IP ACL 结合使用，将终结点锁定为仅接受来自特定 Front Door 资源的请求。 [有关更多详细信息](front-door-faq.yml#how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door-)，请参阅常见问题 |
| X-Forwarded-For | *X-Forwarded-For：127.0.0.1* </br> X-Forwarded-For (XFF) HTTP 标头字段通常标识通过 HTTP 代理或负载均衡器连接到 web 服务器的客户端的原始 IP 地址。 如果存在现有的 XFF 标头，则 Front Door 会将客户端套接字 IP 追加到该标头，或使用客户端套接字 IP 添加 XFF 标头。 |
| X-Forwarded-Host | *X-Forwarded-Host：contoso.azurefd.net* </br> X-Forwarded-Host HTTP 标头字段是一种常用方法，用于标识主机 HTTP 请求标头中的客户端请求的原始主机。 这是因为 Front Door 的主机名可能不同于处理请求的后端服务器。 任何以前的值都将由 Front Door 覆盖。 |
| X-Forwarded-Proto | *X-Forwarded-Proto：http* </br> X-Forwarded-Proto HTTP 标头字段通常用于标识 HTTP 请求的原始协议。 根据配置，Front Door 可能使用 HTTPS 与后端通信。 即使发送到反向代理的请求为 HTTP 也是如此。 任何以前的值都将由 Front Door 覆盖。 |
| X-FD-HealthProbe | X-FD-HealthProbe HTTP 标头字段用于标识来自 Front Door 的运行状况探测。 如果此标头设置为 1，则请求源自运行状况探测。 它可用于限制来自 X-Forwarded-Host 标头字段为特定值的 Front Door 的访问。 |
| X-Azure-FDID | *X-Azure-FDID 标头：437c82cd-360a-4a54-94c3-5ff707647783* </br> 此字段包含 frontdoorID，可用于标识传入请求来自哪个 Front Door。 此字段由 Front Door 服务填充。 | 

## <a name="front-door-to-client"></a>Front Door 到客户端

此外，从后端发送到 Front Door 的所有标头也传递给客户端。 下面是从 Front Door 发送到客户端的标头。

| Header  | 示例和说明 |
| ------------- | ------------- |
| X-Azure-Ref |  *X-Azure-Ref：0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> 这是标识 Front Door 处理请求的唯一引用字符串，这对于故障排除至关重要，因为将被用于搜索访问日志。|
| X-Cache | X 缓存：此标头描述请求的缓存状态 <br/> - X-Cache: TCP_HIT：请求的第一个字节是 Front Door 边缘中的缓存命中。 <br/> - X-Cache: TCP_REMOTE_HIT：请求的第一个字节是区域缓存中的缓存命中（源防护层），但边缘缓存中未命中。 <br/> - X-Cache: TCP_MISS：请求的第一个字节是缓存未命中，内容从源端提供。 <br/> - X-Cache: PRIVATE_NOSTORE：无法缓存请求，因为缓存控制响应头设置为“专用”或“无存储”。 <br/> - X-Cache: CONFIG_NOCACHE：请求配置为不在 Front Door 配置文件中缓存。 |

需要发送“X-Azure-DebugInfo：1”请求标头，以启用以下可选的响应标头。

| Header  | 示例和说明 |
| ------------- | ------------- |
| X-Azure-OriginStatusCode |  *X-Azure-OriginStatusCode：503* </br> 此标头包含后端返回的 HTTP 状态代码。 使用此标头，可以识别后端中运行的应用程序返回的 HTTP 状态代码，而无需查看后端日志。 此状态代码可能不同于由 Front Door 发送到客户端的响应中的 HTTP 状态代码。 此标头可用于确定后端是否有问题，或者问题是否出在 Front Door 服务中。 |
| X-Azure-InternalError | 此标头包含 Front Door 处理请求时遇到的错误代码。 此错误表明问题存在于 Front Door 服务/基础结构内部。 向客户支持报告问题。  |
| X-Azure-ExternalError | *X-Azure-ExternalError：0x830c1011，不熟悉证书颁发机构。* </br> 此标头显示 Front Door 服务器在与后端服务器建立连接以处理请求时遇到的错误代码。 此标头有助于识别 Front Door 与后端应用程序之间的连接问题。 此标头将包括详细的错误消息，以帮助你确定后端的连接问题（例如 DNS 解析、无效证书等）。 |

## <a name="next-steps"></a>后续步骤

- [创建 Front Door](quickstart-create-front-door.md)
- [Front Door 的工作原理](front-door-routing-architecture.md)
