---
title: 在 Azure Kubernetes 服务 (AKS) 中配置 Azure CNI 网络
description: 了解如何在 Azure Kubernetes 服务 (AKS) 中配置 Azure CNI（高级）网络，包括将 AKS 群集部署到现有虚拟网络和子网中。
services: container-service
ms.topic: article
ms.date: 06/03/2019
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: d26459080e57f8998b40c181306ca10508ad4749
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123099220"
---
# <a name="configure-azure-cni-networking-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes 服务 (AKS) 中配置 Azure CNI 网络

默认情况下，AKS 群集使用 [kubenet][kubenet]，系统会为你创建虚拟网络和子网。 使用 *kubenet*，节点从虚拟网络子网获取 IP 地址。 然后会在节点上配置网络地址转换 (NAT)，并且 Pod 将接收“隐藏”在节点 IP 背后的 IP 地址。 这种方法减少了需要在网络空间中保留供 Pod 使用的 IP 地址数量。

借助 [Azure 容器网络接口 (CNI)][cni-networking]，每个 Pod 都可以从子网获得 IP 地址，并且可供直接访问。 这些 IP 地址在网络空间中必须唯一，并且必须事先计划。 每个节点都有一个配置参数来表示它支持的最大 Pod 数。 这样，就会为每个节点预留相应的 IP 地址数。 使用此方法需要经过更详细的规划，并且经常会耗尽 IP 地址，或者在应用程序需求增长时需要在更大的子网中重建群集。

本文展示了如何使用 *Azure CNI* 网络来创建和使用 AKS 群集的虚拟网络子网。 有关网络选项和注意事项的详细信息，请参阅 [Kubernetes 和 AKS 的网络概念][aks-network-concepts]。

## <a name="prerequisites"></a>先决条件

