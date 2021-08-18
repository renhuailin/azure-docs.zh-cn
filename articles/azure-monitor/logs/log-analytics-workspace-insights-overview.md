---
title: Log Analytics 工作区见解
description: Log Analytics 工作区见解的概述 - 引入、使用情况、运行状况、代理等
services: azure-monitor
ms.topic: conceptual
author: noakup
ms.author: noakuper
ms.date: 05/06/2021
ms.openlocfilehash: 0d5fb976bfeb496e6c69e7df401ed0a756985475
ms.sourcegitcommit: 6ea4d4d1cfc913aef3927bef9e10b8443450e663
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2021
ms.locfileid: "113295877"
---
# <a name="log-analytics-workspace-insights-preview"></a>Log Analytics 工作区见解（洞察）

Log Analytics 工作区见解（预览版）可让你通过工作区使用情况、性能、运行状况、代理、查询和更改日志的统一视图对工作区进行全面的监视。 本文将帮助你了解如何加入和使用 Log Analytics 工作区见解（预览版）。

## <a name="overview-your-log-analytics-workspaces"></a>概览你的 Log Analytics 工作区

通过 Azure Monitor 见解访问 Log Analytics 工作区见解时，将显示“大规模”透视图。 在此处可以查看你的工作区在全球的分布情况，查看其保留期、上限和许可证详细信息（带颜色编码），并选择一个工作区以查看其见解。


:::image type="content" source="media/log-analytics-workspace-insights-overview/at-scale.png" alt-text="显示 Log Analytics 工作区见解中工作区列表的屏幕截图" lightbox="media/log-analytics-workspace-insights-overview/at-scale-expanded.png":::


若要大规模启动 Log Analytics 工作区见解，请执行以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com/)

2. 在 Azure 门户的左侧窗格中选择“监视”，然后在“见解中心”部分下选择“Log Analytics 工作区见解(预览版)” 。

## <a name="view-insights-for-a-log-analytics-workspace"></a>查看 Log Analytics 工作区的见解

在特定工作区的上下文中启动“见解”会显示工作区性能、使用情况、运行状况、代理、查询和更改日志的丰富数据和分析结果。


:::image type="content" source="media/log-analytics-workspace-insights-overview/at-resource.png" alt-text="显示 Log Analytics 工作区见解概述的屏幕截图" lightbox="media/log-analytics-workspace-insights-overview/at-resource.png":::

若要访问 Log Analytics 工作区见解，请执行以下操作：

1. 按前面所述从 Azure Monitor 打开 Log Analytics 工作区见解

2. 选择一个工作区以深入查看信息

或 

1. 在 Azure 门户中选择“Log Analytics 工作区”

2. 选择 Log Analytics 工作区

3. 在“工作区”菜单中选择“见解”（在“监视”下）

数据已在选项卡中进行组织，顶部的时间范围（默认为 24 小时）适用于所有选项卡。 某些图表和表使用不同的时间范围，其标题中会指出这一点。


### <a name="overview-tab"></a>概述选项卡

在“概述”选项卡上可以查看：

* 主要统计信息和设置
    - 工作区的每月引入量
    - 发送了检测信号的计算机数，即，连接到此工作区的计算机数（在所选时间范围内）
    - 过去一小时内未发送检测信号的计算机数（在所选时间范围内）
    - 设置的数据保留期
    - 设置的每日上限，以及最近一天已引入的数据量

* 引入量最大的 5 个表 – 用于分析在过去一个月内引入量最大的 5 个表的图表。
    - 在每个表中引入的数据量
    - 每个表中的每日引入量 - 用于直观显示高峰或低谷
    - 引入异常 - 列出在这些表中识别到的引入高峰和低谷


### <a name="usage-tab"></a>“使用情况”选项卡

#### <a name="usage-dashboard"></a>使用情况仪表板

此选项卡提供有关工作区使用情况的信息。 仪表板子选项卡按表显示引入数据，默认显示在所选时间范围内引入量最大的 5 个表（“概述”页中也显示了这几个表）。 可以通过“工作区表”下拉菜单选择要显示的表。 

:::image type="content" source="media/log-analytics-workspace-insights-overview/workspace-usage.png" alt-text="工作区使用情况选项卡的屏幕截图" lightbox="media/log-analytics-workspace-insights-overview/workspace-usage.png":::

