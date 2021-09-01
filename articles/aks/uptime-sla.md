---
title: Azure Kubernetes 服务 (AKS) 与运行时间 SLA
description: 了解 Azure Kubernetes 服务 (AKS) API 服务器的可选运行时间 SLA 产品/服务。
services: container-service
ms.topic: conceptual
ms.date: 01/08/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 77bdfb4ed75b9287b911a6b0d2742c235b37e297
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122325070"
---
# <a name="azure-kubernetes-service-aks-uptime-sla"></a>Azure Kubernetes 服务 (AKS) 运行时间 SLA

运行时间 SLA 是一个层，用于为 AKS 群集启用具有财务支持的更高 SLA。 具有运行时间 SLA（也被视为 AKS REST API 中的付费层）的群集附带更多控制平面资源，并自动缩放以满足群集的负载。 对于使用[可用性区域][availability-zones]的群集，运行时间 SLA 可保证 Kubernetes API 服务器终结点 99.95% 的可用性，对于不使用可用性区域的群集，可保证 99.9% 的可用性。 AKS 跨更新域和容错域使用主节点副本，以确保满足 SLA 要求。

AKS 建议在生产工作负载中使用运行时间 SLA，以确保控制平面组件的可用性。 相比之下，免费层上的群集的副本较少且控制平面资源有限，不适用于生产工作负载。

客户仍可以创建服务级别目标 (SLO) 为 99.5% 的无限数量的免费群集，并选择首选 SLO。 

> [!IMPORTANT]
> 对于出口锁定的群集，请参阅[限制出口流量](limit-egress-traffic.md)以打开适当的端口。

## <a name="region-availability"></a>上市区域

* [支持 AKS](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service) 的公共区域和 Azure 政府区域提供运行时间 SLA。
* 在支持 AKS 的所有公共区域中，[专用 AKS 群集][private-clusters]都可以使用运行时间 SLA。

## <a name="sla-terms-and-conditions"></a>SLA 条款和条件

运行时间 SLA 是付费功能，按群集启用。 运行时间 SLA 定价取决于离散群集的数量，而不是各个群集的大小。 有关详细信息，可以查看[运行时间 SLA 定价详细信息](https://azure.microsoft.com/pricing/details/kubernetes-service/)。

## <a name="before-you-begin"></a>开始之前

* 安装 [Azure CLI](/cli/azure/install-azure-cli) 版本 2.8.0 或更高版本

## <a name="creating-a-new-cluster-with-uptime-sla"></a>创建具有运行时间 SLA 的新群集

若要创建具有运行时间 SLA 的群集，请使用 Azure CLI。

以下示例在 eastus 位置创建名为 myResourceGroup 的资源组：  

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location eastus
```

使用 [`az aks create`][az-aks-create] 命令创建 AKS 群集。 以下示例创建一个具有一个节点的名为 myAKSCluster 的群集。 此操作需要几分钟才能完成：

```azurecli-interactive
# Create an AKS cluster with uptime SLA
az aks create --resource-group myResourceGroup --name myAKSCluster --uptime-sla --node-count 1
```

片刻之后，该命令将会完成，并返回有关群集的 JSON 格式信息。 下面的 JSON 片段显示了 SKU 的付费层，表明群集已启用了运行时间 SLA：

```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
```

## <a name="modify-an-existing-cluster-to-use-uptime-sla"></a>修改现有群集以使用运行时间 SLA

可以选择更新现有群集，以使用运行时间 SLA。

如果使用前面的步骤创建了 AKS 群集，请删除资源组：

```azurecli-interactive
# Delete the existing cluster by deleting the resource group 
az group delete --name myResourceGroup --yes --no-wait
```

创建新的资源组：

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location eastus
```

创建新群集，不使用运行时间 SLA：

```azurecli-interactive
# Create a new cluster without uptime SLA
az aks create --resource-group myResourceGroup --name myAKSCluster--node-count 1
```

使用 [`az aks update`][az-aks-update] 命令更新现有群集：

```azurecli-interactive
# Update an existing cluster to use Uptime SLA
az aks update --resource-group myResourceGroup --name myAKSCluster --uptime-sla
```

下面的 JSON 片段显示了 SKU 的付费层，表明群集已启用了运行时间 SLA：

```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
```

## <a name="opt-out-of-uptime-sla"></a>选择退出运行时间 SLA

可更新群集以更改为免费层，并选择退出运行时间 SLA。

```azurecli-interactive
# Update an existing cluster to opt out of Uptime SLA
 az aks update --resource-group myResourceGroup --name myAKSCluster --no-uptime-sla
```

## <a name="clean-up"></a>清理

若要避免收费，请清理创建的任何资源。 若要删除群集，请使用 [`az group delete`][az-group-delete] 命令删除 AKS 资源组：

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>后续步骤

使用[可用性区域][availability-zones]增加 AKS 群集工作负载的高可用性。

将群集配置为[限制出口流量](limit-egress-traffic.md)。

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[faq]: ./faq.md
[availability-zones]: ./availability-zones.md
[az-aks-create]: /cli/azure/aks?#az_aks_create
[limit-egress-traffic]: ./limit-egress-traffic.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-aks-update]: /cli/azure/aks#az_aks_update
[az-group-delete]: /cli/azure/group#az_group_delete
[private-clusters]: private-clusters.md
