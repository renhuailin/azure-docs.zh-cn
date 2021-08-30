---
title: 来自容器见解的日志警报 | Microsoft Docs
description: 本文介绍如何从容器见解创建内存和 CPU 使用率的自定义日志警报。
ms.topic: conceptual
ms.date: 07/29/2021
ms.openlocfilehash: 82d6629ba903b656db9932b3c6bd6f5a2b92ea6a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121738486"
---
# <a name="how-to-create-log-alerts-from-container-insights"></a>如何从容器见解创建日志警报

容器见解可监视部署到托管或自托管 Kubernetes 群集的容器工作负荷的性能。 为了针对重要内容发出警报，本文介绍如何在以下场景中使用 AKS 群集创建基于日志的警报：

- 当群集节点上的 CPU 或内存利用率超过阈值时
- 当控制器中任何容器上的 CPU 或内存利用率超过阈值时（与相应资源中设置的限制相比）
- “未就绪”状态节点计数
- “失败”、“挂起”、“未知”、“正在运行”或“成功”Pod 阶段计数    
- 当群集节点上的可用磁盘空间超过阈值时

若要针对群集节点上的 CPU 或内存利用率过高或可用磁盘空间不足发出警报，请使用提供的查询来创建指标警报或指标度量警报。 虽然指标警报的延迟比日志警报低，但日志警报提供了高级查询和更复杂的功能。 日志警报查询通过使用 now 运算符并将时间往过去推一个小时，将某个日期/时间与当前时间进行比较。 （容器见解以协调世界时 (UTC) 格式存储所有日期。）

