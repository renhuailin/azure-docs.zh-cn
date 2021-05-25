---
title: Microsoft 全球网络 - Azure
description: 了解 Microsoft 如何构建和运营全球最大的主干网络之一，以及该网络在提供卓越的云体验方面为何能够起到核心作用。
services: networking
documentationcenter: ''
author: KumudD
manager: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/05/2020
ms.author: kumud
ms.reviewer: ypitsch
ms.openlocfilehash: a33c7e7d637b519b249c250481a73d04c661d4d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "97915838"
---
# <a name="microsoft-global-network"></a>Microsoft 全球网络

Microsoft 拥有并运营全球最大的主干网络之一。 此全球性的复杂体系结构跨越 165,000 英里以上，将我们的数据中心和客户连接到一起。 
 
世界各地的客户每天都会连接 Microsoft Azure、必应、Dynamics 365、Microsoft 365、XBox 等服务并向其传递数以万亿的请求。 不管是哪种类型的客户，他们都希望我们的服务能够保持可靠并即时做出响应。 
 
[Microsoft 全球网络](https://azure.microsoft.com/global-infrastructure/global-network/) (WAN) 是提供卓越云体验的核心所在。 我们的全球网络连接了 61 个 Azure 区域中的 Microsoft [数据中心](https://azure.microsoft.com/global-infrastructure/)以及战略性地放置于全球的边缘节点的庞大网格，提供可满足任何需求的可用性、容量和灵活性。

![Microsoft 全球网络](./media/microsoft-global-network/microsoft-global-wan.png)
 
## <a name="get-the-premium-cloud-network"></a>获取高级云网络
 
使用 Microsoft 云时，选择获得[尽可能最佳的体验](https://www.sdxcentral.com/articles/news/azure-tops-aws-gcp-in-cloud-performance-says-thousandeyes/2018/11/)十分容易。 从客户流量通过战略性地放置的边缘节点进入我们的全球网络那一刻，其数据就会以接近光速的速度通过优化的路由传输。 这可以确保通过尽量降低延迟来实现最佳性能。 这些边缘节点全部通过位于 175 个以上位置的数千个连接互连到 4000 多个独特的 Internet 合作伙伴（对等方），提供了互连策略的基础。 
 
无论是从伦敦连接到东京，还是从华盛顿特区连接到洛杉矶，网络性能都可以量化，并受延迟、抖动、丢包和吞吐量等因素的影响。  Microsoft 优先使用直接互连而不是传输链路，这可使响应流量保持对称，并有助于尽量缩短和简化跃点、对等方和路径。 

例如，如果身处伦敦的某个用户尝试访问位于东京的某个服务，则 Internet 流量将进入伦敦的某个边缘，流经法国的 Microsoft WAN（我们在欧洲与印度之间架设的跨阿拉伯路径），然后进入托管该服务的日本区域。 响应流量是对称的。 这有时称为[冷土豆路由](https://en.wikipedia.org/wiki/Hot-potato_and_cold-potato_routing)，表示在转移流量之前，尽可能长时间地将流量保持在 Microsoft 网络上。  
  
那么，这包括使用 Microsoft 服务时的任何流量吗？ 是的，数据中心之间的、Microsoft Azure 内部的或者 Microsoft 服务（例如虚拟机、Microsoft 365、XBox、SQL 数据库、存储和虚拟网络）之间的任何流量，都会在我们的全球网络内部路由，而永远不会通过公共 Internet 路由，以确保最佳性能和完整性。  
 
我们为跨地下、地面和海底路径的光纤容量及多样性投入了巨量资金，这对于我们持续保持较高服务水平，并推动云和联机服务的极致发展至关重要。 最近添加到我们全球网络中的是 [MAREA](https://www.submarinecablemap.com/#/submarine-cable/marea) 海底光缆（本行业的第一条跨海底开放线路系统 (OLS)，在西班牙毕尔巴鄂与美国弗吉尼亚海滩之间建立了连接）、美国纽约与爱尔兰都柏林之间的 [AEC](https://www.submarinecablemap.com/#/submarine-cable/aeconnect-1) 以及日本东京与美国俄勒冈州波特兰之间的[新跨太平洋海缆 (NCP)](https://www.submarinecablemap.com/#/submarine-cable/new-cross-pacific-ncp-cable-system)。 
 

## <a name="our-network-is-your-network"></a>我们的网络就是你的网络

我们凭借二十年的经验以及对网络的大量投资来持续确保最佳性能。 企业可以充分利用我们的网络资产，立足于此构建先进的叠加体系结构。 
 
Microsoft Azure 提供最丰富的服务和功能组合，使客户能够在任何位置快速轻松地构建、扩展和满足网络需求。 我们的连接服务系列涵盖了区域间虚拟网络对等互连、混合方案、云中点到站点体系结构、站点到站点体系结构以及全球 IP 传输方案。  对于想要将自己的数据中心或网络连接到 Azure 的企业，或者需要进行大规模数据引入或传输的客户，[ExpressRoute](../expressroute/expressroute-introduction.md) 和 [ExpressRoute Direct](../expressroute/expressroute-erdirect-about.md) 提供高达 100 Gbps 的带宽选项，用于在世界各地的对等互连位置直接接入 Microsoft 的全球网络。  
 
[ExpressRoute Global Reach](../expressroute/expressroute-global-reach.md) 旨在补充服务提供商的 WAN 实施，并连接客户在世界各地的本地站点。 如果你从事全球运营，可将 ExpressRoute Global Reach 与你偏好的当地服务提供商网络结合使用，通过 Microsoft 全球网络连接你的所有全球站点。 通过 Azure 虚拟 WAN 可在云 (WAN) 中扩展新网络，以覆盖大量的分支站点。Azure 虚拟 WAN 内置了易于使用的自动化连接和配置管理，可让你使用 SDWAN 和 VPN 设备（即客户现场设备，简称 CPE）将分支无缝连接到 Microsoft 全球网络。 
 
[全球 VNet 对等互连](../virtual-network/virtual-network-peering-overview.md)使客户能够跨区域无缝连接两个或更多个 Azure 虚拟网络。 一旦建立对等互连，这些虚拟网络就像融为了一体。 对等虚拟网络中虚拟机之间的流量通过 Microsoft 主干基础结构路由，非常类似于只通过专用 IP 地址在同一虚拟网络中的虚拟机之间路由流量。 
 

## <a name="well-managed-using-software-defined-innovation"></a>使用软件定义的创新进行合理的管理

Microsoft 运营世界领先的云之一，在构建和管理高性能全球基础架构方面积累了丰富的知识和经验。  
 
我们遵守一套严格的运营原则： 
 
- 在各个网络层中使用同类最佳的交换硬件。  
- 在不对最终用户造成任何影响的前提下部署新功能。  
- 安全、可靠、尽快地在整个设备群中推出更新。 时间以小时计，而不是以周计。  
- 利用云规模的深度遥测和全自动化的故障缓解措施。  
- 使用软件定义的统一网络技术控制网络中的所有硬件组件。  消除重复项并减少故障。 
 
这些原则适用于网络的所有层：从主机网络接口、交换平台、数据中心内的网络功能（例如负载均衡器），一直到包含流量工程平台和光纤网络的 WAN。  
 
Azure 及其网络的指数级发展已达到一个全新高度，以致我们最终认识到，不再可以依赖人类直觉来管理全球性的网络运营。 为了满足验证网络的长期、中期和短期变化的需求，我们开发了一个平台，用于综合地镜像和仿真我们的生产网络。 由于可以创建镜像环境并运行数百万个仿真项目，我们得以能够在将软件和硬件更改提交到生产平台和网络之前，先对这些更改及其造成的影响进行测试。 

## <a name="next-steps"></a>后续步骤
- [了解 Microsoft 如何通过智能软件提高全球网络的可靠性](https://azure.microsoft.com/blog/advancing-global-network-reliability-through-intelligent-software-part-1-of-2/)
- [详细了解 Azure 中提供的网络服务](https://azure.microsoft.com/product-categories/networking/)