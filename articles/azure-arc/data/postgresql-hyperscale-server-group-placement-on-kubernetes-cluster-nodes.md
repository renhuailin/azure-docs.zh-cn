---
title: Kubernetes 群集节点上的超大规模 PostgreSQL 服务器组的放置
description: 说明如何将形成超大规模 PostgreSQL 服务器组的 PostgreSQL 实例放置在 Kubernetes 群集节点上
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: b99df2f95838fe1913876a3e6a138935806df836
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121724659"
---
# <a name="azure-arc-enabled-postgresql-hyperscale-server-group-placement"></a>已启用 Azure Arc 的 PostgreSQL 超大规模服务器组放置

本文将举例说明如何将已启用 Azure Arc 的 PostgreSQL 超大规模服务器组的 PostgreSQL 实例放置在托管它们的 Kubernetes 群集的物理节点上。 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="configuration"></a>配置

在此示例中，我们使用的是具有四个物理节点的 Azure Kubernetes 服务 (AKS) 群集。 

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/1_cluster_portal.png" alt-text="Azure 门户中的 4 节点 AKS 群集":::

列出 Kubernetes 群集的物理节点。 运行以下命令：

```console
kubectl get nodes
```

`kubectl` 返回 Kubernetes 群集中的四个物理节点：

```output
NAME                                STATUS   ROLES   AGE   VERSION
aks-agentpool-42715708-vmss000000   Ready    agent   11h   v1.17.9
aks-agentpool-42715708-vmss000001   Ready    agent   11h   v1.17.9
aks-agentpool-42715708-vmss000002   Ready    agent   11h   v1.17.9
aks-agentpool-42715708-vmss000003   Ready    agent   11h   v1.17.9
```

体系结构可以表示为：

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/2_logical_cluster.png" alt-text="Kubernetes 群集中分组的 4 个节点的逻辑表示":::

Kubernetes 群集托管一个 Azure Arc 数据控制器和一个已启用 Azure Arc 的 PostgreSQL 超大规模服务器组。 此服务器组由三个 PostgreSQL 实例构成：一个协调器和两个工作器。

用命令列出 Pod：

```console
kubectl get pods -n arc3
```
`kubectl` 返回：

```output
NAME                 READY   STATUS    RESTARTS   AGE
…
postgres01c-0         3/3     Running   0          9h
postgres01w-0         3/3     Running   0          9h
postgres01w-1         3/3     Running   0          9h
```
其中每个 Pod 都托管一个 PostgreSQL 实例。 这些 Pod 共同构成了已启用 Azure Arc 的 PostgreSQL 超大规模服务器组：

```output
Pod name        Role in the server group
postgres01c-0 Coordinator
postgres01w-0   Worker
postgres01w-1   Worker
```

## <a name="placement"></a>放置
了解 Kubernetes 如何放置服务器组的 Pod。 描述每个 Pod，并确定它们被放置在 Kubernetes 群集的哪个物理节点上。 例如，对于协调器，运行以下命令：

```console
kubectl describe pod postgres01c-0 -n arc3
```

`kubectl` 返回：

```output
Name:         postgres01c-0
Namespace:    arc3
Priority:     0
Node:         aks-agentpool-42715708-vmss000000
Start Time:   Thu, 17 Sep 2020 00:40:33 -0700
…
```

为每个 Pod 运行此命令时，我们会将当前的放置汇总为：

| 服务器组角色|服务器组 Pod|托管 Pod 的 Kubernetes 物理节点 |
|--|--|--|
| 辅助角色|postgres01-1|aks-agentpool-42715708-vmss000002 |
| 辅助角色|postgres01-2|aks-agentpool-42715708-vmss000003 |

同时还请注意，在 Pod 的描述中，每个 Pod 托管的容器的名称。 例如，对于第二个工作器，运行以下命令：

```console
kubectl describe pod postgres01w-1 -n arc3
```

`kubectl` 返回：

```output
…
Node:         aks-agentpool-42715708-vmss000003/10.240.0.7
..
Containers:
  Fluentbit:
…
  Postgres:
…
  Telegraf:
…
```

已启用 Azure Arc 的 PostgreSQL 超大规模服务器组中的每个 Pod 托管以下三个容器：

|容器|说明
|----|----|
|`Fluentbit` |数据 * 日志收集器： https://fluentbit.io/
|`Postgres`|已启用 Azure Arc 的 PosgreSQL 超大规模服务器组的 PostgreSQL 实例部分
|`Telegraf` |指标收集器： https://www.influxdata.com/time-series-platform/telegraf/

