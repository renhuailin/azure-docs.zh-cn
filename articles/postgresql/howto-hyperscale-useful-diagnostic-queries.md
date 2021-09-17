---
title: 有用的诊断查询 - 超大规模 (Citus) - Azure Database for PostgreSQL
description: 用于了解分布式数据和其他信息的查询
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 8/23/2021
ms.openlocfilehash: efd251e48beb4e2f2b3db16f694da14888e876dd
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122822533"
---
# <a name="useful-diagnostic-queries"></a>有用的诊断查询

## <a name="finding-which-node-contains-data-for-a-specific-tenant"></a>确定哪个节点包含特定租户的数据

在多租户用例中，我们可以确定哪个工作器节点包含特定租户的行。  超大规模 (Citus) 将分布式表的行分组成分片，并将每个分片放在服务器组中的某个工作器节点上。 

假设应用程序的租户是商店，而我们想要确定哪个工作器节点包含指定了商店 ID=4 的数据。  换言之，我们想要查找包含分布列中具有值 4 的行的分片：

``` postgresql
SELECT shardid, shardstate, shardlength, nodename, nodeport, placementid
  FROM pg_dist_placement AS placement,
       pg_dist_node AS node
 WHERE placement.groupid = node.groupid
   AND node.noderole = 'primary'
   AND shardid = (
     SELECT get_shard_id_for_distribution_column('stores', 4)
   );
```

输出将包含工作器数据库的主机和端口。

```
┌─────────┬────────────┬─────────────┬───────────┬──────────┬─────────────┐
│ shardid │ shardstate │ shardlength │ nodename  │ nodeport │ placementid │
├─────────┼────────────┼─────────────┼───────────┼──────────┼─────────────┤
│  102009 │          1 │           0 │ 10.0.0.16 │     5432 │           2 │
└─────────┴────────────┴─────────────┴───────────┴──────────┴─────────────┘
```

## <a name="finding-the-distribution-column-for-a-table"></a>查找表的分布列

超大规模 (Citus) 中的每个分布式表都有一个“分布列”。 （有关详细信息，请参阅[分布式数据建模](concepts-hyperscale-choose-distribution-column.md)。）知道它是哪个列很重要。 例如，在联接或筛选表时，可能会现错误消息，其中包含类似于“将筛选器添加到分布列”的提示。

协调器节点上的 `pg_dist_*` 表包含有关分布式数据库的各种元数据。 具体而言，`pg_dist_partition` 包含有关每个表的分布列的信息。 可以使用便捷的实用工具函数在元数据的低级别详细信息中查找分布列名。 下面是一个示例及其输出：

``` postgresql
-- create example table

CREATE TABLE products (
  store_id bigint,
  product_id bigint,
  name text,
  price money,

  CONSTRAINT products_pkey PRIMARY KEY (store_id, product_id)
);

-- pick store_id as distribution column

SELECT create_distributed_table('products', 'store_id');

-- get distribution column name for products table

SELECT column_to_column_name(logicalrelid, partkey) AS dist_col_name
  FROM pg_dist_partition
 WHERE logicalrelid='products'::regclass;
```

示例输出：

```
┌───────────────┐
│ dist_col_name │
├───────────────┤
│ store_id      │
└───────────────┘
```

## <a name="detecting-locks"></a>检测锁

此查询将对所有工作器节点运行，并识别锁、锁保持打开状态的时长，以及有问题的查询：

``` postgresql
SELECT run_command_on_workers($cmd$
  SELECT array_agg(
    blocked_statement || ' $ ' || cur_stmt_blocking_proc
    || ' $ ' || cnt::text || ' $ ' || age
  )
  FROM (
    SELECT blocked_activity.query    AS blocked_statement,
           blocking_activity.query   AS cur_stmt_blocking_proc,
           count(*)                  AS cnt,
           age(now(), min(blocked_activity.query_start)) AS "age"
    FROM pg_catalog.pg_locks         blocked_locks
    JOIN pg_catalog.pg_stat_activity blocked_activity
      ON blocked_activity.pid = blocked_locks.pid
    JOIN pg_catalog.pg_locks         blocking_locks
      ON blocking_locks.locktype = blocked_locks.locktype
     AND blocking_locks.DATABASE IS NOT DISTINCT FROM blocked_locks.DATABASE
     AND blocking_locks.relation IS NOT DISTINCT FROM blocked_locks.relation
     AND blocking_locks.page IS NOT DISTINCT FROM blocked_locks.page
     AND blocking_locks.tuple IS NOT DISTINCT FROM blocked_locks.tuple
     AND blocking_locks.virtualxid IS NOT DISTINCT FROM blocked_locks.virtualxid
     AND blocking_locks.transactionid IS NOT DISTINCT FROM blocked_locks.transactionid
     AND blocking_locks.classid IS NOT DISTINCT FROM blocked_locks.classid
     AND blocking_locks.objid IS NOT DISTINCT FROM blocked_locks.objid
     AND blocking_locks.objsubid IS NOT DISTINCT FROM blocked_locks.objsubid
     AND blocking_locks.pid != blocked_locks.pid
    JOIN pg_catalog.pg_stat_activity blocking_activity ON blocking_activity.pid = blocking_locks.pid
    WHERE NOT blocked_locks.GRANTED
     AND blocking_locks.GRANTED
    GROUP BY blocked_activity.query,
             blocking_activity.query
    ORDER BY 4
  ) a
$cmd$);
```

