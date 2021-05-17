---
title: 点到站点 VPN 会话管理
titleSuffix: Azure VPN Gateway
description: 了解如何查看点到站点 VPN 会话以及断开其连接。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 04/26/2021
ms.author: cherylmc
ms.openlocfilehash: 79432bfd65feeae79017a883be990d88134cbb10
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108202526"
---
# <a name="point-to-site-vpn-session-management"></a>点到站点 VPN 会话管理

可以通过 Azure 虚拟网络网关轻松查看当前的点到站点 VPN 会话并断开其连接。 可以根据本文的要求来查看当前会话并断开其连接。 会话状态将每 5 分钟更新一次。 它不会立即更新。 


## <a name="portal"></a>门户

>[!NOTE]
> 仅对于 IKEv2 和 OpenVPN 连接才会提供连接源信息。
> 

若要在门户中查看会话并断开其连接，请执行以下操作：

1. 导航到 VPN 网关。
1. 在“监视”部分，选择“点到站点会话” 。

   :::image type="content" source="./media/p2s-session-management/portal.png" alt-text="门户示例":::
1. 可以查看窗口窗格中的所有当前会话。
1. 对于要断开连接的会话，请选择“…”，然后选择“断开连接”。

目前，无法在 VpnGw4 和 VpnGw5 SKU 的门户中使用此功能。 如果你使用其中某一网关，请使用下一节介绍的 PowerShell 方法。

## <a name="powershell"></a>PowerShell

若要使用 PowerShell 查看某个会话及断开其连接，请执行以下操作：

1. 运行以下 PowerShell 命令以列出活动会话：

   ```azurepowershell-interactive
   Get-AzVirtualNetworkGatewayVpnClientConnectionHealth -VirtualNetworkGatewayName <name of the gateway>  -ResourceGroupName <name of the resource group>
   ```
1. 复制要断开连接的会话的 VpnConnectionId。

   :::image type="content" source="./media/p2s-session-management/powershell.png" alt-text="PowerShell 示例":::
1. 若要断开会话的连接，请运行以下命令：

   ```azurepowershell-interactive
   Disconnect-AzVirtualNetworkGatewayVpnConnection -VirtualNetworkGatewayName <name of the gateway> -ResourceGroupName <name of the resource group> -VpnConnectionId <VpnConnectionId of the session>
   ```

## <a name="next-steps"></a>后续步骤

有关点到站点连接的详细信息，请参阅[关于点到站点 VPN](point-to-site-about.md)。
