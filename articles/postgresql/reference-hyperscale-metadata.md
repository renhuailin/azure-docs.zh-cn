---
title: 系统表 – 超大规模 (Citus) - Azure Database for PostgreSQL
description: 用于分布式查询执行的元数据
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 08/10/2020
ms.openlocfilehash: 8213ca39f85a50d7e5354948467f3fbd66cb4797
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2021
ms.locfileid: "129094721"
---
# <a name="system-tables-and-views"></a>系统表和视图

超大规模 (Citus) 会创建并维护特殊表，其中包含有关服务器组中的分布式数据的信息。 规划如何在工作器节点间运行查询时，协调器节点会咨询这些表。

## <a name="coordinator-metadata"></a>协调器元数据

超大规模 (Citus) 会基于分布列将每个分布式表划分为多个逻辑分片。 协调器随后会维护元数据表，以跟踪有关这些分片的运行状况和位置的统计信息和信息。

在本部分中，我们会介绍每个元数据表及其架构。
登录协调器节点后，可以使用 SQL 查看和查询这些表。

> [!NOTE]
>
> 运行旧版 Citus 引擎的超大规模 (Citus) 服务器组可能不提供以下列出的所有表。

### <a name="partition-table"></a>分区表

pg\_dist\_partition 表存储有关数据库中的分布式表的元数据。 对于每个分布式表，它还存储有关分布方法的信息以及有关分布列的详细信息。

| 名称         | 类型     | 说明                                                                                                                                                                                                                                           |
|--------------|----------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| logicalrelid | regclass | 此行对应的分布式表。 此值引用 pg_class 系统目录表中的 relfilenode 列。                                                                                                                   |
| partmethod   | char     | 用于分区/分布的方法。 与不同分布方法对应的此列值是追加：“a”，哈希：“h”，引用表：“n”                                                                          |
| partkey      | text     | 有关分布列的详细信息，包括列号、类型和其他相关信息。                                                                                                                                      |
| colocationid | integer  | 此表所属的归置组。 同一组中的表允许在其他优化之间实现归置联接和分布式汇总。 此值引用 pg_dist_colocation 表中的 colocationid 列。                      |
| repmodel     | char     | 用于数据复制的方法。 此列与不同复制方法相对应的值包括：Citus 基于语句的复制：“c”，postgresql 流式处理复制：“s”，两阶段提交（适用于引用表）：“t” |

```
SELECT * from pg_dist_partition;
 logicalrelid  | partmethod |                                                        partkey                                                         | colocationid | repmodel 
---------------+------------+------------------------------------------------------------------------------------------------------------------------+--------------+----------
 github_events | h          | {VAR :varno 1 :varattno 4 :vartype 20 :vartypmod -1 :varcollid 0 :varlevelsup 0 :varnoold 1 :varoattno 4 :location -1} |            2 | c
 (1 row)
```

### <a name="shard-table"></a>分片表

pg\_dist\_shard 表存储有关表的各个分片的元数据。 Pg_dist_shard 包含有关分片所属的分布式表的信息，以及有关分片的分布列的统计信息。
对于追加分布式表，这些统计信息对应于分布列的最小值/最大值。 对于哈希分布式表，它们是分配给该分片的哈希标记范围。 这些统计信息用于在 SELECT 查询期间删除不相关的分片。

| 名称          | 类型     | 说明                                                                                                                                                                                  |
|---------------|----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| logicalrelid  | regclass | 此行对应的分布式表。 此值引用 pg_class 系统目录表中的 relfilenode 列。                                                          |
| shardid       | bigint   | 分配给此分片的全局唯一标识符。                                                                                                                                           |
| shardstorage  | char     | 用于此分片的存储类型。 下表讨论了不同的存储类型。                                                                                               |
| shardminvalue | text     | 对于追加分布式表，是此分片中分布列的最小值（含）。 对于哈希分布式表，是分配给该分片的最小哈希标记值（含）。 |
| shardmaxvalue | text     | 对于追加分布式表，是此分片中分布列的最大值（含）。 对于哈希分布式表，是分配给该分片的最大哈希标记值（含）。 |

