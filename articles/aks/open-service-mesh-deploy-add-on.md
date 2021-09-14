---
title: 部署 Open Service Mesh（预览版）
description: 在 Azure Kubernetes 服务 (AKS) 上部署 Open Service Mesh
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: 8476d6c01c0c388eefad42eb89d65011c57bfa65
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123439937"
---
# <a name="deploy-the-open-service-mesh-aks-add-on-preview"></a>部署 Open Service Mesh AKS 加载项（预览版）
本文讨论如何将 OSM 加载项部署到 AKS。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>先决条件

- Azure CLI 2.20.0 或更高版本
- `aks-preview` 扩展 0.5.5 或更高版本
- OSM 版本 v0.9.1 或更高版本
- JSON 处理器“jq”版本 1.6+

## <a name="install-the-aks-preview-extension"></a>安装 aks-preview 扩展

将需要 aks-preview Azure CLI 扩展 0.5.24 或更高版本。 使用 [az extension add][az-extension-add] 命令安装 aks-preview Azure CLI 扩展。 或者使用 [az extension update][az-extension-update] 命令安装任何可用的更新。

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="register-the-aks-openservicemesh-preview-feature"></a>注册 `AKS-OpenServiceMesh` 预览版功能

若要创建可使用 Open Service Mesh 附加产品的 AKS 群集，必须在订阅中启用 `AKS-OpenServiceMesh` 功能标志。

使用 [az feature register][az-feature-register] 命令注册 `AKS-OpenServiceMesh` 功能标志，如以下示例所示：

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "AKS-OpenServiceMesh"
```

状态显示为“已注册”需要几分钟时间。 使用 [az feature list][az-feature-list] 命令验证注册状态：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-OpenServiceMesh')].{Name:name,State:properties.state}"
```

准备就绪后，使用 [az provider register][az-provider-register] 命令刷新 Microsoft.ContainerService 资源提供程序的注册：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="install-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-a-new-aks-cluster"></a>为新的 AKS 群集安装 Open Service Mesh (OSM) Azure Kubernetes 服务 (AKS) 附加产品

对于新的 AKS 群集部署方案，你将从 AKS 群集的全新部署开始，然后在创建群集时启用 OSM 附加产品。

### <a name="create-a-resource-group"></a>创建资源组

