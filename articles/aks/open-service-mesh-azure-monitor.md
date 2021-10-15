---
title: 使用 Azure Monitor 和 Application Insights
description: 如何将 Azure Monitor 和 Application Insights 与 Open Service Mesh 结合使用
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: 4e1df1275e225b42bf01bd2a4092cea07f9c4c12
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2021
ms.locfileid: "129057807"
---
# <a name="open-service-mesh-osm-monitoring-and-observability-using-azure-monitor-and-applications-insights"></a>使用 Azure Monitor 和 Azure Application Insights 的 Open Service Mesh (OSM) 监视和可观测性

Azure Monitor 和 Azure Application Insights 有助于最大程度提高应用程序和服务的可用性和性能。 这些服务提供了一个全面的解决方案，用于从云和本地环境收集、分析和处理遥测数据。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

OSM AKS 加载项将深度集成到这两项 Azure 服务，并提供无缝的 Azure 体验来查看和响应 OSM 指标提供的关键 KPI。 

## <a name="enable-azure-monitor"></a>启用 Azure Monitor

在 AKS 群集上启用 OSM AKS 加载项后，需要通过 Azure 门户在群集中启用 Azure Monitor。 单击 AKS 群集，导航到“监视”下的“见解”选项卡，然后选择“启用”。 

启用 Azure Monitor 后，应该可以在 kube-system 命名空间中看到以下日志： 

```
kube-system     omsagent-5pn4c                        1/1     Running   0          24m
kube-system     omsagent-6r6zt                        1/1     Running   0          24m
kube-system     omsagent-j8xrh                        1/1     Running   0          24m
kube-system     omsagent-rs-74b8f7dfd8-rp5vx          1/1     Running   1          24m
```

## <a name="enable-metrics-in-osm-monitored-namespaces"></a>在 OSM 监视的命名空间中启用指标

对于要从网格监视的特定命名空间中抓取的指标，需要运行以下命令：

```sh
osm metrics enable --osm-namespace <namespace>
```

例如，如果你正在运行 [bookstore 演示](https://docs.openservicemesh.io/docs/getting_started/quickstart/manual_demo/)，请在以下命名空间上运行 `osm metrics enable` 命令：

```sh
osm metrics enable --osm-namespace bookbuyer
osm metrics enable --osm-namespace bookstore
osm metrics enable --osm-namespace bookthief
osm metrics enable --osm-namespace bookwarehouse
```
## <a name="apply-configmaps"></a>应用 ConfigMap

在 `kube-system` 中创建以下 ConfigMap，告知 AzMon 应该监视哪些命名空间。 例如，对于 bookbuyer/bookstore 演示，ConfigMap 如下所示： 

```yaml
kind: ConfigMap
apiVersion: v1
data:
  schema-version: v1
  config-version: ver1
  osm-metric-collection-configuration: |-
    # OSM metric collection settings
    [osm_metric_collection_configuration]
      [osm_metric_collection_configuration.settings]
          # Namespaces to monitor
          monitor_namespaces = ["bookstore", "bookbuyer", "bookthief", "bookwarehouse"]
metadata:
  name: container-azm-ms-osmconfig
  namespace: kube-system

```

接下来，需要创建第二个 ConfigMap 以[将 monitor_kubernetes_pods 设置为 true](https://github.com/microsoft/Docker-Provider/blob/24b709f9e3c3b18779102b491fc98b87a99d1335/kubernetes/container-azm-ms-agentconfig.yaml#L72)。

```yaml
kind: ConfigMap
apiVersion: v1
metadata:
  name: container-azm-ms-agentconfig
  namespace: kube-system
data:
  schema-version: v1
  config-version: ver1
  prometheus-data-collection-settings: |-
    [prometheus_data_collection_settings.cluster]
        interval = "30s"
        monitor_kubernetes_pods = true
```

## <a name="view-metrics-in-the-azure-portal"></a>在 Azure 门户中查看指标

在 Azure 门户中选择 Kubernetes 群集，然后选择“监视”下的“日志”选项卡。 现在应该可以查询 `InsightsMetrics` 表，以查看已启用的命名空间中的指标。 例如，如果你要查看 `bookbuyer` 的 envoy 指标，请使用以下查询：

```sh
InsightsMetrics
| where Name contains "envoy"
| extend t=parse_json(Tags)
| where t.app == "bookbuyer"
```

## <a name="additional-information"></a>其他信息

有关如何为 OSM AKS 加载项启用和配置 Azure Monitor 和 Azure Application Insights 的详细信息，请访问[适用于 OSM 的 Azure Monitor](https://aka.ms/azmon/osmpreview) 页。