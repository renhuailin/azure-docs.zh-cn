---
title: Azure ExpressRoute：使用 Azure 门户添加 IPv6 支持
description: 了解如何使用 Azure 门户添加 IPv6 支持以连接到 Azure 部署。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/09/2021
ms.author: duau
ms.openlocfilehash: c8d12e533da11f1cf1db2be800b8f1ac3ed02d90
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129669162"
---
# <a name="add-ipv6-support-for-private-peering-using-the-azure-portal-preview"></a>使用 Azure 门户添加对专用对等互连的 IPv6 支持（预览）

本文介绍如何使用 Azure 门户添加 IPv6 支持，以通过 ExpressRoute 连接到 Azure 中的资源。

>[!NOTE]
> 门户体验的某些方面仍在实现中。 因此，请按照本文档中提供的说明的确切顺序通过门户成功添加 IPv6 支持。 具体而言，请确保在门户中创建新的虚拟网络网关之前，先创建虚拟网络和子网，或将 IPv6 地址空间添加到现有的虚拟网络和 GatewaySubnet。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

在浏览器中，转到 [Azure 门户](https://portal.azure.com)，然后使用你的 Azure 帐户登录。

## <a name="add-ipv6-private-peering-to-your-expressroute-circuit"></a>将 IPv6 专用对等互连添加到 ExpressRoute 线路

1. [创建 ExpressRoute 线路](expressroute-howto-circuit-portal-resource-manager.md)，或导航到要更改的现有线路。

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-circuit.png" alt-text="ExpressRoute 线路列表的屏幕截图。":::

1. 选择“Azure 专用”对等互连配置。

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-peering.png" alt-text="ExpressRoute 概述页的屏幕截图。":::

1. 通过为“子网”选择“两者”将 IPv6 专用对等添加到你的现有 IPv4 专用对等配置，或者仅选择“IPv6”在新线路上启用 IPv6 专用对等互连。 为主链接和辅助链接提供一对 /126 IPV6 子网。 在每个子网中，当 Microsoft 将第二个可用的 IP 用于其路由器时，你需为路由器分配第一个可用的 IP 地址。 指定所有参数后，请保存对等互连配置。

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-peering.png" alt-text="在专用对等互连页上添加 Ipv6 的屏幕截图。":::

1. 成功接受配置后，应看到类似于以下示例的内容。

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/view-ipv6-peering.png" alt-text="为专用对等互连配置的 Ipv6 屏幕截图。":::

## <a name="update-your-connection-to-an-existing-virtual-network"></a>更新与现有虚拟网络的连接

如果你已有 Azure 资源环境，并且你想要对该环境使用 IPv6 专用对等互连，请执行以下步骤。

1. 导航到 ExpressRoute 线路连接到的虚拟网络。

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-vnet.png" alt-text="虚拟网络列表的屏幕快照。":::

1. 导航到“地址空间”选项卡，然后将 IPv6 地址空间添加到虚拟网络。 保存地址空间。

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-space.png" alt-text="将 Ipv6 地址空间添加到虚拟网络的屏幕截图。":::

1. 导航到“子网”选项卡，然后选择“GatewaySubnet”。 选中“添加 IPV6 地址空间”并为子网提供 IPV6 地址空间。 网关 IPv6 子网应为 /64 或更大。 指定所有参数后，请保存配置。

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-gateway-space.png" alt-text="将 Ipv6 地址空间添加到子网的屏幕截图。":::
    
1. 如果你有现有的区域冗余网关，请在 PowerShell 中运行以下各项来启用 IPv6 连接（请注意，更改可能需要 1 小时才能反映出来）。 否则，请使用任意 SKU 和标准静态公共 IP 地址[创建虚拟网络网关](./expressroute-howto-add-gateway-portal-resource-manager.md)。 如果计划使用 FastPath，请使用 UltraPerformance 或 ErGw3AZ（请注意，此选项仅适用于使用 ExpressRoute Direct 的线路）。

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name "GatewayName" -ResourceGroupName "ExpressRouteResourceGroup"
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw
    
>[!NOTE]
> If you have an existing gateway that is not zone-redundant (meaning it is Standard, High Performance, or Ultra Performance SKU), you will need to delete and [recreate the gateway](expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway) using any SKU and a Standard, Static public IP address.

## Create a connection to a new virtual network

Follow the steps below if you plan to connect to a new set of Azure resources using your IPv6 Private Peering.

1. Create a dual-stack virtual network with both IPv4 and IPv6 address space. For more information, see [Create a virtual network](../virtual-network/quick-create-portal.md#create-a-virtual-network).

1. [Create the dual-stack gateway subnet](expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet).

1. [Create the virtual network gateway](expressroute-howto-add-gateway-resource-manager.md) using any SKU and a Standard, Static public IP address. If you plan to use FastPath, use UltraPerformance or ErGw3AZ (note that this option is only available for circuits using ExpressRoute Direct). **NOTE:** Please use the PowerShell instructions for this step as the Azure portal experience is still under development.

1. [Link your virtual network to your ExpressRoute circuit](expressroute-howto-linkvnet-portal-resource-manager.md).

## Limitations
While IPv6 support is available for connections to deployments in Public Azure regions, it doesn't support the following use cases:

* Connections to *existing* ExpressRoute gateways that are not zone-redundant. Note that *newly* created ExpressRoute gateways of any SKU (both zone-redundant and not) using  a Standard, Static IP address can be used for dual-stack ExpressRoute connections
* Global Reach connections between ExpressRoute circuits
* Use of ExpressRoute with virtual WAN
* FastPath with non-ExpressRoute Direct circuits
* FastPath with circuits in the following peering locations: Dubai
* Coexistence with VPN Gateway

## Next steps

To troubleshoot ExpressRoute problems, see the following articles:

* [Verifying ExpressRoute connectivity](expressroute-troubleshooting-expressroute-overview.md)
* [Troubleshooting network performance](expressroute-troubleshooting-network-performance.md)
