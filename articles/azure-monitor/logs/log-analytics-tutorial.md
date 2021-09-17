---
title: Log Analytics 教程
description: 本教程介绍如何使用 Azure Monitor 中的 Log Analytics 功能生成和运行日志查询，并在 Azure 门户中分析其结果。
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 06/28/2021
ms.openlocfilehash: bf19056cbde9c719c8d8665d2fc9a954e7a943b6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729348"
---
# <a name="log-analytics-tutorial"></a>Log Analytics 教程
Log Analytics 是 Azure 门户中的工具，用于根据 Azure Monitor 日志收集的数据编辑和运行日志查询，并交互式分析其结果。 你可以使用 Log Analytics 查询来检索符合特定条件的记录，确定趋势，分析模式，并提供对数据的各种见解。 

本教程将介绍 Log Analytics 界面，引导你开始使用一些基本查询，并演示如何处理结果。 你将了解以下内容：

> [!div class="checklist"]
> * 了解日志数据的架构
> * 编写和运行简单的查询，以及修改查询时间范围
> * 对查询结果进行筛选、排序和分组
> * 查看、修改和共享查询结果的视觉对象
> * 加载、导出和复制查询与结果

> [!IMPORTANT]
> 在本教程中，你将使用 Log Analytics 功能生成一个查询，并使用另一个示例查询。 准备了解查询语法并开始直接编辑查询本身时，请阅读 [Kusto 查询语言教程](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor)。 该教程演示可供在 Log Analytics 中编辑和运行的示例查询。 它使用了本教程将介绍的多项功能。


## <a name="prerequisites"></a>先决条件
本教程使用 [Log Analytics 演示环境](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade)，其中包含大量支持示例查询的示例数据。 你也可以使用自己的 Azure 订阅，但相同的表中可能没有数据。

## <a name="open-log-analytics"></a>打开 Log Analytics
打开 [Log Analytics 演示环境](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade)，或从订阅中的 Azure Monitor 菜单选择“日志”。 此步骤将把初始范围设置为 Log Analytics 工作区，这样查询将从该工作区中的所有数据中进行选择。 如果从 Azure 资源的菜单中选择“日志”，则范围将设置为仅来自该资源的记录。 有关范围的详细信息，请参阅[日志查询范围](./scope.md)。

可在屏幕左上角查看范围。 如果使用自己的环境，则会看到一个用于选择其他范围的选项。 但在演示环境中不提供此选项。

:::image type="content" source="media/log-analytics-tutorial/scope.png" alt-text="显示演示的 Log Analytics 范围的屏幕截图。" lightbox="media/log-analytics-tutorial/scope.png":::

## <a name="view-table-information"></a>查看表信息
屏幕左侧包含“表”选项卡，可用于检查当前范围内可用的表。 默认情况下，根据解决方案对这些表进行分组，但你可以更改其分组或对其进行筛选。 

展开“日志管理”解决方案并找到 AppRequests 表 。 可以展开该表以查看其架构，或将鼠标悬停在其名称上以显示有关它的更多信息。 

:::image type="content" source="media/log-analytics-tutorial/table-details.png" alt-text="显示表视图的屏幕截图。" lightbox="media/log-analytics-tutorial/table-details.png":::

选择“有用链接”下的链接，转到记录每个表及其列的表格引用。 选择“预览数据”，快速查看表中的一些近期记录。 此预览有助于在运行查询前确保这是你需要的数据。

:::image type="content" source="media/log-analytics-tutorial/sample-data.png" alt-text="显示示例数据的屏幕截图。" lightbox="media/log-analytics-tutorial/sample-data.png":::

## <a name="write-a-query"></a>编写查询
使用 AppRequests 表编写查询。 双击其名称以将其添加到查询窗口。 也可以直接在窗口中键入其名称。 甚至可以使用 IntelliSense，它有助于填写当前范围内和 Kusto 查询语言 (KQL) 命令中的表的名称。

这是我们可以编写的最简单的查询。 它只返回表中的所有记录。 选择“运行”按钮，或将光标置于查询文本中的任意位置并选择 Shift+Enter，即可运行该查询。

:::image type="content" source="media/log-analytics-tutorial/query-results.png" alt-text="显示查询结果的屏幕截图。" lightbox="media/log-analytics-tutorial/query-results.png":::

可以看到我们确实得到了结果。 查询返回的记录数显示在右下角。 

## <a name="filter-query-results"></a>筛选查询结果

让我们向查询添加筛选器以减少返回的记录数。 选择左窗格中的“筛选器”选项卡。 此选项卡显示查询结果中的列，这些列可用来筛选结果。 对于这些列中排名前列的值，将显示具有该值的记录数。 在“ResultCode”下选择“200”，然后选择“应用并运行”  。 

:::image type="content" source="media/log-analytics-tutorial/query-pane.png" alt-text="显示查询窗格的屏幕截图。" lightbox="media/log-analytics-tutorial/query-pane.png":::

将 where 语句添加到具有所选择的值的查询中。 现在的结果仅包含具有该值的记录，因此可以看到记录数减少了。

