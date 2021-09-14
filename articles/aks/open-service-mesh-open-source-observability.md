---
title: OSM OSS 可观测性
description: 如何为 Open Service Mesh 配置开放源代码可观测性
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: ce03fc4007ad55485150feb715242d4cc216433e
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123439804"
---
# <a name="manually-deploy-prometheus-grafana-and-jaeger-to-view-open-service-mesh-osm-metrics-for-observability"></a>手动部署 Prometheus、Grafana 和 Jaeger，查看用于观察状况的 Open Service Mesh (OSM) 指标

> [!WARNING]
> 我们提供 Prometheus、Grafana 和 Jaeger 的安装作为常规指导，以说明如何使用这些工具来查看 OSM 的指标数据。 安装指南不用于生产设置。 请参阅每个工具的文档，了解如何根据需求优化这些安装。 需要注意的最重要一点是缺少永久性存储，这意味着，一旦终止 Prometheus Pod、Grafana Pod 和/或 Jaeger Pod，所有数据都将丢失。

Open Service Mesh (OSM) 将生成与网格中所有流量相关的详细指标。 这些指标使用户能够深入了解网格中的应用程序的行为，从而帮助用户维护和分析应用程序以及进行故障排除。

到目前为止，OSM 直接从挎斗代理 (Envoy) 收集指标。 OSM 为网格中所有服务的传入流量和传出流量提供丰富的指标。 通过这些指标，用户可以获取有关流量总量、流量中的错误和请求的响应时间的信息。

OSM 使用 Prometheus 为网格中运行的所有应用程序收集并存储一致的流量指标和统计信息。 Prometheus 是一个开源监视和警报工具包，在 Kubernetes 环境和服务网格环境（但不限于）中普遍使用。

属于网格一部分的每个应用程序都在 Pod 中运行，其中所包含的 Envoy 挎斗以 Prometheus 格式公开指标（代理指标）。 此外，属于网格一部分的每个 Pod 都具有 Prometheus 注释，使 Prometheus 服务器可以动态抓取应用程序。 只要向网格中添加新的命名空间/Pod/服务，此机制就会自动启用指标抓取。

可以使用开源可视化和分析软件 Grafana 来查看 OSM 指标。 它用于查询指标、可视化指标、针对指标发出警报以及浏览指标。

在本教程中，将：

> [!div class="checklist"]
>
> - 创建并部署 Prometheus 实例
> - 将 OSM 配置为允许 Prometheus 抓取
> - 更新 Prometheus `Configmap`
> - 创建并部署 Grafana 实例
> - 为 Grafana 配置 Prometheus 数据源
> - 导入用于 Grafana 的 OSM 仪表板
> - 创建并部署 Jaeger 实例
> - 为 OSM 配置 Jaeger 跟踪

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>开始之前

必须已安装以下资源：

- Azure CLI 2.20.0 或更高版本
- `aks-preview` 扩展 0.5.5 或更高版本
- OSM 版本 v0.8.0 或更高版本
- JSON 处理器“jq”版本 1.6+

## <a name="deploy-and-configure-a-prometheus-instance-for-osm"></a>为 OSM 部署并配置 Prometheus 实例

我们将使用 Helm 来部署 Prometheus 实例。 运行以下命令，通过 Helm 安装 Prometheus：

```azurecli-interactive
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install stable prometheus-community/prometheus
```

如果安装成功，你应该会看到与下面类似的输出。 请记下 Prometheus 服务器端口和群集 DNS 名称。 稍后会使用这些信息将 Prometheus 配置为 Grafana 的数据源。

```Output
NAME: stable
LAST DEPLOYED: Fri Mar 26 13:34:51 2021
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
The Prometheus server can be accessed via port 80 on the following DNS name from within your cluster:
stable-prometheus-server.default.svc.cluster.local


Get the Prometheus server URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=server" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9090


The Prometheus alertmanager can be accessed via port 80 on the following DNS name from within your cluster:
stable-prometheus-alertmanager.default.svc.cluster.local


Get the Alertmanager URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=alertmanager" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9093
#################################################################################
######   WARNING: Pod Security Policy has been moved to a global property.  #####
######            use .Values.podSecurityPolicy.enabled with pod-based      #####
######            annotations                                               #####
######            (e.g. .Values.nodeExporter.podSecurityPolicy.annotations) #####
#################################################################################


The Prometheus PushGateway can be accessed via port 9091 on the following DNS name from within your cluster:
stable-prometheus-pushgateway.default.svc.cluster.local


Get the PushGateway URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=pushgateway" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9091

For more information on running Prometheus, visit:
https://prometheus.io/
```

