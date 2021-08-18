---
title: Azure Cosmos DB 查询语言中的 LOG10
description: 了解 Azure Cosmos DB 中的 LOG10 SQL 系统函数，以便返回指定数值表达式的以 10 为底的对数
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 7397af2322063f2f9b0b8c3f9fedc47fca40f3fc
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206126"
---
# <a name="log10-azure-cosmos-db"></a>LOG10 (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 返回指定数值表达式以 10 为底的对数。  
  
## <a name="syntax"></a>语法
  
```sql
LOG10 (<numeric_expr>)  
```  
  
## <a name="arguments"></a>参数
  
*numeric_expression*  
   是一个数值表达式。  
  
## <a name="return-types"></a>返回类型
  
  返回一个数值表达式。  
  
## <a name="remarks"></a>备注
  
  LOG10 和 POWER 函数互为反函数。 例如，10 ^ LOG10(n) = n。 此系统函数不会使用索引。
  
## <a name="examples"></a>示例
  
  以下示例声明一个变量并返回指定变量 (100) 的 LOG10 值。  
  
```sql
SELECT LOG10(100) AS log10 
```  
  
 下面是结果集。  
  
```json
[{log10: 2}]  
```  

## <a name="next-steps"></a>后续步骤

- [数学函数 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](../introduction.md)
