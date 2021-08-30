---
title: 如何查看和配置针对 Azure ExpressRoute 线路维护的警报
description: 了解如何使用 Azure 门户的“服务运行状况”页来配置针对 ExpressRoute 线路维护的自定义警报。
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: how-to
ms.date: 05/10/2021
ms.author: mialdrid
ms.openlocfilehash: 93335c6f163e016c619fd4803a595ffcc3418a9d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121735266"
---
# <a name="how-to-view-and-configure-alerts-for-azure-expressroute-circuit-maintenance"></a>如何查看和配置针对 Azure ExpressRoute 线路维护的警报

ExpressRoute 使用 Azure 服务运行状况来通知你计划内和即将进行的 ExpressRoute 线路维护。 通过服务运行状况，你可以在 Azure 门户中查看计划内维护和过去的维护，同时配置最适合你需求的警报和通知。 若要详细了解 Azure 服务运行状况，请参阅[什么是 Azure 服务运行状况？](../service-health/overview.md)

> [!NOTE]
> * 在维护活动期间，或在出现影响某个连接的计划外事件的情况下，Microsoft 将更愿意使用 AS 路径预置，将流量排出到正常的连接。 从 Microsoft 配置路径预置，并正确配置所需的路由播发以避免任何服务中断时，需要确保流量能够路由到正常的路径。 
> * 在有状态设备上终止 ExpressRoute BGP 连接可能会导致 Microsoft 或 ExpressRoute 提供商计划内或计划外维护期间的故障转移问题。 应对设置进行测试，确保流量能够正确地进行故障转移，并在可能的情况下终止无状态设备上的 BGP 会话。
>

## <a name="view-planned-maintenance"></a>查看计划内维护

1. 登录 [Azure 门户](https://portal.azure.com/)。

1. 导航到“服务运行状况”页。 

    :::image type="content" source="./media/maintenance-alerts/search-service-health.png" alt-text="显示搜索“服务运行状况”页的屏幕截图。"::: 

1. 在页面左侧的“活动事件”下选择“计划内维护”。 在此页上，可以通过筛选目标订阅、Azure 区域以及 Azure 服务来查看各个维护事件。

    :::image type="content" source="./media/maintenance-alerts/view-maintenance.png" alt-text="“服务运行状况”中“计划内维护”页的屏幕截图。" lightbox="./media/maintenance-alerts/view-maintenance-expanded.png"::: 

1. 从“服务”下拉列表中选择“ExpressRoute”，可以仅查看与 ExpressRoute 相关的维护。 然后从列表中选择问题以查看事件摘要。 选择“问题更新”选项卡，可以详细了解正在进行的维护。

    :::image type="content" source="./media/maintenance-alerts/summary.png" alt-text="ExpressRoute 维护摘要的屏幕截图。" lightbox="./media/maintenance-alerts/summary-expanded.png":::

## <a name="view-past-maintenance"></a>查看过去的维护

1. 若要查看过去的维护事件，请在页面左侧“历史记录”部分下选择“运行状况历史记录”。 

    :::image type="content" source="./media/maintenance-alerts/health-history.png" alt-text="在“服务运行状况”中选择“运行状况历史记录”的屏幕截图。" lightbox="./media/maintenance-alerts/health-history-expanded.png"::: 

1. 在此页上，可以通过筛选目标订阅和 Azure 区域来查看各个维护事件。 若要进一步缩小运行状况历史记录事件的范围，可以选择运行状况事件类型并定义一个过去的时间范围。 若要筛选计划内的 ExpressRoute 线路维护，请将“运行状况事件类型”设置为“计划内维护”。

    :::image type="content" source="./media/maintenance-alerts/past-maintenance.png" alt-text="“运行状况历史记录”页上过去的维护的屏幕截图。" lightbox="./media/maintenance-alerts/past-maintenance-expanded.png"::: 

## <a name="create-alerts-and-notifications-for-maintenance-events"></a>为维护事件创建警报和通知

1. Azure 服务运行状况支持对维护事件进行自定义警报。 若要为 ExpressRoute 线路维护配置警报，请在页面左侧的“警报”部分下，导航到“运行状况警报”。 此时会显示一个表，其中列出了以前配置的警报。

1.  若要创建新警报，请选择页面顶部的“添加服务运行状况警报”。

    :::image type="content" source="./media/maintenance-alerts/health-alerts.png" alt-text="在“服务运行状况”中选择“运行状况警报”的屏幕截图。" lightbox="./media/maintenance-alerts/health-alerts-expanded.png"::: 

1. 选择或输入以下信息以创建预警规则。

    | 类别 | 设置 | 值 | 
    | --- | -------- | ----- |
    | Condition | 订阅 | 选择目标订阅。 |
    |               | 服务 | ExpressRoute \ ExpressRoute 线路 |
    |               | 区域 | 选择一个区域，或保留为“全局”，以显示所有区域的运行状况事件。
    |               | 事件类型 | 选择“计划内维护”。 |
    | **操作** | 操作组名称 | 操作组确定通知类型并定义接收通知的受众。 有关创建和管理操作组的帮助，请参阅在 Azure 门户中[创建和管理操作组](../azure-monitor/alerts/action-groups.md)的内容。 |
    | **警报规则详细信息** | 预警规则名称 | 输入用于标识预警规则的名称。 |
    |                        | 说明 | 提供此预警规则的功能描述。 | 
    |                        | 将警报规则保存到资源组 | 选择要在其中创建此预警规则的资源组。 |
    |                        | 创建时启用预警规则 | 选中此框，即可在创建后启用此预警规则。 |

1. 选择“创建预警规则”，保存配置。

    :::image type="content" source="./media/maintenance-alerts/create-alert-rule.png" alt-text="“创建警报规则”页的屏幕截图。"::: 

## <a name="next-steps"></a>后续步骤

* 了解有关 [Azure ExpressRoute](expressroute-introduction.md)、[Network Insights](../azure-monitor/insights/network-insights-overview.md) 和 [Network Watcher](../network-watcher/network-watcher-monitoring-overview.md) 的详细信息
* [自定义指标](expressroute-monitoring-metrics-alerts.md)并创建[连接监视器](../network-watcher/connection-monitor-overview.md)

