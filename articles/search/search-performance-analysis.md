---
title: 分析性能
titleSuffix: Azure Cognitive Search
description: TBD
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/06/2021
ms.openlocfilehash: 298508f8068b47cbfc720f1d1e8ddf370323ed28
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2021
ms.locfileid: "106582014"
---
# <a name="analyze-performance-in-azure-cognitive-search"></a>在 Azure 认知搜索中分析性能

本文介绍认知搜索中用于分析查询和索引性能的工具、行为和方法。

## <a name="develop-baseline-numbers"></a>制定基准数字

在所有大型实现中，将认知搜索服务投入生产之前，对其执行性能基准测试非常重要。 不仅应测试预期的搜索查询负载，还应测试预期的数据引入工作负载（如果可能，请同时运行这两者）。 具有基准数字有助于验证正确的[搜索层](search-sku-tier.md)、[服务配置](search-capacity-planning.md)和预期的[查询延迟](search-performance-analysis.md#average-query-latency)。

若要制定基准，建议使用 [azure-search-performance-testing (GitHub)](https://github.com/Azure-Samples/azure-search-performance-testing) 工具。

为避免分布式服务体系结构的影响，请尝试测试单个副本和单个分区的服务配置。

> [!NOTE]
> 与“标准”层相比，“存储优化”层（L1 和 L2）的查询吞吐量应更低，延迟应更高。
>

## <a name="use-diagnostic-logging"></a>使用诊断日志记录

对于管理员而言，[诊断日志记录](search-monitor-logs.md)是诊断潜在性能问题的最重要工具，它会收集有关搜索服务的操作数据和指标。 诊断日志记录通过 [Azure Monitor](../azure-monitor/overview.md) 启用。 使用 Azure Monitor 并存储数据会产生费用，但如果为服务启用此功能，将有助于调查性能问题。

无论是在网络传输期间，还是在应用服务层或搜索服务中的内容处理期间，都可能出现延迟。 诊断日志记录的主要好处是：从搜索服务角度记录活动，这意味着日志有助于确定性能问题是由查询或索引的问题引起，还是其他一些故障点引起。

:::image type="content" source="media/search-performance/perf-log-event-chain.png" alt-text="记录的事件链" border="true":::

诊断日志记录提供用于存储记录的信息的选项。 建议使用 [Log Analytics](../azure-monitor/logs/log-analytics-overview.md)，由此可对数据执行高级 Kusto 查询，从而解答有关使用情况和性能的许多问题。 

在搜索服务门户页面上，可以通过“诊断设置”启用日志记录，然后选择“日志”对 Log Analytics 发出 Kusto 查询 。 有关设置的详细信息，请参阅[收集和分析日志数据](search-monitor-logs.md)。

:::image type="content" source="media/search-performance/perf-log-menu-options.png" alt-text="日志记录菜单选项" border="true":::

## <a name="throttling-behaviors"></a>限制行为

当搜索服务从资源角度已达到其可处理的范围限制时，即会出现限制。 在查询或索引过程中可能会出现限制。 从客户端来看，API 调用在受到限制后会出现 503 HTTP 响应。 在索引过程中，还可能会收到 207 HTTP 响应，这表示一个或多个项未能编制索引。 此错误表示搜索服务接近容量。 

根据经验，最好对看到的限制进行量化。 例如，如果限制了 500,000 个搜索查询中的一个，则问题可能不大。 但是，如果在一段时间内限制了大量的查询数，这就值得警惕了。 查看一段时间内的限制还有助于确定更可能出现限制的期限，并帮助决定如何最好地解决这种情况。

解决大多数限制问题的一种简单方法是在搜索服务上投入更多资源（通常是基于查询的限制的副本，或基于索引的限制的分区）。 但增加副本或分区会增加成本，这就是为什么了解限制发生的原因很重要。 下面几节将介绍调查导致限制的情况。 

下面是一个 Kusto 查询示例，可识别来自已加载的搜索服务的 HTTP 响应的明细。 通过 7 天的查询，所呈现的条形图显示，与成功（200 个）响应的数量相比，有较大百分比的搜索查询受到了限制。

```kusto
AzureDiagnostics
| where TimeGenerated > ago(7d)
| summarize count() by resultSignature_d 
| render barchart 
```

:::image type="content" source="media/search-performance/perf-bar-chart-http-errors.png" alt-text="Http 错误计数条形图" border="true":::

检查特定时间段内的限制有助于确定限制更可能发生的时间。 在下面的示例中，时序图用于显示在指定期限内发生的受限制的查询数。 在本例中，受限制的查询数与执行性能基准测试的时间相关联。

```kusto
let ['_startTime']=datetime('2021-02-25T20:45:07Z');
let ['_endTime']=datetime('2021-03-03T20:45:07Z');
let intervalsize = 1m; 
AzureDiagnostics 
| where TimeGenerated > ago(7d)
| where resultSignature_d != 403 and resultSignature_d != 404 and OperationName in ("Query.Search", "Query.Suggest", "Query.Lookup", "Query.Autocomplete")
| summarize 
  ThrottledQueriesPerMinute=bin(countif(OperationName in ("Query.Search", "Query.Suggest", "Query.Lookup", "Query.Autocomplete") and resultSignature_d == 503)/(intervalsize/1m), 0.01)
  by bin(TimeGenerated, intervalsize)
| render timechart   
```

:::image type="content" source="media/search-performance/perf-line-chart-throttled-queries.png" alt-text="受限制的查询数的折线图" border="true":::

## <a name="measure-individual-queries"></a>测试单个查询

在某些情况下，测试单个查询以查看其执行情况会很有用。 若要执行此操作，必须能够查看搜索服务完成工作所需的时间，以及从客户端发出往返请求并返回到客户端的时间。 诊断日志可用于查找单个操作，但使用客户端工具（如 Postman）可以更轻松地执行此操作。

在下面的示例中，执行了基于 REST 的搜索查询。 认知搜索在每个响应中均包含完成查询所用的毫秒数（显示在在“标头”选项卡的“运行时间”中）。 在响应顶部的“状态”旁边，可看到往返持续时间。 在本例中为 418 毫秒。 在结果部分中，选择了“标头”选项卡。 使用下图中用红色框突出显示的这两个值，我们看到搜索服务耗时 21 毫秒完成搜索查询，而整个客户端往返请求耗时 125 毫秒。 将这两个数字相减，我们可以确定将搜索查询传输到搜索服务以及将搜索结果传输回客户端需要额外耗时 104 毫秒。

这对于确定是否有网络延迟或其他因素影响查询性能非常有用。

:::image type="content" source="media/search-performance/perf-elapsed-time.png" alt-text="查询持续时间指标" border="true":::

## <a name="query-rates"></a>查询速率

搜索服务限制请求的一个可能原因是，要执行的查询数量巨大，其中查询量捕获为每秒查询数 (QPS) 或每分钟查询数 (QPM)。 随着搜索服务接收更多的 QPS，通常需要更长时间才能响应这些查询数，最终它将无法继续运行，因为它将发送回限制 503 HTTP 响应。 

以下 Kusto 查询显示了单位为 QPM 的查询量，以及单位为毫秒的查询平均持续时间 (AvgDurationMS) 和每个查询中返回的文档平均数量 (AvgDocCountReturned)。

```kusto
AzureDiagnostics
| where OperationName == "Query.Search" and TimeGenerated > ago(1d)
| extend MinuteOfDay = substring(TimeGenerated, 0, 16) 
| project MinuteOfDay, DurationMs, Documents_d, IndexName_s
| summarize QPM=count(), AvgDuractionMs=avg(DurationMs), AvgDocCountReturned=avg(Documents_d)  by MinuteOfDay
| order by MinuteOfDay desc 
| render timechart
```

:::image type="content" source="media/search-performance/perf-line-chart-queries-per-minute.png" alt-text="显示每分钟查询数的图表" border="true":::

> [!TIP]
> 若要显示此图表后面的数据，请删除此行 `| render timechart` 然后重新运行查询。

## <a name="impact-of-indexing-on-queries"></a>对查询编制索引的影响

查看性能时要考虑的一个重要因素是，索引使用的资源与搜索查询相同。 如果正在为大量内容编制索引，则当服务尝试同时容纳这两种工作负载时，可以预计延迟会增加。

如果查询速度变慢，请查看索引活动的时间，看看它是否与查询同期发生。 例如，可能是索引器运行的是每日或每小时的作业，该作业与已降低的搜索查询性能相关联。 

本节提供一组可帮助你可视化搜索和索引速度的查询。 对于这些示例，时间范围已设置在查询中。 在 Azure 门户中运行查询时，请确保指定“在查询中设置”。

:::image type="content" source="media/search-performance/perf-set-query-time-range.png" alt-text="在查询工具中设置时间范围" border="true":::

<a name="average-query-latency"></a>

### <a name="average-query-latency"></a>平均查询延迟

在下面的查询中，时间间隔大小为 1 分钟，用于显示搜索查询的平均延迟。 在该图中，可以看到直到下午 5:45 平均延迟很低，并且持续到下午 5:53。

```kusto
let intervalsize = 1m; 
let _startTime = datetime('2021-02-23 17:40');
let _endTime = datetime('2021-02-23 18:00');
AzureDiagnostics
| where TimeGenerated between(['_startTime']..['_endTime']) // Time range filtering
| summarize AverageQueryLatency = avgif(DurationMs, OperationName in ("Query.Search", "Query.Suggest", "Query.Lookup", "Query.Autocomplete"))
    by bin(TimeGenerated, intervalsize)
| render timechart
```

:::image type="content" source="media/search-performance/perf-line-chart-average-query-latency.png" alt-text="显示平均查询延迟的图表" border="true":::

### <a name="average-queries-per-minute-qpm"></a>平均每分钟查询数 (QPM)

使用下面的查询，我们可以查看平均每分钟查询数，以确保在搜索请求中没有某种峰值（可能会影响延迟）。 从图表中可以看出存在一些波动，但没有任何迹象表明请求计数峰值。

```kusto
let intervalsize = 1m; 
let _startTime = datetime('2021-02-23 17:40');
let _endTime = datetime('2021-02-23 18:00');
AzureDiagnostics
| where TimeGenerated between(['_startTime'] .. ['_endTime']) // Time range filtering
| summarize QueriesPerMinute=bin(countif(OperationName in ("Query.Search", "Query.Suggest", "Query.Lookup", "Query.Autocomplete"))/(intervalsize/1m), 0.01)
  by bin(TimeGenerated, intervalsize)
| render timechart
```

:::image type="content" source="media/search-performance/perf-line-chart-average-queries-per-minute.png" alt-text="显示平均每分钟查询数的图表" border="true":::

### <a name="indexing-operations-per-minute-opm"></a>每分钟索引操作数 (OPM)

下面我们将介绍每分钟索引操作的数量。 在该图中，可以看到从下午 5:42 开始对大量数据编制索引，并在下午 5:50 结束。 该编制索引操作在搜索查询开始出现延迟前 3 分钟开始，并在搜索查询不再延迟前 3 分钟结束。

从中我们可以看到，从编制索引操作开始，搜索服务花了大约 3 分钟的时间忙碌起来，开始影响搜索查询延迟。 可以看到，在编制索引操作完成后，搜索服务额外需要 3 分钟才能完成新索引内容中的所有工作，直到搜索查询开始不再延迟。

```kusto
let intervalsize = 1m; 
let _startTime = datetime('2021-02-23 17:40');
let _endTime = datetime('2021-02-23 18:00');
AzureDiagnostics
| where TimeGenerated between(['_startTime'] .. ['_endTime']) // Time range filtering
| summarize IndexingOperationsPerSecond=bin(countif(OperationName == "Indexing.Index")/ (intervalsize/1m), 0.01)
  by bin(TimeGenerated, intervalsize)
| render timechart 
```

:::image type="content" source="media/search-performance/perf-line-chart-indexing-operations.png" alt-text="显示每分钟索引操作的图表" border="true":::

## <a name="background-service-processing"></a>后台服务处理

在查询或索引延迟中查看定期峰值并不罕见。 由于索引或查询率高，可能会出现峰值，但合并操作期间也可能出现。 搜索索引以区块或分片存储。 系统会定期将较小的分片合并为大型分片，这有助于优化服务性能。 此合并过程还会清除以前标记为要从索引中删除的文档，从而恢复存储空间。 

合并分片的速度很快，但会占用大量资源，因此可能会降低服务性能。 出于此原因，如果发现短暂的查询延迟突发情况，且这些突发情况与索引内容的最近更改同期发生，则该延迟可能由分片合并操作导致。 

## <a name="next-steps"></a>后续步骤

查看以下与分析服务性能相关的其他文章。

+ [性能提示](search-performance-tips.md)
+ [选择服务层](search-sku-tier.md)
+ [管理容量](search-capacity-planning.md)
