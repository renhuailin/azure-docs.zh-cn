---
title: 在 Azure Kubernetes 服务 (AKS) 中使用多个节点池
description: 了解如何为 Azure Kubernetes 服务 (AKS) 中的群集创建和管理多个节点池
services: container-service
ms.topic: article
ms.date: 02/11/2021
ms.openlocfilehash: ef6b23cf7564cff57f398c76162f9c25efac7075
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110081273"
---
# <a name="create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>为 Azure Kubernetes 服务 (AKS) 中的群集创建和管理多个节点池

在 Azure Kubernetes 服务 (AKS) 中，采用相同配置的节点分组成节点池。 这些节点池包含运行应用程序的底层 VM。 节点的初始数量和大小 (SKU) 是创建 AKS 群集时（此时会创建[系统节点池][use-system-pool]）定义的。 若要支持具有不同计算或存储需求的应用程序，可以创建额外的用户节点池。 系统节点池主要用于托管关键系统 Pod（例如 CoreDNS 和 tunnelfront）。 用户节点池主要用于托管应用程序 Pod。 但是，如果希望在 AKS 群集中只有一个池，可以在系统节点池上计划应用程序 Pod。 用户节点池用于放置应用程序特定的 Pod。 例如，使用这些额外的用户节点池可为计算密集型应用程序提供 GPU，或者访问高性能 SSD 存储。

> [!NOTE]
> 利用此功能可以更好地控制如何创建和管理多个节点池。 因此，需要使用单独的命令来执行创建/更新/删除操作。 以前，通过 `az aks create` 或 `az aks update` 执行的群集操作使用 managedCluster API，并且只能通过这些操作更改控制平面和单个节点池。 此功能通过 agentPool API 为代理池公开单独的操作集，并要求使用 `az aks nodepool` 命令集对单个节点池执行操作。

本文介绍如何在 AKS 群集中创建和管理多个节点池。

## <a name="before-you-begin"></a>准备阶段

需要安装并配置 Azure CLI 2.2.0 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][install-azure-cli]。

## <a name="limitations"></a>限制

创建和管理支持多个节点池的 AKS 群集时存在以下限制：

* 请参阅 [Azure Kubernetes 服务 (AKS) 中可用的配额、虚拟机大小限制和区域][quotas-skus-regions]。
* 可以删除系统节点池，前提是有另一个可在 AKS 群集中取代它的系统节点池。
* 系统池必须至少包含一个节点，而用户节点池则可能包含零个或零个以上的节点。
* AKS 群集必须通过标准 SKU 负载均衡器来使用多个节点池，而基本 SKU 负载均衡器并不支持该功能。
* AKS 群集必须对节点使用虚拟机规模集。
* 节点池的名称只能包含小写字母数字字符，且必须以小写字母开头。 Linux 节点池的名称长度必须为 1 到 12 个字符；Windows 节点池的名称长度必须为 1 到 6 个字符。
* 所有节点池都必须位于同一虚拟网络中。
* 在创建群集的过程中创建多个节点池时，节点池使用的所有 Kubernetes 版本都必须与已为控制平面设置的版本相匹配。 这可以在已使用每节点池操作预配了群集后更新。

## <a name="create-an-aks-cluster"></a>创建 AKS 群集

> [!Important]
> 如果在生产环境中为 AKS 群集运行单个系统节点池，则建议至少将三个节点用作节点池。

若要开始，请创建包含单个节点池的 AKS 群集。 以下示例使用 [az group create][az-group-create] 命令在 eastus 区域中创建名为 myResourceGroup 的资源组 。 然后使用 [az AKS create][az-aks-create] 命令创建名为 *myAKSCluster* 的 AKS 群集。

> [!NOTE]
> 使用多个节点池时，**不支持**“基本”负载均衡器 SKU。 默认情况下，AKS 群集是在 Azure CLI 和 Azure 门户中使用“标准”负载均衡器 SKU 创建的。

```azurecli-interactive
# Create a resource group in East US
az group create --name myResourceGroup --location eastus

# Create a basic single-node AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 2 \
    --generate-ssh-keys \
    --load-balancer-sku standard
```

创建群集需要几分钟时间。

> [!NOTE]
> 为了确保群集可靠运行，应在默认节点池中至少运行 2（两）个节点，因为基本系统服务将在整个节点池中运行。

群集准备就绪后，请使用 [az aks get-credentials][az-aks-get-credentials] 命令获取要与 `kubectl` 结合使用的群集凭据：

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>添加节点池

