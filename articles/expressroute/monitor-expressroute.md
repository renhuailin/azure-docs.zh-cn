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
ms.openlocfilehash: 2e54166f71071a21b84e5f58e27c4ebc8a7df03a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128587851"
---
# <a name="monitoring-azure-expressroute"></a>监视 Azure ExpressRoute

当你的关键应用程序和业务流程依赖于 Azure 资源时，你需要监视这些资源的可用性、性能和操作。 

本文介绍 Azure ExpressRoute 生成的监视数据。 Azure ExpressRoute 使用 [Azure Monitor](../azure-monitor/overview.md)。 如果你不熟悉所有 Azure 服务普遍使用的 Azure Monitor 功能，请参阅[使用 Azure Monitor 监视 Azure 资源](../azure-monitor/essentials/monitor-azure-resource.md)。
 
## <a name="expressroute-insights"></a>ExpressRoute 见解

Azure 中的某些服务在 Azure 门户中具有一个特殊且醒目的预生成仪表板，可以从此仪表板着手监视服务。 这些特殊仪表板称为“见解”。

ExpressRoute 使用网络见解提供所有 ExpressRoute 组件（对等互连、连接、网关）相互之间的的详细拓扑映射。 用于 ExpressRoute 的网络见解还具有预加载的指标仪表板，用于提供可用性、吞吐量、数据包丢弃率和网关指标。 有关详细信息，请参阅[使用网络见解的 Azure ExpressRoute 见解](expressroute-network-insights.md)。

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

可以从“Azure Monitor”菜单中打开“指标”，使用指标资源管理器根据来自其他 Azure 服务的指标分析 Azure ExpressRoute 的指标 。 有关使用此工具的详细信息，请参阅 [Azure 指标资源管理器入门](../azure-monitor/essentials/metrics-getting-started.md)。 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/metrics-page.png" alt-text="ExpressRoute 的指标仪表板的屏幕截图。":::

若要参考，可以查看 [Azure Monitor 中所有受支持的资源指标](../azure-monitor/essentials/metrics-supported.md)列表。

* 若要查看 ExpressRoute 指标，请按资源类型“ExpressRoute 线路”进行筛选   。 
* 若要查看 **Global Reach** 指标，请按资源类型“ExpressRoute 线路”进行筛选，然后选择一个已启用 Global Reach 的 ExpressRoute 线路资源。  
* 若要查看 **ExpressRoute Direct** 指标，请按“ExpressRoute 端口”筛选资源类型。  

选择指标后，将应用默认聚合。 （可选）可以应用拆分，它将显示具有不同维度的指标。

## <a name="analyzing-logs"></a>分析日志

Azure Monitor 日志中的数据以表形式存储，每个表具有自己独有的属性集。  

Azure Monitor 中的所有资源日志都具有后跟服务特定字段的相同字段。 [Azure Monitor 资源日志架构](../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema)概述了常见架构。 可在 [Azure Expressroute 数据引用](monitor-expressroute-reference.md#schemas)中找到 ExpressRoute 资源日志的架构。 

[活动日志](../azure-monitor/essentials/activity-log.md)是一种平台日志，可用于深入了解订阅级别事件。 你可以单独查看它或将它路由到 Azure Monitor 日志，然后便可以在其中使用 Log Analytics 执行复杂得多的查询。

ExpressRoute 将数据存储在以下表中。

| 表 | 说明 |
| ----- | ----------- |
| AzureDiagnostics | 由多个服务用来存储资源日志的公用表。 可以使用 `MICROSOFT.NETWORK` 识别来自 ExpressRoute 的资源日志。 |
| AzureMetrics | 由 ExpressRoute 发出的指标数据，用于衡量其运行状况和性能。 

若要查看这些表，可以导航到 ExpressRoute 线路资源并选择“监视”下的“日志”。

### <a name="sample-kusto-queries"></a>示例 Kusto 查询

可在“日志搜索”搜索栏中输入下面这些查询，以便监视 Azure ExpressRoute 资源。 这些查询使用[新语言](../azure-monitor/logs/log-query-overview.md)。

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

* 以 5 分钟为间隔查询 ArpAvailability 图示。
    
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
| ARP 可用性下降 | 维度名称：对等互连类型，聚合类型：Avg，运算符：Less than，阈值：100% | 对等互连类型的 ARP 可用性下降时。 |
| BGP 可用性下降 | 维度名称：对等机，聚合类型：Avg，运算符：Less than，阈值：100% | 对等机的 BGP 可用性下降时。 |

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
