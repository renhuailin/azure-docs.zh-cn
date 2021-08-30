---
title: 升级 Azure Kubernetes 服务 (AKS) 群集
description: 了解如何升级 Azure Kubernetes 服务 (AKS) 群集以获取最新的功能和安全更新。
services: container-service
ms.topic: article
ms.date: 12/17/2020
ms.openlocfilehash: 9dcbe21ccddb13037cbd7633127880320da9ff2b
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121860867"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>升级 Azure Kubernetes 服务 (AKS) 群集

AKS 群集生命周期的一部分涉及到定期升级到最新的 Kubernetes 版本。 必须应用最新的安全版本，或者通过升级来获取最新功能。 本文演示如何在 AKS 群集中升级主组件或单个默认的节点池。

对于使用多个节点池或 Windows Server 节点的 AKS 群集，请参阅[升级 AKS 中的节点池][nodepool-upgrade]。

## <a name="before-you-begin"></a>准备阶段

本文要求运行 Azure CLI 2.0.65 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。

> [!WARNING]
> AKS 群集升级会触发节点的隔离和排空。 如果可用计算配额较低，则升级可能会失败。 有关详细信息，请参阅[增加配额](../azure-portal/supportability/resource-manager-core-quotas-request.md)

## <a name="check-for-available-aks-cluster-upgrades"></a>检查是否有可用的 AKS 群集升级

若要检查哪些 Kubernetes 版本可用于群集，请使用 [az aks get-upgrades][az-aks-get-upgrades] 命令。 下面的示例会检查 myResourceGroup 中到 myAKSCluster 的可用升级 ：

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> 升级受支持的 AKS 群集时，不能跳过 Kubernetes 次要版本。 所有升级都必须按主版本号依次执行。 例如，允许 1.14.x -> 1.15.x 或 1.15.x -> 1.16.x 之间进行升级，但不允许 1.14.x -> 1.16.x 之间的升级     。 
> > 仅当从不受支持的版本升级回受支持的版本时，才可以跳过多个版本 。 例如，可从不受支持的 1.10.x 升级到受支持的 1.15.x 。

以下示例输出表明，群集可升级到版本 1.19.1 和 1.19.3 ：

```console
Name     ResourceGroup    MasterVersion    Upgrades
-------  ---------------  ---------------  --------------
default  myResourceGroup  1.18.10          1.19.1, 1.19.3
```

如果没有可用的升级，你将收到消息：

```console
ERROR: Table output unavailable. Use the --query option to specify an appropriate query. Use --debug for more info.
```

## <a name="customize-node-surge-upgrade"></a>自定义节点激增升级

> [!Important]
> 节点激增需要的订阅配额对应于为每个升级操作请求的最大激增计数。 例如，具有 5 个节点池（每个池有 4 个节点）的群集总共包含 20 个节点。 如果每个节点池的最大激增值为 50%，则额外需要 10 个节点（2 个节点/池 * 5 个池）的计算和 IP 配额才能完成升级。
>
> 如果使用 Azure CNI，则还需要验证子网中是否有可用 IP 来[满足 Azure CNI 的 IP 要求](configure-azure-cni.md)。

默认情况下，AKS 会将升级配置为使用一个额外节点进行激增。 最大激增设置的默认值为 1，这样会使 AKS 能够在隔离/排出现有应用程序之前创建一个额外的节点来替换旧版本的节点，从而最大限度地减少工作负荷中断。 可以为每个节点池自定义最大激增值，以便在升级速度与升级中断之间进行权衡。 通过增大最大激增值，升级过程可以更快地完成，但是，设置大的最大激增值可能会导致升级过程中出现中断的情况。 

例如，最大激增值为 100% 时，可以让升级过程尽可能快（将节点计数加倍），但也会导致节点池中的所有节点同时被耗尽。 对于测试环境，你可能希望使用这样的较高的值。 对于生产节点池，建议将 max_surge 设置为 33%。

AKS 可以接受整数值和百分比值作为最大激增值。 整数值，例如 5，指示要激增五个额外的节点。 值为“50%”指示激增值为池中当前节点计数的一半。 最大激增百分比值的最小值可以为 1%，最大值可以为 100%。 百分比值将向上舍入到最近的节点计数。 如果最大激增值低于升级时的当前节点计数，则使用当前节点计数作为最大激增值。

在升级过程中，最大激增值最小可以为 1，最大为节点池中的节点数。 你可以设置较大的值，但用于最大激增的最大节点数不会高于升级时池中的节点数。

> [!Important]
> 节点池的最大激增设置是永久性的。  后续的 Kubernetes 升级或节点版本升级都将使用此设置。 你可以随时更改节点池的最大激增值。 对于生产节点池，建议将 max-surge 设置为 33%。

请使用以下命令为新的或现有的节点池设置最大激增值。

```azurecli-interactive
# Set max surge for a new node pool
az aks nodepool add -n mynodepool -g MyResourceGroup --cluster-name MyManagedCluster --max-surge 33%
```

```azurecli-interactive
# Update max surge for an existing node pool 
az aks nodepool update -n mynodepool -g MyResourceGroup --cluster-name MyManagedCluster --max-surge 5
```

## <a name="upgrade-an-aks-cluster"></a>升级 AKS 群集

