---
title: Azure Cosmos DB 查询语言中的 LOWER
description: 了解 Azure Cosmos DB 中的 LOWER SQL 系统函数，以便返回在将大写字符数据转换为小写后的字符串表达式
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 04/07/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6e5aee7173534a33f5ec1563873a02c26e409687
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206216"
---
# <a name="lower-azure-cosmos-db"></a>LOWER (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 返回在将大写字符数据转换为小写后的字符串表达式。  

LOWER 系统函数不使用索引。 如果计划频繁进行不区分大小写的比较，则 LOWER 系统函数可能会消耗大量 RU。 如果是这种情况，可以在插入时将大小写规范化，而不是每次都使用 LOWER 系统函数将数据规范化以进行比较。 于是，诸如 SELECT * FROM c WHERE LOWER(c.name) = 'bob' 的查询只需变成 SELECT * FROM c WHERE c.name = 'bob'。

## <a name="syntax"></a>语法
  
```sql
LOWER(<str_expr>)  
```  
  
## <a name="arguments"></a>参数
  
*str_expr*  
   是一个字符串表达式。  
  
## <a name="return-types"></a>返回类型
  
  返回一个字符串表达式。  
  
## <a name="examples"></a>示例
  
  以下示例演示如何在查询中使用 `LOWER`。  
  
```sql
SELECT LOWER("Abc") AS lower
```  
  
 下面是结果集。  
  
```json
[{"lower": "abc"}]  
  
```  

## <a name="remarks"></a>备注

此系统函数将不[使用索引](../index-overview.md#index-usage)。

## <a name="next-steps"></a>后续步骤

- [字符串函数 Azure Cosmos DB](sql-query-string-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](../introduction.md)
