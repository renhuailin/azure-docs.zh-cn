---
title: Azure 数据工厂中的数据整理函数
description: Azure 数据工厂中的可用数据整理函数概述
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.date: 04/16/2021
ms.openlocfilehash: 2af1e7f9e1b787e73247d9537b4a8876cc4f7220
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129361240"
---
# <a name="transformation-functions-in-power-query-for-data-wrangling"></a>Power Query 中用于数据整理的转换函数

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

使用 Azure 数据工厂中的数据整理，可以通过将 Power Query ```M``` 脚本转换为数据流脚本，进行云规模的无代码敏捷数据准备和整理。 ADF 与 [Power Query Online](/powerquery-m/power-query-m-reference) 集成，并通过执行 Spark 使用数据流 Spark 基础结构，使 Power Query ```M``` 函数可用于数据整理。 

目前，尽管在创作过程中可以使用 Power Query M 函数，但并非所有的 Power Query M 函数都支持用于数据处理。 在构建 Power Query 混合时，如果函数不受支持，系统将提示以下错误消息：

`UserQuery : Expression.Error: The transformation logic is not supported as it requires dynamic access to rows of data, which cannot be scaled out.`

下面列出了支持的 Power Query M 函数。

## <a name="column-management"></a>列管理

* 选择：[Table.SelectColumns](/powerquery-m/table-selectcolumns)
* 删除：[Table.RemoveColumns](/powerquery-m/table-removecolumns)
* 重命名：[Table.RenameColumns](/powerquery-m/table-renamecolumns)、[Table.PrefixColumns](/powerquery-m/table-prefixcolumns)、[Table.TransformColumnNames](/powerquery-m/table-transformcolumnnames)
* 重新排序：[Table.ReorderColumns](/powerquery-m/table-reordercolumns)

## <a name="row-filtering"></a>行筛选

使用 M 函数 [Table.SelectRows](/powerquery-m/table-selectrows) 根据以下条件进行筛选：

* 相等和不相等
* 数字、文本和日期比较（但不是日期/时间）
* 数字信息，如 [Number.IsEven](/powerquery-m/number-iseven)/[Odd](/powerquery-m/number-iseven)
* 使用 [Text.Contains](/powerquery-m/text-contains)、[Text.StartsWith](/powerquery-m/text-startswith) 或 [Text.EndsWith](/powerquery-m/text-endswith) 的文本包含
* 日期范围，包括所有“IsIn”[日期函数](/powerquery-m/date-functions) 
* 使用 and、or 或 not 条件的组合

## <a name="adding-and-transforming-columns"></a>添加和转换列

以下 M 函数添加或转换列：[Table.AddColumn](/powerquery-m/table-addcolumn)、[Table.TransformColumns](/powerquery-m/table-transformcolumns)、[Table.ReplaceValue](/powerquery-m/table-replacevalue)、[Table.DuplicateColumn](/powerquery-m/table-duplicatecolumn)。 下面是支持的转换函数。