体系结构如下：

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/3_pod_placement.png" alt-text="3 个 Pod，每个 Pod 放置在单独的节点上":::

这意味着，此时，构成已启用 Azure Arc 的 PostgreSQL 超大规模服务器组的每个 PostgreSQL 实例托管在 Kubernetes 容器中的特定物理主机上。 此配置可让已启用 Azure Arc 的 PostgreSQL 超大规模服务器组实现最佳性能，因为每个角色（协调器和工作器）均使用了每个物理节点的资源。 这些资源不会在多个 PostgreSQL 角色之间共享。

## <a name="scale-out-azure-arc-enabled-postgresql-hyperscale"></a>横向扩展已启用 Azure Arc 的 PostgreSQL 超大规模服务器组

现在，进行横向扩展，向服务器组添加第三个工作器节点并观察发生的情况。 此操作将创建一个托管在第四个 Pod 中的第四个 PostgreSQL 实例。
运行以下命令进行横向扩展：

```azurecli
az postgres arc-server edit --name postgres01 --workers 3 --k8s-namespace <namespace> --use-k8s
```

这将生成以下输出：

```output
Updating postgres01 in namespace `arc3`
postgres01 is Ready
```

列出 Azure Arc 数据控制器中部署的服务器组，并验证该服务器组现在是否运行了三个工作器。 运行以下命令：

```azurecli
az postgres arc-server list --k8s-namespace <namespace> --use-k8s
```

观察是否从两个工作器扩展到三个工作器：

```output
Name        State    Workers
----------  -------  ---------
postgres01  Ready    3
```

如之前所做的操作一样，观察服务器组现在是否总共使用四个 Pod：

```console
kubectl get pods -n arc3
```

```output
NAME                 READY   STATUS    RESTARTS   AGE
…
postgres01c-0         3/3     Running   0          11h
postgres01w-0         3/3     Running   0          11h
postgres01w-1         3/3     Running   0          11h
postgres01w-2         3/3     Running   0          5m2s
```

描述新的 Pod，以确定新 Pod 托管在 Kubernetes 群集的哪个物理节点上。
运行以下命令：

```console
kubectl describe pod postgres01w-2 -n arc3
```

识别托管节点的名称：

```output
Name:         postgres01w-2
Namespace:    arc3
Priority:     0
Node:         aks-agentpool-42715708-vmss000000
```

现在，群集物理节点上的 PostgreSQL 实例的放置：

|服务器组角色|服务器组 Pod|托管 Pod 的 Kubernetes 物理节点
|-----|-----|-----
|Coordinator|postgres01-0|aks-agentpool-42715708-vmss000000
|辅助角色|postgres01-1|aks-agentpool-42715708-vmss000002
|辅助角色|postgres01-2|aks-agentpool-42715708-vmss000003
|辅助角色|postgres01-3|aks-agentpool-42715708-vmss000000

请注意，新工作器 (postgres01w-2) 的 Pod 已置于与协调器相同的节点上。 

体系结构如下：

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/4_pod_placement_.png" alt-text="与协调器相同的节点上的第四个 Pod":::

为什么不将新的工作器/Pod 放置在 Kubernetes 群集的剩余物理节点 aks-agentpool-42715708-vmss000003 上？

原因在于，Kubernetes 群集的最后一个物理节点实际上托管了多个 Pod，这些 Pod 托管运行已启用 Azure Arc 的数据服务所需的其他组件。 Kubernetes 评估认为，在计划时最适合托管额外工作器的物理节点是 aks-agentpool-42715708-vmss000000。 

使用与上面相同的命令;我们可以看到每个物理节点所托管的内容：