在上一步骤中创建的群集包含单个节点池。 让我们使用 [az aks nodepool add][az-aks-nodepool-add] 命令添加另一个节点池。 以下示例创建名为 *mynodepool* 的节点池，该节点池运行 *3* 个节点：

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3
```

> [!NOTE]
> 节点池的名称必须以小写字母开头，且只能包含字母数字字符。 Linux 节点池的名称长度必须为 1 到 12 个字符；Windows 节点池的名称长度必须为 1 到 6 个字符。

若要查看节点池的状态，请使用 [az aks node pool list][az-aks-nodepool-list] 命令并指定资源组和群集名称：

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster
```

以下示例输出显示已成功地在节点池中创建包含三个节点的 *mynodepool*。 在上一步骤中创建 AKS 群集时，已创建包含 *2* 个节点的默认 *nodepool1*。

```output
[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

> [!TIP]
> 如果在添加节点池时未指定 VmSize ，则 Windows 节点池的默认大小为 Standard_D2s_v3，Linux 节点池的默认大小为 Standard_DS2_v2  。 如果未指定 OrchestratorVersion，则它将默认为与控制平面相同的版本。

### <a name="add-a-node-pool-with-a-unique-subnet-preview"></a>添加一个具有唯一子网的节点池（预览）

某个工作负载可能会要求将群集的节点拆分为单独的池以进行逻辑隔离。 可使用专用于群集中每个节点池的单独子网来支持这种隔离。 这可以满足例如在节点池中拆分非连续的虚拟网络地址空间的要求。

#### <a name="limitations"></a>限制

* 分配给节点池的所有子网都必须属于同一虚拟网络。
* 系统 Pod 必须有权访问群集中的所有节点/pod，以提供关键功能，例如 DNS 解析和隧道 kubectl logs/exec/port-forward 代理。
* 如果在创建群集后扩展 VNET，则必须更新群集（执行任意托管群集操作，但节点池操作不算），然后添加到原始 cidr 之外的子网。 尽管我们最初允许，但 AKS 会在现在添加的代理池上出错。 如果你不知道如何协调群集文件，请提交支持票证。 
* 不支持 Calico 网络策略。 
* 不支持 Azure 网络策略。
* Kube-proxy 应有单个连续 CIDR，并将其用于三个优化。 有关详细信息，请参阅此 [K.E.P.](https://github.com/kubernetes/enhancements/tree/master/keps/sig-network/2450-Remove-knowledge-of-pod-cluster-CIDR-from-iptables-rules) 和[此处](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-proxy/)的 --cluster-cidr。 在 azure cni 中，第一个节点池的子网将被提供给 kube-proxy。 

若要创建具有专用子网的节点池，请在创建节点池时将子网资源 ID 作为附加参数传递。

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3 \
    --vnet-subnet-id <YOUR_SUBNET_RESOURCE_ID>
```

## <a name="upgrade-a-node-pool"></a>升级节点池

