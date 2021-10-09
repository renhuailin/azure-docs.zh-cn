---
title: 排查 Azure Front Door 配置问题
description: 本教程介绍如何自我排查在使用 Azure Front Door 实例时可能会遇到的一些常见问题。
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/08/2021
ms.author: duau
ms.openlocfilehash: ed47d310f418936b84c505fcf254947a67f0eb6d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124824399"
---
# <a name="troubleshooting-common-routing-problems"></a>排查常见的路由问题

本文介绍如何排查在使用 Azure Front Door 配置时可能会遇到的常见路由问题。

## <a name="503-response-from-azure-front-door-after-a-few-seconds"></a>数秒后接收到来自 Azure Front Door 的 503 响应

### <a name="symptom"></a>症状

* 未通过 Azure Front Door 发送到后端的常规请求会成功。 通过 Azure Front Door 发送则出现 503 错误响应。
* 此 Azure Front Door 故障通常会在大约 30 秒后出现。
* 日志 `ErrorInfo: OriginInvalidResponse` 出现间歇性 503 错误。

### <a name="cause"></a>原因

此问题的原因可能是以下三种情况之一：
 
* 后端接收来自 Azure Front Door 的请求所用的时间超过配置的超时（默认值为 30 秒）。
* 后端发送 Azure Front Door 请求的响应所用的时间超过超时值。
* 客户端使用 `Accept-Encoding header` 发送了字节范围请求（压缩启用）。

### <a name="troubleshooting-steps"></a>疑难解答步骤

* 直接向后端发送请求（不通过 Azure Front Door）。 看后端通常需要多久才能进行响应。
* 通过 Azure Front Door 发送请求，看是否会收到任何 503 响应。 如果没有收到任何 503 响应，则问题可能与超时无关。 请联系支持人员。
* 如果通过 Azure Front Door 发送的请求导致 503 错误响应代码，请为 Azure Front Door 配置“发送/接收超时(秒)”设置。 可以将默认超时延长到 4 分钟（240 秒）。 可以通过转到 Front Door 设计器并选择“设置”对此设置进行配置。

    :::image type="content" source=".\media\troubleshoot-route-issues\send-receive-timeout.png" alt-text="Front Door 设计器中发送/接收超时字段的屏幕截图。":::

* 如果超时无法解决问题，请使用 Fiddler 或浏览器开发人员工具等工具来检查客户端是否正在发送包含“Accept-Encoding”标头的字节范围请求，从而导致源使用不同的内容长度做出响应。 如果是，则可以在源/Azure Front Door 上禁用压缩，也可以创建规则集规则以从字节范围请求中删除 `accept-encoding`。

    :::image type="content" source=".\media\troubleshoot-route-issues\remove-encoding-rule.png" alt-text="规则引擎中的接受编码规则的屏幕截图。":::

## <a name="requests-sent-to-the-custom-domain-return-a-400-status-code"></a>发送到自定义域的请求返回 400 状态代码

### <a name="symptom"></a>症状

* 你已创建 Azure Front Door 实例，但对域或前端主机的请求返回了 HTTP 400 状态代码。
* 你已创建了自定义域到所配置的前端主机的 DNS 映射。 但是，向自定义域主机名发送请求会返回 HTTP 400 状态代码。 它似乎没有路由到已配置的后端。

### <a name="cause"></a>原因

如果没有为已添加为前端主机的自定义域配置传递规则，则会出现此问题。 需要为该前端主机显式添加传递规则。 即使已为 Azure Front Door 子域 (*.azurefd.net) 下的前端主机配置了传递规则，也是如此。

### <a name="troubleshooting-steps"></a>疑难解答步骤

为自定义域添加路由规则以将流量定向到选定的后端池。

## <a name="azure-front-door-doesnt-redirect-http-to-https"></a>Azure Front Door 没有将 HTTP 重定向到 HTTPS

### <a name="symptom"></a>症状

Azure Front Door 具有将 HTTP 重定向到 HTTPS 的传递规则，但访问域时仍然始终使用 HTTP 作为协议。

### <a name="cause"></a>原因

如果没有正确地为 Azure Front Door 配置传递规则，则会出现此行为。 基本而言，当前配置不具体，并且可能有冲突的规则。

### <a name="troubleshooting-steps"></a>疑难解答步骤

## <a name="request-to-a-frontend-host-name-returns-a-404-status-code"></a>对前端主机名发送请求时返回 404 状态代码

