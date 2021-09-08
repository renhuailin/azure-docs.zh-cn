---
title: OData 筛选器参考
titleSuffix: Azure Cognitive Search
description: 在 Azure 认知搜索查询中用于创建筛选器表达式的 OData 语言参考和完整语法。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: be51077c5aad6056cf4e28656c0552bc052a7ead
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829908"
---
# <a name="odata-filter-syntax-in-azure-cognitive-search"></a>Azure 认知搜索中的 OData $filter 语法

除了全文搜索字词以外，Azure 认知搜索还可使用 [OData 筛选表达式](query-odata-filter-orderby-syntax.md)将其他条件应用到搜索查询。 本文详细介绍筛选器的语法。 有关筛选器的定义以及如何使用筛选器来实现特定查询方案的其他一般信息，请参阅 [Azure 认知搜索中的筛选器](search-filters.md)。

## <a name="syntax"></a>语法

OData 语言中的筛选器是一个布尔表达式，而该表达式又可以是多种类型的表达式之一，如以下 EBNF（[扩展巴科斯-瑙尔范式](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)）所示：

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
boolean_expression ::=
    collection_filter_expression
    | logical_expression
    | comparison_expression
    | boolean_literal
    | boolean_function_call
    | '(' boolean_expression ')'
    | variable

/* This can be a range variable in the case of a lambda, or a field path. */
variable ::= identifier | field_path
```

下面还提供了交互式语法图：

> [!div class="nextstepaction"]
> [Azure 认知搜索的 OData 语法图](https://azuresearch.github.io/odata-syntax-diagram/#boolean_expression)

> [!NOTE]
> 请参阅[适用于 Azure 认知搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)以获取完整的 EBNF。

布尔表达式的类型包括：

- 使用 `any` 或 `all` 的集合筛选表达式。 这些表达式将筛选条件应用到集合字段。 有关详细信息，请参阅 [Azure 认知搜索中的 OData 集合运算符](search-query-odata-collection-operators.md)。
- 使用 `and`、`or` 和 `not` 运算符合并其他布尔表达式的逻辑表达式。 有关详细信息，请参阅 [Azure 认知搜索中的 OData 逻辑运算符](search-query-odata-logical-operators.md)。
- 使用 `eq`、`ne`、`gt`、`lt`、`ge` 和 `le` 运算符将字段或范围变量与常量值进行比较的比较表达式。 有关详细信息，请参阅 [Azure 认知搜索中的 OData 比较运算符](search-query-odata-comparison-operators.md)。 比较表达式还可用于通过 `geo.distance` 函数比较地理空间坐标之间的距离。 有关详细信息，请参阅 [Azure 认知搜索中的 OData 地理空间函数](search-query-odata-geo-spatial-functions.md)。
- 布尔文本值 `true` 和 `false`。 在以编程方式生成筛选器时，这些常量可能很有用，否则在实践中很少用到它们。
- 布尔函数的调用，包括：
  - `geo.intersects`，用于测试给定的点是否在给定的多边形范围内。 有关详细信息，请参阅 [Azure 认知搜索中的 OData 地理空间函数](search-query-odata-geo-spatial-functions.md)。
  - `search.in`，用于将字段或范围变量与值列表中的每个值进行比较。 有关详细信息，请参阅 [Azure 认知搜索中的 OData `search.in` 函数](search-query-odata-search-in-function.md)。
  - `search.ismatch` 和 `search.ismatchscoring`，用于在筛选器上下文中执行全文搜索操作。 有关详细信息，请参阅 [Azure 认知搜索中的 OData 全文搜索函数](search-query-odata-full-text-search-functions.md)。
- 类型为 `Edm.Boolean` 的字段路径或范围变量。 例如，如果索引中包含名为 `IsEnabled` 的布尔字段，而你希望返回此字段为 `true` 的所有文档，则筛选表达式只需使用名称 `IsEnabled` 即可。
- 括号中的布尔表达式。 使用括号可以帮助显式确定筛选器中的操作顺序。 有关 OData 运算符的默认优先顺序的详细信息，请参阅下一部分。

### <a name="operator-precedence-in-filters"></a>筛选器中的运算符优先顺序

如果编写的筛选表达式未将其子表达式括在括号中，Azure 认知搜索将会根据一组运算符优先顺序规则评估该表达式。 这些规则基于用于合并子表达式的运算符。 下表按从高到低的优先顺序列出了运算符组：

| 组 | 运算符 |
| --- | --- |
| 逻辑运算符 | `not` |
| 比较运算符 | `eq`, `ne`, `gt`, `lt`, `ge`, `le` |
| 逻辑运算符 | `and` |
| 逻辑运算符 | `or` |

上表中优先顺序较高的运算符与其操作数之间的绑定密切性高于其他运算符。 例如，`and` 的优先顺序高于 `or`，而比较运算符的优先顺序又高于两者中的任何一个，因此，以下两个表达式是等效的：

```odata-filter-expr
    Rating gt 0 and Rating lt 3 or Rating gt 7 and Rating lt 10
    ((Rating gt 0) and (Rating lt 3)) or ((Rating gt 7) and (Rating lt 10))
```

`not` 运算符的优先顺序最高 -- 甚至高于比较运算符。 正因如此，你会尝试编写如下所示的筛选器：

```odata-filter-expr
    not Rating gt 5
```

将出现以下错误消息：

```text
    Invalid expression: A unary operator with an incompatible type was detected. Found operand type 'Edm.Int32' for operator kind 'Not'.
```

发生此错误的原因是，运算符仅与 `Edm.Int32` 类型的 `Rating` 字段相关联，而不与整个比较表达式相关联。 解决方法是将 `not` 的操作数括在括号中：

```odata-filter-expr
    not (Rating gt 5)
```

<a name="bkmk_limits"></a>

### <a name="filter-size-limitations"></a>筛选器大小限制

可以发送到 Azure 认知搜索的筛选表达式的大小和复杂性存在限制。 限制大致基于筛选器表达式中的子句数。 一条合理的指导原则是，如果存在数百个子句，则存在超限的风险。 我们建议正确设计应用程序，使之不会生成大小不受限制的筛选器。

> [!TIP]
> 使用 [`search.in` 函数](search-query-odata-search-in-function.md)而不是相等性比较的较长析取可帮助避免超出筛选子句限制，因为一个函数调用算作一个子句。

## <a name="examples"></a>示例

查找至少有一间客房的基本价格低于 200 美元且评分为 4 分或以上的所有酒店：

```odata-filter-expr
    $filter=Rooms/any(room: room/BaseRate lt 200.0) and Rating ge 4
```

查找除“Sea View Motel”以外的自 2010 年以来经过翻修的所有酒店：

```odata-filter-expr
    $filter=HotelName ne 'Sea View Motel' and LastRenovationDate ge 2010-01-01T00:00:00Z
```

查找在 2010 年或以后经过翻修的所有酒店。 日期时间文本包括太平洋标准时间的时区信息：  

```odata-filter-expr
    $filter=LastRenovationDate ge 2010-01-01T00:00:00-08:00
```

查找提供停车位并且所有客房禁止吸烟的所有酒店：

```odata-filter-expr
    $filter=ParkingIncluded and Rooms/all(room: not room/SmokingAllowed)
```

 \- 或 -  

```odata-filter-expr
    $filter=ParkingIncluded eq true and Rooms/all(room: room/SmokingAllowed eq false)
```

查找等级为豪华或包含停车场且评分为 5 分的所有酒店：  

```odata-filter-expr
    $filter=(Category eq 'Luxury' or ParkingIncluded eq true) and Rating eq 5
```

查找至少有一间客房提供“wifi”标记的所有酒店（每间客房的标记存储在 `Collection(Edm.String)` 字段中）：  

```odata-filter-expr
    $filter=Rooms/any(room: room/Tags/any(tag: tag eq 'wifi'))
```

查找提供任何客房的所有酒店：  

```odata-filter-expr
    $filter=Rooms/any()
```

查找不提供客房的所有酒店：

```odata-filter-expr
    $filter=not Rooms/any()
```

查找与给定参考点的距离在 10 公里范围内的所有酒店（其中 `Location` 是 `Edm.GeographyPoint` 类型的字段）：

```odata-filter-expr
    $filter=geo.distance(Location, geography'POINT(-122.131577 47.678581)') le 10
```

查找描述为多边形的给定视区内的所有酒店（其中 `Location` 是 Edm.GeographyPoint 类型的字段）。 多边形必须处于闭合状态，这意味着第一个点集和最后一个点集必须相同。 此外，[点必须以逆时针顺序列出](/rest/api/searchservice/supported-data-types#Anchor_1)。

```odata-filter-expr
    $filter=geo.intersects(Location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')
```

查找“描述”字段为 null 的所有酒店。 如果未曾设置该字段，或者显式将其设置为 null，则该字段为 null：  

```odata-filter-expr
    $filter=Description eq null
```

查找名称为“Sea View motel”或“Budget hotel”的所有酒店。 这些短语包含空格，而空格是默认的分隔符。 可以将单引号中的备用分隔符指定为第三个字符串参数：  

```odata-filter-expr
    $filter=search.in(HotelName, 'Sea View motel,Budget hotel', ',')
```

查找名称为“Sea View motel”或“Budget hotel”并以“|”分隔的所有酒店：  

```odata-filter-expr
    $filter=search.in(HotelName, 'Sea View motel|Budget hotel', '|')
```

查找所有客房具有“wifi”或“浴缸”标记的所有酒店：

```odata-filter-expr
    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub'))
```

在集合中查找短语匹配项，例如标记中的“heated towel racks”或“hairdryer included”。

```odata-filter-expr
    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))
```

查找包含“waterfront”一词的文档。 此筛选器查询与包含 `search=waterfront` 的[搜索请求](/rest/api/searchservice/search-documents)相同。

```odata-filter-expr
    $filter=search.ismatchscoring('waterfront')
```

查找带有“hostel”一词且评分大于或等于 4 分的文档，或带有“motel”一词且评分等于 5 分的文档。 在不使用 `search.ismatchscoring` 函数的情况下无法表达此请求，因为它使用 `or` 将全文搜索与筛选操作合并在一起。

```odata-filter-expr
    $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5
```

查找没有“luxury”一词的文档。

```odata-filter-expr
    $filter=not search.ismatch('luxury')
```

查找包含短语“ocean view”或评分等于 5 分的文档。 `search.ismatchscoring` 查询仅针对 `HotelName` 和 `Description` 字段执行。 仅与析取的第二个子句匹配的文档也将被返回，即 `Rating` 等于 5 分的酒店。 为了清楚地表明这些文档与表达式的任何评分部分都不匹配，它们返回的分数等于零。

```odata-filter-expr
    $filter=search.ismatchscoring('"ocean view"', 'Description,HotelName') or Rating eq 5
```

查找除描述以外，其他位置的“酒店”和“机场”描述内容不超过五个单词，且所有客房都禁止吸烟的酒店。 此查询使用[完整 Lucene 查询语言](query-lucene-syntax.md)。

```odata-filter-expr
    $filter=search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and not Rooms/any(room: room/SmokingAllowed)
```

查找在“说明”字段中包含以字母“lux”开头的单词的文档。 此查询结合使用[前缀搜索](query-simple-syntax.md#prefix-queries)和 `search.ismatch`。

```odata-filter-expr
    $filter=search.ismatch('lux*', 'Description')
```

## <a name="next-steps"></a>后续步骤  

- [Azure 认知搜索中的筛选器](search-filters.md)
- [Azure 认知搜索的 OData 表达式语言概述](query-odata-filter-orderby-syntax.md)
- [Azure 认知搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)
- [搜索文档（Azure 认知搜索 REST API）](/rest/api/searchservice/Search-Documents)