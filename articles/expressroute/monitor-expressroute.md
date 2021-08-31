---
title: 监视 Azure ExpressRoute
description: 从此处开始了解如何监视 Azure ExpressRoute。
services: expressroute
author: duongau
ms.author: duau
ms.service: expressroute
ms.topic: how-to
ms.custom: subject-monitoring
ms.date: 06/22/2021
ms.openlocfilehash: 4f07659381c852e35585b5127d97c932a05088c8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121735268"
---
# <a name="monitoring-azure-expressroute"></a>监视 Azure ExpressRoute

当你的关键应用程序和业务流程依赖于 Azure 资源时，你需要监视这些资源的可用性、性能和操作。 

本文介绍 Azure ExpressRoute 生成的监视数据。 Azure ExpressRoute 使用 [Azure Monitor](../azure-monitor/overview.md)。 如果你不熟悉所有 Azure 服务普遍使用的 Azure Monitor 功能，请参阅[使用 Azure Monitor 监视 Azure 资源](../azure-monitor/essentials/monitor-azure-resource.md)。
 
## <a name="expressroute-insights"></a>ExpressRoute 见解

Azure 中的某些服务在 Azure 门户中具有一个特殊且醒目的预生成仪表板，可以从此仪表板着手监视服务。 这些特殊仪表板称为“见解”。

ExpressRoute 使用网络见解来提供彼此相关的所有 ExpressRoute 组件（对等互连、连接、网关）的详细拓扑映射。 用于 ExpressRoute 的网络见解还具有预加载的指标仪表板，用于提供可用性、吞吐量、数据包丢弃率和网关指标。 有关详细信息，请参阅[使用网络见解的 Azure ExpressRoute 见解](expressroute-network-insights.md)。

## <a name="monitoring-data"></a>监视数据 

Azure ExpressRoute 收集与[监视 Azure 资源中的数据](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)中所述的其他 Azure 资源相同的监视数据。 

有关 Azure ExpressRoute 创建的指标和日志指标的详细信息，请参阅[监视 Azure ExpressRoute 数据参考](monitor-expressroute-reference.md)。

## <a name="collection-and-routing"></a>收集和路由

平台指标和活动日志会自动收集和存储，但你可以使用诊断设置将其路由到其他位置。  

在创建诊断设置并将其路由到一个或多个位置之前，不会收集和存储资源日志。

有关使用 Azure 门户、CLI 或 PowerShell 创建诊断设置的详细过程，请参阅[创建诊断设置以收集 Azure 中的平台日志和指标](../azure-monitor/essentials/diagnostic-settings.md)。 创建诊断设置时，请指定要收集的日志类别。 [Azure ExpressRoute 监视数据参考](monitor-expressroute-reference.md#resource-logs)中列出了 Azure ExpressRoute 类别。

> [!IMPORTANT]
> 启用这些设置需要额外的 Azure 服务（存储帐户、事件中心或 Log Analytics），这可能会增加成本。 若要估算成本，请访问 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator)。

以下部分将讨论可以收集的指标和日志。

## <a name="analyzing-metrics"></a>分析指标

可以从“Azure Monitor”菜单中打开“指标”，通过使用指标资源管理器的其他 Azure 服务的指标来为 Azure ExpressRoute 分析指标。 有关使用此工具的详细信息，请参阅 [Azure 指标资源管理器入门](../azure-monitor/essentials/metrics-getting-started.md)。 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/metrics-page.png" alt-text="适用于 ExpressRoute 的指标仪表板的屏幕截图。":::

若要参考，可以查看 [Azure Monitor 中所有受支持的资源指标](../azure-monitor/essentials/metrics-supported.md)列表。

* 若要查看 ExpressRoute 指标，请按资源类型“ExpressRoute 线路”进行筛选   。 
* 若要查看 **Global Reach** 指标，请按资源类型“ExpressRoute 线路”进行筛选，然后选择一个已启用 Global Reach 的 ExpressRoute 线路资源。  
* 若要查看 **ExpressRoute Direct** 指标，请按“ExpressRoute 端口”筛选资源类型。  

选择指标后，将应用默认聚合。 （可选）可以应用拆分，它将显示具有不同维度的指标。

### <a name="aggregation-types"></a>聚合类型

