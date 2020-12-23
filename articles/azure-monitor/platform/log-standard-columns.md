---
title: Azure Monitor 日志记录中的标准列 | Microsoft Docs
description: 介绍 Azure Monitor 日志中多种数据类型共有的列。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/09/2020
ms.openlocfilehash: 2370f76bacb8645f1b343da4f056c8bcf06a26dd
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2020
ms.locfileid: "95796724"
---
# <a name="standard-columns-in-azure-monitor-logs"></a>Azure Monitor 日志中的标准列
Azure Monitor 日志中的数据[作为一组记录存储在 Log Analytics 工作区或 Application Insights 应用程序](./data-platform-logs.md)中，每条记录都具有特定的数据类型，该数据类型包含一组惟一的列。 许多数据类型都具有在多种类型中通用的标准列。 本文介绍这些列，并提供如何在查询中使用它们的示例。

Application Insights 中基于工作区的应用程序将其数据存储在 Log Analytics 工作区中，并使用与该工作区中其他表相同的标准列。 经典应用程序单独存储其数据，并具有本文所述的不同标准列。

> [!NOTE]
> 某些标准列不会显示在 Log Analytics 的架构视图或 intellisense 中，也不会显示在查询结果中，除非在输出中显式指定该列。

## <a name="timegenerated-and-timestamp"></a>TimeGenerated 和 timestamp
TimeGenerated（Log Analytics 工作区）和 timestamp（Application Insights 应用程序）列包含数据源创建记录的日期和时间 。 有关更多详细信息，请参阅 [Azure Monitor 中的日志数据引入时间](data-ingestion-time.md)。

TimeGenerated 和 timestamp 提供了一个用于按时间进行筛选或汇总的常用列 。 为 Azure 门户中的视图或仪表板选择时间范围时，它使用 TimeGenerated 或 timestamp 来筛选结果。 

### <a name="examples"></a>示例

以下查询返回过去一周内每天创建的错误事件数。

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by TimeGenerated asc 
```

以下查询返回过去一周内每天产生的异常数。

```Kusto
exceptions
| where timestamp between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by timestamp asc 
```

## <a name="_timereceived"></a>\_TimeReceived
**\_TimeReceived** 列包含 Azure 云中的 Azure Monitor 引入点收到记录的日期和时间。 这对于查明数据源与云之间的延迟问题非常有用。 例如，网络问题会导致从代理发送数据时出现延迟。 有关更多详细信息，请参阅 [Azure Monitor 中的日志数据引入时间](data-ingestion-time.md)。

对于某个代理提供的事件记录，以下查询提供了按小时计的的平均延迟。 这包括从代理到云的时间，以及记录可供日志查询使用的总时间。

```Kusto
Event
| where TimeGenerated > ago(1d) 
| project TimeGenerated, TimeReceived = _TimeReceived, IngestionTime = ingestion_time() 
| extend AgentLatency = toreal(datetime_diff('Millisecond',TimeReceived,TimeGenerated)) / 1000
| extend TotalLatency = toreal(datetime_diff('Millisecond',IngestionTime,TimeGenerated)) / 1000
| summarize avg(AgentLatency), avg(TotalLatency) by bin(TimeGenerated,1hr)
``` 

## <a name="type-and-itemtype"></a>Type 和 itemType
Type（Log Analytics 工作区）和 itemType（Application Insights 应用程序）列保存从中检索记录的表的名称，也可以将其视为记录类型 。 此列在将多个表的记录进行组合的查询中非常有用，例如，使用 `search` 运算符区分不同类型的记录的那些查询。 在某些地方， **$table** 可以用来替代 **Type**。

### <a name="examples"></a>示例
以下查询返回过去一小时内按类型收集的记录计数。

```Kusto
search * 
| where TimeGenerated > ago(1h)
| summarize count() by Type

```
## <a name="_itemid"></a>\_ItemId
**\_ItemId** 列保留记录的唯一标识符。


## <a name="_resourceid"></a>\_ResourceId
**\_ResourceId** 列包含与记录关联的资源的唯一标识符。 这为你提供了一个标准列，用于将查询范围限定为仅来自特定资源的记录，或者跨多个表联接相关数据。

对于 Azure 资源， **_ResourceId** 的值是 [Azure 资源 ID URL](../../azure-resource-manager/templates/template-functions-resource.md)。 此列仅限于 Azure 资源，包括 [Azure Arc](../../azure-arc/overview.md) 资源，或用于在引入过程中指明资源 ID 的自定义日志。

> [!NOTE]
> 某些数据类型已具有包含 Azure 资源 ID 或至少包含其一部分（例如订阅 ID）的字段。 虽然为了实现向后兼容而保留了这些字段，但是建议使用 _ResourceId 来执行交叉关联，因为它将更为一致。

### <a name="examples"></a>示例
以下查询联接每台计算机的性能和事件数据。 它显示 ID 为 _101_ 且处理器利用率超过 50% 的所有事件。

```Kusto
Perf 
| where CounterName == "% User Time" and CounterValue  > 50 and _ResourceId != "" 
| join kind=inner (     
    Event 
    | where EventID == 101 
) on _ResourceId
```

以下查询将 _AzureActivity_ 记录与 _SecurityEvent_ 记录进行联接。 它显示了登录到这些计算机的用户的所有活动操作。

```Kusto
AzureActivity 
| where  
    OperationName in ("Restart Virtual Machine", "Create or Update Virtual Machine", "Delete Virtual Machine")  
    and ActivityStatus == "Succeeded"  
