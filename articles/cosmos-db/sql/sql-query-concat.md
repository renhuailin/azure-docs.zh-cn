---
title: Azure Cosmos DB 查询语言中的 CONCAT
description: 了解 Azure Cosmos DB 中的 CONCAT SQL 系统函数如何返回一个字符串来表示连接两个或更多字符串值的结果
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: fbedaf0c1b01b4c3ce8ad08f7fec51fe12bf8593
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206245"
---
# <a name="concat-azure-cosmos-db"></a>CONCAT (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 返回一个字符串，该字符串是连接两个或多个字符串值的结果。  
  
## <a name="syntax"></a>语法
  
```sql
CONCAT(<str_expr1>, <str_expr2> [, <str_exprN>])  
```  
  
## <a name="arguments"></a>参数
  
*str_expr*  
   是要连接到其他值的字符串表达式。 `CONCAT` 函数需要至少两个 *str_expr* 参数。  
  
## <a name="return-types"></a>返回类型
  
  返回一个字符串表达式。  
  
## <a name="examples"></a>示例
  
  以下示例返回将指定值串联后形成的字符串。  
  
```sql
SELECT CONCAT("abc", "def") AS concat  
```  
  
 下面是结果集。  
  
```json
[{"concat": "abcdef"}]  
```  
  
## <a name="remarks"></a>备注

此系统函数不会使用索引。

## <a name="next-steps"></a>后续步骤

- [字符串函数 Azure Cosmos DB](sql-query-string-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](../introduction.md)
