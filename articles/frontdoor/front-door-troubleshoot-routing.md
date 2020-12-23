---
title: 排查 Azure 前门配置问题
description: 在本教程中，你将了解如何自行解决你的 Azure 前门实例可能遇到的一些常见问题。
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/30/2020
ms.author: duau
ms.openlocfilehash: 15cdcefe628a392704e650b560243e2f6a134ec2
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629982"
---
# <a name="troubleshooting-common-routing-problems"></a>常见路由问题疑难解答

本文介绍如何解决 Azure 前门配置可能会遇到的常见路由问题。

## <a name="503-response-from-azure-front-door-after-a-few-seconds"></a>几秒钟后，从 Azure 前门开始响应503

### <a name="symptom"></a>症状

* 不通过 Azure 前门就发送到后端的常规请求将会成功。 通过 Azure 前门会导致503错误响应。
* Azure 前门的故障通常会在大约30秒后显示。

### <a name="cause"></a>原因

此问题的原因可能是以下两种情况之一：
 
* 后端时间比配置的超时长 (默认值为30秒) 用于接收来自 Azure 前门的请求。
* 向 Azure 前门请求发送响应所用的时间超过了超时值。 

### <a name="troubleshooting-steps"></a>疑难解答步骤

* 直接将请求发送到后端 (，无需通过 Azure 前门) 。 查看后端通常需要多长时间才能响应。
* 通过 Azure 前门发送请求，查看是否收到503响应。 如果不是，则问题可能不是超时问题。 请联系支持人员。
* 如果通过 Azure 前门，则会生成503错误响应代码，并 `sendReceiveTimeout` 为 Azure 前门配置字段。  (240 秒) ，你可以将默认超时扩展到4分钟。 该设置位于下 `backendPoolSettings` ，并被调用 `sendRecvTimeoutSeconds` 。 

## <a name="requests-sent-to-the-custom-domain-return-a-400-status-code"></a>发送到自定义域的请求返回400状态代码

### <a name="symptom"></a>症状

* 你创建了一个 Azure 前门实例，但向域或前端主机发出的请求将返回 HTTP 400 状态代码。
* 你为自定义域创建了 DNS 映射到你配置的前端主机。 但是，向自定义域主机名发送请求将返回 HTTP 400 状态代码。 它似乎不会路由到您配置的后端。

### <a name="cause"></a>原因

如果未为作为前端主机添加的自定义域配置路由规则，则会出现此问题。 需要为该前端主机显式添加路由规则。 即使已为 Azure 前门子域下的前端主机配置了路由规则 ( azurefd.net) 也是如此。

### <a name="troubleshooting-steps"></a>疑难解答步骤

为自定义域添加路由规则以将流量定向到选定的后端池。

## <a name="azure-front-door-doesnt-redirect-http-to-https"></a>Azure 前门不会将 HTTP 重定向到 HTTPS

### <a name="symptom"></a>症状

Azure 前门具有将 HTTP 重定向到 HTTPS 的路由规则，但访问域仍将 HTTP 作为协议进行维护。

### <a name="cause"></a>原因

如果未正确配置 Azure 前门的路由规则，则会发生此行为。 基本上，当前配置不特定，并且可能存在冲突的规则。

### <a name="troubleshooting-steps"></a>疑难解答步骤

## <a name="request-to-a-frontend-host-name-returns-a-404-status-code"></a>请求前端主机名返回404状态代码

### <a name="symptom"></a>症状

你创建了一个 Azure 前门实例，方法是：配置前端主机、至少包含一个后端的后端池，以及一个将前端主机连接到后端池的路由规则。 向配置的前端主机发出请求时，你的内容将不可用。 因此，请求将返回 HTTP 404 状态代码。

### <a name="cause"></a>原因

有多个可能的原因会导致此症状：

