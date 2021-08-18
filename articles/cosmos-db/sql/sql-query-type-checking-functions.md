---
title: Azure Cosmos DB 查询语言中的类型检查函数
description: 了解 Azure Cosmos DB 中的类型检查 SQL 系统函数。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/26/2021
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 61dd347254a6e691250172cd428d8ef88f13e800
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206294"
---
# <a name="type-checking-functions-azure-cosmos-db"></a>类型检查函数 (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

使用类型检查函数可以检查 SQL 查询中表达式的类型。 当项中的属性可变或未知时，可以使用类型检查函数即时确定这些属性的类型。 

## <a name="functions"></a>函数

以下函数支持对输入值进行类型检查，并且每个都会返回一个布尔值。 “索引使用情况”列假设，在适用的情况下，你会用相等筛选器将类型检查函数与另一个值进行比较。

| 系统函数                           | 索引使用情况 | [使用标量聚合函数的查询中的索引使用情况](../index-overview.md#index-utilization-for-scalar-aggregate-functions) | 备注 |
| ----------------------------------------- | ----------- | ------------------------------------------------------------ | ------- |
| [IS_ARRAY](sql-query-is-array.md)         | 完全扫描   | 完全扫描                                                    |         |
| [IS_BOOL](sql-query-is-bool.md)           | 索引查找  | 索引查找                                                   |         |
| [IS_DEFINED](sql-query-is-defined.md)     | 索引查找  | 索引查找                                                   |         |
| [IS_NULL](sql-query-is-null.md)           | 索引查找  | 索引查找                                                   |         |
| [IS_NUMBER](sql-query-is-number.md)       | 索引查找  | 索引查找                                                   |         |
| [IS_OBJECT](sql-query-is-object.md)       | 完全扫描   | 完全扫描                                                    |         |
| [IS_PRIMITIVE](sql-query-is-primitive.md) | 索引查找  | 索引查找                                                   |         |
| [IS_STRING](sql-query-is-string.md)       | 索引查找  | 索引查找                                                   |         

## <a name="next-steps"></a>后续步骤

- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](../introduction.md)
- [用户定义的函数](sql-query-udfs.md)
- [聚合](sql-query-aggregate-functions.md)
