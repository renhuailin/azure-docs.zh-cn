---
title: Azure 前门标准/高级 (预览版) 日志记录
description: 本文介绍如何在 Azure 前门标准/高级版中进行日志记录。
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: article
ms.date: 02/18/2020
ms.author: duau
ms.openlocfilehash: 6a1cf3112cd936ec842c755eb90b2c7b094aa781
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2021
ms.locfileid: "101098448"
---
# <a name="azure-front-door-standardpremium-preview-logging"></a>Azure 前门标准/高级 (预览版) 日志记录

> [!Note]
> 本文档适用于 Azure 前门标准/高级 (预览版) 。 正在寻找有关 Azure 前门的信息？ 查看 [此处](../front-door-overview.md)。

Azure 前门提供不同的日志记录，以帮助你跟踪、监视和调试前门。 

* 访问日志包含 AFD 收到的每个请求的详细信息，帮助你分析和监视访问模式以及调试问题。 
* 活动日志让你能够了解对 Azure 资源执行的操作。  
* 运行状况探测日志为源的每个失败的探测提供日志。 
* Web 应用程序防火墙 (WAF) 日志提供通过 Azure 前门终结点的检测或阻止模式记录的请求的详细信息。 还可以通过这些日志查看配置了 WAF 的自定义域。

> [!IMPORTANT]
> Azure 前门标准/高级 (预览版) 目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

默认情况下，不会启用访问日志、运行状况探测日志和 WAF 日志。 使用以下步骤启用日志记录。 默认情况下会收集活动日志条目，可在 Azure 门户中查看这些条目。 日志可能有几分钟的延迟。 

可通过三种方式存储日志： 

* **存储帐户：** 如果日志存储时间较长，并在需要时查看，最好使用存储帐户。 
* **事件中心：** 事件中心是一种很好的选择，用于与其他安全信息和事件管理 (SIEM) 工具或外部数据存储区集成。 例如： Splunk/DataDog/Sumo logic。 
* **Azure Log Analytics：** Azure Monitor 中的 azure Log Analytics 最适用于对 Azure 前门性能进行一般实时监视和分析。

## <a name="configure-logs"></a>配置日志

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 搜索 "Azure 前门标准/高级"，并选择 "Azure 前门" 配置文件。

1. 在配置文件中，请参阅 " **监视**"，然后选择 " **诊断设置**"。 选择“添加诊断设置”。

   :::image type="content" source="../media/how-to-logging/front-door-logging-1.png" alt-text="诊断设置登录页的屏幕截图。":::

1. 在 " **诊断设置**" 下，输入  **诊断设置名称** 的名称。

1. 选择 **FrontDoorAccessLog**、 **FrontDoorHealthProbeLog** 和 **FrontDoorWebApplicationFirewallLog** 中的  **日志**。

1. 选择 **目标详细信息**。 目标选项有： 

    * 发送到 Log Analytics
        * 选择“订阅”和“Log Analytics 工作区” 。
    * 存档到存储帐户
        * 选择“订阅”和“存储帐户” 。 并将 **保留期 (天)**。
    * **流式传输到事件中心**
        * 选择 " *订阅"、"事件中心命名空间"、"事件中心名称" (可选)*"和" *事件中心策略名称*"。 

     :::image type="content" source="../media/how-to-logging/front-door-logging-2.png" alt-text="诊断设置页的屏幕截图。":::

1. 单击“保存”  。

## <a name="access-log"></a>访问日志

Azure 前门目前提供单个 API 请求，其中每个条目具有以下架构并按 JSON 格式登录，如下所示。 

