---
title: Azure Monitor 中的指标 | Microsoft Docs
description: 了解 Azure Monitor 中的指标，即能够支持准实时方案的轻型监视数据。
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2021
ms.author: bwren
ms.openlocfilehash: 3986e59965842166ce675cd778d04984896d671d
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129458522"
---
# <a name="azure-monitor-metrics-overview"></a>Azure Monitor 指标概述
Azure Monitor 指标是 Azure Monitor 的一项功能，它将[受监视的资源](../monitor-reference.md)中的数值数据收集到时序数据库。 指标是定期收集的数值，用于描述系统在某一特定时间的某些情况。 

Azure Monitor 中的指标是轻型指标，能够支持准实时方案，这使得它们有助于发出问题警报和快速检测问题。 你可以使用指标资源管理器以交互方式分析它们，在值超过阈值时主动接收警报通知，或者在工作簿或仪表板中将其可视化。

> [!NOTE]
> Azure Monitor 指标是支持 Azure Monitor 的数据平台的一半。 另一半是 [Azure Monitor 日志](../logs/data-platform-logs.md)，它收集和组织日志和性能数据，并允许使用丰富的查询语言分析那些数据。 
>
> 指标功能只能将数值数据存储在特定结构中，而日志功能可以存储各种数据类型（每个数据类型都有自己的结构）。 还可使用日志查询对日志数据执行复杂的分析，只是无法分析指标数据。

## <a name="what-can-you-do-with-azure-monitor-metrics"></a>Azure Monitor 指标有何用途？
下表列出了在 Azure Monitor 中指标功能的使用方式。

