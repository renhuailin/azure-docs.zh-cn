---
title: 了解并解决 Azure SQL 阻塞问题
titleSuffix: Azure SQL Database
description: 特定于 Azure SQL 数据库的关于阻塞和故障排除主题的概述。
services: sql-database
dev_langs:
- TSQL
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.date: 2/24/2021
ms.openlocfilehash: b829d7045ac520cfe908c3c8809ae17702d6175d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101691427"
---
# <a name="understand-and-resolve-azure-sql-database-blocking-problems"></a>了解并解决 Azure SQL 数据库阻塞问题
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

## <a name="objective"></a>目标

本文介绍了 Azure SQL 数据库中的阻塞，并演示了如何排除和解决阻塞。 

在本文中，术语“连接”指的是数据库的单一登录会话。 在许多 DMV 中，每个连接都显示为会话 ID (SPID) 或 session_id。 其中每个 SPID 通常称为一个进程，尽管它不是常规意义上的单独进程上下文。 而每个 SPID 都由服务器资源和数据结构组成，这些资源和数据结构是为来自给定客户端的单一连接请求提供服务所必需的。 单个客户端应用程序可能有一个或多个连接。 从 Azure SQL 数据库的角度来看，来自单个客户端计算机上的单个客户端应用程序的多个连接与来自多个客户端应用程序或多个客户端计算机的多个连接之间没有区别；它们是原子的。 不考虑源客户端，一个连接可以阻塞另一个连接。

> [!NOTE]
> **此内容侧重于 Azure SQL 数据库。** Azure SQL 数据库基于最新稳定版本的 Microsoft SQL Server 数据库引擎，因此很多内容是相似的，尽管故障排除选项和工具可能有所不同。 有关 SQL Server 中阻塞的详细信息，请参阅[了解并解决 SQL Server 阻塞问题](/troubleshoot/sql/performance/understand-resolve-blocking)。

## <a name="understand-blocking"></a>了解阻塞 
 
对于采用基于锁的并发机制的任何关系数据库管理系统 (RDBMS) 而言，阻塞是不可避免以及设计使然的特征。 如前所述，在 SQL Server 中，当一个会话持有特定资源上的锁，而另一个 SPID 试图获取同一资源上的冲突锁类型时，就会发生阻塞。 通常，第一个 SPID 锁定资源的时间范围很小。 当拥有的会话释放锁时，第二个连接就可以自由地获取自己对资源的锁并继续处理。 这是正常的行为，可能在一天中发生多次，对系统性能没有明显影响。

查询的持续时间和事务上下文决定其锁的保留时间，从而决定它们对其他查询的影响。 如果查询没有在事务中执行（并且没有使用锁提示），SELECT 语句的锁只会在实际读取资源时（而不是在查询期间）保留。 对于 INSERT、UPDATE 和 DELETE 语句，在查询过程中会保留锁，以确保数据一致性，并允许在必要时回滚查询。

对于在事务中执行的查询，锁的持续时间取决于查询类型、事务隔离级别以及查询中是否使用锁提示。 有关锁定、锁定提示和事务隔离级别的说明，请参阅以下文章：

