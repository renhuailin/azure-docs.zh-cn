---
title: Azure 中逻辑服务器的资源限制
description: 本文概述了 Azure SQL 数据库和 Azure Synapse Analytics 所使用的 Azure 中逻辑服务器的资源限制。 它还提供了有关在达到或超过这些资源限制时会发生什么情况的信息。
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: sashan,moslake,josack
ms.date: 02/02/2021
ms.openlocfilehash: 34613633b6b27fc3387e6a9fa63caf4a194ba963
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "101691223"
---
# <a name="resource-limits-for-azure-sql-database-and-azure-synapse-analytics-servers"></a>Azure SQL 数据库和 Azure Synapse Analytics 服务器的资源限制
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

本文概述了 Azure SQL 数据库和 Azure Synapse Analytics 所使用的逻辑服务器的资源限制。 还提供了有关当达到或超过这些资源限制时会发生的情况的信息，并描述了用于执行这些限制的资源治理机制。

> [!NOTE]
> 有关 Azure SQL 托管实例限制，请参阅[托管实例的 SQL 数据库资源限制](../managed-instance/resource-limits.md)。

## <a name="maximum-resource-limits"></a>最大资源限制

| 资源 | 限制 |
| :--- | :--- |
| 每个服务器的数据库数 | 5000 |
| 任意区域中每个订阅的服务器默认数量 | 20 |
| 任意区域中每个订阅的服务器数上限 | 200 |  
| 每个服务器的 DTU/eDTU 配额 | 54,000 |  
| 每个服务器/实例的 vCore 配额 | 540 |
| 每个服务器的最大池数 | 受限于 DTU 或 vCore 数。 例如，如果每个池是 1000 个 DTU，则一个服务器可以支持 54 个池。|
|||

> [!IMPORTANT]
> 随着数据库的数量接近每个服务器的限制，可能出现以下情况：
>
> - 对主数据库运行查询的延迟增加。  这包括资源利用率统计信息的视图，如 sys.resource_stats。
> - 管理操作和呈现门户视点（涉及枚举服务器中的数据库）的延迟增加。

> [!NOTE]
> 若要获取更高的 DTU/eDTU 配额、vCore 配额或超过默认数量的服务器，请在 Azure 门户中提交新的支持请求。 有关详细信息，请参阅[请求增加 Azure SQL 数据库的配额](quota-increase-request.md)。

### <a name="storage-size"></a>存储大小

对于单一数据库资源存储大小，请参阅[基于 DTU 的资源限制](resource-limits-dtu-single-databases.md)或[基于 vCore 的资源限制](resource-limits-vcore-single-databases.md)，了解每个定价层的存储大小限制。

## <a name="what-happens-when-database-resource-limits-are-reached"></a>如果达到数据库资源限制，会发生什么？

### <a name="compute-cpu"></a>计算 CPU

当数据库计算 CPU 使用率变高时，查询延迟也会增加，查询甚至可能超时。在上述情况下，服务可能对查询排队，并在资源可用时向查询提供资源以用于执行。
计算使用率变高时，风险缓解选项包括：

