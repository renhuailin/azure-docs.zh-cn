---
title: 列式表存储 - 超大规模 (Citus) - Azure Database for PostgreSQL
description: 使用列式存储压缩数据
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 08/03/2021
ms.openlocfilehash: 557fe7d2242b67f8fa53541aef25cfc44090e48b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121745161"
---
# <a name="columnar-table-storage"></a>列式表存储

对于分析和数据仓库工作负载，Azure Database for PostgreSQL - 超大规模 (Citus) 支持仅限追加的列式表存储。 当列（而不是行）在磁盘上连续存储时，数据的可压缩性会提高，并且查询可以更快地请求列的子集。

## <a name="usage"></a>使用情况

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

由于测量方式的不同，压缩率可能与表的行式与列式存储之间的大小差异匹配，也可能不匹配。 真正发现这种差异的唯一方法是构造包含相同数据的行式和列式表，并进行比较。

## <a name="measuring-compression"></a>测量压缩

使用更多数据新建示例，以对压缩节省情况进行基准检验。

```postgresql
-- first a wide table using row storage
CREATE TABLE perf_row(
  c00 int8, c01 int8, c02 int8, c03 int8, c04 int8, c05 int8, c06 int8, c07 int8, c08 int8, c09 int8,
  c10 int8, c11 int8, c12 int8, c13 int8, c14 int8, c15 int8, c16 int8, c17 int8, c18 int8, c19 int8,
  c20 int8, c21 int8, c22 int8, c23 int8, c24 int8, c25 int8, c26 int8, c27 int8, c28 int8, c29 int8,
  c30 int8, c31 int8, c32 int8, c33 int8, c34 int8, c35 int8, c36 int8, c37 int8, c38 int8, c39 int8,
  c40 int8, c41 int8, c42 int8, c43 int8, c44 int8, c45 int8, c46 int8, c47 int8, c48 int8, c49 int8,
  c50 int8, c51 int8, c52 int8, c53 int8, c54 int8, c55 int8, c56 int8, c57 int8, c58 int8, c59 int8,
  c60 int8, c61 int8, c62 int8, c63 int8, c64 int8, c65 int8, c66 int8, c67 int8, c68 int8, c69 int8,
  c70 int8, c71 int8, c72 int8, c73 int8, c74 int8, c75 int8, c76 int8, c77 int8, c78 int8, c79 int8,
  c80 int8, c81 int8, c82 int8, c83 int8, c84 int8, c85 int8, c86 int8, c87 int8, c88 int8, c89 int8,
  c90 int8, c91 int8, c92 int8, c93 int8, c94 int8, c95 int8, c96 int8, c97 int8, c98 int8, c99 int8
);

-- next a table with identical columns using columnar storage
CREATE TABLE perf_columnar(LIKE perf_row) USING COLUMNAR;
```

使用同一大型数据集填充两个表：

```postgresql
INSERT INTO perf_row
  SELECT
    g % 00500, g % 01000, g % 01500, g % 02000, g % 02500, g % 03000, g % 03500, g % 04000, g % 04500, g % 05000,
    g % 05500, g % 06000, g % 06500, g % 07000, g % 07500, g % 08000, g % 08500, g % 09000, g % 09500, g % 10000,
    g % 10500, g % 11000, g % 11500, g % 12000, g % 12500, g % 13000, g % 13500, g % 14000, g % 14500, g % 15000,
    g % 15500, g % 16000, g % 16500, g % 17000, g % 17500, g % 18000, g % 18500, g % 19000, g % 19500, g % 20000,
    g % 20500, g % 21000, g % 21500, g % 22000, g % 22500, g % 23000, g % 23500, g % 24000, g % 24500, g % 25000,
    g % 25500, g % 26000, g % 26500, g % 27000, g % 27500, g % 28000, g % 28500, g % 29000, g % 29500, g % 30000,
    g % 30500, g % 31000, g % 31500, g % 32000, g % 32500, g % 33000, g % 33500, g % 34000, g % 34500, g % 35000,
    g % 35500, g % 36000, g % 36500, g % 37000, g % 37500, g % 38000, g % 38500, g % 39000, g % 39500, g % 40000,
    g % 40500, g % 41000, g % 41500, g % 42000, g % 42500, g % 43000, g % 43500, g % 44000, g % 44500, g % 45000,
    g % 45500, g % 46000, g % 46500, g % 47000, g % 47500, g % 48000, g % 48500, g % 49000, g % 49500, g % 50000
  FROM generate_series(1,50000000) g;

INSERT INTO perf_columnar
  SELECT
    g % 00500, g % 01000, g % 01500, g % 02000, g % 02500, g % 03000, g % 03500, g % 04000, g % 04500, g % 05000,
    g % 05500, g % 06000, g % 06500, g % 07000, g % 07500, g % 08000, g % 08500, g % 09000, g % 09500, g % 10000,
    g % 10500, g % 11000, g % 11500, g % 12000, g % 12500, g % 13000, g % 13500, g % 14000, g % 14500, g % 15000,
    g % 15500, g % 16000, g % 16500, g % 17000, g % 17500, g % 18000, g % 18500, g % 19000, g % 19500, g % 20000,
    g % 20500, g % 21000, g % 21500, g % 22000, g % 22500, g % 23000, g % 23500, g % 24000, g % 24500, g % 25000,
    g % 25500, g % 26000, g % 26500, g % 27000, g % 27500, g % 28000, g % 28500, g % 29000, g % 29500, g % 30000,
    g % 30500, g % 31000, g % 31500, g % 32000, g % 32500, g % 33000, g % 33500, g % 34000, g % 34500, g % 35000,
    g % 35500, g % 36000, g % 36500, g % 37000, g % 37500, g % 38000, g % 38500, g % 39000, g % 39500, g % 40000,
    g % 40500, g % 41000, g % 41500, g % 42000, g % 42500, g % 43000, g % 43500, g % 44000, g % 44500, g % 45000,
    g % 45500, g % 46000, g % 46500, g % 47000, g % 47500, g % 48000, g % 48500, g % 49000, g % 49500, g % 50000
  FROM generate_series(1,50000000) g;

VACUUM (FREEZE, ANALYZE) perf_row;
VACUUM (FREEZE, ANALYZE) perf_columnar;
```

对于这些数据，可以在列式表中看到压缩率超过 8 倍。

```postgresql
SELECT pg_total_relation_size('perf_row')::numeric/
       pg_total_relation_size('perf_columnar') AS compression_ratio;
 compression_ratio
--------------------
 8.0196135873627944
(1 row)
```

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
