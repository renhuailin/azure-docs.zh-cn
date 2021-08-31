---
title: 排查事务日志问题
titleSuffix: Azure SQL Database and Azure SQL Managed Instance
description: 提供对 Azure SQL 数据库或 Azure SQL 托管实例中的 Azure SQL 数据库事务日志问题进行排查的步骤
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.topic: troubleshooting
ms.custom: ''
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.date: 07/23/2021
ms.openlocfilehash: 0b9e47d23968c29a0c69a2da198bcf8d183f1bb0
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2021
ms.locfileid: "114675099"
---
# <a name="troubleshooting-transaction-log-errors-with-azure-sql-database-and-azure-sql-managed-instance"></a>排查 Azure SQL 数据库和 Azure SQL 托管实例的事务日志错误
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

事务日志已满且无法接受新事务时，你可能会看到错误 9002 或 40552。 当由 Azure SQL 数据库或 Azure SQL 托管实例管理的数据库事务日志超过空间阈值且无法继续接受事务时，便会发生这些错误。 

这些错误类似于 SQL Server 中事务日志已满的问题，但在 Azure SQL 数据库或 Azure SQL 托管实例中具有不同的解决方案。

> [!NOTE]
> **本文重点介绍 Azure SQL 数据库和 Azure SQL 托管实例。** Azure SQL 数据库和 Azure SQL 托管实例基于最新稳定版本的 Microsoft SQL Server 数据库引擎，因此很多内容是相似的，不过故障排除选项和工具可能有所不同。 有关排除 SQL Server 中事务日志故障的更多信息，请参阅[排除完整事务日志故障 (SQL Server Error 9002)](/sql/relational-databases/logs/troubleshoot-a-full-transaction-log-sql-server-error-9002)。

## <a name="automated-backups-and-the-transaction-log"></a>自动备份和事务日志

Azure SQL 数据库和 Azure SQL 托管实例在数据库文件空间管理方面存在一些主要差异。 

- 在 Azure SQL 数据库或 Azure SQL 托管实例中，事务日志备份会自动进行。 有关频率、保留期和更多信息，请参阅[自动备份 - Azure SQL 数据库和 SQL 托管实例](automated-backups-overview.md)。 
- 在 Azure SQL 数据库中，还可管理可用磁盘空间、数据库文件增长和文件位置，因此事务日志问题的常见原因和解决方案与 SQL Server 不同。 
- 在 Azure SQL 托管实例中，无法管理数据库文件的位置和名称，但管理员可以管理数据库文件和文件自动增长设置。 事务日志问题的常见原因和解决方法类似于 SQL Server。 

与 SQL Server 类似，每当进行日志备份时，每个数据库的事务日志都会被截断。 截断在日志文件中保留空白，之后日志文件可以访问新事务。 当日志文件无法由日志备份截断时，日志文件会增长，以容纳新事务。 如果日志文件在 Azure SQL 数据库或 Azure SQL 托管实例中增长到其上限，则无法接受新事务。 这是一种很不寻常的场景。

## <a name="prevented-transaction-log-truncation"></a>事务日志截断受阻

