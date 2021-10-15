---
title: Azure 负载均衡器和可用性区域
titleSuffix: Azure Load Balancer
description: 通过此学习路径开始使用 Azure 标准负载均衡器和可用性区域。
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/07/2020
ms.author: allensu
ms.openlocfilehash: 06b8e193f48a4e1d3956c8c40ee03dfd11a4a088
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129276012"
---
# <a name="load-balancer-and-availability-zones"></a>负载均衡器和可用性区域

Azure 负载均衡器支持可用性区域方案。 可以使用标准负载均衡器通过将资源与局部区域相适应并分布到多个局部区域来提高整个方案的可用性。  请查看本文档了解这些概念和基本场景设计指导

负载均衡器可以是局部区域冗余的、局部区域性的或非局部区域性的 。 若要为负载均衡器配置局部区域相关的属性（如前所述），请选择所需的相应前端类型。

## <a name="zone-redundant"></a>区域冗余

在包含可用性区域的 Azure 区域中，标准负载均衡器可以是局部区域冗余的。 此流量由单个 IP 地址提供。

在发生局部区域故障后，单个前端 IP 地址可以幸存。 不管局部区域是什么，都可以使用前端 IP 访问所有（未受影响的）后端池成员。 一个或多个可用性区域可能会发生故障，而数据路径可以幸存，但前提是 Azure 区域中有一个局部区域保持正常。

前端的 IP 地址由多个可用性区域中多个独立的基础结构部署同时提供。 在未受该局部区域的故障影响的其他局部区域中，任何重试或重建都会成功。

<p align="center">
  <img src="./media/az-zonal/zone-redundant-lb-1.svg" alt="Figure depicts a zone-redundant standard load balancer directing traffic in three different zones to three different subnets in a zone redundant configuration." width="512" title="虚拟网络 NAT">
</p>

图：局部区域冗余的负载均衡器

## <a name="zonal"></a>局部区域

可以选择将某个前端指定为保证位于单个局部区域，这称为局部区域性前端。  此方案意味着，任何入站或出站流将由某个 Azure 区域中的单个局部区域提供。  前端的运行状态取决于该局部区域。  区域（保证前端所在的区域除外）中发生故障不会影响数据路径。 可以使用局域性前端来按可用性区域公开 IP 地址。  

此外，支持在每个局部区域中直接为负载均衡的终结点使用局部区域性前端。 可以使用此配置来按局部区域负载均衡的终结点公开 IP 地址，以单独监视每个局部区域。 对于公共终结点，可将其与[流量管理器](../traffic-manager/traffic-manager-overview.md) 等 DNS 负载均衡产品相集成，并使用单个 DNS 名称。


<p align="center">
  <img src="./media/az-zonal/zonal-lb-1.svg" alt="Figure depicts three zonal standard load balancers each directing traffic in a zone to three different subnets in a zonal configuration." width="512" title="虚拟网络 NAT">
</p>

图：局部区域负载均衡器

对于公共负载均衡器前端，需将 zones 参数添加到公共 IP。 此公共 IP 由相应规则使用的前端 IP 配置引用。

对于内部负载均衡器前端，需将 zones 参数添加到内部负载均衡器前端 IP 配置。 局部区域性前端保证子网中的某个 IP 地址位于特定的局部区域。

## <a name="non-zonal"></a>非局部区域

负载均衡器也可在非区域配置中创建，方法是使用“非局部区域”前端（公共 IP 或公共 IP 前缀）。  此选项不保证冗余。 请注意，[已升级](../virtual-network/public-ip-upgrade-portal.md)的所有公共 IP 地址都属于“无区域”类型。

## <a name="design-considerations"></a><a name="design"></a>设计注意事项

了解标准负载均衡器的局部区域相关属性后，以下设计注意事项也许可以帮助你完成高可用性设计。

### <a name="tolerance-to-zone-failure"></a>局部区域容错

- 局部区域冗余的负载均衡器可通过一个 IP 地址为任何局部区域中的局部区域资源提供服务。  只要 Azure 区域中至少有一个局部区域保持正常，在一个或多个局部区域发生故障时，该 IP 就可以幸存。
- 局部区域性前端是在单个局部区域中部署服务的简化设计，其命运与相应的局部区域相同。 如果你的部署所在的局部区域发生故障，则该部署在此次故障时也不能幸存。

建议为生产工作负载使用局部区域冗余的负载均衡器。

### <a name="multiple-frontends"></a>多个前端

使用多个前端可以在多个端口和/或 IP 地址上对流量进行负载均衡。  设计体系结构时，必须考虑到区域冗余和多个前端可以交互的方式。  请注意，如果目标是始终使每个前端都能够灵活应对故障，则用作前端的所有 IP 地址都必须是区域冗余的。   如果一组前端旨在与单个区域关联，那么这组前端的每个 IP 地址都必须与该特定区域关联。  不需要每个区域都有负载均衡器；相反，每个局部区域前端（或局部区域前端集）可以与该特定可用性区域中的后端池中的虚拟机相关联。

### <a name="transition-between-regional-zonal-models"></a>区域局部区域模型之间的转换

如果区域已扩充为具有[可用性区域](../availability-zones/az-overview.md)，则将保持非局部区域性。 为了确保体系结构可以利用新区域，建议创建新前端 IP，并复制相应的规则和配置以利用这些新的公共 IP。

### <a name="control-vs-data-plane-implications"></a>对控制平面和数据平面的影响

局部区域冗余并不意味着数据平面或控制平面不会受到冲击。 局部区域冗余流可以使用任何局部区域，而你的流将使用 Azure 区域中所有正常的局部区域。 在发生局部区域故障时，使用正常局部区域的流量流不受影响。

发生局部区域故障时使用了该局部区域的流量流可能会受影响，但应用程序可以恢复。 在 Azure 融合各方力量从局部区域故障中恢复并重新传输数据后，流量将继续传入 Azure 区域中正常的局部区域。

请查看 [Azure 云设计模式](/azure/architecture/patterns/)来改善应用程序在故障情况下的复原能力。

## <a name="limitations"></a>限制

* 创建后，资源的区域无法更改、更新或创建。
* 创建后，无法将资源从“局部区域”更新为“区域冗余”，反之亦然。

## <a name="next-steps"></a>后续步骤
- 详细了解[可用性区域](../availability-zones/az-overview.md)
- 详细了解[标准负载均衡器](./load-balancer-overview.md)
- 了解如何[使用局部区域标准负载均衡器在局部区域中对 VM 进行负载均衡](./quickstart-load-balancer-standard-public-cli.md)
- 了解如何[使用局部区域冗余的标准负载均衡器跨局部区域对 VM 进行负载均衡](./quickstart-load-balancer-standard-public-cli.md)
- 了解可以改善应用程序在故障情况下的复原能力的 [Azure 云设计模式](/azure/architecture/patterns/)。
