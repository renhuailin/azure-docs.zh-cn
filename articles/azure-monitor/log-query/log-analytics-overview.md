---
title: Azure Monitor Log Analytics 概述
description: 描述 Log Analytics 是 Azure 门户中的一种工具，用于编辑和运行用于分析 Azure Monitor 日志中的数据的日志查询。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/04/2020
ms.openlocfilehash: ba27739ff9e9d992ffe6202629a1db19604b1409
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186110"
---
# <a name="overview-of-log-analytics-in-azure-monitor"></a>Azure Monitor Log Analytics 概述
Log Analytics 是 Azure 门户中的一种工具，用于用 Azure Monitor 日志中的数据编辑和运行日志查询。 您可以编写一个简单的查询，该查询返回一组记录，然后使用 Log Analytics 的功能对它们进行排序、筛选和分析。 或者，您可以编写更高级的查询来执行统计分析并在图表中显示结果，以确定特定趋势。 无论你是以交互方式使用查询的结果，还是将它们与其他 Azure Monitor 功能（如日志查询警报或工作簿）一起使用，Log Analytics 是你要使用写入和测试的工具。 


> [!TIP]
> 本文提供了 Log Analytics 及其每项功能的说明。 若要直接转到教程，请参阅 [Log Analytics 教程](./log-analytics-tutorial.md)。



## <a name="starting-log-analytics"></a>开始 Log Analytics
从 Azure 门户的 " **Azure Monitor** " 菜单中的 "**日志**" 开始 Log Analytics。 在大多数 Azure 资源的菜单中，也会看到此选项。 无论从何处开始，它都是相同的 Log Analytics 工具。 用于启动 Log Analytics 的菜单将确定可用的数据。 如果从 " **Azure Monitor** " 菜单或 " **Log Analytics 工作区** " 菜单中启动，则可以访问工作区中的所有记录。 如果从另一种类型的资源中选择 **日志** ，则数据将被限制为该资源的日志数据。 有关详细信息，请参阅 [Azure Monitor Log Analytics 中的日志查询范围和时间范围](scope.md)。

