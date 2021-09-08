---
title: Azure Cosmos DB 中的分区和水平缩放
description: 了解 Azure Cosmos DB 中的分区、逻辑分区、物理分区、选择分区键时的最佳做法，以及如何管理逻辑分区
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: 43f722bf102566cf737e43732bf1ab3c39fdecc1
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123032568"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Azure Cosmos DB 中的分区和水平缩放
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB 使用分区缩放数据库中的单个容器，以满足应用程序的性能需求。 在分区中，可将容器中的项分割成不同的子集（称作“逻辑分区”）。 逻辑分区是根据与容器中每个项关联的分区键值形成的。 逻辑分区中的所有项都具有相同的分区键值。

例如，某个容器保存项。 每个项具有唯一的 `UserID` 属性值。 如果 `UserID` 充当容器中的项的分区键，并且有 1,000 个唯一的 `UserID` 值，则会为容器创建 1,000 个逻辑分区。

除了用于确定项的逻辑分区的分区键以外，容器中的每个项还有一个项 ID（在逻辑分区中保持唯一）。 将分区键与项 ID 相结合可创建项的索引，用来唯一地标识该项。  [分区键的选择](#choose-partitionkey)非常重要，这会影响应用程序的性能。

本文介绍了逻辑分区与物理分区之间的关系。 还讨论了用于分区的最佳做法，并且深入介绍了横向缩放在 Azure Cosmos DB 中的工作方式。 并非一定要了解这些内部详细信息才能选择分区键，但我们还是介绍了这些内容，以便你清晰地了解 Azure Cosmos DB 的工作方式。

## <a name="logical-partitions"></a>逻辑分区

逻辑分区由一组具有相同分区键的项构成。 例如，在包含食物营养相关数据的容器中，所有项都包含 `foodGroup` 属性。 可以使用 `foodGroup` 作为该容器的分区键。 具有特定 `foodGroup` 值（例如 `Beef Products`、`Baked Products` 和 `Sausages and Luncheon Meats`）的项组构成了独立的逻辑分区。

逻辑分区也定义数据库事务的范围。 可以使用[支持快照隔离的事务](database-transactions-optimistic-concurrency.md)来更新逻辑分区中的项。 当向容器中添加新项时，系统将透明地创建新的逻辑分区。 无需担心在删除基础数据时是否会删除逻辑分区。

容器中逻辑分区的数量是没有限制的。 每个逻辑分区最多可以存储 20GB 数据。 如果分区键的可能值范围广泛，那么这些分区键是良好的分区键选择。 例如，在一个其中所有项都包含 `foodGroup` 属性的容器中，`Beef Products` 逻辑分区内的数据最多可以增长到 20 GB。 [选择具有多种可能值的分区键](#choose-partitionkey)会确保容器能够缩放。

## <a name="physical-partitions"></a>物理分区

容器是通过在物理分区之间分配数据和吞吐量来进行缩放的。 在内部，一个或多个逻辑分区映射到一个物理分区。 通常，较小的容器会有许多逻辑分区，但这些容器只需要一个物理分区。 与逻辑分区不同，物理分区是系统的内部实现，并且全部由 Azure Cosmos DB 管理。

容器中的物理分区数依赖于以下各项：

* 预配的吞吐量（每个单独的物理分区最多可以提供每秒 10,000 个请求单位的吞吐量） 由于每个逻辑分区仅会映射到一个物理分区，若物理分区受到 10,000 RU/秒的限制，则意味着逻辑分区也会受 10,000 RU/秒的限制。

* 总数据存储量（每个单独的物理分区最多可以存储 50GB 数据）。

> [!NOTE]
> 物理分区是系统的内部实现，并且全部由 Azure Cosmos DB 管理。 开发解决方案时，请不要将重点放在物理分区上，因为你无法对其进行控制。 而应重点关注分区键。 如果选择在逻辑分区间平均分配吞吐量消耗的分区键，将会确保物理分区间的吞吐量消耗保持均衡。

容器中物理分区的总数是没有限制的。 随着预配的吞吐量或数据量规模的增长，Azure Cosmos DB 将会通过拆分现有物理分区来自动创建新物理分区。 物理分区拆分不影响应用程序可用性。 物理分区拆分后，单个逻辑分区内的所有数据仍将存储在同一个物理分区中。 物理分区拆分只是创建逻辑分区到物理分区的新映射。

为容器预配的吞吐量在物理分区之间均匀划分。 未均匀分配请求的分区键设计可能会导致过多的请求定向到变“热”的一小组分区。 热分区会导致预配吞吐量的使用效率低下，进而可能会导致速率受限和成本上升。

在 Azure 门户的“指标”边栏选项卡的“存储”部分中，可以看到容器的物理分区 ：

:::image type="content" source="./media/partitioning-overview/view-partitions-zoomed-out.png" alt-text="查看物理分区数" lightbox="./media/partitioning-overview/view-partitions-zoomed-in.png" ::: 

在上面的屏幕截图中，容器具有 `/foodGroup` 作为分区键。 图中三个条形中的每一个都表示一个物理分区。 在此图中，分区键范围与物理分区相同。 选定的物理分区包含前 3 个最重要的逻辑分区：`Beef Products`、`Vegetable and Vegetable Products` 和 `Soups, Sauces, and Gravies`。

如果预配每秒 18,000 个请求单位 (RU/s) 的吞吐量，则三个物理分区中的每一个都可以利用总预配吞吐量的 1/3。 在选定的物理分区中，逻辑分区键 `Beef Products`、`Vegetable and Vegetable Products` 和 `Soups, Sauces, and Gravies` 可以共同利用为物理分区预配的每秒 6,000 个 RU。 由于预配的吞吐量是在容器的物理分区间平均分配的，因此，请务必通过[选择正确的逻辑分区键](#choose-partitionkey)来选择平均分配吞吐量消耗的分区键。 

## <a name="managing-logical-partitions"></a>管理逻辑分区

Azure Cosmos DB 以透明方式自动管理逻辑分区在物理分区上的位置，以有效满足容器的可伸缩性和性能需求。 随着应用程序的吞吐量和存储要求的提高，Azure Cosmos DB 会移动逻辑分区，以便自动在更多的物理分区之间分散负载。 可以详细了解[物理分区](partitioning-overview.md#physical-partitions)。

Azure Cosmos DB 使用基于哈希的分区在物理分区之间分散逻辑分区。 Azure Cosmos DB 对项的分区键值进行哈希处理。 哈希处理结果确定了物理分区。 然后，Azure Cosmos DB 在物理分区之间均匀分配分区键哈希的键空间。

只允许针对单个逻辑分区中的项执行事务（在存储过程或触发器中）。

## <a name="replica-sets"></a>副本集

每个物理分区都包含一组副本（也称为[副本集](global-dist-under-the-hood.md)）。 每个副本集都托管数据库引擎的一个实例。 副本集使物理分区中存储的数据具有持久性、高可用性和一致性。 构成物理分区的每个副本均继承该分区的存储配额。 物理分区的所有副本共同支持分配给物理分区的吞吐量。 Azure Cosmos DB 自动管理副本集。

通常，较小的容器只需要一个物理分区，但这些容器仍将至少具有 4 个副本。

下图显示了逻辑分区如何映射到全局分布的物理分区。 映像中的[分区集](global-dist-under-the-hood.md#partition-sets)是指一组物理分区，这些物理分区跨多个区域管理相同的逻辑分区键：

:::image type="content" source="./media/partitioning-overview/logical-partitions.png" alt-text="演示 Azure Cosmos DB 分区的插图" border="false":::

## <a name="choosing-a-partition-key"></a><a id="choose-partitionkey"></a>选择分区键

 分区键具有两个组成部分：分区键路径和分区键值。 假设有一个项{ "userId" :"Andrew", "worksFor":"Microsoft" }，如果选择 "userId" 作为分区键，以下是分区键的两个部分：

* 分区键路径（例如 "/userId"）。 分区键路径接受字母数字和下划线 (_) 字符。 还可以通过标准路径表示法 (/) 来使用嵌套的对象。

* 分区键值（例如 "Andrew"）。 分区键值可以是字符串或数值类型。

若要了解有关吞吐量、存储和分区键长度的限制，请参阅 [Azure Cosmos DB 服务配额](concepts-limits.md)一文。

选择分区键是 Azure Cosmos DB 中的一个简单但重要的设计选择。 选择分区键后，将无法就地进行更改。 如果需要更改分区键，应将数据移动到带有所需新分区键的新容器。

对于 **所有** 容器，分区键应当：

* 是一个属性，并且其值不会更改。 如果某个属性是分区键，那么你不能更新该属性的值。

* 具有较高的基数。 换言之，该属性应具有范围广泛的可能值。

* 将请求单位 (RU) 消耗和数据存储均匀分配到所有逻辑分区上。 这可确保跨物理分区均匀分配 RU 消耗和存储。

如果在 Azure Cosmos DB 中需要[多项 ACID 事务](database-transactions-optimistic-concurrency.md#multi-item-transactions)，则需要使用[存储过程或触发器](how-to-write-stored-procedures-triggers-udfs.md#stored-procedures)。 所有基于 JavaScript 的存储过程和触发器的作用域都是单个逻辑分区。

## <a name="partition-keys-for-read-heavy-containers"></a>读取密集型容器的分区键

对于大多数容器，上述条件就是在选择分区键时需要考虑的全部。 但对于较大的读取密集型容器，可能需要选择在查询中经常作为筛选器出现的分区键。 通过在筛选器谓词中包含分区键，查询可以[高效地专门路由到相关的物理分区](how-to-query-container.md#in-partition-query)。

如果大多数工作负荷请求是查询，并且大多数查询在同一属性上都有一个等式筛选器，则此属性可以成为不错的分区键选择。 例如，如果经常运行在 `UserID` 上筛选的查询，则选择 `UserID` 作为分区键将减少[跨分区查询](how-to-query-container.md#avoiding-cross-partition-queries)的数目。

但是，如果容器很小，那么你的物理分区数量可能并非很多，无需你担心跨分区查询的性能影响。 Azure Cosmos DB 中的大多数小容器只需要一个或两个物理分区。

如果容器可能会增长到许多个物理分区，则应确保选择一个可以最大程度地减少跨分区查询的分区键。 如果满足以下任一条件，则容器将需要许多个物理分区：

* 容器已预配了 30000 以上的 RU

* 容器将存储超过 100 GB 的数据

## <a name="using-item-id-as-the-partition-key"></a>使用项 ID 作为分区键

如果容器具有一个属性，并且该属性的可能值范围十分广泛，则该属性很可能是非常好的分区键选择。 此类属性的一个可能示例是项 ID。 对于较小的读取密集型容器或任意大小的写入密集型容器，项 ID 自然是很好的分区键选择。

系统属性“项 ID”存在于容器中的每一项内。 可能会有其他用于表示项逻辑 ID 的属性。 在许多情况下，出于与项 ID 相同的原因，这些属性也会是非常好的分区键选择。

项 ID 是很好的分区键选择，原因如下：

* 其可能值范围十分广泛（每个项一个唯一的项 ID）。
* 由于每个项都有一个唯一的项 ID，因此，项 ID 在均衡 RU 消耗和数据存储方面有显著作用。
* 你可以轻松执行高效的点读取，因为如果你知道项的项 ID，你将始终知道项的分区键。

选择项 ID 作为分区键时要考虑的一些事项包括：

* 如果项 ID 为分区键，则它会成为整个容器中的唯一标识符。 不同的项不能具有相同的项 ID。
* 如果一个读取密集型容器有大量[物理分区](partitioning-overview.md#physical-partitions)，则当查询具有一个包含项 ID 的等式筛选器时，查询将更高效。
* 不能跨多个逻辑分区运行存储过程或触发器。

## <a name="next-steps"></a>后续步骤

* 了解 [Azure Cosmos DB 中的预配吞吐量](request-units.md)。
* 了解 [Azure Cosmos DB 中的全局分布](distribute-data-globally.md)。
* 了解[如何对 Azure Cosmos 容器预配吞吐量](how-to-provision-container-throughput.md)。
* 了解[如何对 Azure Cosmos 数据库预配吞吐量](how-to-provision-database-throughput.md)。
* 请参阅 Learn 模块，了解如何[在 Azure Cosmos DB 中为数据建模和分区](/learn/modules/model-partition-data-azure-cosmos-db/)。
* 尝试为迁移到 Azure Cosmos DB 进行容量计划？ 可以使用有关现有数据库群集的信息进行容量规划。
    * 如果你只知道现有数据库群集中的 vCore 和服务器数量，请阅读[使用 vCore 或 vCPU 估算请求单位](convert-vcore-to-request-unit.md) 
    * 如果知道当前数据库工作负荷的典型请求速率，请阅读[使用 Azure Cosmos DB 容量计划工具估算请求单位](estimate-ru-with-capacity-planner.md)
