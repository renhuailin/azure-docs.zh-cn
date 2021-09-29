---
title: 排查复制延迟问题 - Azure Database for MySQL
description: 了解如何使用 Azure Database for MySQL 只读副本排查复制延迟问题。
keywords: mysql, 排查, 复制延迟秒数
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: troubleshooting
ms.date: 01/13/2021
ms.openlocfilehash: fc206254180e2b5b35f10538bfb4940970b1609e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128560800"
---
# <a name="troubleshoot-replication-latency-in-azure-database-for-mysql"></a>排查 Azure Database for MySQL 中的复制延迟问题

[!INCLUDE[applies-to-mysql-single-flexible-server](includes/applies-to-mysql-single-flexible-server.md)]

使用[只读副本](concepts-read-replicas.md)功能可将数据从 Azure Database for MySQL 服务器复制到只读副本服务器。 可以通过将读取和报告查询从应用程序路由到副本服务器来横向扩展工作负荷。 此设置可减小源服务器上的压力。 它还改进了应用程序在缩放时的整体性能和延迟。

副本使用 MySQL 引擎的原生二进制日志 (binlog) 文件基于位置的复制技术以异步方式进行更新。 有关详细信息，请参阅 [MySQL binlog 文件基于位置的复制配置概述](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)。

辅助只读副本上的复制延迟取决于多个因素。 这些因素包括但不限于：

- 网络延迟。
- 源服务器上的事务量。
- 源服务器和辅助只读副本服务器的计算层。
- 在源服务器和辅助服务器上运行的查询。

在本文中，你将了解如何排查 Azure Database for MySQL 中的复制延迟问题。 你还将了解副本服务器上复制延迟增加的一些常见原因。

> [!NOTE]
> 本文包含对术语“从属”的引用，这是 Microsoft 不再使用的术语。 在从软件中删除该术语后，我们会将其从本文中删除。
>

## <a name="replication-concepts"></a>复制概念

启用了二进制日志时，源服务器会将已提交的事务写入二进制日志中。 二进制日志用于复制。 默认情况下，会为最多支持 16 TB 存储的所有新预配的服务器启用此日志。 每台副本服务器上运行两个线程。 一个线程是 IO 线程，另一个线程是 SQL 线程：

- IO 线程连接到源服务器并请求更新的二进制日志。 此线程接收二进制日志更新。 这些更新保存在副本服务器上，位于称为“中继日志”的本地日志中。
- SQL 线程读取中继日志，然后在副本服务器上应用数据更改。

## <a name="monitoring-replication-latency"></a>监视复制延迟

Azure Database for MySQL 在 [Azure Monitor](concepts-monitoring.md) 中提供了以秒为单位的复制延迟指标。 此指标仅适用于只读副本服务器。 它由 MySQL 中提供的 seconds_behind_master 指标计算得出。 

