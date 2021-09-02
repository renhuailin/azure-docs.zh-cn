---
title: 使用 Azure Monitor 监视虚拟机：警报
description: 了解如何使用 Azure Monitor 从虚拟机及其来宾工作负载中创建警报。
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/21/2021
ms.openlocfilehash: b272d4cb11ab948043f6c47b5be12fc0488d070f
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122444185"
---
# <a name="monitor-virtual-machines-with-azure-monitor-alerts"></a>使用 Azure Monitor 监视虚拟机：警报

本文是[在 Azure Monitor 中监视虚拟机及其工作负载](monitor-virtual-machine.md)方案的一部分。 文中对于为虚拟机及其来宾操作系统创建警报规则提供了指导。 [Azure Monitor 中的警报](../alerts/alerts-overview.md)会主动通知你在监视数据中发现的值得关注的数据和模式。 虚拟机没有预配置的警报规则，但你可以依据 VM 见解收集的数据创建自己的警报规则。 

> [!NOTE]
> 本文中说明的警报不包括由 [Azure Monitor 为 VM 来宾运行状况](vminsights-health-overview.md)（当前为公共预览版中的功能）所创建的警报。 由于此功能即将正式发布，因此将合并警报指南。

> [!IMPORTANT]
> 大多数警报规则都需要付费，费用取决于规则的类型、规则包含的维度数量以及规则运行的频率。 创建任何警报规则之前，请参阅 [Azure Monitor 定价](https://azure.microsoft.com/pricing/details/monitor/)中的警报规则。

## <a name="choose-the-alert-type"></a>选择警报类型
Azure Monitor 中最常见的警报规则类型是[指标警报](../alerts/alerts-metric.md)和[日志查询警报](../alerts/alerts-log-query.md)。 为特定方案创建的警报规则类型取决于触发警报的数据所在的位置。 你可能会遇到这种情况：某一特定警报方案的数据在指标和日志中都有提供，而你需要决定使用哪种规则类型。 你还可以灵活地收集某些数据，并由你所决定的警报规则类型来决定采用何种数据收集方法。

通常情况下，最佳策略是尽可能使用指标警报而不是日志警报，因为指标警报响应更迅速，状态也更稳定。 若要使用指标警报，触发警报的数据必须在指标中是可用的。 VM 见解当前会将其所有数据发送到日志，因此必须安装 Azure Monitor 代理，以便对来宾操作系统的数据使用指标警报。 当指标数据在指标中不可用，或者要求的逻辑超过了指标警报规则相对简单的逻辑时，请对指标数据使用日志查询警报。

### <a name="metric-alert-rules"></a>指标警报规则
[指标警报规则](../alerts/alerts-metric.md)可在特定指标超过阈值时发出警报。 例如，当计算机 CPU 的使用率较高时。 指标警报规则的目标可以是特定的计算机、资源组或订阅。 在此实例中，你可以创建一项规则，将其应用于一组计算机。

适用于虚拟机的指标规则可以使用以下数据：

- 用于 Azure 虚拟机的主机指标，这些指标会自动收集。 
- Azure Monitor 代理从来宾操作系统中收集的指标。 

> [!NOTE]
> 当 VM 见解支持 Azure Monitor 代理（当前为公共预览版中的功能）时，VM 见解会将来宾操作系统的性能数据发送到指标，这样你就可以使用指标警报。

### <a name="log-alerts"></a>日志警报
[日志警报](../alerts/alerts-metric.md)可以对日志查询结果执行两种不同类型的度量，每种度量都支持不同的方案对虚拟机进行监视：

- [指标度量](../alerts/alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value)：为查询结果中数值超过警报规则定义阈值的每条记录创建单独警报。 指标度量非常适合度量非数值数据（例如 Log Analytics 代理收集的 Windows 和 Syslog 事件），也适合分析多台计算机上的性能趋势。
- [结果数](../alerts/alerts-unified-log.md#count-of-the-results-table-rows)：在查询至少返回指定数量的记录时创建单个警报。 结果数度量非常适合度量非数值数据（例如 [Log Analytics 代理](../agents/log-analytics-agent.md)收集的 Windows 和 Syslog 事件），也适合分析多台计算机上的性能趋势。 如果想要最大程度地减少警报数，或尽可能仅在多台计算机发生相同错误情况时创建警报，你可以选择此策略。

### <a name="target-resource-and-impacted-resource"></a>目标资源和受影响资源

> [!NOTE]
> 以资源为中心的日志警报规则（当前为公共预览版中的功能）可简化针对虚拟机的日志查询警报，并替换当前由指标度量查询提供的功能。 你可以将计算机用作规则的目标，从而更好地将计算机标识为受影响的资源。 你还可以将单个警报规则应用于特定资源组或说明中的所有计算机。 资源中心日志查询警报正式发布后，将更新此方案中的指导。
> 
Azure Monitor 中的每个警报都有一个“受影响资源”属性，该属性由规则的目标定义。 对于指标警报规则，受影响资源是计算机，这可以让你在标准警报视图中轻松识别该计算机。 日志查询警报与工作区资源关联而不是与计算机关联，即使在使用为每个计算机创建警报的指标度量警报时，也不会改变这一点。 你需要通过查看警报的详细信息来查看受影响的计算机。

计算机名称存储在受影响资源的属性中，你可以在警报的详细信息中查看该属性。 该属性还会在警报发送的电子邮件中显示为维度。

:::image type="content" source="media/monitor-virtual-machines/alert-metric-measurement.png" alt-text="显示警报及受影响资源的屏幕截图。" lightbox="media/monitor-virtual-machines/alert-metric-measurement.png":::

你可能希望有一个视图可以列出警报以及受影响的计算机。 你可以使用自定义工作簿，该工作簿使用自定义 [Resource Graph](../../governance/resource-graph/overview.md) 提供此视图。 请使用以下查询显示警报，并使用工作簿中的 Azure Resource Graph 数据源。

```kusto
alertsmanagementresources
| extend dimension = properties.context.context.condition.allOf
| mv-expand dimension
| extend dimension = dimension.dimensions
| mv-expand dimension
| extend Computer = dimension.value
| extend AlertStatus = properties.essentials.alertState
| summarize count() by Alert=name, tostring(AlertStatus), tostring(Computer)
| project Alert, AlertStatus, Computer
```
## <a name="common-alert-rules"></a>常用警报规则
以下部分列出了 Azure Monitor 中适用于虚拟机的常见警报规则。 指标警报和日志指标度量警报的详细信息也已分别提供。 有关应使用哪种类型警报的指导，请参阅[选择警报类型](#choose-the-alert-type)。

如果不熟悉在 Azure Monitor 中创建警报规则的过程，请参阅以下文章了解相关指导：

- [使用 Azure Monitor 创建、查看和管理指标警报](../alerts/alerts-metric.md)
- [使用 Azure Monitor 创建、查看和管理日志警报](../alerts/alerts-log.md)

### <a name="machine-unavailable"></a>计算机不可用
最基本的要求是在计算机不可用时发送警报。 计算机可能停止运行，来宾操作系统可能无响应，代理也可能无响应。 有多种方式可配置此警报，但最常用的方式是使用从 Log Analytics 代理发送的检测信号。 

#### <a name="log-query-alert-rules"></a>日志查询警报规则
日志查询警报使用[检测信号表](/azure/azure-monitor/reference/tables/heartbeat)，该表应会每分钟从每台计算机中获取一条检测信号记录。 

单独警报

将指标度量规则用于以下查询。

```kusto
Heartbeat
| summarize TimeGenerated=max(TimeGenerated) by Computer
| extend Duration = datetime_diff('minute',now(),TimeGenerated)
| summarize AggregatedValue = min(Duration) by Computer, bin(TimeGenerated,5m)
```

单个警报

将多个结果警报用于以下查询。

```kusto
Heartbeat
| summarize LastHeartbeat=max(TimeGenerated) by Computer 
| where LastHeartbeat < ago(5m)
```

#### <a name="metric-alert-rules"></a>指标警报规则
每个 Log Analytics 工作区都包含一个称为“检测信号”的指标。 连接到该工作区的每个虚拟机会每分钟发送一个检测信号指标值。 由于计算机是一个指标维度，因此当有任何计算机无法发送检测信号时，就可以触发警报。 请将“聚合类型”设置为“计数”，并将“阈值”与“计算粒度”相匹配。

### <a name="cpu-alerts"></a>CPU 警报
#### <a name="metric-alert-rules"></a>指标警报规则

| 目标 | 指标 |
|:---|:---|
| 主机 | CPU 百分比 |
| Windows 来宾 | \Processor Information(_Total)\% Processor Time |
| Linux 来宾 | cpu/usage_active |

#### <a name="log-alert-rules"></a>日志警报规则

**CPU 使用率** 

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer
```

**订阅中所有计算资源的 CPU 利用率**

```kusto
 InsightsMetrics
 | where Origin == "vm.azm.ms"
 | where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" and (_ResourceId contains "/providers/Microsoft.Compute/virtualMachines/" or _ResourceId contains "/providers/Microsoft.Compute/virtualMachineScaleSets/") 
 | where Namespace == "Processor" and Name == "UtilizationPercentage"<br>\| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId
```

**资源组中所有计算资源的 CPU 利用率** 

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/" or _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachineScaleSets/"
| where Namespace == "Processor" and Name == "UtilizationPercentage"<br>\| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId 
```

### <a name="memory-alerts"></a>内存警报

#### <a name="metric-alert-rules"></a>指标警报规则

| 目标 | 指标 |
|:---|:---|
| Windows 来宾 | \Memory\% Committed Bytes in Use<br>\Memory\Available Bytes |
| Linux 来宾 | mem/available<br>mem/available_percent |

#### <a name="log-alert-rules"></a>日志警报规则

可用内存 (MB) 

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Memory" and Name == "AvailableMB"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer
```

可用内存（百分比） 

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Memory" and Name == "AvailableMB"
| extend TotalMemory = toreal(todynamic(Tags)["vm.azm.ms/memorySizeMB"])<br>\| extend AvailableMemoryPercentage = (toreal(Val) / TotalMemory) * 100.0
| summarize AggregatedValue = avg(AvailableMemoryPercentage) by bin(TimeGenerated, 15m), Computer  
``` 

### <a name="disk-alerts"></a>磁盘警报

#### <a name="metric-alert-rules"></a>指标警报规则

| 目标 | 指标 |
|:---|:---|
| Windows 来宾 | \Logical Disk\(_Total)\% Free Space<br>\Logical Disk\(_Total)\Free Megabytes |
| Linux 来宾 | disk/free<br>disk/free_percent |

#### <a name="log-query-alert-rules"></a>日志查询警报规则

**使用的逻辑磁盘 - 每台计算机上的所有磁盘** 

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

**使用的逻辑磁盘 - 个别磁盘** 

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, Disk 
```

逻辑磁盘 IOPS

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "LogicalDisk" and Name == "TransfersPerSecond"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m) ), Computer, _ResourceId, Disk 
```
**逻辑磁盘数据速率**

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "LogicalDisk" and Name == "BytesPerSecond"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m) , Computer, _ResourceId, Disk 
```

## <a name="network-alerts"></a>网络警报

#### <a name="metric-alert-rules"></a>指标警报规则

| 目标 | 指标 |
|:---|:---|
| Windows 来宾 | \Network Interface\Bytes Sent/sec<br>\Logical Disk\(_Total)\Free Megabytes |
| Linux 来宾 | disk/free<br>disk/free_percent |

### <a name="log-query-alert-rules"></a>日志查询警报规则

**接收的网络接口字节数 - 所有接口**

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId  
```

**接收的网络接口字节数 - 个别接口**

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface 
```

**发送的网络接口字节数 - 所有接口**

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

**发送的网络接口字节数 - 个别接口**

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface 
```

## <a name="comparison-of-log-query-alert-measures"></a>日志查询警报度量值的比较
为了比较两种日志警报度量值的行为，下面将演练在虚拟机 CPU 使用率超过 80% 时，每种行为创建警报的过程。 所需数据位于 [InsightsMetrics 表](/azure/azure-monitor/reference/tables/insightsmetrics)中。 以下查询将返回需要对警报进行评估的记录。 每种类型的警报规则都使用此查询的变体。

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "Processor" and Name == "UtilizationPercentage" 
```

### <a name="metric-measurement"></a>指标度量
指标度量的度量值将为查询中超过警报规则定义阈值的每条记录创建单独警报。 这些警报规则非常适用于虚拟机的性能数据，因为这些规则可为每台计算机创建独立的警报。 针对此度量值的日志查询需要返回每台计算机的值。 警报规则中的阈值决定返回值是否应触发警报。

> [!NOTE]
> 以资源为中心的日志警报规则（当前为公共预览版中的功能）可简化针对虚拟机的日志查询警报，并替换当前由指标度量查询提供的功能。 你可以将计算机用作规则的目标，从而更好地将计算机标识为受影响的资源。 你还可以将单个警报规则应用于特定资源组或说明中的所有计算机。 资源中心日志查询警报正式发布后，将更新此方案中的指导。

#### <a name="query"></a>查询
对使用指标度量规则的查询必须包含每台计算机的记录，记录带有一个数值属性，名为 AggregatedValue。 该值将与警报规则中的阈值进行比较。 查询不需要将该值与阈值进行比较，因为阈值是在警报规则中定义的。

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "Processor" and Name == "UtilizationPercentage" 
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer
```

#### <a name="alert-rule"></a>警报规则
从 Azure Monitor 菜单中选择“日志”以打开 Log Analytics。 请确保为作用域选择了正确的工作区。 如果没有，请单击左上角的“选择作用域”，然后选择正确的工作区。 粘贴包含所需逻辑的查询，然后选择“运行”以验证查询是否返回正确的结果。

:::image type="content" source="media/monitor-virtual-machines/log-alert-metric-query-results.png" alt-text="显示指标度量警报查询结果的屏幕截图。" lightbox="media/monitor-virtual-machines/log-alert-metric-query-results.png":::

选择“新建警报规则”以创建当前查询的规则。 该规则将为“资源”使用你的工作区。

选择“条件”以查看配置。 查询中已填充了返回值的图形视图，这些返回值是从每台计算机的查询中获取的值。 从下拉列表中的“透视对象”中选择计算机。

向下滚动到“警报逻辑”，然后为“依据”属性选择“指标度量”。 由于希望在使用率超过 80% 时触发警报，请将“聚合值”设置为“大于”，并将“阈值”设置为“80”。

向下滚动到“警报逻辑”，然后为“依据”属性选择“指标度量”。 请提供一个阈值，用于与查询的返回值进行比较。 在此示例中，使用的阈值是 80。 在“警报触发依据”中，指定在创建警报之前必须超过阈值的次数。 例如，如果处理器在超过阈值一次后又恢复正常，你可能并不在意，但如果处理器在多次连续度量中持续超过阈值，你肯定会关注。 在此示例中，请将“连续违反次数”设置为 3。

向下滚动到“计算依据”。 “时间段”用于指定查询的时间跨度。 指定该值为 15 分钟，这意味着查询仅使用过去 15 分钟内收集的数据。 “频率”用于指定运行查询的频繁程度。 该值越小，警报规则的响应度就越高，但成本也越高。 指定该值为 15，即每 15 分钟运行一次查询。

:::image type="content" source="media/monitor-virtual-machines/log-alert-metric-rule.png" alt-text="显示指标度量警报查询规则的屏幕截图。" lightbox="media/monitor-virtual-machines/log-alert-metric-rule.png":::

### <a name="number-of-results-rule"></a>结果数规则
结果数规则可在查询至少返回指定数量的记录时创建单个警报。 这种警报规则类型中的日志查询通常会标识警报条件，而警报规则的阈值将决定是否需返回了足够数量的记录。

#### <a name="query"></a>查询
此示例中，CPU 使用率的阈值已包含在查询中。 查询返回的记录数量是超过该阈值的计算机数量。 警报规则的阈值是触发警报所需的最少计算机数量。 如果希望在单个计算机出错时发出警报，则警报规则的阈值应设置为零。

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "Processor" and Name == "UtilizationPercentage" 
| summarize AverageUtilization = avg(Val) by Computer
| where AverageUtilization > 80
```

#### <a name="alert-rule"></a>警报规则
从 Azure Monitor 菜单中选择“日志”以打开 Log Analytics。 请确保为作用域选择了正确的工作区。 如果没有，请单击左上角的“选择作用域”，然后选择正确的工作区。 粘贴包含所需逻辑的查询，然后选择“运行”以验证查询是否返回正确的结果。 你当前可能没有超过阈值的计算机，因此请暂时更改为较低的阈值以验证结果。 然后，在创建警报规则之前设置适当的阈值。

:::image type="content" source="media/monitor-virtual-machines/log-alert-number-query-results.png" alt-text="显示结果数警报查询结果的屏幕截图。" lightbox="media/monitor-virtual-machines/log-alert-number-query-results.png":::

选择“新建警报规则”以创建当前查询的规则。 该规则将为“资源”使用你的工作区。

选择“条件”以查看配置。 查询中已填充了过去几分钟内从该查询返回的记录数的图形视图。 

向下滚动到“警报逻辑”，然后为“依据”属性选择“结果数”。 对于此示例，你希望只要有任何记录返回就触发警报，这意味着至少有一台虚拟机的处理器利用率应高于 80%。 请为“运算符”选择“大于”，并为“阈值”选择“0”。

向下滚动到“计算依据”。 “时间段”用于指定查询的时间跨度。 指定该值为 15 分钟，这意味着查询仅使用过去 15 分钟内收集的数据。 “频率”用于指定运行查询的频繁程度。 该值越小，警报规则的响应度就越高，但成本也越高。 指定该值为 15，即每 15 分钟运行一次查询。

:::image type="content" source="media/monitor-virtual-machines/log-alert-number-rule.png" alt-text="显示结果数警报查询规则的屏幕截图。" lightbox="media/monitor-virtual-machines/log-alert-number-rule.png":::

## <a name="next-steps"></a>后续步骤

* [监视虚拟机上运行的工作负载。](monitor-virtual-machine-workloads.md)
* [分析针对虚拟机收集的监视数据。](monitor-virtual-machine-analyze.md)
