---
title: 使用 Network Insight 的 Azure ExpressRoute Insights
description: 了解使用 Network Insight 的 Azure ExpressRoute Insights。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 04/05/2021
ms.author: duau
ms.openlocfilehash: a5e01a4fc84f852b43e5a76114bebe145da10793
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443557"
---
# <a name="azure-expressroute-insights-using-network-insights"></a>使用 Network Insight 的 Azure ExpressRoute Insights

本文介绍了 Network Insights 如何助你一站式查看 ExpressRoute 指标和配置。 通过使用 Network Insights，你可以查看包含重要 ExpressRoute 信息的拓扑图和运行状况仪表板，而无需完成任何额外的设置。

:::image type="content" source="./media/expressroute-network-insights/network-monitor-page.png" alt-text="网络监视器登录页的屏幕截图。" lightbox="./media/expressroute-network-insights/monitor-landing-page-expanded.png":::

## <a name="visualize-functional-dependencies"></a>可视化功能依赖项

1. 导航到 Azure Monitor 页，然后选择“网络”。

    :::image type="content" source="./media/expressroute-network-insights/monitor-page.png" alt-text="监视器登录页的屏幕截图。":::

1. 选择 ExpressRoute Circuits 卡。 

1. 然后，选择待查看线路的拓扑按钮。

   :::image type="content" source="./media/expressroute-network-insights/monitor-landing-page.png" alt-text="ExpressRoute 监视器登录页的屏幕截图。" lightbox="./media/expressroute-network-insights/monitor-landing-page-expanded.png"::: 

1. 功能依赖关系视图提供了 ExpressRoute 设置的清晰图片，其中概述了不同 ExpressRoute 组件（对等互连、连接、网关）之间的关系。

    :::image type="content" source="./media/expressroute-network-insights/topology-view.png" alt-text="Network Insight 拓扑视图的屏幕截图。" lightbox="./media/expressroute-network-insights/topology-view-expanded.png":::

1. 将鼠标悬停在拓扑图中的任何组件上，即可查看配置信息。 例如，将鼠标悬停在 ExpressRoute 对等互连组件上即可查看线路带宽和 Global Reach 启用状况等详细信息。

    :::image type="content" source="./media/expressroute-network-insights/topology-hovered.png" alt-text="在拓扑视图资源上悬停的屏幕截图。" lightbox="./media/expressroute-network-insights/topology-hovered-expanded.png":::

## <a name="view-a-detailed-and-pre-loaded-metrics-dashboard"></a>查看详细的预加载指标仪表板

使用功能依赖关系视图查看 ExpressRoute 设置的拓扑后，请选择“查看详细指标”以导航到“详细指标”视图，以了解线路的性能。 此视图提供了一个排列有序的链接资源列表和一个内容丰富的重要 ExpressRoute 指标仪表板。

“链接的资源”部分列出了已连接的 ExpressRoute 网关和已配置的对等互连，可以选中以导航到相应的资源页。

:::image type="content" source="./media/expressroute-network-insights/linked-resources.png" alt-text="监视页面上链接资源的屏幕截图。":::


“ExpressRoute Metrics”部分包括跨各种类别的“Availability”、“Throughput”、“Packet Drops”和“Gateway Metrics”的重要线路指标的图表。

### <a name="availability"></a>可用性

“Availability”选项卡会追踪 ARP 和 BGP 可用性，为整个线路和单个连接（主要和次要）绘制数据。 

:::image type="content" source="./media/expressroute-network-insights/arp-bgp-availability.png" alt-text="可用性指标图形的屏幕截图。" lightbox="./media/expressroute-network-insights/arp-bgp-availability-expanded.png":::

### <a name="throughput"></a>吞吐量

同样，“Throughput”选项卡将以位/秒为单位绘制线路入口和出口流量的总吞吐量。 你还可以查看各个连接的吞吐量和每种类型的已配置对等互连。

:::image type="content" source="./media/expressroute-network-insights/throughput.png" alt-text="吞吐量指标图形的屏幕截图。" lightbox="./media/expressroute-network-insights/throughput-expanded.png":::

### <a name="packet-drops"></a>数据包丢弃

“Packet Drops”选项卡会以位/秒为单位，绘制每入口和出口的每秒流量丢弃量。 如果你经常需要或超过线路带宽，则可利用此方法，轻松监视可能出现的性能问题。

:::image type="content" source="./media/expressroute-network-insights/dropped-packets.png" alt-text="丢弃的数据包图表的屏幕截图。" lightbox="./media/expressroute-network-insights/dropped-packets-expanded.png":::

### <a name="gateway-metrics"></a>网关指标

最后，“Gateway Metrics”选项卡会生成所选 ExpressRoute 门户的关键指标图标（来自链接源部分）。 当需要监视特定虚拟网络的连接时，请使用此选项卡。

:::image type="content" source="./media/expressroute-network-insights/gateway-metrics.png" alt-text="网关吞吐量和 CPU 指标的屏幕截图。" lightbox="./media/expressroute-network-insights/gateway-metrics-expanded.png":::

## <a name="next-steps"></a>后续步骤

配置 ExpressRoute 连接。
  
* 了解有关 [Azure ExpressRoute](expressroute-introduction.md)、[Network Insights](../azure-monitor/insights/network-insights-overview.md) 和 [Network Watcher](../network-watcher/network-watcher-monitoring-overview.md) 的详细信息
* [创建和修改线路](expressroute-howto-circuit-arm.md)
* [创建和修改对等配置](expressroute-howto-routing-arm.md)
* [将 VNet 链接到 ExpressRoute 线路](expressroute-howto-linkvnet-arm.md)
* [自定义指标](expressroute-monitoring-metrics-alerts.md)并创建[连接监视器](../network-watcher/connection-monitor-overview.md)
