---
title: 使用 Azure Cosmos DB 中的时间点还原功能进行连续备份
description: Azure Cosmos DB 的时间点还原功能可帮助用户在执行意外的写入、删除操作后恢复数据，或者将数据还原到任何区域。 了解定价和当前限制。
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/29/2021
ms.author: govindk
ms.reviewer: sngun
ms.custom: references_regions
ms.openlocfilehash: e7d46d1680e11307eb873383e91e6e682f545549
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129546532"
---
# <a name="continuous-backup-with-point-in-time-restore-in-azure-cosmos-db"></a>使用 Azure Cosmos DB 中的时间点还原功能进行连续备份
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Azure Cosmos DB 的时间点还原功能可在多种方案中发挥作用，例如：

* 在容器中执行意外的写入或删除操作后进行恢复。
* 还原已删除的帐户、数据库或容器。
* 还原到任何区域（其中存在备份）中的还原时间点。

Azure Cosmos DB 在后台执行数据备份，不会消耗任何额外的预配吞吐量 (RU)，也不会影响数据库的性能和可用性。 连续备份是在帐户所在的每个区域中创建的。 下图显示了如何将写入区域为“美国西部”、读取区域为“美国东部”和“美国东部 2”的容器备份到相应区域中的远程 Azure Blob 存储帐户。 默认情况下，每个区域会将备份存储在本地冗余的存储帐户中。 如果该区域已启用[可用性区域](high-availability.md#availability-zone-support)，则备份将存储在区域冗余的存储帐户中。

:::image type="content" source="./media/continuous-backup-restore-introduction/continuous-backup-restore-blob-storage.png" alt-text="将 Azure Cosmos DB 数据备份到 Azure Blob 存储。" lightbox="./media/continuous-backup-restore-introduction/continuous-backup-restore-blob-storage.png" border="false":::

可用的还原时间范围（也称为保留期）为以下两者中的较小值：从现在起的过去 30 天，或直到资源创建时间 。 还原时间点可以是保留期内的任一时间戳。

当前，可以使用 [Azure 门户](restore-account-continuous-backup.md#restore-account-portal)、[Azure 命令行接口](restore-account-continuous-backup.md#restore-account-cli) (az CLI)、[Azure PowerShell](restore-account-continuous-backup.md#restore-account-powershell) 或者 [Azure 资源管理器](restore-account-continuous-backup.md#restore-arm-template)将 SQL API 或 MongoDB 内容时间点的 Azure Cosmos DB 帐户还原到另一个帐户。

## <a name="backup-storage-redundancy"></a>备份存储冗余

默认情况下，Azure Cosmos DB 将连续模式备份数据存储在本地冗余存储 Blob 中。 对于配置了区域冗余的区域，备份存储在区域冗余存储 Blob 中。 在连续备份模式下，你无法更新备份存储冗余。

## <a name="what-is-restored"></a>还原哪些内容？

处于稳定状态时，在源帐户中执行的所有改动（包括数据库、容器和项）都会在 100 秒内进行异步备份。 如果备份媒体（即 Azure 存储）关闭或不可用，则在媒体恢复正常之前，改动的内容将保存在本地。媒体恢复正常后，将清除这些内容，以防止可还原操作的保真度出现任何损失。

你可以选择还原预配的吞吐量容器、共享吞吐量数据库或整个帐户的任意组合。 还原操作会将所有数据及其索引属性还原到新帐户中。 还原过程可确保在帐户、数据库或容器中还原的所有数据在指定的还原时间之前都是一致的。 还原的持续时间取决于需要还原的数据量。

> [!NOTE]
> 使用连续备份模式时，将在提供你的 Azure Cosmos DB 帐户的每个区域中创建备份。 为每个区域帐户创建的备份默认是本地冗余的备份；如果你的帐户在该区域中启用了[可用性区域](high-availability.md#availability-zone-support)功能，则备份是区域冗余的备份。 还原操作始终将数据还原到新帐户。

## <a name="what-is-not-restored"></a>不还原哪些内容？

执行时间点恢复后，不会还原以下配置：

* 防火墙、VNET、专用终结点设置。
* 一致性设置。 默认情况下，会使用会话一致性来还原帐户。  
* 区域。
* 存储过程、触发器、UDF。

完成还原后，可将这些配置添加到还原的帐户。

## <a name="restore-scenarios"></a>还原方案

下面是时间点还原功能可解决的一些重要方案。 方案 [a] 到 [c] 演示在事先知道还原时间戳的情况下如何触发还原。
但在某些情况下，你可能并不知道发生意外删除或损坏的确切时间。 方案 [d] 和 [e] 演示如何在可还原的数据库或容器中使用新的事件源 API 来发现还原时间戳。

:::image type="content" source="./media/continuous-backup-restore-introduction/restorable-account-scenario.png" alt-text="具有可还原帐户时间戳的生命周期事件。" lightbox="./media/continuous-backup-restore-introduction/restorable-account-scenario.png" border="false":::

1. 还原已删除的帐户 -“还原”窗格中显示了所有可还原的已删除帐户 。 例如，如果在时间戳 T3 处删除了帐户 A。 在这种情况下，使用紧靠在 T3、位置、目标帐户名称、资源组和目标帐户名称前面的时间戳就足以能够从 [Azure 门户](restore-account-continuous-backup.md#restore-deleted-account)、[PowerShell](restore-account-continuous-backup.md#trigger-restore-ps) 或 [CLI](restore-account-continuous-backup.md#trigger-restore-cli) 还原。  

:::image type="content" source="./media/continuous-backup-restore-introduction/restorable-container-database-scenario.png" alt-text="具有可还原数据库和容器时间戳的生命周期事件。" lightbox="./media/continuous-backup-restore-introduction/restorable-container-database-scenario.png" border="false":::

2. 还原特定区域中的帐户数据 - 例如，如果帐户 A 位于“美国东部”和“美国西部”这两个区域中的时间戳 T3 处  。 如果你需要“美国西部”中帐户 A 的副本，可以使用“美国西部”作为目标位置，从 [Azure 门户](restore-account-continuous-backup.md#restore-deleted-account)、[PowerShell](restore-account-continuous-backup.md#trigger-restore-ps) 或 [CLI](restore-account-continuous-backup.md#trigger-restore-cli) 执行时间点还原。

3. 执行意外的写入或删除操作后在具有已知还原时间戳的容器中恢复 - 例如，如果你知道数据库 1 中容器 1 的内容在时间戳 T3 处发生意外的修改  。 可以在 [Azure 门户](restore-account-continuous-backup.md#restore-live-account)、[PowerShell](restore-account-continuous-backup.md#trigger-restore-ps) 或 [CLI](restore-account-continuous-backup.md#trigger-restore-cli) 中从时间戳 T3 处执行到另一个帐户的时间点还原，以恢复容器的所需状态。

4. 将帐户还原到在意外删除数据库之前的某个时间点 - 在 [Azure 门户](restore-account-continuous-backup.md#restore-live-account)中，可以使用事件源窗格确定数据库的删除时间并查找还原时间。 同样，在 [Azure CLI](restore-account-continuous-backup.md#trigger-restore-cli) 和 [PowerShell](restore-account-continuous-backup.md#trigger-restore-ps) 中，可以通过枚举数据库事件源来发现数据库删除事件，然后结合所需的参数触发 restore 命令。

5. 将帐户还原到在意外删除或修改容器属性之前的某个时间点。 - 在 [Azure 门户](restore-account-continuous-backup.md#restore-live-account)中，可以使用事件源窗格确定容器的创建、修改或删除时间，以找到还原时间。 同样，在 [Azure CLI](restore-account-continuous-backup.md#trigger-restore-cli) 和 [PowerShell](restore-account-continuous-backup.md#trigger-restore-ps) 中，可以通过枚举容器事件源来发现所有容器事件，然后结合所需的参数触发 restore 命令。

## <a name="permissions"></a>权限

Azure Cosmos DB 允许隔离和限制将连续备份帐户还原为特定角色或主体的权限。 帐户所有者可以触发还原，并将角色分配给其他主体以执行还原操作。 有关详细信息，请参阅[权限](continuous-backup-restore-permissions.md)一文。

## <a name="pricing"></a><a id="continuous-backup-pricing"></a>定价

已启用连续备份的 Azure Cosmos DB 帐户会产生存储备份和还原数据的每月额外费用 。 每次启动还原操作，还原费用都会累加。 如果你在帐户中配置了连续备份但未还原数据，则帐单中只包括备份存储费用。

以下示例基于美国非政府区域部署的 Azure Cosmos 帐户的价格。 定价和计算可能因所使用的区域而异。有关最新定价信息，请参阅 [Azure Cosmos DB 定价页](https://azure.microsoft.com/pricing/details/cosmos-db/)。

* 已启用连续备份策略的所有帐户都会产生额外的每月备份存储费用，其计算方式如下：

  $0.20/GB * 帐户中的数据大小 (GB) * 区域数量

* 每次调用还原 API 都会产生一次性费用。 该费用取决于数据还原量，其计算方式如下：

  $0.15/GB * 数据大小 (GB)。

例如，如果你在两个区域中有 1-TB 数据，那么：

* 备份存储费用的计算方式为 (1000 * 0.20 * 2) = $400/月

* 每次还原的还原费用计算方式为 (1000 * 0.15) = $150

## <a name="current-limitations"></a>当前限制

目前，时间点还原功能具有以下限制：

* 仅用于 SQL 和 MongoDB 的 Azure Cosmos DB API 支持连续备份。 Cassandra、表和 Gremlin API 尚不支持连续备份。

* Azure 主权云和 Azure 政府云区域尚不支持连续备份。

* 具有客户管理的密钥的帐户不支持使用连续备份。

* 不支持多区域写入帐户。

* Azure Synapse Link 和定期备份模式可在同一数据库帐户中共存。 但是，分析存储数据并不包含在备份和还原中。 启用 Synapse Link 后，Azure Cosmos DB 将继续按计划的备份间隔自动对事务性存储中的数据执行备份。 

* Azure Synapse Link 和连续备份模式可在同一数据库帐户中共存。 目前，启用 Synapse Link 的数据库帐户无法使用连续备份模式，反之亦然。

* 还原的帐户是在源帐户所在的区域中创建的。 如果源帐户未存在于某个区域中，则无法将该帐户还原到该区域。

* 还原时间范围只有 30 天且无法更改。

* 备份不能自动抵御地理灾难。 你必须显式添加另一个区域，以使帐户和备份具有复原能力。

* 当还原正在进行时，请不要修改或删除为帐户授予权限的标识和访问管理 (IAM) 策略，也不要更改任何 VNET、防火墙配置。

* 如果用于 SQL 或 MongoDB 的 Azure Cosmos DB API 帐户在创建容器后创建了唯一索引，则不支持使用该帐户进行连续备份。 只有在最初创建容器的过程中创建了唯一索引的容器才受支持。 对于 MongoDB 帐户，可以使用[扩展命令](mongodb/custom-commands.md)创建唯一索引。

* 时间点还原功能始终会还原到新的 Azure Cosmos 帐户。 当前不支持还原到现有帐户。 如果你有意提供有关就地还原的反馈，请通过客户代表联系 Azure Cosmos DB 团队。

* 还原后，对于某些集合，可能会重新生成一致的索引。 可以通过 [IndexTransformationProgress](how-to-manage-indexing-policy.md) 属性检查重新生成操作的状态。

* 还原过程会还原容器的所有属性，包括其 TTL 配置。 因此，还原的数据可能会被立即删除（如果你进行了这样的配置）。 为了防止出现这种情况，在，还原时间戳必须是将 TTL 属性添加到容器之前的某个时间。

* 创建连续备份模式帐户或将帐户从定期模式迁移到连续模式时，无法添加或更新适用于 MongoDB 的 API 中的唯一索引。

## <a name="next-steps"></a>后续步骤

* 使用 [Azure 门户](provision-account-continuous-backup.md#provision-portal)、[PowerShell](provision-account-continuous-backup.md#provision-powershell)、[CLI](provision-account-continuous-backup.md#provision-cli) 或 [Azure 资源管理器](provision-account-continuous-backup.md#provision-arm-template)预配连续备份。
* 使用 [Azure 门户](restore-account-continuous-backup.md#restore-account-portal)、[PowerShell](restore-account-continuous-backup.md#restore-account-powershell)、[CLI](restore-account-continuous-backup.md#restore-account-cli) 或 [Azure 资源管理器](restore-account-continuous-backup.md#restore-arm-template)还原连续备份帐户。
* [将帐户从定期备份迁移到连续备份](migrate-continuous-backup.md)。
* [管理](continuous-backup-restore-permissions.md)以连续备份模式还原数据所需的权限。
* [连续备份模式的资源模型](continuous-backup-restore-resource-model.md)
