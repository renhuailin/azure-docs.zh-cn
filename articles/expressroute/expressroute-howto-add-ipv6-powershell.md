---
title: Azure ExpressRoute：使用 Azure PowerShell 添加 IPv6 支持
description: 了解如何使用 Azure PowerShell 添加 IPv6 支持以连接到 Azure 部署。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: 62616465b98feca1052ac3e45932120fdf0ffd42
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128584501"
---
# <a name="add-ipv6-support-for-private-peering-using-azure-powershell-preview"></a>使用 Azure PowerShell 添加对专用对等互连的 IPv6 支持（预览版）

本文介绍如何使用 Azure PowerShell 添加 IPv6 支持，以通过 ExpressRoute 连接到 Azure 中的资源。

## <a name="working-with-azure-powershell"></a>使用 Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="add-ipv6-private-peering-to-your-expressroute-circuit"></a>将 IPv6 专用对等互连添加到 ExpressRoute 线路

1. [创建 ExpressRoute 线路](./expressroute-howto-circuit-arm.md)或使用现有线路。 通过运行 Get-AzExpressRouteCircuit 命令检索线路：

    ```azurepowershell-interactive
    $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
    ```

2. 通过运行 Get-AzExpressRouteCircuitPeeringConfig 检索线路的专用对等互连配置：

    ```azurepowershell-interactive
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
    ```

3. 将 IPv6 专用对等互连添加到现有的 IPv4 专用对等互连配置中。 为主链接和辅助链接提供一对自己拥有的 /126 IPV6 子网。 在每个子网中，当 Microsoft 将第二个可用的 IP 用于其路由器时，你需为路由器分配第一个可用的 IP 地址。

    > [!Note]
    > 对等 ASN 和 VlanId 应与 IPv4 专用对等互连配置中的对等方匹配。

    ```azurepowershell-interactive
    Set-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 200 -PeerAddressType IPv6

    Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
    ```

4. 成功保存配置后，通过运行 Get-AzExpressRouteCircuit 命令再次获取线路。 响应应当类似于以下示例：

    ```azurepowershell
    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : eastus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                       }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Washington DC",
                                         "BandwidthInMbps": 50
                                       }
    ExpressRoutePort                 : null
    BandwidthInGbps                  :
    Stag                             : 29
    ServiceKey                       : **************************************
    Peerings                         : [
                                         {
                                           "Name": "AzurePrivatePeering",
                                           "Etag": "W/\"facc8972-995c-4861-a18d-9a82aaa7167e\"",
                                           "Id": "/subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/AzurePrivatePeering",
                                           "PeeringType": "AzurePrivatePeering",
                                           "State": "Enabled",
                                           "AzureASN": 12076,
                                           "PeerASN": 100,
                                           "PrimaryPeerAddressPrefix": "192.168.15.16/30",
                                           "SecondaryPeerAddressPrefix": "192.168.15.20/30",
                                           "PrimaryAzurePort": "",
                                           "SecondaryAzurePort": "",
                                           "VlanId": 200,
                                           "ProvisioningState": "Succeeded",
                                           "GatewayManagerEtag": "",
                                           "LastModifiedBy": "Customer",
                                           "Ipv6PeeringConfig": {
                                             "State": "Enabled",
                                             "PrimaryPeerAddressPrefix": "3FFE:FFFF:0:CD30::/126",
                                             "SecondaryPeerAddressPrefix": "3FFE:FFFF:0:CD30::4/126"
                                           },
                                           "Connections": [],
                                           "PeeredConnections": []
                                         },
                                       ]
    Authorizations                   : []
    AllowClassicOperations           : False
    GatewayManagerEtag               :
    ```

## <a name="update-your-connection-to-an-existing-virtual-network"></a>更新与现有虚拟网络的连接

如果你已有 Azure 资源环境，并且你想要对该环境使用 IPv6 专用对等互连，请执行以下步骤。

1. 检索 ExpressRoute 线路连接到的虚拟网络。

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork -Name "VirtualNetwork" -ResourceGroupName "ExpressRouteResourceGroup"
    ```

2. 将 IPv6 地址空间添加到虚拟网络。

    ```azurepowershell-interactive
    $vnet.AddressSpace.AddressPrefixes.add("ace:daa:daaa:deaa::/64")
    Set-AzVirtualNetwork -VirtualNetwork $vnet
    ```

3. 将 IPv6 地址空间添加到网关子网。 网关 IPv6 子网应为 /64 或更大。

    ```azurepowershell-interactive
    Set-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix "10.0.0.0/26", "ace:daa:daaa:deaa::/64"
    Set-AzVirtualNetwork -VirtualNetwork $vnet
    ```

4. 如果已有区域冗余网关，请运行以下各项来启用 IPv6 连接（请注意，更改可能需要 1 小时才能反映出来）。 否则，请使用任意 SKU [创建虚拟网络网关](./expressroute-howto-add-gateway-resource-manager.md)。 如果计划使用 FastPath，请使用 UltraPerformance 或 ErGw3AZ（请注意，此选项仅适用于使用 ExpressRoute Direct 的线路）。

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name "GatewayName" -ResourceGroupName "ExpressRouteResourceGroup"
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw
    ```
>[!NOTE]
> 如果现有网关不是区域冗余的（这意味着它是标准、高性能或超高性能 SKU），则需要使用任意 SKU 和标准静态公共 IP 地址来删除并[重新创建网关](./expressroute-howto-add-gateway-resource-manager.md#add-a-gateway)。

## <a name="create-a-connection-to-a-new-virtual-network"></a>创建与新虚拟网络的连接

如果计划使用 IPv6 专用对等互连连接到一组新的 Azure 资源，请执行以下步骤。

1. 创建包含 IPv4 和 IPv6 地址空间的双堆栈虚拟网络。 有关详细信息，请参阅[创建虚拟网络](../virtual-network/quick-create-portal.md#create-a-virtual-network)。

2. [创建双堆栈网关子网](./expressroute-howto-add-gateway-resource-manager.md#add-a-gateway)。

3. 使用任意 SKU [创建虚拟网络网关](./expressroute-howto-add-gateway-resource-manager.md#add-a-gateway)。 如果计划使用 FastPath，请使用 UltraPerformance 或 ErGw3AZ（请注意，此选项仅适用于使用 ExpressRoute Direct 的线路）。

4. [将虚拟网络链接到 ExpressRoute 线路](./expressroute-howto-linkvnet-arm.md)。

## <a name="limitations"></a>限制
虽然 IPv6 支持可用于连接到公共 Azure 区域中的部署，但它不支持以下用例：

* 连接到“非”区域冗余的现有 ExpressRoute 网关。 请注意，使用标准静态 IP 地址新创建的任何 SKU（区域冗余和非区域冗余）ExpressRoute 网关均可用于双堆栈 ExpressRoute 连接
* ExpressRoute 线路之间的 Global Reach 连接
* 将 ExpressRoute 用于虚拟 WAN
* FastPath 与非 ExpressRoute Direct 线路
* FastPath 与以下对等互连位置的线路：迪拜
* 与 VPN 网关共存

## <a name="next-steps"></a>后续步骤

若要排查 ExpressRoute 问题，请参阅以下文章：

* [验证 ExpressRoute 连接](expressroute-troubleshooting-expressroute-overview.md)
* [网络性能故障排除](expressroute-troubleshooting-network-performance.md)
