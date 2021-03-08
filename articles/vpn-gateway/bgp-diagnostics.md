---
title: 查看 BGP 状态和指标
titleSuffix: Azure VPN Gateway
description: 查看与 BGP 相关的重要信息以进行故障排除。
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.devlang: powershell
ms.topic: sample
ms.date: 02/22/2021
ms.author: alzam
ms.openlocfilehash: 097568dddd5c8568d4523cdeb05ab0c889c31670
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101742773"
---
# <a name="view-bgp-metrics-and-status-using-powershell"></a>使用 PowerShell 查看 BGP 指标和状态

使用 Get-AzVirtualNetworkGatewayBGPPeerStatus 查看所有 BGP 对等体和状态

[!INCLUDE [VPN Gateway PowerShell instructions](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayBgpPeerStatus -ResourceGroupName resourceGroup -VirtualNetworkGatewayName gatewayName

Asn               : 65515
ConnectedDuration : 9.01:04:53.5768637
LocalAddress      : 10.1.0.254
MessagesReceived  : 14893
MessagesSent      : 14900
Neighbor          : 10.0.0.254
RoutesReceived    : 1
State             : Connected
```

使用 Get-AzVirtualNetworkGatewayLearnedRoute 查看网关通过 BGP 获悉的所有路由。

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayLearnedRoute -ResourceGroupName resourceGroup -VirtualNetworkGatewayname gatewayName

AsPath       :
LocalAddress : 10.1.0.254
Network      : 10.1.0.0/16
NextHop      :
Origin       : Network
SourcePeer   : 10.1.0.254
Weight       : 32768

AsPath       :
LocalAddress : 10.1.0.254
Network      : 10.0.0.254/32
NextHop      :
Origin       : Network
SourcePeer   : 10.1.0.254
Weight       : 32768

AsPath       : 65515
LocalAddress : 10.1.0.254
Network      : 10.0.0.0/16
NextHop      : 10.0.0.254
Origin       : EBgp
SourcePeer   : 10.0.0.254
Weight       : 32768
```

使用 Get-AzVirtualNetworkGatewayAdvertisedRoute 查看网关通过 BGP 播发到其对等体的所有路由。

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName gatewayName -ResourceGroupName resourceGroupName -Peer 10.0.0.254
```

## <a name="clean-up-resources"></a>清理资源

如果不再需要所创建的资源，请使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 命令删除资源组。 该命令将删除资源组及其包含的所有资源。

```azurepowershell-interactive
Remove-AzResourceGroup -Name ResourceGroupName
```

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/)。
