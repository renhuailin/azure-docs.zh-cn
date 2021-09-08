---
title: 为 Azure Cosmos DB 帐户配置定期备份
description: 本文介绍了如何为 Azure Cosmos DB 帐户配置定期备份和备份时间间隔， 以及保留期。 此外，它还介绍了如何联系支持人员来还原数据。
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/30/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 56e9bfe95a78c8bf0771acdc98c761df9994a708
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123221062"
---
# <a name="configure-azure-cosmos-db-account-with-periodic-backup"></a>为 Azure Cosmos DB 帐户配置定期备份
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB 会定期自动备份数据。 自动备份不会影响数据库操作的性能或可用性。 所有备份均单独存储在一个存储服务中，并会进行全局复制，以便在发生区域性灾难时可以复原。 使用 Azure Cosmos DB，数据和数据备份都高度冗余，并且具有可复原性，能抵御区域性灾难。 以下步骤演示 Azure Cosmos DB 如何执行数据备份：

* Azure Cosmos DB 每 4 小时自动完整备份数据库一次，默认情况下在任何时间点都只存储最新的 2 个备份。 如果默认间隔不能满足工作负荷要求，则可以从 Azure 门户更改备份间隔和保留期。 可以在创建 Azure Cosmos 帐户期间或之后更改备份配置。 如果删除了容器或数据库，Azure Cosmos DB 会将给定容器或数据库中的现有快照保留 30 天。

* Azure Cosmos DB 将这些备份存储在 Azure Blob 存储中，而实际数据以本地形式驻留在 Azure Cosmos DB 中。

* 为保证低延迟，备份的快照将存储在 Azure Blob 存储中，且所在区域与当前写入区域（如果有多区域写入配置，则为其中一个写入区域）相同。 对于针对区域性灾难的恢复，Azure Blob 存储中备份数据的屏幕快照将通过异地冗余存储 (GRS) 再次复制到另一区域。 根据源区域以及与源区域关联的区域对确定备份复制的目标区域。 若要了解详细信息，请参阅 [Azure 区域的异地冗余对列表](../best-practices-availability-paired-regions.md)一文。 不能直接访问此数据库。 当你通过支持请求来请求还原后，Azure Cosmos DB 团队将还原你的备份。

  下图显示了如何在美国西部的远程 Azure Blob 存储帐户中备份 Azure Cosmos 容器（其三个主要物理分区全部位于美国西部），然后将其复制到美国东部：

  :::image type="content" source="./media/configure-periodic-backup-restore/automatic-backup.png" alt-text="GRS Azure 存储中所有 Cosmos DB 实体的定期完整备份。" lightbox="./media/configure-periodic-backup-restore/automatic-backup.png" border="false":::

* 备份不会影响应用程序的性能或可用性。 Azure Cosmos DB 在后台执行数据备份，不会消耗任何额外的预配吞吐量 (RU)，也不会影响数据库的性能和可用性。

> [!Note]
> 对于已启用 Azure Synapse Link 的帐户，分析存储数据不包含在备份和还原中。 启用 Synapse Link 后，Azure Cosmos DB 将继续按计划的备份间隔自动对事务性存储中的数据执行备份。 目前不支持自动备份和还原分析存储中的数据。

## <a name="backup-storage-redundancy"></a><a id="backup-storage-redundancy"></a>备份存储冗余

Azure Cosmos DB 默认将定期模式备份数据存储在复制到[配对区域](../best-practices-availability-paired-regions.md)的异地冗余 [blob 存储](../storage/common/storage-redundancy.md)中。  

要确保备份数据保留在预配 Azure Cosmos DB 帐户所在的同一区域内，可更改默认异地冗余备份存储，并配置本地冗余或区域冗余存储。 Azure 存储冗余机制会存储多个备份副本，以防范各种计划内和计划外的事件，包括暂时性的硬件故障、网络中断或断电、重大自然灾害等。

Azure Cosmos DB 中的备份数据会在主要区域中复制三次。 可在创建帐户时为定期备份模式配置存储冗余，或针对现有帐户进行更新。 在定期备份模式中，可使用以下三种数据冗余选项：

* 异地冗余备份存储：此选项跨配对区域异步复制数据。

* 区域冗余备份存储：此选项跨主要区域中的三个 Azure 可用性区域异步复制数据。

