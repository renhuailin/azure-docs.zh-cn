---
title: Azure Cosmos DB 的 SQL 关键字
description: 了解 Azure Cosmos DB 的 SQL 关键字。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: tisande
ms.openlocfilehash: 7468b544f36645609e1da344aef583c33157da7d
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206155"
---
# <a name="keywords-in-azure-cosmos-db"></a>Azure Cosmos DB 中的关键字
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

本文详细介绍可在 Azure Cosmos DB SQL 查询中使用的关键字。

## <a name="between"></a>BETWEEN

可以使用 `BETWEEN` 关键字来表达针对字符串或数值范围的查询。 例如，以下查询返回其中第一个孩子的年级为 1-5（含）的所有项。

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

还可以在 `SELECT` 子句中使用 `BETWEEN` 关键字，如以下示例所示。

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

与 ANSI SQL 不同，在 SQL API 中，可以针对混合类型的属性表达范围查询。 例如，在某些项中，`grade` 可能是类似于 `5` 的数字；而在其他一些项中，它可能是类似于 `grade4` 的字符串。 在这些情况下（与在 JavaScript 中一样），两个不同类型之间的比较会生成 `Undefined`，因此会跳过该项。

## <a name="distinct"></a>DISTINCT

`DISTINCT` 关键字可消除查询投影中的重复项。

在此示例中，查询将投影每个姓氏的值：

```sql
SELECT DISTINCT VALUE f.lastName
FROM Families f
```

结果有：

```json
[
    "Andersen"
]
```

也可以投影唯一对象。 在本例中，两个文档中的一个文档不存在 lastName 对象，因此查询将返回一个空对象。

```sql
SELECT DISTINCT f.lastName
FROM Families f
```

结果有：

```json
[
    {
        "lastName": "Andersen"
    },
    {}
]
```

还可以在子查询内的投影中使用 `DISTINCT`：

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

此查询投影包含每个孩子的 givenName 的数组，并删除了重复项。 此数组的别名为 ChildNames，并在外部查询中投影。

结果有：

```json
[
    {
        "id": "AndersenFamily",
        "ChildNames": []
    },
    {
        "id": "WakefieldFamily",
        "ChildNames": [
            "Jesse",
            "Lisa"
        ]
    }
]
```

不支持查询包含使用 `DISTINCT` 的聚合系统函数和子查询。 例如，不支持以下查询：

```sql
SELECT COUNT(1) FROM (SELECT DISTINCT f.lastName FROM f)
```

## <a name="like"></a>LIKE

根据特定字符串是否与指定的模式匹配，返回布尔值。 模式可以包含常规字符和通配符。 可以使用 `LIKE` 关键字或 [RegexMatch](sql-query-regexmatch.md) 系统函数编写在逻辑上等效的查询。 无论选择哪一种，索引利用率都将相同。 因此，如果与正则表达式相比，你更喜欢 `LIKE` 的语法，那么你应使用 LIKE。

> [!NOTE]
> 由于 `LIKE` 可以利用索引，因此你应为你要使用 `LIKE` 来比较的属性[创建范围索引](./../index-policy.md)。

可以将以下通配符与 LIKE 配合使用：

| 通配符 | 描述                                                  | 示例                                     |
| -------------------- | ------------------------------------------------------------ | ------------------------------------------- |
| %                    | 包含零个或多个字符的任意字符串                      | WHERE   c.description LIKE   “%SO%PS%”      |
| _（下划线）     | 任何单个字符                                       | WHERE   c.description LIKE   “%SO_PS%”      |
| [ ]                  | 在指定范围 ([a-f]) 或集合 ([abcdef]) 内的任何单个字符。 | WHERE   c.description LIKE   “%SO[t-z]PS%”  |
| [^]                  | 不属于指定范围 ([a-f]) 或集合 ([abcdef]) 的任何单个字符。 | WHERE   c.description LIKE   “%SO[^abc]PS%” |


### <a name="using-like-with-the--wildcard-character"></a>使用带 % 通配符的 LIKE

`%` 字符匹配包含零个或多个字符的任意字符串。 例如，通过在模式的开头和结尾放置 `%`，以下查询将返回带有包含 `fruit` 的说明的所有项：

```sql
SELECT *
FROM c
WHERE c.description LIKE "%fruit%"
```

如果只在模式结尾处使用 `%` 字符，则只会返回带有以 `fruit` 开头的说明的项：

```sql
SELECT *
FROM c
WHERE c.description LIKE "fruit%"
```


### <a name="using-not-like"></a>使用 NOT LIKE

下面的示例返回带有不包含 `fruit` 的说明的所有项：

```sql
SELECT *
FROM c
WHERE c.description NOT LIKE "%fruit%"
```

### <a name="using-the-escape-clause"></a>使用 ESCAPE 子句

可以使用 ESCAPE 子句搜索包含一个或多个通配符的模式。 例如，如果要搜索包含字符串 `20-30%` 的说明，则不需要将 `%` 解释为通配符。

```sql
SELECT *
FROM c
WHERE c.description LIKE '%20-30!%%' ESCAPE '!'
```

### <a name="using-wildcard-characters-as-literals"></a>将通配符作为文字使用

可以将通配符括在中括号内，以便将它们视为文本字符。 将通配符放在中括号内时，其所有特殊属性均会去除。 下面是一些示例：

| 模式           | 含义 |
| ----------------- | ------- |
| LIKE   “20-30[%]” | 20-30%  |
| LIKE   “[_]n”     | _n      |
| LIKE   “[ [ ]”    | [       |
| LIKE   “]”        | ]       |

## <a name="in"></a>IN

使用 IN 关键字可以检查指定的值是否与列表中的任一值匹配。 例如，以下查询返回 `id` 为 `WakefieldFamily` 或 `AndersenFamily` 的所有家庭项。

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

以下示例返回状态为任何指定值的所有项：

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

SQL API 支持[循环访问 JSON 数组](sql-query-object-array.md#Iteration)，它可以通过 FROM 源中的 IN 关键字添加一个新的构造。

如果在 `IN` 筛选器中包含分区键，则查询会自动地仅筛选出相关分区。

## <a name="top"></a>TOP

TOP 关键字以未定义的顺序返回前 `N` 个查询结果。 最佳做法是将 TOP 与 `ORDER BY` 子句配合使用，将结果限制为前 `N` 个有序值。 要预见性地指示哪些行受到 TOP 的影响，只能结合使用这两个子句。

可以结合一个常量值使用 TOP（如以下示例中所示），或者在参数化查询中结合一个变量值使用 TOP。

```sql
    SELECT TOP 1 *
    FROM Families f
```

结果有：

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

## <a name="next-steps"></a>后续步骤

- [入门](sql-query-getting-started.md)
- [联接](sql-query-join.md)
- [子查询](sql-query-subquery.md)