> [!NOTE]
> 不能对群集或节点池同时执行升级和缩放操作，否则会返回错误。 而只能先在目标资源上完成一个操作类型，然后再在同一资源上执行下一个请求。 请阅读[故障排除指南](./troubleshooting.md#im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-upgraded-or-has-failed-upgrade)中的详细信息。

本部分中的命令说明如何升级单个特定的节点池。 [下一部分](#upgrade-a-cluster-control-plane-with-multiple-node-pools)将会说明升级控制平面与节点池的 Kubernetes 版本之间的关系。

> [!NOTE]
> 节点池的 OS 映像版本与群集的 Kubernetes 版本相关联。 只能先升级 OS 映像，然后再升级群集。

由于本示例包含两个节点池，因此必须使用 [az aks nodepool upgrade][az-aks-nodepool-upgrade] 来升级节点池。 若要查看可用的升级，请使用 [az aks get-upgrades][az-aks-get-upgrades]

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

现在升级 mynodepool。 如以下示例中所示，使用 [az aks nodepool upgrade][az-aks-nodepool-upgrade] 命令升级节点池：

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version KUBERNETES_VERSION \
    --no-wait
```

使用 [az aks node pool list][az-aks-nodepool-list] 命令再次列出节点池的状态。 以下示例显示“mynodepool”处于“升级”状态，正在升级到 KUBERNETES_VERSION  ：

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "KUBERNETES_VERSION",
    ...
    "provisioningState": "Upgrading",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

将节点升级到指定的版本需要花费几分钟时间。

最佳做法是将 AKS 群集中的所有节点池升级到相同的 Kubernetes 版本。 `az aks upgrade` 的默认行为是将所有节点池连同控制平面一起升级，以实现这种一致性。 升级单个节点池的功能可让你执行滚动升级，并在节点池之间计划 pod，使应用程序的正常运行时间保持在上述约束范围内。

## <a name="upgrade-a-cluster-control-plane-with-multiple-node-pools"></a>升级包含多个节点池的群集控制平面

> [!NOTE]
> Kubernetes 使用标准的[语义化版本控制](https://semver.org/)方案。 版本号以 *x.y.z* 表示，其中，*x* 是主要版本，*y* 是次要版本，*z* 是修补程序版本。 例如，版本 *1.12.6* 中的 1 是主要版本，12 是次要版本，6 是修补程序版本。 控制平面和初始节点池的 Kubernetes 版本是在群集创建过程中设置的。 将所有附加节点池添加到群集时，将为其设置 Kubernetes 版本。 不同节点池的 Kubernetes 版本可能不同，节点池与控制平面的 Kubernetes 版本也可能不同。

AKS 群集包含两个具有关联 Kubernetes 版本的群集资源对象。

1. 群集控制平面 Kubernetes 版本。
2. 具有 Kubernetes 版本的节点池。

一个控制平面映射到一个或多个节点池。 升级操作的行为取决于所用的 Azure CLI 命令。

升级 AKS 控制平面需要使用 `az aks upgrade`。 此命令将升级群集中的控制平面版本和所有节点池。

结合 `--control-plane-only` 标志发出 `az aks upgrade` 命令只会升级群集控制平面， 而不会更改群集中任何关联的节点池。

升级单个节点池需要使用 `az aks nodepool upgrade`。 此命令只会升级具有指定 Kubernetes 版本的目标节点池

### <a name="validation-rules-for-upgrades"></a>升级验证规则

群集控制平面和节点池的有效 Kubernetes 升级由以下规则集验证。

* 用于升级节点池的有效版本的规则：
   * 节点池版本的主要版本必须与控制平面相同。
   * 节点池的次要版本必须在控制平面版本的两个次要版本范围内。 
   * 节点池版本不能大于控制平面的 `major.minor.patch` 版本。

* 提交升级操作的规则：
   * 无法降级控制平面或节点池的 Kubernetes 版本。
   * 如果未指定节点池的 Kubernetes 版本，则行为取决于所用的客户端。 资源管理器模板中的声明会回退到为节点池定义的现有版本（如果已使用）。如果未设置现有版本，则会使用控制平面版本进行回退。
   * 可以在给定的时间升级或者缩放控制平面或节点池，而不能同时对单个控制平面或节点池资源提交多个操作。

## <a name="scale-a-node-pool-manually"></a>手动缩放节点池

当应用程序工作负荷需求发生变化时，你可能需要缩放节点池中的节点数。 可以增加或减少节点数。

<!--If you scale down, nodes are carefully [cordoned and drained][kubernetes-drain] to minimize disruption to running applications.-->

若要缩放节点池中的节点数，请使用 [az aks node pool scale][az-aks-nodepool-scale] 命令。 以下示例将 *mynodepool* 中的节点数缩放为 *5*：

```azurecli-interactive
az aks nodepool scale \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 5 \
    --no-wait
```

使用 [az aks node pool list][az-aks-nodepool-list] 命令再次列出节点池的状态。 以下示例显示 *mynodepool* 处于 *Scaling*（正在缩放）状态，其中的新节点计数为 *5*：

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Scaling",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

需要花费几分钟时间来完成缩放操作。

## <a name="scale-a-specific-node-pool-automatically-by-enabling-the-cluster-autoscaler"></a>通过启用群集自动缩放程序来自动缩放特定节点池

AKS 提供了一项单独的功能，用于通过一项称为[群集自动缩放程序](cluster-autoscaler.md)的功能来自动缩放节点池。 可以为每个节点池启用此功能，每个节点池具有唯一的最小和最大规模计数。 了解如何[对每个节点池使用群集自动缩放程序](cluster-autoscaler.md#use-the-cluster-autoscaler-with-multiple-node-pools-enabled)。

## <a name="delete-a-node-pool"></a>删除节点池

不再需要某个池时，可将其删除并删除底层 VM 节点。 若要删除节点池，请使用 [az aks node pool delete][az-aks-nodepool-delete] 命令并指定节点池名称。 以下示例删除在前面步骤中创建的 *mynoodepool*：

> [!CAUTION]
> 如果删除节点池时发生数据丢失，没有任何选项可以恢复数据。 如果无法在其他节点池中计划 pod，则这些应用程序将不可用。 当使用中的应用程序没有数据备份或者无法在群集中的其他节点池上运行时，请务必不要删除节点池。

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

[az aks node pool list][az-aks-nodepool-list] 命令的以下示例输出显示 *mynodepool* 处于 *Deleting*（正在删除）状态：

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Deleting",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

删除节点和节点池需要花费几分钟时间。

## <a name="specify-a-vm-size-for-a-node-pool"></a>指定节点池的 VM 大小

在前面的创建节点池示例中，对群集中创建的节点使用了默认 VM 大小。 一个更常见的场景是创建具有不同 VM 大小和功能的节点池。 例如，可以创建一个包含具有大量 CPU 或内存的节点的节点池，或创建一个提供 GPU 支持的节点池。 下一步骤将[使用排斥和容许](#setting-nodepool-taints)来告知 Kubernetes 计划程序如何将访问权限限制为可在这些节点上运行的 pod。

在以下示例中，创建使用 Standard_NC6 VM 大小的基于 GPU 的节点池。 这些 VM 采用 NVIDIA Tesla K80 卡。 有关可用 VM 大小的信息，请参阅 [Azure 中的 Linux 虚拟机大小][vm-sizes]。

再次使用 [az aks node pool add][az-aks-nodepool-add] 命令创建节点池。 这一次请指定名称 gpunodepool，并使用 `--node-vm-size` 参数指定 Standard_NC6 大小 ：

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --no-wait
```

[az aks node pool list][az-aks-nodepool-list] 命令的以下示例输出显示 *gpunodepool* 正在创建具有指定 *VmSize* 的节点：

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 1,
    ...
    "name": "gpunodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "vmSize": "Standard_NC6",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

成功创建 *gpunodepool* 需要花费几分钟时间。

## <a name="specify-a-taint-label-or-tag-for-a-node-pool"></a>指定节点池的排斥、标签或标记

创建节点池时，可将排斥、标签或标记添加到该节点池。 添加排斥、标签或标记时，该节点池中的所有节点也会获取该排斥、标签或标记。

> [!IMPORTANT]
> 应该使用 `az aks nodepool` 为整个节点池向节点添加排斥、标签或标记。 不建议使用 `kubectl` 将排斥、标签或标记应用于节点池中的单个节点。  

### <a name="setting-nodepool-taints"></a>设置节点池排斥

若要创建具有排斥的节点池，请使用 [az aks nodepool add][az-aks-nodepool-add]。 指定名称 taintnp，并使用 `--node-taints` 参数为排斥指定 sku=gpu:NoSchedule。 

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name taintnp \
    --node-count 1 \
    --node-taints sku=gpu:NoSchedule \
    --no-wait
```

> [!NOTE]
> 只能在创建节点池期间为节点池设置排斥。

[az aks nodepool list][az-aks-nodepool-list] 命令的以下示例输出显示 taintnp 正在创建具有指定 nodeTaints 的节点：  

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "taintnp",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "nodeTaints":  [
      "sku=gpu:NoSchedule"
    ],
    ...
  },
 ...
]
```

排斥信息将显示在 Kubernetes 中，以便于处理节点的计划规则。 Kubernetes 计划程序能够使用排斥和容许来限制可在节点上运行的工作负荷。

* 将 **排斥** 应用到指明了只能计划特定 pod 的节点。
* 然后，将 **容许** 应用到可以 *容许* 节点排斥的 pod。

有关如何使用 Kubernetes 高级计划功能的详细信息，请参阅[有关 AKS 中的高级计划程序功能的最佳做法][taints-tolerations]

在上一步中，你在创建节点池时应用了 *sku=gpu:NoSchedule* 排斥。 以下基本示例 YAML 清单使用容许来允许 Kubernetes 计划程序在该节点池中的节点上运行 NGINX Pod。

创建名为 `nginx-toleration.yaml` 的文件，并将其复制到以下示例 YAML 中：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: mcr.microsoft.com/oss/nginx/nginx:1.15.9-alpine
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 1
        memory: 2G
  tolerations:
  - key: "sku"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```

使用 `kubectl apply -f nginx-toleration.yaml` 命令计划 pod：

```console
kubectl apply -f nginx-toleration.yaml
```

只需花费几秒钟时间即可计划 pod 并提取 NGINX 映像。 使用 [kubectl describe pod][kubectl-describe] 命令查看 pod 状态。 以下精简示例输出显示已应用 *sku=gpu:NoSchedule* 容许。 在 events 节中，计划程序已将 Pod 分配到 aks-taintnp-28993262-vmss000000 节点：

```console
kubectl describe pod mypod
```

```output
[...]
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
                 sku=gpu:NoSchedule
Events:
  Type    Reason     Age    From                Message
  ----    ------     ----   ----                -------
  Normal  Scheduled  4m48s  default-scheduler   Successfully assigned default/mypod to aks-taintnp-28993262-vmss000000
  Normal  Pulling    4m47s  kubelet             pulling image "mcr.microsoft.com/oss/nginx/nginx:1.15.9-alpine"
  Normal  Pulled     4m43s  kubelet             Successfully pulled image "mcr.microsoft.com/oss/nginx/nginx:1.15.9-alpine"
  Normal  Created    4m40s  kubelet             Created container
  Normal  Started    4m40s  kubelet             Started container
```

只能在 taintnp 中的节点上计划已应用此容许的 Pod。 任何其他 pod 将在 *nodepool1* 节点池中计划。 如果创建额外的节点池，可以使用额外的排斥和容许来限制可在这些节点资源上计划的 pod。

### <a name="setting-nodepool-labels"></a>设置节点池标签

还可以在创建节点池期间向节点池添加标签。 在节点池中设置的标签将添加到节点池中的每个节点。 这些[标签将显示在 Kubernetes 中][kubernetes-labels]，以便于处理节点的计划规则。

若要创建具有标签的节点池，请使用 [az aks nodepool add][az-aks-nodepool-add]。 指定名称 labelnp，并使用 `--labels` 参数为标签指定 dept=IT 和 costcenter=9999。  

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name labelnp \
    --node-count 1 \
    --labels dept=IT costcenter=9999 \
    --no-wait
```

> [!NOTE]
> 只能在创建节点池期间为节点池设置标签。 此外，标签必须是键/值对，并采用[有效的语法][kubernetes-label-syntax]。

[az aks nodepool list][az-aks-nodepool-list] 命令的以下示例输出显示 labelnp 正在创建具有指定 nodeLabels 的节点：  

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "labelnp",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "nodeLabels":  {
      "dept": "IT",
      "costcenter": "9999"
    },
    ...
  },
 ...
]
```

### <a name="setting-nodepool-azure-tags"></a>设置 nodepool Azure 标记

可将 Azure 标记应用到 AKS 群集中的节点池。 应用到某个节点池的标记将应用到该节点池中的每个节点，并通过升级持久保存。 标记还会应用于在横向扩展操作期间添加到节点池的新节点。 添加标记有助于完成策略跟踪或成本估算等任务。

进行操作时（例如，通过搜索密钥来检索标记时），Azure 标记可以使用不区分大小写的密钥。 在这种情况下，将更新或检索带有给定密钥的标记，而不考虑大小写。 标记值区分大小写。

在 AKS 中，如果设置了多个键相同但大小写不同的标记，则使用的标记是第一个（按字母顺序）。 例如，`{"Key1": "val1", "kEy1": "val2", "key1": "val3"}` 会导致设置 `Key1` 和 `val1`。

使用 [az aks nodepool add][az-aks-nodepool-add] 创建节点池。 指定名称 tagnodepool，并使用 `--tag` 参数为标记指定 dept=IT 和 costcenter=9999。  

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name tagnodepool \
    --node-count 1 \
    --tags dept=IT costcenter=9999 \
    --no-wait
```

