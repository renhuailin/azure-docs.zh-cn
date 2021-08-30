---
title: 已启用 Azure Arc 的 Open Service Mesh（预览版）
description: 已启用 Arc 的 Kubernetes 群集上的 Open Service Mesh (OSM) 扩展
services: azure-arc
ms.service: azure-arc
ms.date: 07/23/2021
ms.topic: article
author: mayurigupta13
ms.author: mayg
ms.openlocfilehash: ebf73d6a79048a7cd08b0995e98da229f9df46ca
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122068221"
---
# <a name="azure-arc-enabled-open-service-mesh-preview"></a>已启用 Azure Arc 的 Open Service Mesh（预览版）

[Open Service Mesh (OSM)](https://docs.openservicemesh.io/) 是一种轻型、可扩展的云原生服务网格，可让用户统一地管理、保护和获取高度动态微服务环境现成的可观测性功能。

OSM 在 Kubernetes 上运行基于 Envoy 的控制平面，可以使用 [SMI](https://smi-spec.io/) API 进行配置，并通过将 Envoy 代理作为挎斗容器注入到每个应用程序实例旁边来运行。 [详细了解](https://docs.openservicemesh.io/#features) Open Service Mesh 实现的服务网格方案。

### <a name="support-limitations-for-arc-enabled-open-service-mesh"></a>已启用 Arc 的 Open Service Mesh 的支持限制

- 在一个已连接到 Arc 的 Kubernetes 群集上，只能部署一个 Open Service Mesh 实例
- 为 Open Service Mesh v0.8.4 和更高版本推出了此功能的公共预览版。 在[此处](https://github.com/Azure/osm-azure/releases)可以找到最新发布版本。
- 目前支持以下 Kubernetes 发行版
    - AKS 引擎
    - Cluster API Azure
    - Google Kubernetes Engine
    - Canonical Kubernetes Distribution
    - Rancher Kubernetes 引擎
    - OpenShift Kubernetes Distribution
    - Amazon Elastic Kubernetes Service
- 可将 Azure Monitor 与已启用 Azure Arc 的 Open Service Mesh 相集成，但[支持将受到限制](https://github.com/microsoft/Docker-Provider/blob/ci_dev/Documentation/OSMPrivatePreview/ReadMe.md)。


[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

### <a name="prerequisites"></a>先决条件

- 确保满足[此处](extensions.md#prerequisites)列出的所有一般群集扩展先决条件。
- 使用版本 >= v0.4.0 的 az k8s-extension CLI

## <a name="install-arc-enabled-open-service-mesh-osm-on-an-arc-enabled-kubernetes-cluster"></a>在已启用 Arc 的 Kubernetes 群集上安装已启用 Arc 的 Open Service Mesh (OSM)

以下步骤假设已有一个包含受支持 Kubernetes 发行版且已连接到 Azure Arc 的群集。

### <a name="install-a-specific-version-of-osm"></a>安装特定版本的 OSM

确保 KUBECONFIG 环境变量指向要在其中安装 OSM 扩展的 Kubernetes 群集的 kubeconfig。

设置环境变量：

```azurecli-interactive
export VERSION=0.8.4
export CLUSTER_NAME=<arc-cluster-name>
export RESOURCE_GROUP=<resource-group-name>
```

在已启用 Arc 的 Open Service Mesh 的预览期，`az k8s-extension create` 命令仅接受对 `--release-train` 标志使用 `pilot`。 `--auto-upgrade-minor-version` 始终设置为 `false`，且必须提供版本。 如果你有 OpenShift 群集，请使用[此部分](#install-a-specific-version-of-osm-on-openshift-cluster)中的步骤。

```azurecli-interactive
az k8s-extension create --cluster-name $CLUSTER_NAME --resource-group $RESOURCE_GROUP --cluster-type connectedClusters --extension-type Microsoft.openservicemesh --scope cluster --release-train pilot --name osm --version $VERSION
```

应会看到如下所示的输出。 将实际 OSM Helm 图表部署到群集可能需要 3-5 分钟。 在此部署发生之前，你将持续看到 installState 为 Pending。

```json
{
  "autoUpgradeMinorVersion": false,
  "configurationSettings": {},
  "creationTime": "2021-04-29T17:50:11.4116524+00:00",
  "errorInfo": {
    "code": null,
    "message": null
  },
  "extensionType": "microsoft.openservicemesh",
  "id": "/subscriptions/<subscription-id>/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.Kubernetes/connectedClusters/$CLUSTER_NAME/providers/Microsoft.KubernetesConfiguration/extensions/osm",
  "identity": null,
  "installState": "Pending",
  "lastModifiedTime": "2021-04-29T17:50:11.4116525+00:00",
  "lastStatusTime": null,
  "location": null,
  "name": "osm",
  "releaseTrain": "pilot",
  "resourceGroup": "$RESOURCE_GROUP",
  "scope": {
    "cluster": {
      "releaseNamespace": "arc-osm-system"
    },
    "namespace": null
  },
  "statuses": [],
  "type": "Microsoft.KubernetesConfiguration/extensions",
  "version": "0.8.4"
}
```

### <a name="install-a-specific-version-of-osm-on-openshift-cluster"></a>在 OpenShift 群集上安装特定版本的 OSM

1. 将以下内容复制并保存到 JSON 文件中。 如果已创建配置设置文件，请将以下行添加到现有文件，以保留以前的更改。
   ```json
   {
       "osm.OpenServiceMesh.enablePrivilegedInitContainer": "true"
   }
   ```

   将文件路径设置为环境变量：
   ```azurecli-interactive
   export SETTINGS_FILE=<json-file-path>
   ```
   
2. 运行 `az k8s-extension create` 命令用于创建 OSM 扩展，并使用配置设置传入设置文件：
   ```azurecli-interactive
   az k8s-extension create --cluster-name $CLUSTER_NAME --resource-group $RESOURCE_GROUP --cluster-type connectedClusters --extension-type Microsoft.openservicemesh --scope cluster --release-train pilot --name osm --version $VERSION --configuration-settings-file $SETTINGS_FILE
   ```
   
3. 将特权[安全上下文约束](https://docs.openshift.com/container-platform/4.7/authentication/managing-security-context-constraints.html)添加到网格中应用程序的每个服务帐户。
   ```azurecli-interactive
   oc adm policy add-scc-to-user privileged -z <service account name> -n <service account namespace>
   ```

将实际 OSM Helm 图表部署到群集可能需要 3-5 分钟。 在此部署发生之前，你将持续看到 installState 为 Pending。

为确保特权 init 容器设置不会还原为默认设置，请在所有后续 az k8s-extension create 命令中传入 "osm.OpenServiceMesh.enablePrivilegedInitContainer" : "true" 配置设置。

### <a name="install-arc-enabled-osm-using-arm-template"></a>使用 ARM 模板安装已启用 Arc 的 OSM

将群集连接到 Azure Arc 后，创建采用以下格式的 JSON 文件，并确保更新 <cluster-name> 值：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "ConnectedClusterName": {
            "defaultValue": "<cluster-name>",
            "type": "String",
            "metadata": {
                "description": "The Connected Cluster name."
            }
        },
        "ExtensionInstanceName": {
            "defaultValue": "osm",
            "type": "String",
            "metadata": {
                "description": "The extension instance name."
            }
        },
        "ExtensionVersion": {
            "defaultValue": "0.8.4",
            "type": "String",
            "metadata": {
                "description": "The extension type version."
            }
        },
        "ExtensionType": {
            "defaultValue": "Microsoft.openservicemesh",
            "type": "String",
            "metadata": {
                "description": "The extension type."
            }
        },
        "ReleaseTrain": {
            "defaultValue": "Pilot",
            "type": "String",
            "metadata": {
                "description": "The release train."
            }
        }
    },
    "functions": [],
    "resources": [
        {
            "type": "Microsoft.KubernetesConfiguration/extensions",
            "apiVersion": "2020-07-01-preview",
            "name": "[parameters('ExtensionInstanceName')]",
            "properties": {
                "extensionType": "[parameters('ExtensionType')]",
                "releaseTrain": "[parameters('ReleaseTrain')]",
                "version": "[parameters('ExtensionVersion')]"
            },
            "scope": "[concat('Microsoft.Kubernetes/connectedClusters/', parameters('ConnectedClusterName'))]"
        }
    ]
}
```

现在设置环境变量：

```azurecli-interactive
export TEMPLATE_FILE_NAME=<template-file-path>
export DEPLOYMENT_NAME=<desired-deployment-name>
```

最后，运行以下命令以通过 az CLI 安装 OSM 扩展：

```azurecli-interactive
az deployment group create --name $DEPLOYMENT_NAME --resource-group $RESOURCE_GROUP --template-file $TEMPLATE_FILE_NAME
```

现在，应该可以查看 OSM 资源并在群集中使用 OSM 扩展。

## <a name="validate-the-arc-enabled-open-service-mesh-installation"></a>验证已启用 Arc 的 Open Service Mesh 安装

运行以下命令。

```azurecli-interactive
az k8s-extension show --cluster-type connectedClusters --cluster-name $CLUSTER_NAME --resource-group $RESOURCE_GROUP --name osm
```

应会看到如下所示的 JSON 输出：

```json
{
  "autoUpgradeMinorVersion": false,
  "configurationSettings": {},
  "creationTime": "2021-04-29T19:22:00.7649729+00:00",
  "errorInfo": {
    "code": null,
    "message": null
  },
  "extensionType": "microsoft.openservicemesh",
  "id": "/subscriptions/<subscription-id>/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.Kubernetes/connectedClusters/$CLUSTER_NAME/providers/Microsoft.KubernetesConfiguration/extensions/osm",
  "identity": null,
  "installState": "Installed",
  "lastModifiedTime": "2021-04-29T19:22:00.7649731+00:00",
  "lastStatusTime": "2021-04-29T19:23:27.642+00:00",
  "location": null,
  "name": "osm",
  "releaseTrain": "pilot",
  "resourceGroup": "$RESOURCE_GROUP",
  "scope": {
    "cluster": {
      "releaseNamespace": "arc-osm-system"
    },
    "namespace": null
  },
  "statuses": [],
  "type": "Microsoft.KubernetesConfiguration/extensions",
  "version": "0.8.4"
}
```
## <a name="osm-controller-configuration"></a>OSM 控制器配置
OSM 在 arc-osm-system 命名空间中部署 MeshConfig 资源 `osm-mesh-config` 作为其控制平面的一部分。 此 MeshConfig 的目的是使网格所有者/操作员能够根据其需求更新某些网格配置。 若要查看默认值，请使用以下命令。

```azurecli-interactive
kubectl describe meshconfig osm-mesh-config -n arc-osm-system
```
输出将显示默认值：

```azurecli-interactive
Certificate:
    Service Cert Validity Duration:  24h
  Feature Flags:
    Enable Egress Policy:      true
    Enable Multicluster Mode:  false
    Enable WASM Stats:         true
  Observability:
    Enable Debug Server:  false
    Osm Log Level:        info
    Tracing:
      Address:   jaeger.osm-system.svc.cluster.local
      Enable:    false
      Endpoint:  /api/v2/spans
      Port:      9411
  Sidecar:
    Config Resync Interval:            0s
    Enable Privileged Init Container:  false
    Envoy Image:                       mcr.microsoft.com/oss/envoyproxy/envoy:v1.18.3
    Init Container Image:              mcr.microsoft.com/oss/openservicemesh/init:v0.9.1
    Log Level:                         error
    Max Data Plane Connections:        0
    Resources:
  Traffic:
    Enable Egress:                          false
    Enable Permissive Traffic Policy Mode:  true
    Inbound External Authorization:
      Enable:              false
      Failure Mode Allow:  false
      Stat Prefix:         inboundExtAuthz
      Timeout:             1s
    Use HTTPS Ingress:     false
```
有关详细信息，请参阅[配置 API 参考](https://docs.openservicemesh.io/docs/api_reference/config/v1alpha1/)。 请注意，“spec.traffic.enablePermissiveTrafficPolicyMode”设置为“true” 。 在 OSM 中的宽松流量策略模式下，系统会绕过 [SMI](https://smi-spec.io/) 流量策略强制执行。 在此模式下，OSM 会自动发现属于服务网格一部分的服务，并在每个 Envoy 代理挎斗上对流量策略规则进行编程，以便能够与这些服务通信。

### <a name="making-changes-to-osm-controller-configuration"></a>对 OSM 控制器配置进行更改

> [!NOTE]
> MeshConfig `osm-mesh-config` 中的值在升级过程中保持不变。

可以使用 kubectl patch 命令对 `osm-mesh-config` 进行更改。 在以下示例中，宽松流量策略模式更改为了 false。

```azurecli-interactive
kubectl patch meshconfig osm-mesh-config -n arc-osm-system -p '{"spec":{"traffic":{"enablePermissiveTrafficPolicyMode":false}}}' --type=merge
```

如果使用了不正确的值，则 MeshConfig CRD 上的验证将阻止更改并显示一条错误消息，说明值无效的原因。 例如，下面的命令显示了将 enableEgress 修补为非布尔值时会发生的情况。

```azurecli-interactive
kubectl patch meshconfig osm-mesh-config -n arc-osm-system -p '{"spec":{"traffic":{"enableEgress":"no"}}}'  --type=merge

# Validations on the CRD will deny this change
The MeshConfig "osm-mesh-config" is invalid: spec.traffic.enableEgress: Invalid value: "string": spec.traffic.enableEgress in body must be of type boolean: "string"
```

## <a name="osm-controller-configuration-version-v084"></a>OSM 控制器配置（版本 v0.8.4）

目前可以通过 ConfigMap 访问和配置 OSM 控制器配置。 若要查看 OSM 控制器配置设置，请通过 `kubectl` 查询 `osm-config` ConfigMap 来查看其配置设置。

```azurecli-interactive
kubectl get configmap osm-config -n arc-osm-system -o json
```

输出：

```json
{
  "egress": "false",
  "enable_debug_server": "false",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "permissive_traffic_policy_mode": "true",
  "prometheus_scraping": "true",
  "service_cert_validity_duration": "24h",
  "tracing_enable": "false",
  "use_https_ingress": "false"
}
```

请参阅 [OSM ConfigMap 文档](https://release-v0-8.docs.openservicemesh.io/docs/osm_config_map/)了解每个可用配置。 

若要对 OSM ConfigMap v0.8.4 版进行更改，请参考以下指导：

1. 将你要做出的更改复制并保存在 JSON 文件中。 在此示例中，我们要将 permissive_traffic_policy_mode 从 true 更改为 false。 每次对 `osm-config` 进行更改时，都必须在 JSON 文件中提供完整的更改列表（与默认的 `osm-config` 相比较）。
    ```json
    {
        "osm.OpenServiceMesh.enablePermissiveTrafficPolicy" : "false"
    }
    ```
    
    将文件路径设置为环境变量：
    
    ```azurecli-interactive
    export SETTINGS_FILE=<json-file-path>
    ```
    
2. 运行同一个 `az k8s-extension create` 命令用于创建扩展，但现在请传入配置设置文件： 
    ```azurecli-interactive
    az k8s-extension create --cluster-name $CLUSTER_NAME --resource-group $RESOURCE_GROUP --cluster-type connectedClusters --extension-type Microsoft.openservicemesh --scope cluster --release-train pilot --name osm --version $VERSION --configuration-settings-file $SETTINGS_FILE
    ```
    
    > [!NOTE]
    > 为确保 ConfigMap 更改不会还原为默认值，请在所有后续 az k8s-extension create 命令中传入相同的配置设置。

## <a name="using-the-arc-enabled-open-service-mesh"></a>使用已启用 Arc 的 Open Service Mesh

若要开始使用 OSM 功能，首先需要将应用程序命名空间加入服务网格。 从 [OSM GitHub 版本页](https://github.com/openservicemesh/osm/releases/)下载 OSM CLI。 将命名空间添加到网格后，可以配置 SMI 策略以实现所需的 OSM 功能。

### <a name="onboard-namespaces-to-the-service-mesh"></a>将命名空间加入服务网格

运行以下命令将命名空间添加到网格：

```azurecli-interactive
osm namespace add <namespace_name>
```

在[此处](https://docs.openservicemesh.io/docs/guides/app_onboarding/#onboard-services)可以找到有关加入服务的详细信息。

### <a name="configure-osm-with-service-mesh-interface-smi-policies"></a>为 OSM 配置服务网格接口 (SMI) 策略

可以从某个[演示应用程序](https://docs.openservicemesh.io/docs/getting_started/quickstart/manual_demo/#deploy-applications)着手，或者使用测试环境来试用 SMI 策略。

> [!NOTE] 
> 确保运行的 bookstore 应用程序版本与群集上安装的 OSM 扩展版本相匹配。 例如：如果使用的是 OSM 扩展 v0.8.4，请使用 OSM 上游存储库的 release-v0.8 分支中的 bookstore 演示应用程序。

### <a name="configuring-your-own-jaeger-prometheus-and-grafana-instances"></a>配置自己的 Jaeger、Prometheus 和 Grafana 实例

OSM 扩展不会安装 [Jaeger](https://www.jaegertracing.io/docs/getting-started/)、[Prometheus](https://prometheus.io/docs/prometheus/latest/installation/) 和 [Grafana](https://grafana.com/docs/grafana/latest/installation/) 等附加组件，以便用户可以改为将 OSM 与他们自己的、正在运行的这些工具实例相集成。 若要与你自己的实例集成，请查看以下文档：

> [!NOTE]
> 请谨慎使用 OSM GitHub 文档中提供的命令。 对 `osm-mesh-config` 进行更改时，请确保使用正确的命名空间名称“arc-osm-system”。

- [BYO-Jaeger 实例](https://docs.openservicemesh.io/docs/guides/observability/tracing/#byo-bring-your-own)
- [BYO-Prometheus 实例](https://docs.openservicemesh.io/docs/guides/observability/metrics/#byo-prometheus)
- [BYO-Grafana 仪表板](https://docs.openservicemesh.io/docs/guides/observability/metrics/#importing-dashboards-on-a-byo-grafana-instance)


## <a name="monitoring-application-using-azure-monitor-and-applications-insights"></a>使用 Azure Monitor 和 Applications Insights 监视应用程序

Azure Monitor 和 Azure Application Insights 都提供用于收集、分析和处理来自云环境与本地环境的遥测数据的综合解决方案，可帮助你将应用程序和服务的可用性和性能最大化。

已启用 Arc 的 Open Service Mesh 将深度集成到这两个 Azure 服务，并提供无缝的 Azure 体验用于查看和响应 OSM 指标所提供的关键 KPI。 按照以下步骤允许 Azure Monitor 抓取 prometheus 终结点以收集应用程序指标。 

1. 确保在 `osm-mesh-config` 中将 prometheus_scraping 设置为 true。

2. 确保要监视的应用程序命名空间已加入到网格。 请按照[此处](#onboard-namespaces-to-the-service-mesh)提供的指导操作。

3. 公开应用程序命名空间的 prometheus 终结点。
    ```azurecli-interactive
    osm metrics enable --namespace <namespace1>
    osm metrics enable --namespace <namespace2>
    ```

4. 参考[此处](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md?toc=/azure/azure-arc/kubernetes/toc.json)提供的指导安装 Azure Monitor 扩展。

5. 在 container-azm-ms-osmconfig ConfigMap 中添加要监视的命名空间。 从[此处](https://github.com/microsoft/Docker-Provider/blob/ci_prod/kubernetes/container-azm-ms-osmconfig.yaml)下载 ConfigMap。
    ```azurecli-interactive
    monitor_namespaces = ["namespace1", "namespace2"]
    ```

6. 运行以下 kubectl 命令
    ```azurecli-interactive
    kubectl apply -f container-azm-ms-osmconfig.yaml
    ```

最长可能需要在 15 分钟后，指标才会显示在 Log Analytics 中。 可以尝试查询 InsightsMetrics 表。

```azurecli-interactive
InsightsMetrics
| where Name contains "envoy"
| extend t=parse_json(Tags)
| where t.app == "namespace1"
```
在[此处](https://github.com/microsoft/Docker-Provider/blob/ci_dev/Documentation/OSMPrivatePreview/ReadMe.md)详细了解如何与 Azure Monitor 集成。

### <a name="navigating-the-osm-dashboard"></a>在 OSM 仪表板中导航

1. 使用[此链接](https://aka.ms/azmon/osmarcux)访问已连接到 Arc 的 Kubernetes 群集。
2. 转到 Azure Monitor，导航到“报告”选项卡以访问 OSM 工作簿。
3. 选择时间范围和命名空间以限定服务的范围。

![OSM 工作簿](./media/tutorial-arc-enabled-open-service-mesh/osm-workbook.jpg)

#### <a name="requests-tab"></a>“请求”选项卡

- 此选项卡提供通过 OSM 中的服务间通信发送的所有 HTTP 请求的摘要。
- 可以通过在网格中选择服务来查看所有服务以及与其通信的所有服务。
- 可以查看请求总数、请求错误率和 P90 延迟。
- 可以向下钻取到目标，并查看 HTTP 错误/成功代码、成功率、Pod 资源利用率和以不同百分位表示的延迟的趋势。

#### <a name="connections-tab"></a>“连接”选项卡

- 此选项卡提供 Open Service Mesh 中服务之间的所有连接的摘要。
- 出站连接：源与目标服务之间的连接总数。
- 出站活动连接：所选时间范围内源与目标之间的活动连接的上次计数。
- 出站失败连接：源与目标服务之间的失败连接总数

## <a name="upgrade-the-osm-extension-instance-to-a-specific-version"></a>将 OSM 扩展实例升级到特定版本

在升级期间，控制平面可能会停机一段时间。 只有在升级 CRD 期间，数据平面才会受影响。

### <a name="supported-upgrades"></a>支持的升级

最多可将 OSM 扩展升级到下一个次要版本。 目前不支持降级和主要版本升级。

### <a name="crd-upgrades"></a>CRD 升级

如果新版本包含 CRD 版本更新，则在不首先删除现有 CRD 的情况下，无法将 OSM 扩展升级到新版本。 可以通过查看 [OSM 发行说明](https://github.com/openservicemesh/osm/releases)的“CRD 更新”部分，来检查 OSM 升级是否也包含 CRD 版本更新。

在删除 CRD 之前确保备份自定义资源，以便在升级后可以轻松重新创建这些资源。 然后，按照下面捕获的升级说明进行操作。

> [!NOTE] 
> 升级 CRD 会影响数据平面，因为在删除 SMI 策略之后到重新创建这些策略之前的这段时间，这些策略并不存在。

### <a name="upgrade-instructions"></a>升级说明

1. 删除旧的 CRD 和自定义资源（从 [OSM 存储库](https://github.com/openservicemesh/osm)的根目录运行）。 确保 [OSM CRD](https://github.com/openservicemesh/osm/tree/main/charts/osm/crds) 的标记对应于图表的新版本。
    ```azurecli-interactive
    kubectl delete --ignore-not-found --recursive -f ./charts/osm/crds/

2. Install the updated CRDs.
    ```azurecli-interactive
    kubectl apply -f charts/osm/crds/
    ```

3. 将新图表版本设置为环境变量：
    ```azurecli-interactive
    export VERSION=<chart version>
    ```
    
4. 结合新图表版本运行 az k8s-extension create
    ```azurecli-interactive
    az k8s-extension create --cluster-name $CLUSTER_NAME --resource-group $RESOURCE_GROUP --cluster-type connectedClusters --extension-type Microsoft.openservicemesh --scope cluster --release-train pilot --name osm --version $VERSION --configuration-settings-file $SETTINGS_FILE
    ```

5. 使用新 CRD 重新创建自定义资源

## <a name="uninstall-arc-enabled-open-service-mesh"></a>部署已启用 Arc 的 Open Service Mesh

请使用以下命令：

```azurecli-interactive
az k8s-extension delete --cluster-type connectedClusters --cluster-name $CLUSTER_NAME --resource-group $RESOURCE_GROUP --name osm -y
```

验证是否已删除扩展实例：

```azurecli-interactive
az k8s-extension list --cluster-type connectedClusters --cluster-name $CLUSTER_NAME --resource-group $RESOURCE_GROUP
```

此输出不应包含 OSM。 如果群集上未安装任何其他扩展，则输出只是一个空数组。

使用 az k8s-extension 命令删除 OSM 扩展时，将不会删除 arc-osm-system 命名空间，该命名空间中的实际资源（例如变异 Webhook 配置和 osm-controller Pod）将在大约 10 分钟后才会删除。

> [!NOTE] 
> 使用 az k8s-extension CLI 卸载 Arc 管理的 OSM 组件。Arc 不支持使用 OSM CLI 进行卸载，此操作可能导致不利的行为。

## <a name="troubleshooting"></a>故障排除

参阅[此处](troubleshooting.md#arc-enabled-open-service-mesh)提供的故障排除指南。

## <a name="next-steps"></a>后续步骤

> 想尝试一下吗？  
> 使用群集 API 快速开始实现 [Azure Arc Jumpstart](https://aka.ms/arc-jumpstart-osm) 方案。
