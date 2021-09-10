---
title: 通过 ExpressRoute 专用对等互连建立的站点到站点 VPN 连接
titleSuffix: Azure VPN Gateway
description: 了解如何通过 ExpressRoute 专用对等互连配置站点到站点 VPN 连接来对流量进行加密。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 04/28/2021
ms.author: cherylmc
ms.openlocfilehash: 68dee7e8d00f3f1b6853a7be5c9328647d96371d
ms.sourcegitcommit: 7b6ceae1f3eab4cf5429e5d32df597640c55ba13
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123272072"
---
# <a name="configure-a-site-to-site-vpn-connection-over-expressroute-private-peering"></a>通过 ExpressRoute 专用对等互连配置站点到站点 VPN 连接

可使用 RFC 1918 IP 地址通过 ExpressRoute 专用对等互连配置连接到虚拟网络网关的站点到站点 VPN。 该配置具有以下优势：

* 通过专用对等互连的流量已经过加密。

* 连接到虚拟网络网关的点到站点用户可使用 ExpressRoute（通过站点到站点隧道）访问本地资源。

* 可以通过 ExpressRoute 专用对等互连部署站点到站点 VPN 连接，同时通过同一 VPN 网关上的 Internet 部署站点到站点 VPN 连接。

>[!NOTE]
>此功能仅在区域冗余网关上受支持。 例如，VpnGw1AZ、VpnGw2AZ 等。
>

若要完成此配置，请验证是否满足以下先决条件：

* 有一个正常运行的 ExpressRoute 线路，该线路链接到在其中（或将在其中）创建 VPN 网关的 VNet。

* 可以通过 ExpressRoute 线路访问 VNet 中 RFC1918（专用）IP 上的资源。

## <a name="routing"></a><a name="routing"></a>路由

“图 1”显示了通过 ExpressRoute 专用对等互连建立的 VPN 连接的示例。 在此示例中，你将看到本地网络中通过 ExpressRoute 专用对等互连连接到 Azure 中心 VPN 网关的网络。 此配置的一个重要方面是通过 ExpressRoute 和 VPN 路径在本地网络与 Azure 之间进行路由。

图 1

:::image type="content" source="media/site-to-site-vpn-private-peering/routing.png" alt-text="图 1":::

建立连接非常简单：

1. 与 ExpressRoute 线路和专用对等互连建立 ExpressRoute 连接。

1. 使用本文中的步骤建立 VPN 连接。

### <a name="traffic-from-on-premises-networks-to-azure"></a>从本地网络发往 Azure 的流量

对于从本地网络发往 Azure 的流量，Azure 前缀通过 ExpressRoute 专用对等互连 BGP 和 VPN BGP 进行播发。 这会建立从本地网络发往 Azure 的两个网络路由（路径）：

•一个通过 IPsec 保护路径建立的网络路由。

•一个不受 IPsec 保护的通过 ExpressRoute 直接建立的网络路由

若要将加密应用于通信，必须确保对于“图 1”中所示的已连接 VPN 的网络，优先使用通过本地 VPN 网关建立的 Azure 路由，而不是通过直接 ExpressRoute 路径建立的路由。

### <a name="traffic-from-azure-to-on-premises-networks"></a>从 Azure 发往本地网络的流量

相同的要求适用于从 Azure 发往本地网络的流量。 为了确保优先使用 IPsec 路径而不是直接 ExpressRoute 路径（不受 IPsec 保护），可以采用两种做法：

• “在已连接 VPN 的网络的 VPN BGP 会话中播发更具体的前缀”。 可以通过 ExpressRoute 专用对等互连播发包含已连接 VPN 的网络的更大范围，然后在 VPN BGP 会话中播发更具体的范围。 例如，通过 ExpressRoute 播发 10.0.0.0/16，通过 VPN 播发 10.0.1.0/24。