在 Azure 中，可将相关的资源分配到资源组。 使用 [az group create](/cli/azure/group#az_group_create) 创建资源组。 以下示例在 eastus2 位置（区域）创建名为 myOsmAksGroup 的资源组： 

```azurecli-interactive
az group create --name <my-osm-aks-cluster-rg> --location <azure-region>
```

### <a name="deploy-an-aks-cluster-with-the-osm-add-on-enabled"></a>部署启用了 OSM 附加产品的 AKS 群集

现在将部署启用了 OSM 附加产品的新 AKS 群集。

> [!NOTE]
> 请注意，以下 AKS 部署命令将操作系统临时磁盘用于示例 AKS 部署。 可在此处找到有关 [AKS 的临时 OS 磁盘](./cluster-configuration.md#ephemeral-os)的详细信息

```azurecli-interactive
az aks create -n <my-osm-aks-cluster-name> -g <my-osm-aks-cluster-rg> --node-osdisk-type Ephemeral --node-osdisk-size 30 --network-plugin azure --enable-managed-identity -a open-service-mesh
```

#### <a name="get-aks-cluster-access-credentials"></a>获取 AKS 群集访问凭据

获取新的托管 Kubernetes 群集的访问凭据。

```azurecli-interactive
az aks get-credentials -n <my-osm-aks-cluster-name> -g <my-osm-aks-cluster-rg>
```

## <a name="enable-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-an-existing-aks-cluster"></a>为现有 AKS 群集启用 Open Service Mesh (OSM) Azure Kubernetes 服务 (AKS) 附加产品

对于现有 AKS 群集方案，你将为已部署的现有 AKS 群集启用 OSM 附加产品。

### <a name="enable-the-osm-add-on-to-existing-aks-cluster"></a>为现有 AKS 群集启用 OSM 附加产品

若要启用 AKS OSM 附加产品，你需要运行 `az aks enable-addons --addons` 命令并传递 `open-service-mesh` 参数

```azurecli-interactive
az aks enable-addons --addons open-service-mesh -g <my-osm-aks-cluster-rg> -n <my-osm-aks-cluster-name>
```

你应该会看到如下所示的输出，用于确认是否已安装 AKS OSM 附加产品。

```json
{- Finished ..
  "aadProfile": null,
  "addonProfiles": {
    "KubeDashboard": {
      "config": null,
      "enabled": false,
      "identity": null
    },
    "openServiceMesh": {
      "config": {},
      "enabled": true,
      "identity": {
...
```

## <a name="validate-the-aks-osm-add-on-installation"></a>验证 AKS OSM 附加产品安装

你需要运行几个命令来检查 AKS OSM 附加产品的所有组件是否均已启用并正在运行：

首先，我们可以查询群集的附加产品配置文件，以检查已安装附加产品的启用状态。 以下命令应返回“true”。

```azurecli-interactive
az aks list -g <my-osm-aks-cluster-rg> -o json | jq -r '.[].addonProfiles.openServiceMesh.enabled'
```

以下 `kubectl` 命令将报告 osm-controller 的状态。

```azurecli-interactive
kubectl get deployments -n kube-system --selector app=osm-controller
kubectl get pods -n kube-system --selector app=osm-controller
kubectl get services -n kube-system --selector app=osm-controller
```

## <a name="accessing-the-aks-osm-add-on-configuration"></a>访问 AKS OSM 加载项配置

目前可以通过 OSM MeshConfig 资源访问和配置 OSM 控制器配置。 若要通过 CLI 查看 OSM 控制器配置设置，请使用 kubectl get 命令，如下所示。

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n kube-system -o yaml
```

MeshConfig 的输出应如下所示：

```
apiVersion: config.openservicemesh.io/v1alpha1
kind: MeshConfig
metadata:
  creationTimestamp: "0000-00-00A00:00:00A"
  generation: 1
  name: osm-mesh-config
  namespace: kube-system
  resourceVersion: "2494"
  uid: 6c4d67f3-c241-4aeb-bf4f-b029b08faa31
spec:
  certificate:
    serviceCertValidityDuration: 24h
  featureFlags:
    enableEgressPolicy: true
    enableMulticlusterMode: false
    enableWASMStats: true
  observability:
    enableDebugServer: true
    osmLogLevel: info
    tracing:
      address: jaeger.osm-system.svc.cluster.local
      enable: false
      endpoint: /api/v2/spans
      port: 9411
  sidecar:
    configResyncInterval: 0s
    enablePrivilegedInitContainer: false
    envoyImage: mcr.microsoft.com/oss/envoyproxy/envoy:v1.18.3
    initContainerImage: mcr.microsoft.com/oss/openservicemesh/init:v0.9.1
    logLevel: error
    maxDataPlaneConnections: 0
    resources: {}
  traffic:
    enableEgress: true
    enablePermissiveTrafficPolicyMode: true
    inboundExternalAuthorization:
      enable: false
      failureModeAllow: false
      statPrefix: inboundExtAuthz
      timeout: 1s
    useHTTPSIngress: false
```

请注意 enablePermissiveTrafficPolicyMode 配置为“true” 。 在 OSM 中的宽松流量策略模式下，系统会绕过 [SMI](https://smi-spec.io/) 流量策略强制执行。 在此模式下，OSM 会自动发现属于服务网格一部分的服务，并在每个 Envoy 代理挎斗上对流量策略规则进行编程，以便能够与这些服务通信。 有关宽松流量模式的更多详细信息，请访问并阅读[宽松流量策略模式](https://docs.openservicemesh.io/docs/guides/traffic_management/permissive_mode/)一文。 

> [!WARNING]
> 在继续操作之前，请验证宽松流量策略模式是否设置为 true，如果不是，请使用以下命令将其更改为 true

```OSM Permissive Mode to True
kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"traffic":{"enablePermissiveTrafficPolicyMode":true}}}' --type=merge
```