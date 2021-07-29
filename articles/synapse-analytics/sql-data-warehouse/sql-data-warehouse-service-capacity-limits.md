---
title: 专用 SQL 池的容量限制
description: Azure Synapse Analytics 中专用 SQL 池的各个组件所允许的最大值。
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 2/19/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: d915577cd6729ead60a00250e186fb2df444b3b1
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2021
ms.locfileid: "112029164"
---
# <a name="capacity-limits-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics 中专用 SQL 池的容量限制

Azure Synapse Analytics 中专用 SQL 池的各个组件所允许的最大值。

## <a name="workload-management"></a>工作负荷管理

| 类别 | 说明 | 最大值 |
|:--- |:--- |:--- |
| [数据仓库单位 (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |单个专用 SQL 池的最大 DWU  | Gen1：DW6000<br></br>Gen2：DW30000c |
| [数据仓库单位 (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |每个服务器的默认 DTU |54,000<br></br>默认情况下，每个 SQL Server（例如 myserver.database.windows.net）的 DTU 配额为 54,000，最多可以允许 DW6000c。 此配额仅仅只是安全限制。 可以通过[创建支持票证](sql-data-warehouse-get-started-create-support-ticket.md)并选择“配额”  作为请求类型来增加配额。  若要计算 DTU 需求，请将所需的 DWU 总数乘以 7.5 或将所需的 cDWU 总数乘以 9。 例如：<br></br>DW6000 x 7.5 = 45,000 DTU<br></br>DW7500c x 9 = 67,500 DTU。<br></br>可以在门户中的 SQL Server 选项中查看当前 DTU 消耗量。 已暂停和未暂停的数据库都计入 DTU 配额。 |
| 数据库连接 |并发打开的最大会话数 |1024<br/><br/>并发打开的会话数因所选 DWU 而异。 DWU1000c 及更高版本支持最多 1024 个打开的会话。 DWU500c 及更低版本支持最多 512 个并发打开的会话。 请注意，可并发执行的查询数量是有限制的。 当超出并发限制时，请求将进入内部队列等待处理。 |
| 数据库连接 |预处理语句的最大内存 |20 MB |
| [工作负荷管理](resource-classes-for-workload-management.md) |并发查询数上限 |128<br/><br/>  最多可执行 128 个并发查询，其余查询将排队。<br/><br/>将用户分配到更高的资源类或降低[数据仓库单位](memory-concurrency-limits.md)设置时，并发查询的数量可能会减少。 某些查询（例如 DMV 查询）始终允许运行，并且不会影响并发查询限制。 若要详细了解如何执行并发查询，请参阅[并发最大值](memory-concurrency-limits.md)一文。 |
| [tempdb](sql-data-warehouse-tables-temporary.md) |最大 GB |每 DW100c 399 GB。 在使用 DWU1000c 的情况下，tempdb 的大小为 3.99 TB。 |
||||

## <a name="database-objects"></a>数据库对象

| 类别 | 说明 | 最大值 |
|:--- |:--- |:--- |
| 数据库 |最大大小 | Gen1：磁盘上压缩后 240 TB。 此空间与 tempdb 或日志空间无关，因此，此空间专用于永久表。  聚集列存储压缩率估计为 5 倍。  此压缩率允许数据库在所有表都为聚集列存储（默认表类型）的情况下增长到大约 1 PB。 <br/><br/> Gen2：列存储表的存储空间不受限制。  数据库的行存储部分仍限制为在磁盘上的压缩大小为 240 TB。 |
| 表 |最大大小 |列存储表的大小不受限制。 <br>磁盘上压缩的行存储表的大小为 60 TB。 |
| 表 |每个数据库的表数 | 100,000 |
| 表 |每个表的列数 |1024 个列 |
| 表 |每个列的字节数 |取决于列[数据类型](sql-data-warehouse-tables-data-types.md)。 char 数据类型的限制为 8000，nvarchar 数据类型的限制为 4000，MAX 数据类型的限制为 2 GB。 |
| 表 |每行的字节数，定义的大小 |8060 字节<br/><br/>每行字节数的计算方式同于使用页面压缩的 SQL Server。 与 SQL Server 一样，支持行溢出存储，这样可以将 **可变长度列** 脱行推送。 对可变长度行进行拖行推送时，只将 24 字节的根存储在主记录中。 有关详细信息，请参阅[超过 8 KB 的行溢出数据](/previous-versions/sql/sql-server-2008-r2/ms186981(v=sql.105))。 |
| 表 |每个表的分区数 |15,000<br/><br/>为了实现高性能，建议在满足业务需求的情况下尽量减少所需的分区数。 随着分区数目的增长，数据定义语言 (DDL) 和数据操作语言 (DML) 操作的开销也会增长，导致性能下降。 |
| 表 |每个分区边界值的字符数。 |4000 |
| 索引 |每个表的非聚集索引数。 |50<br/><br/>仅适用于行存储表。 |
| 索引 |每个表的聚集索引数。 |1<br><br/>适用于行存储和列存储表。 |
| 索引 |索引键大小。 |900 字节。<br/><br/>仅适用于行存储索引。<br/><br/>如果创建索引时列中的现有数据未超过 900 字节，那么可以创建最大大小超过 900 字节的 varchar 列上的索引。 但是，以后导致总大小超过 900 字节的对列的 INSERT 或 UPDATE 操作将失败。 |
| 索引 |每个索引的键列数。 |16<br/><br/>仅适用于行存储索引。 聚集列存储索引包括所有列。 |
| 统计信息 |组合的列值的大小。 |900 字节。 |
| 统计信息 |每个统计对象的列数。 |32 |
| 统计信息 |每个表的列上创建的统计信息条数。 |30,000 |
| 存储过程 |最大嵌套级数。 |8 |
| 视图 |每个视图的列数 |1,024 |
||||

## <a name="loads"></a>加载

| 类别 | 说明 | 最大值 |
|:--- |:--- |:--- |
| Polybase 加载 |每行 MB 数 |1<br/><br/>Polybase 加载小于 1 MB 的行。 不支持将 LOB 数据类型加载到具有聚集列存储索引 (CCI) 的表。<br/> |
|Polybase 加载|文件总数|1,000,000<br/><br/>Polybase 加载不能超过 1 百万个文件。 你可能会遇到以下错误：“操作失败，因为拆分计数超过上限 1000000”。|

## <a name="queries"></a>查询

| 类别 | 说明 | 最大值 |
|:--- |:--- |:--- |
| 查询 |用户表的排队查询数。 |1000 |
| 查询 |系统视图的并发查询数。 |100 |
| 查询 |系统视图的排队查询数。 |1000 |
| 查询 |最大值参数 |2098 |
| 批处理 |最大大小 |65,536*4096 |
| SELECT 结果 |每个行的列数 |4096<br/><br/>在 SELECT 结果中每行的列数始终不得超过 4096。 无法保证最大值始终为 4096。 如果查询计划需要一个临时表，则可能应用每个表最多 1024 列的最大值。 |
| SELECT |嵌套子查询 |32<br/><br/>在 SELECT 语句中的嵌套子查询数始终不得超过 32 个。 无法保证最大值始终为 32 个。 例如，JOIN 可以将子查询引入查询计划。 还可以通过可用内存来限制子查询的数量。 |
| SELECT |每个 JOIN 的列数 |1024 个列<br/><br/>JOIN 中的列数始终不得超过 1024。 无法保证最大值始终为 1024。 如果 JOIN 计划需要列数多于 JOIN 结果的临时表，那么将 1024 限制应用于此临时表。 |
| SELECT |每个 GROUP BY 列的字节数。 |8060<br/><br/>GROUP BY 子句中的列的字节数最大为 8060 字节。 |
| SELECT |每个 ORDER BY 列的字节数 |8060 字节<br/><br/>ORDER BY 子句中的列的字节数最大为 8060 字节 |
| 每个语句的标识符数 |被引用的标识符数 |65,535<br/><br/> 可以包含在一条查询的单个表达式中的标识符数会受到限制。 超过此数字会导致 SQL Server 错误 8632。 有关详细信息，请参阅[内部错误：已达到表达式服务限制](https://support.microsoft.com/help/913050/error-message-when-you-run-a-query-in-sql-server-2005-internal-error-a)。 |
| 字符串文本 | 一个语句中字符串文本的数量 | 20,000 <br/><br/>一条查询的单个表达式中字符串常量的数量会受到限制。 超过此数字会导致 SQL Server 错误 8632。|
||||

## <a name="metadata"></a>Metadata

专用 SQL 池暂停或缩放时，DMV 将重置。

| 系统视图 | 最大行数 |
|:--- |:--- |
| [sys.dm_pdw_dms_cores](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-cores-transact-sql?view=azure-sqldw-latest&preserve-view=true) |100 |
| [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?view=azure-sqldw-latest&preserve-view=true) |最近 1000 个 SQL 请求的 DMS 辅助角色的总数。 |
| [sys.dm_pdw_errors](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-errors-transact-sql?view=azure-sqldw-latest&preserve-view=true) |10,000 |
| [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest&preserve-view=true) |10,000 |
| [sys.dm_pdw_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-sessions-transact-sql?view=azure-sqldw-latest&preserve-view=true) |10,000 |
| [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?view=azure-sqldw-latest&preserve-view=true) |sys.dm_pdw_exec_requests 中存储的最近 1000 个 SQL 请求的步骤总数。 |
| [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?view=azure-sqldw-latest&preserve-view=true) |sys.dm_pdw_exec_requests 中存储的最近 1000 个 SQL 请求。 |
|||

## <a name="next-steps"></a>后续步骤

有关使用 Azure Synapse 的建议，请参阅[速查表](cheat-sheet.md)。