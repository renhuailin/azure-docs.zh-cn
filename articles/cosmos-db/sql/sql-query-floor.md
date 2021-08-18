---
title: Azure Cosmos DB 查询语言中的 FLOOR
description: 了解 Azure Cosmos DB 中的 FLOOR SQL 系统函数，该函数返回小于或等于指定数值表达式的最大整数
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: dddaa1d4b89c41318b6496ec1702030773f2676a
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206227"
---
# <a name="floor-azure-cosmos-db"></a>FLOOR (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 返回小于或等于指定数值表达式的最大整数。  
  
## <a name="syntax"></a>语法
  
```sql
FLOOR (<numeric_expr>)  
```  
  
## <a name="arguments"></a>参数
  
*numeric_expr*  
   是一个数值表达式。  
  
## <a name="return-types"></a>返回类型
  
  返回一个数值表达式。  
  
## <a name="examples"></a>示例
  
  以下示例显示了如何对正值、负值和零值使用 `FLOOR` 函数。  
  
```sql
SELECT FLOOR(123.45) AS fl1, FLOOR(-123.45) AS fl2, FLOOR(0.0) AS fl3  
```  
  
 下面是结果集。  
  
```json
[{fl1: 123, fl2: -124, fl3: 0}]  
```

## <a name="remarks"></a>备注

此系统函数将从[范围索引](../index-policy.md#includeexclude-strategy)中获益。

## <a name="next-steps"></a>后续步骤

- [数学函数 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](../introduction.md)
