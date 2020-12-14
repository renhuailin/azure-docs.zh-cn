---
title: 查询类型
titleSuffix: Azure Cognitive Search
description: 了解认知搜索中支持的查询类型，包括可用文本、筛选器、自动完成和建议、异地搜索、系统查询和文档查找。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/11/2020
ms.openlocfilehash: 9ce0ab34aac1a3dda823c9270f4eacebfb99166f
ms.sourcegitcommit: ea17e3a6219f0f01330cf7610e54f033a394b459
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2020
ms.locfileid: "97387660"
---
# <a name="querying-in-azure-cognitive-search"></a>在 Azure 中查询认知搜索

Azure 认知搜索提供丰富的查询语言，支持从自由文本搜索到高度指定的查询模式的各种方案。 本文总结了您可以创建的查询类型。

在认知搜索中，查询是指往返操作的完整规范 **`search`** ，其参数既用于通知查询执行，也可用于返回响应。 参数和分析器确定查询请求的类型。 下面的查询示例使用 [搜索文档 (REST API 的) ](/rest/api/searchservice/search-documents)，目标为 [宾馆演示索引](search-get-started-portal.md)。

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
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

查询执行过程中使用的参数：

+ **`queryType`** 设置分析器，该分析器可以是 [默认的简单查询分析器](search-query-simple-examples.md)（最适合用于全文搜索），也可以是 [完整的 Lucene 查询分析器](search-query-lucene-examples.md)（用于正则表达式、邻近搜索、模糊和通配符搜索等高级查询构造）。

+ **`search`** 提供包含或不含运算符的匹配条件，通常为整个词或短语。 在索引架构中设置了 *searchable* 属性的任何字段都适合指定此参数。

+ **`searchFields`** 将查询执行限制为特定的可搜索字段。

用于对响应进行整形的参数：

+ `select` 指定要在响应中返回哪些字段。 仅可在 select 语句中使用索引内标记为“可检索”的字段。

+ `top` 返回指定数目的最匹配的文档。 在本例中，仅返回 10 个命中项。 你可使用 top 和 skip（未显示）分页显示结果。

+ `count` 指出整体上整个索引中多少文档匹配，该数目可能比返回的数目多。 

+ 如果你想要按值（例如排名或位置）对结果分类，则使用 `orderby`。 否则，默认使用相关性分数对结果进行排名。 字段的属性必须是可 *排序* 的，才能成为此参数的候选项。

上面的列表是代表，但并不完整。 有关查询请求上参数的完整列表，请参阅 [搜索文档 (REST API) ](/rest/api/searchservice/search-documents)。

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>查询类型

如果有几个值得注意的异常，则查询请求将循环访问为快速扫描而构建的反转索引，其中可以在任何数量的搜索文档中的任何字段中找到匹配项。 在认知搜索中，查找匹配项的主要方法是全文搜索或筛选器，但也可以实现其他众所周知的搜索体验，如自动完成或地理位置搜索。 本文的其余部分汇总了认知搜索中的查询，并提供了详细信息和示例的链接。

## <a name="full-text-search"></a>全文搜索

如果搜索应用包含收集术语输入的搜索框，则可能是全文搜索支持的查询操作。 全文搜索接受 **`search`** 索引中所有可 *搜索* 字段内的参数中传递的字词或短语。 查询字符串中的可选布尔运算符可指定包含或排除条件。 简单分析器和完全分析器都支持全文搜索。

在认知搜索中，全文搜索基于 Apache Lucene 查询引擎构建。 全文搜索中的查询字符串经过词法分析以提高扫描效率。 分析包含小写的所有术语，删除诸如 "the" 之类的停止词，并将字词减为基元根窗体。 默认分析器为标准 Lucene。