### <a name="configure-osm-to-allow-prometheus-scraping"></a>将 OSM 配置为允许 Prometheus 抓取

要确保为 OSM 组件配置了 Prometheus 抓取，我们需要检查 osm-config 配置文件中的 prometheus_scraping 配置。 使用以下命令查看该配置：

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data.prometheus_scraping'
```

如果为 OSM 配置了 Prometheus 抓取，前一个命令的输出应返回 `true`。 如果返回值为 `false`，则需要将配置更新为 `true`。 运行以下命令以启用 OSM Prometheus 抓取：

```azurecli-interactive
kubectl patch configmap -n kube-system osm-config --type merge --patch '{"data":{"prometheus_scraping":"true"}}'
```

你会看到以下输出。

```Output
configmap/osm-config patched
```

### <a name="update-the-prometheus-configmap"></a>更新 Prometheus Configmap

Prometheus 的默认安装将包含两个 Kubernetes `configmaps`。 可以使用以下命令查看 Prometheus `configmaps` 的列表。

```azurecli-interactive
kubectl get configmap | grep prometheus
```

```Output
stable-prometheus-alertmanager   1      4h34m
stable-prometheus-server         5      4h34m
```

我们需要将 stable-prometheus-server `configmap` 中的 prometheus.yml 配置替换为以下 OSM 配置。 有多种文件编辑方法可用于完成此任务。 一种简单而安全的方法是导出 `configmap`，为其创建备份副本，然后使用 Visual Studio Code 等编辑器对其进行编辑。

> [!NOTE]
> 如果尚未安装 Visual Studio Code，可以在[此处](https://code.visualstudio.com/Download)下载并安装它。

首先导出 stable-prometheus-server configmap，然后创建备份副本。

```azurecli-interactive
kubectl get configmap stable-prometheus-server -o yaml > cm-stable-prometheus-server.yml
cp cm-stable-prometheus-server.yml cm-stable-prometheus-server.yml.copy
```

接下来，让我们使用 Visual Studio Code 打开该文件进行编辑。

```azurecli-interactive
code cm-stable-prometheus-server.yml
```

在 Visual Studio Code 编辑器中打开 `configmap` 后，将 prometheus.yml 文件替换为以下 OSM 配置并保存文件。

> [!WARNING]
> 务必保留 yaml 文件的缩进结构，这一点非常重要。 对 yaml 文件结构所做的任何更改都可能导致无法重新应用 configmap。

```OSM Prometheus Configmap Configuration
prometheus.yml: |
    global:
      scrape_interval: 10s
      scrape_timeout: 10s
      evaluation_interval: 1m

    scrape_configs:
      - job_name: 'kubernetes-apiservers'
        kubernetes_sd_configs:
        - role: endpoints
        scheme: https
        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
          # TODO need to remove this when the CA and SAN match
          insecure_skip_verify: true
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: '(apiserver_watch_events_total|apiserver_admission_webhook_rejection_count)'
          action: keep
        relabel_configs:
        - source_labels: [__meta_kubernetes_namespace, __meta_kubernetes_service_name, __meta_kubernetes_endpoint_port_name]
          action: keep
          regex: default;kubernetes;https

      - job_name: 'kubernetes-nodes'
        scheme: https
        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
        kubernetes_sd_configs:
        - role: node
        relabel_configs:
        - action: labelmap
          regex: __meta_kubernetes_node_label_(.+)
        - target_label: __address__
          replacement: kubernetes.default.svc:443
        - source_labels: [__meta_kubernetes_node_name]
          regex: (.+)
          target_label: __metrics_path__
          replacement: /api/v1/nodes/${1}/proxy/metrics

      - job_name: 'kubernetes-pods'
        kubernetes_sd_configs:
        - role: pod
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: '(envoy_server_live|envoy_cluster_upstream_rq_xx|envoy_cluster_upstream_cx_active|envoy_cluster_upstream_cx_tx_bytes_total|envoy_cluster_upstream_cx_rx_bytes_total|envoy_cluster_upstream_cx_destroy_remote_with_active_rq|envoy_cluster_upstream_cx_connect_timeout|envoy_cluster_upstream_cx_destroy_local_with_active_rq|envoy_cluster_upstream_rq_pending_failure_eject|envoy_cluster_upstream_rq_pending_overflow|envoy_cluster_upstream_rq_timeout|envoy_cluster_upstream_rq_rx_reset|^osm.*)'
          action: keep
        relabel_configs:
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
          action: keep
          regex: true
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_path]
          action: replace
          target_label: __metrics_path__
          regex: (.+)
        - source_labels: [__address__, __meta_kubernetes_pod_annotation_prometheus_io_port]
          action: replace
          regex: ([^:]+)(?::\d+)?;(\d+)
          replacement: $1:$2
          target_label: __address__
        - source_labels: [__meta_kubernetes_namespace]
          action: replace
          target_label: source_namespace
        - source_labels: [__meta_kubernetes_pod_name]
          action: replace
          target_label: source_pod_name
        - regex: '(__meta_kubernetes_pod_label_app)'
          action: labelmap
          replacement: source_service
        - regex: '(__meta_kubernetes_pod_label_osm_envoy_uid|__meta_kubernetes_pod_label_pod_template_hash|__meta_kubernetes_pod_label_version)'
          action: drop
        # for non-ReplicaSets (DaemonSet, StatefulSet)
        # __meta_kubernetes_pod_controller_kind=DaemonSet
        # __meta_kubernetes_pod_controller_name=foo
        # =>
        # workload_kind=DaemonSet
        # workload_name=foo
        - source_labels: [__meta_kubernetes_pod_controller_kind]
          action: replace
          target_label: source_workload_kind
        - source_labels: [__meta_kubernetes_pod_controller_name]
          action: replace
          target_label: source_workload_name
        # for ReplicaSets
        # __meta_kubernetes_pod_controller_kind=ReplicaSet
        # __meta_kubernetes_pod_controller_name=foo-bar-123
        # =>
        # workload_kind=Deployment
        # workload_name=foo-bar
        # deplyment=foo
        - source_labels: [__meta_kubernetes_pod_controller_kind]
          action: replace
          regex: ^ReplicaSet$
          target_label: source_workload_kind
          replacement: Deployment
        - source_labels:
          - __meta_kubernetes_pod_controller_kind
          - __meta_kubernetes_pod_controller_name
          action: replace
          regex: ^ReplicaSet;(.*)-[^-]+$
          target_label: source_workload_name

      - job_name: 'smi-metrics'
        kubernetes_sd_configs:
        - role: pod
        relabel_configs:
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
          action: keep
          regex: true
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_path]
          action: replace
          target_label: __metrics_path__
          regex: (.+)
        - source_labels: [__address__, __meta_kubernetes_pod_annotation_prometheus_io_port]
          action: replace
          regex: ([^:]+)(?::\d+)?;(\d+)
          replacement: $1:$2
          target_label: __address__
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: 'envoy_.*osm_request_(total|duration_ms_(bucket|count|sum))'
          action: keep
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_(\d{3})_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: response_code
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_(.*)_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_(.*)_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_(.*)_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_(.*)_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_pod
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_(.*)_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: destination_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_(.*)_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: destination_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_(.*)_destination_pod_.*_osm_request_total
          target_label: destination_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_(.*)_osm_request_total
          target_label: destination_pod
        - source_labels: [__name__]
          action: replace
          regex: .*(osm_request_total)
          target_label: __name__

        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_(.*)_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_(.*)_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_(.*)_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_(.*)_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_pod
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_(.*)_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_(.*)_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_(.*)_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_(.*)_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_pod
        - source_labels: [__name__]
          action: replace
          regex: .*(osm_request_duration_ms_(bucket|sum|count))
          target_label: __name__

      - job_name: 'kubernetes-cadvisor'
        scheme: https
        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
        kubernetes_sd_configs:
        - role: node
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: '(container_cpu_usage_seconds_total|container_memory_rss)'
          action: keep
        relabel_configs:
        - action: labelmap
          regex: __meta_kubernetes_node_label_(.+)
        - target_label: __address__
          replacement: kubernetes.default.svc:443
        - source_labels: [__meta_kubernetes_node_name]
          regex: (.+)
          target_label: __metrics_path__
          replacement: /api/v1/nodes/${1}/proxy/metrics/cadvisor
