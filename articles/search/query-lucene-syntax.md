---
title: Lucene 查询语法
titleSuffix: Azure Cognitive Search
description: 在适用于通配符、模糊搜索、正则表达式和其他高级查询构造的 Azure 认知搜索中使用的完整 Lucene 查询语法的参考。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/08/2021
ms.openlocfilehash: c7cc9ba4cddb21dd68af4a4e3253361e1e3e62fd
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111754882"
---
# <a name="lucene-query-syntax-in-azure-cognitive-search"></a>Azure 认知搜索中的 Lucene 查询语法

创建查询时，可以选择用于专用查询窗体的 [Lucene 查询分析](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)语法：通配符、模糊搜索、邻近搜索、正则表达式。 除了通过 `$filter` 表达式构造的“范围搜索”之外，大部分 Lucene 查询分析器语法都[在 Azure 认知搜索中完整实现](search-lucene-query-architecture.md)。 

若要使用完整的 Lucene 语法，需要将“queryType”设置为“full”，并传入一个针对通配符、模糊搜索或完整语法支持的其他查询形式之一进行了模式化处理的查询表达式。 在 REST 中，查询表达式将在[搜索文档 (REST API)](/rest/api/searchservice/search-documents) 请求的 `search` 参数中提供。

## <a name="example-full-syntax"></a>示例（完整语法）

下面的示例是一个使用完整语法构造的搜索请求。 此特定示例演示了字段内搜索和字词提升。 它查找类别字段中包含“预算”这一字词的酒店。 由于字词提升值 (3)，任何包含短语“recently renovated”的文档排名都会更高。  

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
  "queryType": "full",
  "search": "category:budget AND \"recently renovated\"^3",
  "searchMode": "all"
}
```

`searchMode` 参数虽然不特定于任何查询类型，但在本例中却是相关的。 无论运算符何时出现在查询上，通常都应该设置 `searchMode=all` 以确保匹配所有条件  。  

有关其他示例，请参阅 [Lucene 查询语法示例](search-query-lucene-examples.md)。 有关查询请求和参数（包括 searchMode）的详细信息，请参阅[搜索文档 (REST API)](/rest/api/searchservice/Search-Documents)。

## <a name="syntax-fundamentals"></a><a name="bkmk_syntax"></a> 语法基础  

下面的语法基础适用于所有使用 Lucene 语法的查询。  

### <a name="operator-evaluation-in-context"></a>上下文中的运算符评估

位置决定符号解释为运算符或者解释为字符串中的另一个字符。

例如，Lucene 完整语法中，波浪线 (~) 用于模糊搜索和邻近搜索。 如果放在引用短语之后，则 ~ 调用邻近搜索。 如果放在术语末尾，则 ~ 调用模糊搜索。

该术语中，例如“business~analyst”，字符不评估为运算符。 在此情况下，假设查询是术语或短语查询，则使用[词法分析](search-lucene-query-architecture.md#stage-2-lexical-analysis)的[全文搜索](search-lucene-query-architecture.md)会删除 ~ 并将术语“business~analyst”分为两部分：business 或 analyst。

上面的示例是波形符 (~)，不过相同原则也适用于每个运算符。

### <a name="escaping-special-characters"></a>转义特殊字符

若要使用任何搜索运算符作为搜索文本的一部分，请使用一个反斜杠 (`\`) 作为前缀对该字符进行转义。 例如，若要执行对 `https://` 的通配符搜索（其中 `://` 是查询字符串的一部分），需要指定 `search=https\:\/\/*`。 同样，转义的电话号码模式可能类似于 `\+1 \(800\) 642\-7676`。

需要转义的特殊字符包括下列项：  
`+ - & | ! ( ) { } [ ] ^ " ~ * ? : \ /`  

