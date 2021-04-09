---
title: Azure Cosmos DB 查询语言中的 GetCurrentTicks
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 GetCurrentTicks。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 16004e6e471094c99229c32a63396ac3b0490905
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "99524266"
---
# <a name="getcurrentticks-azure-cosmos-db"></a>GetCurrentTicks (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

返回自 1970 年 1 月 1 日星期四 00:00:00 以来经过的 100 纳秒时钟周期数。
  
## <a name="syntax"></a>语法
  
```sql
GetCurrentTicks ()
```

## <a name="return-types"></a>返回类型

返回一个有符号的数值，即自 Unix 纪元以来当前已经过的 100 纳秒时钟周期数。 换句话说，GetCurrentTicks 返回自 1970 年 1 月 1 日星期四 00:00:00 以来经过的 100 纳秒时钟周期数。

## <a name="remarks"></a>备注

GetCurrentTicks() 是非确定性的函数。 返回的结果采用 UTC（协调世界时）格式。

> [!NOTE]
> 此系统函数不会使用索引。 如果需要将值与当前时间进行比较，请在查询执行之前获取当前时间，并在 `WHERE` 子句中使用该常量字符串值。

## <a name="examples"></a>示例

下面的示例返回当前时间（以时钟周期为单位）：

```sql
SELECT GetCurrentTicks() AS CurrentTimeInTicks
```

```json
[
    {
        "CurrentTimeInTicks": 15973607943002652
    }
]
```

## <a name="next-steps"></a>后续步骤

- [日期和时间函数 Azure Cosmos DB](sql-query-date-time-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
