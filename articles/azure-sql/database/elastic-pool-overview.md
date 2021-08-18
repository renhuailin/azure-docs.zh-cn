---
title: 使用弹性池管理多个数据库
description: 使用弹性池在 Azure SQL 数据库中管理和缩放多个数据库（成千上万个）。 可以按一个价格将资源分布到需要的任何位置。
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: arvindshmicrosoft
ms.author: arvindsh
ms.reviewer: mathoma
ms.date: 06/23/2021
ms.openlocfilehash: a3847bb1ac435779e50ce8cc41a3940bb6adfab7
ms.sourcegitcommit: cd7d099f4a8eedb8d8d2a8cae081b3abd968b827
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2021
ms.locfileid: "112965123"
---
# <a name="elastic-pools-help-you-manage-and-scale-multiple-databases-in-azure-sql-database"></a>弹性池有助于在 Azure SQL 数据库中管理和缩放多个数据库
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL 数据库弹性池是一种简单且经济高效的解决方案，用于管理和缩放具有不断变化且不可预测的使用需求的多个数据库。 同一弹性池中的所有数据库位于单个服务器上，并以固定价格共享固定数量的资源。 Azure SQL 数据库中的弹性池可使 SaaS 开发人员将一组数据库的价格性能优化在规定的预算内，同时为每个数据库提供性能弹性。

## <a name="what-are-sql-elastic-pools"></a>什么是 SQL 弹性池

SaaS 开发人员构建在由多个数据库组成的大规模数据层上的应用程序。 常见的应用程序模式是为每位客户设置单一数据库。 但不同的客户通常有不同的和不可预测的使用模式，并且很难预测每个数据库用户的资源需求。 传统上，有两个选择：

- 基于高峰使用情况和付款情况过度预配资源或
- 采用低配节省成本，但这样会牺牲高峰期的性能和客户满意度。

弹性池通过确保数据库获取需要时所需的性能资源来解决这个问题。 它们提供了一个可预测预算内的简单的资源分配机制。 若要深入了解如何通过弹性池设计 SaaS 应用程序的模式，请参阅 [具有 Azure SQL 数据库的多租户 SaaS 应用程序的设计模式](saas-tenancy-app-design-patterns.md)。
>
> [!IMPORTANT]
> 弹性池没有按照数据库收取的费用。 对于池存在的每个小时，需要支付最高的 eDTU 或 vCore 费用，无论使用量是多少，也不管池处于活动状态的时间是否小于一小时。

弹性池可让开发人员为由多个数据库共享的池购买资源，以适应单一数据库使用时段不可预测的情况。 可以根据[基于 DTU 的购买模型](service-tiers-dtu.md)或[基于 vCore 的购买模型](service-tiers-vcore.md)为池配置资源。 池的资源要求取决于其数据库的聚合使用量。 池可用的资源数量由开发者预算控制。 开发者只需将数据库添加到池，根据需要为数据库设置最小和最大资源（最小和最大 DTU 数，或者最小或最大 vCore 数，具体取决于所选的资源模型），然后基于预算设置池的资源。 开发人员可以使用池顺畅地扩大其服务，以渐增的规模从精简的新创公司发展到成熟的企业。

在池中，单独的数据库都被赋予了在固定参数内自动缩放的灵活性。 高负荷下的数据库可能会消耗更多的资源以满足需求。 低负荷下的数据库消耗较少的资源，没有任何负荷的数据库不会消耗任何资源。 设置整个池（而非单个数据库）的资源简化了管理任务。 此外，必须具有该池的可预测预算。 可在故障时间最短的情况下向现有池添加其他资源。 同样，随时可以从现有池中删除不再需要的额外资源。 并且可以从池中添加或删除数据库。 如果可以预测到数据库的资源利用率不足，则将其移出。

> [!NOTE]
> 将数据库移入或移出弹性池时，除了在操作结束时删除数据库连接时有短暂的停机时间（大约为几秒）外，几乎没有停机时间。

## <a name="when-should-you-consider-a-sql-database-elastic-pool"></a>何时应当考虑使用 SQL 数据库弹性池

池很适合具有特定使用模式的大量数据库。 对于给定的数据库，此模式的特征是低平均使用量与相对不频繁的使用高峰。 相反，具有持久性中-高利用率的多个数据库不应放在同一弹性池中。

可以加入池的数据库越多，就可以节省更多的成本。 可能只需两个 S3 数据库就可以节省开支，具体取决于应用程序使用模式。

以下各部分有助于了解如何评估特定的数据库集合是否会因使用池而受益。 这些示例使用标准池，但同样的原理也适用于基本和高级池。

### <a name="assessing-database-utilization-patterns"></a>评估数据库使用模式

下图显示了一个数据库示例，该数据库有大量的闲置时间，但也会定期出现活动高峰。 这是适合池的使用模式：

   ![适用于池的单一数据库](./media/elastic-pool-overview/one-database.png)

