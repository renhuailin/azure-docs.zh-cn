---
title: 使用虚拟网络的方案
description: 将容器组部署到 Azure 虚拟网络的方案、资源和限制。
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 4d56ba43480182077acc114200ebc69569835bca
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121742465"
---
# <a name="virtual-network-scenarios-and-resources"></a>虚拟网络方案和资源

[Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)为 Azure 资源和本地资源提供安全的专用网络。 将容器组部署到 Azure 虚拟网络后，容器可与该虚拟网络中的其他资源安全通信。 

本文提供有关虚拟网络方案、限制和资源的背景知识。 有关使用 Azure CLI 的部署示例，请参阅[将容器实例部署到 Azure 虚拟网络](container-instances-vnet.md)。

> [!IMPORTANT]
> 虚拟网络中的容器组部署通常适用于大多数同时可使用 Azure 容器实例的区域中的 Linux 容器。 有关详细信息，请参阅[区域和资源可用性](container-instances-region-availability.md)。 

## <a name="scenarios"></a>方案

部署到 Azure 虚拟网络的容器组可实现如下所述的方案：

* 在同一子网中的容器组之间直接通信
* 将容器实例的[基于任务](container-instances-restart-policy.md)的工作负荷输出发送到虚拟网络中的数据库
* 从虚拟网络中的[服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)检索容器实例的内容
* 通过 [VPN 网关](../vpn-gateway/vpn-gateway-about-vpngateways.md)或 [ExpressRoute](../expressroute/expressroute-introduction.md) 实现容器与本地资源的通信
* 与 [Azure 防火墙](../firewall/overview.md)集成，以确定源自容器的出站流量 
* 通过内部 Azure DNS 解析名称，以便与虚拟网络中的 Azure 资源（例如虚拟机）通信
* 使用 NSG 规则控制容器对子网或其他网络资源的访问

## <a name="unsupported-networking-scenarios"></a>不支持的网络方案 

* **Azure 负载均衡器** - 不支持在网络容器组中将 Azure 负载均衡器置于容器实例之前
* **全球虚拟网络对等互连** - 不支持全球对等互连（跨 Azure 区域连接虚拟网络）
* **公共 IP 或 DNS 标签** - 部署到虚拟网络的容器组目前不支持使用公共 IP 地址或完全限定的域名直接向 Internet 公开容器
* **虚拟网络 NAT** - 部署到虚拟网络的容器组目前不支持使用 NAT 网关资源进行出站 Internet 连接。

## <a name="other-limitations"></a>其他限制

* 目前，部署到虚拟网络的容器组仅支持 Linux 容器。
* 若要将容器组部署到某个子网，该子网不能包含其他资源类型。 将容器组部署到现有子网之前，请从该子网中删除所有现有资源，或创建新的子网。
* 不能在部署到虚拟网络的容器组中使用[托管标识](container-instances-managed-identity.md)。
* 不能在部署到虚拟网络的容器组中启用[运行情况探测](container-instances-liveness-probe.md)或[就绪情况探测](container-instances-readiness-probe.md)。
* 由于涉及到其他网络资源，部署到虚拟网络通常比部署标准容器实例要慢。
* 目前不支持到端口 25 的出站连接。
* 如果要将容器组连接到 Azure 存储帐户，则必须向该资源添加[服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)。
* 目前不支持 [IPv6 地址](../virtual-network/ipv6-overview.md)。 

## <a name="required-network-resources"></a>所需的网络资源

将容器组部署到虚拟网络需要三个 Azure 虚拟网络资源：[虚拟网络](#virtual-network)本身、虚拟网络中[委托的子网](#subnet-delegated)，以及[网络配置文件](#network-profile)。 

### <a name="virtual-network"></a>虚拟网络

虚拟网络定义要在其中创建一个或多个子网的地址空间。 然后，可将 Azure 资源（例如容器组）部署到虚拟网络中的子网。

### <a name="subnet-delegated"></a>子网（委托）

子网将部署资源的虚拟网络分段成可由 Azure 资源使用的独立地址空间。 在一个虚拟网络中创建一个或多个子网。

用于容器组的子网只能包含容器组。 首次将容器组部署到某个子网时，Azure 会将该子网委托给 Azure 容器实例。 委托后，该子网只可用于容器组。 如果尝试将除容器组以外的资源部署到委派的子网，该操作将会失败。

### <a name="network-profile"></a>网络配置文件

网络配置文件是 Azure 资源的网络配置模板。 它指定资源的某些网络属性，例如，该资源应部署到哪个子网。 首次使用 [az container create][az-container-create] 命令将容器组部署到某个子网（并因此部署到虚拟网络）时，Azure 会自动创建一个网络配置文件。 以后在部署到该子网时，可以使用该网络配置文件。 

若要使用资源管理器模板、YAML 文件或编程方法将容器组部署到子网，则需要提供网络配置文件的完整资源管理器资源 ID。 可以使用以前使用 [az container create][az-container-create] 创建的配置文件，也可以使用资源管理器模板创建配置文件（请参阅[模板示例](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.containerinstance/aci-vnet)和[参考](/azure/templates/microsoft.network/networkprofiles)）。 若要获取以前创建的配置文件的 ID，请使用 [az network profile list][az-network-profile-list] 命令。 

在下图中，有多个容器组已部署到委派给 Azure 容器实例的子网。 将一个容器组部署到某个子网后，可以通过指定相同的网络配置文件，将其他容器组部署到该子网。

![虚拟网络中的容器组][aci-vnet-01]

## <a name="next-steps"></a>后续步骤

* 有关使用 Azure CLI 的部署示例，请参阅[将容器实例部署到 Azure 虚拟网络](container-instances-vnet.md)。
* 若要使用资源管理器模板部署新的虚拟网络、子网、网络配置文件和容器组，请参阅 [Create an Azure container group with VNet](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.containerinstance/aci-vnet
)（使用 VNet 创建 Azure 容器组）。
* 使用 [Azure 门户](container-instances-quickstart-portal.md)创建容器实例时，还可以在“网络”选项卡上为新的或现有的虚拟网络提供设置。


<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-virtual-network-concepts/aci-vnet-01.png

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-network-profile-list]: /cli/azure/network/profile#az_network_profile_list