> [!NOTE]
> 在使用 [az aks nodepool update][az-aks-nodepool-update] 命令时以及在创建群集期间，也可以使用 `--tags` 参数。 在创建群集期间，`--tags` 参数会将标记应用到连同群集一起创建的初始节点池。 所有标记名称必须遵守[使用标记来组织 Azure 资源][tag-limitation]中所述的限制。 使用 `--tags` 参数更新节点池会更新所有现有标记值，并追加任何新标记。 例如，如果节点池对标记使用了 dept=IT 和 costcenter=9999，而你使用标记的 team=dev 和 costcenter=111 更新了该节点池，则该节点池将对标记使用 dept=IT、costcenter=111 和 team=dev。      

[az aks nodepool list][az-aks-nodepool-list] 命令的以下示例输出显示 tagnodepool 正在创建具有指定标记的节点：  

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 1,
    ...
    "name": "tagnodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "tags": {
      "dept": "IT",
      "costcenter": "9999"
    },
    ...
  },
 ...
]
```

## <a name="add-a-fips-enabled-node-pool-preview"></a>添加已启用 FIPS 的节点池（预览版）

美国联邦信息处理标准 (FIPS) 140-2 是美国政府标准，用于定义信息技术产品和系统中加密模块的最低安全要求。 AKS 支持创建已启用 FIPS 140-2 的基于 Linux 的节点池。 在已启用 FIPS 的节点池上运行的部署可以使用这些加密模块来增加安全性，并帮助满足 FedRAMP 合规性中的安全控制要求。 有关 FIPS 140-2 的更多详细信息，请参阅 [美国联邦信息处理标准 (FIPS) 140-2][fips]。

已启用 FIPS 的节点池目前为预览版。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

将需要 *aks-preview* Azure CLI 扩展 *0.5.11* 或更高版本。 使用 [az extension add][az-extension-add] 命令安装 aks-preview Azure CLI 扩展。 或者使用 [az extension update][az-extension-update] 命令安装任何可用的更新。

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

若要使用该功能，还必须在订阅上启用 `FIPSPreview` 功能标志。

使用 [az feature register][az-feature-register] 命令注册 `FIPSPreview` 功能标志，如以下示例所示：

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "FIPSPreview"
```

