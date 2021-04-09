---
title: 查看 BGP 状态和指标
titleSuffix: Azure VPN Gateway
description: 查看与 BGP 相关的重要信息以进行故障排除。
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: sample
ms.date: 03/10/2021
ms.author: alzam
ms.openlocfilehash: f97bbccc980705699af822ba2730233239cdfd5f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "103148786"
---
# <a name="view-bgp-metrics-and-status"></a>查看 BGP 指标和状态

可以使用 Azure 门户或 Azure PowerShell 来查看 BGP 指标和状态。

## <a name="azure-portal"></a>Azure 门户

在 Azure 门户中，可以查看 BGP 对等方、已获知的路由和已播发的路由。 还可以下载包含此数据的 .csv 文件。

1. 在 [Azure 门户](https://portal.azure.com)中导航到你的虚拟网络网关。
1. 在“监视”下，选择“BGP 对等方”打开“BGP 对等方”页。 

   :::image type="content" source="./media/bgp-diagnostics/bgp-portal.jpg" alt-text="Azure 门户中的指标屏幕截图。":::

### <a name="learned-routes"></a>已获知的路由

1. 在门户中最多可以查看 50 个已获知的路由。

   :::image type="content" source="./media/bgp-diagnostics/learned-routes.jpg" alt-text="已获知的路由的屏幕截图。":::

1. 还可以下载已获知的路由文件。 如果已获知的路由超过 50 个，查看所有这些路由的唯一方法就是下载并查看 .csv 文件。 若要下载，请选择“下载已获知的路由”。

   :::image type="content" source="./media/bgp-diagnostics/download-routes.jpg" alt-text="下载已获知的路由的屏幕截图。":::
1. 然后查看文件。

   :::image type="content" source="./media/bgp-diagnostics/learned-routes-file.jpg" alt-text="下载的已获知路由的屏幕截图。":::

### <a name="advertised-routes"></a>已播发的路由

1. 若要查看已播发的路由，请选择要查看的网络末尾的“...”，然后单击“查看已播发的路由”。 

   :::image type="content" source="./media/bgp-diagnostics/select-route.png" alt-text="展示如何查看已播发的路由的屏幕截图。" lightbox="./media/bgp-diagnostics/route-expand.png":::
1. 在“已播发到对等方的路由”页上，最多可以查看 50 个已播发的路由。

   :::image type="content" source="./media/bgp-diagnostics/advertised-routes.jpg" alt-text="已播发的路由的屏幕截图。":::
1. 还可以下载已播发的路由文件。 如果已播发的路由超过 50 个，查看所有这些路由的唯一方法就是下载并查看 .csv 文件。 若要下载，请选择“下载已播发的路由”。

   :::image type="content" source="./media/bgp-diagnostics/download-advertised.jpg" alt-text="选择下载的已播发路由的屏幕截图。":::
1. 然后查看文件。

   :::image type="content" source="./media/bgp-diagnostics/advertised-routes-file.jpg" alt-text="下载的已播发路由的屏幕截图。":::

### <a name="bgp-peers"></a>BGP 对等方

1. 在门户中最多可以查看 50 个 BGP 对等方。

   :::image type="content" source="./media/bgp-diagnostics/peers.jpg" alt-text="BGP 对等方的屏幕截图。":::
1. 还可以下载 BGP 对等方文件。 如果 BGP 对等方超过 50 个，查看所有这些 BGP 对等方的唯一方法就是下载并查看 .csv 文件。 若要下载，请在门户页上选择“下载 BGP 对等方”。

   :::image type="content" source="./media/bgp-diagnostics/download-peers.jpg" alt-text="下载 BGP 对等方的屏幕截图。":::
1. 然后查看文件。

   :::image type="content" source="./media/bgp-diagnostics/peers-file.jpg" alt-text="下载的 BGP 对等方的屏幕截图。":::

## <a name="powershell"></a>PowerShell

使用 **Get-AzVirtualNetworkGatewayBGPPeerStatus** 查看所有 BGP 对等方和状态。

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

## <a name="next-steps"></a>后续步骤

有关 BGP 的详细信息，请参阅[为 VPN 网关配置 BGP](bgp-howto.md)。
