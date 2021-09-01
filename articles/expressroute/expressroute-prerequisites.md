---
title: Azure ExpressRoute：先决条件
description: 本页提供了在订购 Azure ExpressRoute 线路之前需要满足的要求列表。 其中包括了一个清单。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: duau
ms.openlocfilehash: 6f5b3cc109d3583defce78c3ae52848188fca900
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114284546"
---
# <a name="expressroute-prerequisites--checklist"></a>ExpressRoute 先决条件和清单
若要使用 ExpressRoute 连接到 Microsoft 云服务，需确认是否符合以下部分中所列的要求。

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

## <a name="azure-account"></a>Azure 帐户
* 使用中的有效 Microsoft Azure 帐户。 需有此帐户才能设置 ExpressRoute 线路。 ExpressRoute 线路是 Azure 订阅中的资源。 即使连接限于非 Azure Microsoft 云服务（如 Microsoft 365），Azure 订阅也是一个必要条件。
* 激活 Microsoft 365 订阅（如果要使用 Microsoft 365 服务）。 有关详细信息，请参阅本文中的“Microsoft 365 的具体要求”部分。

## <a name="connectivity-provider"></a>连接服务提供商

* 可使用 [ExpressRoute 连接合作伙伴](expressroute-locations.md#partners) 连接到 Microsoft 云。 有 [三种方法](expressroute-introduction.md)可以在本地网络与 Microsoft 之间建立连接。
* 即使提供商不是 ExpressRoute 连接合作伙伴，也可以通过 [云交换提供商](expressroute-locations.md#connectivity-through-exchange-providers)连接到 Microsoft 云。

## <a name="network-requirements"></a>网络要求
* **每个对等互连位置存在冗余**：Microsoft 要求在 Microsoft 的路由器和每条 ExpressRoute 线路上的对等互连路由器之间建立冗余性的 BGP 会话（即使只有 [一个与云交换器的物理连接](expressroute-faqs.md#onep2plink)）。
* **灾难恢复冗余**：Microsoft 强烈建议在不同的对等互连位置中至少设置两条 ExpressRoute 线路，以免出现单点故障。
* **路由**：你或提供商需设置和管理针对 [路由域](expressroute-circuit-peerings.md)的 BGP 会话，具体取决于连接到 Microsoft 云的方式。 某些以太网连接服务提供商或云交换服务提供商可能会以增值服务的形式提供 BGP 管理。
* **NAT**：Microsoft 只能通过 Microsoft 对等互连方式接受公共 IP 地址。 如果使用的是本地网络中的专用 IP 地址，你或提供商需[使用 NAT](expressroute-nat.md)将专用 IP 地址转换为公共 IP 地址。
* **QoS**：Skype for Business 的各种服务（例如语音、视频、文本）所要求的 QoS 处理方式各有差异。 你和提供商应遵循 [QoS 要求](expressroute-qos.md)。
* **网络安全**：通过 ExpressRoute 连接到 Microsoft 云时，应考虑 [网络安全](/azure/cloud-adoption-framework/reference/networking-vdc)。

## <a name="microsoft-365"></a>Microsoft 365
如果打算在 ExpressRoute 上启用 Microsoft 365，请参阅以下文档，详细了解 Microsoft 365 方面的要求。

* [适用于 Microsoft 365 的 Azure ExpressRoute](/microsoft-365/enterprise/azure-expressroute)
* [通过适用于 Microsoft 365 的 ExpressRoute 进行路由](/microsoft-365/enterprise/routing-with-expressroute)
* [使用 ExpressRoute 实现高可用性和故障转移](/microsoft-365/enterprise/network-planning-with-expressroute)
* [Microsoft 365 URL 和 IP 地址范围](/microsoft-365/enterprise/urls-and-ip-address-ranges)
* [针对 Microsoft 365 的网络规划和性能优化](/microsoft-365/enterprise/network-planning-and-performance)
* [Microsoft 365 的网络和迁移规划](/microsoft-365/enterprise/network-and-migration-planning)
* [Microsoft 365 与本地环境的集成](/microsoft-365/enterprise/microsoft-365-integration)
* [随时了解 Office 365 IP 地址更改的最新动态](/microsoft-365/enterprise/microsoft-365-ip-web-service?view=o365-worldwide)
* [Office 365 中的 ExpressRoute 高级培训视频](https://channel9.msdn.com/series/aer/)

## <a name="next-steps"></a>后续步骤
* 有关 ExpressRoute 的详细信息，请参阅 [ExpressRoute 常见问题](expressroute-faqs.md)。
* 查找 ExpressRoute 连接服务提供商。 请参阅 [ExpressRoute 合作伙伴和对等位置](expressroute-locations.md)。
* 请参阅[路由](expressroute-routing.md)、[NAT](expressroute-nat.md) 和 [QoS](expressroute-qos.md) 的要求。
* 配置 ExpressRoute 连接。
  * [创建 ExpressRoute 线路](expressroute-howto-circuit-arm.md)
  * [配置路由](expressroute-howto-routing-arm.md)
  * [将 VNet 链接到 ExpressRoute 线路](expressroute-howto-linkvnet-arm.md)