示例输出：

```
┌───────────────────────────────────────────────────────────────────────────────────┐
│                               run_command_on_workers                              │
├───────────────────────────────────────────────────────────────────────────────────┤
│ (10.0.0.16,5432,t,"")                                                             │
│ (10.0.0.20,5432,t,"{""update ads_102277 set name = 'new name' where id = 1; $ sel…│
│…ect * from ads_102277 where id = 1 for update; $ 1 $ 00:00:03.729519""}")         │
└───────────────────────────────────────────────────────────────────────────────────┘
```

## <a name="querying-the-size-of-your-shards"></a>查询分片的大小

此查询将提供名为 `my_distributed_table` 的给定分布式表的每个分片大小：

``` postgresql
SELECT *
FROM run_command_on_shards('my_distributed_table', $cmd$
  SELECT json_build_object(
    'shard_name', '%1$s',
    'size',       pg_size_pretty(pg_table_size('%1$s'))
  );
$cmd$);
```

示例输出：

```
┌─────────┬─────────┬───────────────────────────────────────────────────────────────────────┐
│ shardid │ success │                                result                                 │
├─────────┼─────────┼───────────────────────────────────────────────────────────────────────┤
│  102008 │ t       │ {"shard_name" : "my_distributed_table_102008", "size" : "2416 kB"}    │
│  102009 │ t       │ {"shard_name" : "my_distributed_table_102009", "size" : "3960 kB"}    │
│  102010 │ t       │ {"shard_name" : "my_distributed_table_102010", "size" : "1624 kB"}    │
│  102011 │ t       │ {"shard_name" : "my_distributed_table_102011", "size" : "4792 kB"}    │
└─────────┴─────────┴───────────────────────────────────────────────────────────────────────┘
```

## <a name="querying-the-size-of-all-distributed-tables"></a>查询所有分布式表的大小

此查询获取每个分布式表的大小及其索引大小的列表。

``` postgresql
SELECT
  tablename,
  pg_size_pretty(
    citus_total_relation_size(tablename::text)
  ) AS total_size
FROM pg_tables pt
JOIN pg_dist_partition pp
  ON pt.tablename = pp.logicalrelid::text
WHERE schemaname = 'public';
```

示例输出：

```
┌───────────────┬────────────┐
│   tablename   │ total_size │
├───────────────┼────────────┤
│ github_users  │ 39 MB      │
│ github_events │ 98 MB      │
└───────────────┴────────────┘
```

请注意，其他某些超大规模 (Citus) 函数也可用于查询分布式表的大小，具体请参阅[确定表大小](howto-hyperscale-table-size.md)。

## <a name="identifying-unused-indices"></a>识别未使用的索引

以下查询将识别给定分布式表 (`my_distributed_table`) 的工作器节点上未使用的索引

``` postgresql
SELECT *
FROM run_command_on_shards('my_distributed_table', $cmd$
  SELECT array_agg(a) as infos
  FROM (
    SELECT (
      schemaname || '.' || relname || '##' || indexrelname || '##'
                 || pg_size_pretty(pg_relation_size(i.indexrelid))::text
                 || '##' || idx_scan::text
    ) AS a
    FROM  pg_stat_user_indexes ui
    JOIN  pg_index i
    ON    ui.indexrelid = i.indexrelid
    WHERE NOT indisunique
    AND   idx_scan < 50
    AND   pg_relation_size(relid) > 5 * 8192
    AND   (schemaname || '.' || relname)::regclass = '%s'::regclass
    ORDER BY
      pg_relation_size(i.indexrelid) / NULLIF(idx_scan, 0) DESC nulls first,
      pg_relation_size(i.indexrelid) DESC
  ) sub
$cmd$);
```

示例输出：

```
┌─────────┬─────────┬───────────────────────────────────────────────────────────────────────┐
│ shardid │ success │                            result                                     │
├─────────┼─────────┼───────────────────────────────────────────────────────────────────────┤
│  102008 │ t       │                                                                       │
│  102009 │ t       │ {"public.my_distributed_table_102009##some_index_102009##28 MB##0"}   │
│  102010 │ t       │                                                                       │
│  102011 │ t       │                                                                       │
└─────────┴─────────┴───────────────────────────────────────────────────────────────────────┘
```

