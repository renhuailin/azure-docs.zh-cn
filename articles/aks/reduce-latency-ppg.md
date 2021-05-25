---
title: 使用邻近放置组来降低 Azure Kubernetes 服务 (AKS) 群集的延迟
description: 了解如何使用邻近放置组来降低 AKS 群集工作负载的延迟。
services: container-service
manager: gwallace
ms.topic: article
ms.date: 10/19/2020
ms.openlocfilehash: c0c1bf83bf5a816debe61cab0ceab856bfbd062f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102181170"
---
# <a name="reduce-latency-with-proximity-placement-groups"></a>使用邻近放置组降低延迟

> [!Note]
> 在 AKS 上使用邻近放置组时，归置仅适用于代理节点。 节点到节点延迟以及相应的托管 Pod 到 Pod 延迟得到改善。 归置不会影响群集的控制平面的放置。

在 Azure 中部署应用程序时，跨区域或可用性区域分布虚拟机 (VM) 实例会造成网络延迟，这可能会影响应用程序的总体性能。 邻近放置组是一种逻辑分组，用于确保 Azure 计算资源的物理位置彼此接近。 有些应用程序（如游戏、工程模拟和高频交易 (HFT)）需要低延迟和快速完成的任务。 对于这样的高性能计算 (HPC) 场景，请考虑为群集的节点池使用[邻近放置组](../virtual-machines/co-location.md#proximity-placement-groups) (PPG)。

## <a name="before-you-begin"></a>开始之前

本文要求运行 Azure CLI 版本 2.14 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。

### <a name="limitations"></a>限制

* 邻近放置组最多只能映射到一个可用性区域。
* 节点池必须使用虚拟机规模集来关联邻近放置组。
* 节点池只能在节点池创建时关联邻近放置组。

## <a name="node-pools-and-proximity-placement-groups"></a>节点池和邻近放置组

使用邻近放置组部署的第一个资源会附加到特定的数据中心。 使用同一邻近放置组部署的其他资源归置在同一数据中心。 停止（解除分配）或删除使用邻近放置组的所有资源后，邻近放置组将不再附加。

* 多个节点池可以与单个邻近放置组相关联。
* 一个节点池只能与单个邻近放置组相关联。

### <a name="configure-proximity-placement-groups-with-availability-zones"></a>通过可用性区域配置邻近放置组

> [!NOTE]
> 尽管邻近放置组要求一个节点池最多使用一个可用性区域，但对于单个区域中的 VM，[99.9% 的基线 Azure VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/) 仍然有效。

邻近放置组是一个节点池概念，与每个单独的节点池相关联。 使用 PPG 资源对 AKS 控制平面可用性没有影响， 可能会影响群集的区域设计方式。 若要确保群集跨多个区域分布，建议采用以下设计。

* 预配一个群集，其中第一个系统池使用 3 个区域，并且不关联任何邻近放置组。 这可确保系统 Pod 位于专用节点池，该池将分布在多个区域中。
* 添加额外用户节点池，其具有唯一区域和与每个池关联的邻近放置组。 例如区域 1 中的 nodepool1 和 PPG1、区域 2 中的 nodepool2 和 PPG2、区域 3 中的 nodepool3 和 PPG3。 这可确保在群集级别，节点跨多个区域分布，而每个单独的节点池归置于指定的区域中，具有专用的 PPG 资源。

## <a name="create-a-new-aks-cluster-with-a-proximity-placement-group"></a>使用邻近放置组创建新的 AKS 群集

以下示例使用 [az group create][az-group-create] 命令在 centralus 区域中创建名为 myResourceGroup 的资源组 。 然后使用 [az AKS create][az-aks-create] 命令创建名为 *myAKSCluster* 的 AKS 群集。

加速网络可极大地提高虚拟机的网络性能。 理想情况是将邻近放置组与加速网络一起使用。 默认情况下，AKS 在[支持的虚拟机实例](../virtual-network/create-vm-accelerated-networking-cli.md?toc=/azure/virtual-machines/linux/toc.json#limitations-and-constraints)上使用加速网络，其中包括具有两个或多个 vCPU 的大多数 Azure 虚拟机。

创建一个新的 AKS 集群，其中的邻近放置组与第一个系统节点池相关联：

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```
运行以下命令，并存储返回的 ID：

```azurecli-interactive
# Create proximity placement group
az ppg create -n myPPG -g myResourceGroup -l centralus -t standard
```

此命令生成输出，其中包括接下来的 CLI 命令所需的 id 值：

```output
{
  "availabilitySets": null,
  "colocationStatus": null,
  "id": "/subscriptions/yourSubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.Compute/proximityPlacementGroups/myPPG",
  "location": "centralus",
  "name": "myPPG",
  "proximityPlacementGroupType": "Standard",
  "resourceGroup": "myResourceGroup",
  "tags": {},
  "type": "Microsoft.Compute/proximityPlacementGroups",
  "virtualMachineScaleSets": null,
  "virtualMachines": null
}
```

在以下命令中将邻近放置组资源 ID 用作myPPGResourceID 值：

```azurecli-interactive
# Create an AKS cluster that uses a proximity placement group for the initial system node pool only. The PPG has no effect on the cluster control plane.
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --ppg myPPGResourceID
```

## <a name="add-a-proximity-placement-group-to-an-existing-cluster"></a>向现有群集添加邻近放置组

可通过创建一个新节点池来向现有群集添加邻近放置组。 然后，可选择将现有工作负载迁移到新节点池，然后删除原始节点池。

使用之前创建的同一邻近放置组，这将确保 AKS 群集的两个节点池中的代理节点物理上位于同一数据中心。

使用之前创建的邻近放置组中的资源 ID，并使用 [`az aks nodepool add`][az-aks-nodepool-add] 命令添加新的节点池：

```azurecli-interactive
# Add a new node pool that uses a proximity placement group, use a --node-count = 1 for testing
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 1 \
    --ppg myPPGResourceID
```

## <a name="clean-up"></a>清理

若要删除群集，请使用 [`az group delete`][az-group-delete] 命令删除 AKS 资源组：

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>后续步骤

* 详细了解[邻近放置组][proximity-placement-groups]。

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[proximity-placement-groups]: ../virtual-machines/co-location.md#proximity-placement-groups
[az-aks-create]: /cli/azure/aks#az-aks-create
[system-pool]: ./use-system-pools.md
[az-aks-nodepool-add]: /cli/azure/aks/nodepool#az-aks-nodepool-add
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete