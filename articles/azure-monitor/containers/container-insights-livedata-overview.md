---
title: 查看容器见解的实时数据 |Microsoft Docs
description: 本文介绍了 Kubernetes 日志、事件和 pod 指标的实时视图，而无需在 Container insights 中使用 kubectl。
ms.topic: conceptual
ms.date: 03/04/2021
ms.custom: references_regions
ms.openlocfilehash: 5277f5051e291e9058255d8920ac0be950389704
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102203192"
---
# <a name="how-to-view-kubernetes-logs-events-and-pod-metrics-in-real-time"></a>如何实时查看 Kubernetes 日志、事件和 Pod 指标

容器见解包括 "实时数据" 功能，这是一项高级诊断功能，可让你直接访问 Azure Kubernetes 服务 (AKS) 容器日志 (stdout/stderror) 、事件和 pod 指标。 它公开对 `kubectl logs -c`、`kubectl get` 事件和 `kubectl top pods` 的直接访问。 控制台窗格显示由容器引擎生成的日志、事件和指标，可以进一步协助实时排查问题。

本文详细介绍了此功能，并帮助你了解如何使用此功能。

有关设置实时数据功能或排查其问题的帮助，请参阅我们的 [安装指南](container-insights-livedata-setup.md)。 该功能直接访问 Kubernetes API，有关身份验证模型的其他信息可以在[此处](https://kubernetes.io/docs/concepts/overview/kubernetes-api/)找到。

## <a name="view-aks-resource-live-logs"></a>查看 AKS 资源实时日志
使用以下过程从 AKS 资源视图查看适用于或不包含容器见解的 pod、部署和副本集的实时日志。

1. 在 Azure 门户中，浏览到 AKS 群集资源组，然后选择 AKS 资源。

2. 在菜单的 " **Kubernetes 资源**" 部分中选择 **工作负荷**。

3. 从相应的选项卡中选择 pod、部署和副本集。

4. 从资源菜单中选择 " **实时日志** "。

5. 选择要开始收集实时数据的 pod。

    [![部署实时日志](./media/container-insights-livedata-overview/live-data-deployment.png)](./media/container-insights-livedata-overview/live-data-deployment.png#lightbox)

## <a name="view-logs"></a>查看日志

可以在“节点”、“控制器”和“容器”视图中查看容器引擎生成的实时日志数据  。 若要查看日志数据，请执行以下步骤。

1. 在 Azure 门户中，浏览到 AKS 群集资源组，然后选择 AKS 资源。

2. 在 AKS 群集仪表板上，在左侧的“监视”下，选择“见解” 。

3. 选择“节点”、“控制器”或“容器”选项卡  。

4. 从 "性能" 网格中选择一个对象，然后在右侧的 "属性" 窗格中选择 " **查看实时数据** " 选项。 如果使用 Azure AD 为 AKS 群集配置了单一登录，系统会在该浏览器会话期间向你提示进行首次使用所需的身份验证。 选择帐户，然后使用 Azure 完成身份验证。

    >[!NOTE]
    >通过从属性窗格中选择“在分析中查看”选项从 Log Analytics 工作区中查看数据时，日志搜索结果可能会显示可能不再存在的节点、守护程序集、副本集、作业、Cron 作业、Pod 和容器       。 尝试搜索在 `kubectl` 中不可用的容器的日志也会失败。 查看[在分析中查看](container-insights-log-search.md#search-logs-to-analyze-data)功能，了解有关查看历史日志、事件和指标的详细信息。

成功进行身份验证后，"实时数据" 控制台窗格将显示在 "性能" 数据网格下，你可以在其中查看连续流中的日志数据。 如果提取状态指示器显示绿色复选标记（位于窗格最右侧），则表示可以检索数据并开始将其流式传输到控制台。

![节点属性窗格的“查看数据”选项](./media/container-insights-livedata-overview/node-properties-pane.png)

窗格标题会显示进行容器分组时所使用的 Pod 的名称。

## <a name="view-events"></a>查看事件

当选择了容器、pod、node、ReplicaSet、DaemonSet、job、CronJob 或 Deployment 时，可以从 " **节点**"、" **控制器**"、" **容器** **" 和 "部署"** 视图中的容器引擎生成实时事件数据。 若要查看事件，请执行以下步骤。

1. 在 Azure 门户中，浏览到 AKS 群集资源组，然后选择 AKS 资源。

2. 在 AKS 群集仪表板上，在左侧的“监视”下，选择“见解” 。

3. 选择 " **节点**"、" **控制器**"、" **容器**" 或 " **部署** " 选项卡。

4. 从 "性能" 网格中选择一个对象，然后在右侧的 "属性" 窗格中选择 " **查看实时数据** " 选项。 如果使用 Azure AD 为 AKS 群集配置了单一登录，系统会在该浏览器会话期间向你提示进行首次使用所需的身份验证。 选择帐户，然后使用 Azure 完成身份验证。

    >[!NOTE]
    >通过从属性窗格中选择“在分析中查看”选项从 Log Analytics 工作区中查看数据时，日志搜索结果可能会显示可能不再存在的节点、守护程序集、副本集、作业、Cron 作业、Pod 和容器       。 尝试搜索在 `kubectl` 中不可用的容器的日志也会失败。 查看[在分析中查看](container-insights-log-search.md#search-logs-to-analyze-data)功能，了解有关查看历史日志、事件和指标的详细信息。

成功进行身份验证后，"实时数据" 控制台窗格将显示在 "性能" 数据网格下。 如果提取状态指示器显示绿色复选标记（位于窗格最右侧），则表示可以检索数据并开始将其流式传输到控制台。

如果所选对象是容器，请在窗格中选择“事件”选项。 如果选择了节点、Pod 或控制器，则会自动选择“查看事件”。

![控制器属性窗格查看事件](./media/container-insights-livedata-overview/controller-properties-live-event.png)

窗格标题会显示进行容器分组时所使用的 Pod 的名称。

### <a name="filter-events"></a>筛选事件

在查看事件时，可以进一步对结果进行限制，只需使用在搜索栏右侧发现的“筛选器”药丸图标即可。 根据所选资源，药丸图标会列出可供选择的 Pod、命名空间或群集。

## <a name="view-metrics"></a>查看指标

仅当选择了 Pod 时，才能从“节点”或“控制器”视图中查看容器引擎生成的实时指标数据  。 若要查看指标，请执行以下步骤。

1. 在 Azure 门户中，浏览到 AKS 群集资源组，然后选择 AKS 资源。

2. 在 AKS 群集仪表板上，在左侧的“监视”下，选择“见解” 。

3. 选择“节点”或“控制器”选项卡 。

4. 从 "性能" 网格中选择一个 **Pod** 对象，然后在右侧的 "属性" 窗格中选择 " **查看实时数据** " 选项。 如果使用 Azure AD 为 AKS 群集配置了单一登录，系统会在该浏览器会话期间向你提示进行首次使用所需的身份验证。 选择帐户，然后使用 Azure 完成身份验证。

    >[!NOTE]
    >通过从属性窗格中选择“在分析中查看”选项从 Log Analytics 工作区中查看数据时，日志搜索结果可能会显示可能不再存在的节点、守护程序集、副本集、作业、Cron 作业、Pod 和容器       。 尝试搜索在 `kubectl` 中不可用的容器的日志也会失败。 查看[在分析中查看](container-insights-log-search.md#search-logs-to-analyze-data)功能，了解有关查看历史日志、事件和指标的详细信息。

成功进行身份验证后，"实时数据" 控制台窗格将显示在 "性能" 数据网格下。 检索指标数据并开始流式传输到控制台，以便在两个图表中显示。 窗格标题会显示进行容器分组时所使用的 Pod 的名称。

![查看 Pod 指标示例](./media/container-insights-livedata-overview/pod-properties-live-metrics.png)

## <a name="using-live-data-views"></a>使用实时数据视图
以下各部分介绍可在不同实时数据视图中使用的功能。

### <a name="search"></a>搜索
实时数据功能包括搜索功能。 在“搜索”字段中，可以通过键入关键字或字词来筛选结果，并突出显示任何匹配结果以允许快速查看。 在查看事件时，可以进一步对结果进行限制，只需使用在搜索栏右侧发现的“筛选器”药丸图标即可。 根据所选资源，药丸图标会列出可供选择的 Pod、命名空间或群集。

![实时数据控制台窗格筛选器示例](./media/container-insights-livedata-overview/livedata-pane-filter-example.png)

![用于部署的实时数据控制台窗格筛选器示例](./media/container-insights-livedata-overview/live-data-deployment-search.png)

### <a name="scroll-lock-and-pause"></a>滚动锁定和暂停

若要暂停自动滚动并控制窗格的行为，以便通过手动方式滚动浏览读取的新数据，可使用“滚动”选项。 若要重新启用自动滚动，只需再次选择“滚动”选项。 也可通过选择“暂停”选项暂停对日志或事件数据的检索，并在准备好继续时，只需选择“开始”即可 。

![实时数据控制台窗格的暂停实时视图](./media/container-insights-livedata-overview/livedata-pane-scroll-pause-example.png)

![用于部署的实时数据控制台窗格的暂停实时视图](./media/container-insights-livedata-overview/live-data-deployment-pause.png)



>[!IMPORTANT]
>在对问题进行故障排除时，建议只短暂地挂起或暂停自动滚动。 这些请求可能会影响群集上 Kubernetes API 的可用性和限制。

>[!IMPORTANT]
>此功能运行期间不会永久存储任何数据。 当你关闭或离开浏览器时，将删除在此会话期间捕获的所有信息。 数据只在指标功能的五分钟窗口内显示，任何超过五分钟的指标也将删除。 实时数据缓冲区查询合理的内存使用量限制。

## <a name="next-steps"></a>后续步骤

- 若要继续学习如何使用 Azure Monitor 以及如何监视 AKS 群集的其他方面，请参阅[查看 Azure Kubernetes 服务运行状况](container-insights-analyze.md)。

- 查看[日志查询示例](container-insights-log-search.md#search-logs-to-analyze-data)，了解预定义的查询和示例，以创建警报、呈现可视化效果或对群集执行进一步分析。