|其他 Pod 名称\* |使用情况|托管 Pod 的 Kubernetes 物理节点
|----|----|----
|bootstrapper-jh48b|这种服务可处理用于创建、编辑和删除自定义资源（例如 SQL 托管实例、超大规模 PostgreSQL 服务器组和数据控制器）的传入请求|aks-agentpool-42715708-vmss000003
|control-gwmbs||aks-agentpool-42715708-vmss000002
|controldb-0|用于存储数据控制器的配置和状态的控制器数据存储。|aks-agentpool-42715708-vmss000001
|controlwd-zzjp7|控制器“监视”服务，用于监视数据控制器的可用性。|aks-agentpool-42715708-vmss000000
|logsdb-0|弹性搜索实例，用于存储所有 Arc 数据服务 Pod 中收集的所有日志。 Elasticsearch，接收来自每个 Pod 的 `Fluentbit` 容器的数据|aks-agentpool-42715708-vmss000003
|logsui-5fzv5|位于弹性搜索数据库顶部的 Kibana 实例，用于展示 log analytics GUI。|aks-agentpool-42715708-vmss000003
|metricsdb-0|InfluxDB 实例，用于存储所有 Arc 数据服务 Pod 中收集的所有指标。 InfluxDB，接收来自每个 Pod 的 `Telegraf` 容器的数据|aks-agentpool-42715708-vmss000000
|metricsdc-47d47|部署在群集中所有 Kubernetes 节点上的守护程序集，用于收集节点的节点级指标。|aks-agentpool-42715708-vmss000002
|metricsdc-864kj|部署在群集中所有 Kubernetes 节点上的守护程序集，用于收集节点的节点级指标。|aks-agentpool-42715708-vmss000001
|metricsdc-l8jkf|部署在群集中所有 Kubernetes 节点上的守护程序集，用于收集节点的节点级指标。|aks-agentpool-42715708-vmss000003
|metricsdc-nxm4l|部署在群集中所有 Kubernetes 节点上的守护程序集，用于收集节点的节点级指标。|aks-agentpool-42715708-vmss000000
|metricsui-4fb7l|位于 InfluxDB 数据库顶部的 Grafana 实例，用于显示监视仪表板 GUI。|aks-agentpool-42715708-vmss000003
|mgmtproxy-4qppp|位于 Grafana 和 Kibana 实例前部的 Web 应用代理层。|aks-agentpool-42715708-vmss000002

> \* Pod 名称的后缀将因其他部署而不相同。 此外，我们仅在此处列出 Azure Arc 数据控制器的 Kubernetes 命名空间中托管的 Pod。

体系结构如下：

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/5_full_list_of_pods.png" alt-text="各个节点上命名空间中的所有 Pod":::

如上所述，已启用 Azure Arc 的 PosgreSQL 超大规模服务器组的协调器节点 (Pod 1) 与服务器组的第三个工作器节点 (Pod 4) 共享相同的物理资源。 这是可接受的，因为相较于工作器节点而言，协调器节点使用的资源通常非常少。 因此，请谨慎选择：
- Kubernetes 群集的大小以及群集每个物理节点（内存、vCore）的特征
- Kubernetes 群集中的物理节点数量：
- 在 Kubernetes 群集上托管的应用程序或工作负载。

在 Kubernetes 群集上托管过多工作负载的影响在于：可能会使已启用 Azure Arc 的 PosgreSQL 超大规模服务器组受到限制。 如果发生这种情况，那么横向扩展对于用户的意义可能没有那么大。 用户从系统中获得的性能不仅仅是物理节点的放置或物理特性，也不仅仅是存储系统。 用户获得的性能还取决于在 Kubernetes 群集内部运行的每一个资源的配置方式（包括已启用 Azure Arc 的 PostgreSQL 超大规模服务器组），例如为内存和 vCore 设置的请求和限制。 在指定的 Kubernetes 群集上可以托管的工作负载量是相对于 Kubernetes 群集的特性、工作负载的性质、用户数量、Kubernetes 群集的工作方式而言……

## <a name="scale-out-aks"></a>横向扩展 AKS

说明一下，横向扩展 AKS 群集和已启用 Azure Arc 的 PosgreSQL 超大规模服务器组能够最大程度地从已启用 Azure Arc 的 PostgreSQL 超大规模服务器组的高性能中获益。
向 AKS 群集添加第五个节点：

:::row:::
    :::column:::
        以前
    :::column-end:::
    :::column:::
        之后
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        :::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/6_layout_before.png" alt-text="Azure 门户布局之前":::
    :::column-end:::
    :::column:::
        :::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/7_layout_after.png" alt-text="Azure 门户布局之后":::
    :::column-end:::
:::row-end:::

体系结构如下：

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/8_logical_layout_after.png" alt-text="更新后 Kubernetes 群集上的逻辑布局":::

通过运行命令来查看新的 AKS 物理节点上托管了哪些 Arc 数据控制器命名空间的 Pod：

```console
kubectl describe node aks-agentpool-42715708-vmss000004
```

接下来，我们将更新系统体系结构表示法：

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/9_updated_list_of_pods.png" alt-text="群集逻辑关系图上的所有 Pod":::

我们观察看到，Kubernetes 群集的新物理节点仅托管 Azure Arc 数据服务所需的指标 Pod。 请注意，在此示例中，我们仅重点展示了 Arc 数据控制器的命名空间，而没有展示其他 Pod。

## <a name="scale-out-azure-arc-enabled-postgresql-hyperscale-again"></a>再次横向扩展已启用 Azure Arc 的 PostgreSQL 超大规模服务器组