|  | 说明 |
|:---|:---|
| **分析** | 使用[指标资源管理器](metrics-charts.md)分析图表上收集的指标，并比较来自各种资源的指标。 |
| **Alert** | 配置[指标警报规则](../alerts/alerts-metric.md)，以在指标值超过阈值时发送通知或执行[自动化操作](../alerts/action-groups.md)。 |
| **可视化** | 将指标资源管理器中的图表固定到 [Azure 仪表板](../app/tutorial-app-dashboards.md)。<br>创建一个[工作簿](../visualize/workbooks-overview.md)以与交互式报表中的多组数据合并。 将查询结果导出到 [Grafana](../visualize/grafana-plugin.md) 以使用其仪表板并与其他数据源合并。 |
| **自动化** |  使用[自动缩放](../autoscale/autoscale-overview.md)并根据超过阈值的指标值来增加或减少资源。 |
| **检索** | 使用 [PowerShell cmdlet](/powershell/module/az.monitor) 通过命令行来访问指标值。<br>使用 [REST API](./rest-api-walkthrough.md) 通过自定义应用程序来访问指标值。<br>使用 [Azure CLI](/cli/azure/monitor/metrics) 通过命令行来访问指标值。 |
| **导出** | [将指标路由到日志](./resource-logs.md#send-to-azure-storage)，以将 Azure Monitor 指标中的数据与 Azure Monitor 日志中的数据一起进行分析，并将指标值存储超过 93 天。<br>将指标流式传输到[事件中心](./stream-monitoring-data-event-hubs.md)，以路由到外部系统。 |
| **存档** | 出于符合性、审核或脱机报告目的，对资源的性能或运行状况历史记录进行 [存档](./platform-logs-overview.md)。 |

![显示指标的源和用途的图表。](media/data-platform-metrics/metrics-overview.png)

## <a name="data-collection"></a>数据收集
Azure Monitor 收集来自以下源的指标。 在 Azure Monitor 指标数据库中收集这些指标后，就可以一起评估它们，而不管它们的数据源是什么。

- **Azure 资源**。 平台指标由 Azure 资源创建，可用于洞察这些资源的运行状况和性能。 每种资源创建[一组非重复性指标](./metrics-supported.md)，无需进行任何配置。 平台指标每分钟从 Azure 资源中收集一次，除非在指标的定义中另有规定。 

- **应用程序**。 Application Insights 为受监视的应用程序创建指标，以帮助检测性能问题，以及跟踪应用程序的用途趋势。 值包括服务器响应时间和浏览器异常 。

- **虚拟机代理**。 指标从虚拟机的来宾操作系统收集。 可使用 [Windows 诊断扩展](../agents/diagnostics-extension-overview.md)为 Windows 虚拟机启用来宾 OS 指标，并使用 [InfluxData Telegraf 代理](https://www.influxdata.com/time-series-platform/telegraf/)为 Linux 虚拟机启用来宾 OS 指标。

- 自定义指标。 可以定义除了自动可用的标准指标之外的指标。 可在 Application Insights 监视的[应用程序中定义自定义指标](../app/api-custom-events-metrics.md)，也可使用[自定义指标 API](./metrics-store-custom-rest-api.md) 为 Azure 服务创建自定义指标。

有关可向 Azure Monitor 指标发送数据的完整数据源列表，请参阅 [Azure Monitor 监视哪些内容？](../monitor-reference.md)。

## <a name="metrics-explorer"></a>指标资源管理器
使用[指标资源管理器](metrics-charts.md)以交互方式分析指标数据库中的数据，并绘制随时间变化的多个指标的值图表。 可以将图表固定到仪表板，以便使用其他可视化效果来查看它们。 还可以使用 [Azure 监视 REST API](./rest-api-walkthrough.md) 检索指标。

![指标资源管理器中显示服务器请求、服务器响应时间和失败请求的示例图的屏幕截图。](media/data-platform-metrics/metrics-explorer.png)

有关详细信息，请参阅 [Azure Monitor 指标资源管理器入门指南](./metrics-getting-started.md)。

## <a name="data-structure"></a>数据结构
Azure Monitor 指标收集的数据存储在为分析带有时间戳的数据而进行优化的时序数据库中。 每组指标值都是具有以下属性的时序：

* 收集值的时间。
* 与值关联的资源。
* 充当指标类别的命名空间。
* 指标名称。
* 值本身。
* 存在[多个维度](#multi-dimensional-metrics)时。 请注意，自定义指标限制为 10 个维度。

## <a name="multi-dimensional-metrics"></a>多维指标
指标数据面临的一大挑战是，通常只有有限的信息来为收集的值提供上下文。 Azure Monitor 通过多维指标解决了这一挑战。 

指标维度是携带附加数据来描述指标值的名称/值对。 例如，名为“可用磁盘空间”的指标的维度可能名为“驱动器”，其值为 C: 和 D:   。 该维度将允许查看所有驱动器的可用磁盘空间或单独查看每个驱动器的可用磁盘空间。

以下示例演示了名为“网络吞吐量”的假设指标的两个数据集。 第一个数据集不包含维度。 第二个数据集使用两个维度（IP 和方向）显示值 。

### <a name="network-throughput"></a>网络吞吐量

| 时间戳     | 指标值 |
| ------------- |:-------------|
| 8/9/2017 8:14 | 1,331.8 Kbps |
| 8/9/2017 8:15 | 1,141.4 Kbps |
| 8/9/2017 8:16 | 1,110.2 Kbps |

上述不包含维度的指标只能回答类似“在某个给定时间我的网络吞吐量是多少？”的基本问题

### <a name="network-throughput-and-two-dimensions-ip-and-direction"></a>网络吞吐量和两个维度（“IP”和“方向”）

| 时间戳     | “IP”维度   | “方向”维度 | 指标值|
| ------------- |:-----------------|:------------------- |:-----------|
| 8/9/2017 8:14 | IP="192.168.5.2" | Direction="Send"    | 646.5 Kbps |
| 8/9/2017 8:14 | IP="192.168.5.2" | Direction="Receive" | 420.1 Kbps |
| 8/9/2017 8:14 | IP="10.24.2.15"  | Direction="Send"    | 150.0 Kbps |
| 8/9/2017 8:14 | IP="10.24.2.15"  | Direction="Receive" | 115.2 Kbps |
| 8/9/2017 8:15 | IP="192.168.5.2" | Direction="Send"    | 515.2 Kbps |
| 8/9/2017 8:15 | IP="192.168.5.2" | Direction="Receive" | 371.1 Kbps |
| 8/9/2017 8:15 | IP="10.24.2.15"  | Direction="Send"    | 155.0 Kbps |
| 8/9/2017 8:15 | IP="10.24.2.15"  | Direction="Receive" | 100.1 Kbps |

此指标可以回答类似“每个 IP 地址的网络吞吐量是多少？” 以及“相对于收到的数据，发送的数据有多少？”的问题 与不包含维度的指标相比，多维指标具有更多分析值和诊断值。

### <a name="viewing-multi-dimensional-performance-counter-metrics-in-metrics-explorer"></a>在指标资源管理器中查看多维性能计数器指标 
不能通过经典来宾指标 API 将包含星号 (\*) 的性能计数器指标发送到 Azure Monitor。 此 API 无法显示包含星号的指标，因为它是一个多维指标，而经典指标不支持多维指标。

要使用 Azure 诊断扩展配置和查看多维来宾 OS 性能计数器指标：

1. 转到虚拟机的“诊断设置”页。
2. 选择“性能计数器”选项卡。 
3. 选择“自定义”以配置要收集的性能计数器。

   ![诊断设置页的性能计数器部分的屏幕截图。](media/data-platform-metrics/azure-monitor-perf-counter.png)

4. 选择“接收器”。 然后，选择“启用”将数据发送到 Azure Monitor。

   ![诊断设置页的接收器部分的屏幕截图。](media/data-platform-metrics/azure-monitor-sink.png)

5. 要查看 Azure Monitor 中的指标，请在指标命名空间下拉列表中选择“虚拟机来宾” 。

   ![指标命名空间的屏幕截图。](media/data-platform-metrics/vm-guest-namespace.png)

6. 选择“应用拆分”并填写详细信息，以按实例拆分指标。 然后可查看按每个可能的值（由配置中的星号表示）划分的指标。 在本示例中，星号表示逻辑磁盘的数量以及总和。

   ![按实例拆分指标的屏幕截图。](media/data-platform-metrics/split-by-instance.png)

## <a name="retention-of-metrics"></a>指标保留期
对于 Azure 中的大多数资源，平台指标的存储时间为 93 天。 有一些例外情况：

- 经典来宾 OS 指标：这些是由 [Windows 诊断扩展](../agents/diagnostics-extension-overview.md)或 [Linux 诊断扩展](../../virtual-machines/extensions/diagnostics-linux.md)收集并路由到 Azure 存储帐户的性能计数器。 保证这些指标的保留期至少为 14 天，但不会将到期日期写入存储帐户。 
  
  出于性能原因，门户会根据卷限制显示的数据量。 因此，如果写入的数据量不大，则门户检索到的实际天数可能会超过 14 天。 

- 发送到 Azure Monitor 指标的来宾 OS 指标：这些是由 [Windows 诊断扩展](../agents/diagnostics-extension-overview.md)收集并通过数据收集规则发送到 [Azure Monitor 数据接收器](../agents/diagnostics-extension-overview.md#data-destinations)或 Linux 计算机上的 [InfluxData Telegraf 代理](https://www.influxdata.com/time-series-platform/telegraf/)或较新的 [Azure Monitor 代理](../agents/azure-monitor-agent-overview.md)的性能计数器。 这些指标的保留期为 93 天。

- Log Analytics 代理收集的来宾 OS 指标：这些是 Log Analytics 代理收集并发送到 Log Analytics 工作区的性能计数器。 这些指标的保留期为 31 天，最多可延长到 2 年。

- **基于 Application Insights 日志的指标**。 在后台，[基于日志的指标](../app/pre-aggregated-metrics-log-metrics.md)转换为日志查询。 它们的保留期可变，并与基础日志中的事件保留期一致（31 天到两年）。 对于 Application Insights 资源，日志的存储时间为 90 天。

> [!NOTE]
> 可[将 Azure Monitor 资源的平台指标发送到 Log Analytics 工作区](./resource-logs.md#send-to-azure-storage)，以了解长期趋势。

> [!NOTE]
> 如上所述，对于 Azure 中的大多数资源，平台指标存储 93 天。 但是，只能在任何单个图表的“指标”磁贴中查询最多 30 天的数据。 此限制不适用于基于日志的指标。 
>
> 如果显示空白图表，或图表仅显示部分指标数据，请验证时间选取器中开始日期和结束日期之间的间隔是否不超过 30 天。 选择 30 天间隔后，可以[平移](./metrics-charts.md#pan)图表以查看完整的保留期窗口。

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure Monitor 数据平台](../data-platform.md)。
- 了解 [Azure Monitor 中的日志数据](../logs/data-platform-logs.md)。
- 了解适用于 Azure 中各种资源的[监视数据](../agents/data-sources.md)。