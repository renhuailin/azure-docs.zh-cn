---
title: Azure Cosmos DB 查询语言中的 DateTimeAdd
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 DateTimeAdd。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: ae59c4284598a247d4d7da967909ea1164db3887
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206229"
---
# <a name="datetimeadd-azure-cosmos-db"></a>DateTimeAdd (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

返回通过将指定的数值（作为带符号的整数）添加到指定的 DateTime 字符串而产生的 DateTime 字符串值  
  
## <a name="syntax"></a>语法
  
```sql
DateTimeAdd (<DateTimePart> , <numeric_expr> ,<DateTime>)
```

## <a name="arguments"></a>参数
  
*DateTimePart*  
   DateTimeAdd 向其添加整数的日期部分。 此表列出了所有有效的 DateTimePart 参数：

| DateTimePart | 缩写        |
| ------------ | -------------------- |
| Year         | "year", "yyyy", "yy" |
| Month        | "month", "mm", "m"   |
| 日期          | "day", "dd", "d"     |
| Hour         | "hour", "hh"         |
| Minute       | "minute", "mi", "n"  |
| 秒       | "second", "ss", "s"  |
| Millisecond  | "millisecond", "ms"  |
| Microsecond  | "microsecond", "mcs" |
| Nanosecond   | "nanosecond"、"ns"   |

*numeric_expr*  
   是一个带符号的整数值，它会添加到指定日期/时间的 DateTimePart

*DateTime*  
   `YYYY-MM-DDThh:mm:ss.fffffffZ` 格式的 UTC 日期和时间 ISO 8601 字符串值，其中：
  
|格式|描述|
|-|-|
|YYYY|四位数的年份|
|MM|两位数的月份（01 = 1 月，依此类推。）|
|DD|两位数的月份日期（01 到 31）|
|T|时间元素开头的符号|
|hh|两位数的小时（00 到 23）|
|MM|两位数的分钟（00 到 59）|
|ss|两位数的秒（00 到 59）|
|.fffffff|七位数的小数秒|
|Z|UTC（协调世界时）指示符|
  
  有关 ISO 8601 格式的详细信息，请参阅 [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

## <a name="return-types"></a>返回类型

以 `YYYY-MM-DDThh:mm:ss.fffffffZ` 格式返回 UTC 日期和时间 ISO 8601 字符串值，其中：
  
|格式|描述|
|-|-|
|YYYY|四位数的年份|
|MM|两位数的月份（01 = 1 月，依此类推。）|
|DD|两位数的月份日期（01 到 31）|
|T|时间元素开头的符号|
|hh|两位数的小时（00 到 23）|
|MM|两位数的分钟（00 到 59）|
|ss|两位数的秒（00 到 59）|
|.fffffff|七位数的小数秒|
|Z|UTC（协调世界时）指示符|

## <a name="remarks"></a>备注

DateTimeAdd 将返回 `undefined`，原因如下：

- 指定的 DateTimePart 值无效
- 指定的 numeric_expr 不是有效的整数
- 参数或结果中的日期/时间不是有效的 ISO 8601 日期/时间。

## <a name="examples"></a>示例
  
下面的示例将 1 个月添加到日期/时间：`2020-07-09T23:20:13.4575530Z`

```sql
SELECT DateTimeAdd("mm", 1, "2020-07-09T23:20:13.4575530Z") AS OneMonthLater
```

```json
[
    {
        "OneMonthLater": "2020-08-09T23:20:13.4575530Z"
    }
]
```  

下面的示例从日期/时间中减去 2 小时：`2020-07-09T23:20:13.4575530Z`

```sql
SELECT DateTimeAdd("hh", -2, "2020-07-09T23:20:13.4575530Z") AS TwoHoursEarlier
```

```json
[
    {
        "TwoHoursEarlier": "2020-07-09T21:20:13.4575530Z"
    }
]
```  

## <a name="next-steps"></a>后续步骤

- [日期和时间函数 Azure Cosmos DB](sql-query-date-time-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](../introduction.md)
