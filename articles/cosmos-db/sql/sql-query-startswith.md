---
title: Azure Cosmos DB 查询语言中的 StartsWith
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 STARTSWITH。
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d850ae0a01a93e2214d4dc26df98234f54e28a8b
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206315"
---
# <a name="startswith-azure-cosmos-db"></a>STARTSWITH (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 返回一个布尔值，指示第一个字符串表达式是否以第二个字符串表达式开头。  
  
## <a name="syntax"></a>语法
  
```sql
STARTSWITH(<str_expr1>, <str_expr2> [, <bool_expr>])  
```  
  
## <a name="arguments"></a>参数
  
*str_expr1*  
   是一个字符串表达式。
  
*str_expr2*  
   要与 str_expr1 的开头进行比较的字符串表达式。

bool_expr 忽略大小写的可选值。 如果设置为 true，则 STARTSWITH 将执行不区分大小写的搜索。 如果未指定，则此值为 false。

## <a name="return-types"></a>返回类型
  
  返回一个布尔表达式。  
  
## <a name="examples"></a>示例
  
以下示例检查了字符串“abc”是否以“b”和“A”开头。  
  
```sql
SELECT STARTSWITH("abc", "b", false) AS s1, STARTSWITH("abc", "A", false) AS s2, STARTSWITH("abc", "A", true) AS s3
```  
  
 下面是结果集：  
  
```json
[
    {
        "s1": false,
        "s2": false,
        "s3": true
    }
]
```  

## <a name="remarks"></a>备注

了解[此字符串系统函数使用索引的方式](sql-query-string-functions.md)。

## <a name="next-steps"></a>后续步骤

- [字符串函数 Azure Cosmos DB](sql-query-string-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](../introduction.md)
