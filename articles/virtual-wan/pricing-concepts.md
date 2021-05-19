---
title: 关于虚拟 WAN 定价
titleSuffix: Azure Virtual WAN
description: 本文介绍了常见的虚拟 WAN 定价问题
services: virtual-wan
author: reyandap
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 12/08/2020
ms.author: reyandap
ms.custom: references_pricing
ms.openlocfilehash: 13451291544f704000ab61d41ed5014fa69298e4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "100517682"
---
# <a name="about-virtual-wan-pricing"></a>关于虚拟 WAN 定价

Azure 虚拟 WAN 在统一框架中将多个网络和安全服务汇集在一起。 它基于中心辐射型体系结构，其中，中心由 Microsoft 使用其中提供的各种服务进行管理，这些服务包括 VPN、ExpressRoute、用户 VPN（点到站点）、防火墙、路由，等等。

虚拟 WAN 中的每项服务都是有价格的。 因此，建议一个单一价格不适用于虚拟 WAN。 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)提供了一种机制，可以基于虚拟 WAN 中预配的服务来得出成本。 本文讨论了有关虚拟 WAN 定价的常见问题。

>[!NOTE]
>有关当前的定价信息，请参阅[虚拟 WAN 定价](https://azure.microsoft.com/pricing/details/virtual-wan/)。 中心间（中心到中心）费用不会显示在虚拟 WAN 定价页中，因为这是区域间（洲内/洲际）费用，即 [Azure 数据传输费用](https://azure.microsoft.com/pricing/details/bandwidth/)。

## <a name="common-pricing-questions"></a><a name="questions"></a>常见定价问题

### <a name="what-is-a-scale-unit"></a><a name="scale-unit"></a>什么是缩放单元？

缩放单元提供了虚拟中心内站点到站点 (S2S)、点到站点 (P2S) 以及 ExpressRoute (ER) 的聚合容量单元。 例如：

* 1 S2S VPN 缩放单元意味着，虚拟中心内有一个总容量为 500 Mbps 的 VPN 网关（部署了双实例以提供复原能力），成本为 0.361 美元/小时。
* 1 ER 缩放单元意味着，虚拟中心内有一个总容量为 2 Gbps 的 ER 网关，成本为 0.42 美元/小时。
* 5 ER 缩放单元意味着，虚拟中心 VNet 内有一个总容量为 10 Gbps 的 ER 网关，价格为 0.42 美元*5/小时。 从第 6 个缩放单元到第 10 个缩放单元的 ER 递增量为 0.25 美元/小时。

### <a name="what-is-a-connection-unit"></a><a name="connection-unit"></a>什么是连接单元？

连接单元适用于连接到 Azure 网关的任何本地/非 Microsoft 终结点。 对于站点到站点 VPN，此值表示分支。 对于用户 VPN（点到站点），此值表示远程用户。 对于 ExpressRoute，此值表示 ExpressRoute 线路连接。<br>例如：

* 连接到虚拟中心内的 Azure VPN 的一个分支连接的成本为 0.05 美元/小时。 因此，连接到 Azure 虚拟中心的 100 个分支连接的成本为 0.05 美元*100/小时。

* 连接到虚拟中心的两个 ExpressRoute 线路连接的成本为 0.05 美元*2/小时。

* 连接到 Azure 虚拟中心 P2S 网关的三个远程用户连接的成本为 0.03 美元*3/小时。

### <a name="how-are-data-transfer-charges-calculated"></a><a name="data-transfer"></a>数据传输费用是如何计算的？

* 进入 Azure 的任何流量都不收费。 离开 Azure 的流量（通过 VPN、ExpressRoute，或点到站点用户 VPN 连接）需要支付标准 [Azure 数据传输费用](https://azure.microsoft.com/pricing/details/bandwidth/)。

* 在虚拟 WAN 中心与远程虚拟 WAN 中心之间或者与源中心所在区域之外的区域中的 VNet 之间进行数据传输时，离开中心的流量会产生数据传输费用。 示例：对于离开“美国东部”中心前往“美国西部”中心的流量，将按 0.02 美元/GB 计费。 进入“美国西部”中心的流量不收费。 所有中心到中心流量都需要支付区域间（洲内/洲际）[Azure 数据传输费用](https://azure.microsoft.com/pricing/details/bandwidth/)。 

### <a name="what-is-the-difference-between-a-standard-hub-fee-and-a-standard-hub-processing-fee"></a><a name="fee"></a>标准中心费用与标准中心处理费用的区别是什么？

虚拟 WAN 有两种形式：

* 基本虚拟 WAN：用户可以在其中部署多个中心并使用 VPN 站点到站点连接。 基本虚拟 WAN 没有高级功能，例如完全网格化中心、ExpressRoute 连接、用户 VPN/点到站点 VPN 连接、VNet 到 VNet 传递性连接、VPN 和 ExpressRoute 传输连接或 Azure 防火墙，等等。基本虚拟 WAN 中的中心不产生基本费用或数据处理费用。

* 标准虚拟 WAN：提供高级功能，例如完全网格化中心、ExpressRoute 连接、用户 VPN/点到站点 VPN 连接、VNet 到 VNet 传递性连接、VPN 和 ExpressRoute 传输连接、Azure 防火墙，等等。所有的虚拟中心路由都由一个路由器提供，该路由器在虚拟中心内实现了多项服务。 中心会产生基本费用，价格为 0.25 美元/小时。 对于 VNet 到 VNet 传输连接，还会对虚拟中心路由器中的数据处理收费。 请参阅下图。

 下面的示例图中有两个 VNet：VNET 1 和 VNET 2。 假设有 1 Gbps 数据从 VNET 1 中的一个 VM 发送到 VNET 2 中的另一个 VM。 将产生以下费用：

* 虚拟中心基本费用：0.25 美元/小时

* 1 Gbps 的虚拟中心数据处理费用：0.02 美元/GB

**示例**

   :::image type="content" source="./media/pricing-concepts/figure-1.png" alt-text="示例":::

## <a name="next-steps"></a>后续步骤

* 有关虚拟 WAN 的详细信息，请参阅[常见问题解答](virtual-wan-faq.md)。

* 有关当前定价，请参阅[虚拟 WAN 定价](https://azure.microsoft.com/pricing/details/virtual-wan/)。
