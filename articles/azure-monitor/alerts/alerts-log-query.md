---
title: 优化日志警报查询 | Microsoft Docs
description: 有关编写高效警报查询的建议
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 82f40d33d0465a7981105472ccd0487e7e24da80
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128635121"
---
# <a name="optimizing-log-alert-queries"></a>优化日志警报查询
本文介绍如何编写和转换[日志警报](./alerts-unified-log.md)查询，以获得最佳性能。 经过优化的查询可降低延迟并加载频繁运行的警报。

## <a name="how-to-start-writing-an-alert-log-query"></a>如何开始编写警报日志查询

警报查询开始于[查询 Log Analytics 中指示问题的日志数据](alerts-log.md#create-a-log-alert-rule-with-the-azure-portal)。 可以使用[警报查询示例主题](../logs/queries.md)来了解可发现的内容。 还可以[开始编写你自己的查询](../logs/log-analytics-tutorial.md)。 

### <a name="queries-that-indicate-the-issue-and-not-the-alert"></a>指示问题而非警报的查询

生成警报流是为了将指示问题的结果转换为警报。 例如，在如下查询中：

``` Kusto
SecurityEvent
| where EventID == 4624
```

如果用户的意图是发出警报，那么当这个事件类型发生时，警报逻辑会将 `count` 追加到查询中。 要运行的查询将如下所示：

``` Kusto
SecurityEvent
| where EventID == 4624
| count
```

无需向查询添加警报逻辑，这样做甚至可能导致问题。 在上面的示例中，如果你在查询中包括 `count`，它将始终得到值 1，因为警报服务将执行 `count` 的 `count`。

### <a name="avoid-limit-and-take-operators"></a>避免使用 `limit` 和 `take` 运算符

在查询中使用 `limit` 和 `take` 可能会增加警报的延迟和负载，因为结果会随着时间的推移而产生不一致。 最好仅在必要时才使用它。

## <a name="log-query-constraints"></a>日志查询约束
[Azure Monitor 中的日志查询](../logs/log-query-overview.md)以 table、[`search`](/azure/kusto/query/searchoperator) 或 [`union`](/azure/kusto/query/unionoperator) 运算符开头。

对日志预警规则的查询应始终以表开始，以定义明确的范围，从而提高查询性能和结果的相关性。 预警规则中的查询经常运行，因此使用 `search` 和 `union` 会造成额外开销，并增加警报的延迟，因为它需要扫描多个表。 这些运算符还会降低警报服务优化查询的性能。

除了跨资源查询，我们不支持创建或修改使用 `search` 或 `union` 运算符的日志警报规则。

例如，以下警报查询的范围限定为 SecurityEvent 表，并搜索特定的事件 ID。 这是查询必须处理的唯一的表。

``` Kusto
SecurityEvent
| where EventID == 4624
```

使用[跨资源查询](../logs/cross-workspace-query.md)的日志预警规则不受此更改的影响，因为跨资源查询使用 `union` 类型，这会将查询范围限制为特定资源。 以下示例将是有效的日志警报查询：

```Kusto
union
app('Contoso-app1').requests,
app('Contoso-app2').requests,
workspace('Contoso-workspace1').Perf 
```

>[!NOTE]
> 新的 [scheduledQueryRules API](/rest/api/monitor/scheduledqueryrules) 支持[跨资源查询](../logs/cross-workspace-query.md)。 如果仍使用[旧版 Log Analytics 警报 API](./api-alerts.md) 来创建日志警报，可在[此处](../alerts/alerts-log-api-switch.md)了解如何切换。

## <a name="examples"></a>示例
以下示例包括使用 `search` 和 `union` 的日志查询，并提供可用于修改这些查询以与预警规则配合使用的步骤。

### <a name="example-1"></a>示例 1
想要使用以下查询创建日志预警规则，该查询使用 `search` 检索性能信息： 

``` Kusto
search *
| where Type == 'Perf' and CounterName == '% Free Space'
| where CounterValue < 30
```

若要修改此查询，请首先使用以下查询来标识属性所属的表：

``` Kusto
search *
| where CounterName == '% Free Space'
| summarize by $table
```

此查询结果将显示 CounterName 属性来自 Perf 表 。

可使用此结果创建以下查询，可将该查询用于预警规则：

``` Kusto
Perf
| where CounterName == '% Free Space'
| where CounterValue < 30
```

### <a name="example-2"></a>示例 2
想要使用以下查询创建日志预警规则，该查询使用 `search` 检索性能信息： 

``` Kusto
search ObjectName =="Memory" and CounterName=="% Committed Bytes In Use"
| summarize Avg_Memory_Usage =avg(CounterValue) by Computer
| where Avg_Memory_Usage between(90 .. 95)  
```

若要修改此查询，请首先使用以下查询来标识属性所属的表：

``` Kusto
search ObjectName=="Memory" and CounterName=="% Committed Bytes In Use"
| summarize by $table
```

此查询结果将显示 ObjectName 和 CounterName 属性来自 Perf 表  。

可使用此结果创建以下查询，可将该查询用于预警规则：

``` Kusto
Perf
| where ObjectName =="Memory" and CounterName=="% Committed Bytes In Use"
| summarize Avg_Memory_Usage=avg(CounterValue) by Computer
| where Avg_Memory_Usage between(90 .. 95)
```

### <a name="example-3"></a>示例 3

想要使用以下查询创建日志预警规则，该查询使用 `search` 和 `union` 检索性能信息： 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")
| where Computer !in (
    union *
    | where CounterName == "% Processor Utility"
    | summarize by Computer)
| summarize Avg_Idle_Time = avg(CounterValue) by Computer
```

若要修改此查询，请首先使用以下查询来标识查询第一部分中属性所属的表： 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")
| summarize by $table
```

此查询结果将显示所有这些属性来自 Perf 表。

现在，将 `union` 与 `withsource` 命令配合使用，确定哪个源表提供了每行。

``` Kusto
union withsource=table *
| where CounterName == "% Processor Utility"
| summarize by table
```

此查询结果将显示这些属性也来自 Perf 表。

可使用这些结果创建以下查询，可将该查询用于预警规则： 

``` Kusto
Perf
| where ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total"
| where Computer !in (
    (Perf
    | where CounterName == "% Processor Utility"
    | summarize by Computer))
| summarize Avg_Idle_Time = avg(CounterValue) by Computer
``` 

### <a name="example-4"></a>示例 4
想要使用以下查询创建日志预警规则，该查询联接两条 `search` 查询的结果：

```Kusto
search Type == 'SecurityEvent' and EventID == '4625'
| summarize by Computer, Hour = bin(TimeGenerated, 1h)
| join kind = leftouter (
    search in (Heartbeat) OSType == 'Windows'
    | summarize arg_max(TimeGenerated, Computer) by Computer , Hour = bin(TimeGenerated, 1h)
    | project Hour , Computer
) on Hour
```

若要修改此查询，请首先使用以下查询来标识包含左侧联接中属性的表： 

``` Kusto
search Type == 'SecurityEvent' and EventID == '4625'
| summarize by $table
```

结果指示左侧联接中的属性属于 SecurityEvent 表。 

现在使用以下查询来标识包含右侧联接中属性的表： 
 
``` Kusto
search in (Heartbeat) OSType == 'Windows'
| summarize by $table
```
 
结果指示右侧联接中的属性属于 Heartbeat 表。

可使用这些结果创建以下查询，可将该查询用于预警规则： 

``` Kusto
SecurityEvent
| where EventID == '4625'
| summarize by Computer, Hour = bin(TimeGenerated, 1h)
| join kind = leftouter (
    Heartbeat
    | where OSType == 'Windows'
    | summarize arg_max(TimeGenerated, Computer) by Computer , Hour = bin(TimeGenerated, 1h)
    | project Hour , Computer
) on Hour
```

## <a name="next-steps"></a>后续步骤
- 了解 Azure Monitor 中的[日志警报](alerts-log.md)。
- 了解[日志查询](../logs/log-query-overview.md)。
