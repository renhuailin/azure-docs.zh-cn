---
title: 了解和解决 Azure SQL 阻止问题
titleSuffix: Azure SQL Database
description: 有关阻止和故障排除的 Azure SQL 数据库的概述。
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
ms.date: 1/14/2020
ms.openlocfilehash: 1341d0e64a01ff428fe42735d198c5e6b74b0ce8
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2021
ms.locfileid: "99093302"
---
# <a name="understand-and-resolve-azure-sql-database-blocking-problems"></a>了解和解决 Azure SQL 数据库阻塞问题
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

## <a name="objective"></a>目标

本文介绍了如何在 Azure SQL 数据库中进行阻止，并演示了如何排查和解决阻塞问题。 

在本文中，术语 "连接" 是指数据库的单一登录会话。 每个连接作为会话 ID 显示 (SPID) 或多个 Dmv 中的 session_id。 其中每个 Spid 通常称为进程，但它并不是一个在常见意义上不同的过程上下文。 相反，每个 SPID 都包含为来自给定客户端的单个连接请求提供服务所需的服务器资源和数据结构。 单个客户端应用程序可能有一个或多个连接。 从 Azure SQL 数据库的角度来看，来自单个客户端计算机上单个客户端应用程序的多个连接与多个客户端应用程序或多台客户端计算机之间的多个连接没有区别。它们是原子的。 一个连接可以阻止另一个连接，而不考虑源客户端。

> [!NOTE]
> **此内容特定于 Azure SQL 数据库。** Azure SQL 数据库基于 Microsoft SQL Server 数据库引擎的最新稳定版本，因此，很多内容都是类似的，但疑难解答选项和工具可能有所不同。 有关 SQL Server 中阻止的详细信息，请参阅 [了解和解决 SQL Server 阻止问题](/troubleshoot/sql/performance/understand-resolve-blocking)。

## <a name="understand-blocking"></a>了解阻止 
 
阻塞在任何关系数据库管理系统 (RDBMS) 与基于锁的并发性之间是不可避免和设计的特征。 如前所述，在 SQL Server 中，当一个会话持有特定资源的锁，而另一个 SPID 尝试获取同一资源上的冲突锁类型时，会发生阻塞。 通常，第一个 SPID 锁定资源的时间范围很小。 当拥有的会话释放锁定后，第二个连接可以自由地获取其自己的对资源的锁定并继续处理。 这是正常行为，可能在一天中的一天内发生了很多情况，对系统性能没有明显影响。

查询的持续时间和事务上下文决定了锁的保留时间，从而决定了其锁对其他查询的影响。 如果查询不在事务 (中执行，并且没有) 使用任何锁提示，则 SELECT 语句的锁将仅在实际读取时（而不是在查询期间）保留在资源上。 对于 INSERT、UPDATE 和 DELETE 语句，在查询过程中将保留锁，这两者都用于数据一致性，并允许在必要时回滚查询。

对于在事务中执行的查询，持有锁的持续时间由查询类型、事务隔离级别以及是否在查询中使用锁提示决定。 有关锁定、锁定提示和事务隔离级别的说明，请参阅以下文章：