该图显示了从 12:00 到 1:00 的 1 小时内的 DTU 使用情况，其中每个数据点的粒度为 1 分钟。 在 12:10，DB1 使用的 DTU 个数达到峰值（90 个 DTU），但其整体平均使用量低于五个 DTU。 在单一数据库中运行此工作负荷需要 S3 计算大小，但在低活动期间，这可使大多数资源处于未使用状态。

池可让这些未使用的 DTU 跨多个数据库共享，因此减少了所需的 DTU 数和总体成本。

以上一个示例为基础，假设有其他数据库具有与 DB1 类似的使用模式。 在接下来的两个图中，4 个数据库和 20 个数据库的使用量分层放在同一图形中，以演示在使用基于 DTU 的购买模型时数据库使用量在整个时间段上非重叠的性质：

   ![使用模式适用于池的 4 个数据库](./media/elastic-pool-overview/four-databases.png)

   ![使用模式适用于池的 20 个数据库](./media/elastic-pool-overview/twenty-databases.png)

在上图中，黑线表示跨所有 20 个数据库的聚合 DTU 使用量。 其中表明聚合 DTU 使用量永远不会超过 100 个 DTU，并指出 20 个数据库可以在此时间段内共享 100 个 eDTU。 相比于将每个数据库放入单一数据库的 S3 计算大小，这会导致 DTU 减少 20 倍和价格降低 13 倍。

由于以下原因，此示例很理想：

- 每一数据库之间的高峰使用量和平均使用量有相当大的差异。
- 每个数据库的高峰使用量在不同时间点发生。
- eDTU 会在多个数据库之间共享。

在 DTU 购买模型中，池的价格取决于池的 eDTU。 尽管池的 eDTU 单位价格比单一数据库的 DTU 单位价格多 1.5 倍，但 **池 eDTU 可由多个数据库共享，因而所需的 eDTU 总数更少**。 定价方面和 eDTU 共享的这些差异是池可以提供成本节省可能性的基础。

在 vCore 购买模型中，弹性池的 vCore 单价与单一数据库的 vCore 单价相同。

## <a name="how-do-i-choose-the-correct-pool-size"></a>如何选择正确的池大小

池的最佳大小取决于聚合池中所有数据库所需的资源。 这涉及到决定以下项：

- 池中所有数据库使用的最大计算资源。  计算资源由 eDTU 或 vCore 编制索引，具体取决于所选的购买模型。
- 池中所有数据库使用的最大存储字节。

有关每种购买模型中的服务层级和资源限制，请参阅[基于 DTU 的购买模型](service-tiers-dtu.md)或[基于 vCore 的购买模型](service-tiers-vcore.md)。

以下步骤可帮助你评估池是否比单一数据库更具成本效益：

1. 通过如下方式来估算池所需的 eDTU 或 vCore：
   - 对于基于 DTU 的购买模型：
     - MAX（<DB 的总数目 &times; 每 DB 的平均 DTU 使用率>、<同时达到峰值的 DB 数目 &times; 每 DB 的峰值 DTU 使用率>）   
   - 对于基于 vCore 的购买模型：
     - MAX（<DB 的总数目 &times; 每 DB 的平均 vCore 使用率>、<同时达到峰值的 DB 数目 &times; 每 DB 的峰值 vCore 使用率>）   
