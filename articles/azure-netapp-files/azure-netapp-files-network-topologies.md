---
title: Azure NetApp 文件网络规划指南 | Microsoft Docs
description: 介绍可帮助你使用 Azure NetApp 文件设计有效网络体系结构的指南。
services: azure-netapp-files
documentationcenter: ''
author: ram-kakani
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/04/2021
ms.author: ramakk
ms.openlocfilehash: 09f09b5c50dd04f39f95405df9875d458a258b25
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129545956"
---
# <a name="guidelines-for-azure-netapp-files-network-planning"></a>Azure NetApp 文件网络规划指南

网络体系结构规划是设计任何应用程序基础结构的关键要素。 本文可帮助你设计有效的网络体系结构，使你的工作负荷能够受益于 Azure NetApp 文件丰富的功能。

Azure NetApp 文件卷设计为包含在 Azure 虚拟网络中名为[委派子网](../virtual-network/virtual-network-manage-subnet.md)这一特殊用途的子网内。 因此，你可以根据需要通过 VNet 对等互连直接从 Azure 中访问这些卷，或通过虚拟网络网关（ExpressRoute 或 VPN 网关）直接本地访问。 此子网专用于 Azure NetApp 文件，并且没有与 Internet 建立连接。 

## <a name="configurable-network-features"></a>可配置的网络功能  

 Azure NetApp 文件的[标准网络功能](configure-network-features.md)配置提供公共预览版。 在订阅中注册此功能后，可以在受支持的区域中选择标准或基本网络功能来创建新的卷 。 在不支持标准网络功能的区域中，卷默认使用基本网络功能。  