* 主网格 - 可在此处查看按解决方案分组的表，以及有关每个表的信息
    - 在此表中引入的数据量（在所选时间范围内）
    - 此表在引入总量中所占的百分比（在所选时间范围内）。 这有助于识别对引入影响最大的表。 在以下屏幕截图中可以看到，仅仅是 AzureDiagnostics 和 ContainerLog 这两个表就占据了此工作区中引入的数据量的 2/3 (64%)。
    - 有关每个表的使用情况统计信息的上次更新时间 - 我们通常预期使用情况统计信息每小时会刷新一次。 由于刷新使用情况统计信息是一个重复性的服务内部操作，因此在刷新数据时产生的延迟只会进行标注，以便你知道如何正确解释数据。 你（作为用户）无需执行任何操作。
    - 可计费项 - 指示哪些表产生了费用，哪些表是免费的。

* 特定于表的详细信息

    在页面底部，可以查看有关在主网格中选择的表的详细信息。
    - 引入量 - 从每个资源引入到表中的数据量，以及这些数据在一段时间内的分布情况。 如果一个资源的引入量超过发送到此表的总引入量的 30%，那么该资源将标有警告符号，以便让你注意。
    - 引入延迟 - 引入操作花费的时间，根据发送到此表的请求的第 50、90 或 95 百分位进行分析。 此区域中引入量最大的表描述了请求的端到端总引入时间（根据所选的百分位）- 从事件发生时间开始，到事件被引入工作区为止。
    以下图表分别显示了代理的延迟（代理将日志发送到工作区所花费的时间）和管道的延迟（服务处理数据并将其推送到工作区所花费的时间）。
    :::image type="content" source="media/log-analytics-workspace-insights-overview/workspace-usage-ingestion-latency.png" alt-text="工作区使用情况 -“引入延迟”子选项卡的屏幕截图" lightbox="media/log-analytics-workspace-insights-overview/workspace-usage-ingestion-latency.png":::

#### <a name="additional-usage-queries"></a>其他使用情况查询

“其他查询”子选项卡公开跨所有工作区表运行的查询（而不是依赖于每小时刷新的使用情况元数据）。 由于这些查询的开销要高得多且效率低下，因此它们不会自动运行。 但是，它们可以显示有关哪些资源发送到工作区的日志最多（可能会影响计费）的有用信息。

:::image type="content" source="media/log-analytics-workspace-insights-overview/workspace-usage-additional-queries.png" alt-text="工作区使用情况 -“其他查询”选项卡的屏幕截图" lightbox="media/log-analytics-workspace-insights-overview/workspace-usage-additional-queries.png":::

一个这样的查询是“哪些 Azure 资源发送到此工作区的日志最多”（显示前 50 个）。
在我们的演示工作区中可以清楚地看到，有 3 个 Kuberbetes 群集发送的数据量远远超过所有其他资源总共发送的数据量，并且其中有一个特定的群集加载该工作区的次数最多。

:::image type="content" source="media/log-analytics-workspace-insights-overview/workspace-usage-additional-query-run.png" alt-text="工作区使用情况 -“其他查询”选项卡的屏幕截图，其中显示了其他某个查询的结果" lightbox="media/log-analytics-workspace-insights-overview/workspace-usage-additional-query-run.png":::


### <a name="health-tab"></a>“运行状况”选项卡

