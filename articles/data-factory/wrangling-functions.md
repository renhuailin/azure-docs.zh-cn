---
title: Azure 数据工厂中的数据整理函数
description: Azure 数据工厂中的可用数据整理函数概述
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/19/2021
ms.openlocfilehash: a88f9fab2b10271aa7856a6d0b5ee114f46cfb49
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2021
ms.locfileid: "98633831"
---
# <a name="transformation-functions-in-power-query-for-data-wrangling"></a>用于数据整理的 Power Query 中的转换函数

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

使用 Azure 数据工厂中的数据整理，可以通过将 Power Query ```M``` 脚本转换为数据流脚本，在云范围内进行无代码敏捷数据准备和整理。 ADF 与 [Power Query Online](/powerquery-m/power-query-m-reference) 集成，使用数据流 ```M``` Spark 基础结构通过 Spark 执行将 Power Query 函数提供给整理。 

> [!NOTE]
> ADF 中的 Power Query 当前在公共预览版中可用

当前并非所有 Power Query M 函数都支持数据整理，但在创作期间可用。 生成混合时，如果函数不受支持，系统将提示你输入以下错误消息：

`The Wrangling Data Flow is invalid. Expression.Error: The transformation logic is not supported. Please try a simpler expression.`

下面是受支持的 Power Query M 函数的列表。

## <a name="column-management"></a>列管理

* 选择： [SelectColumns](/powerquery-m/table-selectcolumns)
* 删除： [table.removecolumns](/powerquery-m/table-removecolumns)
* 重命名： [RenameColumns](/powerquery-m/table-renamecolumns)、 [PrefixColumns](/powerquery-m/table-prefixcolumns)、 [TransformColumnNames](/powerquery-m/table-transformcolumnnames)
* 重新排序： [ReorderColumns](/powerquery-m/table-reordercolumns)

## <a name="row-filtering"></a>行筛选

使用 M 函数 [SelectRows](/powerquery-m/table-selectrows) 根据以下条件进行筛选：

* 相等和不相等
* 数值、文本和日期的比较 (但不是 DateTime) 
* 数字信息，如[IsEven](/powerquery-m/number-iseven) / [奇数](/powerquery-m/number-iseven)
* 使用 Text 的文本 [包含](/powerquery-m/text-contains)、 [StartsWith](/powerquery-m/text-startswith)或 [EndsWith。](/powerquery-m/text-endswith)
* 日期范围，包括所有 "IsIn' [Date 函数](/powerquery-m/date-functions))  
* 使用 and、or 或 not 条件的组合

## <a name="adding-and-transforming-columns"></a>添加和转换列

以下 M 函数添加或转换列： [AddColumn](/powerquery-m/table-addcolumn)、 [table.transformcolumns](/powerquery-m/table-transformcolumns)、 [ReplaceValue](/powerquery-m/table-replacevalue)、 [DuplicateColumn](/powerquery-m/table-duplicatecolumn)。 下面是受支持的转换函数。

