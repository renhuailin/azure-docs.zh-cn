---
title: Azure Cosmos DB 查询语言中的 ROUND
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 ROUND。
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 76543268d26970db0566040184807a7ead329a1e
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206266"
---
# <a name="round-azure-cosmos-db"></a>ROUND (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 返回一个数值，四舍五入到最接近的整数值。  
  
## <a name="syntax"></a>语法
  
```sql
ROUND(<numeric_expr>)  
```  
  
## <a name="arguments"></a>参数
  
*numeric_expr*  
   是一个数值表达式。  
  
## <a name="return-types"></a>返回类型
  
  返回一个数值表达式。  
  
## <a name="remarks"></a>备注
  
执行的四舍五入运算遵循远离零的中点四舍五入。 如果输入是正好介于两个整数之间的数值表达式，则结果将是离零最近的整数值。 此系统函数将从[范围索引](../index-policy.md#includeexclude-strategy)中获益。
  
|<numeric_expr>|已四舍五入|
|-|-|
|-6.5000|-7|
|-0.5|-1|
|0.5|1|
|6.5000|7|
  
## <a name="examples"></a>示例
  
以下示例将以下正数和负数舍入到最接近的整数。  
  
```sql
SELECT ROUND(2.4) AS r1, ROUND(2.6) AS r2, ROUND(2.5) AS r3, ROUND(-2.4) AS r4, ROUND(-2.6) AS r5  
```  
  
下面是结果集。  
  
```json
[{r1: 2, r2: 3, r3: 3, r4: -2, r5: -3}]  
```  

## <a name="next-steps"></a>后续步骤

- [数学函数 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](../introduction.md)