- 提高数据库或弹性池的计算大小，为数据库提供更多计算资源。 请参阅[缩放单一数据库资源](single-database-scale.md)和[缩放弹性池资源](elastic-pool-scale.md)。
- 优化查询，以降低每个查询的 CPU 资源利用率。 有关详细信息，请参阅[查询优化/提示](performance-guidance.md#query-tuning-and-hinting)。

### <a name="storage"></a>存储

当使用的数据库空间到达上限时，将无法进行增加数据大小的数据库插入和更新操作，客户端会收到[错误消息](troubleshoot-common-errors-issues.md)。 SELECT 和 DELETE 语句不受影响。

空间使用率变高时，风险缓解选项包括：

- 提高数据库或弹性池的大小上限，或添加更多存储资源。 请参阅[缩放单一数据库资源](single-database-scale.md)和[缩放弹性池资源](elastic-pool-scale.md)。
- 如果数据库在弹性池内，可选择将数据库移出弹性池，从而避免与其他数据库共享存储空间。
- 收缩数据库来回收未使用的空间。 有关详细信息，请参阅[管理 Azure SQL 数据库中的文件空间](file-space-manage.md)。
- 检查高空间利用率是否是由永久性版本存储 (PVS) 大小的峰值所造成的。 PVS 是每个数据库的一部分，用于实现[加速数据库恢复](../accelerated-database-recovery.md)。 若要确定当前的 PVS 大小，请参阅 [PVS 故障排除](/sql/relational-databases/accelerated-database-recovery-management#troubleshooting)。 PVS 大小较大的常见原因是事务长时间（数小时）开放，从而无法清理 PVS 中较旧的版本。

### <a name="sessions-and-workers-requests"></a>会话和辅助角色（请求）

会话和辅助角色的数目上限由服务层级和计算大小（DTU/eDTU 或 vCore）决定。 当到达会话或辅助角色上限时，新的请求将被拒绝，客户端将收到错误消息。 虽然应用程序可以轻松地控制可用的连接数，但并行辅助角色数通常更难以估计和控制。 在负荷高峰期，当数据库资源达到上限，辅助角色由于查询运行时间较长、阻塞链大或查询并行度过高而堆积时，这种情况尤其突出。

会话或辅助角色使用率变高时，风险缓解选项包括：

- 提高数据库或弹性池的服务层级或计算大小。 请参阅[缩放单一数据库资源](single-database-scale.md)和[缩放弹性池资源](elastic-pool-scale.md)。
- 如果争用计算资源造成了辅助角色使用率上升，请优化查询，以降低每项查询的资源使用率。 有关详细信息，请参阅[查询优化/提示](performance-guidance.md#query-tuning-and-hinting)。
- 减小 [MAXDOP](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines)（最大并行度）设置。
- 优化查询工作负荷，以减少查询受阻的发生次数和持续时间。 有关详细信息，请参阅[了解并解决 Azure SQL 阻塞问题](understand-resolve-blocking.md)。

### <a name="memory"></a>内存

与其他资源（CPU、辅助角色、存储）不同，达到内存限制不会对查询性能产生负面影响，也不会导致错误和失败。 正如[内存管理体系结构指南](/sql/relational-databases/memory-management-architecture-guide)中详细描述的那样，SQL Server 数据库引擎通常使用所有可用内存，这是设计使然。 内存主要用于缓存数据，以避免更昂贵的存储访问。 因此，较高的内存利用率通常会提高查询性能，因为从内存中读取的速度更快，而从存储中读取的速度更慢。

在数据库引擎启动之后，当工作负载开始从存储中读取数据时，数据库引擎会积极地在内存中缓存数据。 经过这一初始增长期之后，通常可看到 [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 中的 `avg_memory_usage_percent` 和 `avg_instance_memory_percent` 列接近或等于 100%，尤其是对于非空闲且不能完全装入内存的数据库。

除了数据缓存之外，内存还用于数据库引擎的其他组件。 当有内存需求且所有可用内存已被数据缓存占用时，数据库引擎将动态收缩数据缓存大小以使内存可供其他组件使用，并在其他组件释放内存时动态增加数据缓存。

在极少数情况下，要求十分高的工作负载可能会导致内存不足，从而导致内存不足错误。 这可能发生在内存使用率介于 0% 和 100% 之间的任何级别。 这更有可能发生在具有比例较小的内存限制的较小计算大小和/或使用更多内存进行查询处理的工作负载上，例如在[密集的弹性池](elastic-pool-resource-management.md)中。

遇到内存不足错误时，缓解选项包括：
- 提高数据库或弹性池的服务层级或计算大小。 请参阅[缩放单一数据库资源](single-database-scale.md)和[缩放弹性池资源](elastic-pool-scale.md)。
- 优化查询和配置以降低内存使用率。 下表中介绍了常见的解决方案。

|解决方案|说明|
| :----- | :----- |
|减少内存授予的大小|有关内存授予的详细信息，请参阅[了解 SQL Server 内存授予](https://techcommunity.microsoft.com/t5/sql-server/understanding-sql-server-memory-grant/ba-p/383595)博客文章。 避免过大内存授予的一个常见解决方案是使[统计信息](/sql/relational-databases/statistics/statistics)保持最新状态。 这可使查询引擎更准确地估计内存消耗，从而避免内存授予过大。</br></br>在使用兼容级别 140 及更高级别的数据库中，数据库引擎可使用[批处理模式内存授予反馈](/sql/relational-databases/performance/intelligent-query-processing#batch-mode-memory-grant-feedback)自动调整内存授予大小。 在使用兼容级别 150 及更高级别的数据库中，数据库引擎类似地使用[行模式内存授予反馈](/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback)，用于更常见的行模式查询。 此内置功能有助于避免由于内存授予过大而导致的内存不足错误。|
|减小查询计划缓存的大小|数据库引擎在内存中缓存查询计划，以避免为每次查询执行编译查询计划。 若要避免由于仅使用一次的缓存计划而导致的查询计划缓存膨胀，请启用 OPTIMIZE_FOR_AD_HOC_WORKLOADS [数据库范围内的配置](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)。|
|减小锁定内存的大小|数据库引擎将内存用于[锁定](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#Lock_Engine)。 如果可能，请避免可能获取大量锁定并导致高锁定内存消耗的大型事务。|


## <a name="resource-consumption-by-user-workloads-and-internal-processes"></a>用户工作负荷和内部进程的资源消耗量

将在 [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 和 [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) 视图的 `avg_cpu_percent` 和 `avg_memory_usage_percent` 列中报告每个数据库中的用户工作负荷的 CPU 和内存消耗量。 对于弹性池，将在 [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) 视图中报告池级别的资源消耗量。 对于池级别的[单一数据库](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserversdatabases)和[弹性池](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools)，还会通过 Azure Monitor 指标 `cpu_percent` 报告用户工作负荷的 CPU 消耗量。

Azure SQL 数据库需要使用计算资源来实现核心服务功能，例如高可用性和灾难恢复、数据库备份和还原、监视、查询存储、自动优化，等等。对于这些内部进程，系统会使用[资源治理](#resource-governance)机制从总体资源中为其留出有限的一部分特定资源，使剩余的资源可供用户工作负载使用。 当内部进程不使用计算资源时，系统会将其提供给用户工作负载使用。

将在 [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 和 [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) 视图的 `avg_instance_cpu_percent` 和 `avg_instance_memory_percent` 列中报告用户工作负载和内部进程的总 CPU 和内存消耗量。 对于池级别的[单一数据库](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserversdatabases)和[弹性池](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools)，还会通过 Azure Monitor 指标 `sqlserver_process_core_percent` 和 `sqlserver_process_memory_percent` 报告此数据。

[sys.dm_resource_governor_resource_pools_history_ex](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database) 和 [sys.dm_resource_governor_workload_groups_history_ex](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-history-ex-azure-sql-database) 视图报告了用户工作负载和内部流程最近资源消耗的更详细信息。 有关这些视图中提及的资源池和工作负载组的详细信息，请参阅[资源治理](#resource-governance)。 这些视图报告了相关资源池和工作负载组中用户工作负载和特定内部流程的资源利用情况。

在性能监视和故障排除上下文中，必须考虑用户 CPU 消耗量（`avg_cpu_percent`、`cpu_percent`），以及用户工作负载和内部进程的 CPU 总消耗量（`avg_instance_cpu_percent`、`sqlserver_process_core_percent`） 。

用户 CPU 消耗量的计算值为每个服务目标中用户工作负荷限制的一个百分比。 用户 CPU 利用率为 100% 表示用户工作负荷达到了服务目标的限制。 但是，当 CPU 总消耗量达到 70-100% 范围时，即使报告的用户 CPU 消耗量明显低于 100%，也可能会看到用户工作负载吞吐量保持平稳，但查询延迟增大 。 对适度分配的计算资源但相对密集的用户工作负载（例如在[密集弹性池](elastic-pool-resource-management.md)中）使用较小的服务目标时，更有可能会发生这种情况。 当内部进程临时需要更多的资源时（例如，在创建数据库的新副本时），使用较小的服务目标也可能发生这种情况。

当 CPU 总消耗量较高时，缓解措施与前面所述相同，也包括增大服务目标和/或优化用户工作负载。

## <a name="resource-governance"></a>资源调控

为了强制实施资源限制，Azure SQL 数据库使用基于 SQL Server [Resource Governor](/sql/relational-databases/resource-governor/resource-governor)、经过修改和扩展的资源治理实现在 Azure SQL 数据库中运行。 在 SQL 数据库中，多个[资源池](/sql/relational-databases/resource-governor/resource-governor-resource-pool)和[工作负载组](/sql/relational-databases/resource-governor/resource-governor-workload-group)以及在池和组级别设置的资源限制提供了[均衡的数据库即服务](https://azure.microsoft.com/blog/resource-governance-in-azure-sql-database/)。 用户的工作负载和内部工作负载分为单独的资源池和工作负载组。 主副本和可读次要副本（包括地理副本）上的用户工作负载分为 `SloSharedPool1` 资源库和 `UserPrimaryGroup.DBId[N]` 工作负载组，其中 `N` 代表数据库 ID 值。 此外，还有多个资源库和各种内部工作负载的工作负载组。

除使用 Resource Governor 来管控 SQL 进程中的资源外，Azure SQL 数据库还使用 Windows [作业对象](/windows/win32/procthread/job-objects)进行流程级资源治理，并使用 Windows [文件服务器资源管理器](/windows-server/storage/fsrm/fsrm-overview) (FSRM) 来存储配额管理。

Azure SQL 数据库资源治理本质上是分层的。 自上而下，使用操作系统资源治理机制和 Resource Governor 在 OS 级别和存储卷级别执行限制，然后使用 Resource Governor 在资源池级别执行限制，再使用 Resource Governor 在工作负载组级别执行限制。 [sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) 视图中显示当前数据库或弹性池的资源治理限制。

### <a name="data-io-governance"></a>数据 IO 治理

数据 IO 治理是 Azure SQL 数据库中的一个过程，用于限制对数据库数据文件的读取和写入物理 IO。 为每个服务级别设置 IOPS 限制，以最大限度地减少“邻近干扰”效果，在多租户服务中提供资源分配公平性，并保持在基础硬件和存储功能范围内。

对于单个数据库，工作负载组限制适用于针对数据库的所有存储 IO，而资源池限制适用于同一专用 SQL 池中的所有数据库，包括 `tempdb` 数据库。 对于弹性池，工作负载组限制适用于池中的每个数据库，而资源池限制适用于整个弹性池，包括池中所有数据库共享的 `tempdb` 数据库。 一般来说，由于工作负载组限制低于资源池限制，并且限制 IOPS/吞吐量的速度更快，因此无法通过针对数据库的工作负载（单一或公用）实现资源池限制。 但是，可通过对同一池中的多个数据库的组合工作负载来达到池限制。

例如，如果查询在没有任何 IO 资源治理的情况下生成 1000 IOPS，但工作负载组的最大 IOPS 限制设置为 900 IOPS，则该查询将无法生成超过 900 的 IOPS。 但是，如果资源池的最大 IOPS 限制设置为 1500 IOPS，并且与资源池关联的所有工作负载组的总 IO 超过 1500 IOPS，则同一查询的 IO 可能会降低到 900 IOPS 的工作组限制以下。

[sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) 视图返回的 IOPS 和吞吐量最小/最大值作为限制/上限，而不是保证。 而且，资源治理并不保证任何特定的存储延迟。 给定用户工作负载的最佳可实现延迟、IOPS 和吞吐量不仅取决于 IO 资源治理限制，还取决于所使用的 IO 大小组合以及基础存储的功能。 SQL 数据库使用的 IO 大小在 512 KB 和 4 MB 之间变化。 为了执行 IOPS 限制，除 Azure 存储中包含的数据文件的数据库外，每个 IO 都会被计入，无论其大小如何。 在这种情况下，大于 256 KB 的 IO 计为多个 256-KB IO，以符合 Azure 存储 IO 数据记录。

对于在 Azure 存储中使用数据文件的基础、标准和通用数据库，如果数据库没有足够的数据文件来累计提供此数量的 IOPS，或者如果数据没有在文件之间均匀分布，再或者基础 Blob 的性能层将 IOPS/吞吐量限制到低于资源治理限制，则可能无法实现 `primary_group_max_io` 值。 同样，对于因频繁事务提交而产生的小型日志 IO，由于存在基础 Azure 存储 Blob 的 IOPS 限制，可能无法通过工作负载实现 `primary_max_log_rate` 值。 对于使用 Azure 高级存储的数据库，Azure SQL 数据库使用足够大的存储 Blob 来获取所需的 IOPS/吞吐量，无论数据库大小如何。 对于较大的数据库，将创建多个数据文件，以提高 IOPS/吞吐量总容量。

资源利用率值（如 [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)、[sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) 和 [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) 视图中报告的 `avg_data_io_percent` 和 `avg_log_write_percent`），被计为最大值资源治理限制的百分比。 因此，当资源治理以外的因素限制 IOPS/吞吐量时，即使报告的资源利用率仍低于 100%，IOPS/吞吐量仍可能随着工作负载的增加而趋于平缓和延迟增加。

若要查看每个数据库文件的读取和写入 IOPS、吞吐量和延迟，请使用 [sys.dm_io_virtual_file_stats()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) 函数。 此函数将呈现针对数据库的所有 IO，包括后台 IO，此 IO 没有计入 `avg_data_io_percent`，但使用基础存储的 IOPS 和吞吐量并可能会影响观测到的存储延迟。 该函数分别在 `io_stall_queued_read_ms` 列和 `io_stall_queued_write_ms` 列中显示 IO 资源治理可能引入的读取和写入额外延迟。

### <a name="transaction-log-rate-governance"></a>事务日志速率调控

事务日志速率调控是 Azure SQL 数据库中的一个进程，用于限制批量插入、SELECT INTO 和索引生成等工作负荷的高引入速率。 无论针对数据文件发出多少 IO，系统都会对日志记录生成速率跟踪并实施这些限制，使其保持在亚秒级以下，并限制吞吐量。  目前，事务日志生成速率会线性提高到与硬件相关的临界点，在 vCore 购买模型中，允许的最大日志速率为 96 MB/秒。

> [!NOTE]
> 向事务日志文件发出的实际物理 IO 不会受到调控或限制。

日志速率的设置应该做到可在各种场合下实现并保持该速率，同时，整个系统可以在尽量减轻对用户负载造成的影响的前提下保持其功能。 日志速率调控可确保事务日志备份保留在已发布的可恢复性 SLA 范围内。  这种调控还可以防止次要副本带来过多的积压工作。

生成日志记录后，将评估每个操作，以确定是否要将其延迟，从而保持最大所需日志速率（MB/秒）。 将日志记录刷新到存储时不会增大延迟，日志速率治理是在日志速率生成期间应用的。

在运行时实施的实际日志生成速率还可能受到反馈机制（暂时降低允许的日志速率，使系统保持稳定）的影响。 日志文件空间管理可避免遇到日志空间不间的情况，可用性组复制机制可以暂时降低总体系统限制。

可通过以下 wait 类型（在 [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) 和 [sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql) 视图中公开）查看日志速率调控器流量的形状：

| Wait 类型 | 说明 |
| :--- | :--- |
| LOG_RATE_GOVERNOR | 数据库限制 |
| POOL_LOG_RATE_GOVERNOR | 池限制 |
| INSTANCE_LOG_RATE_GOVERNOR | 实例级限制 |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | 反馈控制。高级/业务关键型工作负荷中的可用性组物理复制不会保持 |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | 反馈控制。限制速率可以避免出现日志空间不足的情况 |
| HADR_THROTTLE_LOG_RATE_MISMATCHED_SLO | 地理复制反馈控制、限制日志速率，以避免高数据延迟和地理辅助系统不可用|
|||

当日志速率限制阻碍实现所需的可伸缩性时，请考虑以下选项：

- 纵向扩展到更高的服务级别，以获得 96 MB/秒的最大日志速率，或切换到不同的服务层。 无论选择的服务级别如何，[超大规模](service-tier-hyperscale.md)服务层均提供 100 MB/s 日志速率。
- 如果加载的数据是暂时性的（例如 ETL 过程中的暂存数据），可将其载入 tempdb（记录最少量的数据）。
- 对于分析方案，可将数据载入聚集列存储涵盖的表中。 这样，可以通过压缩来降低所需的日志速率。 此方法确实会增大 CPU 利用率，并且仅适用于可从聚集列存储索引受益的数据集。

### <a name="storage-space-governance"></a>存储空间治理

在高级和业务关键服务层中，数据和事务日志文件存储在托管数据库或弹性池的计算机的本地 SSD 卷上。 这会提供高 IOPS 和吞吐量，以及低 IO 延迟。 此本地卷的大小取决于硬件功能，并且是有限的。 在给定计算机上，客户数据库（包括 `tempdb`、操作系统、管理软件、监控数据、日志等）消耗了本地卷空间。随着数据库的创建、删除和空间使用量的增加/减少，计算机上的本地空间消耗会随着时间而波动。 

如果系统检测到计算机上的可用自由空间较少，并且数据库或弹性池有耗尽空间的风险，它会将数据库或弹性池移动到具有足够自由空间的不同计算机，从而允许增长到配置服务目标的最大大小限制。 此移动以联机方式发生，类似于数据库缩放操作，并具有类似的[影响](single-database-scale.md#impact)，包括操作结束时的短暂（几秒）故障转移。 此故障转移会终止打开的连接并回退事务，这可能会影响当时使用数据库的应用程序。

由于数据被物理复制到其他计算机中，移动较大的数据库可能需要大量时间。 在此期间，如果大型用户数据库或弹性池或 `tempdb` 数据库的本地空间消耗增长非常快，则空间耗尽的风险会增加。 该系统以平衡的方式启动数据库移动，以防止空间耗尽错误，并避免不必要的故障转移。

## <a name="next-steps"></a>后续步骤

- 有关常规 Azure 限制的相关信息，请参阅 [Azure 订阅和服务限制、配额和约束](../../azure-resource-manager/management/azure-subscription-service-limits.md)。
- 有关 DTU 和 eDTU 的信息，请参阅 [DTU 和 eDTU](purchasing-models.md#dtu-based-purchasing-model)。
- 有关 tempdb 大小限制的信息，请参阅 [Azure SQL 数据库中的 TempDB](/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database)。