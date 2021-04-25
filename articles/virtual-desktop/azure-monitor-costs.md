---
title: 监视 Windows 虚拟桌面成本定价估算 — Azure
description: 如何评估使用 Windows 虚拟桌面 Azure Monitor 的成本和定价。
author: Heidilohr
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 5bd89a734a20c913bacca1f5531aa76d76418c80
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448213"
---
# <a name="estimate-azure-monitor-costs"></a>估计 Azure Monitor 成本

Azure Monitor 日志是收集、索引和存储环境生成的数据的服务。 因此，Azure Monitor 的定价模型基于每日 Log Analytics 工作区投入并处理（或引入）的数据量（以 GB 为单位）。 Log Analytics 工作区的成本不仅取决于收集的数据量，还取决于所选的 Azure 付款计划，以及你选择用于存储环境生成数据的时间。

本文将通过介绍以下内容帮助你了解 Azure Monitor 的定价方式：

- 在启用此功能之前，如何提前估计数据引入和存储成本
- 如何测量和控制引入和存储，以便在使用此功能时降低成本

>[!NOTE]
> 本文列出的所有规模和定价都只是用于说明估算工作方式的示例。 若要求基于 Azure Monitor Log Analytics 定价模型和 Azure 区域的更准确的评估，请参阅 [Azure Monitor 价格](https://azure.microsoft.com/pricing/details/monitor/)。

## <a name="estimate-data-ingestion-and-storage-costs"></a>估计数据引入和存储成本

建议在 Log Analytics 工作区中使用以日志形式编写的预定义数据集。 在下面的示例中，我们将以默认配置查看计费数据

Windows 虚拟桌面 Azure Monitor 的预定义数据集包括：

- 会话主机的性能计数器
- 会话主机的 Windows 事件日志
- 服务基础结构中的 Windows 虚拟桌面诊断

数据引入和存储成本取决于你的环境规模、健康状况和使用情况。 本文所使用的示例，建立在正常运行的虚拟机从轻到强运行的基础之上，基于[虚拟机大小调整准则](/remote/remote-desktop-services/virtual-machine-recs)计算所需的成本范围，以计算一系列数据引入和存储成本。

我们将在本示例中使用的轻运行虚拟机包含以下组件：

- 4 个 vCPU，1 个磁盘
- 每天 16 个会话
- 平均会话持续时间为 2 小时（120 分钟）
- 每个会话的进程数为 100

我们在本示例中使用的强运行虚拟机包含以下组件：

- 6个 vCPU，1 个磁盘
- 每日 6 个会话
- 平均会话持续时间为 4 小时（240 分钟）
- 每个会话的进程数为 200

## <a name="estimating-performance-counter-ingestion"></a>估计性能计数器的引入

性能计数器显示系统资源的执行情况。 性能计数器的数据引入取决于你的环境规模和使用情况。 在大多数情况下，性能计数器将占到 Windows 虚拟桌面 Azure Monitor 数据引入的 80-99%。

开始估计之前，请知悉每个性能计数器都按特定频率发送数据。 我们将采样率（每分钟）设置为默认（你还可以在设置中编辑此速率），但会根据计数器的不同会以不同的乘法因子应用该速率。 以下因素会影响速率：

- 对于每个虚拟机 (VM)，每个计数器将在 VM 运行时以默认采样率（每分钟）向环境中的每个 VM 发送数据。 可以通过将默认的每分钟采样乘以环境中的 VM 数，然后将该数字与每日的平均 VM 运行时间相乘，来估算这些计数器每天发送的记录数。

   总结：

   默认的每分钟采样率×VM SKU 中的 CPU 内核数×VM 个数×每日平均 VM 运行时间=每天发送的记录数

- 对于每个 CPU，每个计数器都按 VM 运行时每分钟默认的采样率向环境中的每个 VM 中的每个 vCPU 发送记录。 你可以通过将每分钟的默认采样率乘以 VM SKU 中的 CPU 内核数，然后将该数乘以 VM 运行的分钟数和环境中的 VM 数，来估计计数器每天发送的记录数。

   总结：
   
   默认的每分钟采样率×VM SKU 中的 CPU 内核数×VM 运行分钟数×VM 个数=每天发送的记录数

- 对于每个磁盘，每个计数器都按默认的每分钟采样率向环境中的每个 VM 的每个磁盘发送数据。 这些计数器每天发送的记录数等于默认的每分钟采样率乘以 VM SKU 中的磁盘数，乘以每小时 60 分钟，最后乘以 VM 的平均活动小时数。

   总结：

   默认的每分钟采样率×VM SKU 中的磁盘数×每小时 60 分钟×VM 个数×每日平均 VM 运行时间=每天发送的记录数

- 对于每个会话，每个计数器在会话连接时，按默认的每分钟采样率向环境中的每个会话发送数据。 你可以通过将每分钟的默认采样率乘以每日平均会话数和平均会话持续时间，来估计这些计数器每天发送的记录数。

   总结：

   默认的每分钟采样率×每日会话数×平均会话持续时间=每天发送的记录数

- 对于每个进程，每个计数器都按默认的每分钟采样率向环境中的每个会话的每个进程发送数据。 你可以通过将默认的每分钟采样率乘以每分钟平均会话数，然后乘以平均会话持续时间数和每个会话的平均进程数，来估计这些计数器将每天发送的记录数。
  
   总结：

   默认的每分钟采样率×每日会话数×平均会话持续时间×每个会话的平均进程数=每天发送的记录数

下表列出了收集到的 20 个适用于 Windows 虚拟桌面 Azure Monitor 的性能计数器及其默认采样率：

| 计数器名称 | 默认采样速率 | 频率系数 |
|--------------|---------------------|------------------|
| 逻辑磁盘 (C:)\\% 可用空间 | 60 秒  | 每磁盘             |
| 逻辑磁盘 (C:)\\ 平均磁盘队列长度   | 30 秒    | 每磁盘             |
| 逻辑磁盘 (C:)\\ 平均磁盘秒/传输  | 60 秒       | 每磁盘             |
| 逻辑磁盘 (C:)\\ 当前磁盘队列长度  | 30 秒      | 每磁盘             |
| Memory(\*)\\Available Mbytes | 30 秒    | 每个 VM  |
| 内存 (\*)\\ 页面错误/秒 | 30 秒   | 每个 VM  |
| 内存 (\*)\\ 页/秒 | 30 秒   | 每个 VM  |
| 内存 (\*)\\% 使用中的已提交字节数 | 30 秒    | 每个 VM  |
| 物理磁盘 (\*)\\ 平均磁盘队列长度 | 30 秒      | 每磁盘             |
| 物理磁盘 (\*)\\ 平均磁盘秒/读取 | 30 秒  | 每磁盘             |
| 物理磁盘 (\*)\\ 平均磁盘秒/传输 | 30 秒  | 每磁盘             |
| 物理磁盘 (\*)\\ 平均磁盘秒/写入 | 30 秒 | 每磁盘             |
| 处理器信息 (_总计)\\% 处理器时间 | 30 秒 | 每核/CPU         |
| 终端服务 (\*)\\ 活动会话          | 60 秒 | 每个 VM  |
| 终端服务 (\*)\\ 非活动会话        | 60 秒 | 每个 VM  |
| 终端服务 (\*)\\ 会话总数 | 60 秒 | 每个 VM  |
| 每进程用户输入延迟 (\*)\\ 最大输入延迟         | 30 秒 | 每进程          |
| User Input Delay per Session(\*)\\Max Input Delay        | 30 秒 | 每个会话          |
| RemoteFX 网络 (\*)\\ 当前 TCP RTT | 30 秒 | 每个 VM  |
| RemoteFX 网络 (\*)\\ 当前 UDP 带宽     | 30 秒 | 每个 VM  |

如果我们将每个记录大小估算为 200 字节，则在轻工作负荷下运行的示例 VM 在默认采样率下，每天每个 VM 将发送大约 90 MB 的性能计数器数据。 同时，在强工作负荷下运行的示例 VM，每天每个 VM 将发送大约 130 MB 的性能计数器数据。 不过，记录大小和环境使用情况可能会有所不同，因此，你的部署使用的每日 MB 数可能也会有所不同。

若要了解有关输入延迟性能计数器的详细信息，请参阅[用户输入延迟性能计数器](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/)。

## <a name="estimating-windows-event-log-ingestion"></a>估算 Windows 事件日志引入

Windows 事件日志是在 Windows 虚拟机上的 Log Analytics 代理收集的数据源。 除了由需要监视的应用程序创建的自定义日志，还可以从标准日志，如“系统”和“应用程序”中收集事件。

下面是适用于 Windows 虚拟桌面 Azure Monitor 的默认 Windows 事件：

- 应用程序
- Microsoft-Windows-TerminalServices-RemoteConnectionManager/Admin
- Microsoft-Windows-TerminalServices-LocalSessionManager/Operational
- 系统
- Microsoft-FSLogix-Apps/Operational
- Microsoft-FSLogix-Apps/Admin

只要在环境中满足事件的条款，就会发送 Windows 事件。 处于正常状态的计算机将发送比处于不正常状态的计算机更少的事件。 由于事件计数是不可预测的，因此，我们将根据此估算的健康环境中的示例，让每天每个 VM 使用 1,000-10,000 个事件。 例如，如果我们将此示例中的每个事件记录大小估算为 1500 字节，则指定环境中每日大约产生 2-15 MB 的事件数据。

若要了解更多有关 Windows 事件的信息，请参阅 [windows 事件记录属性](../azure-monitor/agents/data-sources-windows-events.md)。

## <a name="estimating-diagnostics-ingestion"></a>估算诊断引入

诊断服务为用户和管理活动创建活动日志。

诊断计数器跟踪的活动日志的名称如下：

- WVDCheckpoints
- WVDConnections
- WVDErrors
- WVDFeeds
- WVDManagement
- WVDAgentHealthStatus

每当环境满足建立记录所需的条款时，服务就会发送诊断信息。 由于诊断记录计数是不可预测的，因此，我们将根据此估算的健康环境作为示例，让每天每个 VM 使用 500-1000 个事件。

例如，如果我们将此示例中的每个诊断记录大小估算为 200 字节，则每天每个 VM 引入数据的总量将小于每个 1 MB。

若要了解更多有关活动日志类别的信息，请参阅 [Windows 虚拟桌面诊断](diagnostics-log-analytics.md)。

## <a name="estimating-total-costs"></a>估算总成本

最后，我们来估算总成本。 在此示例中，假设我们基于前面几节中的示例值得出以下结果：

| 数据源        | 每天的大小估算（以 MB 为单位）   |
|-------------------------------------|------------------------------------------|
| 性能计数器   | 90-130 |
| 事件    | 2-15 |
| Windows 虚拟桌面诊断 | \< 1 |

在此示例中，Windows 虚拟桌面 Azure Monitor 的总引入数据为每天每个 VM 92-145 MB。 换言之，每 31 天，每个 VM 引入约 3-5 GB 数据。

使用 [Log Analytics 定价](https://azure.microsoft.com/pricing/details/monitor/)的默认即用即付模型，可以估算每月 Azure Monitor 的数据收集和存储成本。 根据你的数据引入，还可以考虑 Log Analytics 定价的产能预留模型。

## <a name="manage-your-data-ingestion-to-reduce-costs"></a>管理数据引入以降低成本

本部分将介绍如何测量和管理数据引入以降低成本。

若要了解如何管理工作簿的权限，请参阅[访问控制](../azure-monitor/visualize/workbooks-access-control.md)。

>[!NOTE]
>删除数据点将影响其 Windows 虚拟桌面 Azure Monitor 中对应的视觉对象。

### <a name="log-analytics-settings"></a>Log Analytics 设置

下面是优化 Log Analytics 设置以管理数据引入的一些建议：

- 使用 Windows 虚拟桌面资源的指定 Log Analytics 工作区确保 Log Analytics 只收集 Windows 虚拟桌面部署中的性能计数器和虚拟机事件。
- 调整 Log Analytics 存储设置以管理成本。 可以缩短保持期，评估固定的存储定价层是否会更具成本效益，或对引入数据量的多少设置边界，减轻不正常部署的影响。 想了解更多信息，请参阅[管理 Azure Monitor 日志的使用和成本](../azure-monitor/platform/manage-cost-storage.md)。

### <a name="remove-excess-data"></a>删除过量数据

默认配置是我们唯一建议用于 Windows 虚拟桌面 Azure Monitor 的数据集。 你始终可以选择添加其它数据点，并在“主机诊断：主机浏览器”中查看它们或为它们自定义图表，但添加的数据会增加你的 Log Analytics 成本。 为了节省成本，可以将其删除。

### <a name="measure-and-manage-your-performance-counter-data"></a>测量和管理性能计数器数据

实际监视成本将取决于你的环境规模、使用情况和健康状况。 若要了解如何测量 Log Analytics 工作区中的数据引入，请参阅[了解引入日志数据量](../azure-monitor/logs/manage-cost-storage.md#understanding-ingested-data-volume)。

会话主机所使用的性能计数器可能是 Windows 虚拟桌面 Azure Monitor 的最大引入数据源。 以下 Log Analytics 工作区的自定义查询模板可以跟踪前一天每个性能计数器的频率和引入数据量：

```azure
let WVDHosts = dynamic(['Host1.MyCompany.com', 'Host2.MyCompany.com']);
Perf
| where TimeGenerated > ago(1d)
| where Computer in (WVDHosts)
| extend PerfCounter = strcat(ObjectName, ":", CounterName)
| summarize Records = count(TimeGenerated), InstanceNames = dcount(InstanceName), Bytes=sum(_BilledSize) by PerfCounter
| extend Billed_MBytes = Bytes / (1024 * 1024), BytesPerRecord = Bytes / Records
| sort by Records desc
```

>[!NOTE]
>请确保将模板的占位符值替换为你的环境使用的值，否则查询将不起作用。

此查询将显示在环境中启用的所有性能计数器，而不只是 Windows 虚拟桌面 Azure Monitor 的默认计数器。 该信息可帮助你了解哪些领域要降低成本，比如降低计数器的频率或将其全部删除。

你还可以通过删除性能计数器来降低成本。 若要了解如何删除性能计数器或编辑以降低现有计数器频率，请参阅[配置性能计数器](../azure-monitor/platform/data-sources-performance-counters.md#configuring-performance-counters)。

### <a name="manage-windows-event-logs"></a>管理 Windows 事件日志

当所有主机都运行正常时，Windows 事件不太可能导致数据引入高峰。 不正常的主机会增加发送到日志的事件数量，但这些信息对于解决主机的问题至关重要。 建议保留它们。 了解更多如何管理 Windows 事件日志的信息，请参阅[配置 Windows 事件日志](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs)。

### <a name="manage-diagnostics"></a>管理诊断

Windows 虚拟桌面诊断占数据存储成本的比例应不足 1%，因此，我们不建议将它们删除。 管理 Windows 虚拟桌面诊断，请[使用 Log Analytics 诊断功能](diagnostics-log-analytics.md)。

## <a name="next-steps"></a>后续步骤

在下列文章中了解更多有关 Windows 虚拟桌面 Azure Monitor 的详细信息：

- [使用 Windows 虚拟桌面 Azure Monitor 监视部署](azure-monitor.md)。
- 使用[词汇表](azure-monitor-glossary.md)了解有关术语和概念的详细信息。
- 如果遇到问题，请查看[故障排除指南](troubleshoot-azure-monitor.md)以获取帮助。
- 查看 [Azure Monitor 中的监视使用情况和估计成本](../azure-monitor/usage-estimated-costs.md)，了解更多有关管理监视成本的详细信息。
