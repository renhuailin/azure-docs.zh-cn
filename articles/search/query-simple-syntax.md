---
title: 简化的查询语法
titleSuffix: Azure Cognitive Search
description: Azure 认知搜索中用于全文搜索查询的简单查询语法的参考文档。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: f679d6fbab57bcbcccc09b722f6b2f670df49eb2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "97516575"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Azure 认知搜索中的简单查询语法

Azure 认知搜索实现两种基于 Lucene 的查询语言：[简单查询分析器](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html)和 [Lucene 查询分析器](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)。 简单分析器更加灵活，并且即使请求撰写得不够完美，它也会尝试对其进行解释。 这种灵活性使其成为了 Azure 认知搜索中查询的默认设置。

简单语法用于在[搜索文档(REST API)](/rest/api/searchservice/search-documents) 请求的 `search` 参数中传递的查询表达式，请不要将其与用于同一请求中的 [`$filter`](search-filters.md) 和 [`$orderby`](search-query-odata-orderby.md) 表达式的 [OData 语法](query-odata-filter-orderby-syntax.md)相混淆。 OData 参数的用于构造查询、对字符串进行转义等操作的语法和规则有所不同。

尽管简单分析器基于 [Apache Lucene 简单查询分析器](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html)类，但认知搜索中的实现排除了模糊搜索。 如果需要[模糊搜索](search-query-fuzzy.md)，请考虑改用另一种可供选择的语法：[完整 Lucene 查询语法](query-lucene-syntax.md)。

## <a name="example-simple-syntax"></a>示例（简单语法）

虽然下面设置了 `queryType`，但它是默认值，除非你是从替代类型中进行还原，否则可以省略它。 下面的示例是针对独立字词的搜索，要求所有匹配文档都包含“pool”。

```http
POST https://{{service-name}}.search.windows.net/indexes/hotel-rooms-sample/docs/search?api-version=2020-06-30
{
  "queryType": "simple",
  "search": "budget hotel +pool",
  "searchMode": "all"
}
```

`searchMode` 参数在此示例中是相关的。 通常情况下，只要布尔运算符出现在查询中，就应设置 `searchMode=all` 以确保匹配所有条件。 否则，可以使用默认的 `searchMode=any`，以便更有利于提高召回率（与提高精准率相比）。

如需其他示例，请参阅[简单查询语法示例](search-query-simple-examples.md)。 有关查询请求和参数的详细信息，请参阅[搜索文档 (REST API)](/rest/api/searchservice/Search-Documents)。

## <a name="keyword-search-on-terms-and-phrases"></a>针对字词和短语的关键字搜索

传递给 `search` 参数的字符串可以包括任何受支持语言中的字词或短语、布尔运算符、优先级运算符、“开头为”查询的通配符或前缀字符、转义符和 URL 编码字符。 `search` 参数是可选的。 在未指定该参数的情况下，搜索（`search=*` 或 `search=" "`）会以任意（未排名）顺序返回前 50 个文档。

+ 字词搜索是针对一个或多个字词的查询，其中的任何字词都被视为一个匹配项。

+ 短语搜索是指搜索用引号 `" "` 引起来的精确短语。 例如，```Roach Motel```（没有引号）会以任何顺序在任何位置搜索包含 ```Roach``` 和/或 ```Motel``` 的文档，而 ```"Roach Motel"```（带引号）则只会匹配包含整个短语并按该顺序排列的文档（词法分析仍然适用）。 

  可能需要在短语搜索中对引号进行转义，具体取决于你的搜索客户端。 例如，在某个 POST 请求的 Postman 中，请求正文中针对 `"Roach Motel"` 的短语搜索应指定为 `"\"Roach Motel\""`。

默认情况下，在 `search` 参数中传递的所有字词或短语都会进行词法分析。 请务必了解所用分析器的词汇切分行为。 通常情况下，当查询结果不是预期的结果时，可以将原因追踪到在查询时对字词进行切分的方式。

包含一个或多个词条的任何文本都被视为查询执行的有效起点。 Azure 认知搜索将匹配包含任何或所有词条的文档，其中包括在分析文本期间发现的任何变体。