指标资源管理器支持 SUM、MAX、MIN、AVG 和 COUNT 作为[聚合类型](../azure-monitor/essentials/metrics-charts.md#aggregation)。 查看每个 ExpressRoute 指标的见解时，请使用建议的聚合类型。

* 总和：在聚合间隔期间捕获的所有值的总和。 
* 计数：在聚合间隔期间捕获的度量数。 
* Average：在聚合间隔期间捕获的指标值的平均值。 
* 最小值：在聚合间隔期间捕获的最小值。 
* 最大值：在聚合间隔期间捕获的最大值。

>[!NOTE]
>建议不要使用 **经典指标**。
>

### <a name="circuits-metrics"></a>线路指标

#### <a name="bits-in-and-out---metrics-across-all-peerings"></a>进位和出位 - 所有对等互连的指标

聚合类型：Avg

可以查看给定 ExpressRoute 线路上所有对等互连的指标。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg" alt-text="线路指标":::

#### <a name="bits-in-and-out---metrics-per-peering"></a>进位和出位 - 每个对等互连的指标

聚合类型：Avg

可以查看专用、公共和 Microsoft 对等互连的指标（以位/秒为单位）。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg" alt-text="每个对等互连的指标":::

#### <a name="bgp-availability---split-by-peer"></a>BGP 可用性 - 按对等机拆分  

聚合类型：Avg

可以查看跨对等互连和对等机（主要和辅助 ExpressRoute 路由器）的近实时 BGP（第 3 层连接性）可用性。 此仪表板显示专用对等互连的主 BGP 会话状态为“已启动”，专用对等互连的第二个 BGP 会话状态为“关闭”。 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg" alt-text="每个对等机的 BGP 可用性":::

### <a name="arp-availability---split-by-peering"></a>ARP 可用性 - 按对等互连拆分  

聚合类型：Avg

可以查看跨对等互连和对等机（主要和辅助 ExpressRoute 路由器）的近实时 [ARP](./expressroute-troubleshooting-arp-resource-manager.md)（第 3 层连接性）可用性。 此仪表板显示跨两个对等机的专用对等互连 ARP 会话状态为“已启动”，但对于两个对等机的 Microsoft 对等互连，状态则为“关闭”。 已跨两个对等机利用了默认聚合（平均）。  

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg" alt-text="每个对等机的 ARP 可用性":::

### <a name="expressroute-direct-metrics"></a>ExpressRoute Direct 指标

#### <a name="admin-state---split-by-link"></a>管理状态 - 按链路拆分

聚合类型：Avg

可以查看 ExpressRoute Direct 端口对的每个链路的管理状态。 管理状态表示物理端口是打开还是关闭。 跨 ExpressRoute Direct 连接传递流量时需要此状态。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg" alt-text="ER Direct 管理状态":::

#### <a name="bits-in-per-second---split-by-link"></a>每秒传入位数 - 按链路拆分

聚合类型：Avg

可以查看 ExpressRoute Direct 端口对的跨两个链路的每秒传入位数。 监视此仪表板以比较这两个链接的入站带宽。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg" alt-text="ER Direct 每秒传入位数":::

#### <a name="bits-out-per-second---split-by-link"></a>每秒传出位数 - 按链路拆分

聚合类型：Avg

还可以查看 ExpressRoute Direct 端口对的跨两个链路的每秒传出位数。 监视此仪表板以比较这两个链接的出站带宽。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg" alt-text="ER Direct 每秒传出位数":::

#### <a name="line-protocol---split-by-link"></a>线路协议 - 按链路拆分

聚合类型：Avg

可以查看 ExpressRoute Direct 端口对的跨每个链路的线路协议。 “线路协议”指示物理链路是否已启动以及是否通过 ExpressRoute Direct 运行。 监视此仪表板并设置警报，以了解物理连接是否已经关闭。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg" alt-text="ER Direct 线路协议":::

#### <a name="rx-light-level---split-by-link"></a>Rx 轻型级别 - 按链路拆分

聚合类型：Avg

可以查看每个端口的 Rx 轻型级别（ExpressRoute Direct 端口的轻型级别是 **接收**）。 正常的 Rx 光能级通常在 -10 dBm 到 0 dBm 范围内。 设置警报，以在 Rx 光能级超出正常范围时接收通知。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg" alt-text="ER Direct 线路 Rx 轻型级别":::

#### <a name="tx-light-level---split-by-link"></a>Tx 轻型级别 - 按链路拆分

聚合类型：Avg

可以查看每个端口的 Tx 轻型级别（ExpressRoute Direct 端口的轻型级别为 **传输**）。 正常的 Tx 光能级通常在 -10 dBm 到 0 dBm 范围内。 设置警报，以在 Tx 光能级超出正常范围时接收通知。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg" alt-text="ER Direct 线路 Tx 轻型级别":::

### <a name="expressroute-virtual-network-gateway-metrics"></a>ExpressRoute 虚拟网络网关指标

聚合类型：Avg

部署 ExpressRoute 网关时，Azure 会管理网关的计算和功能。 用户可以使用六个网关指标来更好地了解你的网关的性能：

* CPU 使用率
* 每秒数据包数
* 播发到对等机的路由计数
* 从对等机获知的路由计数
* 路由更改频率
* 虚拟网络中的 VM 数量  

强烈建议为每个指标设置警报，以便了解网关在什么情况下会出现性能问题。

#### <a name="cpu-utilization---split-instance"></a>CPU 使用率 - 拆分实例

聚合类型：Avg

用户可以查看每个网关实例的 CPU 利用率。 在例行主机维护过程中，CPU 利用率可能会迅速激增，但 CPU 利用率长时间居高不下，可能表明网关达到性能瓶颈。 增加 ExpressRoute 网关的大小可以解决此问题。 针对 CPU 利用率超过特定阈值的频率设置警报。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/cpu-split.jpg" alt-text="CPU 利用率 - 拆分指标的屏幕截图。":::

#### <a name="packets-per-second---split-by-instance"></a>每秒数据包数 - 按实例拆分

聚合类型：Avg

此指标捕获遍历 ExpressRoute 网关的入站数据包的数目。 如果网关接收来自本地网络的流量，用户应该在这里看到一致的数据流。 针对每秒的数据包数量低于某个阈值时设置警报，指示网关不再接收流量。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/pps-split.jpg" alt-text="每秒数据包数量 - 拆分指标的屏幕截图。":::

#### <a name="count-of-routes-advertised-to-peer---split-by-instance"></a>播发到对等机的路由计数 - 按实例拆分

聚合类型：Count

此指标是 ExpressRoute 网关向线路播发的路由数的计数。 地址空间可能包括使用 VNet 对等互连连接并使用远程 ExpressRoute 网关的虚拟网络。 除非虚拟网络地址空间频繁更改，否则路由数应保持一致。 针对播发的路由数低于所知的虚拟网络地址空间数阈值时设置警报。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-advertised-to-peer.png" alt-text="播发到对等机的路由计数的屏幕截图。":::

#### <a name="count-of-routes-learned-from-peer---split-by-instance"></a>从对等机获知的路由计数 - 按实例拆分

聚合类型：Max

此指标显示 ExpressRoute 网关从连接到 ExpressRoute 线路的对等机中获知的路由数。 这些路由可以来自连接到同一线路的另一个虚拟网络，也可以从本地获知。 针对获知的路由数低于特定阈值时设置警报。 大量路由被丢弃可能表示网关出现性能问题，或者远程对等机不再向 ExpressRoute 线路播发路由。 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-learned-from-peer.png" alt-text="从对等机获知的路由计数的屏幕截图。":::

#### <a name="frequency-of-routes-change---split-by-instance"></a>路由更改频率 - 按实例拆分

聚合类型：Sum

此指标显示从远程对等机获知或播发到远程对等机的路由的频率。 应首先调查本地设备，了解网络更改如此频繁的原因。 路由更改频率较高可能表示 ExpressRoute 网关存在性能问题，其中向上缩放网关 SKU 可能会解决此问题。 针对频率阈值设置警报，以在 ExpressRoute 网关检测到异常路由更改时收到通知。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/frequency-of-routes-changed.png" alt-text="路由更改频率指标的屏幕截图。":::

#### <a name="number-of-vms-in-the-virtual-network"></a>虚拟网络中的 VM 数量

聚合类型：Max

此指标显示使用 ExpressRoute 网关的虚拟机数量。 虚拟机数量可能包括来自使用同一 ExpressRoute 网关的对等互连虚拟网络的虚拟机。 如果虚拟机数量超过可能会影响网关性能的特定阈值，针对此指标设置警报。 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/number-of-virtual-machines-virtual-network.png" alt-text="虚拟网络中虚拟机数量指标的屏幕截图。":::

#### <a name="expressroute-gateway-connections-in-bitsseconds"></a>ExpressRoute 网关连接（以位/秒为单位）

聚合类型：Avg

此指标显示通向 ExpressRoute 线路的特定连接的带宽使用情况。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erconnections.jpg" alt-text="网关连接带宽使用情况指标的屏幕截图。":::

## <a name="analyzing-logs"></a>分析日志

Azure Monitor 日志中的数据以表形式存储，每个表具有自己独有的属性集。  

Azure Monitor 中的所有资源日志都具有后跟服务特定字段的相同字段。 [Azure Monitor 资源日志架构](../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema)概述了常见架构。 可在 [Azure Expressroute 数据引用](monitor-expressroute-reference.md#schemas)中找到 ExpressRoute 资源日志的架构。 

[活动日志](../azure-monitor/essentials/activity-log.md)是一种平台日志，可用于深入了解订阅级别事件。 你可以单独查看它或将它路由到 Azure Monitor 日志，然后便可以在其中使用 Log Analytics 执行复杂得多的查询。

ExpressRoute 将数据存储在下表中。

| 表 | 说明 |
| ----- | ----------- |
| AzureDiagnostics | 由多个服务用来存储资源日志的公用表。 可以用 `MICROSOFT.NETWORK` 标识 ExpressRoute 的资源日志。 |
| AzureMetrics | 由 ExpressRoute 服务发出的指标数据，用于度量其运行状况和性能。 

要查看这些表，请导航到 ExpressRoute 线路资源，并在“监视”下选择“日志”。

### <a name="sample-kusto-queries"></a>示例 Kusto 查询

可在“日志搜索”栏中输入下面这些查询，以便监视 Azure ExpressRoute 资源。 这些查询使用[新语言](../azure-monitor/logs/log-query-overview.md)。

* 查询最近 12 个小时学习的 BGP 路由表。

    ```Kusto
    AzureDiagnostics
    | where TimeGenerated > ago(12h)
    | where ResourceType == "EXPRESSROUTECIRCUITS"
    | project TimeGenerated, ResourceType , network_s, path_s, OperationName
    ```

* 按级别、资源类型和网络查询 BGP 信息性消息。

    ```Kusto
    AzureDiagnostics
    | where Level == "Informational"
    | where ResourceType == "EXPRESSROUTECIRCUITS"
    | project TimeGenerated, ResourceId , Level, ResourceType , network_s, path_s
    ```

* 查询最近一小时内的流量图 BitInPerSeconds。
    
    ```Kusto
    AzureMetrics
    | where MetricName == "BitsInPerSecond"
    | summarize by Average, bin(TimeGenerated, 1h), Resource
    | render timechart
    ```

* 查询最近一小时内的流量图 BitOutPerSeconds。
    
    ```Kusto
    AzureMetrics
    | where MetricName == "BitsOutPerSecond"
    | summarize by Average, bin(TimeGenerated, 1h), Resource
    | render timechart
    ```

* 以 5 分钟为间隔查询 Arp 可用性图示。
    
    ```Kusto
    AzureMetrics
    | where MetricName == "ArpAvailability"
    | summarize by Average, bin(TimeGenerated, 5m), Resource
    | render timechart
    ```

* 以 5 分钟为间隔查询 BGP 可用性图示。

    ```Kusto
    AzureMetrics
    | where MetricName == "BGPAvailability"
    | summarize by Average, bin(TimeGenerated, 5m), Resource
    | render timechart
    ```

## <a name="alerts"></a>警报

在监视数据中发现重要情况时，Azure Monitor 警报会主动通知你。 有了警报，你就可以在客户注意到你的系统中的问题之前确定和解决它们。 可以在[指标](../azure-monitor/alerts/alerts-metric-overview.md)、[日志](../azure-monitor/alerts/alerts-unified-log.md)和[活动日志](../azure-monitor/alerts/activity-log-alerts.md)上设置警报。 不同类型的警报各有优缺点。

下表列出了常见和推荐使用的 ExpressRoute 的警报规则。

| 警报类型 | 条件 | 说明  |
|:---|:---|:---|
| ARP 可用性下降 | 维度名称：对等互连类型，聚合类型：Avg，运算符：小于，阈值：100% | 对等互连类型的 ARP 可用性下降时。 |
| BGP 可用性下降 | 维度名称：对等机，聚合类型：Avg，运算符：小于，阈值：100% | 对等机的 BGP 可用性下降时。 |

### <a name="alerts-for-expressroute-gateway-connections"></a>ExpressRoute 网关连接的警报

1. 若要配置警报，请导航到“Azure Monitor”，然后选择“警报”。

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg" alt-text="alerts":::
2. 选择“+ 选择目标”，然后选择 ExpressRoute 网关连接资源。

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg" alt-text="目标":::
3. 定义警报详细信息。

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg" alt-text="操作组":::
4. 定义和添加操作组。

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/actiongroup.png" alt-text="添加操作组":::


### <a name="alerts-based-on-each-peering"></a>基于每个对等互连的警报

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg" alt-text="每个对等互连":::

### <a name="configure-alerts-for-activity-logs-on-circuits"></a>为线路上的活动日志配置警报

在 **警报条件** 中，可以选择“活动日志”作为信号类型并选择“信号”。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg" alt-text="活动日志":::

## <a name="next-steps"></a>后续步骤

* 如需参考 ExpressRoute 创建的指标、日志和其他关键值，请查看[监视 ExpressRoute 数据参考](monitor-expressroute-reference.md)。
* 有关监视 Azure 资源的详细信息，请参阅[使用 Azure Monitor 监视 Azure 资源](../azure-monitor/overview.md)。