* [数据库引擎中的锁定](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide)
* [自定义锁定和行版本控制](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#customizing-locking-and-row-versioning)
* [锁模式](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#lock_modes)
* [锁兼容性](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#lock_compatibility)
* [事务](/sql/t-sql/language-elements/transactions-transact-sql)

当锁定和阻塞持续到对系统性能产生不利影响的点时，原因可能是以下原因之一：

* SPID 会长时间保留一组资源的锁，然后再将其释放。 这种类型的阻止会在一段时间内自行解析，但会导致性能下降。

* SPID 持有一组资源的锁，而永远不会释放这些资源。 这种类型的阻止不会自行解决，阻止对受影响的资源进行无限期的访问。

在第一个方案中，这种情况可能非常流畅，因为不同的 Spid 导致一段时间内的不同资源发生阻塞，从而创建一个移动目标。 这些情况难以使用 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 进行故障排除，以将问题范围缩小到各个查询中。 与此相反，第二种情况会导致一致的状态，从而使其更易于诊断。

## <a name="applications-and-blocking"></a>应用程序和阻塞

面临阻碍性问题时，可能会关注服务器端优化和平台问题。 但是，只向数据库支付的注意可能不会导致解决问题，并且可以更好地在检查客户端应用程序和它所提交的查询时获得更好的精力。 无论应用程序公开的数据库调用的可见性级别如何，阻止性问题都经常需要检查应用程序所提交的准确 SQL 语句，以及应用程序与查询取消、连接管理、提取所有结果行等相关的确切行为。 如果开发工具不允许显式控制连接管理、查询取消、查询超时、结果提取等，则阻塞问题可能无法解析。 在选择适用于 Azure SQL 数据库的应用程序开发工具之前，应仔细检查这种可能性，尤其是对于性能敏感的 OLTP 环境。 

在数据库和应用程序的设计和构建阶段，请注意数据库的性能。 具体而言，应为每个查询计算资源消耗、隔离级别和事务路径长度。 每个查询和事务应尽可能轻量。 良好的连接管理训练必须在不使用它的情况下进行，但在用户数量较低的情况下，应用程序的性能可能会下降，但随着用户数量的增加，性能可能会显著降低。 

使用正确的应用程序和查询设计，Azure SQL 数据库能够在一台服务器上支持成千上万同时进行的用户，几乎不会受到阻止。

> [!Note]
> 有关应用程序开发指南的详细信息，请参阅 [排查 AZURE Sql 数据库和 azure sql 的连接问题和其他错误托管实例](troubleshoot-common-errors-issues.md) 和 [暂时性故障处理](/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling)。

## <a name="troubleshoot-blocking"></a>阻塞疑难解答

无论我们处于何种阻止情况，用于排查锁定问题的方法都是相同的。 这种逻辑分离将规定本文的其余部分。 其概念是查找 head 阻止程序，并识别该查询正在执行的操作及其阻止的原因。 确定有问题的查询后 (也就是说，长时间段内持有锁) ，下一步是分析并确定发生阻塞的原因。 了解原因后，我们可以通过重新设计查询和事务来做出更改。

故障排除步骤：

1. 标识主阻塞会话 (head 阻止) 

2. 查找导致阻塞的查询和事务 (长时间持有锁) 

3. 分析/了解发生长时间阻塞的原因

4. 通过重新设计查询和事务解决阻止问题

现在，让我们深入探讨如何使用适当的数据捕获来找出主阻塞会话。

## <a name="gather-blocking-information"></a>收集阻塞信息

为了应对阻碍性问题的疑难解答，数据库管理员可以使用在 Azure SQL 数据库中持续监视锁定状态和阻止状态的 SQL 脚本。 若要收集此数据，实质上有两种方法。 

第一种是 (DMOs) 查询动态管理对象，并存储与时间进行比较的结果。 本文中提到的某些对象是动态管理视图 (Dmv) ，一些是动态管理功能 (Dmf) 。 第二种方法是使用 XEvents 来捕获正在执行的操作。 


## <a name="gather-information-from-dmvs"></a>从 Dmv 收集信息

引用 Dmv 以排查阻塞问题的目标是确定 (会话 ID 的 SPID，) 阻塞链的头和 SQL 语句。 查找被阻止的牺牲品 Spid。 如果其他 SPID 正在阻止任何 SPID，则调查拥有该资源的 SPID (阻止的 SPID) 。 所有者 SPID 是否也被阻止？ 你可以遍历链来找到 head 阻止拦截，然后调查其锁定的原因。

请记得在目标 Azure SQL 数据库中运行其中的每个脚本。

* Sp_who 和 sp_who2 命令是用于显示所有当前会话的旧命令。 DMV sys.dm_exec_sessions 在结果集中返回更易于查询和筛选的数据。 你会发现其他查询的核心 sys.dm_exec_sessions。 

* 如果已确定特定的会话，则可以使用 `DBCC INPUTBUFFER(<session_id>)` 查找由会话提交的最后一条语句。 可以使用 sys.dm_exec_input_buffer 动态管理函数 (DMF) 返回类似的结果，该结果集中更易于查询和筛选，同时提供 session_id 和 request_id。 例如，若要返回 session_id 66 和 request_id 0 提交的最新查询：

```sql
SELECT * FROM sys.dm_exec_input_buffer (66,0);
```

* 请参阅 sys.dm_exec_requests，并引用 blocking_session_id 列。 当 blocking_session_id = 0 时，会话将不会被阻止。 虽然 sys.dm_exec_requests 仅列出了当前正在执行的请求，但任何活动的或不) 的 (连接将在 sys.dm_exec_sessions 中列出。 在下一次查询 sys.dm_exec_requests 和 sys.dm_exec_sessions 之间的此常见联接上构建。

* 运行此示例查询，使用 [sys.dm_exec_sql_text](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql) 或 [sys.dm_exec_input_buffer](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-input-buffer-transact-sql) dmv 查找主动执行的查询及其当前的 SQL 批处理文本或输入缓冲区文本。 如果 sys.dm_exec_sql_text 的字段返回的数据 `text` 为 NULL，则当前不执行该查询。 在这种情况下， `event_info` sys.dm_exec_input_buffer 的字段将包含传递给 SQL 引擎的最后一个命令字符串。 

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

* 若要捕获长时间运行或未提交的事务，请使用另一组 Dmv 来查看当前打开的事务，包括 [sys.dm_tran_database_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-database-transactions-transact-sql)、 [sys.dm_tran_session_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-session-transactions-transact-sql)、 [sys.dm_exec_connections](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-connections-transact-sql)和 sys.dm_exec_sql_text。 存在多个与跟踪事务关联的 Dmv，请在此处查看有关 [事务的](/sql/relational-databases/system-dynamic-management-views/transaction-related-dynamic-management-views-and-functions-transact-sql) 更多 dmv。 

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

* 在 SQL 的线程/任务层 [sys.dm_os_waiting_tasks](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) 引用。 这会返回有关请求当前遇到的 SQL 等待类型的信息。 与 sys.dm_exec_requests 一样，sys.dm_os_waiting_tasks 只返回活动请求。 

> [!Note]
> 有关等待类型的详细信息（包括随时间推移而聚合的等待统计信息），请参阅 DMV [sys.dm_db_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database)。 此 DMV 只返回当前数据库的聚合等待统计信息。

* 使用 [Sys.dm_tran_locks](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql) DMV，更详细地了解查询所放置的锁。 此 DMV 可以在生产 SQL Server 上返回大量数据，并且对于诊断当前持有的锁很有用。 

由于 sys.dm_os_waiting_tasks 上的内部联接，下面的查询将 sys.dm_tran_locks 输出限制为当前被阻止的请求、其等待状态和锁：

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

* 使用 Dmv，在一段时间内存储查询结果将提供数据点，这些数据点允许你查看在指定时间间隔内阻塞的时间，以确定持久化阻塞或趋势。 

## <a name="gather-information-from-extended-events"></a>从扩展事件中收集信息

除了上述信息之外，通常还需要捕获服务器上的活动跟踪，以彻底调查 Azure SQL 数据库上的阻塞问题。 例如，如果会话在一个事务内执行多个语句，则只会表示最后提交的语句。 但前面的语句之一可能是仍在持有锁的原因。 利用跟踪，你可以查看由当前事务中的会话执行的所有命令。

可以通过两种方式在 SQL Server 中捕获跟踪：扩展事件 (XEvents) 和探查器跟踪。 但是， [SQL Server Profiler](/sql/tools/sql-server-profiler/sql-server-profiler) 是 Azure SQL 数据库不支持的跟踪技术。 [扩展事件](/sql/relational-databases/extended-events/extended-events) 是一种较新的跟踪技术，它允许对观察到的系统进行更多通用性和更小的影响，并且其接口已集成到 SQL SERVER MANAGEMENT STUDIO (SSMS) 中。 

请参阅介绍如何使用 SSMS 中的 " [扩展事件" "新建会话向导](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server) " 的文档。 但对于 Azure SQL 数据库，SSMS 在对象资源管理器中的每个数据库下提供扩展事件子文件夹。 使用扩展事件会话向导捕获这些有用的事件： 

-   类别错误：
    -   Attention
    -   Error_reported  
    -   Execution_warning

-   类别警告： 
    -   Missing_join_predicate

-   类别执行：
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

## <a name="identify-and-resolve-common-blocking-scenarios"></a>识别和解决常见的阻止方案

通过检查上述信息，你可以确定大多数阻碍性问题的原因。 本文的其余部分将讨论如何使用此信息来识别和解决一些常见的阻止情况。 本讨论假设你使用了) 之前引用的阻塞脚本 (来捕获有关阻塞 Spid 的信息并使用 XEvent 会话捕获了应用程序活动。

## <a name="analyze-blocking-data"></a>分析阻塞数据 

* 使用 blocking_these 和 session_id 检查 Dmv sys.dm_exec_requests 的输出，并 sys.dm_exec_sessions 确定阻塞链的头。 这最能清楚地确定哪些请求被阻止，哪些请求被阻止。 进一步了解阻止和阻止的会话。 阻塞链是否有公用或根？ 它们可能共享一个公用表，而阻塞链中涉及的一个或多个会话执行写入操作。 

* 检查 Dmv sys.dm_exec_requests 的输出，并 sys.dm_exec_sessions 有关阻塞链开头的 Spid 的信息。 查找以下字段： 

    -    `sys.dm_exec_requests.status`  
    此列显示特定请求的状态。 通常，睡眠状态表明 SPID 已完成执行，正在等待应用程序提交另一个查询或批处理。 可运行或运行状态表明 SPID 当前正在处理查询。 下表提供了各种状态值的简短说明。

    | 状态 | 含义 |
    |:-|:-|
    | 背景 | SPID 正在运行一个后台任务，例如死锁检测、日志编写器或检查点。 |
    | Sleeping | SPID 当前未执行。 这通常表示 SPID 正在等待来自应用程序的命令。 |
    | 运行 | SPID 当前正在计划程序上运行。 |
    | 可运行 | SPID 位于计划程序的可运行队列中，正在等待获取计划程序时间。 |
    | 已挂起 | SPID 正在等待某个资源，如锁或闩锁。 | 
                       
    -    `sys.dm_exec_sessions.open_transaction_count`  
    此字段告诉您此会话中打开的事务数。 如果此值大于0，则 SPID 在打开的事务中，并可能持有事务中的任何语句所获取的锁。

    -    `sys.dm_exec_requests.open_transaction_count`  
    同样，此字段会告诉您此请求中打开的事务数。 如果此值大于0，则 SPID 在打开的事务中，并可能持有事务中的任何语句所获取的锁。

    -   `sys.dm_exec_requests.wait_type`、`wait_time` 和 `last_wait_type`  
    如果  `sys.dm_exec_requests.wait_type`   为 NULL，则表示请求当前未等待任何内容，并且  `last_wait_type`   值指示请求遇到的最后一个  `wait_type`   。 有关  `sys.dm_os_wait_stats` 最常见等待类型的详细信息和说明，请参阅 [sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)。 此  `wait_time`   值可用于确定请求是否正在进行。 当针对 sys.dm_exec_requests 表的查询返回的列中的值  `wait_time`   小于  `wait_time`   之前 sys.dm_exec_requests 的先前查询中的值时，这表示已获取并释放以前的锁，并且现在正在等待 (假定为非零) 的新锁 `wait_time` 。 这可以通过比较 `wait_resource`   sys.dm_exec_requests 输出（显示请求正在等待的资源）进行验证。

    -   `sys.dm_exec_requests.wait_resource` 此字段指示被阻止的请求正在等待的资源。 下表列出了常用  `wait_resource`   格式及其含义：

    | 资源 | 格式 | 示例 | 说明 | 
    |:-|:-|:-|:-|
    | 表 | DatabaseID： ObjectID： IndexID | 选项卡：5:261575970:1 | 在这种情况下，数据库 ID 5 是 pubs 示例数据库，对象 ID 261575970 是标题表，1是聚集索引。 |
    | 页 | DatabaseID： FileID： PageID | 页面：5:1:104 | 在这种情况下，数据库 ID 5 是 pubs，文件 ID 1 是主数据文件，页面104是属于标题表的页面。 若要标识页面所属的 object_id，请使用动态管理函数 [sys.dm_db_page_info](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-page-info-transact-sql)，并传入 DatabaseID、FileId、PageId `wait_resource` 。 | 
    | 键 | DatabaseID： Hobt_id 索引键 (哈希值)  | 密钥： 5:72057594044284928 (3300a4f361aa)  | 在这种情况下，数据库 ID 5 是 Pubs，Hobt_ID 72057594044284928 对应于 object_id 261575970 (标题表) index_id 2。 使用 sys.databases 目录视图将 hobt_id 关联到特定 index_id 并 object_id。 无法将索引键哈希进行解到特定键值。 |
    | 行 | DatabaseID： FileID： PageID：槽 (行)  | RID：5:1:104:3 | 在这种情况下，数据库 ID 5 是 pubs，文件 ID 1 是主数据文件，页104是属于标题表的页，槽3指示行在页面上的位置。 |
    | Compile  | DatabaseID： FileID： PageID：槽 (行)  | RID：5:1:104:3 | 在这种情况下，数据库 ID 5 是 pubs，文件 ID 1 是主数据文件，页104是属于标题表的页，槽3指示行在页面上的位置。 |

    -    `sys.dm_tran_active_transactions`[Sys.dm_tran_active_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-active-transactions-transact-sql) DMV 包含有关可联接到其他 dmv 的开放事务的数据，以获取等待提交或回滚的事务的完整图片。 使用以下查询返回有关已联接到其他 Dmv （包括 [sys.dm_tran_session_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-session-transactions-transact-sql)）的打开事务的信息。 请考虑事务的当前状态、 `transaction_begin_time` 和其他用例数据，以评估它是否可以是阻塞的源。

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

        [Sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql)和[sys.dm_exec_request](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql)中的其余列还可以深入了解问题的根源。 它们的有用性会因问题的发生而有所不同。 例如，你可以确定问题只发生于某些客户端 (hostname) ，在某些网络库上 (net_library) ，当 SPID 提交的最后一个批处理是 sys.dm_exec_sessions 时 `last_request_start_time` ，在 sys.dm_exec_requests 中使用了请求的时间， `start_time` 等等。


## <a name="common-blocking-scenarios"></a>常见的阻止方案

下表将常见症状映射到其可能的原因。  

`wait_type`、 `open_transaction_count` 和 `status` 列引用[sys.dm_exec_request](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql)返回的信息， [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql)可能返回其他列。 "解决？" 列指示阻塞是否会自行解析，或是否应通过命令终止会话 `KILL` 。 有关详细信息，请参阅 [KILL (transact-sql) ](/sql/t-sql/language-elements/kill-transact-sql)。

| 方案 | Waittype | Open_Tran | 状态 | 解析? | 其他症状 |  
|:-|:-|:-|:-|:-|:-|--|
| 1 | NOT NULL | >= 0 | 运行 | 是，当查询完成时。 | 在 sys.dm_exec_sessions 中， **读取**、 **cpu_time** 和/或 **memory_usage** 列将随着时间的推移而增加。 完成后，查询的持续时间将较高。 |
| 2 | Null | \>0 | 正在睡眠 | 不可以，但可以终止 SPID。 | 此 SPID 的扩展事件会话中可能出现了注意信号，指示发生了查询超时或取消。 |
| 3 | Null | \>= 0 | 运行 | 不能。 在客户端提取所有行或关闭连接之前，将无法解析。 可以终止 SPID，但可能最多需要30秒。 | 如果 open_transaction_count = 0，并且 SPID 持有锁，而事务隔离级别为默认值 (READ COMMMITTED) ，则可能是这种情况。 |  
| 4 | 多种多样 | \>= 0 | 运行 | 不能。 在客户端取消查询或关闭连接之前，将不会解析。 可以终止 Spid，但可能最多需要30秒。 | 阻塞链开头的 SPID sys.dm_exec_sessions 中的 " **主机名** " 列将与它阻止的一个 spid 相同。 |  
| 5 | Null | \>0 | 回滚 | 是的。 | 此 SPID 的扩展事件会话中可能出现了注意信号，指示已发生查询超时或取消，或者只是发出了 rollback 语句。 |  
| 6 | Null | \>0 | 正在睡眠 | 最终. 当 Windows NT 确定会话不再处于活动状态时，Azure SQL 数据库连接将中断。 | `last_request_start_time`Sys.dm_exec_sessions 中的值早于当前时间。 |

以下方案将在这些情况下扩展。 

1.  由运行时间较长的正常运行的查询导致的阻塞

    **解决** 方法：针对此类阻塞问题的解决方案是寻找优化查询的方法。 实际上，此类阻塞问题可能只是性能问题，要求你将其视为。 有关解决特定运行缓慢的查询的信息，请参阅 [如何解决 SQL Server 上的运行速度缓慢的查询](/troubleshoot/sql/performance/troubleshoot-slow-running-queries)。 有关详细信息，请参阅[监视和优化性能](/sql/relational-databases/performance/monitor-and-tune-for-performance)。 

    通过 SSMS 中的 [查询存储](/sql/relational-databases/performance/best-practice-with-the-query-store) 报告，还提供了一个很好的建议和有用的工具，用于确定最高成本的查询和不理想的执行计划。 另外，请查看 Azure SQL 数据库的 Azure 门户的 " [智能性能](intelligent-insights-overview.md) " 部分，其中包括 [Query Performance Insight](query-performance-insight-use.md)。

    如果有长时间运行的查询阻止其他用户并且无法优化，请考虑将其从 OLTP 环境移到专用报表系统（ [数据库的同步只读副本](read-scale-out.md)）。

1.  具有未提交事务的休眠 SPID 导致的阻塞

    此类阻塞通常可以由正在休眠或等待命令的 SPID 标识，但其事务嵌套级别 (的 `@@TRANCOUNT` `open_transaction_count` sys.dm_exec_requests) 大于零。 如果应用程序遇到查询超时，则可能出现此情况，或在未发出所需的 ROLLBACK 和/或 COMMIT 语句数的情况下发出取消。 当 SPID 收到查询超时或取消时，它将终止当前查询和批处理，但不会自动回滚或提交事务。 应用程序负责执行此操作，因为 Azure SQL 数据库不会假设必须回滚整个事务，因为一个查询被取消。 在扩展事件会话中，"查询超时" 或 "取消" 将作为 SPID 的 "注意" 信号事件显示。

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

    第二个查询的输出指示事务嵌套级别为1。 事务中获取的所有锁仍将保留，直到提交或回滚事务为止。 如果应用程序显式打开并提交事务，则通信或其他错误可能会使会话及其事务处于打开状态。 

    使用基于 sys.dm_tran_active_transactions 的上述脚本来确定当前未提交的事务。

    **解决方法**：

     -   此外，此类阻塞问题也可能是性能问题，要求你将其视为。 如果可以降低查询执行时间，则不会发生查询超时或取消。 应用程序可以处理超时或取消方案（如果出现），这一点很重要，但你也可以从检查查询的性能中获益。 
     
     -    应用程序必须正确管理事务嵌套级别，否则它们可能会导致在以这种方式取消查询后出现阻塞问题。 考虑以下情况：  

            *    在客户端应用程序的错误处理程序中，执行 `IF @@TRANCOUNT > 0 ROLLBACK TRAN` 任何错误，即使客户端应用程序不认为事务处于打开状态也是如此。 需要检查是否存在打开的事务，因为在批处理过程中调用的存储过程可能在没有客户端应用程序的知识的情况下启动了事务。 某些条件（如取消查询）会阻止过程在当前语句之后执行，因此即使过程具有检查 `IF @@ERROR <> 0` 和中止事务的逻辑，这种情况下也不会执行此回滚代码。  
            *    如果在打开连接的应用程序中使用连接池，并在将连接释放回池之前运行少量查询（例如基于 Web 的应用程序），则暂时禁用连接池可能有助于缓解此问题，直到修改客户端应用程序以相应地处理错误。 通过禁用连接池，释放连接将导致 Azure SQL 数据库连接物理断开连接，从而导致服务器回滚任何打开的事务。  
            *    用于 `SET XACT_ABORT ON` 连接，或在任何开始事务的存储过程中使用，不会在错误后进行清理。 发生运行时错误时，此设置将中止所有打开的事务并将控制权返回给客户端。 有关详细信息，请查看 [ (transact-sql) XACT_ABORT 集 ](/sql/t-sql/statements/set-xact-abort-transact-sql)。
    > [!NOTE]
    > 在从连接池中重用之前，不会重置连接，因此用户可能会打开事务，然后释放到连接池的连接，但它可能不会重复使用几秒钟，在这段时间内事务会保持打开状态。 如果不重复使用连接，则在连接超时并从连接池中删除时，将中止该事务。 因此，客户端应用程序最好在其错误处理程序中中止事务，或使用 `SET XACT_ABORT ON` 来避免这种潜在的延迟。

    > [!CAUTION]
    > 之后 `SET XACT_ABORT ON` ，将不会执行导致错误的语句后面的 t-sql 语句。 这可能会影响现有代码的预期流动。

1.  由其对应客户端应用程序未将所有结果行提取到完成的 SPID 导致的阻塞

    将查询发送到服务器后，所有应用程序都必须立即提取所有结果行完成。 如果应用程序不能提取所有结果行，则可以对表进行锁定，阻止其他用户。 如果你使用的应用程序以透明方式向服务器提交 SQL 语句，则应用程序必须提取所有结果行。 如果未 (并且无法将其配置为) ，则可能无法解决阻止性问题。 若要避免此问题，可以将性能不佳的应用程序限制在报告或决策支持数据库中。
    
    > [!NOTE]
    > 请参阅连接到 Azure SQL 数据库的应用程序的 [重试逻辑指南](./troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors) 。 
    
    **解决方法**：必须重新编写应用程序，以获取结果的所有行完成。 这不会排除 [在查询的 ORDER BY 子句中使用 OFFSET 和 FETCH](/sql/t-sql/queries/select-order-by-clause-transact-sql#using-offset-and-fetch-to-limit-the-rows-returned) 以执行服务器端分页。

1.  处于回滚状态的 SPID 导致的阻塞

    将回滚在用户定义的事务之外终止或取消的数据修改查询。 这也可能是客户端网络会话断开连接的副作用，或者作为死锁牺牲品选择的请求。 这通常可以通过观察 sys.dm_exec_requests 的输出来确定，这可能指示 ROLLBACK **命令**， **percent_complete 的列** 可能会显示进度。 

    由于2019中引入了 [加速数据库恢复功能](../accelerated-database-recovery.md) ，因此应极少发生较长的回滚。
    
    **解决方法**：等待 SPID 完成回滚所做的更改。 

    若要避免这种情况，请不要在 OLTP 系统上的繁忙时间执行大型批处理写入操作或索引创建或维护操作。 如果可能，请在活动较少时执行此类操作。

1.  孤立连接导致的阻塞

    如果客户端应用程序捕获到错误或重新启动客户端工作站，则在某些情况下可能不会立即取消到服务器的网络会话。 从 Azure SQL 数据库的角度看，客户端似乎仍存在，但仍会保留获取的任何锁。 有关详细信息，请参阅 [如何对 SQL Server 中的孤立连接进行故障排除](/sql/t-sql/language-elements/kill-transact-sql#remarks)。 

    **解决方法**：如果客户端应用程序已断开连接，但未正确清理其资源，则可以使用命令终止 SPID `KILL` 。 该 `KILL` 命令采用 SPID 值作为输入。 例如，若要终止 SPID 99，请发出以下命令：

    ```sql
    KILL 99
    ```

## <a name="see-also"></a>请参阅

* [Azure SQL 数据库与 Azure SQL 托管实例中的监视和性能优化](/azure/azure-sql/database/monitor-tune-overview)
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
* [暂时性故障处理](/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling)