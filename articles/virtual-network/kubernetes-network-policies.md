---
title: Azure Kubernetes 网络策略 | Microsoft Docs
description: 了解用于确保 Kubernetes 群集安全的 Kubernetes 网络策略。
services: virtual-network
documentationcenter: na
author: aanandr
manager: NarayanAnnamalai
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/25/2018
ms.author: aanandr
ms.custom: ''
ms.openlocfilehash: f8a8e5785af1bd74fb958a315de379facdc87171
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128575965"
---
# <a name="azure-kubernetes-network-policies-overview"></a>Azure Kubernetes 网络策略概述

网络策略为 Pod 提供微分段，就像网络安全组 (NSG) 为 VM 提供微分段一样。 Azure 网络策略管理器（也称为 Azure NPM）实现支持标准的 Kubernetes 网络策略规范。 可以使用标签来选择一组 Pod 并定义入口和出口规则的列表，以筛选从这些 Pod 出入的流量。 在 [Kubernetes 文档](https://kubernetes.io/docs/concepts/services-networking/network-policies/)中详细了解 Kubernetes 网络策略。

![Kubernetes 网络策略概述](./media/kubernetes-network-policies/kubernetes-network-policies-overview.png)

Azure NPM 实现可以与为容器提供 VNet 集成的 Azure CNI 配合使用。 目前仅 Linux 支持 NPM。 此实现通过根据定义的策略来配置 Linux IPTable 中的允许和拒绝规则，来强制执行流量筛选。 使用 Linux IPSet 将这些规则组合在一起。

## <a name="planning-security-for-your-kubernetes-cluster"></a>规划 Kubernetes 群集的安全性
为群集实现安全性时，请使用网络安全组 (NSG) 来筛选出入群集子网的流量（北-南流量）。 为群集中 Pod 之间的流量（东-西流量）使用 Azure NPM。

## <a name="using-azure-npm"></a>使用 Azure NPM
可以通过下述方式使用 Azure NPM 为 Pod 提供微分段。

### <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes 服务 (AKS)
NPM 在 AKS 中以本机方式提供，并且可在创建群集时启用。 有关详细信息，请参阅[在 Azure Kubernetes 服务 (AKS) 中使用网络策略保护 Pod 之间的流量](../aks/use-network-policies.md)。

### <a name="aks-engine"></a>AKS-engine
AKS-Engine 是一项工具，用于生成 Azure 资源管理器模板，以便在 Azure 中部署 Kubernetes 群集。 群集配置在 JSON 文件中指定，该文件在生成模板时传递给工具。 若要详细了解受支持的群集设置及其说明的完整列表，请参阅“Microsoft Azure 容器服务引擎 - 群集定义”。

若要在使用 acs-engine 部署的群集上启用策略，请在群集定义文件中将 networkPolicy 设置的值指定为“azure”。

#### <a name="example-configuration"></a>配置示例

下面的 JSON 示例配置使用 Azure CNI 创建了一个新的虚拟网络和子网，并在其中部署了 Kubernetes 群集。 建议使用“记事本”来编辑此 JSON 文件。 
```json
{
  "apiVersion": "vlabs",
  "properties": {
    "orchestratorProfile": {
      "orchestratorType": "Kubernetes",
      "kubernetesConfig": {
         "networkPolicy": "azure"
       }
    },
    "masterProfile": {
      "count": 1,
      "dnsPrefix": "<specify a cluster name>",
      "vmSize": "Standard_D2s_v3"
    },
    "agentPoolProfiles": [
      {
        "name": "agentpool",
        "count": 2,
        "vmSize": "Standard_D2s_v3",
        "availabilityProfile": "AvailabilitySet"
      }
    ],
   "linuxProfile": {
      "adminUsername": "<specify admin username>",
      "ssh": {
        "publicKeys": [
          {
            "keyData": "<cut and paste your ssh key here>"
          }
        ]
      }
    },
    "servicePrincipalProfile": {
      "clientId": "<enter the client ID of your service principal here >",
      "secret": "<enter the password of your service principal here>"
    }
  }
}

```
### <a name="do-it-yourself-diy-kubernetes-clusters-in-azure"></a>Azure 中的自制 (DIY) Kubernetes 群集
 对于 DIY 群集，请先安装 CNI 插件，然后在群集中的每个虚拟机上启用它。 如需详细说明，请参阅[为自行部署的 Kubernetes 群集部署插件](deploy-container-networking.md#deploy-plug-in-for-a-kubernetes-cluster)。

部署群集以后，请运行下面的 `kubectl` 命令，以便下载 Azure NPM 守护程序集并将其应用到群集。

  ```
  kubectl apply -f https://raw.githubusercontent.com/Azure/acs-engine/master/parts/k8s/addons/kubernetesmasteraddons-azure-npm-daemonset.yaml

  ```
此解决方案也是开源的，代码在 [Azure 容器网络存储库](https://github.com/Azure/azure-container-networking/tree/master/npm)中提供。

## <a name="monitor-and-visualize-network-configurations-with-azure-npm"></a>使用 Azure NPM 监视和可视化网络配置
Azure NPM 包含信息丰富的 Prometheus 指标，可用于监视和更好地了解网络配置。 它在 Azure 门户或 Grafana 实验室中提供了内置的可视化效果。 你可以使用 Azure Monitor 或 Prometheus 服务器开始收集这些指标。

### <a name="benefits-of-azure-npm-metrics"></a>Azure NPM 指标的优点
以前，用户只能使用在群集节点内运行的命令 `iptables -L` 了解其网络配置，这会产生冗长且难以理解的输出。 NPM 指标具有以下与网络策略、IPTable 规则和 IPSet 相关的好处。
- 提供有关这三项与时间维度之间关系的见解，以对配置进行调试。
- 所有 IPSet 和每个 IPSet 中的条目数。
- 应用具有 IPTable/IPSet 级别粒度的策略所花费的时间。
 
### <a name="supported-metrics"></a>支持的指标
下面是支持的指标的列表：

|标准名称 |说明  |Prometheus 指标类型  |标签  |
|---------|---------|---------|---------|
|`npm_num_policies`     |网络策略数          |仪表         |-         |
|`npm_num_iptables_rules`     | IPTable 规则数     | 仪表        |-         |         
|`npm_num_ipsets`     |IPSet 数         |仪表            |-         |
|`npm_num_ipset_entries`     |所有 IPSet 中的 IP 地址条目数         |仪表         |-         |
|`npm_add_policy_exec_time`     |用于添加网络策略的运行时         |总结         |分位数（0.5、0.9 或 0.99）         |
|`npm_add_iptables_rule_exec_time`     |用于添加 IPTable 规则的运行时         |总结         |分位数（0.5、0.9 或 0.99）         |
|`npm_add_ipset_exec_time`     |用于添加 IPSet 的运行时         |总结         |分位数（0.5、0.9 或 0.99）         |
|`npm_ipset_counts`（高级）     |每个 IPSet 中的条目数         |GaugeVec         |set name & hash         |

“exec_time”指标中不同的分位数级别可帮助你区分一般情况和最坏情况。

对于每个“exec_time”汇总指标，还有一个“exec_time_count”和“exec_time_sum”指标。

可以通过用于容器的 Azure Monitor 或 Prometheus 抓取指标。

### <a name="setup-for-azure-monitor"></a>Azure Monitor 的设置
第一步是为 Kubernetes 群集启用用于容器的 Azure Monitor。 可在[用于容器的 Azure Monitor 概述](../azure-monitor/containers/container-insights-overview.md)中找到相关步骤。 启用用于容器的 Azure Monitor 后，请配置[用于容器的 Azure Monitor ConfigMap](https://aka.ms/container-azm-ms-agentconfig) 以启用 NPM 集成和 Prometheus NPM 指标的收集。 用于容器的 Azure Monitor ConfigMap 有一个 ```integrations``` 部分，其中具有用于收集 NPM 指标的设置。 默认情况下，这些设置在 ConfigMap 中处于禁用状态。 启用基本设置 ```collect_basic_metrics = true```，将收集基本 NPM 指标。 启用高级设置 ```collect_advanced_metrics = true``` 后除收集基本指标外，还收集高级指标。 

编辑 ConfigMap 后，将其保存在本地，并按如下所示将 ConfigMap 应用到群集。

`kubectl apply -f container-azm-ms-agentconfig.yaml`

 以下是[用于容器的 Azure Monitor ConfigMap](https://aka.ms/container-azm-ms-agentconfig) 的片段，其中显示了通过高级指标收集启用的 NPM 集成。
```
integrations: |-
    [integrations.azure_network_policy_manager]
        collect_basic_metrics = false
        collect_advanced_metrics = true
```
高级指标是可选的，打开它们将自动启用基本指标收集。 高级指标当前仅包含 `npm_ipset_counts`

详细了解 [ConfigMap 中用于容器的 Azure Monitor 集合设置](../azure-monitor/containers/container-insights-agent-config.md)

### <a name="visualization-options-for-azure-monitor"></a>Azure Monitor 的可视化选项
启用 NPM 指标收集后，可以使用容器见解或 Grafana 查看 Azure 门户中的指标。

#### <a name="viewing-in-azure-portal-under-insights-for-the-cluster"></a>在 Azure 门户中查看群集的“见解”
打开 Azure 门户。 进入群集的“见解”后，导航至“工作簿”并打开“网络策略管理器 (NPM) 配置”。

除了查看工作簿（下图）之外，还可以在“见解”部分下的“日志”中直接查询 Prometheus 指标。 例如，此查询将返回所收集的所有指标。
| 其中 TimeGenerated > ago(5h) | 其中名称包含“npm_”

还可以直接向 Log Analytics 查询指标数据。 通过 [Log Analytics 查询入门](../azure-monitor/containers/container-insights-log-query.md)了解更多相关信息 

#### <a name="viewing-in-grafana-dashboard"></a>在 Grafana 仪表板中查看
设置 Grafana 服务器，并按照[此处](https://grafana.com/grafana/plugins/grafana-azure-monitor-datasource)所述配置 Log Analytics 数据源。 然后，将[带有 Log Analytics 后端的 Grafana 仪表板](https://grafana.com/grafana/dashboards/10956)导入 Grafana 实验室。

该仪表板具有类似于 Azure 工作簿的视觉对象。 可以从 InsightsMetrics 表中添加面板以绘制图表并可视化 NPM 指标。

### <a name="setup-for-prometheus-server"></a>安装 Prometheus 服务器
一些用户可能选择使用 Prometheus 服务器而不是用于容器的 Azure Monitor 来收集指标。 仅需在抓取配置中添加两个作业即可收集 NPM 指标。

若要安装简单的 Prometheus 服务器，请在群集上添加此 helm 存储库
```
helm repo add stable https://kubernetes-charts.storage.googleapis.com
helm repo update
```
然后添加服务器
```
helm install prometheus stable/prometheus -n monitoring \
--set pushgateway.enabled=false,alertmanager.enabled=false, \
--set-file extraScrapeConfigs=prometheus-server-scrape-config.yaml
```
其中 `prometheus-server-scrape-config.yaml` 包括
```
- job_name: "azure-npm-node-metrics"
  metrics_path: /node-metrics
  kubernetes_sd_configs:
  - role: node
  relabel_configs:
  - source_labels: [__address__]
    action: replace
    regex: ([^:]+)(?::\d+)?
    replacement: "$1:10091"
    target_label: __address__
- job_name: "azure-npm-cluster-metrics"
  metrics_path: /cluster-metrics
  kubernetes_sd_configs:
  - role: service
  relabel_configs:
  - source_labels: [__meta_kubernetes_namespace]
    regex: kube-system
    action: keep
  - source_labels: [__meta_kubernetes_service_name]
    regex: npm-metrics-cluster-service
    action: keep
# Comment from here to the end to collect advanced metrics: number of entries for each IPSet
  metric_relabel_configs:
  - source_labels: [__name__]
    regex: npm_ipset_counts
    action: drop
```


也可以将 `azure-npm-node-metrics` 作业替换为以下内容，或将其合并到 Kubernetes Pod 的现有作业中：
```
- job_name: "azure-npm-node-metrics-from-pod-config"
  metrics_path: /node-metrics
  kubernetes_sd_configs:
  - role: pod
  relabel_configs:
  - source_labels: [__meta_kubernetes_namespace]
    regex: kube-system
    action: keep
  - source_labels: [__meta_kubernetes_pod_annotationpresent_azure_npm_scrapeable]
    action: keep
  - source_labels: [__address__]
    action: replace
    regex: ([^:]+)(?::\d+)?
    replacement: "$1:10091"
    target_label: __address__
```

### <a name="visualization-options-for-prometheus"></a>Prometheus 的可视化选项
使用 Prometheus 服务器时，仅支持 Grafana 仪表板。 

请设置 Grafana 服务器并配置 Prometheus 数据源（如果尚未这样做）。 然后，将[带有 Prometheus 后端的 Grafana 仪表板](https://grafana.com/grafana/dashboards/13000)导入 Grafana 实验室。

该仪表板的视觉对象与具有容器见解/Log Analytics 后端的仪表板相同。

### <a name="sample-dashboards"></a>示例仪表板
以下是容器见解 (CI) 和 Grafana 中 NPM 指标的一些示例仪表板

#### <a name="ci-summary-counts"></a>CI 汇总计数
![Azure 工作簿汇总计数](media/kubernetes-network-policies/workbook-summary-counts.png)

#### <a name="ci-counts-over-time"></a>一段时间内的 CI 计数
[![一段时间内的 Azure Workbook 计数](media/kubernetes-network-policies/workbook-counts-over-time.png)](media/kubernetes-network-policies/workbook-counts-over-time.png#lightbox)

#### <a name="ci-ipset-entries"></a>CI IPSet 条目数
[![Azure Workbook IPSet 条目数](media/kubernetes-network-policies/workbook-ipset-entries.png)](media/kubernetes-network-policies/workbook-ipset-entries.png#lightbox)

#### <a name="ci-runtime-quantiles"></a>CI 运行时分位数
![Azure 工作簿运行时分位数](media/kubernetes-network-policies/workbook-runtime-quantiles.png)

#### <a name="grafana-dashboard-summary-counts"></a>Grafana 仪表板汇总计数
![Grafana 仪表板汇总计数](media/kubernetes-network-policies/grafana-summary-counts.png)

#### <a name="grafana-dashboard-counts-over-time"></a>一段时间内的 Grafana 仪表板计数
[![一段时间内的 Grafana 仪表板计数](media/kubernetes-network-policies/grafana-counts-over-time.png)](media/kubernetes-network-policies/grafana-counts-over-time.png#lightbox)

#### <a name="grafana-dashboard-ipset-entries"></a>Grafana 仪表板 IPSet 条目数
[![Grafana 仪表板 IPSet 条目数](media/kubernetes-network-policies/grafana-ipset-entries.png)](media/kubernetes-network-policies/grafana-ipset-entries.png#lightbox)

#### <a name="grafana-dashboard-runtime-quantiles"></a>Grafana 仪表板运行时分位数
[![Grafana 仪表板运行时分位数](media/kubernetes-network-policies/grafana-runtime-quantiles.png)](media/kubernetes-network-policies/grafana-runtime-quantiles.png#lightbox)



## <a name="next-steps"></a>后续步骤
- 了解 [Azure Kubernetes 服务](../aks/intro-kubernetes.md)。
-  了解[容器网络](container-networking-overview.md)。
- 为 Kubernetes 群集或 Docker 容器[部署插件](deploy-container-networking.md)。
