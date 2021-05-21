---
title: 表共置 – 超大规模 (Citus) - Azure Database for PostgreSQL
description: 如何将相关信息存储在一起以加快查询速度
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 842563319e09a001fd6e85403d8aee6fb14690ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "90884426"
---
# <a name="table-colocation-in-azure-database-for-postgresql--hyperscale-citus"></a>Azure Database for PostgreSQL - 超大规模 (Citus) 中的表共置

共置是指将相关信息一起存储在相同节点上。 当提供了所有必要数据时，查询可以快速运行，而且不会产生任何网络流量。 将相关数据共置在不同节点上可使查询在每个节点上高效地并行运行。

## <a name="data-colocation-for-hash-distributed-tables"></a>哈希分布式表的数据共置

在 Azure Database for PostgreSQL - 超大规模 (Citus) 中，如果分布列中值的哈希值处于某个分片的哈希范围内，则行将存储在该分片中。 具有相同哈希范围的分片始终放置在同一节点上。 分布列值相等的行始终位于跨表的同一节点上。

:::image type="content" source="media/concepts-hyperscale-colocation/colocation-shards.png" alt-text="Shards":::

## <a name="a-practical-example-of-colocation"></a>实用的共置示例

假设下表可能是某个多租户 Web Analytics SaaS 的一部分：

```sql
CREATE TABLE event (
  tenant_id int,
  event_id bigint,
  page_id int,
  payload jsonb,
  primary key (tenant_id, event_id)
);

CREATE TABLE page (
  tenant_id int,
  page_id int,
  path text,
  primary key (tenant_id, page_id)
);
```

现在，我们需要应答面向客户的仪表板可能发出的查询。 一个示例查询是“返回租户 6 中以‘/blog’开头的所有页在过去一周的访问次数。”

如果我们的数据采用“单服务器”部署选项，则我们可以使用 SQL 提供的丰富关系操作集轻松表达该查询：

```sql
SELECT page_id, count(event_id)
FROM
  page
LEFT JOIN  (
  SELECT * FROM event
  WHERE (payload->>'time')::timestamptz >= now() - interval '1 week'
) recent
USING (tenant_id, page_id)
WHERE tenant_id = 6 AND path LIKE '/blog%'
GROUP BY page_id;
```

只要此查询的[工作集](https://en.wikipedia.org/wiki/Working_set)能够装入内存，则单服务器表就是一个适当的解决方案。 让我们考虑通过“超大规模 (Citus)”部署选项缩放数据模型的可能性。

### <a name="distribute-tables-by-id"></a>按 ID 分布表

随着租户数量以及为每个租户存储的数据不断增长，单服务器查询将开始减慢速度。 工作集不再可以装入内存，而 CPU 成了瓶颈。

在这种情况下，我们可以使用超大规模 (Citus) 将数据分片到许多节点。 决定分片时，需要做出的第一项、也是最重要的选择是分布列。 让我们从最原始的选择着手：对事件表使用 `event_id`，对 `page` 表使用 `page_id`：

```sql
-- naively use event_id and page_id as distribution columns

SELECT create_distributed_table('event', 'event_id');
SELECT create_distributed_table('page', 'page_id');
```

将数据分散到不同的辅助角色后，我们无法像在单个 PostgreSQL 节点上那样执行联接操作， 而是需要发出两个查询：

```sql
-- (Q1) get the relevant page_ids
SELECT page_id FROM page WHERE path LIKE '/blog%' AND tenant_id = 6;

-- (Q2) get the counts
SELECT page_id, count(*) AS count
FROM event
WHERE page_id IN (/*…page IDs from first query…*/)
  AND tenant_id = 6
  AND (payload->>'time')::date >= now() - interval '1 week'
GROUP BY page_id ORDER BY count DESC LIMIT 10;
```

然后，应用程序需要合并两个步骤的结果。

运行查询必须查阅分散在节点之间的分片中的数据。

:::image type="content" source="media/concepts-hyperscale-colocation/colocation-inefficient-queries.png" alt-text="低效的查询":::

在这种情况下，数据分布会在以下方面造成严重的弊端：

-   查询每个分片并运行多个查询所产生的开销。
-   向客户端返回大量行的查询 1 的开销。
-   查询 2 变得庞大。
-   在多个步骤中编写查询需要在应用程序中进行更改。

数据是分散的，因此可以并行化查询。 仅当查询执行的工作量大大超过查询许多分片所产生的开销时，这种做法才有利。

### <a name="distribute-tables-by-tenant"></a>按租户分布表

在超大规模 (Citus) 中，可以保证具有相同分布列值的行位于同一节点上。 我们可以使用 `tenant_id` 作为分布列，从头开始创建表。

```sql
-- co-locate tables by using a common distribution column
SELECT create_distributed_table('event', 'tenant_id');
SELECT create_distributed_table('page', 'tenant_id', colocate_with => 'event');
```

现在，超大规模 (Citus) 无需经过修改，即可应答原始的单服务器查询（查询 1）：

```sql
SELECT page_id, count(event_id)
FROM
  page
LEFT JOIN  (
  SELECT * FROM event
  WHERE (payload->>'time')::timestamptz >= now() - interval '1 week'
) recent
USING (tenant_id, page_id)
WHERE tenant_id = 6 AND path LIKE '/blog%'
GROUP BY page_id;
```

由于根据 tenant_id 执行了筛选和联接，超大规模 (Citus) 知道可以使用包含该特定租户的数据的共置分片集来应答整个查询。 单个 PostgreSQL 节点可以通过单个步骤应答查询。

:::image type="content" source="media/concepts-hyperscale-colocation/colocation-better-query.png" alt-text="更好的查询":::

在某些情况下，必须更改查询和表架构，以在唯一约束和联接条件中包含租户 ID。 通常可以直接进行这种更改。

## <a name="next-steps"></a>后续步骤

- 参阅[多租户教程](tutorial-design-database-hyperscale-multi-tenant.md)了解租户数据的共置方式。
