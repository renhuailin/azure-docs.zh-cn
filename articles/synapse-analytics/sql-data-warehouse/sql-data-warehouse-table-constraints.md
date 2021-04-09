---
title: 主键、外键和唯一键
description: 在 Azure Synapse Analytics 中使用专用 SQL 池时的表约束支持
services: synapse-analytics
author: mstehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 09/05/2019
ms.author: emtehran
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 88b63ce30000340a70811e9f623e4273ccbb272a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98117276"
---
# <a name="primary-key-foreign-key-and-unique-key-using-dedicated-sql-pool-in-azure-synapse-analytics"></a>在 Azure Synapse Analytics 中使用专用 SQL 池的主键、外键和唯一键

了解专用 SQL 池中的表约束，包括主键、外键和唯一键。

## <a name="table-constraints"></a>表约束

专用 SQL 池支持以下表约束： 
- 仅当同时使用 NONCLUSTERED 和 NOT ENFORCED 时才支持 PRIMARY KEY。    
- 仅在使用 NOT ENFORCED 时才支持 UNIQUE 约束。

有关语法，请查看 [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql) 和 [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse)。 

专用 SQL 池不支持外键约束。  


## <a name="remarks"></a>备注

有了主键和/或唯一键，专用 SQL 池引擎就可以为查询生成最佳执行计划。  主键列或唯一约束列中的所有值都应独一无二。

在专用 SQL 池中创建具有主键或唯一约束的表以后，用户需确保这些列中的所有值都是独一无二的。  违反这一点可能会导致查询返回不准确的结果。  此示例表明，如果主键或唯一约束列包含重复值，查询可能返回不准确的结果。  

```sql
 -- Create table t1
CREATE TABLE t1 (a1 INT NOT NULL, b1 INT) WITH (DISTRIBUTION = ROUND_ROBIN)

-- Insert values to table t1 with duplicate values in column a1.
INSERT INTO t1 VALUES (1, 100)
INSERT INTO t1 VALUES (1, 1000)
INSERT INTO t1 VALUES (2, 200)
INSERT INTO t1 VALUES (3, 300)
INSERT INTO t1 VALUES (4, 400)

-- Run this query.  No primary key or unique constraint.  4 rows returned. Correct result.
SELECT a1, COUNT(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
1           2
2           1
3           1
4           1

(4 rows affected)
*/

-- Add unique constraint
ALTER TABLE t1 ADD CONSTRAINT unique_t1_a1 unique (a1) NOT ENFORCED

-- Re-run this query.  5 rows returned.  Incorrect result.
SELECT a1, count(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
4           1
1           1
3           1
1           1

(5 rows affected)
*/

-- Drop unique constraint.
ALTER TABLE t1 DROP CONSTRAINT unique_t1_a1

-- Add primary key constraint
ALTER TABLE t1 add CONSTRAINT PK_t1_a1 PRIMARY KEY NONCLUSTERED (a1) NOT ENFORCED

-- Re-run this query.  5 rows returned.  Incorrect result.
SELECT a1, COUNT(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
4           1
1           1
3           1
1           1

(5 rows affected)
*/

-- Manually fix the duplicate values in a1
UPDATE t1 SET a1 = 0 WHERE b1 = 1000

-- Verify no duplicate values in column a1 
SELECT * FROM t1

/*
a1          b1
----------- -----------
2           200
3           300
4           400
0           1000
1           100

(5 rows affected)
*/

-- Add unique constraint
ALTER TABLE t1 add CONSTRAINT unique_t1_a1 UNIQUE (a1) NOT ENFORCED  

-- Re-run this query.  5 rows returned.  Correct result.
SELECT a1, COUNT(*) as total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
3           1
4           1
0           1
1           1

(5 rows affected)
*/

-- Drop unique constraint.
ALTER TABLE t1 DROP CONSTRAINT unique_t1_a1

-- Add primary key contraint
ALTER TABLE t1 ADD CONSTRAINT PK_t1_a1 PRIMARY KEY NONCLUSTERED (a1) NOT ENFORCED

-- Re-run this query.  5 rows returned.  Correct result.
SELECT a1, COUNT(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
3           1
4           1
0           1
1           1

(5 rows affected)
*/

```

## <a name="examples"></a>示例

创建具有主键的专用 SQL 池表： 

```sql 
CREATE TABLE mytable (c1 INT PRIMARY KEY NONCLUSTERED NOT ENFORCED, c2 INT);
```

创建具有唯一约束的专用 SQL 池表：

```sql
CREATE TABLE t6 (c1 INT UNIQUE NOT ENFORCED, c2 INT);
```

## <a name="next-steps"></a>后续步骤

为专用 SQL 池创建表后，下一步是将数据加载到该表中。 有关加载的教程，请参阅[将数据加载到专用 SQL 池](load-data-wideworldimportersdw.md)。