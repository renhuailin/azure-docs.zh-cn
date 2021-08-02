---
title: 在 Azure Front Door 中监视指标和日志 | Microsoft Docs
description: 本文介绍 Azure Front Door 支持的不同指标和访问日志
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2020
ms.author: yuajia
ms.openlocfilehash: a972123604cafbfc4c6753c1fe3ad21147b53b2d
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2021
ms.locfileid: "106550653"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door"></a>在 Azure Front Door 中监视指标和日志

使用 Azure Front Door，可以通过以下方式监视资源：

- **指标** Azure Front Door 目前有八个指标来查看性能计数器。
- **日志**。 通过活动和诊断日志，可出于监视目的从资源保存或使用性能、访问及其他数据。

##  <a name="metrics"></a><a name="metrics"></a>指标

指标是某些 Azure 资源的一项功能，可让你能够查看门户中的性能计数器。 以下是可用的 Front Door 指标：

| 指标 | 指标显示名称 | 计价单位 | 维度 | 说明 |
| --- | --- | --- | --- | --- |
| RequestCount | 请求计数 | 计数 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Front Door 服务的客户端请求数。  |
| RequestSize | 请求大小 | 字节 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | 以请求的形式从客户端发送到 Front Door 的字节数。 |
| ResponseSize | 响应大小 | 字节 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | 以响应的形式从 Front Door 发送到客户端的字节数。 |
| TotalLatency | 总延迟 | 毫秒 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | 从 Front Door 接收到客户端请求到最后一个响应字节从 AFD 发送到客户端的总时间。 |
| BackendRequestCount | 后端请求计数 | 计数 | HttpStatus</br>HttpStatusGroup</br>后端 | 从 Front Door 发送到后端的请求数。 |
| BackendRequestLatency | 后端请求延迟 | 毫秒 | 后端 | 自 Front Door 向后端发送请求起，直至 Front Door 接收到来自后端的最后一个响应字节为止，所计算的时间。 |
| BackendHealthPercentage | 后端运行状况百分比 | 百分比 | 后端</br>BackendPool | 从 Front Door 到后端，成功运行状况探测的百分比。 |
| WebApplicationFirewallRequestCount | Web 应用程序防火墙请求计数 | 计数 | PolicyName</br>RuleName</br>操作 | Front Door 的应用层安全性所处理的客户端请求数。 |

## <a name="activity-logs"></a><a name="activity-log"></a>活动日志

活动日志提供有关在 Front Door 中执行的操作的信息。 它们还确定 Front Door 上执行的任何写入操作（put、post 或 delete）的内容、操作者和时间。

>[!NOTE]
>活动日志不包括读取 (get) 操作。 此外，也不包括使用 Azure 门户或原始管理 API 执行的操作。

可以在 Azure Monitor 中访问 Front Door 中的活动日志或 Azure 资源的所有日志。 要查看活动日志，请执行以下操作：

1. 选择你的 Front Door 实例。
2. 选择“活动日志”。

    :::image type="content" source="./media/front-door-diagnostics/activity-log.png" alt-text="活动日志":::

3. 选择一个筛选范围，然后选择“应用”。

## <a name="diagnostic-logs"></a><a name="diagnostic-logging"></a>诊断日志
诊断日志提供了大量有关操作和错误的信息，这些信息对于审核和故障排除非常重要。 诊断日志不同于活动日志。

活动日志让你能够了解对 Azure 资源执行的操作。 诊断日志提供资源已完成的操作的深入信息。 有关详细信息，请参阅 [Azure Monitor 诊断日志](../azure-monitor/essentials/platform-logs-overview.md)。

:::image type="content" source="./media/front-door-diagnostics/diagnostic-log.png" alt-text="诊断日志":::

若要为 Front Door 配置诊断日志：

1. 选择 Azure Front Door。

2. 选择“诊断设置”。

3. 选择“启用诊断”。 将诊断日志与指标一起存档到存储帐户，将其流式传输到事件中心，或者将其发送到 Azure Monitor 日志。

Front Door 目前提供诊断日志。 诊断日志提供单独的 API 请求，其中每个条目具有以下架构：

