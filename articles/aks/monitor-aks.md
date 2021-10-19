---
title: 使用 Azure Monitor 监视 Azure Kubernetes 服务 (AKS)
description: 介绍如何使用 Azure Monitor 监视 AKS 群集及其工作负载的运行状况和性能。
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/29/2021
ms.openlocfilehash: 464e848814da046600b05a1feea632a1ceaa141a
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129713837"
---
# <a name="monitoring-azure-kubernetes-service-aks-with-azure-monitor"></a>使用 Azure Monitor 监视 Azure Kubernetes 服务 (AKS)
此方案描述如何使用 Azure Monitor 监视 Azure Kubernetes 服务 (AKS) 的运行状况和性能。 它包括收集对收集到的数据进行监视、分析和可视化来确定趋势至关重要的遥测，还介绍如何配置警报来主动通知关键问题。

[云监视指南](/azure/cloud-adoption-framework/manage/monitor/)定义了在 Azure 资源方面应关注的[主要监视目标](/azure/cloud-adoption-framework/strategy/monitoring-strategy#formulate-monitoring-requirements)。 此方案着重于使用 Azure Monitor 监视运行状况和状态。

## <a name="scope-of-the-scenario"></a>此方案的应用范围
本方案适用于使用 Azure Monitor 监视 AKS 的客户。 它不包含以下内容，不过，这些内容可能会在方案的后续更新中加入。

- 监视 Azure 之外的 Kubernetes 群集，参考已启用 Azure Arc 的 Kubernetes 的现有内容除外。 
- 使用 Azure Monitor 以外的工具监视 AKS，填补了 Azure Monitor 和容器见解的空白。

> [!NOTE]
> Azure Monitor 旨在监视云资源的可用性和性能。 尽管存储在 Azure Monitor 的操作数据对调查安全事件可能有用，但 Azure 中的其他服务旨在监视安全。 AKS 的安全监视是通过 [Azure Sentinel](../sentinel/overview.md) 和 [Azure 安全中心](../security-center/security-center-introduction.md)完成的。 若要了解 Azure 中的安全监视工具及其与 Azure Monitor 的关系，请参阅[使用 Azure Monitor 监视虚拟机 - 安全监视](../azure-monitor/vm/monitor-virtual-machine-security.md)。
>
> 若要了解如何使用安全服务来监视 AKS，请参阅 [Azure Defender for Kubernetes - 优点和功能](../security-center/defender-for-kubernetes-introduction.md)和[将 Azure Kubernetes 服务 (AKS) 诊断日志连接到 Azure Sentinel](../sentinel/data-connectors-reference.md#azure-kubernetes-service-aks)。
## <a name="container-insights"></a>容器见解
像任何其他 Azure 资源一样，AKS 会生成[平台指标和资源日志](monitor-aks-reference.md)，你可以使用它们来监视 AKS 的基本运行状况和性能。 请启用[容器见解](../azure-monitor/containers/container-insights-overview.md)来扩展此监视。 容器见解是 Azure Monitor 中的一项功能，除了监视其他群集配置之外，还监视托管在 AKS 上的托管 Kubernetes 群集的运行状况和性能。 容器见解提供交互式视图和工作簿，用于分析针对各种监视方案收集的数据。 

[Prometheus](https://prometheus.io/) 和 [Grafana](https://www.prometheus.io/docs/visualization/grafana/) 是 CNCF 支持的广受欢迎的开源工具，可用于监视 Kubernetes。 AKS 以 Prometheus 格式公开了许多指标，这使 Prometheus 成为一种常见的监视选择。 [容器见解](../azure-monitor/containers/container-insights-overview.md)与 AKS 进行了本机集成，可收集关键指标和日志，就发现的问题发出警报，并通过工作簿提供可视化效果。 它还会收集一些 Prometheus 指标，许多本机 Azure Monitor 见解是基于 Prometheus 指标构建的。 容器见解对 AKS 的 E2E 监视进行了补充和完善，包括日志收集，这是独立工具 Prometheus 不提供的。 许多客户将 Prometheus 集成和 Azure Monitor 相结合来进行 E2E 监视。

若要详细了解如何使用容器见解，请参阅[容器见解概述](../azure-monitor/containers/container-insights-overview.md)。 下面的[使用容器见解监视 AKS 层](#monitor-layers-of-aks-with-container-insights)介绍了容器见解的各种功能及其支持的监视方案。

:::image type="content" source="media/monitor-aks/container-insights.png" alt-text="容器见解" lightbox="media/monitor-aks/container-insights.png":::


## <a name="configure-monitoring"></a>配置监视
以下部分介绍使用 Azure Monitor 配置 AKS 群集的完全监视所需的步骤。
### <a name="create-log-analytics-workspace"></a>创建 Log Analytics 工作区
至少需要一个 Log Analytics 工作区来支持容器见解并收集和分析有关 AKS 群集的其他遥测数据。 该工作区免费，但在收集数据时，会产生引入费用和保留费用。 有关详细信息，请参阅[使用 Azure Monitor 日志管理使用情况和成本](../azure-monitor/logs/manage-cost-storage.md)。

如果是刚开始使用 Azure Monitor，请从单个工作区开始，根据需求的发展可考虑创建更多工作区。 许多环境会对其监视的所有 Azure 资源使用单个工作区。 你甚至可共享 [Azure 安全中心和 Azure Sentinel](../azure-monitor/vm/monitor-virtual-machine-security.md) 所使用的工作区，尽管许多客户选择将其可用性和性能遥测与安全数据分隔开。 

若要详细了解设计工作区配置时应考虑的逻辑，请参阅[设计 Azure Monitor 日志部署](../azure-monitor/logs/design-logs-deployment.md)。

### <a name="enable-container-insights"></a>启用容器见解
为 AKS 群集启用容器见解时，它会部署一个容器化版本的 [Log Analytics 代理](../agents/../azure-monitor/agents/log-analytics-agent.md)，用于将数据发送到 Azure Monitor。 你可以通过多种方法启用容器见解，具体取决于你使用的是新 AKS 群集还是现有 AKS 群集。 有关先决条件和配置选项，请参阅[启用容器见解](../azure-monitor/containers/container-insights-onboard.md)。


### <a name="configure-collection-from-prometheus"></a>配置从 Prometheus 收集
借助容器见解，无需 Prometheus 服务器即可在 Log Analytics 工作区中收集一些 Prometheus 指标。 你可以使用 Azure Monitor 功能分析此数据以及容器见解收集的其他数据。 有关此配置的详细信息，请参阅[使用容器见解配置 Prometheus 指标的抓取](../azure-monitor/containers/container-insights-prometheus-integration.md)。


### <a name="collect-resource-logs"></a>收集资源日志
AKS 控制平面组件的日志在 Azure 中是以[资源日志](../azure-monitor/essentials/resource-logs.md)的形式实现的。 容器见解目前不会使用这些日志，因此你需要创建自己的日志查询来查看和分析它们。 若要详细了解这些日志的结构以及如何编写查询，请参阅[如何从容器见解查询日志](../azure-monitor/containers/container-insights-log-query.md#resource-logs)。

你需要创建诊断设置来收集资源日志。 创建多个诊断设置，以将不同的日志集发送到不同的位置。 若要为 AKS 群集创建诊断设置，请参阅[创建诊断设置以将平台日志和指标发送到不同的目标](../azure-monitor/essentials/diagnostic-settings.md)。 

向工作区发送资源日志会产生费用，因此，只应收集那些要使用的日志类别。 如果需要保留信息，但不要求信息随时可供分析，请将日志发送到 Azure 存储帐户以降低成本。  有关可用于 AKS 的类别的说明，请参阅[资源日志](monitor-aks-reference.md#resource-logs)。有关引入和保留日志数据的成本的详细信息，请参阅[使用 Azure Monitor 日志管理使用情况和成本](../azure-monitor/logs/manage-cost-storage.md)。 首先收集最少数量的类别，然后随着需求的增加和对相关成本的了解，修改诊断设置以收集其他类别。

如果不确定一开始要启用哪些资源日志，请采用下表中根据最常见客户要求提出的建议。 如果以后发现需要其他类别，可启用此类别。

| 类别 | 是否启用？ | 目标 |
|:---|:---|:---|
| cluster-autoscaler      | 启用自动缩放后启用 | Log Analytics 工作区 |
| 防护                   | 启用 Azure Active Directory 后启用 | Log Analytics 工作区 |
| kube-apiserver          | 启用 | Log Analytics 工作区 |
| kube-audit              | 启用 | Azure 存储。 这样可以将成本保持在最低水平，但仍然保留审核日志供审核者使用。 |
| kube-audit-admin        | 启用 | Log Analytics 工作区 |
| kube-controller-manager | 启用 | Log Analytics 工作区 |
| kube-scheduler          | 禁用 | |
| AllMetrics              | 启用 | Log Analytics 工作区 |





## <a name="access-azure-monitor-features"></a>访问 Azure Monitor 功能

对于订阅中的所有 AKS 群集，可通过 Azure 门户中的“监视”菜单访问 Azure Monitor 功能；对于单个 AKS 群集，可通过“Kubernetes 服务”菜单的“监视”部分访问这些功能  。 以下屏幕截图显示了群集的“监视”菜单。

:::image type="content" source="media/monitor-aks/monitoring-menu.png" alt-text="AKS 监视菜单" lightbox="media/monitor-aks/monitoring-menu.png":::

| 菜单选项 | 说明 |
|:---|:---|
| 洞察力 | 为当前群集打开容器见解。 从“监视”菜单中选择“容器”，为所有群集打开容器见解 。  |
| 警报 | 查看当前群集的警报。 |
| 指标 | 打开指标资源管理器，并将范围设置为当前群集。 |
| 诊断设置 | 为群集创建诊断设置，以收集资源日志。 |
| 顾问 | Azure 顾问就当前群集提供的建议。 |
| 日志 | 打开 Log Analytics 并将范围设置为当前群集，以分析日志数据和访问预生成的查询。 |
| 工作簿 | 打开 Kubernetes 服务的工作簿库。 |




## <a name="monitor-layers-of-aks-with-container-insights"></a>使用容器见解监视 AKS 层
由于不同 Kubernetes 实现的差异很大，每个客户对 AKS 监视都有独特的要求。 你采用的方法应基于缩放、拓扑、组织角色和多群集租户等多种因素。 本部分介绍一种通用策略，它采用自下而上的方式，从基础结构开始，一直到应用程序。 每一层都有不同的监视要求。 这些层在下图中进行了说明，并在以下各部分中进行了更详细的讨论。

:::image type="content" source="media/monitor-aks/layers.png" alt-text="AKS 层"  border="false":::

### <a name="level-1---cluster-level-components"></a>级别 1 - 群集级别组件
群集级别包括以下组件。

| 组件 | 监视要求 |
|:---|:---|
| 节点 |  了解每个节点的 CPU、内存和磁盘的就绪状态和性能，并在部署任何工作负载之前主动监视其使用趋势。 |


使用容器见解中的现有视图和报告来监视群集级别组件。 通过“群集”视图，可快速查看群集中节点的性能，包括其 CPU 和内存利用率。 使用“节点”视图可以查看每个节点的运行状况以及在每个节点上运行的 Pod 的运行状况和性能。 若要详细了解如何使用此视图并分析节点的运行状况和性能，请参阅[使用容器见解监视 Kubernetes 群集性能](../azure-monitor/containers/container-insights-analyze.md)。

:::image type="content" source="media/monitor-aks/container-insights-cluster-view.png" alt-text="容器见解群集视图" lightbox="media/monitor-aks/container-insights-cluster-view.png":::

除了 GPU 使用情况外，还可使用容器见解中的节点工作簿分析磁盘容量和 IO。 有关这些工作簿的说明，请参阅[节点工作簿](../azure-monitor/containers/container-insights-reports.md#node-workbooks)。

:::image type="content" source="media/monitor-aks/container-insights-node-workbooks.png" alt-text="容器见解节点工作簿" lightbox="media/monitor-aks/container-insights-node-workbooks.png":::


在故障排除方案中，你可能需要直接访问 AKS 节点来实现维护或即时日志收集。 出于安全目的，AKS 节点不会向 Internet 公开，但你可使用 `kubectl debug` 通过 SSH 连接到 AKS 节点。 有关此过程的详细信息，请参阅[使用 SSH 连接到 Azure Kubernetes 服务 (AKS) 群集节点以进行维护或故障排除](ssh.md)。



### <a name="level-2---managed-aks-components"></a>级别 2 - 托管 AKS 组件
托管 AKS 级别包括以下组件。

| 组件 | 监视 |
|:---|:---|
| API 服务器 | 监视 API 服务器的状态，识别服务关闭时请求负载的增加和瓶颈。 |
| Kubelet | 监视 Kubelet 有助于排查 Pod 管理问题，或者 Pod 未启动、节点未就绪或 Pod 被终止的问题。  |

Azure Monitor 和容器见解尚未提供对 API 服务器的完全监视。 可以使用指标资源管理器查看“即时请求”计数器，但应参考 Prometheus 中的指标来全面了解 API 服务器性能。 这包括诸如请求延迟和工作队列处理时间之类的值。 [Grafana Labs](https://grafana.com/grafana/dashboards/12006) 提供一个 Grafana 仪表盘，它提供 API 服务器的关键指标视图。 请在现有的 Grafana 服务器上使用此仪表盘，或者使用[在 Grafana 中监视 Azure 服务](../azure-monitor/visualize/grafana-plugin.md)在 Azure 中设置新的 Grafana 服务器

:::image type="content" source="media/monitor-aks/grafana-api-server.png" alt-text="Grafana API 服务器" lightbox="media/monitor-aks/grafana-api-server.png":::

使用 Kubelet 工作簿查看每个 Kubelet 的运行状况和性能。 有关此工作簿的详细信息，请参阅[资源监视工作簿](../azure-monitor/containers/container-insights-reports.md#resource-monitoring-workbooks)。 对于故障排除方案，可按照[从 Azure Kubernete 服务 (AKS) 群集节点获取 kubelet 日志](kubelet-logs.md)中描述的过程中访问 kubelet 日志。

:::image type="content" source="media/monitor-aks/container-insights-kubelet-workbook.png" alt-text="容器见解 kubelet 工作簿" lightbox="media/monitor-aks/container-insights-kubelet-workbook.png":::

### <a name="resource-logs"></a>资源日志
通过[使用资源日志进行日志查询](../azure-monitor/containers/container-insights-log-query.md#resource-logs)来分析由 AKS 组件生成的控制平面日志。 

### <a name="level-3---kubernetes-objects-and-workloads"></a>级别 3 - Kubernetes 对象和工作负载
Kubernetes 对象和工作负载级别包括以下组件。

| 组件 | 监视要求 |
|:---|:---|
| 部署 | 监视部署的实际状态与预期状态，以及在其上运行的 Pod 的状态和资源利用率。  | 
| Pod | 监视 AKS 群集上运行的 Pod 的状态和资源利用率，包括 CPU 和内存。 |
| 容器 | 监视 AKS 群集上运行的容器的资源利用率，包括 CPU 和内存。 |


使用容器见解中的现有视图和报告来监视容器和 Pod。 使用“节点”和“控制器”视图查看在其上运行的 Pod 的运行状况和性能，并向下钻取到其容器的运行状况和性能。 直接从“容器”视图查看容器的运行状况和性能。 若要详细了解如何使用此视图并分析容器的运行状况和性能，请参阅[使用容器见解监视 Kubernetes 群集性能](../azure-monitor/containers/container-insights-analyze.md)。

:::image type="content" source="media/monitor-aks/container-insights-containers-view.png" alt-text="容器见解容器视图" lightbox="media/monitor-aks/container-insights-containers-view.png":::

使用容器见解中的“部署”工作簿查看为部署收集的指标。 有关详细信息，请参阅[使用容器见解收集的部署和 HPA 指标](../azure-monitor/containers/container-insights-deployment-hpa-metrics.md)。

> [!NOTE]
> 容器见解中的部署视图目前处于公共预览阶段。

:::image type="content" source="media/monitor-aks/container-insights-deployments-workbook.png" alt-text="容器见解部署工作簿" lightbox="media/monitor-aks/container-insights-deployments-workbook.png":::

#### <a name="live-data"></a>实时数据
在故障排除方案中，容器见解提供对实时 AKS 容器日志 (stdout/stderror)、事件和 Pod 指标的访问。 若要详细了解如何使用此功能，请参阅[如何实时查看 Kubernetes 日志、事件和 Pod 指标](../azure-monitor/containers/container-insights-livedata-overview.md)。

:::image type="content" source="media/monitor-aks/container-insights-live-data.png" alt-text="容器见解实时数据" lightbox="media/monitor-aks/container-insights-live-data.png":::

### <a name="level-4--applications"></a>级别 4 - 应用程序
应用程序级别包括在 AKS 群集中运行的应用程序工作负载。

| 组件 | 监视要求 |
|:---|:---|
| 应用程序 | 监视微服务应用程序部署，以识别应用程序故障和延迟问题。 包括请求速率、响应时间和异常等信息。 |

Application Insights 对在 AKS 和其他环境中运行的应用程序提供完全监视。 如果你有 Java 应用程序，则可以按照[针对 Kubernetes 的零检测应用程序监视 - Azure Monitor Application Insights](../azure-monitor/app/kubernetes-codeless.md) 提供监视，而无需检测代码。 但是，若要进行完全监视，应根据应用程序配置基于代码的监视。

- [ASP.NET 应用程序](../azure-monitor/app/asp-net.md)
- [ASP.NET Core 应用程序](../azure-monitor/app/asp-net-core.md)
- [.NET 控制台应用程序](../azure-monitor/app/console.md)
- [Java](../azure-monitor/app/java-in-process-agent.md)
- [Node.js](../azure-monitor/app/nodejs.md)
- [Python](../azure-monitor/app/opencensus-python.md)
- [其他平台](../azure-monitor/app/platforms.md)

请参阅[什么是 Application Insights？](../azure-monitor/app/app-insights-overview.md) 

### <a name="level-5--external-components"></a>级别 5 - 外部组件
AKS 的外部组件包括以下内容。

| 组件 | 监视要求 |
|:---|:---|
| 服务网格、入口、出口 | 基于组件的指标。 |
| 数据库和工作队列 | 基于组件的指标。 |

使用 Prometheus 和 Grafana 或其他专有工具监视外部组件，例如服务网格、入口、出口。 使用 Azure Monitor 的其他功能监视数据库和其他 Azure 资源。

## <a name="analyze-metric-data-with-metrics-explorer"></a>使用指标资源管理器分析指标数据
若要对为容器收集的指标数据执行自定义分析，请使用指标资源管理器。 通过指标资源管理器，可以绘制图表、直观地关联趋势，并调查指标值中的峰值和谷值。 创建指标警报以在指标值超过阈值时主动通知你，并将图表固定到仪表板，供组织的不同成员使用。

若要详细了解如何使用此功能，请参阅 [Azure 指标资源管理器入门](../azure-monitor/essentials/metrics-getting-started.md)。 如需为 AKS 收集的平台指标列表，请参阅[监视 AKS 数据参考指标](monitor-aks-reference.md#metrics)。 为群集启用容器见解后，可使用[其他指标值](../azure-monitor/containers/container-insights-update-metrics.md)。

:::image type="content" source="media/monitor-aks/metrics-explorer.png" alt-text="指标资源管理器" lightbox="media/monitor-aks/metrics-explorer.png":::



## <a name="analyze-log-data-with-log-analytics"></a>使用 Log Analytics 分析日志数据
若要分析资源日志或深入了解用于在容器见解中创建视图的数据，请使用 Log Analytics。 通过 Log Analytics，可对日志数据执行自定义分析。 

若要详细了解如何使用日志查询来分析容器见解收集的数据，请参阅[如何从容器见解查询日志](../azure-monitor/containers/container-insights-log-query.md)。 若要了解如何使用这些查询，请查阅[在 Azure Monitor 日志分析中使用查询](../azure-monitor/logs/queries.md)；如需在完整教程中了解如何使用 Log Analytics 来运行查询并使用其结果，请参阅 [Log Analytics 教程](../azure-monitor/logs/log-analytics-tutorial.md)。

如需为 AKS 收集的可在指标资源管理器中分析的表格列表，请参阅[监视 AKS 数据参考日志](monitor-aks-reference.md#azure-monitor-logs-tables)。

:::image type="content" source="media/monitor-aks/log-analytics-queries.png" alt-text="针对 Kubernetes 的 Log Analytics 查询" lightbox="media/monitor-aks/log-analytics-queries.png":::

除了容器见解数据外，你还可以使用日志查询来分析 AKS 中的资源日志。 有关可用日志类别的列表，请参阅 [AKS 数据参考资源日志](monitor-aks-reference.md#resource-logs)。 在收集数据之前，必须按照[配置监视](#configure-monitoring)中所述，创建一个诊断设置来收集每个类别。 




## <a name="alerts"></a>警报
[Azure Monitor 中的警报](../azure-monitor/alerts/alerts-overview.md)会主动通知你在监视数据中发现的值得关注的数据和模式。 有了警报，你就可以在客户注意到你的系统中的问题之前确定和解决它们。 AKS 群集没有预配置的警报规则，但你可以根据容器见解收集的数据创建自己的警报规则。

> [!IMPORTANT]
> 大多数警报规则都会产生成本，具体成本取决于规则类型、规则包含的维度数量以及规则运行的频率。 创建任何警报规则之前，请参阅 [Azure Monitor 定价](https://azure.microsoft.com/pricing/details/monitor/)中的“警报规则”。


### <a name="choosing-the-alert-type"></a>选择警报类型
Azure Monitor 中最常见的警报规则类型是[指标警报](../azure-monitor/alerts/alerts-metric.md)和[日志查询警报](../azure-monitor/alerts/alerts-log-query.md)。 你为特定方案创建的警报规则类型取决于你要发出警报的数据所在的位置。 但你可能会遇到这样的情况，即某一警报方案的数据在指标和日志中都有，而你需要确定使用哪种规则类型。 

通常，最佳策略是尽可能使用指标警报而不是日志警报，因为前者响应速度更快且提供状态。 你可以针对可在指标资源管理器中分析的任何值创建指标警报。 如果警报规则的逻辑需要日志中的数据，或者需要更复杂的逻辑，则可以使用日志查询警报规则。

例如，若要在应用程序工作负载消耗过多 CPU 时发出警报，可以使用 CPU 指标创建指标警报。 如果需要在控制平面日志中发现特定消息时发出警报，则需要创建日志警报。
### <a name="metric-alert-rules"></a>指标警报规则
指标警报规则使用与指标资源管理器相同的指标值。 事实上，你可以使用当前正在分析的数据直接从指标资源管理器创建警报规则。 你可以将 [AKS 数据参考指标](monitor-aks-reference.md#metrics)中的任何值用于指标警报规则。

容器见解包含一项现为公共预览版的功能，它可为 AKS 群集创建一组建议的指标警报规则。 此功能会创建警报规则使用的新指标值（也为预览版），你也可在指标资源管理器中使用这些值。 若要详细了解此功能，并详细了解如何为 AKS 创建指标警报，请参阅[来自容器见解的建议的指标警报（预览版）](../azure-monitor/containers/container-insights-metric-alerts.md)。


### <a name="log-alerts-rules"></a>日志警报规则
使用日志警报规则根据日志查询的结果生成警报。 这可能是由容器见解或从 AKS 资源日志收集的数据。 若要详细了解 AKS 的日志警报规则以及一组专为警报规则设计的示例查询，请参阅[如何从容器见解创建日志警报](../azure-monitor/containers/container-insights-log-alerts.md)。 此外，若要详细了解可针对警报规则修改的日志查询，可查阅[如何从容器见解查询日志](../azure-monitor/containers/container-insights-log-query.md)。

### <a name="virtual-machine-alerts"></a>虚拟机警报
AKS 依赖于必须正常运行的虚拟机规模集来运行 AKS 工作负载。 可按照[使用 Azure Monitor 监视虚拟机：警报](../azure-monitor/vm/monitor-virtual-machine-alerts.md)中的指南，对虚拟机的 CPU、内存和存储等关键指标发出警报。

### <a name="prometheus-alerts"></a>Prometheus 警报
如果 Azure Monitor 没有警报条件所需的数据，或者警报的响应可能不够及时，则应在 Prometheus 中配置警报。 针对 API 服务器发出警报就是这种情况。 Azure Monitor 不会收集 API 服务器的关键信息，包括它是否可用或是否遇到瓶颈。 你可以使用 kube-apiserver 资源日志类别中的数据创建日志查询警报，但可能需要几分钟才能收到警报，这可能难以满足你的要求。 


## <a name="next-steps"></a>后续步骤

- 有关 AKS 创建的指标、日志和其他重要值的参考信息，请参阅[监视 AKS 数据参考](monitor-aks-reference.md)。