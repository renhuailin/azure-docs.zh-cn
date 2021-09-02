---
title: Azure Cosmos DB 中的高可用性
description: 本文介绍 Azure Cosmos DB 如何提供高可用性
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/07/2021
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: ec09ac444999be23fa0caed741e1e1534117fa0c
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123105613"
---
# <a name="how-does-azure-cosmos-db-provide-high-availability"></a>Azure Cosmos DB 如何提供高可用性？
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB 主要通过两种方式提供高可用性。 第一种方式是 Azure Cosmos DB 跨 Cosmos 帐户中配置的区域复制数据。 第二种方式是 Azure Cosmos DB 在一个区域内维护 4 个数据副本。

Azure Cosmos DB 是一个全球分布式数据库服务，也是在[可以使用 Azure 的所有区域](https://azure.microsoft.com/global-infrastructure/services/?products=cosmos-db&regions=all)中提供的一项基础服务。 可将任意数量的 Azure 区域与 Azure Cosmos 帐户相关联，并且数据会自动且透明地得到复制。 可随时向 Azure Cosmos 帐户添加或从中删除区域。 Cosmos DB 在提供给客户的所有五种不同的 Azure 云环境中均可使用：

* Azure 公有云，全球通用。

* Microsoft 与是中国最大的 Internet 提供商之一的世纪互联展开独特合作，推出了 Azure 中国世纪互联。

* **Azure 德国** 以数据受托人模式提供服务，这可确保客户数据仍保留在德国，由 Deutsche Telekom 的子公司 T-Systems International GmbH 充当德国数据受托人，对这些数据进行管控。

* Azure 政府在美国的四个区域向美国政府机构及其合作伙伴提供服务。

* **面向国防部 (DoD) 的 Azure 政府** 在美国的两个区域向美国国防部提供服务。

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
|写入   | 99.99   |99.99   |99.999|
|读取    | 99.99   |99.999  |99.999|

> [!NOTE]
> 在实践中，有限过期、会话、一致前缀和最终一致性模型的实际写入可用性明显高于发布的 SLA。 所有一致性级别的实际读取可用性明显高于发布的 SLA。

## <a name="high-availability-with-azure-cosmos-db-in-the-event-of-regional-outages"></a>使用 Azure Cosmos DB 在发生区域性服务中断时提供高可用性

对于区域性服务中断的罕见情况，Azure Cosmos DB 可确保你的数据库始终保持高可用性。 下面根据 Azure Cosmos 帐户配置详细介绍 Azure Cosmos DB 在服务中断期间的行为：

* 使用 Azure Cosmos DB 时，在客户端确认写入操作之前，数据将由接受写入操作的区域中的副本仲裁持续提交。 有关更多详细信息，请参阅[一致性级别和吞吐量](./consistency-levels.md#consistency-levels-and-throughput)

* 配置有多个写入区域的多区域帐户对于写入和读取都将具有高可用性。 区域故障转移在 Azure Cosmos DB 客户端中进行检测和处理。 它们也可在瞬间完成，不需要在应用程序中进行任何更改。

* 发生区域性服务中断时，单区域帐户可能会失去可用性。 始终建议对 Azure Cosmos 帐户至少设置两个区域（最好至少设置两个写入区域），以确保始终保持高可用性。

> [!IMPORTANT]
> 使用 SQL API 时，请务必将 Cosmos DB SDK 配置为使用所有指定的读取区域，以利用更高的可用性。 有关详细信息，请参阅[此文](troubleshoot-sdk-availability.md)。

### <a name="multi-region-accounts-with-a-single-write-region-write-region-outage"></a>配置为使用单个写入区域的多区域帐户（写入区域服务中断）

* 在写入区域服务中断期间，如果在 Azure Cosmos 帐户上配置了“启用自动故障转移”，则 Azure Cosmos 帐户会自动将次要区域提升为新的主要写入区域。 当启用后，将按您指定的区域优先级顺序故障转移到其他区域。

* 请注意，手动故障转移不应触发，并且在源或目标区域出现故障时，不会成功。 这是因为故障转移过程需要进行一致性检查，而这需要在区域之间建立连接。

* 当上一个受影响的区域重新联机时，可以通过[冲突源](how-to-manage-conflicts.md#read-from-conflict-feed)使用该区域发生故障时未复制的任何写入数据。 应用程序可以读取冲突源，根据应用程序特定的逻辑解决冲突，并相应地将更新后的数据写回 Azure Cosmos 容器。

* 以前受影响的写入区域恢复后，它将自动用作读取区域。 可以切换回到用作写入区域的已恢复区域。 可以使用 [PowerShell、Azure CLI 或 Azure 门户](how-to-manage-database-account.md#manual-failover)来切换区域。 在切换写入区域之前、期间或之后，**不会丢失数据或可用性**，应用程序将继续保持高可用性。

> [!IMPORTANT]
> 强烈建议将用于生产工作负载的 Azure Cosmos 帐户配置为“启用自动故障转移”  。 这样，Cosmos DB 将能够自动将帐户数据库故障转移到可用区域。 如果不存在此配置，则帐户在写入区域中断的整个持续时间内会丢失写入可用性，因为手动故障转移会由于缺少区域连接而无法成功。

### <a name="multi-region-accounts-with-a-single-write-region-read-region-outage"></a>配置为使用单个写入区域的多区域帐户（读取区域服务中断）

* 在读取区域服务中断期间，使用任何一致性级别或强一致性且具有三个或更多读取区域的 Azure Cosmos 帐户仍将对读取和写入保持高可用性。

* 使用强一致性且具有三个区域（一个写入区域，两个读取区域）的 Azure Cosmos 帐户将在读取区域服务中断期间维持写入可用性。 对于具有两个区域且启用了自动故障转移的帐户，该帐户将停止接受写入，直到将该区域标记为失败并发生自动故障转移。

* 受影响的区域将自动断开连接，并标记为脱机。 [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) 会将读取调用重定向到首选区域列表中的下一个可用区域。

* 如果首选区域列表中没有区域可用，则会自动让调用返回到当前的写入区域。

* 处理读取区域服务中断不需要对应用程序代码进行更改。 当受影响的读取区域重新联机时，它会自动与当前写入区域同步，并再次可用于为读取请求提供服务。

* 后续的读取会重定向到恢复的区域，不需更改应用程序代码。 在故障转移和重新加入之前发生故障的区域期间，Azure Cosmos DB 会继续提供读取一致性保证。

* 即使在发生了 Azure 区域永久无法恢复的罕见不幸事件中，如果为多区域 Azure Cosmos 帐户配置了强一致性，也不会丢失数据。 如果出现永久不可恢复的写入区域，对于配置了有限过期一致性的多区域 Azure Cosmos 帐户，潜在的数据丢失时段限制为过期时段（K 或 T），其中 K = 100,000 次更新或 T = 5 分钟（以先发生者为准）。 对于会话、一致前缀和最终一致性级别，潜在的数据丢失时段限制为最多 15 分钟。 有关 Azure Cosmos DB 的 RTO 和 RPO 目标的详细信息，请参阅[一致性级别和数据持续性](./consistency-levels.md#rto)

## <a name="availability-zone-support"></a>可用性区域支持

除了跨区域复原以外，在选择要与 Azure Cosmos 帐户关联的区域时，Azure Cosmos DB 还支持所支持区域中的 **区域冗余**。

有了可用性区域 (AZ) 支持，Azure Cosmos DB 可确保将副本放置在给定区域内的多个区域中，以提供高可用性并提高对区域性故障的复原能力。 可用性区域提供 99.995% 的可用性 SLA，同时延迟无更改。 如果发生单一区域故障，区域冗余将提供 RPO = 0 的完全数据持续性以及 RTO = 0 的可用性。 区域冗余是区域复制的补充功能。 只有区域冗余无法实现区域复原。

仅当向 Azure Cosmos 帐户添加新区域时，才能配置区域冗余。 现有区域若要启用区域冗余，可以先删除该区域，重新添加时再启用区域冗余。 对于单个区域帐户，这需要额外添加一个区域以作为暂时的故障转移目标，然后删除所需区域后重新添加，届时启用区域冗余。

为 Azure Cosmos 帐户配置多区域写入时，可以选择无需另付费用的区域冗余。 如若不然，请参阅下表了解有关区域冗余支持的定价。 有关可用性区域可用的区域列表，请参阅[可用性区域](../availability-zones/az-region.md)。

下表总结了各种帐户配置的高可用性功能：

|KPI|没有 AZ 的单区域|有 AZ 的单区域|没有 AZ 的多区域、单区域写入|有 AZ 的多区域、单区域写入|有或没有 AZ 的多区域、多区域写入|
|---------|---------|---------|---------|---------|---------|
|写入可用性 SLA | 99.99% | 99.995% | 99.99% | 99.995% | 99.999% |
|读取可用性 SLA  | 99.99% | 99.995% | 99.999% | 99.999% | 99.999% |
|区域故障 - 数据丢失 | 数据丢失 | 无数据丢失 | 无数据丢失 | 无数据丢失 | 无数据丢失 |
|区域故障 - 可用性 | 可用性缺失 | 无可用性缺失 | 无可用性缺失 | 无可用性缺失 | 无可用性缺失 |
|区域性服务中断 - 数据丢失 | 数据丢失 |  数据丢失 | 取决于一致性级别。 有关详细信息，请参阅[一致性、可用性和性能权衡](./consistency-levels.md)。 | 取决于一致性级别。 有关详细信息，请参阅[一致性、可用性和性能权衡](./consistency-levels.md)。 | 取决于一致性级别。 有关详细信息，请参阅[一致性、可用性和性能权衡](./consistency-levels.md)。
|区域性服务中断 - 可用性 | 可用性缺失 | 可用性缺失 | 读取区域故障没有可用性缺失，写入区域故障有暂时缺失 | 读取区域故障没有可用性缺失，写入区域故障有暂时缺失 | 无可用性缺失 |
|价格 (***1** _) | 空值 | 预配 RU/秒 x 1.25 速率 | 预配 RU/秒 x n 个区域 | 预配 RU/秒 x 1.25 速率 x 个区域 (2)_*** | 多区域写入速率 x n 个区域 |

***1*** 对于无服务器帐户，请求单位 (RU) 乘以系数 1.25。

***2*** 1.25 速率仅适用于已启用 AZ 的区域。

可以通过以下方式启用可用性区域：

* [Azure 门户](how-to-manage-database-account.md#addremove-regions-from-your-database-account)

* [Azure PowerShell](manage-with-powershell.md#create-account)

* [Azure CLI](sql/manage-with-cli.md#add-or-remove-regions)

* [Azure Resource Manager 模板](./manage-with-templates.md)

## <a name="building-highly-available-applications"></a>生成高可用性应用程序

* 了解发生这些事件期间的 [Azure Cosmos SDK 预期行为](troubleshoot-sdk-availability.md)以及会影响它的具体配置。

* 若要确保较高的写入和读取可用性，请将 Azure Cosmos 帐户配置为跨越至少两个区域并使用多个写入区域。 对于读取和写入，此配置都可提供由 SLA 作为保障的最高可用性、最低延迟和最佳可伸缩性。 若要了解详细信息，请参阅如何[将 Azure Cosmos 帐户配置为使用多个写入区域](tutorial-global-distribution-sql-api.md)。

* 对于配置为使用单个写入区域的多区域 Azure Cosmos 帐户，请[使用 Azure CLI 或 Azure 门户启用自动故障转移](how-to-manage-database-account.md#automatic-failover)。 启用自动故障转移后，每当发生区域性灾难时，Cosmos DB 都会自动故障转移你的帐户。  

* 即使 Azure Cosmos 帐户具有高可用性，应用程序也不一定能够正常保持高可用性。 若要测试应用程序的端到端高可用性，请在应用程序测试或灾难恢复 (DR) 演练过程中，暂时禁用帐户的自动故障转移功能，[使用 PowerShell、Azure CLI 或 Azure 门户调用手动故障转移](how-to-manage-database-account.md#manual-failover)，然后监视应用程序的故障转移。 完成后，可以故障回复到主区域，然后还原该帐户的自动故障转移。

> [!IMPORTANT]
> 在 Cosmos DB 服务中断期间，请勿在源区域或目标区域上调用手动故障转移，因为这需要区域连接才能维持数据一致性，且不会成功。

* 在全球分布式数据库环境中，当发生区域范围的服务中断时，一致性级别与数据持续性之间存在直接关系。 制定业务连续性计划时，需了解应用程序在中断事件发生后完全恢复之前的最大可接受时间。 应用程序完全恢复所需的时间称为恢复时间目标 (RTO)。 此外，还需要了解从中断事件恢复时，应用程序可忍受最近数据更新丢失的最长期限。 可以承受更新丢失的时限称为恢复点目标 (RPO)。 若要查看 Azure Cosmos DB 的 RPO 和 RTO，请参阅[一致性级别和数据持续性](./consistency-levels.md#rto)

## <a name="what-to-expect-during-a-cosmos-db-region-outage"></a>Cosmos DB 区域中断期间可能发生的情况

对于单区域帐户，客户端将出现读取和写入可用性缺失。

多区域帐户的情况会有所不同，具体取决于下表各条件。

| 写入区域 | 自动故障转移 | 期望 | 要执行的操作 |
| -- | -- | -- | -- |
| 单个写入区域 | 未启用 | 当读取区域发生服务中断时，所有客户端都将重定向到其他区域。 不会发生读取或写入可用性缺失。 无数据丢失。 <p/> 当写入区域发生服务中断时，客户端将存在写入可用性缺失。 如果未选择强一致性级别，则表示某些数据可能尚未复制到剩余的活动区域。 这取决于所选的一致性级别，正如[本节](consistency-levels.md#rto)所述。 如果受影响的区域发生永久性数据丢失，未复制的数据可能会丢失。 <p/> 当服务中断结束时，Cosmos DB 将自动恢复写入可用性。 | 在中断过程中，请确保剩余区域中预配了足够的 RU 以支持读取流量。 <p/> 服务中断期间不会触发手动故障转移，因为不会成功。 <p/> 当中断结束时，请根据需要重新调整预配的 RU。 |
| 单个写入区域 | 已启用 | 当读取区域发生服务中断时，所有客户端都将重定向到其他区域。 不会发生读取或写入可用性缺失。 无数据丢失。 <p/> 在写入区域发生服务中断时，客户端将出现写入可用性缺失，直至 Cosmos DB 根据偏好自动选择新的区域作为新的写入区域。 如果未选择强一致性级别，则表示某些数据可能尚未复制到剩余的活动区域。 这取决于所选的一致性级别，正如[本节](consistency-levels.md#rto)所述。 如果受影响的区域发生永久性数据丢失，未复制的数据可能会丢失。 | 在中断过程中，请确保剩余区域中预配了足够的 RU 以支持读取流量。 <p/> 服务中断期间不会触发手动故障转移，因为不会成功。 <p/> 当中断结束时，可以将写入区域移回原始区域，并根据需要重新调整预配的 RU。 使用 SQL API 的帐户还可从[冲突源](how-to-manage-conflicts.md#read-from-conflict-feed)恢复失败区域中的未复制数据。 |
| 多个写入区域 | 不适用 | 不会发生读取或写入可用性缺失。 <p/> 失败区域中最新更新的数据可能在其余的活跃区域中不可用。 最终的一致性前缀和会话一致性级别可保证过期时间少于 15 分钟。 根据配置的不同，有限过期可保证更新次数低于 K 或时间少于 T 秒。 如果受影响的区域发生永久性数据丢失，未复制的数据可能会丢失。 | 在中断过程中，请确保剩余区域中预配了足够的 RU 以支持额外流量。 <p/> 当中断结束时，可以根据需要重新调整预配的 RU。 如果可能，Cosmos DB 将使用为 SQL API 帐户配置的冲突解决方法以及针对其他 API 帐户的 Last Write Wins 策略，自动恢复失败区域中的未复制数据。 |

## <a name="next-steps"></a>后续步骤

接下来可以阅读以下文章：

* [各种一致性级别的可用性和性能权衡](./consistency-levels.md)

* [全局缩放预配的吞吐量](./request-units.md)

* [全球分布 - 揭秘](global-dist-under-the-hood.md)

* [Azure Cosmos DB 中的一致性级别](consistency-levels.md)

* [如何将 Cosmos 帐户配置为使用多个写入区域](how-to-multi-master.md)

* [多区域环境中的 SDK 行为](troubleshoot-sdk-availability.md)