* ***标准***  
    选择此设置可实现更高的 IP 限制和标准 VNet 功能，如本文所述的[网络安全组](../virtual-network/network-security-groups-overview.md)和[用户定义路由](../virtual-network/virtual-networks-udr-overview.md#user-defined)，以及其他连接模式。

* ***基本***  
    选择此设置将启用[注意事项](#considerations)部分提到的选择性连接模式和有限 IP 规模。 所有[约束](#constraints)都适用于此设置。 

## <a name="considerations"></a>注意事项  

在规划 Azure NetApp 文件网络时应了解一些注意事项。

### <a name="constraints"></a>约束

下表描述了每项网络功能配置支持的内容：

|      功能     |      标准网络功能     |      基本网络功能     |
|---|---|---|
|     含有 Azure NetApp 文件（包括立即对等的 VNet）的 VNet 中使用的 IP 数量    |     [按照 VM 的标准限制](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits)    |     1000    |
|     每个 VNet 的 ANF 委托子网数    |     1    |     1    |
|     在 Azure NetApp 文件委托子网中的[网络安全组](../virtual-network/network-security-groups-overview.md) (NSG)    |     是    |     否    |
|     在 Azure NetApp 文件委托子网中的[用户定义路由](../virtual-network/virtual-networks-udr-overview.md#user-defined) (UDR)    |     是    |     否    |
|     连接到[专用终结点](../private-link/private-endpoint-overview.md)    |     否    |     否    |
|     连接到[服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)    |     否    |     否    |
|     针对 Azure NetApp 文件接口的 Azure 策略（例如自定义命名策略）    |     否    |     否    |
|     Azure NetApp 文件流量的负载均衡器    |     否    |     否    |
|     双堆栈（IPv4 和 IPv6）VNet    |     否 <br> （仅支持 IPv4）    |     否 <br> （仅支持 IPv4）    |

### <a name="supported-network-topologies"></a>支持的网络拓扑

下表描述了 Azure NetApp 文件的每项网络功能配置支持的网络拓扑。 

|      拓扑     |      标准网络功能     |      基本网络功能     |
|---|---|---|
|     连接至本地 VNet 中的卷    |     是    |     是    |
|     连接至对等互连 VNet 中的卷（同一区域）    |     是    |     是    |
|     连接到对等互连 VNet 中的卷（跨区域或全球对等互连）    |     否    |     否    |
|     通过 ExpressRoute 网关连接至卷    |     是    |     是    |
|     ExpressRoute (ER) FastPath    |     是    |     否    |
|     通过 ExpressRoute 网关和带有网关传输的 VNet 对等互连从本地连接到辐射 VNet 中的卷    |     是    |     是    |
|     通过 VPN 网关从本地连接到分支 VNet 中的卷    |     是    |     是    |
|     通过 VPN 网关和带有网关传输的 VNet 对等互连从本地连接到分支 VNet 中的卷    |     是    |     是    |
|     通过主动/被动 VPN 网关进行连接    |     是    |     是    |
|     通过主动/主动 VPN 网关进行连接    |     是    |     否    |
|     通过主动/主动区域冗余网关进行连接    |     否    |     否    |
|     通过虚拟 WAN (VWAN) 进行连接    |     否    |     否    |

## <a name="virtual-network-for-azure-netapp-files-volumes"></a>Azure NetApp 文件卷的虚拟网络

本部分介绍可帮助你进行虚拟网络规划的概念。

### <a name="azure-virtual-networks"></a>Azure 虚拟网络

在预配 Azure NetApp 文件卷之前，需要先创建 Azure 虚拟网络 (VNet) 或使用订阅中已存在的虚拟网络。 VNet 定义卷的网络边界。  有关创建虚拟网络的详细信息，请参阅 [Azure 虚拟网络文档](../virtual-network/virtual-networks-overview.md)。

### <a name="subnets"></a>子网

子网将虚拟网络分段成独立的地址空间，这些地址空间可供里面的 Azure 资源使用。  Azure NetApp 文件卷包含在名为[委派子网](../virtual-network/virtual-network-manage-subnet.md)这一特殊用途的子网内。 

子网委派为 Azure NetApp 文件服务提供了显式权限，以便在子网中创建服务特定的资源。  它在部署服务时会使用唯一标识符。 在这种情况下，将创建一个网络接口以实现到 Azure NetApp 文件的连接。

如果使用新的 VNet，可以按照[将子网委派给 Azure NetApp 文件](azure-netapp-files-delegate-subnet.md)中的说明创建子网并将其委派给 Azure NetApp 文件。 还可以将未委派的现有的空子网委派给其他服务。

如果此 VNet 与其他 VNet 对等互连，你则不能扩展此 VNet 地址空间。 因此，需要在此 VNet 地址空间内创建新的委派子网。 如果需要扩展地址空间，则必须在扩展地址空间之前删除 VNet 对等互连。

### <a name="udrs-and-nsgs"></a>UDR 和 NSG

用户定义路由 (UDR) 和网络安全组 (NSG) 仅 Azure NetApp 文件委托子网上受支持，该子网至少有一个使用标准网络功能创建的卷。  

> [!NOTE]
> Azure NetApp 文件网络接口不支持在网络接口级别关联 NSG。 

如果子网包含具有标准网络功能的卷和基本网络功能的卷（或未注册功能预览版的现有卷），则委托子网上应用的 UDR 和 NSG 将仅适用于具有标准网络功能的卷。

对于具有基本网络功能的卷，不支持在带有委托子网地址前缀且下一个跃点为 NVA 的源 VM 子网上配置用户定义路由 (UDR)。 这样设置将导致连接问题。

## <a name="azure-native-environments"></a>Azure 原生环境

下图展现了 Azure 本机环境：

![Azure 本机网络环境](../media/azure-netapp-files/azure-netapp-files-network-azure-native-environment.png)

### <a name="local-vnet"></a>本地 VNet

基本方案是从同一 VNet 中的虚拟机 (VM) 创建或连接到 Azure NetApp 文件卷。 对于上图中的 VNet 2 而言，卷 1 是在委派子网中创建的，并且可以装入到默认子网中的 VM 1 上。

### <a name="vnet-peering"></a>VNet 对等互连

如果同一区域中有需要访问彼此资源的其他 VNet，可以使用 [VNet 对等互连](../virtual-network/virtual-network-peering-overview.md)来连接这些 VNet，从而通过 Azure 基础结构实现安全连接。 

请考虑上图中的 VNet 2 和 VNet 3。 如果 VM 1 需要连接到 VM 2 或卷 2，或者 VM 2 需要连接到 VM 1 或卷 1，则需要在 VNet 2 和 VNet 3 之间启用 VNet 对等互连。 

此外，请考虑一种方案，其中 VNet 1 与 VNet 2 对等互连，而 VNet 2 在同一区域中与 VNet 3 对等互连。 VNet 1 中的资源可以连接到 VNet 2 中的资源，但无法连接到 VNet 3 中的资源，除非 VNet 1 和 VNet 3 是对等互连的。 

在上图中，尽管 VM 3 可以连接到卷 1，但 VM 4 无法连接到卷 2。  导致出现这种情况是因为辐射 VNet 不是对等互连的，并且不支持通过 VNet 对等互连实现传输路由。

## <a name="hybrid-environments"></a>混合环境

下图展现了混合环境： 

![混合网络环境](../media/azure-netapp-files/azure-netapp-files-network-hybrid-environment.png)

在混合方案中，本地数据中心内的应用程序需要访问 Azure 中的资源。  无论是要将数据中心扩展到 Azure，还是要使用 Azure 本机服务或进行灾难恢复，都是如此。 有关如何通过站点到站点 VPN 或 ExpressRoute 将多个资源本地连接到 Azure 中的资源的信息，请参阅 [VPN 网关规划选项](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable)。

在混合中心辐射型拓扑中，Azure 中的中心 VNet 充当到本地网络的连接的中心点。 辐射是与中心对等互连的 VNet，可用于隔离工作负荷。

根据配置，可以将本地资源连接到中心和辐射中的资源。

在上面所示的拓扑中，本地网络连接到 Azure 中的中心 VNet，同一区域中有 2 个辐射 VNet 与此中心 VNet 对等互连。  在此方案中，Azure NetApp 文件卷支持的连接选项如下所示：

* 本地资源 VM 1 和 VM 2 可以通过站点到站点 VPN 或 ExpressRoute 线路连接到中心中的卷 1。 
* 本地资源 VM 1 和 VM 2 可以通过站点到站点 VPN 和区域 VNet 对等互连连接到卷 2或卷 3。
* 中心 VNet 中的 VM 3 可以连接到辐射 VNet 1 中的卷 2 以及辐射 VNet 2 中的卷 3。
* 辐射 VNet 1 中的 VM 4 和辐射 VNet 2 中的 VM 5 可以连接到中心 VNet 中的卷 1。
* 辐射 VNet 1 中的 VM 4 无法连接到辐射 VNet 2 中的卷 3。 此外，辐射 VNet2 中的 VM 5 无法连接到辐射 VNet 1 中的卷 2。 这是因为辐射 VNet 不是对等互连的，并且不支持通过 VNet 对等互连实现传输路由。
* 在上述体系结构中，如果辐射 VNET 中还存在一个网关，那么此中心中通过网关从本地到 ANF 卷的连接将丢失。 按照设计，首选连接将提供给辐射 VNet 中的网关，因此只有通过该网关连接的计算机才能连接到 ANF 卷。

## <a name="next-steps"></a>后续步骤

* [将子网委派给 Azure NetApp 文件](azure-netapp-files-delegate-subnet.md)
* [为 Azure NetApp 文件卷配置网络功能](configure-network-features.md) 
