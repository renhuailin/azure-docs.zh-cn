---
title: 如何使用 Azure Monitor 监视 Synapse Analytics
description: 了解如何使用 Azure Monitor 指标、警报和日志监视 Synapse Analytics 工作区
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: f495de13c20aa2953f55e2837fab15a6c62e1083
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466368"
---
# <a name="use-azure-monitor-with-your-azure-synapse-analytics-workspace"></a>将 Azure Monitor 与 Azure Synapse Analytics 工作区配合使用

云应用程序比较复杂，包含很多移动部件。 监视功能可以提供数据来确保应用程序始终处于正常运行状态。 监视功能还有助于避免潜在问题，或者排查以往的问题。 可以使用监视数据深入了解应用程序的情况。 了解这些情况有助于改进应用程序的性能或可维护性。 此外，还有助于实现本来需要手动干预的操作的自动化。

Azure Monitor 提供了大多数 Azure 服务的基本基础结构指标、警报和日志。 Azure 诊断日志由资源发出，提供与该资源的操作相关的各种频繁生成的数据。 Azure Synapse Analytics 可以 Azure Monitor 中写入诊断日志。

有关详细信息，请参阅 [Azure Monitor 概述](../../azure-monitor/overview.md)。

## <a name="metrics"></a>指标

使用 Monitor 可以洞察 Azure 工作负荷的性能与运行状况。 最重要的 Monitor 数据类型是指标（也称为性能计数器）。 大多数 Azure 资源都会发出指标。 Monitor 提供多种方式来配置和使用这些指标，以便进行监视与故障排除。

若要访问这些指标，请完成 [Azure Monitor 数据平台](../../azure-monitor/platform/data-platform.md)中的说明。

### <a name="workspace-level-metrics"></a>工作区级指标

下面是工作区发出的一些指标：

| **指标**                           | **度量值类别，显示名称**                  | **单位** | **聚合类型** | **说明**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| IntegrationActivityRunsEnded         | 集成，活动运行指标                     | 计数    | Sum (默认) ，计数                | 1分钟窗口内发生/结束的活动运行总数。 </br></br> 使用此度量值的 "结果" 维度，按成功、失败或已取消的最终状态进行筛选。|
| IntegrationPipelineRunsEnded         | 集成，管道运行指标                     | 计数    | Sum (默认) ，计数                | 1分钟窗口内发生/结束的管道运行总数。 </br></br> 使用此度量值的 "结果" 维度，按成功、失败或已取消的最终状态进行筛选。 |
| IntegrationTriggerRunsEnded          | 集成，触发器运行指标                      | 计数    | Sum (默认) ，计数                | 1分钟窗口内发生/结束的触发器运行总数。 </br></br> 使用此度量值的 "结果" 维度，按成功、失败或已取消的最终状态进行筛选。 |
| BuiltinSqlPoolDataProcessedBytes     | 内置 SQL 池，处理的数据 (字节)  | Byte | Sum (默认值)  | 内置无服务器 SQL 池处理的数据量。 |
| BuiltinSqlPoolLoginAttempts          | 内置 SQL 池，登录尝试 | 计数 | Sum (默认值)  | 内置无服务器 SQL 池的登录尝试次数。 |
| BuiltinSqlPoolDataRequestsEnded      | 内置 SQL 池，请求 (字节结束)  | 计数 | Sum (默认值)  | 为内置无服务器 SQL 池结束的 SQL 请求数。 </br></br> 使用此度量值的 "结果" 维度按最终状态进行筛选。 |

### <a name="dedicated-sql-pool-metrics"></a>专用 SQL 池指标

下面是专用 SQL 池发出的一些指标：

