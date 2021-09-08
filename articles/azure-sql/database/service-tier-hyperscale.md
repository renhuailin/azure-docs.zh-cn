---
title: 什么是“超大规模”服务层级？
description: 本文介绍 Azure SQL 数据库中基于 vCore 的购买模型中的“超大规模”服务层级，并说明它与“常规用途”服务层级和“业务关键”服务层级的不同之处。
services: sql-database
ms.service: sql-database
ms.subservice: service-overview
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: mathoma
ms.date: 7/8/2021
ms.openlocfilehash: bd5a9d64b237fe8c6591cac841b13f96a9c16f1d
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122864450"
---
# <a name="hyperscale-service-tier"></a>“超大规模”服务层级
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL 数据库基于 SQL Server 数据库引擎体系结构，该体系结构已根据云环境做出调整，以确保即使在发生基础结构故障时，也仍能提供 99.99% 的可用性。 Azure SQL 数据库中使用了三种体系结构模型：

- 常规用途/标准
- 超大规模
- 业务关键/高级

Azure SQL 数据库中的“超大规模”服务层级是基于 vCore 的购买模型中的最新服务层级。 此服务层级是一个高度可缩放的存储和计算性能层，它利用 Azure 体系结构来横向扩展 Azure SQL 数据库的存储和计算资源，远远超出了“常规用途”和“业务关键”服务层级的可用限制。

> [!NOTE]
>
> - 有关基于 vCore 的购买模型中的“常规用途”服务层级和“业务关键”服务层级的详细信息，请参阅[常规用途](service-tier-general-purpose.md)服务层级和[业务关键](service-tier-business-critical.md)服务层级。 有关基于 vCore 购买模型与基于 DTU 购买模型的比较，请参阅 [Azure SQL 数据库购买模型和资源](purchasing-models.md)。
> - “超大规模”服务层级目前仅可用于 Azure SQL 数据库，不可用于 Azure SQL 托管实例。

## <a name="what-are-the-hyperscale-capabilities"></a>“超大规模”服务层级具有哪些功能

Azure SQL 数据库中的“超大规模”服务层级提供了以下附加功能：

- 支持高达 100 TB 的数据库大小
- 几乎瞬时完成数据库备份（基于存储在 Azure Blob 存储中的文件快照），无论数据库大小，也不会对计算资源造成 IO 影响  
- 在几分钟内快速完成数据库还原（基于文件快照），无需数小时或数天（不基于数据操作的大小）
- 无论数据卷如何，由于更高的日志吞吐量和更快的事务提交速度，整体性能更高
- 快速横向扩展 - 可预配一个或多个[只读副本](service-tier-hyperscale-replicas.md)，以减轻读取工作负载并将这些副本用作热备用服务器
- 快速纵向扩展 - 可在不变的时间内纵向扩展计算资源，以在需要时应对繁重的工作负载，然后在不需要时重新纵向缩减计算资源。

“超大规模”服务层级消除了传统上云数据库中出现的许多实际限制。 当大多数其他数据库受单个节点中可用资源的限制时，“超大规模”服务层级中的数据库则没有此类限制。 它具备灵活的存储体系结构，存储可按需增长。 实际上，不会使用定义的最大大小创建“超大规模”数据库。 “超大规模”数据库会按需扩大，你仅需为所使用的容量付费。 对于读取密集型工作负载，“超大规模”服务层级通过按需预配其他副本来减轻读取工作负载，从而实现快速横向扩展。

此外，创建数据库备份或纵向扩展/横向扩展所需的时间不再与数据库中的数据卷相关。 几乎可以即时备份“超大规模”数据库。 还可在几分钟内纵向扩展或横向扩展数十 TB 的数据库。 此功能使你无需担心受初始配置选项的约束。

