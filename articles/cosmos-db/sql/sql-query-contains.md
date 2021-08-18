---
title: Azure Cosmos DB 查询语言中的 Contains
description: 了解 Azure Cosmos DB 中的 CONTAINS SQL 系统函数如何返回一个布尔值，指示第一个字符串表达式是否包含第二个字符串表达式
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 4ae5f786fe75d96bd0dc1aa0afab7c5a8965c826
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206275"
---
# <a name="contains-azure-cosmos-db"></a>CONTAINS (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

返回一个布尔值，该值指示第一个字符串表达式是否包含第二个字符串表达式。  
  
## <a name="syntax"></a>语法
  
```sql
CONTAINS(<str_expr1>, <str_expr2> [, <bool_expr>])  
```  
  
## <a name="arguments"></a>参数
  
*str_expr1*  
   是要搜索的字符串表达式。  
  
*str_expr2*  
   是要查找的字符串表达式。  

bool_expr 忽略大小写的可选值。 如果设置为 true，CONTAINS 将执行不区分大小写的搜索。 如果未指定，则此值为 false。
  
## <a name="return-types"></a>返回类型
  
  返回一个布尔表达式。  
  
## <a name="examples"></a>示例
  
  下面的示例检查“abc”是否包含“ab”，以及“abc”是否包含“A”。  
  
```sql
SELECT CONTAINS("abc", "ab", false) AS c1, CONTAINS("abc", "A", false) AS c2, CONTAINS("abc", "A", true) AS c3
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
