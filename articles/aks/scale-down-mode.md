---
title: 为 Azure Kubernetes 服务 (AKS) 群集使用纵向缩减模式（预览版）
titleSuffix: Azure Kubernetes Service
description: 了解如何在 Azure Kubernetes 服务 (AKS) 中使用纵向缩减模式。
services: container-service
ms.topic: article
ms.date: 09/01/2021
ms.author: qpetraroia
author: qpetraroia
ms.openlocfilehash: 2d14a624b62e47a7abbbb1718efbe3be350b110a
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123439901"
---
# <a name="use-scale-down-mode-to-deletedeallocate-nodes-in-azure-kubernetes-service-aks-preview"></a>使用纵向缩减模式在 Azure Kubernetes 服务 (AKS) 中删除/解除分配节点（预览版）

默认情况下，通过手动方式或通过群集自动缩放程序执行的纵向扩展操作需要分配和预配新节点，而纵向缩减操作会删除节点。 使用纵向缩减模式，可以决定在执行纵向缩减时，是要在 Azure Kubernetes 服务 (AKS) 群集中删除节点还是解除分配节点。 

如果 Azure VM 处于 `Stopped`（已解除分配）状态，你将不会为 VM 计算资源付费。 但是，仍需要为附加到 VM 的任何 OS 和数据存储磁盘付费。 这也意味着容器映像将保留在这些节点上。 有关详细信息，请参阅 [Azure 虚拟机的状态和计费][state-billing-azure-vm]。 此行为可实现更快的操作速度，因为你的部署利用了缓存的映像。 使用纵向缩减模式，不必再预配节点和预先拉取容器映像，从而节省计算成本。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>开始之前

> [!WARNING]
> 为了保存任何已解除分配的 VM，必须将“纵向缩减”模式设置为“解除分配”。 这包括已使用 IaaS API 解除分配的 VM（虚拟机规模集 API）。 将“纵向缩减”模式设置为“删除”会移除任何解除分配 VM。

本文假定你拥有现有的 AKS 群集。 如果需要 AKS 群集，请参阅 AKS 快速入门[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 门户][aks-quickstart-portal]。

### <a name="limitations"></a>限制

- 不支持[临时 OS][ephemeral-os] 磁盘。 请确保在创建群集或节点池时通过 `--node-osdisk-type Managed` 指定托管 OS 磁盘。
- 不支持[现成节点池][spot-node-pool]。

### <a name="install-aks-preview-cli-extension"></a>安装 aks-preview CLI 扩展

还需要 aks-preview Azure CLI 扩展 0.5.30 或更高版本。 使用 [az extension add][az-extension-add] 命令安装 aks-preview Azure CLI 扩展。 或者使用 [az extension update][az-extension-update] 命令安装任何可用的更新。

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-the-aks-scaledownmodepreview-preview-feature"></a>注册 `AKS-ScaleDownModePreview` 预览版功能

若要使用该功能，还必须在订阅上启用 `AKS-ScaleDownModePreview` 功能标志。

使用 [az feature register][az-feature-register] 命令注册 `AKS-ScaleDownModePreview` 功能标志，如以下示例所示：

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "AKS-ScaleDownModePreview"
```

状态显示为“已注册”需要几分钟时间。 使用 [az feature list][az-feature-list] 命令验证注册状态：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-ScaleDownModePreview')].{Name:name,State:properties.state}"
```

准备就绪后，使用 [az provider register][az-provider-register] 命令刷新 Microsoft.ContainerService 资源提供程序的注册状态：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="using-scale-down-mode-to-deallocate-nodes-on-scale-down"></a>使用纵向缩减模式在纵向缩减时解除分配节点

通过设置 `--scale-down-mode Deallocate`，在纵向缩减群集/节点池时，节点将被解除分配。 所有已解除分配的节点都会停止。 当群集/节点池需要纵向扩展时，将首先启动已解除分配的节点，然后再预配任何新节点。

在此示例中，我们将创建一个包含 20 个节点的新节点池，并指定在纵向缩减时，将通过 `--scale-down-mode Deallocate` 解除分配节点。

```azurecli-interactive
az aks nodepool add --node-count 20 --scale-down-mode Deallocate --node-osdisk-type Managed --max-pods 10 --name nodepool2 --cluster-name myAKSCluster --resource-group myResourceGroup
```

通过缩放节点池并将节点计数更改为 5，我们将解除分配 15 个节点。

```azurecli-interactive
az aks nodepool scale --node-count 5 --name nodepool2 --cluster-name myAKSCluster --resource-group myResourceGroup
```

### <a name="deleting-previously-deallocated-nodes"></a>删除先前已解除分配的节点

若要删除已解除分配的节点，可以通过设置 `--scale-down-mode Delete` 将纵向缩减模式更改为 `Delete`。 此时将删除 15 个已解除分配的节点。

```azurecli-interactive
az aks nodepool update --scale-down-mode Delete --name nodepool2 --cluster-name myAKSCluster --resource-group myResourceGroup
```

## <a name="using-scale-down-mode-to-delete-nodes-on-scale-down"></a>使用纵向缩减模式在纵向缩减时删除节点

在不使用纵向缩减模式的情况下，AKS 的默认行为是在你纵向缩减群集时删除节点。 使用纵向缩减模式，这可以通过设置 `--scale-down-mode Delete` 来显式实现。

在此示例中，我们将创建一个新的节点池，并指定将通过 `--scale-down-mode Delete` 在纵向缩减时删除节点。 缩放操作将通过群集自动缩放程序进行处理。

```azurecli-interactive
az aks nodepool add --enable-cluster-autoscaler --min-count 1 --max-count 10 --max-pods 10 --node-osdisk-type Managed --scale-down-mode Delete --name nodepool3 --cluster-name myAKSCluster --resource-group myResourceGroup
```

## <a name="next-steps"></a>后续步骤

- 若要详细了解如何升级 AKS 群集，请参阅[升级 AKS 群集][aks-upgrade]
- 若要详细了解群集自动缩放程序，请参阅[在 AKS 上自动缩放群集以满足应用程序需求][cluster-autoscaler]

<!-- LINKS - Internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-provider-register]: /cli/azure/provider#az_provider_register
[aks-upgrade]: upgrade-cluster.md
[cluster-autoscaler]: cluster-autoscaler.md
[ephemeral-os]: cluster-configuration.md#ephemeral-os
[state-billing-azure-vm]: ../virtual-machines/states-billing.md
[spot-node-pool]: spot-node-pool.md