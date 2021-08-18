---
title: Azure Cosmos DB 查询语言中的 StringEquals
description: 了解 Azure Cosmos DB 中的 StringEquals SQL 系统函数如何返回一个布尔值，指示第一个字符串表达式是否与第二个字符串表达式匹配
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: a1fbe64a1daa4773d478162c836f2386eea6f11e
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206299"
---
# <a name="stringequals-azure-cosmos-db"></a>STRINGEQUALS (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 返回一个布尔值，该值指示第一个字符串表达式是否与第二个字符串表达式匹配。  
  
## <a name="syntax"></a>语法
  
```sql
STRINGEQUALS(<str_expr1>, <str_expr2> [, <bool_expr>])  
```  
  
## <a name="arguments"></a>参数
  
str_expr1  
   要比较的第一个字符串表达式。  
  
str_expr2  
   要比较的第二个字符串表达式。  

bool_expr 忽略大小写的可选值。 如果设置为 true，则 StringEquals 将执行不区分大小写的搜索。 如果未指定，则此值为 false。
  
## <a name="return-types"></a>返回类型
  
  返回一个布尔表达式。  
  
## <a name="examples"></a>示例
  
  下面的示例检查“abc”是否与“abc”匹配，“abc”是否与“ABC”匹配。  
  
```sql
SELECT STRINGEQUALS("abc", "abc", false) AS c1, STRINGEQUALS("abc", "ABC", false) AS c2,  STRINGEQUALS("abc", "ABC", true) AS c3
```  
  
 下面是结果集：  
  
```json
[
    {
        "c1": true,
        "c2": false,
        "c3": true
    }
]
```  

## <a name="remarks"></a>备注

了解[此字符串系统函数使用索引的方式](sql-query-string-functions.md)。

## <a name="next-steps"></a>后续步骤

- [字符串函数 Azure Cosmos DB](sql-query-string-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](../introduction.md)
