---
title: Azure Cosmos DB 查询语言中的字符串函数
description: 了解 Azure Cosmos DB 中的字符串 SQL 系统函数。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/26/2021
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: c3e9948fa6dd32198cd2584589a251b0c4399817
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206186"
---
# <a name="string-functions-azure-cosmos-db"></a>字符串函数 (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

使用字符串函数可以对 Azure Cosmos DB 中的字符串执行操作。

## <a name="functions"></a>函数

下面的标量函数对字符串输入值执行操作，并返回字符串、数值或布尔值。 “索引使用情况”列假设，你在用相等筛选器将字符串系统函数与另一个值进行比较（如果适用）。

| 系统函数                                 | 索引使用情况        | [使用标量聚合函数的查询中的索引使用情况](../index-overview.md#index-utilization-for-scalar-aggregate-functions) | 备注                                                      |
| ----------------------------------------------- | ------------------ | ------------------------------------------------------ | ------------------------------------------------------------ |
| [CONCAT](sql-query-concat.md)                   | 完全扫描          | 完全扫描                                              |                                                              |
| [CONTAINS](sql-query-contains.md)               | 完全索引扫描    | 完全扫描                                              |                                                              |
| [ENDSWITH](sql-query-endswith.md)               | 完全索引扫描    | 完全扫描                                              |                                                              |
| [INDEX_OF](sql-query-index-of.md)               | 完全扫描          | 完全扫描                                              |                                                              |
| [LEFT](sql-query-left.md)                       | 精确索引扫描 | 精确索引扫描                                     |                                                              |
| [LENGTH](sql-query-length.md)                   | 完全扫描          | 完全扫描                                              |                                                              |
| [LOWER](sql-query-lower.md)                     | 完全扫描          | 完全扫描                                              |                                                              |
| [LTRIM](sql-query-ltrim.md)                     | 完全扫描          | 完全扫描                                              |                                                              |
| [REGEXMATCH](sql-query-regexmatch.md)           | 完全索引扫描    | 完全扫描                                              |                                                              |
| [REPLACE](sql-query-replace.md)                 | 完全扫描          | 完全扫描                                              |                                                              |
| [REPLICATE](sql-query-replicate.md)             | 完全扫描          | 完全扫描                                              |                                                              |
| [REVERSE](sql-query-reverse.md)                 | 完全扫描          | 完全扫描                                              |                                                              |
| [RIGHT](sql-query-right.md)                     | 完全扫描          | 完全扫描                                              |                                                              |
| [RTRIM](sql-query-rtrim.md)                     | 完全扫描          | 完全扫描                                              |                                                              |
| [STARTSWITH](sql-query-startswith.md)           | 精确索引扫描 | 精确索引扫描                                     | 如果“不区分大小写”选项为 true，将进行扩展索引扫描。 |
| [STRINGEQUALS](sql-query-stringequals.md)       | 索引查找         | 索引查找                                             | 如果“不区分大小写”选项为 true，将进行扩展索引扫描。 |
| [StringToArray](sql-query-stringtoarray.md)     | 完全扫描          | 完全扫描                                              |                                                              |
| [StringToBoolean](sql-query-stringtoboolean.md) | 完全扫描          | 完全扫描                                              |                                                              |
| [StringToNull](sql-query-stringtonull.md)       | 完全扫描          | 完全扫描                                              |                                                              |
| [StringToNumber](sql-query-stringtonumber.md)   | 完全扫描          | 完全扫描                                              |                                                              |

了解 Azure Cosmos DB 中的[索引使用情况](../index-overview.md#index-usage)。

## <a name="next-steps"></a>后续步骤

- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](../introduction.md)
- [用户定义的函数](sql-query-udfs.md)
- [聚合](sql-query-aggregate-functions.md)
