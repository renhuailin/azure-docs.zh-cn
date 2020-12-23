---
title: Azure Cosmos DB 查询语言中的 ABS
description: 了解 Azure Cosmos DB 中的 Absolute(ABS) SQL 系统函数如何返回指定数值表达式的正值
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b3047c395ce6ef55a4cad8083e98ccfc7105c7a2
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2020
ms.locfileid: "93332825"
---
# <a name="abs-azure-cosmos-db"></a>ABS (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 返回指定数值表达式的绝对（正）值。  
  
## <a name="syntax"></a>语法
  
```sql
ABS (<numeric_expr>)  
```  
  
## <a name="arguments"></a>参数
  
*numeric_expr*  
   是一个数值表达式。  
  
## <a name="return-types"></a>返回类型
  
  返回一个数值表达式。  
  
## <a name="examples"></a>示例
  
  以下示例演示了对三个不同数字使用 `ABS` 函数的结果。  
  
```sql
SELECT ABS(-1) AS abs1, ABS(0) AS abs2, ABS(1) AS abs3 
```  
  
 下面是结果集。  
  
```json
[{abs1: 1, abs2: 0, abs3: 1}]  
```

## <a name="remarks"></a>备注

此系统函数将从[范围索引](index-policy.md#includeexclude-strategy)中获益。

## <a name="next-steps"></a>后续步骤

- [数学函数 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
