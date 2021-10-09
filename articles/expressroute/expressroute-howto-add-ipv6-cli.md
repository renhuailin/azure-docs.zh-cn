---
title: Azure ExpressRoute：使用 Azure CLI 添加 IPv6 支持
description: 了解如何使用 Azure CLI 添加 IPv6 支持以连接到 Azure 部署。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 09/27/2021
ms.author: duau
ms.openlocfilehash: c93d56cb7f8cc6ffe897cb8a9321806b4a2c40ed
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2021
ms.locfileid: "129094826"
---
# <a name="add-ipv6-support-for-private-peering-using-azure-cli-preview"></a>使用 Azure CLI 添加对专用对等互连的 IPv6 支持（预览版）

本文介绍如何使用 Azure CLI 添加 IPv6 支持，以通过 ExpressRoute 连接到 Azure 中的资源。

## <a name="prerequisites"></a>先决条件

* 在开始配置之前，请查看[先决条件](expressroute-prerequisites.md)和[工作流](expressroute-workflows.md)。
* 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 安装最新版本的 CLI 命令（2.0 或更高版本）。 有关安装 CLI 命令的信息，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli.md) 和 [Azure CLI 入门](/cli/azure/get-started-with-azure-cli.md)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="add-ipv6-private-peering-to-your-expressroute-circuit"></a>将 IPv6 专用对等互连添加到 ExpressRoute 线路

1. [创建 ExpressRoute 线路](howto-circuit-cli.md)或使用现有线路。 通过运行以下命令来查看线路详细信息：

    ```azurecli-interactive
    az network express-route show --resource-group "<ExpressRouteResourceGroup>" --name "<MyCircuit>"
    ```

2. 通过运行以下命令来查看线路的专用对等互连配置：

    ```azurecli-interactive
    az network express-route peering show -g "<ExpressRouteResourceGroup>" --circuit-name "<MyCircuit>" --name AzurePrivatePeering
    ```

3. 将 IPv6 专用对等互连添加到现有的 IPv4 专用对等互连配置中。 为主链接和辅助链接提供一对自己拥有的 /126 IPV6 子网。 在每个子网中，当 Microsoft 将第二个可用的 IP 用于其路由器时，你需为路由器分配第一个可用的 IP 地址。

    ```azurecli-interactive
    az network express-route peering update -g "<ExpressRouteResourceGroup>" --circuit-name "<MyCircuit>" --name AzurePrivatePeering --ip-version ipv6 --primary-peer-subnet "<X:X:X:X/126>" --secondary-peer-subnet "<Y:Y:Y:Y/126>"
    ```

## <a name="update-your-connection-to-an-existing-virtual-network"></a>更新与现有虚拟网络的连接

如果你已有 Azure 资源环境，并且你想要对该环境使用 IPv6 专用对等互连，请执行以下步骤。

1. 将 IPv6 地址空间添加到 ExpressRoute 线路连接到的虚拟网络。

    ```azurecli-interactive
    az network vnet update -g "<MyResourceGroup>" -n "<MyVNet>" --address-prefixes "X:X:X:X::/64"
    ```

3. 将 IPv6 地址空间添加到网关子网。 网关 IPv6 子网应为 /64 或更大。

    ```azurecli-interactive
    az network vnet subnet update -g "<MyResourceGroup>" -n "<MySubnet>" -vnet-name "<MyVNet>" --address-prefixes "10.0.0.0/26", "X:X:X:X::/64"
    ```

4. 如果已有区域冗余网关，请运行以下各项来启用 IPv6 连接（请注意，更改可能需要 1 小时才能反映出来）。 否则，请使用任意 SKU [创建虚拟网络网关](expressroute-howto-add-gateway-resource-manager.md)。 如果计划使用 FastPath，请使用 UltraPerformance 或 ErGw3AZ（请注意，此选项仅适用于使用 ExpressRoute Direct 的线路）。

    ```azurecli-interactive
    az network vnet-gateway update --name "<GatewayName>" --resource-group "<MyResourceGroup>"
    ```
>[!NOTE]
> 如果现有网关不是区域冗余的（这意味着它是标准、高性能或超高性能 SKU）并且使用基本 SKU 的公共 IP 地址，则需要使用任意 SKU 和标准静态公共 IP 地址来删除并[重新创建网关](expressroute-howto-add-gateway-resource-manager.md#add-a-gateway)。

## <a name="create-a-connection-to-a-new-virtual-network"></a>创建与新虚拟网络的连接

如果计划使用 IPv6 专用对等互连连接到一组新的 Azure 资源，请执行以下步骤。

1. 创建包含 IPv4 和 IPv6 地址空间的双堆栈虚拟网络。 有关详细信息，请参阅[创建虚拟网络](../virtual-network/quick-create-cli.md)。

2. [创建双堆栈网关子网](expressroute-howto-add-gateway-resource-manager.md#add-a-gateway)。

3. 使用任意 SKU [创建虚拟网络网关](expressroute-howto-add-gateway-resource-manager.md#add-a-gateway)。 如果计划使用 FastPath，请使用 UltraPerformance 或 ErGw3AZ（请注意，此选项仅适用于使用 ExpressRoute Direct 的线路）。

4. [将虚拟网络链接到 ExpressRoute 线路](howto-linkvnet-cli.md)。

## <a name="limitations"></a>限制
虽然 IPv6 支持可用于连接到全局 Azure 区域中的部署，但它不支持以下用例：

* 连接到非区域冗余的现有 ExpressRoute 网关。 请注意，使用标准静态 IP 地址新创建的任何 SKU（区域冗余和非区域冗余）ExpressRoute 网关均可用于双堆栈 ExpressRoute 连接
* ExpressRoute 线路之间的 Global Reach 连接
* 将 ExpressRoute 用于虚拟 WAN
* FastPath 与非 ExpressRoute Direct 线路
* FastPath 与以下对等互连位置的线路：迪拜
* 与 VPN 网关共存

## <a name="next-steps"></a>后续步骤

若要排查 ExpressRoute 问题，请参阅以下文章：

* [验证 ExpressRoute 连接](expressroute-troubleshooting-expressroute-overview.md)
* [网络性能故障排除](expressroute-troubleshooting-network-performance.md)