## <a name="monitoring-client-connection-count"></a>监视客户端连接计数

以下查询统计协调器上打开的连接数，并按类型将这些连接分组。

``` sql
SELECT state, count(*)
FROM pg_stat_activity
GROUP BY state;
```

示例输出：

```
┌────────┬───────┐
│ state  │ count │
├────────┼───────┤
│ active │     3 │
│ idle   │     3 │
│ ∅      │     6 │
└────────┴───────┘
```

## <a name="viewing-system-queries"></a>查看系统查询

### <a name="active-queries"></a>活动查询数

`pg_stat_activity` 视图显示当前正在执行的查询。 你可以筛选以查找主动执行的进程及其后端的进程 ID：

```sql
SELECT pid, query, state
  FROM pg_stat_activity
 WHERE state != 'idle';
```

### <a name="why-are-queries-waiting"></a>查询为何正在等待

我们还可以通过查询查看非空闲查询正在等待的最常见原因。 如需这些原因的说明，请参阅 [PostgreSQL 文档](https://www.postgresql.org/docs/current/monitoring-stats.html#WAIT-EVENT-TABLE)。

```sql
SELECT wait_event || ':' || wait_event_type AS type, count(*) AS number_of_occurences
  FROM pg_stat_activity
 WHERE state != 'idle'
GROUP BY wait_event, wait_event_type
ORDER BY number_of_occurences DESC;
```

在单独查询中并发运行 `pg_sleep` 时的示例输出：

```
┌─────────────────┬──────────────────────┐
│      type       │ number_of_occurences │
├─────────────────┼──────────────────────┤
│ ∅               │                    1 │
│ PgSleep:Timeout │                    1 │
└─────────────────┴──────────────────────┘
```

## <a name="index-hit-rate"></a>索引命中率

此查询将提供所有节点中的索引命中率。 索引点击率有助于确定查询时索引的使用频率。
值在 95% 或以上比较理想。

``` postgresql
-- on coordinator
SELECT 100 * (sum(idx_blks_hit) - sum(idx_blks_read)) / sum(idx_blks_hit) AS index_hit_rate
  FROM pg_statio_user_indexes;

-- on workers
SELECT nodename, result as index_hit_rate
FROM run_command_on_workers($cmd$
  SELECT 100 * (sum(idx_blks_hit) - sum(idx_blks_read)) / sum(idx_blks_hit) AS index_hit_rate
    FROM pg_statio_user_indexes;
$cmd$);
```

示例输出：

```
┌───────────┬────────────────┐
│ nodename  │ index_hit_rate │
├───────────┼────────────────┤
│ 10.0.0.16 │ 96.0           │
│ 10.0.0.20 │ 98.0           │
└───────────┴────────────────┘
```

## <a name="cache-hit-rate"></a>缓存命中率

大多数应用程序通常只是一次性地访问其总数据的一小部分。 PostgreSQL 将频繁访问的数据保存在内存中，以避免从磁盘缓慢读取数据。 可以在 [pg_statio_user_tables](https://www.postgresql.org/docs/current/monitoring-stats.html#MONITORING-PG-STATIO-ALL-TABLES-VIEW) 视图中查看相关统计信息。

一个重要的度量是内存缓存中的数据与工作负载的磁盘中的数据的百分比：

``` postgresql
-- on coordinator
SELECT
  sum(heap_blks_read) AS heap_read,
  sum(heap_blks_hit)  AS heap_hit,
  100 * sum(heap_blks_hit) / (sum(heap_blks_hit) + sum(heap_blks_read)) AS cache_hit_rate
FROM
  pg_statio_user_tables;

-- on workers
SELECT nodename, result as cache_hit_rate
FROM run_command_on_workers($cmd$
  SELECT
    100 * sum(heap_blks_hit) / (sum(heap_blks_hit) + sum(heap_blks_read)) AS cache_hit_rate
  FROM
    pg_statio_user_tables;
$cmd$);
```

示例输出：

```
┌───────────┬──────────┬─────────────────────┐
│ heap_read │ heap_hit │   cache_hit_rate    │
├───────────┼──────────┼─────────────────────┤
│         1 │      132 │ 99.2481203007518796 │
└───────────┴──────────┴─────────────────────┘
```

如果你发现比率明显低于 99%，则可能需要考虑增加可供数据库使用的缓存。

## <a name="next-steps"></a>后续步骤

* 了解可用于诊断的其他[系统表](reference-hyperscale-metadata.md)
