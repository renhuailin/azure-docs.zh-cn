---
title: 密集弹性池中的资源管理
description: 管理包含多个数据库的 Azure SQL 数据库弹性池中的计算资源。
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: mathoma
ms.date: 09/16/2020
ms.openlocfilehash: 48d037e4fe18f214af0f5ecaf9eb4e9b7e3ed59e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121735581"
---
# <a name="resource-management-in-dense-elastic-pools"></a>密集弹性池中的资源管理
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL 数据库[弹性池](./elastic-pool-overview.md)是一种经济高效的解决方案，用于管理资源使用情况各不相同的多个数据库。 弹性池中的所有数据库共享相同的资源（例如，CPU、内存、工作线程、存储空间、tempdb）分配，这里的假设是：在任何给定时间，该池中只有一部分数据库会使用计算资源。 只有符合该假设，弹性池才会既经济又高效。 客户无需为每个数据库可能需要的所有资源付费，只需为在池中所有数据库之间共享的一组小得多的资源付费。

## <a name="resource-governance"></a>资源调控

资源共享要求系统严格控制资源使用情况，以最大程度地降低“邻近干扰”影响，该影响是指资源消耗较高的数据库会影响同一弹性池中的其他数据库。 同时，系统必须为高可用性和灾难恢复 (HADR)、备份和还原、监视、查询存储、自动优化等功能提供充足的资源，否则这些功能无法可靠地运行。

Azure SQL 数据库使用多种资源治理机制实现这些目标，这些机制包括：用于流程级资源治理的 Windows [作业对象](/windows/win32/procthread/job-objects)、用于存储配额管理的 Windows [文件服务器资源管理器 (FSRM)](/windows-server/storage/fsrm/fsrm-overview)，以及用于在 SQL 数据库中实施资源治理的 SQL Server [Resource Governor](/sql/relational-databases/resource-governor/resource-governor) 的已修改和已扩展版本。

弹性池的主要设计目标是确保经济高效。 出于这个原因，系统有意允许客户创建密集池，这些池的数据库的数目接近或达到允许的最大数目，但只进行了中等程度的计算资源分配。 出于同一原因，系统不会为其内部进程保留所有可能需要的资源，但允许在内部进程和用户工作负荷之间进行资源共享。

此方法使客户可以使用密集弹性池来获得充足的性能，同时可以显著节约成本。 但是，如果针对密集池中的多个数据库的工作负荷足够大，资源争用就会变得很明显。 资源争用会降低用户工作负荷性能，并且可能会对内部进程产生负面影响。

> [!IMPORTANT]
> 在具有多个活动数据库的密集池中，将池中的数据库数增加到针对 [DTU](resource-limits-dtu-elastic-pools.md) 和 [vCore](resource-limits-vcore-elastic-pools.md) 弹性池记录的最大值可能不可行。
>
> 可以放置在密集池中且不会导致资源争用和性能问题的数据库数取决于并发活动数据库的数量，以及每个数据库中用户工作负荷的资源消耗情况。 此数字可能会随着一段时间内用户工作负荷的变化而变化。

当进行密集打包的池中发生资源争用时，客户可以选择以下一项或多项操作来缓解它：

- 优化查询工作负荷，以减少一段时间内的资源消耗或将资源消耗分散到多个数据库。
- 通过将一些数据库移到另一个池或使其成为独立数据库，来降低池密度。
- 纵向扩展池以获取更多资源。

