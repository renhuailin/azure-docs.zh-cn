---
title: Azure 德国网络服务 | Microsoft Docs
description: 提供 Azure 德国专用连接的功能比较和指南
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfdocs
ms.openlocfilehash: aa1d648cf3e3f43d4a29c5dc199420f8d731848a
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2020
ms.locfileid: "117029172"
---
# <a name="azure-germany-networking-services"></a>Azure 德国网络服务

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

## <a name="expressroute-private-connectivity"></a>ExpressRoute（专用连接）
Azure ExpressRoute 已在 Azure 德国正式发布。 有关详细信息（包括合作伙伴和对等互连位置），请参阅 [ExpressRoute 全球文档](../expressroute/index.yml)。

### <a name="variations"></a>变体

* Azure 德国客户通过专用的 Azure 德国 ExpressRoute 连接连接到物理隔离的容量。
* Azure 德国使用多个至少相隔 400 公里的区域对来提高可用性和持续性。 
* 默认情况下，所有 Azure 德国 ExpressRoute 连接均配置为支持突发的主动-主动冗余，并提供高达 10G 的线路容量。
* Azure 德国 ExpressRoute 位置为客户和 Azure 德国异地冗余区域提供优化的路径（包括最短跃点、低延迟和高性能）。
* Azure 德国 ExpressRoute 专用连接不使用、不遍历，也不依赖 Internet。
* Azure 德国物理和逻辑基础结构在物理上是专用的，并与国际 Microsoft 云网络分离。
* Azure 德国 ExpressRoute 提供与 Microsoft Azure 云服务的专用连接，但不提供与 Microsoft 365 或 Dynamics 365 云服务的专用连接。

### <a name="considerations"></a>注意事项
两项基本服务提供到 Azure 德国的专用网络连接：ExpressRoute 和 VPN（典型组织的站点到站点 VPN）。

你可使用 ExpressRoute 在 Azure 德国数据中心和本地基础结构或共置环境之间创建专用连接。 ExpressRoute 连接不通过公共 Internet 。 与典型的 Internet 连接相比，这些连接可靠性更高、速度更快、延迟更低。 在某些情况下，使用 ExpressRoute 连接在本地系统和 Azure 之间传输数据可以带来显著的成本效益。   

使用 ExpressRoute，可以在 ExpressRoute 位置（例如 ExpressRoute Exchange 提供商设施）建立与 Azure 的连接。 或者从网络服务提供商提供的现有 WAN（例如多协议标签交换 (MPLS) VPN）直接连接到 Azure。

对于支持 Azure 德国客户应用程序和解决方案的网络服务，强烈建议实现 ExpressRoute（专用连接）来连接到 Azure 德国。 如果使用 VPN 连接，请考虑以下事项：

* 联系授权官方/机构，确定是否需要专用连接或其他安全连接机制，并确定要考虑的任何其他限制。
* 决定是否要强制通过专用连接区域路由站点到站点 VPN。
* 使用授权的专用连接访问提供程序来获取 MPLS 线路或 VPN。

如果使用专用连接体系结构，需要验证是否已为通向 Azure 德国的网关网络/Internet (GN/I) 边缘路由器分界点的连接建立并维护相应的实现。 同样，组织必须在 Azure 德国的本地环境和网关网络/客户 (GN/C) 边缘路由器分界点之间建立网络连接。

如果在 Azure 德国区域内的任何一个对等互连位置通过 ExpressRoute 连接到 Microsoft，将能够访问德国边界内所有区域中的所有 Microsoft Azure 云服务。 例如，如果在柏林通过 ExpressRoute 连接到 Microsoft，则可以访问在 Azure 德国托管的所有 Microsoft 云服务。

要了解有关位置和合作伙伴的详细信息，并获取针对 Azure 德国对等互连位置的 ExpressRoute 的详细列表，请参阅 [ExpressRoute 全球文档](../expressroute/index.yml)中的“概述”选项卡。

可以购买多个 ExpressRoute 线路。 如果拥有多个连接，则可以从异地冗余中获得明显的高可用性优势。 如果有多条 ExpressRoute 线路，你将在公共对等互连路径收到 Microsoft 播发的同一组前缀。 这意味着你将有多个路径从你的网络接入 Microsoft。 这种情况可能会导致在网络中做出欠佳的路由决策。 因此，可能会在不同的服务上遇到欠佳的连接体验。 有关详细信息，请参阅 [ExpressRoute 全球文档](../expressroute/index.yml)中的“操作指南”>“最佳做法”选项卡，并选择“优化路由” 。

## <a name="support-for-load-balancer"></a>对负载均衡器的支持
Azure 负载均衡器已在 Azure 德国正式发布。 有关详细信息，请参阅[负载均衡器全球文档](../load-balancer/load-balancer-overview.md)。 

## <a name="support-for-traffic-manager"></a>对流量管理器的支持
Azure 流量管理器已在 Azure 德国正式发布。 有关详细信息，请参阅[流量管理器全球文档](../traffic-manager/traffic-manager-overview.md)。 

## <a name="support-for-virtual-network-peering"></a>对虚拟网络对等互连的支持 
虚拟网络对等互连已在 Azure 德国正式发布。 有关详细信息，请参阅[虚拟网络对等互连全球文档](../virtual-network/virtual-network-peering-overview.md)。 

## <a name="support-for-vpn-gateway"></a>对 VPN 网关的支持 
Azure VPN 网关已在 Azure 德国正式发布。 有关详细信息，请参阅 [VPN 网关全球文档](../vpn-gateway/vpn-gateway-about-vpngateways.md)。 

## <a name="next-steps"></a>后续步骤
有关补充信息和更新，请订阅 [Azure 德国博客](/archive/blogs/azuregermany/)。