```
SELECT * from pg_dist_shard;
 logicalrelid  | shardid | shardstorage | shardminvalue | shardmaxvalue 
---------------+---------+--------------+---------------+---------------
 github_events |  102026 | t            | 268435456     | 402653183
 github_events |  102027 | t            | 402653184     | 536870911
 github_events |  102028 | t            | 536870912     | 671088639
 github_events |  102029 | t            | 671088640     | 805306367
 (4 rows)
```

#### <a name="shard-storage-types"></a>分片存储类型

pg\_dist\_shard 中的 shardstorage 列指示用于分片的存储类型。 下面是不同分片存储类型及其表示形式的简要概述。

| 存储类型 | Shardstorage 值 | 说明                                                                        |
|--------------|--------------------|------------------------------------------------------------------------------------|
| TABLE        | “t”                | 指示分片存储属于常规分布式表的数据。         |
| COLUMNAR     | “c”                | 指示分片存储纵栏式数据。 （由分布式 cstore_fdw 表使用） |
| FOREIGN      | “f”                | 指示分片存储外部数据。 （由分布式 file_fdw 表使用）    |

### <a name="shard-placement-table"></a>分片放置表

pg\_dist\_placement 表跟踪分片副本在工作器节点上的位置。 分配给特定节点的分片的每个副本称为分片放置。 此表存储有关每个分片放置的运行状况和位置的信息。

| 名称        | 类型   | 说明                                                                                                                               |
|-------------|--------|-------------------------------------------------------------------------------------------------------------------------------------------|
| shardid     | bigint | 与此放置关联的分片标识符。 此值引用 pg_dist_shard 目录表中的 shardid 列。             |
| shardstate  | int    | 描述此放置的状态。 以下部分讨论了不同的分片状态。                                         |
| shardlength | bigint | 对于追加分布式表，是工作器节点上的分片放置大小（以字节为单位）。 对于哈希分布式表，为零。            |
| placementid | bigint | 为每个单独放置自动生成的唯一标识符。                                                                           |
| groupid     | int    | 使用流式处理复制模型时，表示由一台主服务器和零台或多台辅助服务器组成的组。 |

```
SELECT * from pg_dist_placement;
  shardid | shardstate | shardlength | placementid | groupid
 ---------+------------+-------------+-------------+---------
   102008 |          1 |           0 |           1 |       1
   102008 |          1 |           0 |           2 |       2
   102009 |          1 |           0 |           3 |       2
   102009 |          1 |           0 |           4 |       3
   102010 |          1 |           0 |           5 |       3
   102010 |          1 |           0 |           6 |       4
   102011 |          1 |           0 |           7 |       4
```

#### <a name="shard-placement-states"></a>分片放置状态

超大规模 (Citus) 按每个放置管理分片运行状况。 如果放置使系统处于不一致状态，则 Citus 会自动将它标记为不可用。 放置状态记录在 pg_dist_shard_placement 表的 shardstate 列中。 下面是不同分片放置状态的简要概述：

| 状态名称 | Shardstate 值 | 说明                                                                                                                                                                                                                                                                                                                                                                                                                         |
|------------|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| FINALIZED  | 1                | 新分片创建时所处的状态。 此状态下的分片放置被视为是最新的，在查询规划和执行中使用。                                                                                                                                                                                                                                                                                 |
| INACTIVE   | 3                | 此状态下的分片放置被视为不活动，因为与同一分片的其他副本不同步。 此放置的追加、修改（INSERT、UPDATE、DELETE）或 DDL 操作失败时，可能会出现该状态。 查询规划器会在规划和执行期间忽略此状态的放置。 用户可以采用后台活动的形式，将这些分片中的数据与已完成的副本同步。 |
| TO_DELETE  | 4                | 如果 Citus 尝试删除分片放置以响应 master_apply_delete_command 调用，但是失败，则放置会转变为此状态。 用户随后可以采用后续后台活动的形式删除这些分片。                                                                                                                                                                                                              |

