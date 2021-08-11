---
title: Azure Monitor 中的 Log Analytics 概述
description: 介绍 Log Analytics，它是 Azure 门户中的一种工具，用于编辑和运行日志查询，以便分析 Azure Monitor 日志中的数据。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/04/2020
ms.openlocfilehash: 860085cc447b241f622c2be89038bca0014a883e
ms.sourcegitcommit: bb9a6c6e9e07e6011bb6c386003573db5c1a4810
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110493404"
---
# <a name="overview-of-log-analytics-in-azure-monitor"></a>Azure Monitor 中的 Log Analytics 概述
Log Analytics 是 Azure 门户中的一种工具，用于通过 Azure Monitor 日志中的数据编辑和运行日志查询。 可以编写简单查询，以返回记录集，然后使用 Log Analytics 的功能对它们进行排序、筛选和分析。 也可以编写更高级的查询来执行统计分析并在图表中显示结果，以确定特定趋势。 无论是以交互方式处理查询的结果，还是将它们与其他 Azure Monitor 功能（如日志查询警报或工作簿）一起使用，Log Analytics 都是要用于编写和测试它们的工具。 


> [!TIP]
> 本文提供了 Log Analytics 及其每个功能的说明。 如果要直接跳转到教程，请参阅 [Log Analytics 教程](./log-analytics-tutorial.md)。



## <a name="starting-log-analytics"></a>启动 Log Analytics
在 Azure 门户中，从“Azure Monitor”菜单中的“日志”启动 Log Analytics 。 对于大多数 Azure 资源，你也会在菜单中看到此选项。 无论从何处启动，它都是相同的 Log Analytics 工具。 不过，用于启动 Log Analytics 的菜单会确定可用的数据。 如果从“Azure Monitor”菜单或“ Log Analytics 工作区”菜单中启动，则可以访问工作区中的所有记录 。 如果从其他类型的资源选择“日志”，则数据会限制为该资源的日志数据。 有关详细信息，请参阅 [Azure Monitor Log Analytics 中的日志查询范围和时间范围](./scope.md)。