:::image type="content" source="media/log-analytics-tutorial/query-results-filter-01.png" alt-text="显示已筛选的查询结果的屏幕截图。" lightbox="media/log-analytics-tutorial/query-results-filter-01.png":::


### <a name="time-range"></a>时间范围
Log Analytics 工作区中的所有表都有一个名为“TimeGenerated”的列，这是创建记录的时间。 所有查询都具有时间范围，该范围将结果限制为具有该范围内 TimeGenerated 值的记录。 时间范围可在查询中设置，或者使用屏幕顶部的选择器进行设置。

默认情况下，查询将返回最近 24 小时的记录。 这里应该会显示一条消息，指示未显示全部结果。 这是因为日志分析最多可以返回 30,000 条记录，而我们的查询返回的记录数多于此值。 选择“时间范围”下拉列表，并将值更改为“12 小时” 。 再次选择“运行”以返回结果。 

:::image type="content" source="media/log-analytics-tutorial/query-results-max.png" alt-text="显示时间范围的屏幕截图。" lightbox="media/log-analytics-tutorial/query-results-max.png":::


### <a name="multiple-query-conditions"></a>多个查询条件
让我们再添加一个筛选条件，进一步减少结果。 查询可以包含任意数量的筛选器，以便精确定位所需的记录集。 在“名称”下选择“获取主页/索引”，然后选择“应用并运行”  。 

:::image type="content" source="media/log-analytics-tutorial/query-results-filter-02.png" alt-text="显示应用了多个筛选器的查询的结果的屏幕截图。" lightbox="media/log-analytics-tutorial/query-results-filter-02.png":::


## <a name="analyze-results"></a>分析结果
Log Analytics 不仅有助于编写和运行查询，还提供了用于处理结果的功能。 首先，展开记录以查看其所有列的值。

:::image type="content" source="media/log-analytics-tutorial/expand-record.png" alt-text="显示展开记录的屏幕截图。" lightbox="media/log-analytics-tutorial/expand-record.png":::

选择任何列的名称以按该列对结果进行排序。 选择它旁边的筛选器图标以提供筛选条件。 这类似于向查询本身添加筛选条件，但如果再次运行查询，则会清除此筛选器。 如果要在交互式分析过程中快速分析一组记录，请使用此方法。

例如，对 DurationMs 列设置筛选器，将记录限制为超过 100 毫秒的记录 。 

:::image type="content" source="media/log-analytics-tutorial/query-results-filter.png" alt-text="显示查询结果筛选器的屏幕截图。" lightbox="media/log-analytics-tutorial/query-results-filter.png":::

可以按特定列对记录进行分组，而不对结果进行筛选。 清除刚才创建的筛选器，然后打开“组列”切换按钮。 

:::image type="content" source="media/log-analytics-tutorial/query-results-group-columns.png" alt-text="显示启用列分组的屏幕截图。" lightbox="media/log-analytics-tutorial/query-results-group-columns.png":::

将“URL”列拖到分组行中。 现在，结果按该列进行组织，可以折叠每个组以帮助你进行分析。

:::image type="content" source="media/log-analytics-tutorial/query-results-grouped.png" alt-text="显示已分组的查询结果的屏幕截图。" lightbox="media/log-analytics-tutorial/query-results-grouped.png":::

## <a name="work-with-charts"></a>使用图表
接下来看看一个查询，它使用可在图表中查看的数值数据。 我们将选择一个示例查询，而不是生成查询。

在左侧窗格中选择“查询”。 此窗格包含可添加到查询窗口的示例查询。 如果使用自己的工作区，则应具有多个类别的多种查询。 如果使用演示环境，可能只会看到一个“Log Analytics 工作区”类别。 展开此项以查看该类别的查询。

在“应用程序”类别中选择名为“函数错误率”的查询 。 此步骤会将查询添加到查询窗口。 请注意，用空白行分隔了新查询和另一个查询。 KQL 中的查询遇到空白行时将结束，因此它们被视为单独的查询。 

:::image type="content" source="media/log-analytics-tutorial/example-query.png" alt-text="展示新查询的屏幕截图。" lightbox="media/log-analytics-tutorial/example-query.png":::

光标所在位置是当前的查询。 可以看到第一个查询突出显示，表明它是当前查询。 单击新查询中的任意位置以将其选中，然后选择“运行”按钮以运行该查询。

:::image type="content" source="media/log-analytics-tutorial/example-query-output-table.png" alt-text="显示查询结果表的屏幕截图。" lightbox="media/log-analytics-tutorial/example-query-output-table.png":::

若要在关系图中查看结果，请在结果窗格中选择“图表”。  请注意，处理图表的选择有多种，例如将其更改为另一种类型。

:::image type="content" source="media/log-analytics-tutorial/example-query-output-chart.png" alt-text="显示查询结果图表的屏幕截图。" lightbox="media/log-analytics-tutorial/example-query-output-chart.png":::


## <a name="next-steps"></a>后续步骤

现在，你已了解如何使用 Log Analytics，请完成有关使用日志查询的教程：
> [!div class="nextstepaction"]
> [编写 Azure Monitor 日志查询](get-started-queries.md)
