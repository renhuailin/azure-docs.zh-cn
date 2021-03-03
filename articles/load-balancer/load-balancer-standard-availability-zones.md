---
title: Azure 负载均衡器和可用性区域
titleSuffix: Azure Load Balancer
description: 了解此学习途径后，开始 Azure 标准负载均衡器和可用性区域。
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
ms.openlocfilehash: 3c18b6d8dc44762649a9c07b88af348a18888fb5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101699107"
---
# <a name="load-balancer-and-availability-zones"></a>负载均衡器和可用性区域

Azure 负载均衡器支持可用性区域方案。 你可以通过将资源与跨区域进行对齐，使用标准负载均衡器提高方案中的可用性。  查看此文档以了解这些概念和基本方案设计指南

负载均衡器可以是 **区域冗余、区域性** 或 **非区域性** 的。 若要为负载均衡器配置上述) 的区域相关属性 (，请选择所需的相应前端类型。

## <a name="zone-redundant"></a>区域冗余

在可用性区域中，标准负载均衡器可以是区域冗余的。 此流量由单个 IP 地址提供服务。

单个前端 IP 地址将在区域发生故障后仍然存在。 前端 IP 可用于访问与区域无关的所有 (不受影响的) 后端池成员。 一个或多个可用性区域可能会失败，并且数据路径置，只要区域中的一个区域仍处于正常状态。

前端的 IP 地址由多个可用性区域中的多个独立基础结构部署同时提供服务。 任何重试或重新建立都将在不受区域故障影响的其他区域成功完成。

<p align="center">
  <img src="./media/az-zonal/zone-redundant-lb-1.svg" alt="Figure depicts a zone-redundant standard load balancer directing traffic in three different zones to three different subnets in a zone redundant configuration." width="512" title="虚拟网络 NAT">
</p>

*图：区域冗余负载均衡器*

## <a name="zonal"></a>剖面

你可以选择将前端确保为单一区域，这称为 "区域 *"。*  这种情况意味着，任何入站或出站流都由区域中的单个区域提供服务。  前端的运行状态取决于该局部区域。  区域（保证前端所在的区域除外）中发生故障不会影响数据路径。 可以使用局域性前端来按可用性区域公开 IP 地址。  

此外，还支持在每个区域中直接对负载平衡终结点使用区域前端。 可以使用此配置来公开每个区域负载均衡的终结点，以单独监视每个区域。 对于公用终结点，你可以将它们与 [流量管理器](../traffic-manager/traffic-manager-overview.md) 等 dns 负载平衡产品集成，并使用单个 DNS 名称。


<p align="center">
  <img src="./media/az-zonal/zonal-lb-1.svg" alt="Figure depicts three zonal standard load balancers each directing traffic in a zone to three different subnets in a zonal configuration." width="512" title="虚拟网络 NAT">
</p>

*图：区域负载均衡器*

对于公共负载均衡器前端，请将 **区域** 参数添加到公共 IP。 此公共 IP 由各自规则使用的前端 IP 配置引用。

对于内部负载均衡器前端，请将 **区域** 参数添加到内部负载均衡器的前端 IP 配置。 区域性前端保证子网中的 IP 地址指向特定区域。

## <a name="design-considerations"></a><a name="design"></a> 设计注意事项

现在，你已了解标准负载均衡器的区域相关属性，以下设计注意事项可能有助于你设计高可用性。

### <a name="tolerance-to-zone-failure"></a>容错到区域故障

- **区域冗余** 的负载均衡器可以在具有一个 IP 地址的任何区域中为区域资源提供服务。  如果区域中至少有一个区域处于正常状态，则 IP 可能会经受一个或多个区域故障。
- **区域性** 前端是指将服务降至单个区域，并与各自的区域共享命运。 如果你的部署所在的区域发生故障，你的部署将不会经受此故障。

建议为生产工作负荷使用区域冗余负载均衡器。

### <a name="control-vs-data-plane-implications"></a>控件与数据平面的影响

区域冗余并不意味着意味着数据平面或控制平面。 区域冗余流可以使用任何区域，你的流将使用区域中的所有正常区域。 在区域发生故障时，使用健康区域的流量不受影响。

区域发生故障时使用区域的流量可能会受到影响，但应用程序可以恢复。 当 Azure 围绕区域发生故障时，重新传输时，流量将在区域内的正常区域中继续进行。

查看 [Azure 云设计模式](/azure/architecture/patterns/) ，以提高应用程序在故障情况下的复原能力。

## <a name="next-steps"></a>后续步骤
- 详细了解 [可用性区域](../availability-zones/az-overview.md)
- 详细了解[标准负载均衡器](./load-balancer-overview.md)
- 了解如何 [使用区域性标准负载均衡器在区域内对 vm 进行负载均衡](./quickstart-load-balancer-standard-public-cli.md)
- 了解如何 [使用区域冗余标准负载均衡器跨区域对 vm 进行负载均衡](./quickstart-load-balancer-standard-public-cli.md)
- 了解 [Azure 云设计模式](/azure/architecture/patterns/) ，以提高应用程序在故障情况下的复原能力。
