---
title: Azure Monitor 中的 Wire Data 解决方案 | Microsoft Docs
description: 线路数据是具有 Log Analytics 代理的计算机提供的整合网络和性能数据。 网络数据与日志数据结合在一起，可帮助将数据相关联。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/26/2021
ms.openlocfilehash: 52bb368ccf19c23c04bc062e7db50c07ecebaceb
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114296074"
---
# <a name="wire-data-20-preview-solution-in-azure-monitor-retired"></a>Azure Monitor 中的 Wire Data 2.0（预览版）解决方案（已停用）

![Wire Data 符号](media/wire-data/wire-data2-symbol.png)

>[!NOTE]
>已将 Wire Data 解决方案替换为 [VM 见解](../vm/vminsights-overview.md)和[服务映射解决方案](../vm/service-map.md)。  两者都使用 Log Analytics 代理和 Dependency Agent 将网络连接数据收集到 Azure Monitor 中。
>
>Wire Data 解决方案支持将于“2022 年 3 月 31 日”结束。  在停用日期之前，使用 Wire Data 2.0（预览版）解决方案的现有客户可能会继续使用它。
>
>新客户和现有客户应安装 [VM 见解](../vm/vminsights-enable-overview.md)或[服务映射解决方案](../vm/service-map.md)。  它们收集的映射数据集与 Wire Data 2.0（预览版）数据集相当。  VM 见解包含服务映射数据集以及其他性能数据和特性以供分析。 这两个产品/服务都具有[与 Azure Sentinel 的连接](../../sentinel/connect-data-sources.md#map-data-types-with-azure-sentinel-connection-options)。
 

线路数据是通过 Log Analytics 代理（包括由环境中的 Operations Manager 监视的代理）从与 Windows 和 Linux 相连的计算机中收集的网络与性能整合数据。 网络数据与其他日志数据结合在一起，可帮助你将数据进行关联。

除了 Log Analytics 代理之外，Wire Data 解决方案使用在 IT 基础结构中的计算机上安装的 Microsoft 依赖关系代理。 依赖关系代理将监视 [OSI 模型](https://en.wikipedia.org/wiki/OSI_model)中处于网络层 2-3 层中的计算机接收和发送的网络数据，包括使用的各种协议和端口。 然后，这些代理将数据发送到 Azure Monitor。

## <a name="migrate-to-azure-monitor-vm-insights-or-service-map"></a>迁移到 Azure Monitor VM 见解或服务映射

很多情况下，我们会发现，客户在同一虚拟机上经常同时启用了 Wire Data 2.0（预览版）和 [VM 见解](../vm/vminsights-overview.md)或[服务映射解决方案](../vm/service-map.md)。  这意味着你在虚拟机上启用了替代产品/服务。  只需[从 Log Analytics 工作区中删除 Wire Data 2.0（预览版）解决方案](./solutions.md?tabs=portal#remove-a-monitoring-solution)即可。

如果仅在虚拟机上启用了 Wire Data 2.0（预览版），则可以将虚拟机加入 [VM 见解](../vm/vminsights-enable-overview.md)或[服务映射解决方案](../vm/service-map.md)，然后[从 Log Analytics 工作区中删除 Wire Data 2.0（预览版）解决方案](./solutions.md?tabs=portal#remove-a-monitoring-solution)。

## <a name="migrate-your-queries-to-the-vmconnection-table-from-azure-monitor-vm-insights"></a>将查询从 Azure Monitor VM 见解迁移到 VMConnection 表

### <a name="agents-providing-data"></a>提供数据的代理

#### <a name="wire-data-20-query"></a>Wire Data 2.0 查询

```
WireData
| summarize AggregatedValue = sum(TotalBytes) by Computer
| limit 500000
```

#### <a name="vm-insights-and-service-map-query"></a>VM 见解和服务映射查询

```
VMConnection
| summarize AggregatedValue = sum(BytesReceived + BytesSent) by Computer
| limit 500000
```

### <a name="ip-addresses-of-the-agents-providing-data"></a>提供数据的代理的 IP 地址

#### <a name="wire-data-20-query"></a>Wire Data 2.0 查询

```
WireData
| summarize AggregatedValue = count() by LocalIP
```

#### <a name="vm-insights-and-service-map-query"></a>VM 见解和服务映射查询

```
VMComputer
| distinct Computer, tostring(Ipv4Addresses)
```

### <a name="all-outbound-communications-by-remote-ip-address"></a>通过远程 IP 地址的所有出站通信

#### <a name="wire-data-20-query"></a>Wire Data 2.0 查询

```
WireData
| where Direction == "Outbound"
| summarize AggregatedValue = count() by RemoteIP
```

#### <a name="vm-insights-and-service-map-query"></a>VM 见解和服务映射查询

```
VMConnection
| where Direction == "outbound"
| summarize AggregatedValue = count() by RemoteIp
```

### <a name="bytes-received-by-protocol-name"></a>协议名称接收的字节数

#### <a name="wire-data-20-query"></a>Wire Data 2.0 查询

```
WireData 
| where Direction == "Inbound"
| summarize AggregatedValue = sum(ReceivedBytes) by ProtocolName
```

#### <a name="vm-insights-and-service-map-query"></a>VM 见解和服务映射查询

```
VMConnection
| where Direction == "inbound"
| summarize AggregatedValue = sum(BytesReceived) by Protocol
```

### <a name="amount-of-network-traffic-in-bytes-by-process"></a>进程的网络流量数（以字节为单位）

#### <a name="wire-data-20-query"></a>Wire Data 2.0 查询

```
WireData
| summarize AggregatedValue = sum(TotalBytes) by ProcessName
```

#### <a name="vm-insights-and-service-map-query"></a>VM 见解和服务映射查询

```
VMConnection
| summarize sum(BytesReceived), sum(BytesSent) by ProcessName
```

### <a name="more-examples-queries"></a>更多示例查询

有关其他示例查询，请参阅 [VM 见解日志搜索文档](../vm/vminsights-log-search.md)和 [VM 见解警报文档](../vm/monitor-virtual-machine-alerts.md)。

## <a name="uninstall-wire-data-20-solution"></a>卸载 Wire Data 2.0 解决方案

若要卸载 Wire Data 2.0，只需从 Log Analytics 工作区中删除该解决方案。  这可能引发以下情况：

* 从连接到工作区的虚拟机中删除 Wire Data 管理包 
* Wire Data 数据类型不再显示在工作区中

按照[这些说明](./solutions.md?tabs=portal#remove-a-monitoring-solution)操作以删除 Wire Data 解决方案。

>[!NOTE]
>如果工作区中有服务映射或 VM 见解解决方案，则不会删除管理包，因为这些解决方案也使用此管理包。

### <a name="wire-data-20-management-packs"></a>Wire Data 2.0 管理包

在 Log Analytics 工作区中激活 Wire Data 时，将向该工作区中的所有 Windows 服务器发送 300KB 的管理包。 若在[连接的管理组](../agents/om-agents.md)中使用 System Center Operations Manager 代理，则会从 System Center Operations Manager 部署依赖关系监视器管理包。 如果代理是直接连接的，则 Azure Monitor 会传送管理包。

管理包名为 Microsoft.IntelligencePacks.ApplicationDependencyMonitor。 它将写入到 %Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs。 管理包所使用的数据源是 %Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources&lt;AutoGeneratedID&gt;\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll。

## <a name="uninstall-the-dependency-agent"></a>卸载 Dependency Agent

>[!NOTE]
>如果计划用服务映射或 VM 见解替换 Wire Data，则不应删除 Dependency Agent。

使用以下部分帮助删除 Dependency Agent。  

### <a name="uninstall-the-dependency-agent-on-windows"></a>卸载 Windows 上的 Dependency Agent

管理员可通过“控制面板”卸载适用于 Windows 的 Dependency Agent。

管理员还可以运行 %Programfiles%\Microsoft Dependency Agent\Uninstall.exe 卸载 Dependency Agent。

### <a name="uninstall-the-dependency-agent-on-linux"></a>卸载 Linux 上的 Dependency Agent

若要从 Linux 中彻底卸载 Dependency Agent，必须删除代理本身以及随该代理自动安装的连接器。 可使用以下单个命令同时卸载这两项：

```
rpm -e dependency-agent dependency-agent-connector
```

## <a name="using-the-wire-data-20-solution"></a>使用 Wire Data 2.0 解决方案

在 Azure 门户的 Log Analytics 工作区的“概览”页中，单击“Wire Data 2.0”磁贴打开 Wire Data 仪表板。 该仪表板包含下表中的边栏选项卡。 每个边栏选项卡按照指定范围和时间范围列出了匹配该边栏选项卡条件的最多 10 个项。 可通过单击边栏选项卡底部的“查看全部”或单击边栏选项卡标题，运行返回所有记录的日志搜索。

| **边栏选项卡** | **说明** |
| --- | --- |
| 正在捕获网络流量的代理 | 显示正在捕获网络流量的代理数，并列出正在捕获流量的排名前 10 的计算机。 单击数字可以针对 <code>WireData \| summarize sum(TotalBytes) by Computer \| take 500000</code> 运行日志搜索。 单击列表中的某台计算机可运行日志搜索，将返回已捕获的总字节数。 |
| 本地子网 | 显示代理已发现的本地子网数。  单击数字可以针对 <code>WireData \| summarize sum(TotalBytes) by LocalSubnet</code> 运行日志搜索，这将列出所有子网以及通过每个子网发送的字节数。 单击列表中的某个子网可运行日志搜索，将返回通过该子网发送的总字节数。 |
| 应用程序级协议 | 显示代理发现的使用中的应用程序级协议的数目。 单击数字可以针对 <code>WireData \| summarize sum(TotalBytes) by ApplicationProtocol</code> 运行日志搜索。 单击某个协议可运行日志搜索，将返回使用该协议发送的总字节数。 |

![Wire Data 仪表板](./media/wire-data/wire-data-dash.png)

可以使用“正在捕获网络流量的代理”边栏选项卡来确定计算机正在消耗多少网络带宽。 可以通过此边栏选项卡轻松找到你的环境中“最健谈的”计算机。 此类计算机可能负载过重，行为异常，或者使用比平时更多的网络资源。

![Wire Data 2.0 仪表板中的代理捕获网络流量边栏选项卡的屏幕截图，其中显示了每台计算机使用的网络带宽。](./media/wire-data/log-search-example01.png)

类似地，可以使用“本地子网”边栏选项卡确定有多少网络流量正在通过各个子网移动。 用户通常围绕其应用程序的关键领域定义子网。 可以通过此边栏选项卡查看这些领域。

![Wire Data 2.0 仪表板中的本地子网边栏选项卡的屏幕截图，显示每个 LocalSubnet 使用的网络带宽。](./media/wire-data/log-search-example02.png)

“应用程序级协议”边栏选项卡很有用，因为它可以帮助你了解正在使用什么协议。 例如，你可能预料网络环境中没有使用 SSH。 查看此边栏选项卡中提供的信息可以快速确认或否定你的预期。

![Wire Data 2.0 仪表板中的应用程序级协议边栏选项卡的屏幕截图，显示每个协议使用的网络带宽。](./media/wire-data/log-search-example03.png)

了解协议流量是否在随时间推移而增加或减少也非常有用。 例如，如果某个应用程序传输的数据量在增加，则可能有某些事情需要注意或需要特别注意。

## <a name="input-data"></a>输入数据

线路数据使用已经启用的代理来收集网络流量的元数据。 每个代理大约每 15 秒发送一次数据。

## <a name="output-data"></a>输出数据

将为每种输入数据创建 _WireData_ 类型的记录。 WireData 记录具有下表中所示的属性：

| 属性 | 说明 |
|---|---|
| Computer | 从中收集了数据的计算机名称 |
| TimeGenerated | 记录的时间 |
| LocalIP | 本地计算机的 IP 地址 |
| SessionState | 已连接或已断开连接 |
| ReceivedBytes | 已接收的字节数 |
| ProtocolName | 使用的网络协议的名称 |
| IPVersion | IP 版本 |
| 方向 | 入站或出站 |
| MaliciousIP | 某个已知恶意源的 IP 地址 |
| 严重性 | 可疑恶意软件的严重性 |
| RemoteIPCountry | 远程 IP 地址所在的国家/地区 |
| ManagementGroupName | Operations Manager 管理组的名称 |
| SourceSystem | 从中收集了数据的源 |
| SessionStartTime | 会话开始时间 |
| SessionEndTime | 会话结束时间 |
| LocalSubnet | 从中收集了数据的子网 |
| LocalPortNumber | 本地端口号 |
| RemoteIP | 远程计算机使用的远程 IP 地址 |
| RemotePortNumber | 远程 IP 地址使用的端口号 |
| SessionID | 标识两个 IP 地址之间的通信会话的唯一值 |
| SentBytes | 已发送的字节数 |
| TotalBytes | 会话期间发送的总字节数 |
| ApplicationProtocol | 使用的网络协议的类型   |
| ProcessID | Windows 进程 ID |
| ProcessName | 进程的路径和文件名 |
| RemoteIPLongitude | IP 经度值 |
| RemoteIPLatitude | IP 纬度值 |

## <a name="next-steps"></a>后续步骤

- 有关为虚拟机启用监视的要求和方法，请参阅[部署 VM 见解](../vm/vminsights-enable-overview.md)。
- [搜索日志](../logs/log-query-overview.md)以查看详细的线路数据搜索记录。