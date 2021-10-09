---
title: Azure Functions 异地灾难恢复和高可用性
description: 如何使用地理区域实现冗余以及如何在 Azure Functions 中进行故障转移。
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 08/27/2021
ms.openlocfilehash: 27730e2b6c1ed760f43a054901d16b58ca80acd9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128645220"
---
# <a name="azure-functions-geo-disaster-recovery"></a>Azure Functions 异地灾难恢复

当整个 Azure 区域或数据中心遭遇停机时，任务关键型代码需要在不同的区域内继续进行处理。 本文介绍可用于部署函数以实现灾难恢复的一些策略。

## <a name="basic-concepts"></a>基本概念

Azure Functions 在特定区域的函数应用中运行。 没有可用的内置冗余。 为了避免中断期间执行丢失，可以通过冗余方式将相同的函数部署到多个区域的函数应用。  

在多个区域运行相同的函数代码时，可以考虑使用两种模式：

| 模式 | 说明 |
| --- | --- |
|主动/主动 | 两个区域中的函数都以重复或轮流的方式主动运行和处理事件。 建议将主动/主动模式与 [Azure Front Door](../frontdoor/front-door-overview.md) 结合使用，用于关键 HTTP 触发的函数。 |
|主动/被动 | 函数在接收事件的区域中主动运行，而第二个区域中的相同函数保持空闲状态。  需要进行故障转移时，系统会激活次要区域并开始接管处理操作。 建议对事件驱动的非 HTTP 触发函数（如服务总线和事件中心触发的函数）采用此模式。

若要详细了解多区域部署，请参阅[高可用性多区域 Web 应用程序](/azure/architecture/reference-architectures/app-service-web-app/multi-region)中的指南。

## <a name="redundancy-for-http-trigger-functions"></a>HTTP 触发器函数的冗余

主动/主动模式是 HTTP 触发器函数的最佳部署模型。 在这种情况下，需要使用 [Azure Front Door](../frontdoor/front-door-overview.md) 来协调处理两个区域之间的请求。 Azure Front Door 可以在多个区域运行的函数之间路由和轮循 HTTP 请求。它还会定期检查每个终结点的运行状况。 当一个区域中的函数停止响应运行状况检查时，Azure Front Door 会将该函数退出轮转，仅将流量转发到剩余的正常函数。  

![Azure Front Door 和函数的体系结构](media/functions-geo-dr/front-door.png)  

## <a name="redundancy-for-non-http-trigger-functions"></a>非 HTTP 触发器函数的冗余

为使用其他服务中事件的函数实现冗余需采用不同的模式，该模式适用于相关服务的故障转移模式。 

### <a name="activepassive-redundancy-for-non-http-trigger-functions"></a>非 HTTP 触发器函数的主动/被动冗余

使用“主动/被动”模式时，每次只有一个函数处理一条消息，但在发生灾难时可以故障转移到次要区域。 函数应用使用合作伙伴服务的故障转移行为，例如 [Azure 服务总线异地恢复](../service-bus-messaging/service-bus-geo-dr.md)和 [Azure 事件中心异地恢复](../event-hubs/event-hubs-geo-dr.md)。 辅助函数应用被视为被动，因为它连接到的故障转移服务当前未处于活动状态，因此函数应用实质上是空闲的 。

请考虑使用 Azure 事件中心触发器的示例拓扑。 在这种情况下，主动/被动模式需要涉及以下组件：

* Azure 事件中心同时部署到主要区域和次要区域。
* [启用异地灾难恢复](../service-bus-messaging/service-bus-geo-dr.md)以配合主要和次要事件中心。 这还会创建一个“别名”，用于连接到事件中心，并从主要区域切换到次要区域，而无需更改连接信息。
* 函数应用部署到主要和次要（故障转移）区域，次要区域的应用实质上处于空闲状态，因为消息不会发送到该区域。
* 函数应用在其各自事件中心的直接（非别名）连接字符串上触发。 
* 事件中心的发布服务器应发布到别名连接字符串。 

![主动-被动示例体系结构](media/functions-geo-dr/active-passive.png)

在故障转移之前，发送到共享别名的发布服务器将路由到主事件中心。 主要函数应用专门侦听主事件中心。 次要函数应用处于被动和空闲状态。 启动故障转移后，发送到共享别名的发布服务器会路由到次要事件中心。 次要函数应用现在将处于活动状态并开始自动触发。  可以完全从事件中心驱动到次要区域的有效故障转移，并且仅当各自的事件中心处于活动状态时，函数才变为活动状态。

详细了解如何在[服务总线](../service-bus-messaging/service-bus-geo-dr.md)和[事件中心](../event-hubs/event-hubs-geo-dr.md)之间进行故障转移以及需要注意的事项。

### <a name="activeactive-redundancy-for-non-http-trigger-functions"></a>非 HTTP 触发器函数的主动/主动冗余

对于非 HTTP 触发的函数，仍可以实现主动/主动部署。 但是，需要考虑两个活动区域如何相互交互或相互协调。 如果向两个区域部署了同一函数应用，并且每个区域都在同一服务总线队列上触发，那么在对该队列执行取消排队操作时，这两个区域属于竞争性使用者。 虽然这意味着每条消息仅由多个实例中的一个进行处理，但也意味着在单个服务总线实例上仍然存在单一故障点。 

可以改为部署两个服务总线队列，其中一个位于主要区域，另一个位于次要区域。 在这种情况下，可以拥有两个函数应用，每个函数应用都指向区域中活跃的服务总线队列。 此拓扑面临的挑战是如何在两个区域之间分发队列消息。  通常情况下，这意味着每个发布服务器都会尝试将消息发布到两个区域，并且每条消息都由两个活动的函数应用进行处理。 虽然这样会形成所需的“主动/主动”模式，但也会在复制计算以及何时/如何合并数据方面带来其他难题。 由于这些难题，我们建议对非 HTTPS 触发器函数使用主动/被动模式。

## <a name="next-steps"></a>后续步骤

* [创建 Azure Front Door](../frontdoor/quickstart-create-front-door.md)
* [事件中心故障转移注意事项](../event-hubs/event-hubs-geo-dr.md#considerations)