* 后端不是面向公众的后端，对 Azure 前门不可见。
* 后端配置不正确，导致 Azure 前门发送错误的请求。 换句话说，后端仅接受 HTTP，并且你不允许 HTTPS。 因此，Azure 前门正在尝试转发 HTTPS 请求。
* 后端拒绝了连同请求一起转发到后端的主机标头。
* 尚未完全部署后端的配置。

### <a name="troubleshooting-steps"></a>疑难解答步骤

* 检查部署时间：
   * 确保已等待至少10分钟，以便部署配置。

* 检查后端设置：
    * 请参阅应将请求路由到的后端池。  (这取决于你如何配置路由规则。 ) 验证后端主机类型和后端主机名称是否正确。 如果后端是自定义主机，请确保拼写正确。 

    * 检查 HTTP 和 HTTPS 端口。 大多数情况下，80和 443 (分别) 正确且不需要进行任何更改。 但是，您的后端不是以这种方式配置的，并且正在侦听其他端口。

    * 检查为前端主机应路由到的后端配置的后端主机标头。 在大多数情况下，此标头应与后端主机名相同。 但是，如果后端需要某种不同的配置，则错误的值可能导致不同的 HTTP 4xx 状态代码。 如果输入后端的 IP 地址，则可能需要将后端主机标头设置为后端的主机名。

* 检查路由规则设置：
    * 请从有问题的前端主机名路由到后端池的路由规则。 请确保在转发请求时正确配置了接受的协议。 " **接受的协议** " 字段确定 Azure 前门应接受的请求。 转发协议确定 Azure 前门应该使用哪种协议将请求转发到后端。
      
      例如，如果后端仅接受 HTTP 请求，以下配置将有效：
            
      * 接受的协议是 HTTP 和 HTTPS。 转发协议为 HTTP。 匹配请求不起作用，因为 HTTPS 是允许的协议。 如果请求作为 HTTPS 传入，Azure 前门会尝试使用 HTTPS 转发该请求。

      * 接受的协议是 HTTP。 转发协议要么是匹配请求，要么是 HTTP。
    - 默认情况下禁用 **Url 重写** 。 仅当要缩小要使其可用的后端托管资源范围时，才使用此字段。 此字段处于禁用状态时，Azure 前门将转发其收到的相同请求路径。 可以 misconfigure 此字段。 因此，当 Azure 前门请求不可用的后端中的资源时，它将返回 HTTP 404 状态代码。

## <a name="request-to-the-frontend-host-name-returns-a-411-status-code"></a>请求前端主机名返回411状态代码

### <a name="symptom"></a>症状

你创建了一个 Azure 前门实例并配置了前端主机、其中至少包含一个后端的后端池，以及一个将前端主机连接到后端池的路由规则。 当请求进入配置的前端主机时，你的内容似乎不可用，因为返回了 HTTP 411 状态代码。

对这些请求的响应可能还包含响应正文中的 HTML 错误页，其中包含解释性声明。 例如：`HTTP Error 411. The request must be chunked or have a content length`。

### <a name="cause"></a>原因

此症状有多种可能的原因。 整体原因在于，HTTP 请求并非完全符合 RFC。 

例如 `POST` ，如果不使用或标头发送请求，则不符合要求 `Content-Length` `Transfer-Encoding` (例如，使用 `curl -X POST https://example-front-door.domain.com`) 。 此请求不满足 [RFC 7230](https://tools.ietf.org/html/rfc7230#section-3.3.2)中规定的要求。 Azure 前门会阻止其使用 HTTP 411 响应。

此行为独立于 Azure 前门 (WAF) 功能的 Web 应用程序防火墙。 目前无法禁用此行为。 所有 HTTP 请求都必须满足要求，即使未使用 WAF 功能也是如此。

### <a name="troubleshooting-steps"></a>疑难解答步骤

- 验证请求是否符合必要 Rfc 中设置的要求。

- 记下为响应请求而返回的任何 HTML 消息正文。 消息正文通常说明你的请求是不 *相容的。*
