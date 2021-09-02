---
title: SQL 函数 – 超大规模 (Citus) - Azure Database for PostgreSQL
description: 超大规模 (Citus) SQL API 中的函数
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 04/07/2021
ms.openlocfilehash: 65288730cafaa39507eeab4ed2e3d29267080262
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123310474"
---
# <a name="functions-in-the-hyperscale-citus-sql-api"></a>超大规模 (Citus) SQL API 中的函数

本部分包含有关超大规模 (Citus) 提供的用户定义函数的参考信息。 这些函数有助于向超大规模 (Citus) 提供分布式功能。

> [!NOTE]
>
> 运行旧版 Citus 引擎的超大规模 (Citus) 服务器组可能不会提供以下列出的所有函数。

## <a name="table-and-shard-ddl"></a>表和分片 DDL

### <a name="create_distributed_table"></a>create\_distributed\_table

Create\_distributed\_table() 函数用于定义分布式表，并且如果它是哈希分布式表，还用于创建它的分片。 此函数采用表名称、分布列和可选的分发方法，并插入适当的元数据以将表标记为分布式。 如果未指定分布方法，则函数默认为“哈希”分布。 如果表是哈希分布式，则该函数还基于分片计数和分片复制因子配置值创建工作器分片。 如果表包含任何行，会将这些行自动分布到工作器节点。

此函数取代了 master\_create\_distributed\_table() 后跟 master\_create\_worker\_shards() 的用法。

#### <a name="arguments"></a>参数

table\_name：需要分布的表名称。

distribution\_column：表分布到的列。

distribution\_type：（可选）表分布依据的方法。 允许值为“追加”或“哈希”，默认值为“哈希”。

colocate\_with：（可选）将当前表包含在另一个表的并置组中。 默认情况下，当表是由同一类型的列分布、具有相同的分片计数，并且具有相同的复制因子时，将并置这些表。 `colocate_with` 的可能值为 `default`、启动新并置组的 `none`，或与该表并置的另一个表的名称。  （参见[表并置](concepts-hyperscale-colocation.md)。）

请记住，`colocate_with` 的默认值进行隐式并置。 在表之间相关或将互相联接时，[并置](concepts-hyperscale-colocation.md)可能是很好的做法。  但是，如果两个表不相关，但它们的分布列使用相同的数据类型，则意外并置这些表会在[分片再平衡](howto-hyperscale-scale-rebalance.md)期间降低性能。  表分片将以\"级联\"方式不必要地一起移动。

如果新的分布式表与其他表不相关，最好指定 `colocate_with => 'none'`。

#### <a name="return-value"></a>返回值

不适用

#### <a name="example"></a>示例

此示例通知数据库：github\_events 表应由哈希分布到 repo\_id 列。

```postgresql
SELECT create_distributed_table('github_events', 'repo_id');

-- alternatively, to be more explicit:
SELECT create_distributed_table('github_events', 'repo_id',
                                colocate_with => 'github_repo');
```

### <a name="create_reference_table"></a>create\_reference\_table

create\_reference\_table() 函数用于定义小引用表或维度表。 此函数采用表名称，创建一个只包含一个分片的分布式表，并将其复制到每个工作器节点。

#### <a name="arguments"></a>参数

table\_name：需要分布的小型维度表或引用表的名称。

#### <a name="return-value"></a>返回值

不适用

#### <a name="example"></a>示例

此示例通知数据库，国家表应定义为引用表

```postgresql
SELECT create_reference_table('nation');
```

### <a name="upgrade_to_reference_table"></a>upgrade\_to\_reference\_table

