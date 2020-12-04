---
title: Azure ExpressRoute：使用 Azure 门户重置线路对等互连
description: 了解如何使用 Azure 门户禁用和启用 Azure ExpressRoute 线路的对等互连。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 11/30/2020
ms.author: duau
ms.openlocfilehash: d4d6b0b0cce4f5304f7c5790ef2bda05633be52f
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96581648"
---
# <a name="reset-expressroute-circuit-peerings-use-the-azure-portal"></a>使用 Azure 门户重置 ExpressRoute 线路对等互连

本文介绍如何使用 Azure 门户禁用和启用 ExpressRoute 线路的对等互连。 禁用对等互连时，将关闭 ExpressRoute 线路的主连接和辅助连接的 BGP 会话。 你将失去通过此对等互连连接到 Microsoft 的连接。 启用对等互连时，将启动 ExpressRoute 线路的主连接和辅助连接上的 BGP 会话。 你将通过此对等互连与 Microsoft 重新获得连接。 可单独对 ExpressRoute 线路启用和禁用 Microsoft 对等互连和 Azure 专用对等互连。 首次在 ExpressRoute 线路上配置对等互连时，默认情况下会启用对等互连。

存在几个有助于重置 ExpressRoute 对等互连的方案。
* 测试灾难恢复设计和实现。 例如，你有两条 ExpressRoute 线路。 可以禁用一条线路的对等互连，并强制网络流量故障转移到另一条线路。
* 对 ExpressRoute 线路的 Azure 专用对等互连或 Microsoft 对等互连启用双向转发检测 (BFD)。 如果你的 ExpressRoute 线路是在 1 2018 年8月之后创建的，则在 Azure 专用对等互连上，BFD 默认启用。 如果你的 ExpressRoute 线路在 10 2020 年1月之后创建。 如果线路在此之前创建，则未启用 BFD。 可以通过禁用对等互连并重新启用它来启用 BFD。 

### <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

从浏览器导航到 [Azure 门户](https://portal.azure.com)并使用 Azure 帐户登录。

## <a name="reset-a-peering"></a>重置对等互连

1. 选择要进行对等配置更改的线路。

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit-list.png" alt-text="ExpressRoute 线路列表":::

1. 选择要启用或禁用的对等互连配置。

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit.png" alt-text="ExpressRoute 线路概述":::

1. 取消选中 " **启用对等互连** " 并选择 " **保存** " 以禁用对等互连配置。

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/disable-peering.png" alt-text="禁用专用对等互连":::

1. 可以通过选中 " **启用对等互连** " 并选择 " **保存**" 来再次启用对等互连。

## <a name="next-steps"></a>后续步骤
如果需要帮助排查 ExpressRoute 问题，请查看以下文章：
* [验证 ExpressRoute 连接](expressroute-troubleshooting-expressroute-overview.md)
* [网络性能故障排除](expressroute-troubleshooting-network-performance.md)
