---
title: 查询类型
titleSuffix: Azure Cognitive Search
description: 了解认知搜索中支持的查询类型，包括可用文本、筛选器、自动完成和建议、异地搜索、系统查询和文档查找。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/09/2020
ms.openlocfilehash: d1ea2d0ba8ed5850e5d4cd9c06a0b016c4059ca7
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97007851"
---
# <a name="query-types-in-azure-cognitive-search"></a>Azure 认知搜索中的查询类型

在 Azure 认知搜索中，查询是对往返操作的完整规范，其中包含控制查询执行的参数，以及用于重新绘制响应的参数。

## <a name="elements-of-a-request"></a>请求的元素

下面的示例是使用 [REST API 搜索文档](/rest/api/searchservice/search-documents)构造的典型查询。 该示例针对的是[酒店演示索引](search-get-started-portal.md)，它包含常见参数，让你能够了解到查询是什么样子的。

```http
POST https://[service name].search.windows.net/indexes/[index name]/docs/search?api-version=[api-version]
{
    "queryType": "simple" 
    "search": "`New York` +restaurant",
    "searchFields": "Description, Address/City, Tags",
    "select": "HotelId, HotelName, Description, Rating, Address/City, Tags",
    "top": "10",
    "count": "true",
    "orderby": "Rating desc"
}
```

查询始终定向到单个索引的文档集合。 不能联接索引或者创建自定义或临时数据结构作为查询目标。

+ **`queryType`** 设置分析器，该分析器可以是 [默认的简单查询分析器](search-query-simple-examples.md)（最适合用于全文搜索），也可以是 [完整的 Lucene 查询分析器](search-query-lucene-examples.md)（用于正则表达式、邻近搜索、模糊和通配符搜索等高级查询构造）。

+ `search` 提供匹配条件（通常是整个搜索词或短语，但往往带有布尔运算符）。 包含单个独立字词的查询称为字词查询。 由括在引号中的多个部分组成的查询称为短语查询。 搜索可以不用定义（如 `search=*` 中所示），但如果没有要匹配的条件，则结果集由任意选定的文档组成。

+ **`searchFields`** 将查询执行约束为特定的字段。 在索引架构中设置了 *searchable* 属性的任何字段都适合指定此参数。

还可通过查询中包含的参数来调整响应：

+ `select` 指定要在响应中返回哪些字段。 仅可在 select 语句中使用索引内标记为“可检索”的字段。

+ `top` 返回指定数目的最匹配的文档。 在本例中，仅返回 10 个命中项。 你可使用 top 和 skip（未显示）分页显示结果。

+ `count` 指出整体上整个索引中多少文档匹配，该数目可能比返回的数目多。 

+ 如果你想要按值（例如排名或位置）对结果分类，则使用 `orderby`。 否则，默认使用相关性分数对结果进行排名。

> [!Tip]
> 在编写任何代码之前，你都可使用查询工具来学习语法并用不同的参数进行试验。 最快捷的方法是使用内置门户工具 - [搜索浏览器](search-explorer.md)。
>
> 如果按照这份[关于创建酒店演示索引的快速入门](search-get-started-portal.md)操作，则可将此查询字符串粘贴到浏览器的搜索栏中来运行你的第一个查询：`search=+"New York" +restaurant&searchFields=Description, Address/City, Tags&$select=HotelId, HotelName, Description, Rating, Address/City, Tags&$top=10&$orderby=Rating desc&$count=true`

### <a name="how-field-attributes-in-an-index-determine-query-behaviors"></a>索引中的字段特性如何确定查询行为

索引设计和查询设计在 Azure 认知搜索中紧密耦合。 需要提前知道的一个重要事实是，包含每个字段中属性的索引架构确定了可以生成的查询类型。 

字段中的索引属性设置允许的操作 - 字段在索引中是否可搜索、在结果中是否可检索、是否可排序、是否可筛选，等等。 在示例查询字符串中，只有 `"$orderby": "Rating"` 可以正常工作，因为 Rating 字段在索引架构中标记为 *sortable*。 

![酒店示例的索引定义](./media/search-query-overview/hotel-sample-index-definition.png "酒店示例的索引定义")

以上屏幕截图是酒店示例的索引属性的部分列表。 可在门户中查看整个索引架构。 有关索引属性的详细信息，请参阅[创建索引 REST API](/rest/api/searchservice/create-index)。

> [!Note]
> 某些查询功能在索引范围启用，而不是按字段启用。 这些功能包括：[同义词映射](search-synonyms.md)、[自定义分析器](index-add-custom-analyzers.md)、[建议器构造（用于自动完成和建议的查询）](index-add-suggesters.md)、[排名结果的评分逻辑](index-add-scoring-profiles.md)。

## <a name="choose-a-parser-simple--full"></a>选择一个分析器：简单 | 完整

Azure 认知搜索提供了两个查询分析器之间的选择，用于处理典型查询和专用查询。 使用简单分析器的请求是通过[简单查询语法](query-simple-syntax.md)构建的。由于在自由格式文本查询中具有速度和效率优势，这种语法已选作默认语法。 此语法支持多种常用的搜索运算符，包括 AND、OR、NOT、短语、后缀和优先运算符。

