---
title: Azure Cosmos DB 的 SQL 查询运算符
description: 了解 Azure Cosmos DB 支持的 SQL 运算符，例如相等、比较和逻辑运算符。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: tisande
ms.openlocfilehash: d7188bf10a31f3fbad207ec023a621f27c4659fb
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206123"
---
# <a name="operators-in-azure-cosmos-db"></a>Azure Cosmos DB 中的运算符
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

本文详细介绍 Azure Cosmos DB 支持的各种运算符。

## <a name="equality-and-comparison-operators"></a>相等和比较运算符

下表显示了 SQL API 中任意两个 JSON 类型之间等式比较的结果。

| **Op** | 未定义  | **Null** | **布尔值** | **数字** | **字符串** | **Object** | 数组  |
|---|---|---|---|---|---|---|---|
| 未定义  | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined |
| **Null** | Undefined | **正常** | Undefined | Undefined | Undefined | Undefined | Undefined |
| **布尔值** | Undefined | Undefined | **正常** | Undefined | Undefined | Undefined | Undefined |
| **数字** | Undefined | Undefined | Undefined | **正常** | Undefined | Undefined | Undefined |
| **字符串** | Undefined | Undefined | Undefined | Undefined | **正常** | Undefined | Undefined |
| **Object** | Undefined | Undefined | Undefined | Undefined | Undefined | **正常** | Undefined |
| 数组  | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined | **正常** |

对于 `>`、`>=`、`!=`、`<` 和 `<=` 等比较运算符，跨类型的比较或者两个对象或数组之间的比较会生成 `Undefined`。  

如果标量表达式的结果为 `Undefined`，则不会将该项包含在结果中，因为 `Undefined` 不等于 `true`。

例如，以下查询在数字和字符串值之间进行的比较生成 `Undefined`。 因此，筛选器不包含任何结果。

```sql
SELECT *
FROM c
WHERE 7 = 'a'
```

## <a name="logical-and-or-and-not-operators"></a>逻辑（AND、OR 和 NOT）运算符

逻辑运算符对布尔值进行运算。 下表显示了这些运算符的逻辑真值表：

**OR 运算符**

在任一条件为 `true` 时返回 `true`。

|  | **True** | **False** | **Undefined** |
| --- | --- | --- | --- |
| **True** |True |True |True |
| **False** |True |错误 |Undefined |
| **未定义** |True |未定义 |未定义 |

**AND 运算符**

在两个表达式均为 `true` 时返回 `true`。

|  | **True** | **False** | **Undefined** |
| --- | --- | --- | --- |
| **True** |True |错误 |Undefined |
| **False** |False |False |False |
| **未定义** |Undefined |错误 |Undefined |

**NOT 运算符**

反转任何布尔表达式的值。

|  | **NOT** |
| --- | --- |
| **True** |错误 |
| **False** |True |
| **未定义** |Undefined |

**运算符优先级**

逻辑运算符 `OR`、`AND` 和 `NOT` 的优先级如下所示：

| **“运算符”** | **Priority** |
| --- | --- |
| **NOT** |1 |
| **AND** |2 |
| **OR** |3 |

## <a name="-operator"></a>* 运算符

特殊运算符 * 按原样投影整个项。 在使用时，它必须仅为投影的字段。 类似于 `SELECT * FROM Families f` 的查询是有效的，而 `SELECT VALUE * FROM Families f` 和 `SELECT *, f.id FROM Families f` 是无效的。

## <a name="-and--operators"></a>? 和 ?? 运算符

如同在 C# 和 JavaScript 等编程语言中那样，可以使用三元 (?) 和联合 (??) 运算符来生成条件表达式。

可以使用 ? 运算符即时构造新的 JSON 属性。 例如，以下查询将年级分类为 `elementary` 或 `other`：

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

还可以将调用嵌套到 ? 运算符，如以下查询中所示： 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

与其他查询运算符一样， 如果引用的属性缺失或者要比较的类型不同，则 ? 运算符将会排除项。

查询半结构化 或混合类型的数据时，可以使用 ?? 运算符有效地检查项中的属性。 例如，以下查询返回 `lastName`（如果存在）或 `surname`（如果 `lastName` 不存在）。

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a name="next-steps"></a>后续步骤

- [Azure Cosmos DB .NET 示例](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [关键字](sql-query-keywords.md)
- [SELECT 子句](sql-query-select.md)
