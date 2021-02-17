---
title: Azure 数据资源管理器 (预览) 的 Azure Monitor |Microsoft Docs
description: 本文介绍 Azure 数据资源管理器群集的 Azure Monitor 见解。
services: azure-monitor
ms.topic: conceptual
ms.date: 01/05/2021
author: lgayhardt
ms.author: lagayhar
ms.openlocfilehash: 6276527230cf889b471887fdb3da70e06a2fc8d6
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100582347"
---
# <a name="azure-monitor-for-azure-data-explorer-preview"></a>Azure 数据资源管理器 (预览版的 Azure Monitor) 

Azure 数据资源管理器 (预览) Azure Monitor 提供群集性能、操作、使用情况和故障的统一视图，从而全面监视群集。
本文将帮助你了解如何在 Azure 数据资源管理器 (预览) 中加入并使用 Azure Monitor。

## <a name="introduction-to-azure-monitor-for-azure-data-explorer-preview"></a>Azure 数据资源管理器 (预览版 Azure Monitor 简介) 

在深入了解此体验之前，应该了解它如何呈现和可视化信息。
-    **大规模透视** 显示群集主要指标的快照视图，以便轻松跟踪查询、引入和导出操作的性能。
-   向 **下钻取** 特定 Azure 数据资源管理器群集的分析，以帮助执行详细分析。
-    可 **自定义**，你可以在其中更改要查看的指标、修改或设置与限制相符的阈值，并保存你自己的自定义工作簿。 可以将工作簿中的图表固定到 Azure 面板。

## <a name="view-from-azure-monitor-at-scale-perspective"></a>横向 Azure Monitor (的视图) 

从 Azure Monitor 中，可以查看群集的主要性能指标，包括订阅中多个分类的查询、引入和导出操作的指标，并帮助确定性能问题。

若要查看所有订阅中群集的性能，请执行以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com/)

2. 从 "Azure 门户中的左窗格中选择" **监视器** "，然后在" Insights Hub "部分，选择" **Azure 数据资源管理器群集 " (预览)**。

![“概览”体验的屏幕截图，其中显示了多个关系图](./media/data-explorer/insights-hub.png)

### <a name="overview-tab"></a>概述选项卡

在所选订阅的 " **概述** " 选项卡上，表显示在订阅内分组的 Azure 数据资源管理器群集的交互式指标。 你可以根据从下面的下拉列表中选择的选项来筛选结果：

* 订阅–只列出具有 Azure 数据资源管理器群集的订阅。

* Azure 数据资源管理器群集–默认情况下，最多只能预选择5个群集。 如果在作用域选择器中选择 "全部" 或多个群集，则将返回最多200个群集。

* 时间范围–默认情况下，基于所做的相应选择显示最近24小时内的信息。

下拉列表下的 "计数器" 磁贴汇总了所选订阅中的 Azure 数据资源管理器群集总数，并反映了所选的数目。 列有条件颜色 codings：保持活动、CPU、引入利用率和缓存利用率。 橙色编码的单元具有的值对于群集来说不是可持续的。 

