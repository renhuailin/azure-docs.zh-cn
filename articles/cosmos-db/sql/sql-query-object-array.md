---
title: 在 Azure Cosmos DB 中使用数组和对象
description: 了解用于在 Azure Cosmos DB 中创建数组和对象的 SQL 语法。 本文还提供了一些对数组对象执行操作的示例
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/02/2021
ms.author: tisande
ms.openlocfilehash: ca62bbb86134e3553c9ebe822096419b8051a872
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206125"
---
# <a name="working-with-arrays-and-objects-in-azure-cosmos-db"></a>在 Azure Cosmos DB 中使用数组和对象
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Azure Cosmos DB SQL API 的一个重要功能是创建数组和对象。 本文档使用可通过[家庭数据集](sql-query-getting-started.md#upload-sample-data)重新创建的示例。

下面是此数据集中的一个示例项：

```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "parents": [
     { "firstName": "Thomas" },
     { "firstName": "Mary Kay"}
  ],
  "children": [
     {
         "firstName": "Henriette Thaulow",
         "gender": "female",
         "grade": 5,
         "pets": [{ "givenName": "Fluffy" }]
     }
  ],
  "address": { "state": "WA", "county": "King", "city": "Seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

## <a name="arrays"></a>数组

可以构造数组，如以下示例所示：

```sql
SELECT [f.address.city, f.address.state] AS CityState
FROM Families f
```

结果有：

```json
[
  {
    "CityState": [
      "Seattle",
      "WA"
    ]
  },
  {
    "CityState": [
      "NY", 
      "NY"
    ]
  }
]
```

还可以使用 [ARRAY 表达式](sql-query-subquery.md#array-expression)根据[子查询](sql-query-subquery.md)的结果构造数组。 此查询获取数组中子项的所有不同给定名称。

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

其结果是：

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

## <a name="iteration"></a><a id="Iteration"></a>迭代

SQL API 支持循环访问 JSON 数组，其中 [IN 关键字](sql-query-keywords.md#in)在 FROM 源中。 如下示例中：

```sql
SELECT *
FROM Families.children
```

其结果是：

```json
[
  [
    {
      "firstName": "Henriette Thaulow",
      "gender": "female",
      "grade": 5,
      "pets": [{ "givenName": "Fluffy"}]
    }
  ], 
  [
    {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1
    }, 
    {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }
  ]
]
```

下一个查询循环访问 `Families` 容器中的 `children`。 输出的数组与前面的查询不同。 此示例拆分 `children` 并将结果平展为单个数组：  

```sql
SELECT *
FROM c IN Families.children
```

其结果是：

```json
[
  {
      "firstName": "Henriette Thaulow",
      "gender": "female",
      "grade": 5,
      "pets": [{ "givenName": "Fluffy" }]
  },
  {
      "familyName": "Merriam",
      "givenName": "Jesse",
      "gender": "female",
      "grade": 1
  },
  {
      "familyName": "Miller",
      "givenName": "Lisa",
      "gender": "female",
      "grade": 8
  }
]
```

可以进一步筛选该数组的每个条目，如以下示例所示：

```sql
SELECT c.givenName
FROM c IN Families.children
WHERE c.grade = 8
```

其结果是：

```json
[{
  "givenName": "Lisa"
}]
```

还可基于数组迭代的结果进行聚合。 例如，以下查询计数所有家庭中的孩子数目。

```sql
SELECT COUNT(1) AS Count
FROM child IN Families.children
```

其结果是：

```json
[
  {
    "Count": 3
  }
]
```

> [!NOTE]
> 使用 IN 关键字进行迭代时，不能筛选或投射数组外部的任何属性。 应改用 [JOIN](sql-query-join.md)。

有关其他示例，请阅读[有关在 Azure Cosmos DB 中使用数组的博客文章](https://devblogs.microsoft.com/cosmosdb/understanding-how-to-query-arrays-in-azure-cosmos-db/)。

## <a name="next-steps"></a>后续步骤

- [入门](sql-query-getting-started.md)
- [Azure Cosmos DB .NET 示例](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [联接](sql-query-join.md)