状态显示为“已注册”需要几分钟时间。 使用 [az feature list][az-feature-list] 命令验证注册状态：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/FIPSPreview')].{Name:name,State:properties.state}"
```

准备就绪后，使用 [az provider register][az-provider-register] 命令刷新 Microsoft.ContainerService 资源提供程序的注册状态：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

已启用 FIPS 的节点池具有下列限制：

* 目前，只能在 Ubuntu 18.04 上运行已启用 FIPS 的基于 Linux 的节点池。
* 已启用 FIPS 的节点池需要 Kubernetes 1.19 及更高版本。
* 若要更新用于 FIPS 的基础包或模块，必须使用 [节点映像升级][node-image-upgrade]。

> [!IMPORTANT]
> 已启用 FIPS 的 Linux 映像与用于基于 Linux 的节点池的默认 Linux 映像不同。 若要在节点池上启用 FIPS，必须创建新的基于 Linux 的节点池。 不能在现有节点池上启用 FIPS。
> 
> 已启用 FIPS 的节点映像的版本号（如内核版本）可能不同于未启用 FIPS 的映像。 此外，已启用 FIPS 的节点池和节点映像的更新周期可能与未启用 FIPS 的节点池和映像不同。

若要创建已启用 FIPS 的节点池，在创建节点池时，请一起使用 [az aks nodepool add][az-aks-nodepool-add] 和“--enable-fips-image”参数。

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name fipsnp \
    --enable-fips-image
```