当找到匹配的字词时，查询引擎会重构包含匹配项的搜索文档，按相关性顺序对文档进行排名，并在响应中默认) 返回前 50 (。

如果要实现全文搜索，则了解内容如何进行标记会有助于调试任何查询异常。 如果使用默认的 standard Lucene 以外的分析器查询，则可能需要使用默认标准 Lucene 以外的分析器，以确保索引包含正确的标记。 可以用 [语言分析器](index-add-language-analyzers.md#language-analyzer-list) 或用于修改词法分析的 [专用分析器](index-add-custom-analyzers.md#AnalyzerTable) 来覆盖默认值。 一个示例是将字段的全部内容视为单个标记的 [关键字](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) 。 此分析器可用于邮政编码、ID 和某些产品名称等数据。 有关详细信息，请参阅 [部分术语搜索和带有特殊字符的模式](search-query-partial-matching.md)。

如果预计使用布尔运算符（更有可能是包含大文本块的索引 (内容字段或详细说明) ），请确保使用参数测试查询， **`searchMode=Any|All`** 以评估该设置对布尔值的影响。

## <a name="autocomplete-and-suggested-queries"></a>自动完成和建议的查询

["自动完成" 或建议的结果](search-autocomplete-tutorial.md) 是根据 **`search`** 部分字符串输入触发连续查询请求的替代方法， (后面的每个字符) 在 "搜索即用" 体验中。 **`autocomplete`** **`suggestions`** 如 [本教程](tutorial-csharp-type-ahead-and-suggestions.md)中所述，您可以一起或单独使用和参数，但不能将它们与一起使用 **`search`** 。 已完成的字词和建议的查询均从索引内容派生而来。 引擎绝不会返回索引中不存在的字符串或建议。 有关详细信息，请参阅 [自动完成 (REST API) ](/rest/api/searchservice/autocomplete) 和 [建议 (REST API ](/rest/api/searchservice/suggestions)) 。

## <a name="filter-search"></a>筛选器搜索

筛选器广泛用于包括认知搜索的应用。 在应用程序页上，筛选器通常作为 "用户定向筛选" 的链接导航结构中的方面进行可视化。 筛选器还可以在内部用于公开索引内容的切片。 例如，如果索引包含英语和法语字段，则可以使用一种语言进行筛选。 

您可能还需要筛选器来调用专用查询窗体，如下表中所述。 您可以使用具有未指定搜索 (**`search=*`**) 或包含术语、短语、运算符和模式的查询字符串的筛选器。

| 筛选方案 | 说明 |
|-----------------|-------------|
| 范围筛选器 | 在 Azure 认知搜索中，范围查询是使用筛选器参数生成的。 有关详细信息和示例，请参阅 [范围筛选器示例](search-query-simple-examples.md#example-4-range-filters)。 |
| 地理位置搜索 | 如果可搜索字段为 [GeographyPoint 类型](/rest/api/searchservice/supported-data-types)，则可以为 "查找附近的用户" 或基于地图的搜索控件创建筛选器表达式。 驱动器异地搜索包含坐标的字段。 有关详细信息和示例，请参阅 [异地搜索示例](search-query-simple-examples.md#example-5-geo-search)。 |
| 多面导航 | 当你调用筛选器来响应方面的事件时，分面导航结构会成为用户定向导航的作用 `onclick` 。 在这种情况下，facet 和筛选器将投入手。 如果添加分面导航，将需要筛选器来完成体验。 有关详细信息，请参阅 [如何构建分面筛选器](search-filters-facets.md)。 |

> [!NOTE]
> 在查询处理过程中，不会分析在筛选器表达式中使用的文本。 文本输入假定为逐字区分大小写的字符模式，这种模式在匹配时成功或失败。 筛选器表达式是使用 [OData 语法](query-odata-filter-orderby-syntax.md) 构造的，并传入 **`filter`** 索引中所有可 *筛选* 字段中的参数。 有关详细信息，请参阅 [Azure 认知搜索中的筛选器](search-filters.md)。

## <a name="document-look-up"></a>文档查找

与前面所述的查询窗体不同，此 [方法按 ID 检索单个搜索文档](/rest/api/searchservice/lookup-document)，而不会进行相应的索引搜索或扫描。 仅请求并返回一个文档。 当用户在搜索结果中选择某一项时，检索该文档并填充包含字段的详细信息页是典型的响应，并且文档查找是支持它的操作。

## <a name="advanced-search-fuzzy-wildcard-proximity-regex"></a>高级搜索：模糊、通配符、邻近性、正则表达式

高级查询窗体取决于触发特定查询行为的完整 Lucene 分析器和运算符。

| 查询类型 | 使用情况 | 示例和详细信息 |
|------------|--------|------------------------------|
| [字段化搜索](query-lucene-syntax.md#bkmk_fields) | **`search`**  参数 **`queryType=full`**  | 针对单个字段生成复合查询表达式。 <br/>[字段化搜索示例](search-query-lucene-examples.md#example-2-fielded-search) |
| [模糊搜索](query-lucene-syntax.md#bkmk_fuzzy) | **`search`** 参数 **`queryType=full`** | 匹配具有类似构造或拼写方式的字词。 <br/>[模糊搜索示例](search-query-lucene-examples.md#example-3-fuzzy-search) |
| [邻近搜索](query-lucene-syntax.md#bkmk_proximity) | **`search`** 参数 **`queryType=full`** | 查找在文档中相互靠近的字词。 <br/>[邻近搜索示例](search-query-lucene-examples.md#example-4-proximity-search) |
| [术语提升](query-lucene-syntax.md#bkmk_termboost) | **`search`** 参数 **`queryType=full`** | 如果某个文档包含提升的字词（相对于其他未提升的字词），则提高其排名。 <br/>[字词提升示例](search-query-lucene-examples.md#example-5-term-boosting) |
| [正则表达式搜索](query-lucene-syntax.md#bkmk_regex) | **`search`** 参数 **`queryType=full`** | 基于正则表达式的内容进行匹配。 <br/>[正则表达式示例](search-query-lucene-examples.md#example-6-regex) |
|  [通配符或前缀搜索](query-lucene-syntax.md#bkmk_wildcard) | **`search`** 带有 * *_`~`_* 或的 **`?`** 参数 **`queryType=full`**| 基于前缀和波浪符 (`~`) 或单个字符 (`?`) 进行匹配。 <br/>[通配符搜索示例](search-query-lucene-examples.md#example-7-wildcard-search) |

## <a name="next-steps"></a>后续步骤

有关查询实现的详细说明，请查看每个语法的示例。 如果你不熟悉全文搜索，则更详细地了解查询引擎的功能可能是一个相当不错的选择。

+ [简单查询示例](search-query-simple-examples.md)
+ [生成高级查询的 Lucene 语法查询示例](search-query-lucene-examples.md)
+ [Azure 认知搜索中全文搜索的工作原理](search-lucene-query-architecture.md)