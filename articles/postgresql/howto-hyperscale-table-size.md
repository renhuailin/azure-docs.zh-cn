---
title: 确定表大小 - 超大规模 (Citus) - Azure Database for PostgreSQL
description: 如何在超大规模 (Citus) 服务器组中查找分布式表的真实大小
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 1/5/2021
ms.openlocfilehash: 10426baece6b14984dc10a6eadc1d5fc19db382c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631886"
---
# <a name="determine-table-and-relation-size"></a>确定表和关系的大小

在 PostgreSQL 中查找表大小的常用方法 `pg_total_relation_size` 大大低估了超大规模 (Citus) 上分布式表的大小。
此函数在“超大规模(Citus)”服务器组上的作用就是显示协调器节点上表的大小。  实际上，分布式表中的数据位于工作器节点（在分片中）而不是协调器节点上。 实际获得的分布式表大小的度量值是分片大小的总和。 超大规模 (Citus) 提供了帮助程序函数来查询此信息。

<table>
<colgroup>
<col width="40%" />
<col width="59%" />
</colgroup>
<thead>
<tr class="header">
<th>函数</th>
<th>返回</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>citus_relation_size(relation_name)</td>
<td><ul>
<li>表中实际数据（<a href="https://www.postgresql.org/docs/current/static/storage-file-layout.html">主分支</a>）的大小。</li>
<li>关系可以是表或索引的名称。</li>
</ul></td>
</tr>
<tr class="even">
<td>citus_table_size(relation_name)</td>
<td><ul>
<li><p>citus_relation_size 加上：</p>
<blockquote>
<ul>
<li><a href="https://www.postgresql.org/docs/current/static/storage-fsm.html">可用空间图</a>的大小</li>
<li><a href="https://www.postgresql.org/docs/current/static/storage-vm.html">可见性图</a>的大小</li>
</ul>
</blockquote></li>
</ul></td>
</tr>
<tr class="odd">
<td>citus_total_relation_size(relation_name)</td>
<td><ul>
<li><p>citus_table_size 加上：</p>
<blockquote>
<ul>
<li>索引的大小</li>
</ul>
</blockquote></li>
</ul></td>
</tr>
</tbody>
</table>

这些函数类似于三个标准的 PostgreSQL [对象大小函数](https://www.postgresql.org/docs/current/static/functions-admin.html#FUNCTIONS-ADMIN-DBSIZE)，除非它们不能连接到一个节点，那样会出错。

## <a name="example"></a>示例

下面演示如何列出所有分布式表的大小：

``` postgresql
SELECT logicalrelid AS name,
       pg_size_pretty(citus_table_size(logicalrelid)) AS size
  FROM pg_dist_partition;
```

输出：

```
┌───────────────┬───────┐
│     name      │ size  │
├───────────────┼───────┤
│ github_users  │ 39 MB │
│ github_events │ 37 MB │
└───────────────┴───────┘
```

## <a name="next-steps"></a>后续步骤

* 了解如何[缩放服务器组](howto-hyperscale-scale-grow.md)以容纳更多数据。
* 区分超大规模 (Citus) 服务器组中的[表类型](concepts-hyperscale-nodes.md)。
