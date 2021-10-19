---
title: 使用 Azure Monitor 监视虚拟机：分析监视数据
description: 了解 Azure Monitor 的不同功能，你可以使用这些功能来分析虚拟机的运行状况和性能。
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/21/2021
ms.openlocfilehash: 06a37bc77d5eda453740881af9ca45ad4b8746e8
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129715169"
---
# <a name="monitor-virtual-machines-with-azure-monitor-analyze-monitoring-data"></a>使用 Azure Monitor 监视虚拟机：分析监视数据
本文是[在 Azure Monitor 中监视虚拟机及其工作负载](monitor-virtual-machine.md)方案的一部分。 文中介绍在完成配置后如何分析虚拟机的监视数据。

在虚拟机上启用 VM insights 后，就可以使用数据进行分析。 本文介绍 Azure Monitor 的不同功能，你可以使用这些功能来分析虚拟机的运行状况和性能。 其中有些功能可以根据要分析的是单台计算机还是多台计算机来提供不同的体验。 这里通过每项功能的独特行为来介绍每种体验，具体取决于所使用的体验。

> [!NOTE]
> 本文包含有关 Azure Monitor 和 VM insights 所收集数据的分析指导。 如需了解监视虚拟机上运行的工作负载时要配置的数据，请参阅[监视器工作负载](monitor-virtual-machine-workloads.md)。

## <a name="single-machine-experience"></a>单台计算机体验
在每个 Azure 虚拟机和启用 Azure Arc 的服务器上，从 Azure 门户菜单的“监视”部分访问单台计算机分析体验。 这些选项会限制你可以查看的该机算机的数据，或者至少会为此设置一个初始筛选器。 通过这种方式，你可以专注于特定的计算机，查看其当前性能及其在一段时间内的趋势，并帮助确定可能遇到的任何问题。

:::image type="content" source="media/monitor-virtual-machines/vm-menu.png" alt-text="屏幕截图：显示在 Azure 门户中分析虚拟机。" lightbox="media/monitor-virtual-machines/vm-menu.png":::