为了更好地了解每个指标表示的内容，建议通读有关 [Azure 数据资源管理器指标](/azure/data-explorer/using-metrics#cluster-metrics)的文档。

### <a name="query-performance-tab"></a>查询性能选项卡

此选项卡显示查询持续时间、并发查询总数和限制查询总数。

!["查询性能" 选项卡的屏幕截图](./media/data-explorer/query-performance.png)

### <a name="ingestion-performance-tab"></a>引入性能选项卡

此选项卡显示引入延迟、成功引入结果、失败引入结果、引入卷，以及事件/IoT 中心处理的事件。

[!["引入性能" 选项卡的屏幕截图](./media/data-explorer/ingestion-performance.png)](./media/data-explorer/ingestion-performance.png#lightbox)

### <a name="streaming-ingest-performance-tab"></a>流式处理引入性能选项卡

此选项卡提供有关平均数据速率、平均持续时间和请求速率的信息。

### <a name="export-performance-tab"></a>导出性能选项卡

此选项卡提供有关连续导出操作的已导出记录、迟到、挂起计数和使用率百分比的信息。

## <a name="view-from-an-azure-data-explorer-cluster-resource-drill-down-analysis"></a>查看 Azure 数据资源管理器群集资源 (深入分析) 

若要直接从 Azure 数据资源管理器群集访问 Azure 数据资源管理器群集的 Azure Monitor，请执行以下操作：

1. 在 Azure 门户中，选择 " **Azure 数据资源管理器群集**"。

2. 从列表中选择 "Azure 数据资源管理器群集"。 在 "监视" 部分，选择 " **Insights (预览")**。

还可以通过从 Azure Monitor insights 视图中选择 Azure 数据资源管理器群集的资源名称来访问这些视图。

Azure 数据资源管理器 Azure Monitor 结合了日志和指标来提供全局监视解决方案。 包含基于日志的可视化效果要求用户[启用其 Azure 数据资源管理器群集的诊断日志记录，并将其发送到 Log Analytics 工作区。](/azure/data-explorer/using-diagnostic-logs?tabs=commands-and-queries#enable-diagnostic-logs) 应启用的诊断日志包括： **命令**、 **查询**、 **TableDetails** 和 **TableUsageStatistics**。

![显示文本 "为监视启用日志" 的蓝色按钮屏幕截图](./media/data-explorer/enable-logs.png)


 " **概述** " 选项卡显示：

- 度量值磁贴突出显示群集的可用性和总体状态，以便快速评估其运行状况。

- 活动 [顾问建议](/azure/data-explorer/azure-advisor) 和 [资源运行状况](/azure/data-explorer/monitor-with-resource-health) 状态的摘要。

- 显示一段时间内最常见的 CPU 和内存使用者以及唯一用户数的图表。


[![Azure 数据资源管理器群集资源中的视图的屏幕截图](./media/data-explorer/overview.png)](./media/data-explorer/overview.png#lightbox)

" **关键指标** " 选项卡显示了部分群集指标的统一视图，按以下方式分组：常规指标、与查询相关的、引入相关的指标以及与流引入相关的指标。

[![“故障”视图的屏幕截图](./media/data-explorer/key-metrics.png)](./media/data-explorer/key-metrics.png#lightbox)

" **使用情况** " 选项卡允许用户深入了解群集的命令和查询的性能。 在此页上，您可以：
 
 - 查看哪些用户和应用程序正在发送最多查询或消耗最多的 CPU 和内存 (以便您可以了解哪些用户正在提交最繁忙的查询以便群集处理) 。
 - 通过失败的查询识别顶级用户和应用程序。
 -  (与过去16天) ，按用户和应用程序相比，确定查询数量最近的更改。
 - 按用户、应用程序和命令类型识别查询、内存和 CPU 使用率的趋势和高峰。

[![带有环形图的操作视图的屏幕截图，按命令和查询计数显示的顶级应用程序](./media/data-explorer/usage.png)](./media/data-explorer/usage.png#lightbox)

[![操作视图的屏幕截图，其中包含查询计数（按应用程序）、按应用程序的总内存和应用程序总数](./media/data-explorer/usage-2.png)](./media/data-explorer/usage-2.png#lightbox)

" **表** " 选项卡显示群集中表的最新属性和历史属性。 你可以看到哪些表消耗的空间最多，并按表大小、热数据和行数随时间推移跟踪增长历史记录。

" **缓存** " 选项卡允许用户分析其实际查询的 "查看" 模式，并将其与每个表)  (配置的缓存策略进行比较。 您可以确定大多数查询和表所使用的表，这些表根本不会进行查询，并相应地调整缓存策略。 你可能会在 Azure 顾问中的特定表上获得特定的缓存策略建议 (当前情况下，缓存建议仅在过去30天内 [) 的实际查询的](/azure/data-explorer/azure-advisor#use-the-azure-advisor-recommendations) 情况下，在过去30天内查看缓存建议，对于至少95% 的查询，缓存建议仅可用于% 的查询。 Azure 顾问中的缓存缩减建议适用于 "受数据限制" 的群集 (这意味着群集的 CPU 利用率较低，但引入利用率较低，但由于数据量较高，群集无法向下扩展或向下缩放) 。

[![缓存详细信息的屏幕截图](./media/data-explorer/cache-tab.png)](./media/data-explorer/cache-tab.png#lightbox)

## <a name="pin-to-azure-dashboard"></a>固定到 Azure 仪表板

可以通过选择部分右上角的图钉图标，将 "大规模") 透视图 (的任何一个指标部分固定到 Azure 仪表板。

![已选择的固定图标的屏幕截图](./media/data-explorer/pin.png)

## <a name="customize-azure-monitor-for-azure-data-explorer-cluster"></a>自定义 Azure 数据资源管理器群集的 Azure Monitor

此部分重点介绍了编辑工作簿的常见方案，以进行自定义来满足数据分析需求：
* 将工作簿的范围限定为始终选择特定订阅或 Azure 数据资源管理器群集 (s) 
* 更改网格中的指标
* 更改阈值或颜色呈现/编码

从顶部工具栏选择“自定义”按钮可启用编辑模式，以开始自定义。

![“自定义”按钮的屏幕截图](./media/data-explorer/customize.png)

自定义项保存到自定义工作簿，以防止覆盖已发布工作簿中的默认配置。 无论是在专用的“我的报表”部分，还是在有权访问资源组的任何用户均可访问的“共享报表”部分，工作簿都保存在某个资源组中。 保存自定义工作簿后，需要转到工作簿库来启动它。

![工作簿库的屏幕截图](./media/data-explorer/gallery.png)

## <a name="troubleshooting"></a>故障排除

如需常规故障排除指南，请参阅专用的基于工作簿的见解[故障排除文章](troubleshoot-workbooks.md)。

本部分将帮助你诊断和排查在 Azure Monitor 使用 Azure 数据资源管理器群集 (预览) 时可能会遇到的一些常见问题。 使用下面的列表来查找与具体问题相关的信息。

### <a name="why-dont-i-see-all-my-subscriptions-in-the-subscription-picker"></a>为什么在订阅选取器中看不到所有订阅？

我们只显示包含从所选订阅筛选器中选择的 Azure 数据资源管理器群集的订阅，该筛选器在 Azure 门户标头的 "目录 + 订阅" 中选择。

![订阅筛选器的屏幕截图](./media/key-vaults-insights-overview/Subscriptions.png)

### <a name="why-do-i-not-see-any-data-for-my-azure-data-explorer-cluster-under-the-usage-tables-or-cache-sections"></a>为什么在 "使用情况"、"表" 或 "缓存" 部分下看不到 Azure 数据资源管理器群集的任何数据？

若要查看基于日志的数据，需要为要监视的每个 Azure 数据资源管理器群集 [启用诊断日志](/azure/data-explorer/using-diagnostic-logs?tabs=commands-and-queries#enable-diagnostic-logs) 。 这可以在每个群集的诊断设置下完成。 需要将数据发送到 Log Analytics 工作区。 应启用的诊断日志包括：命令、查询、TableDetails 和 TableUsageStatistics。

### <a name="i-have-already-enabled-logs-for-my-azure-data-explorer-cluster-why-am-i-still-unable-to-see-my-data-under-commands-and-queries"></a>我已经为 Azure 数据资源管理器群集启用了日志，为什么我在命令和查询下看不到我的数据？

目前，诊断日志无法以追溯方式，因此，只有在对 Azure 数据资源管理器执行了操作后，才会显示数据。 因此，可能需要一段时间，范围从几小时到一天，具体取决于 Azure 数据资源管理器群集的活动情况。


## <a name="next-steps"></a>后续步骤

查看[使用 Azure Monitor 工作簿创建交互式报表](../visualize/workbooks-overview.md)，了解工作簿旨在支持的方案、创作新报表和自定义现有报表的方式，以及更多信息。