| 属性  | 说明 |
| ------------- | ------------- |
| BackendHostname | 如果请求被转发到后端，则此字段表示后端的主机名。 如果在为传递规则启用缓存时，请求将被重定向或转发到区域缓存，则此字段将为空。 |
| CacheStatus | 对于缓存方案，此字段定义在 POP 处的缓存命中/未命中情况 |
| ClientIp | 发出请求的客户端的 IP 地址。 如果请求中包含 X-Forwarded-For 标头，则从同一个标头中获取客户端 IP。 |
| ClientPort | 发出请求的客户端的 IP 端口。 |
| HttpMethod | 请求使用的 HTTP 方法。 |
| HttpStatusCode | 从代理返回的 HTTP 状态代码。 |
| HttpStatusDetails | 请求的结果状态。 此字符串值的含义可以在状态引用表中找到。 |
| HttpVersion | 请求或连接的类型。 |
| POP | 请求着陆的边缘的短名称。 |
| RequestBytes | HTTP 请求消息的大小（以字节为单位），包括请求标头和请求正文。 |
| RequestUri | 已收到请求的 URI。 |
| ResponseBytes | 后端服务器作为响应发送的字节数。  |
| RoutingRuleName | 请求匹配的传递规则的名称。 |
| RulesEngineMatchNames | 请求匹配的规则的名称。 |
| SecurityProtocol | 请求所使用的 TLS/SSL 协议版本，如果没有加密，则为 null。 |
| SentToOriginShield </br> （已弃用）* 请参阅以下部分中的弃用说明。| 如果为 true，则表示请求是从源防护缓存（而不是边缘 pop）响应的。 源防护是用于提高缓存命中率的父缓存。 |
| isReceivedFromClient | 如果为 true，则表示请求来自客户端。 如果为 false，则请求在边缘（子 POP）未命中，并从源盾牌（父 POP）进行响应。 |
| TimeTaken | 从请求的第一个字节传入 Front Door 到响应的最后一个字节传出 Front Door 的时间长度（以秒为单位）。 |
| TrackingReference | 标识由 Front Door 提供的请求的唯一引用字符串，该请求还会以 X-Azure-Ref 标头的形式发送到客户端。 是搜索特定请求访问日志中的详细信息必需的。 |
| UserAgent | 客户端使用的浏览器类型。 |
| ErrorInfo | 此字段包含特定类型的错误，以便进一步进行故障排除。 </br> 可能的值包括： </br> **NoError**：指示未发现任何错误。 </br> **CertificateError**：常规 SSL 证书错误。</br> **CertificateNameCheckFailed**：SSL 证书中的主机名无效或不匹配。 </br> **ClientDisconnected**：客户端网络连接问题导致请求失败。 </br> **UnspecifiedClientError**：常规客户端错误。 </br> **InvalidRequest**：请求无效。 此错误的可能原因是标头、正文和 URL 格式不正确。 </br> **DNSFailure**：DNS 错误。 </br> **DNSNameNotResolved**：无法解析服务器名称或地址。 </br> **OriginConnectionAborted**：与源的连接突然断开。 </br> **OriginConnectionError**：常规源连接错误。 </br> **OriginConnectionRefused**：无法与源建立连接。 </br> **OriginError**：常规源错误。 </br> **OriginInvalidResponse**：源返回的响应无效或无法识别。 </br> **OriginTimeout**：超过了源请求的超时期限。 </br> **ResponseHeaderTooBig**：源返回的响应头过大。 </br> **RestrictedIP**：由于 IP 受限而阻止了请求。 </br> **SSLHandshakeError**：由于 SSL 握手失败，无法与源建立连接。 </br> **UnspecifiedError**：发生了与表中任何错误都不相符的错误。 |

### <a name="sent-to-origin-shield-deprecation"></a>发送到源盾牌弃用
原始日志属性 isSentToOriginShield 已弃用，并已替换为新的字段 isReceivedFromClient 。 如果正在使用弃用的字段，请改为使用新字段。 

原始日志包括从 CDN 边缘（子 POP）和源盾牌生成的日志。 源盾牌是指位于全球各地的战略性父节点。 这些节点与源服务器通信，可减少源上的流量负载。 