> [!NOTE]
> 创建群集时，还可以一起使用“--enable-fips-image”参数和 [az aks create][az-aks-create]，以在默认节点池上启用 FIPS。 向以此方式创建的群集添加节点池时，在添加节点池时，仍必须使用“--enable-fips-image”参数，以创建已启用 FIPS 的节点池。

若要验证节点池是否已启用 FIPS，请使用 [az aks show][az-aks-show] 检查“agentPoolProfiles”中的“enableFIPS”值。 

```azurecli-interactive
az aks show --resource-group myResourceGroup --cluster-name myAKSCluster --query="agentPoolProfiles[].{Name:name enableFips:enableFips}" -o table
```

以下示例输出表明“fipsnp”节点池已启用 FIPS 而“nodepool1”未启用。 

```output
Name       enableFips
---------  ------------
fipsnp     True
nodepool1  False  
```

还可以使用 `kubectl debug` 在已启用 FIPS 的节点池中的节点上验证部署是否具有对 FIPS 加密库的访问权限。 使用 `kubectl get nodes` 列出节点：

```output
$ kubectl get nodes
NAME                                STATUS   ROLES   AGE     VERSION
aks-fipsnp-12345678-vmss000000      Ready    agent   6m4s    v1.19.9
aks-fipsnp-12345678-vmss000001      Ready    agent   5m21s   v1.19.9
aks-fipsnp-12345678-vmss000002      Ready    agent   6m8s    v1.19.9
aks-nodepool1-12345678-vmss000000   Ready    agent   34m     v1.19.9
```

在上面的示例中，以 `aks-fipsnp` 开始的节点属于已启用 FIPS 的节点池。 使用 `kubectl debug` 在已启用 FIPS 的节点池中的这些节点之一上以交互会话运行部署。

```azurecli-interactive
kubectl debug node/aks-fipsnp-12345678-vmss000000 -it --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
```

在交互会话中，可以验证是否已启用 FIPS 加密库：

```output
root@aks-fipsnp-12345678-vmss000000:/# cat /proc/sys/crypto/fips_enabled
1
```

已启用 FIPS 的节点池还具有 *kubernetes.azure.com/fips_enabled=true* 标签，可供部署用来锁定这些节点池。

## <a name="manage-node-pools-using-a-resource-manager-template"></a>使用资源管理器模板管理节点池

使用 Azure 资源管理器模板创建和管理资源时，通常可以更新该模板中的设置，然后重新部署以更新资源。 对于 AKS 中的节点池，一旦创建 AKS 群集，就无法更新初始节点池的配置文件。 此行为意味着无法更新现有的资源管理器模板，更改节点池，然后重新部署。 必须创建单独的资源管理器模板来仅更新现有 AKS 群集的节点池。

创建一个模板（例如 `aks-agentpools.json`）并粘贴以下示例清单。 此示例模板配置以下设置：

* 将名为 *myagentpool* 的 *Linux* 节点池更新为运行三个节点。
* 将节点池中的节点设置为运行 Kubernetes 版本 1.15.7。
* 将节点大小定义为 *Standard_DS2_v2*。

