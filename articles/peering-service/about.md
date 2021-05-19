---
title: Azure 对等互连服务概述
description: 了解 Azure 对等互连服务概述
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 198ba23920179e71e095e498ee2173d7f0111d42
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "95026723"
---
# <a name="azure-peering-service-overview"></a>Azure 对等互连服务概述

Azure 对等互连服务是一种网络服务，可增强客户与 Microsoft 云服务（例如 Microsoft 365、Dynamics 365、软件即服务 (SaaS) 服务、Azure 或可通过公共 Internet 访问的任何 Microsoft 服务）建立的连接。 Microsoft 已与世界各地的 Internet 服务提供商 (ISP)、Internet 交换合作伙伴 (IXP) 和软件定义的云互连 (SDCI) 提供商建立了合作关系，以提供可靠和高性能的公共连接，并提供从客户到 Microsoft 网络的最佳路由。

使用对等互连服务时，客户可以在给定区域中选择连接良好的合作伙伴服务提供商。 公用连接经过优化，可实现高可靠性和从云服务到最终用户位置的最低延迟。

![与 Microsoft 云的分布式连接](./media/peering-service-about/peering-service-what.png)

客户还可以通过在 Azure 门户中注册对等互连服务连接来选择对等互连服务遥测，例如针对 Microsoft 网络的用户延迟度量值、BGP 路由监视以及针对泄漏和劫持的警报。 

若要使用对等互连服务，客户无需在 Microsoft 进行注册。 唯一的要求是联系[对等互连服务合作伙伴](location-partners.md)以获取服务。 若要选择对等互连服务遥测，客户必须在 Azure 门户中进行注册。

有关如何注册对等互连服务的说明，请参阅[使用 Azure 门户注册对等互连服务](azure-portal.md)。 

> [!NOTE]
> 本文适用于负责与云和 Internet 建立企业连接的网络架构师。


## <a name="what-is-peering-service"></a>什么是对等互连服务？

对等互连服务即：

- 一项使用公共 Internet 的 IP 服务。 
- 一个包含服务提供商的协作平台，以及一项增值服务，旨在通过公共网络经由服务提供商合作伙伴向 Microsoft 云提供最佳且可靠的客户路由。

对等互连服务不是 Azure ExpressRoute 或 VPN 产品等专用连接产品。

> [!NOTE]
> 有关 ExpressRoute 的详细信息，请参阅 [ExpressRoute 文档](../expressroute/index.yml)。
>

## <a name="background"></a>背景

Microsoft 365、Dynamics 365 和任何其他 Microsoft SaaS 服务都托管在多个 Microsoft 数据中心中，并且可从任何地理位置进行访问。 Microsoft 全球网络在世界各地都有 Microsoft Edge 接入点 (PoP) 位置，在这些位置，它可以通过其服务提供商连接到最终用户。 

Microsoft 和合作伙伴服务提供商确保，在对等服务连接中注册的前缀的流量进入并退出 Microsoft 全球网络上最近的 Microsoft Edge PoP 位置。 Microsoft 确保，从在对等服务连接中注册的前缀流出的网络流量采用 Microsoft 全球网络上最近的 Microsoft Edge PoP 位置。

![Microsoft 网络和公共连接](./media/peering-service-about/peering-service-background-final.png)

> [!NOTE]
> 有关 Microsoft 全球网络的详细信息，请参阅 [Microsoft 全球网络](../networking/microsoft-global-network.md)。
>

## <a name="why-use-peering-service"></a>为什么使用对等互连服务？

寻求 Internet 优先接入到云或考虑 SD WAN 体系结构或高频使用 Microsoft SaaS 服务的企业需要可靠且高性能的 Internet 连接。 客户可以通过使用对等互连服务来实现这种转换。 Microsoft 和服务提供商已经建立了合作关系，为 Microsoft 云提供以性能为中心的可靠的公共连接。 下面列出了一些主要的客户功能：

- 基于 Internet 的最佳公共路由可连接到 Microsoft Azure 云服务，以实现最佳性能和可靠性。
- 能够选择首选服务提供商来连接到 Microsoft 云。
- 流量见解，例如延迟报告和前缀监视。
- Microsoft 云中的最佳网络跃点（AS 跃点）。
- 路由分析和统计信息：([BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol)) 路由异常事件（泄漏或劫持检测）和不理想路由。

### <a name="robust-reliable-peering"></a>强大、可靠的对等互连

对等互连服务使用两种类型的冗余：

- 区域冗余

   Microsoft 和服务提供商跨多个 Microsoft Edge PoP 位置互连，以提供对等互连服务。 在每个位置，互连必须支持跨两个路由器进行故障转移。

   每个对等互连位置都预配了冗余和多样的对等互连链接。

- 异地冗余

   Microsoft 已与多个都市位置的服务提供商互连，以便如果其中一个边缘节点的性能下降，流量将通过备用站点与 Microsoft 进行路由。 Microsoft 通过使用基于 SDN 的路由策略来路由其全球网络中的流量，以实现最佳性能。

    这种类型的冗余使用最短路由路径，方法是始终选择最靠近最终用户的 Microsoft Edge PoP，并确保客户是一个远离 Microsoft 的网络跃点（AS 跃点）。

   ![异地冗余](./media/peering-service-about/peering-service-geo-shortest.png)

### <a name="optimal-routing"></a>最佳路由

以下路由方法是首选方法：

-  冷土豆路由

   软件定义的冷土豆路由方法可控制源自 Microsoft 云的网络流量。 它可确保流量始终停留在高容量、低延迟和高可靠性的 Microsoft 全球网络上，直到尽可能地靠近目的地为止。
   
   不使用冷土豆方法的路由称为热土豆路由。 通过热土豆路由，源自 Microsoft 云的流量将经过 Internet。

   ![冷土豆路由](./media/peering-service-about/peering-service-cold-potato.png)

### <a name="monitoring-platform"></a>监视平台

   提供服务监视来分析客户流量和路由，并提供以下功能： 

-  Internet BGP 路由异常检测
          
   此服务用于检测路由异常事件（如路由劫持到客户前缀）并发送警报。

-  客户延迟

   此服务监视客户位置与 Microsoft 之间的路由性能。 
   
   路由性能是通过验证从客户端到 Microsoft Edge PoP 花费的往返时间来衡量的。 客户可以查看不同地理位置的延迟报告。

   监视将捕获事件，以防服务性能下降。

   ![对等互连服务的监视平台](media/peering-service-about/peering-service-latency-report.png)

### <a name="traffic-protection"></a>流量保护

仅通过在对等互连服务中注册客户时定义的首选路径进行路由。

Microsoft 保证即使检测到恶意活动，也通过首选路径路由流量。

在 Azure 门户中报告 BGP 路由异常（如有）。

## <a name="next-steps"></a>后续步骤

- 若要了解对等互连服务连接，请参阅[对等互连服务连接](connection.md)。
- 若要了解对等互连服务连接遥测数据，请参阅[对等互连服务连接遥测数据](connection-telemetry.md)。
- 若要查找服务提供商合作伙伴，请参阅[对等互连服务合作伙伴和位置](location-partners.md)。
- 若要加入对等互连服务连接，请参阅[加入对等互连服务模型](onboarding-model.md)。
- 若要使用 Azure 门户注册连接，请参阅[使用 Azure 门户注册对等互连服务连接](azure-portal.md)。
- 若要度量遥测，请参阅[度量连接遥测](measure-connection-telemetry.md)。