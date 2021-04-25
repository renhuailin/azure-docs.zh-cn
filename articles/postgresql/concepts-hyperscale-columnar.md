---
title: 列式表存储预览版 - 超大规模 (Citus) - Azure Database for PostgreSQL
description: 使用列式存储压缩数据（预览版）
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: c60c398d49a802dbe051ca37c4aea2092ab5144b
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2021
ms.locfileid: "107023796"
---
# <a name="columnar-table-storage-preview"></a>列式表存储（预览版）

> [!IMPORTANT]
> 超大规模 (Citus) 中的列式表存储目前为预览版。 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
>
> 请参阅[超大规模 (Citus) 的预览功能](hyperscale-preview-features.md)，以查看其他新功能的完整列表。

对于分析和数据仓库工作负载，Azure Database for PostgreSQL - 超大规模 (Citus) 支持仅限追加的列式表存储。 当列（而不是行）在磁盘上连续存储时，数据的可压缩性会提高，并且查询可以更快地请求列的子集。

若要使用列式存储，请在创建表时指定 `USING columnar`：

```postgresql
CREATE TABLE contestant (
    handle TEXT,
    birthdate DATE,
    rating INT,
    percentile FLOAT,
    country CHAR(3),
    achievements TEXT[]
) USING columnar;
```

超大规模 (Citus) 在插入过程中会将行转换为“带区”形式的列式存储。 每条带区包含一个事务的数据或 150000 行（以较小者为准）。  （可以使用 [alter_columnar_table_set](reference-hyperscale-functions.md#alter_columnar_table_set) 函数更改列式表的带区大小和其他参数。）

例如，下面的语句将所有五行置于相同带区中，因为所有值都在单个事务中插入：

```postgresql
-- insert these values into a single columnar stripe

INSERT INTO contestant VALUES
  ('a','1990-01-10',2090,97.1,'XA','{a}'),
  ('b','1990-11-01',2203,98.1,'XA','{a,b}'),
  ('c','1988-11-01',2907,99.4,'XB','{w,y}'),
  ('d','1985-05-05',2314,98.3,'XB','{}'),
  ('e','1995-05-05',2236,98.2,'XC','{a}');
```

最好尽可能创建大型带区，因为超大规模 (Citus) 会对每个带区单独压缩列式数据。 可以使用 `VACUUM VERBOSE` 查看有关列式表的事实，如压缩率、带区数和每个带区的平均行数：

```postgresql
VACUUM VERBOSE contestant;
```
```
INFO:  statistics for "contestant":
storage id: 10000000000
total file size: 24576, total data size: 248
compression rate: 1.31x
total row count: 5, stripe count: 1, average rows per stripe: 5
chunk count: 6, containing data for dropped columns: 0, zstd compressed: 6
```

输出显示超大规模 (Citus) 使用 zstd 压缩算法获得 1.31 倍数据压缩。 压缩率会将 a) 暂存在内存中的插入数据的大小与 b) 该数据在最终带区中压缩后的大小进行比较。

由于测量方式的不同，压缩率可能与表的行式与列式存储之间的大小差异匹配，也可能不匹配。 真正发现这种差异的唯一方法是构造包含相同数据的行式和列式表，并进行比较：

```postgresql
CREATE TABLE contestant_row AS
    SELECT * FROM contestant;

SELECT pg_total_relation_size('contestant_row') as row_size,
       pg_total_relation_size('contestant') as columnar_size;
```
```
 row_size | columnar_size
----------+---------------
    16384 |         24576
```

对于我们的微小表，列式存储实际上使用更多空间，但是随着数据增长，压缩会更胜一筹。

## <a name="example"></a>示例

列式存储适用于表分区。 有关示例，请参阅 Citus 引擎社区文档[使用列式存储进行存档](https://docs.citusdata.com/en/stable/use_cases/timeseries.html#archiving-with-columnar-storage)。

## <a name="gotchas"></a>难点

* 列式存储按带区进行压缩。 带区按事务进行创建，因此每个事务插入一行会将单个行置于自己的带区中。 单行带区的压缩和性能会不如行式表。 应始终批量插入列式表。
* 如果你未处理好，使一堆微小带区形成列式存储，便会被卡住。
  唯一的解决方法是创建新的列式表，并在一个事务中从原始表复制数据：
  ```postgresql
  BEGIN;
  CREATE TABLE foo_compacted (LIKE foo) USING columnar;
  INSERT INTO foo_compacted SELECT * FROM foo;
  DROP TABLE foo;
  ALTER TABLE foo_compacted RENAME TO foo;
  COMMIT;
  ```
* 从根本上讲，不可压缩的数据可能成为问题，尽管在选择特定列时，列式存储仍然十分有用。 无需将其他列加载到内存中。
* 在混合了行分区和列分区的已分区表上，必须仔细确定更新的目标。 筛选它们以便仅命中行分区。
   * 如果操作针对特定行分区（例如 `UPDATE p2 SET i = i + 1`），则会成功；如果针对指定列分区（例如 `UPDATE p1 SET i = i + 1`），则会失败。
   * 如果操作针对已分区表，并且具有排除所有列分区的 WHERE 子句（例如 `UPDATE parent SET i = i + 1 WHERE timestamp = '2020-03-15'`），则会成功。
   * 如果操作针对已分区表，但不对分区键列进行筛选，则会失败。 即使具有只与列分区中的行匹配的 WHERE 子句，这也不够 -- 还必须筛选分区键。

## <a name="limitations"></a>限制

此功能仍有一些显著限制。 请参阅[超大规模 (Citus) 限制和局限性](concepts-hyperscale-limits.md#columnar-storage)。

## <a name="next-steps"></a>后续步骤

* 请参阅 Citus [时序教程](https://docs.citusdata.com/en/stable/use_cases/timeseries.html#archiving-with-columnar-storage)（外部链接）中的列式存储示例。
