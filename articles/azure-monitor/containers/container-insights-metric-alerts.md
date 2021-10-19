---
title: 来自容器见解的指标警报
description: 本文介绍容器见解提供的建议指标警报（公共预览版）。
ms.topic: conceptual
ms.date: 10/28/2020
ms.openlocfilehash: 7036bc7a0f161044312687d6b22171df99821e6a
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129714417"
---
# <a name="recommended-metric-alerts-preview-from-container-insights"></a>来自容器见解的建议指标警报（预览版）

若要在系统资源遇到高峰需求并即将达到容量限制时发出警报，可以使用容器见解来基于 Azure Monitor 日志中存储的性能数据创建日志警报。 容器见解现在包括针对 AKS 以及已启用 Azure Arc 的 Kubernetes 群集（公共预览版）的预配置指标警报规则。

本文介绍相关体验，并提供有关如何配置和管理这些警报规则的指导。

如果你不熟悉 Azure Monitor 警报，请在开始之前参阅 [Microsoft Azure 中的警报概述](../alerts/alerts-overview.md)。 若要详细了解指标警报，请参阅 [Azure Monitor 中的指标警报](../alerts/alerts-metric-overview.md)。

> [!NOTE]
> 从 2021 年 10 月 8 日开始，三个警报已更新以正确计算警报条件：“容器 CPU 百分比”、“容器工作集内存百分比”和“持久卷使用率百分比”。   这些新警报与其对应的先前可用警报具有相同的名称，但新警报使用更新后的新指标。 我们建议你禁用本文所述的使用“旧”指标的警报，并启用“新”指标。 “旧”指标在禁用后将不再在推荐警报中可用，但你可以手动重新启用这些旧指标。

## <a name="prerequisites"></a>先决条件

在开始之前，请确认以下事项：

