---
title: Azure Cosmos DB 查询语言中的最小值
description: 了解 Azure Cosmos DB 中 (MIN) SQL 系统函数的最小值。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 679814822cca5a72bd261d3c8944863715e31f70
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552363"
---
# <a name="min-azure-cosmos-db"></a>最小 (Azure Cosmos DB) 
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

此聚合函数返回表达式中的最小值。
  
## <a name="syntax"></a>语法
  
```sql
MIN(<scalar_expr>)  
```  
  
## <a name="arguments"></a>自变量
  
*scalar_expr*  
   是标量表达式。 
  
## <a name="return-types"></a>返回类型
  
返回标量表达式。  
  
## <a name="examples"></a>示例
  
下面的示例返回的最小值 `propertyA` ：
  
```sql
SELECT MIN(c.propertyA)
FROM c
```  

## <a name="remarks"></a>备注

此系统函数将从[范围索引](index-policy.md#includeexclude-strategy)中获益。 中的参数 `MIN` 可以是 number、string、boolean 或 null。 所有未定义的值都将被忽略。

比较不同类型的数据时，按升序 (使用以下优先级顺序) ：

- null
- boolean
- number
- string

## <a name="next-steps"></a>后续步骤

- [Azure Cosmos DB 中的数学函数](sql-query-mathematical-functions.md)
- [Azure Cosmos DB 中的系统函数](sql-query-system-functions.md)
- [Azure Cosmos DB 中的聚合函数](sql-query-aggregate-functions.md)