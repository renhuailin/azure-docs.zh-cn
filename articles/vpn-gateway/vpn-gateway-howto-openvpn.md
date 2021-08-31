---
title: 如何为 P2S VPN 网关启用 OpenVPN
titleSuffix: Azure VPN Gateway
description: 了解如何为点对站点配置在 VPN 网关上启用 OpenVPN 协议。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 07/28/2021
ms.author: cherylmc
ms.openlocfilehash: ab7826a89dc879c1bad62e8c56415047d164c6f9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729494"
---
# <a name="configure-openvpn-for-point-to-site-vpn-gateways"></a>为点到站点 VPN 网关配置 OpenVPN

本文可帮助你在 Azure VPN 网关上设置 **OpenVPN® 协议**。 可以使用门户或 PowerShell 说明。

## <a name="prerequisites"></a>先决条件

* 本文假设你已拥有一个可正常工作的点到站点环境。 如果没有，请使用下列方法之一创建一个。 在创建网关时请注意，“基本”SKU 不支持 OpenVPN 隧道类型。

  * [门户 - 创建点到站点](vpn-gateway-howto-point-to-site-resource-manager-portal.md)

  * [PowerShell - 创建点到站点](vpn-gateway-howto-point-to-site-rm-ps.md)

* 如果已经有一个 VPN 网关，请确认网关没有使用“基本”SKU。 OpenVPN 不支持基本 SKU。 有关 SKU 的详细信息，请参阅 [VPN 网关配置设置](vpn-gateway-about-vpn-gateway-settings.md)。 若要调整基本 SKU 的大小，请参阅[调整旧网关的大小](vpn-gateway-about-skus-legacy.md#resource-manager)。 

## <a name="portal"></a>门户

1. 在门户中，导航至“虚拟网络网关”->“点到站点配置”。
1. 对于“隧道类型”，请从下拉列表中选择“OpenVPN (SSL)” 。

   :::image type="content" source="./media/vpn-gateway-howto-openvpn/portal.png" alt-text="从下拉列表中选择“OpenVPN SSL”":::
1. 保存所做的更改，然后执行后续步骤。

## <a name="powershell"></a>PowerShell

1. 使用以下示例在网关上启用 OpenVPN，并根据需要调整值。

   ```azurepowershell-interactive
   $gw = Get-AzVirtualNetworkGateway -ResourceGroupName TestRG1 -name VNet1GW
   Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
   ```
1. 继续执行后续步骤。

## <a name="next-steps"></a>后续步骤

若要为 OpenVPN 配置客户端，请参阅[配置 OpenVPN 客户端](vpn-gateway-howto-openvpn-clients.md)。

**“OpenVPN”是 OpenVPN Inc. 的商标。**
