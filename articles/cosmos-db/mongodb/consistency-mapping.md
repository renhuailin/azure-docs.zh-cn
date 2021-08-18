---
title: 映射 Azure Cosmos DB API for MongoDB 的一致性级别
description: 映射 Azure Cosmos DB API for MongoDB 的一致性级别。
author: gahl-levy
ms.author: gahllevy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: fa434fc45b46353d04a22000f9f2f252898cb715
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121777888"
---
# <a name="consistency-levels-for-azure-cosmos-db-and-the-api-for-mongodb"></a>Azure Cosmos DB 和 API for MongoDB 的一致性级别
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

与 Azure Cosmos DB 不一样，本机 MongoDB 并不提供精确定义的一致性保证。 与之相反，本机 MongoDB 允许用户配置下述一致性保证：写入关注、读取关注以及 isMaster 指令 - 目的是将读取操作定向到主副本或辅助副本，以便实现所需的一致性级别。

使用 Azure Cosmos DB 的适用于 MongoDB 的 API 时，MongoDB 驱动程序会将写入区域视为主要副本，所有其他区域为只读副本。 可以选择将哪个与 Azure Cosmos 帐户关联的区域作为主副本。

> [!NOTE]
> Azure Cosmos DB 的默认一致性模型是会话。 会话是一种以客户端为中心的一致性模型，未受到 Cassandra 或 MongoDB 的本机支持。 有关选择哪种一致性模型的详细信息，请参阅 [Azure Cosmos DB 中的一致性级别](../consistency-levels.md)

在使用 Azure Cosmos DB 的适用于 MongoDB 的 API 时：

* 写入关注映射到在 Azure Cosmos 帐户上配置的默认一致性级别。

* Azure Cosmos DB 会将 MongoDB 客户端驱动程序指定的读取关注动态映射到根据读取请求动态配置的某个 Azure Cosmos DB 一致性级别。  

* 可以将与 Azure Cosmos 帐户关联的特定区域批注为“主区域”，方法是将该区域设置为第一个可写区域。 

## <a name="mapping-consistency-levels"></a>映射一致性级别

下表演示了在使用 Azure Cosmos DB 的适用于 MongoDB 的 API 时，如何将本机 MongoDB 写入/读取关注映射到 Azure Cosmos 的一致性级别：

:::image type="content" source="../media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png" alt-text="MongoDB 一致性模型映射" lightbox= "../media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png":::

如果使用非常一致性以外的一致性级别配置了 Azure Cosmos 帐户，则可以通过查看概率有限过期 (PBS) 指标，找到客户端获得工作负荷的非常一致读取的概率。 此指标在 Azure 门户中公开，若要了解详细信息，请参阅[监视概率有限过期性 (PBS) 指标](../how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)。

概率有限过期表明了最终一致的最终程度。 通过此指标可深入了解在 Azure Cosmos 帐户中目前配置的一致性级别之间获得更非常一致性的频率。 换句话说，可看到获得写入和读取区域组合的非常一致读取的概率（以毫秒计量）。

## <a name="next-steps"></a>后续步骤

详细了解 Azure Cosmos DB 的全局分发和一致性级别：

* [全局分布概述](../distribute-data-globally.md)
* [一致性级别概述](../consistency-levels.md)
* 高可用性
