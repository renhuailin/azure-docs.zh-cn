---
title: Azure Cosmos DB 查询语言中的 ST_DISTANCE
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 ST_DISTANCE。
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 1edb20524327841125461b7b0d4a85cac4567160
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206316"
---
# <a name="st_distance-azure-cosmos-db"></a>ST_DISTANCE (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 返回两个 GeoJSON Point、Polygon、MultiPolygon 或 LineString 表达式之间的距离。 若要了解详细信息，请参阅[地理空间和 GeoJSON 位置数据](sql-query-geospatial-intro.md)一文。
  
## <a name="syntax"></a>语法
  
```sql
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>参数
  
*spatial_expr*  
   是任何有效的 GeoJSON 点、多边形或 LineString 对象表达式。  
  
## <a name="return-types"></a>返回类型
  
  返回包含距离的一个数字表达式。 这是根据默认参考系统以米为单位表示的。  
  
## <a name="examples"></a>示例
  
  以下示例演示了如何使用 `ST_DISTANCE` 内置函数返回与指定位置的距离在 30 公里内的所有家族文档。 .  
  
```sql
SELECT f.id
FROM Families f
WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000  
```  
  
 下面是结果集。  
  
```json
[{  
  "id": "WakefieldFamily"  
}]  
```

## <a name="remarks"></a>备注

此系统函数将从[地理空间索引](../index-policy.md#spatial-indexes)中受益，但包含聚合的查询除外。

## <a name="next-steps"></a>后续步骤

- [空间函数 Azure Cosmos DB](sql-query-spatial-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](../introduction.md)
