---
title: Azure Cosmos DB 中的高可用性
description: 本文介绍 Azure Cosmos DB 如何提供高可用性
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: f22d97f8a4ab5e5b6e275c405cce523e8a7b8e72
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101656544"
---
# <a name="how-does-azure-cosmos-db-provide-high-availability"></a>Azure Cosmos DB 如何提供高可用性？
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB 主要通过两种方式提供高可用性。 第一种方式是 Azure Cosmos DB 跨 Cosmos 帐户中配置的区域复制数据。 第二种方式是 Azure Cosmos DB 在一个区域内维护 4 个数据副本。

Azure Cosmos DB 是一种全球分布式数据库服务，是可用 [Azure 的所有区域](https://azure.microsoft.com/global-infrastructure/services/?products=cosmos-db&regions=all)提供的基础服务。 可将任意数量的 Azure 区域与 Azure Cosmos 帐户相关联，并且数据会自动且透明地得到复制。 可随时向 Azure Cosmos 帐户添加或从中删除区域。 Cosmos DB 在提供给客户的所有五种不同的 Azure 云环境中均可使用：

* Azure 公有云，全球通用。

* Microsoft 与是中国最大的 Internet 提供商之一的世纪互联展开独特合作，推出了 Azure 中国世纪互联。

* **Azure 德国** 在数据受信者模型下提供服务，这可确保客户数据保留在德国的 T 系统国际 GmbH （德国 Telekom 的子公司）控制下，作为德国数据受信者。

* Azure 政府在美国的四个区域向美国政府机构及其合作伙伴提供服务。

* 适用于美国国防部的 **Azure 政府部门 (DoD)** 在美国到美国国防部的两个区域中提供。

在一个区域内，Azure Cosmos DB 将数据的四个副本作为副本保留在物理分区中，如下图所示：

:::image type="content" source="./media/high-availability/cosmosdb-data-redundancy.png" alt-text="物理分区" border="false":::

* Azure Cosmos 容器中的数据[已水平分区](partitioning-overview.md)。

* 分区集是多个副本集的集合。 在每个区域中，每个分区受副本集的保护，该副本集中的大多数副本将复制并以持久方式提交所有写入内容。 副本分布在最多 10 到 20 个容错域中。

* 将复制所有区域中的每个分区。 每个区域均包含一个 Azure Cosmos 容器的所有数据分区，并且在启用了多区域写入时可供读取和写入。  

如果 Azure Cosmos 帐户分布在 N 个 Azure 区域之间，则所有数据至少有 N x 4 个副本。 在超过 2 个区域中拥有 Azure Cosmos 帐户可提高应用程序的可用性，相关区域之间的延迟较低。

## <a name="slas-for-availability"></a>可用性 SLA

Azure Cosmos DB 提供综合性的 SLA，涵盖了吞吐量、99% 的情况下的延迟、一致性和高可用性。 下表显示 Azure Cosmos DB 针对单区域和多区域帐户提供的高可用性保证。 为提高写入可用性，请将 Azure Cosmos 帐户配置为使用多个写入区域。

|操作类型  | 单区域 |多区域（单区域写入）|多区域（多区域写入） |
|---------|---------|---------|-------|
|写入    | 99.99    |99.99   |99.999|
|读取     | 99.99    |99.999  |99.999|

> [!NOTE]
> 在实践中，有限过期、会话、一致前缀和最终一致性模型的实际写入可用性明显高于发布的 SLA。 所有一致性级别的实际读取可用性明显高于发布的 SLA。

## <a name="high-availability-with-azure-cosmos-db-in-the-event-of-regional-outages"></a>使用 Azure Cosmos DB 在发生区域性服务中断时提供高可用性

对于区域性服务中断的罕见情况，Azure Cosmos DB 可确保你的数据库始终保持高可用性。 下面根据 Azure Cosmos 帐户配置详细介绍 Azure Cosmos DB 在服务中断期间的行为：

* 使用 Azure Cosmos DB 时，在客户端确认写入操作之前，数据将由接受写入操作的区域中的副本仲裁持续提交。 有关更多详细信息，请参阅[一致性级别和吞吐量](./consistency-levels.md#consistency-levels-and-throughput)

* 配置有多个写入区域的多区域帐户对于写入和读取都将具有高可用性。 区域故障转移在 Azure Cosmos DB 客户端中进行检测和处理。 它们也可在瞬间完成，不需要在应用程序中进行任何更改。

* 发生区域性服务中断时，单区域帐户可能会失去可用性。 始终建议对 Azure Cosmos 帐户至少设置两个区域（最好至少设置两个写入区域），以确保始终保持高可用性。

### <a name="multi-region-accounts-with-a-single-write-region-write-region-outage"></a>配置为使用单个写入区域的多区域帐户（写入区域服务中断）

* 在写入区域服务中断期间，如果在 Azure Cosmos 帐户上配置了“启用自动故障转移”，则 Azure Cosmos 帐户会自动将次要区域提升为新的主要写入区域。 当启用后，将按您指定的区域优先级顺序故障转移到其他区域。

* 当上一个受影响的区域重新联机时，可以通过[冲突源](how-to-manage-conflicts.md#read-from-conflict-feed)使用该区域发生故障时未复制的任何写入数据。 应用程序可以读取冲突源，根据应用程序特定的逻辑解决冲突，并相应地将更新后的数据写回 Azure Cosmos 容器。

* 以前受影响的写入区域恢复后，它将自动用作读取区域。 可以切换回到用作写入区域的已恢复区域。 可以使用 [PowerShell、Azure CLI 或 Azure 门户](how-to-manage-database-account.md#manual-failover)来切换区域。 在切换写入区域之前、期间或之后，**不会丢失数据或可用性**，应用程序将继续保持高可用性。

> [!IMPORTANT]
> 强烈建议将用于生产工作负载的 Azure Cosmos 帐户配置为“启用自动故障转移”  。 手动故障转移要求在辅助写入区域与主要写入区域之间进行连接来完成一致性检查，确保在故障转移期间不会丢失数据。 如果主要区域不可用，则此一致性检查无法完成，手动故障转移不会成功，导致在区域服务中断期间不可写入。

### <a name="multi-region-accounts-with-a-single-write-region-read-region-outage"></a>配置为使用单个写入区域的多区域帐户（读取区域服务中断）

* 在读取区域服务中断期间，使用任何一致性级别或强一致性且具有三个或更多读取区域的 Azure Cosmos 帐户仍将对读取和写入保持高可用性。

* 使用强一致性和三个区域的 Azure Cosmos 帐户 (一次写入，两次读取) 会在读取区域中断期间维持写入可用性。 对于具有两个区域和启用了自动故障转移的帐户，该帐户将停止接受写入，直到将该区域标记为失败并发生自动故障转移。

* 受影响的区域将自动断开连接，并标记为脱机。 [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) 会将读取调用重定向到首选区域列表中的下一个可用区域。

* 如果首选区域列表中没有区域可用，则会自动让调用返回到当前的写入区域。

* 处理读取区域服务中断不需要对应用程序代码进行更改。 当受影响的读取区域重新联机时，它会自动与当前写入区域同步，并再次可用于为读取请求提供服务。

* 后续的读取会重定向到恢复的区域，不需更改应用程序代码。 在故障转移和重新加入之前发生故障的区域期间，Azure Cosmos DB 会继续提供读取一致性保证。

* 即使在发生了 Azure 区域永久无法恢复的罕见不幸事件中，如果为多区域 Azure Cosmos 帐户配置了强一致性，也不会丢失数据。 在永久无法恢复的写入区域（配置有有限过期一致性的多区域 Azure Cosmos 帐户）中，潜在的数据丢失时段限制为过期时段 (*k* 或 t) 其中，k 或 *t* （其中，k = 100000 更新或 t = 5 分钟），这种情况先发生。 对于会话、一致前缀和最终一致性级别，潜在的数据丢失时段限制为最多 15 分钟。 有关 Azure Cosmos DB 的 RTO 和 RPO 目标的详细信息，请参阅[一致性级别和数据持续性](./consistency-levels.md#rto)

## <a name="availability-zone-support"></a>可用性区域支持

除了跨区域复原以外，在选择要与 Azure Cosmos 帐户关联的区域时 Azure Cosmos DB，还支持支持区域中的 **区域冗余** 。

使用可用性区域 (AZ) 支持，Azure Cosmos DB 将确保将副本放置在给定区域内的多个区域，以提供高可用性和对区域性故障的复原能力。 可用性区域提供99.995% 的可用性 SLA，无延迟更改。 如果发生单一区域故障，区域冗余将使用 RPO = 0 提供完整的数据持续性，并使用 RTO = 0 实现可用性。 区域冗余是区域复制的补充功能。 只有区域冗余才能实现区域复原。

仅当向 Azure Cosmos 帐户添加新区域时，才能配置区域冗余。 对于现有区域，可以通过删除区域并在启用区域冗余后将其添加回去来启用区域冗余。 对于单个区域帐户，这需要额外添加一个区域来暂时故障转移到，然后删除并添加启用了区域冗余的所需区域。

为你的 Azure Cosmos 帐户配置多区域写入时，你可以选择不额外收费的区域冗余。 否则，请参阅下表，了解有关区域冗余支持的定价。 有关可用区域的列表，请参阅 [可用性区域](../availability-zones/az-region.md)。

下表总结了各种帐户配置的高可用性功能：

|KPI|无 AZs 的单区域|单区域与 AZs|具有 AZs 的多区域单区域写入|具有 AZs 的多区域多区域写入|
|---------|---------|---------|---------|---------|
|写入可用性 SLA | 99.99% | 99.995% | 99.995% | 99.999% |
|读取可用性 SLA  | 99.99% | 99.995% | 99.995% | 99.999% |
|区域故障–数据丢失 | 数据丢失 | 无数据丢失 | 无数据丢失 | 无数据丢失 |
|区域故障–可用性 | 可用性损失 | 无可用性损失 | 无可用性损失 | 无可用性损失 |
|地区性中断–数据丢失 | 数据丢失 |  数据丢失 | 依赖于一致性级别。 有关详细信息，请参阅 [一致性、可用性和性能折衷](./consistency-levels.md) 。 | 依赖于一致性级别。 有关详细信息，请参阅 [一致性、可用性和性能折衷](./consistency-levels.md) 。
|地区性中断–可用性 | 可用性损失 | 可用性损失 | 读取区域故障没有可用性损失，导致写入区域失败 | 无可用性损失 |
|价格 ( ***1** _)  | 不适用 | 预配 RU/s x 1.25 速率 | 预配 RU/s x 1.25 速率 (_ *_2_* * )  | 多区域写入速率 |

***1*** 对于无服务器帐户，请求单位 (RU) 乘以系数1.25。

***2*** 1.25 rate 仅适用于启用了 AZ 的区域。

可以通过以下方式启用可用性区域：

* [Azure 门户](how-to-manage-database-account.md#addremove-regions-from-your-database-account)

* [Azure PowerShell](manage-with-powershell.md#create-account)

* [Azure CLI](manage-with-cli.md#add-or-remove-regions)

* [Azure Resource Manager 模板](./manage-with-templates.md)

## <a name="building-highly-available-applications"></a>生成高可用性应用程序

* 了解发生这些事件期间的 [Azure Cosmos SDK 预期行为](troubleshoot-sdk-availability.md)以及会影响它的具体配置。

* 若要确保较高的写入和读取可用性，请将 Azure Cosmos 帐户配置为跨越至少两个区域并使用多个写入区域。 对于读取和写入，此配置都可提供由 SLA 作为保障的最高可用性、最低延迟和最佳可伸缩性。 若要了解详细信息，请参阅如何[将 Azure Cosmos 帐户配置为使用多个写入区域](tutorial-global-distribution-sql-api.md)。

* 对于配置为使用单个写入区域的多区域 Azure Cosmos 帐户，请[使用 Azure CLI 或 Azure 门户启用自动故障转移](how-to-manage-database-account.md#automatic-failover)。 启用自动故障转移后，每当发生区域性灾难时，Cosmos DB 都会自动故障转移你的帐户。  

* 即使 Azure Cosmos 帐户具有高可用性，应用程序也不一定能够正常保持高可用性。 若要测试应用程序的端到端高可用性，请在应用程序测试或灾难恢复 (DR) 演练过程中，暂时禁用帐户的自动故障转移功能，[使用 PowerShell、Azure CLI 或 Azure 门户调用手动故障转移](how-to-manage-database-account.md#manual-failover)，然后监视应用程序的故障转移。 完成后，可以故障回复到主区域，然后还原该帐户的自动故障转移。

* 在全球分布式数据库环境中，一致性级别与数据持续性之间的直接关系是在发生区域范围的服务中断。 制定业务连续性计划时，需了解应用程序在中断事件发生后完全恢复之前的最大可接受时间。 应用程序完全恢复所需的时间称为恢复时间目标 (RTO)。 此外，还需要了解从中断事件恢复时，应用程序可忍受最近数据更新丢失的最长期限。 可以承受更新丢失的时限称为恢复点目标 (RPO)。 若要查看 Azure Cosmos DB 的 RPO 和 RTO，请参阅[一致性级别和数据持续性](./consistency-levels.md#rto)

## <a name="next-steps"></a>后续步骤

接下来可以阅读以下文章：

* [各种一致性级别的可用性和性能权衡](./consistency-levels.md)

* [全局缩放预配的吞吐量](./request-units.md)

* [全球分布 - 揭秘](global-dist-under-the-hood.md)

* [Azure Cosmos DB 中的一致性级别](consistency-levels.md)

* [如何将 Cosmos 帐户配置为使用多个写入区域](how-to-multi-master.md)

* [多区域环境中的 SDK 行为](troubleshoot-sdk-availability.md)