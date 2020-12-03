---
title: Azure Cosmos DB 查询语言中的 AVG
description: 了解 Azure Cosmos DB 中平均 (AVG) SQL 系统函数的信息。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 2cfbca798a7b404e4ee12b93396b2a5b08d7d5bb
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552367"
---
# <a name="avg-azure-cosmos-db"></a>平均 (Azure Cosmos DB) 
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

此聚合函数返回表达式中的值的平均值。
  
## <a name="syntax"></a>语法
  
```sql
AVG(<numeric_expr>)  
```  
  
## <a name="arguments"></a>参数
  
*numeric_expr*  
   是一个数值表达式。  
  
## <a name="return-types"></a>返回类型
  
返回一个数值表达式。  
  
## <a name="examples"></a>示例
  
下面的示例返回的平均值 `propertyA` ：
  
```sql
SELECT AVG(c.propertyA)
FROM c
```  

## <a name="remarks"></a>备注

此系统函数将从[范围索引](index-policy.md#includeexclude-strategy)中获益。 如果中的任何参数 `AVG` 为 string、boolean 或 null，则整个聚合系统函数将返回 `undefined` 。 如果任何参数有 `undefined` 值，则不会影响 `AVG` 计算。

## <a name="next-steps"></a>后续步骤

- [Azure Cosmos DB 中的数学函数](sql-query-mathematical-functions.md)
- [Azure Cosmos DB 中的系统函数](sql-query-system-functions.md)
- [Azure Cosmos DB 中的聚合函数](sql-query-aggregate-functions.md)