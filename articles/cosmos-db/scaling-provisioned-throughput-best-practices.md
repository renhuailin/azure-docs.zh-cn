---
title: 缩放预配吞吐量的最佳做法（RU/秒）
description: 了解用于手动和自动缩放吞吐量的缩放预配吞吐量的最佳做法
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 08/20/2021
ms.author: dech
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: e23ff2623f124c78fb665641ba1f113b044d075a
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123439813"
---
# <a name="best-practices-for-scaling-provisioned-throughput-rus"></a>缩放预配吞吐量的最佳做法（RU/秒） 
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

本文介绍缩放数据库或容器（集合、表或图表）的吞吐量（RU/秒）的最佳做法和策略。 当你为任何 Azure Cosmos DB API 的任何资源增加预配手动 RU/秒或自动缩放最大 RU/秒时，这些概念适用。 
 
## <a name="prerequisites"></a>必备条件
- 如果你不熟悉 Azure Cosmos DB 中的分区和缩放，建议首先阅读 [Azure Cosmos DB 中的分区和水平缩放](partitioning-overview.md)一文。
- 如果由于 429 异常而计划缩放 RU/秒，请查看[对 Azure Cosmos DB 请求速率过大 (429) 异常进行诊断及故障排除](troubleshoot-request-rate-too-large.md)中的指导。 在增加 RU/秒之前，请确定问题的根本原因，以及增加 RU/秒是否是正确的解决方案。
 
## <a name="background-on-scaling-rus"></a>缩放 RU/秒的背景信息

当你发送请求以增加数据库或容器的 RU/秒时，根据请求的 RU/秒和当前的物理分区布局，纵向扩展操作将立即或异步完成（通常需要 4-6 小时）。 
- **即时纵向扩展**
    - 如果当前物理分区布局支持请求的 RU/秒，则 Azure Cosmos DB 无需拆分或添加新分区。
    - 因此，操作会立即完成，RU/秒可供使用。 
- **异步纵向扩展**
    - 如果请求的 RU/秒高于物理分区布局支持的数量，Azure Cosmos DB 将拆分现有物理分区。 在资源具有支持请求的 RU/秒所需的最小分区数之前，会发生这种情况。 
    - 因此，操作可能需要一些时间才能完成，通常需要 4-6 小时。
 
每个物理分区最多可支持 10,000 RU/秒（适用于所有 API）的吞吐量和 50 GB 存储（适用于所有 API，具有 30 GB 存储的 Cassandra 除外）。 

## <a name="how-to-scale-up-rus-without-changing-partition-layout"></a>如何在不更改分区布局的情况下纵向扩展 RU/秒

### <a name="step-1-find-the-current-number-of-physical-partitions"></a>步骤 1：查找当前物理分区数。 

导航到“见解” > “吞吐量” > “按 PartitionKeyRangeID 显示的规范化 RU 消耗量 (%)”。 统计 PartitionKeyRangeId 的非重复数。 

:::image type="content" source="media/scaling-provisioned-throughput-best-practices/number-of-physical-partitions.png" alt-text="按 PartitionKeyRangeID 图表统计规范化 RU 消耗量 (%) 中 PartitionKeyRangeId 的非重复数":::

