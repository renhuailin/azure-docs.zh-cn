---
title: 使用容器见解进行 Kubernetes 监视 | Microsoft Docs
description: 本文介绍如何使用容器见解查看和分析 Kubernetes 群集的性能。
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: 2751d96a0527328a78030cba618d2026059f04fe
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741368"
---
# <a name="monitor-your-kubernetes-cluster-performance-with-container-insights"></a>使用容器见解监视 Kubernetes 群集性能

借助容器见解，你可以使用性能图表和运行状况，从两个角度监视托管在 Azure Kubernetes 服务 (AKS)、Azure Stack 或其他环境中的 Kubernetes 群集的工作负载。 可以直接从群集进行监视，也可以从 Azure Monitor 查看订阅中的所有群集。 在监视特定 AKS 群集时，还可以查看 Azure 容器实例。

本文将帮助你了解这两个角度，并介绍 Azure Monitor 如何帮助你快速评估、调查和解决检测到的问题。

有关如何启用容器见解的信息，请参阅[加入容器见解](container-insights-onboard.md)。

Azure Monitor 提供一个多群集视图，显示在订阅中跨资源组部署的所有运行 Linux 和 Windows Server 2019 的受监视 Kubernetes 群集的运行状况。 它显示跨所有环境发现的不受解决方案监视的群集。 可以即时了解群集运行状况，并且可以从这里向下钻取到节点和控制器性能页，或者进行导航来查看群集的性能图表。 对于发现的标识为“不受监视”的 AKS 群集，可以随时为该群集启用监视功能。