| join kind= leftouter (    
   SecurityEvent 
   | where EventID == 4624  
   | summarize LoggedOnAccounts = makeset(Account) by _ResourceId 
) on _ResourceId  
```

以下查询将分析每个 Azure 资源组 **_ResourceId** 和聚合计费数据量。

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by resourceGroup | sort by Bytes nulls last 
```

请谨慎使用这些 `union withsource = tt *` 查询，因为跨数据类型执行扫描的开销很大。

使用 \_ SubscriptionId 列比通过分析 ResourceId 列来提取它更有效 \_ 。

## <a name="_substriptionid"></a>\_SubstriptionId
**\_ SubscriptionId** 列包含与该记录关联的资源的订阅 ID。 这为您提供了一种标准列，用于将查询的作用域限定为特定订阅中的记录，或用于比较不同的订阅。

对于 Azure 资源， **__SubscriptionId** 的值是 [AZURE 资源 ID URL](../../azure-resource-manager/templates/template-functions-resource.md)中的订阅部分。 此列仅限于 Azure 资源，包括 [Azure Arc](../../azure-arc/overview.md) 资源，或用于在引入过程中指明资源 ID 的自定义日志。

> [!NOTE]
> 某些数据类型已包含包含 Azure 订阅 ID 的字段。 虽然保留这些字段以实现向后兼容性，但建议使用 " \_ SubscriptionId" 列执行交叉关联，因为它会更一致。
### <a name="examples"></a>示例
下面的查询检查特定订阅的计算机的性能数据。 

```Kusto
Perf 
| where TimeGenerated > ago(24h) and CounterName == "memoryAllocatableBytes"
| where _SubscriptionId == "57366bcb3-7fde-4caf-8629-41dc15e3b352"
| summarize avgMemoryAllocatableBytes = avg(CounterValue) by Computer
```

以下查询分析 **_ResourceId** 并聚合每个 Azure 订阅的计费数据量。

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by _SubscriptionId | sort by Bytes nulls last 
```

请谨慎使用这些 `union withsource = tt *` 查询，因为跨数据类型执行扫描的开销很大。


## <a name="_isbillable"></a>\_IsBillable
**\_IsBillable** 列指定是否对引入的数据进行计费。 **\_IsBillable** 等于 `false` 的数据是免费收集的，系统不会向你的 Azure 帐户收费。

### <a name="examples"></a>示例
若要获取发送计费数据类型的计算机列表，请使用以下查询：

> [!NOTE]
> 请谨慎使用带 `union withsource = tt *` 的查询，因为跨数据类型执行扫描的开销很大。 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

这可以扩展为返回每小时发送计费数据类型的计算机数量：

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## <a name="_billedsize"></a>\_BilledSize
**\_BilledSize** 列指定 **\_IsBillable** 为 true 时将向 Azure 帐户计费的数据字节大小。


### <a name="examples"></a>示例
若要查看每台计算机引入的可计费事件的大小，请使用 `_BilledSize` 列（以字节为单位提供大小）：

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  Computer | sort by Bytes nulls last 
```

若要查看每个订阅引入的可计费事件大小，请使用以下查询：

```Kusto
union withsource=table * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  _SubscriptionId | sort by Bytes nulls last 
```

若要查看每个资源组引入的可计费事件大小，请使用以下查询：

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/resourcegroups/" ResourceGroupName "/" *
| summarize Bytes=sum(_BilledSize) by  _SubscriptionId, ResourceGroupName | sort by Bytes nulls last 

```


若要查看每台计算机引入的事件数，请使用以下查询：

```Kusto
union withsource = tt *
| summarize count() by Computer | sort by count_ nulls last
```

若要查看每台计算机引入的可计费事件数，请使用以下查询： 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize count() by Computer  | sort by count_ nulls last
```

若要查看特定计算机的可计费数据类型的计数，请使用以下查询：

```Kusto
union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last 
```

## <a name="next-steps"></a>后续步骤

- 详细了解如何[存储 Azure Monitor 日志数据](../log-query/log-query-overview.md)。
- 获取有关[编写日志查询](../log-query/get-started-queries.md)的课程。
- 获取有关[在日志查询中联接表](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#joins)的课程。