upgrade\_to\_reference\_table() 函数将使用一个现有分布式表（该表的分片计数为 1），并将其升级为可识别的引用表。 调用此函数后，表就像是用 [create_reference_table](#create_reference_table) 创建的一样。

#### <a name="arguments"></a>参数

table\_name：包含分片计数为 1 的分布式表的名称，将以引用表的形式分布。

#### <a name="return-value"></a>返回值

不适用

#### <a name="example"></a>示例

此示例通知数据库：国家表应定义为引用表

```postgresql
SELECT upgrade_to_reference_table('nation');
```

### <a name="mark_tables_colocated"></a>mark\_tables\_colocated

mark\_tables\_colocated() 函数采用分布式表（源表）和其他表的列表（目标表），并将目标表置于与源表相同的并置组中。 如果源表尚不在组中，则此函数将创建一个组，并为其分配源表和目标表。

应通过 [create_distributed_table](#create_distributed_table) 的 `colocate_with` 参数在表分布时并置表，但也可根据需要，稍后由 `mark_tables_colocated` 并置表。

#### <a name="arguments"></a>参数

source\_table\_name：目标将被分布到匹配的并置组的分布式表的名称。

target\_table\_names：分布式目标表的名称数组，必须为非空。 这些分布式表必须与下列因素中的源表匹配：

> -   distribution 方法
> -   分布列类型
> -   复制类型
> -   分片计数

如果上述条件均不适用，则超大规模 (Citus) 将产生错误。 例如，尝试并置表 `apples` 和 `oranges`，其分布列类型会产生不同的结果：

```
ERROR:  XX000: cannot colocate tables apples and oranges
DETAIL:  Distribution column types don't match for apples and oranges.
```

#### <a name="return-value"></a>返回值

不适用

#### <a name="example"></a>示例

此示例将 `products` 和 `line_items` 置于与 `stores` 相同的并置组中。 该示例假定这些表全部分布到具有匹配类型的列中，很可能是\"存储 id\"。

```postgresql
SELECT mark_tables_colocated('stores', ARRAY['products', 'line_items']);
```

### <a name="create_distributed_function"></a>create\_distributed\_function

将函数从协调器节点传播到工作器，并将其标记为分布式执行。 在协调器上调用分布式函数时，超大规模 (Citus) 使用\"分布参数\"的值选取工作器节点来运行该函数。 在工作器上执行函数会增加并行度，并可以使代码更接近分片中的数据，以降低延迟。

在分布式函数执行期间，Postgres 搜索路径不会从协调器传播到工作器，因此分布式函数代码应完全限定数据库对象的名称。 函数发出的通知也不会显示给用户。

#### <a name="arguments"></a>参数

function\_name：分布的函数的名称。 名称必须在括号中包含函数的参数类型，因为在 PostgreSQL 中有多个函数可以具有相同的名称。 例如，`'foo(int)'` 与 `'foo(int, text)'` 不同。

distribution\_arg\_name：（可选）用于分布的参数名称。 为方便起见（或函数参数没有名称）时，允许使用位置占位符，如 `'$1'`。 如果未指定此参数，则仅在工作器上创建由 `function_name` 命名的函数。 如果将来添加工作器节点，该函数也会自动创建。

colocate\_with：（可选）当分布式函数向分布式表（或更普遍的情况下为并置表）读取或写入数据时，请确保使用 `colocate_with` 参数命名该表。 然后，该函数的每个调用都将在包含相关分片的工作器节点上运行。

#### <a name="return-value"></a>返回值

不适用

#### <a name="example"></a>示例

```postgresql
-- an example function which updates a hypothetical
-- event_responses table which itself is distributed by event_id
CREATE OR REPLACE FUNCTION
  register_for_event(p_event_id int, p_user_id int)
RETURNS void LANGUAGE plpgsql AS $fn$
BEGIN
  INSERT INTO event_responses VALUES ($1, $2, 'yes')
  ON CONFLICT (event_id, user_id)
  DO UPDATE SET response = EXCLUDED.response;
END;
$fn$;

-- distribute the function to workers, using the p_event_id argument
-- to determine which shard each invocation affects, and explicitly
-- colocating with event_responses which the function updates
SELECT create_distributed_function(
  'register_for_event(int, int)', 'p_event_id',
  colocate_with := 'event_responses'
);
```

### <a name="alter_columnar_table_set"></a>alter_columnar_table_set

alter_columnar_table_set() 函数用于更改[纵栏表](concepts-hyperscale-columnar.md)的设置， 对非纵栏表调用此函数会发生错误。 表名以外的所有参数都是可选项。

若要查看所有纵栏表的当前选项，请参阅下表：

```postgresql
SELECT * FROM columnar.options;
```

对于新创建的表，其纵栏设置默认值可通过下列 GUC 来替代：

* columnar.compression
* columnar.compression_level
* columnar.stripe_row_count
* columnar.chunk_row_count

#### <a name="arguments"></a>参数

table_name：纵栏表的名称。

chunk_row_count：（可选）新插入的数据中每个块的最大行数。 现有的数据块不会更改，其中包含的行数可能超过这个最大值。 默认值为 10000。

stripe_row_count：（可选）新插入的数据中每个条带的最大行数。 现有的数据条带不会更改，其中包含的行数可能超过这个最大值。 默认值为 150000。

compression：（可选）`[none|pglz|zstd|lz4|lz4hc]` 新插入的数据的压缩类型。 现有数据不会进行重新压缩或解压缩。 默认值和建议值均为 zstd（如果已将支持整合到其中）。

compression_level：（可选）有效设置是 1 到 19。 如果压缩方法不支持所选级别，那么会改为选择最接近的级别。

#### <a name="return-value"></a>返回值

空值

#### <a name="example"></a>示例

```postgresql
SELECT alter_columnar_table_set(
  'my_columnar_table',
  compression => 'none',
  stripe_row_count => 10000);
```

## <a name="metadata--configuration-information"></a>元数据/配置信息

### <a name="master_get_table_metadata"></a>master\_get\_table\_metadata

master\_get\_table\_metadata() 函数可用于为分布式表返回与分布相关的元数据。 此元数据包括表的关系 ID、存储类型、分布方法、分布列、复制计数、最大分片大小和分片放置策略。 除了这些内容，此函数将查询超大规模 (Citus) 元数据表，以获取所需的信息，并将其连接到元组，然后将其返回给用户。

#### <a name="arguments"></a>参数

table\_name：要为其提取元数据的分布式表的名称。

#### <a name="return-value"></a>返回值

包含以下信息的元组：

logical\_relid：分布式表的 Oid。 它引用 pg\_class 系统目录表中的 relfilenode 列。

part\_storage\_type：用于表的存储类型。 可以是“t”（标准表）、“f”（外表）或“c”（柱状表）。

part\_method：用于表的分布方法。 可以是“a”（追加）或“h”（哈希）。

part\_key：表的分布列。

part\_replica\_count：当前分片复制计数。

part\_max\_size：当前最大分片大小（以字节为单位）。

part\_placement\_policy：用于放置表的分片的分片放置策略。 可以是 1（本地节点优先）或 2（轮循机制）。

#### <a name="example"></a>示例

下面的示例提取并显示 github\_events 表的表元数据。

```postgresql
SELECT * from master_get_table_metadata('github_events');
 logical_relid | part_storage_type | part_method | part_key | part_replica_count | part_max_size | part_placement_policy 
---------------+-------------------+-------------+----------+--------------------+---------------+-----------------------
         24180 | t                 | h           | repo_id  |                  2 |    1073741824 |                     2
(1 row)
```

### <a name="get_shard_id_for_distribution_column"></a>get\_shard\_id\_for\_distribution\_column

超大规模 (Citus) 根据行的分布列的值和表的分布方法，将分布式表的每一行分布给分片。 在大多数情况下，精确映射是数据库管理员可忽略的低级别详细信息。 然而，无论是对于手动数据库维护任务，还是仅仅为了满足好奇心，确定行的分片会很有用。 `get_shard_id_for_distribution_column` 函数提供用于哈希分布式表、范围分布式表以及引用表的信息。 它不适用于追加分布。

#### <a name="arguments"></a>参数

table\_name：分布式表。

distribution\_value：分布列的值。

#### <a name="return-value"></a>返回值

分片 ID 超大规模 (Citus) 与给定表的分布列值关联。

#### <a name="example"></a>示例

```postgresql
SELECT get_shard_id_for_distribution_column('my_table', 4);

 get_shard_id_for_distribution_column
--------------------------------------
                               540007
(1 row)
```

### <a name="column_to_column_name"></a>column\_to\_column\_name

将 `pg_dist_partition` 的 `partkey` 列转换为文本列名称。 该转换可用于确定分布式表的分布列。

有关更详细的讨论，请参阅“[选择分布列](concepts-hyperscale-choose-distribution-column.md)”。

#### <a name="arguments"></a>参数

table **name：分布式表。\_**

column\_var\_text：`pg_dist_partition` 表中的 `partkey` 值。

#### <a name="return-value"></a>返回值

`table_name` 的分布列名称。

#### <a name="example"></a>示例

```postgresql
-- get distribution column name for products table

SELECT column_to_column_name(logicalrelid, partkey) AS dist_col_name
  FROM pg_dist_partition
 WHERE logicalrelid='products'::regclass;
```

输出：

```
┌───────────────┐
│ dist_col_name │
├───────────────┤
│ company_id    │
└───────────────┘
```

### <a name="citus_relation_size"></a>citus\_relation\_size

获取指定的分布式表的所有分片所使用的磁盘空间。
磁盘空间包括\"主分叉\"的大小，但不包括分片的可见性映射和可用空间映射。

#### <a name="arguments"></a>参数

logicalrelid：分布式表的名称。

#### <a name="return-value"></a>返回值

以字节为单位的大小，为 bigint。

#### <a name="example"></a>示例

```postgresql
SELECT pg_size_pretty(citus_relation_size('github_events'));
```

```
pg_size_pretty
--------------
23 MB
```

### <a name="citus_table_size"></a>citus\_table\_size

获取指定的分布式表的所有分片所使用的磁盘空间，不包括索引（但包括 TOAST、可用空间映射和可见性映射）。

#### <a name="arguments"></a>参数

logicalrelid：分布式表的名称。

#### <a name="return-value"></a>返回值

以字节为单位的大小，为 bigint。

#### <a name="example"></a>示例

```postgresql
SELECT pg_size_pretty(citus_table_size('github_events'));
```

```
pg_size_pretty
--------------
37 MB
```

### <a name="citus_total_relation_size"></a>citus\_total\_relation\_size

获取指定的分布式表的所有分片使用的总磁盘空间，包括所有索引和 TOAST 数据。

#### <a name="arguments"></a>参数

logicalrelid：分布式表的名称。

#### <a name="return-value"></a>返回值

以字节为单位的大小，为 bigint。

#### <a name="example"></a>示例

```postgresql
SELECT pg_size_pretty(citus_total_relation_size('github_events'));
```

```
pg_size_pretty
--------------
73 MB
```

### <a name="citus_stat_statements_reset"></a>citus\_stat\_statements\_reset

从 [citus_stat_statements](reference-hyperscale-metadata.md#query-statistics-table) 中移除所有行。
此函数独立于 `pg_stat_statements_reset()` 使用。 如果要重置所有统计信息，请调用这两个函数。

#### <a name="arguments"></a>参数

不适用

#### <a name="return-value"></a>返回值

无

## <a name="server-group-management-and-repair"></a>服务器组管理和修复

### <a name="master_copy_shard_placement"></a>master\_copy\_shard\_placement

如果在修改命令或 DDL 操作过程中无法更新分片放置，则会将其标记为非活动状态。 然后，可以调用 master\_copy\_shard\_placement 函数来通过使用正常放置的数据修复非活动的分片放置。

如果要修复分片，该函数将首先删除不正常的分片放置，并使用协调器上的架构重新创建。 创建分片放置后，该函数将从正常放置复制数据，并更新元数据以将新的分片放置标记为正常。 此函数可确保在修复过程中，避免对分片进行任何并发修改。

#### <a name="arguments"></a>参数

shard\_id：要修复的分片的 ID。

source\_node\_name：存在正常分片放置的节点的 DNS 名称（\"源\"节点）。

source\_node\_port：数据库服务器正在侦听的源工作器节点上的端口。

target\_node\_name：存在无效分片放置的节点的 DNS 名称（\"目标\"节点）。

target\_node\_port：数据库服务器正在侦听的目标工作器节点上的端口。

#### <a name="return-value"></a>返回值

不适用

#### <a name="example"></a>示例

下面的示例将修复分片 12345 的非活动分片放置，该分片放置位于在端口 5432 上“bad\_host”中运行的数据库服务器上。 如果要修复此问题，它将使用以下端口上“good\_host”中运行的服务器上的正常分片放置的数据：
5432.

```postgresql
SELECT master_copy_shard_placement(12345, 'good_host', 5432, 'bad_host', 5432);
```

### <a name="master_move_shard_placement"></a>master\_move\_shard\_placement

此函数将给定分片（以及与其并置的分片）从一个节点移到另一个节点。 通常在分片再平衡期间间接使用此函数，而不是由数据库管理员直接调用。

可以通过两种方式移动数据：阻止或非阻止。 阻止方式意味着在移动数据时，将暂停对分片的所有修改。
第二种方法则可以避免阻止分片写入，这种方法依靠 Postgres 10 逻辑复制。

成功移动操作后，会删除源节点中的分片。 如果移动在任何点上失败，则此函数将引发错误，并使源节点和目标节点保持不变。

#### <a name="arguments"></a>参数

shard\_id：要移动的分片 ID。

source\_node\_name：存在正常分片放置的节点的 DNS 名称（\"源\"节点）。

source\_node\_port：数据库服务器正在侦听的源工作器节点上的端口。

target\_node\_name：存在无效分片放置的节点的 DNS 名称（\"目标\"节点）。

target\_node\_port：数据库服务器正在侦听的目标工作器节点上的端口。

shard\_transfer\_mode：（可选）指定复制方法，是使用 PostgreSQL 逻辑复制还是使用跨工作器 COPY 命令。 可能的值包括：

> -   `auto`：如果可以进行逻辑复制，则需要副本标识，否则使用旧行为（例如，对于分片修复，使用 PostgreSQL 9.6）。 这是默认值。
> -   `force_logical`：使用逻辑复制，即使该表没有副本标识。 在复制过程中对表的任何并发更新/删除语句都将失败。
> -   `block_writes`：对缺少主键或副本标识的表使用 COPY 命令（阻止写入）。

#### <a name="return-value"></a>返回值

不适用

#### <a name="example"></a>示例

```postgresql
SELECT master_move_shard_placement(12345, 'from_host', 5432, 'to_host', 5432);
```

### <a name="rebalance_table_shards"></a>rebalance\_table\_shards

rebalance\_table\_shards() 函数将移动给定表的分片，以使这些分片在工作器之间均匀分布。 函数首先计算需要执行移动的列表，以确保服务器组在给定的阈值内平衡。 然后，它将分片放置逐个从源节点移动到目标节点，并更新相应的分片元数据以反映移动情况。

在确定分片是否\"均匀分布\"时，系统会向每个分片分配成本。默认情况下，每个分片的成本都相同（数值为 1），因此工作器之间的成本均等化分布就表示分片数量也是均等化分布。 固定成本策略称为 \"by\_shard\_count\"，是默认的再平衡策略。

默认策略在以下情况下适用：

*  分片的大小大致相同
*  分片获得大致相同数量的流量
*  工作器节点大小/类型相同
*  分片尚未固定到特定工作器

如果其中任何一个假设不成立，则默认再平衡可能会导致计划不佳。 在这种情况下，可以使用 `rebalance_strategy` 参数自定义策略。

建议在运行 rebalance\_table\_shards 之前调用 [get_rebalance_table_shards_plan](#get_rebalance_table_shards_plan)，查看并验证要执行的操作。

#### <a name="arguments"></a>参数

table\_name：（可选）分片需要再平衡的表的名称。 如果为 NULL，则再平衡所有现有的并置组。

threshold：（可选）介于 0.0 和 1.0 之间的浮点数，指示节点利用率与平均利用率的最大差值。 例如，如果指定 0.1，分片再平衡器会尝试平衡所有节点，使其保持相同数量分片 ±10% 的水平。
具体而言，分片再平衡器会尝试将所有工作器节点的利用率汇聚到 (1 - threshold) \* average\_utilization \... (1
+ threshold) \* average\_utilization 范围。

max\_shard\_moves：（可选）要移动的分片的最大数目。

excluded\_shard\_list：（可选）在再平衡操作期间，不应移动的分片标识符。

shard\_transfer\_mode：（可选）指定复制方法，是使用 PostgreSQL 逻辑复制还是使用跨工作器 COPY 命令。 可能的值包括：

> -   `auto`：如果可以进行逻辑复制，则需要副本标识，否则使用旧行为（例如，对于分片修复，使用 PostgreSQL 9.6）。 这是默认值。
> -   `force_logical`：使用逻辑复制，即使该表没有副本标识。 在复制过程中对表的任何并发更新/删除语句都将失败。
> -   `block_writes`：对缺少主键或副本标识的表使用 COPY 命令（阻止写入）。

drain\_only：（可选）如果为 true，则将在 [pg_dist_node](reference-hyperscale-metadata.md#worker-node-table) 中把 `shouldhaveshards` 设为 false 的分片从工作器节点移出；不移动其他分片。

rebalance\_strategy：（可选）[pg_dist_rebalance_strategy](reference-hyperscale-metadata.md#rebalancer-strategy-table) 中策略的名称。
如果省略此参数，则函数将选择默认策略，如表中所示。

#### <a name="return-value"></a>返回值

不适用

#### <a name="example"></a>示例

下面的示例将在默认阈值内尝试再平衡 github\_events 表的分片。

```postgresql
SELECT rebalance_table_shards('github_events');
```

此示例将尝试再平衡 github\_events 表，而不移动 ID 为 1 和 2 的分片。

```postgresql
SELECT rebalance_table_shards('github_events', excluded_shard_list:='{1,2}');
```

### <a name="get_rebalance_table_shards_plan"></a>get\_rebalance\_table\_shards\_plan

输出 [rebalance_table_shards](#rebalance_table_shards) 的计划分片移动，而不执行该移动。
虽然不太可能，但使用相同参数的 rebalance\_table\_shards 调用会输出一个计划，get\_rebalance\_table\_shards\_plan 输出的计划与以上这个计划稍有不同。 它们不会同时执行，因此服务器组，\-- 例如，磁盘空间\-- 可能会因调用而异。

#### <a name="arguments"></a>参数

与 rebalance\_table\_shards 相同的参数：relation、threshold、max\_shard\_moves、excluded\_shard\_list 和 drain\_only。 请参阅该函数的文档了解参数的含义。

#### <a name="return-value"></a>返回值

包含以下列的元组：

-   table\_name：其中分片将移动的表
-   shardid：相关分片
-   shard\_size：大小（以字节为单位）
-   sourcename：源节点的主机名
-   sourceport：源节点的端口
-   targetname：目标节点的主机名
-   targetport：目标节点的端口

### <a name="get_rebalance_progress"></a>get\_rebalance\_progress

分片再平衡开始后，该 `get_rebalance_progress()` 函数将列出所涉及的每个分片的进度。 它将监视由 `rebalance_table_shards()` 计划和执行的移动。

#### <a name="arguments"></a>参数

不适用

#### <a name="return-value"></a>返回值

包含以下列的元组：

-   sessionid：再平衡监视器的 Postgres PID
-   table\_name：其中分片将移动的表
-   shardid：相关分片
-   shard\_size：大小（以字节为单位）
-   sourcename：源节点的主机名
-   sourceport：源节点的端口
-   targetname：目标节点的主机名
-   targetport：目标节点的端口
-   progress：0 = 正在等待移动；1 = 正在移动；2 = 完成

#### <a name="example"></a>示例

```sql
SELECT * FROM get_rebalance_progress();
```

```
┌───────────┬────────────┬─────────┬────────────┬───────────────┬────────────┬───────────────┬────────────┬──────────┐
│ sessionid │ table_name │ shardid │ shard_size │  sourcename   │ sourceport │  targetname   │ targetport │ progress │
├───────────┼────────────┼─────────┼────────────┼───────────────┼────────────┼───────────────┼────────────┼──────────┤
│      7083 │ foo        │  102008 │    1204224 │ n1.foobar.com │       5432 │ n4.foobar.com │       5432 │        0 │
│      7083 │ foo        │  102009 │    1802240 │ n1.foobar.com │       5432 │ n4.foobar.com │       5432 │        0 │
│      7083 │ foo        │  102018 │     614400 │ n2.foobar.com │       5432 │ n4.foobar.com │       5432 │        1 │
│      7083 │ foo        │  102019 │       8192 │ n3.foobar.com │       5432 │ n4.foobar.com │       5432 │        2 │
└───────────┴────────────┴─────────┴────────────┴───────────────┴────────────┴───────────────┴────────────┴──────────┘
```

### <a name="citus_add_rebalance_strategy"></a>citus\_add\_rebalance\_strategy

向 [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md?#rebalancer-strategy-table) 追加行。

#### <a name="arguments"></a>参数

有关这些参数的详细信息，请参阅 `pg_dist_rebalance_strategy` 中的相应列的值。

name：新策略的标识符

shard\_cost\_function：标识用于确定每个分片\"成本\"的函数

node\_capacity\_function：标识用于测量节点容量的函数

shard\_allowed\_on\_node\_function：标识用于确定哪些分片可放置在哪些节点上的函数

default\_threshold：一个浮点阈值，用于调整如何精确平衡节点间累积分片成本

minimum\_threshold：（可选）一个保护列，用于保存 rebalance\_table\_shards() 阈值参数的最小值。 默认值为 0

#### <a name="return-value"></a>返回值

空值

### <a name="citus_set_default_rebalance_strategy"></a>citus\_set\_default\_rebalance\_strategy

更新 [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md#rebalancer-strategy-table) 表，将其参数命名的策略更改为在再平衡分片时选择的默认策略。

#### <a name="arguments"></a>参数

name：pg\_dist\_rebalance\_strategy 中的策略名称

#### <a name="return-value"></a>返回值

空值

#### <a name="example"></a>示例

```postgresql
SELECT citus_set_default_rebalance_strategy('by_disk_size');
```

### <a name="citus_remote_connection_stats"></a>citus\_remote\_connection\_stats

citus\_remote\_connection\_stats() 函数显示每个远程节点的活动连接数。

#### <a name="arguments"></a>参数

不适用

#### <a name="example"></a>示例

```postgresql
SELECT * from citus_remote_connection_stats();
```

```
    hostname    | port | database_name | connection_count_to_node
----------------+------+---------------+--------------------------
 citus_worker_1 | 5432 | postgres      |                        3
(1 row)
```

### <a name="replicate_table_shards"></a>replicate\_table\_shards

replicate\_table\_shards() 函数将复制给定表中需要复制的分片。 此函数首先计算需要复制的分片和位置（可提取分片用于复制的位置）列表。 然后，该函数将复制这些分片，并更新相应的分片元数据以反映副本。

#### <a name="arguments"></a>参数

table\_name：其中有分片需要复制的表的名称。

shard\_replication\_factor：（可选）每个分片所需的复制因子。

max\_shard\_copies：（可选）要复制的最大分片数，以达到所需的复制因子。

excluded\_shard\_list：（可选）在再平衡操作期间，不应复制的分片标识符。

#### <a name="return-value"></a>返回值

不可用

#### <a name="examples"></a>示例

下面的示例将尝试将 github\_events 的分片复制到 shard\_replication\_factor。

```postgresql
SELECT replicate_table_shards('github_events');
```

此示例将使用最多 10 个分片副本，尝试将 github\_events 表的分片导入到所需的复制因子。 再平衡器会在其尝试中复制最多 10 个分片，以达到所需的复制因子。

```postgresql
SELECT replicate_table_shards('github_events', max_shard_copies:=10);
```

### <a name="isolate_tenant_to_new_shard"></a>isolate\_tenant\_to\_new\_shard

此函数将创建新的分片，用于保存分布列中具有特定单个值的行。 这对于多租户超大规模 (Citus) 用例特别有用，其中，大型租户可单独放置在其自己的分片上，并最终位于其自身的物理节点上。

#### <a name="arguments"></a>参数

table\_name：要获取新分片的表的名称。

tenant\_id：将分配给新分片的分布列的值。

cascade\_option：（可选）当设置为 \"CASCADE,\" 时，还会将分片与当前表的 [并置组](concepts-hyperscale-colocation.md)中的所有表隔离开来。

#### <a name="return-value"></a>返回值

shard\_id：此函数将返回分配给新建分片的唯一 ID。

#### <a name="examples"></a>示例

创建新的分片来保存租户 135 的排列项：

```postgresql
SELECT isolate_tenant_to_new_shard('lineitem', 135);
```

```
┌─────────────────────────────┐
│ isolate_tenant_to_new_shard │
├─────────────────────────────┤
│                      102240 │
└─────────────────────────────┘
```

## <a name="next-steps"></a>后续步骤

* 本文中的许多函数可修改系统[元数据表](reference-hyperscale-metadata.md)
* [服务器参数](reference-hyperscale-parameters.md)可定义一些函数的行为
