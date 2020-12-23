---
title: Azure Cosmos DB 查询语言中的 COT
description: 了解 Azure Cosmos DB 中的 Cotangent(COT) SQL 系统函数如何在指定的数值表达式中返回指定角度（以弧度为单位）的三角余切
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c247ac477f92985fc722f9c06655b6a7474876fb
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2020
ms.locfileid: "93339015"
---
# <a name="cot-azure-cosmos-db"></a>COT (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 返回指定数值表达式中指定角度的三角余切。  
  
## <a name="syntax"></a>语法
  
```sql
COT(<numeric_expr>)  
```  
  
## <a name="arguments"></a>参数
  
*numeric_expr*  
   是一个数值表达式。  
  
## <a name="return-types"></a>返回类型
  
  返回一个数值表达式。  
  
## <a name="examples"></a>示例
  
  以下示例计算指定角度的 `COT`。  
  
```sql
SELECT COT(124.1332) AS cot  
```  
  
 下面是结果集。  
  
```json
[{"cot": -0.040311998371148884}]  
```  

## <a name="remarks"></a>备注

此系统函数不会使用索引。

## <a name="next-steps"></a>后续步骤

- [数学函数 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
