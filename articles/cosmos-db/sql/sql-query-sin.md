---
title: Azure Cosmos DB 查询语言中的 SIN
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 SIN。
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8f96c1546ea75f7052c68b91c27437dd794c85ce
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206327"
---
# <a name="sin-azure-cosmos-db"></a>SIN (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 返回指定表达式中指定角度的三角正弦（弧度）。  
  
## <a name="syntax"></a>语法
  
```sql
SIN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>参数
  
*numeric_expr*  
   是一个数值表达式。  
  
## <a name="return-types"></a>返回类型
  
  返回一个数值表达式。  
  
## <a name="examples"></a>示例
  
  以下示例计算指定角度的 `SIN`。  
  
```sql
SELECT SIN(45.175643) AS sin  
```  
  
 下面是结果集。  
  
```json
[{"sin": 0.929607286611012}]  
```  

## <a name="remarks"></a>备注

此系统函数不会使用索引。

## <a name="next-steps"></a>后续步骤

- [数学函数 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](../introduction.md)
