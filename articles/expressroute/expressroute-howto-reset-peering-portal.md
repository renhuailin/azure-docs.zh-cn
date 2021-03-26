---
title: Azure ExpressRoute：使用 Azure 门户重置线路对等互连
description: 了解如何使用 Azure 门户禁用和启用 Azure ExpressRoute 线路的对等互连。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 11/30/2020
ms.author: duau
ms.openlocfilehash: 432ecedbbb8965926499380eb1165fdf43018426
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "97680280"
---
# <a name="reset-expressroute-circuit-peerings-by-using-the-azure-portal"></a>使用 Azure 门户重置 ExpressRoute 线路对等互连

本文介绍了如何使用 Azure 门户禁用和启用 Azure ExpressRoute 线路的对等互连。 禁用对等互连后，ExpressRoute 线路的主连接和辅助连接上的边界网关协议 (BGP) 会话都将关闭。 启用对等互连后，ExpressRoute 线路的主连接和辅助连接上的 BGP 会话都将还原。

> [!Note]
> 首次在 ExpressRoute 线路上配置对等互连时，将默认启用对等互连。

在以下情况中，重置 ExpressRoute 对等互连可能会有所帮助：

* 你在测试你的灾难恢复设计和实现。 例如，假设你有两条 ExpressRoute 线路。 你可以禁用一条线路的对等互连，并强制网络流量使用另一条线路。

* 你希望在 Azure 专用对等互连或 Microsoft 对等互连上启用双向转发检测 (BFD)。 如果 ExpressRoute 线路是在 2018 年 8 月 1 日之前在 Azure 专用对等互连上创建的，或者是在 2020 年 1 月 10 日之前创建的，则 BFD 没有默认启用。 请重置对等互连以启用 BFD。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

在浏览器中，转到 [Azure 门户](https://portal.azure.com)，然后使用你的 Azure 帐户登录。

## <a name="reset-a-peering"></a>重置对等互连

你可以分别重置 ExpressRoute 线路上的 Microsoft 对等互连和 Azure 专用对等互连。

1. 选择你要更改的线路。

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit-list.png" alt-text="屏幕截图显示了在 ExpressRoute 线路列表中选择线路。":::

1. 选择你要重置的对等互连配置。

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit.png" alt-text="屏幕截图显示了在 ExpressRoute 线路概览中选择对等互连。":::

1. 清除“启用对等互连”复选框，然后选择“保存”以禁用对等互连配置。

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/disable-peering.png" alt-text="屏幕截图显示了清除“启用对等互连”复选框。":::

1. 选择“启用对等互连”复选框，然后选择“保存”以启用对等互连配置。

## <a name="next-steps"></a>后续步骤

若要排查 ExpressRoute 问题，请参阅以下文章：

* [验证 ExpressRoute 连接](expressroute-troubleshooting-expressroute-overview.md)
* [网络性能故障排除](expressroute-troubleshooting-network-performance.md)
