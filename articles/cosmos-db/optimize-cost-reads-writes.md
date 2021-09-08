---
title: 优化 Azure Cosmos DB 中的请求成本
description: 本文介绍了在 Azure Cosmos DB 上发出请求时如何优化成本。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: db6973d19fd8bd4fc4d36903b3205e6c8e01314d
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123029131"
---
# <a name="optimize-request-cost-in-azure-cosmos-db"></a>优化 Azure Cosmos DB 中的请求成本
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

本文介绍了如何将读取和写入请求转换为[请求单位](request-units.md)，以及如何优化这些请求的成本。 读取操作包括点读取和查询。 写入操作包括插入、替换、删除和更新插入项。

Azure Cosmos DB 提供了对容器中的项进行操作的一组丰富的数据库操作。 与这些操作关联的成本取决于完成操作所需的 CPU、IO 和内存。 可以考虑将请求单位 (RU) 视为执行各种数据库操作来处理请求时所需的资源的单一度量值，而无需考虑和管理硬件资源。

## <a name="measuring-the-ru-charge-of-a-request"></a>度量请求的 RU 费用

请务必度量请求的 RU 费用，以了解其实际成本，并评估你的优化的效率。 你可以通过以下方式获取此成本：使用 Azure 门户，或者通过某个 SDK 检查 Azure Cosmos DB 发回的响应。 有关如何实现此目的的详细说明，请参阅[在 Azure Cosmos DB 中查找请求单位费用](find-request-unit-charge.md)。

## <a name="reading-data-point-reads-and-queries"></a>读取数据：点读取和查询

Azure Cosmos DB 中的读取操作通常按 RU 消耗从最快/效率最高到较慢/效率较低进行排序，如下所示：  

* 点读取（对单个项 ID 和分区键进行键/值查找）。
* 单个分区键内具有筛选器子句的查询。
* 针对任何属性都没有等于或范围筛选器子句的查询。
* 不包含筛选器的查询。

### <a name="role-of-the-consistency-level"></a>一致性级别的角色

当使用 **强** 或 **有限过期**[一致性级别](consistency-levels.md)时，任何读取操作（点读取或查询）的 RU 开销都会加倍。

### <a name="point-reads"></a>点读取

影响点读取的 RU 费用的唯一因素（除了使用的一致性级别外）是所检索项的大小。 下表显示了大小为 1 KB 和 100 KB 的项的点读取 RU 成本。

| **项大小** | **一次点读取成本** |
| --- | --- |
| 1 KB | 1 RU |
| 100 KB | 10 RU |

因为点读取（基于项 ID 的键/值查找）是最有效的读取类型，因此你应确保项 ID 具有有意义的值，以便可以在可能的情况下使用点读取（而非查询）来提取项。

### <a name="queries"></a>查询

查询的请求单位依赖于许多因素。 例如，加载/返回的 Azure Cosmos 项的数量、对索引的查找次数、查询编译时间等详细信息。 Azure Cosmos DB 保证在相同数据上执行相同的查询时，即使重复执行，也始终使用相同数量的请求单位。 使用查询执行指标的查询配置文件使你可以很好地了解请求单位的使用情况。  

在某些情况下，可能会在查询的分页执行中看到 200 个和 429 个响应序列以及变量请求单位，这是因为查询将根据可用的 RU 尽可能快地运行。 可能会看到查询执行在服务器和客户端之间分成多个页面/往返。 例如，10,000 个项可以作为多个页面返回，每个页面根据对该页面执行的计算收费。 对这些页面求和时，应获得与整个查询相同的 RU 数。

#### <a name="metrics-for-troubleshooting-queries"></a>用于对查询进行故障排除的指标

查询（包括用户定义的函数）的性能和消耗的吞吐量主要取决于函数主体。 查找 UDF 中的查询执行花费的时间和使用的 RU 数量的最简单方法是启用查询指标。 如果使用的是 .NET SDK，则以下是 SDK 返回的示例查询指标：

```bash
Retrieved Document Count                 :               1              
Retrieved Document Size                  :           9,963 bytes        
Output Document Count                    :               1              
Output Document Size                     :          10,012 bytes        
Index Utilization                        :          100.00 %            
Total Query Execution Time               :            0.48 milliseconds 
  Query Preparation Times 
    Query Compilation Time               :            0.07 milliseconds 
    Logical Plan Build Time              :            0.03 milliseconds 
    Physical Plan Build Time             :            0.05 milliseconds 
    Query Optimization Time              :            0.00 milliseconds 
  Index Lookup Time                      :            0.06 milliseconds 
  Document Load Time                     :            0.03 milliseconds 
  Runtime Execution Times 
    Query Engine Execution Time          :            0.03 milliseconds 
    System Function Execution Time       :            0.00 milliseconds 
    User-defined Function Execution Time :            0.00 milliseconds 
  Document Write Time                    :            0.00 milliseconds 
  Client Side Metrics 
    Retry Count                          :               1              
    Request Charge                       :            3.19 RUs  
```