### <a name="worker-node-table"></a>工作器节点表

pg\_dist\_node 表包含有关群集中的工作器节点的信息。

| 名称             | 类型    | 说明                                                                                                                                                                                |
|------------------|---------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| nodeid           | int     | 为单个节点自动生成的标识符。                                                                                                                                          |
| groupid          | int     | 使用流式处理复制模型时，用于表示由一台主服务器和零台或多台辅助服务器组成的组的标识符。 默认情况下，它与 nodeid 相同。         |
| nodename         | text    | PostgreSQL 工作器节点的主机名或 IP 地址。                                                                                                                                     |
| nodeport         | int     | PostgreSQL 工作器节点正在侦听的端口号。                                                                                                                              |
| noderack         | text    | （可选）工作器节点的机架放置信息。                                                                                                                                 |
| hasmetadata      | boolean | 保留以供内部使用。                                                                                                                                                                 |
| isactive         | boolean | 节点是否处于活动状态，在接受分片放置。                                                                                                                                     |
| noderole         | text    | 节点是主节点还是辅助节点                                                                                                                                                 |
| nodecluster      | text    | 包含此节点的群集的名称                                                                                                                                               |
| shouldhaveshards | boolean | 如果为 false，则分片在重新平衡时会移出节点（排出），新分布式表中的分片也不会置于节点上，除非它们与已在其中的分片归置 |

```
SELECT * from pg_dist_node;
 nodeid | groupid | nodename  | nodeport | noderack | hasmetadata | isactive | noderole | nodecluster | shouldhaveshards
--------+---------+-----------+----------+----------+-------------+----------+----------+-------------+------------------
      1 |       1 | localhost |    12345 | default  | f           | t        | primary  | default     | t
      2 |       2 | localhost |    12346 | default  | f           | t        | primary  | default     | t
      3 |       3 | localhost |    12347 | default  | f           | t        | primary  | default     | t
(3 rows)
```

### <a name="distributed-object-table"></a>分布式对象表

citus.pg\_dist\_object 表包含在协调器节点上创建并传播到工作器节点的对象（如类型和函数）的列表。 当管理员向群集中添加新的工作器节点时，超大规模 (Citus) 会自动在新节点上创建分布式对象的副本（按照正确顺序，以满足对象依赖关系）。

| 名称                        | 类型    | 说明                                          |
|-----------------------------|---------|------------------------------------------------------|
| classid                     | oid     | 分布式对象的类                      |
| objid                       | oid     | 分布式对象的对象 ID                  |
| objsubid                    | integer | 分布式对象的对象子 ID，例如 attnum |
| 类型                        | text    | 在 pg 升级过程中使用的稳定地址的一部分   |
| object_names                | text[]  | 在 pg 升级过程中使用的稳定地址的一部分   |
| object_args                 | text[]  | 在 pg 升级过程中使用的稳定地址的一部分   |
| distribution_argument_index | integer | 仅对分布式函数/过程有效      |
| colocationid                | integer | 仅对分布式函数/过程有效      |

