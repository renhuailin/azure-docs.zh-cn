---
title: 备份和还原 - 快照、异地冗余
description: 了解 Azure Synapse Analytics 专用 SQL 池中的备份和还原的工作原理。 使用备份可以将数据仓库还原到主要区域的某个还原点。 使用异地冗余备份可还原到不同的地理区域。
services: synapse-analytics
author: joannapea
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/13/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019"
ms.openlocfilehash: d17f370739acf5280850beb1eb14ad8cdc0268a6
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129424929"
---
# <a name="backup-and-restore-in-azure-synapse-dedicated-sql-pool"></a>Azure Synapse 专用 SQL 池中的备份和还原

了解如何在 Azure Synapse 专用 SQL 池中使用备份和还原。 使用专用 SQL 池还原点可以恢复或复制主要区域中旧状态的数据仓库。 使用数据仓库异地冗余备份可还原到不同的地理区域。

## <a name="what-is-a-data-warehouse-snapshot"></a>什么是数据仓库快照

数据仓库快照会创建一个还原点，利用该还原点可将数据仓库恢复或复制到以前的状态。  由于专用 SQL 池是一个分布式系统，因此数据仓库快照包含许多位于 Azure 存储中的文件。 快照捕获数据仓库中存储的数据的增量更改。

数据仓库还原是基于现有数据仓库或已删除数据仓库的还原点创建的新数据仓库。 还原数据仓库是任何业务连续性和灾难恢复策略的基本组成部分，因为数据库还原可以在意外损坏或删除数据后重新创建数据。 此外，数据仓库快照是出于测试或开发目的创建数据仓库副本的强大机制。 专用 SQL 池还原速度因数据库大小以及源和目标数据仓库的位置而异。

## <a name="automatic-restore-points"></a>自动还原点

快照是用于创建还原点的内置功能。 无需启用此功能。 但是，专用 SQL 池应处于活动状态才能创建还原点。 如果它频繁暂停，则可能不会创建自动还原点，因此请确保在暂停专用 SQL 池之前创建用户定义的还原点。 用户目前无法删除自动还原点，因为服务使用这些还原点来维护 SLA 以进行恢复。

系统会全天捕获数据仓库的快照，创建可以使用七天的还原点。 无法更改此保留期。 专用 SQL 池支持八小时恢复点目标 (RPO)。 可以根据过去七天捕获的任意一个快照，还原主要区域中的数据仓库。