根据需要编辑这些值，以更新、添加或删除节点池：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "clusterName": {
            "type": "string",
            "metadata": {
                "description": "The name of your existing AKS cluster."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "The location of your existing AKS cluster."
            }
        },
        "agentPoolName": {
            "type": "string",
            "defaultValue": "myagentpool",
            "metadata": {
                "description": "The name of the agent pool to create or update."
            }
        },
        "vnetSubnetId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The Vnet subnet resource ID for your existing AKS cluster."
            }
        }
    },
    "variables": {
        "apiVersion": {
            "aks": "2020-01-01"
        },
        "agentPoolProfiles": {
            "maxPods": 30,
            "osDiskSizeGB": 0,
            "agentCount": 3,
            "agentVmSize": "Standard_DS2_v2",
            "osType": "Linux",
            "vnetSubnetId": "[parameters('vnetSubnetId')]"
        }
    },
    "resources": [
        {
            "apiVersion": "2020-01-01",
            "type": "Microsoft.ContainerService/managedClusters/agentPools",
            "name": "[concat(parameters('clusterName'),'/', parameters('agentPoolName'))]",
            "location": "[parameters('location')]",
            "properties": {
                "maxPods": "[variables('agentPoolProfiles').maxPods]",
                "osDiskSizeGB": "[variables('agentPoolProfiles').osDiskSizeGB]",
                "count": "[variables('agentPoolProfiles').agentCount]",
                "vmSize": "[variables('agentPoolProfiles').agentVmSize]",
                "osType": "[variables('agentPoolProfiles').osType]",
                "storageProfile": "ManagedDisks",
                "type": "VirtualMachineScaleSets",
                "vnetSubnetID": "[variables('agentPoolProfiles').vnetSubnetId]",
                "orchestratorVersion": "1.15.7"
            }
        }
    ]
}
```

如以下示例中所示，使用 [az deployment group create][az-deployment-group-create] 命令部署此模板。 系统将提示输入现有的 AKS 群集名称和位置：

```azurecli-interactive
az deployment group create \
    --resource-group myResourceGroup \
    --template-file aks-agentpools.json
