---
title: 应用程序网关的 Azure Monitor 指标
description: 了解如何使用指标来监视应用程序网关的性能
services: application-gateway
author: azhar2005
ms.service: application-gateway
ms.topic: article
ms.date: 04/19/2021
ms.author: azhussai
ms.openlocfilehash: 2e448f907e129f628c4614c9df703bf2c39ea47a
ms.sourcegitcommit: aaaa6ee55f5843ed69944f5c3869368e54793b48
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2021
ms.locfileid: "113665090"
---
# <a name="metrics-for-application-gateway"></a>应用程序网关的指标

应用程序网关会将称为“指标”的数据点发布到 [Azure Monitor](../azure-monitor/overview.md)，使用户能够监视应用程序网关和后端实例的性能。 这些指标是一组有序时序数据中的数值，用于描述应用程序网关在特定时间的某种状况。 如果请求通过应用程序网关传送，则应用程序网关将会测量其指标并每隔 60 秒发送一次指标。 如果没有任何请求通过应用程序网关传送，或者指标没有数据，则不会报告指标。 有关详细信息，请参阅 [Azure Monitor 指标](../azure-monitor/essentials/data-platform-metrics.md)。

## <a name="metrics-supported-by-application-gateway-v2-sku"></a>应用程序网关 V2 SKU 支持的指标

### <a name="timing-metrics"></a>计时指标

应用程序网关提供多个与请求和响应相关的内置计时指标，这些指标的度量单位均为毫秒。 

:::image type="content" source="./media/application-gateway-metrics/application-gateway-metrics.png" alt-text="[用于应用程序网关的计时指标图" border="false":::

> [!NOTE]
>
> 如果应用程序网关中有多个侦听器，则请在比较不同的延迟指标时始终按“侦听器”维度进行筛选，以便获得有意义的推理结果。

- **后端连接时间**

  与后端应用程序建立连接所花费的时间。 

  这包括网络延迟，以及后端服务器的 TCP 堆栈建立新连接所用的时间。 对于 TLS，它还包括握手所用时间。 

- **后端第一个字节响应时间**

  从开始与后端服务器建立连接，到收到响应标头的第一个字节的间隔时间。 

  此时间近似于“后端连接时间”、请求从应用程序网关抵达后端所用的时间、后端应用程序做出响应所用的时间（服务器生成内容并可能提取数据库查询所用的时间），以及响应的第一个字节从后端抵达应用程序网关所用的时间之和。

- **后端最后一个字节响应时间**

  从开始与后端服务器建立连接，到收到响应正文的最后一个字节的间隔时间。 

  此时间近似于“后端第一字节响应时间”以及数据传输时间（此数字根据请求对象的大小和服务器网络延迟而有很大的不同）之和。

- **应用程序网关总时间**

  接收、处理请求及发送其响应所花费的平均时间。 

  此间隔时间是根据从应用程序网关收到 HTTP 请求的第一个字节的时间，到将最后一个响应字节发送到客户端的时间计算的。 这包括应用程序网关花费的处理时间、“后端最后一个字节响应时间”、应用程序网关发送所有响应所用的时间。

- **客户端 RTT**

  客户端与应用程序网关之间的平均往返时间。



这些指标可用于确定速度减慢的原因是与客户端网络、应用程序网关性能、后端网络和后端服务器 TCP 堆栈饱和、后端应用程序性能还是文件大小较大有关。

例如，如果“后端第一个字节响应时间”趋势存在高峰，但“后端连接时间”趋势稳定，则可以推断应用程序网关与后端之间存在网络延迟，但建立连接所用的时间是稳定的，而出现高峰的原因是后端应用程序的响应时间增大。  另一方面，如果“后端第一个字节响应时间”中的高峰与“后端连接时间”中的相应高峰相关联，则可以推断应用程序网关与后端服务器或后端服务器 TCP 堆栈之间的网络已饱和。  

如果观察到“后端最后一个字节响应时间”出现高峰，但“后端第一个字节响应时间”稳定，则可以推断出现此高峰的原因是请求的文件较大。 