* [数据库引擎中的锁定](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide)
* [自定义锁定和行版本控制](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#customizing-locking-and-row-versioning)
* [锁模式](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#lock_modes)
* [锁兼容性](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#lock_compatibility)
* [事务](/sql/t-sql/language-elements/transactions-transact-sql)

当锁定和阻塞持续到某种程度时，会对系统性能产生不利影响，这是由于以下原因之一：

* SPID 在释放一组资源之前，会在一段较长的时间内保留这些资源上的锁。 这种类型的阻塞会随着时间的推移自行解决，但会导致性能下降。

* SPID 持有一组资源上的锁，并且从不释放这些资源。 这种类型的阻塞不会自行解决，并且会无限期地阻止对受影响资源的访问。

在第一种情况下，局面可能非常不稳定，因为随着时间的推移，不同的 SPID 会阻塞不同的资源，从而会创建一个移动的目标。 使用 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 将问题缩小到单个查询，很难解决这些问题。 相反，第二种情况会导致一致的状态，更容易诊断。

## <a name="applications-and-blocking"></a>应用程序和阻塞

当遇到阻塞问题时，可能会倾向于关注服务器端优化和平台问题。 然而，只关注数据库可能不会导致解决问题，而且会占用时间和精力，以便更好地检查客户端应用程序及其提交的查询。 无论应用程序对正在进行的数据库调用公开了何种级别的可见性，阻塞问题都经常需要检查应用程序提交的准确 SQL 语句，以及应用程序在查询取消、连接管理、获取所有结果行等方面的准确行为。 如果开发工具不允许对连接管理、查询取消、查询超时、结果获取等进行显式控制，则阻塞问题可能无法解决。 在为 Azure SQL 数据库（尤其是对性能敏感的 OLTP 环境）选择应用程序开发工具之前，应仔细检查这种可能性。 

在数据库和应用程序的设计和构造阶段，要注意数据库的性能。 特别是，应为每个查询计算资源消耗、隔离级别和事务路径长度。 每个查询和事务都应尽可能轻量。 必须执行良好的连接管理规程，否则，应用程序在用户数较少时可能会显示出可接受的性能，但随着用户数的增加，性能可能会显著降低。 

通过对应用程序和查询进行适当设计，Azure SQL 数据库可以在一台服务器上同时支持数千个用户，几乎没有阻塞。

> [!Note]
> 有关应用程序开发指南的详细信息，请参阅 [排查 Azure SQL 数据库和 Azure SQL 托管实例的连接问题和其他问题](troubleshoot-common-errors-issues.md)和[暂时性故障处理](/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling)。

## <a name="troubleshoot-blocking"></a>对阻塞进行故障排除

无论处于哪种阻塞情况，对锁定进行故障排除的方法都是相同的。 这些逻辑分离将决定本文的其余部分。 也就是说，要找到头阻塞程序，并确定查询正在执行的操作以及会阻塞的原因。 找出有问题的查询（即在较长时间内保留锁的是什么对象）之后，下一步就是分析并确定阻塞发生的原因。 了解原因后，我们可以通过重新设计查询和事务来进行更改。

故障排除步骤：

1. 标识主阻塞会话（头阻塞程序）

2. 查找导致阻塞的查询和事务（长时间保留锁的对象）

3. 分析/理解长时间阻塞的原因

4. 通过重新设计查询和事务解决阻塞问题

现在让我们深入讨论如何通过适当的数据捕获来确定主阻塞会话。

## <a name="gather-blocking-information"></a>收集阻塞信息

为了攻克对阻塞问题进行故障排除的难题，数据库管理员可以使用 SQL 脚本不断监视 Azure SQL 数据库中的锁定和阻塞状态。 若要收集此数据，实质上有两种方法。 

第一种方法是查询动态管理对象 (DMO) 并存储结果，以便随着时间的推移进行比较。 本文中引用的一些对象是动态管理视图 (DMV)，一些是动态管理函数 (DMF)。 第二种方法是使用 XEvent 来捕获正在执行的内容。 


## <a name="gather-information-from-dmvs"></a>从 DMV 收集信息

引用 DMV 来对阻塞进行故障排除的目的是识别阻塞链和 SQL 语句头的 SPID（会话 ID）。 查找正在被阻塞的牺牲品 SPID。 如果任何 SPID 被另一个 SPID 阻塞，则调查拥有资源的 SPID（阻塞的 SPID）。 所有者 SPID 是否也被阻塞？ 你可以遍历链来找到头阻塞程序，然后调查其锁定的原因。

请记得在目标 Azure SQL 数据库中运行每个脚本。

* sp_who 和 sp_who2 命令是显示所有当前会话的旧命令。 DMV sys.dm_exec_sessions 返回结果集中更易于查询和筛选的更多数据。 需要在其他查询的核心查找 sys.dm_exec_sessions。 

* 如果已经标识了特定会话，可以使用 `DBCC INPUTBUFFER(<session_id>)` 查找会话提交的最后一条语句。 可以通过 sys.dm_exec_input_buffer 动态管理函数 (DMF) 返回类似的结果，该结果集提供 session_id 和 request_id，更易于查询和筛选。 例如，若要返回由 session_id 66 和 request_id 0 提交的最新查询：

```sql
SELECT * FROM sys.dm_exec_input_buffer (66,0);
```

* 请参阅 sys.dm_exec_requests 并引用 blocking_session_id 列。 当 blocking_session_id = 0 时，将不会阻塞会话。 虽然 sys.dm_exec_requests 只列出当前正在执行的请求，但任何连接（活动或不活动）都将列在 sys.dm_exec_sessions 中。 在下一个查询中，在 sys.dm_exec_requests 和 sys.dm_exec_sessions 之间建立此公共联接。

* 使用 [sys.dm_exec_sql_text](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql) 或 [sys.dm_exec_input_buffer](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-input-buffer-transact-sql) DMV 运行此示例查询以查找活动执行的查询及其当前 SQL 批处理文本或输入缓冲区文本。 如果 sys.dm_exec_sql_text 的 `text` 字段返回的数据为 NULL，则当前不执行查询。 在这种情况下，sys.dm_exec_input_buffer 的 `event_info` 字段将包含传递给 SQL 引擎的最后一个命令字符串。 此查询还可以用于标识阻止其他会话的会话，包括每个 session_id 阻止的 session_ids 的列表。 

```sql
WITH cteBL (session_id, blocking_these) AS 
(SELECT s.session_id, blocking_these = x.blocking_these FROM sys.dm_exec_sessions s 
CROSS APPLY    (SELECT isnull(convert(varchar(6), er.session_id),'') + ', '  
                FROM sys.dm_exec_requests as er
                WHERE er.blocking_session_id = isnull(s.session_id ,0)
                AND er.blocking_session_id <> 0
                FOR XML PATH('') ) AS x (blocking_these)
)
SELECT s.session_id, blocked_by = r.blocking_session_id, bl.blocking_these
, batch_text = t.text, input_buffer = ib.event_info, * 
FROM sys.dm_exec_sessions s 
LEFT OUTER JOIN sys.dm_exec_requests r on r.session_id = s.session_id
INNER JOIN cteBL as bl on s.session_id = bl.session_id
OUTER APPLY sys.dm_exec_sql_text (r.sql_handle) t
OUTER APPLY sys.dm_exec_input_buffer(s.session_id, NULL) AS ib
WHERE blocking_these is not null or r.blocking_session_id > 0
ORDER BY len(bl.blocking_these) desc, r.blocking_session_id desc, r.session_id;
```

* 运行 Microsoft 支持部门提供的更详细的示例查询，以确定多个会话阻塞链的头，包括阻塞链中涉及的会话的查询文本。

```sql
WITH cteHead ( session_id,request_id,wait_type,wait_resource,last_wait_type,is_user_process,request_cpu_time
,request_logical_reads,request_reads,request_writes,wait_time,blocking_session_id,memory_usage
,session_cpu_time,session_reads,session_writes,session_logical_reads
,percent_complete,est_completion_time,request_start_time,request_status,command
,plan_handle,sql_handle,statement_start_offset,statement_end_offset,most_recent_sql_handle
,session_status,group_id,query_hash,query_plan_hash) 
AS ( SELECT sess.session_id, req.request_id, LEFT (ISNULL (req.wait_type, ''), 50) AS 'wait_type'
    , LEFT (ISNULL (req.wait_resource, ''), 40) AS 'wait_resource', LEFT (req.last_wait_type, 50) AS 'last_wait_type'
    , sess.is_user_process, req.cpu_time AS 'request_cpu_time', req.logical_reads AS 'request_logical_reads'
    , req.reads AS 'request_reads', req.writes AS 'request_writes', req.wait_time, req.blocking_session_id,sess.memory_usage
    , sess.cpu_time AS 'session_cpu_time', sess.reads AS 'session_reads', sess.writes AS 'session_writes', sess.logical_reads AS 'session_logical_reads'
    , CONVERT (decimal(5,2), req.percent_complete) AS 'percent_complete', req.estimated_completion_time AS 'est_completion_time'
    , req.start_time AS 'request_start_time', LEFT (req.status, 15) AS 'request_status', req.command
    , req.plan_handle, req.[sql_handle], req.statement_start_offset, req.statement_end_offset, conn.most_recent_sql_handle
    , LEFT (sess.status, 15) AS 'session_status', sess.group_id, req.query_hash, req.query_plan_hash
    FROM sys.dm_exec_sessions AS sess
    LEFT OUTER JOIN sys.dm_exec_requests AS req ON sess.session_id = req.session_id
    LEFT OUTER JOIN sys.dm_exec_connections AS conn on conn.session_id = sess.session_id 
    )
, cteBlockingHierarchy (head_blocker_session_id, session_id, blocking_session_id, wait_type, wait_duration_ms,
wait_resource, statement_start_offset, statement_end_offset, plan_handle, sql_handle, most_recent_sql_handle, [Level])
AS ( SELECT head.session_id AS head_blocker_session_id, head.session_id AS session_id, head.blocking_session_id
    , head.wait_type, head.wait_time, head.wait_resource, head.statement_start_offset, head.statement_end_offset
    , head.plan_handle, head.sql_handle, head.most_recent_sql_handle, 0 AS [Level]
    FROM cteHead AS head
    WHERE (head.blocking_session_id IS NULL OR head.blocking_session_id = 0)
    AND head.session_id IN (SELECT DISTINCT blocking_session_id FROM cteHead WHERE blocking_session_id != 0)
    UNION ALL
    SELECT h.head_blocker_session_id, blocked.session_id, blocked.blocking_session_id, blocked.wait_type,
    blocked.wait_time, blocked.wait_resource, h.statement_start_offset, h.statement_end_offset,
    h.plan_handle, h.sql_handle, h.most_recent_sql_handle, [Level] + 1
    FROM cteHead AS blocked
    INNER JOIN cteBlockingHierarchy AS h ON h.session_id = blocked.blocking_session_id and h.session_id!=blocked.session_id --avoid infinite recursion for latch type of blocking
    WHERE h.wait_type COLLATE Latin1_General_BIN NOT IN ('EXCHANGE', 'CXPACKET') or h.wait_type is null
    )
SELECT bh.*, txt.text AS blocker_query_or_most_recent_query 
FROM cteBlockingHierarchy AS bh 
OUTER APPLY sys.dm_exec_sql_text (ISNULL ([sql_handle], most_recent_sql_handle)) AS txt;
```

* 若要捕获长时间运行或未提交的事务，请使用另一组 DMV 来查看当前打开的事务，包括 [sys.dm_tran_database_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-database-transactions-transact-sql)、[sys.dm_tran_session_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-session-transactions-transact-sql)、[sys.dm_exec_connections](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-connections-transact-sql) 以及 sys.dm_exec_sql_text。 存在多个与跟踪事务相关联的 DMV，请在此处参阅[与事务有关的 DMV](/sql/relational-databases/system-dynamic-management-views/transaction-related-dynamic-management-views-and-functions-transact-sql)。 

```sql
SELECT [s_tst].[session_id],
[database_name] = DB_NAME (s_tdt.database_id),
[s_tdt].[database_transaction_begin_time], 
[sql_text] = [s_est].[text] 
FROM sys.dm_tran_database_transactions [s_tdt]
INNER JOIN sys.dm_tran_session_transactions [s_tst] ON [s_tst].[transaction_id] = [s_tdt].[transaction_id]
INNER JOIN sys.dm_exec_connections [s_ec] ON [s_ec].[session_id] = [s_tst].[session_id]
CROSS APPLY sys.dm_exec_sql_text ([s_ec].[most_recent_sql_handle]) AS [s_est];
```

* 引用 [sys.dm_os_waiting_tasks](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql)（位于 SQL 的线程/任务层）。 这将返回关于当前经历的请求的 SQL 等待类型的信息。 与 sys.dm_exec_requests 一样，sys.dm_os_waiting_tasks 只返回活动请求。 

> [!Note]
> 有关等待类型的详细信息（包括随时间变化的聚合的等待统计信息），请参阅 DMV [sys.dm_db_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database)。 此 DMV 仅返回当前数据库的聚合等待统计信息。

* 使用 [sys.dm_tran_locks](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql) DMV，获取有关查询所放置的锁的更详尽信息。 此 DMV 可以返回生产 SQL Server 上的大量数据，这对于诊断当前保留的锁非常有用。 

由于 sys.dm_os_waiting_tasks 的内联，以下查询将 sys.dm_tran_locks 的输出限制为仅当前被阻塞的请求、其等待状态以及其锁：

```sql
SELECT table_name = schema_name(o.schema_id) + '.' + o.name
, wt.wait_duration_ms, wt.wait_type, wt.blocking_session_id, wt.resource_description
, tm.resource_type, tm.request_status, tm.request_mode, tm.request_session_id
FROM sys.dm_tran_locks AS tm
INNER JOIN sys.dm_os_waiting_tasks as wt ON tm.lock_owner_address = wt.resource_address
LEFT OUTER JOIN sys.partitions AS p on p.hobt_id = tm.resource_associated_entity_id
LEFT OUTER JOIN sys.objects o on o.object_id = p.object_id or tm.resource_associated_entity_id = o.object_id
WHERE resource_database_id = DB_ID()
AND object_name(p.object_id) = '<table_name>';
```

* 对于 DMV，随着时间的推移存储查询结果将提供数据点，允许你在指定的时间间隔内查看阻塞，以识别持久的阻塞或趋势。 

## <a name="gather-information-from-extended-events"></a>从扩展事件中收集信息

除了上述信息，通常还需要捕获服务器上活动的跟踪，以彻底调查 Azure SQL 数据库上的阻塞问题。 例如，如果一个会话在一个事务中执行多条语句，则只表示提交的最后一条语句。 但是，前面的语句之一可能是仍保留锁的原因。 跟踪将使你能够查看当前事务中会话执行的所有命令。

在 SQL Server 中捕获跟踪有两种方法：扩展事件 (XEvent) 和探查器跟踪。 但是，[SQL Server Profiler](/sql/tools/sql-server-profiler/sql-server-profiler) 是已被弃用的跟踪技术，Azure SQL 数据库不支持这种技术。 [扩展事件](/sql/relational-databases/extended-events/extended-events)是一种较新的跟踪技术，它支持更多的通用性，对观察到的系统的影响较小，并且它的接口集成到 SQL Server Management Studio (SSMS) 中。 

请参阅说明如何在 SSMS 中使用[扩展事件新建会话向导](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server)的文档。 但对于 Azure SQL 数据库，SSMS 在对象资源管理器中的每个数据库下都提供了扩展事件子文件夹。 使用扩展事件会话向导捕获这些有用的事件： 

-   分类错误：
    -   Attention
    -   Error_reported  
    -   Execution_warning

-   分类警告： 
    -   Missing_join_predicate

-   分类执行：
    -   Rpc_completed
    -   Rpc_starting 
    -   Sql_batch_completed
    -   Sql_batch_starting

-   Lock
    -   Lock_deadlock

-   会话
    -   Existing_connection
    -   登录
    -   Logout

## <a name="identify-and-resolve-common-blocking-scenarios"></a>发现并解决常见阻塞情况

通过检查上述信息，你可以确定大多数阻塞问题的原因。 本文的其余部分将讨论如何使用这些信息来识别和解决一些常见的阻塞情况。 本讨论假设你已使用阻塞脚本（前面引用）捕获有关阻塞 SPID 的信息，并已使用 XEvent 会话捕获应用程序活动。

## <a name="analyze-blocking-data"></a>分析阻塞数据 

* 检查 DMV sys.dm_exec_requests 和 sys.dm_exec_sessions 的输出，以确定阻塞链的头（使用 blocking_these 和 session_id）。 此操作可清楚地标识出哪些请求已被阻塞，哪些请求即将被阻塞。 进一步了解已被阻塞和即将被阻塞的会话。 阻塞链是否有公共点或根？ 它们可能共享一个公共表，并且阻塞链中涉及的一个或多个会话正在执行写入操作。 

* 检查DMV sys.dm_exec_requests 和 sys.dm_exec_sessions 的输出，获取有关阻塞链头 SPID 的信息。 查找以下字段： 

    -    `sys.dm_exec_requests.status`  
    此列显示特定请求的状态。 通常， 睡眠状态表示 SPID 已完成执行，正在等待应用程序提交另一个查询或批处理。 可运行或正在运行状态表示 SPID 当前正在处理查询。 下表简要说明了各种状态值。

    | 状态 | 含义 |
    |:-|:-|
    | 背景 | SPID 正在运行一个后台任务，例如死锁检测、日志编写器或检查点。 |
    | Sleeping | SPID 当前未执行。 这通常表示 SPID 正在等待来自应用程序的命令。 |
    | 运行 | SPID 当前正在计划程序中运行。 |
    | 可运行 | SPID 在计划程序的可运行队列中等待获取计划程序时间。 |
    | 已挂起 | SPID 正在等待某个资源，如锁或闩锁。 | 
                       
    -    `sys.dm_exec_sessions.open_transaction_count`  
    此字段告诉你此会话中打开的事务数。 如果该值大于 0，则 SPID 位于打开的事务中，并且可能保留事务中任何语句获取的锁。

    -    `sys.dm_exec_requests.open_transaction_count`  
    同样，此字段告诉你此请求中打开的事务数。 如果该值大于 0，则 SPID 位于打开的事务中，并且可能保留事务中任何语句获取的锁。

    -   `sys.dm_exec_requests.wait_type`、`wait_time` 和 `last_wait_type`  
    如果  `sys.dm_exec_requests.wait_type`  为 NULL，则请求当前没有等待任何内容， `last_wait_type`  值指示请求遇到的最后一个  `wait_type` 。 有关  `sys.dm_os_wait_stats` 的详细信息和最常见的等待类型的描述，请参阅 [sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)。  `wait_time`  值可用于确定请求是否正在进行。 当针对 sys.dm_exec_requests 表的查询在  `wait_time`  列中返回的值小于前一个 sys.dm_exec_requests 查询中的  `wait_time`  值时，这表示已获取并释放前一个锁，现在正在等待一个新锁（假设为非零 `wait_time`）。 这可以通过比较 sys.dm_exec_requests 输出之间的 `wait_resource`  来验证，该输出显示了请求正在等待的资源。

    -   `sys.dm_exec_requests.wait_resource` 此字段指示已阻塞的请求正在等待的资源。 下表列出了常见的  `wait_resource`  格式及其含义：

    | 资源 | 格式 | 示例 | 说明 | 
    |:-|:-|:-|:-|
    | 表 | DatabaseID:ObjectID:IndexID | TAB:5:261575970:1 | 在本例中，数据库 ID 5 是 pubs 示例数据库，对象 ID 261575970 是标题表，1 是聚集索引。 |
    | 页 | DatabaseID:FileID:PageID | 页：5:1:104 | 在此例中，数据库 ID 5 是 pubs，文件 ID 1 是主数据文件，页 104 是属于标题表的页。 若要标识该页所属的对象，请使用动态管理函数 [sys.dm_db_page_info](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-page-info-transact-sql)，从 `wait_resource` 传入 DatabaseID、FileId、PageId。 | 
    | 键 | DatabaseID:Hobt_id（索引键的哈希值） | 键：5:72057594044284928 (3300a4f361aa) | 在此例中，数据库 ID 5 是 Pubs，Hobt_ID 72057594044284928 对应于 object_id 261575970（标题表）的 index_id 2。 使用 sys.partitions 目录视图将 hobt_id 与特定 index_id 和 object_id 相关联。 无法将索引键散列哈希解哈希为特定的键值。 |
    | 行 | DatabaseID:FileID:PageID:Slot(row) | RID：5:1:104:3 | 在此例中，数据库 ID 5 是 pubs，文件 ID 1 是主数据文件，页 104 是属于标题表的页，且槽 3 指示行在页上的位置。 |
    | Compile  | DatabaseID:FileID:PageID:Slot(row) | RID：5:1:104:3 | 在此例中，数据库 ID 5 是 pubs，文件 ID 1 是主数据文件，页 104 是属于标题表的页，且槽 3 指示行在页上的位置。 |

    -    `sys.dm_tran_active_transactions` [sys.dm_tran_active_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-active-transactions-transact-sql) DMV 包含有关打开事务的数据，这些事务可以联接到其他 DMV 以获得等待提交或回滚的事务的完整图。 使用以下查询返回有关打开事务的信息，这些事务与其他 DMV 联接，包括 [sys.dm_tran_session_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-session-transactions-transact-sql)。 请考虑事务的当前状态 (`transaction_begin_time`) 和其他情况数据，以评估它是否可能是阻塞源。

    ```sql
    SELECT tst.session_id, [database_name] = db_name(s.database_id)
    , tat.transaction_begin_time
    , transaction_duration_s = datediff(s, tat.transaction_begin_time, sysdatetime()) 
    , transaction_type = CASE tat.transaction_type  WHEN 1 THEN 'Read/write transaction'
                                                    WHEN 2 THEN 'Read-only transaction'
                                                    WHEN 3 THEN 'System transaction'
                                                    WHEN 4 THEN 'Distributed transaction' END
    , input_buffer = ib.event_info, tat.transaction_uow     
    , transaction_state  = CASE tat.transaction_state    
                WHEN 0 THEN 'The transaction has not been completely initialized yet.'
                WHEN 1 THEN 'The transaction has been initialized but has not started.'
                WHEN 2 THEN 'The transaction is active - has not been committed or rolled back.'
                WHEN 3 THEN 'The transaction has ended. This is used for read-only transactions.'
                WHEN 4 THEN 'The commit process has been initiated on the distributed transaction.'
                WHEN 5 THEN 'The transaction is in a prepared state and waiting resolution.'
                WHEN 6 THEN 'The transaction has been committed.'
                WHEN 7 THEN 'The transaction is being rolled back.'
                WHEN 8 THEN 'The transaction has been rolled back.' END 
    , transaction_name = tat.name, request_status = r.status
    , azure_dtc_state = CASE tat.dtc_state 
                        WHEN 1 THEN 'ACTIVE'
                        WHEN 2 THEN 'PREPARED'
                        WHEN 3 THEN 'COMMITTED'
                        WHEN 4 THEN 'ABORTED'
                        WHEN 5 THEN 'RECOVERED' END
    , tst.is_user_transaction, tst.is_local
    , session_open_transaction_count = tst.open_transaction_count  
    , s.host_name, s.program_name, s.client_interface_name, s.login_name, s.is_user_process
    FROM sys.dm_tran_active_transactions tat 
    INNER JOIN sys.dm_tran_session_transactions tst  on tat.transaction_id = tst.transaction_id
    INNER JOIN Sys.dm_exec_sessions s on s.session_id = tst.session_id 
    LEFT OUTER JOIN sys.dm_exec_requests r on r.session_id = s.session_id
    CROSS APPLY sys.dm_exec_input_buffer(s.session_id, null) AS ib;
    ```

    -   其他列

        [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql) 和 [sys.dm_exec_request](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) 中其余的列也可以提供对根本问题的见解。 它们的用处因问题的具体情况而异。 例如，你可以确定问题是否仅发生在特定的客户端（主机名）、特定的网络库 (net_library)、SPID 在 sys.dm_exec_sessions 会话中是 `last_request_start_time` 时最后一个批处理何时提交、在 sys.dm_exec_requests 中使用 `start_time` 运行请求的时间等。


## <a name="common-blocking-scenarios"></a>常见的阻塞情况

下表列出了常见症状及其可能原因。  

`wait_type`、`open_transaction_count` 和 `status` 列是指由 [sys.dm_exec_request](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) 返回的信息，其他列可以由 [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql) 返回。 “Resolves?” 列指示阻塞是否将自行解决，或者是否应通过 `KILL` 命令终止会话。 有关详细信息，请参阅 [KILL (Transact-SQL)](/sql/t-sql/language-elements/kill-transact-sql)。

| 方案 | Waittype | Open_Tran | 状态 | Resolves? | 其他症状 |  
|:-|:-|:-|:-|:-|:-|--|
| 1 | NOT NULL | >= 0 | 可运行 | 是，当查询完成时。 | 在 sys.dm_exec_sessions 中，reads、cpu_time 和/或 memory_usage 列将随着时间的推移而增加  。 完成后，查询的持续时间将较长。 |
| 2 | Null | \>0 | 正在睡眠 | 不可以，但可以终止 SPID。 | 在此 SPID 的扩展事件会话中可能会看到一个注意信号，表示已发生了查询超时或已取消。 |
| 3 | Null | \>= 0 | 可运行 | 否。 在客户端获取所有行或关闭连接之前无法解析。 可以终止 SPID，但可能最多需要 30 秒。 | 如果 open_transaction_count = 0，并且 SPID 在事务隔离级别为默认 (READ COMMMITTED) 时保留锁，这可能是一个原因。 |  
| 4 | 多种多样 | \>= 0 | 可运行 | 否。 在客户端取消查询或关闭连接之前无法解析。 可以终止 SPID，但可能最多需要 30 秒。 | sys.dm_exec_sessions 中位于阻塞链头的 SPID 的 hostname 列将与它所阻塞的 SPID 相同。 |  
| 5 | Null | \>0 | 回滚 | 是的。 | 在此 SPID 的扩展事件会话中可能会看到一个注意信号，表示已发生了查询超时或已取消，或者只是发出了一个回滚语句。 |  
| 6 | Null | \>0 | 正在睡眠 | 最终， 当 Windows NT 确定会话不再处于活动状态时，Azure SQL 数据库连接将断开。 | sys.dm_exec_sessions 中的 `last_request_start_time` 值比当前时间早得多。 |

以下情况将基于上述情况扩展。 

1.  由执行时间长的正常运行查询引起的阻塞

    **解决方法**：此类阻塞问题的解决方案是寻找优化查询的方法。 实际上，这类阻塞问题可能只是一个性能问题，需要你按照性能问题来处理。 有关对特定慢速运行的查询进行故障排除的信息，请参阅[如何对 SQL Server 上慢速运行的查询进行故障排除](/troubleshoot/sql/performance/troubleshoot-slow-running-queries)。 有关详细信息，请参阅[监视和优化性能](/sql/relational-databases/performance/monitor-and-tune-for-performance)。 

    来自 SSMS 中的[查询存储](/sql/relational-databases/performance/best-practice-with-the-query-store)的报表也是一个高度推荐的有用工具，可用于识别成本最高的查询、欠佳的执行计划。 另请查看 Azure 门户中关于 Azure SQL 数据库的[智能性能](intelligent-insights-overview.md)部分，包括 [Query Performance Insight](query-performance-insight-use.md)。

    如果有一个长时间运行的查询正在阻塞其他用户，并且无法进行优化，请考虑将其从 OLTP 环境移动到专用的报表系统，即[数据库的同步只读副本](read-scale-out.md)。

1.  由具有未提交事务的睡眠 SPID 引起的阻塞

    此类阻塞通常可以通过正在睡眠或等待命令的 SPID 来识别，但是其事务嵌套级别（来自 sys.dm_exec_requests 的 `@@TRANCOUNT`、`open_transaction_count`）大于零。 如果应用程序遇到查询超时，或者发出取消而不发出所需数量的 ROLLBACK 和/或 COMMIT 语句，则可能发生这种情况。 当 SPID 收到查询超时或取消时，它将终止当前查询和批处理，但不会自动回滚或提交事务。 应用程序对此负责，因为 Azure SQL 数据库不能假定由于取消单个查询而必须回滚整个事务。 查询超时或取消将在扩展事件会话中作为 SPID 的注意信号事件出现。

    若要演示未提交的显式事务，请发出以下查询：

    ```sql
    CREATE TABLE #test (col1 INT);
    INSERT INTO #test SELECT 1;
    BEGIN TRAN
    UPDATE #test SET col1 = 2 where col1 = 1;
    ```

    然后，在同一窗口中执行以下查询：
    ```sql
    SELECT @@TRANCOUNT;
    ROLLBACK TRAN
    DROP TABLE #test;
    ```

    第二个查询的输出指示事务嵌套级别为 1。 在提交或回滚事务之前，事务中获取的所有锁仍将保留。 如果应用程序显式打开并提交事务，则通信或其他错误可能会使会话及其事务处于打开状态。 

    使用上面基于 sys.dm_tran_active_transactions 的脚本来确定当前未提交的事务。

    **解决方法**：

     -   此外，这类阻塞问题也可能是一种性能问题，需要你按照性能问题来处理。 如果可以缩短查询执行时间，则不会发生查询超时或取消。 如果出现超时或取消情况，应确保应用程序能够处理它们，但是你也可以从检查查询的性能中获益。 
     
     -    应用程序必须正确管理事务嵌套级别，否则在以这种方式取消查询之后，它们可能会导致阻塞问题。 考虑以下情况：  

            *    在客户端应用程序的错误处理程序中，在出现任何错误后执行 `IF @@TRANCOUNT > 0 ROLLBACK TRAN`，即使客户端应用程序不认为某个事务已打开。 需要检查打开的事务，因为在批处理期间调用的存储过程可能在客户端应用程序不知情的情况下启动了事务。 某些条件（如取消查询）会阻止过程在当前语句之后执行，因此即使过程具有检查 `IF @@ERROR <> 0` 和中止事务的逻辑，在这种情况下也不会执行此回滚代码。  
            *    如果在应用程序中使用连接池打开连接并在将连接释放回池之前运行少量查询（例如基于 Web 的应用程序），则暂时禁用连接池可以帮助缓解这个问题，直到修改客户端应用程序来适当处理错误。 通过禁用连接池，释放连接将导致 Azure SQL 数据库连接的物理断开，从而导致服务器回滚任何打开的事务。  
            *    将 `SET XACT_ABORT ON` 用于连接，或用于开始事务且在出现错误后未进行清理的任何存储过程中。 如果发生运行时错误，此设置将中止任何打开的事务并将控制权返回给客户端。 有关详细信息，请查看 [SET XACT_ABORT (Transact-SQL)](/sql/t-sql/statements/set-xact-abort-transact-sql)。
    > [!NOTE]
    > 除非从连接池重新使用连接，否则不会重置连接，因此用户可以打开一个事务，然后释放与连接池的连接，但在几秒钟内可能不会重新使用连接，在此期间事务将保持打开状态。 如果不重用连接，则当连接超时并从连接池中删除时，事务将中止。 因此，客户端应用程序最好在错误处理程序中中止事务，或者使用 `SET XACT_ABORT ON` 来避免这种潜在的延迟。

    > [!CAUTION]
    > 在 `SET XACT_ABORT ON` 之后，不会执行导致错误的语句后面的 T-SQL 语句。 这可能会影响现有代码的预期流。

1.  由 SPID 引起的阻塞，其对应的客户端应用程序没有将所有结果行提取到完成位置

    向服务器发送查询后，所有应用程序必须立即将所有结果行提取到完成。 如果应用程序没有提取所有结果行，则表上可能会留下锁，从而阻塞其他用户。 如果你使用的应用程序透明地向服务器提交 SQL 语句，则应用程序必须提取所有结果行。 如果没有（如果无法配置为这样做），则可能无法解决阻塞问题。 若要避免此问题，你可以将性能不佳的应用程序限制在报表或决策支持数据库中。
    
    > [!NOTE]
    > 有关连接到 Azure SQL 数据库的应用程序，请参阅[重试逻辑指南](./troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)。 
    
    **解决方法**：必须重写应用程序才能将结果的所有行提取到完成。 这并不排除使用查询的 [ORDER BY 子句中的 OFFSET 和 FETCH](/sql/t-sql/queries/select-order-by-clause-transact-sql#using-offset-and-fetch-to-limit-the-rows-returned) 来执行服务器端分页。

1.  由处于回滚状态的 SPID 导致的阻塞

    在用户定义事务之外终止或取消的数据修改查询将回滚。 这也可能是客户端网络会话断开连接的副作用，或者请求被选为死锁牺牲品。 这种情况通常可以通过观察 sys.dm_exec_requests 的输出来识别，这可能表示 ROLLBACK 命令，percent_complete 列可能会显示进度 。 

    由于 2019 年推出的[加速数据库恢复功能](../accelerated-database-recovery.md)，冗长的回滚应该很罕见。
    
    **解决方法**：等待 SPID 完成回滚所做的更改。 

    若要避免这种情况，请勿在 OLTP 系统的繁忙时间执行大批量写入操作或索引创建或维护操作。 如果可能，请在低活动期间执行此类操作。

1.  由孤立连接导致的阻塞

    如果客户端应用程序捕获错误或客户端工作站重新启动，则在某些情况下，与服务器的网络会话可能不会立即取消。 从 Azure SQL 数据库的角度来看，客户端似乎仍然存在，并且获得的任何锁可能仍然会保留。 有关详细信息，请参阅[如何对 SQL Server 中的孤立用户进行故障排除](/sql/t-sql/language-elements/kill-transact-sql#remarks)。 

    **解决方法**：如果客户端应用程序已断开连接，但未适当清理其资源，则可以使用 `KILL` 命令终止 SPID。 `KILL` 命令采用 SPID 值作为输入。 例如，若要终止 SPID 99，请发出以下命令：

    ```sql
    KILL 99
    ```

## <a name="see-also"></a>请参阅

* [Azure SQL 数据库与 Azure SQL 托管实例中的监视和性能优化](./monitor-tune-overview.md)
* [使用 Query Store 监视性能](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)
* [事务锁定和行版本控制指南](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide)
* [SET TRANSACTION ISOLATION LEVEL](/sql/t-sql/statements/set-transaction-isolation-level-transact-sql)
* [快速入门：SQL Server 中的扩展事件](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server)
* [智能见解：使用 AI 监视数据库性能并对其进行故障排除](intelligent-insights-overview.md)

## <a name="learn-more"></a>了解详细信息

* [Azure SQL 数据库：通过自动优化改善性能优化](https://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Improving-Performance-Tuning-with-Automatic-Tuning)
* [利用自动优化提高 Azure SQL 数据库性能](https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-Automatic-Tuning)
* [使用 Azure SQL 提供一致的性能](/learn/modules/azure-sql-performance/)
* [排查 Azure SQL 数据库和 Azure SQL 托管实例的连接问题和其他问题](troubleshoot-common-errors-issues.md)
* [Transient Fault Handling](/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling)
