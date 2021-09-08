---
title: 如何从容器见解查询日志
description: 容器见解收集指标和日志数据。本文介绍了这些记录并提供了示例查询。
ms.topic: conceptual
ms.date: 07/19/2021
ms.openlocfilehash: 07ff7a65f6f4ed0865a45e92288caa362051fd20
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123428013"
---
# <a name="how-to-query-logs-from-container-insights"></a>如何从容器见解查询日志

容器见解从容器主机和容器收集性能指标、清单数据和运行状况状态信息。 系统每三分钟收集一次数据，并转发到 Azure Monitor 中的 Log Analytics 工作区，在 Azure Monitor 中，可通过使用 [Log Analytics](../logs/log-analytics-overview.md) 将这些数据用于[日志查询](../logs/log-query-overview.md)。 此数据可应用于包括迁移计划、容量分析、发现和按需性能故障排除在内的方案。 Azure Monitor 日志有助于查找趋势、诊断瓶颈、预测或关联有助于确定是否最优执行当前群集配置的数据。

有关如何使用这些查询的信息，请参阅[在 Azure Monitor Log Analytics 中使用查询](../logs/queries.md)，有关如何使用 Log Analytics 运行查询并处理其结果的完整教程，请参阅 [Log Analytics 教程](../logs/log-analytics-tutorial.md)。

## <a name="open-log-analytics"></a>打开 Log Analytics
有多个选项可启动 Log Analytics，每个选项有不同的作用[范围](../logs/scope.md)。 若要访问工作区中的所有数据，请从“监视器”菜单中选择“日志” 。 若要将数据限制为单个 Kubernetes 群集，请从相关群集的菜单中选择“日志”。 

:::image type="content" source="media/container-insights-log-query/start-log-analytics.png" alt-text="启动 Log Analytics" lightbox="media/container-insights-log-query/start-log-analytics.png":::

## <a name="existing-log-queries"></a>现有的日志查询
无需了解如何编写日志查询也能使用 Log Analytics。 有多个预生成的查询可供选择，可直接运行这些查询而不作任何修改，也可以在其基础上创建自定义查询。 单击 Log Analytics 屏幕顶部的“查询”，并查看“资源类型”为“Kubernetes 服务”的查询  。 

:::image type="content" source="media/container-insights-log-query/log-analytics-queries.png" alt-text="针对 Kubernetes 的 Log Analytics 查询" lightbox="media/container-insights-log-query/log-analytics-queries.png":::