• “为 VPN 和 ExpressRoute 播发不相交的前缀”。 如果已连接 VPN 的网络范围与已连接 ExpressRoute 的其他网络不相交，则可以分别在 VPN 和 ExpressRoute BGP 会话中播发这些前缀。 例如，通过 ExpressRoute 播发 10.0.0.0/24，通过 VPN 播发 10.0.1.0/24。

在这两个示例中，Azure 将通过 VPN 连接将流量发送到 10.0.1.0/24，而不是直接通过不受 VPN 保护的 ExpressRoute 发送。

>[!Warning]
>如果通过 ExpressRoute 和 VPN 连接播发相同的前缀，Azure 将直接使用不受 VPN 保护的 ExpressRoute 路径。
>

## <a name="portal-steps"></a><a name="portal"></a>门户步骤

1. 配置站点到站点连接。 有关步骤，请参阅[站点到站点配置](./tutorial-site-to-site-portal.md)一文。 务必为网关选择区域冗余网关 SKU。 

   区域冗余 SKU 在 SKU 末尾有“AZ”。 例如，“VpnGw1AZ”。 区域冗余网关仅适用于可用性区域服务可用的区域。 有关支持可用性区域的区域信息，请参阅[支持可用性区域的区域](../availability-zones/az-region.md)。

   :::image type="content" source="media/site-to-site-vpn-private-peering/gateway.png" alt-text="网关专用 IP":::
1. 在网关上启用专用 IP。 选择“配置”，并将“网关专用 IP”设置为“启用”。   选择“保存”以保存更改。
1. 在“概述”页上，选择“查看更多”来查看专用 IP 地址。  记下此信息，以便稍后在配置步骤中使用。

   :::image type="content" source="media/site-to-site-vpn-private-peering/gateway-overview.png" alt-text="概述页" lightbox="media/site-to-site-vpn-private-peering/gateway-overview.png":::
1. 若要在连接上启用“使用 Azure 专用 IP 地址”，选择“配置”。  将“使用 Azure 专用 IP 地址”设置为“启用”，并选择“保存”。  

   :::image type="content" source="media/site-to-site-vpn-private-peering/connection.png" alt-text="网关专用 IP - 已启用":::
1. 在防火墙中，对在步骤 3 中记下的专用 IP 进行 ping 操作。 可通过 ExpressRoute 专用对等互连来访问专用 IP。
1. 使用此专用 IP 作为本地防火墙上的远程 IP，以通过 ExpressRoute 专用对等互连建立站点到站点隧道。

## <a name="powershell-steps"></a><a name="powershell"></a>PowerShell 步骤

1. 配置站点到站点连接。 有关步骤，请参阅[配置站点到站点 VPN](./tutorial-site-to-site-portal.md)一文。 务必为网关选择区域冗余网关 SKU。 区域冗余 SKU 在 SKU 末尾有“AZ”。 例如，VpnGw1AZ。
1. 使用以下 PowerShell 命令将标志设置为在网关上使用专用 IP：

   ```azurepowershell-interactive
   $Gateway = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroup <name of resource group>

   Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway -EnablePrivateIpAddress $true
   ```

   应该会看到一个公用和一个专用 IP 地址。 写入输出的“TunnelIpAddresses”部分下的 IP 地址。 稍后的步骤需要用到此信息。
1. 使用以下 PowerShell 命令将连接设置为使用专用 IP 地址：

   ```azurepowershell-interactive
   $Connection = get-AzVirtualNetworkGatewayConnection -Name <name of the connection> -ResourceGroupName <name of resource group>

   Set-AzVirtualNetworkGatewayConnection --VirtualNetworkGatewayConnection $Connection -UseLocalAzureIpAddress $true
   ```
1. 在防火墙中，对在步骤 2 中记下的专用 IP 进行 ping 操作。 可通过 ExpressRoute 专用对等互连来访问专用 IP。
1. 使用此专用 IP 作为本地防火墙上的远程 IP，以通过 ExpressRoute 专用对等互连建立站点到站点隧道。

## <a name="next-steps"></a>后续步骤

有关 VPN 网关的详细信息，请参阅[什么是 VPN 网关？](vpn-gateway-about-vpngateways.md)
