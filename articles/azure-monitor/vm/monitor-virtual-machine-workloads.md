---
title: 使用 Azure Monitor 监视虚拟机：工作负荷
description: 了解如何在 Azure Monitor 中监视虚拟机的来宾工作负荷。
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/21/2021
ms.openlocfilehash: e411f7fd6f8d8a21b2016cbe16d0c7a947313331
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2021
ms.locfileid: "114674857"
---
# <a name="monitor-virtual-machines-with-azure-monitor-workloads"></a>使用 Azure Monitor 监视虚拟机：工作负荷
本文是[在 Azure Monitor 中监视虚拟机及其工作负载](monitor-virtual-machine.md)方案的一部分。 本文介绍了如何监视虚拟机的来宾操作系统上运行的工作负荷。 本文详细介绍了如何在虚拟机上的不同数据源上分析和警示不同数据源。

## <a name="configure-additional-data-collection"></a>配置其他数据收集
VM 见解只收集已启用计算机的来宾操作系统的性能数据。 可以通过配置 Log Analytics 工作区，启用从代理中收集其他性能数据、事件及其他监视数据。 该工作区只需要配置一次，因为连接到工作区的任何代理会自动下载配置并立即开始收集定义的数据。

有关可用数据源的列表和配置这些数据源的详细信息，请参阅 [Azure Monitor 中的代理数据源](../agents/agent-data-sources.md)。

> [!NOTE]
> 不能选择性地为不同的计算机配置数据收集。 所有连接到工作区的计算机都使用该工作区的配置。

> [!IMPORTANT]
> 请注意，只收集您需要的数据。 成本与工作区中收集的任何数据相关联。 你收集的数据应仅支持特定分析和警报方案。

## <a name="convert-management-pack-logic"></a>转换管理包逻辑
大量实施 Azure Monitor 的客户目前使用 System Center Operations Manager 中的管理包来监视其虚拟机工作负荷。 由于平台本质上的不同，没有任何迁移工具可将资产从 Operations Manager 转换为 Azure Monitor。 你的迁移将构成一个标准的 Azure Monitor 实施，而你将继续使用 Operations Manager。 当你根据对不同应用程序和组件的要求自定义 Azure Monitor，并且它获得更多功能时，你就可以开始停用 Operations Manager 中的各种管理包和代理。

不要尝试复制管理包的整个功能，而是分析管理包提供的关键监视。 使用前面几节中所述的方法来决定是否可以复制这些监视要求。 在许多情况下，你可以在 Azure Monitor 中配置数据收集和警报规则，这些规则将重现丰富的功能，足以让你停用某个特定管理包。 管理包通常包含成百上千个规则和监视器。

在大多数情况下，Operations Manager 将数据收集和警报条件组合到同一个规则或监视器中。 在 Azure Monitor 中，你必须为任何警报方案配置数据收集和警报规则。

一种策略是重点关注在环境中触发警报的监视器和规则。 请参阅 [Operations Manager 中可用的现有报告](/system-center/scom/manage-reports-installed-during-setup)，例如“警报”和“最常见警报”，可以帮助您识别一段时间内的警报。  你还可以对操作数据库运行以下查询，以评估最常见的近期警报。

```sql
select AlertName, COUNT(AlertName) as 'Total Alerts' from
Alert.vAlertResolutionState ars
inner join Alert.vAlertDetail adt on ars.AlertGuid = adt.AlertGuid
inner join Alert.vAlert alt on ars.AlertGuid = alt.AlertGuid
group by AlertName
order by 'Total Alerts' DESC
```

评估输出，以确定有关迁移的特定警报。 忽略任何已过时或已知有问题的警报。 查看管理包，确定永不触发的任何感兴趣的关键警报。

## <a name="windows-or-syslog-event"></a>Windows 或 Syslog 事件
在这种常见监视方案中，操作系统和应用程序将写入 Windows 事件或 Syslog。 找到单个事件后立即创建警报。 或者，您可以在特定的时间范围内等待一系列匹配的事件。

若要收集这些事件，请配置 Log Analytics 工作区以收集 [Windows 事件](../agents/data-sources-windows-events.md)或 [Syslog 事件](../agents/data-sources-windows-events.md)。 在工作区中引入和保留此数据需要付费。

Windows 事件存储在 [Event](/azure/azure-monitor/reference/tables/event) 表中，Syslog 事件存储在 Log Analytics 工作区的 [Syslog](/azure/azure-monitor/reference/tables/syslog) 表中。

### <a name="sample-log-queries"></a>示例日志查询

- 按计算机事件日志和事件类型统计事件数量。

    ```kusto
    Event
    | summarize count() by Computer, EventLog, EventLevelName
    | sort by Computer, EventLog, EventLevelName
    ```

