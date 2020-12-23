---
title: 在 Azure Cosmos DB 中优化请求的成本
description: 本文介绍如何在 Azure Cosmos DB 上发出请求时优化成本。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 3f4c33a88d9a8fbf2c3d64135d93da54cf75fab3
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097492"
---
# <a name="optimize-request-cost-in-azure-cosmos-db"></a>优化 Azure Cosmos DB 的请求成本
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

本文介绍如何将读取和写入请求转换为 [请求单位](request-units.md) ，以及如何优化这些请求的费用。 读取操作包括点读取和查询。 写入操作包括插入、替换、删除和更新插入项。

Azure Cosmos DB 提供了一组丰富的数据库操作，这些操作可对容器中的项进行操作。 与这些操作关联的成本取决于完成操作所需的 CPU、IO 和内存。 无需考虑和管理硬件资源，你可以将请求单位 (RU) 为执行各种数据库操作以处理请求所需的资源的单个度量值。

## <a name="measuring-the-ru-charge-of-a-request"></a>衡量请求的 RU 费用

务必要测量请求的 RU 费用，以了解其实际成本，并评估优化的有效性。 你可以通过使用 Azure 门户或检查从 Azure Cosmos DB 中的某个 Sdk 发送回的响应来提取此成本。 有关如何实现的详细说明，请参阅 [查找 Azure Cosmos DB 中的请求单位费用](find-request-unit-charge.md) 。

## <a name="reading-data-point-reads-and-queries"></a>读取数据：点读取和查询

Azure Cosmos DB 中的读取操作通常按以下方式按从最快/最高效到较慢/最有效的 RU 消耗进行排序：  

* 点读取（对单个项 ID 和分区键进行键/值查找）。
* 单个分区键内具有筛选器子句的查询。
* 针对任何属性都没有等于或范围筛选器子句的查询。
* 不包含筛选器的查询。

### <a name="role-of-the-consistency-level"></a>一致性级别的角色

使用 " **强** " 或 " **有限过期** " [一致性级别](consistency-levels.md)时，任何读取操作的 RU 开销 (点读取或查询) 会加倍。

### <a name="point-reads"></a>点读取

影响某个 (点的 RU 费用的唯一因素（) 所使用的一致性级别除外）是检索到的项的大小。 下表显示大小为 1 KB、100 KB 的项的点读取的 RU 开销。

| **项大小** | **一次点读取成本** |
| --- | --- |
| 1 KB | 1 RU |
| 100 KB | 10 RU |

由于 point 读取 (项 ID 上的键/值查找) 是最有效的读取类型，因此应确保项 ID 具有有意义的值，以便可以在可能的情况下，使用一个点读取 (而不是查询) 来提取项。

### <a name="queries"></a>查询

查询的请求单位依赖于许多因素。 例如，加载/返回的 Azure Cosmos 项的数量、对索引的查找次数、查询编译时间等详细信息。 Azure Cosmos DB 保证在相同数据上执行相同的查询时，即使重复执行，也始终使用相同数量的请求单位。 使用查询执行指标的查询配置文件使你可以很好地了解请求单位的使用情况。  

在某些情况下，可能会在查询的分页执行中看到 200 个和 429 个响应序列以及变量请求单位，这是因为查询将根据可用的 RU 尽可能快地运行。 可能会看到查询执行在服务器和客户端之间分成多个页面/往返。 例如，10,000 个项可以作为多个页面返回，每个页面根据对该页面执行的计算收费。 对这些页面求和时，应获得与整个查询相同的 RU 数。

#### <a name="metrics-for-troubleshooting-queries"></a>查询故障排除指标

查询所使用的性能和吞吐量 (包括用户定义函数) 主要取决于函数体。 若要确定在 UDF 中花费多少时间来处理查询执行以及使用的 ru 数，最简单的方法是启用查询度量值。 如果使用的是 .NET SDK，则以下是 SDK 返回的示例查询指标：

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

Azure Cosmos DB 通过使用预配的吞吐量模型，在吞吐量和延迟方面提供可预测的性能。 预配的吞吐量以每秒[请求单位](request-units.md)或 RU/秒表示。 请求单位 (RU) 是对计算 CPU、内存、IO 等资源的逻辑抽象，这是执行请求所必需的。 预留的预配吞吐量 (RU) 专用于容器或数据库，以提供可预测的吞吐量和延迟。 预配置吞吐量使 Azure Cosmos DB 能够以任何规模提供可预测且一致的性能，保证低延迟和高可用性。 请求单位表示规范化货币，简化了对应用程序需要多少资源的推理。

请求标头中返回的请求费用表示给定查询的费用。 例如，如果查询返回 1000 个 1 KB 项，则操作成本为 1000。 因此在一秒内，服务器在对后续请求进行速率限制之前，只接受两个此类请求。 有关详细信息，请参阅[请求单位](request-units.md)一文和请求单位计算器。

## <a name="writing-data"></a>写入数据

写入项的 RU 费用取决于：

- 项大小。
- [索引策略](index-policy.md)所涵盖并需要建立索引的属性的数目。

使用小于5个属性的 1 KB 项插入索引5个 ru 的成本。 将项开销替换为插入同一项所需的电量的两倍。

### <a name="optimizing-writes"></a>优化写入

优化编写操作的 RU 成本的最佳方式是将您的项目和索引的属性数目。

- 在 Azure Cosmos DB 中存储非常大的项会产生较高的 RU 费用，并可将其视为反模式。 特别是，不要存储不需要查询的二进制内容或大块文本。 最佳做法是将此类数据放入 [Azure Blob 存储](../storage/blobs/storage-blobs-introduction.md) ，并将引用存储 (或链接) 到写入 Azure Cosmos DB 的项中的 Blob。
- 优化索引策略以便仅为查询筛选的属性编制索引，可以在写操作所使用的 ru 中产生巨大的差异。 在创建新容器时，默认的索引策略将为每个属性和在项目中找到的每个属性进行索引。 虽然这是开发活动的一个很好的默认设置，但强烈建议您在投入生产环境时或在工作负荷开始接收大量流量时，重新评估并 [自定义索引策略](how-to-manage-indexing-policy.md) 。

当执行批量数据引入时，还建议使用 [Azure Cosmos DB 批量执行器库](bulk-executor-overview.md) ，因为它旨在优化此类操作的 RU 消耗。 另外，还可以使用在同一个库上构建的 [Azure 数据工厂](../data-factory/introduction.md) 。

## <a name="next-steps"></a>后续步骤

接下来，可通过以下文章详细了解 Azure Cosmos DB 中的成本优化：

* 详细了解[开发和测试优化](optimize-dev-test.md)
* 详细了解[了解 Azure Cosmos DB 帐单](understand-your-bill.md)
* 详细了解如何[优化吞吐量成本](optimize-cost-throughput.md)
* 详细了解如何[优化存储成本](optimize-cost-storage.md)
* 详细了解[优化多区域 Azure Cosmos 帐户的成本](optimize-cost-regions.md)
* 详细了解 [Azure Cosmos DB 保留的容量](cosmos-db-reserved-capacity.md)
