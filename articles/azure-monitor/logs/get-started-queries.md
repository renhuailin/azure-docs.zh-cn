---
title: Azure Monitor 中的日志查询入门 | Microsoft Docs
description: 本文提供了有关在 Azure Monitor 中编写日志查询的入门教程。
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 10/24/2019
ms.openlocfilehash: cc06241e948f2f82440d6e41f5a7a26639a54a9a
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2021
ms.locfileid: "122418727"
---
# <a name="get-started-with-log-queries-in-azure-monitor"></a>Azure Monitor 中的日志查询入门

> [!NOTE]
> 如果要从至少一个虚拟机收集数据，你可以在自己的环境中完成此练习。 对于其他应用场景，请使用[演示环境](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade)，它包含大量示例数据。  
>
> 如果已知道如何使用 Kusto 查询语言进行查询，但需要快速创建基于资源类型的有用查询，请参阅[在 Azure Monitor Log Analytics 中使用查询](../logs/queries.md)一文中的保存的示例查询窗格。

在本教程中，你将学习在 Azure Monitor 中编写日志查询。 本文介绍以下操作：

- 了解查询结构。
- 将查询结果排序。
- 筛选查询结果。
- 指定时间范围。
- 选择结果中要包含的字段。
- 定义和使用自定义字段。
- 聚合结果和将结果分组。

有关在 Azure 门户中使用 Log Analytics 的教程，请参阅 [Azure Monitor Log Analytics 入门](./log-analytics-tutorial.md)。

有关 Azure Monitor 中日志查询的详细信息，请参阅 [Azure Monitor 中的日志查询概述](../logs/log-query-overview.md)。

下面是本教程的视频版本：

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE42pGX]

## <a name="write-a-new-query"></a>编写新查询

查询可以从表名或 *search* 命令开始。 最好从表名开始，因为它为查询定义了明确的范围，可改善查询性能和提高结果的相关性。

> [!NOTE]
> Azure Monitor 所使用的 Kusto 查询语言区分大小写。 语言关键字通常采用小写形式。 在查询中使用表名或列名时，请确保使用正确的大小写，如架构窗格中所示。

### <a name="table-based-queries"></a>基于表的查询

Azure Monitor 在表中组织日志数据，每个表由多个列组成。 所有表和列都显示在 Analytics 门户中的 Log Analytics 中的架构窗格内。 找到所需的表，然后看看其中的一些数据：

```Kusto
SecurityEvent
| take 10
```

上面的查询从“SecurityEvent”表返回 10 个结果（无特定顺序）。 这是浏览表并了解其结构和内容的常用方法。 让我们探讨表的生成方式：

* 查询从表名“SecurityEvent”开始，表名定义了查询范围。
* 管道字符 (|) 用来分隔命令，第一个命令的输出是下一个命令的输入。 可以添加任意数目的管道元素。
* 管道下面是 **take** 命令，它从表中返回特定数目的任意记录。

实际上，即使不添加 `| take 10`，也可运行查询。 该查询仍然有效，只不过它会返回多达 10,000 个结果。

### <a name="search-queries"></a>搜索查询

搜索查询的结构较简单，通常更适合用于查找在任何列中包含特定值的记录：

```Kusto
search in (SecurityEvent) "Cryptographic"
| take 10
```

此查询在 *SecurityEvent* 表中搜索包含短语“Cryptographic”的记录。 返回并显示了其中的 10 条记录。 如果省略 `in (SecurityEvent)` 部分，只运行 `search "Cryptographic"`，搜索将遍历所有表，这样做花费的时间更长且效率更低。

> [!IMPORTANT]
> 搜索查询通常比基于表的查询更慢，因为它们必须处理更多的数据。 

## <a name="sort-and-top"></a>sort 和 top
虽然 take 可用于获取一些记录，但结果的选择和显示不遵循特定的顺序。 若要获取排序的视图，可按首选列 **排序**：

```Kusto
SecurityEvent   
| sort by TimeGenerated desc
```

但是，搜索查询可能会返回太多的结果，这可能会花费一些时间。 此查询按 TimeGenerated 列将整个 SecurityEvent 表排序。 而 Analytics 门户仅限显示 10,000 条记录。 当然，这种方法不是最佳的。

仅获取最新 10 条记录的最佳方式是使用 **top**，它会在服务器端将整个表排序，然后返回前几条记录：

```Kusto
SecurityEvent
| top 10 by TimeGenerated
```

降序是默认的排序顺序，所以通常可省略 desc 参数。 输出如下所示：

![前 10 条记录的屏幕截图，按降序排序。](media/get-started-queries/top10.png)


## <a name="the-where-operator-filtering-on-a-condition"></a>where 运算符：按条件筛选
顾名思义，筛选器可按特定的条件筛选数据。 这是将查询结果限制为相关信息的最常用方法。

若要将筛选器添加到查询，请使用 **where** 运算符，后接一个或多个条件。 例如，以下查询只返回 _Level_ 等于 _8_ 的 *SecurityEvent* 记录：

```Kusto
SecurityEvent
| where Level == 8
```

编写筛选条件时，可使用以下表达式：

| 表达式 | 描述 | 示例 |
|:---|:---|:---|
| == | 检查相等性<br>（区分大小写） | `Level == 8` |
| =~ | 检查相等性<br>（不区分大小写） | `EventSourceName =~ "microsoft-windows-security-auditing"` |
| !=, <> | 检查不相等性<br>（两个表达式相同） | `Level != 4` |
| *and*、*or* | 需在条件之间使用| `Level == 16 or CommandLine != ""` |