2. 通过添加池中所有数据库所需的数据大小，估计池所需的总存储空间。 对于 DTU 购买模型，确定提供此存储量的 eDTU 池大小。
3. 对于基于 DTU 的购买模型，请取步骤 1 和步骤 2 中 eDTU 估算值中较大的那个。 对于基于 vCore 的购买模型，请取步骤 1 中的 vCore 估算值。
4. 请参阅 [SQL 数据库定价页](https://azure.microsoft.com/pricing/details/sql-database/)，找到大于步骤 3 中估算值的最小池大小。
5. 将步骤 4 的池价格与使用单一数据库适当计算大小的价格相比较。

> [!IMPORTANT]
> 如果池中的数据库数量接近所支持的最大值，请确保[考虑密集弹性池中的资源管理](elastic-pool-resource-management.md)。

### <a name="per-database-properties"></a>按数据库属性

可以选择设置“按数据库”属性来修改弹性池中的资源使用模式。 有关详细信息，请参阅 [DTU](resource-limits-dtu-elastic-pools.md#database-properties-for-pooled-databases) 和 [vCore](resource-limits-vcore-elastic-pools.md#database-properties-for-pooled-databases) 弹性池的资源限制文档。

## <a name="using-other-sql-database-features-with-elastic-pools"></a>将其他 SQL 数据库功能与弹性池一起使用

### <a name="elastic-jobs-and-elastic-pools"></a>弹性作业和弹性池

借助池，可以通过在 **[elastic jobs](elastic-jobs-overview.md)** 。 弹性作业可消除与大量数据库有关的大部分问题。

有关用于操作多个数据库的其他数据库工具的详细信息，请参阅 [使用 Azure SQL 数据库进行扩展](elastic-scale-introduction.md)。

### <a name="business-continuity-options-for-databases-in-an-elastic-pool"></a>弹性池中的数据库的业务连续性选项

共用数据库通常支持和可用于单一数据库相同的[业务连续性功能](business-continuity-high-availability-disaster-recover-hadr-overview.md)。

- **时间点还原**

  时间点还原使用自动的数据库备份将池中的数据库恢复到特定的时间点。 请参阅[时间点还原](recovery-using-backups.md#point-in-time-restore)

- **异地还原**

  当数据库因其所在的区域发生事故而不可用时，异地还原会提供默认的恢复选项。 请参阅[还原 Azure SQL 数据库或故障转移到辅助数据库](disaster-recovery-guidance.md)

- **活动异地复制**

  对于具有异地还原无法提供的更强烈的恢复要求的应用程序，请配置[活动异地复制](active-geo-replication-overview.md)或[自动故障转移组](auto-failover-group-overview.md)。

## <a name="creating-a-new-sql-database-elastic-pool-using-the-azure-portal"></a>使用 Azure 门户创建新的 SQL 数据库弹性池

在 Azure 门户中可以通过两种方法创建弹性池。

1. 转到 [Azure 门户](https://portal.azure.com)，创建弹性池。 搜索“Azure SQL”并将其选中。
2. 选择“+添加”以打开“选择 SQL 部署选项”页。 可以通过在“数据库”磁贴上选择“显示详细信息”来查看有关弹性池的其他信息 。
3. 在“数据库”磁贴上的“资源类型”下拉列表中选择“弹性池”，然后选择“创建”   ：

   ![创建弹性池](./media/elastic-pool-overview/create-elastic-pool.png)

4. 或者可以创建一个弹性池：导航到现有服务器，然后单击“+ 新建池”，直接在该服务器中创建一个池。

> [!NOTE]
> 可以在服务器上创建多个池，但不能将数据库从不同的服务器添加到同一个池中。

该池的服务层级决定了池中弹性数据库的可用功能，以及每个数据库可用的最大资源量。 有关详细信息，请参阅 [DTU 模型](resource-limits-dtu-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes)中弹性池的资源限制。 有关弹性池的基于 vCore 的资源限制，请参阅[基于 vCore 的资源限制 - 弹性池](resource-limits-vcore-elastic-pools.md)。

若要配置池的资源和定价，请单击“配置池”。 然后选择服务层级，将数据库添加到池，并配置池及其数据库的资源限制。

完成配置池后，可以单击“应用”，将池命名，然后单击“确定”以创建池。

## <a name="monitor-an-elastic-pool-and-its-databases"></a>监视弹性池及其数据库

在 Azure 门户中，可以监视弹性池和该池中的数据库的利用率。 还可以对弹性池进行一组更改，并同时提交所有更改。 这些更改包括添加或删除数据库、更改弹性池设置或更改数据库设置。

可以将内置的[性能监视](./performance-guidance.md)和[警报工具](./alerts-insights-configure-portal.md)与性能等级组合使用。  此外，SQL 数据库可[发出指标和资源日志](./metrics-diagnostic-telemetry-logging-streaming-export-configure.md?tabs=azure-portal)，以方便进行监视。

## <a name="customer-case-studies"></a>客户案例研究

- [SnelStart](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-snelstart/)

  SnelStart 使用 Azure SQL 数据库的弹性池以每月 1,000 个新 Azure SQL 数据库的速度快速扩展其业务服务。

- [Umbraco](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-umbraco/)

  Umbraco 使用 Azure SQL 数据库的弹性池为云中数千个租户快速预配和缩放服务。

- [Daxko/CSI](https://customers.microsoft.com/story/726277-csi-daxko-partner-professional-service-azure)

   Daxko/CSI 使用 Azure SQL 数据库的弹性池加速其开发周期和增强其客户服务与性能。

## <a name="next-steps"></a>后续步骤

- 有关定价信息，请参阅[弹性池定价](https://azure.microsoft.com/pricing/details/sql-database/elastic)。
- 若要缩放弹性池，请参阅[缩放弹性池](elastic-pool-scale.md)和[缩放弹性池 - 示例代码](scripts/monitor-and-scale-pool-powershell.md)
- 若要深入了解如何通过弹性池设计 SaaS 应用程序的模式，请参阅 [具有 Azure SQL 数据库的多租户 SaaS 应用程序的设计模式](saas-tenancy-app-design-patterns.md)。
- 有关使用弹性池的 SaaS 教程，请参阅 [Wingtip SaaS 应用程序简介](saas-dbpertenant-wingtip-app-overview.md)。
- 若要了解具有多个数据库的弹性池中的资源管理，请参阅[密集弹性池中的资源管理](elastic-pool-resource-management.md)。