同理，如果“应用程序网关总时间”出现高峰，但“后端最后一个字节响应时间”稳定，则可能表示应用程序网关出现性能瓶颈，或者客户端与应用程序网关之间的网络出现瓶颈。  此外，如果“客户端 RTT”也出现相应的高峰，则指示出现这种性能下降的原因是客户端与应用程序网关之间的网络有问题。

### <a name="application-gateway-metrics"></a>应用程序网关指标

应用程序网关支持以下指标：

- **接收的字节数**

   应用程序网关从客户端收到的字节数

- **发送的字节数**

   应用程序网关向客户端发送的字节数

- **客户端 TLS 协议**

   与应用程序网关建立了连接的客户端发起的 TLS 和非 TLS 请求计数。 若要查看 TLS 协议分布，请按“TLS 协议”维度进行筛选。

- **当前容量单位数**

   用于对流量进行负载均衡的容量单位计数。 容量单位有三个决定因素 - 计算单位、持久连接和吞吐量。 每个容量单位最多包括：1 个计算单位，或 2500 个持久连接，或 2.22-Mbps 吞吐量。

- **当前计算单位数**

   消耗的处理器容量计数。 影响计算单位的因素包括每秒 TLS 连接数、URL 重写计算和 WAF 规则处理。 

- **当前连接数**

   从客户端到应用程序网关的活动并发连接总数
   
- **估计计费容量单位数**

  使用 v2 SKU，定价模型由消耗量驱动。 容量单位测量在固定价格的基础上按消耗量计收的费用。 “估计计费容量单位数”指示用于估计计费的容量单位数。 此项按“当前容量单位数”（对流量进行负载均衡所需的容量单位数）和“固定计费容量单位数”（保持预配的最小容量单位数）之间的较大值进行计算。

- **失败的请求数**

  应用程序网关已处理但显示了 5xx 服务器错误代码的请求数。 这包括从应用程序网关生成的 5xx 代码，以及从后端生成的 5xx 代码。 可以进一步筛选请求计数，以显示每个/特定后端池 http 设置组合的计数。
   
- **固定计费容量单位数**

  根据应用程序网关配置中的“最小缩放单位数”设置（一个实例转换为 10 个容量单位）保持预配的最小容量单位数。
   
 - **每秒新连接数**

   每秒从客户端到应用程序网关以及从应用程序网关到后端成员建立的新 TCP 连接的平均数目。


- **响应状态**

   应用程序网关返回的 HTTP 响应状态。 可以进一步对响应状态代码分布进行归类来显示 2xx、3xx、4xx 和 5xx 类别的响应。

- **吞吐量**

   应用程序网关每秒提供的字节数

- **请求总数**

   应用程序网关已提供服务的成功请求计数。 可以进一步筛选请求计数，以显示每个/特定后端池 http 设置组合的计数。

### <a name="backend-metrics"></a>后端指标

应用程序网关支持以下指标：

- **后端响应状态**

  后端返回的 HTTP 响应状态代码计数。 这不包括应用程序网关生成的任何响应代码。 可以进一步对响应状态代码分布进行归类来显示 2xx、3xx、4xx 和 5xx 类别的响应。

- **正常的主机计数**

  由运行状况探测判定为正常的后端数。 可以按每个后端池进行筛选来显示特定后端池中的正常主机数。

- **不正常的主机计数**

  由运行状况探测判定为不正常的后端数。 可以按每个后端池进行筛选来显示特定后端池中的不正常主机数。
  
- **每个正常主机每分钟的请求数**

  后端池中的每个正常成员在一分钟内收到的平均请求数。 必须使用 BackendPool HttpSettings 维度指定后端池。  
  

## <a name="metrics-supported-by-application-gateway-v1-sku"></a>应用程序网关 V1 SKU 支持的指标

### <a name="application-gateway-metrics"></a>应用程序网关指标

应用程序网关支持以下指标：

- **CPU 使用率**

  显示分配给应用程序网关的 CPU 的使用率。  正常情况下，CPU 使用率不应经常超过 90%，因为这可能导致托管在应用程序网关后面的网站中出现延迟，并破坏客户端体验。 可以通过修改应用程序网关的配置（具体方法是：增加实例计数和/或移到更大的 SKU 大小）来间接控制或改进 CPU 使用率。

