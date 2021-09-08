---
title: Azure ExpressRoute：监视、指标和警报
description: 了解使用 Azure Monitor（其中集中了 Azure 的所有指标、警报和诊断日志）的 Azure ExpressRoute 监视、指标和警报。
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 04/07/2021
ms.author: duau
ms.openlocfilehash: abcec496f6bf3fdcd42dcffa66ecfb67533c7052
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123449488"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>ExpressRoute 监视、指标和警报

本文可帮助你使用 Azure Monitor 了解 ExpressRoute 监视、指标和警报。 Azure Monitor 是整个 Azure 中所有指标、警报和诊断日志的一站式商店。
 
>[!NOTE]
>建议不要使用 **经典指标**。
>

## <a name="expressroute-metrics"></a>ExpressRoute 指标

若要查看“指标”，请导航到“Azure Monitor”页，选择“指标”。  若要查看 ExpressRoute 指标，请按资源类型“ExpressRoute 线路”进行筛选   。 若要查看 **Global Reach** 指标，请按资源类型“ExpressRoute 线路”进行筛选，然后选择一个已启用 Global Reach 的 ExpressRoute 线路资源。  若要查看 **ExpressRoute Direct** 指标，请按“ExpressRoute 端口”筛选资源类型。  

选择指标后，将应用默认聚合。 （可选）可以应用拆分，它将显示具有不同维度的指标。

### <a name="aggregation-types"></a>聚合类型：