若要了解复制延迟增加的原因，请使用 [MySQL Workbench](connect-workbench.md) 或 [Azure Cloud Shell](https://shell.azure.com) 连接到副本服务器。 然后运行以下命令。

>[!NOTE]
> 在代码中，将示例值替换为你的副本服务器名称和管理员用户名。 对于 Azure Database for MySQL，管理员用户名需要包含 `@\<servername>`。

```azurecli-interactive
mysql --host=myreplicademoserver.mysql.database.azure.com --user=myadmin@mydemoserver -p 
```

下图显示了 Cloud Shell 终端中的该体验过程：

```
Requesting a Cloud Shell.Succeeded.
Connecting terminal...

Welcome to Azure Cloud Shell

Type "az" to use Azure CLI
Type "help" to learn about Cloud Shell

user@Azure:~$mysql -h myreplicademoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 64796
Server version: 5.6.42.0 Source distribution

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
mysql>
```

在同一 Cloud Shell 终端中，运行以下命令：

```
mysql> SHOW SLAVE STATUS;
```

下面是一个典型输出：
  
>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/howto-troubleshoot-replication-latency/show-status.png" alt-text="监视复制延迟":::

输出中包含许多信息。 通常，只需关注下表中所述的行。

|指标|说明|
|---|---|
|Slave_IO_State| 表示 IO 线程的当前状态。 通常情况下，如果源（主）服务器正在同步，则状态为“等待主服务器发送事件”。 诸如“正在连接到主服务器”之类的状态表示副本失去了到源服务器的连接。 请确保源服务器正在运行，或查看防火墙是否正在阻止连接。|
|Master_Log_File| 表示源服务器向其中写入内容的二进制日志文件。|
|Read_Master_Log_Pos| 指示源服务器在二进制日志文件中写入内容的位置。|
|Relay_Master_Log_File| 表示副本服务器从源服务器读取的二进制日志文件。|
|Slave_IO_Running| 指示 IO 线程是否正在运行。 此值应当为 `Yes`。 如果值为 `NO`，则复制可能已中断。|
|Slave_SQL_Running| 指示 SQL 线程是否正在运行。 此值应当为 `Yes`。 如果值为 `NO`，则复制可能已中断。|
|Exec_Master_Log_Pos| 指示副本正在应用的 Relay_Master_Log_File 的位置。 如果存在延迟，则此位置序号应小于 Read_Master_Log_Pos。|
|Relay_Log_Space|指示中继日志大小的上限。 可以通过查询 `SHOW GLOBAL VARIABLES`（例如 `relay_log_space_limit`）来检查大小。|
|Seconds_Behind_Master| 以秒为单位显示复制延迟。|
|Last_IO_Errno|显示 IO 线程错误代码（如果有）。 有关这些代码的详细信息，请参阅 [MySQL 服务器错误消息参考](https://dev.mysql.com/doc/mysql-errors/5.7/en/server-error-reference.html)。|
|Last_IO_Error| 显示 IO 线程错误消息（如果有）。|
|Last_SQL_Errno|显示 SQL 线程错误代码（如果有）。 有关这些代码的详细信息，请参阅 [MySQL 服务器错误消息参考](https://dev.mysql.com/doc/mysql-errors/5.7/en/server-error-reference.html)。|
|Last_SQL_Error|显示 SQL 线程错误消息（如果有）。|
|Slave_SQL_Running_State| 指示当前的 SQL 线程状态。 在此状态下，`System lock` 很常见。 `Waiting for dependent transaction to commit` 也是一种常见状态。 此状态表示副本正在等待源服务器更新已提交的事务。|

如果 Slave_IO_Running 为 `Yes` 且 Slave_SQL_Running 为 `Yes`，则复制正在正常运行。 

接下来，检查 Last_IO_Errno、Last_IO_Error、Last_SQL_Errno 和 Last_SQL_Error。  这些字段显示导致 SQL 线程停止的最新错误的错误号和错误消息。 错误号为 `0` 且消息为空意味着没有错误。 请通过检查 [MySQL 服务器错误消息参考](https://dev.mysql.com/doc/mysql-errors/5.7/en/server-error-reference.html)中的错误代码，调查任何非零错误值。

## <a name="common-scenarios-for-high-replication-latency"></a>复制延迟较高的常见场景

以下部分介绍了通常存在较高复制延迟的场景。

### <a name="network-latency-or-high-cpu-consumption-on-the-source-server"></a>源服务器上的网络延迟或高 CPU 消耗

如果看到以下值，则复制延迟可能是由源服务器上的高网络延迟或高 CPU 消耗导致的。

```
Slave_IO_State: Waiting for master to send event
Master_Log_File: the binary file sequence is larger then Relay_Master_Log_File, e.g. mysql-bin.00020
Relay_Master_Log_File: the file sequence is smaller than Master_Log_File, e.g. mysql-bin.00010
```

在这种情况下，IO 线程在源服务器上处于正在运行和等待状态。 源服务器已将内容写入到编号 20 的二进制日志文件。 副本最多已接收 10 号文件。 在此场景中，高复制延迟的主要因素是源服务器上的网络速度或高 CPU 使用率。  

在 Azure 中，区域内的网络延迟通常以毫秒为单位。 不同区域的延迟在数毫秒到数秒的范围内。

在大多数情况下，IO 线程与源服务器之间的连接延迟是由源服务器上的 CPU 使用率较高引起的。 IO 线程处理速度缓慢。 可以通过使用 Azure Monitor 检查源服务器上的 CPU 使用率和并发连接数来检测此问题。

如果在源服务器上没有发现 CPU 使用率较高，则问题可能是网络延迟。 如果网络延迟突然高得异常，请查看 [Azure 状态页](https://status.azure.com/status)来了解是否发生已知问题或中断。 

### <a name="heavy-bursts-of-transactions-on-the-source-server"></a>源服务器上大量事务突发

如果看到以下值，则导致复制延迟的原因可能是源服务器上大量事务突发。 

```
Slave_IO_State: Waiting for the slave SQL thread to free enough relay log space
Master_Log_File: the binary file sequence is larger then Relay_Master_Log_File, e.g. mysql-bin.00020
Relay_Master_Log_File: the file sequence is smaller then Master_Log_File, e.g. mysql-bin.00010
```

输出表明副本可以检索源服务器背后的二进制日志。 但是，副本 IO 线程表明中继日志空间已满。

网络速度没有导致延迟。 副本正在尝试追赶进度。 但更新后的二进制日志大小超出中继日志空间的上限。

若要排查此问题，请在源服务器上启用[慢速查询日志](concepts-server-logs.md)。 使用慢速查询日志标识源服务器上长时间运行的事务。 然后，优化所标识的查询以降低服务器上的延迟。 

此类复制延迟通常是由源服务器上的数据负载导致的。 遗憾的是，如果源服务器有每周或每月数据负载，则复制延迟不可避免。 在源服务器上的数据负载完成后，副本服务器最终会赶上进度。

### <a name="slowness-on-the-replica-server"></a>副本服务器上的速度缓慢

如果看到以下值，则问题可能出在副本服务器上。

```
Slave_IO_State: Waiting for master to send event
Master_Log_File: The binary log file sequence equals to Relay_Master_Log_File, e.g. mysql-bin.000191
Read_Master_Log_Pos: The position of master server written to the above file is larger than Relay_Log_Pos, e.g. 103978138
Relay_Master_Log_File: mysql-bin.000191
Slave_IO_Running: Yes
Slave_SQL_Running: Yes
Exec_Master_Log_Pos: The position of slave reads from master binary log file is smaller than Read_Master_Log_Pos, e.g. 13468882
Seconds_Behind_Master: There is latency and the value here is greater than 0
```

在此场景中，输出表明 IO 线程和 SQL 线程正在正常运行。 副本读取源服务器写入的同一个二进制日志文件。 但是，副本服务器上的某种延迟反映了来自源服务器的同一事务。

以下部分介绍了此类延迟的常见原因。

#### <a name="no-primary-key-or-unique-key-on-a-table"></a>表中没有主键或唯一键

Azure Database for MySQL 使用基于行的复制。 源服务器会将事件写入到二进制日志，在各个表行中记录更改。 然后，SQL 线程会将这些更改复制到副本服务器上的相应表行。 当表缺少主键或唯一键时，SQL 线程会扫描目标表中的所有行，以应用这些更改。 此扫描可能会导致复制延迟。

在 MySQL 中，主键是一个关联的索引，可确保快速进行查询，因为它不能包含 NULL 值。 如果使用 InnoDB 存储引擎，则表数据以物理方式组织，以基于主键执行超高速度的查找和排序。

建议在创建副本服务器之前，在源服务器中的表上添加主键。 在源服务器上添加主键，然后重新创建只读副本，这样有助于改进复制延迟。

使用以下查询来查明源服务器上缺少主键的表：

```sql
select tab.table_schema as database_name, tab.table_name 
from information_schema.tables tab left join 
information_schema.table_constraints tco 
on tab.table_schema = tco.table_schema 
and tab.table_name = tco.table_name 
and tco.constraint_type = 'PRIMARY KEY' 
where tco.constraint_type is null 
and tab.table_schema not in('mysql', 'information_schema', 'performance_schema', 'sys') 
and tab.table_type = 'BASE TABLE' 
order by tab.table_schema, tab.table_name;

```

#### <a name="long-running-queries-on-the-replica-server"></a>副本服务器上长时间运行的查询

副本服务器上的工作负荷可能会导致 SQL 线程滞后于 IO 线程。 副本服务器上长时间运行的查询是复制延迟较高的常见原因之一。 若要排查此问题，请在副本服务器上启用[慢速查询日志](concepts-server-logs.md)。

慢速查询可能会增加资源消耗或降低服务器的速度，使副本无法赶上源服务器的进度。 在这种情况下，请优化慢速查询。 更快的查询可防止 SQL 线程阻塞并显著改进复制延迟。

#### <a name="ddl-queries-on-the-source-server"></a>源服务器上的 DDL 查询

在源服务器上，数据定义语言 (DDL) 命令（例如 [`ALTER TABLE`](https://dev.mysql.com/doc/refman/5.7/en/alter-table.html)）可能需要花费较长的时间。 当 DDL 命令正在运行时，源服务器上可能会有数千个其他的查询在并行运行。 

复制 DDL 时，为了确保数据库的一致性，MySQL 引擎会在单个复制线程中运行 DDL。 在执行此任务期间，所有其他复制的查询会被阻止，它们必须等待，直到 DDL 操作在副本服务器上完成。 即使联机 DDL 操作也会导致此延迟。 DDL 操作会增加复制延迟。

如果在源服务器上启用了[慢速查询日志](concepts-server-logs.md)，则可以通过检查是否有已在源服务器上运行的 DDL 命令来检测此延迟问题。 通过索引删除、重命名和创建，你可以将 INPLACE 算法用于 ALTER TABLE。 你可能需要复制表数据并重新生成表。

通常，并发 DML 支持 INPLACE 算法。 但在准备和运行操作时，可以在表上简单地采用排他的元数据锁。 因此，对于 CREATE INDEX 语句，可以使用子句 ALGORITHM 和 LOCK 来影响用于表复制的方法以及用于读取和写入的并发级别。 你仍可以通过添加 FULLTEXT 索引或 SPATIAL 索引来阻止 DML 操作。

以下示例使用 ALGORITHM 和 LOCK 子句来创建索引。

```sql
ALTER TABLE table_name ADD INDEX index_name (column), ALGORITHM=INPLACE, LOCK=NONE;
```

遗憾的是，对于需要锁的 DDL 语句，你无法避免复制延迟。 若要减少潜在影响，请在非高峰时间（例如在晚上）执行这些类型的 DDL 操作。

#### <a name="downgraded-replica-server"></a>降级的副本服务器

在 Azure Database for MySQL 中，只读副本使用与源服务器相同的服务器配置。 可以在创建副本服务器后更改其配置。

如果副本服务器已降级，则工作负荷可能会占用更多资源，进而导致复制延迟。 若要检测此问题，请使用 Azure Monitor 来检查副本服务器的 CPU 和内存消耗。

在这种情况下，建议你在保留副本服务器的配置时，使其值等于或大于源服务器的值。 此配置使副本服务器能够跟上源服务器的进度。

#### <a name="improving-replication-latency-by-tuning-the-source-server-parameters"></a>通过优化源服务器参数改进复制延迟

在 Azure Database for MySQL 中，默认情况下，复制经过优化后可在副本上通过并行线程运行。 如果源服务器上的高并发工作负荷导致副本服务器滞后，则可以通过在源服务器上配置参数 binlog_group_commit_sync_delay 来改进复制延迟。

binlog_group_commit_sync_delay 参数控制在同步二进制日志文件之前，二进制日志提交操作需要等待多少微秒。 使用此参数的优点是，源服务器会批量发送二进制日志更新，而不是立即应用每个已提交的事务。 此延迟减少了副本上的 IO，有助于提高性能。

可以将 binlog_group_commit_sync_delay 参数设置为 1000 左右。 然后，监视复制延迟。 请谨慎设置此参数，仅将其用于高并发工作负荷。

> [!IMPORTANT]
> 在副本服务器中，建议将参数 binlog_group_commit_sync_delay 设置为 0。 这是因为副本服务器与源服务器不同，它没有高并发性，并且增加副本服务器上 binlog_group_commit_sync_delay 的值可能会无意中导致复制延迟增加。

对于包含许多单一实例事务的低并发工作负荷，binlog_group_commit_sync_delay 设置可能会增加延迟。 延迟可能会增加，因为 IO 线程会等待批量二进制日志更新，即使只提交了几个事务。

## <a name="next-steps"></a>后续步骤

查看 [MySQL binlog 复制概述](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)。
