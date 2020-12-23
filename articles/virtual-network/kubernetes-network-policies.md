---
title: Azure Kubernetes 网络策略 | Microsoft Docs
description: 了解 Kubernetes 网络策略以保护 Kubernetes 群集。
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
ms.openlocfilehash: 36e5bb33b7d555c3b457b63f94d9032ff390e6cb
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342308"
---
# <a name="azure-kubernetes-network-policies-overview"></a>Azure Kubernetes 网络策略概述

网络策略提供盒微分段，就像 (Nsg 的网络安全组) 为 Vm 提供微分段。 Azure 网络策略管理器 (也称为 Azure NPM) 实现支持标准的 Kubernetes 网络策略规范。 可以使用标签选择一组 pod，并定义入口和出口规则列表，以筛选进出这些 pod 的流量。 在 [Kubernetes 文档](https://kubernetes.io/docs/concepts/services-networking/network-policies/)中详细了解 Kubernetes 网络策略。

![Kubernetes 网络策略概述](./media/kubernetes-network-policies/kubernetes-network-policies-overview.png)

Azure NPM 实现与为容器提供 VNet 集成的 Azure CNI 结合使用。 目前只有 Linux 支持 NPM。 此实现通过基于定义的策略配置 Linux IPTables 中的允许和拒绝 IP 规则，强制实施流量筛选。 使用 Linux IPSets 将这些规则组合在一起。

## <a name="planning-security-for-your-kubernetes-cluster"></a>规划 Kubernetes 群集的安全性
在实现群集的安全性时，请使用网络安全组 (Nsg) 来筛选进入和离开群集子网 (北南部流量) 的流量。 使用 Azure NPM 在群集中的 pod 之间传输流量 (东-西流量) 。

## <a name="using-azure-npm"></a>使用 Azure NPM
可以通过以下方式使用 Azure NPM 来提供 pod 的微分段。

### <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes 服务 (AKS)
NPM 在 AKS 中以本机方式提供，并且可在创建群集时启用。 [使用 Azure Kubernetes Service (AKS) 中的网络策略在 pod 之间的安全流量](https://docs.microsoft.com/azure/aks/use-network-policies)中了解更多相关信息。

### <a name="aks-engine"></a>AKS 引擎
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
### <a name="do-it-yourself-diy-kubernetes-clusters-in-azure"></a>自行 (DIY) Azure 中的 Kubernetes 群集
 对于 DIY 群集，请先安装 CNI 插件，并在群集中的每个虚拟机上启用它。 如需详细说明，请参阅[为自行部署的 Kubernetes 群集部署插件](deploy-container-networking.md#deploy-plug-in-for-a-kubernetes-cluster)。

部署群集后，运行以下 `kubectl` 命令以下载 AZURE NPM *守护* 程序并将其应用到群集。

  ```
  kubectl apply -f https://raw.githubusercontent.com/Azure/acs-engine/master/parts/k8s/addons/kubernetesmasteraddons-azure-npm-daemonset.yaml

  ```
此解决方案也是开源的，代码在 [Azure 容器网络存储库](https://github.com/Azure/azure-container-networking/tree/master/npm)中提供。

## <a name="monitor-and-visualize-network-configurations-with-azure-npm"></a>利用 Azure NPM 监视和可视化网络配置
Azure NPM 包含信息性 Prometheus 指标，可让你监视和更好地了解你的配置。 它在 Azure 门户或 Grafana 实验室中提供内置可视化对象。 你可以使用 Azure Monitor 或 Prometheus 服务器开始收集这些指标。

### <a name="benefits-of-azure-npm-metrics"></a>Azure NPM 指标的优点
用户以前只能使用命令在群集节点内运行的网络配置来了解其网络配置 `iptables -L` ，这会生成详细且难以理解输出的信息。 NPM 指标提供以下与网络策略、IPTables 规则和 IPSets 相关的好处。
- 提供对三个与时间维度之间的关系的深入了解，以对配置进行调试。
- 所有 IPSets 和每个 IPSet 中的条目数。
- 应用具有 IPTable/IPSet 级别粒度的策略所用的时间。
 
### <a name="supported-metrics"></a>支持的指标
下面列出了支持的指标：

|标准名称 |说明  |Prometheus 指标类型  |标签  |
|---------|---------|---------|---------|
|`npm_num_policies`     |网络策略数          |仪表         |-         |
|`npm_num_iptables_rules`     | IPTables 规则数     | 仪表        |-         |         
|`npm_num_ipsets`     |IPSets 数         |仪表            |-         |
|`npm_num_ipset_entries`     |所有 IPSets 中的 IP 地址条目数         |仪表         |-         |
|`npm_add_policy_exec_time`     |用于添加网络策略的运行时         |总结         |分位 (0.5、0.9 或 0.99)          |
|`npm_add_iptables_rule_exec_time`     |用于添加 IPTables 规则的运行时         |总结         |分位 (0.5、0.9 或 0.99)          |
|`npm_add_ipset_exec_time`     |用于添加 IPSet 的运行时         |总结         |分位 (0.5、0.9 或 0.99)          |
|`npm_ipset_counts` (高级)      |每个 IPSet 中的条目数         |GaugeVec         |设置 & 哈希的名称         |

"Exec_time" 指标中的不同分位级别可帮助你区分常规方案和最坏情况。

每个 "exec_time" 摘要指标还有 "exec_time_count" 和 "exec_time_sum" 指标。

可以通过 Azure Monitor 容器或通过 Prometheus 来擦除指标。

### <a name="setup-for-azure-monitor"></a>Azure Monitor 的设置
第一步是为 Kubernetes 群集的容器启用 Azure Monitor。 可在 [容器 Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview)中找到步骤概述。 启用容器 Azure Monitor 后，配置 [容器的 Azure Monitor](https://aka.ms/container-azm-ms-agentconfig) ，以启用 NPM 集成和 Prometheus NPM 指标的收集。 适用于容器的 Azure monitor ConfigMap 有一个 ```integrations``` 节，其中包含用于收集 NPM 度量值的设置。 默认情况下，ConfigMap 中禁用这些设置。 启用 "基本" 设置 ```collect_basic_metrics = true``` ，将收集基本的 NPM 指标。 启用 "高级 ```collect_advanced_metrics = true``` " 设置将收集除基本指标之外的高级度量值。 

编辑 ConfigMap 后，将其保存在本地，并按如下所示将 ConfigMap 应用到群集。

```kubectl apply -f container-azm-ms-agentconfig.yaml``` 下面是 [适用于容器 ConfigMap 的 Azure monitor](https://aka.ms/container-azm-ms-agentconfig)的代码段，其中显示了使用高级指标集合启用的 NPM 集成。
```
integrations: |-
    [integrations.azure_network_policy_manager]
        collect_basic_metrics = false
        collect_advanced_metrics = true
```
高级度量值是可选的，启用它们会自动启用基本指标收集。 高级度量值当前仅包含 `npm_ipset_counts`

详细了解 [config map 中的 Azure monitor for 容器集合设置](https://aka.ms/azmon-containers-agent-collection-settings-doc)

### <a name="visualization-options-for-azure-monitor"></a>Azure Monitor 的可视化选项
启用 NPM 指标收集后，可以使用容器 Insights 或 Grafana 在 Azure 门户中查看指标。

#### <a name="viewing-in-azure-portal-under-insights-for-the-cluster"></a>在群集的 "见解" 下查看 Azure 门户
打开 Azure 门户。 进入群集见解后，导航到 "工作簿"，打开 "网络策略管理器 (NPM) 配置"。

除了在) 下查看工作簿 (图片外，还可以直接在 "见解" 部分下的 "日志" 中查询 Prometheus 度量值。 例如，此查询将返回所收集的所有指标。
|其中，TimeGenerated > 前 (5h) |其中，Name 包含 "npm_"

你还可以直接查询指标 Log Analytics。 通过[Log Analytics 查询入门](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-log-search)了解更多相关信息 

#### <a name="viewing-in-grafana-dashboard"></a>在 Grafana 仪表板中查看
设置 Grafana 服务器，并按照 [此处](https://grafana.com/grafana/plugins/grafana-azure-monitor-datasource)所述配置 Log Analytics 数据源。 然后，将 [具有 Log Analytics 后端的 Grafana 仪表板](https://grafana.com/grafana/dashboards/10956) 导入到 Grafana 实验室。

该仪表板具有类似于 Azure 工作簿的视觉对象。 您可以向图表添加面板 & 直观显示 InsightsMetrics 表中的 NPM 度量值。

### <a name="setup-for-prometheus-server"></a>安装 Prometheus Server
某些用户可能会选择使用 Prometheus 服务器收集指标，而不是针对容器 Azure Monitor。 只需将两个作业添加到擦除 config 即可收集 NPM 指标。

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
其中 `prometheus-server-scrape-config.yaml` 包含
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


你还可以将作业替换为 `azure-npm-node-metrics` 以下内容，或将其合并到 Kubernetes pod 的预先存在的作业中：
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
仅支持使用 Prometheus 服务器的 Grafana 仪表板。 

设置 Grafana 服务器并配置 Prometheus 数据源（如果尚未这样做）。 然后，将 [包含 Prometheus 后端的 Grafana 仪表板](https://grafana.com/grafana/dashboards/13000) 导入 Grafana 实验室。

此仪表板的视觉对象与具有容器 Insights/Log Analytics 后端的仪表板相同。

### <a name="sample-dashboards"></a>示例仪表板
下面是容器 Insights (CI) 和 Grafana 中的 NPM 指标的一些示例仪表板

#### <a name="ci-summary-counts"></a>CI 汇总计数
![Azure 工作簿摘要计数](media/kubernetes-network-policies/workbook-summary-counts.png)

#### <a name="ci-counts-over-time"></a>一段时间内的 CI 计数
[![一段时间内的 Azure 工作簿计数](media/kubernetes-network-policies/workbook-counts-over-time.png)](media/kubernetes-network-policies/workbook-counts-over-time.png#lightbox)

#### <a name="ci-ipset-entries"></a>CI IPSet 条目
[![Azure 工作簿 IPSet 条目](media/kubernetes-network-policies/workbook-ipset-entries.png)](media/kubernetes-network-policies/workbook-ipset-entries.png#lightbox)

#### <a name="ci-runtime-quantiles"></a>CI 运行时分位数
![Azure 工作簿运行时分位数](media/kubernetes-network-policies/workbook-runtime-quantiles.png)

#### <a name="grafana-dashboard-summary-counts"></a>Grafana 仪表板摘要计数
![Grafana 仪表板摘要计数](media/kubernetes-network-policies/grafana-summary-counts.png)

#### <a name="grafana-dashboard-counts-over-time"></a>Grafana 仪表板统计一段时间
[![Grafana 仪表板统计一段时间](media/kubernetes-network-policies/grafana-counts-over-time.png)](media/kubernetes-network-policies/grafana-counts-over-time.png#lightbox)

#### <a name="grafana-dashboard-ipset-entries"></a>Grafana 仪表板 IPSet 条目
[![Grafana 仪表板 IPSet 条目](media/kubernetes-network-policies/grafana-ipset-entries.png)](media/kubernetes-network-policies/grafana-ipset-entries.png#lightbox)

#### <a name="grafana-dashboard-runtime-quantiles"></a>Grafana 仪表板运行时分位数
[![Grafana 仪表板运行时分位数](media/kubernetes-network-policies/grafana-runtime-quantiles.png)](media/kubernetes-network-policies/grafana-runtime-quantiles.png#lightbox)



## <a name="next-steps"></a>后续步骤
- 了解 [Azure Kubernetes 服务](../aks/intro-kubernetes.md)。
-  了解[容器网络](container-networking-overview.md)。
- [部署](deploy-container-networking.md) Kubernetes 群集或 Docker 容器的插件。

    
