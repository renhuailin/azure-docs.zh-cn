---
title: 使用 HTTP 代理配置 Azure Kubernetes 服务 (AKS) 节点
description: 对 Azure Kubernetes 服务 (AKS) 节点使用 HTTP 代理配置功能。
services: container-service
author: nickomang
ms.topic: article
ms.date: 09/09/2021
ms.author: nickoman
ms.openlocfilehash: 19a1392756596a1cbfe7000ebd9c7013c053c153
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2021
ms.locfileid: "129856916"
---
# <a name="http-proxy-support-in-azure-kubernetes-service-preview"></a>Azure Kubernetes 服务中的 HTTP 代理支持（预览版）

无论是部署到托管虚拟网络还是自定义虚拟网络的 Azure Kubernetes 服务 (AKS) 群集，都具有正常运行所需的某些出站依赖项。 以前，在需要通过 HTTP 代理路由 Internet 访问的环境中，这是一个问题。 节点无法启动访问 Internet 服务所需的配置、环境变量和证书。

此功能为 AKS 群集添加了 HTTP 代理支持，并公开了一个简单的接口，群集操作员可以使用该接口来保护依赖代理的环境中需要 AKS 的网络流量。

一些更复杂的解决方案可能需要创建信任链来建立跨网络的安全通信。 此功能还支持在启动群集的过程中，在节点上安装受信任的证书颁发机构。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="limitations-and-other-details"></a>限制和其他详细信息

**不** 支持以下方案：
- 每个节点池使用不同的代理配置
- 创建群集后更新代理设置
- 用户/密码身份验证
- 用于 API 服务器通信的自定义 CA
- 基于 Windows 的群集
- 使用虚拟机可用性集 (VMAS) 的节点池

默认情况下，httpProxy、httpsProxy 和 trustedCa 没有值。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/free)。
* [已安装 Azure CLI](/cli/azure/install-azure-cli)。

### <a name="register-the-httpproxyconfigpreview-preview-feature"></a>注册 `HTTPProxyConfigPreview` 预览版功能

若要使用该功能，还必须在订阅上启用 `HTTPProxyConfigPreview` 功能标志。

使用 [az feature register][az-feature-register] 命令注册 `HTTPProxyConfigPreview` 功能标志，如以下示例所示：

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "HTTPProxyConfigPreview"
```

状态显示为“已注册”需要几分钟时间。 使用 [az feature list][az-feature-list] 命令验证注册状态：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/HTTPProxyConfigPreview')].{Name:name,State:properties.state}"
```

准备就绪后，使用 [az provider register][az-provider-register] 命令刷新 Microsoft.ContainerService 资源提供程序的注册状态：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="configuring-an-http-proxy-using-azure-cli"></a>使用 Azure CLI 配置 HTTP 代理 

可通过以下方式结合使用 AKS 与 HTTP 代理来创建群集：使用 [az aks create][az-aks-create] 命令，并以 JSON 或 YAML 文件的形式传入配置​​。

配置文件的架构如下所示：

```json
"httpProxyConfig": {
    "httpProxy": "string",
    "httpsProxy": "string",
    "noProxy": [
        "string"
    ],
    "trustedCa": "string"
}
```

创建一个文件并为 httpProxy、httpsProxy 和 noProxy 提供值。 如果环境需要，还要提供 trustedCa 值。 接下来，部署群集，并通过 `proxy-configuration-file` 标志传入文件名。

```azurecli
az aks create -n $clusterName -g $resourceGroup --proxy-configuration-file aks-proxy-config.json
```

群集将使用节点上配置的 HTTP 代理进行初始化。

## <a name="configuring-an-http-proxy-using-azure-resource-manager-arm-templates"></a>使用 Azure 资源管理器 (ARM) 模板配置 HTTP 代理

使用通过 ARM 模板配置的 HTTP 代理部署 AKS 群集非常简单。 用于 CLI 部署的相同架构位于属性下的 `Microsoft.ContainerService/managedClusters` 定义中：

```json
"properties": {
    ...,
    "httpProxyConfig": {
        "httpProxy": "string",
        "httpsProxy": "string",
        "noProxy": [
            "string"
        ],
        "trustedCa": "string"
    }
}
```

在模板中，为 httpProxy、httpsProxy 和 noProxy 提供值。 必要时，还需要为 `trustedCa 提供值。 部署模板，群集应使用节点上配置的 HTTP 代理进行初始化。

## <a name="handling-ca-rollover"></a>处理 CA 滚动更新

创建群集后，无法更改 httpProxy、httpsProxy 和 noProxy 的值。 但是，为了支持滚动更新 CA 证书，可以使用 [az aks update][az-aks-update] 命令更改 trustedCa 的值并将其应用于群集。

例如，假设使用名为 aks-proxy-config-2.json 的新 CA 证书的 base64 编码字符串创建了一个新文件，以下操作将更新群集：

```azurecli
az aks update -n $clusterName -g $resourceGroup --proxy-configuration-file aks-proxy-config-2.json
```

## <a name="next-steps"></a>后续步骤
- 有关 AKS 群集的网络要求的详细信息，请参阅[控制 AKS 中群集节点的出口流量][aks-egress]。


<!-- LINKS - internal -->
[aks-egress]: ./limit-egress-traffic.md
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-update]: /cli/azure/aks#az_aks_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register