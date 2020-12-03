---
title: 升级 Azure Kubernetes 服务 (AKS) 节点映像
description: 了解如何升级 AKS 群集节点和节点池上的映像。
ms.service: container-service
ms.topic: conceptual
ms.date: 11/25/2020
ms.author: jpalma
ms.openlocfilehash: 83d7d48922806334e2b49494fe0ef1d15e1a7a6a
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96531473"
---
# <a name="azure-kubernetes-service-aks-node-image-upgrade"></a>Azure Kubernetes 服务 (AKS) 节点映像升级

AKS 支持升级节点上的映像，以便你能够获取最新的操作系统和运行时更新。 AKS 每周提供一个带有最新更新的新映像，因此，建议定期升级节点的映像以使用最新功能，包括 Linux 或 Windows 补丁。 本文介绍如何升级 AKS 群集节点映像以及如何在不升级 Kubernetes 版本的情况下更新节点池映像。

有关 AKS 提供的最新映像的详细信息，请参阅 [AKS 发行说明](https://github.com/Azure/AKS/releases)。

有关如何升级群集的 Kubernetes 版本的信息，请参阅[升级 AKS 群集][upgrade-cluster]。

> [!NOTE]
> AKS 群集必须对节点使用虚拟机规模集。

## <a name="check-if-your-node-pool-is-on-the-latest-node-image"></a>检查节点池是否在最新节点映像上

可以使用以下命令查看节点池可用的最新节点映像版本： 

```azurecli
az aks nodepool get-upgrades \
    --nodepool-name mynodepool \
    --cluster-name myAKSCluster \
    --resource-group myResourceGroup
```

在输出中，您可以在 `latestNodeImageVersion` 下面的示例中看到类似的内容：

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

因此， `nodepool1` 最新的节点映像是 `AKSUbuntu-1604-2020.10.28` 。 你现在可以通过运行以下内容将其与节点池使用的当前节点映像版本进行比较：

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --query nodeImageVersion
```

示例输出如下所示：

```output
"AKSUbuntu-1604-2020.10.08"
```

因此，在此示例中，可以从当前 `AKSUbuntu-1604-2020.10.08` 映像版本升级到最新版本 `AKSUbuntu-1604-2020.10.28` 。 

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

若要更新节点池的 OS 映像而不执行 Kubernetes 群集升级，请使用 `--node-image-only` 以下示例中的选项：

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

## <a name="upgrade-node-images-with-node-surge"></a>通过节点冲击升级节点映像

若要加快节点映像升级过程，可以使用可自定义的节点浪涌值升级节点映像。 默认情况下，AKS 使用另一个节点来配置升级。

如果要提高升级速度，请使用 `--max-surge` 值来配置要用于升级的节点数，使其更快完成。 若要详细了解各种设置的权衡 `--max-surge` ，请参阅 [自定义节点浪涌升级][max-surge]。

以下命令设置用于执行节点映像升级的最大冲击值：

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

使用 `az aks nodepool show` 获取更新的节点池详细信息。 当前节点映像在 `nodeImageVersion` 属性中显示。

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="next-steps"></a>后续步骤

- 参阅 [AKS 发行说明](https://github.com/Azure/AKS/releases)以了解有关最新节点映像的信息。
- 通过阅读[升级 AKS 群集][upgrade-cluster]一文来了解如何升级 Kubernetes 版本。
- [使用 GitHub 操作自动应用群集和节点池升级][github-schedule]
- 通过阅读[创建和管理多个节点池][use-multiple-node-pools]一文来详细了解多个节点池以及如何升级节点池。

<!-- LINKS - internal -->
[upgrade-cluster]: upgrade-cluster.md
[github-schedule]: node-upgrade-github-actions.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
