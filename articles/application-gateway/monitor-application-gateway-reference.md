---
title: 有关监视 Azure 应用程序网关数据的参考
description: 监视应用程序网关时需要了解的重要参考资料
author: vhorne
ms.author: victorh
ms.topic: conceptual
ms.service: application-gateway
ms.custom: subject-monitoring
ms.date: 06/10/2021
ms.openlocfilehash: 5ece5b220a59f562774fde7b093236ed5946f019
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124836243"
---
<!-- VERSION 2.2
Template for monitoring data reference article for Azure services. This article is support for the main "Monitoring [servicename]" article for the service. -->

# <a name="monitoring-azure-application-gateway-data-reference"></a>有关监视 Azure 应用程序网关数据的参考

有关收集和分析 Azure 应用程序网关监视数据的详细信息，请参阅[监视 Azure 应用程序网关](monitor-application-gateway.md)。

## <a name="metrics"></a>指标

<!-- REQUIRED if you support Metrics. If you don't, keep the section but call that out. Some services are only onboarded to logs.
<!-- Please keep headings in this order -->

<!--  OPTION 2 -  Link to the metrics as above, but work in extra information not found in the automated metric-supported reference article.  NOTE: YOU WILL NOW HAVE TO MANUALLY MAINTAIN THIS SECTION to make sure it stays in sync with the metrics-supported link. For highly customized example, see [CosmosDB](../cosmos-db/monitor-cosmos-db-reference.md#metrics). They even regroup the metrics into usage type vs. resource provider and type.
-->

<!-- Example format. Mimic the setup of metrics supported, but add extra information -->

### <a name="application-gateway-v2-metrics"></a>应用程序网关 v2 指标

资源提供程序和类型：[Microsoft.Compute/applicationGateways](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkapplicationgateways)

#### <a name="timing-metrics"></a>计时指标
应用程序网关提供多个与请求和响应相关的内置计时指标，这些指标的度量单位均为毫秒。

> [!NOTE]
>
> 如果应用程序网关中有多个侦听器，请在比较不同的延迟指标时始终按“侦听器”维度进行筛选，以获取更有意义的推理结果。


| 指标 | 计价单位 | 说明|
|:-------|:-----|:------------|
|**后端连接时间**|毫秒|与后端应用程序建立连接所花费的时间。<br><br>这包括网络延迟以及后端服务器的 TCP 堆栈建立新连接所用的时间。 对于 TLS，它还包括握手所用时间。|
|**后端第一个字节响应时间**|毫秒|从开始与后端服务器建立连接，到收到响应标头的第一个字节的间隔时间。<br><br>此时间近似于“后端连接时间”、请求从应用程序网关抵达后端所用的时间、后端应用程序做出响应所用的时间（服务器生成内容并可能提取数据库查询所用的时间），以及响应的第一个字节从后端抵达应用程序网关所用的时间之和。|
|**后端最后一个字节响应时间**|毫秒|从开始与后端服务器建立连接，到收到响应正文的最后一个字节的间隔时间。<br><br>此时间近似于后端第一字节响应时间以及数据传输时间之和。 此数字根据请求对象的大小和服务器网络延迟而有很大的不同。|
|**应用程序网关总时间**|毫秒|接收、处理请求及发送其响应所花费的平均时间。<br><br>此间隔时间是根据从应用程序网关收到 HTTP 请求的第一个字节的时间，到将最后一个响应字节发送到客户端的时间计算的。 这包括应用程序网关花费的处理时间、后端最后一个字节响应时间、应用程序网关发送所有响应花费的时间，以及客户端 RTT。|
|**客户端 RTT**|毫秒|客户端与应用程序网关之间的平均往返时间。|

这些指标可用于确定速度减慢的原因是与客户端网络、应用程序网关性能、后端网络和后端服务器 TCP 堆栈饱和、后端应用程序性能还是文件大小较大有关。

例如，如果“后端第一个字节响应时间”趋势存在高峰，但“后端连接时间”趋势稳定，则可以推断应用程序网关与后端之间存在网络延迟，但建立连接所用的时间是稳定的，而出现高峰的原因是后端应用程序的响应时间增大。  另一方面，如果“后端第一个字节响应时间”中的高峰与“后端连接时间”中的相应高峰相关联，则可以推断应用程序网关与后端服务器或后端服务器 TCP 堆栈之间的网络已饱和。  

如果观察到“后端最后一个字节响应时间”出现高峰，但“后端第一个字节响应时间”稳定，则可以推断出现此高峰的原因是请求的文件较大。 

同理，如果“应用程序网关总时间”出现高峰，但“后端最后一个字节响应时间”稳定，则可能表示应用程序网关出现性能瓶颈，或者客户端与应用程序网关之间的网络出现瓶颈。  此外，如果“客户端 RTT”也出现相应的高峰，则指示出现这种性能下降的原因是客户端与应用程序网关之间的网络有问题。

#### <a name="application-gateway-metrics"></a>应用程序网关指标

| 指标 | 计价单位 | 说明|
|:-------|:-----|:------------|
|**接收的字节数**|字节|应用程序网关从客户端收到的字节数。|
|**发送的字节数**|字节|应用程序网关向客户端发送的字节数。|
|**客户端 TLS 协议**|count|与应用程序网关建立了连接的客户端发起的 TLS 和非 TLS 请求计数。 若要查看 TLS 协议分布，请按“TLS 协议”维度进行筛选。|
|**当前容量单位数**|count|用于对流量进行负载均衡的容量单位计数。 容量单位有三个决定因素 - 计算单位、持久连接和吞吐量。 每个容量单位最多包括：1 个计算单位、2500 个持久连接，或 2.22-Mbps 吞吐量。|
|**当前计算单位数**|count|消耗的处理器容量计数。 影响计算单位的因素包括每秒 TLS 连接数、URL 重写计算和 WAF 规则处理。|
|**当前连接数**|count|从客户端到应用程序网关的活动并发连接总数。|
|**估计计费容量单位数**|count|使用 v2 SKU，定价模型由消耗量驱动。 容量单位测量在固定价格的基础上按消耗量计收的费用。 “估计计费容量单位数”指示用于估计计费的容量单位数。 此项按“当前容量单位数”（对流量进行负载均衡所需的容量单位数）和“固定计费容量单位数”（保持预配的最小容量单位数）之间的较大值进行计算。|
|**失败的请求数**|count|应用程序网关已处理但显示了 5xx 服务器错误代码的请求数。 这包括从应用程序网关生成的 5xx 代码，以及从后端生成的 5xx 代码。 可以进一步筛选请求计数，以显示每个/特定后端池 http 设置组合的计数。|
|**固定计费容量单位数**|count|根据应用程序网关配置中的“最小缩放单位数”设置（一个实例转换为 10 个容量单位）保持预配的最小容量单位数。|
|**每秒新连接数**|count|每秒从客户端到应用程序网关以及从应用程序网关到后端成员建立的新 TCP 连接的平均数目。|
|**响应状态**|状态代码|应用程序网关返回的 HTTP 响应状态。 可以进一步对响应状态代码分布进行归类来显示 2xx、3xx、4xx 和 5xx 类别的响应。|
|**吞吐量**|字节/秒|应用程序网关每秒提供的字节数。|
|**请求总数**|count|应用程序网关已提供服务的成功请求计数。 可以进一步筛选请求计数，以显示每个/特定后端池 http 设置组合的计数。|

#### <a name="backend-metrics"></a>后端指标

| 指标 | 计价单位 | 说明|
|:-------|:-----|:------------|
|**后端响应状态**|count|后端返回的 HTTP 响应状态代码计数。 这不包括应用程序网关生成的任何响应代码。 可以进一步对响应状态代码分布进行归类来显示 2xx、3xx、4xx 和 5xx 类别的响应。|
|**正常的主机计数**|count|由运行状况探测判定为正常的后端数。 可以按每个后端池进行筛选来显示特定后端池中的正常主机数。|
|**不正常的主机计数**|count|由运行状况探测判定为不正常的后端数。 可以按每个后端池进行筛选来显示特定后端池中的不正常主机数。|
|**每个正常主机每分钟的请求数**|count|后端池中的每个正常成员在一分钟内收到的平均请求数。 必须使用 BackendPool HttpSettings 维度指定后端池。|


### <a name="application-gateway-v1-metrics"></a>应用程序网关 v1 指标

#### <a name="application-gateway-metrics"></a>应用程序网关指标

| 指标 | 计价单位 | 说明|
|:-------|:-----|:------------|
|**CPU 使用率**|%|显示分配给应用程序网关的 CPU 的使用率。 正常情况下，CPU 使用率不应经常超过 90%，因为这可能导致托管在应用程序网关后面的网站中出现延迟，并破坏客户端体验。 可以通过修改应用程序网关的配置（具体方法是：增加实例计数和/或移到更大的 SKU 大小）来间接控制或改进 CPU 使用率。|
|**当前连接数**|count|使用应用程序网关建立的当前连接计数。|
|**失败的请求数**|count|因连接问题而失败的请求数。 此计数包括由于超过“请求超时”HTTP 设置而失败的请求，以及由于应用程序网关和后端之间的连接问题而失败的请求。 此计数不包括由于没有可用的正常后端而导致的失败。 来自后端的 4xx 和 5xx 响应也不被视为此指标的一部分。|
|**响应状态**|状态代码|应用程序网关返回的 HTTP 响应状态。 可以进一步对响应状态代码分布进行归类来显示 2xx、3xx、4xx 和 5xx 类别的响应。|
|**吞吐量**|字节/秒|应用程序网关每秒提供的字节数。|
|**请求总数**|count|应用程序网关已提供服务的成功请求计数。 可以进一步筛选请求计数，以显示每个/特定后端池 http 设置组合的计数。|
|**Web 应用程序防火墙阻止的请求计数**|count|WAF 阻止的请求数。|
|**Web 应用程序防火墙阻止的请求分发**|count|筛选后的 WAF 阻止的请求数，显示每个/特定 WAF 规则组或 WAF 规则 ID 组合的计数。|
|**Web 应用程序防火墙规则分发总数**|count|每个特定 WAF 规则组或 WAF 规则 ID 组合收到的请求数。|


<!-- Keep this text as-is -->
有关详细信息，请参阅 [Azure Monitor 支持的所有平台指标的列表](../azure-monitor/essentials/metrics-supported.md)。



## <a name="metric-dimensions"></a>指标维度

<!-- REQUIRED. Please  keep headings in this order -->
<!-- If you have metrics with dimensions, outline it here. If you have no dimensions, say so.  Questions email azmondocs@microsoft.com -->

有关指标维度定义的详细信息，请参阅[多维指标](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics)。


<!-- See https://docs.microsoft.com/azure/storage/common/monitor-storage-reference#metrics-dimensions for an example. Part is copied below. -->

Azure 应用程序网关支持 Azure Monitor 中某些指标的维度。 每个指标附带说明，解释可专用于该指标的维度。


## <a name="resource-logs"></a>资源日志
<!-- REQUIRED. Please  keep headings in this order -->

本部分列出了可为 Azure 应用程序网关收集的资源日志类型。 

<!-- List all the resource log types you can have and what they are for -->  

有关参考，请参阅 [Azure Monitor 支持的所有资源日志类别类型](../azure-monitor/essentials/resource-logs-schema.md)列表。

> [!NOTE]
> 性能日志仅适用于 v1 SKU。 对于 v2 SKU，请对性能数据使用[指标](#metrics)。

有关详细信息，请参阅[应用程序网关的后端运行状况和诊断日志](application-gateway-diagnostics.md#access-log)

<!--  OPTION 2 -  Link to the resource logs as above, but work in extra information not found in the automated metric-supported reference article.  NOTE: YOU WILL NOW HAVE TO MANUALLY MAINTAIN THIS SECTION to make sure it stays in sync with the resource-log-categories link. You can group these sections however you want provided you include the proper links back to resource-log-categories article. 
-->

<!-- Example format. Add extra information -->

### <a name="application-gateway"></a>应用程序网关

资源提供程序和类型：[Microsoft.Network/applicationGateways](../azure-monitor/essentials/resource-logs-categories.md#microsoftnetworkapplicationgateways)

| 类别 | 显示名称 | 信息|
|:---------|:-------------|------------------|
| Activitylog   | 活动日志 | 默认会收集活动日志条目。 可使用 [Azure 活动日志](../azure-monitor/essentials/activity-log.md)（以前称为操作日志和审核日志）查看提交到 Azure 订阅的所有操作及其状态。 |
|**ApplicationGatewayAccessLog**|访问日志| 可以使用此日志查看应用程序网关访问模式并分析重要信息。 这包括调用方的 IP 地址、请求的 URL、响应延迟、返回代码，以及传入和传出的字节数。访问日志每 60 秒收集一次。 此日志包含每个应用程序网关实例的一条记录。 应用程序网关实例由 instanceId 属性标识。|
| **ApplicationGatewayPerformanceLog**|性能日志|可使用此日志查看应用程序网关实例的执行情况。 此日志会捕获每个实例的性能信息，包括服务的总请求数、吞吐量（以字节为单位）、失败请求计数、正常和不正常的后端实例计数。 每隔 60 秒会收集一次性能日志。 性能日志仅适用于 v1 SKU。 对于 v2 SKU，请对性能数据使用[指标](#metrics)。|
|**ApplicationGatewayFirewallLog**|防火墙日志|可使用此日志查看通过应用程序网关（配置有 Web 应用程序防火墙）的检测模式或阻止模式记录的请求。 防火墙日志每 60 秒收集一次。|


## <a name="azure-monitor-logs-tables"></a>Azure Monitor 日志表
<!-- REQUIRED. Please keep heading in this order -->

本部分介绍与 Azure 应用程序网关相关的并可通过 Log Analytics 查询的所有 Azure Monitor 日志 Kusto 表。 


<!-- OPTION 1 - Minimum -  Link to relevant bookmarks in https://docs.microsoft.com/azure/azure-monitor/reference/tables/tables-resourcetype where your service tables are listed. These files are auto generated from the REST API.   If this article is missing tables that you and the PM know are available, both of you contact azmondocs@microsoft.com.  
-->

<!-- Example format. There should be AT LEAST one Resource Provider/Resource Type here. -->

|资源类型 | 说明 |
|-------|-----|
| [应用程序网关](/azure/azure-monitor/reference/tables/tables-resourcetype#application-gateways) |包括 AzureActivity、AzureDiagnostics 和 AzureMetrics |


有关所有 Azure Monitor 日志/Log Analytics 表的参考，请参阅 [Azure Monitor 日志表参考](/azure/azure-monitor/reference/tables/tables-resourcetype)。

### <a name="diagnostics-tables"></a>诊断表
<!-- REQUIRED. Please keep heading in this order -->
<!-- If your service uses the AzureDiagnostics table in Azure Monitor Logs / Log Analytics, list what fields you use and what they are for. Azure Diagnostics is over 500 columns wide with all services using the fields that are consistent across Azure Monitor and then adding extra ones just for themselves.  If it uses service specific diagnostic table, refers to that table. If it uses both, put both types of information in. Most services in the future will have their own specific table. If you have questions, contact azmondocs@microsoft.com -->

Azure 应用程序网关使用 [Azure 诊断](/azure/azure-monitor/reference/tables/azurediagnostics)表来存储资源日志信息。 以下列与此相关。

**Azure 诊断**

| 属性 | 说明 |
|:--- |:---|
requestUri_s | 客户端请求的 URI。|
消息 | 信息性消息，例如“SQL 注入攻击”|
userAgent_s | 客户端请求的用户代理详细信息|
ruleName_s | 用来为此请求提供服务的请求路由规则|
httpMethod_s | 客户端请求的 HTTP 方法|
instanceId_s | 要将客户端请求路由到其中以进行评估的 Appgw 实例|
httpVersion_s | 客户端请求的 HTTP 版本|
clientIP_s | 发出请求的 IP|
host_s | 客户端请求的主机头|
requestQuery_s | 作为客户端请求的一部分的查询字符串|
sslEnabled_s | 是否为客户端请求启用了 SSL|


## <a name="see-also"></a>另请参阅

<!-- replace below with the proper link to your main monitoring service article -->
- 有关监视 Azure 应用程序网关的介绍，请参阅[监视 Azure 应用程序网关](monitor-application-gateway.md)。
- 有关监视 Azure 资源的详细信息，请参阅[使用 Azure Monitor 监视 Azure 资源](../azure-monitor/essentials/monitor-azure-resource.md)。