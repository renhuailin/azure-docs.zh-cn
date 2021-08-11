---
title: Log Analytics 教程
description: 本教程介绍如何使用 Azure Monitor 中 Log Analytics 的功能生成运行日志查询并在 Azure 门户中分析其结果。
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 06/28/2021
ms.openlocfilehash: 6a78bcdb6f8f3036da4f273256d681f94f87a1bb
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113356628"
---
# <a name="log-analytics-tutorial"></a>Log Analytics 教程
Log Analytics 是 Azure 门户中的工具，用于根据 Azure Monitor 日志收集的数据编辑和运行日志查询，并交互式分析其结果。 可以使用 Log Analytics 查询来检索符合特定条件的记录、确定趋势、分析模式，并提供对数据的各种见解。 

本教程将介绍 Log Analytics 界面，引导你开始使用一些基本查询，并演示如何处理结果。 学习以下技能：

> [!div class="checklist"]
> * 了解日志数据的架构
> * 编写和运行简单的查询，以及修改查询时间范围
> * 对查询结果进行筛选、排序和分组
> * 查看、修改和共享查询结果的视觉对象
> * 加载、导出和复制查询与结果

> [!IMPORTANT]
> 本教程使用 Log Analytics 的功能来生成和运行查询，而不介绍查询本身。 你将利用 Log Analytics 功能生成一个查询，并使用另一个示例查询。 准备好学习查询的语法并开始直接编辑查询本身时，请浏览 [Kusto 查询语言教程](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor)。 本教程将介绍几个示例查询，你可以在 Log Analytics 中编辑和运行这些查询，并利用你将在本教程中学习的多个功能。


## <a name="prerequisites"></a>先决条件
本教程使用 [Log Analytics 演示环境](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade)，其中包含大量支持示例查询的示例数据。 你也可以使用自己的 Azure 订阅，但相同的表中可能没有数据。

## <a name="open-log-analytics"></a>打开 Log Analytics
打开 [Log Analytics 演示环境](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade)或从订阅中的 Azure Monitor 菜单选择“日志”。 这会将初始范围设置为 Log Analytics 工作区，这意味着查询将从该工作区中的所有数据中进行选择。 如果从 Azure 资源的菜单中选择“日志”，则范围将设置为仅来自该资源的记录。 有关范围的详细信息，请参阅[日志查询范围](./scope.md)。

可以在屏幕左上角查看范围。 如果使用自己的环境，则会看到一个用于选择其他范围的选项，但此选项在演示环境中不可用。

:::image type="content" source="media/log-analytics-tutorial/scope.png" alt-text="Log Analytics 范围" lightbox="media/log-analytics-tutorial/scope.png":::

## <a name="table-schema"></a>表架构
屏幕左侧包含“表”选项卡，可用于检查当前范围内可用的表。 默认情况下，这些表按解决方案进行分组，可以更改其分组或对其进行筛选。 

展开“日志管理”解决方案并找到 AppRequests 表 。 可以展开该表以查看其架构，或将鼠标悬停在其名称上以显示有关它的其他信息。 

:::image type="content" source="media/log-analytics-tutorial/table-details.png" alt-text="表视图" lightbox="media/log-analytics-tutorial/table-details.png":::

单击“有用的链接”下方的链接，以转到记录每个表及其列的表格引用。 单击“预览数据”以快速查看表中的一些近期记录。 这有助于在实际运行查询之前确保这是你需要的数据。

:::image type="content" source="media/log-analytics-tutorial/sample-data.png" alt-text="示例数据" lightbox="media/log-analytics-tutorial/sample-data.png":::

## <a name="write-a-query"></a>编写查询
接下来，使用 AppRequests 表编写查询。 双击其名称以将其添加到查询窗口。 也可以直接在窗口中键入名称，甚至可以获取智能感知，它将帮助完成当前范围内和 KQL 命令中的表名称。

这是我们可以编写的最简单的查询。 它只返回表中的所有记录。 通过单击“运行”按钮，或将光标置于查询文本中的任意位置并按 Shift+Enter 来运行该查询。

:::image type="content" source="media/log-analytics-tutorial/query-results.png" alt-text="查询结果" lightbox="media/log-analytics-tutorial/query-results.png":::

可以看到我们确实得到了结果。 查询返回的记录数显示在右下角。 

## <a name="filter"></a>筛选器

让我们向查询添加筛选器以减少返回的记录数。 选择左窗格中的“筛选器”选项卡。 这会在查询结果中显示不同的列，可以使用这些列来筛选结果。 这些列中的顶部值将显示具有该值的记录数。 单击“ResultCode”下的“200”，然后单击“应用并运行”  。 

:::image type="content" source="media/log-analytics-tutorial/query-pane.png" alt-text="“查询”窗格" lightbox="media/log-analytics-tutorial/query-pane.png":::

会将 where 语句添加到查询中，其中包含所选的值。 现在，结果仅包含具有该值的记录，因此可以看到记录数减少了。

