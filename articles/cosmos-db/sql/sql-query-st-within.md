---
title: Azure Cosmos DB 查询语言中的 ST_WITHIN
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 ST_WITHIN。
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/21/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 7f3aea14b4da97624abb8515c560281afbdefe18
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128625252"
---
# <a name="st_within-azure-cosmos-db"></a>ST_WITHIN (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 返回一个布尔表达式，指示在第一个参数中指定的 GeoJSON 对象（Point、Polygon、MultiPolygon 或 LineString）是否位于第二个参数中的 GeoJSON（Point、Polygon、MultiPolygon 或 LineString）内。  
  
## <a name="syntax"></a>语法
  
```sql
ST_WITHIN (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>参数
  
*spatial_expr*  
   是 GeoJSON 点、多边形或 LineString 对象表达式。  
  
## <a name="return-types"></a>返回类型
  
  返回一个布尔值。  
  
## <a name="examples"></a>示例
  
  以下示例演示了如何使用 `ST_WITHIN` 查找某个多边形内的所有家族文档。  
  
```sql
SELECT f.id
FROM Families f
WHERE ST_WITHIN(f.location, {  
    'type':'Polygon',
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 下面是结果集。  
  
```json
[{ "id": "WakefieldFamily" }]  
```  

## <a name="remarks"></a>备注

此系统函数将从[地理空间索引](../index-policy.md#spatial-indexes)中受益，但包含聚合的查询除外。

> [!NOTE]
> GeoJSON 规范要求多边形内的点按逆时针顺序指定。 以顺时针顺序指定的多边形表示其中的区域倒转。


## <a name="next-steps"></a>后续步骤

- [空间函数 Azure Cosmos DB](sql-query-spatial-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](../introduction.md)
