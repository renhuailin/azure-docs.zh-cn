---
title: Azure Cosmos DB 查询语言中的最大值
description: 了解 Azure Cosmos DB 中最大 () SQL 系统函数。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: a3d8e3f2687a492461bdbbdd761e26cdb58f9e96
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96551610"
---
# <a name="max-azure-cosmos-db"></a>最大 (Azure Cosmos DB) 
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

此聚合函数返回表达式中的最大值。
  
## <a name="syntax"></a>语法
  
```sql
MAX(<scalar_expr>)  
```  
  
## <a name="arguments"></a>自变量

*scalar_expr*  
   是标量表达式。 
  
## <a name="return-types"></a>返回类型
  
返回标量表达式。  
  
## <a name="examples"></a>示例
  
下面的示例返回的最大值 `propertyA` ：
  
```sql
SELECT MAX(c.propertyA)
FROM c
```  

## <a name="remarks"></a>备注

此系统函数将从[范围索引](index-policy.md#includeexclude-strategy)中获益。 中的参数 `MAX` 可以是 number、string、boolean 或 null。 所有未定义的值都将被忽略。

比较不同类型的数据时，将按降序顺序使用以下优先级顺序)  (：

- 字符串
- 数字
- boolean
- null

## <a name="next-steps"></a>后续步骤

- [Azure Cosmos DB 中的数学函数](sql-query-mathematical-functions.md)
- [Azure Cosmos DB 中的系统函数](sql-query-system-functions.md)
- [Azure Cosmos DB 中的聚合函数](sql-query-aggregate-functions.md)