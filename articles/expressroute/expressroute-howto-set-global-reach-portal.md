---
title: Azure ExpressRoute：使用 Azure 门户配置 Global Reach
description: 本文介绍了如何将 ExpressRoute 线路链接到一起，以在本地网络之间建立专用网络并使用 Azure 门户启用 Global Reach。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/05/2021
ms.author: duau
ms.openlocfilehash: 336bd4aaf881b7315921ef374c92a2ac95ff3c8c
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2021
ms.locfileid: "102431293"
---
# <a name="configure-expressroute-global-reach-using-the-azure-portal"></a>使用 Azure 门户配置 ExpressRoute Global Reach

本文帮助你使用 PowerShell 配置 ExpressRoute Global Reach。 有关详细信息，请参阅 [ExpressRouteRoute Global Reach](expressroute-global-reach.md)。

 ## <a name="before-you-begin"></a>在开始之前

在开始配置之前，请确认满足以下条件：

* 你了解 ExpressRoute 线路预配[工作流](expressroute-workflows.md)。
* ExpressRoute 线路处于“已预配”状态。
* 在 ExpressRoute 线路上配置了 Azure 专用对等互连。
* 如果要在本地运行 PowerShell，请验证计算机上是否安装了最新版本的 Azure PowerShell。

## <a name="identify-circuits"></a>确定线路

1. 从浏览器导航到 [Azure 门户](https://portal.azure.com)并使用 Azure 帐户登录。

2. 确定要使用的 ExpressRoute 线路。 可以在任意两条 ExpressRoute 线路的专用对等互连之间启用 ExpressRoute Global Reach，只要它们位于受支持的国家/地区即可。 需要在不同对等互连位置创建线路。 

   * 如果你的订阅同时拥有这两条线路，则可以选择其中任一条线路来运行以下各部分中的配置。
   * 如果两个线路位于不同的 Azure 订阅中，则你需要获得一个 Azure 订阅的授权。 然后，你在另一个 Azure 订阅中运行配置命令时传入授权密钥。

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/expressroute-circuit-global-reach-list.png" alt-text="ExpressRoute 线路列表的屏幕截图。":::

## <a name="enable-connectivity"></a>启用连接

启用本地网络之间的连接。 同一个 Azure 订阅中的线路以及不同订阅的线路都有单独的说明集。

### <a name="expressroute-circuits-in-the-same-azure-subscription"></a>同一个 Azure 订阅中的 ExpressRoute 线路

1. 选择“Azure 专用”对等互连配置。 

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/expressroute-circuit-private-peering.png" alt-text="ExpressRoute 概述页的屏幕截图。":::

1. 选择“添加 Global Reach”以打开“添加 Global Reach”配置页。

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/private-peering-enable-global-reach.png" alt-text="从专用对等互连启用 Global Reach":::

1. 在“添加 Global Reach”配置页上，为此配置指定一个名称。 选择要连接此线路的 ExpressRoute 线路，对于“Global Reach 子网”，输入“/29 IPv4”。 我们使用此子网中的 IP 地址在两条 ExpressRoute 线路之间建立连接。 请勿在 Azure 虚拟网络或本地网络中使用此子网中的地址。 选择“添加”将线路添加到专用对等互连配置。

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/add-global-reach-configuration.png" alt-text="将 Global Reach 添加到专用对等互连的屏幕截图。":::

1. 选择“保存”，以完成 Global Reach 配置。 操作完成后，两个本地网络之间通过 ExpressRoute 线路进行连接。

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/save-private-peering-configuration.png" alt-text="保存专用对等互连配置的屏幕截图。":::

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>不同 Azure 订阅中的 ExpressRoute 线路

如果两条线路不在同一个 Azure 订阅中，则需要获得授权。 在以下配置中，授权从线路 2 的订阅生成。 然后，将授权密钥传递到线路 1。

1. 生成授权密钥。

   :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/create-authorization-expressroute-circuit.png" alt-text="生成授权密钥的屏幕截图。"::: 

   记下线路 2 的线路资源 ID 以及授权密钥。

1. 选择“Azure 专用”对等互连配置。 

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/expressroute-circuit-private-peering.png" alt-text="概述页上的专用对等互连的屏幕截图。":::

1. 选择“添加 Global Reach”以打开“添加 Global Reach”配置页。

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/private-peering-enable-global-reach.png" alt-text="将 Global Reach 添加到专用对等互连的屏幕截图。":::

1. 在“添加 Global Reach”配置页上，为此配置指定一个名称。 选中“兑换授权”复选框。 输入在步骤 1 中生成并获得的“授权密钥”和“ExpressRoute 线路 ID”。 对于“Global Reach 子网”，请提供“/29 IPv4”。 我们使用此子网中的 IP 地址在两条 ExpressRoute 线路之间建立连接。 请勿在 Azure 虚拟网络或本地网络中使用此子网中的地址。 选择“添加”将线路添加到专用对等互连配置。

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/add-global-reach-configuration-with-authorization.png" alt-text="添加 Global Reach 和授权密钥的屏幕截图。":::

1. 选择“保存”，以完成 Global Reach 配置。 操作完成后，两个本地网络之间通过 ExpressRoute 线路进行连接。

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/save-private-peering-configuration.png" alt-text="保存 Global Reach 专用对等互连配置的屏幕截图。":::

## <a name="verify-the-configuration"></a>验证配置

通过在 ExpressRoute 线路配置下选择“专用对等互连”来验证 Global Reach 配置。 正确配置后，配置应如下所示：

:::image type="content" source="./media/expressroute-howto-set-global-reach-portal/verify-global-reach-configuration.png" alt-text="已配置的 Global Reach 的屏幕截图。":::

## <a name="disable-connectivity"></a>禁用连接

若要禁用单个线路之间的连接，请选择“Global Reach 名称”旁的“删除”按钮以删除它们之间的连接。 然后选择“保存”以完成操作。

:::image type="content" source="./media/expressroute-howto-set-global-reach-portal/disable-global-reach-configuration.png" alt-text="显示如何禁用 Global Reach 的屏幕截图。":::

当这些操作完成后，在本地网络之间将不再具有通过 ExpressRoute 线路建立的连接。

## <a name="next-steps"></a>后续步骤
- [详细了解 ExpressRoute Global Reach](expressroute-global-reach.md)
- [验证 ExpressRoute 连接](expressroute-troubleshooting-expressroute-overview.md)
- [将 ExpressRoute 线路链接到 Azure 虚拟网络](expressroute-howto-linkvnet-arm.md)