- 按计算机事件日志和事件 ID 统计事件数量。
    
    ```kusto
    Event
    | summarize count() by Computer, EventLog, EventLevelName
    | sort by Computer, EventLog, EventLevelName
    ```

### <a name="sample-alert-rules"></a>警报规则示例
在创建特定 Windows 事件时，以下示例将创建一个警报。 该示例使用指标测量警报规则为每台计算机创建单独的警报。

- 针对特定的 Windows 事件创建警报规则。

   此示例显示了应用程序日志中的一个事件。 指定阈值 0，且连续违反次数大于 0。

    ```kusto
    Event 
    | where EventLog == "Application"
    | where EventID == 123 
    | summarize AggregatedValue = count() by Computer, bin(TimeGenerated, 15m)
    ```

- 针对具有特定严重性的 Syslog 事件创建警报规则。

   下面的示例显示了错误授权事件。 指定阈值 0，且连续违反次数大于 0。

    ```kusto
    Syslog
    | where Facility == "auth"
    | where SeverityLevel == "err"
    | summarize AggregatedValue = count() by Computer, bin(TimeGenerated, 15m)
    ```

## <a name="custom-performance-counters"></a>自定义性能计数器
您可能需要特殊的性能计数器，这些计数器是由应用程序或来宾访客系统创建，但不是由 VM 见解来收集。 配置 Log Analytics 工作区以收集此[性能数据](../agents/data-sources-windows-events.md)。 在工作区中引入和保留此数据需要付费。 小心，不要收集 VM 见解已经收集了的性能数据。

工作区配置的性能数据存储在 [Perf](/azure/azure-monitor/reference/tables/perf) 表中。 此表的结构不同于 VM 见解使用的 [InsightsMetrics](/azure/azure-monitor/reference/tables/insightsmetrics) 表。

### <a name="sample-log-queries"></a>示例日志查询

