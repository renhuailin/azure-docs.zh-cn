---
title: Azure Monitor 网络见解
description: 概述 Azure Monitor 网络见解，它为已部署的所有网络资源提供运行状况和指标的全面视图，并且无需任何配置。
ms.topic: conceptual
author: KumudD
ms.author: kumud
ms.date: 11/25/2020
ms.openlocfilehash: a4b433ed7a2eb0a2a9367f921e63091a823df37d
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121862313"
---
# <a name="azure-monitor-network-insights"></a>Azure Monitor 网络见解

Azure Monitor 网络见解为已部署的所有网络资源提供[运行状况](../../service-health/resource-health-checks-resource-types.md)和[指标](../essentials/metrics-supported.md)的全面视图，并且无需任何配置。 它还提供对网络监视功能的访问，如[连接监视器](../../network-watcher/connection-monitor-overview.md)、[网络安全组的流日志记录 (NSG)](../../network-watcher/network-watcher-nsg-flow-logging-overview.md) 和[流量分析](../../network-watcher/traffic-analytics.md)。 它还提供其他网络[诊断](../../network-watcher/network-watcher-monitoring-overview.md#diagnostics)功能。

Azure Monitor 网络见解是围绕以下关键监视组件构建的：
- [网络运行状况和指标](#networkhealth)
- [连接](#connectivity)
- [交通](#traffic)
- [诊断工具包](#diagnostictoolkit)

## <a name="network-health-and-metrics"></a><a name="networkhealth"></a>网络运行状况和指标

通过 Azure Monitor 网络见解的“概览”页面，可轻松地将网络资源清单以及资源运行状况和警报可视化。 它分为四个主要功能区域：搜索和筛选、资源运行状况和指标、警报以及依赖项视图。

[![显示概述页面的屏幕截图](media/network-insights-overview/overview.png)](media/network-insights-overview/overview.png#lightbox)

### <a name="search-and-filtering"></a>搜索和筛选
可以使用“订阅”、“资源组”和“类型”等筛选器来自定义资源运行状况和警报视图  。

可使用搜索框搜索资源及其关联资源。 例如，公共 IP 与应用程序网关相关联。 搜索公共 IP DNS 名称将同时返回公共 IP 和关联的应用程序网关：

[![屏幕截图显示 Azure Monitor 网络见解搜索结果。](media/network-insights-overview/search.png)](media/network-insights-overview/search.png#lightbox)


### <a name="resource-health-and-metrics"></a>资源运行状况和指标
在以下示例中，每个磁贴代表一个资源类型。 该磁贴显示在所有选定订阅上部署的该资源类型的实例数。 它还显示资源的运行状况状态。 在此示例中，部署了 105 个 ER 和 VPN 连接。 103 个运行正常，2 个不可用。

![屏幕截图显示 Azure Monitor 网络见解中的资源运行状况和指标。](media/network-insights-overview/resource-health.png)

如果选择不可用的 ER 和 VPN 连接，会看到一个指标视图： 

![屏幕截图显示 Azure Monitor 网络见解中的指标视图。](media/network-insights-overview/metric-view.png)

可以选择网格视图中的任意项。 选择“运行状况”列中的图标以获取该连接的资源运行状况。 选择“警报”列中的值以转到连接的警报和指标页。 

### <a name="alerts"></a>警报
页面右侧的“警报”框提供所有订阅中选定资源生成的所有警报的视图。 选择警报计数可转到详细的警报页面。

### <a name="dependency-view"></a>“依赖项”视图
“依赖项”视图有助于可视化资源的配置方式。 “依赖项”视图当前可用于 Azure 应用程序网关、Azure 虚拟 WAN 和 Azure 负载均衡器。 例如，对于应用程序网关，你可以通过选择指标网格视图中的应用程序网关资源名称来访问“依赖项”视图。 可以为虚拟 WAN 和负载均衡器执行相同的操作。

![屏幕截图显示 Azure Monitor 网络见解中的应用程序网关视图。](media/network-insights-overview/application-gateway.png)

应用程序网关的“依赖项”视图提供了前端 IP 如何连接到侦听器、规则和后端池的简化视图。 连接线条采用颜色编码，并根据后端池运行状况提供其他详细信息。 该视图还提供了应用程序网关指标和所有相关后端池（如虚拟机规模集和 VM 实例）的指标的详细视图。

[![屏幕截图显示 Azure Monitor 网络见解中的“依赖项”视图。](media/network-insights-overview/dependency-view.png)](media/network-insights-overview/dependency-view.png#lightbox)

通过依赖项图可轻松导航到配置设置。 右键单击后端池可访问其他信息。 例如，如果后端池是 VM，则可以直接访问 VM Insights 和 Azure 网络观察程序连接疑难解答，以确定连接问题：

![屏幕截图显示 Azure Monitor 网络见解中的依赖项视图菜单。](media/network-insights-overview/dependency-view-menu.png)

“依赖项”视图上的搜索和筛选栏提供了一种轻松的方法来搜索图形。 例如，如果在上述示例中搜索“AppGWTestRule”，则该视图将纵向缩减为仅显示通过 AppGWTestRule 连接的所有节点：

![屏幕截图显示 Azure Monitor 网络见解中某个搜索的示例。](media/network-insights-overview/search-example.png)

可借助各种筛选器纵向缩减到特定路径和状态。 例如，从“运行状况状态”列表中仅选择“运行不正常”可显示状态为“运行不正常”的所有边缘 。

选择“查看详细指标”可打开预配置的工作簿，其中提供了应用程序网关、所有后端池资源和前端 IP 的详细指标。 

## <a name="connectivity"></a><a name="connectivity"></a>连接

“连接”选项卡提供了一种简单的方法来为选定的一组订阅直观显示通过连接监视器和[连接监视器（经典版）](../../network-watcher/connection-monitor-overview.md)配置的所有测试。

![屏幕截图显示 Azure Monitor 网络见解中的“连接”选项卡。](media/network-insights-overview/azure-monitor-for-networks-connectivity-tab.png)

测试按“源”和“目标”磁贴进行分组，并显示每个测试的可访问性状态 。 可访问设置可根据失败的检查 (%) 和 RTT (ms) 提供对可访问性条件的配置的轻松访问。 设置值后，每个测试的状态根据选择条件进行更新。

[![屏幕截图显示 Azure Monitor 网络见解中的连接测试。](media/network-insights-overview/azure-monitor-for-networks-connectivity-tests.png)](media/network-insights-overview/azure-monitor-for-networks-connectivity-tests.png#lightbox)

可以选择任意源或目标磁贴来打开指标视图：

[![屏幕截图显示 Azure Monitor 网络见解中的连接指标。](media/network-insights-overview/azure-monitor-for-networks-connectivity-metrics.png)](media/network-insights-overview/azure-monitor-for-networks-connectivity-metrics.png#lightbox)


可以选择网格视图中的任意项。 选择“可访问性”列中的图标，以转到“连接监视器”门户页，并查看逐跳拓扑和已识别的影响连接的问题。 选择“警报”列中的值以转到警报。 选择“检查失败百分比”和“往返时间(ms)”列中的关系图，以转到所选的连接监视器的指标页 。

页面右侧的“警报” **** 框提供针对所有订阅中配置的连接测试生成的所有警报的视图。 选择警报计数可转到详细的警报页面。

## <a name="traffic"></a><a name="traffic"></a>交通
通过“流量”选项卡，可以访问为 [NSG 流日志](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)配置的所有 NSG，和按位置分组的所选订阅集的[流量分析](../../network-watcher/traffic-analytics.md)。 利用此选项卡上提供的搜索功能，可以标识为搜索的 IP 地址配置的 NSG。 可以在环境中搜索任意 IP 地址。 平铺区域视图将显示所有 NSG 和 NSG 流日志，以及流量分析配置状态。

[![屏幕截图显示 Azure Monitor 网络见解中的“流量”选项卡。](media/network-insights-overview/azure-monitor-for-networks-traffic-view.png)](media/network-insights-overview/azure-monitor-for-networks-traffic-view.png#lightbox)

如果选择任何区域磁贴，会显示一个网格视图。 网格通过易于阅读和配置的视图显示 NSG 流日志和流量分析：  

[![屏幕截图显示 Azure Monitor 网络见解中的流量区域视图。](media/network-insights-overview/azure-monitor-for-networks-traffic-region-view.png)](media/network-insights-overview/azure-monitor-for-networks-traffic-region-view.png#lightbox)

可以选择网格视图中的任意项。 选择“流日志配置状态”列中的图标，以编辑 NSG 流日志和流量分析配置。 选择“警报”列中的值，以转到为所选 NSG 配置的流量警报。 同样，可以通过选择“流量分析工作区”来转到流量分析视图。  

页面右侧的“警报” **** 框提供所有订阅的所有基于工作区的流量分析警报的视图。 选择警报计数可转到详细的警报页面。

## <a name="diagnostic-toolkit"></a><a name="diagnostictoolkit"></a>诊断工具包
诊断工具包提供可用于网络故障排除的所有诊断功能的访问权限。 可以使用此下拉列表访问[数据包捕获](../../network-watcher/network-watcher-packet-capture-overview.md)、[VPN 故障排除](../../network-watcher/network-watcher-troubleshoot-overview.md)、[连接故障排除](../../network-watcher/network-watcher-connectivity-overview.md)、[下一跃点](../../network-watcher/network-watcher-next-hop-overview.md)和 [IP 流验证](../../network-watcher/network-watcher-ip-flow-verify-overview.md)等功能：

![显示“诊断工具包”选项卡的屏幕截图。](media/network-insights-overview/azure-monitor-for-networks-diagnostic-toolkit.png)

## <a name="availability-of-resources"></a>资源的可用性 

默认情况下，所有网络资源都显示在网络见解中。 客户可以单击资源类型以查看资源运行状况和指标（如果可用）、订阅详细信息、位置等。网络资源的子集已载入。 对于载入的资源，客户有权访问特定于资源的拓扑视图以及内置指标工作簿。 通过这些全新安装体验，可以更轻松地浏览资源指标并排查问题。  

已载入的资源包括： 
* 虚拟 WAN
* 应用程序网关
* 负载均衡器
* ExpressRoute
* 专用链接
* NAT 网关
* 公共 IP
* NIC

## <a name="troubleshooting"></a>疑难解答 
如需常规故障排除指南，请参阅专用的基于工作簿的见解[故障排除文章](troubleshoot-workbooks.md)。
本部分将帮助你诊断和排除在使用 Azure Monitor 网络见解时可能会遇到的一些常见问题。 

### <a name="how-do-i-resolve-performance-problems-or-failures"></a>如何解决性能问题和故障？

若要了解如何对通过 Azure Monitor 网络见解识别的与网络相关的任何问题进行故障排除，请参阅发生故障的资源的相关故障排除文档。 

下面是一些常用服务的相关故障排除文章的链接。 更多有关这些服务的故障排除文章，请参阅相关服务的目录的“故障排除”部分中的其他文章。
* [Azure 虚拟网络](../../virtual-network/virtual-network-troubleshoot-peering-issues.md)
* [Azure 应用程序网关](../../application-gateway/create-gateway-internal-load-balancer-app-service-environment.md)
* [Azure VPN 网关](../../vpn-gateway/vpn-gateway-troubleshoot.md)
* [Azure ExpressRoute](../../expressroute/expressroute-troubleshooting-expressroute-overview.md) 
* [Azure 负载均衡器](../../load-balancer/load-balancer-troubleshoot.md) 

### <a name="why-dont-i-see-the-resources-for-all-the-subscriptions-ive-selected"></a>为什么我看不到所选择的所有订阅的资源？

Azure Monitor 网络见解一次只能显示 5 个订阅的资源。 

### <a name="how-do-i-make-changes-or-add-visualizations-to-azure-monitor-network-insights"></a>如何进行更改或将可视化效果添加到 Azure Monitor 网络见解？

若要进行更改，请选择“编辑模式”以修改工作簿。 然后，你可以将所做的更改另存为与指定的订阅和资源组相关联的新工作簿。

### <a name="whats-the-time-grain-after-i-pin-any-part-of-the-workbooks"></a>固定工作簿的任何部分后，时间粒度是多少？

Azure Monitor 网络见解使用“自动”时间粒度，因此时间粒度基于所选时间范围。

### <a name="whats-the-time-range-when-any-part-of-a-workbook-is-pinned"></a>固定工作簿的任何部分后，时间范围是多少？

时间范围取决于面板设置。

### <a name="what-if-i-want-to-see-other-data-or-make-my-own-visualizations-how-can-i-make-changes-to-azure-monitor-network-insights"></a>如果想要查看其他数据或生成自己的可视化效果，应该怎么做？ 如何对 Azure Monitor 网络见解进行更改？

你可以通过使用编辑模式来编辑在任何边侧面板或详细指标视图中看到的工作簿。 然后，可以将所做的更改另存为新的工作簿。

## <a name="next-steps"></a>后续步骤

- 详细了解网络监视：[Azure 网络观察程序是什么？](../../network-watcher/network-watcher-monitoring-overview.md)
- 了解工作簿支持的方案、如何创建报表和自定义现有报表等：[使用 Azure Monitor 工作簿创建交互式报表](../visualize/workbooks-overview.md)
