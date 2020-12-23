---
title: Azure Cosmos DB 查询语言中的 RAND
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 RAND。
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: fb3e310970fcc2146ee0d4b790a9744dcd566bad
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341648"
---
# <a name="rand-azure-cosmos-db"></a>RAND (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 返回 [0,1) 中随机生成的数值。
 
## <a name="syntax"></a>语法
  
```sql
RAND ()  
```  

## <a name="return-types"></a>返回类型

  返回一个数值表达式。

## <a name="remarks"></a>备注

  `RAND` 是非确定性的函数。 重复调用 `RAND` 不会返回相同的结果。 此系统函数不会使用索引。


## <a name="examples"></a>示例
  
  以下示例返回一个随机生成的数值。
  
```sql
SELECT RAND() AS rand 
```  
  
 下面是结果集。  
  
```json
[{"rand": 0.87860053195618093}]  
``` 

## <a name="next-steps"></a>后续步骤

- [数学函数 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