有关使用自定义性能计数器的日志查询示例，请参阅[使用性能记录的日志查询](../agents/data-sources-performance-counters.md#log-queries-with-performance-records)。

### <a name="sample-alerts"></a>警报示例

- **创建有关计数器最大值的警报。**
    
    ```kusto
    Perf 
    | where CounterName == "My Counter" 
    | summarize AggregatedValue = max(CounterValue) by Computer
    ```

- **创建有关计数器平均值的警报。**

    ```kusto
    Perf 
    | where CounterName == "My Counter" 
    | summarize AggregatedValue = avg(CounterValue) by Computer
    ```

## <a name="text-logs"></a>文本日志
一些应用程序将事件写入存储在虚拟机上的文本日志中。 在 Log Analytics 工作区中定义[自定义日志](../agents/data-sources-custom-logs.md)以收集这些事件。 定义文本日志的位置及其详细配置。 在工作区中引入和保留此数据需要付费。

文本日志中的事件存储在名称类似于 MyTable_CL 的表中。 配置日志时，可定义日志的名称和结构。

### <a name="sample-log-queries"></a>示例日志查询
此处使用的列名仅用作示例。 定义特定日志时，可以为其定义列名。 你的日志的列名很可能不同。

- **通过代码统计事件的数量。**
    
    ```kusto
    MyApp_CL
    | summarize count() by code
    ```

### <a name="sample-alert-rule"></a>警报规则示例

- **对任何错误事件创建警报规则。**
    
    ```kusto
    MyApp_CL
    | where status == "Error"
    | summarize AggregatedValue = count() by Computer, bin(TimeGenerated, 15m)
    ```
## <a name="iis-logs"></a>IIS 日志
Windows 计算机上运行的 IIS 将日志写入文本文件。 配置 Log Analytics 工作区以收集 [IIS 日志](../agents/data-sources-iis-logs.md)。 在工作区中引入和保留此数据需要付费。

IIS 日志中的记录存储在 Log Analytics 工作区的 [W3CIISLog](/azure/azure-monitor/reference/tables/w3ciislog) 表中。

### <a name="sample-log-queries"></a>示例日志查询

- **按主机的 URL www.contoso.com 统计 IIS 日志条目的数量。**
    
    ```kusto
    W3CIISLog 
    | where csHost=="www.contoso.com" 
    | summarize count() by csUriStem
    ```

- 查看每个 IIS 计算机接收的总字节数。

    ```kusto
    W3CIISLog 
    | summarize sum(csBytes) by Computer
    ```

### <a name="sample-alert-rule"></a>警报规则示例

- 针对返回状态为 500 的任何记录创建警报规则。
    
    ```kusto
    W3CIISLog 
    | where scStatus==500
    | summarize AggregatedValue = count() by Computer, bin(TimeGenerated, 15m)
    ```

## <a name="service-or-daemon"></a>服务或守护程序
若要监视 Windows 服务或 Linux 守护程序的状态，请在 [Azure 自动化中](../../automation/automation-intro.md) 启用 [更改跟踪和清单解决](../../automation/change-tracking/overview.md) 方案。 Azure Monitor 无法监视服务或守护程序的状态。 有一些方法或许能够使用，例如在 Windows 事件日志中查找事件，但此方法并不可靠。 您还可以从 [VMProcess](/azure/azure-monitor/reference/tables/vmprocess) 表中查找与计算机上运行的服务关联的进程。 此表的更新频率仅为仅每小时一次，对于警报来说，这种频率通常是不够的。

> [!NOTE]
> “更改跟踪和分析”解决方案不同于 VM 见解中的[“更改分析”](vminsights-change-analysis.md)功能。 此功能处于公共预览阶段，此方案中尚未包括此功能。

有关在虚拟机上启用“更改跟踪”解决方案的不同选项，请参阅[启用更改跟踪和清单](../../automation/change-tracking/overview.md#enable-change-tracking-and-inventory)。 此解决方案包括用于大规模配置虚拟机的方法。 你必须[创建一个 Azure 自动化帐户](../../automation/automation-quickstart-create-account.md)才能支持解决方案。

启用“更改跟踪和清单”后，将在 Log Analytics 工作区中创建两个新的表。 使用这些表作为日志查询警报规则。

| 表 | 说明 |
|:---|:---|
| [ConfigurationChange](/azure/azure-monitor/reference/tables/configurationdata) | 对来宾内配置数据的更改 |
| [ConfigurationData](/azure/azure-monitor/reference/tables/configurationdata) | 来宾内配置数据的上次报告状态 |


### <a name="sample-log-queries"></a>示例日志查询

- 列出最近启动的所有服务和守护程序。
    
    ```kusto
    ConfigurationChange
    | where ConfigChangeType == "Daemons" or ConfigChangeType == "WindowsServices"
    | where SvcState == "Running"
    | sort by Computer, SvcName
    ```

### <a name="alert-rule-samples"></a>警报规则示例

- 根据特定服务停止时间创建警报规则。

    
    ```kusto
    ConfigurationData
    | where SvcName == "W3SVC" 
    | where SvcState == "Stopped"
    | where ConfigDataType == "WindowsServices"
    | where SvcStartupType == "Auto"
    | summarize AggregatedValue = count() by Computer, SvcName, SvcDisplayName, SvcState, bin(TimeGenerated, 15m)
    ```

- 根据一组服务中某个服务的停止时间创建警报规则。
    
    ```kusto
    let services = dynamic(["omskd","cshost","schedule","wuauserv","heathservice","efs","wsusservice","SrmSvc","CertSvc","wmsvc","vpxd","winmgmt","netman","smsexec","w3svc","sms_site_vss_writer","ccmexe","spooler","eventsystem","netlogon","kdc","ntds","lsmserv","gpsvc","dns","dfsr","dfs","dhcp","DNSCache","dmserver","messenger","w32time","plugplay","rpcss","lanmanserver","lmhosts","eventlog","lanmanworkstation","wnirm","mpssvc","dhcpserver","VSS","ClusSvc","MSExchangeTransport","MSExchangeIS"]);
    ConfigurationData
    | where ConfigDataType == "WindowsServices"
    | where SvcStartupType == "Auto"
    | where SvcName in (services)
    | where SvcState == "Stopped"
    | project TimeGenerated, Computer, SvcName, SvcDisplayName, SvcState
    | summarize AggregatedValue = count() by Computer, SvcName, SvcDisplayName, SvcState, bin(TimeGenerated, 15m)
    ```

## <a name="port-monitoring"></a>端口监视
端口监视验证计算机是否正在侦听特定端口。 此处介绍了端口监视的两种潜在策略。

### <a name="dependency-agent-tables"></a>依赖项代理表
使用 [VMConnection](/azure/azure-monitor/reference/tables/vmconnection) 和 [VMBoundPort](/azure/azure-monitor/reference/tables/vmboundport) 分析计算机上的连接和端口。 VMBoundPort 表每分钟更新一次，其中包含计算机上运行的每个进程及其侦听的端口。 您可以创建类似于丢失检测信号警报的日志查询警报，以查找已停止的进程，或在计算机未在侦听特定端口时发出警报。

### <a name="sample-log-queries"></a>示例日志查询

- 查看 VM 上打开的端口数，这对于评估哪些 VM 出现配置和安全漏洞非常有用。

    ```kusto
    VMBoundPort
    | where Ip != "127.0.0.1"
    | summarize by Computer, Machine, Port, Protocol
    | summarize OpenPorts=count() by Computer, Machine
    | order by OpenPorts desc
    ```

- 列出 VM 上的绑定端口，这对于评估哪些 VM 出现配置和安全漏洞非常有用。

    ```kusto
    VMBoundPort
    | distinct Computer, Port, ProcessName
    ```


- **按端口分析网络活动，确定应用程序或服务的配置方式。**

    ```kusto
    VMBoundPort
    | where Ip != "127.0.0.1"
    | summarize BytesSent=sum(BytesSent), BytesReceived=sum(BytesReceived), LinksEstablished=sum(LinksEstablished), LinksTerminated=sum(LinksTerminated), arg_max(TimeGenerated, LinksLive) by Machine, Computer, ProcessName, Ip, Port, IsWildcardBind
    | project-away TimeGenerated
    | order by Machine, Computer, Port, Ip, ProcessName
    ```

- **查看 VM 的发送和接收字节数趋势。**

    ```kusto
    VMConnection
    | summarize sum(BytesSent), sum(BytesReceived) by bin(TimeGenerated,1hr), Computer
    | order by Computer desc
    | render timechart
    ```

- **使用一段时间的连接故障数来确定故障率是稳定还是变化。**

    ```kusto
    VMConnection
    | where Computer == <replace this with a computer name, e.g. ‘acme-demo’>
    | extend bythehour = datetime_part("hour", TimeGenerated)
    | project bythehour, LinksFailed
    | summarize failCount = count() by bythehour
    | sort by bythehour asc
    | render timechart
    ```

- **链接状态趋势以分析计算机的行为和连接状态。**

    ```kusto
    VMConnection
    | where Computer == <replace this with a computer name, e.g. ‘acme-demo’>
    | summarize  dcount(LinksEstablished), dcount(LinksLive), dcount(LinksFailed), dcount(LinksTerminated) by bin(TimeGenerated, 1h)
    | render timechart
    ```

### <a name="connection-manager"></a>连接管理器
[网络观察程序](../../network-watcher/network-watcher-monitoring-overview.md)的[连接监视器](../../network-watcher/connection-monitor-overview.md)功能用于测试与虚拟机端口的连接。 测试验证计算机是否正在侦听端口，以及是否可通过网络访问。
连接管理器要求在启动测试的客户端计算机上安装网络观察程序扩展。 无需在要测试的计算机上安装。 有关详细信息，请参阅[教程：使用 Azure 门户监视网络通信](../../network-watcher/connection-monitor.md)。

需要额外付费才能使用连接管理器。 有关详细信息，请参阅[网络观察程序定价](https://azure.microsoft.com/pricing/details/network-watcher/)。

## <a name="run-a-process-on-a-local-machine"></a>在本地计算机上运行进程
某些工作负荷的监视需要本地进程。 例如，在本地计算机上运行的 PowerShell 脚本可以连接到应用程序并收集或处理数据。 可以使用 [Azure 自动化](../../automation/automation-intro.md)中包含的[混合 Runbook 辅助角色](../../automation/automation-hybrid-runbook-worker.md)来运行本地 PowerShell 脚本。 混合 Runbook 辅助角色本身是免费的，但它使用的每个 Runbook 并不免费。

Runbook 可以访问本地计算机上的任何资源来收集所需的数据。 Runbook 无法直接将数据发送到 Azure Monitor 或创建警报。 若要创建警报，请让 Runbook 将条目写入自定义日志，然后将该日志配置为由 Azure Monitor 收集。 创建针对该日志条目触发的日志查询警报规则。

## <a name="synthetic-transactions"></a>综合事务
综合事务连接到计算机上运行的应用程序或服务，以模拟用户连接或实际用户流量。 如果应用程序可用，则可以假定计算机正常运行。 Azure Monitor 中的[应用程序见解](../app/app-insights-overview.md)提供了此功能。 仅适用于可通过 Internet 访问的应用程序。 对于内部应用程序，必须打开防火墙以允许通过执行测试的特定 Microsoft URL 进行访问。 或者，可以使用备用监视解决方案，例如 System Center Operations Manager。

|方法 | 说明 |
|:---|:---|
| [URL 测试](../app/monitor-web-app-availability.md) | 确保 HTTP 可用并返回网页 |
| [多步骤测试](../app/availability-multistep.md) | 模拟用户会话 |

## <a name="sql-server"></a>SQL Server

使用 [SQL 见解](../insights/sql-insights-overview.md)来监视在您的虚拟机上运行的 SQL Server。

## <a name="next-steps"></a>后续步骤

* [了解如何使用日志查询分析 Azure Monitor 日志中的数据](../logs/get-started-queries.md)
* [了解 Azure Monitor 中使用指标和日志的警报](../alerts/alerts-overview.md)