---
title: 排查 Azure 前门标准/高级配置问题
description: 在本教程中，你将了解如何对 Azure 前门标准/高级实例可能遇到的一些常见问题进行故障排除。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: 4690a513494d794377ee0c2e8cfb101e8fd66a0f
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2021
ms.locfileid: "101098792"
---
# <a name="troubleshooting-common-routing-problems-with-azure-front-door-standardpremium"></a>排查 Azure 前门标准/高级版的常见路由问题

本文介绍如何解决 Azure 前门配置可能会遇到的常见路由问题。

## <a name="503-response-from-azure-front-door-after-a-few-seconds"></a>几秒钟后，从 Azure 前门开始响应503

### <a name="symptom"></a>症状

* 不通过 Azure 前门就发送到后端的常规请求将会成功。 通过 Azure 前门会导致503错误响应。
* Azure 前门的故障通常会在大约30秒后显示。

### <a name="cause"></a>原因

此问题的原因可能是以下两种情况之一：
 
* 源所用时间超过配置的超时 (默认值为30秒) 用于接收来自 Azure 前门的请求。
* 向 Azure 前门请求发送响应所用的时间超过了超时值。 

### <a name="troubleshooting-steps"></a>疑难解答步骤

* 直接将请求发送到后端 (，无需通过 Azure 前门) 。 查看后端通常需要多长时间才能响应。
* 通过 Azure 前门发送请求，查看是否收到503响应。 如果不是，则问题可能不是超时问题。 请联系支持人员。
* 如果通过 Azure 前门，则会生成503错误响应代码，并 `sendReceiveTimeout` 为 Azure 前门配置字段。  (240 秒) ，你可以将默认超时扩展到4分钟。 该设置位于下 `Endpoint Setting` ，并被调用 `Origin response timeout` 。 

## <a name="requests-sent-to-the-custom-domain-return-a-400-status-code"></a>发送到自定义域的请求返回400状态代码

### <a name="symptom"></a>症状

* 你创建了一个 Azure 前门实例，但向域或前端主机发出的请求将返回 HTTP 400 状态代码。
* 你为自定义域创建了 DNS 映射到你配置的前端主机。 但是，向自定义域主机名发送请求将返回 HTTP 400 状态代码。 它似乎不会路由到您配置的后端。

### <a name="cause"></a>原因

如果未为作为前端主机添加的自定义域配置路由规则，则会出现此问题。 需要为该前端主机显式添加路由规则。 即使已为 Azure 前门子域下的前端主机配置了路由规则 ( azurefd.net) 也是如此。

### <a name="troubleshooting-steps"></a>疑难解答步骤

为自定义域添加路由规则以将流量定向到所选的源组。

## <a name="azure-front-door-doesnt-redirect-http-to-https"></a>Azure 前门不会将 HTTP 重定向到 HTTPS

### <a name="symptom"></a>症状

Azure 前门具有将 HTTP 重定向到 HTTPS 的路由规则，但访问域仍将 HTTP 作为协议进行维护。

### <a name="cause"></a>原因

如果未正确配置 Azure 前门的路由规则，则会发生此行为。 基本上，当前配置不特定，并且可能存在冲突的规则。

### <a name="troubleshooting-steps"></a>疑难解答步骤


## <a name="request-to-the-frontend-host-name-returns-a-411-status-code"></a>请求前端主机名返回411状态代码

### <a name="symptom"></a>症状

你创建了一个 Azure 前门标准/高级实例，并配置了前端主机、其中至少包含一个源的源组，以及一个将前端主机连接到源组的路由规则。 当请求进入配置的前端主机时，你的内容似乎不可用，因为返回了 HTTP 411 状态代码。

对这些请求的响应可能还包含响应正文中的 HTML 错误页，其中包含解释性声明。 例如：`HTTP Error 411. The request must be chunked or have a content length`。

### <a name="cause"></a>原因

此症状有多种可能的原因。 整体原因在于，HTTP 请求并不完全符合 RFC。 

例如 `POST` ，如果不使用或标头发送请求，则不符合要求 `Content-Length` `Transfer-Encoding` (例如，使用 `curl -X POST https://example-front-door.domain.com`) 。 此请求不满足 [RFC 7230](https://tools.ietf.org/html/rfc7230#section-3.3.2)中规定的要求。 Azure 前门会阻止其使用 HTTP 411 响应。

此行为独立于 Azure 前门 (WAF) 功能的 Web 应用程序防火墙。 目前无法禁用此行为。 所有 HTTP 请求都必须满足要求，即使未使用 WAF 功能也是如此。

### <a name="troubleshooting-steps"></a>疑难解答步骤

- 验证你的请求是否符合必要 Rfc 中设置的要求。

- 记下为响应请求而返回的任何 HTML 消息正文。 消息正文通常说明你的请求是不 *相容的。*

## <a name="next-steps"></a>后续步骤

了解如何 [创建前门标准/高级版](create-front-door-portal.md)。