* 数值算术
* 文本串联
* 日期和时间算术（算术运算符、[Date.AddDays](/powerquery-m/date-adddays)、[Date.AddMonths](/powerquery-m/date-addmonths)、[Date.AddQuarters](/powerquery-m/date-addquarters)、[Date.AddWeeks](/powerquery-m/date-addweeks)、[Date.AddYears](/powerquery-m/date-addyears)）
* 持续时间可用于日期和时间算术，但必须转换为另一种类型，然后才能写入接收器（算术运算符、[#duration](/powerquery-m/sharpduration)、[Duration.Days](/powerquery-m/duration-days)、[Duration.Hours](/powerquery-m/duration-hours)、[Duration.Minutes](/powerquery-m/duration-minutes)、[Duration.Seconds](/powerquery-m/duration-seconds)、[Duration.TotalDays](/powerquery-m/duration-totaldays)、[Duration.TotalHours](/powerquery-m/duration-totalhours)、[Duration.TotalMinutes](/powerquery-m/duration-totalminutes)、[Duration.TotalSeconds](/powerquery-m/duration-totalseconds)）    
* 大多数标准、科学和三角数字函数（[Operations](/powerquery-m/number-functions#operations)、[Rounding](/powerquery-m/number-functions#rounding) 和 [Trigonometry](/powerquery-m/number-functions#trigonometry) 下除了 Number.Factorial、Number.Permutations 和 Number.Combinations 以外的所有函数）
* 替换（[Replacer.ReplaceText](/powerquery-m/replacer-replacetext)、[Replacer.ReplaceValue](/powerquery-m/replacer-replacevalue)、[Text.Replace](/powerquery-m/text-replace)、[Text.Remove](/powerquery-m/text-remove)）
* 位置文本提取（[Text.PositionOf](/powerquery-m/text-positionof)、[Text.Length](/powerquery-m/text-length)、[Text.Start](/powerquery-m/text-start)、[Text.End](/powerquery-m/text-end)、[Text.Middle](/powerquery-m/text-middle)、[Text.ReplaceRange](/powerquery-m/text-replacerange)、[Text.RemoveRange](/powerquery-m/text-removerange)）
* 基本文本格式（[Text.Lower](/powerquery-m/text-lower)、[Text.Upper](/powerquery-m/text-upper)、[Text.Trim](/powerquery-m/text-trim)/[Start](/powerquery-m/text-trimstart)/[End](/powerquery-m/text-trimend)、[Text.PadStart](/powerquery-m/text-padstart)/[End](/powerquery-m/text-padend)、[Text.Reverse](/powerquery-m/text-reverse)）
* 日期/时间函数（[Date.Day](/powerquery-m/date-day)、[Date.Month](/powerquery-m/date-month)、[Date.Year](/powerquery-m/date-year) [Time.Hour](/powerquery-m/time-hour)、[Time.Minute](/powerquery-m/time-minute)、[Time.Second](/powerquery-m/time-second)、[Date.DayOfWeek](/powerquery-m/date-dayofweek)、[Date.DayOfYear](/powerquery-m/date-dayofyear)、[Date.DaysInMonth](/powerquery-m/date-daysinmonth)）
* If 表达式（但分支必须具有匹配的类型）
* 作为逻辑列的行筛选器
* Number、text、logical、date 和 datetime 常量

## <a name="mergingjoining-tables"></a>合并/联接表

* Power Query 将生成嵌套联接（Table.NestedJoin；用户还可以手动写入 [Table.AddJoinColumn](/powerquery-m/table-addjoincolumn)）。
    然后，用户必须将嵌套联接列扩展为非嵌套联接（Table.ExpandTableColumn，在任何其他上下文中不受支持）。
* 可以直接写入 M 函数 [Table.Join](/powerquery-m/table-join)，以避免执行额外的扩展步骤，但用户必须确保联接表中没有重复的列名
* 支持的联接类型：[Inner](/powerquery-m/joinkind-inner)、[LeftOuter](/powerquery-m/joinkind-leftouter)、[RightOuter](/powerquery-m/joinkind-rightouter)、[FullOuter](/powerquery-m/joinkind-fullouter)
* [Value.Equals](/powerquery-m/value-equals) 和 [Value.NullableEquals](/powerquery-m/value-nullableequals) 都支持用作键值等同性比较器

## <a name="group-by"></a>Group by

使用 [Table.Group](/powerquery-m/table-group) 聚合值。
* 必须与聚合函数一起使用
* 支持的聚合函数：[List.Sum](/powerquery-m/list-sum)、[List.Count](/powerquery-m/list-count)、[List.Average](/powerquery-m/list-average)、[List.Min](/powerquery-m/list-min)、[List.Max](/powerquery-m/list-max)、[List.StandardDeviation](/powerquery-m/list-standarddeviation)、[List.First](/powerquery-m/list-first)、[List.Last](/powerquery-m/list-last)

## <a name="sorting"></a>排序

使用 [Table.Sort](/powerquery-m/table-sort) 对值进行排序。

## <a name="reducing-rows"></a>减少行

保留和删除前几行，保留范围（对应的 M 函数，仅支持计数，而不支持条件：[Table.FirstN](/powerquery-m/table-firstn)、[Table.Skip](/powerquery-m/table-skip)、[Table.RemoveFirstN](/powerquery-m/table-removefirstn)、[Table.Range](/powerquery-m/table-range)、[Table.MinN](/powerquery-m/table-minn)、[Table.MaxN](/powerquery-m/table-maxn)）

## <a name="known-unsupported-functions"></a>不支持的已知函数

| 函数 | 状态 |
| -- | -- |
| Table.PromoteHeaders | 不支持。 可以通过在数据集中设置“第一行作为页眉”来实现相同的结果。 |
| Table.CombineColumns | 这是一个不直接支持的常见方案，但可通过添加一个连接两个给定列的新列来实现。  例如，Table.AddColumn（RemoveEmailColumn、"Name"、each [FirstName] & " " & [LastName]） |
| Table.TransformColumnTypes | 大多数情况下都支持此项。 不支持以下方案：将字符串转换为货币类型，将字符串转换为时间类型，将字符串转换为百分比类型。 |
| Table.NestedJoin | 仅执行联接将导致验证错误。 必须对列进行扩展，才能使其正常工作。 |
| Table.Distinct | 不支持删除重复的行。 |
| Table.RemoveLastN | 不支持删除最后几行。 |
| Table.RowCount | 不支持，但可通过添加包含值 1 的自定义列，然后使用 List.Sum 聚合该列来实现。 支持 Table.Group。 | 
| 行级别错误处理 | 当前不支持行级别错误处理。 例如，若要筛选出列中的非数字值，一种方法是将文本列转换为数字。 无法转换的每个单元都将处于错误状态，需要对其进行筛选。 此方案在向外扩展的 M 中无法进行。 |
| Table.Transpose | 不支持 |
| Table.Pivot | 不支持 |
| Table.SplitColumn | 部分支持 |

## <a name="m-script-workarounds"></a>M 脚本解决方法

### ```SplitColumn```

下面列出了一个备用项，用于按长度和位置拆分

* Table.AddColumn(Source, "First characters", each Text.Start([Email], 7), type text)
* Table.AddColumn(#"Inserted first characters", "Text range", each Text.Middle([Email], 4, 9), type text)

可从功能区中的“提取”选项访问此选项

:::image type="content" source="media/wrangling-data-flow/power-query-split.png" alt-text="Power Query 添加列":::

### ```Table.CombineColumns```

* Table.AddColumn(RemoveEmailColumn, "Name", each [FirstName] & " " & [LastName])

### <a name="pivots"></a>透视

* 从 PQ 编辑器中选择“透视转换”，然后选择透视列

![Power Query 通用透视](media/wrangling-data-flow/power-query-pivot-1.png)

* 接下来，选择值列和聚合函数

![Power Query 透视选择器](media/wrangling-data-flow/power-query-pivot-2.png)

* 单击“确定”时，将看到编辑器中数据已使用透视值进行更新
* 还会看到一条警告消息，指出转换可能不受支持
* 若要修复此警告，使用 PQ 编辑器手动展开透视列表
* 从功能区中选择“高级编辑器”选项
* 手动展开透视值列表
* 将 List.Distinct() 替换为值列表，如下所示：
```
#"Pivoted column" = Table.Pivot(Table.TransformColumnTypes(#"Changed column type 1", {{"genres", type text}}), {"Drama", "Horror", "Comedy", "Musical", "Documentary"}, "genres", "Rating", List.Average)
in
  #"Pivoted column"
```

## <a name="next-steps"></a>后续步骤

了解如何[在 ADF 中创建数据整理 Power Query](wrangling-tutorial.md)。