此选项卡显示工作区运行状况状态、该状态的上次报告时间，以及操作[错误和警告](../logs/monitor-workspace.md)（从 _LogOperation 表中检索）。 你可以在[此处](../logs/monitor-workspace.md#categories)找到有关所列问题和缓解步骤的更多详细信息。

:::image type="content" source="media/log-analytics-workspace-insights-overview/workspace-health.png" alt-text="工作区运行状况选项卡的屏幕截图" lightbox="media/log-analytics-workspace-insights-overview/workspace-health.png":::

### <a name="agents-tab"></a>“代理”选项卡

此选项卡提供有关向此工作区发送日志的代理的信息。
:::image type="content" source="media/log-analytics-workspace-insights-overview/workspace-agents.png" alt-text="工作区“代理”选项卡的屏幕截图" lightbox="media/log-analytics-workspace-insights-overview/workspace-agents.png":::

* 操作错误和警告 - 这些错误和警告专门与代理相关。 它们已按错误/警告标题分组以帮助你更清楚地了解可能发生的各种问题，但可以展开以显示其中提到的确切时间和资源。 另请注意，可以单击“在日志中运行查询”以通过“日志”体验查询 _LogOperation 表，然后查看原始数据并进一步进行分析。
* 工作区代理 - 这是在所选时间范围内向工作区发送了日志的代理。 可以看到代理的类型和运行状况状态。 标记为正常的代理不一定正常工作 - 此标记仅指示它们在过去一小时内发送了检测信号。 以下网格中详细说明了运行状况状态。
* 代理活动 - 此网格显示有关所有代理、正常代理或不正常代理的信息。 此处的“正常”仅指示相应代理在过去一小时内发送了检测信号。 为了更好地了解其状态，请查看网格中显示的趋势 - 其中显示了此代理在一段时间内发送的检测信号数。 只有在知道了受监视资源如何工作时才能推理出真正的运行状况状态，例如 - 如果用户在特定的时间有意关闭了计算机，则可以预期代理的检测信号会按照某种匹配模式间歇性出现。


### <a name="query-audit-tab"></a>“查询审核”选项卡

查询审核将创建有关在工作区中执行查询的日志。 如果启用此功能，则这些数据将非常有利于了解和改善查询性能、效率与负载。 若要在工作区上启用查询审核或详细了解此功能，请参阅 [Azure Monitor 日志中的审核查询](../logs/query-audit.md)。

#### <a name="performance"></a>性能 
此选项卡显示：
* 查询持续时间 - 一段时间内的第 95 百分位和第 50 百分位（中间值）持续时间，以毫秒为单位。
* 返回的行数 - 一段时间内的第 95 百分位和第 50 百分位（中间值）行计数。
* 处理的数据量 - 一段时间内在所有请求中处理的第 95 百分位、第 50 百分位数据量和数据总量。
* 响应代码 - 所选时间范围内响应代码在所有查询中的分布情况。

:::image type="content" source="media/log-analytics-workspace-insights-overview/workspace-query-audit-performance.png" alt-text="“查询审核”选项卡、“性能”子选项卡的屏幕截图" lightbox="media/log-analytics-workspace-insights-overview/workspace-query-audit-performance.png":::

#### <a name="slow-and-inefficient-queries"></a>慢速和低效查询 
此选项卡显示两个网格，可帮助你识别可能需要改进的慢速和低效查询。 不应在仪表板或警报中使用这些查询，因为它们会在工作区中造成不必要的长期负载。
* 资源最密集的查询 - CPU 需求量最高的 10 个 询，以及每个查询的处理数据量 (KB)、时间范围和文本。
* 最慢的查询 - 10 个最慢的查询，以及每个查询的时间范围和文本。

:::image type="content" source="media/log-analytics-workspace-insights-overview/workspace-query-audit-slow-queries.png" alt-text="“查询审核”选项卡、“慢速查询”子选项卡的屏幕截图" lightbox="media/log-analytics-workspace-insights-overview/workspace-query-audit-slow-queries.png":::

#### <a name="query-users"></a>查询用户 
此选项卡显示针对此工作区执行的用户活动：
* 按用户列出的查询 - 每个用户在所选时间范围内运行的查询数
* 受限制用户 - 运行受限制查询的用户（受限制的原因是过度查询工作区）

### <a name="change-log-tab"></a>“更改日志”选项卡

此选项卡显示过去 90 天内对工作区所做的配置更改（与所选时间范围无关），以及执行这些更改的用户。
它旨在帮助你监视谁更改了重要工作区设置（例如数据上限或工作区许可证）。

:::image type="content" source="media/log-analytics-workspace-insights-overview/workspace-change-log.png" alt-text="工作区“更改日志”选项卡的屏幕截图" lightbox="media/log-analytics-workspace-insights-overview/workspace-change-log.png":::


## <a name="next-steps"></a>后续步骤

查看[使用 Azure Monitor 工作簿创建交互式报表](../visualize/workbooks-overview.md)，了解工作簿旨在支持的方案、创作新报表和自定义现有报表的方式，以及更多信息。