第五个物理节点尚未托管任何工作负载。 当我们横向扩展已启用 Azure Arc 的 PostgreSQL 超大规模服务器组时，Kubernetes 会优化新 PostgreSQL Pod 的放置，不会将其并置在已托管更多工作负载的物理节点上。 运行以下命令，将已启用 Azure Arc 的 PostgreSQL 超大规模服务器组从 3 个工作器扩展到 4 个工作器。 在操作结束时，服务器组将组成，并分布在五个 PostgreSQL 实例中，一个协调器和四个工作器中。

```azurecli
az postgres arc-server edit --name postgres01 --workers 4 --k8s-namespace <namespace> --use-k8s
```

这将生成以下输出：

```output
Updating postgres01 in namespace `arc3`
postgres01 is Ready
```

列出数据控制器中部署的服务器组，并验证该服务器组现在是否运行了四个工作器：

```azurecli
az postgres arc-server list --k8s-namespace <namespace> --use-k8s
```

观察服务器组是否从三个工作器扩展到了四个工作器。 

```console
Name        State    Workers
----------  -------  ---------
postgres01  Ready    4
```

如之前所做的操作一样，观察服务器组现在是否使用四个 Pod：

```output
kubectl get pods -n arc3

NAME                 READY   STATUS    RESTARTS   AGE
…
postgres01c-0         3/3     Running   0          13h
postgres01w-0         3/3     Running   0          13h
postgres01w-1         3/3     Running   0          13h
postgres01w-2         3/3     Running   0          179m
postgres01w-3         3/3     Running   0          3m13s
```

服务器组的形状现在为：

|服务器组角色|服务器组 Pod
|----|-----
|Coordinator|postgres01c-0
|辅助角色|postgres01w-0
|辅助角色|postgres01w-1
|辅助角色|postgres01w-2
|辅助角色|postgres01w-3

我们来讲解一下 postgres01w-3 Pod，以确定它所托管的物理节点：

```console
kubectl describe pod postgres01w-3 -n arc3
```

并观察它运行所在的 Pod：

|服务器组角色|服务器组 Pod| Pod
|----|-----|------
|Coordinator|postgres01c-0|aks-agentpool-42715708-vmss000000
|辅助角色|postgres01w-0|aks-agentpool-42715708-vmss000002
|辅助角色|postgres01w-1|aks-agentpool-42715708-vmss000003
|辅助角色|postgres01w-2|aks-agentpool-42715708-vmss000000
|辅助角色|postgres01w-3|aks-agentpool-42715708-vmss000004

体系结构如下：

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/10_kubernetes_schedules_newest_pod.png" alt-text="Kubernetes 计划将最新的 Pod 安排在使用量最低的节点中":::

Kubernetes 已将新的 PostgreSQL Pod 安排在 Kubernetes 群集中负载量最小的物理节点上。

## <a name="summary"></a>摘要

横向扩展已启用 Azure Arc 的服务器组实现了良好的可伸缩性和性能，为了更充分地利用这些优势，应避免 Kubernetes 群集内的以下各方发生资源争用：
- 已启用 Azure Arc 的 PosgreSQL 超大规模服务器组与同一 Kubernetes 群集上托管的其他工作负载之间
- 构成已启用 Azure Arc 的 PosgreSQL 超大规模服务器组的所有 PostgreSQL 实例之间

有以下多种方法可以避免上述问题：
- 同时横向扩展 Kubernetes 和启用 Azure Arc 的 PostgreSQL 超大规模服务器组：考虑采用相同的方式对 Kubernetes 群集和已启用 Azure Arc 的 PosgreSQL 超大规模服务器组进行横向扩展。 对于添加到服务器组的每个工作器，添加一个物理节点到群集。
- 横向扩展已启用 Azure Arc 的 PostgreSQL 超大规模服务器组，而不扩展 Kubernetes：通过对托管在 Kubernetes（包括已启用 Azure Arc 的 PostgreSQL 超大规模服务器组）中的工作负载设置正确的资源约束（对内存和 vCore 的请求和限制），用户能够将工作负载并置到 Kubernetes 上并降低资源争用的风险。 需要确保 Kubernetes 群集物理节点的物理特性遵循定义的资源约束。 在工作负载随着时间不断变化或 Kubernetes 群集中工作负载增多的情况下，应确保始终保持一个均衡点。
- 使用 Kubernetes 机制（Pod 选择器、相关性、反相关性）来影响 Pod 的放置。

## <a name="next-steps"></a>后续步骤

[通过增加更多的工作器节点横向扩展已启用 Azure Arc 的 PosgreSQL 超大规模服务器组](scale-out-in-postgresql-hyperscale-server-group.md)