#### <a name="best-practices-to-cost-optimize-queries"></a>成本优化查询的最佳做法 

优化成本查询时，请考虑以下最佳做法：

* **共置多个实体类型**

   尝试在单个或较少数量的容器中共置多个实体类型。 此方法不仅对定价有好处，而且对查询执行和事务也有好处。 查询的作用域为单个容器；通过存储过程/触发器的多个记录的原子事务的作用域为单个容器内的分区键。 在同一容器内共置实体可以减少网络往返次数，以便解析记录之间的关系。 因此，它可以提高端到端性能，为较大的数据集启用多个记录的原子事务，从而降低成本。 如果你的情景难以在单个或较少数量的容器中共置多个实体类型，通常是因为你正在迁移现有应用程序且不希望进行任何代码更改 - 你应该考虑在数据库级别预配吞吐量。  

* **测量和优化较低的每秒请求单位使用量**

   查询的复杂性会影响操作使用的请求单位 (RU)数量。 谓词数、谓词性质、UDF 数目以及源数据集的大小。 所有这些因素都会影响查询操作的成本。 

通过使用预配置的吞吐量模型，Azure Cosmos DB 在吞吐量和延迟方面提供可预测的性能。 预配的吞吐量以每秒[请求单位](request-units.md)或 RU/秒表示。 请求单位 (RU) 是对计算 CPU、内存、IO 等资源的逻辑抽象，这是执行请求所必需的。 预留的预配吞吐量 (RU) 专用于容器或数据库，以提供可预测的吞吐量和延迟。 预配置吞吐量使 Azure Cosmos DB 能够以任何规模提供可预测且一致的性能，保证低延迟和高可用性。 请求单位表示规范化货币，简化了对应用程序需要多少资源的推理。

请求标头中返回的请求费用表示给定查询的费用。 例如，如果查询返回 1000 个 1 KB 项，则操作成本为 1000。 因此在一秒内，服务器在对后续请求进行速率限制之前，只接受两个此类请求。 有关详细信息，请参阅[请求单位](request-units.md)一文和请求单位计算器。

## <a name="writing-data"></a>写入数据

写入某个项的 RU 成本取决于：

- 项大小。
- [索引编制策略](index-policy.md)涵盖的需要编制索引的属性的数量。

插入一个没有索引的 1 KB 项大约需要 5.5 RU。 替换某个项的成本是插入同一项所需费用的两倍。

### <a name="optimizing-writes"></a>优化写入

优化写入操作的 RU 成本的最佳方式是合理设置要编制索引的项和属性数。

- 在 Azure Cosmos DB 中存储非常大的项会产生较高的 RU 费用，可将其视为反面模式。 特别要注意的是，不要存储不需要查询的二进制内容或大块文本。 最佳做法是将此类数据置于 [Azure Blob 存储](../storage/blobs/storage-blobs-introduction.md)中，并将指向该 blob 的引用（或链接）存储在要写入到 Azure Cosmos DB 的项中。
- 优化索引编制策略以仅为你的查询筛选的属性编制索引可能会对写入操作消耗的 RU 产生巨大影响。 在创建新容器时，默认的索引编制策略会为在你的项中找到的每个属性编制索引。 虽然这是适用于开发活动的一个良好的默认设置，但我们强烈建议你在转到生产环境时或在工作负荷开始收到大量流量时，重新评估并[自定义索引编制策略](how-to-manage-indexing-policy.md)。

当执行数据的批量引入时，我们还建议你使用 [Azure Cosmos DB 批量执行工具库](bulk-executor-overview.md)，因为它旨在优化此类操作的 RU 消耗。 另外，你还可以使用基于这个相同的库构建的 [Azure 数据工厂](../data-factory/introduction.md)。

## <a name="next-steps"></a>后续步骤

接下来，可通过以下文章详细了解 Azure Cosmos DB 中的成本优化：

* 详细了解[开发和测试优化](optimize-dev-test.md)
* 详细了解[了解 Azure Cosmos DB 帐单](understand-your-bill.md)
* 详细了解如何[优化吞吐量成本](optimize-cost-throughput.md)
* 详细了解如何[优化存储成本](optimize-cost-storage.md)
* 详细了解[优化多区域 Azure Cosmos 帐户的成本](optimize-cost-regions.md)
* 详细了解 [Azure Cosmos DB 保留容量](cosmos-db-reserved-capacity.md)
* 正在尝试为迁移到 Azure Cosmos DB 进行容量计划？ 可以使用有关现有数据库群集的信息进行容量计划。
    * 若只知道现有数据库群集中的 vCore 和服务器数量，请阅读[使用 vCore 或 vCPU 估算请求单位](convert-vcore-to-request-unit.md) 
    * 若知道当前数据库工作负载的典型请求速率，请阅读[使用 Azure Cosmos DB 容量计划工具估算请求单位](estimate-ru-with-capacity-planner.md)