* 数值算术
* 文本连接
* 日期 andTime 算术运算 (算术运算符、 [AddDays](/powerquery-m/date-adddays)、 [AddMonths](/powerquery-m/date-addmonths)、 [AddQuarters](/powerquery-m/date-addquarters)、 [AddWeeks](/powerquery-m/date-addweeks)、 [AddYears) ](/powerquery-m/date-addyears)
* 持续时间可用于日期和时间算法，但必须转换为另一种类型，然后才能写入接收器 (算术运算符、 [#duration](/powerquery-m/sharpduration)、 [duration](/powerquery-m/duration-days)、Duration、 [duration、](/powerquery-m/duration-hours) [TotalDays](/powerquery-m/duration-totaldays)、 [](/powerquery-m/duration-minutes) [TotalHours](/powerquery-m/duration-totalhours) [、duration.](/powerquery-m/duration-seconds) [totaldvrduration.totalminutes](/powerquery-m/duration-totalminutes)、 [duration. timespan.totalseconds](/powerquery-m/duration-totalseconds))     
* 大多数标准、科学和三角数字函数 ([操作](/powerquery-m/number-functions#operations)、[舍入](/powerquery-m/number-functions#rounding)和三角函数下除 Number [](/powerquery-m/number-functions#trigonometry) *之外* 的所有函数。阶乘、数字和组合) 
* 替换 ([替换器，ReplaceText](/powerquery-m/replacer-replacetext)， [替换器](/powerquery-m/replacer-replacevalue) [，](/powerquery-m/text-replace) [text. Remove](/powerquery-m/text-remove)) 
* 位置文本提取 ([PositionOf](/powerquery-m/text-positionof)、Text、 [Length](/powerquery-m/text-length)、text. [Start](/powerquery-m/text-start)、Text. [End](/powerquery-m/text-end)、 [ReplaceRange](/powerquery-m/text-replacerange)、 [](/powerquery-m/text-middle) [RemoveRange](/powerquery-m/text-removerange)) 
* 基本文本格式 ([text. Lower](/powerquery-m/text-lower)、 [text、text](/powerquery-m/text-upper)。 [Trim](/powerquery-m/text-trim) / [开始](/powerquery-m/text-trimstart) / [结束](/powerquery-m/text-trimend)、 [PadStart](/powerquery-m/text-padstart) / [结束](/powerquery-m/text-padend)、[文本。反向](/powerquery-m/text-reverse)) 
* 日期/时间函数 ([日期. 日](/powerquery-m/date-day)、 [日期. 月](/powerquery-m/date-month)、日. [年](/powerquery-m/date-year) [、日](/powerquery-m/time-hour)、日 [、时间](/powerquery-m/time-minute) [、](/powerquery-m/time-second) [DayOfWeek](/powerquery-m/date-dayofweek)、 [DayOfYear](/powerquery-m/date-dayofyear)、 [DaysInMonth](/powerquery-m/date-daysinmonth)) 
* 如果表达式 (但分支必须具有匹配的类型) 
* 作为逻辑列的行筛选器
* Number、text、logical、date 和 datetime 常量

<a name="mergingjoining-tables"></a>合并/联接表
----------------------
* Power Query 将生成嵌套联接 (NestedJoin;用户还可以手动写入 [AddJoinColumn](/powerquery-m/table-addjoincolumn)) 。
    然后，用户必须将嵌套的联接列展开为非嵌套联接 (Table.expandtablecolumn，在任何其他上下文) 中都不受支持。
* 可以直接写入 M 函数   [表](/powerquery-m/table-join) ，以避免需要额外的扩展步骤，但用户必须确保联接的表中没有重复的列名
* 支持的联接类型：   [内部](/powerquery-m/joinkind-inner)、   [LeftOuter](/powerquery-m/joinkind-leftouter)、   [RightOuter](/powerquery-m/joinkind-rightouter)、   [FullOuter](/powerquery-m/joinkind-fullouter)
* 同时[](/powerquery-m/value-equals)支持 NullableEquals 和值等[。](/powerquery-m/value-nullableequals)

## <a name="group-by"></a>Group by

使用 " [表](/powerquery-m/table-group) " 作为聚合值。
* 必须与聚合函数一起使用
* 支持的聚合函数：   [列表. Sum](/powerquery-m/list-sum)、   [list. Count](/powerquery-m/list-count)、   [list. Average](/powerquery-m/list-average)、   [list. Min](/powerquery-m/list-min)、   [Max](/powerquery-m/list-max)、   [StandardDeviation](/powerquery-m/list-standarddeviation)、list、   [First](/powerquery-m/list-first)、   [list. Last](/powerquery-m/list-last)

## <a name="sorting"></a>排序

使用 [表。](/powerquery-m/table-sort) 对值进行排序。

## <a name="reducing-rows"></a>减少行

保留并删除 Top，保留范围 (对应的 M 个函数，仅支持计数，而不支持条件： [](/powerquery-m/table-skip) [FirstN](/powerquery-m/table-firstn)、 [RemoveFirstN](/powerquery-m/table-removefirstn)、[明尼苏达](/powerquery-m/table-minn)、 [MaxN](/powerquery-m/table-maxn)) [](/powerquery-m/table-range)

## <a name="known-unsupported-functions"></a>已知不受支持的函数

| 函数 | 状态 |
| -- | -- |
| Table.PromoteHeaders | 不支持。 可以通过在数据集中将 "第一行作为标题" 来实现相同的结果。 |
| Table.CombineColumns | 这是一个不直接支持的常见方案，但可通过添加一个连接两个给定列的新列来实现。  例如，AddColumn (RemoveEmailColumn，"Name"，每个 [FirstName] & "" & [LastName] )  |
| Table.TransformColumnTypes | 大多数情况下都支持此项。 以下方案不受支持：将字符串转换为货币类型，将字符串转换为时间类型，并将字符串转换为百分比类型。 |
| Table.NestedJoin | 仅执行联接将导致验证错误。 要使其正常工作，必须对列进行扩展。 |
| Table.Distinct | 不支持删除重复的行。 |
| Table.RemoveLastN | 不支持删除下一行。 |
| Table.RowCount | 不支持，但可通过添加包含值1的自定义列来实现，然后使用 List. Sum 将该列聚合在一起。 支持组。 | 
| 行级错误处理 | 当前不支持行级别的错误处理。 例如，若要筛选出列中的非数字值，一种方法是将文本列转换为数字。 未能转换的每个单元都将处于错误状态，需要对其进行筛选。 这种情况在整理的数据流中是不可能的。 |
| Table.Transpose | 不支持 |
| Table.Pivot | 不支持 |

## <a name="next-steps"></a>后续步骤

了解如何 [在 ADF 中创建数据整理 Power Query](wrangling-tutorial.md)。
