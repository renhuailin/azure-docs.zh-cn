---
title: Azure Cosmos DB 查询语言中的 ATAN
description: 了解 Azure Cosmos DB 中的 Arctangent (ATAN ) SQL 系统函数如何返回其正切是指定数值表达式的角度（以弧度为单位）
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d956a99b3ec615a7cc1d97e2efedade08681e47a
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206137"
---
# <a name="atan-azure-cosmos-db"></a>ATAN (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 返回角度（弧度），其正切是指定的数值表达式。 这也被称为反正切。  
  
## <a name="syntax"></a>语法
  
```sql
ATAN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>参数
  
*numeric_expr*  
   是一个数值表达式。  
  
## <a name="return-types"></a>返回类型
  
  返回一个数值表达式。  
  
## <a name="examples"></a>示例
  
  以下示例返回指定值的 `ATAN`。  
  
```sql
SELECT ATAN(-45.01) AS atan  
```  
  
 下面是结果集。  
  
```json
[{"atan": -1.5485826962062663}]  
```  
  
## <a name="remarks"></a>备注

此系统函数不会使用索引。

## <a name="next-steps"></a>后续步骤

- [数学函数 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](../introduction.md)
