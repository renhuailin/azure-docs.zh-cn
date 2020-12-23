---
title: Apache Cassandra 和 Azure Cosmos DB 一致性级别
description: Apache Cassandra 和 Azure Cosmos DB 一致性级别。
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: 3107610215d5b37c43124ce4129b2eb5437e3b62
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516852"
---
# <a name="apache-cassandra-and-azure-cosmos-db-cassandra-api-consistency-levels"></a>Apache Cassandra 和 Azure Cosmos DB Cassandra API 一致性级别
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

与 Azure Cosmos DB 不同，Apache Cassandra 本身并不提供精确定义的一致性保证。 与之相反，Apache Cassandra 提供一个写入一致性级别和一个读取一致性级别，以便进行高可用性、一致性和延迟方面的权衡。 使用 Azure Cosmos DB 的 Cassandra API 时：

* Apache Cassandra 的写入一致性级别映射到在 Azure Cosmos 帐户上配置的默认一致性级别。 写入操作 (CL) 的一致性不能因请求的不同而（即按请求）更改。

* Azure Cosmos DB 会将 Cassandra 客户端驱动程序指定的读取一致性级别动态映射到根据读取请求动态配置的某个 Azure Cosmos DB 一致性级别。

## <a name="multi-region-writes-vs-single-region-writes"></a>多区域写入与单区域写入

Apache Cassandra 数据库在默认情况下是一个多主机系统，并不为包含多区域复制功能的单区域写入提供现成的选项。 但 Azure Cosmos DB 提供了一种允许单区域或 [多区域](how-to-multi-master.md) 写入配置的全包式功能。 能够选择跨多个区域的单个区域写入配置的优点之一是避免了跨区域冲突方案，以及维护跨多个区域的强一致性的选项。 

利用单区域写入，你可以保持高度一致性，同时仍可以在具有 [自动故障转移](high-availability.md#multi-region-accounts-with-a-single-write-region-write-region-outage)功能的区域之间保持高可用性级别。 在此配置中，你仍然可以利用数据位置来减少读取延迟，方法是根据每个请求降级到最终一致性。 除了这些功能以外，Azure Cosmos DB 平台还提供了在选择区域时实现 [区域冗余](high-availability.md#availability-zone-support) 的功能。 因此，与本机 Apache Cassandra 不同的是，Azure Cosmos DB 允许您通过更精细地浏览 CAP 定理的 [平衡](consistency-levels.md#rto) 。

## <a name="mapping-consistency-levels"></a>映射一致性级别

Azure Cosmos DB 平台提供一组五个明确定义的、业务用例的一致性设置，与复制和 [CAP 定理](https://en.wikipedia.org/wiki/CAP_theorem) 和 [PACLC 定理](https://en.wikipedia.org/wiki/PACELC_theorem)定义的折衷相关。 由于此方法与 Apache Cassandra 明显不同，我们建议你花些时间查看并了解 [文档](consistency-levels.md)中 Azure Cosmos DB 一致性设置，或观看此简短 [视频](https://www.youtube.com/watch?v=t1--kZjrG-o) 指南来了解 Azure Cosmos DB 平台中的一致性设置。

下表说明了使用 Cassandra API 时 Apache Cassandra 与 Azure Cosmos DB 一致性级别之间的可能映射。 这将显示单个区域的配置、具有单区域写入的多区域读取和多区域写入。

> [!NOTE]
> 这些不是精确的映射。 相反，我们提供了最接近 Apache Cassandra 的物，并消除了最右边的列中的任何定性差别。 如前所述，我们建议查看 Azure Cosmos DB 的 [一致性设置](consistency-levels.md)。 

:::image type="content" source="./media/cassandra-consistency/account.png" alt-text="Cassandra 一致性帐户级别映射" lightbox="./media/cassandra-consistency/account.png" :::

:::image type="content" source="./media/cassandra-consistency/dynamic.png" alt-text="Cassandra 一致性动态映射" lightbox="./media/cassandra-consistency/dynamic.png" :::

如果使用非常一致性以外的一致性级别配置了 Azure Cosmos 帐户，则可以通过查看概率有限过期 (PBS) 指标，找到客户端获得工作负荷的非常一致读取的概率。 此指标在 Azure 门户中公开，若要了解详细信息，请参阅[监视概率有限过期性 (PBS) 指标](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)。

概率有限过期表明了最终一致的最终程度。 通过此指标可深入了解在 Azure Cosmos 帐户中目前配置的一致性级别之间获得更非常一致性的频率。 换句话说，可看到获得写入和读取区域组合的非常一致读取的概率（以毫秒计量）。

## <a name="next-steps"></a>后续步骤

详细了解 Azure Cosmos DB 的全局分发和一致性级别：

* [全局分布概述](distribute-data-globally.md)
* [一致性级别概述](consistency-levels.md)
* 高可用性