有关如何实现最后两个操作的建议，请参阅本文后面的[操作建议](#operational-recommendations)。 减少资源争用会使用户工作负荷和内部进程受益，并使系统能够可靠地保持预期的服务级别。

## <a name="monitoring-resource-consumption"></a>监视资源消耗情况

若要避免由于资源争用引起的性能降低，使用密集弹性池的客户应主动监视资源消耗情况，在资源争用越来越严重并开始影响工作负荷时及时采取措施。 持续监视很重要，因为池中的资源使用情况会随时间推移而变化，而资源使用情况之所以会变化，是因为用户工作负荷会变化、数据的量和分布会变化、池密度会变化，以及 Azure SQL 数据库服务会变化。

Azure SQL 数据库提供了多个与这种类型的监视相关的指标。 超过为每个指标建议的平均值表示池中存在资源争用，应使用前面提到的其中一项操作解决此问题。

|指标名称|说明|建议的平均值|
|----------|--------------------------------|------------|
|`avg_instance_cpu_percent`|与弹性池关联的 SQL 进程的 CPU 利用率，由基础操作系统度量。 在每个数据库的 [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 视图中以及 `master` 数据库的 [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) 视图中提供。 此指标还会发出到 Azure Monitor，它在其中是[命名的](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools) `sqlserver_process_core_percent`，可在 Azure 门户中查看。 此值对于同一弹性池中的每个数据库都是相同的。|低于 70%。 偶尔且短暂地出现高达 90% 的情况是可以接受的。|
|`max_worker_percent`|[工作线程](/sql/relational-databases/thread-and-task-architecture-guide)利用率。 为池中的每个数据库提供，以及为池本身提供。 对工作线程数的限制在数据库级别和池级别是不同的，因此建议同时在这两个级别监视此指标。 在每个数据库的 [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 视图中以及 `master` 数据库的 [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) 视图中提供。 此指标还会发出到 Azure Monitor，它在其中是[命名的](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools) `workers_percent`，可在 Azure 门户中查看。|低于 80%。 峰值高达 100% 会导致连接尝试和查询失败。|
|`avg_data_io_percent`|读取和写入物理 IO 的 IOPS 利用率。 为池中的每个数据库提供，以及为池本身提供。 对 IOPS 数的限制在数据库级别和池级别是不同的，因此建议同时在这两个级别监视此指标。 在每个数据库的 [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 视图中以及 `master` 数据库的 [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) 视图中提供。 此指标还会发出到 Azure Monitor，它在其中是[命名的](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools) `physical_data_read_percent`，可在 Azure 门户中查看。|低于 80%。 偶尔且短暂地出现高达 100% 的情况是可以接受的。|
|`avg_log_write_percent`|事务日志写入 IO 的吞吐量利用率。 为池中的每个数据库提供，以及为池本身提供。 对日志吞吐量的限制在数据库级别和池级别是不同的，因此建议同时在这两个级别监视此指标。 在每个数据库的 [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 视图中以及 `master` 数据库的 [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) 视图中提供。 此指标还会发出到 Azure Monitor，它在其中是[命名的](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools) `log_write_percent`，可在 Azure 门户中查看。 此指标接近 100% 时，所有数据库修改（INSERT、UPDATE、DELETE、MERGE 语句，SELECT … INTO，BULK INSERT 等）都会变慢。|低于 90%。 偶尔且短暂地出现高达 100% 的情况是可以接受的。|
|`oom_per_second`|弹性池中的内存不足 (OOM) 错误率，这是内存压力指标。 在 [sys.dm_resource_governor_resource_pools_history_ex](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database) 视图中提供。 请参阅[示例](#examples)，其中提供了一个用于计算此指标的示例查询。|0|
|`avg_storage_percent`|弹性池内所有数据库中的数据所用的总存储空间。 不包括数据库文件中的空白区域。 在 `master` 数据库的 [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) 视图中提供。 此指标还会发出到 Azure Monitor，它在其中是[命名的](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools) `storage_percent`，可在 Azure 门户中查看。|低于 80%。 对于没有数据增长的池，可以接近 100%。|
|`avg_allocated_storage_percent`|弹性池内所有数据库的存储中的数据库文件所用的总存储空间。 包括数据库文件中的空白区域。 在 `master` 数据库的 [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) 视图中提供。 此指标还会发出到 Azure Monitor，它在其中是[命名的](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools) `allocated_data_storage_percent`，可在 Azure 门户中查看。|低于 90%。 对于没有数据增长的池，可以接近 100%。|
|`tempdb_log_used_percent`|`tempdb` 数据库中的事务日志空间利用率。 即使某个数据库中创建的临时对象在同一弹性池的其他数据库中不可见，`tempdb` 也是同一池中所有数据库的共享资源。 在 `tempdb` 中，从池中的一个数据库启动的长时间运行的或孤立的事务可能会消耗大量的事务日志，导致同一池的其他数据库中的查询失败。 从 [sys.dm_db_log_space_usage](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-log-space-usage-transact-sql) 和 [sys.database_files](/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql) 视图派生。 此指标还会发出到 Azure Monitor，可在 Azure 门户中查看。 请参阅[示例](#examples)，其中提供了一个会返回此指标的当前值的示例查询。|低于 50%。 偶尔出现高达 80% 的情况是可以接受的。|
|||

除了这些指标以外，Azure SQL 数据库还提供一个返回实际资源治理限制的视图，以及提供在资源池级别和工作负荷组级别返回资源利用率统计信息的其他视图。

|视图名称|说明|  
|-----------------|--------------------------------|  
|[sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database)|返回当前数据库或弹性池中的资源治理机制所使用的实际配置和容量设置。|
|[sys.dm_resource_governor_resource_pools](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-transact-sql)|返回的信息涉及当前资源池状态、资源池的当前配置以及资源池累积统计信息。|
|[sys.dm_resource_governor_workload_groups](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-transact-sql)|返回累积的工作负荷组统计信息和工作负荷组的当前配置。 此视图可以与 `pool_id` 列上的 sys.dm_resource_governor_resource_pools 联接以获取资源池信息。|
|sys.dm_resource_governor_resource_pools_history_ex|返回过去 32 分钟的资源池利用率统计信息。 每一行都表示一个 20 秒的时间间隔。 `delta_` 列返回时间间隔内每项统计信息中的更改。|
|sys.dm_resource_governor_workload_groups_history_ex|返回过去 32 分钟的工作负荷组利用率统计信息。 每一行都表示一个 20 秒的时间间隔。 `delta_` 列返回时间间隔内每项统计信息中的更改。|
|||

这些视图可用于监视资源利用率，以及以准实时方式排查资源争用问题。 主副本和可读次要副本（包括地理副本）上的用户工作负荷分为 `SloSharedPool1` 资源池和 `UserPrimaryGroup.DBId[N]` 工作负荷组，其中的 `N` 代表数据库 ID 值。

除了监视当前资源利用率外，使用密集池的客户还可以在单独的数据存储中维护历史资源利用率数据。 可在预测分析中使用此数据，根据历史趋势和季节性趋势主动管理资源利用率。

## <a name="operational-recommendations"></a>操作建议

**留出足够的资源空余空间**。 如果出现资源争用和性能降低的情况，则缓解操作可能涉及将一些数据库移出受影响的弹性池，或纵向扩展池，如前所述。 但是，这些操作需要额外的计算资源才能完成。 具体而言，对于高级池和业务关键池，这些操作需要传输要移动的数据库的所有数据，或者，如果池已纵向扩展，则需要为弹性池中的所有数据库传输所有数据。 数据传输是一项长时间运行且消耗大量资源的操作。 如果池的资源已经很紧张，则缓解操作本身会进一步降低性能。 在极端情况下，由于所需资源不可用，可能无法通过数据库移动或池纵向扩展来解决资源争用问题。 在这种情况下，在受影响的弹性池中暂时减少查询工作负荷可能是唯一的解决方案。

使用密集池的客户应密切监视资源利用率趋势（如前所述），并采取缓解措施，同时确保指标保持在建议范围内且弹性池中仍有足够的资源。

资源利用率取决于多个因素，这些因素会随着时间的推移针对每个数据库和每个弹性池发生变化。 在密集池中实现最佳的价格/性能比需要进行持续监视和重新平衡，即，将数据库从利用率较高的池移到利用率较低的池，并根据需要创建新的池来应对增加的工作负荷。

**不要移动“热”数据库**。 如果池级别的资源争用主要由少量利用率过高的数据库引起，则你可能很想将这些数据库移到利用率较低的池或使其成为独立数据库。 但是，不建议在数据库利用率仍很高的情况下这样做，因为移动操作会进一步降低性能，对于要移动的数据库和整个池来说都是如此。 与之相反，应等待高利用率下降，或者改为移动利用率较低的数据库以缓解池级别的资源压力。 但在这种情况下，移动利用率很低的数据库并不会带来任何好处，因为不会在池级别实质性降低资源利用率。

**在“隔离”池中创建新数据库**。 在频繁创建新数据库的情况下（例如，使用“每个数据库一个租户”模型的应用程序），会有这样的风险：存在于现有弹性池的新数据库会意外地消耗大量资源，影响池中的其他数据库和内部进程。 若要缓解这种风险，请创建一个分配有充足资源的单独“隔离”池。 将此池用于资源消耗模式仍未知的新数据库。 一旦某个数据库呆在此池中的时间达到某个业务周期（如一周或一个月），并且其资源消耗量已知，则可以将其移到具有足够容量的池，这样就可以应对该额外的资源使用情况。

**监视已使用和已分配的空间**。 当分配的池空间（池中所有数据库的存储中所有数据库文件的总大小）达到最大池大小时，可能会出现空间不足错误。 如果分配的空间存在越来越大的趋势，将达到最大池大小，则缓解选项包括：
- 将一些数据库移出池以减少分配的总空间
- [收缩](file-space-manage.md)数据库文件以减少文件中的空分配空间
- 将池纵向扩展到一个最大池大小更大的服务目标

如果已用池空间（池中所有数据库中的数据的总大小，不包括文件中的空余空间）存在越来越大的趋势，将达到最大池大小，则缓解选项包括：
- 将一些数据库移出池以减少使用的总空间
- 将数据移动（存档）到数据库外部，或删除不再需要的数据
- 实施[数据压缩](/sql/relational-databases/data-compression/data-compression)
- 将池纵向扩展到一个最大池大小更大的服务目标

**避免服务器过于密集**。 Azure SQL 数据库最多[支持](./resource-limits-logical-server.md)每个服务器 5000 个数据库。 如果客户使用具有成千上万个数据库的弹性池，则客户可以考虑将多个弹性池置于单个服务器上，数据库总数最高可以达到支持的限制。 但是，具有成千上万个数据库的服务器会带来运营挑战。 需要枚举服务器上的所有数据库的操作（例如，在门户中查看数据库）会变慢。 操作错误（例如对服务器级别登录名或防火墙规则进行了错误的修改）会影响更多的数据库。 意外删除服务器时，你需要 Microsoft 支持部门的帮助来恢复已删除服务器上的数据库，这会导致所有受影响的数据库出现长时间的服务中断情况。

建议对每个服务器的数据库数进行限制，使之小于系统支持的最大值。 在许多情况下，每个服务器最多使用 1000-2000 个数据库是最理想的。 为了降低意外删除服务器的可能性，建议在服务器或其资源组上放置[删除锁](../../azure-resource-manager/management/lock-resources.md)。

过去，在某些情况下，将数据库移进、移出同一服务器上的弹性池或在这些池之间移动数据库时，其速度要快于在服务器之间移动数据库。 目前，不管源服务器和目标服务器的情况如何，都将以相同的速度移动所有数据库。

## <a name="examples"></a>示例

### <a name="monitoring-memory-utilization"></a>监视内存利用率

此查询计算过去 32 分钟内每个资源池的 `oom_per_second` 指标。 可以在弹性池的任何数据库中执行此查询。

```sql
SELECT pool_id,
       name AS resource_pool_name,
       IIF(name LIKE 'SloSharedPool%' OR name LIKE 'UserPool%', 'user', 'system') AS resource_pool_type,
       SUM(CAST(delta_out_of_memory_count AS decimal))/(SUM(duration_ms)/1000.) AS oom_per_second
FROM sys.dm_resource_governor_resource_pools_history_ex
GROUP BY pool_id, name
ORDER BY pool_id;
```

### <a name="monitoring-tempdb-log-space-utilization"></a>监视 `tempdb` 日志空间利用率

此查询会返回 `tempdb_log_used_percent` 指标的当前值，显示 `tempdb` 事务日志相对于其最大允许大小的相对利用率。 可以在弹性池的任何数据库中执行此查询。

```sql
SELECT (lsu.used_log_space_in_bytes / df.log_max_size_bytes) * 100 AS tempdb_log_space_used_percent
FROM tempdb.sys.dm_db_log_space_usage AS lsu
CROSS JOIN (
           SELECT SUM(CAST(max_size AS bigint)) * 8 * 1024. AS log_max_size_bytes
           FROM tempdb.sys.database_files
           WHERE type_desc = N'LOG'
           ) AS df
;
```

## <a name="next-steps"></a>后续步骤

- 有关弹性池的简介，请参阅[弹性池有助于在 Azure SQL 数据库中管理和缩放多个数据库](./elastic-pool-overview.md)。
- 若要了解如何优化查询工作负荷以降低资源利用率，请参阅[监视和优化](monitoring-tuning-index.yml)与[监视和性能优化](./monitor-tune-overview.md)。