在将 `queryType=full` 添加到请求时所启用的[完整 Lucene 查询语法](query-Lucene-syntax.md#bkmk_syntax)公开作为 [Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) 的一部分开发的、已被广泛采用的且富有表达能力的查询语言。 完整语法扩展了简单语法。 为简单语法编写的任何查询在完整 Lucene 分析器下运行。 

以下示例演示了一个要点：采用不同 queryType 设置的同一个查询会产生不同的结果。 在第一个查询中，`historic` 后面的 `^3` 被视为搜索字词的一部分。 此查询的排名靠前的结果是 "Marquis Plaza & 套件"，其说明中有 *海洋* 。

```http
queryType=simple&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

使用完整 Lucene 分析器的同一查询会将 `^3` 解释为字段内字词提升器。 切换分析器会更改排名，并将包含 *historic* 一词的结果移到最前面。

```http
queryType=full&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>查询类型

Azure 认知搜索支持广泛的查询类型。 

| 查询类型 | 使用情况 | 示例和详细信息 |
|------------|--------|-------------------------------|
| 自由格式文本搜索 | 搜索参数和任一分析器| 全文搜索在索引中所有可搜索字段内扫描一个或多个字词，其工作原理与 Google 或必应等搜索引擎相同。 简介中的示例属于全文搜索。<br/><br/>全文搜索使用标准 Lucene 分析器进行词法分析 (默认情况下) 为小写的所有字词，删除 "the" 之类的停止词。 您可以用 [非英语分析器](index-add-language-analyzers.md#language-analyzer-list) 或用于修改词法分析的 [专用语言不可知分析器](index-add-custom-analyzers.md#AnalyzerTable) 替代默认值。 例如，将整个字段内容视为单个标记的[关键字](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)。 此分析器可用于邮政编码、ID 和某些产品名称等数据。 | 
| 筛选的搜索 | [OData 筛选表达式](query-odata-filter-orderby-syntax.md)和任一分析器 | 筛选器查询对索引中的所有可筛选字段计算布尔表达式  。 与搜索不同，筛选器查询与字段内容完全匹配，包括字符串字段的大小写区分。 另一项差别在于，筛选器查询以 OData 语法表示。 <br/>[筛选表达式示例](search-query-simple-examples.md#example-3-filter-queries) |
| 地理搜索 | 字段中的 [Edm.GeographyPoint 类型](/rest/api/searchservice/supported-data-types)、筛选表达式和任一分析器 | 存储在字段中的具有 Edm.GeographyPoint 的坐标用于“附近查找”或基于地图的搜索控件。 <br/>[地理搜索示例](search-query-simple-examples.md#example-5-geo-search)|
| 范围搜索 | 筛选表达式和简单分析器 | 在 Azure 认知搜索中，范围查询是使用筛选器参数生成的。 <br/>[范围筛选器示例](search-query-simple-examples.md#example-4-range-filters) | 
| [字段化搜索](query-lucene-syntax.md#bkmk_fields) | 搜索参数和完整分析器 | 针对单个字段生成复合查询表达式。 <br/>[字段化搜索示例](search-query-lucene-examples.md#example-2-fielded-search) |
| [自动完成或建议的结果](search-autocomplete-tutorial.md) | 自动完成或建议参数 | 基于搜索即用体验中的部分字符串执行的替代查询窗体。 你可以使用自动完成和建议一起使用，也可以单独使用。 |
| [模糊搜索](query-lucene-syntax.md#bkmk_fuzzy) | 搜索参数和完整分析器 | 匹配具有类似构造或拼写方式的字词。 <br/>[模糊搜索示例](search-query-lucene-examples.md#example-3-fuzzy-search) |
| [邻近搜索](query-lucene-syntax.md#bkmk_proximity) | 搜索参数和完整分析器 | 查找在文档中相互靠近的字词。 <br/>[邻近搜索示例](search-query-lucene-examples.md#example-4-proximity-search) |
| [术语提升](query-lucene-syntax.md#bkmk_termboost) | 搜索参数和完整分析器 | 如果某个文档包含提升的字词（相对于其他未提升的字词），则提高其排名。 <br/>[字词提升示例](search-query-lucene-examples.md#example-5-term-boosting) |
| [正则表达式搜索](query-lucene-syntax.md#bkmk_regex) | 搜索参数和完整分析器 | 基于正则表达式的内容进行匹配。 <br/>[正则表达式示例](search-query-lucene-examples.md#example-6-regex) |
|  [通配符或前缀搜索](query-lucene-syntax.md#bkmk_wildcard) | 搜索参数和完整分析器 | 基于前缀和波浪符 (`~`) 或单个字符 (`?`) 进行匹配。 <br/>[通配符搜索示例](search-query-lucene-examples.md#example-7-wildcard-search) |

## <a name="next-steps"></a>后续步骤

使用门户或其他工具（如 Postman 或 Visual Studio Code）或 Sdk 之一来更深入地浏览查询。 以下链接将帮助你入门。

+ [搜索资源管理器](search-explorer.md)
+ [如何在 .NET 中进行查询](./search-get-started-dotnet.md)
+ [如何在 REST 中进行查询](./search-get-started-powershell.md)