指标资源管理器支持 SUM、MAX、MIN、AVG 和 COUNT 作为[聚合类型](../azure-monitor/essentials/metrics-charts.md#aggregation)。 查看每个 ExpressRoute 指标的见解时，应该使用建议的聚合类型。

* 总和：在聚合间隔期间捕获的所有值的总和。 
* 计数：在聚合间隔期间捕获的度量数。 
* Average：在聚合间隔期间捕获的指标值的平均值。 
* 最小值：在聚合间隔期间捕获的最小值。 
* 最大值：在聚合间隔期间捕获的最大值。 

### <a name="available-metrics"></a>可用指标

|**指标**|**类别**|**维度**|**功能**|
| --- | --- | --- | --- |
|ARP 可用性|可用性|<ul><li>对等机（主要/辅助 ExpressRoute 路由器）</li><li> 对等互连类型（专用/公共/Microsoft）</li></ul>|ExpressRoute|
|BGP 可用性|可用性|<ul><li> 对等机（主要/辅助 ExpressRoute 路由器）</li><li> 对等互连类型</li></ul>|ExpressRoute|
|BitsInPerSecond|交通|<ul><li> 对等互连类型 (ExpressRoute)</li><li>链路 (ExpressRoute Direct)</li></ul>|<ul><li>ExpressRoute</li><li>ExpressRoute Direct</li><li>ExpressRoute 网关连接</li></ul>|
|BitsOutPerSecond|交通| <ul><li>对等互连类型 (ExpressRoute)</li><li> 链路 (ExpressRoute Direct)</li></ul> |<ul><li>ExpressRoute</li><li>ExpressRoute Direct</li><li>ExpressRoute 网关连接</li></ul>|
|CPU 使用率|性能| <ul><li>实例</li></ul>|ExpressRoute 虚拟网络网关|
|每秒数据包数|性能| <ul><li>实例</li></ul>|ExpressRoute 虚拟网络网关|
|播发到对等方的路由计数 |可用性| <ul><li>实例</li></ul>|ExpressRoute 虚拟网络网关|
|从对等方获知的路由计数 |可用性| <ul><li>实例</li></ul>|ExpressRoute 虚拟网络网关|
|路由更改频率 |可用性| <ul><li>实例</li></ul>|ExpressRoute 虚拟网络网关|
|虚拟网络中的 VM 数 |可用性| 不可用 |ExpressRoute 虚拟网络网关|
|GlobalReachBitsInPerSecond|交通|<ul><li>对等互连线路密钥（服务密钥）</li></ul>|Global Reach|
|GlobalReachBitsOutPerSecond|交通|<ul><li>对等互连线路密钥（服务密钥）</li></ul>|Global Reach|
|AdminState|物理连接|链接|ExpressRoute Direct|
|LineProtocol|物理连接|链接|ExpressRoute Direct|
|RxLightLevel|物理连接|<ul><li>链接</li><li>通道</li></ul>|ExpressRoute Direct|
|TxLightLevel|物理连接|<ul><li>链接</li><li>通道</li></ul>|ExpressRoute Direct|
>[!NOTE]
>只有在至少建立了一个 Global Reach 连接的情况下，才能使用 *GlobalGlobalReachBitsInPerSecond* 和 *GlobalGlobalReachBitsOutPerSecond*。
>

## <a name="circuits-metrics"></a>线路指标

### <a name="bits-in-and-out---metrics-across-all-peerings"></a>进位和出位 - 所有对等互连的指标

聚合类型：Avg

可以查看给定 ExpressRoute 线路上所有对等互连的指标。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg" alt-text="线路指标":::

### <a name="bits-in-and-out---metrics-per-peering"></a>进位和出位 - 每个对等互连的指标

聚合类型：Avg

可以查看专用、公共和 Microsoft 对等互连的指标（以位/秒为单位）。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg" alt-text="每个对等互连的指标":::

### <a name="bgp-availability---split-by-peer"></a>BGP 可用性 - 按对等机拆分  

聚合类型：Avg

可以查看跨对等互连和对等机（主要和辅助 ExpressRoute 路由器）的近实时 BGP（第 3 层连接性）可用性。 此仪表板显示专用对等互连的主 BGP 会话状态为“已启动”，专用对等互连的第二个 BGP 会话状态为“关闭”。 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg" alt-text="每个对等机的 BGP 可用性":::

### <a name="arp-availability---split-by-peering"></a>ARP 可用性 - 按对等互连拆分  

聚合类型：Avg

可以查看跨对等互连和对等机（主要和辅助 ExpressRoute 路由器）的近实时 [ARP](./expressroute-troubleshooting-arp-resource-manager.md)（第 3 层连接性）可用性。 此仪表板显示跨两个对等机的专用对等互连 ARP 会话状态为“已启动”，但对于两个对等机的 Microsoft 对等互连，状态则为“关闭”。 已跨两个对等机利用了默认聚合（平均）。  

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg" alt-text="每个对等机的 ARP 可用性":::

## <a name="expressroute-direct-metrics"></a>ExpressRoute Direct 指标

### <a name="admin-state---split-by-link"></a>管理状态 - 按链路拆分

聚合类型：Avg

可以查看 ExpressRoute Direct 端口对的每个链路的管理状态。 管理状态表示物理端口是打开还是关闭。 跨 ExpressRoute Direct 连接传递流量时需要此状态。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg" alt-text="ER Direct 管理状态":::

### <a name="bits-in-per-second---split-by-link"></a>每秒传入位数 - 按链路拆分

聚合类型：Avg

可以查看 ExpressRoute Direct 端口对的跨两个链路的每秒传入位数。 监视此仪表板以比较这两个链接的入站带宽。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg" alt-text="ER Direct 每秒传入位数":::

### <a name="bits-out-per-second---split-by-link"></a>每秒传出位数 - 按链路拆分

聚合类型：Avg

还可以查看 ExpressRoute Direct 端口对的跨两个链路的每秒传出位数。 监视此仪表板以比较这两个链接的出站带宽。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg" alt-text="ER Direct 每秒传出位数":::

### <a name="line-protocol---split-by-link"></a>线路协议 - 按链路拆分

聚合类型：Avg

可以查看 ExpressRoute Direct 端口对的跨每个链路的线路协议。 “线路协议”指示物理链路是否已启动以及是否通过 ExpressRoute Direct 运行。 监视此仪表板并设置警报，以了解物理连接是否已经关闭。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg" alt-text="ER Direct 线路协议":::

### <a name="rx-light-level---split-by-link"></a>Rx 轻型级别 - 按链路拆分

聚合类型：Avg

可以查看每个端口的 Rx 轻型级别（ExpressRoute Direct 端口的轻型级别是 **接收**）。 正常的 Rx 光能级通常在 -10 dBm 到 0 dBm 范围内。 设置警报，以在 Rx 光能级超出正常范围时接收通知。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg" alt-text="ER Direct 线路 Rx 轻型级别":::

### <a name="tx-light-level---split-by-link"></a>Tx 轻型级别 - 按链路拆分

聚合类型：Avg

可以查看每个端口的 Tx 轻型级别（ExpressRoute Direct 端口的轻型级别为 **传输**）。 正常的 Tx 光能级通常在 -10 dBm 到 0 dBm 范围内。 设置警报，以在 Tx 光能级超出正常范围时接收通知。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg" alt-text="ER Direct 线路 Tx 轻型级别":::

## <a name="expressroute-virtual-network-gateway-metrics"></a>ExpressRoute 虚拟网络网关指标

聚合类型：Avg

部署 ExpressRoute 网关时，Azure 会管理网关的计算和功能。 用户可以使用六个网关指标来更好地了解你的网关的性能：

* CPU 使用率
* 每秒数据包数
* 播发到对等机的路由计数
* 从对等机获知的路由计数
* 路由更改频率
* 虚拟网络中的 VM 数量  

我们强烈建议为每个指标设置警报，以便了解网关在什么情况下会出现性能问题。

### <a name="cpu-utilization---split-instance"></a>CPU 使用率 - 拆分实例

聚合类型：Avg

用户可以查看每个网关实例的 CPU 利用率。 在例行主机维护过程中，CPU 利用率可能会迅速激增，但 CPU 利用率长时间居高不下，可能表明网关达到性能瓶颈。 增加 ExpressRoute 网关的大小可以解决此问题。 针对 CPU 利用率超过特定阈值的频率设置警报。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/cpu-split.jpg" alt-text="CPU 利用率 - 拆分指标的屏幕截图。":::

### <a name="packets-per-second---split-by-instance"></a>每秒数据包数 - 按实例拆分

聚合类型：Avg

此指标捕获遍历 ExpressRoute 网关的入站数据包的数目。 如果网关接收来自本地网络的流量，用户应该在这里看到一致的数据流。 针对每秒的数据包数量低于某个阈值时设置警报，指示网关不再接收流量。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/pps-split.jpg" alt-text="每秒数据包数量 - 拆分指标的屏幕截图。":::

### <a name="count-of-routes-advertised-to-peer---split-by-instance"></a>播发到对等方的路由计数 - 按实例拆分

聚合类型：Count

此指标是 ExpressRoute 网关向线路播发的路由数的计数。 地址空间可能包括使用 VNet 对等互连连接并使用远程 ExpressRoute 网关的虚拟网络。 除非虚拟网络地址空间频繁更改，否则路由数应保持一致。 针对播发的路由数低于所知的虚拟网络地址空间数阈值时设置警报。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-advertised-to-peer.png" alt-text="播发到对等机的路由计数的屏幕截图。":::

### <a name="count-of-routes-learned-from-peer---split-by-instance"></a>从对等方获知的路由计数 - 按实例拆分

聚合类型：Max

此指标显示 ExpressRoute 网关从连接到 ExpressRoute 线路的对等机中获知的路由数。 这些路由可以来自连接到同一线路的另一个虚拟网络，也可以从本地获知。 针对获知的路由数低于特定阈值时设置警报。 这可能表示网关出现性能问题，或者远程对等机不再向 ExpressRoute 线路播发路由。 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-learned-from-peer.png" alt-text="从对等机获知的路由计数的屏幕截图。":::

### <a name="frequency-of-routes-change---split-by-instance"></a>路由更改频率 - 按实例拆分

聚合类型：Sum

此指标显示从远程对等机获知或播发到远程对等机的路由的频率。 应首先调查本地设备，了解网络更改如此频繁的原因。 路由更改频率较高可能表示 ExpressRoute 网关存在性能问题，其中向上缩放网关 SKU 可能会解决此问题。 针对频率阈值设置警报，以在 ExpressRoute 网关检测到异常路由更改时收到通知。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/frequency-of-routes-changed.png" alt-text="路由更改频率指标的屏幕截图。":::

### <a name="number-of-vms-in-the-virtual-network"></a>虚拟网络中的 VM 数

聚合类型：Max

此指标显示使用 ExpressRoute 网关的虚拟机数量。 虚拟机数量可能包括来自使用同一 ExpressRoute 网关的对等互连虚拟网络的虚拟机。 如果虚拟机数量超过可能会影响网关性能的特定阈值，针对此指标设置警报。 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/number-of-virtual-machines-virtual-network.png" alt-text="虚拟网络中虚拟机数量指标的屏幕截图。":::

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>ExpressRoute 网关连接（以位/秒为单位）

聚合类型：Avg

此指标显示通向 ExpressRoute 线路的特定连接的带宽使用情况。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erconnections.jpg" alt-text="网关连接带宽使用情况指标的屏幕截图。":::

## <a name="alerts-for-expressroute-gateway-connections"></a>ExpressRoute 网关连接的警报

1. 若要配置警报，请导航到“Azure Monitor”，然后选择“警报”。

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg" alt-text="alerts":::
2. 选择“+选择目标”，然后选择 ExpressRoute 网关连接资源。

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg" alt-text="目标":::
3. 定义警报详细信息。

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg" alt-text="操作组":::
4. 定义和添加操作组。

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/actiongroup.png" alt-text="添加操作组":::

## <a name="alerts-based-on-each-peering"></a>基于每个对等互连的警报

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg" alt-text="每个对等互连":::

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>为线路上的活动日志配置警报

在 **警报条件** 中，可以选择“活动日志”作为信号类型并选择“信号”。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg" alt-text="活动日志":::

## <a name="more-metrics-in-log-analytics"></a>Log Analytics 中的更多指标

还可以通过导航到 ExpressRoute 线路资源并选择“日志”选项卡来查看 ExpressRoute 指标。对于你查询的任何指标，输出将包含以下列。

|**列**|类型|**说明**|
| --- | --- | --- |
|TimeGrain|字符串|PT1M（每分钟推送一次指标值）|
|Count|real|通常等于 2（每个 MSEE 每分钟推送一个指标值）|
|最低配置|real|两个 MSEE 推送的两个指标值中的最小值|
|最大值|real|两个 MSEE 推送的两个指标值中的最大值|
|平均值|real|等于 (最小值 + 最大值)/2|
|总计|real|来自两个 MSEE 的两个指标值的总和（所查询指标的需关注的主要值）|
  
## <a name="next-steps"></a>后续步骤

配置 ExpressRoute 连接。
  
* [创建和修改线路](expressroute-howto-circuit-arm.md)
* [创建和修改对等配置](expressroute-howto-routing-arm.md)
* [将 VNet 链接到 ExpressRoute 线路](expressroute-howto-linkvnet-arm.md)