| 属性 | 说明 |
|----------|-------------| 
| TrackingReference | 唯一的引用字符串，用于标识 AFD 提供的请求，同时作为 X-Azure 引用标头发送到客户端。 是搜索特定请求访问日志中的详细信息必需的。 |
| 时间 | AFD 边缘将请求内容传递给客户端 (UTC) 的日期和时间。 |
| HttpMethod | 请求使用的 HTTP 方法： DELETE、GET、HEAD、OPTIONS、PATCH、POST 或 PUT。 |
| HttpVersion | 请求中指定的查看器的 HTTP 版本。 |
| RequestUri | 已收到请求的 URI。 此字段是完整的方案、端口、域、路径和查询字符串 |
| HostName | 来自客户端的请求中的主机名。 如果启用自定义域并将通配符域 ( *. contoso.com) ，则主机名为 a.contoso.com。 如果使用 Azure 前门域 (contoso.azurefd.net) ，则主机名为 contoso.azurefd.net。 |
| RequestBytes | HTTP 请求消息的大小（以字节为单位），包括请求标头和请求正文。 查看器包含在请求中的数据字节数，包括标头。 |
| ResponseBytes | 后端服务器作为响应发送的字节数。 |
| UserAgent | 客户端使用的浏览器类型。 |
| ClientIp | 发出原始请求的客户端的 IP 地址。 如果请求中有一个 X 转发的标头，则从同一获取客户端 IP。 |
| SocketIp | 与 AFD 边缘的直接连接的 IP 地址。 如果客户端使用 HTTP 代理或负载均衡器来发送请求，则 SocketIp 的值为代理或负载均衡器的 IP 地址。 |
| 延迟 | 从 AFD edge 服务器到 AFD 将最后一字节的响应发送到客户端的时间（以毫秒为单位）的时间长度。 此字段不考虑网络延迟和 TCP 缓冲。 |
| Owin.requestprotocol | 客户端在请求中指定的协议： HTTP、HTTPS。 |
| SecurityProtocol | 请求所使用的 TLS/SSL 协议版本，如果没有加密，则为 null。 可能的值包括： SSLv3、TLSv1、Tlsv1.1 1.1、Tlsv1.1 1。2 |
| SecurityCipher | 当 "请求协议" 的值为 "HTTPS" 时，此字段将指示客户端协商的 TLS/SSL 密码，以及用于加密的 AFD。 |
| 终结点 | AFD 终结点的域名，例如，contoso.z01.azurefd.net |
| HttpStatusCode | 从 AFD 返回的 HTTP 状态代码。 |
| Pop | 响应用户请求的边缘 pop。  |
| 缓存状态 | 提供 CDN 服务处理缓存时如何处理请求的状态代码。 可能的值被命中： HTTP 请求是通过 AFD edge POP 缓存提供的。 <br> 未 **命中**： HTTP 请求是从源提供的。 <br/> **PARTIAL_HIT**：请求中的某些字节来自 AFD edge POP 缓存，而某些字节为对象分块方案源提供。 <br> **CACHE_NOCONFIG**：不使用缓存设置转发请求，包括绕过方案。 <br/> **PRIVATE_NOSTORE**：客户在缓存设置中未配置缓存。 <br> **REMOTE_HIT**：请求是由父节点缓存提供的。 <br/> **N/A**:* * 已被签名的 URL 和规则集拒绝的请求。 |
| MatchedRulesSetName | 已处理规则的名称。 |
| RouteName | 请求匹配的路由的名称。 |
| ClientPort | 发出请求的客户端的 IP 端口。 |
| Referrer | 发起请求的站点的 URL。 |
| 时间 | 从 AFD 的时间（以毫秒为单位）收到请求到发送到客户端的第一个字节的时间，以 Azure 前门为衡量。 此属性不度量客户端数据。 |
| ErrorInfo | 此字段提供每个响应的错误令牌的详细信息。 <br> **NoError**：指示未发现任何错误。 <br> **CertificateError**：通用 SSL 证书错误。 <br> **CertificateNameCheckFailed**： SSL 证书中的主机名无效或不匹配。 <br> **ClientDisconnected**：由于客户端网络连接而导致请求失败。 <br> **ClientGeoBlocked**：客户端由于 IP 的地理位置而被阻止。 <br> **UnspecifiedClientError**：泛型客户端错误。 <br> **InvalidRequest**：请求无效。 由于标头、正文和 URL 格式不正确，可能会发生这种情况。 <br> **DNSFailure**： DNS 故障。 <br> **DNSTimeout**：用于解析后端的 DNS 查询超时。 <br> **DNSNameNotResolved**：无法解析服务器名称或地址。 <br> **OriginConnectionAborted**：与源的连接已异常断开连接。 <br> **OriginConnectionError**：泛型源连接错误。 <br> **OriginConnectionRefused**：未建立与源的连接。 <br> **OriginError**：泛型源错误。 <br> **OriginInvalidRequest**：发送到源的请求无效。 <br> **ResponseHeaderTooBig**：原点返回了太大的响应标头。 <br> **OriginInvalidResponse**:* * 源返回的响应无效或无法识别。 <br> **OriginTimeout**：源请求的超时期限已过期。 <br> **ResponseHeaderTooBig**：原点返回了太大的响应标头。 <br> **RestrictedIP**：由于限制了 IP，请求被阻止。 <br> **SSLHandshakeError**：由于 SSL 错误，无法建立与源的连接。 <br> **SSLInvalidRootCA**： rootca.cer 无效。 <br> **SSLInvalidCipher**：密码无效，HTTPS 连接已建立。 <br> **OriginConnectionAborted**：与源的连接已异常断开连接。 <br> **OriginConnectionRefused**：未建立与源的连接。 <br> **UnspecifiedError**：出现错误，因为表中的任何错误都不适合。 |
| OriginURL | 发送请求的源的完整 URL。 由方案、主机标头、端口、路径和查询字符串组成。 <br> **Url 重写**：如果规则集中存在 url 重写规则，则 path 引用重写的路径。 <br> **边缘 POP 缓存** 如果是在边缘 POP 上命中缓存，则原点为 N/A。 <br> **大请求** 如果请求的内容太大，并且多个分块请求返回到源，此字段将对应于对源的第一个请求。 有关详细信息，请参阅对象分块了解更多详细信息。 |
| OriginIP | 为请求提供服务的源 IP。 <br> **边缘 POP 上的缓存命中** 如果是在边缘 POP 上命中缓存，则原点为 N/A。 <br> **大请求** 如果请求的内容太大，并且多个分块请求返回到源，此字段将对应于对源的第一个请求。 有关详细信息，请参阅对象分块了解更多详细信息。 |
| OriginName| 源 URL 中 (主机名) 到源的完整 DNS 名称。 <br> **边缘 POP 上的缓存命中** 如果是在边缘 POP 上命中缓存，则原点为 N/A。 <br> **大请求** 如果请求的内容太大，并且多个分块请求返回到源，此字段将对应于对源的第一个请求。 有关详细信息，请参阅对象分块了解更多详细信息。 |

