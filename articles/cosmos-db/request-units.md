---
title: Azure Cosmos DB 中的请求单位即吞吐量和性能货币
description: 了解如何指定和评估 Azure Cosmos DB 中的请求单位要求。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/26/2021
ms.custom: seo-nov-2020
ms.openlocfilehash: e58e0911911035578c70ebe218af8f7e8c25bcf5
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123032517"
---
# <a name="request-units-in-azure-cosmos-db"></a>Azure Cosmos DB 中的请求单位
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB 支持多种 API，例如 SQL、MongoDB、Cassandra、Gremlin 和表。 每个 API 具有自身的数据库操作集。 这些操作包括简单的点读取和写入，以及复杂的查询等等。 每个数据库操作根据其复杂性消耗系统资源。

所有数据库操作的成本将由 Azure Cosmos DB 规范化，并以“请求单位”（简称 RU）表示。 可将请求单位视为性能货币，它抽象化了执行 Azure Cosmos DB 支持的数据库操作所需的系统资源，例如 CPU、IOPS 和内存。

对于一个 1 KB 的项，执行点读取（即，按 ID 和分区键值提取单个项）的成本是 1 个请求单位（或 1 RU）。 以类似方式为其他所有数据库操作分配 RU 成本。 不管使用哪个 API 来与 Azure Cosmos 容器和数据库操作交互，都始终以 RU 来计量成本。 无论数据库操作是写入、点读取还是查询，都始终以 RU 来计量成本。

下图展示了 RU 的概要情况。

:::image type="content" source="./media/request-units/request-units.png" alt-text="数据库操作消耗请求单位" border="false":::

为了方便管理和规划容量，Azure Cosmos DB 会确保针对给定数据集执行的给定数据库操作的 RU 数是确定性的。 可以检查响应标头来跟踪任一数据库操作消耗的 RU 数。 了解[影响 RU 费用的因素](request-units.md#request-unit-considerations)以及应用程序吞吐量要求后，可以经济高效地运行应用程序。

你使用的 Azure Cosmos 帐户的类型决定了消耗的 RU 的计费方式。 可以在 3 种模式下创建帐户：

1. **预配的吞吐量模式**：在此模式下，将按秒来预配应用程序的 RU 数，增量为每秒 100 RU。 若要缩放应用程序的预配吞吐量，可以随时增加或减少 RU 数，增量或减量为 100 RU。 可以编程方式或使用 Azure 门户进行更改。 系统会针对你预配的每秒 RU 数按小时向你收费。 若要了解详细信息，请参阅[预配的吞吐量](set-throughput.md)一文。

   可在两个不同的粒度级别预配吞吐量：

   * **容器**：有关详细信息，请参阅 [对 Azure Cosmos 容器预配吞吐量](how-to-provision-container-throughput.md)。
   * **数据库**：有关详细信息，请参阅 [对 Azure Cosmos 数据库预配吞吐量](how-to-provision-database-throughput.md)。

2. 无服务器模式：在此模式下，在 Azure Cosmos 帐户中创建资源时无需预配任何吞吐量。 在计费周期结束时，会针对你的数据库操作已消耗的请求单位量计费。 若要了解详细信息，请参阅[无服务器吞吐量](serverless.md)一文。 

3. **自动缩放模式**：在此模式下，你可以根据使用情况自动且即时地缩放数据库或容器的吞吐量（RU/秒），而不会影响工作负荷的可用性、延迟、吞吐量或性能。 此模式非常适合具有可变或不可预测流量模式且需要高性能和大规模 SLA 的关键工作负荷。 若要了解详细信息，请参阅[自动缩放吞吐量](provision-throughput-autoscale.md)一文。 

## <a name="request-unit-considerations"></a>请求单位注意事项

在估计你的工作负荷消耗的 RU 数量时，请考虑以下因素：

* **项大小**：随着项的增大，读取或写入该项所要消耗的 RU 数也会增加。

* **项索引**：默认情况下会自动为每个项创建索引。 如果选择不为容器中的某些项创建索引，则消耗的 RU 数将会减少。

* **项属性计数**：假设所有属性采用默认索引，写入某个项所要消耗的 RU 数会随着项属性计数的增加而增加。

* **带索引的属性**：每个容器的索引策略都可确定默认情况下要进行索引的属性类别。 若要减少写入操作的 RU 消耗，请限制带索引的属性数目。

* **数据一致性**：在执行读取操作时，非常一致性和有限过期一致性级别消耗的 RU 数大约比其他宽松一致性级别要多两倍。

* 读取的类型：点读取消耗的 RU 明显少于查询。

* **查询模式**：查询的复杂性会影响操作使用的 RU 数。 影响查询操作成本的因素： 
 
  * 查询结果数
  * 谓词数
  * 谓词性质
  * 用户定义的函数数目
  * 源数据的大小
  * 结果集的大小
  * 投影数

  对相同数据的相同查询在重复执行时始终消耗相同数量的 RU。

* **脚本的使用**：与查询一样，存储过程和触发器也是根据所执行的操作的复杂性来消耗 RU。 开发应用程序时，请检查[请求费用标头](./optimize-cost-reads-writes.md#measuring-the-ru-charge-of-a-request)，以更好地了解每个操作消耗的 RU 容量。

## <a name="request-units-and-multiple-regions"></a>请求单位和多个区域

如果针对 Cosmos 容器（或数据库）预配了 'R' 个 RU，则 Cosmos DB 可确保 'R' 个 RU 在与 Cosmos 帐户关联的每个区域中都可用。    无法有选择地将 RU 分配给特定区域。 针对 Cosmos 容器（或数据库）预配的 RU 是在与 Cosmos 帐户关联的所有区域中预配的。

假设为 Cosmos 容器配置了 R 个 RU，并且有 N 个区域与 Cosmos 帐户关联，那么该容器在全局可用的 RU 总数 = R x N 。

所选的[一致性模型](consistency-levels.md)也会影响吞吐量。 与更强的一致性级别（例如，“有限过期”或“强”一致性）相比，更宽松的一致性级别（例如“会话”、“一致前缀”和“最终”一致性）可以获得约 2 倍的读取吞吐量。

## <a name="next-steps"></a>后续步骤

- 详细了解如何[对 Azure Cosmos 容器和数据库预配吞吐量](set-throughput.md)。
- 详细了解 [Azure Cosmos DB 上的无服务器](serverless.md)。
- 详细了解[逻辑分区](./partitioning-overview.md)。
- 了解[如何对 Azure Cosmos 容器预配吞吐量](how-to-provision-container-throughput.md)。
- 了解[如何对 Azure Cosmos 数据库预配吞吐量](how-to-provision-database-throughput.md)。
- 了解如何[查找操作所产生的请求单位费用](find-request-unit-charge.md)。
- 了解如何[在 Azure Cosmos DB 中优化预配的吞吐量成本](optimize-cost-throughput.md)。
- 了解如何[优化 Azure Cosmos DB 中的读取和写入成本](optimize-cost-reads-writes.md)。
- 了解如何[优化 Azure Cosmos DB 中的查询成本](./optimize-cost-reads-writes.md)。
- 了解如何[使用指标监视吞吐量](use-metrics.md)。
- 尝试为迁移到 Azure Cosmos DB 进行容量计划？
    - 如果你只知道现有数据库群集中的 vCore 和服务器数量，请阅读[使用 vCore 或 vCPU 估算请求单位](convert-vcore-to-request-unit.md) 
    - 如果知道当前数据库工作负荷的典型请求速率，请阅读[使用 Azure Cosmos DB 容量计划工具估算请求单位](estimate-ru-with-capacity-planner.md)