| **指标**                           | **显示名称**                  | **单位** | **聚合类型** | **说明**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| DWULimit                            | DWU 限制                       | 计数   | 最大 (默认值) 、Min、Avg | 已配置的 SQL 池大小 |
| DWUUsed                             | 已用的 DWU                        | 计数   | 最大 (默认值) 、Min、Avg | 整个 SQL 池中使用情况的高级表示形式。 按“DWU 限制”*“DWU 百分比”来度量 |
| DWUUsedPercent                      | DWU 使用百分比             | 百分比 | 最大 (默认值) 、Min、Avg | 整个 SQL 池中使用情况的高级表示形式。 通过选择 CPU 百分比与数据 IO 百分比这二者中的最大值来度量 |
| ConnectionsBlockedByFirewall        | 防火墙阻止的连接数 | 计数   | Sum (默认值)    | 防火墙规则阻止的连接计数。 重新访问 SQL 池的访问控制策略，并在计数较高时监视这些连接 |
| AdaptiveCacheHitPercent             | 自适应缓存命中百分比   | 百分比 | 最大 (默认值) 、Min、Avg | 度量工作负荷使用自适应缓存的效果。 将此指标与缓存命中百分比指标一起使用，以确定是要扩展以增加容量还是重新运行工作负荷以生成缓存 |
| AdaptiveCacheUsedPercent            | 自适应缓存使用百分比  | 百分比 | 最大 (默认值) 、Min、Avg | 度量工作负荷使用自适应缓存的效果。 将此指标与缓存已用百分比指标一起使用，以确定是要扩展以增加容量还是重新运行工作负荷以生成缓存 |
| LocalTempDBUsedPercent              | 本地 tempdb 已用百分比    | 百分比 | 最大 (默认值) 、Min、Avg | 所有计算节点上的本地 tempdb 利用率 - 每五分钟发出一次值 |
| MemoryUsedPercent                   | 已用内存百分比          | 百分比 | 最大 (默认值) 、Min、Avg | SQL 池中所有节点的内存使用率 |
| CPUPercent                          | CPU 已用百分比             | 百分比 | 最大 (默认值) 、Min、Avg | SQL 池中所有节点的 CPU 使用率 |
| 连接                         | 连接                     | 计数   | Sum (默认值)   | SQL 池的总登录数计数 |
| ActiveQueries                      | 活动查询数                 | 计数   | Sum (默认值)    | 活动的查询。 如果使用此指标时不进行筛选和拆分，会显示系统上运行的所有活动查询 |
| QueuedQueries                      | 排队的查询数                  | 计数   | Sum (默认值)    | 达到最大并发限制后排队请求的累计计数 |
| WLGActiveQueries                   | 工作负荷组活动查询   | 计数   | Sum (默认值)    | 工作负荷组中的活动查询。 如果使用此指标时不进行筛选和拆分，会显示系统上运行的所有活动查询 |
| WLGActiveQueriesTimeouts           | 工作负荷组查询超时   | 计数   | Sum (默认值)    | 已超时的工作负荷组查询。只有在查询开始执行查询后，此指标才报告查询超时（不包括由于锁定或资源等待而导致的等待时间） |
| WLGQueuedQueries                   | 工作负荷组排队查询   | 计数   | Sum (默认值)    | 达到最大并发限制后排队请求的累计计数 |
| WLGAllocationBySystemPercent        | 按系统百分比列出的工作负荷组分配 | 百分比 | 最大 (默认值) 、Min、Avg、Sum | 相对于整个系统的资源分配百分比 |
| WLGAllocationByEffectiveCapResourcePercent   | 按最大资源百分比列出的工作负荷组分配 | 百分比 | 最大 (默认值) 、Min、Avg | 显示相对于每个工作负荷组的有效上限资源百分比的资源分配百分比。 此指标提供工作负荷组的有效利用率 |
| WLGEffectiveCapResourcePercent      | 有效上限资源百分比  | 百分比 | 最大 (默认值) 、Min、Avg | 工作负荷组的有效上限资源百分比。 如果存在其他 min_percentage_resource > 0 的工作负荷组，effective_cap_percentage_resource 按比例降低 |
| WLGEffectiveMinResourcePercent      | 有效最小资源百分比  | 百分比 | 最大 (默认值) 、Min、Avg、Sum | 在考虑了服务级别和工作负荷组设置的情况下，允许的有效最小资源百分比设置。 在较低的服务级别可将有效 min_percentage_resource 调整为更高 |