```

使用以下命令应用更新后的 `configmap` yaml 文件。

```azurecli-interactive
kubectl apply -f cm-stable-prometheus-server.yml
```

```Output
configmap/stable-prometheus-server configured
```

> [!NOTE]
> 你可能会收到一条消息，提示缺少所需的 kubernetes 注释。 目前可以忽略该消息。

### <a name="verify-prometheus-is-configured-to-scrape-the-osm-mesh-and-api-endpoints"></a>验证是否已将 Prometheus 配置为抓取 OSM 网格和 API 终结点

为了验证 Prometheus 是否已正确配置为抓取 OSM 网格和 API 终结点，我们将使用端口转发来转到 Prometheus Pod 并查看目标配置。 运行以下命令。

```azurecli-interactive
PROM_POD_NAME=$(kubectl get pods -l "app=prometheus,component=server" -o jsonpath="{.items[0].metadata.name}")
kubectl --namespace <promNamespace> port-forward $PROM_POD_NAME 9090
```

在浏览器中打开 `http://localhost:9090/targets`

如果向下滚动，你应该会看到所有 SMI 指标终结点状态为 UP，并且会看到已定义的其他 OSM 指标，如下图所示。

![OSM Prometheus 目标指标 UI 图像](./media/aks-osm-addon/osm-prometheus-smi-metrics-target-scrape.png)

