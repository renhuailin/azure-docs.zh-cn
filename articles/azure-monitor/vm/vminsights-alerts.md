---
title: 来自 VM 见解的警报
description: 介绍了如何基于 VM 见解收集的性能数据创建警报规则。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/10/2020
ms.openlocfilehash: 06c58b7081ed68724a3c907f8fe76dcf5f7b8057
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102046799"
---
# <a name="how-to-create-alerts-from-vm-insights"></a>如何基于 VM 见解创建警报
[Azure Monitor 中的警报](../alerts/alerts-overview.md)会主动通知你在监视数据中发现的值得关注的数据和模式。 VM 见解不包括预配置的警报规则，但你可以基于它收集的数据创建自己的警报规则。 本文提供有关如何创建警报规则的指导，包括一组示例查询。

> [!IMPORTANT]
> 本文所述警报基于从 VM 见解收集的数据进行的日志查询。 这不同于由[用于 VM 来宾运行状况的 Azure Monitor](vminsights-health-overview.md)（当前为公共预览版的一项功能）创建的警报。 由于此功能即将正式发布，因此将合并警报指南。


## <a name="alert-rule-types"></a>警报规则类型
Azure Monitor 具有[不同类型的警报规则](../alerts/alerts-overview.md#what-you-can-alert-on)，具体取决于用来创建警报的数据。 VM 见解收集的所有数据都存储在支持[日志警报](../alerts/alerts-log.md)的 Azure Monitor 日志中。 你当前无法对从 VM 见解收集的性能数据使用[指标警报](../alerts/alerts-log.md)，因为数据不会收集到 Azure Monitor 指标中。 若要为指标警报收集数据，请安装适用于 Windows VM 的[诊断扩展](../agents/diagnostics-extension-overview.md)或适用于 Linux VM 的 [Telegraf 代理](../essentials/collect-custom-metrics-linux-telegraf.md)，以将性能数据收集到指标中。

Azure Monitor 中有两种类型的日志警报：

- [结果数警报](../alerts/alerts-unified-log.md#count-of-the-results-table-rows)在查询至少返回指定数量的记录时创建单个警报。 它们非常适用于非数值数据（例如 [Log Analytics 代理](../agents/log-analytics-agent.md)收集的 Windows 和 Syslog 事件），或适用于分析多台计算机上的性能趋势。
- [指标度量警报](../alerts/alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value)为查询中其值超过警报规则中定义的阈值的每条记录创建一个单独的警报。 这些警报规则非常适用于 VM 见解收集的性能数据，因为它们可以为每台计算机创建单独的警报。


## <a name="alert-rule-walkthrough"></a>警报规则演练
本部分逐步介绍如何使用 VM 见解提供的性能数据来创建指标度量警报规则。 可以将此基本过程与各种日志查询一起使用，以发出针对不同性能计数器的警报。

首先，按照[使用 Azure Monitor 创建、查看和管理日志警报](../alerts/alerts-log.md)中的过程创建一个新的警报规则。 对于“资源”，请选择 Azure Monitor VM 在你的订阅中使用的 Log Analytics 工作区。 由于日志警报规则的目标资源始终是一个 Log Analytics 工作区，因此日志查询必须包含针对特定虚拟机或虚拟机规模集的任何筛选器。 

对于警报规则的“条件”，请使用[下面部分](#sample-alert-queries)的查询之一作为“搜索查询”。 查询必须返回一个名为“AggregatedValue”的数值属性。 它应该按计算机汇总数据，以便你可以为每个超过阈值的虚拟机创建单独的警报。

在“警报逻辑”中，选择“指标度量”，然后提供“阈值”。 在“警报触发依据”中，指定在创建警报之前必须超过阈值的次数。 例如，如果处理器在超过阈值一次后又恢复正常，你可能并不在意，但如果处理器在多次连续度量中持续超过阈值，则你肯定会在意。

“评估依据”部分定义查询的运行频率以及查询的时间范围。 在下面所示的示例中，查询将会每 15 分钟运行一次，并评估过去 15 分钟内收集的性能值。


![指标度量警报规则](media/vminsights-alerts/metric-measurement-alert.png)

## <a name="sample-alert-queries"></a>示例警报查询
可以将以下查询用于使用 VM 见解收集的性能数据的指标度量警报规则。 每个查询都按计算机汇总数据，以便为其值超过阈值的每台计算机创建一个警报。

### <a name="cpu-utilization"></a>CPU 使用率

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "Processor" and Name == "UtilizationPercentage" 
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="available-memory-in-mb"></a>可用内存 (MB)

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Memory" and Name == "AvailableMB"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="available-memory-in-percentage"></a>可用内存（百分比）

```kusto
InsightsMetrics 
| where Origin == "vm.azm.ms" 
| where Namespace == "Memory" and Name == "AvailableMB" 
| extend TotalMemory = toreal(todynamic(Tags)["vm.azm.ms/memorySizeMB"])
| extend AvailableMemoryPercentage = (toreal(Val) / TotalMemory) * 100.0 
| summarize AggregatedValue = avg(AvailableMemoryPercentage) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="logical-disk-used---all-disks-on-each-computer"></a>使用的逻辑磁盘 - 每台计算机上的所有磁盘

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="logical-disk-used---individual-disks"></a>使用的逻辑磁盘 - 个别磁盘

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, Disk
```

### <a name="logical-disk-iops"></a>逻辑磁盘 IOPS

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "LogicalDisk" and Name == "TransfersPerSecond"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m) ), Computer, _ResourceId, Disk
```

### <a name="logical-disk-data-rate"></a>逻辑磁盘数据速率

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "LogicalDisk" and Name == "BytesPerSecond"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m) , Computer, _ResourceId, Disk
```

### <a name="network-interfaces-bytes-received---all-interfaces"></a>接收的网络接口字节数 - 所有接口

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="network-interfaces-bytes-received---individual-interfaces"></a>接收的网络接口字节数 - 个别接口

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface
```

### <a name="network-interfaces-bytes-sent---all-interfaces"></a>发送的网络接口字节数 - 所有接口

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="network-interfaces-bytes-sent---individual-interfaces"></a>发送的网络接口字节数 - 个别接口

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface
```

### <a name="virtual-machine-scale-set"></a>虚拟机规模集
通过订阅 ID、资源组和虚拟机规模集名称进行修改。

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachineScaleSets/my-vm-scaleset" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId
```

### <a name="specific-virtual-machine"></a>特定虚拟机
通过订阅 ID、资源组和 VM 名称进行修改。

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId =~ "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m)
```

### <a name="cpu-utilization-for-all-compute-resources-in-a-subscription"></a>订阅中所有计算资源的 CPU 利用率
通过订阅 ID 进行修改。

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" and (_ResourceId contains "/providers/Microsoft.Compute/virtualMachines/" or _ResourceId contains "/providers/Microsoft.Compute/virtualMachineScaleSets/")
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId
```

### <a name="cpu-utilization-for-all-compute-resources-in-a-resource-group"></a>资源组中所有计算资源的 CPU 利用率
通过订阅 ID 和资源组进行修改。

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/"
or _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachineScaleSets/" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId

```

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure Monitor 中的警报](../alerts/alerts-overview.md)。
- 详细了解[使用来自 VM 见解的数据进行的日志查询](vminsights-log-search.md)。
