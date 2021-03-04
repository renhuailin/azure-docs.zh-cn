---
title: Azure ExpressRoute：使用 Azure 门户添加 IPv6 支持
description: 了解如何使用 Azure 门户添加 IPv6 支持以连接到 Azure 部署。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 2/9/2021
ms.author: duau
ms.openlocfilehash: 640e13261fa76fe89c9f5fbd038f20766c509025
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102038944"
---
# <a name="add-ipv6-support-for-private-peering-using-the-azure-portal-preview"></a>使用 Azure 门户 (预览版添加对专用对等互连的 IPv6 支持) 

本文介绍如何使用 Azure 门户添加 IPv6 支持，以通过 ExpressRoute 连接到 Azure 中的资源。 

> [!Note]
> 此功能目前可在 [具有可用性区域的 Azure 区域](https://docs.microsoft.com/azure/availability-zones/az-region#azure-regions-with-availability-zones)中预览。 因此，可以使用任何对等互连位置创建 ExpressRoute 线路，但它所连接到的基于 IPv6 的部署必须位于具有可用性区域的区域中。

## <a name="register-for-public-preview"></a>注册公共预览版
在添加 IPv6 支持之前，必须先注册订阅。 若要注册，请通过 Azure PowerShell 执行以下操作：
1.  登录到 Azure 并选择订阅。 你必须为包含 ExpressRoute 线路的订阅执行此操作，以及包含 Azure 部署 (的订阅（如果它们) 不同）。

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

2. 使用以下命令注册公共预览版订阅：
    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowIpv6PrivatePeering -ProviderNamespace Microsoft.Network
    ```

然后，ExpressRoute 团队将在2-3 个工作日内批准你的请求。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

从浏览器中转到 [Azure 门户](https://portal.azure.com)，然后使用 Azure 帐户登录。

## <a name="add-ipv6-private-peering-to-your-expressroute-circuit"></a>将 IPv6 专用对等互连添加到 ExpressRoute 线路

1. [创建 ExpressRoute 线路](https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager) ，或导航到要更改的现有线路。

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-circuit.png" alt-text="导航到线路":::

2. 选择 **Azure 专用** 对等互连配置。

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-peering.png" alt-text="导航到对等互连":::

3. 通过选择 "两者" 作为 **子网**，或者只通过选择 "ipv6"，在新线路上启用 ipv6 专用对等互连。 为主链接和辅助链接提供一对/126 IPv6 子网。 在每个子网中，当 Microsoft 将第二个可用的 IP 用于其路由器时，你需为路由器分配第一个可用的 IP 地址。 指定所有参数后，**保存** 对等互连配置。

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-peering.png" alt-text="添加 IPv6 专用对等互连":::

4. 成功接受配置后，应会看到类似于以下示例的内容。

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/view-ipv6-peering.png" alt-text="查看 IPv6 专用对等互连":::

## <a name="update-your-connection-to-an-existing-virtual-network"></a>更新与现有虚拟网络的连接

如果你在某个区域中具有 Azure 资源的现有环境，可用性区域并且你想要将 IPv6 专用对等互连用于，请遵循以下步骤。

1. 导航到 ExpressRoute 线路连接到的虚拟网络。

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-vnet.png" alt-text="导航到 vnet":::

2. 导航到 " **地址空间** " 选项卡，然后将 IPv6 地址空间添加到虚拟网络。 **保存** 地址空间。

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-space.png" alt-text="添加 IPv6 地址空间":::

3. 导航到 " **子网** " 选项卡，然后选择 " **GatewaySubnet**"。 选中 " **添加 ipv6 地址空间** " 并为子网提供 IPv6 地址空间。 网关 IPv6 子网应为/64 或更大。 指定所有参数后，请 **保存** 配置。

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-gateway-space.png" alt-text="将 IPv6 地址空间添加到子网":::

4. 如果已有区域冗余的网关，请导航到 ExpressRoute 网关，并刷新资源以启用 IPv6 连接。 否则，请使用区域冗余 SKU (ErGw1AZ，ErGw2AZ，ErGw3AZ) [创建虚拟网络网关](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager) 。 如果计划使用 FastPath，请使用 ErGw3AZ。

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/refresh-gateway.png" alt-text="刷新网关":::

## <a name="create-a-connection-to-a-new-virtual-network"></a>创建与新虚拟网络的连接

如果计划使用 IPv6 专用对等互连在可用性区域区域中连接到一组新的 Azure 资源，请执行以下步骤。

1. 使用 IPv4 和 IPv6 地址空间创建双堆栈虚拟网络。 有关详细信息，请参阅 [创建虚拟网络](https://docs.microsoft.com/azure/virtual-network/quick-create-portal#create-a-virtual-network)。

2. [创建双堆栈网关子网](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager#create-the-gateway-subnet)。

3. 使用区域冗余 SKU (ErGw1AZ，ErGw2AZ，ErGw3AZ) [创建虚拟网络网关](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager#create-the-virtual-network-gateway)。 如果计划使用 FastPath，请使用 ErGw3AZ。

4. 将[虚拟网络链接到 ExpressRoute 线路](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager)。

## <a name="limitations"></a>限制
虽然 IPv6 支持可用于连接到可用性区域区域中的部署，但它不支持以下用例：

* 通过非 AZ ExpressRoute 网关 SKU 连接到 Azure 中的部署
* 与非 AZ 区域中的部署的连接
* Global Reach ExpressRoute 线路之间的连接
* 在 vWAN 中使用 ExpressRoute

## <a name="next-steps"></a>后续步骤

若要排查 ExpressRoute 问题，请参阅以下文章：

* [验证 ExpressRoute 连接](expressroute-troubleshooting-expressroute-overview.md)
* [网络性能故障排除](expressroute-troubleshooting-network-performance.md)
