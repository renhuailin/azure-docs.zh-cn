---
title: Azure 上的 Kubernetes 教程 - 升级群集
description: 此 Azure Kubernetes 服务 (AKS) 教程介绍如何将现有 AKS 群集升级到最新可用的 Kubernetes 版本。
services: container-service
ms.topic: tutorial
ms.date: 05/24/2021
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 4751081d628f3ea1bff411b5e391377e8b771939
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2021
ms.locfileid: "110697754"
---
# <a name="tutorial-upgrade-kubernetes-in-azure-kubernetes-service-aks"></a>教程：在 Azure Kubernetes 服务 (AKS) 中升级 Kubernetes

在应用程序和群集生命周期中，你可能希望升级到最新可用的 Kubernetes 版本并使用新功能。 可使用 Azure CLI 升级 Azure Kubernetes 服务 (AKS) 群集。

在本教程的第 7 部分中，升级了 Kubernetes 群集。 你将学习如何执行以下操作：

> [!div class="checklist"]
> * 确定 Kubernetes 的当前版本和可用版本
> * 升级 Kubernetes 节点
> * 验证升级是否成功

## <a name="before-you-begin"></a>开始之前

上一教程中，应用程序已打包到容器映像中。 该映像已上传到 Azure容器注册表，同时，你创建了 AKS 群集。 然后，应用程序部署到了 AKS 群集。 如果尚未完成这些步骤，并且想要逐一完成，请先阅读[教程 1 – 创建容器映像][aks-tutorial-prepare-app]。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

本教程需要运行 Azure CLI 2.0.53 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

此教程需要运行 Azure PowerShell 5.9.0 版或更高版本。 运行 `Get-InstalledModule -Name Az` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure PowerShell][azure-powershell-install]。

---

## <a name="get-available-cluster-versions"></a>获取可用的群集版本

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

在升级群集之前，请使用 [az aks get-upgrades][] 命令检查哪些 Kubernetes 版本可用于升级：

```azurecli
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

在以下示例中，当前版本为 1.18.10，可用版本将显示在“升级”下 。

```json
{
  "agentPoolProfiles": null,
  "controlPlaneProfile": {
    "kubernetesVersion": "1.18.10",
    ...
    "upgrades": [
      {
        "isPreview": null,
        "kubernetesVersion": "1.19.1"
      },
      {
        "isPreview": null,
        "kubernetesVersion": "1.19.3"
      }
    ]
  },
  ...
}
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

在升级群集之前，请使用 [Get-AzAksCluster][get-azakscluster] cmdlet 确定正在运行的 Kubernetes 版本及其所在的区域：

```azurepowershell
Get-AzAksCluster -ResourceGroupName myResourceGroup -Name myAKSCluster |
  Select-Object -Property Name, KubernetesVersion, Location
```

在下面的示例中，当前版本是 1.19.9：

```output
Name         KubernetesVersion Location
----         ----------------- --------
myAKSCluster 1.19.9            eastus
```

使用 [Get-AzAksVersion][get-azaksversion] cmdlet 确定哪些 Kubernetes 升级版本在 AKS 群集所在的区域中是可用的：

```azurepowershell
Get-AzAksVersion -Location eastus | Where-Object OrchestratorVersion -gt 1.19.9
```

可用版本显示在 OrchestratorVersion 下。

```output
OrchestratorType    : Kubernetes
OrchestratorVersion : 1.20.2
DefaultProperty     :
IsPreview           :
Upgrades            : {Microsoft.Azure.Commands.Aks.Models.PSOrchestratorProfile}

OrchestratorType    : Kubernetes
OrchestratorVersion : 1.20.5
DefaultProperty     :
IsPreview           :
Upgrades            : {}
```

---

## <a name="upgrade-a-cluster"></a>升级群集

为尽量减少对正在运行的应用程序造成中断，AKS 节点已进行仔细隔离和排空。 在此过程中，执行以下步骤：

1. Kubernetes 计划程序阻止在要升级的节点上计划其他 Pod。
1. 计划在群集中的其他节点上运行该节点上运行的 Pod。
1. 创建运行最新 Kubernetes 组件的节点。
1. 准备好新节点并将其加入群集后，Kubernetes 计划程序开始在该群集上运行 Pod。
1. 删除旧节点，群集的下一个节点随即开始隔离和排空进程。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用 [az aks upgrade][] 命令升级 AKS 群集。

```azurecli
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version KUBERNETES_VERSION
```

> [!NOTE]
> 一次只能升级一个次要版本。 例如，可以从 1.14.x 升级到 1.15.x，但不能从 1.14.x 直接升级到 1.16.x   。 若要从 1.14.x 升级到 1.16.x，请先从 1.14.x 升级到 1.15.x，然后再执行一次升级从 1.15.x 升级到 1.16.x     。

以下精简示例输出显示升级到 1.19.1 的结果。 请注意，kubernetesVersion 现报告 1.19.1 ：

