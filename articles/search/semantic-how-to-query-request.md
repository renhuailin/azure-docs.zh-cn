---
title: 创建语义查询
titleSuffix: Azure Cognitive Search
description: 设置语义查询类型，将深度学习模型附加到查询处理，将意图和上下文推断作为搜索排名和相关性的一部分。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/21/2021
ms.openlocfilehash: e3ae63b202d826e48789bd8d15a197048d5566b7
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122178331"
---
# <a name="create-a-query-that-invokes-semantic-ranking-and-returns-semantic-captions"></a>创建调用语义排名并返回语义标题的查询

> [!IMPORTANT]
> 根据[补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，语义搜索为预览版。 它可通过 Azure 门户、预览版 REST API 和 beta 版本的 SDK 获得。 这些功能将计费。 有关详细信息，请参阅[可用性和定价](semantic-search-overview.md#availability-and-pricing)。

语义搜索是 Azure 认知搜索中的一项高级功能，可对结果集调用语义排名算法并返回语义标题（和可选的[语义式答案](semantic-answers.md)），并突出显示最相关的术语和短语。 在使用“语义”查询类型进行表述的查询中，将返回标题和答案。

标题和答案会从搜索文档的文本中逐字提取。 语义子系统确定哪部分内容具有标题或答案的特征，但不编写新的句子或短语。 出于此原因，包含解释或定义的内容最适用于语义搜索。

## <a name="prerequisites"></a>先决条件

+ 标准层（S1、S2、S3）中的认知搜索服务，位于以下区域之一：美国中北部、美国西部、美国西部 2、美国东部 2、欧洲北部、欧洲西部。 如果在其中一个区域中拥有一个现有的 S1 或更高层级的服务，则无需创建新服务即可注册预览。

+ [注册预览](https://aka.ms/SemanticSearchPreviewSignup)。 预期周转时间大约为两个工作日。

+ 现有搜索索引，其中包含使用[支持的语言](/rest/api/searchservice/preview-api/search-documents#queryLanguage)的内容。 语义搜索最适用于信息性或描述性内容。

+ 用于发送查询的搜索客户端。

  搜索客户端必须支持查询请求的预览版 REST API。 你可以使用 [Postman](search-get-started-rest.md)、[Visual Studio Code](search-get-started-vs-code.md) 或代码对预览版 API 发出 REST 调用。 还可以使用 Azure 门户中的[搜索资源管理器](search-explorer.md)提交语义查询。 你还可以使用 [Azure.Search.Documents 11.3.0-beta.2](https://www.nuget.org/packages/Azure.Search.Documents/11.3.0-beta.2)。

+ [查询请求](/rest/api/searchservice/preview-api/search-documents)必须包括 `queryType=semantic` 和本文中所述的其他参数。

## <a name="whats-a-semantic-query-type"></a>什么是语义查询类型？

在认知搜索中，查询是一个参数化请求，它确定查询处理和响应的形状。 *语义查询*[拥有调用语义重新排序模型的参数](#query-using-rest)，该模型可评估匹配结果的上下文和含义，将更相关的匹配项提升到顶部，并返回语义答案和标题。

以下请求是一个典型的最简单语义查询（不带答案）。

```http
POST https://[service name].search.windows.net/indexes/[index name]/docs/search?api-version=2020-06-30-Preview      
{    
    "search": " Where was Alan Turing born?",    
    "queryType": "semantic",  
    "searchFields": "title,url,body",  
    "queryLanguage": "en-us"  
}
```

与认知搜索中的所有查询一样，请求以单个索引的文档集合为目标。 而且，语义查询会经历与非语义查询相同的解析、分析、扫描和评分序列。 

不同之处在于相关性和评分。 正如在此预览版中定义的那样，语义查询是指其结果通过语义语言模型重新排名的查询，它提供了一种方法来显示语义排名程序认为最相关的匹配项，而不是显示由默认相似性排名算法分配的分数。

只有最初结果中的前 50 个匹配项可以进行语义排序，并且所有结果都在响应中包含标题。 还可以在请求上指定 `answer` 参数，以提取潜在的答案。 有关更多信息，请参见[语义答案](semantic-answers.md)。

## <a name="query-in-azure-portal"></a>Azure 门户中的查询

[搜索资源管理器](search-explorer.md)已更新为包含用于语义查询的选项。 完成以下步骤后，这些选项将在门户中可见：

1. 在启用了预览的搜索服务上，使用该语法打开门户：`https://portal.azure.com/?feature.semanticSearch=true`。

1. 单击“概览”页顶部的“搜索资源管理器”。

1. 选择包含使用[支持的语言](/rest/api/searchservice/preview-api/search-documents#queryLanguage)的内容的索引。

1. 在“搜索资源管理器”中，设置启用语义查询、searchFields 和拼写更正的查询选项。 你还可以将所需的查询参数粘贴到查询字符串中。

:::image type="content" source="./media/semantic-search-overview/search-explorer-semantic-query-options.png" alt-text="搜索浏览器中的查询选项" border="true":::

## <a name="query-using-rest"></a>使用 REST 的查询

使用[搜索文档（REST 预览版）](/rest/api/searchservice/preview-api/search-documents)以编程方式表述请求。 响应会自动包括标题和突出显示。 如果在响应中需要拼写更正或答案，请将 `speller` 或  **添加到请求中。`answers`**

下面的示例使用 [hotels-sample-index](search-get-started-portal.md) 创建具有拼写检查、语义答案和标题的语义查询请求：

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview      
{
    "search": "newer hotel near the water with a great restaurant",
    "queryType": "semantic",
    "queryLanguage": "en-us",
    "searchFields": "HotelName,Category,Description",
    "speller": "lexicon",
    "answers": "extractive|count-3",
    "highlightPreTag": "<strong>",
    "highlightPostTag": "</strong>",
    "select": "HotelId,HotelName,Description,Category",
    "count": true
}
```

下表汇总了语义查询中使用的参数。 如需查询中所有参数的列表，请参阅[搜索文档（REST 预览）](/rest/api/searchservice/preview-api/search-documents)

| 参数 | 类型 | 说明 |
|-----------|-------|-------------|
| queryType | 字符串 | 有效值包括简单值、完整值和语义值。 语义查询需要“语义”值。 |
| queryLanguage | 字符串 | 语义查询必须提供。 指定的词典同样适用于语义排名、标题、答案和拼写检查。 有关详细信息，请参阅[支持的语言（REST API 参考）](/rest/api/searchservice/preview-api/search-documents#queryLanguage)。 |
| searchFields | 字符串 | 可搜索字段的逗号分隔列表。 指定进行语义排名的字段，将从这些字段中提取标题和答案。 </br></br>与简单查询类型和完整查询类型不同，字段的列出顺序决定了优先级。 有关详细的使用说明，请参阅[步骤 2：设置 searchFields](#searchfields)。 |
| speller | 字符串 | 可选参数，不特定于语义查询，用于在拼写错误的字词到达搜索引擎之前对其进行更正。 有关详细信息，请参阅[向查询添加拼写更正](speller-how-to-add.md)。 |
| answers |字符串 | 可选字段，用于指定结果中是否包含语义答案。 目前只实现了“extractive”。 可以将答案配置为最多返回 10 个。 默认值为 1。 此示例显示了三个答案的计数：`extractive\|count-3`。 有关更多信息，请参见[返回语义答案](semantic-answers.md)。|

### <a name="formulate-the-request"></a>表述请求

本部分逐步介绍查询表述。

#### <a name="step-1-set-querytype-and-querylanguage"></a>步骤 1：设置 queryType 和 queryLanguage

向其余部分添加以下参数。 这两个参数都是必需的。

```json
"queryType": "semantic",
"queryLanguage": "en-us",
```

queryLanguage 必须是[支持的语言](/rest/api/searchservice/preview-api/search-documents#queryLanguage)，并且必须与分配给索引架构中的字段定义的任何[语言分析器](index-add-language-analyzers.md)一致。 例如，你使用法语语言分析器（例如“fr”或“fr”）为法语字符串编制索引，则 queryLanguage 也应为法语的语言变体。

在查询请求中，如果还使用[拼写更正](speller-how-to-add.md)，则设置的 queryLanguage 同样适用于拼写检查器、答案和标题。 对于单个部分，不存在重写。 拼写检查支持的[语言更少](speller-how-to-add.md#supported-languages)，因此，如果使用该功能，必须将 queryLanguage 设置为该列表中的一种语言。

尽管搜索索引中的内容可以用多种语言撰写，但查询输入很可能用一种语言。 搜索引擎不检查 queryLanguage、语言分析器以及撰写内容所用的语言的兼容性，因此请确保对查询范围进行相应的限定以避免产生不正确的结果。

<a name="searchfields"></a>

#### <a name="step-2-set-searchfields"></a>步骤 2：设置 searchFields

将 searchFields 添加到请求。 此为可选操作，但强烈建议添加。

```json
"searchFields": "HotelName,Category,Description",
```

searchFields 参数用于识别要对查询进行“语义相似性”评估的段落。 对于预览，不建议将 searchFields 留空，因为模型需要提示哪些字段是需要处理的最重要的字段。

与其他参数相比，searchFields 不是新参数。 对于简单或完整的 Lucene 查询，你可能已在现有代码中使用了 searchFields。 如果是这样，请重新考虑参数的使用方式，以便可以在切换到一种语义查询类型时检查字段顺序。

##### <a name="allowed-data-types"></a>允许的数据类型

设置 searchFields 时，仅选择以下[受支持的数据类型的字段](/rest/api/searchservice/supported-data-types)。 如果包含无效字段，不会出现错误，但这些字段不会用于语义排名。

| 数据类型 | hotels-sample-index 中的示例 |
|-----------|----------------------------------|
| Edm.String | HotelName, Category, Description |
| Edm.ComplexType | Address.StreetNumber, Address.City, Address.StateProvince, Address.PostalCode |
| 集合 (Edm.String) | 标记（逗号分隔的一系列字符串） |

##### <a name="order-of-fields-in-searchfields"></a>searchFields 中字段的顺序

字段顺序至关重要，因为语义排名器会限制它可以处理的内容量，同时仍提供合理的响应时间。 列表开始部分字段中的内容更有可能包含在内；如果达到最大限制，则可能会截断列表末尾的内容。 有关详细信息，请参阅[语义排名期间的预处理](semantic-ranking.md#pre-processing)。

+ 如果只指定一个字段，则选择可在其中找到语义查询答案的描述性字段，例如文档的主要内容。 

+ 对于两个或更多个 searchFields 字段：

  + 第一个字段应保持简洁（例如标题或名称），最好使用不超过 25 个词的字符串。

  + 如果索引的一个 URL 字段是用户可读的（如 `www.domain.com/name-of-the-document-and-other-details`）而不是以计算机为中心的（如 `www.domain.com/?id=23463&param=eis`），则将其放在列表的第二位（如果没有简洁标题字段，则放在第一位）。

  + 上述字段后跟可在其中找到语义查询答案的其他描述性字段，例如文档的主要内容。

#### <a name="step-3-remove-or-bracket-query-features-that-bypass-relevance-scoring"></a>步骤 3：删除或用括号括起绕过相关性评分的查询功能

认知搜索中的一些查询功能不进行相关性评分，有些甚至会完全绕过全文搜索引擎。 如果查询逻辑包含以下功能，你不会得到结果的相关性分数或语义排名：

+ 筛选器、模糊搜索查询和正则表达式会循环访问未切分的文本，并在内容中扫描逐字匹配项。 上述所有查询形式的搜索分数都是统一的 1.0，不会为语义排名提供有意义的输入。

+ 对特定字段的排序（orderBy 子句）也会替代搜索分数和语义分数。 假设将语义分数用于对结果进行排序（包括显式排序逻辑），则会导致返回 HTTP 400 错误。

#### <a name="step-4-add-answers"></a>步骤 4：添加答案

（可选）如果要包含提供答案的其他处理，请添加“答案”。 有关此参数的详细信息，请参阅[如何指定语义答案](semantic-answers.md)。

```json
"answers": "extractive|count-3",
```

答案（和标题）是在 searchFields 列出的字段内找到的段落中提取的。 因此，你需要在 searchFields 中包含内容丰富的字段，以便在响应中获得最佳答案。 不能保证每个请求都能得到答案。 查询必须类似于问题，并且内容必须包含类似于答案的文本。

#### <a name="step-5-add-other-parameters"></a>步骤 5：添加其他参数

在请求中设置所需的任何其他参数。 诸如 [speller](speller-how-to-add.md)、[select](search-query-odata-select.md) 和 count 等参数会提高请求的质量和响应的可读性。

```json
"speller": "lexicon",
"select": "HotelId,HotelName,Description,Category",
"count": true,
"highlightPreTag": "<mark>",
"highlightPostTag": "</mark>",
```

突出显示样式应用于响应中的标题。 可以使用默认样式，也可以选择性地自定义应用于标题的突出显示样式。 标题对文档中汇总响应的关键段落应用突出显示格式。 默认值为 `<em>`。 若要指定格式类型（例如，黄色背景），可以设置 highlightPreTag 和 highlightPostTag。

## <a name="query-using-azure-sdks"></a>使用 Azure SDK 进行查询

Azure SDK 的 Beta 版本包括对语义搜索的支持。 因为该 SDK 是 beta 版本，所以没有文档或示例，但是你可以参考上面的“REST API”部分，了解 API 的工作原理。

| Azure SDK | 包裹 |
|-----------|---------|
| .NET | [Azure.Search.Documents 包 11.3.0-beta.2](https://www.nuget.org/packages/Azure.Search.Documents/11.3.0-beta.2)  |
| Java | [com.azure:azure-search-documents 11.4.0-beta.2](https://search.maven.org/artifact/com.azure/azure-search-documents/11.4.0-beta.2/jar)  |
| JavaScript | [azure/search-documents 11.2.0-beta.2](https://www.npmjs.com/package/@azure/search-documents/v/11.2.0-beta.2)|
| Python | [azure-search-documents 11.2.0b3](https://pypi.org/project/azure-search-documents/11.2.0b3/) |

## <a name="evaluate-the-response"></a>评估响应

与所有查询一样，响应由标记为可检索的所有字段组成，或仅仅由 select 参数中列出的那些字段组成。 它包括原始相关性分数，还可能包括计数或批处理结果，具体取决于你的请求的表述方式。

在语义查询中，响应包含更多元素：在语义上排名的新的相关性分数、带有突出显示效果的纯文本标题，以及一个答案（可选）。

在客户端应用中，可以将搜索页面构建为包含一个标题作为匹配项的说明，而不是包含特定字段的全部内容。 这在“搜索结果”页的各个字段过于密集时很有用。

上述示例查询的响应将返回以下匹配项作为首选项。 标题自动返回，包含纯文本和突出显示版本。 示例中省略了答案，因为无法为此特定查询和语料库确定答案。

```json
"@odata.count": 35,
"@search.answers": [],
"value": [
    {
        "@search.score": 1.8810667,
        "@search.rerankerScore": 1.1446577133610845,
        "@search.captions": [
            {
                "text": "Oceanside Resort. Luxury. New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
                "highlights": "<strong>Oceanside Resort.</strong> Luxury. New Luxury Hotel. Be the first to stay.<strong> Bay</strong> views from every room, location near the pier, rooftop pool, waterfront dining & more."
            }
        ],
        "HotelName": "Oceanside Resort",
        "Description": "New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
        "Category": "Luxury"
    },
```

## <a name="next-steps"></a>后续步骤

回忆一下，语义排名和响应是建立在初始结果集的基础之上。 任何能够提高初始结果质量的逻辑都将应用到语义搜索。 下一步，查看有助于推进初始结果的功能，包括影响如何对字符串进行标记的分析器、可以优化结果的计分概要文件以及默认的关联算法。

+ [用于文本处理的分析器](search-analyzers.md)
+ [相似性排名算法](index-similarity-and-scoring.md)
+ [为配置文件评分](index-add-scoring-profiles.md)
+ [语义搜索概述](semantic-search-overview.md)
+ [语义排名算法](semantic-ranking.md)