> [!NOTE]  
> 尽管转义将标记保留在一起，但在编制索引期间，[词法分析](search-lucene-query-architecture.md#stage-2-lexical-analysis)可能会将它们去除。例如，标准 Lucene 分析器会在连字符、空格和其他字符处断开单词。 如果需要在查询字符串中使用特殊字符，则可能需要使用会将它们保留在索引中的分析器。 可供选择的一些项包括 Microsoft 自然[语言分析器](index-add-language-analyzers.md)（它会保留带连字符的单词）和自定义分析器（用于更复杂的模式）。 有关详细信息，请参阅[部分词语、模式和特殊字符](search-query-partial-matching.md)。

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>对 URL 中的不安全及保留字符进行编码

请确保对 URL 中的所有不安全和保留字符进行编码。 例如，“#”是不安全字符，因为它是 URL 中的片段/定位标识符。 如果用于 URL，则该字符必须编码为 `%23`。 由于“&”和“=”在 Azure 认知搜索中分隔参数并指定值，因而是保留字符的示例。 请参阅 [RFC1738：统一资源定位器 (URL)](https://www.ietf.org/rfc/rfc1738.txt) 获取更多详细信息。

不安全字符为 ``" ` < > # % { } | \ ^ ~ [ ]``。 保留字符为 `; / ? : @ = + &`。

## <a name="boolean-operators"></a><a name="bkmk_boolean"></a> 布尔运算符

可以在查询字符串中嵌入布尔运算符以提高匹配的精准率。 除了字符运算符之外，完整语法还支持文本运算符。 始终全部以大写字母指定文本布尔运算符 (AND、OR、NOT)。

|文本运算符 | 字符 | 示例 | 使用情况 |
|--------------|----------- |--------|-------|
| AND | `&`, `+` | `wifi + luxury` | 指定匹配项必须包含的字词。 在此示例中，查询引擎将查找同时包含 `wifi` 和 `luxury` 的文档。 加号字符 (`+`) 用于所需字词。 例如，`+wifi +luxury` 规定两个术语必须出现在单个文档的某个字段中。|
| OR | `|` | `wifi | luxury` | 找到任一字词就意味着找到匹配项。 在此示例中，查询引擎会返回包含 `wifi` 和/或 `luxury` 的文档的匹配项。 由于 OR 是默认连接运算符，因此也可以省略，这样 `wifi luxury` 等同于 `wifi | luxury`。|
| NOT | `!`, `-` | `wifi –luxury` | 返回不包含该字词的文档的匹配项。 例如：`wifi –luxury` 将搜索包含字词 `wifi` 但不包含 `luxury` 的文档。 <br/><br/>查询请求中的 `+` 参数控制具有 NOT 运算符的字词是通过 AND 运算符还是通过 OR 运算符与查询中的其他字词组合到一起（假定其他字词没有 `searchMode` 或 `|` 运算符）。 有效值包括 `any` 或 `all`。  <br/><br/>`searchMode=any` 通过包含更多结果来提高查询的查全率，且默认情况下 `-` 会被解释为“OR NOT”。 例如，`wifi -luxury` 将匹配包含 `wifi` 词条或不包含 `luxury` 词条的文档。  <br/><br/>`searchMode=all` 通过包含更少结果来提高查询的查准率，且默认情况下“-”会被解释为“AND NOT”。 例如，`wifi -luxury` 将匹配包含 `wifi` 词条且不包含“luxury”词条的文档。 这对于 `-` 运算符来说可能是更直观的行为。 因此，如果想要优化搜索的查准率（而非查全率），且  用户在搜索中频繁使用 `-` 运算符，则应考虑使用 `searchMode=all` 而不是 `searchMode=any`。<br/><br/>在决定 `searchMode` 设置时，请考虑不同应用程序中的查询的用户交互模式。 搜索信息的用户更有可能在查询中包含运算符，相对而言，电子商务网站具有更多的内置导航结构。 |

##  <a name="fielded-search"></a><a name="bkmk_fields"></a> 字段化搜索

可以使用 `fieldName:searchExpression` 语法定义字段化搜索操作，其中的搜索表达式可以是单个词，也可以是一个短语，或者是括号中的更复杂的表达式，可以选择使用布尔运算符。 下面是部分示例：  

- 流派：爵士乐无历史记录  

- 艺术家：（“Miles Davis”、“John Coltrane”）

如果想要两个字符串评估为单个实体，请务必将多个字符串放置在引号内，正如这个在 `artists` 字段中搜索两个不同艺术家的情况一样。  

`fieldName:searchExpression` 中指定的字段必须是 `searchable` 字段。  有关如何在字段定义中使用索引属性的详细信息，请参阅[创建索引](/rest/api/searchservice/create-index)。  

> [!NOTE]
> 使用字段化搜索表达式时，不需使用 `searchFields` 参数，因为每个字段化搜索表达式都有一个显式指定的字段名称。 但是，如果需要运行查询，则仍可使用 `searchFields` 参数，其中的某些部分局限于特定字段，其余部分可以应用到多个字段。 例如，查询 `search=genre:jazz NOT history&searchFields=description` 只将 `jazz` 匹配到 `genre` 字段，而它则会将 `NOT history` 与 `description` 字段匹配。 在 `fieldName:searchExpression` 中提供的字段名称始终优先于 `searchFields` 参数，这就是在此示例中我们不需在 `searchFields` 参数中包括 `genre` 的原因。

##  <a name="fuzzy-search"></a><a name="bkmk_fuzzy"></a> 模糊搜索

模糊搜索查找字词中具有类似构造的匹配项，将一个字词最多扩展为符合距离条件（2 或更低）的 50 个字词。 有关详细信息，请参阅[模糊搜索](search-query-fuzzy.md)。

若要进行模糊搜索，请在单个词末尾使用“~”波形符，另附带指定编辑距离的可选参数（0 到 2 [默认] 之间的值）。 例如“blue~”或“blue~1”会返回“blue”、“blues”和“glue”。

模糊搜索只能应用于术语，不能应用于短语，但是你可以在包含多个部分的名称或短语中将波形符单独追加到每个术语。 例如，“Unviersty~ of~ "Wshington~”会与“University of Washington”匹配。
 
##  <a name="proximity-search"></a><a name="bkmk_proximity"></a> 邻近搜索

邻近搜索用于搜索文档中彼此邻近的术语。 在短语末尾插入波形符“~”，后跟创建邻近边界的词数。 例如 `"hotel airport"~5` 将查找文档中彼此相距 5 个字以内的术语“酒店”和“机场”。  

##  <a name="term-boosting"></a><a name="bkmk_termboost"></a> 术语提升

术语提升是指相对于不包含术语的文档，提高包含提升术语的文档排名。 这不同于计分配置文件，因为计分配置文件提升某些字段，而非特定术语。  

以下示例有助于解释这些差异。 假设某个字段中存在提升匹配度的计分概要文件，例如 [musicstoreindex 示例](index-add-scoring-profiles.md#bkmk_ex)中的“流派”  。 术语提升可用于进一步提升高于其他术语的某些搜索词。 例如 `rock^2 electronic` 将提升“流派”字段（高于搜索中其他搜索字段）中包含搜索词的文档。 另外，由于术语提升值 (2)，包含搜索词“rock”的文档的排名要比包含搜索词“electronic”的要高   。  

 若要提升术语，请使用插入符号“^”，并且所搜索术语末尾还要附加提升系数（数字）。 还可以提升短语。 提升系数越高，术语相对于其他搜索词的相关性也越大。 默认情况下，提升系数是 1。 虽然提升系数必须是正数，但可以小于 1（例如 0.20）。  

##  <a name="regular-expression-search"></a><a name="bkmk_regex"></a> 正则表达式搜索  
 正则表达式搜索根据在 Apache Lucene 下有效的模式找到匹配项，如 [RegExp 类](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html)中所述。 在 Azure 认知搜索中，正则表达式包含在正斜杠 `/` 之间。

 例如，若要查找包含“汽车旅馆”或“酒店”的文档，请指定 `/[mh]otel/`。 正则表达式搜索与单个词匹配。

某些工具和语言施加了额外的转义字符要求。 对于 JSON，包含正斜杠的字符串将使用反斜杠进行转义：“microsoft.com/azure/”变成 `search=/.*microsoft.com\/azure\/.*/`，其中 `search=/.* <string-placeholder>.*/` 设置正则表达式，`microsoft.com\/azure\/` 是包含转义后的正斜杠的字符串。

##  <a name="wildcard-search"></a><a name="bkmk_wildcard"></a> 通配符搜索

可将通常可识别的语法用于多个 (`*`) 或单个 (`?`) 字符通配符搜索。 完整的 Lucene 语法支持前缀、中缀和后缀匹配。 

请注意，Lucene 查询分析器支持将这些符号与单个术语一起使用，但不能与短语一起使用。

| 词缀类型 | 描述和示例 |
|------------|--------------------------|
| 前缀 | 术语片段出现在 `*` 或 `?` 之前。  例如，`search=alpha*` 的查询表达式返回“alphanumeric”或“alphabetical”。 简单和完整的语法都支持前缀匹配。 |
| suffix | 术语片段出现在 `*` 或 `?` 之后，使用正斜杠来分隔构造。 例如 `search=/.*numeric./` 返回“alphanumeric”。 |
| 中辍  | 术语片段中包含 `*` 或 `?`。  例如，`search=/.non*al./` 返回“nonnumerical”和“nonsensical”。 |

可以将操作符合并到一个表达式中。 例如，`980?2*` 匹配“98072-1222”和“98052-1234”，其中 `?` 匹配单个（所需的）字符，而 `*` 匹配跟在后面的任意长度的字符。

后缀和中缀匹配需要正则表达式正斜杠 `/` 分隔符。 通常，在编写代码时，不能使用 * 或 ? 符号作为搜索词的第一个字符，或在不含 `/` 的搜索词中。 在某些工具（如 Postman 或 Azure 门户）中，转义是内置的，你通常可以执行一个没有分隔符的查询。

> [!NOTE]  
> 通常，模式匹配很慢，因此你可能需要使用其他方法，例如边缘 n 元标记化，为搜索词中的字符序列创建标记。 使用 N 元语法词汇切分，索引将更大，但查询的执行速度可能更快，具体取决于模式构造和要编制索引的字符串的长度。
>

### <a name="impact-of-an-analyzer-on-wildcard-queries"></a>分析器对通配符查询的影响

在查询分析期间，以前缀、后缀、通配符或正则表达式形式构建的查询将绕过[词法分析](search-lucene-query-architecture.md#stage-2-lexical-analysis)，按原样传递到查询树。 仅当索引包含查询所指定的格式的字符串时，才会查找匹配项。 在大多数情况下，在编制索引期间需要使用一个可以保留字符串完整性的分析器，使部分字词和模式匹配能够成功。 有关详细信息，请参阅 [Azure 认知搜索查询中的部分字词搜索](search-query-partial-matching.md)。

考虑这样一种情况：你可能希望搜索查询“terminate*”返回包含“terminate”、“termination”和“terminates”等术语的结果。

如果你要用 en.lucene（Lucene 英文版）分析器，它将对每个术语应用主动的词干提取。 例如，将“terminate”、“termination”和“terminates”标记到索引中的标记“termi”。 另一方面，根本不会分析使用通配符或模糊搜索的查询中的术语，因此不会有与“terminate*”查询匹配的结果。

另一方面，Microsoft 分析器（在本例中是 en.microsoft 分析器）更高级一些，使用词形还原而不是词干提取。 这意味着所有生成的标记都应该是有效的英语单词。 例如，“terminate”、“terminates”和“termination”在索引中几乎保持完整，对于严重依赖于通配符和模糊搜索的场景，这是更好的选择。

## <a name="scoring-wildcard-and-regex-queries"></a> 对通配符和正则表达式查询评分

Azure 认知搜索使用基于频率的评分 ([TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)) 进行文本查询。 但是，对于术语范围可能很广的通配符和正则表达式查询，则忽略频率因子，以防止排名偏向于比较少见的术语匹配。 通配符和正则表达式搜索对所有匹配项和正则表达式搜索进行相同处理。

## <a name="special-characters"></a>特殊字符

在某些情况下，可能需要搜索特殊字符，如“❤”表情符号或“€”符号。 在此类情况下，请确保所使用的分析器不会筛选掉这些字符。标准分析器会跳过许多特殊字符，将其从索引中排除。

将特殊字符标记化的分析器包括“空格”分析器，该分析器将由空格分隔的任何字符序列视为标记（因此，“❤”字符串会被视为标记）。 另外，诸如 Microsoft 英语分析器（“en.microsoft”）之类的语言分析器会将“€”字符串视为标记。 可以[测试分析器](/rest/api/searchservice/test-analyzer)，看它为给定的查询生成什么标记。

使用 Unicode 字符时，请确保在查询 URL 中正确转义了符号（例如，对于“❤”，将使用转义序列 `%E2%9D%A4+`）。 Postman 会自动执行此转换。  

## <a name="precedence-grouping"></a>优先级（分组）

可以使用圆括号创建子查询，其包括附加说明语句中的运算符。 例如，`motel+(wifi|luxury)` 将搜索包含“motel”术语以及“wifi”或“luxury”（或两者）的文档。

字段分组与之类似，但将分组范围限定为单个字段。 例如，`hotelAmenities:(gym+(wifi|pool))` 在“hotelAmenities”字段中搜索“gym”和“wifi”，或者“gym”和“pool”。  

## <a name="query-size-limits"></a> 查询大小限制

存在对可以发送到 Azure 认知搜索的查询大小的限制。 具体而言，最多可以有 1024 条子句（以 AND、OR 等分隔的表达式）。 此外，查询中任何单个术语的大小限制为大约 32 KB。 如果应用程序以编程方式生成搜索查询，则建议将其设计为不会生成无限大小的查询。  

## <a name="see-also"></a>另请参阅

+ [简单搜索的查询示例](search-query-simple-examples.md)
+ [完整 Lucene 搜索的查询示例](search-query-lucene-examples.md)
+ [搜索文档](/rest/api/searchservice/Search-Documents)
+ [用于筛选器和排序的 OData 表达式语法](query-odata-filter-orderby-syntax.md)   
+ [Azure 认知搜索中的简单查询语法](query-simple-syntax.md)
