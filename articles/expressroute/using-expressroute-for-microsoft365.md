---
title: 将 ExpressRoute 用于 Microsoft 365 服务 |Microsoft Docs
description: 本文档将客观讨论将 ExpressRoute 线路用于 Microsoft 365 SaaS 服务的情况。
documentationcenter: na
services: expressroute
author: rambk
manager: tracsman
ms.service: expressroute
ms.topic: article
ms.workload: infrastructure-services
ms.date: 4/29/2021
ms.author: rambala
ms.openlocfilehash: 6e6b543741a27b7a99ef447e7e34fbec803403db
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111970513"
---
# <a name="using-expressroute-for-routing-microsoft-365-traffic"></a>将 ExpressRoute 用于路由 Microsoft 365 流量

ExpressRoute 线路可为 Microsoft 主干网络提供专用连接。 
* 该线路提供专用对等互连，以连接到 Azure 区域中的 IaaS 部署的专用终结点 
* 此外，改线路还提供 Microsoft 对等互连，以连接到 Microsoft 网络中 IaaS、PaaS 和 SaaS 服务的公共终结点。 

有关 ExpressRoute 的详细信息，请参阅 [ExpressRoute 技术概述][ExR-Intro]一文。


ExpressRoute 是否可以用于路由 Microsoft 365 SaaS 流量的问题常常会令人困惑不已。 

* 片面论点：ExpressRoute 确实可以提供 Microsoft 对等互连，你可以使用它访问 Microsoft 网络中的大多数公共终结点。 但事实上，可以使用路由筛选器选择需要通过 Microsoft 对等互连向本地网络播发的 Microsoft 365 服务前缀。 这些路由播发允许通过 ExpressRoute 线路路由 Microsoft 365 服务流量。 
* 相反论点：Microsoft 365 是一种分布式服务， 旨在让世界各地的客户能够使用 Internet 连接到服务。 因此，建议不要将 ExpressRoute 用于 Microsoft 365。

本文的目标是： 
* 为论点提供技术推理，并 
* 客观讨论何时使用及何时不适用 ExpressRoute 路由 Microsoft 365 流量。

## <a name="network-requirements-of-microsoft-365-traffic"></a>Microsoft 365 流量的网络要求
Microsoft 365 服务通常包括语音和视频呼叫、联机会议和实时协作等实时流量。 这种实时流量在延迟和抖动方面具有严格的网络性能要求。 在一定的网络延迟限制范围内，可以使用客户端设备上的缓冲区有效处理抖动。 网络延迟是指需要传输的物理距离流量、链路带宽和处理延迟构成的函数。 

## <a name="network-optimization-features-of-microsoft-365"></a>Microsoft 365 的网络优化功能 

Microsoft 竭力在体系结构和功能方面优化所有云应用程序的网络性能。 首先，Microsoft 拥有最大的全球网络之一，该网络经过优化，可实现提供最佳网络性能的核心目标。 Microsoft 网络是软件定义型网络，并且是“冷土豆”网络。 在某种意义上，“冷土豆”网络会吸引并输出流量，使其尽可能接近客户端设备/客户网络。 此外，Microsoft 网络高度冗余且高度可用。 有关体系结构优化的详细信息，请参阅 [Microsoft 如何构建快速且可靠的全球网络][MGN]。

为了应对严格的网络延迟要求，Microsoft 365 通过以下方式缩短路由长度：
* 将最终用户连接以动态方式路由到最近的 Microsoft 365 入口点，并且 
* 从入口点在 Microsoft 全球网络内有效地将其路由到最近（且获得授权）的 Microsoft 365 数据中心。

Azure Front Door (AFD) 可服务 Microsoft 365 入口点。 AFD 是一种在 Microsoft 全球边缘网络中提供的一种广泛分布式服务，有助于创建快速、安全且高度可缩放的 SaaS 应用程序。 如需深入了解 AFD 如何提升 Web 应用性能，请参阅[什么是 Azure Front Door？][AFD]。 选择最近的 Microsoft 365 数据中心时，Microsoft 会考虑地缘政治区域内的数据主权法规。

## <a name="what-is-geo-pinning-connections"></a>什么是地理位置固定连接？

