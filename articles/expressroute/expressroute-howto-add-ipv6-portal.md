---
title: Azure ExpressRoute：使用 Azure 门户添加 IPv6 支持
description: 了解如何使用 Azure 门户添加 IPv6 支持以连接到 Azure 部署。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/09/2021
ms.author: duau
ms.openlocfilehash: 7f5afc05a8d03d33366a2f76318bcf5e039d4d30
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105561655"
---
# <a name="add-ipv6-support-for-private-peering-using-the-azure-portal-preview"></a>使用 Azure 门户添加对专用对等互连的 IPv6 支持（预览）

本文介绍如何使用 Azure 门户添加 IPv6 支持，以通过 ExpressRoute 连接到 Azure 中的资源。 

> [!Note]
> 此功能目前在[具有可用性区域的 Azure 区域](../availability-zones/az-region.md#azure-regions-with-availability-zones)中为预览版。 因此，可以使用任何对等互连位置创建 ExpressRoute 线路，但它所连接到的基于 IPv6 的部署必须位于具有可用性区域的区域中。

## <a name="register-for-public-preview"></a>注册公共预览版
在添加 IPv6 支持之前，必须先注册订阅。 若要注册，请通过 Azure PowerShell 运行以下命令：

1.  登录到 Azure 并选择要使用的订阅。 为包含 ExpressRoute 线路的订阅和包含 Azure 部署的订阅运行以下命令（如果它们不同）。

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

1. 使用以下命令注册公共预览版订阅：
    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowIpv6PrivatePeering -ProviderNamespace Microsoft.Network
    ```

然后，ExpressRoute 团队将在 2-3 个工作日内批准你的请求。

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

如果你有一个现有的 Azure 资源环境，并且你想要将 IPv6 专用对等互连用于该环境的可用性区域，那么请按照以下步骤操作。

1. 导航到 ExpressRoute 线路连接到的虚拟网络。

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-vnet.png" alt-text="虚拟网络列表的屏幕快照。":::

1. 导航到“地址空间”选项卡，然后将 IPv6 地址空间添加到虚拟网络。 保存地址空间。

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-space.png" alt-text="将 Ipv6 地址空间添加到虚拟网络的屏幕截图。":::

1. 导航到“子网”选项卡，然后选择“GatewaySubnet”。 选中“添加 IPV6 地址空间”并为子网提供 IPV6 地址空间。 网关 IPv6 子网应为 /64 或更大。 指定所有参数后，请保存配置。

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-gateway-space.png" alt-text="将 Ipv6 地址空间添加到子网的屏幕截图。":::

1. 如果有现有的区域冗余网关，请导航到 ExpressRoute 网关，并刷新资源以启用 IPv6 连接。 否则，请使用区域冗余 SKU（ErGw1AZ、ErGw2AZ、ErGw3AZ）[创建虚拟网络网关](expressroute-howto-add-gateway-portal-resource-manager.md)。 如果计划使用 FastPath，请使用 ErGw3AZ。

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/refresh-gateway.png" alt-text="刷新网关的屏幕截图。":::

## <a name="create-a-connection-to-a-new-virtual-network"></a>创建与新虚拟网络的连接

如果计划使用 IPv6 专用对等互连在具有可用性区域的区域中连接到一组新的 Azure 资源，请执行以下步骤。

1. 使用 IPv4 和 IPv6 地址空间创建双堆栈虚拟网络。 有关详细信息，请参阅[创建虚拟机](../virtual-network/quick-create-portal.md#create-a-virtual-network)。

1. [创建双堆栈网关子网](expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet)。

1. 使用区域冗余 SKU（ErGw1AZ、ErGw2AZ、ErGw3AZ）[创建虚拟网络网关](expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway)。 如果计划使用 FastPath，请使用 ErGw3AZ（请注意，此选项仅适用于使用 ExpressRoute Direct 的线路）。

1. [将虚拟网络链接到 ExpressRoute 线路](expressroute-howto-linkvnet-portal-resource-manager.md)。

## <a name="limitations"></a>限制
虽然 IPv6 支持可用于连接到具有可用性区域的区域中的部署，但它不支持以下用例：

* 通过非 AZ ExpressRoute 网关 SKU 连接到 Azure 中的部署
* 连接到非 AZ 区域中的部署
* ExpressRoute 线路之间的 Global Reach 连接
* 将 ExpressRoute 用于虚拟 WAN
* FastPath 与非 ExpressRoute Direct 线路
* FastPath 与以下对等位置中的路线：迪拜
* 与 VPN 网关共存

## <a name="next-steps"></a>后续步骤

若要排查 ExpressRoute 问题，请参阅以下文章：

* [验证 ExpressRoute 连接](expressroute-troubleshooting-expressroute-overview.md)
* [网络性能故障排除](expressroute-troubleshooting-network-performance.md)