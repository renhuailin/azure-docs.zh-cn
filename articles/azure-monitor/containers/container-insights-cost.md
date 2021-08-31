---
title: 容器见解的监视成本 | Microsoft Docs
description: 本文介绍容器见解收集的指标和清单数据的监视成本，以帮助客户管理其使用情况和相关成本。
ms.topic: conceptual
ms.date: 05/29/2020
ms.openlocfilehash: 2ff3616b3c78bbd7202acb08ffb3e46e0f7591b0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121738502"
---
# <a name="understand-monitoring-costs-for-container-insights"></a>了解容器见解的监视成本

本文提供容器见解的定价指导，帮助你了解以下内容：

* 启用此见解之前，如何预先估算成本

* 为一个或多个容器启用容器见解之后如何度量成本

* 如何控制数据收集并降低成本

Azure Monitor 日志收集、索引和存储 Kubernetes 群集生成的数据。 

Azure Monitor 定价模型主要基于 Log Analytics 工作区中每天引入的数据量（以 GB 为单位）。 Log Analytics 工作区的成本不仅基于收集的数据量，还取决于所选的计划，以及群集生成的数据的存储时间长短。

>[!NOTE]
>所有规模和定价仅适用于示例估算。 有关基于 Azure Monitor Log Analytics 定价模型和 Azure 区域的最新定价，请参阅 Azure Monitor [定价](https://azure.microsoft.com/pricing/details/monitor/)页。

下面汇总了容器见解从 Kubernetes 群集收集的数据类型，这些数据类型会影响成本，你可以根据使用情况对其进行自定义：

- 来自群集中每个 Kubernetes 命名空间中每个受监视容器的 Stdout、stderr 容器日志

- 来自群集中每个受监视容器的容器环境变量

- 群集中不需要监视的已完成 Kubernetes 作业/Pod

- Prometheus 指标的主动抓取

- AKS 群集中 Kubernetes 主节点日志的[诊断日志收集](../../aks/monitor-aks.md#configure-monitoring)，用于分析由主组件（例如 kube-apiserver 和 kube-controller-manager）生成的日志数据。

## <a name="what-is-collected-from-kubernetes-clusters"></a>从 Kubernetes 群集收集的内容

容器见解包含收集的一组预定义指标和清单项，这些指标和清单项作为日志数据写入到 Log Analytics 工作区中。 默认情况下，下面列出的所有指标每隔一分钟收集一次。

### <a name="node-metrics-collected"></a>收集的节点指标

下面列出了为每个节点收集的 24 个指标：

- cpuUsageNanoCores
- cpuCapacityNanoCores
- cpuAllocatableNanoCores
- memoryRssBytes
- memoryWorkingSetBytes
- memoryCapacityBytes
- memoryAllocatableBytes
- restartTimeEpoch
- used (disk)
- free (disk)
- used_percent (disk)
- io_time (diskio)
- writes (diskio)
- reads (diskio)
- write_bytes (diskio)
- write_time (diskio)
- iops_in_progress (diskio)
- read_bytes (diskio)
- read_time (diskio)
- err_in (net)
- err_out (net)
- bytes_recv (net)
- bytes_sent (net)
- Kubelet_docker_operations (kubelet)

### <a name="container-metrics"></a>容器指标

下面列出了为每个容器收集的 8 个指标：

- cpuUsageNanoCores
- cpuRequestNanoCores
- cpuLimitNanoCores
- memoryRssBytes
- memoryWorkingSetBytes
- memoryRequestBytes
- memoryLimitBytes
- restartTimeEpoch

### <a name="cluster-inventory"></a>群集清单

下面列出了默认情况下收集的群集清单数据：

- KubePodInventory - 每个容器每分钟一次
- KubeNodeInventory - 每个节点每分钟一次
- KubeServices - 每个服务每分钟一次
- ContainerInventory - 每个容器每分钟一次

## <a name="estimating-costs-to-monitor-your-aks-cluster"></a>估算监视 AKS 群集的成本

下面的估算基于具有以下规模示例的 Azure Kubernetes 服务 (AKS) 群集。 而且，该估算仅适用于收集的指标和清单数据。 对于容器日志（stdout、stderr 和环境变量），它根据工作负荷生成的日志大小而变化，因此我们将其排除在估算之外。

如果对按以下方式配置的 AKS 群集启用了监视，

- 三个节点
- 每个节点两个磁盘
- 每个节点一个网络接口
- 20 个 Pod（每个 Pod 一个容器 = 总共 20 个容器）
- 两个 Kubernetes 命名空间
- 五个 Kubernetes 服务（包括 kube-system Pod、服务和命名空间）
- 收集频率 = 60 秒（默认值）

你可以在分配的 Log Analytics 工作区中查看以下表和每小时生成的数据量。 有关每个表的详细信息，请参阅 [Azure Monitor 日志表](../../aks/monitor-aks-reference.md#azure-monitor-logs-tables)。

|表 | 规模估算（MB/小时） |
|------|---------------|
|性能 | 12.9 |
|InsightsMetrics | 11.3 |
|KubePodInventory | 1.5 |
|KubeNodeInventory | 0.75 |
|KubeServices | 0.13 |
|ContainerInventory | 3.6 |
|KubeHealth | 0.1 |
|KubeMonAgentEvents |0.005 |

总计 = 31 MB/小时= 23.1 GB/月（一个月 = 31 天）

通过使用 Log Analytics 的默认[定价](https://azure.microsoft.com/pricing/details/monitor/)（即用即付模型），可以估算每个月的 Azure Monitor 成本。 加上产能预留后，根据所选预留，每个月的价格会更高。

## <a name="controlling-ingestion-to-reduce-cost"></a>控制引入以降低成本

假设组织的不同业务部门共享 Kubernetes 基础结构和 Log Analytics 工作区。 各业务部门由 Kubernetes 命名空间分隔。 可以使用“数据使用情况” runbook（可从“查看工作簿”下拉菜单中获得）来可视化每个工作区中引入的数据量。

[![“查看工作簿”下拉列表](media/container-insights-cost/workbooks-dropdown.png)](media/container-insights-cost/workbooks-dropdown.png#lightbox)


此工作簿可帮助你直观显示数据源，而不必根据我们在文档中分享的内容构建自己的查询库。 此工作簿包含一些图表，通过这些图表，你可以从以下角度查看计费数据：

- 按“解决方案”查看引入的总计费数据
- 按“容器日志(应用程序日志)”查看引入的计费数据
- 按“Kubernetes 命名空间”查看引入的计费容器日志数据
- 按“群集名称”查看引入并分隔的计费容器日志数据
- 按日志源条目查看引入的计费容器日志数据
- 按“诊断主节点日志”查看引入的计费诊断数据

[![数据使用情况工作簿](media/container-insights-cost/data-usage-workbook.png)](media/container-insights-cost/data-usage-workbook.png#lightbox)

若要了解如何管理工作簿的权限，请查看[访问控制](../visualize/workbooks-access-control.md)。

完成分析，确定哪个或哪些源生成的数据最多或超出要求后，可以重新配置数据收集。 [配置代理数据收集设置](container-insights-agent-config.md)一文中介绍了有关配置 stdout、stderr 和环境变量收集的详细信息。

以下示例说明了为帮助控制成本，可通过修改 ConfigMap 文件对群集应用哪些更改。

1. 通过修改 ConfigMap 文件中的以下内容，对群集中的所有命名空间禁用 stdout 日志：

    ```
    [log_collection_settings]       
       [log_collection_settings.stdout]          
          enabled = false
    ```

2. 通过修改 ConfigMap 文件中的以下内容，禁止从开发命名空间（例如 dev-test）收集 stderr 日志，并继续从其他命名空间（例如 prod 和 default）收集 stderr 日志：

    >[!NOTE]
    >kube-system 日志收集默认情况下处于禁用状态。 系统将保留该默认设置，并向 stderr 日志收集应用将 dev-test 命名空间添加到排除命名空间列表的操作。

    ```
    [log_collection_settings.stderr]          
       enabled = true          
          exclude_namespaces = ["kube-system", "dev-test"]
    ```

3. 通过修改 ConfigMap 文件中的以下内容，禁用整个群集中的环境变量收集。 这适用于每个 Kubernetes 命名空间中的所有容器。 

    ```
    [log_collection_settings.env_var]
        enabled = false
    ```

4. 若要清理已完成的作业，请通过修改 ConfigMap 文件中的以下内容，在作业定义中指定清理策略：

    ```
    apiVersion: batch/v1
    kind: Job
    metadata:
      name: pi-with-ttl
    spec:
      ttlSecondsAfterFinished: 100
    ```

将这些更改中的一项或多项应用于 ConfigMap 之后，请参阅[应用已更新的 ConfigMap](container-insights-prometheus-integration.md#applying-updated-configmap)，将其应用于群集。

### <a name="prometheus-metrics-scraping"></a>Prometheus 指标抓取

如果使用 [Prometheus 指标抓取](container-insights-prometheus-integration.md)，请确保考虑以下因素，以限制从群集收集的指标数：

- 确保以最佳方式设置抓取频率（默认值为 60 秒）。 虽然可以将频率提高到 15 秒，但需要确保抓取的指标以该频率发布。 否则，系统会抓取许多重复指标并每隔一段时间发送到 Log Analytics 工作区，这样不仅会增加数据引入和保留成本，用处也不大。 

- 容器见解支持按指标名称分类的排除列表和包含列表。 例如，如果要在群集中抓取 kubedns 指标，默认情况下可能会有数百个指标被抓取，但你很可能只对某个子集感兴趣。 确认你指定了要抓取的指标列表，或者除了少数指标外，排除其他指标以节省数据引入量。 启用抓取但不使用其中许多指标（它们只会给 Log Analytics 帐单增加额外的费用），这一点很容易做到。

- 抓取 Pod 注释时，确保按命名空间进行筛选，以便从不使用的命名空间（例如 dev-test 命名空间）中排除 Pod 指标抓取。

## <a name="next-steps"></a>后续步骤

若要详细了解如何根据数据（通过容器见解收集）中的最新使用模式来了解可能产生的成本，请参阅[管理使用情况并估算成本](../logs/manage-cost-storage.md)。