- **当前连接数**

  使用应用程序网关建立的当前连接计数

- **失败的请求数**

  因连接问题而失败的请求数。 此计数包括由于超过“请求超时”HTTP 设置而失败的请求，以及由于应用程序网关和后端之间的连接问题而失败的请求。 此计数不包括由于没有可用的正常后端而导致的失败。 来自后端的 4xx 和 5xx 响应也不被视为此指标的一部分。

- **响应状态**

  应用程序网关返回的 HTTP 响应状态。 可以进一步对响应状态代码分布进行归类来显示 2xx、3xx、4xx 和 5xx 类别的响应。

- **吞吐量**

  应用程序网关每秒提供的字节数

- **请求总数**

  应用程序网关已提供服务的成功请求计数。 可以进一步筛选请求计数，以显示每个/特定后端池 http 设置组合的计数。

- **Web 应用程序防火墙阻止的请求计数**
- **Web 应用程序防火墙阻止的请求分发**
- **Web 应用程序防火墙规则分发总数**

### <a name="backend-metrics"></a>后端指标

应用程序网关支持以下指标：

- **正常的主机计数**

  由运行状况探测判定为正常的后端数。 可以按每个后端池进行筛选来显示特定后端池中的正常主机数。

- **不正常的主机计数**

  由运行状况探测判定为不正常的后端数。 可以按每个后端池进行筛选来显示特定后端池中的不正常主机数。

## <a name="metrics-visualization"></a>指标可视化

浏览到应用程序网关，并在“监视”下选择“指标” 。 若要查看可用值，请选择“指标”下拉列表  。

在下图中可以看到过去 30 分钟显示的三个指标的示例：

:::image type="content" source="media/application-gateway-diagnostics/figure5.png" alt-text="指标视图。" lightbox="media/application-gateway-diagnostics/figure5-lb.png":::

若要查看当前的指标列表，请参阅 [Azure Monitor 支持的指标](../azure-monitor/essentials/metrics-supported.md)。

### <a name="alert-rules-on-metrics"></a>针对指标的警报规则

可基于资源的指标启动警报规则。 例如，如果应用程序网关的吞吐量在指定时间段内高于、低于或等于阈值，警报即可调用 webhook 或给管理员发送电子邮件。

以下示例指导创建警报规则，以在吞吐量违反阈值时给管理员发送电子邮件：

1. 选择“添加指标警报”，以打开“添加规则”页面 。 也可从指标页面访问此页面。

   ![“添加指标警报”按钮][6]

2. 在“添加规则”页面中，填写名称、条件和通知等部分，然后选择“确定” 。

   * 在“条件”选择器中，选择以下 4 个值之一：“大于”、“大于或等于”、“小于”或“小于或等于”。

   * 在“时间段”选择器中，选择 5 分钟到 6 小时之间的一个时间段。

   * 如果选择“电子邮件所有者、参与者和读者”，则电子邮件将基于有权访问该资源的用户动态发送。 否则，可以在“其他管理员电子邮件”框中提供用户名单并以逗号分隔。

   ![添加规则页][7]

如果违反阈值，用户将收到类似下图内容的电子邮件：

![违反阈值电子邮件][8]

创建指标警报后，会显示警报列表。 它提供所有警报规则概述。

![警报和规则列表][9]

若要了解有关警报通知的详细信息，请参阅[接收警报通知](../azure-monitor/alerts/alerts-overview.md)。

若要深入了解 webhook 以及如何将其与警报搭配使用，请参阅[针对 Azure 指标警报配置 webhook](../azure-monitor/alerts/alerts-webhooks.md)。

## <a name="next-steps"></a>后续步骤

* 使用 [Azure Monitor 日志](../azure-monitor/insights/azure-networking-analytics.md)可视化计数器和事件日志。
* [Visualize your Azure Activity Log with Power BI](https://powerbi.microsoft.com/blog/monitor-azure-audit-logs-with-power-bi/)（使用 Power BI 直观显示 Azure 活动日志）博客文章。
* [View and analyze Azure activity logs in Power BI and more](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/)（在 Power BI 和其他组件中查看和分析 Azure 活动日志）博客文章。

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png
