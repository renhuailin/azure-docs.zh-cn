---
title: 监视 AKS 数据参考
description: 监视 AKS 时所需的重要参考资料
ms.service: container-service
ms.custom: subject-monitoring
ms.date: 07/29/2021
ms.topic: reference
ms.openlocfilehash: 9e8a12f8d3075bade1619ce766c74dc8cac23481
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124816063"
---
# <a name="monitoring-aks-data-reference"></a>监视 AKS 数据参考

有关收集和分析 AKS 监视数据的详细信息，请参阅[监视 AKS](monitor-aks.md)。

## <a name="metrics"></a>指标

下表列出了为 AKS 收集的平台指标。  请访问每个链接，获取每个特定类型指标的详细列表。

|指标类型 | 资源提供程序/类型命名空间<br/> 和到各个指标的链接 |
|-------|-----|
| 托管群集 | [Microsoft.ContainerService/managedClusters](../azure-monitor/essentials/metrics-supported.md#microsoftcontainerservicemanagedclusters)
| 连接的群集 | [microsoft.kubernetes/connectedClusters](../azure-monitor/essentials/metrics-supported.md#microsoftkubernetesconnectedclusters)
| 虚拟机| Microsoft.Compute/virtualMachines  |
| 虚拟机规模集 | [Microsoft.Compute/virtualMachineScaleSets](../azure-monitor/essentials/metrics-supported.md#microsoftcomputevirtualmachinescalesets)|
| 虚拟机规模集虚拟机 | [Microsoft.Compute/virtualMachineScaleSets/virtualMachines](../azure-monitor/essentials/metrics-supported.md#microsoftcomputevirtualmachinescalesetsvirtualmachines)|

有关详细信息，请参阅 [Azure Monitor 支持的所有平台指标的列表](../azure-monitor/essentials/metrics-supported.md)。

## <a name="metric-dimensions"></a>指标维度

下表列出了 AKS 指标的[维度](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics)。 

<!-- listed here /azure/azure-monitor/essentials/metrics-supported#microsoftcontainerservicemanagedclusters-->

| 维度名称 | 说明 |
| ------------------- | ----------------- |
| requestKind | 由“即时请求”等指标用于按请求类型拆分。 |
| condition | 由“各种节点条件的状态”、“处于就绪状态的 Pod 数”等指标用于按条件类型拆分 。 |
| 状态 | 由“各种节点条件的状态”等指标用于按条件状态拆分。 |
| status2 | 由“各种节点条件的状态”等指标用于按条件状态拆分。  |
| 节点 | 由“CPU 使用率(毫核心)”等指标用于按节点名称拆分。 |
| phase | 由“按相位划分的 Pod 数”等指标用于按 Pod 的相位进行拆分。 |
| 命名空间 | 由“按相位划分的 Pod 数”等指标用于按 Pod 的命名空间进行拆分。 |
| Pod | 由“按相位划分的 Pod 数”等指标用于按 Pod 的名称进行拆分。 |
| nodepool | 由“磁盘已用字节数”等指标用于按节点池的名称拆分。 |
| 设备 | 由“磁盘已用字节数”等指标用于按设备的名称拆分。 |

## <a name="resource-logs"></a>资源日志

下表列出了可为 AKS 收集的资源日志类别。 这些是 AKS 控制平面组件的日志。 有关创建诊断设置以收集这些日志的信息，以及有关要启用的设置的建议，请参阅[配置监视](monitor-aks.md#configure-monitoring)。 有关查询示例，请参阅[如何从容器见解查询日志](../azure-monitor/containers/container-insights-log-query.md#resource-logs)。

有关参考，请参阅 [Azure Monitor 支持的所有资源日志类别类型](../azure-monitor/essentials/resource-logs-schema.md)列表。 

| 类别                | 说明 |
|:---|:---|
| cluster-autoscaler       | 了解 AKS 群集为何需要扩展或缩减（可能在意料之外）。 此信息还有助于关联时间间隔，其间群集中可能发生了一些有趣的事情。 |
| 防护                   | 托管的 Azure Active Directory 和 Azure RBAC 审核。 对于托管的 Azure AD，这包括输入令牌和输出用户信息。对于Azure RBAC，这包括输入和输出访问评审。 |
| kube-apiserver          | 来自 API 服务器的日志。 |
| kube-audit              | 每个审核事件的审核日志数据，包括 get、list、create、update、delete、patch 和 post。 |
| kube-audit-admin        | kube-audit 日志类别的子集。 通过从日志中排除 get 和 list 审核事件，大大减少了日志数量。 |
| kube-controller-manager | 更深入地了解 Kubernetes 与 Azure 控制平面之间可能出现的问题。 一个典型的示例是 AKS 群集缺乏与 Azure 交互的权限。 |
| kube-scheduler          | 计划程序中的日志。 |
| AllMetrics              | 包括所有平台指标。 将这些值发送到 Log Analytics 工作区，可在其中使用日志查询对其他数据进行评估。 |

## <a name="azure-monitor-logs-tables"></a>Azure Monitor 日志表

本节介绍与 AKS 相关并可通过 Log Analytics 进行查询的所有 Azure Monitor 日志表。 



|资源类型 | 说明 |
|-------|-----|
| [Kubernetes 服务](/azure/azure-monitor/reference/tables/tables-resourcetype#kubernetes-services) | 访问此链接可获取 AKS 使用的所有表的列表及其结构说明。 |


有关所有 Azure Monitor 日志/Log Analytics 表的参考，请参阅 [Azure Monitor 日志表参考](/azure/azure-monitor/reference/tables/tables-resourcetype)。


## <a name="activity-log"></a>活动日志

下表列出了可能在[活动日志](../azure-monitor/essentials/activity-log.md)中创建的与 AKS 相关的一些示例操作。 使用活动日志可以跟踪信息，例如创建群集或更改群集配置的时间。 你可以在门户中查看此信息，或者创建一个活动日志警报，以便在事件发生时主动得到通知。

| 操作 | 说明 |
|:---|:---|
| Microsoft.ContainerService/managedClusters/write | 创建或更新托管群集 |
| Microsoft.ContainerService/managedClusters/delete | 删除托管群集 |
| Microsoft.ContainerService/managedClusters/listClusterMonitoringUserCredential/action | 列出 clusterMonitoringUser 凭据 |
| Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | 列出 clusterAdmin 凭据 |
| Microsoft.ContainerService/managedClusters/agentpools/write | 创建或更新代理池 |

有关可能的日志条目的完整列表，请参阅 [Microsoft.ContainerService 资源提供程序选项](../role-based-access-control/resource-provider-operations.md#microsoftcontainerservice)。

有关活动日志条目架构的详细信息，请参阅[活动日志架构](../azure-monitor/essentials/activity-log-schema.md)。 

## <a name="see-also"></a>另请参阅

- 有关如何监视 Azure AKS，请参阅[监视 Azure AKS](monitor-aks.md)。
- 有关监视 Azure 资源的详细信息，请参阅[使用 Azure Monitor 监视 Azure 资源](../azure-monitor/essentials/monitor-azure-resource.md)。