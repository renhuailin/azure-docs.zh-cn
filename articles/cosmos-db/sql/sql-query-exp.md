---
title: Azure Cosmos DB 查询语言中的 EXP
description: 了解 Azure Cosmos DB 中的 Exponent (EXP) SQL 系统函数，该函数返回指定数值表达式的指数值
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 18be2065ef2442f8370b780b81c769b2765e5ddb
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206334"
---
# <a name="exp-azure-cosmos-db"></a>EXP (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 返回指定数值表达式的指数值。  
  
## <a name="syntax"></a>语法
  
```sql
EXP (<numeric_expr>)  
```  
  
## <a name="arguments"></a>参数
  
*numeric_expr*  
   是一个数值表达式。  
  
## <a name="return-types"></a>返回类型
  
  返回一个数值表达式。  
  
## <a name="remarks"></a>备注
  
  常量 **e** (2.718281…) 是自然对数的底数。  
  
  一个数字的指数是常量 **e** 的该数字次幂。 例如 EXP(1.0) = e^1.0 = 2.71828182845905，EXP(10) = e^10 = 22026.4657948067。  
  
  某个数的自然对数的指数就是该数本身：EXP (LOG (n)) = n。 并且某个数的指数的自然对数也是该数本身：LOG (EXP (n)) = n。  
  
## <a name="examples"></a>示例
  
  以下示例声明一个变量并返回到指定变量 (10) 的指数值。  
  
```sql
SELECT EXP(10) AS exp  
```  
  
 下面是结果集：  
  
```json
[{exp: 22026.465794806718}]  
```  
  
 以下示例将返回 20 的自然对数的指数值以及 20 的指数的自然对数。 由于这些函数互为反函数，因此在这两种情况下，用于浮点数学舍入的返回值均为 20。  
  
```sql
SELECT EXP(LOG(20)) AS exp1, LOG(EXP(20)) AS exp2  
```  
  
 下面是结果集：  
  
```json
[{exp1: 19.999999999999996, exp2: 20}]  
```  

## <a name="next-steps"></a>后续步骤

- [数学函数 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](../introduction.md)
