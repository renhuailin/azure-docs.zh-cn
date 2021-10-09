---
title: 快速入门：使用 Azure PowerShell 创建并配置路由服务器
description: 这篇快速入门文章介绍如何使用 Azure PowerShell 来创建并配置路由服务器。
services: route-server
author: duongau
ms.author: duau
ms.date: 09/01/2021
ms.topic: quickstart
ms.service: route-server
ms.custom: devx-track-azurepowershell - mode-api
ms.openlocfilehash: 8fe644edd7b08a5f18787b16abfe5e0a1951df40
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2021
ms.locfileid: "129094398"
---
# <a name="quickstart-create-and-configure-route-server-using-azure-powershell"></a>快速入门：使用 Azure PowerShell 创建并配置路由服务器

本文可帮助你使用 Azure PowerShell 将 Azure 路由服务器配置为与虚拟网络中的网络虚拟设备 (NVA) 对等互连。 路由服务器将会从 NVA 获知路由，并将这些路由配置到虚拟网络中的虚拟机上。 Azure 路由服务器还会将虚拟网络路由播发到 NVA。 有关详细信息，请参阅 [Azure 路由服务器](overview.md)。

:::image type="content" source="media/quickstart-configure-route-server-portal/environment-diagram.png" alt-text="使用 Azure PowerShell 的路由服务器部署环境示意图。" border="false":::

> [!IMPORTANT]
> 如果在 9 月 1 日之前创建了 Azure 路由服务器，并且未关联公共 IP 地址，则需要重新创建路由服务器，以便它可以获取 IP 地址用于管理目的。

## <a name="prerequisites"></a>先决条件

