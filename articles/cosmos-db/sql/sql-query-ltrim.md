---
title: Azure Cosmos DB 查询语言中的 LTRIM
description: 了解 Azure Cosmos DB 中的 LTRIM SQL 系统函数，以便返回删除前导空格后的字符串表达式。
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/14/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 7dca2a6354c19031c2b55b7a61d69273a06c6f49
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128661253"
---
# <a name="ltrim-azure-cosmos-db"></a>LTRIM (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 返回在删除前导空格或指定字符后的字符串表达式。   
  
## <a name="syntax"></a>语法
  
```sql
LTRIM(<str_expr1>[, <str_expr2>])  
```  
  
## <a name="arguments"></a>参数
  
*str_expr1*  
   一个字符串表达式

*str_expr2*  
   要从 str_expr1 中剪裁的可选字符串表达式。 如果未设置，则默认为空格。
  
## <a name="return-types"></a>返回类型
  
  返回一个字符串表达式。  
  
## <a name="examples"></a>示例
  
  以下示例演示如何在查询中使用 `LTRIM`。  
  
```sql
SELECT LTRIM("   abc") AS t1, 
LTRIM("   abc   ") AS t2, 
LTRIM("abc   ") AS t3, 
LTRIM("abc") AS t4,
LTRIM("abc", "ab") AS t5,
LTRIM("abc", "abc") AS t6
```  
  
 下面是结果集。  
  
```json
[
    {
        "t1": "abc",
        "t2": "abc   ",
        "t3": "abc   ",
        "t4": "abc",
        "t5": "c",
        "t6": ""
    }
]
``` 

## <a name="remarks"></a>备注

此系统函数不会使用索引。

## <a name="next-steps"></a>后续步骤

- [字符串函数 Azure Cosmos DB](sql-query-string-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](../introduction.md)