### <a name="symptom"></a>症状

你已通过配置前端主机创建了一个 Azure Front Door 实例、至少包含一个后端的后端池，以及用于将前端主机连接到后端池的路由规则。 对已配置的前端主机发出请求时，你的配置不可用。 因此，请求会返回 HTTP 404 状态代码。

### <a name="cause"></a>原因

有多个可能的原因会导致此症状：

* 后端不是面向公众的后端，对 Azure Front Door 不可见。
* 后端配置错误，导致 Azure Front Door 发送错误的请求。 换句话说，后端仅接受 HTTP，禁止 HTTPS。 因此，Azure Front Door 正在尝试转发 HTTPS 请求。
* 后端拒绝了连同请求一起转发到后端的主机标头。
* 后端的配置尚未完全部署。

### <a name="troubleshooting-steps"></a>疑难解答步骤

* 检查部署时间：
   * 请务必等待至少 10 分钟来部署配置。

* 检查后端设置：
    * 转到应将请求路由到的后端池。 （这取决于你如何配置路由规则。）请确认后端主机类型和后端主机名正确无误。 如果后端是自定义主机，请确保拼写正确。 

    * 检查 HTTP 和 HTTPS 端口。 在大多数情况下，80 和 443（分别为 HTTP 和 HTTPS 端口）是正确的，无需更改。 但是，你的后端可能并未采用这种配置，而是侦听另一个端口。

    * 检查为前端主机应路由到的后端配置的后端主机标头。 在大多数情况下，此标头应与后端主机名相同。 但是，如果后端需要某种不同的配置，则错误的值可能导致不同的 HTTP 4xx 状态代码。 输入后端的 IP 地址时，可能需要将后端主机标头设置为后端的主机名。

* 检查路由规则设置：
    * 转到应该从相关前端主机名路由到后端池的路由规则。 确保正确配置转发请求时接受的协议。 “接受的协议”字段可确定 Azure Front Door 应接受的请求。 转发协议可确定 Azure Front Door 将请求转发到后端所应使用的协议。
      
      例如，如果后端仅接受 HTTP 请求，则以下配置有效：
            
      * 接受的协议是 HTTP 和 HTTPS。 转发协议是 HTTP。 匹配请求无效，因为 HTTPS 是允许的协议。 如果传入的是 HTTPS 请求，Azure Front Door 会尝试使用 HTTPS 转发该请求。

      * 接受的协议是 HTTP。 转发协议是匹配请求或 HTTP。
    - 默认情况下禁用“URL 重写”。 仅当你要缩小想要提供的后端托管资源的范围时，才使用此字段。 禁用此字段时，Azure Front Door 会转发它收到的相同请求路径。 此字段可能会配置错误。 因此，当 Azure Front Door 正在请求不可用的后端资源时，将返回 HTTP 404 状态代码。

## <a name="request-to-the-frontend-host-name-returns-a-411-status-code"></a>对前端主机名发送请求时返回 411 状态代码

### <a name="symptom"></a>症状

你已创建了一个 Azure Front Door 实例并配置了前端主机、至少包含一个后端的后端池，以及用于将前端主机连接到后端池的路由规则。 将请求发送到配置的前端主机时，你的配置似乎不可用，因为返回了 HTTP 411 状态代码。

对这些请求的响应可能还会在其正文中包含 HTML 错误页，其中提供解释性说明。 例如：`HTTP Error 411. The request must be chunked or have a content length`。

### <a name="cause"></a>原因

有多个可能的原因会导致此症状。 总体原因是 HTTP 请求不完全符合 RFC 标准。 

例如发送的 `POST` 请求没有 `Content-Length` 或 `Transfer-Encoding` 标头（例如，使用 `curl -X POST https://example-front-door.domain.com`）。 此请求不符合 [RFC 7230](https://tools.ietf.org/html/rfc7230#section-3.3.2) 中规定的要求。 Azure Front Door 会阻止它，并返回 HTTP 411 响应。

此行为独立于 Azure Front Door 的 Web 应用程序防火墙 (WAF) 功能。 目前无法禁用此行为。 所有 HTTP 请求都必须满足要求，即使未使用 WAF 功能也是如此。

### <a name="troubleshooting-steps"></a>疑难解答步骤

- 验证请求是否符合必要 RFC 中规定的要求。

- 记下请求响应中返回的任何 HTML 消息正文。 消息正文通常精确说明了请求不符合标准的情况。
