---
title: Azure Cosmos DB 中的 SELECT 子句
description: 了解 Azure Cosmos DB 的 SQL SELECT 子句。 将 SQL 用作 Azure Cosmos DB JSON 查询语言。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: tisande
ms.openlocfilehash: 8fd6174d13f45deac2eaddb6c98441fad5ce5e4d
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206153"
---
# <a name="select-clause-in-azure-cosmos-db"></a>Azure Cosmos DB 中的 SELECT 子句
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

每个查询按 ANSI SQL 标准由 `SELECT` 子句和可选的 [FROM](sql-query-from.md) 和 [WHERE](sql-query-where.md) 子句组成。 通常，将枚举 `FROM` 子句中的源，`WHERE` 子句对该源应用一个筛选器，以检索 JSON 项的子集。 然后，`SELECT` 子句在 select 列表中投影请求的 JSON 值。

## <a name="syntax"></a>语法

```sql
SELECT <select_specification>  

<select_specification> ::=
      '*'
      | [DISTINCT] <object_property_list>
      | [DISTINCT] VALUE <scalar_expression> [[ AS ] value_alias]  
  
<object_property_list> ::=
{ <scalar_expression> [ [ AS ] property_alias ] } [ ,...n ]  
```  
  
## <a name="arguments"></a>参数
  
- `<select_specification>`  

  要为结果集选择的属性或值。  
  
- `'*'`  

  指定应当在不进行任何更改的情况下检索值。 指定如果处理的值是一个对象，则将检索所有属性。  
  
- `<object_property_list>`  
  
  指定要检索的属性的列表。 每个返回值都是具有指定属性的对象。  
  
- `VALUE`  

  指定应当检索 JSON 值而非整个 JSON 对象。 不同于 `<property_list>`，这不会将投影的值包装在对象中。  

- `DISTINCT`
  
  指定应删除投影属性的重复项。  

- `<scalar_expression>`  

  表示待计算值的表达式。 有关详细信息，请参阅[标量表达式](sql-query-scalar-expressions.md)部分。  

## <a name="remarks"></a>备注

只有 FROM 子句仅声明了一个别名时，`SELECT *` 语法才有效。 `SELECT *` 提供了标识投影，在不需要投影时可能十分有用。 只有当指定了 FROM 子句并且仅引入了单个输入源时，SELECT * 才有效。  
  
`SELECT <select_list>` 和 `SELECT *` 是“语法糖”，可另外使用简单的 SELECT 语句表示，如下所示。  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   等效于：  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   等效于：  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
## <a name="examples"></a>示例

以下 SELECT 查询示例从 `id` 匹配 `AndersenFamily` 的 `Families` 中返回 `address`：

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

结果有：

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

## <a name="next-steps"></a>后续步骤

- [入门](sql-query-getting-started.md)
- [Azure Cosmos DB .NET 示例](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [WHERE 子句](sql-query-where.md)
