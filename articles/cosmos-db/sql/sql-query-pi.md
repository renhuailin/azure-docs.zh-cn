---
title: Azure Cosmos DB 查询语言中的 PI
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 PI。
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 15cb31f1ed98c9100b7bd04ec5f9e7b5102091b3
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206328"
---
# <a name="pi-azure-cosmos-db"></a>PI (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 返回 PI 的常量值。  
  
## <a name="syntax"></a>语法
  
```sql
PI ()  
```  
   
## <a name="return-types"></a>返回类型
  
  返回一个数值表达式。  
  
## <a name="examples"></a>示例
  
  下面的示例返回 `PI` 的值。  
  
```sql
SELECT PI() AS pi 
```  
  
 下面是结果集：  
  
```json
[{"pi": 3.1415926535897931}]  
```  

## <a name="next-steps"></a>后续步骤

- [数学函数 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](../introduction.md)
