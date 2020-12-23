---
title: Azure ExpressRoute：配置 BFD
description: 本文说明如何通过 ExpressRoute 线路的专用对等互连配置 BFD（双向转发检测）。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 12/14/2020
ms.author: duau
ms.openlocfilehash: 254f5909e7ed8db4dc18ade2677a3213b268cf41
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/15/2020
ms.locfileid: "97511257"
---
# <a name="configure-bfd-over-expressroute"></a>配置基于 ExpressRoute 的 BFD

ExpressRoute 支持基于专用对等互连和 Microsoft 对等互连的双向转发检测 (BFD)。 通过 ExpressRoute 启用 BFD 时，可以加快 Microsoft 企业边缘 (MSEE) 设备和你的 ExpressRoute 线路设备与 (CE/PE) 配置的路由器之间的链接故障检测速度。 你可以在边缘路由设备或合作伙伴边缘路由设备上配置 ExpressRoute， (如果你具有第3层连接服务) 。 本文档将逐步讲解 BFD 的需求，以及如何启用基于 ExpressRoute 的 BFD。

## <a name="need-for-bfd"></a>BFD 的需求

下图演示了启用基于 ExpressRoute 线路的 BFD 的好处：[![1]][1]

可以通过第 2 层连接或托管的第 3 层连接启用 ExpressRoute 线路。 在这两种情况下，如果 ExpressRoute 连接路径中有多个第2层设备，则检测路径中的任何链接失败的责任与过量 BGP 会话有关。

在 MSEE 设备上，BGP keep-alive 和持续时间通常分别配置为60和180秒。 出于这种原因，当链接失败时，可能需要长达三分钟的时间来检测任何链接故障，并将流量切换到备用连接。

可以通过在边缘对等互连设备上配置较低的 BGP 保持活动状态和保持时间，来控制 BGP 计时器。 如果两个对等设备之间的 BGP 计时器不相同，则将使用较小时间值建立 BGP 会话。 BGP keep-alive 的设置最小可设置为三秒，最长保留时间为10秒。 但是，不建议设置非常严格的 BGP 计时器，因为这种协议需要大量处理。

在这种情况下，BFD 可发挥作用。 BFD 能够以亚秒级的时间间隔提供低开销的链路故障检测。 


## <a name="enabling-bfd"></a>启用 BFD

在 MSEE 上所有新建的 ExpressRoute 专用对等互连接口中，默认已配置 BFD。 因此，若要启用 BFD，只需在主设备和辅助设备上配置 BFD。 配置 BFD 的过程分为两个步骤。 在接口上配置 BFD，并将其链接到 BGP 会话。

下面显示了 CE/PE 配置示例（使用 Cisco IOS XE）。 

```console
interface TenGigabitEthernet2/0/0.150
   description private peering to Azure
   encapsulation dot1Q 15 second-dot1q 150
   ip vrf forwarding 15
   ip address 192.168.15.17 255.255.255.252
   bfd interval 300 min_rx 300 multiplier 3


router bgp 65020
   address-family ipv4 vrf 15
      network 10.1.15.0 mask 255.255.255.128
      neighbor 192.168.15.18 remote-as 12076
      neighbor 192.168.15.18 fall-over bfd
      neighbor 192.168.15.18 activate
      neighbor 192.168.15.18 soft-reconfiguration inbound
   exit-address-family
```

>[!NOTE]
>若要在现有的专用对等互连中启用 BFD，需要重置该对等互连。 请参阅[重置 ExpressRoute 对等互连][ResetPeering]
>

## <a name="bfd-timer-negotiation"></a>BFD 计时器协商

在两个 BFD 对等方之间，速度较慢的对等方决定了传输速率。 MSEE BFD 传输/接收间隔设置为 300 毫秒。 在某些情况下，可以将间隔设置为 750 毫秒的较高值。 通过配置较大的值，您可以强制这些时间间隔更长，但是不能使它们变得更短。

>[!NOTE]
>如果已配置异地冗余 ExpressRoute 线路，或者使用站点到站点 IPSec VPN 连接作为备份。 启用 BFD 有助于在 ExpressRoute 连接失败之后更快地进行故障转移。 
>

## <a name="next-steps"></a>后续步骤

有关详细信息或帮助，请查看以下链接：

- [创建和修改 ExpressRoute 线路][CreateCircuit]
- [创建和修改 ExpressRoute 线路的路由][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-bfd/BFD_Need.png "BFD 加快链路故障推测时间"

<!--Link References-->
[CreateCircuit]: ./expressroute-howto-circuit-portal-resource-manager.md
[CreatePeering]: ./expressroute-howto-routing-portal-resource-manager.md
[ResetPeering]: ./expressroute-howto-reset-peering.md