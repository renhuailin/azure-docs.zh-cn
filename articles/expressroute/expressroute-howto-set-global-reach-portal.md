---
title: Azure ExpressRoute：使用 Azure 门户配置 Global Reach
description: 本文可帮助你将 ExpressRoute 线路链接在一起，以便在本地网络之间建立专用网络，并使用 Azure 门户启用 Global Reach。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/11/2021
ms.author: duau
ms.openlocfilehash: 8366978d50875389ce872c2d1402f0defa2a7371
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539343"
---
# <a name="configure-expressroute-global-reach-using-the-azure-portal"></a>使用 Azure 门户配置 ExpressRoute Global Reach

本文帮助你使用 PowerShell 配置 ExpressRoute Global Reach。 有关详细信息，请参阅 [ExpressRouteRoute Global Reach](expressroute-global-reach.md)。

 ## <a name="before-you-begin"></a>在开始之前

在开始配置之前，请确认以下标准：

* 了解 ExpressRoute 线路预配 [工作流](expressroute-workflows.md)。
* ExpressRoute 线路处于预配状态。
* 在 ExpressRoute 线路上配置了 Azure 专用对等互连。
* 如果要在本地运行 PowerShell，请验证计算机上是否安装了最新版本的 Azure PowerShell。

## <a name="identify-circuits"></a>识别线路

1. 从浏览器导航到 [Azure 门户](https://portal.azure.com)并使用 Azure 帐户登录。

2. 确定要使用的 ExpressRoute 线路。 您可以在任意两个 ExpressRoute 线路的专用对等互连之间启用 ExpressRoute Global Reach，前提是它们位于支持的国家/地区。 需要在不同对等位置创建线路。 

   * 如果你的订阅同时拥有这两条线路，则可以选择其中任一条线路来运行以下各部分中的配置。
   * 如果两个线路位于不同的 Azure 订阅中，则你需要获得一个 Azure 订阅的授权。 然后，你在另一个 Azure 订阅中运行配置命令时传入授权密钥。

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/expressroute-circuit-global-reach-list.png" alt-text="ExpressRoute 线路列表":::

## <a name="enable-connectivity"></a>启用连接

启用本地网络之间的连接。 对于同一 Azure 订阅中的线路以及不同订阅的线路，都有单独的说明集。

### <a name="expressroute-circuits-in-the-same-azure-subscription"></a>同一 Azure 订阅中的 ExpressRoute 线路

1. 选择 **Azure 专用** 对等互连配置。 

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/expressroute-circuit-private-peering.png" alt-text="ExpressRoute 对等互连概述":::

1. 选中 " **启用 Global Reach** " 复选框，然后选择 " **添加 Global Reach** 以打开" *添加 Global Reach* 配置 "页。

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/private-peering-enable-global-reach.png" alt-text="启用来自专用对等互连的全局访问":::

1. 在 " *添加 Global Reach* 配置" 页上，为此配置指定一个名称。 选择要将此线路连接到的 *ExpressRoute 线路*，并为 *Global Reach 子网* 输入 **/29 个 IPv4** 。 我们使用此子网中的 IP 地址在两条 ExpressRoute 线路之间建立连接。 请勿在 Azure 虚拟网络或本地网络中使用此子网中的地址。 选择 " **添加** " 将线路添加到专用对等互连配置。

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/add-global-reach-configuration.png" alt-text="Global Reach 配置页":::

1. 选择 " **保存** " 以完成 Global Reach 配置。 操作完成后，你将在两个本地网络之间通过 ExpressRoute 线路连接。

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/save-private-peering-configuration.png" alt-text="保存专用对等互连配置":::

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>不同 Azure 订阅中的 ExpressRoute 线路

如果两个线路不在同一 Azure 订阅中，你将需要授权。 在下面的配置中，授权是从2个线路的订阅生成的。 然后，将授权密钥传递到线路1。

1. 生成授权密钥。

   :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/create-authorization-expressroute-circuit.png" alt-text="生成授权密钥"::: 

   记下线路2的线路资源 ID 和授权密钥。

1. 选择 **Azure 专用** 对等互连配置。 

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/expressroute-circuit-private-peering.png" alt-text="线路1对等互连概述":::

1. 选中 " **启用 Global Reach** " 复选框，然后选择 " **添加 Global Reach** 以打开" *添加 Global Reach* 配置 "页。

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/private-peering-enable-global-reach.png" alt-text="启用从线路1进行的全局接触":::

1. 在 " *添加 Global Reach* 配置" 页上，为此配置指定一个名称。 选中 " **兑换授权** " 复选框。 输入在步骤1中生成并获得的 **授权密钥** 和 **ExpressRoute 线路 ID** 。 然后为 *Global Reach 子网* 提供一个 **/29 的 IPv4** 。 我们使用此子网中的 IP 地址在两条 ExpressRoute 线路之间建立连接。 请勿在 Azure 虚拟网络或本地网络中使用此子网中的地址。 选择 " **添加** " 将线路添加到专用对等互连配置。

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/add-global-reach-configuration-with-authorization.png" alt-text="添加具有授权密钥的 Global Reach":::

1. 选择 " **保存** " 以完成 Global Reach 配置。 操作完成后，你将在两个本地网络之间通过 ExpressRoute 线路连接。

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/save-private-peering-configuration.png" alt-text="正在线路1上保存专用对等互连配置":::

## <a name="verify-the-configuration"></a>验证配置

通过在 ExpressRoute 线路配置下选择 *专用对等互连* 来验证 Global Reach 配置。 正确配置后，配置应如下所示：

:::image type="content" source="./media/expressroute-howto-set-global-reach-portal/verify-global-reach-configuration.png" alt-text="验证 Global Reach 配置":::

## <a name="disable-connectivity"></a>禁用连接

禁用 Global Reach 有两个选项。 若要禁用所有线路之间的连接，请取消选中 " **启用 Global Reach** 以禁用所有线路之间的连接。 若要禁用单个线路之间的连接，请选择 *Global Reach 名称* 旁的 "删除" 按钮以删除它们之间的连接。 然后选择 " **保存** " 以完成操作。

:::image type="content" source="./media/expressroute-howto-set-global-reach-portal/disable-global-reach-configuration.png" alt-text="禁用 Global Reach 配置":::

操作完成后，你将不再能够通过 ExpressRoute 线路连接到本地网络。

## <a name="next-steps"></a>后续步骤
1. [详细了解 ExpressRoute Global Reach](expressroute-global-reach.md)
2. [验证 ExpressRoute 连接](expressroute-troubleshooting-expressroute-overview.md)
3. [将 ExpressRoute 线路链接到 Azure 虚拟网络](expressroute-howto-linkvnet-arm.md)
