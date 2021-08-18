---
title: 配置容器见解代理数据收集 | Microsoft Docs
description: 本主题介绍如何配置容器见解代理，以控制 stdout/stderr 和环境变量日志收集。
ms.topic: conceptual
ms.date: 10/09/2020
ms.openlocfilehash: bd818d03d74042e7f58cbc8889ce862279706bec
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741324"
---
# <a name="configure-agent-data-collection-for-container-insights"></a>为容器见解配置代理数据收集

容器见解通过容器化代理，从部署到托管 Kubernetes 群集的容器工作负载收集 stdout、stderr 和环境变量。 可以创建一个自定义的 Kubernetes ConfigMap 用于控制此体验，以配置代理数据收集设置。 

本文演示如何根据要求创建 ConfigMap 和配置数据收集。

>[!NOTE]
>对于 Azure Red Hat OpenShift V3，需在 openshift-azure-logging 命名空间中创建一个模板 ConfigMap 文件。 
>

## <a name="configmap-file-settings-overview"></a>ConfigMap 文件设置概述

我们已提供一个模板 ConfigMap 文件，你可以使用自定义内容轻松编辑此文件，而无需从头开始创建。 在开始之前，请查看有关 [ConfigMap](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) 的 Kubernetes 文档，并熟悉如何创建、配置和部署 ConfigMap。 这样，就可以按命名空间或者在整个群集中筛选 stderr 和 stdout，以及筛选在所有群集 pod/节点中运行的任何容器的环境变量。

>[!IMPORTANT]
>支持从容器工作负荷收集 stdout、stderr 和环境变量的最低代理版本为 ciprod06142019 或以上。 若要验证代理版本，请在“节点”选项卡中选择一个节点，然后在属性窗格中记下“代理映像标记”属性的值。 有关代理版本和每个版本中包含的内容的其他信息，请参阅[代理发行说明](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod)。

### <a name="data-collection-settings"></a>数据收集设置

下表描述了可以配置的用于控制数据收集的设置：

| 密钥 | 数据类型 | 值 | 说明 |
|--|--|--|--|
| `schema-version` | 字符串（区分大小写） | v1 | 这是代理在分析<br> ConfigMap 时使用的架构版本。<br> 当前支持的架构版本为 v1。<br> 不支持修改此值，评估<br> ConfigMap 时会拒绝修改的值。 |
| `config-version` | String |  | 支持在源代码管理系统/存储库中跟踪此配置文件的版本。<br> 允许的最大字符数为 10，所有其他字符将会截掉。 |
| `[log_collection_settings.stdout] enabled =` | 布尔 | true 或 false | 此设置控制是否启用 stdout 容器日志收集。 如果设置为 `true` 且未在 stdout 日志收集中排除任何命名空间<br> （下面的 `log_collection_settings.stdout.exclude_namespaces` 设置），则会从群集中所有 pod/节点的所有容器收集 stdout 日志。 如果未在 ConfigMaps 中指定，<br> 默认值为 `enabled = true`。 |
| `[log_collection_settings.stdout] exclude_namespaces =` | String | 逗号分隔的数组 | 不收集其 stdout 日志的 Kubernetes 命名空间数组。 仅当<br> `log_collection_settings.stdout.enabled`<br> 设置为 `true` 时，此设置才会生效。<br> 如果未在 ConfigMap 中指定，默认值为<br> `exclude_namespaces = ["kube-system"]`. |
| `[log_collection_settings.stderr] enabled =` | 布尔 | true 或 false | 此设置控制是否启用 stderr 容器日志收集。<br> 如果设置为 `true` 且未在 stdout 日志收集中排除任何命名空间<br> （`log_collection_settings.stderr.exclude_namespaces` 设置），则会从群集中所有 pod/节点的所有容器收集 stderr 日志。<br> 如果未在 ConfigMaps 中指定，默认值为<br> `enabled = true`. |
| `[log_collection_settings.stderr] exclude_namespaces =` | String | 逗号分隔的数组 | 不收集其 stderr 日志的 Kubernetes 命名空间数组。<br> 仅当<br> `log_collection_settings.stdout.enabled` 设置为 `true`。<br> 如果未在 ConfigMap 中指定，默认值为<br> `exclude_namespaces = ["kube-system"]`. |
| `[log_collection_settings.env_var] enabled =` | 布尔 | true 或 false | 此设置控制<br> 群集中所有 Pod/节点的环境变量集合，<br> 默认设置为 `enabled = true`（如果未在<br> ConfigMaps 中指定）。<br> 如果环境变量集合已全局启用，则可对特定容器禁用它，<br> 方法是将环境变量<br> `AZMON_COLLECT_ENV` 设置为 False，可以在 Dockerfile 设置中这样做，也可以在 [Pod 的配置文件](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/)（位于 **env:** 部分下）中这样做。<br> 如果环境变量集合已全局禁用，则不能对特定容器启用集合（也就是说，可以在容器级别应用的唯一重写是在集合已全局启用的情况下禁用该集合）。 |
| `[log_collection_settings.enrich_container_logs] enabled =` | 布尔 | true 或 false | 此设置控制容器日志扩充，以填充写入群集中所有容器日志的<br> ContainerLog 表的每条日志记录的 Name 和 Image 属性值。<br> 此设置在 ConfigMap 中未指定时，默认为 `enabled = false`。 |
| `[log_collection_settings.collect_all_kube_events]` | 布尔 | true 或 false | 此设置支持收集所有类型的 Kube 事件。<br> 默认情况下，不收集 Normal 类型的 Kube 事件。 将此设置设为 `true` 时，不再筛选 Normal 事件，并将收集所有事件。<br> 默认情况下，这设置为 `false`。 |

