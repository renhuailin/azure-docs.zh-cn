---
title: Azure Cosmos DB 查询语言中的 IS_STRING
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 IS_STRING。
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 52b1e0002c5af193df964127ca2170f31c6a42db
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206269"
---
# <a name="is_string-azure-cosmos-db"></a>IS_STRING (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 返回一个布尔值，指示指定表达式的类型是否为字符串。  
  
## <a name="syntax"></a>语法
  
```sql
IS_STRING(<expr>)  
```  
  
## <a name="arguments"></a>参数
  
*expr*  
   是任何表达式。  
  
## <a name="return-types"></a>返回类型
  
  返回一个布尔表达式。  
  
## <a name="examples"></a>示例
  
  以下示例使用 `IS_STRING` 函数检查 JSON 布尔、数字、字符串、null、对象、数组和 undefined 类型的对象。  
  
```sql
SELECT   
       IS_STRING(true) AS isStr1,   
       IS_STRING(1) AS isStr2,  
       IS_STRING("value") AS isStr3,   
       IS_STRING(null) AS isStr4,  
       IS_STRING({prop: "value"}) AS isStr5,   
       IS_STRING([1, 2, 3]) AS isStr6,  
       IS_STRING({prop: "value"}.prop2) AS isStr7  
```  
  
 下面是结果集。  
  
```json
[{"isStr1":false,"isStr2":false,"isStr3":true,"isStr4":false,"isStr5":false,"isStr6":false,"isStr7":false}] 
```  

## <a name="remarks"></a>备注

此系统函数将从[范围索引](../index-policy.md#includeexclude-strategy)中获益。

## <a name="next-steps"></a>后续步骤

- [类型检查函数 Azure Cosmos DB](sql-query-type-checking-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](../introduction.md)
