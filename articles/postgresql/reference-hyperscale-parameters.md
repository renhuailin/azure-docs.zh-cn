---
title: 服务器参数 - 超大规模 (Citus) - Azure Database for PostgreSQL
description: 超大规模 (Citus) SQL API 中的参数
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 08/10/2020
ms.openlocfilehash: 07f966c7b0be542f848f1a0a4eaf2b5549735b4b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "91336235"
---
# <a name="server-parameters"></a>服务器参数

有各种服务器参数会影响超大规模 (Citus) 的行为，这些参数既包括标准 PostgreSQL 中的参数，也包括特定于超大规模 (Citus) 的参数。
可以在 Azure 门户中为超大规模 (Citus) 服务器组设置这些参数。 在“设置”类别下，选择“工作器节点参数”或“协调器节点参数”  。 在这些页面中可为所有工作器节点设置参数，或者只是为协调器节点设置参数。

## <a name="hyperscale-citus-parameters"></a>超大规模 (Citus) 参数

> [!NOTE]
>
> 运行旧版 Citus 引擎的超大规模 (Citus) 服务器组不一定提供下面列出的所有参数。

### <a name="general-configuration"></a>常规配置

#### <a name="citususe_secondary_nodes-enum"></a>citus.use\_secondary\_nodes（枚举）