## <a name="health-probe-log"></a>运行状况探测日志

运行状况探测日志提供每个失败探测的日志记录，以帮助你诊断源。这些日志将为你提供可用于使来源返回到服务的信息。 此日志可用于的某些方案为：

* 已注意到，Azure 前门流量已发送到某些来源。 例如，只有三个源接收流量。 要了解源是否正在接收探测，以及失败的原因。  

* 你注意到原始运行状况% 低于预期，并且想要知道哪个源失败和失败的原因。

### <a name="health-probe-log-properties"></a>运行状况探测日志属性

每个运行状况探测日志包含以下架构。

| 属性 | 描述 |
| --- | --- |
| HealthProbeId  | 用于标识请求的唯一 ID。 |
| 时间 | 探测完成时间 |
| HttpMethod | 运行状况探测请求使用的 HTTP 方法。 根据运行状况探测配置，值包括 GET 和 HEAD。 |
| 结果 | 运行状况探测到源的状态，值包括成功和其他错误文本。 |
| HttpStatusCode  | 从源返回的 HTTP 状态代码。 |
| ProbeURL (目标)  | 发送请求的源的完整 URL。 由方案、主机标头、路径和查询字符串组成。 |
| OriginName  | 要向其发送请求的源。 如果源配置为 FDQN，此字段可帮助查找目标位置。 |
| POP | 边缘 pop，它发出探测请求。 |
| 原始 IP | 目标源 IP。 如果使用 FDQN 配置源，则此字段对于查找感兴趣的源很有用。 |
| TotolaLatency | AFDX 边缘的时间将请求发送到源，时间源发送上一次响应到 AFDX 边缘。 |
| ConnectionLatency| 设置 TCP 连接以将 HTTP 探测请求发送到源所用的持续时间。 | 
| DNSResolution 延迟 | 如果源配置为 FDQN 而不是 IP，则为 DNS 解析所用的持续时间。 如果源配置为 IP，则为 N/A。 |

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

活动日志提供有关在 Azure 前门标准/高级版上完成的操作的信息。 日志包含有关在 Azure 前门上完成写操作的人员和时间的详细信息。 

> [!NOTE]
> 活动日志不包括 GET 操作。 它们还不包括使用 Azure 门户或原始管理 API 执行的操作。 

在 Azure Monitor 中访问你的前端或 Azure 资源的所有日志中的活动日志。

要查看活动日志，请执行以下操作：

1. 选择前门配置文件。

1. 选择 " **活动日志"。**

1. 选择 "筛选范围"，然后选择 " **应用**"。

## <a name="next-steps"></a>后续步骤

- 了解 [Azure 前门标准/高级 (预览版) 报表](how-to-reports.md)。
- 了解 [Azure 前门标准/高级 (预览版) 实时监视指标](how-to-monitor-metrics.md)。
