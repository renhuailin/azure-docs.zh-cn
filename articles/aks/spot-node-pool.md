---
title: 将现成节点池添加到 Azure Kubernetes 服务 (AKS) 群集
description: 了解如何将现成节点池添加到 Azure Kubernetes 服务 (AKS) 群集。
services: container-service
ms.service: container-service
ms.topic: article
ms.date: 10/19/2020
ms.openlocfilehash: c943db2577de8fc1e4adc0e9cfbf408bffb5f2c6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128585365"
---
# <a name="add-a-spot-node-pool-to-an-azure-kubernetes-service-aks-cluster"></a>将现成节点池添加到 Azure Kubernetes 服务 (AKS) 群集

现成节点池是[现成虚拟机规模集][vmss-spot]支持的节点池。 将节点的现成 VM 与 AKS 群集结合使用，可以利用 Azure 中未使用的容量并显著节省成本。 可用的未用容量因很多因素而异，包括节点大小、区域、时间等。

部署现成节点池时，如果有可用的容量，Azure 将分配现成节点。 但现成节点没有 SLA。 支持现成节点池的现成规模集部署在单个容错域中，不提供高可用性保证。 只要 Azure 需要恢复容量，Azure 基础结构就会逐出现成节点。

现成节点非常适用于可处理中断、提前终止或逐出的工作负载。 例如，诸如批处理作业、开发和测试环境等工作负载以及大型计算工作负载都是计划在现成节点池上运行的候选项。

本文介绍如何将辅助现成节点池添加到现有 Azure Kubernetes 服务 (AKS) 群集中。

本文假设读者基本了解 Kubernetes 和 Azure 负载均衡器的概念。 有关详细信息，请参阅 [Azure Kubernetes 服务 (AKS) 的 Kubernetes 核心概念][kubernetes-concepts]。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="before-you-begin"></a>准备阶段

当你创建群集以使用现成节点池时，该群集还必须将虚拟机规模集用于节点池和标准 SKU 负载均衡器。 创建群集后，还必须添加更多节点池，才能使用现成节点池。 后面的步骤将介绍如何添加更多节点池。

本文要求运行 Azure CLI 版本 2.14 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。

### <a name="limitations"></a>限制

创建和管理具有现成节点池的 AKS 群集时存在以下限制：

* 现成节点池不能是群集的默认节点池。 现成节点池只能用于辅助池。
* 无法升级现成节点池，因为现成节点池无法保证隔离和排空。 必须将现有的现成节点池替换为新的现成节点池，以执行升级 Kubernetes 版本等操作。 若要替换现成节点池，请使用不同版本的 Kubernetes 创建新的现成节点池，等到其状态变为“就绪”，然后删除旧节点池。
* 不能同时升级控制平面和节点池。 两者必须单独升级，或者删除现成节点池以同时升级控制平面和剩余节点池。
* 现成节点池必须使用虚拟机规模集。
* 创建后，不能更改 ScaleSetPriority 或 SpotMaxPrice。
* 设置 SpotMaxPrice 时，值必须为 -1，或者为小数点后最多五位的正值。
* 现成节点池具有标签 kubernetes.azure.com/scalesetpriority:spot、排斥 kubernetes.azure.com/scalesetpriority=spot:NoSchedule，且系统 pod 将具有反相关性。
* 必须添加[相应容许][spot-toleration]以在现成节点池中计划工作负载。

## <a name="add-a-spot-node-pool-to-an-aks-cluster"></a>将现成节点池添加到 AKS 群集

必须将现成节点池添加到已启用多个节点池的现有群集。 有关创建具有多个节点池的 AKS 群集的详细信息，请参阅[此处][use-multiple-node-pools]。

使用 [az aks nodepool add][az-aks-nodepool-add] 创建节点池。
```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name spotnodepool \
    --priority Spot \
    --eviction-policy Delete \
    --spot-max-price -1 \
    --enable-cluster-autoscaler \
    --min-count 1 \
    --max-count 3 \
    --no-wait
```

