---
title: 升级 Azure Kubernetes 服务 (AKS) 节点映像
description: 了解如何升级 AKS 群集节点和节点池上的映像。
ms.service: container-service
ms.topic: conceptual
ms.date: 11/25/2020
ms.author: jpalma
ms.openlocfilehash: 05d957fac23f1fd9d33a44fa74642eb162550880
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751510"
---
# <a name="azure-kubernetes-service-aks-node-image-upgrade"></a>Azure Kubernetes 服务 (AKS) 节点映像升级

AKS 支持升级节点上的映像，以便你能够获取最新的操作系统和运行时更新。 AKS 每周提供一个带有最新更新的新映像，因此，建议定期升级节点的映像以使用最新功能，包括 Linux 或 Windows 补丁。 虽然客户将通过 AKS 发行说明收到映像升级通知，但在所有区域推出更新可能需要一周时间。 本文介绍了在不升级 Kubernetes 版本的情况下如何升级 AKS 群集节点映像以及如何更新节点池映像。

有关 AKS 提供的最新映像的详细信息，请参阅 [AKS 发行说明](https://github.com/Azure/AKS/releases)。

有关如何升级群集的 Kubernetes 版本的信息，请参阅[升级 AKS 群集][upgrade-cluster]。

> [!NOTE]
> AKS 群集必须对节点使用虚拟机规模集。

## <a name="check-if-your-node-pool-is-on-the-latest-node-image"></a>检查节点池是否在最新节点映像上

可通过以下命令查看可供节点池使用的最新节点映像版本： 

```azurecli
az aks nodepool get-upgrades \
    --nodepool-name mynodepool \
    --cluster-name myAKSCluster \
    --resource-group myResourceGroup
```

在输出中，可以看到 `latestNodeImageVersion`，如以下示例所示：

```output
{
  "id": "/subscriptions/XXXX-XXX-XXX-XXX-XXXXX/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/agentPools/nodepool1/upgradeProfiles/default",
  "kubernetesVersion": "1.17.11",
  "latestNodeImageVersion": "AKSUbuntu-1604-2020.10.28",
  "name": "default",
  "osType": "Linux",
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.ContainerService/managedClusters/agentPools/upgradeProfiles",
  "upgrades": null
}
```

因此，对于 `nodepool1`，可用的最新节点映像是 `AKSUbuntu-1604-2020.10.28`。 现在，可以通过运行以下命令将其与节点池所用的当前节点映像版本进行比较：

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --query nodeImageVersion
```

示例输出将是：

```output
"AKSUbuntu-1604-2020.10.08"
```

因此在本例中，可以从当前的 `AKSUbuntu-1604-2020.10.08` 映像版本升级到最新的 `AKSUbuntu-1604-2020.10.28` 版本。 

## <a name="upgrade-all-nodes-in-all-node-pools"></a>升级所有节点池中的所有节点

升级节点映像是使用 `az aks upgrade` 来完成的。 若要升级节点映像，请使用以下命令：

```azurecli
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-image-only
```

在升级过程中，请使用下面的 `kubectl` 命令来检查节点映像的状态，以获取标签并筛选出当前节点映像信息：

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

升级完成后，请使用 `az aks show` 来获取更新后的节点池详细信息。 当前节点映像在 `nodeImageVersion` 属性中显示。

```azurecli
az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster
```

## <a name="upgrade-a-specific-node-pool"></a>升级特定节点池

升级节点池上的映像类似于升级群集上的映像。

若要在不执行 Kubernetes 群集升级的情况下更新节点池的 OS 映像，请使用以下示例中的 `--node-image-only` 选项：

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-image-only
```

在升级过程中，请使用下面的 `kubectl` 命令来检查节点映像的状态，以获取标签并筛选出当前节点映像信息：

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

升级完成后，请使用 `az aks nodepool show` 来获取更新后的节点池详细信息。 当前节点映像在 `nodeImageVersion` 属性中显示。

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="upgrade-node-images-with-node-surge"></a>通过节点激增升级节点映像

要加快节点映像升级过程，可以使用可自定义的节点激增值升级节点映像。 默认情况下，AKS 使用另一个节点来配置升级。

如果要提高升级速度，请使用 `--max-surge` 值来配置要用于升级的节点数，使其更快完成。 要了解有关各种 `--max-surge` 设置的权衡的更多信息，请参阅[自定义节点激增升级][max-surge]。

以下命令设置用于执行节点映像升级的最大激增值：

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --max-surge 33% \
    --node-image-only \
    --no-wait
```

在升级过程中，请使用下面的 `kubectl` 命令来检查节点映像的状态，以获取标签并筛选出当前节点映像信息：

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

使用 `az aks nodepool show` 可获取更新后的节点池详细信息。 当前节点映像在 `nodeImageVersion` 属性中显示。

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="next-steps"></a>后续步骤

- 参阅 [AKS 发行说明](https://github.com/Azure/AKS/releases)以了解有关最新节点映像的信息。
- 通过阅读[升级 AKS 群集][upgrade-cluster]一文来了解如何升级 Kubernetes 版本。
- [使用 GitHub Actions 自动应用群集和节点池升级][github-schedule]
- 通过阅读[创建和管理多个节点池][use-multiple-node-pools]一文来详细了解多个节点池以及如何升级节点池。

<!-- LINKS - internal -->
[upgrade-cluster]: upgrade-cluster.md
[github-schedule]: node-upgrade-github-actions.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
