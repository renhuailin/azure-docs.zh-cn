---
title: 如何在 Azure VPN 网关上配置 OpenVPN
description: 了解如何使用 PowerShell 在 Azure VPN 网关上为点到站点环境启用 OpenVPN 协议。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/05/2021
ms.author: cherylmc
ms.openlocfilehash: 4b48e98ab35b620030b15165d4c9341c0f11b440
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100393269"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway"></a>为 Azure 点到站点 VPN 网关配置 OpenVPN

本文可帮助你在 Azure VPN 网关上设置 **OpenVPN® 协议**。 可以使用门户或 PowerShell 说明。

## <a name="prerequisites"></a>先决条件

* 本文假设你已拥有一个可正常工作的点到站点环境。 如果不这样做，请使用以下方法之一创建一个。

  * [门户-创建点到站点](vpn-gateway-howto-point-to-site-resource-manager-portal.md)

  * [PowerShell-创建点到站点](vpn-gateway-howto-point-to-site-rm-ps.md)

* 验证 VPN 网关不使用基本 SKU。 OpenVPN 不支持基本 SKU。

## <a name="portal"></a>门户

1. 在门户中，导航到 **虚拟网络网关 > 点到站点配置**。
1. 对于 " **隧道类型**"，请从下拉列表中选择 " **OpenVPN (SSL")** 。

   :::image type="content" source="./media/vpn-gateway-howto-openvpn/portal.png" alt-text="从下拉列表中选择 &quot;OpenVPN SSL&quot;":::
1. 保存更改并继续 **后续步骤**。

## <a name="enable-openvpn-on-your-gateway-using-powershell"></a>使用 PowerShell 在网关上启用 OpenVPN。

1. 使用以下示例在网关上启用 OpenVPN：

   ```azurepowershell-interactive
   $gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
   Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
   ```
1. 继续执行 **后续步骤**。

## <a name="next-steps"></a>后续步骤

若要为 OpenVPN 配置客户端，请参阅[配置 OpenVPN 客户端](vpn-gateway-howto-openvpn-clients.md)。

**“OpenVPN”是 OpenVPN Inc. 的商标。**
