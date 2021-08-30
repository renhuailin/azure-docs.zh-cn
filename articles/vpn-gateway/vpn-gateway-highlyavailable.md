---
title: 关于高可用网关配置
titleSuffix: Azure VPN Gateway
description: 了解使用 Azure VPN 网关的高可用配置选项。
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 05/27/2021
ms.author: yushwang
ms.openlocfilehash: 82a1ad0968a80be1ff1780b1cf83ec928a016dbb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729534"
---
# <a name="highly-available-cross-premises-and-vnet-to-vnet-connectivity"></a>高可用性跨界连接与 VNet 到 VNet 连接

本文概述使用 Azure VPN 网关的跨界连接和 VNet 到 VNet 连接的高可用性配置选项。

## <a name="about-vpn-gateway-redundancy"></a><a name = "activestandby"></a>关于 VPN 网关冗余

每个 Azure VPN 网关由两个采用主动-待机配置的实例组成。 当主动实例发生任何计划内维护或计划外中断时，待机实例会自动接管负载（故障转移），恢复 S2S VPN 连接或 VNet 到 VNet 连接。 这种交接会造成短暂的中断。 发生计划内维护时，10 到 15 秒内应可恢复连接。 发生计划外的问题时，恢复连接所需的时间更长，在最糟的情况下大约需要 1 到 3 分钟。 对于 P2S VPN 客户端到网关的连接，会断开 P2S 连接，并且用户需要从客户端计算机重新连接。

:::image type="content" source="./media/vpn-gateway-highlyavailable/active-standby.png" alt-text="此图中显示了一个本地站点，其中的专用 IP 子网和本地 VPN 通过连接到一个活动 Azure VPN 网关来连接到 Azure 中托管的子网，并有一个可用的备用网关。" border="false":::

## <a name="highly-available-cross-premises"></a>高可用性跨界

若要为跨界连接提供更高的可用性，可以使用以下选项：

* 多个本地 VPN 设备
* 主动-主动 Azure VPN 网关
* 两者的组合

### <a name="multiple-on-premises-vpn-devices"></a><a name = "activeactiveonprem"></a>多个本地 VPN 设备

可以使用本地网络中的多个 VPN 设备连接到 Azure VPN 网关，如下图所示：

:::image type="content" source="./media/vpn-gateway-highlyavailable/multiple-onprem-vpns.png" alt-text="此图显示了多个具有专用 IP 子网和本地 V P N 的本地站点，其中的子网和 V P N 为连接到 Azure 中托管的子网而与某个活动 Azure V P N 网关连接，并提供了备用网关。" border="false":::

此配置提供多个活动隧道用于从同一个 Azure VPN 网关连接到同一位置中的本地设备。 此配置有一些要求和限制：

1. 需要创建从 VPN 设备到 Azure 的多个 S2S VPN 连接。 将多个 VPN 设备从同一本地网络连接到 Azure 时，需要为每个 VPN 设备创建一个本地网络网关，以及一个从 Azure VPN 网关到每个本地网络网关的连接。
1. 对应于 VPN 设备的本地网络网关在“GatewayIpAddress”属性中必须有唯一的公共 IP 地址。
1. 此配置需要 BGP。 必须在“BgpPeerIpAddress”属性中为代表 VPN 设备的每个本地网络网关指定唯一的 BGP 对等 IP 地址。
1. 应使用 BGP 向 Azure VPN 网关播发同一本地网络的相同前缀，流量将同时通过这些隧道转发。
1. 必须使用等价多路径路由 (ECMP)。
1. 每个连接将计入 Azure VPN 网关的隧道数目上限，基本和标准 SKU 的上限为 10，高性能 SKU 的上限为 30。 

