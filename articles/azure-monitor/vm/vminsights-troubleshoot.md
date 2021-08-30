---
title: 对 VM 见解进行故障排除
description: 对 VM 见解安装进行故障排除。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/15/2021
ms.custom: references_regions
ms.openlocfilehash: a7bcce4d18f6bcfe299a31dae3eb036bef12da9b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729284"
---
# <a name="troubleshoot-vm-insights"></a>对 VM 见解进行故障排除
本文提供了当你在启用或使用 VM 见解时遇到问题的情况下所需的故障排除信息。

## <a name="cannot-enable-vm-insights-on-a-machine"></a>无法在计算机上启用 VM 见解
从 Azure 门户载入 Azure 虚拟机时，将执行以下步骤：

- 创建默认的 Log Analytics 工作区（如果已选择该选项）。
- 使用 VM 扩展在 Azure 虚拟机上安装 Log Analytics 代理（如果已安装代理）。
- 使用一个扩展在 Azure 虚拟机上安装依赖项代理（如果确定有必要）。
  
在载入过程中，将验证每一个步骤，并在门户中显示通知状态。 配置工作区和安装代理通常需要 5 到 10 分钟。 还需要另外 5 到 10 分钟才能在门户中查看数据。

如果你在执行载入过程后收到需要载入虚拟机的消息，则允许最多 30 分钟的时间完成此过程。 如果问题仍然存在，请参阅以下部分了解可能的原因。

### <a name="is-the-virtual-machine-running"></a>虚拟机是否正在运行？
 如果虚拟机已关闭一段时间，当前为关闭状态，或者最近才开启，则在数据到达之前，有一段时间不会显示任何数据。

### <a name="is-the-operating-system-supported"></a>操作系统是否受支持？
如果操作系统不在[受支持的操作系统](vminsights-enable-overview.md#supported-operating-systems)列表中，则该扩展将无法安装，你将看到指出正在等待数据到达的此消息。

### <a name="did-the-extension-install-properly"></a>扩展安装是否正确？
如果仍会看到需要载入虚拟机的消息，这可能意味着未能正确安装其中一个或两个扩展。 在 Azure 门户中查看虚拟机的“扩展”页，验证是否列出了以下扩展。

| 操作系统 | 代理 | 
|:---|:---|
| Windows | MicrosoftMonitoringAgent<br>Microsoft.Azure.Monitoring.DependencyAgent |
| Linux | OMSAgentForLinux<br>DependencyAgentLinux |

如果在已安装的扩展列表中看不到你的操作系统的两个扩展，则需要安装它们。 如果已列出扩展，但状态未显示为“预配成功”，则应删除并重新安装该扩展。

### <a name="do-you-have-connectivity-issues"></a>是否存在连接问题？
对于 Windows 计算机，可以使用 TestCloudConnectivity 工具来确定连接问题。 默认情况下，该工具连同代理一起安装在 %SystemDrive%\Program Files\Microsoft Monitoring Agent\Agent 文件夹中。 从提升的命令提示符运行该工具。 它将返回结果，并突出显示测试失败的位置。 

![TestCloudConnectivity 工具](media/vminsights-troubleshoot/test-cloud-connectivity.png)

### <a name="more-agent-troubleshooting"></a>更多代理故障排除

请参阅以下文章，了解如何对 Log Analytics 代理的问题进行故障排除：

- [如何排查 Log Analytics Windows 代理的问题](../agents/agent-windows-troubleshoot.md)
- [如何排查 Log Analytics Linux 代理的问题](../agents/agent-linux-troubleshoot.md)

## <a name="performance-view-has-no-data"></a>性能视图没有数据
如果似乎正确安装了代理，但在性能视图中没有看到任何数据，请参阅以下部分了解可能的原因。

### <a name="has-your-log-analytics-workspace-reached-its-data-limit"></a>Log Analytics 工作区是否达到了其数据限制？
查看[产能预留和数据引入的定价](https://azure.microsoft.com/pricing/details/monitor/)。

### <a name="is-your-virtual-machine-agent-connected-to-azure-monitor-logs"></a>虚拟机代理是否连接到 Azure Monitor 日志？

在 Azure 门户中，从“Azure Monitor”菜单中的“日志”打开 Log Analytics。 对计算机运行以下查询：

```kuso
Heartbeat
| where Computer == "my-computer"
| sort by TimeGenerated desc 
```

如果没有看到任何数据，或者计算机最近未发送检测信号，则代理可能有问题。 查看以上部分，了解代理故障排除信息。

## <a name="virtual-machine-doesnt-appear-in-map-view"></a>虚拟机未出现在映射视图中

### <a name="is-the-dependency-agent-installed"></a>是否安装了 Dependency Agent？
 使用上面部分中的信息确定 Dependency Agent 是否已安装，并且正常工作。

### <a name="are-you-on-the-log-analytics-free-tier"></a>你是否处在 Log Analytics 免费层？
[Log Analytics 免费层](https://azure.microsoft.com/pricing/details/monitor/)这是一种支持最多 5 个唯一服务映射计算机的旧定价计划。 服务映射中不再显示任何其他的计算机，即使前 5 个服务器不再发送数据。

### <a name="is-your-virtual-machine-sending-log-and-performance-data-to-azure-monitor-logs"></a>虚拟机是否将日志和性能数据发送到 Azure Monitor 日志？
使用[性能视图没有数据](#performance-view-has-no-data)部分中的日志查询，确定是否为虚拟机收集了数据。 如果未收集数据，请使用上述的 TestCloudConnectivity 工具来确定是否存在连接问题。


## <a name="virtual-machine-appears-in-map-view-but-has-missing-data"></a>虚拟机显示在映射视图中，但缺少数据
如果虚拟机在映射视图中，则 Dependency Agent 已安装并正在运行，但内核驱动程序未加载。 查看以下位置中的日志文件：

| 操作系统 | 日志 | 
|:---|:---|
| Windows | C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log |
| Linux | /var/opt/microsoft/dependency-agent/log/service.log |

文件的最后几行应指出为何未加载内核。 例如，如果更新内核，则内核在 Linux 上可能不受支持。
## <a name="next-steps"></a>后续步骤

- 有关载入 VM 见解代理的详细信息，请参阅[启用 VM 见解概述](vminsights-enable-overview.md)。
