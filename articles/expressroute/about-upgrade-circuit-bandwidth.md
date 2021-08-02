---
title: 关于升级线路带宽 | Azure ExpressRoute
description: 本文介绍升级 ExpressRoute 线路带宽的最佳做法
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 06/08/2021
ms.author: duau
ms.openlocfilehash: 75570beb243fca802ecebebf34edf02b3d8ca9a5
ms.sourcegitcommit: 190658142b592db528c631a672fdde4692872fd8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2021
ms.locfileid: "112006484"
---
# <a name="about-upgrading-expressroute-circuit-bandwidth"></a>关于升级 ExpressRoute 线路带宽

ExpressRoute 是连接 Microsoft 全球网络的专用连接服务。 可以通过 ExpressRoute 合作伙伴的网络或与 Microsoft Enterprise Edge (MSEE) 设备的直接连接来快速建立连接。 配置并测试物理连接后，可以通过创建 ExpressRoute 线路并配置对等互连来启用第 2 层和第 3 层连接。

## <a name="upgrade-circuit-bandwidth"></a><a name="upgrade"></a>升级线路带宽

若要升级线路带宽，ExpressRoute Direct（或 ExpressRoute 合作伙伴）需要有[足够的可用带宽](#considerations)才能成功升级。

如果有容量，可以使用以下方法升级线路：

* [Azure 门户](expressroute-howto-circuit-portal-resource-manager.md#modify)
* [PowerShell](expressroute-howto-circuit-arm.md#modify)
* [Azure CLI](howto-circuit-cli.md#modify)

## <a name="capacity-considerations"></a><a name="considerations"></a>容量注意事项

### <a name="insufficient-capacity-for-physical-connection"></a>物理连接容量不足

ExpressRoute 线路是基于 Microsoft 和 ExpressRoute 合作伙伴之间的物理连接创建的。 物理连接具有固定的容量。 如果无法扩大线路规模，则意味着现有线路的基础物理连接没有容量进行升级。 如果要改变线路规模，则需要创建新线路。

成功创建新 ExpressRoute 线路后，应将现有虚拟网络链接到此线路。 然后可以先测试和验证新 ExpressRoute 线路的连接性，再取消旧线路。 建议采用这些迁移步骤，最大程度地减少停机时间，减少中断生产工作负载。

### <a name="insufficient-expressroute-partner-bandwidth"></a><a name="bandwidth"></a>ExpressRoute 合作伙伴带宽不足

如果因容量错误，无法创建新 ExpressRoute 线路， 则意味着此 ExpressRoute 合作伙伴的这一对等互连位置容量不足，无法连接到 Microsoft。 请联系 ExpressRoute 合作伙伴，以请求更多容量。

预配新容量后，可按[升级线路带宽](#upgrade)部分包含的步骤创建新线路，配置连接性以及删除旧线路。


### <a name="insufficient-expressroute-direct-bandwidth"></a><a name="bandwidth"></a>ExpressRoute Direct 带宽不足

如果 ExpressRoute Direct 容量不足，则有两种可选方案。 你可以删除与不再需要的 ExpressRoute Direct 资源关联的线路，也可以创建新 ExpressRoute Direct 资源。 有关管理 ExpressRoute Direct 资源的指导，请参阅[如何配置 ExpressRoute Direct](how-to-expressroute-direct-portal.md)。

## <a name="next-steps"></a>后续步骤

* [创建和修改线路](expressroute-howto-circuit-portal-resource-manager.md)
* [创建和修改对等配置](expressroute-howto-routing-portal-resource-manager.md)
* [将虚拟网络链接到 ExpressRoute 线路](expressroute-howto-linkvnet-portal-resource-manager.md)
