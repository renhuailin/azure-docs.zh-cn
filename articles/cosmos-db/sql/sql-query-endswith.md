---
title: Azure Cosmos DB 查询语言中的 EndsWith
description: 了解 Azure Cosmos DB 中的 ENDSWITH SQL 系统函数如何返回一个布尔值，指示第一个字符串表达式是否以第二个字符串表达式结尾
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6e7d67757bd1cdf8b82e15a4c925d2716d414442
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206198"
---
# <a name="endswith-azure-cosmos-db"></a>ENDSWITH (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

返回一个布尔值，指示第一个字符串表达式是否以第二个字符串表达式结尾。  
  
## <a name="syntax"></a>语法
  
```sql
ENDSWITH(<str_expr1>, <str_expr2> [, <bool_expr>])
```  
  
## <a name="arguments"></a>参数
  
*str_expr1*  
   是一个字符串表达式。  
  
*str_expr2*  
   要与 str_expr1 的结尾进行比较的字符串表达式。

*bool_expr* 忽略大小写的可选值。 如果设置为 true，则 ENDSWITH 将执行不区分大小写的搜索。 如果未指定，则此值为 false。
  
## <a name="return-types"></a>返回类型
  
  返回一个布尔表达式。  
  
## <a name="examples"></a>示例
  
以下示例检查字符串“abc”是否以“b”和“bC”结尾。  
  
```sql
SELECT ENDSWITH("abc", "b", false) AS e1, ENDSWITH("abc", "bC", false) AS e2, ENDSWITH("abc", "bC", true) AS e3
```  
  
 下面是结果集：  
  
```json
[
    {
        "e1": false,
        "e2": false,
        "e3": true
    }
]
```  

## <a name="remarks"></a>备注

了解[此字符串系统函数使用索引的方式](sql-query-string-functions.md)。

## <a name="next-steps"></a>后续步骤

- [字符串函数 Azure Cosmos DB](sql-query-string-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](../introduction.md)