* 自定义指标只在一部分 Azure 区域中可用。 [支持的区域](../essentials/metrics-custom-overview.md#supported-regions)中列出了支持的区域。

* 若要支持指标警报和其他指标的引入，对于 AKS 和已启用 Azure Arc 的 Kubernetes 群集，所需的最低代理版本分别为 mcr.microsoft.com/azuremonitor/containerinsights/ciprod:ciprod05262020 和 mcr.microsoft.com/azuremonitor/containerinsights/ciprod:ciprod09252020 。

    若要验证你的群集是否正在运行较新的代理版本，可执行以下操作之一：

    * 运行 `kubectl describe <omsagent-pod-name> --namespace=kube-system` 命令。 在返回的状态中，记下输出的“容器”部分中“映像”下 omsagent 的值。 
    * 在“节点”选项卡上选择群集节点，然后在右侧的“属性”窗格中记下“代理映像标记”下的值。  

    为 AKS 显示的版本值应为 ciprod05262020 或更高。 为已启用 Azure Arc 的 Kubernetes 群集显示的版本值应为 ciprod09252020 或更高。 如果你的群集使用了较低版本，请参阅[如何升级容器见解代理](container-insights-manage-agent.md#upgrade-agent-on-aks-cluster)以了解获取最新版本的步骤。

    有关代理发行版的详细信息，请参阅[代理发行历史记录](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)。 若要验证是否正在收集指标，可以使用 Azure Monitor 指标资源管理器，并验证“指标命名空间”中是否列出了“见解” 。 如果已列出，则可以开始设置警报。 如果未看到任何收集的指标，则表示群集服务主体或 MSI 缺少必要的权限。 若要验证 SPN 或 MSI 是否为“监视指标发布者”角色的成员，请遵循[使用 Azure CLI 按群集进行升级](container-insights-update-metrics.md#upgrade-per-cluster-using-azure-cli)部分中所述的步骤确认并设置角色分配。

## <a name="alert-rules-overview"></a>警报规则概述

为了针对重要情况发出警报，容器见解包括以下针对 AKS 和已启用 Azure Arc 的 Kubernetes 群集的指标警报：

|名称| 描述 |默认阈值 |
|----|-------------|------------------|
|(新)平均容器 CPU 使用率(%) |计算每个容器的平均 CPU 使用率。|当每个容器的平均 CPU 使用率大于 95% 时。| 
|(新)平均容器工作集内存使用率(%) |计算每个容器的平均工作集内存使用率。|当每个容器的平均工作集内存使用率大于 95% 时。 |
|平均 CPU 百分比 |计算每个节点的平均 CPU 使用率。 |当节点平均 CPU 利用率大于 80% 时 |
|平均磁盘使用率(%) |计算节点的平均磁盘使用率。|当节点的磁盘使用率大于 80% 时。 |
|(新)平均永久性卷使用率(%) |计算每个 Pod 的平均 PV 使用率。 |当每个 Pod 的平均 PV 使用率大于 80% 时。|
|平均工作集内存使用率(%) |计算节点的平均工作集内存使用率。 |当节点的平均工作集内存使用率大于 80% 时。 |
|重启容器计数 |计算重启容器的次数。 | 当容器重启次数大于 0 时。 |
|故障 Pod 计数 |计算是否有任何 Pod 处于故障状态。|当处于故障状态的 Pod 数大于 0 时。 |
|节点未就绪状态 |计算是否有任何节点处于未就绪状态。|当处于未就绪状态的节点数大于 0 时。 |
|OOM 终止的容器数 |计算 OOM 终止的容器数。 |当 OOM 终止的容器数大于 0 时。 |
|已就绪 Pod 百分比 |计算处于就绪状态的 Pod 的平均百分比。 |当处于就绪状态的 Pod 百分比小于 80% 时。|
|已完成作业计数 |计算 6 小时之前已完成的作业数。 |当超过 6 小时的已过时作业数大于 0 时。|

所有这些警报规则都有一些通用属性：

* 所有警报规则基于指标。

* 默认已禁用所有警报规则。

* 所有警报规则每分钟评估一次，它们会查看过去 5 分钟的数据。

* 默认未为警报规则分配操作组。 可以在编辑警报规则时，通过选择现有操作组或创建新的操作组，将[操作组](../alerts/action-groups.md)添加到警报。

* 可以通过直接编辑警报规则来修改其阈值。 但是，在修改每个警报规则的阈值之前，请参考该规则中提供的指导。

以下基于警报的指标具有不同于其他指标的独特行为特征：

* 仅当存在 6 个小时之前完成的作业时，才发送 completedJobsCount 指标。

* 仅当有容器重启时，才发送 containerRestartCount 指标。

* 仅当存在 OOM 终止的容器时，才发送 oomKilledContainerCount 指标。

* 当 CPU、内存 RSS、内存工作集值超过配置的阈值（默认阈值为 95%）时，将发送 cpuExceededPercentage、memoryRssExceededPercentage 和 memoryWorkingSetExceededPercentage 指标  。 这些阈值不包括为相应警报规则指定的警报条件阈值。 这意味着，如果你要从[指标资源管理器](../essentials/metrics-getting-started.md)收集这些指标并对其进行分析，我们建议将阈值配置为低于你的警报阈值的值。 可以在 ConfigMaps 文件中的 `[alertable_metrics_configuration_settings.container_resource_utilization_thresholds]` 节下，替代与这些指标的容器资源利用率阈值的收集设置相关的配置。 有关配置 ConfigMap 配置文件的详细信息，请参阅[在 ConfigMaps 中配置可对其发出警报的指标](#configure-alertable-metrics-in-configmaps)部分。

* 当永久性卷使用率百分比超过配置的阈值（默认阈值为 60%）时，将发送 pvUsageExceededPercentage 指标。 此阈值不包括为相应警报规则指定的警报条件阈值。 这意味着，如果你要从[指标资源管理器](../essentials/metrics-getting-started.md)收集这些指标并对其进行分析，我们建议将阈值配置为低于你的警报阈值的值。 可以在 ConfigMaps 文件中的 `[alertable_metrics_configuration_settings.pv_utilization_thresholds]` 节下，替代与永久性卷利用率阈值的收集设置相关的配置。 有关配置 ConfigMap 配置文件的详细信息，请参阅[在 ConfigMaps 中配置可对其发出警报的指标](#configure-alertable-metrics-in-configmaps)部分。 默认情况下，将排除 kube-system 命名空间中具有声明的永久性卷指标的收集。 若要在此命名空间中启用收集，请在 ConfigMap 文件中使用 `[metric_collection_settings.collect_kube_system_pv_metrics]` 节。 有关详细信息，请参阅[指标收集设置](./container-insights-agent-config.md#metric-collection-settings)。

## <a name="metrics-collected"></a>收集的指标

除非另有指定，否则作为此功能的一部分，将启用并收集以下指标。 带有“旧”标签且以粗体显示的指标将被替换为新指标，收集这些新指标是为了正确评估警报。

|指标命名空间 |指标 |说明 |
|---------|----|------------|
|Insights.container/nodes |cpuUsageMillicores |按主机列出的 CPU 使用量，以毫核为单位。|
|Insights.container/nodes |cpuUsagePercentage |按节点列出的 CPU 使用率百分比。|
|Insights.container/nodes |memoryRssBytes |按主机列出的内存 RSS 使用量，以字节为单位。|
|Insights.container/nodes |memoryRssPercentage |按主机列出的内存 RSS 使用率百分比。|
|Insights.container/nodes |memoryWorkingSetBytes |按主机列出的内存工作集使用量，以字节为单位。|
|Insights.container/nodes |memoryWorkingSetPercentage |按主机列出的内存工作集使用率百分比。|
|Insights.container/nodes |nodesCount |按状态列出的节点计数。|
|Insights.container/nodes |diskUsedPercentage |节点上使用的磁盘百分比，按设备列出。|
|Insights.container/pods |podCount |按控制器、命名空间、节点和阶段列出的 Pod 计数。|
|Insights.container/pods |completedJobsCount |早于用户可配置的阈值（默认阈值为 6 小时）的已完成作业计数，按控制器和 Kubernetes 命名空间列出。 |
|Insights.container/pods |restartingContainerCount |按控制器和 Kubernetes 命名空间列出的容器重启次数。|
|Insights.container/pods |oomKilledContainerCount |按控制器和 Kubernetes 命名空间列出的 OOM 终止容器计数。|
|Insights.container/pods |podReadyPercentage |按控制器和 Kubernetes 命名空间列出的处于就绪状态的 Pod 百分比。|
|Insights.container/containers |(旧)cpuExceededPercentage |超过用户可配置的阈值（默认阈值为 95.0）的容器 CPU 利用率百分比，按容器名称、控制器名称、Kubernetes 命名空间和 Pod 名称列出。<br> 已收集  |
|Insights.container/containers |(新)cpuThresholdViolated |超过用户可配置的阈值（默认阈值为 95.0）的容器 CPU 利用率百分比时触发的警报，按容器名称、控制器名称、Kubernetes 命名空间和 Pod 名称列出。<br> 已收集  |
|Insights.container/containers |(旧)memoryRssExceededPercentage |超过用户可配置的阈值（默认阈值为 95.0）的容器内存 RSS 百分比，按容器名称、控制器名称、Kubernetes 命名空间和 Pod 名称列出。|
|Insights.container/containers |(新)memoryRssThresholdViolated |超过用户可配置的阈值（默认阈值为 95.0）的容器内存 RSS 百分比时触发的警报，按容器名称、控制器名称、Kubernetes 命名空间和 Pod 名称列出。|
|Insights.container/containers |(旧)memoryWorkingSetExceededPercentage |超过用户可配置的阈值（默认阈值为 95.0）的容器内存工作集百分比，按容器名称、控制器名称、Kubernetes 命名空间和 Pod 名称列出。|
|Insights.container/containers |(新)memoryWorkingSetThresholdViolated |超过用户可配置的阈值（默认阈值为 95.0）的容器内存工作集百分比时触发的警报，按容器名称、控制器名称、Kubernetes 命名空间和 Pod 名称列出。|
|Insights.container/persistentvolumes |(旧)pvUsageExceededPercentage |超过用户可配置的阈值（默认阈值为 60.0）的永久性卷 PV 利用率百分比，按声明名称、Kubernetes 命名空间、卷名称、Pod 名称和节点名称列出。|
|Insights.container/persistentvolumes |(新)pvUsageThresholdViolated |超过用户可配置的阈值（默认阈值为 60.0）的永久性卷 PV 利用率百分比时触发的指标，按声明名称、Kubernetes 命名空间、卷名称、Pod 名称和节点名称列出。

## <a name="enable-alert-rules"></a>启用警报规则

在 Azure 门户上遵循以下步骤在 Azure Monitor 中启用指标警报。 若要使用资源管理器模板启用指标警报，请参阅[使用资源管理器模板启用](#enable-with-a-resource-manager-template)。

### <a name="from-the-azure-portal"></a>通过 Azure 门户

本部分逐步介绍如何在 Azure 门户中启用容器见解指标警报（预览版）。

1. 登录到 [Azure 门户](https://portal.azure.com/)。

2. 在 Azure 门户的左侧窗格中选择“见解”，可以直接从 AKS 群集访问容器见解指标警报（预览版）功能。

3. 在命令栏中，选择“建议的警报”。

    ![容器见解中“建议的警报”选项](./media/container-insights-metric-alerts/command-bar-recommended-alerts.png)

4. “建议的警报”属性窗格会自动显示在页面右侧。 默认已禁用列表中的所有警报规则。 选择“启用”后，将创建相应的警报规则，而规则名称将会更新，以包含警报资源的链接。

    ![“建议的警报”属性窗格](./media/container-insights-metric-alerts/recommended-alerts-pane.png)

    选择“启用/禁用”切换开关启用警报后，将创建一条警报规则，而规则名称将会更新，以包含实际警报资源的链接。

    ![启用警报规则](./media/container-insights-metric-alerts/recommended-alerts-pane-enable.png)

5. 警报规则不与通知用户已触发警报的[操作组](../alerts/action-groups.md)相关联。 选择“未分配操作组”，然后在“操作组”页上，选择“添加”或“创建”指定现有的操作组或创建操作组   。

    ![选择操作组](./media/container-insights-metric-alerts/select-action-group.png)

### <a name="enable-with-a-resource-manager-template"></a>使用资源管理器模板启用

可以使用 Azure 资源管理器模板和参数文件在 Azure Monitor 中创建包含的指标警报。

基本步骤如下所示：

1. 从 [GitHub](https://github.com/microsoft/Docker-Provider/tree/ci_dev/alerts/recommended_alerts_ARM) 下载说明了如何创建警报的一个或所有可用模板。

2. 创建并使用 JSON 格式的[参数文件](../../azure-resource-manager/templates/parameter-files.md)，以设置创建警报规则所需的值。

3. 从 Azure 门户、PowerShell 或 Azure CLI 部署模板。

#### <a name="deploy-through-azure-portal"></a>通过 Azure 门户进行部署

1. 将 Azure 资源管理器模板和参数文件下载并保存到本地文件夹，以使用以下命令创建警报规则：

2. 若要通过门户部署自定义模板，请在 [Azure 门户](https://portal.azure.com)中选择“创建资源”。

3. 搜索“模板”，然后选择“模板部署” 。

4. 选择“创建”  。

5. 你将看到多个用于创建模板的选项，请选择“在编辑器中生成自己的模板”。

6. 在“编辑模板”页上选择“加载文件”，然后选择模板文件 。

7. 在“编辑模板”页上选择“保存” 。

8. 在“自定义部署”页上指定以下各项，并在完成后选择“购买”以部署该模板并创建警报规则 。

    * 资源组
    * 位置
    * 警报名称
    * 群集资源 ID

#### <a name="deploy-with-azure-powershell-or-cli"></a>使用 Azure PowerShell 或 CLI 部署

1. 将 Azure 资源管理器模板和参数文件下载并保存到本地文件夹，以使用以下命令创建警报规则：

2. 可以通过 PowerShell 或 Azure CLI 使用模板和参数文件创建指标警报。

    使用 Azure PowerShell

    ```powershell
    Connect-AzAccount

    Select-AzSubscription -SubscriptionName <yourSubscriptionName>
    New-AzResourceGroupDeployment -Name CIMetricAlertDeployment -ResourceGroupName ResourceGroupofTargetResource `
    -TemplateFile templateFilename.json -TemplateParameterFile templateParameterFilename.parameters.json
    ```

    使用 Azure CLI

    ```azurecli
    az login

    az deployment group create \
    --name AlertDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file templateFileName.json \
    --parameters @templateParameterFilename.parameters.json
    ```

    >[!NOTE]
    >虽然可以在与目标资源不同的资源组中创建指标警报，但建议使用与目标资源相同的资源组。

## <a name="edit-alert-rules"></a>编辑警报规则

可以查看和管理容器见解警报规则，以编辑其阈值，或者为 AKS 群集配置[操作组](../alerts/action-groups.md)。 除了从 Azure 门户和 Azure CLI 执行这些操作以外，还可以直接在容器见解中从 AKS 群集执行这些操作。

1. 在命令栏中，选择“建议的警报”。

2. 若要修改阈值，请在“建议的警报”窗格中选择已启用的警报。 在“编辑规则”中，选择要编辑的“警报条件” 。

    * 若要修改警报规则阈值，请选择“条件”。
    * 若要指定现有的操作组或创建操作组，请在“操作组”下选择“添加”或“创建”  

若要查看为已启用的规则创建的警报，请在“建议的警报”窗格中选择“查看警报” 。 随后你将重定向到 AKS 群集的警报菜单，可在其中查看当前为群集创建的所有警报。

## <a name="configure-alertable-metrics-in-configmaps"></a>在 ConfigMaps 中配置可对其发出警报的指标

执行以下步骤来配置 ConfigMap 配置文件，以替代默认的利用率阈值。 这些步骤仅适用于以下可对其发出警报的指标：

* cpuExceededPercentage
* memoryRssExceededPercentage
* memoryWorkingSetExceededPercentage
* pvUsageExceededPercentage

1. 编辑 ConfigMap YAML 文件中的 `[alertable_metrics_configuration_settings.container_resource_utilization_thresholds]` 或 `[alertable_metrics_configuration_settings.pv_utilization_thresholds]` 节。

   - 若要将 cpuExceededPercentage 阈值修改为 90%，并在达到或超过该阈值时开始收集此指标，请使用以下示例配置 ConfigMap 文件：

     ```
     [alertable_metrics_configuration_settings.container_resource_utilization_thresholds]
         # Threshold for container cpu, metric will be sent only when cpu utilization exceeds or becomes equal to the following percentage
         container_cpu_threshold_percentage = 90.0
         # Threshold for container memoryRss, metric will be sent only when memory rss exceeds or becomes equal to the following percentage
         container_memory_rss_threshold_percentage = 95.0
         # Threshold for container memoryWorkingSet, metric will be sent only when memory working set exceeds or becomes equal to the following percentage
         container_memory_working_set_threshold_percentage = 95.0
     ```

   - 若要将 pvUsageExceededPercentage 阈值修改为 80%，并在达到或超过该阈值时开始收集此指标，请使用以下示例配置 ConfigMap 文件：

     ```
     [alertable_metrics_configuration_settings.pv_utilization_thresholds]
         # Threshold for persistent volume usage bytes, metric will be sent only when persistent volume utilization exceeds or becomes equal to the following percentage
         pv_usage_threshold_percentage = 80.0
     ```

2. 运行以下 kubectl 命令：`kubectl apply -f <configmap_yaml_file.yaml>`。

    示例：`kubectl apply -f container-azm-ms-agentconfig.yaml`。

配置更改可能需要几分钟时间才能完成并生效，群集中的所有 omsagent pod 将会重启。 所有 omsagent Pod 的重启是轮流式的重启，而不是一次性全部重启。 重启完成后，将显示类似于以下示例并包含结果的消息：`configmap "container-azm-ms-agentconfig" created`。

## <a name="next-steps"></a>后续步骤

- 请参阅[日志查询示例](container-insights-log-query.md)，以查看预定义的查询，以及用于发警报、可视化或分析群集的评估或自定义示例。

- 若要详细了解 Azure Monitor 以及如何监视 Kubernetes 群集的其他方面，请参阅[查看 Kubernetes 群集性能](container-insights-analyze.md)。