对于每个进入源盾牌的请求，都有 2 个日志项：

* 一个传至边缘节点
* 一个传至源盾牌。 

要区分边缘节点与源盾牌的流出或响应，可以使用字段 isReceivedFromClient 来获取正确数据。 

如果值为 false，则表示该请求是从源盾牌到边缘节点的响应。 这种方法用来比较原始日志与账单数据非常有效。 从源盾牌向边缘节点流出不会产生费用。 从边缘节点向客户端流出却会产生费用。 

Kusto 查询示例，不包括在 Log Analytics 的源盾牌上生成的日志。

`AzureDiagnostics 
| where Category == "FrontdoorAccessLog" and isReceivedFromClient_b == true`

> [!NOTE]
> 对于各种路由配置和流量行为，某些字段（如 backendHostname、cacheStatus、isReceivedFromClient 和 POP 字段）可能会用不同的值来响应。 下表说明了这些字段在各种方案中将具有的不同值：

| 方案 | 日志项计数 | POP | BackendHostname | isReceivedFromClient | CacheStatus |
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| 未启用缓存的传递规则 | 1 | 边缘 POP 代码 | 请求被转发到的后端 | True | CONFIG_NOCACHE |
| 启用缓存的传递规则。 缓存命中边缘 POP | 1 | 边缘 POP 代码 | 空 | True | HIT |
| 启用缓存的传递规则。 缓存未命中边缘 POP，但命中父缓存 POP | 2 | 1. 边缘 POP 代码</br>2. 父缓存 POP 代码 | 1. 父缓存 POP 主机名</br>2. 空 | 1. True</br>2. False | 1. MISS</br>2. HIT |
| 启用缓存的传递规则。 缓存未命中边缘 POP，但部分缓存命中父缓存 POP | 2 | 1. 边缘 POP 代码</br>2. 父缓存 POP 代码 | 1. 父缓存 POP 主机名</br>2. 帮助填充缓存的后端 | 1. True</br>2. False | 1. MISS</br>2. PARTIAL_HIT |
| 启用缓存的传递规则。 缓存部分命中边缘 POP，但命中父缓存 POP | 2 | 1. 边缘 POP 代码</br>2. 父缓存 POP 代码 | 1. 边缘 POP 代码</br>2. 父缓存 POP 代码 | 1. True</br>2. False | 1. PARTIAL_HIT</br>2. HIT |
| 启用缓存的传递规则。 缓存未命中边缘和父缓存 POP | 2 | 1. 边缘 POP 代码</br>2. 父缓存 POP 代码 | 1. 边缘 POP 代码</br>2. 父缓存 POP 代码 | 1. True</br>2. False | 1. MISS</br>2. MISS |

> [!NOTE]
> 对于缓存方案，如果从 Front Door 边缘或源盾牌缓存提供请求的部分字节，同时从大型对象的源提供部分字节，则缓存状态的值将为 partial_hit。

Front Door 使用一种被称作对象区块的技术。 请求大型文件时，Front Door 会从源检索文件的较小部分。 在 Front Door POP 服务器收到请求的文件的完整范围或字节范围后，Front Door 边缘服务器会以 8 MB 的区块从源请求文件。

区块到达 Front Door 边缘后，会将区块缓存并立即提供给用户。 然后，Front Door 会并行预提取下一个区块。 此预提取可确保内容先于用户一个区块，这可以减少延迟。 此过程将继续，直到整个文件下载完成（如果已请求），所有字节范围都可用（如果已请求），或客户端关闭连接。 有关字节范围请求的详细信息，请参阅 RFC 7233。 Front Door 会在收到区块后进行缓存。 无需在 Front Door 缓存上缓存整个文件。 随后从 Front Door 缓存中请求文件或字节范围的请求。 如果未在 Front Door 上缓存所有区块，将使用预提取从源请求区块。 此优化取决于源服务器能否支持字节范围请求。 如果源服务器不支持字节范围请求，则此优化不会生效。

## <a name="next-steps"></a>后续步骤

- [创建 Front Door 配置文件](quickstart-create-front-door.md)
- [Front Door 的工作原理](front-door-routing-architecture.md)
