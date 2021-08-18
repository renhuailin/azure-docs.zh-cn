---
title: Azure Cosmos DB 查询语言中的 COUNT
description: 了解 Azure Cosmos DB 中的 Count (COUNT) SQL 系统函数。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: edc3c824bc9ac08e325259c0b5f736867f877062
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206164"
---
# <a name="count-azure-cosmos-db"></a>COUNT (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

此系统函数返回表达式中值的计数。
  
## <a name="syntax"></a>语法
  
```sql
COUNT(<scalar_expr>)  
```  
  
## <a name="arguments"></a>参数
  
scalar_expr  
   是任意标量表达式
  
## <a name="return-types"></a>返回类型
  
返回一个数值表达式。  
  
## <a name="examples"></a>示例
  
以下示例返回容器中项的总计数：
  
```sql
SELECT COUNT(1)
FROM c
``` 
COUNT 可以采用任何标量表达式作为输入。 以下查询将生成等效的结果：

```sql
SELECT COUNT(2)
FROM c
```

## <a name="remarks"></a>备注

此系统函数将受益于查询筛选器中任何属性的[范围索引](../index-policy.md#includeexclude-strategy)。

## <a name="next-steps"></a>后续步骤

- [Azure Cosmos DB 中的数学函数](sql-query-mathematical-functions.md)
- [Azure Cosmos DB 中的系统函数](sql-query-system-functions.md)
- [Azure Cosmos DB 中的聚合函数](sql-query-aggregate-functions.md)