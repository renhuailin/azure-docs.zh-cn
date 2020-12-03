---
title: Azure Cosmos DB 查询语言的总和
description: 了解 Azure Cosmos DB 中) SQL 系统函数的总和 (。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: d5a86cd5af504072480e0cd749caa6c0532d0bc1
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96551613"
---
# <a name="sum-azure-cosmos-db"></a>SUM (Azure Cosmos DB) 
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

此聚合函数返回表达式中的值的总和。
  
## <a name="syntax"></a>语法
  
```sql
SUM(<numeric_expr>)  
```  
  
## <a name="arguments"></a>参数
  
*numeric_expr*  
   是一个数值表达式。  
  
## <a name="return-types"></a>返回类型
  
返回一个数值表达式。  
  
## <a name="examples"></a>示例
  
下面的示例返回的总和 `propertyA` ：
  
```sql
SELECT SUM(c.propertyA)
FROM c
```  

## <a name="remarks"></a>备注

此系统函数将从[范围索引](index-policy.md#includeexclude-strategy)中获益。 如果中的任何参数 `SUM` 为 string、boolean 或 null，则整个聚合系统函数将返回 `undefined` 。 如果任何参数有 `undefined` 值，则不会影响 `SUM` 计算。

## <a name="next-steps"></a>后续步骤

- [Azure Cosmos DB 中的数学函数](sql-query-mathematical-functions.md)
- [Azure Cosmos DB 中的系统函数](sql-query-system-functions.md)
- [Azure Cosmos DB 中的聚合函数](sql-query-aggregate-functions.md)