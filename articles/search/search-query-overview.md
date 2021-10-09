---
title: 查询类型
titleSuffix: Azure Cognitive Search
description: 了解认知搜索中支持的查询类型，包括自定义文本、筛选器、自动完成和建议、地理搜索、系统查询和文档查找。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: b9ca2a4ba836adacb81a82ad59a11c21ffd6c3fb
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129216871"
---
# <a name="querying-in-azure-cognitive-search"></a>Azure 认知搜索中的查询

Azure 认知搜索提供了丰富的查询语言，支持从自定义文本搜索到高度指定的查询模式等各种场景。 本文介绍查询请求以及可以创建哪些类型的查询。

在认知搜索中，查询是往返 `search` 操作的完整规范，其参数既通知查询执行，又形成返回的响应。 参数和分析程序决定查询请求的类型。 下面的查询示例是使用布尔运算符的自定义文本查询，它使用[搜索文档 (REST API)](/rest/api/searchservice/search-documents)，针对 [hotels-sample-index](search-get-started-portal.md) 文档集合。

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "queryType": "simple",
    "searchMode": "all",
    "search": "restaurant +view",
    "searchFields": "HotelName, Description, Address/City, Address/StateProvince, Tags",
    "select": "HotelName, Description, Address/City, Address/StateProvince, Tags",
    "top": "10",
    "count": "true",
    "orderby": "Rating desc"
}
```

查询执行过程中使用的参数包括：

+ **`queryType`** 设置分析器，该分析器可以是 [默认的简单查询分析器](search-query-simple-examples.md)（最适合用于全文搜索），也可以是 [完整的 Lucene 查询分析器](search-query-lucene-examples.md)（用于正则表达式、邻近搜索、模糊和通配符搜索等高级查询构造）。

+ `searchMode` 指定匹配是基于表达式中的“all”条件还是“any”条件。 默认值为“any”。

+ `search` 提供匹配条件（通常是整个搜索词或短语，带或不带运算符）。 在索引架构中设置了 *searchable* 属性的任何字段都适合指定此参数。

+ `searchFields` 将查询执行约束为特定的可搜索字段。 在开发过程中，为选择和搜索使用同一字段列表会很有帮助。 否则，匹配可能是基于你无法在结果中看到的字段值，从而导致不确定返回文档的原因。

用于形成响应的参数：

+ `select` 指定要在响应中返回哪些字段。 仅可在 select 语句中使用索引内标记为“可检索”的字段。

+ `top` 返回指定数目的最匹配的文档。 在本例中，仅返回 10 个命中项。 你可使用 top 和 skip（未显示）分页显示结果。

+ `count` 指出整体上整个索引中多少文档匹配，该数目可能比返回的数目多。 

+ 如果你想要按值（例如排名或位置）对结果分类，则使用 `orderby`。 否则，默认使用相关性分数对结果进行排名。 字段的属性必须设置为“可排序”才能成为此参数的可能值。

以上列表具有代表性，但并不详尽。 有关查询请求参数的完整列表，请参阅[搜索文档 (REST API)](/rest/api/searchservice/search-documents)。

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>查询类型

除了一些值得注意的例外，查询请求会循环访问为快速扫描构建的倒排索引，在该索引中，可以在任何数量的搜索文档的任何字段中找到匹配项。 在认知搜索中，查找匹配项的主要方法是全文搜索或筛选器，但也可以实施其他众所周知的搜索体验，如自动完成或地理位置搜索。 本文的其余部分总结了认知搜索中的查询，并提供指向更多信息和示例的链接。

## <a name="full-text-search"></a>全文搜索

如果你的搜索应用包含一个收集术语输入的搜索框，则全文搜索可能是支持该体验的查询操作。 全文搜索接受索引中所有可搜索字段中的 `search` 参数中传入的搜索词或短语。 查询字符串中的可选布尔运算符可以指定包含或排除条件。 简单的分析程序和完整分析程序都支持全文搜索。

在认知搜索中，全文搜索在 Apache Lucene 查询引擎上构建。 全文搜索中的查询字符串经过词法分析，使扫描更加高效。 分析包括将所有搜索词变为小写，删除停用词（如“the”），并将搜索词精简到原根形式。 默认分析器为 Standard Lucene。

找到匹配搜索词时，查询引擎将重新构建包含匹配项的搜索文档（使用文档键或 ID 组合字段值），并按相关性顺序排列文档，并在响应中返回前 50 项（默认设置），如果指定 `top`，则返回其他数字。

如果要实现全文搜索，了解内容的标记化方式将有助于你调试任何查询异常。 对用连字符连接的字符串或特殊字符的查询可能需要使用默认标准 Lucene 以外的分析器，以确保索引包含正确的令牌。 可将默认设置替代为可以修改词法分析的[语言分析器](index-add-language-analyzers.md#language-analyzer-list)或[专用分析器](index-add-custom-analyzers.md#built-in-analyzers)。 例如，将整个字段内容视为单个令牌的[关键字](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)。 此分析器可用于邮政编码、ID 和某些产品名称等数据。 有关详细信息，请参阅[部分字词搜索和包含特殊字符的模式](search-query-partial-matching.md)。

如果你预计会大量使用布尔运算符（在包含大文本块（内容字段或长描述）的索引中较可能发生这种情况），请确保使用 `searchMode=Any|All` 参数测试查询，以评估该设置对布尔搜索的影响。

## <a name="autocomplete-and-suggested-queries"></a>自动完成和建议的查询

[自动完成或建议的结果](search-add-autocomplete-suggestions.md)是 `search` 的替代方案，它们基于键入时搜索体验中的部分字符串输入（每个字符之后）来触发连续查询请求。 可以同时使用 `autocomplete` 和 `suggestions` 参数，也可以单独使用它们，如[此教程](tutorial-csharp-type-ahead-and-suggestions.md)中所述，但不能将它们用于 `search`  。 已完成的搜索词和建议的查询都派生自索引内容。 引擎永远不会返回索引中不存在的字符串或建议。 有关详细信息，请参阅[自动完成 (REST API)](/rest/api/searchservice/autocomplete) 和[建议 (REST API)](/rest/api/searchservice/suggestions)。

## <a name="filter-search"></a>筛选器搜索

筛选器广泛用于包含认知搜索的应用。 在应用程序页面上，筛选器通常被可视化为用户定向筛选的链接导航结构中的平面。 筛选器还在内部用于公开索引内容的切片。 例如，如果索引包含英语和法语的字段，则你可以使用针对产品类别或语言的筛选器初始化搜索页面。

可能需要使用筛选器来调用专用查询窗体，如下表所述。 可以使用具有未指定搜索 (`search=*`) 的筛选器或具有查询字符串（其中包括搜索词、短语、运算符和模式）的筛选器。

| 筛选场景 | 说明 |
|-----------------|-------------|
| 范围筛选器 | 在 Azure 认知搜索中，范围查询是使用筛选器参数生成的。 有关详细信息和示例，请参阅[范围筛选器示例](search-query-simple-examples.md#example-5-range-filters)。 |
| 地理位置搜索 | 如果可搜索字段为 [Edm.GeographyPoint 类型](/rest/api/searchservice/supported-data-types)，可以为“查找附近”或基于地图的搜索控件创建筛选器表达式。 驱动地理搜索的字段包含坐标。 有关详细信息及示例，请参阅[地理搜索示例](search-query-simple-examples.md#example-6-geo-search)。 |
| 多面导航 | 在[分面导航](search-faceted-navigation.md)树中，用户可以选择构面，这样每次点击都会缩小结果的范围。 每个构面都由筛选器支持，筛选器可排除不再符合条件的文档。 |

> [!NOTE]
> 查询处理过程中不会分析筛选器表达式中使用的文本。 文本输入假定为逐字区分大小写的字符模式，该模式在匹配时要么成功要么失败。 筛选器表达式是使用 [OData 语法](query-odata-filter-orderby-syntax.md)构造的，并传入索引中的所有可筛选字段中的 `filter` 参数中。 有关详细信息，请参阅 [Azure 认知搜索中的筛选器](search-filters.md)。

## <a name="document-look-up"></a>文档查找

与前面描述的查询窗体相比，文档查找[按 ID 检索单个搜索文档](/rest/api/searchservice/lookup-document)，没有相应的索引搜索或扫描。 仅请求并返回一个文档。 当用户在搜索结果中选择项目时，检索文档并使用字段填充详细信息页是典型的响应，文档查找是支持这一点的操作。

## <a name="advanced-search-fuzzy-wildcard-proximity-regex"></a>高级搜索：模糊、通配符、邻近、正则表达式

高级查询窗体取决于触发特定查询行为的 Full Lucene 分析程序和运算符。

| 查询类型 | 使用情况 | 示例和详细信息 |
|------------|--------|------------------------------|
| [字段化搜索](query-lucene-syntax.md#bkmk_fields) | `search` 参数，`queryType=full`   | 针对单个字段生成复合查询表达式。 <br/>[字段化搜索示例](search-query-lucene-examples.md#example-1-fielded-search) |
| [模糊搜索](query-lucene-syntax.md#bkmk_fuzzy) | `search` 参数，`queryType=full`  | 匹配具有类似构造或拼写方式的字词。 <br/>[模糊搜索示例](search-query-lucene-examples.md#example-2-fuzzy-search) |
| [邻近搜索](query-lucene-syntax.md#bkmk_proximity) | `search` 参数，`queryType=full`  | 查找在文档中相互靠近的字词。 <br/>[邻近搜索示例](search-query-lucene-examples.md#example-3-proximity-search) |
| [术语提升](query-lucene-syntax.md#bkmk_termboost) | `search` 参数，`queryType=full`  | 如果某个文档包含提升的字词（相对于其他未提升的字词），则提高其排名。 <br/>[字词提升示例](search-query-lucene-examples.md#example-4-term-boosting) |
| [正则表达式搜索](query-lucene-syntax.md#bkmk_regex) | `search` 参数，`queryType=full`  | 基于正则表达式的内容进行匹配。 <br/>[正则表达式示例](search-query-lucene-examples.md#example-5-regex) |
|  [通配符或前缀搜索](query-lucene-syntax.md#bkmk_wildcard) | `search` 参数与 *`~` 或 `?`，`queryType=full` | 基于前缀和波浪符 (`~`) 或单个字符 (`?`) 进行匹配。 <br/>[通配符搜索示例](search-query-lucene-examples.md#example-6-wildcard-search) |

## <a name="next-steps"></a>后续步骤

如需进一步了解查询实现，请查看每个语法的示例。 如果你不熟悉全文搜索，进一步了解查询引擎的用途可能也是一个不错的选择。

+ [简单查询示例](search-query-simple-examples.md)
+ [生成高级查询的 Lucene 语法查询示例](search-query-lucene-examples.md)
+ [Azure 认知搜索中全文搜索的工作原理](search-lucene-query-architecture.md)git