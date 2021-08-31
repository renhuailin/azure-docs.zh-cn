---
title: Azure 数据资源管理器数据分析（ADX 数据分析）| Microsoft Docs
description: 本文介绍 Azure 数据资源管理器数据分析（ADX 数据分析）
services: azure-monitor
ms.topic: conceptual
ms.date: 01/05/2021
author: lgayhardt
ms.author: lagayhar
ms.openlocfilehash: 872c1e29b6c85f24c4e9841dca359a9429b92321
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114458109"
---
# <a name="azure-data-explorer-insights"></a>Azure 数据资源管理器数据分析

Azure 数据资源管理器数据分析通过提供群集性能、操作、使用情况和故障的统一视图，对群集进行全面监视。
本文将帮助你了解如何加入和使用 Azure 数据资源管理器数据分析。

## <a name="introduction-to-azure-data-explorer-insights"></a>Azure 数据资源管理器数据分析简介

在深入了解此体验之前，应该了解它如何呈现和可视化信息。
-    大规模透视图，其中显示群集主要指标的快照视图，以方便跟踪查询、引入和导出操作的性能。
-   特定 Azure 数据资源管理器群集的深化分析，可帮助执行详细分析。
-    可自定义，可在其中更改要查看的指标，修改或设置与限制相符的阈值，以及保存自己的自定义工作簿。 可以将工作簿中的图表固定到 Azure 面板。

## <a name="view-from-azure-monitor-at-scale-perspective"></a>Azure Monitor 中的视图（大规模透视图）

在 Azure Monitor 中，可以查看群集的主要性能指标（包括订阅中多个群集的查询、引入和导出操作的指标），并帮助识别性能问题。

若要查看所有订阅中群集的性能，请执行以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com/)

2. 在 Azure 门户的左侧窗格中选择“监视”，然后在“见解中心”部分下选择“Azure 数据资源管理器群集” 。

![“概览”体验的屏幕截图，其中显示了多个关系图](./media/data-explorer/insights-hub.png)

### <a name="overview-tab"></a>概述选项卡

在所选订阅的“概述”选项卡上，表格显示了订阅中分组的 Azure 数据资源管理器群集的交互指标。 你可以根据从下面的下拉列表中选择的选项来筛选结果：

* 订阅 - 仅列出了包含 Azure 数据资源管理器群集的订阅。

* Azure 数据资源管理器群集 - 默认情况下，最多只会预先选择 5 个群集。 如果在范围选择器中选择所有或多个群集，则最多会返回 200 个群集。

* 时间范围 – 默认情况下，系统会根据你所做的选择显示过去 24 小时的相应信息。

下拉列表下的计数器磁贴汇总了所选订阅中 Azure 数据资源管理器群集的总数，并反映了选择的群集数。 以下各列带有条件颜色编码：“保持活动状态”、“CPU”、“引入利用率”和“缓存利用率”。 对于群集而言，以橙色编码的单元格中的值是不可长期承受的。 