## <a name="deploy-and-configure-a-grafana-instance-for-osm&quot;></a>为 OSM 部署并配置 Grafana 实例

我们将使用 Helm 来部署 Grafana 实例。 运行以下命令，通过 Helm 安装 Grafana：

```
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
helm install osm-grafana grafana/grafana
```

接下来，我们将检索用于登录到 Grafana 站点的默认 Grafana 密码。

```azurecli-interactive
kubectl get secret --namespace default osm-grafana -o jsonpath=&quot;{.data.admin-password}&quot; | base64 --decode ; echo
```

记下 Grafana 密码。

接下来，我们将检索 Grafana Pod，以使用端口转发来转到用于登录的 Grafana 仪表板。

```azurecli-interactive
GRAF_POD_NAME=$(kubectl get pods -l &quot;app.kubernetes.io/name=grafana&quot; -o jsonpath=&quot;{.items[0].metadata.name}")
kubectl port-forward $GRAF_POD_NAME 3000
```

在浏览器中打开 `http://localhost:3000`

在下图所示的登录屏幕中，输入用户名 admin 并使用先前捕获的 Grafana 密码。

![OSM Grafana 登录页 UI 图像](./media/aks-osm-addon/osm-grafana-ui-login.png)

### <a name="configure-the-grafana-prometheus-data-source"></a>配置 Grafana Prometheus 数据源

成功登录到 Grafana 后，下一步是添加 Prometheus 作为 Grafana 的数据源。 为此，请导航到左侧菜单上的“配置”图标并选择“数据源”，如下所示。

![OSM Grafana 数据源页 UI 图像](./media/aks-osm-addon/osm-grafana-ui-datasources.png)

单击“添加数据源”按钮，然后选择“时序数据库”下的“Prometheus”。

![OSM Grafana 数据源选择页 UI 图像](./media/aks-osm-addon/osm-grafana-ui-datasources-select-prometheus.png)

在“请在下面配置 Prometheus 数据源”页中，为“HTTP URL 设置”输入 Prometheus 服务的 Kubernetes 群集 FQDN。 默认 FQDN 应是 `stable-prometheus-server.default.svc.cluster.local`。 输入 Prometheus 服务终结点后，滚动到页面底部并选择“保存并测试”。 你应该会看到一个绿色复选框，指示数据源正在工作。

### <a name="importing-osm-dashboards"></a>导入 OSM 仪表板

OSM 仪表板通过以下两个途径提供：

