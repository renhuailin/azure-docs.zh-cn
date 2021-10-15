---
title: 可用性和连续性
titleSuffix: Azure Cognitive Search
description: 了解如何使搜索服务具有高可用性和可复原性，以抵御周期中断甚至灾难性故障。
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/04/2021
ms.custom: references_regions
ms.openlocfilehash: c3cb2a64cd2981c73a673776c01ec98bf44c8041
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129458181"
---
# <a name="availability-and-business-continuity-in-azure-cognitive-search"></a>Azure 认知搜索中的可用性和业务连续性

在认知搜索中，可通过多个副本实现可用性，而业务连续性（和灾难恢复）通过多种搜索服务来实现。 本文提供了相关指导，你可以将其用作开发策略的着手点，以通过该策略满足可用性和连续操作的业务要求。

<a name="scale-for-availability"></a>

## <a name="high-availability"></a>高可用性

在认知搜索中，副本是索引的副本。 使用多个副本可让 Azure 认知搜索针对一个副本执行计算机重启和维护，同时可继续针对其他副本执行查询。 有关添加副本的详细信息，请参阅[添加或减少副本和分区](search-capacity-planning.md#adjust-capacity)。

对于每个单独的搜索服务，Microsoft 保证至少 99.9% 的可用性适用于满足以下条件的配置： 

+ 对于只读工作负荷（查询），需要有两个副本才能实现高可用性

+ 针对读写工作负荷（查询和索引），需要有三个或更多副本才可实现高可用性 

没有为免费层提供 SLA。 有关详细信息，请参阅[适用于 Azure 认知搜索的 SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)。

<a name="availability-zones"></a>

## <a name="availability-zones"></a>可用性区域

[可用性区域](../availability-zones/az-overview.md)是一种 Azure 平台功能，将区域的数据中心分成不同的物理位置组，以在同一区域内提供高可用性。 如果将可用性区域用于认知搜索，单个副本是区域分配的单位。 搜索服务在一个地区内运行；其副本在不同的区域中运行。

可以通过将两个或多个副本添加到搜索服务来利用 Azure 认知搜索的可用性区域。 每个副本都将放置在该区域内不同的可用性区域中。 如果副本数比可用性区域数多，那么这些副本将尽可能地跨可用性区域平均分布。 除了在提供可用性区域的区域中[创建搜索服务](search-create-service-portal.md)，然后将该服务配置为使用[多个副本](search-capacity-planning.md#adjust-capacity)之外，没有具体要你执行的操作。

Azure 认知搜索当前支持在以下一个区域中创建的标准层或更高层搜索服务的可用性区域：

+ 澳大利亚东部（2021 年 1 月 30 日或之后创建）
+ 巴西南部（2021 年 5 月 2 日或之后创建）
+ 加拿大中部（2021 年 1 月 30 日或之后创建）
+ 美国中部（2020 年 12 月 4 日或之后创建）
+ 美国东部（2021 年 1 月 27 日或之后创建）
+ 美国东部 2（2021 年 1 月 30 日或之后创建）
+ 法国中部（2020 年 10 月 23 日或之后创建）
+ 德国中西部（2021 年 5 月 3 日或之后创建）
+ 日本东部（2021 年 1 月 30 日或之后创建）
+ 北欧（2021 年 1 月 28 日或之后创建）
+ 美国中南部（2021 年 4 月 30 日或之后创建）
+ 东南亚（2021 年 1 月 31 日或之后创建）
+ 英国南部（2021 年 1 月 30 日或之后创建）
+ US Gov 弗吉尼亚州（2021 年 4 月 30 日或之后创建）
+ 西欧（2021 年 1 月 29 日或之后创建）
+ 美国西部 2（2021 年 1 月 30 日或之后创建）

可用性区域不会影响 [Azure 认知搜索服务级别协议](https://azure.microsoft.com/support/legal/sla/search/v1_0/)。 你仍需要 3 个或更多个副本来实现查询高可用性。

## <a name="multiple-services-in-separate-geographic-regions"></a>不同地理区域中的多个服务

尽管大多数客户只使用一个服务，但若有以下操作要求，则可能需要提供服务冗余：

+ [业务连续性和灾难恢复 (BCDR)](../best-practices-availability-paired-regions.md)（认知搜索不能在发生故障时提供即时故障转移）。
+ 全局部署的应用程序。 如果查询和索引请求来自世界各地，则离主机数据中心最近的用户将获得更快的性能。 在靠近这些用户的地区创建其他服务可以使所有用户的性能一致。
+ [多租户体系结构](search-modeling-multitenant-saas-applications.md)有时会调用两个或更多服务。

如果你还需要两项搜索服务，那么在不同的区域创建它们可以满足应用程序对连续性和恢复的要求，并为全球用户群提供更快的响应时间。

Azure 认知搜索当前不提供自动化方法来跨区域异地复制搜索索引，但有一些技巧，可以用来使此过程很容易实现和管理。 我们会在下面几节介绍这些技巧。

地理分散的搜索服务集的目标是，让两个或更多索引在两个或更多区域中可用，在这些区域中用户会被路由到 Azure 认知搜索服务，以提供最低延迟：

   ![按区域的服务的交叉表][1]

可以通过创建多项服务并设计数据同步策略来实现此体系结构。 可以选择包含资源（如 Azure 流量管理器）来路由请求。 有关详细信息，请参阅[创建搜索服务](search-create-service-portal.md)。

<a name="data-sync"></a>

### <a name="keep-data-synchronized-across-multiple-services"></a>在多个服务之间保持数据同步

有两个选项可让两个或以上分布式搜索服务保持同步，包括使用 [Azure 认知搜索索引器](search-indexer-overview.md)或推送 API（也称为 [Azure 认知搜索 REST API](/rest/api/searchservice/)）。 

#### <a name="option-1-use-indexers-for-updating-content-on-multiple-services"></a>选项 1：使用索引器更新多个服务中的内容

如果已在一个服务中使用索引器，可以在另一个服务中配置另一个索引器，以使用相同的数据源对象，并从相同的位置提取数据。 每个区域中的每个服务具有自身的索引器和目标索引（搜索索引不会共享，这意味着数据是重复的），但每个索引器引用相同的数据源。

下面是该体系结构的概要视图。

   ![具有分布式索引器和服务组合的单一数据源][2]

#### <a name="option-2-use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>选项 2：使用 REST API 在多个服务中推送内容更新

如果使用 Azure 认知搜索 REST API [推送搜索索引中的内容](tutorial-optimize-indexing-push-api.md)，则可以在需要更新时，可以通过将更改推送到所有搜索服务，以保持各种搜索服务同步。 在代码中，请确保处理好这种情况：对一个搜索服务的更新失败，但对于其他搜索服务成功。

### <a name="use-azure-traffic-manager-to-coordinate-requests"></a>使用 Azure 流量管理器协调请求

通过使用 [Azure 流量管理器](../traffic-manager/traffic-manager-overview.md)，可以将请求路由到多个地理定位的网站，而这些网站由多个搜索服务支持。 流量管理器的一个优点是，它可以探测 Azure 认知搜索以确保其可用，并在发生停机时会用户路由到备用搜索服务。 此外，如果通过 Azure 网站路由搜索请求，Azure 流量管理器允许在网站启动但没有 Azure 认知搜索的情形下进行负载均衡。 下面是利用流量管理器的体系结构的示例。

   ![按区域服务的交叉表（带有中央流量管理器）][3]

## <a name="disaster-recovery-and-service-outages"></a>灾难恢复和服务中断

如[服务级别协议 (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/) 中所述，当 Azure 认知搜索服务实例配置有两个或更多副本时，我们保证索引查询请求的高可用性，当 Azure 认知搜索服务实例配置有三个或更多副本时，我们保证索引更新请求的高可用性。 但是，没有任何内置的机制可实现灾难恢复。 如果在超出 Microsoft 控制的灾难性故障中需要连续性服务，建议在其他区域预配另一个服务并实施异地复制策略，确保索引跨所有服务完全冗余。

使用[索引器](search-indexer-overview.md)来填充和刷新索引的客户可利用相同的数据源，通过特定于地区的索引器来处理灾难恢复。 不同区域的两个服务（每个都运行索引器）可对相同数据源进行索引，实现异地冗余。 如果从同样异地冗余的数据源进行索引，请注意 Azure 认知搜索索引器只能从主要副本执行增量索引（从新的、已修改的或已删除的文档合并更新）。 在故障转移事件中，请确保将索引器重新指向到新的主要副本。 

如果不使用索引器，也可使用应用程序代码将对象和数据并行推送到其他搜索服务。 有关详细信息，请参阅[在多个服务之间保持数据同步](#data-sync)。

## <a name="back-up-and-restore-alternatives"></a>备份和还原替代项

由于 Azure 认知搜索不是主数据存储解决方案，因此，Microsoft 不提供正式的自助备份和还原机制。 但是，你可以使用此 [Azure 认知搜索 .NET 示例存储库](https://github.com/Azure-Samples/azure-search-dotnet-samples)中的 **index-backup-restore** 示例代码将索引定义和快照备份到一系列 JSON 文件，然后根据需要使用这些文件来还原索引。 还可以使用此工具在服务层级之间移动索引。

在其他情况下，如果误删索引，用于创建和填充索引的应用程序代码是事实上的还原选项。 要重新生成索引，请删除它（假设其存在），在服务中重新创建该索引，并通过从主数据存储中检索数据来重新加载该索引。

## <a name="next-steps"></a>后续步骤

若要详细了解定价层和每个层的服务限制，请参阅[服务限制](search-limits-quotas-capacity.md)。 查看[容量规划](search-capacity-planning.md)详细了解关于分区和副本组合的信息，或查看[案例研究：使用认知搜索来支持复杂的 AI 场景](https://techcommunity.microsoft.com/t5/azure-ai/case-study-effectively-using-cognitive-search-to-support-complex/ba-p/2804078)了解实际的提示。

有关本文所述技术的性能和演示的讨论，请观看以下视频：

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
>

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png