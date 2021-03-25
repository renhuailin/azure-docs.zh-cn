---
title: Azure Monitor 工作簿图表可视化效果
description: 了解所有 Azure Monitor 工作簿图表可视化效果。
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: 139c8cdb1a227595d728d2acc0b09cf1eb210715
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "100608490"
---
# <a name="chart-visualizations"></a>图表可视化效果

工作簿允许以图表形式提供监视数据。 支持的图表类型包括折线图、条形图、分类条形图、面积图、散点图、饼图和时间图。 作者可以选择自定义图表的高度、宽度、调色板、图例、标题和非数据消息等，以及使用图表设置自定义轴类型和序列颜色。

工作簿支持日志和指标数据源的图表。

## <a name="log-charts"></a>日志图表

Azure Monitor 日志为资源所有者提供了有关其应用和基础结构的详细信息。 与指标不同，默认不收集日志信息，需要以某种方式启动收集。 但是，当日志存在时，它会提供许多有关资源状态的信息以及对诊断有用的数据。 工作簿允许将日志数据显示为直观的图表，以供用户分析。

### <a name="adding-a-log-chart"></a>添加日志图表

以下示例显示了过去几天内对应用的请求的趋势。

1. 选择“编辑”工具栏项，将工作簿切换到编辑模式。
2. 使用“添加查询”链接将日志查询控件添加到工作簿。
3. 选择“日志”作为查询类型，并选择资源类型（例如 Application Insights）以及目标资源。
4. 使用查询编辑器输入用于分析（例如，请求的趋势）的 [KQL](/azure/kusto/query/)。
5. 将可视化效果设置为下列其中一项：“面积图”、“条形图”、“条形图(分类)”、“折线图”、“饼图”、“散点图”或“时间图”。      
6. 根据需要设置其他参数 - 例如时间范围、可视化效果、大小、调色板和图例。