概述文章中在[此处](container-insights-overview.md#what-does-container-insights-provide)描述了使用容器见解监视 Windows Server 群集与监视 Linux 群集的主要差异。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录 [Azure 门户](https://portal.azure.com)。

## <a name="multi-cluster-view-from-azure-monitor"></a>从 Azure Monitor 获得的多群集视图

若要查看已部署的所有 Kubernetes 群集的运行状况，请在 Azure 门户的左窗格中选择“监视”。 在“见解”部分，选择“容器”。 

![Azure Monitor 多群集仪表板示例](./media/container-insights-analyze/azmon-containers-multiview.png)

可以限定网格中显示的结果范围，以显示以下群集：

* **Azure** - Azure Kubernetes 服务中托管的 AKS 和 AKS 引擎群集
* **Azure Stack（预览版）** - Azure Stack 上托管的 AKS 引擎群集
* **非 Azure（预览版）** - 本地托管的 Kubernetes 群集
* **所有** - 查看 Azure、Azure Stack 和本地环境（加入到容器见解）中托管的所有 Kubernetes 群集

要查看特定环境中的群集，请在页面左上角的“环境”框中选择该环境。

![“环境”框选择器示例](./media/container-insights-analyze/clusters-multiview-environment-pill.png)

在“受监视的群集”选项卡上，了解以下情况：

- 多少群集处于严重或不正常状态，多少群集处于正常或未报告状态（也称未知状态）。
- 所有 [Azure Kubernetes 引擎（AKS 引擎）](https://github.com/Azure/aks-engine)部署是否都正常。
- 每个群集部署了多少节点、用户和系统 Pod。
- 多少磁盘空间可用，是否有容量问题。

包含的运行状况有：

* **正常**：VM 没有检测到任何问题，并且按要求运行。
* **严重**：检测到一个或多个严重问题，必须解决这些问题才能按预期还原正常操作状态。
* **警告**：检测到一个或多个必须解决的问题，不解决这些问题可能会导致运行状况变得严重。
* **未知**：如果服务无法与节点或 Pod 建立连接，则状态将更改为“未知”状态。
* **找不到**：工作区、资源组或包含此解决方案的工作区的订阅已删除。
* **未授权**：用户没有读取工作区中的数据所需的权限。
* **错误**：尝试从工作区中读取数据时发生错误。
* **配置不正确**：未在指定工作区中正确配置容器见解。
* **没有数据**：在过去 30 分钟内未向工作区报告数据。

在进行运行状态计算时，会将这三种状况中“最差”的一种视为群集的总体状况，但存在一种例外情况。 如果这三种状态中的任何一种为“未知”，则群集总体状态会显示为“未知”。

下表提供了计算明细，该计算控制多群集视图中受监视群集的运行状况。

| 受监视的群集 |状态 |可用性 |
|-------|-------|-----------------|
|**用户 Pod**| | |
| |正常 |100% |
| |警告 |90 - 99% |
| |关键 |<90% |
| |未知 |如果未在过去 30 分钟报告 |
|**系统 Pod**| | |
| |正常 |100% |
| |警告 |不适用 |
| |关键 |<100% |
| |未知 |如果未在过去 30 分钟报告 |
|**Node** | | |
| |正常 |>85% |
| |警告 |60 - 84% |
| |关键 |<60% |
| |未知 |如果未在过去 30 分钟报告 |

在群集列表中，可以通过选择群集名称向下钻取到“群集”页。 然后选择该特定群集的“节点”列中的节点汇总转到“节点”性能页。 或者，可以通过选择“用户 Pod”或“系统 Pod”列的汇总向下钻取到“控制器”性能页。

## <a name="view-performance-directly-from-a-cluster"></a>直接从群集查看性能

可以直接从 AKS 群集访问容器见解，方法是：从左窗格中选择“见解” > “群集”，或者从多群集视图中选择一个群集 。 有关群集的信息组织成四个透视图：

- 群集
- Nodes
- 控制器
- 容器

>[!NOTE]
>本文剩余部分所述体验也适用于查看从多群集视图中选中时托管在 Azure Stack 或其他环境上的 Kubernetes 群集的性能和运行状况。

默认页会打开并显示四个线形性能图表，这些图表会显示群集的主要性能指标。

![“群集”选项卡上的性能图表示例](./media/container-insights-analyze/containers-cluster-perfview.png)

性能图表显示四个性能指标：

- **节点 CPU 利用率&nbsp;%** ：从聚合视角反映整个群集的 CPU 利用率。 若要按时间范围筛选结果，请在图表上方的百分位选择器中选择“Avg”、“Min”、“50th”、“90th”、“95th”或“Max”。 筛选器可以单独使用，也可以组合使用。
- **节点内存利用率&nbsp;%** ：提供整个群集的内存利用率的聚合视角。 若要按时间范围筛选结果，请在图表上方的百分位选择器中选择“Avg”、“Min”、“50th”、“90th”、“95th”或“Max”。 筛选器可以单独使用，也可以组合使用。
- **节点计数**：Kubernetes 提供的节点计数和状态。 表示的群集节点状态为“总计”、“就绪”和“未就绪”。 可以在图表上方的选择器中单独筛选或以组合方式进行筛选。
- **活动 Pod 计数**：Kubernetes 提供的 Pod 计数和状态。 表示的 Pod 状态为“总计”、“挂起”、“正在运行”、“未知”、“成功”或“失败”。 可以在图表上方的选择器中单独筛选或以组合方式进行筛选。

使用向左和向右箭头键可以循环浏览图表上的每个数据点。 使用向上和向下箭头键可以循环浏览百分位行。 选择其中任一图表右上角的图钉图标会将所选图表固定到你查看的最后一个 Azure 仪表板。 在仪表板中，可以调整图表大小及其位置。 在仪表板中选择图表会将你重定向到容器见解，并加载正确的范围和视图。

容器见解也支持 Azure Monitor [指标资源管理器](../essentials/metrics-getting-started.md)在该管理器中，你可以创建自己的绘图图表、将趋势相关联并对其进行调查，以及将内容固定到仪表板。 在指标资源管理器中，还可以使用所设置的条件将指标可视化为[基于指标的警报规则](../alerts/alerts-metric.md)的基础。

## <a name="view-container-metrics-in-metrics-explorer"></a>在指标资源管理器中查看容器指标

在指标资源管理器中，可以通过容器见解查看聚合的节点和 Pod 利用率指标。 下表汇总了详细信息，这些信息有助于你了解如何使用指标图表来可视化容器指标。

|命名空间 | 指标 | 说明 |
|----------|--------|-------------|
| insights.container/nodes | |
| | cpuUsageMillicores | 整个群集的 CPU 利用率的聚合计量值。 一个 CPU 核心拆分为 1000 个单位（milli 表示 1000）。 用于确定某个容器中的核心使用率，该容器中可能有许多应用程序使用一个核心。|
| | cpuUsagePercentage | 整个群集的聚合平均 CPU 利用率，以百分比计量。|
| | memoryRssBytes | 使用的容器 RSS 内存，以字节为单位。|
| | memoryRssPercentage | 使用的容器 RSS 内存，以百分比表示。|
| | memoryWorkingSetBytes | 使用的容器工作集内存。|
| | memoryWorkingSetPercentage | 使用的容器工作集内存，以百分比表示。 |
| | nodesCount | Kubernetes 中的节点计数。|
| insights.container/pods | |
| | PodCount | Kubernetes 中的 Pod 计数。|

可以[拆分](../essentials/metrics-charts.md#apply-splitting)指标，以便按维度来查看它，并以可视化方式表现其片段相互之间的不同之处。 对于节点，可以按主机维度将图表分段。 对于 Pod，可按以下维度将其分段：

* 控制器
* Kubernetes 命名空间
* 节点
* 阶段

## <a name="analyze-nodes-controllers-and-container-health"></a>分析节点、控制器和容器运行状况

切换到“节点”、“控制器”和“容器”选项卡时，页面右侧会自动显示属性窗格    。 它显示所选项的属性，包括定义用于组织 Kubernetes 对象的标签。 选择一个 Linux 节点时，“本地磁盘容量”部分还会显示可用磁盘空间以及用于每个提供给节点的磁盘的百分比。 单击窗格中的 **>>** 链接可查看或隐藏窗格。

在层次结构中展开对象时，属性窗格将根据所选对象进行更新。 在窗格中，还可通过选择窗格顶部的“查看实时数据(预览)”链接，查看 Kubernetes 容器日志 (stdout/stderror)、事件和 Pod 指标  。 要详细了解授予和控制查看此数据的访问权限所需的配置，请参阅[设置实时数据（预览版）](container-insights-livedata-setup.md)。 查看群集资源时，可以实时查看容器中的该数据。 有关此功能的详细信息，请参阅[如何实时查看 Kubernetes 日志、事件和 Pod 指标](container-insights-livedata-overview.md)。 要根据预定义的日志搜索查看工作区中存储的 Kubernetes 日志数据，请从“在 Analytics 中查看”下拉列表中选择“查看容器日志”。  有关本主题的其他信息，请参阅[如何从容器见解查询日志](container-insights-log-query.md)。

使用页面顶部的“+ 添加筛选器”选项可按“服务”、“节点”、“命名空间”或“节点池”筛选视图的结果。     选择筛选范围后，选择“选择值”字段中显示的某个值。 筛选器在配置后会在用户查看任何视角的 AKS 群集时进行全局应用。 公式只支持等号。 可以在第一个筛选器的基础上添加更多的筛选器，进一步缩小结果范围。 例如，如果指定了一个按“节点”筛选的筛选器，则只能为第二个筛选器选择“服务”或“命名空间”。  

在一个选项卡中指定一个筛选器后，如果又选择一个筛选器，则前者会继续应用。 在选择指定筛选器旁边的 **x** 符号后，该筛选器会被删除。

切换到“节点”选项卡，行层次结构遵循以群集节点开头的 Kubernetes 对象模型。 展开节点可查看在节点上运行的一个或多个 Pod。 如果将多个容器分组到 Pod 中，它们将在层次结构中的最后一行显示。 还可查看有多少非 Pod 相关工作负荷在主机上运行（如果主机有处理器或内存使用压力）。

![性能视图中的 Kubernetes 节点层次结构示例](./media/container-insights-analyze/containers-nodes-view.png)

在列表中，运行 Windows Server 2019 OS 的 Windows Server 容器显示在所有基于 Linux 的节点后面。 展开 Windows Server 节点时，可以查看在节点上运行的一个或多个 Pod 和容器。 选择节点后，属性窗格会显示版本信息。

![列出了 Windows Server 节点的示例节点层次结构](./media/container-insights-analyze/nodes-view-windows.png)

运行 Linux OS 的 Azure 容器实例虚拟节点显示在列表中最后一个 AKS 群集节点之后。 展开容器实例虚拟节点时，可以查看在节点上运行的一个或多个容器实例 Pod 和容器。 不会为节点（只为 Pod）收集和报告指标。

![列出了容器实例的示例节点层次结构](./media/container-insights-analyze/nodes-view-aci.png)

从展开的节点中，你可以从在节点上运行的 pod 或容器向下钻取到控制器来查看针对该控制器筛选的性能数据。 选择特定节点的“控制器”列下的值。

![显示性能视图中从节点到控制器的向下钻取的屏幕截图](./media/container-insights-analyze/drill-down-node-controller.png)

从页面顶部选择控制器或容器，查看这些对象的状态和资源使用率。 若要查看内存利用率，可在“指标”下拉列表中选择“内存 RSS”或“内存工作集”  。 仅 Kubernetes 1.8 版和更高版本支持 **内存 RSS**。 否则，看到的 **Min&nbsp;%** 值会显示为 *NaN&nbsp;%* ，它表示未定义或无法表示的值的数值数据类型值。

![容器节点性能视图](./media/container-insights-analyze/containers-node-metric-dropdown.png)

“内存工作集”显示包含的驻留内存和虚拟内存（缓存），是应用程序正在使用的内存的总和。 “内存 RSS”只显示主内存（即，只显示驻留内存，而不显示任何其他内存）。 此指标显示可用内存的实际容量。 驻留内存与虚拟内存之间有何差别？

- 驻留内存（也称为主内存）是可用于群集节点的实际计算机内存量。

- 虚拟内存是保留的硬盘空间（缓存），操作系统在遇到内存压力时，会使用这些空间将内存中的数据交换到磁盘，并在需要时将数据提回到内存。

默认情况下，性能数据基于过去六个小时的数据，但可以使用左上角的“时间范围”选项更改时间窗口。 还可以在百分位选择器中选择“Min”、“Avg”、“50th”、“90th”、“95th”和“Max”，在时间范围内筛选结果。     

![用于数据筛选的百分位选择](./media/container-insights-analyze/containers-metric-percentile-filter.png)

当鼠标悬停在“趋势”列下的条形图上方时，每一条都显示 15 分钟示例期间内的 CPU 或内存使用情况（具体取决于所选指标）。 通过键盘选择趋势图后，使用 Alt+PageUp 或 Alt+PageDown 键单独循环浏览每个条形。 显示的详细信息与将鼠标悬停在该条形上相同。

![趋势条形图悬停示例](./media/container-insights-analyze/containers-metric-trend-bar-01.png)

在下一个示例中，对于列表中的第一节点 *aks-nodepool1-* ，其“容器”的值为 9。 此值表示部署的容器汇总总数。

![单节点容器数汇总示例](./media/container-insights-analyze/containers-nodes-containerstotal.png)

此信息有助于快速确定群集中节点间的容器是否适当均衡。

下表描述了查看“节点”选项卡时显示的信息。

| 列 | 说明 |
|--------|-------------|
| 名称 | 主机的名称。 |
| 状态 | 节点状态的 Kubernetes 视图。 |
| Min&nbsp;%、Avg&nbsp;%、50th&nbsp;%、90th&nbsp;%、95th&nbsp;%、Max&nbsp;%  | 基于所选时段百分位的平均节点百分比。 |
| Min、Avg、50th、90th、95th、Max | 基于所选时段的平均节点实际值。 平均值根据为节点设置的 CPU/内存限制进行计算。 对于 Pod 和容器，平均值为主机报告的平均值。 |
| 容器 | 容器数量。 |
| 运行时间 | 表示节点启动或重启后的时间。 |
| 控制器 | 仅适用于容器和 Pod。 它显示驻留的控制器。 并非所有 Pod 都在控制器中，因此有些 Pod 可能会显示 **N/A**。 |
| 趋势 Min&nbsp;%、Avg&nbsp;%、50th&nbsp;%、90th&nbsp;%、95th&nbsp;%、Max&nbsp;% | 条形图趋势表示控制器的平均百分位指标百分比。 |

在展开名为“其他进程”的节点后，你可能会注意到一个工作负载。 它表示在节点上运行的非容器化进程，包括：

* 自行托管的或托管 Kubernetes 非容器化进程

* 容器运行时进程

* Kubelet

* 节点上运行的系统进程

* 节点硬件或 VM 上运行的其他非 Kubernetes 工作负载

其计算方法为：CAdvisor 中的总用量  -  容器化进程的用量 。

在选择器中，选择“控制器”。

![选择“控制器”视图](./media/container-insights-analyze/containers-controllers-tab.png)

可以在此处查看控制器和容器实例虚拟节点控制器或未连接到控制器的虚拟节点 Pod 的性能运行状况。

![\<名称> 控制器性能视图](./media/container-insights-analyze/containers-controllers-view.png)

行层次结构以控制器开始。 展开控制器时，可以查看一个或多个 Pod。 展开 Pod，最后一行显示分组到 Pod 的容器。 从展开的控制器中，你可以向下钻取到运行它的节点来查看针对该节点筛选的性能数据。 未连接到控制器的容器实例 Pod 在列表中最后列出。

![列出了容器实例 Pod 的示例控制器层次结构](./media/container-insights-analyze/controllers-view-aci.png)

选择特定控制器的“节点”列下的值。

![性能视图中从控制器到节点的示例向下钻取](./media/container-insights-analyze/drill-down-controller-node.png)

下表描述了查看控制器时显示的信息：

| 列 | 说明 |
|--------|-------------|
| 名称 | 控制器的名称。|
| 状态 | 容器完成运行并处于“正常”、“已终止”、“已失败”、“已停止”或“已暂停”等状态时的汇总状态    。 如果容器仍在运行，但是状态未正确显示或者未被代理选择并且超出 30 分钟后仍未响应，则状态为“未知”。 下表提供了状态图标的更多详细信息。|
| Min&nbsp;%、Avg&nbsp;%、50th&nbsp;%、90th&nbsp;%、95th&nbsp;%、Max&nbsp;%| 每个实体在选定指标和百分位的平均百分比的汇总平均值。 |
| Min、Avg、50th、90th、95th、Max  | 容器在选定百分位的平均 CPU millicore 或内存性能汇总。 平均值根据为 Pod 设置的 CPU/内存限制进行计算。 |
| 容器 | 控制器或 Pod 的容器总数。 |
| 重启数 | 容器重启计数汇总。 |
| 运行时间 | 表示容器启动后的时间。 |
| 节点 | 仅适用于容器和 Pod。 它显示驻留的控制器。 |
| 趋势 Min&nbsp;%、Avg&nbsp;%、50th&nbsp;%、90th&nbsp;%、95th&nbsp;%、Max&nbsp;% | 条形图趋势表示控制器的平均百分位指标。 |

状态字段中的图标指示容器的联机状态。

| 图标 | 状态 |
|--------|-------------|
| ![“就绪”运行状态图标](./media/container-insights-analyze/containers-ready-icon.png) | 正在运行（就绪）|
| ![“正在等待”或“已暂停”状态图标](./media/container-insights-analyze/containers-waiting-icon.png) | “正在等待”或“已暂停”|
| ![“上次报告正在运行”状态图标](./media/container-insights-analyze/containers-grey-icon.png) | 上次报告正在运行但已超过 30 分钟未响应|
| ![成功状态图标](./media/container-insights-analyze/containers-green-icon.png) | 成功停止或无法停止|

状态图标显示的计数基于 Pod 提供的数据。 它显示最差的两个状态。将鼠标悬停在状态上时，它显示容器中所有 Pod 的汇总状态。 如果没有就绪状态，状态值会显示 **(0)** 。

在选择器中，选择“容器”。

![选择“容器”视图](./media/container-insights-analyze/containers-containers-tab.png)

在此处可查看 Azure Kubernetes 和 Azure 容器实例容器的性能运行状况。

![\<名称> 容器性能视图](./media/container-insights-analyze/containers-containers-view.png)

从容器中，可以向下钻取到某个 pod 或节点来查看针对该对象筛选的性能数据。 选择特定容器的“Pod”或“节点”列下的值。 

![性能视图中从节点到容器器的示例向下钻取](./media/container-insights-analyze/drill-down-controller-node.png)

下表描述了查看容器时显示的信息。

| 列 | 说明 |
|--------|-------------|
| 名称 | 控制器的名称。|
| 状态 | 容器状态（如果有）。 接下来的表格提供状态图标的更多详细信息。|
| Min&nbsp;%、Avg&nbsp;%、50th&nbsp;%、90th&nbsp;%、95th&nbsp;%、Max&nbsp;% | 每个实体在选定指标和百分位的平均百分比汇总。 |
| Min、Avg、50th、90th、95th、Max | 容器在选定百分位的平均 CPU millicore 或内存性能汇总。 平均值根据为 Pod 设置的 CPU/内存限制进行计算。 |
| Pod | Pod 驻留的容器。|
| 节点 |  容器驻留的节点。 |
| 重启数 | 表示容器启动后的时间。 |
| 运行时间 | 表示容器启动或重启后的时间。 |
| 趋势 Min&nbsp;%、Avg&nbsp;%、50th&nbsp;%、90th&nbsp;%、95th&nbsp;%、Max&nbsp;% | 条形图趋势表示容器的平均百分位指标百分比。 |

状态字段中的图标指示 Pod 的联机状态，如下表所述。

| 图标 | 状态 |
|--------|-------------|
| ![“就绪”运行状态图标](./media/container-insights-analyze/containers-ready-icon.png) | 正在运行（就绪）|
| ![“正在等待”或“已暂停”状态图标](./media/container-insights-analyze/containers-waiting-icon.png) | “正在等待”或“已暂停”|
| ![“上次报告正在运行”状态图标](./media/container-insights-analyze/containers-grey-icon.png) | 上次报告正在运行但已超过 30 分钟未响应|
| ![“已终止”状态图标](./media/container-insights-analyze/containers-terminated-icon.png) | 成功停止或无法停止|
| ![“已失败”状态图标](./media/container-insights-analyze/containers-failed-icon.png) | “已失败”状态 |

## <a name="monitor-and-visualize-network-configurations"></a>监视和可视化网络配置
Azure 网络策略管理器包含信息丰富的 Prometheus 指标，可用于监视和更好地了解网络配置。 它在 Azure 门户或 Grafana 实验室中提供了内置的可视化效果。 有关详细信息，请参阅[使用 Azure NPM 监视和可视化网络配置](../../virtual-network/kubernetes-network-policies.md#monitor-and-visualize-network-configurations-with-azure-npm)。


## <a name="workbooks"></a>工作簿

工作簿可将文本、日志查询、指标和参数合并到丰富的交互式报表中，使你能够分析群集性能。 有关可用于容器见解的工作簿的说明，请参阅[容器见解中的工作簿](container-insights-reports.md)。


## <a name="next-steps"></a>后续步骤

- 请查看[使用容器见解创建性能警报](./container-insights-log-alerts.md)，了解如何针对 CPU 和内存使用率过高的情况创建警报，为 DevOps 或操作流程和过程提供支持。

- 通过查看[日志查询示例](container-insights-log-query.md)，可查看预定义的查询和示例，从而对其进行评估或自定义，以便对群集执行警报、可视化或分析操作。

- 查看[监视器群集运行状况](./container-insights-overview.md)，了解如何查看 Kubernetes 群集的运行状况状态。