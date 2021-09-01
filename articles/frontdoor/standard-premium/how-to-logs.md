---
title: Azure Front Door 标准版/高级版（预览版）日志记录
description: 本文介绍 Azure Front Door 标准版/高级版中日志记录的工作原理。
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: article
ms.date: 08/26/2021
ms.author: duau
ms.openlocfilehash: 5446c240d1d07b5b99fe6f91a031617756a6018c
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2021
ms.locfileid: "122967586"
---
# <a name="azure-front-door-standardpremium-preview-logging"></a>Azure Front Door 标准版/高级版（预览版）日志记录

> [!Note]
> 本文档适用于 Azure Front Door 标准/高级（预览版）。 正在寻找有关 Azure Front Door 的信息？ 请查看[此处](../front-door-overview.md)。

Azure Front Door 提供不同的日志记录来帮助你跟踪、监视和调试 Front Door。 

* 访问日志包含有关 AFD 收到的每个请求的详细信息，可帮助你分析和监视访问模式以及调试问题。 
* 活动日志让你能够了解对 Azure 资源执行的操作。  
* 运行状况探测日志提供无法对源执行的所有探测的日志。 
* Web 应用程序防火墙 (WAF) 日志提供通过 Azure Front Door 终结点检测或防护模式记录的请求的详细信息。 还可以通过这些日志来查看配置了 WAF 的自定义域。

> [!IMPORTANT]
> Azure Front Door 标准版/高级版（预览版）目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

默认未启用访问日志、运行状况探测日志和 WAF 日志。 使用以下步骤可启用日志记录。 默认情况下会收集活动日志条目，可在 Azure 门户中查看这些条目。 日志最多可能会延迟几分钟。 

可通过三种方式存储日志： 

* **存储帐户：** 如果日志存储时间较长并且只是在需要时才查看日志，则最好使用存储帐户。 
* **事件中心：** 事件中心非常适合于集成其他安全信息和事件管理 (SIEM) 工具或外部数据存储。 例如：Splunk/DataDog/Sumo。 
* **Azure Log Analytics：** Azure Monitor 中的 Azure Log Analytics 最适合用于对 Azure Front Door 性能进行一般性的实时监视和分析。

## <a name="configure-logs"></a>配置日志

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 搜索“Azure Front Door 标准版/高级版”，并选择 Azure Front Door 配置文件。

1. 在配置文件中，转到“监视”并选择“诊断设置”。  选择“添加诊断设置”。

   :::image type="content" source="../media/how-to-logging/front-door-logging-1.png" alt-text="诊断设置登陆页的屏幕截图。":::

1. 在“诊断设置”下的“诊断设置名称”中输入一个名称。 ****

1. 选择来自“FrontDoorAccessLog”、“FrontDoorHealthProbeLog”和“FrontDoorWebApplicationFirewallLog”的 **日志**。  

1. 选择“目标详细信息”。 **** 目标选项有： 

    * 发送到 Log Analytics
        * 选择“订阅”和“Log Analytics 工作区” 。
    * 存档到存储帐户
        * 选择“订阅”和“存储帐户” 。 设置“保留期(天)”。
    * **流式传输到事件中心**
        * 选择“订阅”、“事件中心命名空间”、“事件中心名称(可选)”和“事件中心策略名称”。 **  ** 

     :::image type="content" source="../media/how-to-logging/front-door-logging-2.png" alt-text="诊断设置页的屏幕截图。":::

1. 单击“保存” 。

## <a name="access-log"></a>访问日志

Azure Front Door 目前提供单独的 API 请求，其对应的每个条目采用以下架构并以如下所示的 JSON 格式进行记录。 