```

> [!TIP]
> 可以通过在模板中添加 tag 属性，将标记添加到节点池，如以下示例所示。
> 
> ```json
> ...
> "resources": [
> {
>   ...
>   "properties": {
>     ...
>     "tags": {
>       "name1": "val1"
>     },
>     ...
>   }
> }
> ...
> ```

更新 AKS 群集可能需要花费几分钟时间，具体取决于资源管理器模板中定义的节点池设置和操作。

## <a name="assign-a-public-ip-per-node-for-your-node-pools"></a>为节点池的每个节点分配公共 IP

AKS 节点无需使用自身的公共 IP 地址进行通信。 但某些方案可能需要节点池中的节点收到专用公共 IP 地址。 游戏工作负载就是一种常见方案，此时控制台需要直接连接到云虚拟机才能尽量减少画面跳跃。 可通过使用节点公共 IP 在 AKS 上实现此方案。

首先，创建一个新的资源组。

```azurecli-interactive
az group create --name myResourceGroup2 --location eastus
```

创建新的 AKS 群集并为节点附加公共 IP。 节点池中的每个节点都将收到唯一的公共 IP。 可以通过查看虚拟机规模集实例来验证这一点。

```azurecli-interactive
az aks create -g MyResourceGroup2 -n MyManagedCluster -l eastus  --enable-node-public-ip
```

对于现有 AKS 群集，还可以添加新的节点池，并为节点附加公共 IP。

```azurecli-interactive
az aks nodepool add -g MyResourceGroup2 --cluster-name MyManagedCluster -n nodepool2 --enable-node-public-ip
```

### <a name="use-a-public-ip-prefix"></a>使用公共 IP 前缀

[使用公共 IP 前缀有很多好处][public-ip-prefix-benefits]。 在创建新群集或添加节点池时，通过使用标志 `node-public-ip-prefix` 传递资源 ID，AKS 支持使用节点的现有公共 IP 前缀中的地址。

首先，使用 [az network public-ip prefix create][az-public-ip-prefix-create] 创建公共 IP 前缀：

```azurecli-interactive
az network public-ip prefix create --length 28 --location eastus --name MyPublicIPPrefix --resource-group MyResourceGroup3
```

查看输出，并记下前缀的 `id`：

```output
{
  ...
  "id": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup3/providers/Microsoft.Network/publicIPPrefixes/MyPublicIPPrefix",
  ...
}
```

最后，在创建新群集或添加新的节点池时，使用标志 `node-public-ip-prefix` 并传入前缀的资源 ID：

```azurecli-interactive
az aks create -g MyResourceGroup3 -n MyManagedCluster -l eastus --enable-node-public-ip --node-public-ip-prefix /subscriptions/<subscription-id>/resourcegroups/MyResourceGroup3/providers/Microsoft.Network/publicIPPrefixes/MyPublicIPPrefix
```

### <a name="locate-public-ips-for-nodes"></a>找到节点的公共 IP

可以通过多种方式找到节点的公共 IP：

* 使用 Azure CLI 命令 [az vmss list-instance-public-ips][az-list-ips]。
* 使用 [PowerShell 或 Bash 命令][vmss-commands]。 
* 还可以通过在 Azure 门户中查看虚拟机规模集中的实例来查看公共 IP。

> [!Important]
> [节点资源组][node-resource-group]包含节点及其公共 IP。 执行命令时，使用节点资源组查找节点的公共 IP。

```azurecli
az vmss list-instance-public-ips -g MC_MyResourceGroup2_MyManagedCluster_eastus -n YourVirtualMachineScaleSetName
```

## <a name="clean-up-resources"></a>清理资源

在本文中，你已创建包含基于 GPU 的节点的 AKS 群集。 为了减少不必要的费用，我们建议删除 *gpunodepool* 或整个 AKS 群集。

若要删除基于 GPU 的节点池，请如以下示例中所示使用 [az aks nodepool delete][az-aks-nodepool-delete] 命令：

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

若要删除群集本身，请使用 [az group delete][az-group-delete] 命令删除 AKS 资源组：

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

还可以删除为“节点池的公共 IP”方案创建的其他群集。

```azurecli-interactive
az group delete --name myResourceGroup2 --yes --no-wait
```

## <a name="next-steps"></a>后续步骤

详细了解[系统节点池][use-system-pool]。

本文已介绍如何在 AKS 群集中创建和管理多个节点池。 有关如何跨节点池控制 pod 的详细信息，请参阅[有关 AKS 中的高级计划程序功能的最佳做法][operator-best-practices-advanced-scheduler]。

要创建和使用 Windows Server 容器节点池，请参阅[在 AKS 中创建 Windows Server 容器][aks-windows]。

使用[邻近放置组][reduce-latency-ppg]来降低 AKS 应用程序的延迟。

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubernetes-labels]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
[kubernetes-label-syntax]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set

<!-- INTERNAL LINKS -->
[aks-windows]: windows-container-cli.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest&preserve-view=true#az_aks_get_credentials
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest&preserve-view=true#az_aks_create
[az-aks-get-upgrades]: /cli/azure/aks?view=azure-cli-latest&preserve-view=true#az_aks_get_upgrades
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest&preserve-view=true#az_aks_nodepool_add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool?view=azure-cli-latest&preserve-view=true#az_aks_nodepool_list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool?view=azure-cli-latest&preserve-view=true#az_aks_nodepool_update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool?view=azure-cli-latest&preserve-view=true#az_aks_nodepool_upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool?view=azure-cli-latest&preserve-view=true#az_aks_nodepool_scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool?view=azure-cli-latest&preserve-view=true#az_aks_nodepool_delete
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-extension-add]: /cli/azure/extension?view=azure-cli-latest&preserve-view=true#az_extension_add
[az-extension-update]: /cli/azure/extension?view=azure-cli-latest&preserve-view=true#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-group-create]: /cli/azure/group?view=azure-cli-latest&preserve-view=true#az_group_create
[az-group-delete]: /cli/azure/group?view=azure-cli-latest&preserve-view=true#az_group_delete
[az-deployment-group-create]: /cli/azure/deployment/group?view=azure-cli-latest&preserve-view=true#az_deployment_group_create
[gpu-cluster]: gpu-cluster.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[quotas-skus-regions]: quotas-skus-regions.md
[supported-versions]: supported-kubernetes-versions.md
[tag-limitation]: ../azure-resource-manager/management/tag-resources.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/sizes.md
[use-system-pool]: use-system-pools.md
[ip-limitations]: ../virtual-network/virtual-network-ip-addresses-overview-arm#standard
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[vmss-commands]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine
[az-list-ips]: /cli/azure/vmss?view=azure-cli-latest&preserve-view=true#az_vmss_list_instance_public_ips
[reduce-latency-ppg]: reduce-latency-ppg.md
[public-ip-prefix-benefits]: ../virtual-network/public-ip-address-prefix.md#why-create-a-public-ip-address-prefix
[az-public-ip-prefix-create]: /cli/azure/network/public-ip/prefix?view=azure-cli-latest&preserve-view=true#az_network_public_ip_prefix_create
[node-image-upgrade]: node-image-upgrade.md
[fips]: /azure/compliance/offerings/offering-fips-140-2
