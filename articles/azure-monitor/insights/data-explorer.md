---
title: Azure 数据资源管理器数据分析 | Microsoft Docs
description: 本文介绍如何使用 Azure 数据资源管理器数据分析。
services: azure-monitor
ms.topic: conceptual
ms.date: 01/05/2021
author: lgayhardt
ms.author: lagayhar
ms.openlocfilehash: b522e510968b97d80917bc6fa4123d33e4480cbf
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128662203"
---
# <a name="azure-data-explorer-insights"></a>Azure 数据资源管理器数据分析

Azure 数据资源管理器数据分析通过提供群集性能、操作、使用情况和故障的统一视图，对群集进行全面监视。

其提供了以下功能：

-    大规模透视。 群集主要指标的快照视图，以帮助跟踪查询、引入和导出操作的性能。
-   向下钻取分析。 你可以向下钻取到特定 Azure 数据资源管理器群集以执行详细分析。
-    自定义。 可更改要查看的指标，修改或设置与限制相符的阈值，以及保存自己的自定义工作簿。 然后，可将工作簿中的图表固定到 Azure 仪表板。

本文将帮助你了解如何加入和使用 Azure 数据资源管理器数据分析。

## <a name="view-from-azure-monitor-at-scale-perspective"></a>Azure Monitor 中的视图（大规模透视图）

从 Azure Monitor，可以查看群集的主要性能指标。 这些指标包括以下方面的信息：订阅中的多个群集中的查询、引入和导出操作。 这些指标可以帮助确定性能问题。

若要查看所有订阅中群集的性能：

