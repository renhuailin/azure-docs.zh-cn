---
title: Azure Monitor 工作簿网格可视化效果
description: 了解所有 Azure Monitor 工作簿网格可视化效果。
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: 114245030ba2356861b83a580f103d3b84c0eae3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "100607489"
---
# <a name="grid-visualizations"></a>网格可视化效果

网格或表是向用户呈现数据的常用方式。 工作簿允许用户为网格列单独设置样式，以便在报告中提供丰富的 UI。

以下示例显示了一个网格，其中组合了图标、热度地图和缩微条形图，以呈现复杂的信息。 该工作簿还提供了排序控件、搜索框和“转到分析”按钮。

[![基于日志的网格的屏幕截图](./media/workbooks-grid-visualizations/grid.png)](./media/workbooks-grid-visualizations/grid.png#lightbox)

## <a name="adding-a-log-based-grid"></a>添加基于日志的网格

1. 单击“编辑”工具栏项，将工作簿切换到编辑模式。
2. 使用“添加查询”链接将日志查询控件添加到工作簿。
3. 选择“日志”作为查询类型，并选择资源类型（例如 Application Insights）以及目标资源。
4. 使用查询编辑器输入用于分析（例如，内存低于阈值的 VM）的 KQL
5. 将可视化效果设置为“网格”
6. 根据需要设置其他参数 - 例如时间范围、大小、调色板和图例。

[![基于日志的网格查询的屏幕截图](./media/workbooks-grid-visualizations/grid-query.png)](./media/workbooks-grid-visualizations/grid-query.png#lightbox)

## <a name="log-chart-parameters"></a>日志图表参数

| 参数 | 说明 | 示例 |
| ------------- |:-------------|:-------------|
| `Query Type` | 要使用的查询类型。 | 日志、Azure Resource Graph 等 |
| `Resource Type` | 目标资源类型。 | Application Insights、Log Analytics 或 Azure-first |
| `Resources` | 要从中获取指标值的一组资源。 | MyApp1 |
| `Time Range` | 用于查看日志图表的时间范围。 | 过去 1 小时、过去 24 小时，等等 |
| `Visualization` | 要使用的可视化效果。 | 网格 |
| `Size` | 控件的垂直大小。 | 小、中、大或完整 |
| `Query` | 以图表可视化效果所需的格式返回数据的任何 KQL 查询。 | requests \| summarize Requests = count() by name |

## <a name="simple-grid"></a>简单网格

工作簿可将 KQL 结果呈现为简单的表。 以下网格显示了应用中每个请求类型的请求数和唯一用户数。

```kusto
requests
| where name !endswith('.eot')
| summarize Requests = count(), Users = dcount(user_Id) by name
| order by Requests desc
```

[![编辑模式下基于日志的网格的屏幕截图](./media/workbooks-grid-visualizations/log-chart-simple-grid.png)](./media/workbooks-grid-visualizations/log-chart-simple-grid.png#lightbox)

## <a name="grid-styling"></a>网格样式

尽管纯表可显示数据，但很难阅读，并且见解并不总是显而易见的。 设置网格样式有助于更轻松地阅读和解释数据。

下面是上一部分中的相同网格，样式为热度地图。

[![基于日志的网格的屏幕截图，其中列的样式为热度地图](./media/workbooks-grid-visualizations/log-chart-grid-heatmap.png)](./media/workbooks-grid-visualizations/log-chart-grid-heatmap.png#lightbox)

下面是样式为条形图的相同网格：[![基于日志的网格的屏幕截图，其中列的样式为条形图](./media/workbooks-grid-visualizations/log-chart-grid-bar.png)](./media/workbooks-grid-visualizations/log-chart-grid-bar.png#lightbox)

### <a name="styling-a-grid-column"></a>设置网格列的样式

1. 选择查询控件工具栏上的“列设置”按钮。
2. 在“编辑列设置”中，选择要设置样式的列。
3. 选择列呈现器（例如 heatmap、bar​​、bar underneath 等）以及用于设置列样式的相关设置。

以下示例将“请求”列的样式设置为条形图：

[![基于日志的网格的屏幕截图，其中“请求”列的样式为条形图。](./media/workbooks-grid-visualizations/log-chart-grid-column-settings-start.png)](./media/workbooks-grid-visualizations/log-chart-grid-column-settings-start.png#lightbox)

### <a name="column-renderers"></a>列呈现器

| 列呈现器 | 说明 | 其他选项 |
|:------------- |:-------------|:-------------|
| `Automatic` | 默认值 - 根据列类型使用最适合的呈现器。  |  |
| `Text` | 将列值呈现为文本。 | |
| `Right Aligned` | 与 text 类似，只不过它是右对齐的。 | |
| `Date/Time` | 呈现可读日期时间字符串。 | |
| `Heatmap` | 根据单元格的值为网格单元格着色。 | 调色板和用于缩放的最小/最大值。 |
| `Bar` | 根据单元格的值在单元格旁边呈现一个条形图。 | 调色板和用于缩放的最小/最大值。 |
| `Bar underneath` | 根据单元格的值在单元格的底部附近呈现一个条形图。 | 调色板和用于缩放的最小/最大值。 |
| `Composite bar` | 使用该行中的指定列呈现复合条形图。 有关详细信息，请参阅[复合条形图](workbooks-composite-bar.md)。 | 以相应颜色呈现条形图的列以及显示在条形图顶部的标签。 |
| `Spark bars` | 根据单元格中动态数组的值，在单元格中呈现缩微条形图。 例如，屏幕截图中顶部的“趋势”列。 | 调色板和用于缩放的最小/最大值。 |
| `Spark lines` | 根据单元格中动态数组的值，在单元格中呈现缩微线形图。 | 调色板和用于缩放的最小/最大值。 |
| `Icon` | 根据单元格中的文本值呈现图标。 支持的值包括：`cancelled`、`critical`、`disabled`、`error`、`failed`、`info`、`none`、`pending`、`stopped`、`question`、`success`、`unknown`、`warning`、`uninitialized`、`resource`、`up`、`down`、`left`、`right`、`trendup`、`trenddown`、`4`、`3`、`2`、`1`、`Sev0`、`Sev1`、`Sev2`、`Sev3`、`Sev4`、`Fired`、`Resolved`、`Available`、`Unavailable`、`Degraded`、`Unknown` 和 `Blank`。|  |
| `Link` | 呈现单击时的链接或执行可配置操作。 如果只希望该项成为链接，请使用此列呈现器。  使用 `Make this item a link` 设置，任何其他类型也可成为链接。 有关详细信息，请参阅下面的[链接操作](#link-actions)。 |  |
| `Location` | 根据区域 ID 呈现友好 Azure 区域名称。 |  |
| `Resource type` | 根据资源类型 ID 呈现友好资源类型字符串  |  |
| `Resource` | 根据资源 ID 呈现友好资源名称和链接  | 用于显示资源类型图标的选项  |
| `Resource group` | 根据资源组 ID 呈现友好资源组名称和链接。如果该单元格的值不是资源组，则将其转换为资源组。  | 用于显示资源组图标的选项  |
| `Subscription` | 根据订阅 ID 呈现友好订阅名称和链接。如果该单元格的值不是订阅，则将其转换为订阅。  | 用于显示订阅图标的选项。  |
| `Hidden` | 在网格中隐藏列。 当默认查询返回的列多于所需的列，但不需要 project-away 时，此选项非常有用 |  |

### <a name="link-actions"></a>链接操作

如果选择了 `Link` 呈现器或选中了“将此项设置为链接”复选框，则创建者可以配置在选择该单元格时将发生的链接操作。 这通常会将用户带到其他视图（其中包含来自该单元格的上下文），或者可能会打开一个 URL。

### <a name="custom-formatting"></a>自定义格式设置

工作簿还使用户能够设置其单元格值的数字格式。 通过单击“自定义格式设置”复选框（如果可用）即可完成此操作。

| 格式选项 | 说明 |
|:------------- |:-------------|
| `Units` | 列的单位 - 百分比、计数、时间、字节、计数/时间、字节/时间等各种选项。例如，值 1234 的单位可以设置为毫秒，呈现为 1.234 秒。 |
| `Style` | 呈现的格式 - 十进制、货币、百分比。 |
| `Show group separator` | 用于显示组分隔符的复选框。 在美国将 1234 呈现为 1,234。 |
| `Minimum integer digits` | 要使用的最小整数位数（默认为 1）。 |
| `Minimum fractional digits` | 要使用的最小小数位数（默认为 0）。  |
| `Maximum fractional digits` | 要使用的最大小数位数。 |
| `Minimum significant digits` | 要使用的最小有效位数（默认为 1）。 |
| `Maximum significant digits` | 要使用的最大有效位数。 |
| `Custom text for missing values` | 如果数据点没有值，则显示此自定义文本而不是显示空白。 |

### <a name="custom-date-formatting"></a>自定义日期格式设置

当创建者已指定将列设置为 Date/Time 呈现器时，创建者可使用“自定义日期格式设置”复选框来指定自定义日期格式设置选项。

| 格式选项 | 说明 |
|:------------- |:-------------|
| `Style` | 将日期呈现为短、长、完整格式或将时间呈现为短或长时间格式的格式。 |
| `Show time as` | 允许创建者决定是显示本地时间（默认）还是 UTC 时间。 UTC/时区信息可能不会显示，具体取决于选择的日期格式样式。 |

## <a name="custom-column-width-setting"></a>自定义列宽设置

创建者可使用“列设置”中的“自定义列宽”字段来自定义网格中任何列的宽度 。

![列设置的屏幕截图，其中以红色框突出显示了“自定义列宽”字段](./media/workbooks-grid-visualizations/custom-column-width-setting.png)

如果该字段保留图中的黑色文本，则将根据列中的字符数和可见列数自动确定宽度。 默认单位为“ch”（字符）。

选择标签中的蓝色“(当前宽度)”按钮将会用所选列的当前宽度填充文本字段。 如果“自定义宽度”字段中存在值但没有度量单位，则将使用默认值。

可用的度量单位包括：

| 度量单位 | 定义           |
|:--------------------|:---------------------|
| ch                  | 字符（默认） |
| 像素                  | 像素               |
| fr                  | 小数单位     |
| %                   | percentage           |

输入验证 - 如果验证失败，则会在字段下面弹出一条红色指导消息，但用户仍可以应用该宽度。 如果输入中存在值，则将对其进行分析。如果找不到有效的度量单位，则将使用默认单位。

没有最小/最大宽度，因为这由创建者自行决定。 对于隐藏列，禁用“自定义列宽度”字段。

## <a name="examples"></a>示例

### <a name="spark-lines-and-bar-underneath"></a>缩微线形图和底部条形图

下面的示例按请求名称显示了请求数及其趋势。

```kusto
requests
| make-series Trend = count() default = 0 on timestamp from ago(1d) to now() step 1h by name
| project-away timestamp
| join kind = inner (requests
    | summarize Requests = count() by name
    ) on name
| project name, Requests, Trend
| order by Requests desc
```

[![基于日志的网格的屏幕截图，其中包含底部条形图和缩微线形图](./media/workbooks-grid-visualizations/log-chart-grid-spark-line.png)](./media/workbooks-grid-visualizations/log-chart-grid-spark-line.png#lightbox)

### <a name="heatmap-with-shared-scales-and-custom-formatting"></a>包含共享缩放和自定义格式设置的热度地图

此示例显示了各种请求持续时间指标及其计数。 heatmap 呈现器使用设置中设置的最小值或计算列的最小值和最大值，并根据相对于列的最小值和最大值的单元格的值，从所选调色板分配背景色。

```
requests
| summarize Mean = avg(duration), (Median, p80, p95, p99) = percentiles(duration, 50, 80, 95, 99), Requests = count() by name
| order by Requests desc
```

[![基于日志的网格的屏幕截图，其中热度地图跨列共享缩放](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale.png)](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale.png#lightbox)

在上面的示例中，共享调色板（绿色或红色）和缩放用于为列（平均值、中间值、p80、p95 和 p99）着色。 用于请求列的单独调色板（蓝色）。

#### <a name="shared-scale"></a>共享缩放

获取共享缩放：

1. 使用正则表达式来选择多个列以向它们应用设置。 例如，将列名称设置为 `Mean|Median|p80|p95|p99` 以将它们全部选中。
2. 删除各个列的默认设置。

这将导致新的多列设置为应用其设置以包含共享缩放。

[![基于日志的网格的屏幕截图，其中网格设置为获取跨列共享缩放](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale-settings.png)](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale-settings.png#lightbox)

### <a name="icons-to-represent-status"></a>用于表示状态的图标

下面的示例显示了基于 p95 持续时间的自定义请求状态。

```
requests
| summarize p95 = percentile(duration, 95) by name
| order by p95 desc
| project Status = case(p95 > 5000, 'critical', p95 > 1000, 'error', 'success'), name, p95
```

[![基于日志的网格的屏幕截图，其中热度地图使用上述查询跨列共享缩放。](./media/workbooks-grid-visualizations/log-chart-grid-icons.png)](./media/workbooks-grid-visualizations/log-chart-grid-icons.png#lightbox)

支持的图标名称包括：`cancelled`、`critical`、`disabled`、`error`、`failed`、`info`、`none`、`pending`、`stopped`、`question`、`success`、`unknown`、`warning` `uninitialized`、`resource`、`up`、`down`、`left`、`right`、`trendup`、`trenddown`、`4`、`3`、`2`、`1`、`Sev0`、`Sev1`、`Sev2`、`Sev3`、`Sev4`、`Fired`、`Resolved`、`Available`、`Unavailable`、`Degraded`、`Unknown` 和 `Blank`。

### <a name="using-thresholds-with-links"></a>将阈值与链接结合使用

下面的说明将演示如何将阈值与链接结合使用来分配图标和打开不同的工作簿。 网格中的每个链接将为该 Application Insights 资源打开不同的工作簿模板。

1. 选择“编辑”工具栏项，将工作簿切换到编辑模式。
2. 选择“添加”，然后选择“添加查询”。
3. 将“数据源”更改为“JSON”，并将“可视化效果”更改为“网格” 。
4. 输入以下查询。

```json
[ 
    { "name": "warning", "link": "Community-Workbooks/Performance/Performance Counter Analysis" },
    { "name": "info", "link": "Community-Workbooks/Performance/Performance Insights" },
    { "name": "error", "link": "Community-Workbooks/Performance/Apdex" }
]
```

5. 运行查询。
6. 选择“列设置”以打开设置。
7. 从“列”中选择“名称”。
8. 在“列呈现器”下，选择“阈值”。
9.  输入并选择以下“阈值设置”。
   
    | 运算符 | 值   | 图标   |
    |----------|---------|---------|
    | ==       | warning | 警告 |
    | ==       | error   | 失败  |

    ![包含以上设置的“编辑列设置”选项卡的屏幕截图。](./media/workbooks-grid-visualizations/column-settings.png)

    保留默认行不变。 可输入你喜欢的任何文本。 “文本”列接受字符串格式的输入，并使用列值和单位（如果指定）填充该列。 例如，如果 warning 为列值，则文本可为“{0} {1} link!”，它将显示为“warning link!”。
10. 选择“将此项设置为链接”框。
    1. 在“打开视图”下，选择“工作簿(模板)”。
    2. 在“链接值来源”下，选择“链接”。
    3. 选择“在上下文边栏选项卡中打开链接”框。
    4. 在“工作簿链接设置”中选择以下设置
        1. 在“模板 ID 来源”下，选择“列”。
        2. 在“列”下，选择“链接”。

    ![包含以上设置的链接设置的屏幕截图。](./media/workbooks-grid-visualizations/make-this-item-a-link.png)

11. 从“列”中选择“链接”。 在“列呈现器”旁的“设置”下，选择“(隐藏列)”。
1. 若要更改“名称”列的显示名称，请选择“标签”选项卡。在以“名称”为其“列 ID”的行上，在“*列标签”下输入要显示的名称。
2. 选择“应用”

![包含以上设置的“网格中的阈值”的屏幕截图](./media/workbooks-grid-visualizations/thresholds-workbooks-links.png)

## <a name="fractional-units-percentages"></a>小数单位百分比

小数单位 (fr) 是各种网格中常用的动态度量单位。 当窗口大小/分辨率发生变化时，fr 宽度也随之更改。

下面的屏幕截图显示了一个具有八列的表，其中每个列的宽度相同，均为 1fr。 随着窗口大小的变化，每列的宽度会按比例变化。

[![网格中列的屏幕截图，其中每个列的宽度值为 1fr](./media/workbooks-grid-visualizations/custom-column-width-fr.png)](./media/workbooks-grid-visualizations/custom-column-width-fr.png#lightbox)

下图显示了相同的表，只不过第一列的宽度设置为 50%。 这会动态地将列设置为总网格宽度的一半。 除非窗口大小太小，否则调整窗口大小将继续保留 50% 的宽度。 这些动态列的最小宽度取决于其内容。 剩余 50% 的网格划分为全部 8 个小数单位。 下面的“种类”列设置为 2fr，因此它占据了剩余空间的四分之一。 由于其他列均为 1fr，它们各自占网格右半部分的八分之一。

[![网格中列的屏幕截图，其中有一列的宽度值为 50%，其余每列均为 1fr](./media/workbooks-grid-visualizations/custom-column-width-fr2.png)](./media/workbooks-grid-visualizations/custom-column-width-fr2.png#lightbox)

可将 fr、%、px 和 ch 宽度结合使用，并且其工作方式与前面的示例类似。 设置为静态单位（ch 和 px）的宽度是硬常量，即使更改了窗口/分辨率，宽度也不会更改。 设置为 % 的列将占据总网格宽度的百分比（由于前面的最小宽度，因此可能并非完全如此）。 设置为 fr 的列只会根据分配的小数单位数来拆分剩余的网格空间。

[![网格中列的屏幕截图，其中使用了各种不同的宽度单位](./media/workbooks-grid-visualizations/custom-column-width-fr3.png)](./media/workbooks-grid-visualizations/custom-column-width-fr3.png#lightbox)

## <a name="next-steps"></a>后续步骤

* 了解如何[在工作簿中创建树](workbooks-tree-visualizations.md)。
* 了解如何创建[工作簿文本参数](workbooks-text.md)。