---
title: 使用 Azure Cosmos DB 中的 JSON
description: 了解如何查询和访问嵌套的 JSON 属性并在 Azure Cosmos DB 中使用特殊字符
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/19/2020
ms.author: tisande
ms.openlocfilehash: 4bf732c8a22239cf37e3ab7737cec8765bd3ef19
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122770327"
---
# <a name="working-with-json-in-azure-cosmos-db"></a>使用 Azure Cosmos DB 中的 JSON
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

在 Azure Cosmos DB 的 SQL (Core) API 中，项存储为 JSON。 类型系统和表达式仅限于处理 JSON 类型。 有关详细信息，请参阅 [JSON 规范](https://www.json.org/)。

我们将总结一些使用 JSON 的重要方面：

- 始终使用大括号 `{` `}` 将 JSON 对象括起来
- 可以将 JSON 属性彼此[嵌套](#nested-properties)
- JSON 属性值可以是数组
- JSON 属性名称区分大小写
- JSON 属性名称可以是任何字符串值（包括空格或不是字母的字符）

## <a name="nested-properties"></a>嵌套属性

可以使用 Dot 访问器访问嵌套的 JSON。 可以在查询中使用嵌套的 JSON 属性，其方式与使用任何其他属性的方式相同。

下面是带有嵌套 JSON 的文档：

```JSON
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "address": {
      "state": "WA",
      "county": "King",
      "city": "Seattle"
      },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

在这种情况下，`state`、`country` 和 `city` 属性都嵌套在 `address` 属性中。

以下示例投影两个嵌套属性：`f.address.state` 和 `f.address.city`。

```sql
    SELECT f.address.state, f.address.city
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

结果有：

```json
    [{
      "state": "WA",
      "city": "Seattle"
    }]
```

## <a name="working-with-arrays"></a>使用数组

除嵌套属性外，JSON 还支持数组。

下面是一个包含数组的示例文档：

```json
{
  "id": "WakefieldFamily",
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
      },
      {
        "familyName": "Miller",
         "givenName": "Lisa",
         "gender": "female",
         "grade": 8
      }
  ],
}
```

使用数组时，可以通过引用数组中的特定元素的位置来访问该元素：

```sql
SELECT *
FROM Families f
WHERE f.children[0].givenName = "Jesse"
```

但在大多数情况下，在使用数组时，你将使用[子查询](sql-query-subquery.md)或[自联接](sql-query-join.md)。

例如，下面的文档显示客户银行帐户的每日余额。

```json
{
  "id": "Contoso-Checking-Account-2020",
  "balance": [
      {
        "checkingAccount": 1000,
        "savingsAccount": 5000
      },
      {
        "checkingAccount": 100,
        "savingsAccount": 5000
      },
      {
        "checkingAccount": -10,
        "savingsAccount": 5000
      },
      {
        "checkingAccount": 5000,
        "savingsAccount": 5000
      }
        
  ]
}
```

如果你想要运行一个查询，该查询显示在某个时间点所有余额为负数的客户，则可以将 [EXISTS](sql-query-subquery.md#exists-expression) 与一个子查询一起使用：

```sql
SELECT c.id
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.balance
    WHERE n.checkingAccount < 0
)
```

## <a name="difference-between-null-and-undefined"></a>Null 和未定义之间的区别

如果一个属性没有在项中定义，那么它的值为 `undefined`。 值为 `null` 的属性必须显式定义并分配 `null` 值。

例如，假设有以下示例项：

```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "address": {
      "state": "WA",
      "county": "King",
      "city": "Seattle"
      },
  "creationDate": null
}
```

在该示例中，属性 `isRegistered` 的值为 `undefined`，因为它在项中已省略。 属性 `creationDate` 具有 `null` 值。

Azure Cosmos DB 支持 `null` 和 `undefined` 属性的两个有用的类型检查系统函数：

* [IS_NULL](sql-query-is-null.md) - 检查属性值是否为 `null`
* [IS_DEFINED](sql-query-is-defined.md) - 检查是否已定义属性值

你可以了解[ 支持的运算符](sql-query-operators.md)及其对 `null` 和 `undefined` 值的行为。

## <a name="reserved-keywords-and-special-characters-in-json"></a>JSON 中的保留关键字和特殊字符

可以使用带引号的属性运算符 `[]` 访问属性。 例如，由于再也无法解析标识符“Families”，因此 `SELECT c.grade` and `SELECT c["grade"]` 是等效的。 此语法很适合用于转义包含空格和特殊字符的属性，或者其名称与 SQL 关键字或保留字相同的属性。

例如，下面是一个文档，具有一个名为 `order` 的属性和一个包含特殊字符的属性 `price($)`：

```json
{
  "id": "AndersenFamily",
  "order": {
         "orderId": "12345",
         "productId": "A17849",
         "price($)": 59.33
   },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

如果运行包含 `order` 属性或 `price($)` 属性的查询，则会收到语法错误。

```sql
SELECT * FROM c where c.order.orderid = "12345"
```

```sql
SELECT * FROM c where c.order.price($) > 50
```

结果为：

`
Syntax error, incorrect syntax near 'order'
`

应按如下所示重写相同的查询：

```sql
SELECT * FROM c WHERE c["order"].orderId = "12345"
```

```sql
SELECT * FROM c WHERE c["order"]["price($)"] > 50
```

## <a name="json-expressions"></a>JSON 表达式

投影也支持 JSON 表达式，如以下示例所示：

```sql
    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

结果有：

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle",
        "name": "AndersenFamily"
      }
    }]
```

在上述示例中，`SELECT` 子句需要创建一个 JSON 对象；由于该示例未提供键，因此子句使用了隐式参数变量名称 `$1`。 以下查询返回两个隐式参数变量：`$1` 和 `$2`。

```sql
    SELECT { "state": f.address.state, "city": f.address.city },
           { "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

结果有：

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle"
      },
      "$2": {
        "name": "AndersenFamily"
      }
    }]
```

## <a name="aliasing"></a>别名

可以显式为查询中的值指定别名。 如果查询包含两个同名的属性，请使用别名来重命名其中一个或两个属性，以便可以在投影的结果中消除其歧义。

### <a name="examples"></a>示例

如以下示例所示，将第二个值投影为 `NameInfo` 时，用于别名的 `AS` 关键字是可选的：

```sql
    SELECT
           { "state": f.address.state, "city": f.address.city } AS AddressInfo,
           { "name": f.id } NameInfo
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

结果有：

```json
    [{
      "AddressInfo": {
        "state": "WA",
        "city": "Seattle"
      },
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]
```

### <a name="aliasing-with-reserved-keywords-or-special-characters"></a>带有保留关键字或特殊字符的别名

不能使用别名将值投影为带有空格、特殊字符或保留字的属性名称。 例如，如果要将值的投影更改为带有空格的属性名称，则可以使用 [JSON 表达式](#json-expressions)。

下面是一个示例：

```sql
    SELECT
           {"JSON expression with a space": { "state": f.address.state, "city": f.address.city }},
           {"JSON expression with a special character!": { "name": f.id }}
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

## <a name="next-steps"></a>后续步骤

- [入门](sql-query-getting-started.md)
- [SELECT 子句](sql-query-select.md)
- [WHERE 子句](sql-query-where.md)