* 本地冗余备份存储：此选项在主要区域中的单个物理位置异步复制数据三次。

> [!NOTE]
> 区域冗余存储目前仅在[特定区域](high-availability.md#availability-zone-support)可用。 根据为新帐户选择的区域或现有帐户使用的区域；区域冗余选项将不可用。
>
> 更新备份存储冗余不会对备份存储定价产生任何影响。

## <a name="modify-the-backup-interval-and-retention-period"></a><a id="configure-backup-interval-retention"></a>修改备份时间间隔和保持期

Azure Cosmos DB 每 4 小时自动对数据库执行一次完整备份，而且在任何时候都只存储最新的 2 个备份。 此配置是默认选项，无需额外付费即可使用。 你可以在创建 Azure Cosmos 帐户期间或创建帐户之后更改默认备份时间间隔和保持期。 备份配置是在 Azure Cosmos 帐户级别设置的，需要在每个帐户上配置。 为帐户配置备份选项后，它将应用于该帐户中的所有容器。 目前，你只能从 Azure 门户更改它们的备份选项。

如果数据被意外删除或损坏，在你创建支持请求以申请还原数据之前，请确保将帐户的备份保留期延长到至少 7 天。最好在发生此事件的 8 小时内延长保留期。 这样，Azure Cosmos DB 团队才有足够的时间来还原你的帐户。

### <a name="modify-backup-options-for-an-existing-account"></a>修改现有帐户的备份选项

使用以下步骤可为现有 Azure Cosmos 帐户更改默认备份选项：

1. 登录到 [Azure 门户](https://portal.azure.com/)。
1. 导航到你的 Azure Cosmos 帐户，打开“备份和还原”窗格。 根据需要更新备份间隔和备份保持期。

   * **备份间隔** - 这是 Azure Cosmos DB 尝试对数据进行备份的间隔。 备份需要一定的时间，在某些情况下，可能由于下游依赖项而失败。 Azure Cosmos DB 会尽力按配置的间隔进行备份，但它不能保证备份在该时间间隔内完成。 可以按小时或分钟配置此值。 备份间隔不能小于 1 小时且不能超过 24 小时。 更改此间隔后，新间隔将从进行上次备份时开始生效。

   * 备份保持期 - 它表示保留每个备份的时间段。 可以按小时或天配置它。 最小保持期不能小于备份间隔的两倍（以小时为单位），并且不能超过 720 小时。

   * 保留的数据副本 - 默认情况下，会免费提供数据的两个备份副本。 如果需要两个以上的副本，则需支付额外的费用。 请参阅[定价页](https://azure.microsoft.com/pricing/details/cosmos-db/)中的“已用存储”部分，了解额外副本的确切价格。

   * 备份存储冗余 — 选择所需的存储冗余选项，有关可用选项，请参阅[备份存储冗余](#backup-storage-redundancy)部分。 现有定期备份模式帐户默认具有异地冗余存储。 可选择本地冗余等其他存储，以确保备份不会复制到另一个区域。 对现有帐户所做更改将仅应用于将来的备份。 更新现有帐户的备份存储冗余后，可能需要最多两次备份间隔才能使更改生效，但将无法立即还原旧备份。

   > [!NOTE]
   > 必须在订阅级别分配 Azure [Cosmos DB 帐户读取者角色](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)才能配置备份存储冗余。

   :::image type="content" source="./media/configure-periodic-backup-restore/configure-backup-options-existing-accounts.png" alt-text="为现有 Azure Cosmos 帐户配置备份间隔、保留期和存储冗余。" border="true":::

### <a name="modify-backup-options-for-a-new-account"></a>修改新帐户的备份选项

预配新帐户时，请在“备份策略”选项卡中选择“定期”备份策略。 _ 通过定期策略可配置备份时间间隔、备份保留期和备份存储冗余。 例如，可选择“本地冗余备份存储”*或“区域冗余备份存储”选项，以防止在区域外进行备份数据复制。

:::image type="content" source="./media/configure-periodic-backup-restore/configure-backup-options-new-accounts.png" alt-text="为新 Azure Cosmos 帐户配置定期或连续备份策略。" border="true":::

## <a name="request-data-restore-from-a-backup"></a><a id="request-restore"></a>请求从备份还原数据

如果意外删除了数据库或容器，可以[提交支持工单](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)或[联系 Azure 支持](https://azure.microsoft.com/support/options/)，以便从自动联机备份中还原数据。 Azure 支持仅适用于选定的计划，例如“标准”、“开发人员”以及更高级别的计划 。 不适用于基本计划  。 若要了解不同的支持方案，请参阅 [Azure 支持计划](https://azure.microsoft.com/support/plans/)页。

若要还原备份的特定快照，Azure Cosmos DB 要求数据在该快照的备份周期内可用。
在请求还原之前，应该了解以下详细信息：

* 准备好订阅 ID。

* 应根据数据被意外删除或修改的方式，准备好提供其他信息。 建议提前准备可用的信息，从而尽量减少可能在某些有时效的情况下造成不良影响的来回传输。

* 如果删除了整个 Azure Cosmos DB 帐户，则需要提供删除的帐户的名称。 如果创建了同名的另一个帐户，请与支持团队共享该帐户，因为这有助于确定要选择的正确帐户。 建议为删除的每个帐户提交不同的支持工单，因为这可以最大限度地减少还原状态的混乱。

* 如果删除了一个或多个数据库，应提供 Azure Cosmos 帐户及 Azure Cosmos 数据库名，并指定是否存在同名的新数据库。

* 如果删除了一个或多个容器，应提供 Azure Cosmos 帐户名、数据库名和容器名。 并指定是否存在同名容器。

* 如果意外删除或损坏了数据，则应在 8 小时内联系 [Azure 支持](https://azure.microsoft.com/support/options/)，以便 Azure Cosmos DB 团队帮助你从备份中还原数据。 在你创建支持请求以申请还原数据之前，请确保将帐户的[备份保留期延长](#configure-backup-interval-retention)到至少 7 天。最好在发生此事件的 8 小时内延长保留期。 这样，Azure Cosmos DB 支持团队才有足够的时间来还原你的帐户。

除了 Azure Cosmos 帐户名、数据库名、容器名以外，还应指定数据可以还原到的时间点。 务必尽量精确，因为这有助于我们确定当时可用的最佳备份。 **指定 UTC 时间也很重要。**

下面的屏幕截图说明如何为容器（集合/图/表）创建通过 Azure 门户还原数据的支持请求。 提供其他详细信息（例如数据类型、还原目的、删除数据的时间），以帮助我们设置请求的优先级。

:::image type="content" source="./media/configure-periodic-backup-restore/backup-support-request-portal.png" alt-text="使用 Azure 门户创建备份支持请求。" border="true":::

## <a name="considerations-for-restoring-the-data-from-a-backup"></a>从备份还原数据时的注意事项

在以下情况之一中，可能会意外删除或修改数据：  

* 删除整个 Azure Cosmos 帐户。

* 删除一个或多个 Azure Cosmos 数据库。

* 删除一个或多个 Azure Cosmos 容器。

* 删除或修改容器中的 Azure Cosmos 项（例如文档）。 此特定情况通常称为"数据损坏"。

* 删除或损坏共享产品数据库中的共享产品数据库或容器。

在上述所有情况中，Azure Cosmos DB 均可还原数据。 进行还原时，会创建一个新的 Azure Cosmos 帐户来保存还原数据。 如果未指定新帐户的名称，则其名称将采用 `<Azure_Cosmos_account_original_name>-restored1` 格式。 如果尝试多次还原，则最后一位数将递增。 不能将数据还原到预先创建的 Azure Cosmos 帐户中。

意外删除 Azure Cosmos 帐户后，如果该帐户名未被使用，则可将数据还原到相同名称的帐户中。 因此，建议不要在删除帐户后重新创建它。 因为这样不仅会阻止还原数据使用相同的名称，还会难以确定正确还原帐户。

意外删除 Azure Cosmos 数据库后，我们可以还原整个数据库或该数据库中的容器子集。 还可以跨数据库选择特定容器并将它们还原到新的 Azure Cosmos 帐户中。

如果意外删除或修改了容器中的一个或多个项（即数据损坏情况），需要指定还原到的时间。 如果数据损坏，则时间很重要。 由于容器是实时的，所以备份仍在运行，因此如果超过了保持期（默认值为 8 小时），备份将被覆盖。 若要防止备份被覆盖，请将帐户的备份保留期增加到至少七天。 最好在发生数据损坏后的 8 小时内延长保留期。

如果意外删除或损坏了数据，则应在 8 小时内联系 [Azure 支持](https://azure.microsoft.com/support/options/)，以便 Azure Cosmos DB 团队帮助你从备份中还原数据。 这样，Azure Cosmos DB 支持团队才有足够的时间来还原你的帐户。

> [!NOTE]
> 还原数据后，并非所有源功能或设置都会转移到还原的帐户。 以下设置不会转移到新帐户：
> * VNET 访问控制列表
> * 存储过程、触发器和用户定义的函数
> * 多区域设置  

如果在数据库级别预配吞吐量，那么在这种情况下，将对整个数据库，而不是单个容器进行备份和还原。 在这种情况下，无法选择还原容器子集。

## <a name="required-permissions-to-change-retention-or-restore-from-the-portal"></a>从门户中更改保留期或还原所需的权限
属于 [CosmosdbBackupOperator](../role-based-access-control/built-in-roles.md#cosmosbackupoperator)、所有者或参与者角色的主体可以请求还原或更改保留期。

## <a name="understanding-costs-of-extra-backups"></a>了解额外备份的成本
免费提供 2 个备份，额外备份需要根据[备份存储定价](https://azure.microsoft.com/pricing/details/cosmos-db/)中介绍的基于区域的备份存储定价付费。 例如，如果将“备份保留期”配置为 240 小时，即 10 天，并将“备份间隔”设置为 24 小时， 则意味着备份数据会有 10 个副本。 假定在美国西部 2 有 1 TB 数据，那么对于给定月份中的备份存储，费用将为 0.12 * 1000 * 8。

## <a name="options-to-manage-your-own-backups"></a>管理自己的备份的选项

通过 Azure Cosmos DB SQL API 帐户，还可采用以下某一方法维护备份：

* 使用 [Azure 数据工厂](../data-factory/connector-azure-cosmos-db.md)定期将数据移至所选的存储。

* 使用 Azure Cosmos DB [更改源](change-feed.md)定期读取数据来进行完整备份或增量更改，并将其存储在自己的存储中。

## <a name="post-restore-actions"></a>还原后的操作

数据还原的主要目标是恢复意外删除或修改的数据。 因此，建议先检查已还原数据，确保其中包含所需内容。 如果一切正常，则可以将数据迁移回主帐户。 尽管可以使用已还原帐户作为新的活动帐户，但如果有生产工作负载则不建议这样做。 

还原数据后，你会收到有关新帐户名（通常采用 `<original-name>-restored1` 格式）和帐户要还原到的时间的通知。 还原的帐户与原始帐户具有相同的预配吞吐量、索引策略，并且二者位于同一区域。 角色为订阅管理员或共同管理员的用户可以看到还原的帐户。

### <a name="migrate-data-to-the-original-account"></a>将数据迁移到原始帐户

可通过下述不同方式将数据迁移回原始帐户：

* 使用 [Azure Cosmos DB 数据迁移工具](import-data.md)。
* 使用 [Azure 数据工厂](../data-factory/connector-azure-cosmos-db.md)。
* 使用 Azure Cosmos DB 中的[更改源](change-feed.md)。
* 可以编写自己的自定义代码。

建议在迁移数据之后立即删除容器或数据库。 如果不删除已还原的数据库或容器，它们将在请求单位、存储和流出量方面产生成本。

## <a name="next-steps"></a>后续步骤

* 若要提出还原请求，请联系 Azure 支持，并[从 Azure 门户提交票证](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。
* 使用 [Azure 门户](provision-account-continuous-backup.md#provision-portal)、[PowerShell](provision-account-continuous-backup.md#provision-powershell)、[CLI](provision-account-continuous-backup.md#provision-cli) 或 [Azure 资源管理器](provision-account-continuous-backup.md#provision-arm-template)预配连续备份。
* 使用 [Azure 门户](restore-account-continuous-backup.md#restore-account-portal)、[PowerShell](restore-account-continuous-backup.md#restore-account-powershell)、[CLI](restore-account-continuous-backup.md#restore-account-cli) 或 [Azure 资源管理器](restore-account-continuous-backup.md#restore-arm-template)还原连续备份帐户。
* [将帐户从定期备份迁移到连续备份](migrate-continuous-backup.md)。
* [管理](continuous-backup-restore-permissions.md)以连续备份模式还原数据所需的权限。