“稳定地址”唯一标识独立于特定服务器的对象。 超大规模 (Citus) 使用通过 [pg\_identify\_object\_as\_address()](https://www.postgresql.org/docs/current/functions-info.html#FUNCTIONS-INFO-OBJECT-TABLE) 函数创建的稳定地址在 PostgreSQL 升级过程中跟踪对象。

下面是 `create_distributed_function()` 如何向 `citus.pg_dist_object` 表中添加条目的示例：

```psql
CREATE TYPE stoplight AS enum ('green', 'yellow', 'red');

CREATE OR REPLACE FUNCTION intersection()
RETURNS stoplight AS $$
DECLARE
        color stoplight;
BEGIN
        SELECT *
          FROM unnest(enum_range(NULL::stoplight)) INTO color
         ORDER BY random() LIMIT 1;
        RETURN color;
END;
$$ LANGUAGE plpgsql VOLATILE;

SELECT create_distributed_function('intersection()');

-- will have two rows, one for the TYPE and one for the FUNCTION
TABLE citus.pg_dist_object;
```

```
-[ RECORD 1 ]---------------+------
classid                     | 1247
objid                       | 16780
objsubid                    | 0
type                        |
object_names                |
object_args                 |
distribution_argument_index |
colocationid                |
-[ RECORD 2 ]---------------+------
classid                     | 1255
objid                       | 16788
objsubid                    | 0
type                        |
object_names                |
object_args                 |
distribution_argument_index |
colocationid                |
```

### <a name="colocation-group-table"></a>归置组表

pg\_dist\_colocation 表包含有关表中应放置在一起（即进行[归置](concepts-hyperscale-colocation.md)）的分片的信息。
当两个表处于同一个归置组中时，超大规模 (Citus) 会确保将具有相同分区值的分片放置在同一个工作器节点上。
归置可实现联接优化、特定分布式汇总和外键支持。 当两个表之间的分片计数、复制因子和分区列类型全部匹配时，会推断进行分片归置；但是，如果需要，可以在创建分布式表时指定自定义归置组。

| 名称                   | 类型 | 说明                                                                   |
|------------------------|------|-------------------------------------------------------------------------------|
| colocationid           | int  | 此行对应的归置组的唯一标识符。          |
| shardcount             | int  | 此归置组中所有表的分片计数                          |
| replicationfactor      | int  | 此归置组中所有表的复制因子。                  |
| distributioncolumntype | oid  | 此归置组中所有表的分布列的类型。 |

```
SELECT * from pg_dist_colocation;
  colocationid | shardcount | replicationfactor | distributioncolumntype 
 --------------+------------+-------------------+------------------------
             2 |         32 |                 2 |                     20
  (1 row)
```

### <a name="rebalancer-strategy-table"></a>再平衡器策略表

此表定义 [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards) 可用于确定将分片移动到的位置的策略。

| 名称                           | 类型    | 说明                                                                                                                                       |
|--------------------------------|---------|---------------------------------------------------------------------------------------------------------------------------------------------------|
| default_strategy               | boolean | rebalance_table_shards 是否应在默认情况下选择此策略。 使用 citus_set_default_rebalance_strategy 更新此列             |
| shard_cost_function            | regproc | 开销函数的标识符，该函数必须以 bigint 的形式采用 shardid，并以类型 real 返回开销概念                                |
| node_capacity_function         | regproc | 容量函数的标识符，该函数必须以 int 的形式采用 nodeid，并以类型 real 返回节点容量概念                          |
| shard_allowed_on_node_function | regproc | 一个函数的标识符，该函数在给定 shardid bigint 和 nodeidarg int 的情况下，返回表示 Citus 是否可以在节点上存储分片的布尔值 |
| default_threshold              | float4  | 认为节点太满或太空的阈值，可确定 rebalance_table_shards 何时应尝试移动分片                    |
| minimum_threshold              | float4  | 防止将 rebalance_table_shards() 的阈值参数设置得太低的安全措施                                                  |

超大规模 (Citus) 安装在表中附带以下策略：

```postgresql
SELECT * FROM pg_dist_rebalance_strategy;
```

```
-[ RECORD 1 ]-------------------+-----------------------------------
Name                            | by_shard_count
default_strategy                | true
shard_cost_function             | citus_shard_cost_1
node_capacity_function          | citus_node_capacity_1
shard_allowed_on_node_function  | citus_shard_allowed_on_node_true
default_threshold               | 0
minimum_threshold               | 0
-[ RECORD 2 ]-------------------+-----------------------------------
Name                            | by_disk_size
default_strategy                | false
shard_cost_function             | citus_shard_cost_by_disk_size
node_capacity_function          | citus_node_capacity_1
shard_allowed_on_node_function  | citus_shard_allowed_on_node_true
default_threshold               | 0.1
minimum_threshold               | 0.01
```

默认策略 `by_shard_count` 会为每个分片分配相同开销。 其效果是在节点间均衡分片计数。 另一种预定义策略 `by_disk_size` 会为每个分片分配与其磁盘大小（以字节为单位）以及与之归置的分片磁盘大小匹配的开销。 磁盘大小使用 `pg_total_relation_size` 进行计算，因此包括索引。 此策略尝试在每个节点上实现相同的磁盘空间。 请注意阈值 0.1 -- 它可防止磁盘空间中的微小差异导致不必要的分片移动。

#### <a name="creating-custom-rebalancer-strategies"></a>创建自定义再平衡器策略

下面是可以在新分片再平衡器策略中使用，并通过 [citus_add_rebalance_strategy](reference-hyperscale-functions.md#citus_add_rebalance_strategy) 函数在 [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md?#rebalancer-strategy-table) 中注册的函数示例。

-   按主机名模式设置节点容量异常：

    ```postgresql
    CREATE FUNCTION v2_node_double_capacity(nodeidarg int)
        RETURNS boolean AS $$
        SELECT
            (CASE WHEN nodename LIKE '%.v2.worker.citusdata.com' THEN 2 ELSE 1 END)
        FROM pg_dist_node where nodeid = nodeidarg
        $$ LANGUAGE sql;
    ```

-   按照 [citus_stat_statements](reference-hyperscale-metadata.md#query-statistics-table) 的度量，按发送到分片的查询数重新平衡：

    ```postgresql
    -- example of shard_cost_function

    CREATE FUNCTION cost_of_shard_by_number_of_queries(shardid bigint)
        RETURNS real AS $$
        SELECT coalesce(sum(calls)::real, 0.001) as shard_total_queries
        FROM citus_stat_statements
        WHERE partition_key is not null
            AND get_shard_id_for_distribution_column('tab', partition_key) = shardid;
    $$ LANGUAGE sql;
    ```

-   在节点上（地址为“10.0.0.1”）隔离特定分片 (10000)：

    ```postgresql
    -- example of shard_allowed_on_node_function

    CREATE FUNCTION isolate_shard_10000_on_10_0_0_1(shardid bigint, nodeidarg int)
        RETURNS boolean AS $$
        SELECT
            (CASE WHEN nodename = '10.0.0.1' THEN shardid = 10000 ELSE shardid != 10000 END)
        FROM pg_dist_node where nodeid = nodeidarg
        $$ LANGUAGE sql;

    -- The next two definitions are recommended in combination with the above function.
    -- This way the average utilization of nodes is not impacted by the isolated shard.
    CREATE FUNCTION no_capacity_for_10_0_0_1(nodeidarg int)
        RETURNS real AS $$
        SELECT
            (CASE WHEN nodename = '10.0.0.1' THEN 0 ELSE 1 END)::real
        FROM pg_dist_node where nodeid = nodeidarg
        $$ LANGUAGE sql;
    CREATE FUNCTION no_cost_for_10000(shardid bigint)
        RETURNS real AS $$
        SELECT
            (CASE WHEN shardid = 10000 THEN 0 ELSE 1 END)::real
        $$ LANGUAGE sql;
    ```

### <a name="query-statistics-table"></a>查询统计信息表

超大规模 (Citus) 提供 `citus_stat_statements` 以获取有关查询执行情况和为谁执行查询的统计信息。 它与 PostgreSQL 中的 [pg\_stat\_statements](https://www.postgresql.org/docs/current/static/pgstatstatements.html) 视图类似（并且可以与之联接），后者跟踪有关查询速度的统计信息。

此视图可以在多租户应用程序中跟踪对发起租户的查询，这有助于确定何时进行租户隔离。

| 名称          | 类型   | 说明                                                                      |
|---------------|--------|----------------------------------------------------------------------------------|
| queryid       | bigint | 标识符（适用于 pg_stat_statements 联接）                                   |
| userid        | oid    | 运行查询的用户                                                           |
| dbid          | oid    | 协调器的数据库实例                                                 |
| query         | text   | 匿名查询字符串                                                          |
| executor      | text   | 使用的 Citus 执行程序：自适应、实时、任务跟踪器、路由器或插入-选择 |
| partition_key | text   | 路由器执行的查询中分布列的值，否则为 NULL               |
| calls         | bigint | 运行查询的次数                                                |

```sql
-- create and populate distributed table
create table foo ( id int );
select create_distributed_table('foo', 'id');
insert into foo select generate_series(1,100);

-- enable stats
-- pg_stat_statements must be in shared_preload libraries
create extension pg_stat_statements;

select count(*) from foo;
select * from foo where id = 42;

select * from citus_stat_statements;
```

结果：

```
-[ RECORD 1 ]-+----------------------------------------------
queryid       | -909556869173432820
userid        | 10
dbid          | 13340
query         | insert into foo select generate_series($1,$2)
executor      | insert-select
partition_key |
calls         | 1
-[ RECORD 2 ]-+----------------------------------------------
queryid       | 3919808845681956665
userid        | 10
dbid          | 13340
query         | select count(*) from foo;
executor      | adaptive
partition_key |
calls         | 1
-[ RECORD 3 ]-+----------------------------------------------
queryid       | 5351346905785208738
userid        | 10
dbid          | 13340
query         | select * from foo where id = $1
executor      | adaptive
partition_key | 42
calls         | 1
```

注意：

-   统计数据不进行复制，在数据库崩溃或故障转移后会丢失
-   跟踪有限数量的查询，由 `pg_stat_statements.max` GUC 设置（默认值为 5000）
-   若要截断表，请使用 `citus_stat_statements_reset()` 函数

### <a name="distributed-query-activity"></a>分布式查询活动

超大规模 (Citus) 提供了用于在整个群集中监视查询和锁定的特殊视图，包括用于在内部为分布式查询生成结果的特定于分片的查询。

-   citus\_dist\_stat\_activity：显示在所有节点上执行的分布式查询。 `pg_stat_activity` 的超集，无论后者在何处都可使用。
-   citus\_worker\_stat\_activity：显示针对工作器的查询，包括针对单个分片的片段查询。
-   citus\_lock\_waits：整个群集中阻止的查询。

前两个视图包括 [pg\_stat\_activity](https://www.postgresql.org/docs/current/static/monitoring-stats.html#PG-STAT-ACTIVITY-VIEW) 的所有列，以及发起查询的工作器的主机/端口和群集协器节点的主机/端口。

例如，请考虑计算分布式表中的行数：

```postgresql
-- run from worker on localhost:9701

SELECT count(*) FROM users_table;
```

我们可以看到查询出现在 `citus_dist_stat_activity` 中：

```postgresql
SELECT * FROM citus_dist_stat_activity;

-[ RECORD 1 ]----------+----------------------------------
query_hostname         | localhost
query_hostport         | 9701
master_query_host_name | localhost
master_query_host_port | 9701
transaction_number     | 1
transaction_stamp      | 2018-10-05 13:27:20.691907+03
datid                  | 12630
datname                | postgres
pid                    | 23723
usesysid               | 10
usename                | citus
application\_name      | psql
client\_addr           | 
client\_hostname       | 
client\_port           | -1
backend\_start         | 2018-10-05 13:27:14.419905+03
xact\_start            | 2018-10-05 13:27:16.362887+03
query\_start           | 2018-10-05 13:27:20.682452+03
state\_change          | 2018-10-05 13:27:20.896546+03
wait\_event_type       | Client
wait\_event            | ClientRead
state                  | idle in transaction
backend\_xid           | 
backend\_xmin          | 
query                  | SELECT count(*) FROM users_table;
backend\_type          | client backend
```

此查询需要所有分片中的信息。 某些信息处于分片 `users_table_102038` 中，该分片恰好存储在 `localhost:9700` 中。 可以通过查看 `citus_worker_stat_activity` 视图来查看访问分片的查询：

```postgresql
SELECT * FROM citus_worker_stat_activity;

-[ RECORD 1 ]----------+-----------------------------------------------------------------------------------------
query_hostname         | localhost
query_hostport         | 9700
master_query_host_name | localhost
master_query_host_port | 9701
transaction_number     | 1
transaction_stamp      | 2018-10-05 13:27:20.691907+03
datid                  | 12630
datname                | postgres
pid                    | 23781
usesysid               | 10
usename                | citus
application\_name      | citus
client\_addr           | ::1
client\_hostname       | 
client\_port           | 51773
backend\_start         | 2018-10-05 13:27:20.75839+03
xact\_start            | 2018-10-05 13:27:20.84112+03
query\_start           | 2018-10-05 13:27:20.867446+03
state\_change          | 2018-10-05 13:27:20.869889+03
wait\_event_type       | Client
wait\_event            | ClientRead
state                  | idle in transaction
backend\_xid           | 
backend\_xmin          | 
query                  | COPY (SELECT count(*) AS count FROM users_table_102038 users_table WHERE true) TO STDOUT
backend\_type          | client backend
```

`query` 字段显示从要计数的分片复制出的数据。

> [!NOTE]
> 如果在没有事务块的情况下执行路由器查询（例如多租户应用程序中的单租户，“SELECT
> * FROM table WHERE tenant_id = X”），则 master\_query\_host\_name 和 master\_query\_host\_port 列在 citus\_worker\_stat\_activity 中会为 NULL。

下面是可以使用 `citus_worker_stat_activity` 生成的有用查询示例：

```postgresql
-- active queries' wait events on a certain node

SELECT query, wait_event_type, wait_event
  FROM citus_worker_stat_activity
 WHERE query_hostname = 'xxxx' and state='active';

-- active queries' top wait events

SELECT wait_event, wait_event_type, count(*)
  FROM citus_worker_stat_activity
 WHERE state='active'
 GROUP BY wait_event, wait_event_type
 ORDER BY count(*) desc;

-- total internal connections generated per node by Citus

SELECT query_hostname, count(*)
  FROM citus_worker_stat_activity
 GROUP BY query_hostname;

-- total internal active connections generated per node by Citus

SELECT query_hostname, count(*)
  FROM citus_worker_stat_activity
 WHERE state='active'
 GROUP BY query_hostname;
```

下个视图为 `citus_lock_waits`。 若要查看它的工作情况，可以手动生成锁定情况。 首先，我们会从协调器设置一个测试表：

```postgresql
CREATE TABLE numbers AS
  SELECT i, 0 AS j FROM generate_series(1,10) AS i;
SELECT create_distributed_table('numbers', 'i');
```

随后通过在协调器上使用两个会话，可以运行以下语句序列：

```postgresql
-- session 1                           -- session 2
-------------------------------------  -------------------------------------
BEGIN;
UPDATE numbers SET j = 2 WHERE i = 1;
                                       BEGIN;
                                       UPDATE numbers SET j = 3 WHERE i = 1;
                                       -- (this blocks)
```

`citus_lock_waits` 视图显示了相关情况。

```postgresql
SELECT * FROM citus_lock_waits;

-[ RECORD 1 ]-------------------------+----------------------------------------
waiting_pid                           | 88624
blocking_pid                          | 88615
blocked_statement                     | UPDATE numbers SET j = 3 WHERE i = 1;
current_statement_in_blocking_process | UPDATE numbers SET j = 2 WHERE i = 1;
waiting_node_id                       | 0
blocking_node_id                      | 0
waiting_node_name                     | coordinator_host
blocking_node_name                    | coordinator_host
waiting_node_port                     | 5432
blocking_node_port                    | 5432
```

在此示例中，查询从协调器上发起，但是该视图也可以列出在工作器上发起的查询（例如，使用超大规模 (Citus) MX 执行）之间的锁定。

## <a name="next-steps"></a>后续步骤

* 了解某些[超大规模 (Citus) 函数](reference-hyperscale-functions.md)如何改变系统表
* 查看[节点和表](concepts-hyperscale-nodes.md)的概念
