---
title: 关于虚拟 WAN 定价
titleSuffix: Azure Virtual WAN
description: 本文解答常见的虚拟 WAN 定价问题。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/02/2021
ms.author: cherylmc
ms.custom: references_pricing
ms.openlocfilehash: 853005cdc9e44357661cdb0b3b50e7f6ba5a0bbf
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124730102"
---
# <a name="about-virtual-wan-pricing"></a>关于虚拟 WAN 定价

Azure 虚拟 WAN 是一个网络服务，其中整合了多种网络、安全和路由功能，提供单一操作界面。 这些功能包括分支连接（通过 SD-WAN 或 VPN CPE 等虚拟 WAN 合作伙伴设备的连接自动化）、站点到站点 VPN 连接、远程用户 VPN（点到站点）连接、专用 (ExpressRoute) 连接、云内部连接（虚拟网络的可中转连接）、VPN ExpressRoute 互连、路由、Azure 防火墙、专用连接加密。

本文介绍 Azure 虚拟 WAN 的三种常见部署方案，以及基于所列价格的典型部署估算价格。 此外，虚拟 WAN 在其他许多方案中也能发挥作用。

> [!IMPORTANT]
> 本文中显示的定价仅用作示例。 
>  * 定价随时可能更改。 有关当前定价信息，请参阅[虚拟 WAN 定价](https://azure.microsoft.com/pricing/details/virtual-wan/)页。 
>  * 中心间（中心到中心）费用不会显示在虚拟 WAN 定价页中，因为定价是根据区域间（洲内/洲际）费用确定的。 有关详细信息，请参阅 [Azure 数据传输费用](https://azure.microsoft.com/pricing/details/bandwidth/)。
>

## <a name="pricing-components"></a><a name="pricing"></a>定价组成部分

下图显示了涉及虚拟 WAN 的网络中的典型数据路由，以及按每小时和每 GB 列出的不同定价组成部分。

:::image type="content" source="./media/pricing-concepts/data-routes.png" alt-text="数据路由示意图。" lightbox="./media/pricing-concepts/data-routes.png":::

| 值 | 方案| 每小时成本 | 每 GB 成本 |
| --- | --- | --- | --- |
| 1 | 通过美国东部的标准 vWAN 中心将数据从支路 VNet 传输到 VPN 站点到站点分支|部署小时（0.25 美元/小时）+ VPN S2S 缩放单元（0.261 美元/小时）+ VPN S2S 连接单元（0.05 美元/小时）= 0.561 美元/小时|VNet 对等互连（出站）（0.01 美元/GB）+ 标准出站区域 1（0.087 美元/GB）= 0.097 美元/GB|
| 1'|通过美国东部的标准 vWAN 中心将数据从 VPN 站点到站点分支传输到支路 VNet|部署小时（0.25 美元/小时）+ VPN S2S 缩放单元（0.261 美元/小时）+ VPN S2S 连接单元（0.05 美元/小时）= 0.561 美元/小时|VNet 对等互连（入站）（0.01 美元/GB）= 0.01 美元/GB|
|2|通过标准 vWAN 中心将数据从 VPN 站点到站点分支传输到美国东部的另一个 VPN 站点到站点分支|部署小时（0.25 美元/小时）+ VPN S2S 缩放单元（0.261 美元/小时）+ VPN S2S 连接单元（2x0.05 美元/小时）= 0.611 美元/小时|标准出站区域 1（0.087 美元/GB）= 0.087 美元/GB|
|2'|通过标准 vWAN 中心将数据从 VPN 站点到站点分支传输到美国东部的 ExpressRoute 联网数据中心/总部|部署小时（0.25 美元/小时）+ VPN S2S 缩放单元（0.261 美元/小时）+ VPN S2S 连接单元（0.05 美元/小时）+ ExpressRoute 缩放单元（0.42 美元/小时）+ ExpressRoute 连接单元（0.05 美元/小时）= 1.03 美元/小时|ExpressRoute 按流量计费的出站区域 1（0.025 美元/GB）= 0.025 美元/GB|
|3|通过标准 vWAN 中心将数据从 VPN 站点到站点分支传输到美国东部的 ExpressRoute 联网数据中心/总部|部署小时（0.25 美元/小时）+ VPN S2S 缩放单元（0.261 美元/小时）+ VPN S2S 连接单元（0.05 美元/小时）+ ExpressRoute 缩放单元（0.42 美元/小时）+ ExpressRoute 连接单元（0.05 美元/小时）= 1.03 美元/小时|ExpressRoute 按流量计费的出站区域 1（0.025 美元/GB）= 0.025 美元/GB|
|4|通过美国东部的标准 vWAN 中心将数据从支路 VNet 传输到 ExpressRoute 联网数据中心/总部|部署小时（0.25 美元/小时）+ ExpressRoute 缩放单元（0.42 美元/小时）+ ExpressRoute 连接单元（0.05 美元/小时）= 0.72 美元/小时|VNet 对等互连（出站）（0.01 美元/GB）+ ExpressRoute 按流量计费的出站区域 1（0.025 美元/GB）= 0.035 美元/GB|
|4'|通过美国东部的标准 vWAN 中心将数据从 ExpressRoute 联网数据中心/总部传输到支路 VNet|部署小时（0.25 美元/小时）+ ExpressRoute 缩放单元（0.42 美元/小时）+ ExpressRoute 连接单元（0.05 美元/小时）= 0.72 美元/小时|VNet 对等互连（入站）（0.01 美元/GB）= 0.01 美元/GB|
|4"|通过欧洲的标准 vWAN 中心将数据从 ExpressRoute 联网数据中心/总部传输到远程支路 VNet|部署小时（2x0.25 美元/小时）+ ExpressRoute 缩放单元（0.42 美元/小时）+ ExpressRoute 连接单元（0.05 美元/小时）= 0.97 美元/小时|VNet 对等互连（入站）（0.01 美元/GB）+ 中心数据处理（欧洲）（0.02 美元/GB）+ 区域间数据传输（美国东部到欧洲）（0.05 美元/GB）= 0.08 美元/GB|
|5|通过美国东部的标准 vWAN 中心将数据从一个支路 VNet 传输到另一个支路 VNet|部署小时（0.25 美元/小时）= 0.25 美元/小时|VNet 对等互连（出站 + 入站）（2x0.01 美元/GB）+ 中心数据处理（0.02 美元/GB）= 0. 04 美元/GB|
|6|将数据从已连接到某个美国东部中心的支路 VNet 传输到欧洲（不同区域）的已连接到某个欧洲中心的另一个支路 VNet|部署小时（2x0.25 美元/小时）= 0.50 美元/小时|VNet 对等互连（出站 + 入站）（2x0.01 美元/GB）+ 中心数据处理（2x0.02 美元/GB）+ 区域间数据传输（美国东部到欧洲）（0.05 美元/GB）= 0. 11 美元/GB|
|7|通过欧洲的标准 vWAN 中心将数据从支路 VNet 传输到用户 VPN（点到站点）|部署小时（0.25 美元/小时）+ VPN P2S 缩放单元（0.261 美元/小时）+ VPN P2S 连接单元（0.0125 美元/小时）= 0.524 美元/小时|VNet 对等互连（出站）（0.01 美元/GB）+ 标准出站区域 1（0.087 美元/GB）= 0.097 美元/GB|
|7'|通过欧洲的标准 vWAN 中心将数据从用户 VPN（点到站点）传输到支路 VNet|部署小时（0.25 美元/小时）+ VPN P2S 缩放单元（0.261 美元/小时）+ VPN P2S 连接单元（0.0125 美元/小时）= 0.524 美元/小时|VNet 对等互连（入站）（0.01 美元/GB）= 0.01 美元/GB|
|8|通过位于新加坡中心的网络虚拟设备将数据从 SD-WAN 分支传输到同一区域的另一个 SD-WAN 分支|标准 vWAN 中心部署小时（0.25 美元/小时）+ NVA 基础结构单元（0.25 美元/小时）= 0.50 美元/小时<br> *可能需要支付额外的第三方许可费用|标准出站区域 2（0.12 美元/GB）= 0.12 美元/GB|

## <a name="common-topology-scenarios"></a><a name="topologies"></a>常见拓扑方案

### <a name="microsoft-global-backbone-wan"></a><a name="global"></a>Microsoft 全球主干 WAN

在此方案中，你将在全球范围内创建完全网格化的自动区域中心，这些区域中心将充当 Microsoft 主干网以用于全球流量连接。 典型的连接涉及到分支（站点到站点 VPN 或 SD-WAN）、远程用户（点到站点 VPN）和专用线路 (ExpressRoute) 等终结点。 这依赖于使用 Microsoft 主干网在全球传输流量。

方案 1 示意图：Microsoft 全球主干 WAN

:::image type="content" source="./media/pricing-concepts/global-backbone.png" alt-text="Microsoft 全球主干 WAN 示意图。" lightbox="./media/pricing-concepts/global-backbone.png":::

#### <a name="alternatives"></a>备选方法

* 还可以选择创建一个安全 vWAN 中心（包括防火墙），用作一个中心点来解决每个全球区域的路由和安全需求。

### <a name="software-defined-wide-area-network-sd-wan"></a><a name="sdwan"></a>软件定义的广域网 (SD-WAN)

在此方案中，迁移到 SD-WAN 技术的商店将使用 vWAN 中心来实现自动商店终止，以访问 Azure 上的资源并回头访问本地（数据中心）。 这些商店通过 VPN 隧道进行连接，在 Internet 上通过中心安全地将流量发送到本地数据中心，或用于访问 Azure 上的共享应用。

方案 2 示意图：软件定义的广域网 (SD-WAN)

:::image type="content" source="./media/pricing-concepts/sd-wan.png" alt-text="SD-WAN 示意图。" lightbox="./media/pricing-concepts/sd-wan.png":::

#### <a name="alternatives"></a>备选方法

* 可以选择在中心内使用第三方网络虚拟设备，并将其连接到零售店和中心。

* 还可以选择创建一个安全 vWAN 中心（包括防火墙），用作一个中心点来解决路由和安全需求。

### <a name="remote-user-connectivity"></a><a name="remote"></a>远程用户连接

在此方案中，远程用户会话在 vWAN 中心终止。 这些会话可以是来自虚拟网络的远程用户会话和/或 Azure 虚拟桌面会话。 目前，每个中心支持 10 万个用户。

下图显示了通过虚拟网络连接在支路 VNet 与 vWAN 中心之间跨隧道传输加密流量的点到站点 VPN。 还可以选择在不同支路 VNet 之间建立虚拟网络对等连接，以实现直接连接。 例如，在共享支路 VNet 与 VDI 支路 VNet 之间建立这种连接。

方案 3 示意图：远程用户连接

:::image type="content" source="./media/pricing-concepts/remote.png" alt-text="远程用户连接示意图。" lightbox="./media/pricing-concepts/remote.png":::

## <a name="data-flow-calculations"></a>数据流计算

> [!IMPORTANT]
> 本文中显示的定价仅用作示例，随时可能更改。 有关最新定价，请参阅[虚拟 WAN 定价](https://azure.microsoft.com/pricing/details/virtual-wan/)页。
>

### <a name="microsoft-global-backbone-wan-calculation"></a>Microsoft 全球主干 WAN 计算

在此方案中，我们假设总共有 8-TB 数据通过 vWAN 中心流经全球网络，如下图所示。 此方案的总数据传输成本为 600 美元（下图中所有数据流成本之和，假设 ExpressRoute 采用按流量计费），总中心成本（3 个缩放单元 + 3 个连接单元 (ER) + 3 个中心部署）为 1534 美元。

方案 1 示意图：Microsoft 全球主干 WAN 计算

:::image type="content" source="./media/pricing-concepts/global-backbone-pricing.png" alt-text="Microsoft 主干网定价示意图。" lightbox="./media/pricing-concepts/global-backbone-pricing.png":::

| 值 | 计算 |
| --- | --- |
|新加坡 S2S VPN 中心 |(1 个 S2S VPN 缩放单元 (0.361 美元/小时) + 1 个连接单元 (0.05 美元/小时)) x 730 小时 = 300 美元/月|
|美国 ExpressRoute 中心 |(1 个 ER 缩放单元 (0.42 美元/小时) + 1 个连接单元 (0.05 美元/小时)) x 730 小时 = 343 美元/月|
|欧洲 ExpressRoute 中心|(1 个 ER 缩放单元 (0.42 美元/小时) + 1 个连接单元 (0.05 美元/小时)) x 730 小时 = 343 美元/月|
|标准中心部署成本 |3 个中心 x 730 小时 x 0.25 美元/小时 = 548 美元/月|
|**总计**|1534 美元/月 |

### <a name="software-defined-wide-area-network-sd-wan-calculation"></a>软件定义的广域网 (SD-WAN) 计算

在此方案中，我们假设美国东部区域总共有 12-TB 数据通过 vWAN 中心传输，如下图所示。 数据传输总成本为 434 美元（下面显示的所有数据流成本之和；包括中心处理费、对等连接、带宽、按流量计费的 ER 数据传输成本），总中心成本（2 个缩放单位 + 3 个连接单元（2 个 S2S、1 个 ER）+ 1 个中心部署）为 863 美元。

方案 2 示意图：软件定义的广域网 (SD-WAN) 计算

:::image type="content" source="./media/pricing-concepts/sd-wan-pricing.png" alt-text="SD-WAN 定价示意图。" lightbox="./media/pricing-concepts/sd-wan-pricing.png":::

| 值 | 计算 |
| --- | --- |
|VPN S2S（分支） |(1 个 S2S VPN 缩放单元 (0.361 美元/小时) + 2 个连接单元 (2x0.05 美元/小时)) x 730 小时 = 337 美元/月|
|ExpressRoute 中心（总部） |(1 个 ER 缩放单元 (0.42 美元/小时) + 1 个连接单元 (0.05 美元/小时)) x 730 小时 = 343 美元/月|
|标准中心部署成本|1 个中心 x 730 小时 x 0.25 美元/小时 = 183 美元/月|
|**总计**|863 美元/月|

### <a name="remote-user-connectivity-calculation"></a>远程用户连接计算

在此方案中，我们假设美国东部区域总共有 15-TB 数据流经网络。 总数据传输成本为 405 美元（包括中心处理费、对等连接、带宽、按流量计费的 ER 数据传输费），总中心成本（2 个缩放单元 + 2 个连接单元 + 2 个中心部署）为 708 美元。

方案 3 示意图：远程用户连接计算
    
:::image type="content" source="./media/pricing-concepts/remote-pricing.png" alt-text="远程用户定价示意图。" lightbox="./media/pricing-concepts/remote-pricing.png":::

| 值 | 计算 |
| --- | --- |
|ExpressRoute 中心（总部） |(1 个 ER 缩放单元 (0.42 美元/小时) + 1 个连接单元 (0.05 美元/小时)) x 730 小时 = 343 美元/月 |
|标准中心部署成本| 2 个中心 x 730 小时 x 0.25 美元/小时 = 365 美元/月 |
|**总计** |708 美元/月 |

## <a name="pricing-faq"></a>定价常见问题解答

> [!IMPORTANT]
> 本文中显示的定价仅用作示例，随时可能更改。 有关最新定价，请参阅[虚拟 WAN 定价](https://azure.microsoft.com/pricing/details/virtual-wan/)页。
>

### <a name="what-is-a-scale-unit"></a><a name="scale-unit"></a>什么是缩放单元？

缩放单元提供了虚拟中心内站点到站点 (S2S)、点到站点 (P2S) 以及 ExpressRoute (ER) 的聚合容量单元。 例如：

* 1 个 S2S VPN 缩放单元意味着，虚拟中心内有一个总容量为 500-Mbps 的 VPN 网关（部署了双实例以提供复原能力），成本为 0.361 美元/小时。

* 1 个 ER 缩放单元意味着，虚拟中心内有一个总容量为 2-Gbps 的 ER 网关，成本为 0.42 美元/小时。

* 5 个 ER 缩放单元意味着，虚拟中心 VNet 内有一个总容量为 10-Gbps 的 ER 网关，价格为 0.42 美元*5/小时。 从第 6 个缩放单元到第 10 个缩放单元的 ER 递增量为 0.25 美元/小时。

### <a name="what-is-a-connection-unit"></a><a name="connection-unit"></a>什么是连接单元？

连接单元适用于连接到 Azure 网关的任何本地/非 Microsoft 终结点。 对于站点到站点 VPN，此值表示分支。 对于用户 VPN（点到站点），此值表示远程用户。 对于 ExpressRoute，此值表示 ExpressRoute 线路连接。<br>例如：

* 连接到虚拟中心内的 Azure VPN 的一个分支连接的成本为 0.05 美元/小时。 因此，连接到 Azure 虚拟中心的 100 个分支连接的成本为 0.05 美元*100/小时。

* 连接到虚拟中心的两个 ExpressRoute 线路连接的成本为 0.05 美元*2/小时。

* 连接到 Azure 虚拟中心 P2S 网关的三个远程用户连接的成本为 0.03 美元*3/小时。

### <a name="how-are-data-transfer-charges-calculated"></a><a name="data-transfer"></a>数据传输费用是如何计算的？

* 进入 Azure 的任何流量都不收费。 离开 Azure 的流量（通过 VPN、ExpressRoute，或点到站点用户 VPN 连接）需要支付标准 [Azure 数据传输费用](https://azure.microsoft.com/pricing/details/bandwidth/)，使用 ExpressRoute 时则按 [ExpressRoute 定价](https://azure.microsoft.com/pricing/details/expressroute/)支付。

* 当已连接到 vWAN 中心的 VNet 发送或接收数据时，将收取对等互连费用。 有关详细信息，请参阅[虚拟网络定价](https://azure.microsoft.com/pricing/details/virtual-network/)。

* 在虚拟 WAN 中心与远程虚拟 WAN 中心之间或者与源中心所在区域之外的区域中的 VNet 之间进行数据传输时，离开中心的流量会产生数据传输费用。 示例：对于离开“美国东部”中心前往“美国西部”中心的流量，将按 0.02 美元/GB 计费。 进入“美国西部”中心的流量不收费。 所有中心到中心流量都需要支付区域间（洲内/洲际）[Azure 数据传输费用](https://azure.microsoft.com/pricing/details/bandwidth/)。 

### <a name="what-is-the-difference-between-a-standard-hub-fee-and-a-standard-hub-processing-fee"></a><a name="fee"></a>标准中心费用与标准中心处理费用的区别是什么？

虚拟 WAN 有两种形式：

* 基本虚拟 WAN，用户可在其中部署多个中心并使用 VPN 站点到站点连接。 基本虚拟 WAN 没有高级功能，例如完全网格化中心、ExpressRoute 连接、用户 VPN/点到站点 VPN 连接、VNet 到 VNet 传递性连接、VPN 和 ExpressRoute 传输连接或 Azure 防火墙。 基本虚拟 WAN 中的中心不产生基本费用或数据处理费用。

* 标准虚拟 WAN 提供了高级功能，例如完全网格化中心、ExpressRoute 连接、用户 VPN/点到站点 VPN 连接、VNet 到 VNet 传递连接、VPN 和 ExpressRoute 传输连接、Azure 防火墙等。所有虚拟中心路由都由在虚拟中心内启用多个服务的路由器提供。 中心会产生基本费用，价格为 0.25 美元/小时。 对于 VNet 到 VNet 传输连接，还会对虚拟中心路由器中的数据处理收费。 如[定价组件](#pricing)中所示，虚拟中心路由器中的数据处理费用不适用于分支到分支传输（方案 2、2'、3），或通过同一 vWAN 中心进行的 VNet 到分支传输（方案 1、1'）。 

## <a name="next-steps"></a>后续步骤

* 有关当前定价，请参阅[虚拟 WAN 定价](https://azure.microsoft.com/pricing/details/virtual-wan/)。

* 有关虚拟 WAN 的详细信息，请参阅[常见问题解答](virtual-wan-faq.md)。


