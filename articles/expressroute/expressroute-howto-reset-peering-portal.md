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
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680280"
---
# <a name="reset-expressroute-circuit-peerings-by-using-the-azure-portal"></a>使用 Azure 门户重置 ExpressRoute 线路对等互连

本文介绍如何使用 Azure 门户禁用和启用 Azure ExpressRoute 线路的对等互连。 禁用对等互连时，将关闭 ExpressRoute 线路的主连接和辅助连接的边界网关协议 (BGP) 会话。 启用对等互连时，将还原 ExpressRoute 线路的主连接和辅助连接上的 BGP 会话。

> [!Note]
> 首次在 ExpressRoute 线路上配置对等互连时，默认情况下会启用对等互连。

在以下方案中，重置 ExpressRoute 对等互连可能会有所帮助：

* 正在测试灾难恢复设计和实现。 例如，假设您有两个 ExpressRoute 线路。 可以禁用一个线路的对等互连，并强制网络流量使用另一个线路。

* 要在 Azure 专用对等互连或 Microsoft 对等互连上启用 (BFD) 的双向转发检测。 如果 ExpressRoute 线路是在2018年8月1日之前创建的，则在 Azure 专用对等互连上或在2020之前的之前，默认情况下未启用 BFD。 重置对等互连以启用 BFD。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

从浏览器中转到 [Azure 门户](https://portal.azure.com)，然后使用 Azure 帐户登录。

## <a name="reset-a-peering"></a>重置对等互连

你可以单独重置 ExpressRoute 线路上的 Microsoft 对等互连和 Azure 专用对等互连。

1. 选择要更改的线路。

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit-list.png" alt-text="显示在 ExpressRoute 线路列表中选择线路的屏幕截图。":::

1. 选择要重置的对等互连配置。

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit.png" alt-text="屏幕截图显示了如何选择 ExpressRoute 线路概述中的对等互连。":::

1. 清除 " **启用对等互连** " 复选框，然后选择 " **保存** " 以禁用对等互连配置。

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/disable-peering.png" alt-text="屏幕截图，显示清除 &quot;启用对等互连&quot; 复选框。":::

1. 选中 " **启用对等互连** " 复选框，然后选择 " **保存** " 以重新启用对等互连配置。

## <a name="next-steps"></a>后续步骤

若要排查 ExpressRoute 问题，请参阅以下文章：

* [验证 ExpressRoute 连接](expressroute-troubleshooting-expressroute-overview.md)
* [网络性能故障排除](expressroute-troubleshooting-network-performance.md)