* AKS 群集的虚拟网络必须允许出站 Internet 连接。
* AKS 群集不得将 `169.254.0.0/16`、`172.30.0.0/16`、`172.31.0.0/16` 或 `192.0.2.0/24` 用于 Kubernetes 服务地址范围、Pod 地址范围或群集虚拟网络地址范围。
* AKS 群集使用的群集标识在虚拟网络中的子网上必须至少具有[网络参与者](../role-based-access-control/built-in-roles.md#network-contributor)权限。 如果希望定义[自定义角色](../role-based-access-control/custom-roles.md)而不是使用内置的网络参与者角色，则需要以下权限：
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`
* 分配给 AKS 节点池的子网不能是[委托子网](../virtual-network/subnet-delegation-overview.md)。
* 如果提供自己的子网，则必须管理与该子网关联的网络安全组 (NSG)。 AKS 不会修改与该子网关联的任何 NSG。 还必须确保 NSG 中的安全规则允许节点和 Pod CIDR 范围之间的流量。

## <a name="plan-ip-addressing-for-your-cluster"></a>规划群集的 IP 地址

使用 Azure CNI 网络配置的群集需要额外的规划。 虚拟网络及其子网的大小必须适应计划运行的 Pod 数以及群集的节点数。

Pod 和群集节点的 IP 地址是从虚拟网络中指定的子网分配的。 每个节点都配置了主 IP 地址。 默认情况下，Azure CNI 预先配置了 30 个额外的 IP 地址，这些地址被分配给安排在节点上的 Pod。 横向扩展群集时，将使用子网中的 IP 地址以类似方式配置每个节点。 还可以查看[每个节点的最大 Pod 数](#maximum-pods-per-node)。

> [!IMPORTANT]
> 应在考虑到升级和缩放操作的基础上确定所需的 IP 地址数。 如果设置的 IP 地址范围仅支持固定数量的节点，则无法升级或缩放群集。
>
> * **升级** AKS 群集时，会将一个新节点部署到该群集中。 服务和工作负荷开始在新节点上运行，旧节点将从群集中删除。 这种滚动升级过程要求至少有一个额外的 IP 地址块可用。 那么，节点计数是 `n + 1`。
>   * 使用 Windows Server 节点池时，此注意事项尤其重要。 AKS 中的 Windows Server 节点不会自动应用 Windows 更新，相反，你需要在节点池上执行升级。 此升级使用最新的 Window Server 2019 基本节点映像和安全修补程序部署新节点。 有关升级 Windows Server 节点池的详细信息，请参阅[升级 AKS 中的节点池][nodepool-upgrade]。
>
> * **缩放** AKS 群集时，会将一个新节点部署到该群集中。 服务和工作负荷开始在新节点上运行。 确定 IP 地址范围时需要考虑到如何纵向扩展群集可以支持的节点和 Pod 数目。 此外，应该为升级操作包含一个额外的节点。 那么，节点计数是 `n + number-of-additional-scaled-nodes-you-anticipate + 1`。

如果预期节点将会运行最大数目的 Pod，并且会定期销毁和部署 Pod，则还应该考虑为每个节点分配一些额外的 IP 地址。 分配这些额外的 IP 地址是考虑到删除某个服务以及为了部署新服务并获取地址而释放 IP 地址可能需要几秒钟时间。

AKS 群集 IP 地址计划包括虚拟网络、至少一个节点和 Pod 子网以及 Kubernetes 服务地址范围。

| 地址范围 / Azure 资源 | 限制和调整大小 |
| --------- | ------------- |
| 虚拟网络 | Azure 虚拟网络的大小可以为 /8，但仅限于 65,536 个已配置的 IP 地址。 在配置地址空间之前，请考虑所有网络需求（包括与其他虚拟网络中的服务进行通信）。 例如，如果配置的地址空间太大，则可能会遇到与网络中的其他地址空间重叠的问题。|
| 子网 | 大小必须足以容纳群集中可能预配的节点、Pod 以及所有 Kubernetes 和 Azure 资源。 例如，如果部署内部 Azure 负载均衡器，其前端 IP 分配自群集子网（而不是公共 IP）。 子网大小还应考虑到帐户升级操作或将来的缩放需求。<p />若要计算最小子网大小，包括用于升级操作的其他节点：`(number of nodes + 1) + ((number of nodes + 1) * maximum pods per node that you configure)`<p/>50 个节点群集的示例：`(51) + (51  * 30 (default)) = 1,581`（/21 或更大）<p/>50 节点群集的示例，其中还包括纵向扩展额外 10 个节点的预配：`(61) + (61 * 30 (default)) = 1,891`（/21 或更大）<p>如果在创建群集时没有指定每个节点的最大 Pod 数，则每个节点的最大 Pod 数将设置为 30。 所需的最小 IP 地址数取决于该值。 如果基于不同的最大值计算最小 IP 地址要求，请参阅[如何配置每个节点的最大 Pod 数](#configure-maximum---new-clusters)，以便在部署群集时设置此值。 |
| Kubernetes 服务地址范围 | 此范围不应由此虚拟网络上或连接到此虚拟网络的任何网络元素使用。 服务地址 CIDR 必须小于 /12。 可以在不同 AKS 群集中重复使用此范围。 |
| Kubernetes DNS 服务 IP 地址 | Kubernetes 服务地址范围内的 IP 地址，将由群集服务发现使用。 请勿使用地址范围内的第一个 IP 地址。 子网范围内的第一个地址用于 kubernetes.default.svc.cluster.local 地址。 |
| Docker 桥地址 | Docker 桥网络地址表示所有 Docker 安装中都存在的默认 docker0 桥网络地址。 虽然 AKS 群集或 Pod 本身不使用 docker0 桥，但必须设置此地址以继续支持 AKS 群集内的 docker build 等方案。 需要为 Docker 桥网络地址选择 CIDR，否则 Docker 会自动选择一个可能与其他 CIDR 冲突的子网。 必须选择一个不与网络上其他 CIDR（包括群集的服务 CIDR 和 Pod CIDR）冲突的地址空间。 默认地址为 172.17.0.1/16。 可以在不同 AKS 群集中重复使用此范围。 |

## <a name="maximum-pods-per-node"></a>每个节点的最大 Pod 数

AKS 群集中每个节点的最大 Pod 数为 250。 每个节点的默认最大 Pod 数因 *kubenet* 和 *Azure CNI* 网络以及群集部署方法而异。

| 部署方法 | Kubenet 默认值 | Azure CNI 默认值 | 可在部署时配置 |
| -- | :--: | :--: | -- |
| Azure CLI | 110 | 30 | 是（最大 250） |
| Resource Manager 模板 | 110 | 30 | 是（最大 250） |
| 门户 | 110 | 110（在“节点池”选项卡中配置） | 否 |

### <a name="configure-maximum---new-clusters"></a>配置最大值 - 新群集

可以在群集部署时或在添加新节点池时配置每个节点的最大 Pod 数。 如果使用 Azure CLI 或资源管理器模板进行部署，则可以设置每个节点的最大 Pod 数，最高可以设置为 250。

如果在创建新节点池时未指定 maxPod，则会收到 Azure CNI 的默认值 30。

强制执行每个节点最大 Pod 的最小值，以保证对于群集运行状况而言至关重要的系统 Pod 空间。 当且仅当每个节点池的配置有至少 30 个 Pod 的空间时，可以为每个节点的最大 Pod 数设置的最小值为 10。 例如，如果将每个节点的最大 Pod 数设置为最少 10 个，则要求每个单独的节点池至少有 3 个节点。 此要求也适用于创建的每个新节点池，因此，如果将每个节点的最大 Pod 数定义为 10，则后续添加的每个节点池必须至少有 3 个节点。

| 网络 | 最小值 | 最大值 |
| -- | :--: | :--: |
| Azure CNI | 10 | 250 |
| Kubenet | 10 | 110 |

> [!NOTE]
> 上表中的最小值由 AKS 服务严格强制实施。 不能将 maxPods 值设置为低于所示的最小值，因为这样做可能会阻止群集启动。

* **Azure CLI**：使用 [az aks create][az-aks-create] 命令部署群集时，请指定 `--max-pods` 参数。 最大值为 250。
* **资源管理器模板**：使用资源管理器模板部署群集时，在 [ManagedClusterAgentPoolProfile] 对象中指定 `maxPods` 属性。 最大值为 250。
* **Azure 门户**：使用 Azure 门户部署群集时，不能更改每个节点的最大 Pod 数。 使用 Azure 门户部署时，Azure CNI 网络群集中每个节点的 Pod 数限制为 110 个。

### <a name="configure-maximum---existing-clusters"></a>配置最大值 - 现有群集

创建新节点池时，可以定义“每个节点的 maxPod”设置。 如果需要增加现有群集的“每个节点的 maxPod”设置，请使用新的所需 maxPod 计数添加新的节点池。 将 Pod 迁移到新池后，请删除旧池。 若要删除群集中的任何旧池，请确保按[系统节点池文档][system-node-pools]中的定义设置节点池模式。

## <a name="deployment-parameters"></a>部署参数

创建 AKS 群集时，可为 Azure CNI 网络配置以下参数：

**虚拟网络**：要将 Kubernetes 群集部署到的虚拟网络。 要为群集创建新的虚拟网络，请选择“新建”，并按照“创建虚拟网络”部分中的步骤操作 。 有关 Azure 虚拟网络的限制和配额的信息，请参阅 [Azure 订阅和服务限制、配额和约束](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits)。

**子网**：要将群集部署到的虚拟网络中的子网。 若要在虚拟网络中为群集创建新的子网，请选择“新建”，并按照“创建子网”部分中的步骤操作 。 对于混合连接，地址范围不应与环境中的其他任何虚拟网络重叠。

**Azure 网络插件**：使用 Azure 网络插件时，无法从 clusterCIDR 中具有不属于 AKS 群集的 IP 的 VM 访问“externalTrafficPolicy=Local”的内部 LoadBalancer 服务。

**Kubernetes 服务地址范围**：该参数是 Kubernetes 分配给群集中的内部 [服务][services]的一组虚拟 IP。 可以使用任何专用地址范围，只要其符合以下要求即可：

* 不得在群集的虚拟网络 IP 地址范围内
* 不得与群集虚拟网络对等互连的任何其他虚拟网络重叠
* 不得与任何本地 IP 重叠
* 不得在范围 `169.254.0.0/16`、`172.30.0.0/16`、`172.31.0.0/16` 或 `192.0.2.0/24` 中

虽然从技术上来说可以在群集所在的虚拟网络中指定一个服务地址范围，但建议不要这样做。 如果使用重叠的 IP 范围，则可能导致不可预测的行为。 有关详细信息，请参阅本文中的[常见问题解答](#frequently-asked-questions)部分。 有关 Kubernetes 服务的详细信息，请参阅 Kubernetes 文档中的[服务][services]。

**Kubernetes DNS 服务 IP 地址**：群集的 DNS 服务的 IP 地址。 此地址必须在 Kubernetes 服务地址范围内。 请勿使用地址范围内的第一个 IP 地址。 子网范围内的第一个地址用于 kubernetes.default.svc.cluster.local 地址。

**Docker 桥地址**：Docker 桥网络地址表示所有 Docker 安装中都存在的默认 docker0 桥网络地址。 虽然 AKS 群集或 Pod 本身不使用 docker0 桥，但必须设置此地址以继续支持 AKS 群集内的 docker build 等方案。 需要为 Docker 桥网络地址选择 CIDR，否则 Docker 会自动选择一个可能与其他 CIDR 冲突的子网。 必须选择一个不与网络上其他 CIDR（包括群集的服务 CIDR 和 Pod CIDR）冲突的地址空间。

## <a name="configure-networking---cli"></a>配置网络 - CLI

使用 Azure CLI 创建 AKS 群集时，还可以配置 Azure CNI 网络。 可以使用以下命令在启用了 Azure CNI 网络的情况下创建新的 AKS 群集。

首先，将现有子网的子网资源 ID 加入到 AKS 群集将加入的子网资源 ID：

```azurecli-interactive
$ az network vnet subnet list \
    --resource-group myVnet \
    --vnet-name myVnet \
    --query "[0].id" --output tsv

/subscriptions/<guid>/resourceGroups/myVnet/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/default
```

使用带有 `--network-plugin azure` 参数的 [az aks create][az-aks-create] 命令创建具有高级网络的群集。 使用上一步中收集的子网 ID 更新 `--vnet-subnet-id` 值：

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 \
    --generate-ssh-keys
```

## <a name="configure-networking---portal"></a>配置网络 - 门户

以下 Azure 门户屏幕截图显示了在创建 AKS 群集过程中配置这些设置的示例：

![Azure 门户中的高级网络配置][portal-01-networking-advanced]

## <a name="dynamic-allocation-of-ips-and-enhanced-subnet-support-preview"></a>IP 动态分配和增强版子网支持（预览版）

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

传统 CNI 的一个缺点是，随着 AKS 群集的增长，Pod IP 地址将耗尽，导致需要在更大的子网中重建整个群集。 Azure CNI 中的新增 IP 动态分配功能可从与托管 AKS 群集的子网分隔的子网分配 Pod IP，以此来解决这个问题。 这能带来以下好处：

* **更高的 IP 利用率**：IP 从 Pod 子网动态分配给群集 Pod。 与传统的 CNI 解决方案（为每个节点静态分配 IP）相比，此功能可以优化群集中的 IP 利用率。  

* **可缩放性和灵活性**：可以单独缩放节点和 Pod 子网。 单个 Pod 子网可以在群集的多个节点池之间或在同一 VNet 中部署的多个 AKS 群集之间共享。 你还可以为节点池配置单独的 Pod 子网。  

* **高性能**：由于为 Pod 分配了 VNet IP，因此它们可以直接连接到 VNet 中的其他群集 Pod 和资源。 此解决方案支持非常大的群集，且丝毫不会降低性能。

* **用于 Pod 的单独 VNet 策略**：由于 Pod 具有单独的子网，因此你可以单独为它们配置不同于节点策略的 VNet 策略。 这样可以实现许多有用的场景，例如只允许 Pod 而不允许节点连接 Internet，使用 VNet 网络 NAT 修复节点池中 Pod 的源 IP 以及使用 NSG 筛选节点池之间的流量。  

* **Kubernetes 网络策略**：Azure 网络策略和 Calico 都适用于此新解决方案。  

### <a name="additional-prerequisites"></a>其他先决条件

已列出的适用于 Azure CNI 的[先决条件][prerequisites]仍适用，但还有一些额外的限制：

* 仅支持 Linux 节点群集和节点池。
* 不支持 AKS 引擎和 DIY 群集。

### <a name="install-the-aks-preview-azure-cli"></a>安装 `aks-preview` Azure CLI

需要 aks-preview Azure CLI 扩展。 使用 [az extension add][az-extension-add] 命令安装 aks-preview Azure CLI 扩展。 或者使用 [az extension update][az-extension-update] 命令安装任何可用的更新。

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-the-podsubnetpreview-preview-feature"></a>注册 `PodSubnetPreview` 预览版功能

若要使用该功能，还必须在订阅上启用 `PodSubnetPreview` 功能标志。

使用 [az feature register][az-feature-register] 命令注册 `PodSubnetPreview` 功能标志，如以下示例所示：

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "PodSubnetPreview"
```

状态显示为“已注册”需要几分钟时间。 使用 [az feature list][az-feature-list] 命令验证注册状态：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSubnetPreview')].{Name:name,State:properties.state}"
```

准备就绪后，使用 [az provider register][az-provider-register] 命令刷新 Microsoft.ContainerService 资源提供程序的注册状态：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="planning-ip-addressing"></a>计划 IP 寻址

使用此功能时，计划会简单得多。 由于节点和 Pod 是独立缩放的，因此也可以单独计划其地址空间。 由于可以将 Pod 子网配置为节点池的粒度，因此客户在添加节点池时始终可以添加新的子网。 群集/节点池中的系统 Pod 也会从 Pod 子网中接收 IP，因此需要考虑此行为。

K8S 服务和 Docker 桥的 IP 计划保持不变。

### <a name="maximum-pods-per-node-in-a-cluster-with-dynamic-allocation-of-ips-and-enhanced-subnet-support"></a>具有 IP 动态分配和增强版子网支持的群集中每个节点的最大 Pod 数

使用具有 IP 动态分配功能的 Azure CNI 时，每个节点的 Pod 值与传统的 CNI 行为相比略有变化：

|CNI|部署方法|默认|可在部署时配置|
|--|--| :--: |--|
|传统 Azure CNI|Azure CLI|30|是（最大 250）|
|具有 IP 动态分配功能的 Azure CNI|Azure CLI|250|是（最大 250）|

与配置每个 Pod 的最大节点数相关的所有其他指南保持不变。

### <a name="additional-deployment-parameters"></a>其他部署参数

上面所述的部署参数仍然有效，但有一个例外：

* 现在，subnet 参数是指与群集节点有关的子网。
* 另一个参数 pod subnet 用于指定其 IP 地址将动态分配给 Pod 的子网。

### <a name="configure-networking---cli-with-dynamic-allocation-of-ips-and-enhanced-subnet-support"></a>配置网络 - 具有 IP 动态分配和增强版子网支持的 CLI

在群集中使用 IP 动态分配和增强版子网支持类似于配置群集 Azure CNI 的默认方法。 下面的示例演示如何创建一个新的虚拟网络，其中包含一个用于节点的子网和一个用于 Pod 的子网，以及如何创建一个群集，该群集使用具有 IP 动态分配和增强版子网支持的 Azure CNI。 确保用自己的值替换变量（如 `$subscription`）：

首先创建包含两个子网的虚拟网络：

```azurecli-interactive
resourceGroup="myResourceGroup"
vnet="myVirtualNetwork"
location="westcentralus"

# Create the resource group
az group create --name $resourceGroup --location $location

# Create our two subnet network 
az network vnet create -g $resourceGroup --location $location --name $vnet --address-prefixes 10.0.0.0/8 -o none 
az network vnet subnet create -g $resourceGroup --vnet-name $vnet --name nodesubnet --address-prefixes 10.240.0.0/16 -o none 
az network vnet subnet create -g $resourceGroup --vnet-name $vnet --name podsubnet --address-prefixes 10.241.0.0/16 -o none 
```

然后，创建群集，使用 `--vnet-subnet-id` 引用节点子网，并使用 `--pod-subnet-id` 引用 Pod 子网：

```azurecli-interactive
clusterName="myAKSCluster"
subscription="aaaaaaa-aaaaa-aaaaaa-aaaa"

az aks create -n $clusterName -g $resourceGroup -l $location \
  --max-pods 250 \
  --node-count 2 \
  --network-plugin azure \
  --vnet-subnet-id /subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.Network/virtualNetworks/$vnet/subnets/nodesubnet \
  --pod-subnet-id /subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.Network/virtualNetworks/$vnet/subnets/podsubnet  
```

#### <a name="adding-node-pool"></a>添加节点池

添加节点池时，请使用 `--vnet-subnet-id` 引用节点子网，并使用 `--pod-subnet-id` 引用 Pod 子网。 以下示例创建了两个新子网，然后在创建新节点池时引用它们：

```azurecli-interactive
az network vnet subnet create -g $resourceGroup --vnet-name $vnet --name node2subnet --address-prefixes 10.242.0.0/16 -o none 
az network vnet subnet create -g $resourceGroup --vnet-name $vnet --name pod2subnet --address-prefixes 10.243.0.0/16 -o none 

az aks nodepool add --cluster-name $clusterName -g $resourceGroup  -n newnodepool \
  --max-pods 250 \
  --node-count 2 \
  --vnet-subnet-id /subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.Network/virtualNetworks/$vnet/subnets/node2subnet \
  --pod-subnet-id /subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.Network/virtualNetworks/$vnet/subnets/pod2subnet \
  --no-wait 
```

## <a name="frequently-asked-questions"></a>常见问题

以下问题和解答适用于 **Azure CNI** 网络配置。

* 是否可以在群集子网中部署 VM？

  是的。

* 外部系统查看什么源 IP 来获取源自某个支持 Azure CNI 的 Pod 的流量？

  与 AKS 群集处于同一虚拟网络中的系统将 Pod IP 视为来自 Pod 的任何流量的源地址。 AKS 群集虚拟网络外部的系统将节点 IP 视为来自 Pod 的任何流量的源地址。

* *是否可以配置基于 Pod 的网络策略？*

  是的，Kubernetes 网络策略在 AKS 中可用。 若要开始使用，请参阅[在 AKS 中使用网络策略保护 Pod 之间的流量][network-policy]。

* 可部署到节点的 Pod 数上限是否可配置？

  是的，使用 Azure CLI 或资源管理器模板部署群集时可配置。 请参阅[每个节点的最大 Pod 数](#maximum-pods-per-node)。

  无法在现有群集上更改每个节点的最大 Pod 数。

* 如何配置创建 AKS 群集期间创建的子网的其他属性？例如服务终结点。

  可以在 Azure 门户的标准虚拟网络配置页中，配置创建 AKS 群集期间创建的虚拟网络和子网的完整属性列表。

* 是否可以在我的群集虚拟网络中将另一子网用于 Kubernetes 服务地址范围？

  此配置是可以的，但建议不要这样做。 该服务地址范围是 Kubernetes 分配给群集中的内部服务的虚拟 IP (VIP) 的集合。 Azure 网络无法查看 Kubernetes 群集的服务 IP 范围。 由于无法查看群集的服务地址范围，因此有可能以后会在群集虚拟网络中创建新的子网，该子网与服务地址范围重叠。 如果出现这种形式的重叠，则 Kubernetes 为服务分配的 IP 可能是子网中另一资源正在使用的，导致不可预测的行为或故障。 如果能够确保所用地址范围不在群集的虚拟网络中，则可避免这种重叠风险。

### <a name="dynamic-allocation-of-ip-addresses-and-enhanced-subnet-support-faqs"></a>IP 地址动态分配和增强版子网支持常见问题解答

以下问题和答案适用于使用 IP 地址动态分配和增强版子网支持时的 Azure CNI 网络配置。

* 能否将多个 Pod 子网分配给群集/节点池？

  只能向群集或节点池分配一个子网。 但是，多个群集或节点池可以共享一个子网。

* 能否完全从不同的 VNet 分配 Pod 子网？

  Pod 子网应与群集来自同一 VNet。  

* 能否群集中的一些节点池使用传统的 CNI，而其他节点池使用新的 CNI？

  整个群集只能使用一种类型的 CNI。

## <a name="aks-engine"></a>AKS 引擎

[Azure Kubernetes 服务引擎（AKS 引擎）][aks-engine]是一个开源项目，它能够生成 Azure 资源管理器模板用于在 Azure 上部署 Kubernetes 群集。

使用 AKS 引擎创建的 Kubernetes 群集支持 [kubenet][kubenet] 和 [Azure CNI][cni-networking] 插件。 因此，AKS 引擎同时支持这两种网络方案。

## <a name="next-steps"></a>后续步骤

通过以下文章详细了解 AKS 中的网络：

* [将静态 IP 地址用于 Azure Kubernetes 服务 (AKS) 负载均衡器](static-ip.md)
* [使用包含 Azure 容器服务 (AKS) 的内部负载均衡器](internal-lb.md)

* [创建具有外部网络连接的基本入口控制器][aks-ingress-basic]
* [启用 HTTP 应用程序路由附加产品][aks-http-app-routing]
* [创建使用内部、专用网络和 IP 地址的入口控制器][aks-ingress-internal]
* [使用动态公共 IP 创建入口控制器并配置 Let 's Encrypt 以自动生成 TLS 证书][aks-ingress-tls]
* [使用静态公共 IP 创建入口控制器并配置 Let 's Encrypt 以自动生成 TLS 证书][aks-ingress-static-tls]
<!-- IMAGES -->
[advanced-networking-diagram-01]: ./media/networking-overview/advanced-networking-diagram-01.png [portal-01-networking-advanced]: ./media/networking-overview/portal-01-networking-advanced.png

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[services]: https://kubernetes.io/docs/concepts/services-networking/service/
[portal]: https://portal.azure.com
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[az-aks-create]: /cli/azure/aks#az_aks_create
[aks-ssh]: ssh.md
[ManagedClusterAgentPoolProfile]: /azure/templates/microsoft.containerservice/managedclusters#managedclusteragentpoolprofile-object
[aks-network-concepts]: concepts-network.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-internal]: ingress-internal-ip.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[network-policy]: use-network-policies.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[network-comparisons]: concepts-network.md#compare-network-models
[system-node-pools]: use-system-pools.md
[prerequisites]: configure-azure-cni.md#prerequisites
