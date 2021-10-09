---
title: 排查 Azure Front Door Standard/Premium 配置问题
description: 在本教程中，你将了解如何自我排查在使用 Azure Front Door Standard/Premium 实例时可能会遇到的一些常见问题。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 09/08/2021
ms.author: duau
ms.openlocfilehash: b49d7d051b099c47fa6bfe65ed8c0ee8f4b03872
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124792335"
---
# <a name="troubleshooting-common-routing-problems-with-azure-front-door-standardpremium"></a>排查 Azure Front Door Standard/Premium 的常见路由问题

本文介绍如何排查在使用 Azure Front Door 配置时可能会遇到的常见路由问题。

## <a name="503-response-from-azure-front-door-after-a-few-seconds"></a>数秒后接收到来自 Azure Front Door 的 503 响应

### <a name="symptom"></a>症状

* 未通过 Azure Front Door 发送到后端的常规请求会成功。 通过 Azure Front Door 发送则出现 503 错误响应。
* 此 Azure Front Door 故障通常会在大约 30 秒后出现。
* 日志 `ErrorInfo: OriginInvalidResponse` 出现间歇性 503 错误。

### <a name="cause"></a>原因

此问题的原因可能是以下三种情况之一：
 
* 源接收来自 Azure Front Door 的请求所用的时间超过配置的超时（默认值为 30 秒）。
* 它发送对 Azure Front Door 请求的响应所用的时间超过了超时值。
* 客户端使用 `Accept-Encoding header` 发送了字节范围请求（压缩启用）。

### <a name="troubleshooting-steps"></a>疑难解答步骤

* 直接向后端发送请求（不通过 Azure Front Door）。 看后端通常需要多久才能进行响应。
* 通过 Azure Front Door 发送请求，看是否会收到任何 503 响应。 如果没有收到任何 503 响应，则问题可能与超时无关。 请联系支持人员。
* 如果通过 Azure Front Door 发送的请求导致 503 错误响应代码，请为终结点配置“源响应超时(秒)”。 可以将默认超时延长到 4 分钟（240 秒）。 可以通过转到“Endpoint Manager”并选择“编辑终结点”对此设置进行配置。

    :::image type="content" source="..\media\troubleshoot-route-issues\origin-response-timeout-1.png" alt-text="从 Endpoint Manager 中选择“编辑终结点”的屏幕截图。":::

    然后选择“终结点属性”以配置“源响应超时”：

    :::image type="content" source="..\media\troubleshoot-route-issues\origin-response-timeout-2.png" alt-text="选择“终结点属性”和“源响应超时”字段的屏幕截图。" lightbox="..\media\troubleshoot-route-issues\origin-response-timeout-2-expanded.png":::

* 如果超时无法解决问题，请使用 Fiddler 或浏览器开发人员工具等工具来检查客户端是否正在发送包含“Accept-Encoding”标头的字节范围请求，从而导致源使用不同的内容长度做出响应。 如果是，则可以在源/Azure Front Door 上禁用压缩，也可以创建规则集规则以从字节范围请求中删除 `accept-encoding`。

    :::image type="content" source="..\media\troubleshoot-route-issues\remove-encoding-rule.png" alt-text="规则集中的接受编码规则的屏幕截图。":::

## <a name="requests-sent-to-the-custom-domain-return-a-400-status-code"></a>发送到自定义域的请求返回 400 状态代码

### <a name="symptom"></a>症状

* 你已创建 Azure Front Door 实例，但对域或前端主机的请求返回了 HTTP 400 状态代码。
* 你已创建了自定义域到所配置的前端主机的 DNS 映射。 但是，向自定义域主机名发送请求会返回 HTTP 400 状态代码。 它似乎没有路由到已配置的后端。

### <a name="cause"></a>原因

如果没有为已添加为前端主机的自定义域配置传递规则，则会出现此问题。 需要为该前端主机显式添加传递规则。 即使已为 Azure Front Door 子域 (*.azurefd.net) 下的前端主机配置了传递规则，也是如此。

### <a name="troubleshooting-steps"></a>疑难解答步骤

为自定义域添加传递规则，以将流量定向到选定的源组。

## <a name="azure-front-door-doesnt-redirect-http-to-https"></a>Azure Front Door 没有将 HTTP 重定向到 HTTPS

### <a name="symptom"></a>症状

Azure Front Door 具有将 HTTP 重定向到 HTTPS 的传递规则，但访问域时仍然始终使用 HTTP 作为协议。

### <a name="cause"></a>原因

如果没有正确地为 Azure Front Door 配置传递规则，则会出现此行为。 基本而言，当前配置不具体，并且可能有冲突的规则。

### <a name="troubleshooting-steps"></a>疑难解答步骤


## <a name="request-to-the-frontend-host-name-returns-a-411-status-code"></a>对前端主机名发送请求时返回 411 状态代码

### <a name="symptom"></a>症状

你创建了一个 Azure Front Door Standard/Premium 实例，并配置了前端主机、其中至少包含一个源的源组，以及一个将前端主机连接到源组的传递规则。 将请求发送到配置的前端主机时，你的内容似乎不可用，因为返回了 HTTP 411 状态代码。

对这些请求的响应可能还会在响应正文中包含 HTML 错误页，其中包含解释性说明。 例如：`HTTP Error 411. The request must be chunked or have a content length`。

### <a name="cause"></a>原因

有多个可能的原因会导致此症状。 总体原因是 HTTP 请求不完全符合 RFC 标准。 

例如，发送的 `POST` 请求没有 `Content-Length` 或 `Transfer-Encoding` 标头（例如，使用 `curl -X POST https://example-front-door.domain.com`）。 此请求不符合 [RFC 7230](https://tools.ietf.org/html/rfc7230#section-3.3.2) 中规定的要求。 Azure Front Door 会阻止它，并返回 HTTP 411 响应。

此行为独立于 Azure Front Door 的 Web 应用程序防火墙 (WAF) 功能。 目前无法禁用此行为。 所有 HTTP 请求都必须满足要求，即使未使用 WAF 功能。

### <a name="troubleshooting-steps"></a>疑难解答步骤

- 验证请求是否符合必要的 RFC 中规定的要求。

- 记下请求响应中返回的任何 HTML 消息正文。 消息正文通常准确地说明了请求不符合标准的情况。

## <a name="next-steps"></a>后续步骤

了解如何[创建 Front Door 标准版/高级版](create-front-door-portal.md)。