有关“超大规模”服务层级计算大小的详细信息，请参阅[服务层级特征](service-tiers-vcore.md#service-tiers)。

## <a name="who-should-consider-the-hyperscale-service-tier"></a>哪些群体应考虑使用“超大规模”服务层级

“超大规模”服务层级适用于大多数业务工作负荷，因为它通过可单独缩放的计算和存储资源提供极大的灵活性和高性能。 由于能够将存储自动缩放到最大 100 TB，因此对于以下客户而言，它是一个极佳的选择：

- 在本地部署了大型数据库，并希望通过迁移到云来实现应用程序的现代化
- 已迁移到云中，但受限于其他服务层的最大数据库大小 (1-4 TB) 的限制
- 使用较小的数据库，但需要快速的垂直和水平计算缩放、高性能、即时备份和快速数据库还原。

“超大规模”服务层级支持各种 SQL Server 工作负荷，包括单纯的 OLTP 到单纯的分析，但它主要是针对 OLTP 和混合事务以及分析处理 (HTAP) 工作负荷优化的。

> [!IMPORTANT]
> 弹性池不支持“超大规模”服务层级。

## <a name="hyperscale-pricing-model"></a>“超大规模”定价模型

仅 [vCore 模型](service-tiers-vcore.md)提供“超大规模”服务层级。 为了适应新的体系结构，它的定价模型与“常规用途”或“业务关键”服务层级略有不同：

- **计算**：

  “超大规模”计算单位按副本计费。 [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)价格将自动应用于高可用性副本和命名副本。 我们默认将为每个超大规模数据库创建一个主要副本和一个次要的[高可用性副本](service-tier-hyperscale-replicas.md)。  用户可根据所需的 [SLA](https://azure.microsoft.com/support/legal/sla/azure-sql-database/) 在 0-4 的范围内调整高可用性副本总数。 

- **存储**：

  配置“超大规模”数据库时，无需指定最大数据大小。 超大规模层级中根据实际分配收取数据库存储费用。 将在 40 GB 到 100 TB 之间自动分配存储，增量为 10 GB。 如果需要，可以同时增大多个数据文件。 创建的“超大规模”数据库的初始大小为 10 GB，每 10 分钟开始增大 10 GB，直到达到 40 GB 大小。

有关“超大规模”服务层级定价的详细信息，请参阅 [Azure SQL 数据库定价](https://azure.microsoft.com/pricing/details/sql-database/single/)

## <a name="distributed-functions-architecture"></a>分布式功能体系结构

不同于将所有数据管理功能集中在一个位置/进程中的传统数据库引擎（即便是当今生产中所谓的分布式数据库也有单片数据引擎的多个副本），“超大规模”数据库将查询处理引擎（其中各种数据引擎的语义不同）与为数据提供长期存储和持久性的组件分隔开来。 通过这种方式，可根据需要顺利地扩大存储容量（初始目标是 100 TB）。 高可用性副本和命名副本共享相同的存储组件，因此无需进行数据复制即可运转新副本。

下图说明了“超大规模”数据库中不同类型的节点：

![体系结构](./media/service-tier-hyperscale/hyperscale-architecture.png)

“超大规模”数据库包含以下不同类型的组件：

### <a name="compute"></a>计算

计算节点是关系引擎所在的位置。 将在其中进行语言、查询和事务处理。 所有用户与“超大规模”数据库的交互都通过这些计算节点进行。 计算节点具有基于 SSD 的缓存（在上图中标记为 RBPEX - 可复原缓冲池扩展），可最小化提取一页数据所需的网络往返次数。 其中有一个处理所有读写工作负载和事务的主计算节点。 有一个或多个充当热备用服务器节点的辅助计算节点，用于进行故障转移，也充当用于卸载只读工作负载的只读计算节点（如需此功能）。

超大规模计算节点上运行的数据库引擎与其他 Azure SQL 数据库服务层相同。 当用户与超大规模计算节点上的数据库引擎进行交互时，除[已知限制](#known-limitations)外，支持的外围应用和引擎行为与其他服务层相同。

### <a name="page-server"></a>页面服务器

页面服务器是表示横向扩展存储引擎的系统。  每个页面服务器负责数据库中页面的一个子集。  名义上，每个页面服务器控制最多 128 GB 或 1 TB 的数据。 多个页面服务器上不共享任何数据（为冗余和可用性而保留的页面服务器副本除外）。 页面服务器的任务是按需向计算节点提供数据库页面，并在事务更新数据时持续更新页面。 页面服务器通过从日志服务播放日志记录来保持最新。 页面服务器还保留基于 SSD 的缓存，可提高性能。 数据页的长期存储保存在 Azure 存储中，可提高可靠性。

### <a name="log-service"></a>日志服务

日志服务接受来自主要计算副本的日志记录，将其保存在永久缓存中，并将日志记录转发给其余计算副本（以便它们可以更新缓存）以及一个或多个相关页面服务器，以便可在此处更新数据。 通过这种方式，主要计算副本中的所有数据更改都通过日志服务传播到所有次要计算副本和页面服务器中。 最后，日志记录被推送到 Azure 存储的长期存储（本质上是一个无限期存储库）中。 此机制消除了频繁截断日志的需要。 日志服务还具有本地内存和 SSD 缓存，可加快日志记录的访问速度。

### <a name="azure-storage"></a>Azure 存储

Azure 存储在某个数据库中包含所有数据文件。 页面服务器使 Azure 存储中的数据文件保持最新状态。 此存储用于备份用途以及 Azure 区域之间的复制。 备份是使用数据文件的存储快照实现的。 无论数据大小如何，使用快照的还原操作都能快速完成。 数据可以还原到数据库备份保留期内的任意时间点。

## <a name="backup-and-restore"></a>备份和还原

备份是文件快照库，因此它们几乎是瞬时完成的。 存储和计算分离可将备份/还原操作推送到存储层，以减少主要计算副本的处理负担。 因此，数据库备份不会影响主计算节点的性能。 同样，时间点还原 (PITR) 是通过还原到文件快照来完成的，因此规模不像数据操作那样大。 还原同一 Azure 区域中的超大规模数据库是一项时间恒定的操作，即使是若干 TB 的数据库也能在数分钟内还原，而无需几个小时甚至几天。 通过还原现有备份创建新数据库的过程也利用此功能：创建数据库副本用于开发或测试目的，即使是数 TB 的数据库，也能在数分钟内创建完成。

有关超大规模数据库的异地还原，请参阅[将超大规模数据库还原到其他区域](#restoring-a-hyperscale-database-to-a-different-region)。

## <a name="scale-and-performance-advantages"></a>缩放和性能优势

超大规模体系结构能快速启动/关闭其他只读计算节点，拥有重要的读取扩展功能，还可以释放主计算节点，以满足更多写入请求。 此外，由于超大规模体系结构的共享存储体系结构，可快速纵向扩展/横向扩展计算节点。

## <a name="create-a-hyperscale-database"></a>创建“超大规模”数据库

可以使用 [Azure 门户](https://portal.azure.com)、[T-SQL](/sql/t-sql/statements/create-database-transact-sql)、[PowerShell](/powershell/module/azurerm.sql/new-azurermsqldatabase) 或 [CLI](/cli/azure/sql/db#az_sql_db_create) 创建超大规模数据库。 仅可通过[基于 vCore 的购买模型](service-tiers-vcore.md)使用超大规模数据库。

以下 T-SQL 命令可创建一个“超大规模”数据库。 必须在 `CREATE DATABASE` 语句中指定版本和服务目标。 有关有效服务目标的列表，请参阅[资源限制](./resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen4)。

```sql
-- Create a Hyperscale Database
CREATE DATABASE [HyperscaleDB1] (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

这会在配有 4 个核心的第 5 代硬件上创建一个超大规模数据库。

## <a name="upgrade-existing-database-to-hyperscale"></a>将现有数据库升级到超大规模

可以使用 [Azure 门户](https://portal.azure.com)、[T-SQL](/sql/t-sql/statements/alter-database-transact-sql)、[PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase) 或 [CLI](/cli/azure/sql/db#az_sql_db_update) 将 Azure SQL 数据库中的现有数据库迁移到“超大规模”层级。 目前，这是一种单向迁移。 只能导出和导入数据，而不能将“超大规模”中的数据库移到其他服务层级。 对于概念证明 (POC)，我们建议创建生产数据库的副本，并将该副本迁移到“超大规模”。 将 Azure SQL 数据库中的现有数据库迁移到“超大规模”层级是与数据大小相关的操作。

以下 T-SQL 命令可将数据库移动到“超大规模”服务层级。 必须在 `ALTER DATABASE` 语句中指定版本和服务目标。

```sql
-- Alter a database to make it a Hyperscale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

> [!NOTE]
> 要将作为[异地复制](active-geo-replication-overview.md)关系的一部分的数据库（作为主数据库或辅助数据库）移动到“超大规模”，必须停止复制。 [故障转移组](auto-failover-group-overview.md)中的数据库必须先从组中删除。
>
> 将数据库移动到“超大规模”后，你可以为该数据库创建新的“超大规模”异地副本。 超大规模的异地复制目前为预览版，具有一些[限制](active-geo-replication-overview.md)。

## <a name="database-high-availability-in-hyperscale"></a>“超大规模”中的数据库高可用性

与所有其他服务层级一样，“超大规模”保证已提交事务的数据持久性，而不管计算副本的可用性如何。 由于主要副本不可用而导致的停机时间取决于故障转移的类型（计划内或计划外），以及是否至少存在一个高可用性副本。 在计划内故障转移（例如维护事件）中，系统将在启动故障转移之前创建新的主要副本，或使用现有的高可用性副本作为故障转移目标。 在计划外故障转移（例如，主要副本发生硬件故障）中，系统使用高可用性副本（如果存在）作为故障转移目标，或者在可用计算容量池中创建新的主要副本。 对于后一种情况，停机持续时间更长，因为需要执行额外的步骤来创建新的主要副本。

有关“超大规模”SLA，请参阅 [Azure SQL 数据库的 SLA](https://azure.microsoft.com/support/legal/sla/azure-sql-database)。

## <a name="disaster-recovery-for-hyperscale-databases"></a>超大规模数据库的灾难恢复

### <a name="restoring-a-hyperscale-database-to-a-different-region"></a>将超大规模数据库还原到其他区域

如果在执行灾难恢复操作或演练、重新定位期间或者出于任何其他原因，需要将 Azure SQL 数据库中的某个超大规模数据库还原到其他位置（而不是其当前所在位置），主要方法是执行数据库的异地还原。 异地还原所涉及的步骤与将 SQL 数据库中任何其他数据库还原到其他区域的步骤完全相同：

1. 如果目标区域中没有适当的服务器，请在其中创建一个[服务器](logical-servers.md)。  此服务器应该由原始（源）服务器的同一个订阅拥有。
2. 请遵照有关从自动备份还原 Azure SQL 数据库中数据库的页面上的[异地还原](./recovery-using-backups.md#geo-restore)主题中的说明操作。

> [!NOTE]
> 由于源与目标位于不同的区域，因此数据库无法像在非异地还原方案中一样与源数据库共享快照存储，而非异地还原可以快速完成，无论数据库大小如何。 异地还原超大规模数据库属于一种与数据大小相关的操作，即使目标位于异地复制存储的配对区域中。 因此，异地还原需要的时间与所要还原的数据库的大小成正比。 如果目标位于配对区域中，数据传输将会位于某个区域内，这将会比跨区域数据传输快得多，但它仍将是与数据大小相关的操作。

## <a name="available-regions"></a><a name=regions></a>可用区域

所有区域均提供 Azure SQL 数据库超大规模层级选项，但在下列区域中默认启用该层级。 如果要在默认未启用超大规模的区域中创建超大规模数据库，可以通过 Azure 门户发送加入请求。 相关说明，请参阅[请求增加 Azure SQL 数据库配额](quota-increase-request.md)。 提交请求时，请遵循以下准则：

- 使用[区域访问](quota-increase-request.md#region) SQL 数据库配额类型。
- 在说明中，添加计算 SKU/内核总数（包括高可用性副本和命名副本），并表明你正在请求超大规模容量。
- 还要以 TB 为单位指定一段时间内所有数据库总大小的推测值。

已启用区域：
- 澳大利亚东部
- 澳大利亚东南部
- 澳大利亚中部
- 巴西南部
- 加拿大中部
- 加拿大东部
- 美国中部
- 中国东部 2
- 中国北部 2
- 东亚
- 美国东部
- 美国东部 2
- 法国中部
- 德国中西部
- 日本东部
- 日本西部
- 韩国中部
- 韩国南部
- 美国中北部
- 北欧
- 挪威东部
- 挪威西部
- 南非北部
- 美国中南部
- 东南亚
- 瑞士西部
- 英国南部
- 英国西部
- US DoD 中部
- US DoD 东部
- Us Govt 亚利桑那州
- US Govt 德克萨斯州
- 美国中西部
- 西欧
- 美国西部
- 美国西部 2

## <a name="known-limitations"></a>已知的限制

下面是正式版发布之前“超大规模”服务层级的当前限制。  我们正在尽一切努力消除其中的许多限制。

| 问题 | 说明 |
| :---- | :--------- |
| 服务器的“管理备份”窗格不显示“超大规模”数据库。 视图中会将它们筛选掉。  | “超大规模”服务层级具有单独的备份管理方法，因此“长期保留”和“时间点备份保留”设置不适用。 相应地，“超大规模”数据库不会显示在“管理备份”窗格中。<br><br>对于从其他 Azure SQL 数据库服务层级迁移到超大规模的数据库，预迁移备份会在源数据库的[备份保持期](automated-backups-overview.md#backup-retention)持续时间内保留。 这些备份可以用于将源数据库[还原](recovery-using-backups.md#programmatic-recovery-using-automated-backups)到迁移之前的某个时间点。|
| 时间点还原 | 无法将非超大规模数据库还原到超大规模数据库，也无法将超大规模数据库还原到非超大规模数据库。 对于已通过更改服务层级迁移到“超大规模”层级的非超大规模数据库，支持[以编程方式](recovery-using-backups.md#programmatic-recovery-using-automated-backups)还原到迁移之前且在数据库备份保留期内的某个时间点。 还原后的数据库是非超大规模的。 |
| 在将 Azure SQL 数据库服务层级更改为“超大规模”时，如果数据库中有任何大于 1 TB 的数据文件，更改操作就会失败 | 有些情况下，在尝试将服务层级更改为“超大规模”之前，先将大文件[收缩](file-space-manage.md#shrinking-data-files)至 1 TB 以下，或许可以解决此问题。 使用以下查询来确定数据库文件的当前大小。 `SELECT file_id, name AS file_name, size * 8. / 1024 / 1024 AS file_size_GB FROM sys.database_files WHERE type_desc = 'ROWS'`;|
| SQL 托管实例 | 超大规模数据库目前不支持 Azure SQL 托管实例。 |
| 弹性池 |  超大规模目前不支持弹性池。|
| 迁移到“超大规模”服务层级目前是单向操作 | 将数据库迁移到“超大规模”层级后，它不能直接迁移到非“超大规模”服务层级。 目前，将数据库从“超大规模”迁移到非“超大规模”的唯一方法是，使用 bacpac 文件或其他数据移动技术（大容量复制、Azure 数据工厂、Azure Databricks、SSIS 等）进行导出/导入。不支持从 Azure 门户、PowerShell（使用 [New-AzSqlDatabaseExport](/powershell/module/az.sql/new-azsqldatabaseexport) 或 [New-AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport)）、Azure CLI（使用 [az sql db export](/cli/azure/sql/db#az_sql_db_export) 和 [az sql db import](/cli/azure/sql/db#az_sql_db_import)）以及从 [REST API](/rest/api/sql/) 进行 bacpac 导出/导入。 支持使用 SSMS 和 [SqlPackage](/sql/tools/sqlpackage) 版本 18.4 及更高版本对较小的超大规模数据库（最多 200 GB）进行 bacpac 导入/导出。 对于较大的数据库，bacpac 导出/导入可能需要很长时间，并且可能会因各种原因失败。|
| 迁移包含内存中 OLTP 对象的数据库 | 超大规模支持内存中 OLTP 对象的子集，包括内存优化表类型、表变量和本机编译模块。 但是，如果要迁移的数据库中存在任何类型的内存中 OLTP 对象，则不支持从“高级”和“业务关键”服务层级迁移到“超大规模”。 若要将此类数据库迁移到“超大规模”，必须删除所有内存中 OLTP 对象及其依赖项。 迁移数据库之后，可以重新创建这些对象。 “超大规模”目前不支持持久的和非持久的内存优化表，必须将这些表更改为为磁盘表。|
| 异地复制  | 超大规模上的[异地复制](active-geo-replication-overview.md)现为公共预览版。 |
| 智能数据库功能 | 除了“强制计划”选项外，所有其他“自动优化”选项在“超大规模”中尚不受支持：这些选项可能看上去已启用，但不会提出任何建议或执行任何操作。 |
| 查询性能见解 | “超大规模”数据库目前不支持 Query Performance Insights。 |
| 收缩数据库 | “超大规模”数据库目前不支持 DBCC SHRINKDATABASE 或 DBCC SHRINKFILE。 |
| 数据库完整性检查 | “超大规模”数据库目前不支持 DBCC CHECKDB。 可使用 DBCC CHECKFILEGROUP 和 DBCC CHECKTABLE 作为解决方法。 有关 Azure SQL 数据库中数据完整性管理的详细信息，请参阅 [Azure SQL 数据库中的数据完整性](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)。 |
| 弹性作业 | 不支持使用超大规模数据库作为作业数据库。 但是，弹性作业可将超大规模数据库用作目标，就如同将任何其他 Azure SQL 数据库用作目标一样。 |

## <a name="next-steps"></a>后续步骤

- 有关“超大规模”的常见问题，请参阅[“超大规模”常见问题解答](service-tier-hyperscale-frequently-asked-questions-faq.yml)。
- 有关服务层级的信息，请参阅[服务层级](purchasing-models.md)
- 有关服务器和订阅级别限制的信息，请参阅[服务器上的资源限制概述](resource-limits-logical-server.md)。
- 有关单一数据库的购买模型限制的信息，请参阅 [适用于单一数据库的 Azure SQL 数据库基于 vCore 的购买模型限制](resource-limits-vcore-single-databases.md)。
- 有关功能和比较列表，请参阅 [SQL 常用功能](features-comparison.md)。
