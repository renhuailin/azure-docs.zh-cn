---
title: 教程 - Azure ExpressRoute：向 VNet 添加网关 - Azure PowerShell
description: 本教程指导你将 VNet 网关添加到已使用 Azure PowerShell 为 ExpressRoute 创建的资源管理器 VNet 中。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/05/2020
ms.author: duau
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: e8bc2d0ed29cbac171e1d03bc9a6806b1ef64f1f
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2021
ms.locfileid: "112465304"
---
# <a name="tutorial-configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>教程：使用 PowerShell 配置 ExpressRoute 的虚拟网络网关
> [!div class="op_single_selector"]
> * [Resource Manager - Azure 门户](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [经典 - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [视频 - Azure 门户](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 

本教程指导你为预先存在的 VNet 添加虚拟网络 (VNet) 网关、重设网关大小以及删除网关。 此配置的步骤适用于使用资源管理器部署模型创建的 VNet（针对 ExpressRoute 配置）。 有关详细信息，请参阅[关于 ExpressRoute 的虚拟网络网关](expressroute-about-virtual-network-gateways.md)。

在本教程中，你将了解如何执行以下操作：
> [!div class="checklist"]
> - 创建网关子网。
> - 创建虚拟网络网关。

## <a name="prerequisites"></a>先决条件

### <a name="configuration-reference-list"></a>配置参考列表

此任务的步骤使用的 VNet 基于以下配置参考列表中的值。 此列表中也概述了其他设置和名称。 尽管我们确实基于此列表中的值添加变量，但是我们在任何步骤中不会直接使用此列表。 可以复制列表作为参考，并将列表中的值替换为自己的值。

| 设置                   | 值                                              |
| ---                       | ---                                                |
| 虚拟网络名称 | *TestVNet* |    
| 虚拟网络地址空间 | *192.168.0.0/16* |
| 资源组 | *TestRG* |
| Subnet1 名称 | *FrontEnd* |   
| Subnet1 地址空间 | *192.168.1.0/24* |
| Subnet1 名称 | *FrontEnd* |
| 网关子网名称 | *GatewaySubnet* |    
| 网关子网地址空间 | *192.168.200.0/26* |
| 区域 | *美国东部* |
| 网关名称 | *GW* |   
| 网关 IP 名称 | *GWIP* |
| 网关 IP 配置名称 | *gwipconf* |
| 类型 | *ExpressRoute* |
| 网关公共 IP 名称  | *gwpip* |

> [!IMPORTANT]
> 对专用对等互连的 IPv6 支持目前为公共预览版。 如果要将虚拟网络连接到配置了基于 IPv6 的专用对等互连的 ExpressRoute 线路，请确保虚拟网络为双重堆栈，并遵循[此处](../virtual-network/ipv6-overview.md)所述的指南。
> 
> 

## <a name="add-a-gateway"></a>添加网关

1. 若要连接到 Azure，请运行 `Connect-AzAccount`。

1. 声明此练习的变量。 请务必编辑此示例，使之反映想要使用的设置。

   ```azurepowershell-interactive 
   $RG = "TestRG"
   $Location = "East US"
   $GWName = "GW"
   $GWIPName = "GWIP"
   $GWIPconfName = "gwipconf"
   $VNetName = "TestVNet"
   ```
1. 将虚拟网络对象存储为变量。

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
   ```
1. 将网关子网添加到虚拟网络中。 网关子网必须命名为“GatewaySubnet”。 网关子网必须是 /27 或更大（/26、/25 等）。 如果计划将 16 个 ExpressRoute 线路连接到网关，则必须创建 /26 或更大的网关子网。

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26
   ```
    如果你使用的是双重堆栈虚拟网络，并且计划通过 ExpressRoute 使用基于 IPv6 的专用对等互连，请改为创建双重堆栈网关子网。

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix "10.0.0.0/26","ace:daa:daaa:deaa::/64"
   ```
1. 设置配置。

   ```azurepowershell-interactive
   $vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
1. 将网关子网存储为变量。

   ```azurepowershell-interactive
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
   ```
1. 请求公共 IP 地址。 创建网关之前请求 IP 地址。 无法指定要使用的 IP 地址；将自动分配该地址。 后面的配置部分将使用此 IP 地址。 AllocationMethod 必须是动态的。

   ```azurepowershell-interactive
   $pip = New-AzPublicIpAddress -Name $GWIPName  -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
   ```
      
   如果打算在 ExpressRoute 上使用基于 IPv6 的专用对等互连，请将 IP SKU 设置为“标准”，将 AllocationMethod 设置为“静态”：
   ```azurepowershell-interactive
   $pip = New-AzPublicIpAddress -Name $GWIPName  -ResourceGroupName $RG -Location $Location -AllocationMethod Static -SKU Standard
   ```
   
1. 创建网关配置。 网关配置定义要使用的子网和公共 IP 地址。 在此步骤中，将指定创建网关时使用的配置。 使用以下示例创建网关配置。

   ```azurepowershell-interactive
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
   ```
1. 创建网关。 在此步骤中，**-GatewayType** 尤其重要。 必须使用值 **ExpressRoute**。 运行这些 cmdlet 后，可能需要 45 分钟或更长时间才能创建好网关。

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard
   ```
> [!IMPORTANT]
> 如果计划在 ExpressRoute 上使用基于 IPv6 的专用对等互连，请确保为“-GatewaySku”选择 AZ SKU（ErGw1AZ、ErGw2AZ、ErGw3AZ），或为使用标准和静态公共 IP的 -GatewaySKU 使用非 AZ SKU（标准、HighPerformance、UltraPerformance）。
> 
> 

## <a name="verify-the-gateway-was-created"></a>验证是否已创建网关
使用以下命令来验证是否已创建网关：

```azurepowershell-interactive
Get-AzVirtualNetworkGateway -ResourceGroupName $RG
```

## <a name="resize-a-gateway"></a>重设网关大小
有许多[网关 SKU](expressroute-about-virtual-network-gateways.md)。 可以使用以下命令随时更改网关 SKU。

> [!IMPORTANT]
> 此命令对 UltraPerformance 网关不起作用。 要将网关更改为 UltraPerformance 网关，首先要删除现有的 ExpressRoute 网关，然后创建新的 UltraPerformance 网关。 要将网关从 UltraPerformance 网关降级，首先要删除 UltraPerformance 网关，然后创建新网关。
> 

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

## <a name="clean-up-resources"></a>清理资源
使用以下命令删除网关：

```azurepowershell-interactive
Remove-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
```

## <a name="next-steps"></a>后续步骤
创建 VNet 网关后，可将 VNet 链接到 ExpressRoute 线路。 

> [!div class="nextstepaction"]
> [将虚拟网络链接到 ExpressRoute 线路](expressroute-howto-linkvnet-arm.md)