在客户端-服务器之间强制流量经过位于某个地理位置的特定网络设备，这种情况被称为“固定网络连接的地理位置”。 采用以静态方式定位客户端-服务器的基础设计原则的传统网络体系结构通常会固定连接的地理位置。
例如，当你强制企业 Internet 连接遍历整个企业网络并从中央位置输出（通常通过一组代理服务器或防火墙）时，你便是在固定 Internet 连接的地理位置。  

同样地，在 SaaS 应用程序体系结构中，如果你强制通过位于某个区域的中间数据中心（如云安全）或通过位于某个特定位置的一个或多个中间网络设备（如 ExpressRoute）路由流量，你便是在固定 SaaS 连接的地理位置。

## <a name="when-not-to-use-expressroute-for-microsoft-365"></a>何时不将 ExpressRoute 用于 Microsoft 365？

由于 Microsoft 365 能够动态缩短路由长度并可以根据客户端位置动态选择最近的服务器数据中心，因此被称为专为 Internet 设计。 除此之外，某些 Microsoft 365 流量仅通过 Internet 进行路由。
当你的 SaaS 客户端广泛分布在某个区域或全球范围时，如果你将连接固定到某个地理位置，你便是在迫使客户端远离固定的地理位置，进而导致网络延迟更高。 更高的网络延迟会导致网络性能和应用性能欠佳。

因此，如果你的 SaaS 客户端广泛分布或你的客户端的流动性很强，你将不希望通过任何方式固定连接的地理位置，其中包括强制通过位于某个特定对等互连位置的 ExpressRoute 线路路由流量。


## <a name="when-to-use-expressroute-for-microsoft-365"></a>何时将 ExpressRoute 用于 Microsoft 365？

下面是你可能想要使用 ExpressRoute 路由 Microsoft 365 流量的部分原因：
* SaaS 客户端集中在某个地理位置，并且连接到 Microsoft 全球网络的最佳方式是通过 ExpressRoute 线路
* SaaS 客户端集中在全球多个位置，并且每个位置都有自己的 ExpressRoute 线路，这些线路可与 Microsoft 全球网络建立最佳连接
* 法律要求你通过专用连接来路由云绑定流量
* 你需要将所有 SaaS 流量路由到一个固定的集中位置（专用数据中心或公共数据中心），并且将集中位置连接到 Microsoft 全球网络的最佳方式是通过 ExpressRoute 进行连接
* 对于某些静态 SaaS 客户端而言，仅 ExpressRoute 可提供最佳连接，但对于其他客户端而言，请使用 Internet

使用 ExpressRoute 期间，可以应用与 ExpressRoute 的 Microsoft 对等互连关联的路由筛选器，以便仅通过 ExpressRoute 线路路由部分 Microsoft 365 服务和/或 Azure PaaS 服务。 有关详细信息，请参阅[教程：配置用于 Microsoft 对等互连的路由筛选器][ExRRF]。

## <a name="next-steps"></a>后续步骤

* 如需了解 Microsoft Teams 如何调用流，以及如何优化不同情况下的网络连接（包括使用 Express Route 获得最佳结果期间），请参阅 [Microsoft Teams 调用流][Teams]。
* 如需你想通过测试了解各个办公地点的 Microsoft 365 连接问题，请参阅 [Microsoft 365 网络连接测试][Microsoft 365-Test]。
* 如需建立基线和性能历史记录，以便检测 Microsoft 365 性能的最新问题，请参阅[使用基线和性能历史记录优化 Office 365 性能][Microsoft 365perf]。

<!--Link References-->
[ExR-Intro]: ./expressroute-introduction.md
[CreatePeering]: ./expressroute-howto-routing-portal-resource-manager.md
[MGN]: https://azure.microsoft.com/blog/how-microsoft-builds-its-fast-and-reliable-global-network/
[AFD]: ../frontdoor/front-door-overview.md
[ExRRF]: ./how-to-routefilter-portal.md
[Teams]: /microsoftteams/microsoft-teams-online-call-flows
[Microsoft 365-Test]: https://connectivity.office.com/
[Microsoft 365perf]: /microsoft-365/enterprise/performance-tuning-using-baselines-and-history