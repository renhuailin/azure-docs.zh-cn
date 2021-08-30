---
title: 使用 Azure Monitor 见解监视虚拟 WAN
description: 了解如何使用 Azure Monitor 见解监视 Azure 虚拟 WAN。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 06/22/2021
ms.author: cherylmc
ms.openlocfilehash: 73209f93e2b7fd96dc6ebb540d62624249db5be8
ms.sourcegitcommit: 5fabdc2ee2eb0bd5b588411f922ec58bc0d45962
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2021
ms.locfileid: "112539225"
---
# <a name="azure-monitor-insights-for-virtual-wan"></a>虚拟 WAN 的 Azure Monitor 见解

用于 Azure 虚拟 WAN 的 [Azure Monitor 见解](../azure-monitor/insights/network-insights-overview.md)使用户和操作员能够查看通过自动发现的拓扑映射显示的虚拟 WAN 的状态。 资源状态在映射上的覆盖为你提供了虚拟 WAN 整体运行状况的快照视图。 可以通过对虚拟 WAN 门户资源配置页的一键式访问，在映射上导航资源。

虚拟 WAN 资源级指标通过预打包的虚拟 WAN 指标工作簿进行收集和显示。 工作簿显示了虚拟 WAN、中心、网关和连接级别的指标。 本文将指导你完成使用用于虚拟 WAN 的 Azure Monitor 见解集中在一个位置查看虚拟 WAN 拓扑和指标的步骤。

> [!NOTE]
> “见解”菜单选项位于虚拟 WAN 门户的“监视”下。 可以使用用于网络的 Azure Monitor 访问虚拟 WAN 拓扑和指标工作簿。 有关详细信息，请参阅[用于网络的 Azure Monitor](../azure-monitor/insights/network-insights-overview.md)。 
>

## <a name="before-you-begin"></a>准备阶段

若要完成本文中的步骤，需要具有一个或多个中心的虚拟 WAN。 若要创建虚拟 WAN 和中心，请执行以下文章中的步骤：

* [创建虚拟 WAN](virtual-wan-site-to-site-portal.md#openvwan)
* [创建中心](virtual-wan-site-to-site-portal.md#hub)

## <a name="view-vwan-topology"></a><a name="topology"></a>查看 VWAN 拓扑

转到“Azure 门户” > “虚拟 WAN” 。 在左侧窗格的“监视器”菜单中，选择“见解(预览版)” 。 此时将显示“见解”视图。 其中显示了虚拟 WAN 依赖项映射和高级“指标”小型工作簿。

图 1：**监视器 > 见解菜单**

:::image type="content" source="./media/azure-monitor-insights/monitor-menu.png" alt-text="显示“见解(预览版)”视图的屏幕截图。" lightbox="./media/azure-monitor-insights/monitor-menu.png":::

在“见解”视图中，可以查看自动发现的虚拟 WAN 资源。 这些资源包括中心、网关、防火墙、连接和分支虚拟网络、第三方 NVA，以及端到端虚拟 WAN 中的分支。 有关示例，请参阅“图 2”。

资源状态以颜色编码，并叠加在映射中的资源图标上。 高级虚拟 WAN 指标（如中心容量和网关利用率）显示在小型工作簿中窗口的右侧。

图 2：**见解视图**

:::image type="content" source="./media/azure-monitor-insights/insights-view.png" alt-text="显示“见解”视图的屏幕截图。" lightbox="./media/azure-monitor-insights/insights-view.png":::

## <a name="dependency-view"></a><a name="dependency"></a>“依赖项”视图

虚拟 WAN 的“依赖项”视图可直观显示组织到中心辐射型体系结构中的相互连接的大量虚拟 WAN 资源。

图 3： **“VWAN 依赖项”视图**

:::image type="content" source="./media/azure-monitor-insights/dependency-map.png" alt-text="显示“依赖项”视图的屏幕截图。" lightbox="./media/azure-monitor-insights/dependency-map.png":::

依赖项视图映射将以下资源显示为连接图表：

* 跨各种 Azure 区域的虚拟 WAN 中心。
* 直接连接到中心的分支虚拟网络。
* 通过 ExpressRoute、S2S、P2S 连接和虚拟网络网关连接到各中心的 VPN 和 Azure ExpressRoute 分支站点和 P2S 用户。
* 中心（安全中心）中部署的 Azure 防火墙（包括第三方防火墙代理）。
* 部署在分支虚拟网络中的第三方 NVA（网络虚拟设备）。

依赖项映射还显示间接连接的虚拟网络（与虚拟 WAN 分支虚拟网络对等互连的虚拟网络）。

通过依赖项映射可以轻松地导航到每个资源的配置设置。 例如，可以将鼠标悬停在中心资源上，查看基本资源配置，如中心区域和中心前缀。 右键单击以访问中心资源的“Azure 门户”页。

图 4：**导航到特定于资源的信息**

:::image type="content" source="./media/azure-monitor-insights/resource-information.png" alt-text="显示如何导航到特定于资源的信息的屏幕截图。":::

“依赖项”视图上的搜索和筛选栏提供了一种轻松的方法来搜索图形。 各种筛选器有助于将搜索范围缩小到特定路径和状态。

图 5：**搜索和筛选**

:::image type="content" source="./media/azure-monitor-insights/search-filter.png" alt-text="显示搜索和筛选栏的屏幕截图。" lightbox="./media/azure-monitor-insights/search-filter.png":::

## <a name="detailed-metrics"></a><a name="detailed"></a>详细指标

可以选择“查看详细指标”来访问详细的“指标”页 。 “指标”页是一个使用单独的选项卡预配置的仪表板。 这些选项卡提供对虚拟 WAN 级别、中心级别和单个连接级别的虚拟 WAN 资源容量、性能以及利用率的见解。

**图 6：详细指标仪表板**

:::image type="content" source="./media/azure-monitor-insights/detailed-metrics.png" alt-text="显示详细指标仪表板的屏幕截图。" lightbox="./media/azure-monitor-insights/detailed-metrics.png":::

## <a name="next-steps"></a>后续步骤

* 若要了解详细信息，请参阅 [Azure Monitor 中的指标](../azure-monitor/essentials/data-platform-metrics.md)。
* 有关所有虚拟 WAN 指标的完整说明，请参阅[监视虚拟 WAN](monitor-virtual-wan.md)。