[![开始 Log Analytics](media/log-analytics-overview/start-log-analytics.png)](media/log-analytics-overview/start-log-analytics.png#lightbox)

当你开始 Log Analytics 时，你将看到的第一件事是包含 [示例查询](example-queries.md)的对话框。 这些是按解决方案进行分类的，你可以浏览或搜索符合特定要求的查询。 您可以找到确实需要的内容，或者将其加载到编辑器中，并根据需要进行修改。 浏览示例查询实际上是了解如何编写您自己的查询的一种很好的方法。 当然，如果您想要从空脚本开始，并自己编写，则可以关闭示例查询。 如果要返回，只需单击屏幕顶部的 **查询** 。

## <a name="log-analytics-interface"></a>Log Analytics 接口
下图标识了 Log Analytics 的不同组件。

[![Log Analytics](media/log-analytics-overview/log-analytics.png)](media/log-analytics-overview/log-analytics.png#lightbox)

### <a name="1-top-action-bar"></a>1. 顶部操作栏
查询窗口中用于处理查询的控件。

| 选项 | 说明 |
|:---|:---|
| 作用域 | 指定用于查询的数据的作用域。 这可以是 Log Analytics 工作区中的所有数据，也可以是跨多个工作区的特定资源的数据。 请参阅 [查询范围](scope.md)。 |
| “运行”按钮 | 单击此项可在查询窗口中运行所选查询。 还可以按 shift + enter 来运行查询。 |
| 时间选取器 | 选择可用于查询的数据的时间范围。 如果在查询中包含时间筛选器，则会重写此项。 请参阅 [Azure Monitor Log Analytics 中的日志查询范围和时间范围](scope.md)。 |
| “保存”按钮 | 将查询保存到工作区的查询资源管理器。 |
 “复制”按钮 | 将查询链接、查询文本或查询结果复制到剪贴板。 |
| "新建警报规则" 按钮 | 使用空查询创建新选项卡。 |
| “导出”按钮 | 将查询结果导出到 CSV 文件或查询，以便 Power Query 用于 Power Bi 的公式语言格式。 |
| "固定到仪表板" 按钮 | 将查询结果添加到 Azure 仪表板。 |
| 格式查询按钮 | 排列所选文本以便于阅读。 |
| "查询示例" 按钮 | 打开在第一次打开 Log Analytics 时显示的 "示例查询" 对话框。 |
| 查询资源管理器按钮 | 打开 **查询资源管理器** ，它提供对工作区中已保存的查询的访问权限。 |


### <a name="2-sidebar"></a>2. 边栏
工作区中的表列表、示例查询以及当前查询的筛选选项。

| 选项卡 | 说明 |
|:---|:---|
| 表 | 列出属于所选作用域的表。 选择 " **分组依据** " 可更改表的分组。 将鼠标悬停在表名称上可显示一个对话框，其中包含表的说明和用于查看其文档和预览数据的选项。 展开表以查看其列。 双击表或列的名称，将其添加到查询中。 |
| 查询 | 可在查询窗口中打开的示例查询列表。 这是打开 Log Analytics 时显示的同一列表。 选择 " **分组依据** " 可更改查询的分组。 双击查询以将其添加到查询窗口，或将鼠标悬停在其上以查看其他选项。 |
| 筛选器 | 基于查询结果创建筛选器选项。 运行查询后，将显示结果中具有不同值的列。 选择一个或多个值，然后单击 " **应用" & "运行** "，向查询中添加一个 **where** 命令，然后再次运行该命令。 |

### <a name="3-query-window"></a>3. 查询窗口
在查询窗口中编辑查询。 这包括用于 KQL 命令的 intellisense 和用于提高可读性的颜色编码。 单击 **+** 窗口顶部的，以打开另一个选项卡。

单个窗口中可以包含多个查询。 查询不能包含任何空行，因此可以在具有一个或多个空行的窗口中分隔多个查询。 当前查询是指光标置于其中任意位置的查询。

若要运行当前查询，请单击 " **运行** " 按钮或按 Shift + enter。

### <a name="4-results-window"></a>4. 结果窗口
查询的结果将显示在 "结果" 窗口中。 默认情况下，结果显示为一个表。 若要显示为图表，请在 "结果" 窗口中选择 " **图表** "，或向查询中添加 " **渲染** " 命令。

#### <a name="results-view"></a>结果视图 (Results view)
在按列和行组织的表中显示查询结果。 单击行的左侧可展开其值。 单击 " **列** " 下拉列表可更改列的列表。 通过单击列名称对结果进行排序。 通过单击列名称旁边的漏斗来筛选结果。 清除筛选器，然后再次运行查询以重置排序。

选择 " **组列** " 可以在查询结果上方显示分组条。 通过将结果拖到栏上来按任意列对结果进行分组。 通过添加其他列在结果中创建嵌套组。 

#### <a name="chart-view"></a>图表视图
将结果显示为多个可用图表类型之一。 可以在查询的 **render** 命令中指定图表类型，也可以从 " **可视化效果类型** " 下拉列表中选择图表类型。

| 选项 | 说明 |
|:---|:---|
| **可视化效果类型** | 要显示的图表类型。 |
| **X 轴** | 要用于 X 轴的结果中的列 
| **Y 轴** | 要用于 Y 轴的结果中的列。 这通常是数值列。 |
| **拆分依据** | 定义图表中的序列的结果中的列。 为列中的每个值创建一个序列。 |
| **聚合** | 要对 Y 轴中的数值执行的聚合的类型。 |

## <a name="relationship-to-azure-data-explorer"></a>与 Azure 数据资源管理器的关系
如果你已熟悉 Azure 数据资源管理器 Web UI，Log Analytics 应熟悉。 这是因为它构建于 Azure 数据资源管理器之上，并使用相同的 Kusto 查询语言 (KQL) 。 Log Analytics 将添加特定于 Azure Monitor 的功能，如按时间范围筛选，以及从查询创建警报规则的功能。 这两种工具都包含一个资源管理器，可让你扫描可用表的结构，但 Azure 数据资源管理器 Web UI 主要适用于 Azure 数据资源管理器数据库中的表，同时 Log Analytics 与 Log Analytics 工作区中的表一起使用。 

## <a name="next-steps"></a>后续步骤
- 演练[有关在 Azure 门户中使用 Log Analytics 的教程](./log-analytics-tutorial.md)。
- 演练[有关编写查询的教程](get-started-queries.md)。