### <a name="apache-spark-pool-metrics"></a>Apache Spark 池指标

下面是 Apache Spark 池发出的一些指标：

| **指标**                           | **度量值类别，显示名称**                  | **单位** | **聚合类型** | **说明**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| BigDataPoolApplicationsEnded  | Apache Spark 应用程序结束  | 计数 | Sum (默认值)  | 已结束的 Apache Spark 池应用程序数 |
| BigDataPoolAllocatedCores     | 分配到 Apache Spark 池的 Vcore 数                 | 计数 | 最大 (默认值) 、Min、Avg | 为 Apache Spark 池分配的 Vcore |
| BigDataPoolAllocatedMemory    | 分配给 Apache Spark 池的内存 (GB) 量            | 计数 | 最大 (默认值) 、Min、Avg | 为 Apache Spark 池分配的内存 (GB)  |
| BigDataPoolApplicationsActive | 活动 Apache Spark 应用程序 | 计数 | 最大 (默认值) 、Min、Avg | 活动 Apache Spark 池应用程序的数目 |

## <a name="alerts"></a>警报

登录到 Azure 门户，选择“Monitor” > “警报”以创建警报。

### <a name="create-alerts"></a>创建警报

1. 选择“+ 创建新的预警规则”，创建新的警报。

1. 定义 **警报条件** 以指定触发警报的时间。

    > [!NOTE]
    > 请务必在“按资源类型筛选”下拉列表中选择“所有”。

1. 定义 **警报详细信息** ，进一步指定应如何配置警报。

1. 定义 " **操作组** "，以确定应接收警报的人员 (和) 。

## <a name="logs"></a>日志

### <a name="workspace-level-logs"></a>工作区级日志

下面是 Azure Synapse Analytics 工作区发出的日志：

| Log Analytics 表名称 | 日志类别名称                 | 说明 |
|-------------------------------|-------------------------------------------------|-------------|
| SynapseGatewayApiRequests     | GatewayApiRequests             | Azure Synapse 网关 API 请求。 |
| SynapseRbacOperations         | SynapseRbacOperations          | Azure Synapse 基于角色的访问控制 (SRBAC) 操作。 |
| SynapseBuiltinSqlPoolRequestsEnded   | BuiltInSqlReqsEnded    | 已结束 Azure Synapse 内置无服务器 SQL 请求。 |

### <a name="dedicated-sql-pool-logs"></a>专用 SQL 池日志

下面是由专用 SQL 池发出的日志：

| Log Analytics 表名称        | 日志类别名称             | 说明 |
|----------------------|--------------------------------------|-------------|
| SynapseSqlPoolExecRequests  | ExecRequests | Azure Synapse 专用 SQL 池中的 SQL 请求/查询的相关信息。
| SynapseSqlPoolDmsWorkers    | DmsWorkers   | 有关在 Azure Synapse 专用 SQL 池中完成 DMS 步骤的工作线程的信息。
| SynapseSqlPoolRequestSteps  | RequestSteps | 有关在 Azure Synapse 专用 SQL 池中编写给定 SQL 请求/查询的请求步骤的信息。
| SynapseSqlPoolSqlRequests   | SqlRequests  | 有关 Azure Synapse 专用 SQL 池中 SQL 请求/查询步骤的查询分发的信息。
| SynapseSqlPoolWaits         | 等待        | 有关在 Azure Synapse 专用 SQL 池中执行 SQL 请求/查询期间遇到的等待状态的信息，包括锁和等待传输队列。

有关这些日志的详细信息，请参阅以下信息：
- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

### <a name="apache-spark-pool-log"></a>Apache Spark 池日志

下面是 Apache Spark 池发出的日志：

| Log Analytics 表名称               | 日志类别名称              | 说明                 |
|-----------------------------|---------------------------------------|-----------------------------|
| SynapseBigDataPoolApplicationsEnded | BigDataPoolAppsEnded | Apache Spark 应用程序已结束的相关信息 |