设置选择 SELECT 查询的节点时要使用的策略。 如果设置为“always”，则规划器仅查询 [pg_dist_node](reference-hyperscale-metadata.md#worker-node-table) 中标记为“secondary”节点角色的节点。

此枚举支持的值为：

-   never：（默认值）所有读取都在主要节点上发生。
-   always：读取针对辅助节点运行，而插入/更新语句将会禁用。

#### <a name="cituscluster_name-text"></a>citus.cluster\_name（文本）

告知协调器节点规划器它要协调哪个群集。 设置 cluster\_name 后，规划器只会查询该群集中的工作器节点。

#### <a name="citusenable_version_checks-boolean"></a>citus.enable\_version\_checks（布尔值）

升级超大规模 (Citus) 版本需要重启服务器（以选取新的共享库），然后运行 ALTER EXTENSION UPDATE 命令。  执行这两个步骤失败可能会导致出错或崩溃。
超大规模 (Citus) 因而会验证代码版本和扩展版本是否匹配，如果不匹配，则会出错。

此值默认为 true，并在协调器上生效。 在罕见的情况下，复杂的升级过程可能要求将此参数设置为 false，从而禁用检查。

#### <a name="cituslog_distributed_deadlock_detection-boolean"></a>citus.log\_distributed\_deadlock\_detection（布尔值）

是否在服务器日志中记录分布式死锁检测相关的处理。 默认值为 false。

#### <a name="citusdistributed_deadlock_detection_factor-floating-point"></a>citus.distributed\_deadlock\_detection\_factor（浮点值）

设置在检查分布式死锁之前要等待的时间。 具体而言，等待时间是将此值与 PostgreSQL 的 [deadlock\_timeout](https://www.postgresql.org/docs/current/static/runtime-config-locks.html) 设置相乘后得到的结果。 默认值为 `2`。 指定 `-1` 值会禁用分布式死锁检测。

#### <a name="citusnode_connection_timeout-integer"></a>citus.node\_connection\_timeout（整数）

`citus.node_connection_timeout` GUC 设置等待建立连接的最大持续时间（以毫秒为单位）。 如果在至少建立一个工作器连接之前已超时，超大规模 (Citus) 将引发错误。 此 GUC 会影响从协调器到工作器的连接，以及工作器彼此之间的连接。

-   默认值：5 秒
-   最小值：10 毫秒
-   最大值：1 小时

```postgresql
-- set to 30 seconds
ALTER DATABASE foo
SET citus.node_connection_timeout = 30000;
```

### <a name="query-statistics"></a>查询统计信息

#### <a name="citusstat_statements_purge_interval-integer"></a>citus.stat\_statements\_purge\_interval（整数）

设置维护守护程序从 [citus_stat_statements](reference-hyperscale-metadata.md#query-statistics-table) 中删除不匹配 `pg_stat_statements` 的记录的频率。 此配置值设置清除时间间隔（以秒为单位），默认值为 10。 指定 0 值会禁用清除。

```psql
SET citus.stat_statements_purge_interval TO 5;
```

此参数在协调器上生效，可在运行时更改。

### <a name="data-loading"></a>数据加载

#### <a name="citusmulti_shard_commit_protocol-enum"></a>citus.multi\_shard\_commit\_protocol（枚举）

设置对哈希分布式表执行复制时要使用的提交协议。 在每个分片位置，复制将在事务块中执行，以确保在复制期间出错时不会引入任何数据。 但存在一种特殊的故障情况：复制在所有位置成功，但在提交所有事务之前发生（硬件）故障。 使用此参数可在以下提交协议之间进行选择，以防止在出现这种情况时丢失数据：

-   2pc：（默认）首先使用 PostgreSQL 的[两阶段提交](http://www.postgresql.org/docs/current/static/sql-prepare-transaction.html)来准备在分片位置执行复制时所在的事务，然后提交这些事务。 可以使用 COMMIT PREPARED 或 ROLLBACK PREPARED 来分别手动恢复或中止失败的提交。
    使用 2pc 时，应在所有工作器上增大 [max\_prepared\_transactions](http://www.postgresql.org/docs/current/static/runtime-config-resource.html)，通常应将设置为与 max\_connections 相同的值。
-   1pc：通过一个轮次提交在分片位置执行复制时所在的事务。 如果在所有位置复制成功后提交失败，则可能会丢失数据（这种情况很罕见）。

#### <a name="citusshard_replication_factor-integer"></a>citus.shard\_replication\_factor（整数）

设置分片的复制因子，即，要将分片放置到的节点数，默认值为 1。 此参数可在运行时设置，并在协调器上生效。 此参数的理想值取决于群集大小和节点故障率。  例如，如果你运行大型群集并发现节点故障更频繁发生，则可能需要增大此复制因子。

#### <a name="citusshard_count-integer"></a>citus.shard\_count（整数）

为哈希分区表设置分片计数，默认值为 32。  创建哈希分区表时，此值将由 [create_distributed_table](reference-hyperscale-functions.md#create_distributed_table) UDF使用。 此参数可在运行时设置，并在协调器上生效。

#### <a name="citusshard_max_size-integer"></a>citus.shard\_max\_size（整数）

设置分片在被拆分之前要增长到的最大大小，默认值为 1 GB。 如果一个分片的源文件大小（用于暂存）超过此配置值，数据库会确保创建新分片。 此参数可在运行时设置，并在协调器上生效。

### <a name="planner-configuration"></a>规划器配置

#### <a name="cituslimit_clause_row_fetch_count-integer"></a>citus.limit\_clause\_row\_fetch\_count（整数）

设置每个任务要提取的行数以进行 limit 子句优化。
在某些情况下，包含 limit 子句的 select 查询可能需要从每个任务中提取所有行来生成结果。 在这种情况下，以及当近似值会生成有意义的结果时，此配置值将设置要从每个分片中提取的行数。 默认会禁用 limit 近似值，此参数设置为 -1。 此值可在运行时设置，并在协调器上生效。

#### <a name="cituscount_distinct_error_rate-floating-point"></a>citus.count\_distinct\_error\_rate（浮点值）

超大规模 (Citus) 可以使用 postgresql-hll 扩展计算 count(distinct) 近似值。 在计算 count(distinct) 时，此配置条目将设置所需的错误率。 指定 0.0（默认值）会禁用 count(distinct) 的近似值；指定 1.0 不能保证结果的准确性。 建议将此参数设置为 0.005 以获得最佳结果。 此值可在运行时设置，并在协调器上生效。

#### <a name="citustask_assignment_policy-enum"></a>citus.task\_assignment\_policy（枚举）

> [!NOTE]
> 仅当 [shard_replication_factor](reference-hyperscale-parameters.md#citusshard_replication_factor-integer) 大于 1 时，此 GUC 才适用；或者，此 GUC 仅适用于针对 [reference_tables](concepts-hyperscale-distributed-data.md#type-2-reference-tables) 运行的查询。

设置将任务分配给工作器时要使用的策略。 协调器基于分片位置将任务分配给工作器。 此配置值指定进行这些分配时要使用的策略。
目前可以使用三个可能的任务分配策略。

-   贪婪：贪婪策略是默认策略，旨在将任务均匀分配到工作器。
-   轮循机制：轮循机制策略以轮循方式将任务分配到工作器（在不同的副本之间交替分配）。 当表的分片计数小于工作器数量时，此策略可以提高群集利用率。
-   首个副本：首个副本策略基于分片的位置（副本）插入顺序分配任务。 换言之，对某个分片的片段查询将分配到包含该分片的第一个副本的工作器。
    使用此方法可以在有关要在哪些节点上使用哪些分片方面获得可靠的保证（即，获取更可靠的内存驻留保证）。

此参数可在运行时设置，并在协调器上生效。

### <a name="intermediate-data-transfer"></a>中间数据传输

#### <a name="citusbinary_worker_copy_format-boolean"></a>citus.binary\_worker\_copy\_format（布尔值）

使用二进制复制格式在工作器之间传输中间数据。
在联接大型表的过程中，超大规模 (Citus) 可能需要动态重新分区，并在不同的工作器之间随机排布数据。 默认情况下，将以文本格式传输此数据。 启用此参数会指示数据库使用 PostgreSQL 的二进制序列化格式传输此数据。 此参数在工作器上生效，需要在 postgresql 文件中进行更改。 编辑配置文件后，用户可以发送 SIGHUP 信号或重启服务器，使此更改生效。

#### <a name="citusbinary_master_copy_format-boolean"></a>citus.binary\_master\_copy\_format（布尔值）

使用二进制复制格式在协调器与工作器之间传输数据。 运行分布式查询时，工作器会将其中间结果传输到协调器进行最终聚合。 默认情况下，将以文本格式传输此数据。 启用此参数会指示数据库使用 PostgreSQL 的二进制序列化格式传输此数据。
此参数可在运行时设置，并在协调器上生效。

#### <a name="citusmax_intermediate_result_size-integer"></a>citus.max\_intermediate\_result\_size（整数）

无法推送到工作器节点以供执行的 CTE 以及复杂子查询的中间结果的最大大小 (KB)。 默认值为 1 GB，值 -1 表示无限制。
超过限制的查询将被取消并生成错误消息。

### <a name="ddl"></a>DDL

#### <a name="citusenable_ddl_propagation-boolean"></a>citus.enable\_ddl\_propagation（布尔值）

指定是否将协调器中发生的 DDL 更改自动传播到所有工作器。 默认值为 true。 由于某些架构更改需要在表中设置一个访问排他锁，并且自动传播按顺序应用于所有工作器，因此它可能会导致超大规模 (Citus) 群集的响应能力暂时降低。 你可以选择禁用此设置并手动传播更改。

### <a name="executor-configuration"></a>执行程序配置

#### <a name="general"></a>常规

##### <a name="citusall_modifications_commutative"></a>citus.all\_modifications\_commutative

超大规模 (Citus) 强制实施交换性规则，并获取修改操作的相应锁以保证行为的正确性。 例如，假设一个 INSERT 语句可与另一个 INSERT 语句交换，但不可与某个 UPDATE 或 DELETE 语句交换。 同理，假设一个 UPDATE 或 DELETE 语句不可与另一个 UPDATE 或 DELETE 语句交换。 这种防备意味着 UPDATE 和 DELETE 语句要求超大规模 (Citus) 获取更强的锁。

如果你的 UPDATE 语句可与 INSERT 或其他 UPDATE 语句交换，则你可以通过将此参数设置为 true 来放宽这些交换性假设。 当此参数设置为 true 时，所有命令将被视为可交换并声明一个共享锁，这可以提高总体吞吐量。 此参数可在运行时设置，并在协调器上生效。

##### <a name="citusremote_task_check_interval-integer"></a>citus.remote\_task\_check\_interval（整数）

设置超大规模 (Citus) 检查任务跟踪器执行程序所管理作业的状态的频率。 默认值为 10 毫秒。 协调器将任务分配给工作器，然后定期在工作器上检查每个任务的进度。 此配置值设置每两次后续检查的时间间隔。 此参数在协调器上生效，可在运行时设置。

##### <a name="citustask_executor_type-enum"></a>citus.task\_executor\_type（枚举）

超大规模 (Citus) 提供三种执行程序类型用于运行分布式 SELECT 查询。  可以通过设置此配置参数来选择所需的执行程序。 此参数的可接受值为：

-   adaptive：默认值。 要快速响应涉及到跨多个分片进行聚合与并置联接的查询，这是最佳值。
-   task-tracker：task-tracker 执行程序非常适合用于需要跨工作器节点随机排布数据的长时间运行的复杂查询，以及实现高效的资源管理。
-   real-time：（已弃用）与 adaptive 执行程序的作用类似，但灵活性更低，可能会导致工作器节点上的连接压力增大。

此参数可在运行时设置，并在协调器上生效。

##### <a name="citusmulti_task_query_log_level-enum-multi_task_logging"></a>citus.multi\_task\_query\_log\_level（枚举）{#multi_task_logging}

为生成多个任务（即，命中多个分片）的任何查询设置日志级别。 在迁移多租户应用程序期间，日志记录非常有用，因为你可以选择对此类查询显示错误或警告、查找这些查询并对其添加 tenant\_id 筛选器。 此参数可在运行时设置，并在协调器上生效。 此参数的默认值为 \'off\'。

此枚举支持的值为：

-   off：禁用记录任何生成多个任务（即，命中多个分片）的查询
-   debug：在“调试”严重性级别记录语句。
-   log：在“日志”严重性级别记录语句。 日志行将包含运行的 SQL 查询。
-   notice：在“通知”严重性级别记录语句。
-   warning：在“警告”严重性级别记录语句。
-   error：在“错误”严重性级别记录语句。

有利的用法可能是在开发测试期间使用 `error`，在实际生产部署期间使用更低的日志级别，例如 `log`。
选择 `log` 会导致多任务查询显示在数据库日志中，查询本身显示在 \"STATEMENT\" 之后。

```
LOG:  multi-task query about to be executed
HINT:  Queries are split to multiple tasks if they have to be split into several queries on the workers.
STATEMENT:  select * from foo;
```

##### <a name="citusenable_repartition_joins-boolean"></a>citus.enable\_repartition\_joins（布尔值）

一般情况下，尝试使用 adaptive 执行程序执行重新分区联接会失败并出现错误消息。  但是，将 `citus.enable_repartition_joins` 设置为 true 可使超大规模 (Citus) 能够暂时切换到 task-tracker 执行程序来执行联接。  默认值为 false。

#### <a name="task-tracker-executor-configuration"></a>任务跟踪器执行程序配置

##### <a name="citustask_tracker_delay-integer"></a>citus.task\_tracker\_delay（整数）

此参数设置每完成一个任务管理轮次后任务跟踪器的睡眠时间，默认值为 200 毫秒。 任务跟踪器进程定期唤醒，逐个查看分配给它的所有任务，然后计划并执行这些任务。  然后，任务跟踪器将睡眠一段时间，唤醒后再次逐个查看这些任务。
此配置值确定睡眠时长。 此参数在工作器上生效，需要在 postgresql 文件中进行更改。 编辑配置文件后，用户可以发送 SIGHUP 信号或重启服务器，使更改生效。

可以减小此参数，以通过减少管理轮次之间的时间差来缩减任务跟踪器执行程序造成的延迟。
如果分片查询较短，因而会定期更新其状态，则降低延迟会很有作用。

##### <a name="citusmax_assign_task_batch_size-integer"></a>citus.max\_assign\_task\_batch\_size（整数）

协调器上的任务跟踪器执行程序以同步方式将任务分批分配到工作器上的守护程序。 此参数设置可在一个批中分配的最大任务数。 选择较大的批大小可以加快任务分配。 但是，如果工作器数量很大，则所有工作器可能需要花费更长的时间来获取任务。  此参数可在运行时设置，并在协调器上生效。

##### <a name="citusmax_running_tasks_per_node-integer"></a>citus.max\_running\_tasks\_per\_node（整数）

任务跟踪器进程将会适当地计划并执行分配给它的任务。 此配置值设置在任意给定时间，在一个节点上可并发执行的最大任务数，默认值为 8。

该限制可确保不会有许多任务同时命中磁盘，并有助于避免磁盘 I/O 争用。 如果从内存或 SSD 为查询提供服务，则你可以放心地增大 max\_running\_tasks\_per\_node。

##### <a name="cituspartition_buffer_size-integer"></a>citus.partition\_buffer\_size（整数）

设置用于分区操作的缓冲区大小，默认值为 8 MB。
超大规模 (Citus) 允许在联接两个大型表时将表数据重新分区到多个文件中。 填满此分区缓冲区后，已重新分区的数据将刷新到磁盘上的文件中。  此配置条目可在运行时设置，并在工作器上生效。

#### <a name="explain-output"></a>Explain 输出

##### <a name="citusexplain_all_tasks-boolean"></a>citus.explain\_all\_tasks（布尔值）

默认情况下，对分布式查询运行 [EXPLAIN](http://www.postgresql.org/docs/current/static/sql-explain.html) 时，超大规模 (Citus) 将显示单个任意任务的输出。 在大多数情况下，explain 输出在各个任务中都是类似的。 偶尔会以不同的方式规划某些任务，或者某些任务的执行时间要长得多。 在这种情况下，启用此参数会很有用，EXPLAIN 输出会在此参数之后包括所有任务。 解释所有任务可能会导致 EXPLAIN 花费更长的时间。

## <a name="postgresql-parameters"></a>PostgreSQL 参数

* [DateStyle](https://www.postgresql.org/docs/current/datatype-datetime.html#DATATYPE-DATETIME-OUTPUT) - 设置日期和时间值的显示格式
* [IntervalStyle](https://www.postgresql.org/docs/current/datatype-datetime.html#DATATYPE-INTERVAL-OUTPUT) - 设置间隔值的显示格式
* [TimeZone](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-TIMEZONE) - 设置用于显示和解释时间戳的时区
* [application_name](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-APPLICATION-NAME) - 设置要在统计信息和日志中报告的应用程序名称
* [array_nulls](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-ARRAY-NULLS) - 在数组中启用 NULL 元素输入
* [autovacuum](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM) - 启动 autovacuum 子进程
* [autovacuum_analyze_scale_factor](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-ANALYZE-SCALE-FACTOR) - 在将元组作为 reltuples 的分数进行分析之前所要达到的元组插入、更新或删除次数
* [autovacuum_analyze_threshold](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-ANALYZE-THRESHOLD) - 在分析元组之前所要达到的元组插入、更新或删除最小次数
* [autovacuum_naptime](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-NAPTIME) - 每两次 autovacuum 运行之间的睡眠时间
* [autovacuum_vacuum_cost_delay](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-COST-DELAY) - autovacuum 的清空成本延迟，以毫秒为单位
* [autovacuum_vacuum_cost_limit](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-COST-LIMIT) - autovacuum 在短暂睡眠之前可用的清空成本量
* [autovacuum_vacuum_scale_factor](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-SCALE-FACTOR) - 在将元组作为 reltuples 的分数清空之前所要达到的元组更新或删除次数
* [autovacuum_vacuum_threshold](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-THRESHOLD) - 在清空之前所要达到的元组更新或删除次数
* [autovacuum_work_mem](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-AUTOVACUUM-WORK-MEM) - 设置每个 autovacuum 工作进程要使用的最大内存
* [backend_flush_after](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BACKEND-FLUSH-AFTER) - 在将以前执行的写入刷新到磁盘之前所要达到的页数
* [backslash_quote](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-BACKSLASH-QUOTE) - 设置字符串字面量中是否允许“\'”
* [bgwriter_delay](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BGWRITER-DELAY) - 每两个轮次之间的后台写入器休眠时间
* [bgwriter_flush_after](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BGWRITER-FLUSH-AFTER) - 在将以前执行的写入刷新到磁盘之前所要达到的页数
* [bgwriter_lru_maxpages](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BGWRITER-LRU-MAXPAGES) - 要在每个轮次刷新的后台写入器最大 LRU 页数
* [bgwriter_lru_multiplier](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BGWRITER-LRU-MULTIPLIER) - 要在每个轮次释放的平均缓冲区使用量的倍数
* [bytea_output](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-BYTEA-OUTPUT) - 设置 bytea 的输出格式
* [check_function_bodies](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-CHECK-FUNCTION-BODIES) - 在运行 CREATE FUNCTION 期间检查函数主体
* [checkpoint_completion_target](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-CHECKPOINT-COMPLETION-TARGET) - 在运行检查点函数期间将脏缓冲区作为检查点间隔的分数进行刷新所花费的时间
* [checkpoint_timeout](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-CHECKPOINT-TIMEOUT) - 设置自动 WAL 检查点之间的最大时间
* [checkpoint_warning](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-CHECKPOINT-WARNING) - 如果填充检查点段的频率高于此值，则启用警告
* [client_encoding](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-CLIENT-ENCODING) - 设置客户端的字符集编码
* [client_min_messages](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-CLIENT-MIN-MESSAGES) - 设置发送到客户端的消息级别
* [commit_delay](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-COMMIT-DELAY) - 设置提交事务与将 WAL 刷新到磁盘之间的延迟，以微秒为单位
* [commit_siblings](https://www.postgresql.org/docs/12/runtime-config-wal.html#GUC-COMMIT-SIBLINGS) - 设置执行 commit_delay 之前所要达到的最小并发打开事务数
* [constraint_exclusion](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CONSTRAINT-EXCLUSION) - 允许规划器使用约束来优化查询
* [cpu_index_tuple_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CPU-INDEX-TUPLE-COST) - 设置规划器对索引扫描期间每个索引条目的处理成本的估算值
* [cpu_operator_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CPU-OPERATOR-COST) - 设置规划器对每个运算符或函数调用的处理成本的估算值
* [cpu_tuple_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CPU-TUPLE-COST) - 设置规划器对每个元组（行）的处理成本的估算值
* [cursor_tuple_fraction](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CURSOR-TUPLE-FRACTION) - 设置规划器对要检索的游标行的分数的估算值
* [deadlock_timeout](https://www.postgresql.org/docs/current/runtime-config-locks.html#GUC-DEADLOCK-TIMEOUT) - 设置在检查死锁之前等待某个锁的时长，以毫秒为单位
* [debug_pretty_print](https://www.postgresql.org/docs/current/runtime-config-logging.html#id-1.6.6.11.5.2.3.1.3) - 缩进分析和计划树的显示内容
* [debug_print_parse](https://www.postgresql.org/docs/current/runtime-config-logging.html#id-1.6.6.11.5.2.2.1.3) - 记录每个查询的分析树
* [debug_print_plan](https://www.postgresql.org/docs/current/runtime-config-logging.html#id-1.6.6.11.5.2.2.1.3) - 记录每个查询的执行计划
* [debug_print_rewritten](https://www.postgresql.org/docs/current/runtime-config-logging.html#id-1.6.6.11.5.2.2.1.3) - 记录每个查询的重写分析树
* [default_statistics_target](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-DEFAULT-STATISTICS-TARGET) - 设置默认的统计目标
* [default_tablespace](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TABLESPACE) - 设置要在其中创建表和索引的默认表空间
* [default_text_search_config](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TEXT-SEARCH-CONFIG) - 设置默认文本搜索配置
* [default_transaction_deferrable](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TRANSACTION-DEFERRABLE) - 设置新事务的默认可推迟状态
* [default_transaction_isolation](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TRANSACTION-ISOLATION) - 设置每个新事务的事务隔离级别
* [default_transaction_deferrable](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TRANSACTION-READ-ONLY) - 设置新事务的默认只读状态
* default_with_oids - 默认创建使用 OID 的新表
* [effective_cache_size](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-EFFECTIVE-CACHE-SIZE) - 设置规划器对磁盘缓存大小做出的假设
* [enable_bitmapscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-BITMAPSCAN) - 允许规划器使用位图扫描计划
* [enable_gathermerge](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-GATHERMERGE) - 允许规划器使用收集合并计划
* [enable_hashagg](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-HASHAGG) - 允许规划器使用哈希聚合计划
* [enable_hashjoin](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-HASHJOIN) - 允许规划器使用哈希联接计划
* [enable_indexonlyscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-INDEXONLYSCAN) - 允许规划器使用仅限索引的扫描计划
* [enable_indexscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-INDEXSCAN) - 允许规划器使用索引扫描计划
* [enable_material](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-MATERIAL) - 允许规划器使用具体化
* [enable_mergejoin](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-MERGEJOIN) - 允许规划器使用合并联接计划
* [enable_nestloop](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-NESTLOOP) - 允许规划器使用嵌套循环联接计划
* [enable_seqscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-SEQSCAN) - 允许规划器使用顺序扫描计划
* [enable_sort](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-SORT) - 允许规划器使用显式排序步骤
* [enable_tidscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-TIDSCAN) - 允许规划器使用 TID 扫描计划
* [escape_string_warning](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-ESCAPE-STRING-WARNING) - 警告普通字符串字面量中存在反斜杠转义符
* [exit_on_error](https://www.postgresql.org/docs/current/runtime-config-error-handling.html#GUC-EXIT-ON-ERROR) - 发生任何错误时终止会话
* [extra_float_digits](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-EXTRA-FLOAT-DIGITS) - 设置要为浮点值显示的位数
* [force_parallel_mode](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-FORCE-PARALLEL-MODE) - 强制使用并行查询工具
* [from_collapse_limit](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-FROM-COLLAPSE-LIMIT) - 设置 FROM 列表大小，超过该大小时将不会折叠子查询
* [geqo](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO) - 启用基因查询优化
* [geqo_effort](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-EFFORT) - GEQO：为其他 GEQO 参数设置默认值所付出的工作量
* [geqo_generations](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-GENERATIONS) - GEQO：算法的迭代次数
* [geqo_pool_size](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-POOL-SIZE) - GEQO：种群中的个体数目
* [geqo_seed](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-SEED) - GEQO：用于选择随机路径的种子
* [geqo_selection_bias](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-SELECTION-BIAS) - GEQO：种群内部的选择性压力
* [geqo_threshold](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-THRESHOLD) - 设置 FROM 项的阈值，超过该阈值时会使用 GEQO
* [gin_fuzzy_search_limit](https://www.postgresql.org/docs/current/runtime-config-client.html#id-1.6.6.14.5.2.2.1.3) - 设置按 GIN 进行的精确匹配搜索允许的最大结果数
* [gin_pending_list_limit](https://www.postgresql.org/docs/current/runtime-config-client.html#id-1.6.6.14.2.2.23.1.3) - 设置 GIN 索引待定列表的最大大小
* [idle_in_transaction_session_timeout](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-IDLE-IN-TRANSACTION-SESSION-TIMEOUT) - 设置任何空闲事务允许的最大持续时间
* [join_collapse_limit](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-JOIN-COLLAPSE-LIMIT) - 设置 FROM 列表的大小，超过该大小时将不平展 JOIN 构造
* [lc_monetary](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-LC-MONETARY) - 设置用于指定货币金额格式的区域设置
* [lc_numeric](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-LC-NUMERIC) - 设置用于指定数字格式的区域设置
* [lo_compat_privileges](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-LO-COMPAT-PRIVILEGES) - 为针对大型对象的特权检查启用向后兼容模式
* [lock_timeout](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-LOCK-TIMEOUT) - 设置任何等待锁操作允许的最长持续时间（以毫秒为单位）。 指定 0 会禁用此设置
* [log_autovacuum_min_duration](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#) - 设置最短执行时间，超过该时间将将记录 autovacuum 操作
* [log_checkpoints](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-CHECKPOINTS) - 记录每个检查点
* [log_connections](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-CONNECTIONS) - 记录每个成功的连接
* [log_destination](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-DESTINATION) - 设置服务器日志输出的目标
* [log_disconnections](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-DISCONNECTIONS) - 记录会话结束时间，包括持续时间
* [log_duration](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-DURATION) - 记录每个已完成 SQL 语句的持续时间
* [log_error_verbosity](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-ERROR-VERBOSITY) - 设置记录的消息的详细程度
* [log_lock_waits](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LOCK-WAITS) - 记录长时间的锁等待
* [log_min_duration_statement](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-MIN-DURATION-STATEMENT) - 设置最短执行时间（以毫秒为单位），超过该时间后将记录语句。 指定 -1 会禁用记录语句持续时间
* [log_min_error_statement](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-MIN-ERROR-STATEMENT) - 导致记录在此级别或更高级别生成错误的所有语句
* [log_min_messages](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-MIN-MESSAGES) - 设置记录的消息级别
* [log_replication_commands](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-REPLICATION-COMMANDS) - 记录每个复制命令
* [log_statement](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-STATEMENT) - 设置记录的语句类型
* [log_statement_stats](https://www.postgresql.org/docs/current/runtime-config-statistics.html#id-1.6.6.12.3.2.1.1.3) - 对于每个查询，将累积性能统计信息写入服务器日志
* [log_temp_files](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-TEMP-FILES) - 对使用超过此数字（以 KB 为单位）的临时文件的情况进行日志记录
* [maintenance_work_mem](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAINTENANCE-WORK-MEM) - 设置要用于维护操作的最大内存
* [max_parallel_workers](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PARALLEL-WORKERS) - 设置每次可处于活动状态的并行工作进程的最大数目
* [max_parallel_workers_per_gather](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PARALLEL-WORKERS-PER-GATHER) - 设置每个执行程序节点的最大并行进程数
* [max_pred_locks_per_page](https://www.postgresql.org/docs/current/runtime-config-locks.html#GUC-MAX-PRED-LOCKS-PER-PAGE) - 设置每页的谓词锁定元组的最大数目
* [max_pred_locks_per_relation](https://www.postgresql.org/docs/current/runtime-config-locks.html#GUC-MAX-PRED-LOCKS-PER-RELATION) - 设置每个关系的谓词锁定页和元组的最大数目
* [max_standby_archive_delay](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-MAX-STANDBY-ARCHIVE-DELAY) - 设置当热备用服务器处理存档的 WAL 数据时，在取消查询之前的最大延迟
* [max_standby_streaming_delay](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-MAX-STANDBY-STREAMING-DELAY) - 设置当热备用服务器处理流式传输的 WAL 数据时，在取消查询之前的最大延迟
* [max_sync_workers_per_subscription](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-MAX-SYNC-WORKERS-PER-SUBSCRIPTION) - 每个订阅的表同步工作进程的最大数目
* [max_wal_size](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-MAX-WAL-SIZE) - 设置触发检查点的 WAL 大小
* [min_parallel_index_scan_size](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-MIN-PARALLEL-INDEX-SCAN-SIZE) - 设置并行扫描的最小索引数据量
* [min_wal_size](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-MIN-WAL-SIZE) - 设置要将 WAL 收缩到的最小大小
* [operator_precedence_warning](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-OPERATOR-PRECEDENCE-WARNING) - 针对自 PostgreSQL 9.4 以来其含义已发生更改的构造发出警告
* [parallel_setup_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-PARALLEL-SETUP-COST) - 设置对并行查询启动工作进程的成本的规划器估算值
* [parallel_tuple_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-PARALLEL-TUPLE-COST) - 设置将每个元组（行）从工作器端传递给主后端的成本的规划器估算值
* [pg_stat_statements.save](https://www.postgresql.org/docs/current/pgstatstatements.html#id-1.11.7.38.8) - 每次服务器关机后保存 pg_stat_statements 统计信息
* [pg_stat_statements.track](https://www.postgresql.org/docs/current/pgstatstatements.html#id-1.11.7.38.8) - 选择 pg_stat_statements 要跟踪的语句
* [pg_stat_statements.track_utility](https://www.postgresql.org/docs/current/pgstatstatements.html#id-1.11.7.38.8) - 选择 pg_stat_statements 是否要跟踪实用工具命令
* [quote_all_identifiers](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-QUOTE-ALL-IDENTIFIERS) - 生成 SQL 片段时将所有标识符括在引号中
* [random_page_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-RANDOM-PAGE-COST) - 设置不按顺序提取的磁盘页的成本的规划器估计值
* [row_security](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-ROW-SECURITY) - 启用行安全性
* [search_path](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-SEARCH-PATH) - 设置非架构限定的名称的架构搜索顺序
* [seq_page_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-SEQ-PAGE-COST) - 设置按顺序提取的磁盘页的成本的规划器估计值
* [session_replication_role](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-SESSION-REPLICATION-ROLE) - 设置会话对触发器和重写规则的行为
* [standard_conforming_strings](https://www.postgresql.org/docs/current/runtime-config-compatible.html#id-1.6.6.16.2.2.7.1.3) - 导致以字面形式处理“...”字符串中的反斜杠
* [statement_timeout](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-STATEMENT-TIMEOUT) - 设置任何语句允许的最长持续时间（以毫秒为单位）。 指定 0 会禁用此设置
* [synchronize_seqscans](https://www.postgresql.org/docs/current/runtime-config-compatible.html#id-1.6.6.16.2.2.8.1.3) - 启用同步顺序扫描
* [synchronous_commit](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-SYNCHRONOUS-COMMIT) - 设置当前事务的同步级别
* [tcp_keepalives_count](https://www.postgresql.org/docs/current/runtime-config-connection.html#GUC-TCP-KEEPALIVES-COUNT) - TCP keepalive 重新传输的最大次数
* [tcp_keepalives_idle](https://www.postgresql.org/docs/current/runtime-config-connection.html#GUC-TCP-KEEPALIVES-IDLE) - 发出 TCP keepalive 的间隔时间
* [tcp_keepalives_interval](https://www.postgresql.org/docs/current/runtime-config-connection.html#GUC-TCP-KEEPALIVES-INTERVAL) - TCP keepalive 重新传输的间隔时间
* [temp_buffers](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-TEMP-BUFFERS) - 设置每个数据库会话使用的临时缓冲区的最大数目
* [temp_tablespaces](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-TEMP-TABLESPACES) - 设置用于临时表和排序文件的表空间
* [track_activities](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-ACTIVITIES) - 收集有关执行命令的信息
* [track_counts](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-COUNTS) - 收集有关数据库活动的统计信息
* [track_functions](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-FUNCTIONS) - 收集有关数据库活动的函数级统计信息
* [track_io_timing](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-IO-TIMING) - 收集数据库 I/O 活动的计时统计信息
* [transform_null_equals](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-TRANSFORM-NULL-EQUALS) - 将“expr=NULL”视为“expr IS NULL”
* [vacuum_cost_delay](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-DELAY) - 清空成本延迟，以毫秒为单位
* [vacuum_cost_limit](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-LIMIT) - 在短暂睡眠之前可用的清空成本量
* [vacuum_cost_page_dirty](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-PAGE-DIRTY) - 被清空进程弄脏的页的清空成本
* [vacuum_cost_page_hit](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-PAGE-HIT) - 在缓冲区缓存中找到的页的清空成本
* [vacuum_cost_page_miss](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-PAGE-MISS) - 在缓冲区缓存中找不到的页的清空成本
* [vacuum_defer_cleanup_age](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-VACUUM-DEFER-CLEANUP-AGE) - VACUUM 和 HOT 清理应推迟的事务数（如果有）
* [vacuum_freeze_min_age](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-VACUUM-FREEZE-MIN-AGE) - 在 VACUUM 冻结表行之前所要达到的最短期限
* [vacuum_freeze_table_age](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-VACUUM-FREEZE-TABLE-AGE) - 在 VACUUM 扫描整个表以冻结元组之前所要达到的期限
* [vacuum_multixact_freeze_min_age](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-VACUUM-MULTIXACT-FREEZE-MIN-AGE) - 在 VACUUM 冻结表行中的 MultiXactId 之前所要达到的最短期限
* [vacuum_multixact_freeze_table_age](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-VACUUM-MULTIXACT-FREEZE-TABLE-AGE) - 在 VACUUM 扫描整个表以冻结元组之前所要达到的 Multixact 期限
* [wal_receiver_status_interval](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-WAL-RECEIVER-STATUS-INTERVAL) - 设置向主要节点报告 WAL 接收器状态的最大时间间隔
* [wal_writer_delay](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-WAL-WRITER-DELAY) - 在 WAL 写入器中执行 WAL 刷新的间隔时间
* [wal_writer_flush_after](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-WAL-WRITER-FLUSH-AFTER) - WAL 写入器写出的 WAL 数量，达到该数量将触发刷新
* [work_mem](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-WORK-MEM) - 设置写入到临时磁盘文件之前内部排序操作和哈希表要使用的内存量
* [xmlbinary](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-XMLBINARY) - 设置如何在 XML 中对二进制值进行编码
* [xmloption](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-XMLOPTION) - 设置是要将隐式分析和序列化操作中的 XML 数据视为文档还是内容片段

## <a name="next-steps"></a>后续步骤

* 除服务器参数外，另一种形式的配置也是超大规模 (Citus) 服务器组中的资源[配置选项](concepts-hyperscale-configuration-options.md)。
* 底层 PostgreSQL 数据库也有[配置参数](http://www.postgresql.org/docs/current/static/runtime-config.html)。
