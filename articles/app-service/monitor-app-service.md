---
title: 使用 Azure Monitor 监视应用服务
description: 通过本文开始了解如何监视应用服务
author: msangapu-msft
ms.author: msangapu
ms.topic: article
ms.service: app-service
ms.custom: subject-monitoring
ms.date: 04/16/2021
ms.openlocfilehash: 537173792c4e14ac1dfdb142d8ab15e3e68e5649
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114230579"
---
# <a name="monitoring-app-service"></a>监视应用服务

当你的关键应用程序和业务流程依赖于 Azure 资源时，你需要监视这些资源的可用性、性能和操作。 本文介绍由应用服务生成的并传送到 [Azure Monitor](../azure-monitor/overview.md) 的监视数据。 你还可以使用[内置诊断来监视资源](troubleshoot-diagnostic-logs.md)，以帮助调试应用服务应用。 如果你不熟悉所有 Azure 服务普遍使用的 Azure Monitor 功能，请参阅[使用 Azure Monitor 监视 Azure 资源](../azure-monitor/essentials/monitor-azure-resource.md)。

> [!NOTE]
> Azure Monitor 与应用服务的集成目前以[预览版](https://aka.ms/appsvcblog-azmon)提供。
>

## <a name="monitoring-data"></a>监视数据 

应用服务收集与[监视 Azure 资源中的数据](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)中所述的其他 Azure 资源相同的监视数据。 

有关应用服务创建的指标和日志指标的详细信息，请参阅[监视“应用服务”数据参考](monitor-app-service-reference.md)。

应用服务还提供内置诊断来帮助调试应用。 有关启用内置日志的详细信息，请参阅[启用诊断日志记录](troubleshoot-diagnostic-logs.md)。 若要监视应用服务实例，请参阅[使用运行状况检查监视应用服务实例](monitor-instances-health-check.md)。

## <a name="collection-and-routing"></a>收集和路由

平台指标和活动日志会自动收集和存储，但你可以使用诊断设置将其路由到其他位置。  

在创建诊断设置并将其路由到一个或多个位置之前，不会收集和存储资源日志。

有关使用 Azure 门户、CLI 或 PowerShell 创建诊断设置的详细过程，请参阅[创建诊断设置以收集 Azure 中的平台日志和指标](../azure-monitor/essentials/diagnostic-settings.md)。 创建诊断设置时，请指定要收集的日志类别。 “应用服务”的类别在[应用服务监视数据参考](monitor-app-service-reference.md#resource-logs)中列出。

以下部分将讨论可以收集的指标和日志。

## <a name="analyzing-metrics"></a>分析指标

可以从“Azure Monitor”菜单中打开“指标”，使用指标资源管理器根据来自其他 Azure 服务的指标分析应用服务的指标 。 有关使用此工具的详细信息，请参阅 [Azure 指标资源管理器入门](../azure-monitor/essentials/metrics-getting-started.md)。 

有关为应用服务收集的平台指标的列表，请参阅[监视应用服务数据参考指标](monitor-app-service-reference.md#metrics)  

若要参考，可以查看 [Azure Monitor 中所有受支持的资源指标](../azure-monitor/essentials/metrics-supported.md)列表。

## <a name="analyzing-logs"></a>分析日志

Azure Monitor 日志中的数据以表形式存储，每个表具有自己独有的属性集。  

Azure Monitor 中的所有资源日志都具有后跟服务特定字段的相同字段。 [Azure Monitor 资源日志架构](troubleshoot-diagnostic-logs.md#send-logs-to-azure-monitor-preview)概述了常见架构。

[活动日志](../azure-monitor/essentials/activity-log.md)是一种平台日志，可用于深入了解订阅级别的事件。 可以单独查看它或将它路由到 Azure Monitor 日志。 路由到 Azure Monitor 日志的好处是可以使用 Log Analytics 运行复杂的查询。

有关为应用服务收集的资源日志类型的列表，请参阅[监视应用服务数据参考](monitor-app-service-reference.md#resource-logs)  

有关 Azure Monitor 日志和 Log Analytics 使用的可查询表的列表，请参阅[监视应用服务数据参考](monitor-app-service-reference.md#azure-monitor-logs-tables)。  

### <a name="sample-kusto-queries"></a>示例 Kusto 查询

> [!IMPORTANT]
> 在“应用服务”菜单中选择“日志”时，Log Analytics 随即打开，其查询范围设置为当前资源。 这意味着日志查询只包含来自该资源的数据。 如果要运行的查询包含来自他 [资源] 或其他 Azure 服务的数据，请在“Azure Monitor”菜单中选择“日志” 。 有关详细信息，请参阅 [Azure Monitor Log Analytics 中的日志查询范围和时间范围](/azure/azure-monitor/log-query/scope/)。

以下示例查询可帮助你使用 `AppServiceAppLogs` 监视应用日志：

```Kusto
AppServiceAppLogs 
| project CustomLevel, _ResourceId
| summarize count() by CustomLevel, _ResourceId
```

以下示例查询可帮助你使用 `AppServiceHTTPLogs` 监视 HTTP 日志（其中的 `HTTP response code` 为 `500` 或更大）：

```Kusto
AppServiceHTTPLogs 
//| where ResourceId = "MyResourceId" // Uncomment to get results for a specific resource Id when querying over a group of Apps
| where ScStatus >= 500
| reduce by strcat(CsMethod, ':\\', CsUriStem)
```

以下示例查询可帮助你通过联接 `AppServiceConsoleLogs` 和 `AppserviceHTTPLogs` 来监视 HTTP 500 错误：

```Kusto
let myHttp = AppServiceHTTPLogs | where  ScStatus == 500 | project TimeGen=substring(TimeGenerated, 0, 19), CsUriStem, ScStatus;  

let myConsole = AppServiceConsoleLogs | project TimeGen=substring(TimeGenerated, 0, 19), ResultDescription;

myHttp | join myConsole on TimeGen | project TimeGen, CsUriStem, ScStatus, ResultDescription;   
```

有关更多示例查询，请参阅[应用服务的 Azure Monitor 查询](https://github.com/microsoft/AzureMonitorCommunity/tree/master/Azure%20Services/App%20Services/Queries)。

## <a name="alerts"></a>警报

在监视数据中发现重要情况时，Azure Monitor 警报会主动通知你。 有了警报，你就可以在客户注意到你的系统中的问题之前确定和解决它们。 可以在[指标](../azure-monitor/alerts/alerts-metric-overview.md)、[日志](../azure-monitor/alerts/alerts-unified-log.md)和[活动日志](../azure-monitor/alerts/activity-log-alerts.md)上设置警报。

如果你在应用服务上运行应用程序，[Azure Monitor Application Insights](../azure-monitor/overview.md#application-insights) 可以提供其他类型的警报。

下表列出了对应用服务常用和建议使用的警报规则。

| 警报类型 | 条件 | 示例  |
|:---|:---|:---|
| 指标 | 平均连接数| 当连接数超过设置的值时|
| 指标 | HTTP 404| 当 HTTP 404 响应数超过设置的值时|
| 指标 | HTTP 服务器错误数| 当 HTTP 5xx 错误数超过设置的值时|
| 活动日志 | 创建或更新 Web 应用 | 创建或更新应用时|
| 活动日志 | 删除 Web 应用 | 删除应用时|
| 活动日志 | 重新启动 Web 应用| 重启应用时|
| 活动日志 | 停止 Web 应用| 停止应用时|

## <a name="next-steps"></a>后续步骤

- 有关应用服务创建的指标、日志和其他重要值的参考信息，请参阅[监视应用服务数据参考](monitor-app-service-reference.md)。

- 有关监视 Azure 资源的详细信息，请参阅[使用 Azure Monitor 监视 Azure 资源](../azure-monitor/essentials/monitor-azure-resource.md)。