1. 登录 [Azure 门户](https://portal.azure.com/)。

2. 在左窗格中选择“监视”。 在“见解中心”部分中，选择“Azure 数据资源管理器群集”。 

![用于查看 Azure 数据资源管理器性能的选项的屏幕截图。](./media/data-explorer/insights-hub.png)

### <a name="overview-tab"></a>概述选项卡

在所选订阅的“概述”选项卡上，表格显示了订阅中分组的 Azure 数据资源管理器群集的交互指标。 可以根据你从下面的下拉列表中选择的选项来筛选结果：

* 订阅：仅列出了包含 Azure 数据资源管理器群集的订阅。

* Azure 数据资源管理器群集：默认情况下，最多会预先选择 5 个群集。 如果在范围选择器中选择所有或多个群集，则最多会返回 200 个群集。

* 时间范围：默认情况下，表会根据你所做的选择显示过去 24 小时的相应信息。

下拉列表下的计数器磁贴提供了所选订阅中 Azure 数据资源管理器群集的总数，并显示已选择的群集数。 以下各列带有条件颜色编码：“保持活动状态”、“CPU”、“引入利用率”和“缓存利用率”。    对于群集而言，以橙色编码的单元格中的值是不可长期承受的。 

为了更好地了解每个指标的含义，建议通读有关 [Azure 数据资源管理器指标](/azure/data-explorer/using-metrics#cluster-metrics)的文档。

### <a name="query-performance-tab"></a>“查询性能”选项卡

“查询性能”选项卡显示查询持续时间、并发查询总数和受限制查询总数。

![“查询性能”选项卡的屏幕截图。](./media/data-explorer/query-performance.png)

### <a name="ingestion-performance-tab"></a>“引入性能”选项卡

“引入性能”选项卡显示引入延迟、成功引入结果数、失败的引入结果数、引入量，以及事件中心和 IoT 中心处理的事件数。

[![“引入性能”选项卡的屏幕截图。](./media/data-explorer/ingestion-performance.png)](./media/data-explorer/ingestion-performance.png#lightbox)

### <a name="streaming-ingest-performance-tab"></a>“流引入性能”选项卡

“流引入性能”选项卡提供有关平均数据速率、平均持续时间和请求速率的信息。

### <a name="export-performance-tab"></a>“导出性能”选项卡

“导出性能”选项卡提供有关连续导出操作的已导出记录数、延迟、挂起计数和利用率百分比的信息。

## <a name="view-from-an-azure-data-explorer-cluster-resource-drill-down-analysis"></a>Azure 数据资源管理器群集资源中的视图（向下钻取分析）

直接从 Azure 数据资源管理器群集中访问 Azure 数据资源管理器见解：

1. 在 Azure 门户中，选择“Azure 数据资源管理器群集”。

2. 从列表中选择一个 Azure 数据资源管理器群集。 在“监视”部分中选择“见解”。

还可以通过从 Azure Monitor 见解视图中选择某个 Azure 数据资源管理器群集的资源名称来访问这些视图。

> [!NOTE]
> Azure 数据资源管理器数据分析结合日志和指标，提供全局监视解决方案。 包含基于日志的可视化效果要求用户[启用其 Azure 数据资源管理器群集的诊断日志记录，并将日志发送到 Log Analytics 工作区](/azure/data-explorer/using-diagnostic-logs?tabs=commands-and-queries#enable-diagnostic-logs)。 应启用的命令包括：Command、Query、SucceededIngestion、FailedIngestion、IngestionBatching、TableDetails 和 TableUsageStatistics。       （启用 SucceededIngestion 日志可能会成本很高。 仅当需要监视成功引入时，才启用这些日志。）

![配置监视日志的按钮的屏幕截图。](./media/data-explorer/enable-logs.png)

### <a name="overview-tab"></a>概述选项卡

“概述”选项卡显示：

- 突出显示群集可用性和总体状态的指标磁贴，以便快速评估健康状况。

- 活动的 [Azure 顾问建议](/azure/data-explorer/azure-advisor)和[资源运行状况](/azure/data-explorer/monitor-with-resource-health)状态的摘要。

- 一些图表，其中显示了一段时间内消耗量最大的几个 CPU 消耗者和内存消耗者以及唯一用户数。

[![Azure 数据资源管理器群集资源中的视图的屏幕截图。](./media/data-explorer/overview.png)](./media/data-explorer/overview.png#lightbox)

### <a name="key-metrics-tab"></a>“关键指标”选项卡

“关键指标”选项卡显示群集某些指标的统一视图。 它们分为一般指标、与查询相关的指标、与引入相关的指标以及与流式引入相关的指标。

[![“关键指标”选项卡上图形的屏幕截图。](./media/data-explorer/key-metrics.png)](./media/data-explorer/key-metrics.png#lightbox)

### <a name="usage-tab"></a>“使用情况”选项卡

“使用情况”选项卡可让用户深入了解群集命令和查询的性能。 在此选项卡上，可以：
 
- 查看哪些工作负荷组、用户和应用程序发送的查询最多或消耗的 CPU 和内存最多。 然后，您可以了解群集处理的最繁重查询是由哪些工作负荷提交的。
- 按照失败的查询数识别排名靠前的工作负载组、用户和应用程序。
- 通过将查询数按工作负载组、用户和应用程序与（过去 16 天的）历史每日平均值进行比较，识别查询数最近发生的变化。
- 按照工作负载组、用户、应用程序和命令类型，识别查询数、内存和 CPU 消耗量的趋势与峰值。

[![操作视图的屏幕截图，其中显示了与命令和查询相关的圆环图。](./media/data-explorer/usage.png)](./media/data-explorer/usage.png#lightbox)

[![操作视图的屏幕截图，其中包含与查询和内存相关的折线图。](./media/data-explorer/usage-2.png)](./media/data-explorer/usage-2.png#lightbox)

### <a name="tables-tab"></a>“表”选项卡

“表”选项卡显示群集中的表的最新属性和历史属性。 可以看到哪些表占用的空间最多。 还可以按表大小、热数据和行数跟踪一段时间内的增长历史记录。

### <a name="cache-tab"></a>“缓存”选项卡

“缓存”选项卡可让用户分析其实际查询的回溯时段图，并将其与已配置的缓存策略（针对每个表）进行比较。 可以识别由大多数查询和表使用的、但根本未查询的表，并相应地调整缓存策略。 

可能会获得针对 Azure 顾问中某些特定表的缓存策略建议。 目前仅通过 [Azure 顾问主仪表板](/azure/data-explorer/azure-advisor#use-the-azure-advisor-recommendations)来提供缓存建议。 它们基于过去 30 天内实际查询的回看窗口，以及至少 95% 查询的未优化缓存策略。 

Azure 顾问中提供的缓存减少建议适用于“受数据限制”的群集。 这意味着群集的 CPU 负载和输入利用率较低，但由于数据容量较高，群集无法横向缩减或纵向缩减。

[![缓存详细信息的屏幕截图。](./media/data-explorer/cache-tab.png)](./media/data-explorer/cache-tab.png#lightbox)

### <a name="cluster-boundaries-tab"></a>“群集边界”选项卡

“群集边界”选项卡根据使用情况显示群集边界。 在此选项卡中，你可以检查 CPU、引入和缓存利用率。 这些指标评分为“低”、“中”或“高”。   当您决定集群的最佳 SKU 和实例数时，这些指标和分数很重要。 Azure 顾问 SKU/大小建议中会将这些指标考虑在内。 

在此选项卡中，可以选择一个指标磁贴，深入了解其趋势以及如何确定其分数。 还可以查看群集的 Azure 顾问 SKU/大小建议。 例如，在下图中，可以看到所有指标都评分为“低”。 群集会收到成本建议，该建议允许进行横向缩减/纵向缩减并节省成本。

> [!div class="mx-imgBorder"]
> [![群集边界的屏幕截图。](./media/data-explorer/cluster-boundaries.png)](./media/data-explorer/cluster-boundaries.png#lightbox)

## <a name="pin-to-an-azure-dashboard"></a>固定到 Azure 仪表板

可将（“大规模”透视图的）任一指标部分固定到 Azure 仪表板，只需选择该部分右上角的图钉图标即可。

![已选择的固定图标的屏幕截图。](./media/data-explorer/pin.png)

## <a name="customize-azure-data-explorer-insights"></a>自定义 Azure 数据资源管理器数据分析

你可以编辑工作簿，以便对其进行自定义以支持数据分析需求：
* 限定工作簿的范围，以便始终选择特定的订阅或 Azure 数据资源管理器群集。
* 更改网格中的指标。
* 更改阈值或颜色呈现/编码。

选择顶部工具栏中的“自定义”按钮即可开始进行自定义。

![“自定义”按钮的屏幕截图。](./media/data-explorer/customize.png)

自定义项将保存到自定义工作簿，以防止覆盖已发布工作簿中的默认配置。 无论是在私有的“我的报表”部分中，还是在有权访问资源组的每个人均可访问的“共享报表”部分中，工作簿都保存在某个资源组中。 保存自定义工作簿后，转到工作簿库来打开它。

![工作簿库的屏幕截图。](./media/data-explorer/gallery.png)

## <a name="troubleshooting"></a>疑难解答

有关一般疑难解答指南，请参阅[基于工作簿的见解疑难解答](troubleshoot-workbooks.md)文章。

以下部分将帮助你诊断和解决使用 Azure 数据资源管理器数据分析时可能会遇到的一些常见问题。

### <a name="why-dont-i-see-all-my-subscriptions-in-the-subscription-picker"></a>为什么在订阅选取器中看不到所有订阅？

Azure 数据资源管理器数据分析仅显示特定订阅，这些订阅包含从所选订阅筛选器中选择的 Azure 数据资源管理器群集。 在 Azure 门户中的“目录 + 订阅”下选择订阅筛选器。

![选择订阅筛选器的屏幕截图。](./media/key-vaults-insights-overview/Subscriptions.png)

### <a name="why-dont-i-see-any-data-for-my-azure-data-explorer-cluster-under-the-usage-tables-or-cache-section"></a>“使用情况”、“表”或“缓存”部分下为何不显示我的 Azure 数据资源管理器群集的任何数据？

若要查看基于日志的数据，需要为你要监视的每个 Azure 数据资源管理器群集[启用诊断日志](/azure/data-explorer/using-diagnostic-logs?tabs=commands-and-queries#enable-diagnostic-logs)。 可以在每个群集的诊断设置下完成此操作。 需要将数据发送到 Log Analytics 工作区。 应启用的诊断日志包括：Command、Query、TableDetails 和 TableUsageStatistics。   

### <a name="ive-already-enabled-logs-for-my-azure-data-explorer-cluster-why-am-i-still-unable-to-see-my-data-under-commands-and-queries"></a>我已为 Azure 数据资源管理器群集启用了日志。 为什么我在 Commands 和 Queries 下仍然看不到我的数据？

目前，诊断日志不能追溯方式下工作。 在 Azure 数据资源管理器中执行操作之后，数据将开始出现。 可能需要在一段时间后才会显示你的数据，所需时间为几小时到一天不等，具体取决于 Azure 数据资源管理器群集的活跃程度。

## <a name="next-steps"></a>后续步骤

查看[使用 Azure Monitor 工作簿创建交互式报表](../visualize/workbooks-overview.md)，了解工作簿在设计上支持的方案、创作新报表和自定义现有报表的方式以及更多信息。