:::image type="content" source="media/log-analytics-tutorial/query-results-filter-01.png" alt-text="已筛选的查询结果" lightbox="media/log-analytics-tutorial/query-results-filter-01.png":::


## <a name="time-range"></a>时间范围
Log Analytics 工作区中的所有表都有一个名为 TimeGenerated 的列，这是创建记录的时间。 所有查询都具有时间范围，该范围将结果限制为具有该范围内 TimeGenerated 值的记录。 时间范围可以在查询中设置，也可以在屏幕顶部的选择器中设置。

默认情况下，查询将返回最近 24 小时的记录。 应会显示一条消息，指示未显示全部结果。 这是因为日志分析最多可以返回 30,000 条记录，而我们的查询返回的记录数多于此值。 选择“时间范围”下拉列表，并将其更改为“12 小时” 。 再次单击“运行”以返回结果。 

:::image type="content" source="media/log-analytics-tutorial/query-results-max.png" alt-text="时间范围" lightbox="media/log-analytics-tutorial/query-results-max.png":::


## <a name="multiple-query-conditions"></a>多个查询条件
让我们再添加一个筛选条件，进一步减少结果。 查询可以包含任意数量的筛选器，以便精确定位所需的记录集。 在“名称”下选择“获取主页/索引”，然后单击“应用并运行”  。 

:::image type="content" source="media/log-analytics-tutorial/query-results-filter-02.png" alt-text="查询结果多个筛选器" lightbox="media/log-analytics-tutorial/query-results-filter-02.png":::


## <a name="analyze-results"></a>分析结果
Log Analytics 不仅有助于编写和运行查询，还提供了用于处理结果的功能。 首先，展开记录以查看其所有列的值。

:::image type="content" source="media/log-analytics-tutorial/expand-record.png" alt-text="展开记录" lightbox="media/log-analytics-tutorial/expand-record.png":::

单击任意列的名称以按该列对结果进行排序。 单击该列旁边的筛选器图标以提供筛选条件。 这类似于向查询本身添加筛选条件，但如果再次运行查询，则会清除此筛选器。 如果要在交互式分析过程中快速分析一组记录，请使用此方法。

例如，在 DurationMs 列上设置筛选器，以将记录限制为超过 100 毫秒的记录 。 

:::image type="content" source="media/log-analytics-tutorial/query-results-filter.png" alt-text="查询结果筛选器" lightbox="media/log-analytics-tutorial/query-results-filter.png":::

可以按特定列对记录进行分组，而不对结果进行筛选。 清除刚才创建的筛选器，然后打开“组列”滑块。 

:::image type="content" source="media/log-analytics-tutorial/query-results-group-columns.png" alt-text="组列" lightbox="media/log-analytics-tutorial/query-results-group-columns.png":::

现在，将 Url 列拖到分组行中。 现在，结果按该列进行组织，可以折叠每个组以帮助你进行分析。

:::image type="content" source="media/log-analytics-tutorial/query-results-grouped.png" alt-text="已分组的查询结果" lightbox="media/log-analytics-tutorial/query-results-grouped.png":::

## <a name="work-with-charts"></a>使用图表
让我们看一个查询，该查询使用可在图表中查看的数值数据。 我们将选择一个示例查询，而不是生成查询。

在左窗格中，单击“查询”。 此窗格包含可添加到查询窗口的示例查询。 如果使用自己的工作区，则应该具有多种类别的多个查询；但如果使用演示环境，则可能只会看到一个“Log Analytics 工作区”类别。 展开此项以查看该类别的查询。

在“应用程序”类别中单击名为“函数错误率”的查询 。 这会将查询添加到查询窗口。 请注意，用空白行分隔了新查询和另一个查询。 KQL 中的查询遇到空白行时将结束，因此它们被视为单独的查询。 

:::image type="content" source="media/log-analytics-tutorial/example-query.png" alt-text="新查询" lightbox="media/log-analytics-tutorial/example-query.png":::

光标所在位置是当前的查询。 可以看到第一个查询突出显示，表明它是当前查询。 单击新查询中的任意位置以将其选中，然后单击“运行”按钮以运行该查询。

:::image type="content" source="media/log-analytics-tutorial/example-query-output-table.png" alt-text="查询结果表" lightbox="media/log-analytics-tutorial/example-query-output-table.png":::

若要在图中查看结果，请在结果窗格中选择“图表”。  请注意，有多种处理图表的选项，例如将其更改为另一种类型。


:::image type="content" source="media/log-analytics-tutorial/example-query-output-chart.png" alt-text="查询结果图表" lightbox="media/log-analytics-tutorial/example-query-output-chart.png":::


## <a name="next-steps"></a>后续步骤

现在，你已了解如何使用 Log Analytics，请完成有关使用日志查询的教程。
> [!div class="nextstepaction"]
> [编写 Azure Monitor 日志查询](get-started-queries.md)