若要发现在特定情况下阻止日志截断的内容，请参阅 `sys.databases` 中的 `log_reuse_wait_desc`。 日志重复使用等待会告知哪些情况或原因会阻止常规日志备份截断事务日志。 有关详细信息，请参阅 [sys.databases (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql)。 

```sql
SELECT [name], log_reuse_wait_desc FROM sys.databases;
```

`sys.databases` 中 `log_reuse_wait_desc` 的以下值可能表示阻止数据库事务日志截断的原因：

| log_reuse_wait_desc | 诊断 | 要求响应 |
|--|--|--|
| **NOTHING** | 典型状态。 没有任何内容阻止日志截断。 | 否。 |
| **CHECKPOINT** | 日志截断需要检查点。 罕见。 | 除非持续，否则无需响应。 如果持续，请向 [Azure 支持](https://portal.azure.com/#create/Microsoft.Support)提交支持请求。 | 
| **LOG BACKUP** | 日志备份正在进行。 | 除非持续，否则无需响应。 如果持续，请向 [Azure 支持](https://portal.azure.com/#create/Microsoft.Support)提交支持请求。 | 
| **ACTIVE BACKUP OR RESTORE** | 数据库备份正在进行。 | 除非持续，否则无需响应。 如果持续，请向 [Azure 支持](https://portal.azure.com/#create/Microsoft.Support)提交支持请求。 | 
| **ACTIVE TRANSACTION** | 正在进行的事务在阻止日志截断。 | 由于活动事务和/或未提交的事务，无法截断日志文件。 请参阅下一节。| 
| **REPLICATION** | 在 Azure SQL 数据库中，可能由于[更改数据捕获 (CDC)](/sql/relational-databases/track-changes/about-change-data-capture-sql-server) 功能。<BR>在 Azure SQL 托管实例中，由于[复制](../managed-instance/replication-transactional-overview.md)或 CDC。 | 在 Azure SQL 数据库中查询 [sys.dm_cdc_errors](/sql/relational-databases/system-dynamic-management-views/change-data-capture-sys-dm-cdc-errors) 并解析错误。 如果错误无法解析，请向 [Azure 支持](https://portal.azure.com/#create/Microsoft.Support)提交支持请求。<BR>在 Azure SQL 托管实例中，如果持续，请调查与 CDC 或复制有关的代理。 若要排除 CDC 故障，请在 [msdb.dbo.cdc_jobs](/sql/relational-databases/system-tables/dbo-cdc-jobs-transact-sql) 中查询作业。 如果不存在，请通过 [sys.sp_cdc_add_job](/sql/relational-databases/system-stored-procedures/sys-sp-cdc-add-job-transact-sql) 添加。 对于复制，请考虑[排除事务复制故障](/sql/relational-databases/replication/troubleshoot-tran-repl-errors)。 如果错误无法解析，请向 [Azure 支持](https://portal.azure.com/#create/Microsoft.Support)提交支持请求。 | 
| **AVAILABILITY_REPLICA** | 正在同步到次要副本。 | 除非持续，否则无需响应。 如果持续，请向 [Azure 支持](https://portal.azure.com/#create/Microsoft.Support)提交支持请求。 | 

### <a name="log-truncation-prevented-by-an-active-transaction"></a>活动事务阻止的日志截断

对于不能接受新事务的事务日志，最常见的情况是长时间运行或受阻的事务。

运行此示例查询以查找未提交的事务或活动事务及其属性。

- 从 [sys.dm_tran_active_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-session-transactions-transact-sql) 返回有关事务属性的信息。
- 从 [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql) 返回会话连接信息。
- 从 [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) 返回请求信息（活动请求）。 此查询还可用于标识受阻止的会话，查找 `request_blocked_by`。 有关阻止的详细信息，请参阅[收集阻止信息](understand-resolve-blocking.md#gather-blocking-information)。 
- 使用 [sys.dm_exec_sql_text](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql) 或 [sys.dm_exec_input_buffer](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-input-buffer-transact-sql) DMV 返回当前请求的文本或输入缓冲区文本。 如果 `sys.dm_exec_sql_text` 的 `text` 字段返回的数据为 NULL，则表示请求未处于活动状态，但具有未完成的事务。 在这种情况下，`sys.dm_exec_input_buffer` 的 `event_info` 字段将包含传递给数据库引擎的最后一个命令字符串。 

```sql
SELECT [database_name] = db_name(s.database_id)
, tat.transaction_id, tat.transaction_begin_time, tst.session_id 
, session_open_transaction_count = tst.open_transaction_count --uncommitted and unrolled back transactions open. 
, transaction_duration_s = datediff(s, tat.transaction_begin_time, sysdatetime())
, input_buffer = ib.event_info
, request_text = CASE  WHEN r.statement_start_offset = 0 and r.statement_end_offset= 0 THEN left(est.text, 4000)
                       ELSE    SUBSTRING ( est.[text],    r.statement_start_offset/2 + 1, 
                                           CASE WHEN r.statement_end_offset = -1 THEN LEN (CONVERT(nvarchar(max), est.[text])) 
                                                ELSE r.statement_end_offset/2 - r.statement_start_offset/2 + 1
                                           END  )  END
, request_status = r.status
, request_blocked_by = r.blocking_session_id
, transaction_state = CASE tat.transaction_state    
                     WHEN 0 THEN 'The transaction has not been completely initialized yet.'
                     WHEN 1 THEN 'The transaction has been initialized but has not started.'
                     WHEN 2 THEN 'The transaction is active - has not been committed or rolled back.'
                     WHEN 3 THEN 'The transaction has ended. This is used for read-only transactions.'
                     WHEN 4 THEN 'The commit process has been initiated on the distributed transaction. This is for distributed transactions only. The distributed transaction is still active but further processing cannot take place.'
                     WHEN 5 THEN 'The transaction is in a prepared state and waiting resolution.'
                     WHEN 6 THEN 'The transaction has been committed.'
                     WHEN 7 THEN 'The transaction is being rolled back.'
                     WHEN 8 THEN 'The transaction has been rolled back.' END 
, transaction_name = tat.name
, azure_dtc_state    --Applies to: Azure SQL Database only
             =    CASE tat.dtc_state 
                 WHEN 1 THEN 'ACTIVE'
                 WHEN 2 THEN 'PREPARED'
                 WHEN 3 THEN 'COMMITTED'
                 WHEN 4 THEN 'ABORTED'
                 WHEN 5 THEN 'RECOVERED' END
, transaction_type = CASE tat.transaction_type    WHEN 1 THEN 'Read/write transaction'
                                             WHEN 2 THEN 'Read-only transaction'
                                             WHEN 3 THEN 'System transaction'
                                             WHEN 4 THEN 'Distributed transaction' END
, tst.is_user_transaction
, local_or_distributed = CASE tst.is_local WHEN 1 THEN 'Local transaction, not distributed' WHEN 0 THEN 'Distributed transaction or an enlisted bound session transaction.' END
, transaction_uow    --for distributed transactions. 
, s.login_time, s.host_name, s.program_name, s.client_interface_name, s.login_name, s.is_user_process
, session_cpu_time = s.cpu_time, session_logical_reads = s.logical_reads, session_reads = s.reads, session_writes = s.writes
, observed = sysdatetimeoffset()
FROM sys.dm_tran_active_transactions AS tat 
INNER JOIN sys.dm_tran_session_transactions AS tst  on tat.transaction_id = tst.transaction_id
INNER JOIN Sys.dm_exec_sessions AS s on s.session_id = tst.session_id 
LEFT OUTER JOIN sys.dm_exec_requests AS r on r.session_id = s.session_id
CROSS APPLY sys.dm_exec_input_buffer(s.session_id, null) AS ib 
OUTER APPLY sys.dm_exec_sql_text (r.sql_handle) AS est;
```


### <a name="file-management-to-free-more-space"></a>文件管理以释放更多空间

如果事务日志受阻而无法进行截断，在分配数据库文件时释放更多空间可能是解决方案的一部分。 但是，解决阻止事务日志文件截断的根本原因是关键。 

在某些情况下，临时创建更多磁盘空间可让长时间运行的事务完成，从而消除正常事务日志备份截断事务日志文件受阻的情况。 但是，释放分配中的空间也许只能在事务日志再次增长之前提供暂时的缓解。 

有关管理数据库和弹性池的文件空间的详细信息，请参阅[管理 Azure SQL 数据库中数据库的文件空间](file-space-manage.md)。


### <a name="error-40552-the-session-has-been-terminated-because-of-excessive-transaction-log-space-usage"></a>错误 40552：由于过度使用事务日志空间，已终止会话

``40552: The session has been terminated because of excessive transaction log space usage. Try modifying fewer rows in a single transaction.``

若要解决此问题，请尝试执行以下方法：

1. 此问题可能是插入、更新或删除操作造成的。 查看事务以避免不必要的写入。 通过立即实施批处理或拆分为多个较小的事务，来尝试减少操作的行数。 有关详细信息，请参阅[如何使用批处理来改善 SQL 数据库应用程序的性能](../performance-improve-use-batching.md)。
2. 此问题可能是索引重建操作造成的。 若要避免此问题，请确保以下公式为 true：（表中受影响的行数）乘以（更新的字段的平均大小，以字节为单位 + 80）< 2 GB。 对于大型表，请考虑仅对表的一些分区创建分区和执行索引维护。 有关详细信息，请参阅[创建分区表和索引](/sql/relational-databases/partitions/create-partitioned-tables-and-indexes?view=azuresqldb-current&preserve-view=true)。
3. 如果在使用 `bcp.exe` 实用程序或 `System.Data.SqlClient.SqlBulkCopy` 类执行大容量插入，则可尝试使用 `-b batchsize` 或 `BatchSize` 选项来限制在各事务中复制到服务器的行数。 有关详细信息，请参阅 [bcp Utility](/sql/tools/bcp-utility)。
4. 如果使用 `ALTER INDEX` 语句重新生成索引，请使用 `SORT_IN_TEMPDB = ON` 和 `ONLINE = ON` 选项。 有关详细信息，请参阅 [ALTER INDEX (Transact-SQL)](/sql/t-sql/statements/alter-index-transact-sql)。

> [!NOTE]
> 有关其他资源调控器错误的详细信息，请参阅[资源调控错误](troubleshoot-common-errors-issues.md#resource-governance-errors)。

## <a name="next-steps"></a>后续步骤

- [排查 Azure SQL 数据库和 Azure SQL 托管实例的连接问题和其他问题](troubleshoot-common-errors-issues.md)
- [排查 SQL 数据库和 SQL 托管实例中的临时连接错误](troubleshoot-common-connectivity-issues.md)


有关事务日志大小的信息，请参阅： 
- 有关单一数据库的 vCore 资源限制，请参阅[使用 vCore 购买模型的单一数据库的资源限制](resource-limits-vcore-single-databases.md)
- 有关弹性池的 vCore 资源限制，请参阅[使用 vCore 购买模型的弹性池的资源限制](resource-limits-vcore-elastic-pools.md)
- 有关单一数据库的 DTU 资源限制，请参阅[使用 DTU 购买模型的单一数据库的资源限制](resource-limits-dtu-single-databases.md)
- 有关弹性池的 DTU 资源限制，请参阅[使用 DTU 购买模型的弹性池的资源限制](resource-limits-dtu-elastic-pools.md)
- 有关 SQL 托管实例的资源限制，请参阅 [SQL 托管实例资源限制](../managed-instance/resource-limits.md)。

