---
title: 使用 Bicep 部署 Open Service Mesh AKS 加载项
description: 使用 Bicep 在 Azure Kubernetes 服务 (AKS) 上部署 Open Service Mesh
services: container-service
ms.topic: article
ms.date: 9/20/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: 2096176c64781e99161188ce66b7dd87d226278e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128594316"
---
# <a name="deploy-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-using-bicep"></a>使用 Bicep 部署 Open Service Mesh (OSM) Azure Kubernetes 服务 (AKS) 加载项

本文介绍如何使用 [Bicep](/azure/azure-resource-manager/bicep/) 模板将 OSM 加载项部署到 AKS。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

[Bicep](/azure/azure-resource-manager/bicep/overview) 是一种特定于域的语言 (DSL)，使用声明性语法来部署 Azure 资源。 如果你不想要创建 Azure [ARM](/azure/azure-resource-manager/templates/overview) 模板来部署基础结构即代码 Azure 资源，可以改用 Bicep。

## <a name="prerequisites"></a>先决条件

- Azure CLI 2.20.0 或更高版本
- `aks-preview` 扩展 0.5.5 或更高版本
- OSM 版本 v0.9.1 或更高版本
- 用于部署 AKS 的 SSH 公钥
- 利用 Bash 终端的 [Visual Studio Code](https://code.visualstudio.com/)
- Visual Studio Code [Bicep 扩展](/azure/azure-resource-manager/bicep/install)

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

准备就绪后，使用 [az provider register][az-provider-register] 命令刷新 Microsoft.ContainerService 资源提供程序的注册状态：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="install-the-osm-aks-add-on-for-a-new-aks-cluster-using-bicep"></a>使用 Bicep 为新的 AKS 群集安装 OSM AKS 加载项

对于新的 AKS 群集部署方案，请从一个在创建群集时启用了 OSM 加载项的全新 AKS 群集部署开始。 以下一系列指导将使用一个通用 Bicep 模板，该模板通过使用临时磁盘、使用 [`kubenet`](/azure/aks/configure-kubenet) CNI 并启用 AKS OSM 加载项来部署 AKS 群集。 有关更高级的部署方案，请访问 [Bicep](/azure/azure-resource-manager/bicep/overview) 文档。

### <a name="create-a-resource-group"></a>创建资源组

在 Azure 中，可以使用资源组来关联相关资源。 使用 [az group create](/cli/azure/group#az_group_create) 创建资源组。 以下示例用于在指定的 Azure 位置（区域）创建一个资源组：

```azurecli-interactive
az group create --name <my-osm-bicep-aks-cluster-rg> --location <azure-region>
```

### <a name="create-the-main-and-parameters-bicep-files"></a>创建 main 和 parameters Bicep 文件

在 Bash 终端保持打开状态的情况下，使用 Visual Studio Code 创建一个目录用于存储所需的 Bicep 部署文件。 以下示例创建名为 `bicep-osm-aks-addon` 的目录并切换到该目录

```azurecli-interactive
mkdir bicep-osm-aks-addon
cd bicep-osm-aks-addon
```

接下来，如以下示例中所示创建 main 和 parameters 文件。

```azurecli-interactive
touch osm.aks.bicep && touch osm.aks.parameters.json
```

打开 `osm.aks.bicep` 文件，将以下示例内容复制到其中，然后保存该文件。

```azurecli-interactive
// https://docs.microsoft.com/azure/aks/troubleshooting#what-naming-restrictions-are-enforced-for-aks-resources-and-parameters
@minLength(3)
@maxLength(63)
@description('Provide a name for the AKS cluster. The only allowed characters are letters, numbers, dashes, and underscore. The first and last character must be a letter or a number.')
param clusterName string
@minLength(3)
@maxLength(54)
@description('Provide a name for the AKS dnsPrefix. Valid characters include alphanumeric values and hyphens (-). The dnsPrefix can\'t include special characters such as a period (.)')
param clusterDNSPrefix string
param k8Version string
param sshPubKey string


resource aksCluster 'Microsoft.ContainerService/managedClusters@2021-03-01' = {
  name: clusterName
  location: resourceGroup().location
  identity: {
    type: 'SystemAssigned'
  }
  properties: {
    kubernetesVersion: k8Version
    dnsPrefix: clusterDNSPrefix
    enableRBAC: true
    agentPoolProfiles: [
      {
        name: 'agentpool'
        count: 3
        vmSize: 'Standard_DS2_v2'
        osDiskSizeGB: 30
        osDiskType: 'Ephemeral'
        osType: 'Linux'
        mode: 'System'
      }
    ]
    linuxProfile: {
      adminUsername: 'adminUserName'
      ssh: {
        publicKeys: [
          {
            keyData: sshPubKey
          }
        ]
      }
    }
    addonProfiles: {
        openServiceMesh: {
            enabled: true
            config: {}
      }
    }
  }
}
```

打开 `osm.aks.parameters.json` 文件，并将以下示例内容复制到其中。 添加特定于部署的参数，然后保存该文件。

> [!NOTE]
> `osm.aks.parameters.json` 是 Bicep 部署所需的示例模板 parameters 文件。 需要专门为部署环境更新指定的参数。 此示例使用的特定参数值需要更新以下参数。 这些参数是 clusterName、clusterDNSPrefix、k8Version 和 sshPubKey   。 若要在区域中查找支持的 Kubernetes 版本列表，请使用 `az aks get-versions --location <region>` 命令。

```azurecli-interactive
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "value": "<YOUR CLUSTER NAME HERE>"
    },
    "clusterDNSPrefix": {
      "value": "<YOUR CLUSTER DNS PREFIX HERE>"
    },
    "k8Version": {
      "value": "<YOUR SUPPORTED KUBERNETES VERSION HERE>"
    },
    "sshPubKey": {
      "value": "<YOUR SSH KEY HERE>"
    }
  }
}
```

### <a name="deploy-the-bicep-file"></a>部署 Bicep 文件

若要部署前面创建的 Bicep 文件，请打开终端并使用 `az login` 命令对 Azure CLI 的 Azure 帐户进行身份验证。 对 Azure 订阅进行身份验证后，运行以下命令进行部署。

```azurecli-interactive
az group create --name osm-bicep-test --location eastus2

az deployment group create \
  --name OSMBicepDeployment \
  --resource-group osm-bicep-test \
  --template-file osm.aks.bicep \
  --parameters @osm.aks.parameters.json
```

部署完成后，应会看到一条指出部署成功的消息。

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

目前可以通过 OSM MeshConfig 资源来访问和配置 OSM 控制器配置，并可以通过 CLI 使用 kubectl get 命令查看 OSM 控制器配置设置，如下所示。

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n kube-system -o yaml
```

MeshConfig 的输出如下所示：

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

请注意 enablePermissiveTrafficPolicyMode 配置为“true” 。 在 OSM 中的宽松流量策略模式下，系统会绕过 [SMI](https://smi-spec.io/) 流量策略强制执行。 在此模式下，OSM 将自动发现作为服务网格一部分的服务。 将在每个 Envoy 代理分支上为发现的服务编写流量策略规则，以允许在这些服务之间通信。

> [!WARNING]
> 在继续操作之前，请验证宽松流量策略模式是否设置为 true，如果不是，请使用以下命令将其更改为 true

```OSM Permissive Mode to True
kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"traffic":{"enablePermissiveTrafficPolicyMode":true}}}' --type=merge
```

## <a name="clean-up-resources"></a>清理资源

如果不再需要 Azure 资源，请使用 Azure CLI 删除部署测试资源组。

```
az group delete --name osm-bicep-test
```

<!-- Links -->
<!-- Internal -->

[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