| 属性 | 说明 |
|----------|-------------| 
| TrackingReference | 标识由 AFD 提供服务的请求的唯一引用字符串，该字符串也会作为 X-Azure-Ref 头发送到客户端。 是搜索特定请求访问日志中的详细信息必需的。 |
| 时间 | AFD 边缘将请求的内容传送到客户端的日期和时间 (UTC)。 |
| HttpMethod | 请求使用的 HTTP 方法：DELETE、GET、HEAD、OPTIONS、PATCH、POST 或 PUT。 |
| HttpVersion | 查看方在请求中指定的 HTTP 版本。 |
| RequestUri | 已收到请求的 URI。 此字段是完整的方案、端口、域、路径和查询字符串 |
| HostName | 来自客户端的请求中的主机名。 如果启用自定义域并使用通配符域 (*.contoso.com)，则主机名为 a.contoso.com。 如果使用 Azure Front Door 域 (contoso.azurefd.net)，则主机名为 contoso.azurefd.net。 |
| RequestBytes | HTTP 请求消息的大小（以字节为单位），包括请求标头和请求正文。 查看方在请求中包含的数据字节数（包括头）。 |
| ResponseBytes | 后端服务器作为响应发送的字节数。 |
| UserAgent | 客户端使用的浏览器类型。 |
| ClientIp | 发出原始请求的客户端的 IP 地址。 如果请求中包含 X-Forwarded-For 头，则从同一个头中获取客户端 IP。 |
| SocketIp | 与 AFD 边缘建立直接连接的 IP 地址。 如果客户端使用 HTTP 代理或负载均衡器发送了请求，则 SocketIp 的值是该代理或负载均衡器的 IP 地址。 |
| timeTaken | 从 AFD 边缘服务器收到客户端的请求到 AFD 将响应的最后一个字节发送到客户端所经历的时间，以毫秒为单位。 此字段不考虑网络延迟和 TCP 缓冲。 |
| RequestProtocol | 客户端在请求中指定的协议：HTTP、HTTPS。 |
| SecurityProtocol | 请求所使用的 TLS/SSL 协议版本，如果没有加密，则为 null。 可能的值包括：SSLv3、TLSv1、TLSv1.1、TLSv1.2 |
| SecurityCipher | 当“请求协议”的值为 HTTPS 时，此字段指示客户端和 AFD 协商的用于加密的 TLS/SSL 密码。 |
| 端点 | AFD 终结点的域名，例如 contoso.z01.azurefd.net |
| HttpStatusCode | 从 AFD 返回的 HTTP 状态代码。 |
| Pop | 响应用户请求的边缘 pop。  |
| 缓存状态 | 提供状态代码，指示当请求进入缓存时 CDN 服务如何处理该请求。 可能的值包括 HIT：通过 AFD 边缘 POP 缓存为 HTTP 请求提供服务。 <br> **MISS**：通过源为 HTTP 请求提供服务。 <br/> **PARTIAL_HIT**：通过 AFD 边缘 POP 缓存为请求中的一部分字节提供服务，通过对象分块方案的源为另一部分字节提供服务。 <br> **CACHE_NOCONFIG**：转发不使用缓存设置（包括绕过方案）的请求。 <br/> **PRIVATE_NOSTORE**：客户未在缓存设置中配置缓存。 <br> **REMOTE_HIT**：由父节点缓存为请求提供服务。 <br/> **N/A**：** 被已签名的 URL 和规则集拒绝的请求。 |
| MatchedRulesSetName | 已处理的规则的名称。 |
| RouteName | 请求匹配的路由的名称。 |
| ClientPort | 发出请求的客户端的 IP 端口。 |
| Referrer | 发起请求的站点的 URL。 |
| TimetoFirstByte | 从 AFD 收到请求到将第一个字节发送到客户端所经历的时间，以毫秒为单位，在 Azure Front Door 中测量。 此属性不测量客户端数据。 |
| ErrorInfo | 此字段提供每个响应的错误标记的详细信息。 <br> **NoError**：指示未发现任何错误。 <br> **CertificateError**：常规 SSL 证书错误。 <br> **CertificateNameCheckFailed**：SSL 证书中的主机名无效或不匹配。 <br> **ClientDisconnected**：客户端网络连接问题导致请求失败。 <br> **ClientGeoBlocked**：由于 IP 地理位置方面的原因而阻止了客户端。 <br> **UnspecifiedClientError**：常规客户端错误。 <br> **InvalidRequest**：请求无效。 此错误的可能原因是标头、正文和 URL 格式不正确。 <br> **DNSFailure**：DNS 错误。 <br> **DNSTimeout**：用于解析后端的 DNS 查询超时。 <br> **DNSNameNotResolved**：无法解析服务器名称或地址。 <br> **OriginConnectionAborted**：与源的连接异常断开。 <br> **OriginConnectionError**：常规源连接错误。 <br> **OriginConnectionRefused**：未与源建立连接。 <br> **OriginError**：常规源错误。 <br> **OriginInvalidRequest**：发送到源的请求无效。 <br> **ResponseHeaderTooBig**：源返回了过大的响应头。 <br> **OriginInvalidResponse**：** 源返回了无效或无法识别的响应。 <br> **OriginTimeout**：超过了源请求的超时期限。 <br> **ResponseHeaderTooBig**：源返回了过大的响应头。 <br> **RestrictedIP**：由于 IP 受限而阻止了请求。 <br> **SSLHandshakeError**：由于 SSL 握手失败，无法与源建立连接。 <br> **SSLInvalidRootCA**：RootCA 无效。 <br> SSLInvalidCipher：用于建立 HTTPS 连接的密码无效。 <br> **OriginConnectionAborted**：与源的连接异常断开。 <br> **OriginConnectionRefused**：未与源建立连接。 <br> **UnspecifiedError**：发生了与表中任何错误都不相符的错误。 |
| OriginURL | 请求所发送到的源的完整 URL。 由方案、主机头、端口、路径和查询字符串构成。 <br> **URL 重写**：如果规则集中存在 URL 重写规则，则路径将引用重写的路径。 <br> 边缘 POP 上的缓存：如果是边缘 POP 上的缓存命中，则源为 N/A。 <br> **大请求**：如果请求的内容很大，包含返回到源的多个分块请求，则此字段将对应于向源发出的第一个请求。 有关更多详细信息，请参阅“对象分块”。 |
| OriginIP | 为请求提供服务的源 IP。 <br> 边缘 POP 上的缓存命中：如果是边缘 POP 上的缓存命中，则源为 N/A。 <br> **大请求**：如果请求的内容很大，包含返回到源的多个分块请求，则此字段将对应于向源发出的第一个请求。 有关更多详细信息，请参阅“对象分块”。 |
| OriginName| 源的完整 DNS 名称（源 URL 中的主机名）。 <br> 边缘 POP 上的缓存命中：如果是边缘 POP 上的缓存命中，则源为 N/A。 <br> **大请求**：如果请求的内容很大，包含返回到源的多个分块请求，则此字段将对应于向源发出的第一个请求。 有关更多详细信息，请参阅“对象分块”。 |