> [!NOTE]
> 图表最多只显示 50 个 PartitionKeyRangeId。 如果资源超过 50 个，可以使用 [Azure Cosmos DB REST API](https://docs.microsoft.com/rest/api/cosmos-db/get-partition-key-ranges#example) 来统计分区总数。 

每个 PartitionKeyRangeId 映射到一个物理分区，并分配用于保存一系列可能的哈希值的数据。 

Azure Cosmos DB 根据分区键跨逻辑分区和物理分区分布数据，以实现横向缩放。 在数据写入后，Azure Cosmos DB 使用分区键值的哈希来确定数据位于哪个逻辑分区和物理分区。

### <a name="step-2-calculate-the-default-maximum-throughput"></a>步骤 2：计算默认最大吞吐量
在无需触发 Azure Cosmos DB 拆分分区的情况下，可以缩放到的最高 RU/秒等于 `Current number of physical partitions * 10,000 RU/s`。

#### <a name="example"></a>示例
假设我们有一个现有的容器，其中有五个物理分区和 30,000 RU/秒的手动预配吞吐量。 我们可以立即将 RU/秒增加到 5 * 10,000 RU/秒 = 50,000 RU/秒。 同样，如果我们有一个容器，它的自动缩放最大 RU/秒为 30,000 RU/秒（在 3000 - 30,000 RU/秒之间缩放），我们可以立即将最大 RU/秒增加到 50,000 RU/秒（在 5000 - 50,000 RU/秒之间缩放）。 
> [!TIP]
> 如果要纵向扩展 RU/秒以响应请求速率过大异常 (429)，建议先将 RU/秒增大到当前物理分区布局支持的最高 RU/秒，并评估新的 RU/秒是否足够，然后再进一步增加。
 
## <a name="how-to-ensure-even-data-distribution-during-asynchronous-scaling"></a>如何在异步缩放期间确保数据分布均匀

### <a name="background"></a>背景

将 RU/秒增大到超过当前物理分区数 * 10,000 RU/秒时，Azure Cosmos DB 将拆分现有分区，直到分区的新数量 = `ROUNDUP(requested RU/s / 10,000 RU/s)`。 在拆分期间，父分区拆分为两个子分区。 

例如，假设我们有一个容器，该容器具有三个物理分区和 30,000 RU/秒的手动预配吞吐量。 如果将吞吐量增加到 45,000 RU/秒，Azure Cosmos DB 将拆分两个现有物理分区，以便总共有 `ROUNDUP(45,000 RU/s / 10,000 RU/s)` = 5 个物理分区。 

> [!NOTE]
> 在拆分期间，应用程序始终可以引入或查询数据。 Azure Cosmos DB 客户端 SDK 和服务会自动处理这种情况，并确保将请求路由到正确的物理分区，因此不需要额外的用户操作。 
 
如果工作负荷在存储和请求量方面分布非常均匀（通常通过按 /id 等高基数字段进行分区来实现），则建议在纵向扩展时设置 RU/秒，以便所有分区均匀拆分。 
 
为了了解原因，我们来举个例子，我们有一个现有的容器，它具有 2 个物理分区、20,000 RU/秒和 80 GB 数据。

由于选择了一个基数较高的良好分区键，数据大致均匀分布在两个物理分区中。 每个物理分区分配有大约 50% 的键空间，这定义为可能的哈希值的总范围。

此外，Azure Cosmos DB 在所有物理分区中均匀分布 RU/秒。 因此，每个物理分区具有 10,000 RU/秒和 50% (40 GB) 的总数据。 下图显示了我们的当前状态。 

:::image type="content" source="media/scaling-provisioned-throughput-best-practices/diagram-1-baseline.png" alt-text="两个 PartitionKeyRangeId，每个都具有 10,000 RU/秒、40 GB 和 50% 的总键空间":::

现在，假设我们要将 RU/秒从 20,000 RU/秒增加到 30,000 RU/秒。 
 
如果只是将 RU/秒增加到 30,000 RU/秒，则只会拆分其中一个分区。 拆分后，我们将具有：
-   一个分区，其中包含 50% 的数据（此分区未拆分）
-   两个分区，其中每个分区包含 25% 的数据（这些分区是拆分父分区产生的子分区）

由于 Azure Cosmos DB 在所有物理分区中均匀分布 RU/秒，因此每个物理分区仍将获得 10,000 RU/秒。 不过，现在存储和请求分布存在偏差。 

在下图中，我们看到分区 3 和 4（分区 2 的子分区）分别有 10,000 RU/秒用于处理 20 GB 数据的请求，而分区 1 具有 10,000 RU/秒用于处理两倍数据量 (40 GB) 的请求。

:::image type="content" source="media/scaling-provisioned-throughput-best-practices/diagram-2-uneven-partition-split.png" alt-text="拆分后，有 3 个 PartitionKeyRangeId，每个具有 10,000 RU/秒。但是，其中一个 PartitionKeyRangeId 具有总键空间的 50% (40 GB)，而其中两个 PartitionKeyRangeId 具有总键空间的 25% (20 GB)":::

为了保持均匀的存储分布，我们首先可以纵向扩展 RU/秒，以确保每个分区会拆分。 然后，我们可以将 RU/秒降低至所需状态。

因此，如果我们从两个物理分区开始，为了保证拆分后的分区是均匀的，需要设置 RU/秒，以便最终得到四个物理分区。 为此，我们首先将每个分区的 RU/秒设置为 4 * 10,000 RU/秒，即 40,000 RU/秒。 然后，拆分完成后，我们可以将 RU/秒降低至 30,000 RU/秒。 

因此，如下图所示，每个物理分区获得 30,000 RU/秒/4 = 7500 RU/秒，用于处理 20 GB 数据的请求。 总的来说，我们在分区之间保持均匀的存储和请求分布。 

:::image type="content" source="media/scaling-provisioned-throughput-best-practices/diagram-3-even-partition-split.png" alt-text="拆分完成且 RU/秒从 40,000 RU/秒降低至 30,000 RU/秒后，有 4 个 PartitionKeyRangeId，每个 PartitionKeyRangeId 具有 7500 RU/秒和 25% (20 GB) 的总键空间":::

### <a name="general-formula"></a>常规公式

#### <a name="step-1-increase-your-rus-to-guarantee-that-all-partitions-split-evenly"></a>步骤 1：增加 RU/秒，以确保所有分区均匀拆分

一般情况下，如果你的物理分区的起始数目为 `P`，并且你想要设置所需的 RU/秒 `S`：

将 RU/秒增加到：`10,000 * P * 2 ^ (ROUNDUP(LOG_2 (S/(10,000 * P)))`。 这会为所需的值提供最接近的 RU/秒，以确保所有分区均匀拆分。 

> [!NOTE]
> 当你增加数据库或容器的 RU/秒时，这可能会影响将来降低到的目标最小 RU/秒。 通常，最小 RU/秒等于 MAX(400 RU/秒，以 GB * 10 RU/秒为单位的当前存储，曾经预配的最高 RU/秒/100)。 例如，如果曾经缩放到的最高 RU/秒为 100,000 RU/秒，则将来可以设置的最低 RU/秒为 1000 RU/秒。 详细了解[最小 RU/秒](concepts-limits.md#minimum-throughput-limits)。

#### <a name="step-2-lower-your-rus-to-the-desired-rus"></a>步骤 2：将 RU/秒降低至所需的 RU/秒
 
例如，假设我们有五个物理分区，即 50,000 RU/秒，并且需要缩放到 150,000 RU/秒。 首先应设置 `10,000 * 5 * 2 ^ (ROUND(LOG_2(150,000/(10,000 * 5)))` = 200,000 RU/秒，然后降低至 150,000 RU/秒。 

现在，当我们纵向扩展到 200,000 RU/秒时，我们可以在将来设置的最低手动 RU/秒为 2000 RU/秒。 我们可以设置的[最低自动缩放最大 RU/秒](autoscale-faq.yml#lowering-the-max-ru-s)为 20,000 RU/秒（缩放范围为 2000 - 20,000 RU/秒）。 由于我们的目标 RU/秒为 150,000 RU/秒，因此不受最小 RU/秒的影响。

## <a name="how-to-optimize-rus-for-large-data-ingestion"></a>如何优化大型数据引入的 RU/秒
 
当你计划将大量数据迁移或引入到 Azure Cosmos DB 时，建议设置容器的 RU/秒，以便 Azure Cosmos DB 预先预配存储你计划提前引入的总数据量所需的物理分区。 否则，在引入期间，Azure Cosmos DB 可能需要拆分分区，这会增加数据引入的时间。 
 
我们可以利用这样一个事实，即在容器创建期间，Azure Cosmos DB 使用起始 RU/秒的启发式公式来计算要开始统计的物理分区数。 
 
### <a name="step-1-review-the-choice-of-partition-key"></a>步骤 1：查看分区键的选择
遵循选择分区键的[最佳做法](partitioning-overview.md#choose-partitionkey)，确保在迁移后均匀分布请求卷和存储。 
 
### <a name="step-2-calculate-the-number-of-physical-partitions-youll-need"></a>步骤 2：计算所需的物理分区数 
`Number of physical partitions = Total data size in GB / Target data per physical partition in GB`

每个物理分区最多可以容纳 50 GB 的存储（对于 Cassandra API 为 30 GB）。 应为 `Target data per physical partition in GB` 选择的值取决于物理分区的填充程度，以及迁移后存储预期增长的量。 

例如，如果预计存储量将继续增加，可以选择将值设置为 30 GB。 假设你选择了一个均匀分布存储的良好分区键，则每个分区将填充大约 60% (30 GB/50 GB)。 在将来写入数据时，可以将其存储在现有的物理分区集上，而无需让服务立即添加更多的物理分区。

相反，如果你认为存储在迁移后不会显著增加，可以选择将值设置为更高的值，例如 45 GB。 这意味着每个分区将填充大约 90% (45 GB/50 GB)。 这会最大程度地减少数据所分布的物理分区的数量，意味着每个物理分区可以获得预配的总 RU/秒中更大的一部分。 
 
### <a name="step-3-calculate-the-number-of-rus-to-start-with"></a>步骤 3：计算要开始统计的 RU/秒数
`Starting RU/s = Number of physical partitions * Initial throughput per physical partition`.
- 使用自动缩放或共享吞吐量数据库时 `Initial throughput per physical partition` = 10,000 RU/秒
- 使用手动吞吐量时 `Initial throughput per physical partition` = 6000 RU/秒 
 
### <a name="example"></a>示例
假设我们计划引入 1 TB (1000 GB) 的数据，并且我们需要使用手动吞吐量。 Azure Cosmos DB 中每个物理分区的容量为 50 GB。 假设我们的目标是将分区填充 80% (40 GB)，为将来的增长留出空间。 
 
这意味着对于 1 TB 的数据，我们需要 1000 GB / 40 GB = 25 个物理分区。 为了确保获得 25 个物理分区，如果我们使用手动吞吐量，我们首先预配 25 * 6000 RU/秒 = 150,000 RU/秒。 然后，在创建容器后，为了帮助我们更快地进行数据引入，我们将 RU/秒增加到 250,000 RU/秒，然后开始数据引入（立即发生，因为我们已经有 25 个物理分区）。 这样，每个分区最多可以获得 10,000 RU/秒。 

如果我们使用自动缩放吞吐量或共享吞吐量数据库，要获得 25 个物理分区，我们首先预配 25 * 10,000 RU/秒 = 250,000 RU/秒。 由于我们已达到 25 个物理分区所能支持的最高 RU/秒，因此我们不会在进行数据引入之前进一步增加我们的预配 RU/秒。
 
理论上，对于 250,000 RU/秒和 1 TB 的数据，如果我们假设需要 1-kb 文档和 10 RU 用于写入，理论上可以在以下时间内完成数据引入：1000 GB * (1,000,000 kb / 1 GB) * (1 个文档 / 1 kb) * (10 RU/文档) * (1 秒 / 150,000 RU) * (1 小时 / 3600 秒) = 11.1 小时。 

这个计算得到是一个估计值，假设执行数据引入的客户端可以使吞吐量完全饱和，并将写入分布到所有物理分区。 作为最佳做法，建议在客户端“混排”数据。 这可以确保在每一秒，客户端都在写入许多不同的逻辑（从而写入物理）分区。 
 
迁移完成后，我们可以降低 RU/秒，或者根据需要启用自动缩放。

## <a name="next-steps"></a>后续步骤
* 监视数据库或容器的[规范化 RU/s 消耗量](monitor-normalized-request-units.md)。
* 对请求速率过大 (429) 异常进行[诊断及故障排除](troubleshoot-request-rate-too-large.md)。
* [在数据库或容器上启用自动缩放](provision-throughput-autoscale.md)。