### <a name="diagnostic-settings"></a>诊断设置

可以使用诊断设置来配置非计算资源的诊断日志。 用于资源控制的诊断设置具有以下功能：

* 指定要将诊断日志发送到何处。 例如，发送到 Azure 存储帐户、Azure 事件中心或 Monitor 日志。
* 指定要发送的日志类别。
* 指定要将每个日志类别保留在存储帐户中多长时间。
* 保留期为 0 天表示永久保留日志。 如果不需要永久保留，可将该值设置为 1 到 2,147,483,647 的任意天数。
* 如果设置了保留策略，但禁止将日志存储在存储帐户中，则保留策略无效。 例如，如果仅选择了“事件中心”或“Monitor 日志”选项，此可能会发生这种情况。
* 保留策略按天应用。 一天的结束时间可能出现在协调世界时 (UTC) 的午夜。 在一天结束时，会删除当天已超过保留策略期限的日志。 例如，如果保留策略的期限为一天，则在今天开始时，会删除前天的日志。

通过 Azure Monitor 诊断设置，你可以将诊断日志路由到多个不同的目标。

* **存储帐户**：将诊断日志保存到存储帐户进行审核或手动检查。 可以使用诊断设置指定保留时间（天）。
* **事件中心**：将日志流式传输到 Azure 事件中心。 日志可用作合作伙伴服务/自定义分析解决方案（例如 Power BI）的输入。
* **Log Analytics 工作区**：通过 Log Analytics 分析日志。 Azure Synapse 与 Log Analytics 的集成在以下情况下非常有用：
  * 你想要对 Azure Synapse 发布的一组丰富的指标编写复杂的查询，以便 Log Analytics。 可以通过 Azure Monitor 为这些查询创建自定义警报。
  * 希望跨工作区进行监视。 可以将多个工作区中的数据路由到单个 Log Analytics 工作区。

你还可以使用存储帐户或事件中心命名空间，该命名空间不在发出日志的资源的订阅中。 配置此设置的用户必须对两个订阅都具有适当的 Azure 基于角色的访问控制 (Azure RBAC) 访问权限。

#### <a name="configure-diagnostic-settings"></a>配置诊断设置

创建或添加工作区、专用 SQL 池或 Apache Spark 池的诊断设置。

1. 在门户中，转到“监视”。 选择 "**设置**" "  >  **诊断设置**"。

1. 选择要为其创建诊断设置的 Synapse 工作区、专用 SQL 池或 Apache Spark 池。

1. 如果所选工作区中不存在任何诊断设置，系统将提示你创建设置。 选择“启用诊断”。

   如果工作区中已有诊断设置，你将看到已在资源上配置的设置列表。 选择“添加诊断设置”。

1. 为设置指定名称，选择 " **发送到 Log Analytics**"，然后从 **Log Analytics 工作** 区中选择一个工作区。

    > [!NOTE]
    > 由于 Azure 日志表的列数不能超过500，因此 **强烈建议** 选择 " _特定于资源的模式_"。 有关详细信息，请参阅 [Log Analytics 已知限制](../../azure-monitor/platform/resource-logs.md#column-limit-in-azurediagnostics)。

1. 选择“保存”。

几分钟后，新设置将出现在工作区的设置列表、专用 SQL 池或 Apache Spark 池。 一旦生成新的事件数据，就会将诊断日志流式传输到该工作区。 发出事件后可能需要最多 15 分钟的时间该事件才会出现在 Log Analytics 中。

## <a name="next-steps"></a>后续步骤

有关监视管道运行的详细信息，请参阅 [在 Synapse Studio 中运行的监视管道](how-to-monitor-pipeline-runs.md) 一文。 

有关监视 Apache Spark 应用程序的详细信息，请参阅在 [Synapse Studio 中监视 Apache Spark 应用程序](apache-spark-applications.md) 一文。

有关监视 SQL 请求的详细信息，请参阅在 [Synapse Studio 中监视 sql 请求](how-to-monitor-sql-requests.md) 一文。