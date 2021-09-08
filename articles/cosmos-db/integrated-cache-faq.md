---
title: Azure Cosmos DB 集成缓存常见问题解答
description: 有关 Azure Cosmos DB 集成缓存的常见问题解答。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/26/2021
ms.author: tisande
ms.openlocfilehash: 7fee5b4133cc048576ff0b794d441fac3ee162e6
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123035938"
---
# <a name="azure-cosmos-db-integrated-cache-frequently-asked-questions"></a>Azure Cosmos DB 集成缓存常见问题解答
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB 集成缓存是内置于 Azure Cosmos DB 的内存中缓存。 本文解答有关 Azure Cosmos DB 集成缓存的常见问题。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="why-does-the-integrated-cache-require-a-dedicated-gateway"></a>为什么集成缓存需要专用网关？

如果已使用网关模式连接到 Azure Cosmos DB，则已使用标准网关。 尽管 Azure Cosmos DB 后端（预配的吞吐量和存储）为每个容器提供专用容量，但标准网关在许多客户之间共享。 对于许多客户而言，共享标准网关是切实可行的，因为这样每个客户消耗的计算资源最少。 由于集成缓存特定于 Azure Cosmos DB 帐户，且需要大量的 CPU 和内存，因此它需要专用网关节点。

### <a name="what-is-a-dedicated-gateway"></a>什么是专用网关？

[专用网关](dedicated-gateway.md)是服务器端计算单元，充当 Azure Cosmos DB 帐户中数据的前端。 当你连接到专用网关终结点时，应用程序会将请求发送到专用网关，然后该网关会将请求路由到不同的后端分区。

### <a name="does-using-the-dedicated-gateway-offer-any-other-performance-benefits-over-using-the-standard-gateway"></a>与使用标准网关相比，使用专用网关是否还具有其他性能优势？

通常，与标准网关路由的请求相比，专用网关路由的请求的延迟更低且更一致。 即使是不使用集成缓存的请求，其延迟也会比标准网关略低。

### <a name="what-kind-of-latency-should-i-expect-from-the-integrated-cache"></a>集成缓存的延迟是哪种延迟？

集成缓存提供的请求速度更快，因为缓存数据存储在专用网关的内存中，而不是存储在后端。 对于缓存点读取，延迟可能为 2-4 毫秒。

对于缓存查询，延迟取决于查询。 查询缓存的工作原理是缓存查询引擎对特定查询的响应。 然后将此响应发送回客户端的 SDK 进行处理。 对于简单查询，SDK 中所需的处理是最少的，并且延迟通常为 2-4 毫秒。 而使用 `GROUP BY` 或 `DISTINCT` 进行的更复杂的查询在 SDK 中的处理也更繁琐，因此延迟也可能更高，甚至会产生查询缓存。

如果之前使用直接模式连接到 Azure Cosmos DB，并切换为使用专用网关进行连接，某些请求的延迟可能会略微增加。 使用网关模式需要将请求发送到网关（在这种情况下为专用网关），然后以适当方式路由到后端。 顾名思义，直接模式使客户端可以直接与后端进行通信，从而消除了多余的跃点。 

如果应用先前使用直接模式，则集成缓存的延迟优势仅在以下方案中较为显著：

- 大型项 (> 16 KB) 的点读取延迟
- 高 RU 或复杂查询

如果应用之前将网关模式与标准网关一起使用，则集成缓存可在几乎所有方案中减少延迟。 

### <a name="does-the-azure-cosmos-db-availability-sla-extend-to-the-dedicated-gateway-and-integrated-cache"></a>Azure Cosmos DB 可用性 SLA 是否扩展到专用网关和集成缓存？

该功能正式发布后，会在专用网关（即也在集成缓存）上提供可用性 SLA/SLO。 对于需要高可用性的方案，应预配所需专用网关实例数的 3 倍。 例如，如果生产中需要一个专用网关节点，应再预配两个专用网关节点，以应对可能发生的停机或中断。

### <a name="the-integrated-cache-is-only-available-for-sql-core-api-right-now-are-you-planning-on-releasing-it-for-other-apis-as-well"></a>集成缓存目前仅适用于 SQL (Core) API。 是否还计划将其发布给其他 API？

长期路线图上规划了将集成缓存扩展到 SQL API 之外，但会超出集成缓存的初始公共预览版的范围。

## <a name="next-steps"></a>后续步骤

- [集成缓存](integrated-cache.md)
- [配置集成缓存](how-to-configure-integrated-cache.md)
- [专用网关](dedicated-gateway.md)
- 正在尝试为迁移到 Azure Cosmos DB 进行容量计划？ 可以使用有关现有数据库群集的信息进行容量计划。
    - 若只知道现有数据库群集中的 vCore 和服务器数量，请阅读[使用 vCore 或 vCPU 估算请求单位](convert-vcore-to-request-unit.md) 
    - 若知道当前数据库工作负载的典型请求速率，请阅读[使用 Azure Cosmos DB 容量计划工具估算请求单位](estimate-ru-with-capacity-planner.md)