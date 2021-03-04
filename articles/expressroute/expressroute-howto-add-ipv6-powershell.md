---
title: Azure ExpressRoute：使用 Azure PowerShell 添加 IPv6 支持
description: 了解如何使用 Azure PowerShell 添加 IPv6 支持以连接到 Azure 部署。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: c0d153ff8125a6ef16a69aec72a27dd4b234eab5
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102100043"
---
# <a name="add-ipv6-support-for-private-peering-using-azure-powershell-preview"></a>使用 Azure PowerShell (预览版添加对专用对等互连的 IPv6 支持) 

本文介绍如何使用 Azure PowerShell 添加 IPv6 支持以通过 ExpressRoute 连接到 Azure 中的资源。

> [!Note]
> 此功能目前可在 [具有可用性区域的 Azure 区域](https://docs.microsoft.com/azure/availability-zones/az-region#azure-regions-with-availability-zones)中预览。 因此，可以使用任何对等互连位置创建 ExpressRoute 线路，但它所连接到的基于 IPv6 的部署必须位于具有可用性区域的区域中。

## <a name="working-with-azure-powershell"></a>使用 Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="register-for-public-preview"></a>注册公共预览版
在添加 IPv6 支持之前，必须先注册订阅。 若要注册，请通过 Azure PowerShell 执行以下操作：
1.  登录到 Azure 并选择订阅。 你必须为包含 ExpressRoute 线路的订阅执行此操作，以及包含 Azure 部署 (的订阅（如果它们) 不同）。

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

2. 请求使用以下命令注册公共预览版订阅：
    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowIpv6PrivatePeering -ProviderNamespace Microsoft.Network
    ```

然后，ExpressRoute 团队将在2-3 个工作日内批准你的请求。

## <a name="add-ipv6-private-peering-to-your-expressroute-circuit"></a>将 IPv6 专用对等互连添加到 ExpressRoute 线路

1. [创建 ExpressRoute 线路](https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-arm) 或使用现有线路。 通过运行 **AzExpressRouteCircuit** 命令检索线路：

    ```azurepowershell-interactive
    $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
    ```

2. 通过运行 **AzExpressRouteCircuitPeeringConfig** 来检索线路的专用对等互连配置：

    ```azurepowershell-interactive
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
    ```

3. 将 IPv6 专用对等互连添加到现有的 IPv4 专用对等互连配置。 为主链接和辅助链接提供一对/126 IPv6 子网。 在每个子网中，当 Microsoft 将第二个可用的 IP 用于其路由器时，你需为路由器分配第一个可用的 IP 地址。

    > [!Note]
    > 对等 ASN 和 VlanId 应与 IPv4 专用对等互连配置中的相同。

    ```azurepowershell-interactive
    Set-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 200 -PeerAddressType IPv6

    Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
    ```

4. 成功保存配置后，通过运行 **AzExpressRouteCircuit** 命令再次获取该线路。 响应应类似于以下示例：

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

如果你在某个区域中具有 Azure 资源的现有环境，可用性区域并且你想要将 IPv6 专用对等互连用于，请遵循以下步骤。

1. 检索 ExpressRoute 线路连接到的虚拟网络。

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork -Name "VirtualNetwork" -ResourceGroupName "ExpressRouteResourceGroup"
    ```

2. 将 IPv6 地址空间添加到虚拟网络。

    ```azurepowershell-interactive
    $vnet.AddressSpace.AddressPrefixes.add("ace:daa:daaa:deaa::/64")
    Set-AzVirtualNetwork -VirtualNetwork $vnet
    ```

3. 将 IPv6 地址空间添加到网关子网。 网关 IPv6 子网应为/64 或更大。

    ```azurepowershell-interactive
    Set-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix "10.0.0.0/26", "ace:daa:daaa:deaa::/64"
    Set-AzVirtualNetwork -VirtualNetwork $vnet
    ```

4. 如果已有区域冗余的网关，请运行以下各项来启用 IPv6 连接。 否则，请使用区域冗余 SKU (ErGw1AZ，ErGw2AZ，ErGw3AZ) [创建虚拟网络网关](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-resource-manager) 。

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name "GatewayName" -ResourceGroupName "ExpressRouteResourceGroup"
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw
    ```

## <a name="create-a-connection-to-a-new-virtual-network"></a>创建与新虚拟网络的连接

如果计划使用 IPv6 专用对等互连在可用性区域区域中连接到一组新的 Azure 资源，请执行以下步骤。

1. 使用 IPv4 和 IPv6 地址空间创建双堆栈虚拟网络。 有关详细信息，请参阅 [创建虚拟网络](https://docs.microsoft.com/azure/virtual-network/quick-create-portal#create-a-virtual-network)。

2. [创建双堆栈网关子网](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-resource-manager#add-a-gateway)。

3. 使用区域冗余 SKU (ErGw1AZ，ErGw2AZ，ErGw3AZ) [创建虚拟网络网关](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-resource-manager#add-a-gateway)。 如果计划使用 FastPath，请使用 ErGw3AZ。

4. 将[虚拟网络链接到 ExpressRoute 线路](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-arm)。

## <a name="limitations"></a>限制
虽然 IPv6 支持可用于连接到可用性区域区域中的部署，但它不支持以下用例：

* 通过非 AZ ExpressRoute 网关 SKU 连接到 Azure 中的部署
* 与非 AZ 区域中的部署的连接
* Global Reach ExpressRoute 线路之间的连接
* 将 ExpressRoute 用于虚拟 WAN

## <a name="next-steps"></a>后续步骤

若要排查 ExpressRoute 问题，请参阅以下文章：

* [验证 ExpressRoute 连接](expressroute-troubleshooting-expressroute-overview.md)
* [网络性能故障排除](expressroute-troubleshooting-network-performance.md)
