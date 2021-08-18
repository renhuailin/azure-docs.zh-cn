---
title: Azure Kubernetes 服务 (AKS) 中的 API 服务器已授权 IP 范围
description: 了解如何使用用于访问 Azure Kubernetes 服务 (AKS) 中 API 服务器的 IP 地址范围保护群集
services: container-service
ms.topic: article
ms.date: 09/21/2020
ms.openlocfilehash: cf65fcfe297cfbff60490b165771095e6d3b402a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733653"
---
# <a name="secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>使用 Azure Kubernetes 服务 (AKS) 中的已授权 IP 地址范围保护对 API 服务器的访问

在 Kubernetes 中，API 服务器接收在群集中执行操作的请求，例如，创建资源或缩放节点数目。 API 服务器是与群集交互和管理群集的中心位置。 为了提高群集安全性并尽量减少遭到的攻击，只应从有限的一组 IP 地址范围访问 API 服务器。

本文介绍如何使用 API 服务器已授权 IP 地址范围来限制哪些 IP 地址和 CIDR 可以访问控制平面。

## <a name="before-you-begin"></a>准备阶段

本文介绍如何使用 Azure CLI 创建 AKS 群集。

需要安装并配置 Azure CLI 2.0.76 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][install-azure-cli]。

### <a name="limitations"></a>限制