为了更好地了解每个指标的含义，建议通读有关 [Azure 数据资源管理器指标](/azure/data-explorer/using-metrics#cluster-metrics)的文档。

### <a name="query-performance-tab"></a>“查询性能”选项卡

此选项卡显示查询持续时间、并发查询总数和受限制查询总数。

![“查询性能”选项卡的屏幕截图](./media/data-explorer/query-performance.png)

### <a name="ingestion-performance-tab"></a>“引入性能”选项卡

此选项卡显示引入延迟、成功引入结果数、失败的引入结果数、引入量，以及事件中心/IoT 中心处理的事件数。

[![“引入性能”选项卡的屏幕截图](./media/data-explorer/ingestion-performance.png)](./media/data-explorer/ingestion-performance.png#lightbox)

### <a name="streaming-ingest-performance-tab"></a>“流引入性能”选项卡

此选项卡提供有关平均数据速率、平均持续时间和请求速率的信息。

### <a name="export-performance-tab"></a>“导出性能”选项卡

此选项卡提供有关连续导出操作的已导出记录数、延迟、挂起计数和利用率百分比的信息。

## <a name="view-from-an-azure-data-explorer-cluster-resource-drill-down-analysis"></a>Azure 数据资源管理器群集资源中的视图（深化分析）

直接从 Azure 数据资源管理器群集中访问 Azure 数据资源管理器数据分析：

1. 在 Azure 门户中，选择“Azure 数据资源管理器群集”。

2. 从列表中选择一个 Azure 数据资源管理器群集。 在“监视”部分中，选择“见解”。

还可以通过从 Azure Monitor 见解视图中选择某个 Azure 数据资源管理器群集的资源名称来访问这些视图。

Azure 数据资源管理器数据分析结合日志和指标，提供全局监视解决方案。 包含基于日志的可视化效果需要用户[启用其 Azure 数据资源管理器群集的诊断日志记录，并将日志发送到 Log Analytics 工作区](/azure/data-explorer/using-diagnostic-logs?tabs=commands-and-queries#enable-diagnostic-logs)。 应启用的诊断日志包括：Command、Query、TableDetails 和 TableUsageStatistics   。

![显示文本“启用日志以进行监视”的蓝色按钮屏幕截图](./media/data-explorer/enable-logs.png)


 “概述”选项卡显示：

- “指标”磁贴，其中突出显示了群集的可用性和总体状态，以用于快速评估其运行状况。

- 有效[顾问建议](/azure/data-explorer/azure-advisor)和[资源运行状况](/azure/data-explorer/monitor-with-resource-health)的摘要。

- 一些图表，其中显示了一段时间内消耗量最大的几个 CPU 消耗者和内存消耗者以及唯一用户数。


[![Azure 数据资源管理器群集资源中的视图的屏幕截图](./media/data-explorer/overview.png)](./media/data-explorer/overview.png#lightbox)

“关键指标”选项卡显示某些群集指标的统一视图，其分组依据如下：常规指标、与查询相关的指标、与引入相关的指标，以及与流引入相关的指标。

[![“故障”视图的屏幕截图](./media/data-explorer/key-metrics.png)](./media/data-explorer/key-metrics.png#lightbox)

“使用情况”选项卡可让用户深入了解群集命令和查询的性能。 在此页中，可以：
 
 - 查看哪些工作负载组、用户和应用程序发送的查询最多或者消耗的 CPU 和内存最多（这样就可以了解哪些工作负载正在提交最繁忙的查询供群集处理）。
 - 按照失败的查询数识别排名靠前的工作负载组、用户和应用程序。
 - 通过将查询数按工作负载组、用户和应用程序与（过去 16 天的）历史每日平均值进行比较，识别查询数最近发生的变化。
 - 按照工作负载组、用户、应用程序和命令类型，识别查询数、内存和 CPU 消耗量的趋势与峰值。

[![操作视图的屏幕截图，其中的圆环图按命令和查询计数显示了排名最高的应用程序、按命令和查询计数显示了排名最高的主体，按命令类型显示了排名最高的命令](./media/data-explorer/usage.png)](./media/data-explorer/usage.png#lightbox)

[![操作视图的屏幕截图，其中的折线图按应用程序显示了查询计数、按应用程序显示了内存总消耗量，并按应用程序显示了 CPU 总消耗量](./media/data-explorer/usage-2.png)](./media/data-explorer/usage-2.png#lightbox)

“表”选项卡显示群集中的表的最新属性和历史属性。 可以查看哪些表占用的空间最多，按表大小、热数据和行数跟踪一段时间内的增长历史记录。

“缓存”选项卡可让用户分析其实际查询的回溯时段图，并将其与已配置的缓存策略（针对每个表）进行比较。 可以识别由大多数查询和表使用的、但根本未查询的表，并相应地调整缓存策略。 可以基于实际查询在过去 30 天的回溯时段和适用于至少 95% 查询的未优化缓存策略，在 Azure 顾问中获取有关特定表的特殊缓存策略建议（目前，只能从 [Azure 顾问主仪表板](/azure/data-explorer/azure-advisor#use-the-azure-advisor-recommendations)查看缓存建议）。 Azure 顾问中的缓存缩减建议适用于“受数据限制”的群集（即，这些群集的 CPU 利用率和引入利用率较低，但由于数据容量较高，群集无法横向或纵向缩减）。

[![缓存详细信息的屏幕截图](./media/data-explorer/cache-tab.png)](./media/data-explorer/cache-tab.png#lightbox)

“群集边界”选项卡根据使用情况显示群集边界。 在此选项卡中，你可以检查 CPU、引入和缓存利用率。 这些指标评分为“低”、“中”或“高”。 确定群集的最佳 SKU 和实例计数时，这些指标和分数非常重要，并且在 Azure 顾问 SKU/大小建议中需考虑这些内容。 在此选项卡中，可以选择一个指标图块，深入了解其趋势以及如何确定其分数。 还可以查看群集的 Azure 顾问 SKU/大小建议。 例如，在下图中，你可以看到所有指标均评分为“低”，因此该群集会收到一项成本建议，使其能够横向缩减/按比例缩减，从而节省成本。

> [!div class="mx-imgBorder"]
> [![群集边界的屏幕截图。](./media/data-explorer/cluster-boundaries.png)](./media/data-explorer/cluster-boundaries.png#lightbox)

## <a name="pin-to-azure-dashboard"></a>固定到 Azure 仪表板

可将（“大规模”透视图的）任一指标部分固定到 Azure 仪表板，只需选择该部分右上角的图钉图标即可。

![已选择的固定图标的屏幕截图](./media/data-explorer/pin.png)

## <a name="customize-azure-data-explorer-insights"></a>自定义 Azure 数据资源管理器数据分析

此部分重点介绍了编辑工作簿的常见方案，以进行自定义来满足数据分析需求：
* 限定工作簿的范围，以便始终选择特定的订阅或 Azure 数据资源管理器群集
* 更改网格中的指标
* 更改阈值或颜色呈现/编码

从顶部工具栏选择“自定义”按钮可启用编辑模式，以开始自定义。

![“自定义”按钮的屏幕截图](./media/data-explorer/customize.png)

自定义项保存到自定义工作簿，以防止覆盖已发布工作簿中的默认配置。 无论是在专用的“我的报表”部分，还是在有权访问资源组的任何用户均可访问的“共享报表”部分，工作簿都保存在某个资源组中。 保存自定义工作簿后，需要转到工作簿库来启动它。

![工作簿库的屏幕截图](./media/data-explorer/gallery.png)

## <a name="troubleshooting"></a>故障排除

如需常规故障排除指南，请参阅专用的基于工作簿的见解[故障排除文章](troubleshoot-workbooks.md)。

本部分帮助你诊断和排查在使用 Azure 数据资源管理器数据分析时可能遇到的一些常见问题。 使用下面的列表来查找与具体问题相关的信息。

### <a name="why-dont-i-see-all-my-subscriptions-in-the-subscription-picker"></a>为什么在订阅选取器中看不到所有订阅？

我们只显示从所选订阅筛选器中选择的包含 Azure 数据资源管理器群集的订阅（在 Azure 门户标题上的“目录 + 订阅”中选择）。

![订阅筛选器的屏幕截图](./media/key-vaults-insights-overview/Subscriptions.png)

### <a name="why-do-i-not-see-any-data-for-my-azure-data-explorer-cluster-under-the-usage-tables-or-cache-sections"></a>“使用情况”、“表”或“缓存”部分下为何不显示我的 Azure 数据资源管理器群集的任何数据？

若要查看基于日志的数据，需要为你要监视的每个 Azure 数据资源管理器群集[启用诊断日志](/azure/data-explorer/using-diagnostic-logs?tabs=commands-and-queries#enable-diagnostic-logs)。 可以在每个群集的诊断设置下完成此操作。 需要将数据发送到 Log Analytics 工作区。 应启用的诊断日志包括：Command、Query、TableDetails 和 TableUsageStatistics。

### <a name="i-have-already-enabled-logs-for-my-azure-data-explorer-cluster-why-am-i-still-unable-to-see-my-data-under-commands-and-queries"></a>我已经为我的 Azure 数据资源管理器群集启用了日志，但为何“命令”和“查询”下仍不显示我的数据？

目前，诊断日志无法追溯，只有在对 Azure 数据资源管理器执行了操作后，才会开始显示数据。 因此，可能需要在一段时间后才会显示你的数据，所需时间为几小时到一天不等，具体取决于 Azure 数据资源管理器群集的活跃程度。


## <a name="next-steps"></a>后续步骤

查看[使用 Azure Monitor 工作簿创建交互式报表](../visualize/workbooks-overview.md)，了解工作簿旨在支持的方案、创作新报表和自定义现有报表的方式，以及更多信息。
