---
title: Azure Cosmos DB 查询语言中的数学函数
description: 了解 Azure Cosmos DB 中的数学函数，以便基于作为参数提供的输入值执行计算，并返回数值。
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 06/22/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9e3114220b6250afb9d633b073342ba6f2cf92b8
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206241"
---
# <a name="mathematical-functions-azure-cosmos-db"></a>数学函数 (Azure Cosmos DB)  
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

每个数学函数均执行一个计算，基于作为参数提供的输出值，并返回数值。

可以运行以下示例所示的查询：

```sql
    SELECT VALUE ABS(-4)
```

结果为：

```json
    [4]
```

## <a name="functions"></a>函数

以下支持的内置数学函数通常基于输入参数执行计算，并返回数值表达式。 “索引使用情况”列假设，你在用相等筛选器将数学系统函数与另一个值进行比较（如果适用）。
 
| 系统函数                 | 索引使用情况 | [使用标量聚合函数的查询中的索引使用情况](../index-overview.md#index-utilization-for-scalar-aggregate-functions) | 备注                                                      |
| ------------------------------- | ----------- | ------------------------------------------------------ | ------------------------------------------------------------ |
| [ABS](sql-query-abs.md)         | 索引查找  | 索引查找                                             |                                                              |
| [ACOS](sql-query-acos.md)       | 完全扫描   | 完全扫描                                              |                                                              |
| [ASIN](sql-query-asin.md)       | 完全扫描   | 完全扫描                                              |                                                              |
| [ATAN](sql-query-atan.md)       | 完全扫描   | 完全扫描                                              |                                                              |
| [ATN2](sql-query-atn2.md)       | 完全扫描   | 完全扫描                                              |                                                              |
| [CEILING](sql-query-ceiling.md) | 索引查找  | 索引查找                                             |                                                              |
| [COS](sql-query-cos.md)         | 完全扫描   | 完全扫描                                              |                                                              |
| [COT](sql-query-cot.md)         | 完全扫描   | 完全扫描                                              |                                                              |
| [DEGREES](sql-query-degrees.md) | 索引查找  | 索引查找                                             |                                                              |
| [EXP](sql-query-exp.md)         | 完全扫描   | 完全扫描                                              |                                                              |
| [FLOOR](sql-query-floor.md)     | 索引查找  | 索引查找                                             |                                                              |
| [LOG](sql-query-log.md)         | 完全扫描   | 完全扫描                                              |                                                              |
| [LOG10](sql-query-log10.md)     | 完全扫描   | 完全扫描                                              |                                                              |
| [PI](sql-query-pi.md)           | 空值         | 空值                                                    | PI () 返回一个常数值。 因为结果是确定的，所以与 PI() 的比较可以使用索引。 |
| [POWER](sql-query-power.md)     | 完全扫描   | 完全扫描                                              |                                                              |
| [RADIANS](sql-query-radians.md) | 索引查找  | 索引查找                                             |                                                              |
| [RAND](sql-query-rand.md)       | 空值         | 空值                                                    | Rand() 返回一个随机数。 因为结果是不确定的，所以涉及 Rand() 的比较不能使用索引。 |
| [ROUND](sql-query-round.md)     | 索引查找  | 索引查找                                             |                                                              |
| [SIGN](sql-query-sign.md)       | 索引查找  | 索引查找                                             |                                                              |
| [SIN](sql-query-sin.md)         | 完全扫描   | 完全扫描                                              |                                                              |
| [SQRT](sql-query-sqrt.md)       | 完全扫描   | 完全扫描                                              |                                                              |
| [SQUARE](sql-query-square.md)   | 完全扫描   | 完全扫描                                              |                                                              |
| [TAN](sql-query-tan.md)         | 完全扫描   | 完全扫描                                              |                                                              |
| [TRUNC](sql-query-trunc.md)     | 索引查找  | 索引查找                                              |                                                              |
## <a name="next-steps"></a>后续步骤

- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](../introduction.md)
- [用户定义的函数](sql-query-udfs.md)
- [聚合](sql-query-aggregate-functions.md)