API 服务器授权的 IP 范围功能具有以下限制：
- 在将 API 服务器授权的 IP 地址范围移出 2019 年 10 月的预览后所创建的群集上，仅标准 SKU 负载均衡器支持 API 服务器授权的 IP 地址范围。 配置了基本 SKU 负载均衡器和 API 服务器已授权 IP 地址范围的现有群集将继续按原有方式工作，但不能迁移到标准 SKU 负载均衡器 。 即使 Kubernetes 版本或控制平面升级后，这些现有群集也会继续工作。 专用群集不支持 API 服务器授权 IP 地址范围。
- 此功能与[对每个节点使用公共 IP](use-multiple-node-pools.md#assign-a-public-ip-per-node-for-your-node-pools) 的群集不兼容。

## <a name="overview-of-api-server-authorized-ip-ranges"></a>API 服务器已授权 IP 范围的概述

Kubernetes API 服务器用于公开基础 Kubernetes API。 此组件为管理工具（如 `kubectl` 或 Kubernetes 仪表板）提供交互。 AKS 提供单租户群集控制平面和专用 API 服务器。 默认将为 API 服务器分配一个公共 IP 地址，你应使用 Kubernetes 基于角色的访问控制 (Kubernetes RBAC) 或 Azure RBAC 来控制访问。

若要保护对其他可公开访问的 AKS 控制平面/API 服务器的访问，可以启用并使用已授权 IP 范围。 这些已授权 IP 范围仅允许定义的 IP 地址范围与 API 服务器通信。 从不属于这些授权 IP 范围的 IP 地址向 API 服务器发出的请求被阻止。 请继续使用 Kubernetes RBAC 或 Azure RBAC 来授权用户及其请求的操作。

有关 API 服务器和其他群集组件的详细信息，请参阅 [AKS 的 Kubernetes 核心概念][concepts-clusters-workloads]。

## <a name="create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled"></a>创建启用 API 服务器已授权 IP 范围的 AKS 群集

使用 [az aks create][az-aks-create] 创建群集，并指定 `--api-server-authorized-ip-ranges` 参数提供已授权 IP 地址范围的列表。 这些 IP 地址范围通常是本地网络或公共 IP 使用的地址范围。 指定 CIDR 范围时，请先指定该范围内的第一个 IP 地址。 例如，*137.117.106.90/29* 是有效范围，但请确保指定该范围内的第一个 IP 地址，如 *137.117.106.88/29*。

> [!IMPORTANT]
> 群集默认使用可用于配置出站网关的[标准 SKU 负载均衡器][standard-sku-lb]。 在创建群集期间启用 API 服务器已授权 IP 范围时，除了允许指定的范围以外，默认还允许群集的公共 IP。 对于 `--api-server-authorized-ip-ranges`，如果指定 *""* 或不指定任何值，API 服务器授权的 IP 范围被禁用。 请注意，如果使用的是 PowerShell，请使用 `--api-server-authorized-ip-ranges=""`（带等于号）来避免任何解析问题。

以下示例在名为 *myResourceGroup* 的资源组中，创建名为 *myAKSCluster*、已启用 API 服务器已授权 IP 范围的单节点群集。 允许的 IP 地址范围为 *73.140.245.0/24*：

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 73.140.245.0/24 \
    --generate-ssh-keys
```

> [!NOTE]
> 应将以下范围添加到允许列表：
> - 防火墙公共 IP 地址
> - 代表你要从中管理群集的网络的任何范围
>
> 可指定的 IP 范围数的上限为 200。
>
> 规则最多可能需要 2 分钟才生效。 在测试连接时，请等待相应时长的时间。

### <a name="specify-the-outbound-ips-for-the-standard-sku-load-balancer"></a>指定标准 SKU 负载均衡器的出站 IP

创建 AKS 群集时，如果为群集指定出站 IP 地址或前缀，则也会允许这些地址或前缀。 例如：

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 73.140.245.0/24 \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2> \
    --generate-ssh-keys
```

在上面的示例中，允许参数 *`--load-balancer-outbound-ip-prefixes`* 中提供的所有 IP 以及 *`--api-server-authorized-ip-ranges`* 参数中的 IP。

相反，可以指定 `--load-balancer-outbound-ip-prefixes` 参数以允许出站负载均衡器 IP 前缀。

### <a name="allow-only-the-outbound-public-ip-of-the-standard-sku-load-balancer"></a>仅允许标准 SKU 负载均衡器的出站公共 IP

在创建群集期间启用 API 服务器已授权 IP 范围时，除了允许指定的范围以外，默认还允许群集的标准 SKU 负载均衡器的出站公共 IP。 若要仅允许标准 SKU 负载均衡器的出站公共 IP，请在指定 `--api-server-authorized-ip-ranges` 参数时使用 0.0.0.0/32。

在以下示例中，仅允许标准 SKU 负载均衡器的出站公共 IP，你只能从群集中的节点访问 API 服务器。

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 0.0.0.0/32 \
    --generate-ssh-keys
```

## <a name="update-a-clusters-api-server-authorized-ip-ranges"></a>更新群集的 API 服务器已授权 IP 范围

要在现有群集上更新 API 服务器授权的 IP 范围，请使用 [ az aks update][az-aks-update]命令并使用 `--api-server-authorized-ip-ranges`、--load-balancer-outbound-ip-prefixes *、`--load-balancer-outbound-ips` 或 --load-balancer-outbound-ip-prefixes* 参数。

以下示例更新名为 *myResourceGroup* 的资源组中名为 *myAKSCluster* 的群集上的 API 服务器已授权 IP 范围。 要授权的 IP 地址范围为 *73.140.245.0/24*：

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges  73.140.245.0/24
```

在指定 `--api-server-authorized-ip-ranges` 参数以仅允许标准 SKU 负载平衡器的公共 IP 时，你也可以使用 0.0.0.0/32 。

## <a name="disable-authorized-ip-ranges"></a>禁用已授权 IP 范围

若要禁用 API 服务器已授权 IP 范围，请使用 [az aks update][az-aks-update] 并指定一个空范围。 例如：

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="find-existing-authorized-ip-ranges"></a>查找现有的已授权 IP 范围

若要查找已经过授权的 IP 范围，请使用 [az aks show][az-aks-show]，并指定群集的名称和资源组。 例如：

```azurecli-interactive
az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query apiServerAccessProfile.authorizedIpRanges
```

## <a name="update-disable-and-find-authorized-ip-ranges-using-azure-portal"></a>使用 Azure 门户来更新、禁用和查找已授权 IP 范围

还可以在 Azure 门户中执行上述添加、更新、查找和禁用已授权 IP 范围的操作。 若要访问，请在群集资源的菜单边栏选项卡中导航到“设置”下的“网络” 。

:::image type="content" source="media/api-server-authorized-ip-ranges/ip-ranges-specified.PNG" alt-text="在浏览器中，显示群集资源的网络设置 Azure 门户页面。选项“设置指定的 IP 范围”和“指定的 IP 范围”已突出显示。":::

## <a name="how-to-find-my-ip-to-include-in---api-server-authorized-ip-ranges"></a>如何找到要包含在 `--api-server-authorized-ip-ranges` 中的 IP？

必须将开发计算机、工具或自动化 IP 地址添加到 AKS 群集的已批准 IP 范围列表，以便从该处访问 API 服务器。 

另一种做法是在防火墙虚拟网络中的单独子网内，使用所需的工具配置 Jumpbox。 这假设环境有具有相应网络的防火墙，并且你已将防火墙 IP 添加到授权的范围。 同样，如果已强制建立从 AKS 子网到防火墙子网的隧道，那么也可以在群集子网中设置 jumpbox。

使用以下命令将另一个 IP 地址添加到已批准范围。

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)
# Add to AKS approved list
az aks update -g $RG -n $AKSNAME --api-server-authorized-ip-ranges $CURRENT_IP/32
```

> [!NOTE]
> 上面的示例在群集上追加了 API 服务器已授权 IP 范围。 若要禁用已授权 IP 范围，请使用“az aks update”并指定空范围 ""。 

另一种方法是在 Windows 系统上使用以下命令来获取公共 IPv4 地址，也可以使用[查找 IP 地址](https://support.microsoft.com/en-gb/help/4026518/windows-10-find-your-ip-address)中的步骤。

```azurepowershell-interactive
Invoke-RestMethod http://ipinfo.io/json | Select -exp ip
```

也可以通过在 Internet 浏览器中搜索“我的 IP 地址是什么”来找到此地址。

## <a name="next-steps"></a>后续步骤

在本文中，你启用了 API 服务器已授权 IP 范围。 这是运行安全 AKS 群集的方法之一。

有关详细信息，请参阅 [AKS 中应用程序和群集的 安全性概念][concepts-security]以及[有关 AKS 中群集安全性和升级的最佳做法][operator-best-practices-cluster-security]。

<!-- LINKS - external -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[dev-spaces-ranges]: /previous-versions/azure/dev-spaces/#aks-cluster-network-requirements
[kubenet]: https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/#kubenet

<!-- LINKS - internal -->
[az-aks-update]: /cli/azure/aks#az_aks_update
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-network-public-ip-list]: /cli/azure/network/public-ip#az_network_public_ip_list
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[route-tables]: ../virtual-network/manage-route-table.md
[standard-sku-lb]: load-balancer-standard.md