若要按多个条件筛选，可使用以下方法之一：

使用“and”，如下所示：

```Kusto
SecurityEvent
| where Level == 8 and EventID == 4672
```

使用管道符逐一分隔多个“where”元素，如下所示：

```Kusto
SecurityEvent
| where Level == 8 
| where EventID == 4672
```
    
> [!NOTE]
> 值可以是不同的类型，因此可能需要将其强制转换，以针对正确的类型执行比较。 例如，SecurityEvent 中“Level”列为 String 类型，必须先将其强制转换为“int”或“long”等数字类型，然后才能对其使用数字运算符，例如：`SecurityEvent | where toint(Level) >= 10`  

## <a name="specify-a-time-range"></a>指定时间范围

### <a name="use-the-time-picker"></a>使用时间选取器

时间选取器显示在“运行”按钮旁，指示你只查询过去 24 小时的记录。 这是应用到所有查询的默认时间范围。 如果只需过去一个小时的记录，请选择“过去一小时”并再次运行查询。

![时间选取器及其时间范围命令列表的屏幕截图。](media/get-started-queries/timepicker.png)


### <a name="add-a-time-filter-to-the-query"></a>向查询添加时间筛选器

还可以通过将时间筛选器添加到查询来定义自己的时间范围。 最好是紧靠在表名的后面添加时间筛选器： 

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m) 
| where toint(Level) >= 10
```

在前面的时间筛选器中，`ago(30m)` 表示“30 分钟前”，这意味着此查询仅返回过去 30 分钟的记录（例如，表示为 30m）。 其他时间单位包括天（例如 2d）以及秒（例如 10s）。


## <a name="use-project-and-extend-to-select-and-compute-columns"></a>使用 project 和 extend 来选择和计算列

使用 **投影** 可以选择要包含在结果中的特定列：

```Kusto
SecurityEvent 
| top 10 by TimeGenerated 
| project TimeGenerated, Computer, Activity
```

上面的示例生成以下输出：

![project 查询的结果列表的屏幕截图。](media/get-started-queries/project.png)

还可以使用 **投影** 来重命名列，并定义新列。 下一个示例使用 project 执行以下操作：

* 仅选择 *Computer* 和 *TimeGenerated* 原始列。
* 将 Activity 列显示为 EventDetails 。
* 创建名为 *EventCode* 的新列。 **substring()** 函数用于仅获取 Activity 字段中的前四个字符。


```Kusto
SecurityEvent
| top 10 by TimeGenerated 
| project Computer, TimeGenerated, EventDetails=Activity, EventCode=substring(Activity, 0, 4)
```

可使用 extend 来保留结果集中的所有原始列并定义其他列。 以下查询使用 **extend** 添加 *EventCode* 列。 此列可能不会显示在表结果的末尾，在这种情况下，需要展开记录的详细信息才能查看它。

```Kusto
SecurityEvent
| top 10 by TimeGenerated
| extend EventCode=substring(Activity, 0, 4)
```

## <a name="use-summarize-to-aggregate-groups-of-rows"></a>使用 summarize 聚合行组
使用 summarize 可以根据一个或多个列标识记录组，并向其应用聚合。 summarize 最常见的用途是计数，可以返回每个组中的结果数。

以下查询检查过去一小时的所有 *Perf* 记录，按 *ObjectName* 将其分组，然后统计每个组中的记录数： 

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName
```

有时，按多个维度定义组会很有利。 这些值的每个唯一组合定义了一个单独的组：

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName, CounterName
```

另一个常见用途是对每个组执行数学或统计计算。 以下示例查询计算每台计算机的平均 CounterValue：

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer
```

遗憾的是，此查询的结果没有意义，因为我们混合了各种不同的性能计数器。 若要使结果更有意义，可分别计算每个“CounterName”和“Computer”组合的平均值 ：

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer, CounterName
```

### <a name="summarize-by-a-time-column"></a>按时间列汇总
此外，分组结果可以基于时间列或其他连续值。 不过，只汇总 `by TimeGenerated` 会针对时间范围内的每一毫秒创建组，因为这些值是唯一的。 

若要创建基于连续值的组，最好是使用 bin 将范围划分为可管理的单位。 以下查询分析 *Perf* 记录，这些记录度量特定计算机上的可用内存 (*Available MBytes*)。 它计算过去 7 天中每 1 小时时段的平均值：

```Kusto
Perf 
| where TimeGenerated > ago(7d)
| where Computer == "ContosoAzADDS2" 
| where CounterName == "Available MBytes" 
| summarize avg(CounterValue) by bin(TimeGenerated, 1h)
```

若要使输出更清晰，可选择通过时间图表的形式显示一段时间内的可用内存：

![显示一段时间内的可用内存值的屏幕截图。](media/get-started-queries/chart.png)



## <a name="next-steps"></a>后续步骤

- 有关在日志查询中使用字符串数据的详细信息，请参阅[在 Azure Monitor 日志查询中使用字符串](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#string-operations)。
- 有关在日志查询中聚合数据的详细信息，请参阅 [Azure Monitor 日志查询中的高级聚合](/azure/data-explorer/write-queries#advanced-aggregations)。
- 有关如何联接多个表中的数据的信息，请参阅 [Azure Monitor 日志查询中的联接](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#joins)。
- 在 [KQL 语言参考](/azure/kusto/query/)中获取有关完整 Kusto 查询语言的文档。
