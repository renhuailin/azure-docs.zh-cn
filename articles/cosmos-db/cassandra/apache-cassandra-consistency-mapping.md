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
ms.openlocfilehash: 8159fa7574830a6754a409245f7fca4590a0c5c3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778372"
---
# <a name="apache-cassandra-and-azure-cosmos-db-cassandra-api-consistency-levels"></a>Apache Cassandra 和 Azure Cosmos DB Cassandra API 一致性级别
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

与 Azure Cosmos DB 不同，Apache Cassandra 本身并不提供精确定义的一致性保证。 与之相反，Apache Cassandra 提供一个写入一致性级别和一个读取一致性级别，以便进行高可用性、一致性和延迟方面的权衡。 使用 Azure Cosmos DB 的 Cassandra API 时：

* Apache Cassandra 的写入一致性级别映射到在 Azure Cosmos 帐户上配置的默认一致性级别。 写入操作 (CL) 的一致性不能因请求的不同而（即按请求）更改。

* Azure Cosmos DB 会将 Cassandra 客户端驱动程序指定的读取一致性级别动态映射到根据读取请求动态配置的某个 Azure Cosmos DB 一致性级别。

## <a name="multi-region-writes-vs-single-region-writes"></a>多区域写入与单区域写入

默认情况下，Apache Cassandra 数据库是一个多主系统，不提供使用单区域进行写入并使用多区域复制进行读取的现成选项。 但是，Azure Cosmos DB 提供了允许单区域或[多区域](../how-to-multi-master.md)写入配置的全包式功能。 能够跨多个区域选择单区域写入配置的优点之一是可以避免跨区域冲突情况，并且可以选择跨多个区域保持强一致性。 

使用单区域写入，你可以保持强一致性，同时可以通过[自动故障转移](../high-availability.md#multi-region-accounts-with-a-single-write-region-write-region-outage)跨区域保持一定级别的高可用性。 在此配置中，你仍然可以利用数据局部性，通过将每个请求降级到最终一致性来减少读取延迟。 除了这些功能以外，Azure Cosmos DB 平台还提供了在选择区域时启用[区域冗余](../high-availability.md#availability-zone-support)的功能。 因此，与原生 Apache Cassandra 不同，Azure Cosmos DB 允许你以更细的粒度浏览 CAP 定理[权衡范围](../consistency-levels.md#rto)。

## <a name="mapping-consistency-levels"></a>映射一致性级别

Azure Cosmos DB 平台提供了一组包含五个明确定义、面向业务用例的一致性设置，这些设置与复制和 [CAP 定理](https://en.wikipedia.org/wiki/CAP_theorem)及 [PACLC 定理](https://en.wikipedia.org/wiki/PACELC_theorem)定义的权衡相关。 由于此方法与 Apache Cassandra 有很大的不同，因此建议你花时间查看并了解我们的[文档](../consistency-levels.md)中的 Azure Cosmos DB 一致性设置，或者观看这个简短的[视频](https://www.youtube.com/watch?v=t1--kZjrG-o)指南，以了解 Azure Cosmos DB 平台中的一致性设置。

下表说明了使用 Cassandra API 时 Apache Cassandra 与 Azure Cosmos DB 一致性级别之间的可能映射。 这显示了针对单区域读取、多区域读取以及单区域写入和多区域写入的配置。

> [!NOTE]
> 这些不是确切的映射。 更确切地说，我们提供了与 Apache Cassandra 最接近的类似物，并在最右边的列中消除了任何定性差异的歧义。 如上所述，我们建议查看 Azure Cosmos DB 的[一致性设置](../consistency-levels.md)。 

:::image type="content" source="./media/apache-cassandra-consistency-mapping/account.png" alt-text="Cassandra 一致性帐户级映射" lightbox="./media/apache-cassandra-consistency-mapping/account.png" :::

:::image type="content" source="./media/apache-cassandra-consistency-mapping/dynamic.png" alt-text="Cassandra 一致性动态映射" lightbox="./media/apache-cassandra-consistency-mapping/dynamic.png" :::

如果使用非常一致性以外的一致性级别配置了 Azure Cosmos 帐户，则可以通过查看概率有限过期 (PBS) 指标，找到客户端获得工作负荷的非常一致读取的概率。 此指标在 Azure 门户中公开，若要了解详细信息，请参阅[监视概率有限过期性 (PBS) 指标](../how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)。

概率有限过期表明了最终一致的最终程度。 通过此指标可深入了解在 Azure Cosmos 帐户中目前配置的一致性级别之间获得更非常一致性的频率。 换句话说，可看到获得写入和读取区域组合的非常一致读取的概率（以毫秒计量）。

## <a name="next-steps"></a>后续步骤

详细了解 Azure Cosmos DB 的全局分发和一致性级别：

* [全局分布概述](../distribute-data-globally.md)
* [一致性级别概述](../consistency-levels.md)
* 高可用性