## <a name="container-tables"></a>容器表
请参阅 [Azure Monitor 表格引用](/azure/azure-monitor/reference/tables/tables-resourcetype#kubernetes-services)，查看 Container Insights 所用表格的列表及其详细说明。 所有表均可用于日志查询。


## <a name="example-log-queries"></a>示例日志查询
从一两个示例开始生成查询，然后修改它们以适应需求的做法通常很有用。 可使用以下示例查询进行试验，帮助生成更高级的查询：

### <a name="list-all-of-a-containers-lifecycle-information"></a>列出容器的所有生命周期信息

```kusto
ContainerInventory
| project Computer, Name, Image, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime
| render table
```

### <a name="kubernetes-events"></a>Kubernetes 事件

``` kusto
KubeEvents
| where not(isempty(Namespace))
| sort by TimeGenerated desc
| render table
```
### <a name="image-inventory"></a>映像清单

``` kusto
ContainerImageInventory
| summarize AggregatedValue = count() by Image, ImageTag, Running
```

### <a name="container-cpu"></a>容器 CPU

``` kusto
Perf
| where ObjectName == "K8SContainer" and CounterName == "cpuUsageNanoCores" 
| summarize AvgCPUUsageNanoCores = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName 
```

### <a name="container-memory"></a>容器内存

```kusto
Perf
| where ObjectName == "K8SContainer" and CounterName == "memoryRssBytes"
| summarize AvgUsedRssMemoryBytes = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName
```

### <a name="requests-per-minute-with-custom-metrics"></a>每分钟请求数（按照自定义指标）

```kusto
InsightsMetrics
| where Name == "requests_count"
| summarize Val=any(Val) by TimeGenerated=bin(TimeGenerated, 1m)
| sort by TimeGenerated asc<br> &#124; project RequestsPerMinute = Val - prev(Val), TimeGenerated
| render barchart 
```
### <a name="pods-by-name-and-namespace"></a>按名称和名称空间划分的 Pod

```kusto
let startTimestamp = ago(1h);
KubePodInventory
| where TimeGenerated > startTimestamp
| project ContainerID, PodName=Name, Namespace
| where PodName contains "name" and Namespace startswith "namespace"
| distinct ContainerID, PodName
| join
(
    ContainerLog
    | where TimeGenerated > startTimestamp
)
on ContainerID
// at this point before the next pipe, columns from both tables are available to be "projected". Due to both
// tables having a "Name" column, we assign an alias as PodName to one column which we actually want
| project TimeGenerated, PodName, LogEntry, LogEntrySource
| summarize by TimeGenerated, LogEntry
| order by TimeGenerated desc
```

### <a name="pod-scale-out-hpa"></a>Pod 横向扩展 (HPA)
返回每个部署中横向扩展的副本的数量。 使用 HPA 中配置的最大副本数来计算横向扩展百分比。


```kusto
let _minthreshold = 70; // minimum threshold goes here if you want to setup as an alert
let _maxthreshold = 90; // maximum threshold goes here if you want to setup as an alert
let startDateTime = ago(60m);
KubePodInventory
| where TimeGenerated >= startDateTime 
| where Namespace !in('default', 'kube-system') // List of non system namespace filter goes here.
| extend labels = todynamic(PodLabel)
| extend deployment_hpa = reverse(substring(reverse(ControllerName), indexof(reverse(ControllerName), "-") + 1))
| distinct tostring(deployment_hpa)
| join kind=inner (InsightsMetrics 
    | where TimeGenerated > startDateTime 
    | where Name == 'kube_hpa_status_current_replicas'
    | extend pTags = todynamic(Tags) //parse the tags for values
    | extend ns = todynamic(pTags.k8sNamespace) //parse namespace value from tags
    | extend deployment_hpa = todynamic(pTags.targetName) //parse HPA target name from tags
    | extend max_reps = todynamic(pTags.spec_max_replicas) // Parse maximum replica settings from HPA deployment
    | extend desired_reps = todynamic(pTags.status_desired_replicas) // Parse desired replica settings from HPA deployment
    | summarize arg_max(TimeGenerated, *) by tostring(ns), tostring(deployment_hpa), Cluster=toupper(tostring(split(_ResourceId, '/')[8])), toint(desired_reps), toint(max_reps), scale_out_percentage=(desired_reps * 100 / max_reps)
    //| where scale_out_percentage > _minthreshold and scale_out_percentage <= _maxthreshold
    )
    on deployment_hpa
```

### <a name="nodepool-scale-outs"></a>Nodepool 横向扩展 
返回每个节点池中的活动节点数。 计算可用的活动节点数和自动缩放器设置中的最大节点配置，以确定横向扩展百分比。 查看查询中的注释行，将其用于“结果数”警报规则。

```kusto
let nodepoolMaxnodeCount = 10; // the maximum number of nodes in your auto scale setting goes here.
let _minthreshold = 20;
let _maxthreshold = 90;
let startDateTime = 60m;
KubeNodeInventory
| where TimeGenerated >= ago(startDateTime)
| extend nodepoolType = todynamic(Labels) //Parse the labels to get the list of node pool types
| extend nodepoolName = todynamic(nodepoolType[0].agentpool) // parse the label to get the nodepool name or set the specific nodepool name (like nodepoolName = 'agentpool)'
| summarize nodeCount = count(Computer) by ClusterName, tostring(nodepoolName), TimeGenerated
//(Uncomment the below two lines to set this as an log search alert)
//| extend scaledpercent = iff(((nodeCount * 100 / nodepoolMaxnodeCount) >= _minthreshold and (nodeCount * 100 / nodepoolMaxnodeCount) < _maxthreshold), "warn", "normal")
//| where scaledpercent == 'warn'
| summarize arg_max(TimeGenerated, *) by nodeCount, ClusterName, tostring(nodepoolName)
| project ClusterName, 
    TotalNodeCount= strcat("Total Node Count: ", nodeCount),
    ScaledOutPercentage = (nodeCount * 100 / nodepoolMaxnodeCount),  
    TimeGenerated, 
    nodepoolName
```

### <a name="system-containers-replicaset-availability"></a>系统容器（副本集）可用性
返回系统容器（副本集）并报告不可用部分的百分比。 查看查询中的注释行，将其用于“结果数”警报规则。

```kusto
let startDateTime = 5m; // the minimum time interval goes here
let _minalertThreshold = 50; //Threshold for minimum and maximum unavailable or not running containers
let _maxalertThreshold = 70;
KubePodInventory
| where TimeGenerated >= ago(startDateTime)
| distinct ClusterName, TimeGenerated
| summarize Clustersnapshot = count() by ClusterName
| join kind=inner (
    KubePodInventory
    | where TimeGenerated >= ago(startDateTime)
    | where Namespace in('default', 'kube-system') and ControllerKind == 'ReplicaSet' // the system namespace filter goes here
    | distinct ClusterName, Computer, PodUid, TimeGenerated, PodStatus, ServiceName, PodLabel, Namespace, ContainerStatus
    | summarize arg_max(TimeGenerated, *), TotalPODCount = count(), podCount = sumif(1, PodStatus == 'Running' or PodStatus != 'Running'), containerNotrunning = sumif(1, ContainerStatus != 'running')
        by ClusterName, TimeGenerated, ServiceName, PodLabel, Namespace
    )
    on ClusterName
| project ClusterName, ServiceName, podCount, containerNotrunning, containerNotrunningPercent = (containerNotrunning * 100 / podCount), TimeGenerated, PodStatus, PodLabel, Namespace, Environment = tostring(split(ClusterName, '-')[3]), Location = tostring(split(ClusterName, '-')[4]), ContainerStatus
//Uncomment the below line to set for automated alert
//| where PodStatus == "Running" and containerNotrunningPercent > _minalertThreshold and containerNotrunningPercent < _maxalertThreshold
| summarize arg_max(TimeGenerated, *), c_entry=count() by PodLabel, ServiceName, ClusterName
//Below lines are to parse the labels to identify the impacted service/component name
| extend parseLabel = replace(@'k8s-app', @'k8sapp', PodLabel)
| extend parseLabel = replace(@'app.kubernetes.io/component', @'appkubernetesiocomponent', parseLabel)
| extend parseLabel = replace(@'app.kubernetes.io/instance', @'appkubernetesioinstance', parseLabel)
| extend tags = todynamic(parseLabel)
| extend tag01 = todynamic(tags[0].app)
| extend tag02 = todynamic(tags[0].k8sapp)
| extend tag03 = todynamic(tags[0].appkubernetesiocomponent)
| extend tag04 = todynamic(tags[0].aadpodidbinding)
| extend tag05 = todynamic(tags[0].appkubernetesioinstance)
| extend tag06 = todynamic(tags[0].component)
| project ClusterName, TimeGenerated,
    ServiceName = strcat( ServiceName, tag01, tag02, tag03, tag04, tag05, tag06),
    ContainerUnavailable = strcat("Unavailable Percentage: ", containerNotrunningPercent),
    PodStatus = strcat("PodStatus: ", PodStatus), 
    ContainerStatus = strcat("Container Status: ", ContainerStatus)
```

### <a name="system-containers-daemonsets-availability"></a>系统容器（守护程序集）可用性
返回系统容器（守护程序集）并报告不可用部分的百分比。 查看查询中的注释行，将其用于“结果数”警报规则。

```kusto
let startDateTime = 5m; // the minimum time interval goes here
let _minalertThreshold = 50; //Threshold for minimum and maximum unavailable or not running containers
let _maxalertThreshold = 70;
KubePodInventory
| where TimeGenerated >= ago(startDateTime)
| distinct ClusterName, TimeGenerated
| summarize Clustersnapshot = count() by ClusterName
| join kind=inner (
    KubePodInventory
    | where TimeGenerated >= ago(startDateTime)
    | where Namespace in('default', 'kube-system') and ControllerKind == 'DaemonSet' // the system namespace filter goes here
    | distinct ClusterName, Computer, PodUid, TimeGenerated, PodStatus, ServiceName, PodLabel, Namespace, ContainerStatus
    | summarize arg_max(TimeGenerated, *), TotalPODCount = count(), podCount = sumif(1, PodStatus == 'Running' or PodStatus != 'Running'), containerNotrunning = sumif(1, ContainerStatus != 'running')
        by ClusterName, TimeGenerated, ServiceName, PodLabel, Namespace
    )
    on ClusterName
| project ClusterName, ServiceName, podCount, containerNotrunning, containerNotrunningPercent = (containerNotrunning * 100 / podCount), TimeGenerated, PodStatus, PodLabel, Namespace, Environment = tostring(split(ClusterName, '-')[3]), Location = tostring(split(ClusterName, '-')[4]), ContainerStatus
//Uncomment the below line to set for automated alert
//| where PodStatus == "Running" and containerNotrunningPercent > _minalertThreshold and containerNotrunningPercent < _maxalertThreshold
| summarize arg_max(TimeGenerated, *), c_entry=count() by PodLabel, ServiceName, ClusterName
//Below lines are to parse the labels to identify the impacted service/component name
| extend parseLabel = replace(@'k8s-app', @'k8sapp', PodLabel)
| extend parseLabel = replace(@'app.kubernetes.io/component', @'appkubernetesiocomponent', parseLabel)
| extend parseLabel = replace(@'app.kubernetes.io/instance', @'appkubernetesioinstance', parseLabel)
| extend tags = todynamic(parseLabel)
| extend tag01 = todynamic(tags[0].app)
| extend tag02 = todynamic(tags[0].k8sapp)
| extend tag03 = todynamic(tags[0].appkubernetesiocomponent)
| extend tag04 = todynamic(tags[0].aadpodidbinding)
| extend tag05 = todynamic(tags[0].appkubernetesioinstance)
| extend tag06 = todynamic(tags[0].component)
| project ClusterName, TimeGenerated,
    ServiceName = strcat( ServiceName, tag01, tag02, tag03, tag04, tag05, tag06),
    ContainerUnavailable = strcat("Unavailable Percentage: ", containerNotrunningPercent),
    PodStatus = strcat("PodStatus: ", PodStatus), 
    ContainerStatus = strcat("Container Status: ", ContainerStatus)
```

## <a name="resource-logs"></a>资源日志
AKS 的资源日志存储在 [AzureDiagnostics](/azure/azure-monitor/reference/tables/azurediagnostics) 表中。可以使用“类别”列区分不同的日志。 有关每个类别的说明，请参阅 [AKS 参考资源日志](../../aks/monitor-aks-reference.md)。 以下示例需要诊断扩展，以将 AKS 群集的资源日志发送到 Log Analytics 工作区。 有关详细信息，请参阅[配置监视](../../aks/monitor-aks.md#configure-monitoring)。

### <a name="api-server-logs"></a>API 服务器日志

```kusto
AzureDiagnostics 
| where Category == "kube-apiserver"
```

### <a name="count-logs-for-each-category"></a>统计每个类别的日志

```kusto
AzureDiagnostics
| where ResourceType == "MANAGEDCLUSTERS"
| summarize count() by Category
```

## <a name="query-prometheus-metrics-data"></a>查询 Prometheus 指标数据

以下示例是一个 Prometheus 指标查询，显示每个节点每个磁盘每秒的磁盘读取次数。

```
InsightsMetrics
| where Namespace == 'container.azm.ms/diskio'
| where TimeGenerated > ago(1h)
| where Name == 'reads'
| extend Tags = todynamic(Tags)
| extend HostName = tostring(Tags.hostName), Device = Tags.name
| extend NodeDisk = strcat(Device, "/", HostName)
| order by NodeDisk asc, TimeGenerated asc
| serialize
| extend PrevVal = iif(prev(NodeDisk) != NodeDisk, 0.0, prev(Val)), PrevTimeGenerated = iif(prev(NodeDisk) != NodeDisk, datetime(null), prev(TimeGenerated))
| where isnotnull(PrevTimeGenerated) and PrevTimeGenerated != TimeGenerated
| extend Rate = iif(PrevVal > Val, Val / (datetime_diff('Second', TimeGenerated, PrevTimeGenerated) * 1), iif(PrevVal == Val, 0.0, (Val - PrevVal) / (datetime_diff('Second', TimeGenerated, PrevTimeGenerated) * 1)))
| where isnotnull(Rate)
| project TimeGenerated, NodeDisk, Rate
| render timechart

```

若要查看 Azure Monitor 擦除的按命名空间筛选的 Prometheus 指标，请指定“prometheus”。 下面是一个示例查询，演示如何从 `default` kubernetes 命名空间查看 Prometheus 指标。

```
InsightsMetrics 
| where Namespace == "prometheus"
| extend tags=parse_json(Tags)
| summarize count() by Name
```

Prometheus 数据也可直接按名称查询。

```
InsightsMetrics 
| where Namespace == "prometheus"
| where Name contains "some_prometheus_metric"
```

### <a name="query-config-or-scraping-errors"></a>查询配置或抓取错误

为了调查任何配置或抓取错误，下面的示例查询将返回 `KubeMonAgentEvents` 表中的信息性事件。

```
KubeMonAgentEvents | where Level != "Info" 
```

输出显示类似于以下示例的结果：

![通过代理记录信息性事件的查询结果](./media/container-insights-log-query/log-query-example-kubeagent-events.png)

## <a name="next-steps"></a>后续步骤

容器见解不包含预定义的警报集。 请查看[使用容器见解创建性能警报](./container-insights-log-alerts.md)，了解如何针对 CPU 和内存使用率过高的情况创建建议的警报，为 DevOps 或操作流程和过程提供支持。