## <a name="health-probe-log"></a>运行状况探测日志

运行状况探测日志提供每个失败探测的日志记录以帮助你诊断源。这些日志将提供可用来使源恢复正常运行的信息。 此日志可发挥作用的部分场景包括：

* 你发现 Azure Front Door 流量只是发送到了一部分源。 例如，共有四个源，但只有三个源在接收流量。 你想要知道源是否在接收探测，如果未接收，失败的原因是什么。  

* 你发现正常源的百分比低于预期，并想要知道哪个源失败以及失败的原因是什么。

### <a name="health-probe-log-properties"></a>运行状况探测日志属性

每个运行状况探测日志采用以下架构。

| 属性 | 说明 |
| --- | --- |
| HealthProbeId  | 用于标识请求的唯一 ID。 |
| 时间 | 探测完成时间 |
| HttpMethod | 运行状况探测请求使用的 HTTP 方法。 值包括 GET 和 HEAD，具体取决于运行状况探测配置。 |
| 结果 | 对源进行运行状况探测的状态，值包括 success 和其他错误文本。 |
| HttpStatusCode  | 从源返回的 HTTP 状态代码。 |
| ProbeURL（目标） | 请求所发送到的源的完整 URL。 由方案、主机头、路径和查询字符串构成。 |
| OriginName  | 请求所发送到的源。 如果源配置为 FDQN，此字段可帮助查找相关的源。 |
| POP | 发出探测请求的边缘 POP。 |
| 原始 IP | 目标源 IP。 如果使用 FDQN 配置源，则此字段对于查找相关的源会很有用。 |
| TotolaLatency | 从 AFDX 边缘向源发送请求到源向 AFDX 边缘发送最后一个响应所经历的时间。 |
| ConnectionLatency| 设置 TCP 连接以将 HTTP 探测请求发送到源所用的持续时间。 | 
| DNSResolution Latency | 当源配置为 FDQN 而不是 IP 时 DNS 解析所用的持续时间。 如果源配置为 IP，则为 N/A。 |

### <a name="health-probe-log-sample-in-json"></a>JSON 中的运行状况探测日志示例

`{ "records": [ { "time": "2021-02-02T07:15:37.3640748Z",
      "resourceId": "/SUBSCRIPTIONS/27CAFCA8-B9A4-4264-B399-45D0C9CCA1AB/RESOURCEGROUPS/AFDXPRIVATEPREVIEW/PROVIDERS/MICROSOFT.CDN/PROFILES/AFDXPRIVATEPREVIEW-JESSIE",
      "category": "FrontDoorHealthProbeLog",
      "operationName": "Microsoft.Cdn/Profiles/FrontDoorHealthProbeLog/Write",
      "properties": { "healthProbeId": "9642AEA07BA64675A0A7AD214ACF746E",
        "POP": "MAA",
        "httpVerb": "HEAD",
        "result": "OriginError",
        "httpStatusCode": "400",
        "probeURL": "http://afdxprivatepreview.blob.core.windows.net:80/",
        "originName": "afdxprivatepreview.blob.core.windows.net",
        "originIP": "52.239.224.228:80",
        "totalLatencyMilliseconds": "141",
        "connectionLatencyMilliseconds": "68",
        "DNSLatencyMicroseconds": "1814" } } ]
} `

## <a name="activity-logs"></a>活动日志

活动日志提供有关在 Azure Front Door 标准版/高级版中执行的操作的信息。 这些日志包括有关在 Azure Front Door 中执行的写入操作的内容、执行者和执行时间的详细信息。 

> [!NOTE]
> 活动日志不包括 GET 操作。 此外，也不包括使用 Azure 门户或原始管理 API 执行的操作。 

可以在 Azure Monitor 中访问 Front Door 中的活动日志或 Azure 资源的所有日志。

要查看活动日志，请执行以下操作：

1. 选择你的 Front Door 配置文件。

1. 选择“活动日志”。

1. 选择一个筛选范围，然后选择“应用”。

## <a name="next-steps"></a>后续步骤

- 了解 [Azure Front Door 标准版/高级版（预览版）报告](how-to-reports.md)。
- 了解 [Azure Front Door 标准版/高级版（预览版）实时监视指标](how-to-monitor-metrics.md)。
