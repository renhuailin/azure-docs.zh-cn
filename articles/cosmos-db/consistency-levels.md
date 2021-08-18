---
title: Azure Cosmos DB 中的一致性级别
description: Azure Cosmos DB 提供五种一致性级别来帮助在最终一致性、可用性和延迟之间做出取舍。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: 4d0197e76659e864ab0f5553317b64b2d74b867d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121725470"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Azure Cosmos DB 中的一致性级别
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

依赖于复制实现高可用性和/或低延迟的分布式数据库必须在 [PACLC 定理](https://en.wikipedia.org/wiki/PACELC_theorem)定义的读取一致性、可用性、延迟和吞吐量之间进行基本权衡。 非常一致性模型的可线性化是数据可编程性的黄金标准。 但是，由于数据必须跨远距离进行复制和提交，因此写入延迟较高，从而增加了高昂的成本。 由于数据无法在每个区域中复制和提交，因此非常一致性也可能因可用性降低（在失败期间）而受到影响。 最终一致性提供了更高的可用性和更好的性能，但更难对应用程序编程，因为数据可能不会在所有区域完全一致。

目前市场上大多数商业用途的分布式 NoSQL 数据库只能提供非常一致性和最终一致性。 Azure Cosmos DB 提供五个妥善定义的级别。 按最强到最弱的顺序，级别分别为：

- *非常*
- *有限过期*
- *会话*
- *一致前缀*
- *最终*

每个级别在可用性与性能方面各有利弊。 下图以范围区间形式显示了不同的一致性级别。

:::image type="content" source="./media/consistency-levels/five-consistency-levels.png" alt-text="范围形式的一致性" border="false" :::

一致性级别与区域无关，无论在哪个区域为读取和写入操作提供服务、与 Azure Cosmos 帐户关联的区域数量是多少，或者帐户是配置了单个还是多个写入区域，都可以保证所有操作获得这种一致性。

## <a name="consistency-levels-and-azure-cosmos-db-apis"></a>一致性级别和 Azure Cosmos DB API

Azure Cosmos DB 为常用数据库提供对与线路协议兼容的 API 的本机支持。 这些数据库包括 MongoDB、Apache Cassandra、Gremlin 和 Azure 表存储。 使用 Gremlin API 和表 API 时，会使用 Azure Cosmos 帐户上配置的默认一致性级别。 有关 Cassandra API 或适用于 MongoDB 的 API 与 Azure Cosmos DB 的一致性级别之间的一致性级别映射的详细信息，请参阅 [Cassandra API 一致性映射](cassandra/apache-cassandra-consistency-mapping.md)和[适用于 MongoDB 的 API 一致性映射](mongodb/consistency-mapping.md)。

## <a name="scope-of-the-read-consistency"></a>读取一致性的范围

读取一致性适用于逻辑分区范围内的单个读取操作。 读取操作可能由远程客户端或存储过程发出。

## <a name="configure-the-default-consistency-level"></a>配置默认一致性级别

随时都可在 Azure Cosmos DB 帐户中配置默认的一致性级别。 在帐户中配置的默认一致性级别适用于该帐户下的所有 Azure Cosmos 数据库和容器。 针对某个容器或数据库发出的所有读取和查询默认使用指定的一致性级别。 有关详细信息，请参阅如何[配置默认一致性级别](how-to-manage-consistency.md#configure-the-default-consistency-level)。 还可以覆盖特定请求的默认一致性级别，若要了解详细信息，请参阅如何[覆盖默认一致性级别](how-to-manage-consistency.md?#override-the-default-consistency-level)一文。

> [!TIP]
> 替代默认一致性级别的操作仅适用于 SDK 客户端中的读取。 默认情况下，配置为强一致性的帐户仍会将数据同步写入和复制到帐户中的所有区域。 SDK 客户端实例或请求使用会话或较弱一致性替代此级别时，将使用单个副本执行读取。 有关更多详细信息，请参阅[一致性级别和吞吐量](consistency-levels.md#consistency-levels-and-throughput)。

> [!IMPORTANT]
> 更改默认的一致性级别后，需要重新创建任何 SDK 实例。 这可以通过重启应用程序来完成。 这可确保 SDK 使用新的默认一致性级别。

## <a name="guarantees-associated-with-consistency-levels"></a>与一致性级别关联的保证

Azure Cosmos DB 可保证 100% 的读取请求满足所选一致性级别的一致性保证。 [azure-cosmos-tla](https://github.com/Azure/azure-cosmos-tla) GitHub 存储库中提供了 Azure Cosmos DB 中使用 TLA+ 规范语言精确定义的五个一致性级别。

以下部分描述了五个一致性级别的语义。

### <a name="strong-consistency"></a>非常一致性

非常一致性提供可线性化保证。 可线性化是指并发处理请求。 保证读取操作返回项的最新提交版本。 客户端永远不会看到未提交或不完整的写入。 始终保证用户读取最新确认的写入。

  下图以乐谱形式演示了非常一致性。 将数据写入“美国西部 2”区域后，当你从其他区域读取这些数据时，将会获得最新的值：

  :::image type="content" source="media/consistency-levels/strong-consistency.gif" alt-text="非常一致性级别的插图":::

### <a name="bounded-staleness-consistency"></a>有限过期一致性

在有限过期一致性中，保证读取操作遵循一致性前缀保证。  读取操作可以滞后于写入操作最多“K”个项版本（即“更新”）或“T”时间间隔，以先达到者为准。 换言之，如果选择有限过期，则可以通过两种方式配置“过期”：

- 项的版本数 (*K*)
- 时间间隔 (T) 读取操作可以滞后于写入操作

对于单区域帐户，K 和 T 的最小值为 10 个写入操作或 5 秒 。 对于多区域帐户，K 和 T 的最小值为 100,000 个写入操作或 300 秒 。

有限过期在“过期窗口”之外提供全局整体顺序。 当客户端在接受写入的区域中执行读取操作时，有限过期一致性提供的保证与非常一致性的保证相同。 随着过期窗口接近时间或更新（以更近者为准），服务将限制新写入，以允许复制跟上并遵守一致性保证。

在过期窗口内，有限过期提供以下一致性保证：

- 对于具有单个写入区域的帐户，同一区域中的客户端的一致性为“非常”
- 对于具有单个写入区域的帐户，不同区域中的客户端的一致性为“一致前缀”
- 对于具有多个写入区域的帐户，写入单个区域的客户端的一致性为“一致前缀”
- 对于具有多个写入区域的帐户，写入不同区域的客户端的一致性为“最终”

  预期写入延迟较低，但需要保证全局整体顺序的全球分步式应用程序经常选择“有限过期”。 有限过期非常适合提供小组协作和共享、股票行情、发布-订阅/排队等功能的应用程序。下图以乐谱形式演示了有限过期一致性。 将数据写入“美国西部 2”区域后，“美国东部 2”和“澳大利亚东部”区域将会根据所配置的最大滞后时间或最大操作数目读取写入的值：

  :::image type="content" source="media/consistency-levels/bounded-staleness-consistency.gif" alt-text="有限过期一致性级别的插图":::

### <a name="session-consistency"></a>会话一致性

对于会话一致性，在单个客户端会话中，将保证读取操作遵循一致前缀、单调读取、单调写入、读取写入和读取后写入保证。 这采用单个“写入器”会话，或者多个写入器共享会话令牌。

在会话外部执行写入的客户端将获得以下保证：

- 对于具有单个写入区域的帐户，同一区域中的客户端的一致性为“一致前缀”
- 对于具有单个写入区域的帐户，不同区域中的客户端的一致性为“一致前缀”
- 对于具有多个写入区域的帐户，写入单个区域的客户端的一致性为“一致前缀”
- 对于具有多个写入区域的帐户，写入多个区域的客户端的一致性为“最终”

  “会话一致性”是最广泛用于单个区域以及全球分步式应用程序的一致性级别。 它不仅提供与最终一致性相当的写入延迟、可用性和读取吞吐量，还提供一致性保证，从而满足了编写为在用户上下文中运行的应用程序的需求。 下图以乐谱形式演示了会话一致性。 “美国西部 2 写入器”和“美国西部 2 读取器”正在使用同一个会话（会话 A），因此它们会同时读取相同的数据。 而“澳大利亚东部”区域正在使用“会话 B”，因此，它会稍后才会接收到数据，但接收顺序与写入顺序相同。

  :::image type="content" source="media/consistency-levels/session-consistency.gif" alt-text="会话一致性级别的插图":::

### <a name="consistent-prefix-consistency"></a>一致前缀一致性

在一致前缀选项中，返回的更新包含所有更新的一些前缀，不带间隔。 一致前缀一致性级别保证读取操作永远不会看到无序写入。

如果写入是按 `A, B, C` 顺序执行的，则客户端会看到 `A`、`A,B` 或 `A,B,C`，但永远不会看到类似于 `A,C` 或 `B,A,C` 的失序排列情况。 一致前缀的延迟、可用性和读取吞吐量与最终一致性相当，但还会提供顺序保证，以适应顺序非常重要的方案的需求。

下面是一致前缀的一致性保证：

- 对于具有单个写入区域的帐户，同一区域中的客户端的一致性为“一致前缀”
- 对于具有单个写入区域的帐户，不同区域中的客户端的一致性为“一致前缀”
- 对于具有多个写入区域的帐户，写入单个区域的客户端的一致性为“一致前缀”
- 对于具有多个写入区域的帐户，写入多个区域的客户端的一致性为“最终”

下图以乐谱形式演示了一致前缀一致性。 在所有区域中，读取操作永远不会看到无序写入：

  :::image type="content" source="media/consistency-levels/consistent-prefix.gif" alt-text="一致前缀的插图":::

### <a name="eventual-consistency"></a>最终一致性

对于最终一致性，不保证读取的顺序。 如果缺少任何进一步的写入，则副本最终会收敛。  
最终一致性是最弱的一致性形式，因为客户端可能会读取比之前读取的值还要旧的值。 最终一致性非常适合不需要任何顺序保证的应用程序。 示例包括推文、点赞或无回复评论的计数。 下图以乐谱形式演示了最终一致性。

  :::image type="content" source="media/consistency-levels/eventual-consistency.gif" alt-text="最终一致性的插图":::

## <a name="consistency-guarantees-in-practice"></a>一致性保证的实践

在实践中，你可能经常会获得更强的一致性保证。 读取操作的一致性保证对应于所请求的数据库状态的新旧程度和顺序。 读取一致性与写入/更新操作的排序和传播有关。  

如果未在数据库上执行任何写入操作，具有“最终”、“会话”或“一致前缀”一致性级别的读取操作可能产生与具有非常一致性级别的读取操作相同的结果  。

如果使用非常一致性以外的一致性级别配置了 Azure Cosmos 帐户，则可以通过查看概率有限过期 (PBS) 指标，找到客户端获得工作负荷的非常一致读取的概率。 此指标在 Azure 门户中公开，若要了解详细信息，请参阅[监视概率有限过期性 (PBS) 指标](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)。

概率有限过期表明了最终一致的最终程度。 通过此指标可深入了解在 Azure Cosmos 帐户中目前配置的一致性级别之间获得更非常一致性的频率。 换句话说，可看到获得写入和读取区域组合的非常一致读取的概率（以毫秒计量）。

## <a name="consistency-levels-and-latency"></a>一致性级别和延迟

所有一致性级别的读取延迟始终保证在第 99 百分位小于 10 毫秒。 平均（在第 50 百分位）读取延迟通常不超过 4 毫秒。

所有一致性级别的写入延迟始终保证在第 99 百分位小于 10 毫秒。 平均（在第 50 百分位）写入延迟通常不超过 5 毫秒。 跨多个区域且使用非常一致性配置的 Azure Cosmos 帐户不在此保障内。

### <a name="write-latency-and-strong-consistency"></a>写入延迟和强一致性

对于配置了强一致性（与多个区域一致）的 Azure Cosmos 帐户，99% 的情况下写入延迟等于任意两个最远区域之间的往返时间 (RTT) 的两倍加上 10 毫秒。 区域之间的高网络 RTT 将转变为 Cosmos DB 请求的更高延迟，因为只有在确保已将操作提交到帐户中的所有区域之后，强一致性才会完成该操作。

确切的 RTT 延迟取决于光速距离和 Azure 网络拓扑。 Azure 网络不为任何两个 Azure 区域之间的 RTT 提供任何延迟 SLA，但它会发布 [Azure 网络往返延迟统计信息](../networking/azure-network-latency.md)。 对于你的 Azure Cosmos 帐户，将在 Azure 门户中显示复制延迟。 可以使用 Azure 门户（转到“指标”边栏选项卡，选择“一致性”选项卡）监视与 Azure Cosmos 帐户关联的各个区域之间的复制延迟。

> [!IMPORTANT]
> 由于写入延迟大，默认情况下会阻止区域跨越 5000 英里（8000 公里）以上的帐户的强一致性。 若要启用此功能，请联系支持人员。

## <a name="consistency-levels-and-throughput"></a>一致性级别和吞吐量

- 对于强一致性和有限过期一致性，将针对一个四副本集中的两个副本（少数仲裁）进行读取，以提供一致性保证。 会话一致性、一致前缀一致性和最终一致性执行单副本读取。 结果是，在请求单位数量相同的情况下，强一致性和有限过期一致性的读取吞吐量是其他一致性级别的一半。

- 对于给定类型的写入操作（例如插入、替换、更新插入和删除），所有一致性级别为请求单元提供的写入吞吐量是相同的。

|**一致性级别**|**仲裁读取**|**仲裁写入**|
|--|--|--|
|**非常**|本地少数|全局多数|
|**有限过期**|本地少数|本地多数|
|**会话**|单个副本（使用会话令牌）|本地多数|
|**一致前缀**|单个副本|本地多数|
|**最终**|单个副本|本地多数|

> [!NOTE]
> 就读取成本 (RU/s) 而言，本地少数读取是较弱一致性级别的两倍，因为读取从两个副本进行，以便为非常和有限过期提供一致性保证。

## <a name="consistency-levels-and-data-durability"></a><a id="rto"></a>一致性级别和数据持续性

在全球分布式数据库环境中，当发生区域范围的服务中断时，一致性级别与数据持续性之间存在直接关系。 制定业务连续性计划时，需了解应用程序在中断事件发生后完全恢复之前的最大可接受时间。 应用程序完全恢复所需的时间称为 **恢复时间目标** (**RTO**)。 此外，还需要了解从中断事件恢复时，应用程序可忍受最近数据更新丢失的最长期限。 可以承受更新丢失的时限称为 **恢复点目标** (**RPO**)。

下表定义了当发生区域范围的服务中断时，一致性模型与数据持续性之间的关系。 请务必注意，在分布式系统中，由于 [CAP 定理](https://en.wikipedia.org/wiki/CAP_theorem)的存在，即使一致性较高，也不可能存在 RPO 和 RTO 为零的分布式数据库。

|**区域**|**复制模式**|**一致性级别**|**RPO**|**RTO**|
|---------|---------|---------|---------|---------|
|1|单个或多个写入区域|任何一致性级别|< 240 分钟|<1 周|
|>1|单个写入区域|会话、一致的前缀或最终|< 15 分钟|< 15 分钟|
|>1|单个写入区域|有限过期|*K* & *T*|< 15 分钟|
|>1|单个写入区域|强|0|< 15 分钟|
|>1|多个写入区域|会话、一致的前缀或最终|< 15 分钟|0|
|>1|多个写入区域|有限过期|*K* & *T*|0|

*K* = 某个项的“K”版本（即更新）的数目。 

*T* = 自上次更新以来的时间间隔“T”。 

对于单区域帐户，K 和 T 的最小值为 10 个写入操作或 5 秒 。 对于多区域帐户，K 和 T 的最小值为 100,000 个写入操作或 300 秒 。 这定义了使用有限过期时数据的最小 RPO。

## <a name="strong-consistency-and-multiple-write-regions"></a>非常一致性和多个写入区域

具有多个写入区域的 Cosmos 帐户不能配置为实施非常一致性，因为分布式系统不可能在提供为零的 RPO 的同时提供为零的 RTO。 另外，将非常一致性与多个写入区域配合使用时，没有写入延迟优势，因为对任何区域的任何写入必须在复制后提交到帐户中的所有已配置区域。 这导致写入延迟与单写入区域帐户相同。

## <a name="additional-reading"></a>其他阅读材料

若要详细了解一致性的概念，请阅读以下文章：

- [Azure Cosmos DB 提供的五个一致性级别的高级 TLA+ 规范](https://github.com/Azure/azure-cosmos-tla)
- [Doug Terry 借助棒球阐释复制数据一致性（视频）](https://www.youtube.com/watch?v=gluIh8zd26I)
- [Doug Terry 借助棒球阐释复制数据一致性（白皮书）](https://www.microsoft.com/research/publication/replicated-data-consistency-explained-through-baseball/)
- [弱一致性重复数据的会话保证](https://dl.acm.org/citation.cfm?id=383631)
- [现代分布式数据库系统设计中的一致性利弊：CAP 只是冰山一角](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Probabilistic Bounded Staleness (PBS) for Practical Partial Quorums](https://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)（实用部分仲裁的概率有限过期性 (PBS)）
- [最终一致性 - 再探](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>后续步骤

要详细了解 Azure Cosmos DB 中的一致性级别，请阅读以下文章：

- [配置默认一致性级别](how-to-manage-consistency.md#configure-the-default-consistency-level)
- [替代默认一致性级别](how-to-manage-consistency.md#override-the-default-consistency-level)
- [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/)