如果有一系列适用于 AKS 群集的版本，则可使用 [az aks upgrade][az-aks-upgrade] 命令进行升级。 在升级过程中，AKS 将： 
- 向运行指定 Kubernetes 版本的群集添加一个新的缓冲区节点（或 [max surge](#customize-node-surge-upgrade) 中配置的节点数）。 
- [隔离并排空][kubernetes-drain]其中一个旧节点，以最大程度地减少对正在运行的应用程序的干扰（如果你使用的是最大浪涌，它会同时[隔离并排空][kubernetes-drain]与指定的缓冲区节点数相同的节点数）。 
- 旧节点在完全排空时，会被重置映像以接收新版本，并且会成为下一个要升级的节点的缓冲区节点。 
- 此过程会重复进行，直至群集中的所有节点都已升级完毕。 
- 在此过程结束时，将删除上一个缓冲区节点，从而维持现有的代理节点计数和区域均衡。

```azurecli-interactive
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version KUBERNETES_VERSION
```

升级群集需要几分钟时间，具体取决于有多少节点。

> [!IMPORTANT]
> 确保任何 `PodDisruptionBudgets` (PDB) 都允许一次至少移动 1 个 Pod 副本，否则排空/逐出操作将失败。
> 如果排空操作失败，则根据设计，升级操作将失败，以确保应用程序不会中断。 请消除导致操作停止的原因（PDB 错误、缺少配额等），然后重试该操作。

若要确认升级是否成功，请使用 [az aks show][az-aks-show] 命令：

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

以下示例输出表明群集现在运行 1.18.10：

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.18.10              Succeeded            myakscluster-dns-379cbbb9.hcp.eastus.azmk8s.io
```

## <a name="set-auto-upgrade-channel"></a>设置自动升级通道

除了手动升级群集之外，还可以在群集上设置自动升级通道。 有以下升级通道可用：

|通道| 操作 | 示例
|---|---|---|
| `none`| 禁用自动升级并使群集保持其当前版本的 Kubernetes| 如果保持不变，则为默认设置|
| `patch`| 当有最新的受支持补丁版本可用时，将群集自动升级到该版本，同时使次要版本保持不变。| 例如，如果群集正在运行版本 1.17.7 且版本 1.17.9、1.18.4、1.18.6 和 1.19.1 可用，则你的群集将升级到 1.17.9。|
| `stable`| 自动将群集升级到次要版本 N-1 的最新受支持补丁发行版，其中 N 是最新的受支持次要版本。| 例如，如果群集正在运行版本 1.17.7 且版本 1.17.9、1.18.4、1.18.6 和 1.19.1 可用，则你的群集将升级到 1.18.6。
| `rapid`| 自动将群集升级到最新受支持次要版本的最新受支持补丁发行版。| 如果群集的 Kubernetes 版本的次要版本为 N-2，其中 N 是最新的受支持次要版本，则群集将首先升级到 N-1 次要版本的最新受支持补丁版本。 例如，如果群集正在运行版本 1.17.7 且版本 1.17.9、1.18.4、1.18.6 和 1.19.1 可用，则群集将首先升级到 1.18.6，然后再升级到 1.19.1。
| `node-image`| 自动将节点映像升级到可用的最新版本。| Microsoft 经常（通常每周）为映像节点提供补丁和新映像，但除非升级节点映像，正在运行的节点将不会获得新映像。 当有新版本可用时，打开节点映像通道将自动更新节点映像。 |

> [!NOTE]
> 群集自动升级仅更新到 Kubernetes 的 GA 版，不会更新到预览版。

自动升级群集遵循与手动升级群集相同的过程。 有关更多详细信息，请参阅[升级 AKS 群集][upgrade-cluster]。

AKS 群集的群集自动升级是一项预览功能。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

为 `az cli` 添加以下扩展。

```azurecli-interactive
az extension add --name aks-preview
```

使用 [az feature register][az-feature-register] 命令注册 `AutoUpgradePreview` 功能标志，如以下示例所示：

```azurecli-interactive
az feature register --namespace Microsoft.ContainerService -n AutoUpgradePreview
```

状态显示为“已注册”需要几分钟时间。 使用 [az feature list][az-feature-list] 命令验证注册状态：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AutoUpgradePreview')].{Name:name,State:properties.state}"
```

准备就绪后，使用 [az provider register][az-provider-register] 命令刷新 Microsoft.ContainerService 资源提供程序的注册：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

若要在创建群集时设置自动升级通道，请使用 auto-upgrade-channel 参数，类似于以下示例。

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --auto-upgrade-channel stable --generate-ssh-keys
```

若要在现有群集上设置自动升级通道，请更新 auto-upgrade-channel 参数，类似于以下示例。

```azurecli-interactive
az aks update --resource-group myResourceGroup --name myAKSCluster --auto-upgrade-channel stable
```

## <a name="using-cluster-auto-upgrade-with-planned-maintenance"></a>与计划内维护配合使用群集自动升级

如果同时使用计划内维护和自动升级，则在指定的维护时段内将开始升级。 有关计划内维护的更多详细信息，请参阅 [使用计划内维护为 Azure Kubernetes 服务 (AKS) 群集安排维护时段（预览版）][planned-maintenance]。

## <a name="next-steps"></a>后续步骤

本文演示了如何升级现有的 AKS 群集。 若要详细了解如何部署和管理 AKS 群集，请参阅相关教程系列。

> [!div class="nextstepaction"]
> [AKS 教程][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az_aks_get_upgrades
[az-aks-upgrade]: /cli/azure/aks#az_aks_upgrade
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-provider-register]: /cli/azure/provider#az_provider_register
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[upgrade-cluster]:  #upgrade-an-aks-cluster
[planned-maintenance]: planned-maintenance.md