若要查看上一个快照的启动时间，可对联机专用 SQL 池运行此查询。

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs
order by run_id desc
;
```

## <a name="user-defined-restore-points"></a>用户定义的还原点

使用此功能，可以在大型修改之前和之后手动触发快照，以便创建数据仓库的还原点。 此功能可确保在出现工作负荷中断或用户错误的情况下，还原点在逻辑上是一致的，这样可以提供额外的数据保护，缩短恢复时间。 用户定义的还原点可以使用七天，然后系统会替你将它自动删除。 无法更改用户定义的还原点的保留期。 无论在任何时间点，均会保证 **42 个用户定义的还原点**，因此，它们必须在创建另一个还原点之前 [删除](/powershell/module/azurerm.sql/remove-azurermsqldatabaserestorepoint)。 可以通过 [PowerShell](/powershell/module/az.sql/new-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.jsont#examples) 或 Azure 门户触发快照来创建用户定义的还原点。

> [!NOTE]
> 如需将还原点保留 7 天以上，请在[此处](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/35114410-user-defined-retention-periods-for-restore-points)为此功能投票。 此外，可以创建用户定义的还原点，然后从新建的还原点还原到新数据仓库。 还原后，专用 SQL 池将会联机，可以无限期将其暂停，以节省计算成本。 暂停的数据库按 Azure Synapse 存储费率收取存储费用。 如需已还原数据仓库的活动副本，可以执行恢复，只需花费几分钟时间。

### <a name="restore-point-retention"></a>还原点保留期

下面列出了还原点保留期的详细信息：

1. 专用 SQL 池会在达到 7 天保留期并且总共至少有 42 个还原点（包括用户定义的还原点和自动还原点）时删除还原点。
2. 暂停专用 SQL 池时不会创建快照。
3. 还原点的存在时长是从创建还原点的时间算起的绝对日历天数（包括 SQL 池暂停的时间）。
4. 在任何时间点，专用 SQL 池均保证能够存储最多 42 个用户定义的还原点或 42 个自动还原点，只要这些还原点尚未达到 7 天保留期的限制
5. 如果创建了快照，然后专用 SQL 池暂停 7 天以上的时间，然后进行了恢复，那么还原点会持续存在，直到总共有 42 个还原点（包括用户定义的还原点和自动还原点）

### <a name="snapshot-retention-when-a-sql-pool-is-dropped"></a>删除 SQL 池时的快照保留

删除专用 SQL 池时，系统会创建最终的快照并将其保存七天。 可以将专用 SQL 池还原至删除时所创建的最终还原点。 如果专用 SQL 池是在暂停状态下删除的，则不会创建快照。 在这种情况下，请确保在删除专用 SQL 池之前创建用户定义的还原点。

> [!IMPORTANT]
> 如果删除托管专用 SQL 池的服务器/工作区，则属于该服务器/工作区的所有数据库也会被删除且不可恢复。 无法还原已删除的服务器。

## <a name="geo-backups-and-disaster-recovery"></a>异地备份和灾难恢复

每日在[配对的数据中心](../../best-practices-availability-paired-regions.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)创建一次异地备份。 异地还原的 RPO 为 24 小时。 可以将异地备份还原到支持专用 SQL 池的任何其他区域的服务器。 使用异地备份可在无法访问主要区域中的还原点时还原数据仓库。

如果专用 SQL 池不需要异地备份，则可以禁用这些备份，从而节省灾难恢复的存储成本。 为此，请参阅[操作方法指南：禁用专用 SQL 池（之前称为 SQL DW）的异地备份](disable-geo-backup.md)。 请注意，如果禁用异地备份，则在主 Azure 数据中心不可用时，将无法将专用 SQL 池恢复到配对的 Azure 区域。 

> [!NOTE]
> 如果希望异地备份的 RPO 更短，请在[此处](https://feedback.azure.com/forums/307516-sql-data-warehouse)为此功能投票。 此外，可以创建用户定义的还原点，然后从新建的还原点还原到其他区域中的新数据仓库。 还原后，数据仓库将会联机，可以无限期将其暂停，以节省计算成本。 暂停的数据库按 Azure 高级存储费率收取存储费用。 较短恢复点的另一种常见模式是将数据并行引入到数据仓库的主要实例和辅助实例中。 在这种情况下，数据从一个（或多个）源中引入并持久保存到数据仓库的两个独立实例（主要实例和辅助实例）。 为了节省计算成本，可以暂停仓库的辅助实例。 如果需要数据仓库的活动副本，可以执行恢复，只需花费几分钟时间。

## <a name="data-residency"></a>数据驻留 

如果配对的数据中心位于你所在国家/地区以外的位置，则可以通过在本地冗余存储 (LRS) 上预配数据库，确保你的数据保留在你的区域内。 如果你的数据库已经在 RA-GRS（只读地理冗余存储，当前的默认值）上预配，那么可以选择不进行异地备份，但你的数据库将继续驻留在复制到区域对的存储上。 要确保客户数据保留在你的区域中，你可以将专用 SQL 池预配或还原为本地冗余存储。 有关如何预配或还原为本地冗余存储的详细信息，请参阅[操作指南：如何在 Azure Synapse Analytics 中为专用 SQL 池（以前成为 SQL DW）配置单区域驻留](single-region-residency.md)

若要确认配对的数据中心是否位于不同的国家/地区，请参阅 [Azure 配对区域](../../best-practices-availability-paired-regions.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。

## <a name="backup-and-restore-costs"></a>备份和还原成本

Azure 帐单上将列出存储的明细项目，以及灾难恢复存储的明细项目。 存储费用是在主要区域中存储数据的费用加上快照捕获增量更改的费用。 有关快照收费方式的更详细说明，请参阅[了解快照如何收取费用](/rest/api/storageservices/Understanding-How-Snapshots-Accrue-Charges?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。 异地冗余费用是指存储异地备份的费用。  

主数据仓库和 7 天快照更改的总费用根据 TB 数的舍入近似值计算。 例如，如果数据仓库为 1.5 TB，快照捕获了 100 GB，则会根据 Azure 高级存储费率计收 2 TB 数据的费用。

如果使用的是异地冗余存储，则会单独收取异地存储费。 异地冗余存储按标准的读取访问异地冗余存储 (RA-GRS) 费率计费。

有关 Azure Synapse 定价的详细信息，请参阅 [Azure Synapse 定价](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/)。 跨区域还原时，不会对数据流出量收费。

## <a name="restoring-from-restore-points"></a>从还原点还原

每个快照创建一个代表快照开始时间的还原点。 如果要还原数据仓库，请选择一个还原点，并发出还原命令。  

可以保留还原的数据仓库和当前的数据仓库，也可以删除其中一个。 如果希望使用已还原数据仓库替换当前数据仓库，可使用 [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 的 MODIFY NAME 选项将其重命名。

若要还原数据仓库，请参阅[还原专用 SQL 池](sql-data-warehouse-restore-points.md#create-user-defined-restore-points-through-the-azure-portal)。

若要还原已删除或已暂停的数据仓库，则可以[创建支持票证](sql-data-warehouse-get-started-create-support-ticket.md)。

## <a name="cross-subscription-restore"></a>跨订阅还原

如果需要跨订阅直接还原，请在[此处](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/36256231-enable-support-for-cross-subscription-restore)为此功能投票。 还原到其他服务器，并跨订阅[“移动”](../../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)服务器以执行跨订阅还原。

## <a name="geo-redundant-restore"></a>异地冗余还原

可以[将专用 SQL 池还原](sql-data-warehouse-restore-from-geo-backup.md#restore-from-an-azure-geographical-region-through-powershell)到支持所选性能级别的专用 SQL 池的任何区域。

> [!NOTE]
> 若要执行异地冗余还原，不能选择退出此功能。

## <a name="next-steps"></a>后续步骤

有关还原点的详细信息，请参阅[用户定义的还原点](sql-data-warehouse-restore-points.md)