[![启动 Log Analytics](media/log-analytics-overview/start-log-analytics.png)](media/log-analytics-overview/start-log-analytics.png#lightbox)

启动 Log Analytics 时，你会看到的第一个内容是包含[示例查询](../logs/queries.md)的对话框。 这些查询按解决方案进行分类，你可以浏览或搜索符合特定要求的查询。 能够查找精确执行所需操作的查询，或者将一个查询加载到编辑器中，并根据需要进行修改。 浏览示例查询实际上是一种了解如何编写自己的查询的极好方法。 

当然，如果要从空脚本开始并自己编写，则可以关闭示例查询。 如果要重新打开它们，只需单击屏幕顶部的“查询”。

## <a name="log-analytics-interface"></a>Log Analytics 界面
下图标识了 Log Analytics 的不同组件。

[![Log Analytics](media/log-analytics-overview/log-analytics.png)](media/log-analytics-overview/log-analytics.png#lightbox)

### <a name="1-top-action-bar"></a>1.顶部操作栏
用于在查询窗口中处理查询的控件。

| 选项 | 说明 |
|:---|:---|
| 作用域 | 指定用于查询的数据范围。 这可以是 Log Analytics 工作区中的所有数据，或是跨多个工作区的特定资源的数据。 请参阅[查询范围](./scope.md)。 |
| “运行”按钮 | 单击可在查询窗口中运行所选查询。 还可以按 Shift+Enter 来运行查询。 |
| 时间选取器 | 选择可用于查询的数据时间范围。 如果在查询中包含时间筛选器，则会替代此项。 请参阅 [Azure Monitor Log Analytics 中的日志查询范围和时间范围](./scope.md)。 |
| “保存”按钮 | 将查询保存到工作区的查询资源管理器。 |
 “复制”按钮 | 将查询链接、查询文本或查询结果复制到剪贴板。 |
| “新建警报规则”按钮 | 创建包含空查询的新选项卡。 |
| “导出”按钮 | 将查询结果导出到 CSV 文件或将查询导出为 Power Query 公式语言格式以便用于 Power BI。 |
| “固定到仪表板”按钮 | 将查询结果添加到 Azure 仪表板。 |
| “设置查询格式”按钮 | 排列所选文本以便于阅读。 |
| “示例查询”按钮 | 打开在第一次打开 Log Analytics 时显示的示例查询对话框。 |
| “查询资源管理器”按钮 | 打开“查询资源管理器”以便访问工作区中已保存的查询。 |


### <a name="2-sidebar"></a>2.边栏
工作区中的表、示例查询以及适用于当前查询的筛选选项的列表。

| 选项卡 | 说明 |
|:---|:---|
| 表 | 列出属于所选范围的表。 选择“分组依据”可更改表的分组。 将鼠标悬停在表名称上方可显示一个对话框，其中包含表的说明以及用于查看其文档和预览其数据的选项。 展开表可查看其列。 双击表或列名可将其添加到查询中。 |
| 查询 | 可在查询窗口中打开的示例查询的列表。 这是打开 Log Analytics 时显示的同一个列表。 选择“分组依据”可更改查询的分组。 双击查询可将其添加到查询窗口，或将鼠标悬停在其上方可查看其他选项。 |
| 筛选器 | 基于查询结果创建筛选器选项。 运行查询之后，将显示在结果中具有不同值的列。 选择一个或多个值，然后单击“应用并运行”可将 where 命令添加到查询中，然后再次运行 。 |

### <a name="3-query-window"></a>3.查询窗口
查询窗口是编辑查询的位置。 这包括用于 KQL 命令的 IntelliSense 和用于提高可读性的颜色编码。 单击窗口顶部的“+”可打开另一个选项卡。

单个窗口中可以包含多个查询。 查询不能包含任何空白行，因此可以在一个窗口中使用一个或多个空白行分隔多个查询。 光标所在位置是当前的查询。

若要运行当前查询，请单击“运行”按钮或按 Shift+Enter。

### <a name="4-results-window"></a>4.结果窗口
查询结果显示在结果窗口中。 默认情况下，结果显示为表。 若要显示为图表，请在结果窗口中选择“图表”，或者向查询添加 render 命令 。

#### <a name="results-view"></a>结果视图 (Results view)
在按列和行组织的表中显示查询结果。 单击行的左侧可展开其值。 单击“列”下拉列表可更改列的列表。 可通过单击列名对结果进行排序。 可通过单击列名旁的漏斗来筛选结果。 可通过再次运行查询来清除筛选器并重置排序。

选择“组合列”可在查询结果上方显示分组栏。 可通过将任何列拖动到该栏上来按该列对结果进行分组。 可通过添加其他列在结果中创建嵌套组。 

#### <a name="chart-view"></a>图表视图
将结果显示为多个可用图表类型之一。 可以在查询的 render 命令中指定图表类型，也可以从“可视化效果类型”下拉列表中进行选择 。

| 选项 | 说明 |
|:---|:---|
| 可视化效果类型 | 要显示的图表类型。 |
| X 轴 | 结果中要用于 X 轴的列 
| Y 轴 | 结果中要用于 Y 轴的列。 这通常是数值列。 |
| **拆分依据** | 结果中用于定义图表中的序列的列。 为列中的每个值创建一个序列。 |
| **聚合** | 要对 Y 轴中的数值执行的聚合类型。 |

## <a name="relationship-to-azure-data-explorer"></a>与 Azure 数据资源管理器的关系
如果你已熟悉 Azure 数据资源管理器 Web UI，Log Analytics 应看上去很熟悉。 这是因为它构建在 Azure 数据资源管理器的基础之上，使用相同的 Kusto 查询语言 (KQL)。 Log Analytics 添加了特定于 Azure Monitor 的功能，如按时间范围筛选以及通过查询创建警报规则的功能。 这两种工具都包含一个资源管理器，使你可以扫描可用表的结构，但 Azure 数据资源管理器 Web UI 主要处理 Azure 数据资源管理器数据库中的表，而 Log Analytics 处理 Log Analytics 工作区中的表。 

## <a name="next-steps"></a>后续步骤
- 演练[有关在 Azure 门户中使用 Log Analytics 的教程](./log-analytics-tutorial.md)。
- 演练[有关编写查询的教程](./get-started-queries.md)。
