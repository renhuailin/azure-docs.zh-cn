---
title: 启动和停止 Azure Kubernetes 服务 (AKS)
description: 了解如何停止或启动 Azure Kubernetes 服务 (AKS) 群集。
services: container-service
ms.topic: article
ms.date: 08/09/2021
author: palma21
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fefdb4619c017d7c43e4dfa84c8099450310ca2f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121743941"
---
# <a name="stop-and-start-an-azure-kubernetes-service-aks-cluster"></a>停止和启动 Azure Kubernetes 服务 (AKS) 群集

AKS 工作负载可能不需要连续运行，例如仅在工作时间使用的开发群集。 这会导致 Azure Kubernetes 服务 (AKS) 群集可能处于空闲状态，运行的时间不超过系统组件的运行时间。 可以通过将[所有 `User` 节点池缩放为 0](scale-cluster.md#scale-user-node-pools-to-0) 来减少群集内存占用量，但当该群集运行时，仍需要 [`System` 池](use-system-pools.md)来运行系统组件。
若要在这段时间内进一步优化成本，可以完全关闭（停止）群集。 此操作将完全停止控制平面和代理节点，使你可以节省所有计算成本，同时维护所有存储的对象和群集状态，以备再次启动时使用。 然后，你可以在周末结束后继续工作，或仅在运行批处理作业时才运行群集。

## <a name="before-you-begin"></a>开始之前

本文假定你拥有现有的 AKS 群集。 如果需要 AKS 群集，请参阅[使用 Azure CLI][aks-quickstart-cli] 的 AKS 快速入门、[使用 Azure PowerShell][kubernetes-walkthrough-powershell] 的 AKS 快速入门或[使用 Azure 门户][aks-quickstart-portal]的 AKS 快速入门。

### <a name="limitations"></a>限制

使用群集启动/停止功能时，存在以下限制：

- 只有虚拟机规模集支持的群集才支持此功能。
- 已停止的 AKS 群集的群集状态将最多保留 12 个月。 如果群集停止超过 12 个月，则无法恢复群集状态。 有关详细信息，请参阅 [AKS 支持策略](support-policies.md)。
- 只能启动或删除已停止的 AKS 群集。 若要执行任何操作（例如缩放或升级），请先启动群集。
- 启动某个已停止的 AKS 群集时，需要删除并重新创建链接到专用群集的客户预配 PrivateEndpoints。

## <a name="stop-an-aks-cluster"></a>停止 AKS 群集

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

可以使用 `az aks stop` 命令停止正在运行的 AKS 群集的节点和控制平面。 以下示例停止名为 myAKSCluster 的群集：

```azurecli-interactive
az aks stop --name myAKSCluster --resource-group myResourceGroup
```

可以使用 [az aks show][az-aks-show] 命令并确认 `powerState` 显示为 `Stopped` 来验证群集停止的时间，如以下输出所示：

```json
{
[...]
  "nodeResourceGroup": "MC_myResourceGroup_myAKSCluster_westus2",
  "powerState":{
    "code":"Stopped"
  },
  "privateFqdn": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
[...]
}
```

如果 `provisioningState` 显示为 `Stopping`，则表明群集尚未完全停止。

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

可以使用 [Stop-AzAksCluster][stop-azakscluster] cmdlet 停止正在运行的 AKS 群集的节点和控制平面。 以下示例停止名为 myAKSCluster 的群集：

```azurepowershell-interactive
Stop-AzAksCluster -Name myAKSCluster -ResourceGroupName myResourceGroup
```

可以使用 [Get-AzAksCluster][get-azakscluster] cmdlet 验证群集是否已停止并确认 `ProvisioningState` 显示为 `Stopped`，如以下输出所示：

```Output
ProvisioningState       : Stopped
MaxAgentPools           : 100
KubernetesVersion       : 1.20.7
...
```

如果 `ProvisioningState` 显示为 `Stopping`，则表明群集尚未完全停止。

---

> [!IMPORTANT]
> 如果使用的是 [Pod 中断预算](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/)，停止操作可能需要更长的时间，因为排出过程将花费更多的时间才能完成。

## <a name="start-an-aks-cluster"></a>启动 AKS 群集

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

可以使用 `az aks start` 命令启动已停止的 AKS 群集的节点和控制平面。 使用先前的控制平面状态和代理节点数重启群集。
以下示例启动名为 myAKSCluster 的群集：

```azurecli-interactive
az aks start --name myAKSCluster --resource-group myResourceGroup
```

可以使用 [az aks show][az-aks-show] 命令并确认 `powerState` 显示为 `Running` 来验证群集启动的时间，如以下输出所示：

```json
{
[...]
  "nodeResourceGroup": "MC_myResourceGroup_myAKSCluster_westus2",
  "powerState":{
    "code":"Running"
  },
  "privateFqdn": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
[...]
}
```

如果 `provisioningState` 显示为 `Starting`，则表明群集尚未完全启动。

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

可以使用 [Start-AzAksCluster][start-azakscluster] cmdlet 启动已停止的 AKS 群集的节点和控制平面。 使用先前的控制平面状态和代理节点数重启群集。
以下示例启动名为 myAKSCluster 的群集：

```azurepowershell-interactive
Start-AzAksCluster -Name myAKSCluster -ResourceGroupName myResourceGroup
```

可以使用 [Get-AzAksCluster][get-azakscluster] cmdlet 验证群集何时启动并确认 `ProvisioningState` 显示 `Succeeded`，如以下输出所示：

```Output
ProvisioningState       : Succeeded
MaxAgentPools           : 100
KubernetesVersion       : 1.20.7
...
```

如果 `ProvisioningState` 显示为 `Starting`，则表明群集尚未完全启动。

---

> [!NOTE]
> 启动群集备份时，预期会出现以下行为：
>
> * API 服务器的 IP 地址可能会更改。
> * 如果使用的是群集自动缩放程序，则在启动群集备份时，当前节点计数可能不在所设置的最小和最大范围值之间。 群集以运行其工作负载所需的节点数（不受自动缩放程序设置影响）开始。 当群集执行缩放操作时，最小值和最大值将影响当前节点计数，并且群集最终将进入并保持在所需范围内，直到停止群集为止。

## <a name="next-steps"></a>后续步骤

- 若要了解如何将 `User` 池缩放为 0，请参阅[将 `User` 池缩放为 0](scale-cluster.md#scale-user-node-pools-to-0)。
- 若要了解如何使用现成 VM 实例节省成本，请参阅[将现成节点池添加到 AKS](spot-node-pool.md)。
- 若要了解有关 AKS 支持策略的详细信息，请参阅 [AKS 支持策略](support-policies.md)。

<!-- LINKS - external -->

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-aks-show]: /cli/azure/aks#az_aks_show
[kubernetes-walkthrough-powershell]: kubernetes-walkthrough-powershell.md
[stop-azakscluster]: /powershell/module/az.aks/stop-azakscluster
[get-azakscluster]: /powershell/module/az.aks/get-azakscluster
[start-azakscluster]: /powershell/module/az.aks/start-azakscluster