[![处于编辑模式的日志图表的屏幕截图](./media/workbooks-chart-visualizations/log-chart.png)](./media/workbooks-chart-visualizations/log-chart.png#lightbox)

### <a name="log-chart-parameters"></a>日志图表参数

| 参数 | 说明 | 示例 |
| ------------- |:-------------|:-------------|
| `Query Type` | 要使用的查询类型。 | 日志、Azure Resource Graph 等 |
| `Resource Type` | 目标资源类型。 | Application Insights、Log Analytics 或 Azure-first |
| `Resources` | 要从中获取指标值的一组资源。 | MyApp1 |
| `Time Range` | 用于查看日志图表的时间范围。 | 过去 1 小时、过去 24 小时，等等 |
| `Visualization` | 要使用的可视化效果。 | 面积图、条形图、折线图、饼图、散点图、时间图、分类条形图 |
| `Size` | 控件的垂直大小。 | 小、中、大或完整 |
| `Color palette` | 要在图表中使用的调色板。 在多指标或分段模式下将被忽略。 | 蓝色、绿色、红色，等等 |
| `Legend` | 用于图例的聚合函数。 | 值的总和或平均值，或者最大值、最小值、第一个值或最后一个值 |
| `Query` | 以图表可视化效果所需的格式返回数据的任何 KQL 查询。 | _requests \| make-series Requests = count() default = 0 on timestamp from ago(1d) to now() step 1h_ |

### <a name="time-series-charts"></a>时序图表

使用工作簿中的查询控件，可以轻松创建时序图，如面积图、条形图、折线图、散点图和时间图。 关键是在结果集中具有时间和指标信息。

#### <a name="simple-time-series"></a>简单时序

下面的查询返回包含两个列的表：时间戳和请求 。 查询控件将时间戳用于 X 轴，将请求用于 Y 轴 。

```kusto
requests
| summarize Requests = count() by bin(timestamp, 1h)
```

[![简单时序日志折线图的屏幕截图。](./media/workbooks-chart-visualizations/log-chart-line-simple.png)](./media/workbooks-chart-visualizations/log-chart-line-simple.png#lightbox)

#### <a name="time-series-with-multiple-metrics"></a>具有多个指标的时序

下面的查询返回包含三个列的表：时间戳、请求和用户  。 查询控件将时间戳用于 X 轴，将请求和用户用作 Y 轴上的单独序列  。

```kusto
requests
| summarize Requests = count(), Users = dcount(user_Id) by bin(timestamp, 1h)
```

[![具有多个指标日志折线图的时序的屏幕截图。](./media/workbooks-chart-visualizations/log-chart-line-multi-metric.png)](./media/workbooks-chart-visualizations/log-chart-line-multi-metric.png#lightbox)

#### <a name="segmented-time-series"></a>分段时序

下面的查询返回包含三个列的表：时间戳、请求和 RequestName，其中 RequestName 是包含请求名称的分类列   。 这里的查询控件将时间戳用于 X 轴，并为每个 RequestName 的值添加一个序列 。

```
requests
| summarize Request = count() by bin(timestamp, 1h), RequestName = name
```

[![分段时序日志折线图的屏幕截图。](./media/workbooks-chart-visualizations/log-chart-line-segmented.png)](./media/workbooks-chart-visualizations/log-chart-line-segmented.png#lightbox)

### <a name="summarize-vs-make-series"></a>汇总与建立序列

上一节中的示例使用 `summarize` 运算符，因为它更易于理解。 不过，summarize 确实有一个比较大的限制，即：如果 Bucket 中没有任何项，它将省略结果行。 它可以根据空 Bucket 位于时间范围的前半段还是后半段来改变图表时间窗口的效果。

通常最好使用 `make-series` 运算符来创建时序数据，它有为空 Bucket 提供默认值的选项。

以下查询使用 `make-series` 运算符。

```kusto
requests
| make-series Requests = count() default = 0 on timestamp from ago(1d) to now() step 1h by RequestName = name
```

下面的查询使用 `summarize` 运算符显示类似的图表

```kusto
requests
| summarize Request = count() by bin(timestamp, 1h), RequestName = name
```

即使基础结果集不同也是如此。 用户只需将可视化效果设置为面积图、折线图、条形图或时间图，工作簿将负责处理其余的工作。

[![由生成序列查询创建的日志折线图的屏幕截图](./media/workbooks-chart-visualizations/log-chart-line-make-series.png)](./media/workbooks-chart-visualizations/log-chart-line-make-series.png#lightbox)

### <a name="categorical-bar-chart-or-histogram"></a>分类条形图或直方图

分类图允许用户在图表的 X 轴上表示维度或列，这在直方图中特别有用。 下面的示例通过请求结果代码显示请求的分布情况。

```kusto
requests
| summarize Requests = count() by Result = strcat('Http ', resultCode)
| order by Requests desc
```

查询返回两个列：“请求”指标和“结果”类别 。 “结果”列的每个值都将在图表中获得其自己的条形，其高度与请求指标成比例 。

[![请求的分类条形图（按结果代码）的屏幕截图](./media/workbooks-chart-visualizations/log-chart-categorical-bar.png)](./media/workbooks-chart-visualizations/log-chart-categorical-bar.png#lightbox)

### <a name="pie-charts"></a>饼图

饼图可以呈现数字比例的可视化效果。 下面的示例按请求结果代码显示请求的比例。

```kusto
requests
| summarize Requests = count() by Result = strcat('Http ', resultCode)
| order by Requests desc
```

查询返回两个列：“请求”指标和“结果”类别 。 “结果”列的每个值都将在饼图中获得其自己的切片，其大小与“请求”指标成比例 。

[![饼图的屏幕截图，其中有代表结果代码的切片](./media/workbooks-chart-visualizations/log-chart-pie-chart.png)](./media/workbooks-chart-visualizations/log-chart-pie-chart.png#lightbox)

## <a name="metric-charts"></a>指标图表

大多数 Azure 资源都会发出有关状态和运行状况的指标数据（例如 CPU 利用率、存储可用性、数据库事务计数、失败的应用请求等）。 工作簿允许将此类数据可视化为时序图。

### <a name="adding-a-metric-chart"></a>添加指标图表

以下示例将显示前一个小时内存储帐户中的事务数。 这使存储所有者可以查看事务趋势并查找行为中的异常。

1. 选择“编辑”工具栏项，将工作簿切换到编辑模式。
2. 使用“添加指标”链接将指标控件添加到工作簿。
3. 选择资源类型（例如“存储帐户”）、目标资源、指标命名空间和名称，以及要使用的聚合。
4. 根据需要设置其他参数 - 例如时间范围、拆分依据、可视化效果、大小和调色板。

[![处于编辑模式的指标图表的屏幕截图](./media/workbooks-chart-visualizations/metric-chart.png)](./media/workbooks-chart-visualizations/metric-chart.png#lightbox)

### <a name="metric-chart-parameters"></a>指标图表参数

| 参数 | 说明 | 示例 |
| ------------- |:-------------|:-------------|
| `Resource Type` | 目标资源类型。 | “存储”或“虚拟机”。 |
| `Resources` | 要从中获取指标值的一组资源。 | MyStorage1 |
| `Namespace` | 包含该指标的命名空间。 | 存储 > Blob |
| `Metric` | 要可视化的指标。 | 存储 > Blob > 事务 |
| `Aggregation` | 要应用到指标的聚合函数。 | Sum、Count、Average 等 |
| `Time Range` | 用于查看指标的时间范围。 | 过去 1 小时、过去 24 小时，等等 |
| `Visualization` | 要使用的可视化效果。 | 面积图、条形图、折线图、散点图、网格 |
| `Split By` | （可选）按维度拆分指标。 | 按地理类型列出事务 |
| `Size` | 控件的垂直大小。 | 小、中或大 |
| `Color palette` | 要在图表中使用的调色板。 使用 `Split by` 参数时将被忽略。 | 蓝色、绿色、红色，等等 |

### <a name="examples"></a>示例

将按 API 名称拆分的事务显示为折线图：

[![按 API 名称拆分的存储事务指标折线图的屏幕截图](./media/workbooks-chart-visualizations/metric-chart-storage-split-line.png)](./media/workbooks-chart-visualizations/metric-chart-storage-split-line.png#lightbox)

将按响应类型拆分的事务显示为大型条形图：

[![按响应类型拆分的存储事务大型指标条形图的屏幕截图](./media/workbooks-chart-visualizations/metric-chart-storage-bar-large.png)](./media/workbooks-chart-visualizations/metric-chart-storage-bar-large.png#lightbox)

将平均延迟显示为散点图：

[![存储延迟指标散点图的屏幕截图](./media/workbooks-chart-visualizations/metric-chart-storage-scatter.png)](./media/workbooks-chart-visualizations/metric-chart-storage-scatter.png#lightbox)

## <a name="chart-settings"></a>图表设置

创建者可以使用图表设置来自定义图表轴中使用的字段、轴单位、自定义格式设置、范围、分组行为、图例和序列颜色。

### <a name="the-settings-tab"></a>“设置”选项卡

“设置”选项卡控件：

- 轴设置，包括具有此功能的字段和自定义格式设置：允许用户将数字格式设置为轴值和自定义范围。
- 分组设置，包括用于以下设置的字段：在创建“其他”组之前的限制。
- 图例设置，包括在底部显示指标（序列名称、颜色和数字）和/或图例（序列名称和颜色）。

![图表设置的屏幕截图。](./media/workbooks-chart-visualizations/chart-settings.png)

#### <a name="custom-formatting"></a>自定义格式设置

数字格式设置选项包括：

| 格式选项             | 说明                                                                                           |
|:---------------------------- |:-------------------------------------------------------------------------------------------------------|
| `Units`                      | 列的单位 - 百分比、计数、时间、字节、计数/时间、字节/时间等各种选项。例如，值 1234 的单位可以设置为毫秒，并呈现为 1.234 秒。                                  |
| `Style`                      | 呈现的格式 - 十进制、货币、百分比。                                               |
| `Show group separator`       | 用于显示组分隔符的复选框。 在美国将 1234 呈现为 1,234。                                    |
| `Minimum integer digits`     | 要使用的最小整数位数（默认为 1）。                                                   |
| `Minimum fractional digits`  | 要使用的最小小数位数（默认为 0）。                                                |
| `Maximum fractional digits`  | 要使用的最大小数位数。                                                            |
| `Minimum significant digits` | 要使用的最小有效位数（默认为 1）。                                               |
| `Maximum significant digits` | 要使用的最大有效位数。                                                           |

![X 轴设置的屏幕截图。](./media/workbooks-chart-visualizations/number-format-settings.png)

### <a name="the-series-tab"></a>“序列”选项卡

可通过序列设置选项卡调整为图表中的序列显示的标签和颜色。

- `Series name` 字段用于匹配数据中的序列，如果匹配，将显示相应的显示标签和颜色。
- `Comment` 字段对模板作者很有用，作者添加此注释后，翻译人员可以用它对显示标签进行本地化。

![序列设置的屏幕截图。](./media/workbooks-chart-visualizations/series-settings.png)

## <a name="next-steps"></a>后续步骤

- 了解如何[在工作簿中创建磁贴](workbooks-tile-visualizations.md)。
- 了解如何创建[交互式工作簿](workbooks-interactive.md)。