尽管听起来很简单，但 Azure 认知搜索中的查询执行的一个方面 *可能会* 产生意外结果，导致搜索结果增加而不是减少，因为更多的词条和运算符被添加到输入字符串中。 这种扩展是否会实际发生取决于是否包含 NOT 运算符（与 `searchMode` 参数设置组合使用，该参数设置决定了如何根据 AND 或 OR 行为解释 NOT）。 有关详细信息，请参阅[布尔运算符](#boolean-operators)下的 NOT 运算符。

## <a name="boolean-operators"></a>布尔运算符

可以在查询字符串中嵌入布尔运算符以提高匹配的精准率。 在简单语法中，布尔运算符是基于字符的。 文本运算符（如 AND 一词）不受支持。

| 字符 | 示例 | 使用情况 |
|----------- |--------|-------|
| `+` | `pool + ocean` | AND 运算。 例如，`pool + ocean` 规定文档必须同时包含这两个词。|
| `|` | `pool | ocean` | 找到其中一个词意味着 OR 运算找到匹配项。 在此示例中，查询引擎会返回包含 `pool` 和/或 `ocean` 的文档的匹配项。 由于 OR 是默认连接运算符，因此也可以省略，这样 `pool ocean` 等同于 `pool | ocean`。|
| `-` | `pool – ocean` | NOT 运算会返回不包含该词的文档的匹配项。 <br/><br/>若要获得 NOT 表达式的预期行为，可考虑对请求设置 `searchMode=all`。 否则，在使用默认设置 `searchMode=any` 的情况下，你会获得 `pool` 的匹配项，以及不包含 `ocean` 的所有文档（数量可能很大）的匹配项。 查询请求中的 `searchMode` 参数控制具有 NOT 运算符的字词是通过 AND 运算符还是通过 OR 运算符与查询中的其他字词组合到一起（假定其他字词没有 `+` 或 `|` 运算符）。 使用 `searchMode=all` 会通过包含较少结果来提高查询的精准率，默认情况下会被解释为“AND NOT”。 <br/><br/>在决定 `searchMode` 设置时，请考虑不同应用程序中的查询的用户交互模式。 搜索信息的用户更有可能在查询中包含运算符，相对而言，电子商务网站具有更多的内置导航结构。 |

<a name="prefix-search"></a>

## <a name="prefix-queries"></a>前缀查询

对于“开头为”查询，请添加后缀运算符 (`*`) 作为字词剩余部分的占位符。 若要添加后缀运算符，前缀查询必须以至少一个字母数字字符开头。

| 字符 | 示例 | 使用情况 |
|----------- |--------|-------|
| `*` | `lingui*` 会匹配“linguistic”或“linguini” | 星号 (`*`) 表示一个或多个任意长度的字符，忽略大小写。  |

与筛选器类似，前缀查询查找完全匹配项。 因此，不存在相关性评分（所有结果的搜索分数均为 1.0）。 请注意，前缀查询可能会很慢，尤其是在索引较大且前缀包含的字符数较少的情况下。 另一种方法（如“边缘 n 元语法标记化”）执行速度可能较快。

简单语法只支持前缀匹配。 对于与一个词的末尾或中间匹配的后缀或中缀匹配，请使用[适用于通配符搜索的完整 Lucene 语法](query-lucene-syntax.md#bkmk_wildcard)。

## <a name="escaping-search-operators"></a>转义搜索运算符  

在简单语法中，搜索运算符包含以下字符：`+ | " ( ) ' \`  

如果其中的任何字符是索引中的令牌的一部分，请在查询中为其添加一个反斜杠 (`\`) 作为前缀对其进行转义。 例如，假设你使用自定义分析器完成了对整个术语的词汇切分，并且索引包含字符串“Luxury+Hotel”。 若要获得此令牌的完全匹配项，请插入转义字符：`search=luxury\+hotel`。

为了让更典型的情况变得简单，此规则有两个不需要进行转义的例外：  

+ 仅当 NOT 运算符 `-` 是空格之后的第一个字符时才需要对其进行转义。 如果 `-` 位于中间（例如在 `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD` 中），则不需要对其进行转义。

+ 仅当后缀运算符 `*` 是空格之前的最后一个字符时才需要对其进行转义。 如果 `*` 位于中间（例如在 `4*4=16` 中），则不需要对其进行转义。

> [!NOTE]  
> 默认情况下，标准分析器会在[词法分析](search-lucene-query-architecture.md#stage-2-lexical-analysis)时删除连字符、空格、& 符和其他字符，并在这些字符处拆分单词。 如果需要在查询字符串中保留特殊字符，则可能需要使用一个分析器将它们保留在索引中。 可供选择的一些项包括 Microsoft 自然[语言分析器](index-add-language-analyzers.md)（它会保留带连字符的单词）和自定义分析器（用于更复杂的模式）。 有关详细信息，请参阅[部分词语、模式和特殊字符](search-query-partial-matching.md)。

## <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>对 URL 中的不安全及保留字符进行编码

请确保对 URL 中的所有不安全字符和保留字符进行编码。 例如，“#”是不安全字符，因为它是 URL 中的片段/定位标识符。 如果用于 URL，则该字符必须编码为 `%23`。 由于“&”和“=”在 Azure 认知搜索中分隔参数并指定值，因而是保留字符的示例。 有关详细信息，请参阅 [RFC1738：统一资源定位器 (URL)](https://www.ietf.org/rfc/rfc1738.txt)。

不安全字符为 ``" ` < > # % { } | \ ^ ~ [ ]``。 保留字符为 `; / ? : @ = + &`。

## <a name="special-characters"></a>特殊字符

在某些情况下，可能需要搜索特殊字符，如“❤”表情符号或“€”符号。 在此类情况下，请确保所使用的分析器不会筛选掉这些字符。标准分析器会跳过许多特殊字符，将其从索引中排除。

将特殊字符标记化的分析器包括“空格”分析器，该分析器将由空格分隔的任何字符序列视为标记（因此，“❤”字符串会被视为标记）。 另外，诸如 Microsoft 英语分析器（“en.microsoft”）之类的语言分析器会将“€”字符串视为标记。 可以[测试分析器](/rest/api/searchservice/test-analyzer)，看它为给定的查询生成什么标记。

使用 Unicode 字符时，请确保在查询 URL 中正确转义了符号（例如，对于“❤”，将使用转义序列 `%E2%9D%A4+`）。 Postman 会自动执行此转换。  

## <a name="precedence-grouping"></a>优先级（分组）

可以使用圆括号创建子查询，其包括附加说明语句中的运算符。 例如，`motel+(wifi|luxury)` 将搜索包含“motel”术语以及“wifi”或“luxury”（或两者）的文档。

## <a name="query-size-limits"></a> 查询大小限制

如果应用程序以编程方式生成搜索查询，则建议将其设计为不会生成无限大小的查询。 

+ 对于 GET，URL 的长度不能超出 8 KB。

+ 对于请求正文包含 `search` 和其他参数（例如 `filter` 和 `orderby`）的 POST（以及任何其他请求），最大大小为 16 MB，其中 `search` 内的子句（由 AND、OR 等运算符分隔的表达式）的最大数量为 1024。 此外，查询中任何单个术语的大小限制为大约 32 KB。 有关详细信息，请参阅 [API 请求限制](search-limits-quotas-capacity.md#api-request-limits)。

## <a name="next-steps"></a>后续步骤

若要以编程方式构造查询，请查看 [Azure 认知搜索中的全文搜索](search-lucene-query-architecture.md)，了解查询处理的各个阶段以及文本分析的影响。

还可以查看以下文章，详细了解查询构造：

+ [简单搜索的查询示例](search-query-simple-examples.md)
+ [完整 Lucene 搜索的查询示例](search-query-lucene-examples.md)
+ [搜索文档 REST API](/rest/api/searchservice/Search-Documents)
+ [Lucene 查询语法](query-lucene-syntax.md)
+ [Filter 和 Select (OData) 表达式语法](query-odata-filter-orderby-syntax.md)