```json
{
  "agentPoolProfiles": [
    {
      "count": 3,
      "maxPods": 110,
      "name": "nodepool1",
      "osType": "Linux",
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS1_v2",
    }
  ],
  "dnsPrefix": "myAKSClust-myResourceGroup-19da35",
  "enableRbac": false,
  "fqdn": "myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io",
  "id": "/subscriptions/<Subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "kubernetesVersion": "1.19.1",
  "location": "eastus",
  "name": "myAKSCluster",
  "type": "Microsoft.ContainerService/ManagedClusters"
}
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

使用 [AzAksCluster][set-azakscluster] cmdlet 升级 AKS 群集。

```azurepowershell
Set-AzAksCluster -ResourceGroupName myResourceGroup -Name myAKSCluster -KubernetesVersion <KUBERNETES_VERSION>
```

> [!NOTE]
> 一次只能升级一个次要版本。 例如，可以从 1.14.x 升级到 1.15.x，但不能从 1.14.x 直接升级到 1.16.x   。 若要从 1.14.x 升级到 1.16.x，请先从 1.14.x 升级到 1.15.x，然后再执行一次升级从 1.15.x 升级到 1.16.x     。

以下精简示例输出显示升级到 1.19.9 的结果。 请注意，kubernetesVersion 现报告为 1.20.2 ：

```output
ProvisioningState       : Succeeded
MaxAgentPools           : 100
KubernetesVersion       : 1.20.2
PrivateFQDN             :
AgentPoolProfiles       : {default}
Name                    : myAKSCluster
Type                    : Microsoft.ContainerService/ManagedClusters
Location                : eastus
Tags                    : {}
```

---

## <a name="validate-an-upgrade"></a>验证升级

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用 [az aks show][] 命令确认升级成功，如下所示：

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

以下示例输出显示 AKS 群集运行 KubernetesVersion 1.19.1：

```output
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.19.1               Succeeded            myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

使用 [Get-AzAksCluster][get-azakscluster] cmdlet 确认升级成功，如​​下所示：

```azurepowershell
Get-AzAksCluster -ResourceGroupName myResourceGroup -Name myAKSCluster |
  Select-Object -Property Name, Location, KubernetesVersion, ProvisioningState
```

以下示例输出显示 AKS 群集运行 KubernetesVersion 1.20.2：

```output
Name         Location KubernetesVersion ProvisioningState
----         -------- ----------------- -----------------
myAKSCluster eastus   1.20.2            Succeeded
```

---

## <a name="delete-the-cluster"></a>删除群集

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

由于此教程是该系列的最后一部分，因此可能需要删除 AKS 群集。 当 Kubernetes 节点在 Azure 虚拟机 (VM) 上运行时，即使你不使用群集，它们也会继续产生费用。 可以使用 [az group delete][az-group-delete] 命令删除资源组、容器服务及所有相关资源。

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```
### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

由于此教程是该系列的最后一部分，因此可能需要删除 AKS 群集。 当 Kubernetes 节点在 Azure 虚拟机 (VM) 上运行时，即使你不使用群集，它们也会继续产生费用。 使用 [Remove-AzResourceGroup][remove-azresourcegroup] cmdlet 删除资源组、容器服务及所有相关资源。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

---

> [!NOTE]
> 删除群集时，AKS 群集使用的 Azure Active Directory 服务主体不会被删除。 有关如何删除服务主体的步骤，请参阅 [AKS 服务主体的注意事项和删除][sp-delete]。 如果你使用了托管标识，则该标识由平台托管，不需要你预配或轮换任何机密。

## <a name="next-steps"></a>后续步骤

在本教程中，在 AKS 群集中升级了 Kubernetes。 你已了解如何执行以下操作：

> [!div class="checklist"]
> * 确定 Kubernetes 的当前版本和可用版本
> * 升级 Kubernetes 节点
> * 验证升级是否成功

有关 AKS 的详细信息，请参阅 [AKS 概述][aks-intro]。 有关使用 AKS 创建完整解决方案的指南，请参阅 [AKS 解决方案指南][aks-solution-guidance]。

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-intro]: ./intro-kubernetes.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az aks show]: /cli/azure/aks#az_aks_show
[az aks get-upgrades]: /cli/azure/aks#az_aks_get_upgrades
[az aks upgrade]: /cli/azure/aks#az_aks_upgrade
[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-delete]: /cli/azure/group#az_group_delete
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[aks-solution-guidance]: /azure/architecture/reference-architectures/containers/aks-start-here?WT.mc_id=AKSDOCSPAGE
[azure-powershell-install]: /powershell/azure/install-az-ps
[get-azakscluster]: /powershell/module/az.aks/get-azakscluster
[get-azaksversion]: /powershell/module/az.aks/get-azaksversion
[set-azakscluster]: /powershell/module/az.aks/set-azakscluster
[remove-azresourcegroup]: /powershell/module/az.resources/remove-azresourcegroup