- [我们的存储库](https://github.com/grafana/grafana)，可通过 Web 管理门户作为 json blob 导入
- [在线提供，网址为 Grafana.com](https://grafana.com/grafana/dashboards/14145)

若要导入仪表板，请在左侧菜单中查找 `+` 符号，然后选择`import`。
可以直接在 `Grafana.com` 上按 ID 导入仪表板。 例如，我们的`OSM Mesh Details`仪表板使用 ID `14145`，你可以直接在表格中使用该 ID 并选择`import`：

![OSM Grafana 仪表板导入页 UI 图像](./media/aks-osm-addon/osm-grafana-dashboard-import.png)

选择“导入”后，系统会自动定向到导入的仪表板。

![OSM Grafana 面板网格详细信息页 UI 图像](./media/aks-osm-addon/osm-grafana-mesh-dashboard-details.png)

## <a name="deploy-and-configure-a-jaeger-operator-on-kubernetes-for-osm"></a>在适用于 OSM 的 Kubernetes 上部署并配置 Jaeger Operator

[Jaeger](https://www.jaegertracing.io/) 是一个开源跟踪系统，用于监视分布式系统以及对其进行故障排除。 它可以作为新实例与 OSM 一起部署，你也可以创建自己的实例。 按照以下说明将 Jaeger 的新实例部署到 AKS 群集上的 `jaeger` 命名空间。

### <a name="deploy-jaeger-to-the-aks-cluster"></a>将 Jaeger 部署到 AKS 群集

应用以下清单以安装 Jaeger：

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: jaeger
  namespace: jaeger
  labels:
    app: jaeger
spec:
  replicas: 1
  selector:
    matchLabels:
      app: jaeger
  template:
    metadata:
      labels:
        app: jaeger
    spec:
      containers:
        - name: jaeger
          image: jaegertracing/all-in-one
          args:
          - --collector.zipkin.host-port=9411
          imagePullPolicy: IfNotPresent
          ports:
          - containerPort: 9411
          resources:
            limits:
              cpu: 500m
              memory: 512M
            requests:
              cpu: 100m
              memory: 256M
---
kind: Service
apiVersion: v1
metadata:
  name: jaeger
  namespace: jaeger
  labels:
    app: jaeger
spec:
  selector:
    app: jaeger
  ports:
  - protocol: TCP
    # Service port and target port are the same
    port: 9411
  type: ClusterIP
EOF
```

```Output
deployment.apps/jaeger created
service/jaeger created
```

### <a name="enable-tracing-for-the-osm-add-on"></a>为 OSM 附加产品启用跟踪

接下来，我们将需要为 OSM 附加产品启用跟踪。

运行以下命令，为 OSM 附加产品启用跟踪：

```azurecli-interactive
kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"observability":{"tracing":{"enable":true}}}}' --type=merge
```

```Output
meshconfig.config.openservicemesh.io/osm-mesh-config patched
```

### <a name="view-the-jaeger-ui-with-port-forwarding"></a>使用端口转发查看 Jaeger UI

Jaeger 的 UI 正在端口 16686 上运行。 若要查看 Web UI，可以使用 kubectl port-forward：

```azurecli-interactive
JAEGER_POD=$(kubectl get pods -n jaeger --no-headers  --selector app=jaeger | awk 'NR==1{print $1}')
kubectl port-forward -n jaeger $JAEGER_POD  16686:16686
http://localhost:16686/
```

在浏览器中，你应该会看到“服务”下拉菜单，可以从中选择由 bookstore 演示部署的各个应用程序。 选择一个服务以查看其中的所有时间跨度。 例如，如果选择“`bookbuyer`”并为“回溯”选择一小时，将显示该服务与 bookstore v1 和 bookstore v2 的交互并按时间排序。

![OSM Jaeger 跟踪页 UI 图像](./media/aks-osm-addon/osm-jaeger-trace-view-ui.png)

选择任何项可查看其更多详细信息。 选择多个项可比较跟踪。 例如，你可以实时比较 `bookbuyer` 在特定时刻与 bookstore 和 bookstore-v2 的交互。

你还可以选择“系统体系结构”选项卡，查看各个应用程序的交互/通信情况的图表。 该图表可让你了解流量在应用程序之间的流动情况。

![OSM Jaeger“系统体系结构”UI 映像](./media/aks-osm-addon/osm-jaeger-sys-arc-view-ui.png)