默认情况下，创建具有多个节点池的群集时，将在 AKS 群集中创建“priority”为“Regular”的节点池。  上述命令将辅助节点池添加到“priority”为“Spot”的现有 AKS 群集。  如果“priority”为“Spot” ，则节点池将成为现成节点池。 在上述示例中，“eviction-policy”参数设置为默认值“Delete”。 将[逐出策略][eviction-policy]设置为“Delete”时，节点池的底层规模集中的节点会在被逐出时删除。 还可以将逐出策略设置为“Deallocate”。 将逐出策略设置为“Deallocate”时，底层规模集中的节点会在逐出时设置为 stopped-deallocated 状态。 处于 stopped-deallocated 状态的节点会根据计算配额进行计数，可能会导致群集缩放或升级问题。 “priority”和“eviction-policy”值只能在创建节点池时设置。 以后不能更新这些值。

该命令还将启用[群集自动缩放程序][cluster-autoscaler]，此程序建议用于现成节点池。 根据群集中运行的工作负载，群集自动缩放程序将纵向扩展和缩减节点池中的节点数。 对于现成节点池，如果仍需要更多节点，则群集自动缩放程序将在逐出之后纵向扩展节点数。 如果更改节点池可以具有的最大节点数，还需要调整与群集自动缩放程序关联的 `maxCount` 值。 如果不使用群集自动缩放程序，则在逐出时，现成池最终将减小到零，且需要手动操作才能接收任何额外的现成节点。

> [!Important]
> 仅在可处理中断的现成节点池上计划工作负载，如批处理作业和测试环境。 建议在现成节点池上设置[排斥和容许][taints-tolerations]，以确保仅在现成节点池上计划可处理节点逐出的工作负载。 例如，上述命令默认情况下会添加排斥 kubernetes.azure.com/scalesetpriority=spot:NoSchedule，因此在此节点上仅计划具有相应容许的 pod。

## <a name="verify-the-spot-node-pool"></a>验证现成节点池

若要验证是否已将节点池添加为现成节点池，请执行以下命令：

```azurecli
az aks nodepool show --resource-group myResourceGroup --cluster-name myAKSCluster --name spotnodepool
```

确认 scaleSetPriority 为“Spot”。

若要计划 pod 在现成节点上运行，请添加与应用到现成节点的排斥相对应的容许。 以下示例介绍了 yaml 文件的一部分，该部分定义了与上一步中使用的 kubernetes.azure.com/scalesetpriority=spot:NoSchedule 排斥相对应的容许。

```yaml
spec:
  containers:
  - name: spot-example
  tolerations:
  - key: "kubernetes.azure.com/scalesetpriority"
    operator: "Equal"
    value: "spot"
    effect: "NoSchedule"
   ...
```

部署具有此容许的 pod 后，Kubernetes 可以成功地在应用了排斥的节点上计划 pod。

## <a name="max-price-for-a-spot-pool"></a>现成池的最高价格
[现成实例的定价根据区域和 SKU 而有所浮动][pricing-spot]。 有关详细信息，请参阅针对 [Linux][pricing-linux] 和 [Windows][pricing-windows] 的定价。

使用可变定价，你可以设置最高价格，以美元 (USD) 为单位，最多可使用 5 个小数位。 例如，值 0.98765 表示最高价格为 0.98765 美元/小时。 如果将最高价格设置为 -1，则不会根据价格逐出实例。 实例的价格将是现成的当前价格或标准实例的价格，以较低者为准，前提是有可用的容量和配额。

## <a name="next-steps"></a>后续步骤

本文介绍了如何将现成节点池添加到 AKS 群集。 有关如何跨节点池控制 pod 的详细信息，请参阅[有关 AKS 中的高级计划程序功能的最佳做法][operator-best-practices-advanced-scheduler]。

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - Internal -->
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-nodepool-add]: /cli/azure/aks/nodepool#az_aks_nodepool_add
[cluster-autoscaler]: cluster-autoscaler.md
[eviction-policy]: ../virtual-machine-scale-sets/use-spot.md#eviction-policy
[kubernetes-concepts]: concepts-clusters-workloads.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[pricing-linux]: https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/
[pricing-spot]: ../virtual-machine-scale-sets/use-spot.md#pricing
[pricing-windows]: https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/
[spot-toleration]: #verify-the-spot-node-pool
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[use-multiple-node-pools]: use-multiple-node-pools.md
[vmss-spot]: ../virtual-machine-scale-sets/use-spot.md
