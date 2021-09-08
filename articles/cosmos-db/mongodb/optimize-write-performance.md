---
title: 优化 Azure Cosmos DB API for MongoDB 中的写入性能
description: 本文介绍如何优化 Azure Cosmos DB API for MongoDB 中的写入性能，以便尽可能地以最低的成本获得最高的吞吐量。
author: gahl-levy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 08/26/2021
ms.author: gahllevy
ms.openlocfilehash: 2e04953e766c76275079731751cb006fe46712e7
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123039547"
---
# <a name="optimize-write-performance-in-azure-cosmos-db-api-for-mongodb"></a>优化 Azure Cosmos DB API for MongoDB 中的写入性能
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

优化写入性能有助于充分利用 Azure Cosmos DB API for MongoDB 的无限缩放能力。 与其他托管的 MongoDB 服务不同，API for MongoDB 可自动以透明方式将集合分片（使用分片集合时），以便能够无限缩放。 

你写入数据的方式需要考虑到这一点。为此可将数据并行化并分散到多个分片，以充分利用数据库和集合在写入方面的优势。 本文将介绍优化写入性能的最佳做法。

## <a name="spread-the-load-across-your-shards"></a>将负载分散到多个分片
将数据写入到分片的 API for MongoDB 集合时，数据将拆分（分片）为微小的切片，并根据分片键字段的值写入到每个分片。 可将每个切片视为虚拟机的一小部分，其中仅存储了包含一个唯一分片键值的文档。 

如果应用程序将大量数据写入单个分片，则这种做法是低效的，因为该应用只会最大程度地利用一个分片的吞吐量，而不会将负载分布到所有分片。 写入负载将通过并行写入到多个包含唯一分片键值的文档，在整个集合中均匀分布。

这种做法的一个例子是根据类别字段分片的产品目录应用程序。 此类应用程序不应该是每次写入到一个类别（分片），而最好是同时写入到所有类别，以实现最大写入吞吐量。 

## <a name="reduce-the-number-of-indexes"></a>减少索引数
[索引编制](../mongodb-indexing.md)是一个很好的功能，可以显著减少查询数据所需的时间。 为了获得最灵活的查询体验，API for MongoDB 默认将对数据启用通配符索引，以便能够极快地针对所有字段进行查询。 但是，在写入数据时，包含通配符索引的所有索引会造成额外的负载，因为写入操作会更改集合与索引。 

将索引数减少至支持查询所需的索引数能使写入速度变得更快且开销更低。 通常，我们建议采取以下做法：

* 用作筛选依据的任何字段应有相应的单字段索引。 这种做法还会启用多字段筛选。
* 用作排序依据的任何字段组应有其组合索引。 

## <a name="set-ordered-to-false-in-the-mongodb-drivers"></a>在 MongoDB 驱动程序中将 ordered 设置为 false
默认情况下，在写入数据时，MongoDB 驱动程序会将 ordered 选项设置为“true”，即，按顺序逐个写入每个文档。 此选项会降低写入性能，因为每个写入请求必须等待上一个写入请求完成。 写入数据时，将此选项设置为 false 可提高性能。 

```JavaScript
db.collection.insertMany(
   [ <doc1> , <doc2>, ... ],
   {
      ordered: false
   }
)
```

## <a name="tune-for-the-optimal-batch-size-and-thread-count"></a>进行优化以使用最佳批大小和线程计数
在多个线程/进程之间并行化写入操作是缩放写入操作的关键所在。 对于每个进程/线程，API for MongoDB 接受按批（一批最多包含 1,000 个文档）进行写入。 

如果每个进程/线程一次写入 1,000 个以上的文档，则应将 `insertMany()` 等客户端函数限制为大约 1,000 个文档。 否则，客户端将等待每个批次提交，然后再转到下一批。 在某些情况下，将批拆分为包含少于或者略多于 1,000 个文档可以提高速度。



## <a name="next-steps"></a>后续步骤

* 详细了解如何[在 API for MongoDB 中编制索引](../mongodb-indexing.md)。
* 详细了解 [Azure Cosmos DB 的分片/分区](../partitioning-overview.md)。
* 详细了解如何[排查常见问题](error-codes-solutions.md)。
* 尝试为迁移到 Azure Cosmos DB 进行容量计划？ 可以使用有关现有数据库群集的信息进行容量规划。
    * 如果只知道现有数据库群集中的 vCore 和服务器数量，请阅读[使用 vCore 或 vCPU 估算请求单位](../convert-vcore-to-request-unit.md) 
    * 如果你知道当前数据库工作负载的典型请求速率，请阅读[使用 Azure Cosmos DB 容量规划工具估算请求单位](estimate-ru-capacity-planner.md)