在此配置中，Azure VPN 网关仍处于主动-待机模式，因此，仍会发生[上述](#activestandby)故障转移行为和短暂中断。 但是，这种设置可针对本地网络和 VPN 设备故障或中断提供保护。

### <a name="active-active-vpn-gateways"></a>主动-主动 VPN 网关

可以在主动-主动配置中创建一个 Azure VPN 网关，其中的两个网关 VM 实例将与本地 VPN 设备建立 S2S VPN 隧道，如下图所示：

:::image type="content" source="./media/vpn-gateway-highlyavailable/active-active.png" alt-text="此图显示了一个本地站点，其中的专用 IP 子网和本地 VPN 通过连接到两个活动的 Azure VPN 网关来连接到 Azure 中托管的子网。" border="false":::

在此配置中，每个 Azure 网关实例都有唯一的公共 IP 地址，每个实例将与本地网络网关和连接中指定的本地 VPN 设备建立 IPsec/IKE S2S VPN 隧道。 请注意，这两个 VPN 隧道实际上属于同一个连接。 仍然需要配置本地 VPN 设备，以便与这两个 Azure VPN 网关公共 IP 地址建立两条 S2S VPN 隧道，或者接受这种通道。

由于 Azure 网关实例采用主动-主动配置，因此，从 Azure 虚拟网络到本地网络的流量同时通过这两条隧道路由，即使本地 VPN 设备优先选择其中一个隧道，也是如此。 对于单个 TCP 或 UDP 流，Azure 在将数据包发送到本地网络时会尝试使用同一隧道。 但是，本地网络可以使用其他隧道将数据包发送到 Azure。

当一个网关实例发生计划内维护或计划外事件时，从该实例到本地 VPN 设备的 IPsec 隧道会断开。 VPN 设备上的对应路由应会自动删除或撤消，以便将流量切换到其他活动 IPsec 隧道。 在 Azure 端，会自动从受影响的实例切换到活动实例。

### <a name="dual-redundancy-active-active-vpn-gateways-for-both-azure-and-on-premises-networks"></a>双重冗余：Azure 和本地网络的主动-主动 VPN 网关

最可靠的选项是结合网络和 Azure 上的主动-主动网关，如下图所示。

:::image type="content" source="./media/vpn-gateway-highlyavailable/dual-redundancy.png" alt-text="此图显示了双重冗余方案。" border="false":::

此处创建并设置了采用主动-主动配置的 Azure VPN 网关，并针对上述两个本地 VPN 设备创建了两个本地网络网关和两个连接。 结果是在 Azure 虚拟网络与本地网络之间建立了包含 4 个 IPsec 隧道的全面网格连接。

所有网关和隧道从 Azure 端激活，因此流量同时分散在 4 个隧道之间，每个 TCP 或 UDP 流量再次沿着源自 Azure 端的相同隧道或路径传送。 即使通过分发流量，你仍可能通过 IPsec 隧道看到略微更高的吞吐量，此配置的主要目标是实现高可用性。 由于分散的统计特征，难以通过某种测量方式来确定不同的应用程序流量状况对聚合吞吐量造成的影响。

此拓扑需要使用两个本地网络网关和两个连接来支持本地 VPN 设备对，需要使用 BGP 来与同一个本地网络建立两个连接。 [上述](#activeactiveonprem)配置同样需要满足这些要求。 

## <a name="highly-available-vnet-to-vnet"></a>高可用性 VNet 到 VNet 

上述主动-主动配置同样适用于 Azure VNet 到 VNet 连接。 可为两个虚拟网络创建主动-主动 VPN 网关并将它们连接到一起，同样在两个 VNet 之间构成包含 4 个隧道的全面网格连接，如下图所示：

:::image type="content" source="./media/vpn-gateway-highlyavailable/vnet-to-vnet.png" alt-text="此图显示了两个托管专用 IP 子网的 Azure 区域和两个 Azure VPN 网关，两个虚拟站点通过这两个网关进行连接。" border="false":::

这可以确保用于任何计划内维护事件的两个虚拟网络之间始终有一对隧道，进一步提高可用性。 尽管用于跨界连接的相同拓扑需要两个连接，但如上所示的 VNet 到 VNet 拓扑只需要为每个网关建立一个连接。 此外，除非基于 VNet 到 VNet 连接的传输路由是必需的，否则 BGP 是可选的。

## <a name="next-steps"></a>后续步骤
请参阅使用 [Azure 门户](active-active-portal.md)或 [PowerShell](vpn-gateway-activeactive-rm-powershell.md) 来[配置主动-主动网关](active-active-portal.md)。

