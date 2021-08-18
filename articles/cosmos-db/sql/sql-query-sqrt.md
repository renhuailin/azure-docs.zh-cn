---
title: Azure Cosmos DB 查询语言中的 SQRT
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 SQRT。
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 2d8db5874a9e421cf6971a1603f1e90583fd7e60
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206265"
---
# <a name="sqrt-azure-cosmos-db"></a>SQRT (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 返回指定数值的平方根。  
  
## <a name="syntax"></a>语法
  
```sql
SQRT(<numeric_expr>)  
```  
  
## <a name="arguments"></a>参数
  
*numeric_expr*  
   是一个数值表达式。  
  
## <a name="return-types"></a>返回类型
  
  返回一个数值表达式。  
  
## <a name="examples"></a>示例
  
  以下示例返回数字 1-3 的平方根。  
  
```sql
SELECT SQRT(1) AS s1, SQRT(2.0) AS s2, SQRT(3) AS s3  
```  
  
 下面是结果集。  
  
```json
[{s1: 1, s2: 1.4142135623730952, s3: 1.7320508075688772}]  
```  

## <a name="remarks"></a>备注

此系统函数不会使用索引。

## <a name="next-steps"></a>后续步骤

- [数学函数 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](../introduction.md)