- **概述页面**：选择“监视”选项卡，以显示该虚拟主机的 [平台指标](../essentials/data-platform-metrics.md)。 你可以快速查看重要指标在不同时间段的趋势，如 CPU、网络和磁盘。 由于这些是主机指标，因此不包括来宾操作系统中的计数器（如内存）。 选择在[指标资源管理器](../essentials/metrics-getting-started.md)中处理此数据时要使用的图表，你可以在其中执行各种汇总，并添加更多的计数器进行分析。
- 活动日志：查看为当前虚拟机筛选的[活动日志](../essentials/activity-log.md#view-the-activity-log)条目。 使用此日志来查看计算机的最近活动，如任何配置更改以及停止和启动时间。 
- 见解：打开 [VM insights](../vm/vminsights-overview.md)，并选定当前虚拟机的映射图。 该映射图显示计算机上运行的进程、其他计算机上的依赖关系，以及外部进程。 有关如何将映射视图用于单台计算机的详细信息，请参阅[使用 VM insights 的映射功能了解应用程序组件](vminsights-maps.md#view-a-map-from-a-vm)。

    选择“性能”选项卡，以查看在不同时间段内关键性能计数器的趋势。 在虚拟机菜单中打开 VM insights 时，你还会看到一个表，其中包含每个磁盘的详细指标。 有关如何将映射视图用于单台计算机的详细信息，请参阅 [VM insights 的图表性能](vminsights-performance.md#view-performance-directly-from-an-azure-vm)。 

- 警报：查看当前虚拟机的[警报](../alerts/alerts-overview.md)。 这些警报仅将计算机用作目标资源，因此可能存在与之关联的其他警报。 若要查看所有资源的警报，可能需要使用 Azure Monitor 菜单中的“警报”选项。 有关详细信息，请参阅[使用 Azure Monitor 监视虚拟机：警报](monitor-virtual-machine-alerts.md)。
- 指标：打开指标资源管理器，并将范围设置为该计算机。 此选项与从“概述”页面选择其中一个性能图表时相同，只不过尚未添加指标。
- 诊断设置：为当前虚拟机启用和配置[诊断扩展](../agents/diagnostics-extension-overview.md)。 此选项不同于其他 Azure 资源的“诊断设置”选项。 如果需要将数据发送到 Azure 事件中心或 Azure 存储，请仅启用诊断扩展。
- 顾问建议：查阅 [Azure 顾问](../../advisor/index.yml)就当前虚拟机提供的建议。
- 日志：打开[日志分析](../logs/log-analytics-overview.md)，并将[范围](../logs/scope.md)设置为当前虚拟机。 你可以从各种现有查询中选择，以便仅针对此计算机深化日志和性能数据。 
- 连接监视器：打开[网络观察程序连接监视器](../../network-watcher/connection-monitor-overview.md)，以监视当前虚拟机和其他虚拟机之间的连接。 
- 工作簿：打开工作簿库，其中包含用于单台计算机的 VM insights 工作簿。 有关为单台计算机设计的 VM insights 工作簿列表，请参阅 [VM insights 工作簿](vminsights-workbooks.md#vm-insights-workbooks)。

## <a name="multiple-machine-experience"></a>多台计算机体验
在每个 Azure 虚拟机和启用 Azure Arc 的服务器上，从 Azure 门户的“监视”菜单访问多台计算机分析体验。 这些选项提供对所有数据的访问权限，以便你可以选择要进行比较的虚拟机。

:::image type="content" source="media/monitor-virtual-machines/monitor-menu.png" alt-text="屏幕截图：显示在 Azure 门户中分析多台虚拟机。" lightbox="media/monitor-virtual-machines/monitor-menu.png":::

- 活动日志：查看为所有资源筛选的[活动日志](../essentials/activity-log.md#view-the-activity-log)条目。 为虚拟机或虚拟机规模集的“资源类型”创建筛选器，以便查看所有计算机的事件。
- 警报：查看所有资源的[警报](../alerts/alerts-overview.md)，包括与虚拟机相关但与工作区关联的警报。 为虚拟机或虚拟机规模集的“资源类型”创建筛选器，以便查看所有计算机的警报。 
- 指标：打开[指标资源管理器](../essentials/metrics-getting-started.md)，并且未选择任何范围。 如果要在多台计算机之间比较趋势，此功能特别有用。 选择订阅或资源组，以快速添加一组要一起分析的计算机。
- 日志：打开[日志分析](../logs/log-analytics-overview.md)，并将[范围](../logs/scope.md)设置为此工作区。 你可以从各种现有查询中选择，以便针对所有计算机深化日志和性能数据。 也可以创建自定义查询来执行其他分析。
- 工作簿：打开工作簿库，其中包含用于多台计算机的 VM insights 工作簿。 有关为多台计算机设计的 VM insights 工作簿列表，请参阅 [VM insights 工作簿](vminsights-workbooks.md#vm-insights-workbooks)。 
- 虚拟机：打开 [VM insights](../vm/vminsights-overview.md)，并打开“开始”选项卡。 此操作会示 Azure 订阅中的所有计算机，并标识正在监视的计算机。 使用此视图加入目前尚未监视的单个计算机。

    选择“性能”选项卡，比较不同时间段内多台计算机的关键性能计数器趋势。 选择视图中要包含的订阅或资源组中的所有计算机。 有关如何将映射视图用于单台计算机的详细信息，请参阅 [VM insights 的图表性能](vminsights-performance.md#view-performance-directly-from-an-azure-vm)。

    选择“映射”选项卡以查看计算机上正在运行的进程、计算机之间的依赖关系以及外部进程。 选择订阅或资源组中的所有计算机，或检查单个计算机的数据。 有关如何将映射视图用于多台计算机的详细信息，请参阅[使用 VM insights 的映射功能了解应用程序组件](vminsights-maps.md#view-a-map-from-azure-monitor)。 
 
## <a name="compare-metrics-and-logs"></a>比较指标和日志
对于 Azure Monitor 的许多功能，你无需了解它使用的各类数据及其存储位置。 例如，你可以使用 VM insights，但无需了解要用哪些数据填充性能视图、映射视图和工作簿。 你只需关注要分析的逻辑。 深入挖掘数据时，你需要了解[指标](../essentials/data-platform-metrics.md)和[日志](../logs/data-platform-logs.md)之间的差异。 Azure Monitor 的不同功能会使用不同种类的数据。 用于特定方案的警报类型取决于是否在特定位置提供该数据。

如果你不熟悉 Azure Monitor，这种程度的详细信息可能会造成混淆。 以下信息可帮助你了解数据类型之间的差异：

- 所有非数值数据（如事件）都存储在日志中。 指标只能包含固定时间间隔的取样数值数据。
- 数值数据可以同时存储在指标和日志中，以便使用不同的分析方式，并支持不同类型的警报。
- 通过使用 Log Analytics 代理，VM insights 将来宾操作系统的性能数据发送到日志。
- Azure Monitor 代理将来宾操作系统的性能数据发送到指标。

> [!NOTE]
> Azure Monitor 代理会将数据同时发送到指标和日志。 在此方案中，它仅用于指标，因为 Log Analytics 代理会按当前 VM insights 的要求将数据发送到日志。 当 VM insights 使用 Azure Monitor 代理时，我们将更新此方案以删除 Log Analytics 代理。

## <a name="analyze-data-with-vm-insights"></a>用 VM insights 分析数据
VM insights 包含多个性能图表，可帮助你快速获取受监视计算机的操作状态、随时间推移的趋势性能以及计算机与进程之间的依赖关系。 此外，还提供任何受监视计算机不同方面的合并视图，如 Log Analytics 工作区中收集的属性和事件。

“开始”选项卡显示 Azure 订阅中的所有计算机，并标识哪些计算机正在被监视。 使用此视图可快速确定哪些计算机不受监视，并将尚未监视的单个计算机加入其中。

:::image type="content" source="media/monitor-virtual-machines/vminsights-get-started.png" alt-text="屏幕截图：显示 VM insights 启动。" lightbox="media/monitor-virtual-machines/vminsights-get-started.png":::

“性能”视图包括多个包含若干关键绩效指标 (KPI) 的图表，有助你确定计算机的运行状况。 这些图表显示一段时间内的资源利用率。 你可以通过这些图表识别瓶颈和异常，或切换到列出每个计算机的透视图，以根据所选指标查看资源利用率。 有关如何使用性能视图的详细信息，请参阅 [VM insights 的性能图表](vminsights-performance.md)。

:::image type="content" source="media/monitor-virtual-machines/vminsights-performance.png" alt-text="屏幕截图：显示 VM insights 的性能。" lightbox="media/monitor-virtual-machines/vminsights-performance.png":::

使用“映射”视图可以查看计算机上运行的进程及其在其他计算机上的依赖关系，以及外部进程。 你可以更改视图的时间窗口，以确定这些依赖关系是否在一段时间后发生改变。 有关如何使用映射视图的详细信息，请参阅[使用 VM insights 的映射功能了解应用程序组件](vminsights-maps.md)。

:::image type="content" source="media/monitor-virtual-machines/vminsights-map.png" alt-text="屏幕截图：显示 VM insights 的映射。" lightbox="media/monitor-virtual-machines/vminsights-map.png":::

## <a name="analyze-metric-data-with-metrics-explorer"></a>使用指标资源管理器分析指标数据
使用指标资源管理器，可以绘制图表、直观地关联趋势，并调查指标中的峰值和谷值。 有关此工具用法的详细信息，请参阅 [Azure 指标资源管理器入门](../essentials/metrics-getting-started.md)。 

虚拟机使用三个命名空间。

| 命名空间 | 说明 | 要求 |
|:---|:---|:---|
| 虚拟主机 | 为所有 Azure 虚拟机自动收集的主机指标。 有关详细指标列表，请参见 [Microsoft.Compute/virtualMachines](../essentials/metrics-supported.md#microsoftcomputevirtualmachines)。 | 自动收集，无需任何配置。 |
| 来宾(经典) | 一组有限的来宾操作系统和应用程序性能数据。 在指标资源管理器中可用，但不能用于 Azure Monitor 的其他功能（如指标警报）。  | 已安装[诊断扩展](../agents/diagnostics-extension-overview.md)。 从 Azure 存储读取数据。  |
| 虚拟机来宾 | 使用指标的所有 Azure Monitor 功能都可以使用来宾操作系统和应用程序性能数据。 | 安装 [Azure Monitor 代理](../agents/azure-monitor-agent-overview.md)和[数据收集规则](../agents/data-collection-rule-overview.md)。 |

## <a name="analyze-log-data-with-log-analytics"></a>使用 Log Analytics 分析日志数据
通过使用 Log Analytics，可以对日志数据执行自定义分析。 若要深入发掘在 VM insights 中创建视图时使用的数据，请使用 Log Analytics。 你可能想要分析这些数据的不同逻辑和聚合，将 Azure 安全中心和 Azure Sentinel 收集的安全数据与自己的运行状况和可用性数据相关联，或者使用为[工作负载](monitor-virtual-machine-workloads.md)收集的数据。

无需了解如何编写日志查询就可以使用 Log Analytics。 你可以选择多个预构建的查询，这些查询无需修改即可运行，也可以用来自定义查询。 在 Log Analytics 屏幕顶部选择“查询”，并查看“资源类型”为“虚拟机”或“虚拟机规模集”的查询。 有关如何使用这些查询的信息，请参阅[在 Azure Monitor Log Analytics 中使用查询](../logs/queries.md)。 有关如何使用 Log Analytics 运行查询并处理其结果的教程，请参阅 [Log Analytics 教程](../logs/log-analytics-tutorial.md)。

:::image type="content" source="media/monitor-virtual-machines/vm-queries.png" alt-text="屏幕截图：显示虚拟机查询。" lightbox="media/monitor-virtual-machines/vm-queries.png":::

通过使用“性能”或“映射”视图中的属性窗格，从 VM insights 启动 Log Analytics 时，它会列出包含所选计算机数据的表格。 选择一个表格，使用一个简单的查询来打开 Log Analytics，该查询将返回表格中所选计算机的所有记录。 处理这些结果或修改查询以进行更复杂的分析。 将[范围](../log/../logs/scope.md)设置为“工作区”意味着你可以访问使用该工作区的所有计算机的数据。 

:::image type="content" source="media/monitor-virtual-machines/table-query.png" alt-text="屏幕截图：显示表格查询。" lightbox="media/monitor-virtual-machines/table-query.png":::

## <a name="visualize-data-with-workbooks"></a>通过工作簿实现数据可视化
[工作簿](../visualize/workbooks-overview.MD)在 Azure 门户中提供交互式报表，并将不同类型的数据合并到一个视图中。 工作簿可将文本、 [日志查询](/azure/data-explorer/kusto/query/)、指标和参数合并到丰富的交互式报告中。 有权访问相同 Azure 资源的其他团队成员都可编辑工作簿。

在如下所述的场景中，工作簿非常有用：

* 在未提前了解相关指标（例如 CPU 利用率、磁盘空间、内存和网络依赖项）的情况下探索虚拟机的使用情况。 与其他使用情况分析工具不同，工作簿可以结合多个类型的可视化效果和分析，非常适合这种自由探索。
* 通过显示关键计数器的指标和其他日志事件，向团队解释最近预配的 VM 的性能如何。
* 与团队的其他成员分享调整 VM 试验规模的结果。 你可以用文本解释试验的目标， 然后显示用于评估试验的每个使用情况指标和分析查询，并提供清晰的标注，说明每个指标是高于还是低于目标。
* 结合数据、文本说明和后续步骤讨论，报告故障对 VM 使用的影响，从而防止未来发生故障。

VM insights 包含以下工作簿。 你可以使用这些工作簿，也可以由此开始创建自定义工作簿，以满足自己的特定要求。

### <a name="single-virtual-machine"></a>单个虚拟机

| 工作簿 | 说明 |
|----------|-------------|
| 性能 | 提供可自定义的性能视图，并在视图中使用所有已启用的 Log Analytics 性能计数器。 | 
| 连接 | 提供关于 VM 的入站和出站连接的深入视图。 | 

### <a name="multiple-virtual-machines"></a>多台虚拟机

| 工作簿 | 说明 |
|----------|-------------|
| 性能 | 在单个工作簿中提供可自定义的“前 N 项列表”和“图表”视图，并在视图中使用所有已启用的 Log Analytics 性能计数器。|
| 性能计数器 | 提供基于众多性能计数器集的“前 N 项图表”视图。 |
| 连接 | 提供关于受监视计算机的入站和出站连接的深入视图。 |
| 活动端口 | 提供已绑定到受监视计算机上端口的进程列表，及其在所选时间范围内的活动。 |
| 打开端口 | 提供受监视计算机上打开的端口数，以及这些打开端口的详细信息。 |
| 失败的连接数 | 显示受监视计算机上的失败连接计数、失败趋势，以及失败百分比是否不断增大。 |
| 安全和审核 | 提供 TCP/IP 流量分析，其中报告连接总数、恶意连接数，以及 IP 终结点的全球位置。 若要启用所有功能，需要启用“安全性检测”。 |
| TCP 流量 | 提供受监视计算机的排名报告，在网格中以趋势线的形式显示它们已发送、接收的网络流量和总网络流量。 |
| 流量比较 | 比较一台计算机或一组计算机的网络流量趋势。 |
| Log Analytics 代理 | 分析代理的运行状况，包括连接到不正常工作区的代理数，以及代理对计算机性能的影响。 与其他工作簿不同，此工作簿不可从 VM insights 获取。 在 Azure Monitor 菜单上，转到“工作簿”，然后选择“公共模板”。 |

有关如何创建自定义工作簿的说明，请参阅[使用工作簿创建交互式报表 VM insights](vminsights-workbooks.md)。

:::image type="content" source="media/monitor-virtual-machines/workbook-example.png" alt-text="屏幕截图：显示虚拟机的工作簿。" lightbox="media/monitor-virtual-machines/workbook-example.png":::

## <a name="next-steps"></a>后续步骤

* [根据收集的数据创建警报](monitor-virtual-machine-alerts.md)
* [监视虚拟机上运行的工作负载](monitor-virtual-machine-workloads.md)
