---
title: 确定表大小-超大规模 (Citus) -Azure Database for PostgreSQL
description: 如何在超大规模 (Citus) 服务器组中查找分布式表的真实大小
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 1/5/2021
ms.openlocfilehash: 6ebdbe250862ccd462259900ba56d007f002a52f
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937560"
---
# <a name="determine-table-and-relation-size"></a>确定表和关系大小

在 PostgreSQL 中查找表大小的常用方法， `pg_total_relation_size` 大大降低了超大规模 (Citus) 上的分布式表的大小。
此函数对超大规模 (Citus) 服务器组执行的所有工作就是在协调器节点上显示表的大小。  事实上，分布式表中的数据位于分片) 而不是协调器 (的辅助节点上。 分布式表大小的真正度量值作为分片大小的总和获得。 超大规模 (Citus) 提供帮助程序函数来查询此信息。

<table>
<colgroup>
<col style="width: 40%" />
<col style="width: 59%" />
</colgroup>
<thead>
<tr class="header">
<th>函数</th>
<th>返回</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>citus_relation_size (relation_name) </td>
<td><ul>
<li>表中的实际数据的大小 ("<a href="https://www.postgresql.org/docs/current/static/storage-file-layout.html">main 分叉</a>" ) 。</li>
<li>关系可以是表或索引的名称。</li>
</ul></td>
</tr>
<tr class="even">
<td>citus_table_size (relation_name) </td>
<td><ul>
<li><p>citus_relation_size 加上：</p>
<blockquote>
<ul>
<li><a href="https://www.postgresql.org/docs/current/static/storage-fsm.html">可用空间图</a>的大小</li>
<li><a href="https://www.postgresql.org/docs/current/static/storage-vm.html">可见性地图</a>的大小</li>
</ul>
</blockquote></li>
</ul></td>
</tr>
<tr class="odd">
<td>citus_total_relation_size (relation_name) </td>
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

这些函数类似于三个标准 PostgreSQL [对象大小函数](https://www.postgresql.org/docs/current/static/functions-admin.html#FUNCTIONS-ADMIN-DBSIZE)，除非它们无法连接到节点，它们会出错。

## <a name="example"></a>示例

下面介绍了如何列出所有分布式表的大小：

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

* 了解如何 [缩放服务器组](howto-hyperscale-scale-grow.md) 以容纳更多数据。
* 区分超大规模 (Citus) 服务器组中的 [表类型](concepts-hyperscale-nodes.md) 。