> [!IMPORTANT]
> 大多数警报规则会产生成本，具体成本取决于规则类型、规则包含的维度数量以及规则运行的频率。 创建任何警报规则之前，请参阅 [Azure Monitor 定价](https://azure.microsoft.com/pricing/details/monitor/)中的“警报规则”。

如果你不熟悉 Azure Monitor 警报，请在开始之前参阅 [Microsoft Azure 中的警报概述](../alerts/alerts-overview.md)。 若要详细了解使用日志查询的警报，请参阅 [Azure Monitor 中的日志警报](../alerts/alerts-unified-log.md)。 有关指标警报的详细信息，请参阅 [Azure Monitor 中的指标警报](../alerts/alerts-metric-overview.md)。

## <a name="log-query-measurements"></a>日志查询度量
日志查询警报可以对日志查询结果执行两种不同类型的度量，每种度量支持采用不同的方案监视虚拟机。

[指标度量](../alerts/alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value)为查询结果中数值超过警报规则定义阈值的每条记录创建单独的警报。 这些方案非常适合用于 CPU 等数值数据。

[结果数](../alerts/alerts-unified-log.md#count-of-the-results-table-rows)在查询至少返回指定数量的记录时创建单个警报。 这些方案非常适合用于非数值数据，以及分析多台计算机的性能趋势。 如果你想要最大程度地减少警报数，或尽可能仅在多个组件发生相同错误情况时创建警报，可以选择此策略。

> [!NOTE]
> 以资源为中心的日志警报规则（目前为公共预览版）将会简化日志查询警报，并取代当前由指标度量查询提供的功能。 可以使用 AKS 群集作为规则的目标，从而更好地将此群集识别为受影响资源。 资源中心日志查询警报正式发布后，将更新此方案中的指导。

## <a name="create-a-log-query-alert-rule"></a>创建日志查询警报规则
[日志查询警报度量的比较](../vm/monitor-virtual-machine-alerts.md#comparison-of-log-query-alert-measures)为适用于每种度量的日志查询警报规则提供了完整演练，包括支持每种度量的日志查询的比较。 可以遵循与此相同的过程，使用类似于本文的查询为 AKS 群集创建警报规则。

## <a name="resource-utilization"></a>资源利用率 

每分钟的平均 CPU 利用率，即成员节点的平均 CPU 利用率（指标度量）

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuCapacityNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```

每分钟的平均内存利用率，即成员节点的平均内存利用率（指标度量）

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryCapacityBytes';
let usageCounterName = 'memoryRssBytes';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```


>[!IMPORTANT]
>以下查询使用占位符值 \<your-cluster-name> 和 \<your-controller-name> 来表示群集和控制器。 设置警报时，请将这些占位符替换为环境特定的值。

每分钟控制器中所有容器的平均 CPU 利用率，即控制器中每个容器实例的平均 CPU 利用率（指标度量）

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuLimitNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

每分钟控制器中所有容器的平均内存利用率，即控制器中每个容器实例的平均内存利用率（指标度量）

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryLimitBytes';
let usageCounterName = 'memoryRssBytes';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

## <a name="resource-availability"></a>资源可用性 

处于“就绪”和“未就绪”状态的节点和计数（指标度量）

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let clusterName = '<your-cluster-name>';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| distinct ClusterName, Computer, TimeGenerated
| summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName, Computer
| join hint.strategy=broadcast kind=inner (
    KubeNodeInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | summarize TotalCount = count(), ReadyCount = sumif(1, Status contains ('Ready'))
                by ClusterName, Computer,  bin(TimeGenerated, trendBinSize)
    | extend NotReadyCount = TotalCount - ReadyCount
) on ClusterName, Computer, TimeGenerated
| project   TimeGenerated,
            ClusterName,
            Computer,
            ReadyCount = todouble(ReadyCount) / ClusterSnapshotCount,
            NotReadyCount = todouble(NotReadyCount) / ClusterSnapshotCount
| order by ClusterName asc, Computer asc, TimeGenerated desc
```
以下查询基于所有阶段返回 Pod 阶段计数：“失败”、“挂起”、“未知”、“正在运行”或“成功”。      

```kusto
let endDateTime = now(); 
let startDateTime = ago(1h);
let trendBinSize = 1m;
let clusterName = '<your-cluster-name>';
KubePodInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ClusterName == clusterName
    | distinct ClusterName, TimeGenerated
    | summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName
    | join hint.strategy=broadcast (
        KubePodInventory
        | where TimeGenerated < endDateTime
        | where TimeGenerated >= startDateTime
        | summarize PodStatus=any(PodStatus) by TimeGenerated, PodUid, ClusterName
        | summarize TotalCount = count(),
                    PendingCount = sumif(1, PodStatus =~ 'Pending'),
                    RunningCount = sumif(1, PodStatus =~ 'Running'),
                    SucceededCount = sumif(1, PodStatus =~ 'Succeeded'),
                    FailedCount = sumif(1, PodStatus =~ 'Failed')
                by ClusterName, bin(TimeGenerated, trendBinSize)
    ) on ClusterName, TimeGenerated
    | extend UnknownCount = TotalCount - PendingCount - RunningCount - SucceededCount - FailedCount
    | project TimeGenerated,
              TotalCount = todouble(TotalCount) / ClusterSnapshotCount,
              PendingCount = todouble(PendingCount) / ClusterSnapshotCount,
              RunningCount = todouble(RunningCount) / ClusterSnapshotCount,
              SucceededCount = todouble(SucceededCount) / ClusterSnapshotCount,
              FailedCount = todouble(FailedCount) / ClusterSnapshotCount,
              UnknownCount = todouble(UnknownCount) / ClusterSnapshotCount
| summarize AggregatedValue = avg(PendingCount) by bin(TimeGenerated, trendBinSize)
```

>[!NOTE]
>若要针对特定的 Pod 阶段（例如“挂起”、“失败”或“未知”）发出警报，请修改查询的最后一行。   例如，若要针对“失败计数”发出警报，请使用： <br/>`| summarize AggregatedValue = avg(FailedCount) by bin(TimeGenerated, trendBinSize)`

以下查询返回可用空间超过 90% 的已用群集节点磁盘。 若要获取群集 ID，请首先运行以下查询并从 `ClusterId` 属性中复制值：

```kusto
InsightsMetrics
| extend Tags = todynamic(Tags)            
| project ClusterId = Tags['container.azm.ms/clusterId']   
| distinct tostring(ClusterId)   
``` 

```kusto
let clusterId = '<cluster-id>';
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
InsightsMetrics
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where Origin == 'container.azm.ms/telegraf'            
| where Namespace == 'container.azm.ms/disk'            
| extend Tags = todynamic(Tags)            
| project TimeGenerated, ClusterId = Tags['container.azm.ms/clusterId'], Computer = tostring(Tags.hostName), Device = tostring(Tags.device), Path = tostring(Tags.path), DiskMetricName = Name, DiskMetricValue = Val   
| where ClusterId =~ clusterId       
| where DiskMetricName == 'used_percent'
| summarize AggregatedValue = max(DiskMetricValue) by bin(TimeGenerated, trendBinSize)
| where AggregatedValue >= 90
```



单个容器的重启次数（结果数量）<br>
当过去 10 分钟内单个系统容器的重启次数超过阈值时发出警报。

 
```kusto
let _threshold = 10m; 
let _alertThreshold = 2;
let Timenow = (datetime(now) - _threshold); 
let starttime = ago(5m); 
KubePodInventory
| where TimeGenerated >= starttime
| where Namespace in ('default', 'kube-system') // the namespace filter goes here
| where ContainerRestartCount > _alertThreshold
| extend Tags = todynamic(ContainerLastStatus)
| extend startedAt = todynamic(Tags.startedAt)
| where startedAt >= Timenow
| summarize arg_max(TimeGenerated, *) by Name
```

## <a name="next-steps"></a>后续步骤

- 请参阅[日志查询示例](container-insights-log-query.md)，以查看预定义的查询，以及用于发警报、可视化或分析群集的评估或自定义示例。

- 若要详细了解 Azure Monitor 以及如何监视 Kubernetes 群集的其他方面，请参阅[查看 Kubernetes 群集性能](container-insights-analyze.md)和[查看 Kubernetes 群集运行状况](./container-insights-overview.md)。