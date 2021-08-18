---
title: 在 Application Insights 中分析 Azure Functions 遥测数据
description: 本文介绍了如何查看和查询由 Azure Application Insights 收集并存储在其中的 Azure Functions 遥测数据。
ms.topic: how-to
ms.date: 10/14/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: 530f38794e452df17aff6d7f0c890f8e9306d3e5
ms.sourcegitcommit: e0ef8440877c65e7f92adf7729d25c459f1b7549
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2021
ms.locfileid: "113568489"
---
# <a name="analyze-azure-functions-telemetry-in-application-insights"></a>在 Application Insights 中分析 Azure Functions 遥测数据 

Azure Functions 与 Application Insights 集成，以用于更好地监视函数应用。 Application Insights 收集函数应用生成的遥测数据，包括应用写入到日志的信息。 通常，在创建函数应用时会启用 Application Insights 集成。 如果函数应用未设置检测密钥，则必须先[启用 Application Insights 集成](configure-monitoring.md#enable-application-insights-integration)。 

默认情况下，从函数应用中收集的数据会存储在 Application Insights 中。 在 [Azure 门户](https://portal.azure.com)中，Application Insights 提供了一组全面的遥测数据可视化效果。 你可以深入查看错误日志，并且可以查询事件和指标。 本文提供了一些基本示例来说明如何查看和查询收集的数据。 若要详细了解如何在 Application Insights 中探索函数应用数据，请参阅[什么是 Application Insights？](../azure-monitor/app/app-insights-overview.md)。 

若要详细了解数据保留和可能产生的存储成本，请参阅 [Application Insights 中的数据收集、保留和存储](../azure-monitor/app/data-retention-privacy.md)。   

## <a name="viewing-telemetry-in-monitor-tab"></a>在“监视”选项卡中查看遥测数据

[启用 Application Insights 集成](configure-monitoring.md#enable-application-insights-integration)后，可以在“监视”选项卡中查看遥测数据。

1. 在函数应用页中，选择在配置 Application Insights 之后至少运行过一次的函数。 然后，在左窗格中选择“监视”。 定期选择“刷新”，直到显示函数调用列表。

   ![调用列表](media/functions-monitoring/monitor-tab-ai-invocations.png)

    > [!NOTE]
    > 在遥测客户端对数据进行批处理以传输到服务器时，最长可能需要 5 分钟的时间才会显示该列表。 这种延迟不适用于[实时指标流](../azure-monitor/app/live-stream.md)。 加载页面时，该服务会连接到 Functions 主机，因此，日志会直接流式传输到页面。

1. 若要查看特定函数调用的日志，选择该调用对应的“日期 (UTC)”列链接。 该调用的日志记录输出显示在新页中。

   ![调用详细信息](media/functions-monitoring/invocation-details-ai.png)

1. 选择“在 Application Insights 中运行”，以查看在 Azure 日志中检索 Azure Monitor 日志数据的查询源。 如果这是首次在订阅中使用 Azure Log Analytics，系统会要求启用它。

1. 启用 Log Analytics 后，将显示以下查询。 可以看到，查询结果仅限于最近 30 天 (`where timestamp > ago(30d)`)，而且显示的结果不超过 20 行 (`take 20`)。 相比之下，函数的调用详细信息列表显示最近 30 天的信息，没有任何限制。

   ![Application Insights Analytics 调用列表](media/functions-monitoring/ai-analytics-invocation-list.png)

有关详细信息，请参阅本文稍后的[查询遥测数据](#query-telemetry-data)。

## <a name="view-telemetry-in-application-insights"></a>在 Application Insights 中查看遥测

若要在 [Azure 门户](https://portal.azure.com)中从某个函数应用打开 Application Insights，请执行以下操作：

1. 在门户中浏览到你的函数应用。

1. 在左侧页面中，选择“设置”下方的“Application Insights” 。 

1. 如果这是你首次通过自己的订阅使用 Application Insights，系统将会提示你启用它。 为此，请选择“打开 Application Insights”，然后在下一页上选择“应用” 。

![从函数应用“概述”页面打开 Application Insights](media/functions-monitoring/ai-link.png)

有关如何使用 Application Insights 的信息，请参阅 [Application Insights 文档](/azure/application-insights/)。 本部分介绍如何在 Application Insights 中查看数据的一些示例。 如果已经熟悉 Application Insights，则可以直接转到[有关如何配置和自定义遥测数据的部分](configure-monitoring.md#configure-log-levels)。

![Application Insights“概述”选项卡](media/functions-monitoring/metrics-explorer.png)

在评估函数中的行为、性能和错误时，Application Insights 的以下方面可能会有所帮助：

| 调查 | 说明 |
| ---- | ----------- |
| **[失败](../azure-monitor/app/asp-net-exceptions.md)** |  基于函数失败和服务器异常来创建图表和警报。 操作名称是函数名称。 不显示依赖项中的失败，除非为依赖项实现了自定义遥测。 |
| **[性能](../azure-monitor/app/performance-counters.md)** | 通过查看每个 Cloud 角色实例的资源利用率和吞吐量来分析性能问题。 如果要针对函数阻碍基础资源正常工作的情况进行调试，此性能数据会很有用。 |
| **[指标](../azure-monitor/essentials/metrics-charts.md)** | 创建基于指标的图表和警报。 指标包括函数调用次数、执行时间和成功率。 |
| **[实时指标：](../azure-monitor/app/live-stream.md)** | 随着指标数据的创建，近实时地查看指标数据。 |

## <a name="query-telemetry-data"></a>查询遥测数据

借助 [Application Insights Analytics](../azure-monitor/logs/log-query-overview.md)，便可以访问数据库中以表形式存储的所有遥测数据。 Analytics 提供了一种用于提取、处理和可视化数据的查询语言。 

选择“日志”以浏览或查询记录的事件。

![Analytics 示例](media/functions-monitoring/analytics-traces.png)

下面是一个查询示例，它显示过去 30 分钟内每个辅助角色的请求的分布。

```kusto
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```

可用的表会显示在左侧的“架构”选项卡中。 可以在下表中找到由函数调用生成的数据：

| 表 | 说明 |
| ----- | ----------- |
| **traces** | 由函数代码中的运行时、规模控制器和跟踪创建的日志。 |
| **requests** | 一个请求用于一个函数调用。 |
| **异常** | 由运行时引发的任何异常。 |
| **customMetrics** | 成功和失败调用的计数、成功率和持续时间。 |
| **customEvents** | 运行时跟踪的事件，例如：触发函数的 HTTP 请求。 |
| **performanceCounters** | 有关运行函数的服务器的性能的信息。 |

其他表适用于可用性测试、客户端和浏览器遥测。 可以实现自定义遥测以向其中添加数据。

在每个表内，一些函数特定的数据位于 `customDimensions` 字段。  例如，以下查询检索所有具有日志级别 `Error` 的跟踪。

```kusto
traces 
| where customDimensions.LogLevel == "Error"
```

运行时提供了 `customDimensions.LogLevel` 和 `customDimensions.Category` 字段。 可以在日志中提供在函数代码中编写的其他字段。 有关用 C# 编写的示例，请参阅 .NET 类库开发人员指南中的[结构化日志记录](functions-dotnet-class-library.md#structured-logging)。

## <a name="query-scale-controller-logs"></a>查询规模控制器日志

_此功能为预览版。_

启用[规模控制器日志](configure-monitoring.md#configure-scale-controller-logs)和[ Application Insights 集成](configure-monitoring.md#enable-application-insights-integration)后，可以使用 Application Insights 日志搜索来查询已发出的规模控制器日志。 规模控制器日志保存在 ScaleControllerLogs 下的 `traces` 集合中。

以下查询可用于在指定时间段内搜索当前函数应用的所有规模控制器日志：

```kusto
traces 
| extend CustomDimensions = todynamic(tostring(customDimensions))
| where CustomDimensions.Category == "ScaleControllerLogs"
```

以下查询对上一个查询进行了扩展，以显示如何只获取指示规模更改的日志：

```kusto
traces 
| extend CustomDimensions = todynamic(tostring(customDimensions))
| where CustomDimensions.Category == "ScaleControllerLogs"
| where message == "Instance count changed"
| extend Reason = CustomDimensions.Reason
| extend PreviousInstanceCount = CustomDimensions.PreviousInstanceCount
| extend NewInstanceCount = CustomDimensions.CurrentInstanceCount
```

## <a name="consumption-plan-specific-metrics"></a>特定于消耗计划的指标

在[消耗计划](consumption-plan.md)中运行时，单个函数执行的执行成本以“GB 秒”来度量 。 执行成本是通过将其内存用量与执行时间相结合计算得出的。 若要了解详细信息，请参阅[估算消耗计划成本](functions-consumption-costs.md)。

以下遥测数据查询特定于对在消耗计划中运行函数的成本有影响的指标。

[!INCLUDE [functions-consumption-metrics-queries](../../includes/functions-consumption-metrics-queries.md)]

## <a name="next-steps"></a>后续步骤

请参阅以下文章，以详细了解如何监视 Azure Functions：

+ [监视 Azure Functions](functions-monitoring.md)
+ [如何配置对 Azure Functions 的监视](configure-monitoring.md)
