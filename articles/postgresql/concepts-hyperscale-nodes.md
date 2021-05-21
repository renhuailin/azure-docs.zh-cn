---
title: 节点 – 超大规模 (Citus) - Azure Database for PostgreSQL
description: 了解 Azure Database for PostgreSQL 服务器组中的节点类型和表类型。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 07/28/2019
ms.openlocfilehash: b3eda2c8de8319552f32938f20ff98af0e0a49fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "91314820"
---
# <a name="nodes-and-tables-in-azure-database-for-postgresql--hyperscale-citus"></a>Azure Database for PostgreSQL – 超大规模 (Citus) 中的节点和表

## <a name="nodes"></a>节点

使用超大规模 (Citus) 托管类型时，Azure Database for PostgreSQL 服务器（称为节点）可以在“无共享内容”体系结构下相互协调。 与在单个服务器上相比，服务器组中的节点总共能够容纳的数据和使用的 CPU 核心数都更多。 在该体系结构中，你还可以通过在服务器组中添加更多节点来缩放数据库。

### <a name="coordinator-and-workers"></a>协调器和工作器

每个服务器组都有一个协调器节点和多个工作器。 应用程序将查询发送到协调器节点，此节点将它中继到相关工作进程并累积结果。 应用程序无法直接连接到工作器。

使用超大规模 (Citus) 时，数据库管理员能够分布表，将不同的行存储在不同的工作器节点中。 分布式表是影响超大规模 (Citus) 性能的关键因素。 如果表无法分布，它们将全部位于协调器节点中，这样就无法实现跨计算机并行。

对于针对分布式表的每个查询，协调器会将其路由到单个工作器节点，或跨多个工作器节点并行运行该查询，具体取决于所需数据位于单个节点还是多个节点中。 协调器通过查阅元数据表决定执行哪种操作。 这些表跟踪工作器节点的 DNS 名称和运行状况，以及跨节点数据的分布情况。

## <a name="table-types"></a>表类型

超大规模 (Citus) 服务器组中有三种表类型，每种表都以不同方式存储在节点中，并且用于不同的目的。

### <a name="type-1-distributed-tables"></a>类型 1：分布式表

第一种类型是分布式表，也是最常见的类型。 对于 SQL 语句而言，它们看似是普通的表，但它们在各个工作器节点之间进行水平分区。 也就是说，表的各个行存储在不同节点上的片段表（称为分片）中。

超大规模 (Citus) 不仅在群集中运行 SQL 语句，还运行 DDL 语句。
更改分布式表的架构会执行级联，进而更新各个工作器中的所有表分片。

#### <a name="distribution-column"></a>分布列

超大规模 (Citus) 使用分片算法将行分配到分片。 基于表列（称为分布列）的值执行分配，此分配具有确定性。 群集管理员在分布表时必须指定此列。
做出正确的选择，这一点对于性能和功能有重要影响。

### <a name="type-2-reference-tables"></a>类型 2：引用表

引用表是一种分布式表，其所有内容都集中到了单个分片中。 此分片复制到了每一个工作器中。 针对任何工作器的查询都可以在本地访问引用信息，无需从另一个节点请求行，因此也不会产生此类网络开销。 引用表没有分布列，因为无需区分每行的各个分片。

引用表通常很小，并用于存储与任何工作器节点上运行的查询相关的数据。 例如，订单状态或产品类别等枚举值。

### <a name="type-3-local-tables"></a>类型 3：本地表

使用超大规模 (Citus) 时，连接到的协调器节点是一个常规的 PostgreSQL 数据库。 可以在此协调器上创建普通的表，并选择不将这些表分片。

本地表最好是不参与连接查询的小型管理表。 例如，用于执行应用程序登录和身份验证的用户表。

## <a name="shards"></a>Shards

前面的部分介绍了分布式表如何存储为工作器节点中的分片。 本部分将进一步介绍技术细节。

协调器上的 `pg_dist_shard` 元数据表包含系统中每个分布式表的每个分片的行。 该行与分片 ID 相匹配，分片 ID 的范围是一组哈希整数 (shardminvalue, shardmaxvalue)。

```sql
SELECT * from pg_dist_shard;
 logicalrelid  | shardid | shardstorage | shardminvalue | shardmaxvalue
---------------+---------+--------------+---------------+---------------
 github_events |  102026 | t            | 268435456     | 402653183
 github_events |  102027 | t            | 402653184     | 536870911
 github_events |  102028 | t            | 536870912     | 671088639
 github_events |  102029 | t            | 671088640     | 805306367
 (4 rows)
```

如果协调器节点要确定哪个分片包含 `github_events` 行，它将对行中分布列的值执行哈希算法。 然后此节点检查哪个分片的范围包含此哈希值。 定义范围后，哈希函数的图像就是两者的并查。

### <a name="shard-placements"></a>分片放置

假设分片 102027 与相应的行关联。 在某个工作器中的 `github_events_102027` 表中读取或写入此行。 是哪个工作器？ 这完全由元数据表确定。 分片映射到工作器的过程称为分片放置。

协调器节点将查询重写为引用特定表（例如 `github_events_102027`）的片段，并对相应工作器运行这些片段。 下面的查询示例在后台运行，旨在查找分片 ID 为 102027 的节点。

```sql
SELECT
    shardid,
    node.nodename,
    node.nodeport
FROM pg_dist_placement placement
JOIN pg_dist_node node
  ON placement.groupid = node.groupid
 AND node.noderole = 'primary'::noderole
WHERE shardid = 102027;
```

```output
┌─────────┬───────────┬──────────┐
│ shardid │ nodename  │ nodeport │
├─────────┼───────────┼──────────┤
│  102027 │ localhost │     5433 │
└─────────┴───────────┴──────────┘
```

## <a name="next-steps"></a>后续步骤

- [确定应用程序类型](concepts-hyperscale-app-type.md)，为执行数据建模做准备