### <a name="metric-collection-settings"></a>指标收集设置

下表描述了可以配置的用于控制指标收集的设置：

| 密钥 | 数据类型 | 值 | 描述 |
|--|--|--|--|
| `[metric_collection_settings.collect_kube_system_pv_metrics] enabled =` | Boolean | true 或 false | 此设置允许在 kube 系统命名空间中收集永久性卷 (PV) 使用指标。 默认情况下，不会在 kube 系统命名空间中收集具有永久性卷声明的永久性卷的使用指标。 当此设置设为 `true` 时，将收集所有命名空间的 PV 使用指标。 默认情况下，这设置为 `false`。 |

ConfigMap 是一个全局列表，只能将一个 ConfigMap 应用到代理。 不能使用推翻收集规则的其他 ConfigMap。

## <a name="configure-and-deploy-configmaps"></a>使用 ConfigMap 进行配置和部署

执行以下步骤，以配置 ConfigMap 配置文件并将其部署到群集。

1. 下载[模板 ConfigMap YAML 文件](https://aka.ms/container-azm-ms-agentconfig)，并将其另存为 container-azm-ms-agentconfig.yaml。 

   > [!NOTE]
   > 使用 Azure Red Hat OpenShift V3 时，此步骤不是必需的，因为群集中已存在 ConfigMap 模板。

2. 使用自定义内容编辑 ConfigMap yaml 文件，以便收集 stdout、stderr 和/或环境变量。 如果你正在编辑 Azure Red Hat OpenShift V3 的 ConfigMap yaml 文件，请先运行命令 `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging`，在文本编辑器中打开该文件。

    - 若要排除特定命名空间的 stdout 日志收集，可以参考以下示例配置键/值：`[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]`。
    
    - 若要禁用特定容器的环境变量收集，请设置键/值 `[log_collection_settings.env_var] enabled = true` 以全局启用变量收集，然后遵循[此处](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container)所述的步骤完成特定容器的配置。
    
    - 若要在群集范围禁用 stderr 日志收集，请参考以下示例配置键/值：`[log_collection_settings.stderr] enabled = false`。
    
    在编辑器中保存更改。

3. 对于 Azure Red Hat OpenShift V3 以外的群集，请通过运行以下 kubectl 命令来创建 ConfigMap：`kubectl apply -f <configmap_yaml_file.yaml>`。 
    
    示例：`kubectl apply -f container-azm-ms-agentconfig.yaml`。 

配置更改可能需要几分钟时间才能完成并生效，群集中的所有 omsagent pod 将会重启。 所有 omsagent pod 的重启是轮流式的重启，而不是一次性全部重启。 重启完成后，系统会显示包含结果的消息，如下所示：`configmap "container-azm-ms-agentconfig" created`。

## <a name="verify-configuration"></a>验证配置

若要验证配置是否已成功应用于 Azure Red Hat OpenShift V3 以外的群集，请使用以下命令查看来自代理 Pod 的日志：`kubectl logs omsagent-fdf58 -n kube-system`。 如果 omsagent pod 存在配置错误，输出中会显示如下所示的错误：

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

还可以查看有关应用配置更改的错误。 以下选项可用于对配置更改执行其他故障排除：

- 使用同一个 `kubectl logs` 命令从代理 Pod 日志。 

    >[!NOTE]
    >此命令不适用于 Azure Red Hat OpenShift V3 群集。
    > 

- 从实时日志。 实时日志显示类似于以下内容的错误：

    ```
    config::error::Exception while parsing config map for log collection/env variable settings: \nparse error on value \"$\" ($end), using defaults, please check config map for errors
    ```

- 从 Log Analytics 工作区中的 **KubeMonAgentEvents** 表。 数据每小时发送一次，其中包含严重性为“错误”的配置错误。 如果没有错误，表中的条目将包含严重性为“信息”的数据，这些数据不会报告错误。 **Tags** 属性包含有关发生错误的 Pod 和容器 ID 的详细信息、第一次发生错误的 Pod 和容器 ID、最后一次发生错误的 Pod 和容器 ID 以及最后一小时内的错误计数。

- 使用 Azure Red Hat OpenShift V3 时，请通过搜索 ContainerLog 表来检查 omsagent 日志，以验证是否启用了 openshift-azure-logging 日志收集功能。

更正除 Azure Red Hat OpenShift V3 之外的群集上的 ConfigMap 中的错误后，请保存 yaml 文件，并运行以下命令来应用已更新的 ConfigMap：`kubectl apply -f <configmap_yaml_file.yaml`。 对于 Azure Red Hat OpenShift V3，请运行以下命令来编辑并保存已更新的 ConfigMaps：

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

## <a name="applying-updated-configmap"></a>应用已更新的 ConfigMap

如果已经在 Azure Red Hat OpenShift V3 以外的群集上部署了 ConfigMap，但想要使用较新的配置对其进行更新，则可编辑以前使用的 ConfigMap 文件，然后使用与以前相同的命令 `kubectl apply -f <configmap_yaml_file.yaml` 应用该文件。 对于 Azure Red Hat OpenShift V3，请运行以下命令来编辑并保存已更新的 ConfigMaps：

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

配置更改可能需要几分钟时间才能完成并生效，群集中的所有 omsagent pod 将会重启。 所有 omsagent pod 的重启是轮流式的重启，而不是一次性全部重启。 重启完成后，系统会显示包含结果的消息，如下所示：`configmap "container-azm-ms-agentconfig" updated`。

## <a name="verifying-schema-version"></a>验证架构版本

omsagent pod 上以 pod 注释 (schema-versions) 的形式提供了支持的配置架构版本。 可以使用以下 kubectl 命令查看版本：`kubectl describe pod omsagent-fdf58 -n=kube-system`

输出中会显示如下所示的包含注释架构版本的内容：

```
    Name:           omsagent-fdf58
    Namespace:      kube-system
    Node:           aks-agentpool-95673144-0/10.240.0.4
    Start Time:     Mon, 10 Jun 2019 15:01:03 -0700
    Labels:         controller-revision-hash=589cc7785d
                    dsName=omsagent-ds
                    pod-template-generation=1
    Annotations:    agentVersion=1.10.0.1
                  dockerProviderVersion=5.0.0-0
                    schema-versions=v1 
```

## <a name="next-steps"></a>后续步骤

- 容器见解不包含预定义的警报集。 请查看[使用容器见解创建性能警报](./container-insights-log-alerts.md)，了解如何针对 CPU 和内存使用率过高的情况创建建议的警报，为 DevOps 或操作流程和过程提供支持。

- 有关启用监视来收集 AKS 或混合群集及其中运行的工作负载的运行状况和资源利用率的信息，请参阅[如何使用](container-insights-analyze.md)容器见解。

- 请参阅[日志查询示例](container-insights-log-query.md)，以查看预定义的查询，以及用于发警报、可视化或分析群集的评估或自定义示例。
