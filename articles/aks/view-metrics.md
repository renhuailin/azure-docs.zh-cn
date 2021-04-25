---
title: 查看 Azure Kubernetes Service (AKS) 群集指标
description: 查看 Azure Kubernetes Service (AKS) 群集指标。
services: container-service
ms.topic: article
ms.date: 03/30/2021
ms.openlocfilehash: 32d8745e0ea780b5f86588fabdc25b244cf2cd8e
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105975713"
---
# <a name="view-cluster-metrics-for-azure-kubernetes-service-aks"></a>查看 Azure Kubernetes Service (AKS) 群集指标

AKS 提供一组控制平面指标，包括 API 服务器和群集自动扩缩程序以及群集节点。 该组指标可用于监控群集健康状况并排除故障。 你可以通过 Azure 门户查看群集指标。

> [!NOTE]
> 该组 AKS 群集指标与 [Kubernetes 提供的指标][kubernetes-metrics]子集重叠。

## <a name="view-metrics-for-your-aks-cluster-using-the-azure-portal"></a>通过 Azure 门户查看 AKS 群集指标

查看 AKS 群集指标：

1. 登录[Azure 门户][azure-portal]并导航到 AKS 群集。
1. 在左侧窗格中的“监控”下选择“群集”。
1. 为要查看的指标创建图表。 例如，创建图表：
    1. 对于“作用域”，请选择群集。
    1. 对于“指标命名空间”，请选择“容器服务（托管）标准指标”。
    1. 对于“指标”，在“Pods”下选择“各阶段 Pod 数量”。
    1. 对于“聚合”，请选择“Avg”。

:::image type="content" source="media/metrics/metrics-chart.png" alt-text="{alt-text}":::

上例演示了“myAKSCluster”Pod 平均数的指标。

## <a name="available-metrics"></a>可用度量值

提供以下群集指标：

| 名称 | 组 | ID | 说明 |
| --- | --- | --- | ---- |
| 进行中的请求数 | API 服务器（预览版） |apiserver_current_inflight_requests | API 服务器当前活跃的即时请求最大数量（按请求类型）。 |
| 群集运行状况 | 群集自动缩放程序（预览版） | cluster_autoscaler_cluster_safe_to_autoscale | 确定群集自动缩放程序是否会对群集执行操作。 |
| 纵向缩减散热设备 | 群集自动缩放程序（预览版） | cluster_autoscaler_scale_down_in_cooldown | 确定纵向缩减是否在冷却时间内——在该时间范围内不会删除任何节点。 |
| 多余节点 | 群集自动缩放程序（预览版） | cluster_autoscaler_unneeded_nodes_count | 群集自动缩放程序将这些节点标记为要删除的候选项，并最终将其删除。 |
| 不可调度的 pod | 群集自动缩放程序（预览版） | cluster_autoscaler_unschedulable_pods_count | 目前群集不可调度的 pod 数。 |
| 托管群集中可用 CPU 内核的总数 | 节点 | kube_node_status_allocatable_cpu_cores | 托管群集可用 CPU 内核总数。 |
| 托管群集中可用内存的总量 | 节点 | kube_node_status_allocatable_memory_bytes | 托管群集可用内存总量。 |
| 各种节点条件的状态 | 节点 | kube_node_status_condition | 各种节点条件的状态 |
| CPU 使用率（毫核心） | 节点（预览版） | node_cpu_usage_millicores | 群集中 CPU 使用率（毫核心）的聚合度量。 |
| CPU 使用率百分比 | 节点（预览版） | node_cpu_usage_percentage | 整个群集的聚合平均 CPU 利用率，以百分比计量。 |
| 内存 RSS 字节数 | 节点（预览版） | node_memory_rss_bytes | 使用的容器 RSS 内存，以字节为单位。 |
| 内存 RSS 百分比 | 节点（预览版） | node_memory_rss_percentage | 使用的容器 RSS 内存，以百分比表示。 |
| 内存工作集字节数 | 节点（预览版） | node_memory_working_set_bytes | 使用的容器工作集内存（字节）。 |
| 内存工作集百分比 | 节点（预览版） | node_memory_working_set_percentage | 使用的容器工作集内存，以百分比表示。 |
| 磁盘已用字节数 | 节点（预览版） | node_disk_usage_bytes | 设备占用磁盘空间（字节）。 |
| 磁盘已用百分比 | 节点（预览版） | node_disk_usage_percentage | 设备使用的磁盘空间（百分比）。 |
| 网络（字节） | 节点（预览版） | node_network_in_bytes | 网络接收字节数。 |
| 网络传出字节数 | 节点（预览版） | node_network_out_bytes | 网络传输字节数。 |
| 就绪状态下的 Pod 数 | Pod | kube_pod_status_ready | “就绪”状态的 pod 数。 |
| 依据阶段的 Pod 数 | Pod | kube_pod_status_phase | 各阶段 Pod 数。 |

> [!IMPORTANT]
> 可在预览状态下更新或更改预览的指标，包括其名称和说明。

## <a name="next-steps"></a>后续步骤

除 AKS 群集指标外，还可在 AKS 群集上使用 Azure Monitor。 有关在 AKS 群集上使用 Azure Monitor 的详细信息，请参阅[用于容器的 Azure Monitor][aks-azure-monitory]。

[aks-azure-monitory]: ../azure-monitor/containers/container-insights-overview.md
[azure-portal]: https://portal.azure.com/
[kubernetes-metrics]: https://kubernetes.io/docs/concepts/cluster-administration/system-metrics/