* 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 请确保具有最新的 PowerShell 模块，或者可以在门户中使用 Azure Cloud Shell。
* 查看 [Azure 路由服务器的服务限制](route-server-faq.md#limitations)。
* 如果在本地运行 PowerShell，则还需运行 `Connect-AzAccount` 以创建与 Azure 的连接。

## <a name="create-resource-group-and-a-virtual-network"></a>创建资源组和虚拟网络

### <a name="create-a-resource-group"></a>创建资源组 

必须先创建资源组来托管路由服务器，然后才能创建 Azure 路由服务器。 使用 [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup) 创建资源组。 此示例在“美国西部”创建了名为 myRouteServerRG 的资源组 ：

```azurepowershell-interactive
$rg = @{
    Name = 'myRouteServerRG'
    Location = 'WestUS'
}
New-AzResourceGroup @rg
```

### <a name="create-a-virtual-network"></a>创建虚拟网络

使用 [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) 创建虚拟网络。 此示例在“美国西部”创建名为 myVirtualNetwork 的默认虚拟网络：如果已有虚拟网络，可以跳到下一部分 。

```azurepowershell-interactive
$vnet = @{
    Name = 'myVirtualNetwork'
    ResourceGroupName = 'myRouteServerRG'
    Location = 'WestUS'
    AddressPrefix = '10.0.0.0/16'    
}
$virtualNetwork = New-AzVirtualNetwork @vnet
```

### <a name="add-a-dedicated-subnet"></a>添加专用子网

Azure 路由服务器需要名为 RouteServerSubnet 的专用子网。 子网大小必须至少为 /27 或为短前缀（例如 /26 或 /25），否则在部署路由服务器时会收到错误消息。 使用 [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) 创建名为 RouteServerSubnet 的子网配置：

```azurepowershell-interactive
$subnet = @{
    Name = 'RouteServerSubnet'
    VirtualNetwork = $virtualNetwork
    AddressPrefix = '10.0.0.0/24'
}
$subnetConfig = Add-AzVirtualNetworkSubnetConfig @subnet

$virtualnetwork | Set-AzVirtualNetwork
```

## <a name="create-the-route-server"></a>创建路由服务器

1. 若要确保与管理路由服务器配置的后端服务的连接，需要分配一个公共 IP 地址。 使用 [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) 创建名为 RouteServerIP 标准公共 IP 地址：

    ```azurepowershell-interactive
    $ip = @{
        Name = 'myRouteServerIP'
        ResourceGroupName = 'myRouteServerRG'
        Location = 'WestUS'
        AllocationMethod = 'Static'
        IpAddressVersion = 'Ipv4'
        Sku = 'Standard'
    }
    $publicIp = New-AzPublicIpAddress @ip
    ```
    
2. 使用 [New-AzRouteServer](/powershell/module/az.network/new-azrouteserver) 创建 Azure 路由服务器。 以下示例在“美国西部”创建了名为 myRouteServer 的 Azure 路由服务器 。 HostedSubnet 是在前一部分中创建的 RouteServerSubnet 的资源 ID。

    ```azurepowershell-interactive
    $rs = @{
        RouteServerName = 'myRouteServer'
        ResourceGroupName = 'myRouteServerRG'
        Location = 'WestUS'
        HostedSubnet = $subnetConfig.Id
        PublicIP = $publicIp
    }
    New-AzRouteServer @rs 
    ```

## <a name="create-bgp-peering-with-an-nva"></a>创建与 NVA 对等互连的 BGP

若要建立从路由服务器到 NVA 的 BGP 对等互连，请使用 [Add-AzRouteServerPeer](/powershell/module/az.network/add-azrouteserverpeer)：

“your_nva_ip”是分配到 NVA 的虚拟网络 IP。 “your_nva_asn”是在 NVA 中配置的自治系统编号 (ASN)。 该 ASN 可以是 65515-65520 范围之外的任意 16 位数字。 此 ASN 范围是 Microsoft 保留的。

```azurepowershell-interactive
$peer = @{
    PeerName = 'myNVA"
    PeerIp = '192.168.0.1'
    PeerAsn = '65501'
    RouteServerName = 'myRouteServer'
    ResourceGroupName = myRouteServerRG'
}
Add-AzRouteServerPeer @peer
```

若要设置与不同的 NVA 或同一 NVA 的另一个实例的对等互连以实现冗余，请使用与上述相同的命令，但需要使用不同的 PeerName、PeerIp 和 PeerAsn  。

## <a name="complete-the-configuration-on-the-nva"></a>在 NVA 上完成配置

若要在 NVA 上完成配置并启用 BGP 会话，需要 Azure 路由服务器的 IP 和 ASN。 可通过使用 [Get-AzRouteServer](/powershell/module/az.network/get-azrouteserver) 来获取该信息：

```azurepowershell-interactive
$routeserver = @{
    RouteServerName = 'myRouteServer'
    ResourceGroupName = 'myRouteServerRG'
} 
Get-AzRouteServer @routeserver
```

输出将如下所示：

``` 
RouteServerAsn : 65515
RouteServerIps : {10.5.10.4, 10.5.10.5}
```

## <a name="configure-route-exchange"></a><a name = "route-exchange"></a>配置路由交换

如果在同一虚拟网络中有一个 ExpressRoute 和 Azure VPN 网关，并且需要让它们交换路由，则可以在 Azure 路由服务器上启用路由交换。

1. 若要在 Azure 路由服务器和网关之间启用路由交换，请使用带有 -AllowBranchToBranchTraffic 标志的 [Update-AzRouteServer](/powershell/module/az.network/update-azrouteserver)：

```azurepowershell-interactive
$routeserver = @{
    RouteServerName = 'myRouteServer'
    ResourceGroupName = 'myRouteServerRG'
    AllowBranchToBranchTraffic
}  
Update-AzRouteServer @routeserver 
```

2. 若要在 Azure 路由服务器和网关之间禁用路由交换，请使用不带 -AllowBranchToBranchTraffic 标志的 [Update-AzRouteServer](/powershell/module/az.network/update-azrouteserver)：

```azurepowershell-interactive
$routeserver = @{
    RouteServerName = 'myRouteServer'
    ResourceGroupName = 'myRouteServerRG'
}  
Update-AzRouteServer @routeserver 
```

## <a name="troubleshooting"></a>疑难解答

使用 [Get-AzRouteServerPeerAdvertisedRoute](/powershell/module/az.network/get-azrouteserverpeeradvertisedroute) 查看 Azure 路由服务器播发的路由。

```azurepowershell-interactive
$remotepeer = @{
    RouteServerName = 'myRouteServer'
    ResourceGroupName = 'myRouteServerRG'
    PeerName = 'myNVA'
}
Get-AzRouteServerPeerAdvertisedRoute @routeserver
```

使用 [Get-AzRouteServerPeerLearnedRoute](/powershell/module/az.network/get-azrouteserverpeerlearnedroute) 查看 Azure 路由服务器获知的路由。

```azurepowershell-interactive
$routeserver = @{
    RouteServerName = 'myRouteServer'
    ResourceGroupName = 'myRouteServerRG'
    AllowBranchToBranchTraffic
}  
Get-AzRouteServerPeerLearnedRoute @routeserver
```
## <a name="clean-up-resources"></a>清理资源

如果不再需要 Azure 路由服务器，请使用第一个命令删除 BGP 对等互连，然后使用第二个命令删除路由服务器。 

1. 使用 [Remove-AzRouteServerPeer](/powershell/module/az.network/remove-azrouteserverpeer) 命令删除 Azure 路由服务器与 NVA 之间的 BGP 对等互连：

```azurepowershell-interactive
$peer = @{
    PeerName = 'myNVA'
    RouteServerName = 'myRouteServer'
    ResourceGroupName = 'myRouteServerRG'
} 
Remove-AzRouteServerPeer @peer
```

2. 使用 [Remove-AzRouteServer](/powershell/module/az.network/remove-azrouteserver) 删除 Azure 路由服务器：

```azurepowershell-interactive 
$routeserver = @{
    RouteServerName = 'myRouteServer'
    ResourceGroupName = 'myRouteServerRG'
} 
Remove-AzRouteServer @routeserver
```

## <a name="next-steps"></a>后续步骤

在创建 Azure 路由服务器后，请继续了解有关 Azure 路由服务器如何与 ExpressRoute 和 VPN 网关进行交互的详细信息： 

> [!div class="nextstepaction"]
> [Azure ExpressRoute 和 Azure VPN 支持](expressroute-vpn-support.md)
