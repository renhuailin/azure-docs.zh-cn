---
title: 启用云控制器管理器（预览版）
description: 了解如何启用树外云提供商
services: container-service
ms.topic: article
ms.date: 8/25/2021
ms.author: juda
ms.openlocfilehash: 98f8fe2e00a7671078da7dae2174401b6c33fd30
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128667609"
---
# <a name="enable-cloud-controller-manager-preview"></a>启用云控制器管理器（预览版）

作为云提供商，Microsoft Azure 与 Kubernetes 社区密切合作，以用户的名义支持我们的基础结构。

目前，Kubernetes 中的云提供商集成是“树内”的，其中对云特定功能的任何更改都必须遵循标准的 Kubernetes 发布周期。  如果我们发现问题、修复问题或需要推出增强功能，必须在 Kubernetes 社区的发布周期内这样做。

Kubernetes 社区现在采用“树外”模型，其中云提供商将通过 [cloud-provider-azure][cloud-provider-azure] 组件独立于核心 Kubernetes 发布计划来控制他们的发布。  我们已经推出了云存储接口 (CSI) 驱动程序，作为 Kubernetes 版本 1.21 及更高版本的默认设置。

> [!Note]
> 在 AKS 群集上启用云控制器管理器时，还将启用树外 CSI 驱动程序。

云控制器管理器将是 Kubernetes 1.22 中的默认控制器，受 AKS 支持。


[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>准备阶段

必须安装了以下资源：

* Azure CLI
* `aks-preview` 扩展 0.5.5 或更高版本
* Kubernetes 版本 1.20.x 或更高版本


### <a name="register-the-enablecloudcontrollermanager-feature-flag"></a>注册 `EnableCloudControllerManager` 功能标志

若要使用云控制器管理器功能，必须在订阅上启用 `EnableCloudControllerManager` 功能标志。 

```azurecli
az feature register --name EnableCloudControllerManager --namespace Microsoft.ContainerService
```
可使用 [az feature list][az-feature-list] 命令来检查注册状态：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableCloudControllerManager')].{Name:name,State:properties.state}"
```

准备就绪后，使用 [az provider register][az-provider-register] 命令刷新 Microsoft.ContainerService 资源提供程序的注册状态：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-cloud-controller-manager"></a>使用云控制器管理器创建 AKS 群集

要使用云控制器管理器创建群集，请使用 Azure CLI 将 `EnableCloudControllerManager=True` 作为客户标头传递给 Azure API。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
az aks create -n aks -g myResourceGroup --aks-custom-headers EnableCloudControllerManager=True
```

## <a name="upgrade-an-aks-cluster-to-cloud-controller-manager"></a>将 AKS 群集升级到云控制器管理器

要升级群集以使用云控制器管理器，请使用 Azure CLI 将 `EnableCloudControllerManager=True` 作为客户标头传递给 Azure API。

```azurecli-interactive
az aks upgrade -n aks -g myResourceGroup --aks-custom-headers EnableCloudControllerManager=True
```

## <a name="next-steps"></a>后续步骤

- 有关 CSI 驱动程序以及 Kubernetes 1.21 以上版本的默认行为的详细信息，请参阅我们的[文档][csi-docs]。

- 如需详细了解有关树外提供程序的 Kubernetes 社区指南，请参阅[社区博客文章][community-blog]。


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[csi-docs]: csi-storage-drivers.md

<!-- LINKS - External -->
[community-blog]: https://kubernetes.io/blog/2019/04/17/the-future-of-cloud-providers-in-kubernetes
[cloud-provider-azure]: https://github.com/kubernetes-sigs/cloud-provider-azure
