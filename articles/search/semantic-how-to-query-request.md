---
title: 创建语义查询
titleSuffix: Azure Cognitive Search
description: 设置语义查询类型，将深度学习模型附加到查询处理，将意图和上下文推断作为搜索排名和相关性的一部分。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: c33739124092a17acf0590f00b2f9c3c09bf894e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104654656"
---
# <a name="create-a-query-for-semantic-captions-in-cognitive-search"></a>在认知搜索中为语义标题创建查询

> [!IMPORTANT]
> 语义搜索功能目前为公共预览版，可通过预览版 REST API 和 Azure 门户使用。 根据[补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，预览功能按原样提供。 这些功能将计费。 有关详细信息，请参阅[可用性和定价](semantic-search-overview.md#availability-and-pricing)。

本文介绍如何表述使用语义排名并返回语义标题（以及可选的[语义答案](semantic-answers.md)）的搜索请求，并突出显示最相关的字词和短语。 在使用“语义”查询类型进行表述的查询中，将返回标题和答案。

标题和答案会从搜索文档的文本中逐字提取。 语义子系统确定哪部分内容具有标题或答案的特征，但不编写新的句子或短语。 出于此原因，包含解释或定义的内容最适用于语义搜索。

## <a name="prerequisites"></a>先决条件

+ 标准层（S1、S2、S3）中的搜索服务，位于以下区域之一：美国中北部、美国西部、美国西部 2、美国东部 2、欧洲北部、欧洲西部。 如果在其中一个区域中拥有一个现有的 S1 或更高层级的服务，则无需创建新服务即可请求访问。

+ 访问语义搜索预览：[注册](https://aka.ms/SemanticSearchPreviewSignup)

+ 包含英文内容的现有搜索索引

+ 用于发送查询的搜索客户端

  搜索客户端必须支持查询请求的预览 REST API。 你可以使用 [Postman](search-get-started-rest.md)、[Visual Studio Code](search-get-started-vs-code.md) 或代码对预览版 API 发出 REST 调用。 还可以使用 Azure 门户中的[搜索资源管理器](search-explorer.md)提交语义查询。

+ [查询请求](/rest/api/searchservice/preview-api/search-documents)必须包括语义选项和本文中所述的其他参数。

## <a name="whats-a-semantic-query"></a>什么是语义查询？

在认知搜索中，查询是一个参数化请求，它确定查询处理和响应的形状。 语义查询添加了调用语义重新排序模型的参数，该模型可评估匹配结果的上下文和含义，将更相关的匹配项提升到顶部，并返回语义答案和标题。

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

只有最初结果中的前 50 个匹配项可以进行语义排序，并且所有匹配项都在响应中包含标题。 还可以在请求上指定 `answer` 参数，以提取潜在的答案。 有关更多信息，请参见[语义答案](semantic-answers.md)。

## <a name="query-with-search-explorer"></a>使用搜索浏览器查询

[搜索资源管理器](search-explorer.md)已更新为包含用于语义查询的选项。 完成以下步骤后，这些选项将在门户中可见：

1. [注册](https://aka.ms/SemanticSearchPreviewSignup)并将搜索服务加入预览计划

1. 使用以下语法打开门户：`https://portal.azure.com/?feature.semanticSearch=true`

查询选项包括那些可启用语义查询、searchFields 和拼写更正的开关。 你还可以将所需的查询参数粘贴到查询字符串中。

:::image type="content" source="./media/semantic-search-overview/search-explorer-semantic-query-options.png" alt-text="搜索浏览器中的查询选项" border="true":::

## <a name="query-using-rest"></a>使用 REST 的查询

使用[搜索文档（REST 预览版）](/rest/api/searchservice/preview-api/search-documents)以编程方式表述请求。

响应会自动包括标题和突出显示。 如果希望响应包含拼写更正或答案，请在请求中添加可选的 **`speller`** 或 **`answers`** 参数。

下面的示例使用 hotels-sample-index 创建具有语义答案和标题的语义查询请求：

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

下表汇总了语义查询中使用的查询参数，以便你可以全面地查看这些参数。 有关所有参数的列表，请参阅[搜索文档（REST 预览）](/rest/api/searchservice/preview-api/search-documents)

| 参数 | 类型 | 说明 |
|-----------|-------|-------------|
| queryType | 字符串 | 有效值包括简单值、完整值和语义值。 语义查询需要“语义”值。 |
| queryLanguage | 字符串 | 语义查询必须提供。 目前只实现了“en-us”。 |
| searchFields | 字符串 | 可搜索字段的逗号分隔列表。 指定进行语义排名的字段，将从这些字段中提取标题和答案。 </br></br>与简单查询类型和完整查询类型不同，字段的列出顺序决定了优先级。 有关详细的使用说明，请参阅[步骤 2：设置 searchFields](#searchfields)。 |
| speller | 字符串 | 可选参数，不特定于语义查询，用于在拼写错误的字词到达搜索引擎之前对其进行更正。 有关详细信息，请参阅[向查询添加拼写更正](speller-how-to-add.md)。 |
| answers |字符串 | 可选字段，用于指定结果中是否包含语义答案。 目前只实现了“extractive”。 可以将答案配置为最多返回 5 个。 默认值为 1。 此示例显示了三个答案的计数：“extractive\|count3”`。 有关更多信息，请参见[返回语义答案](semantic-answers.md)。|

### <a name="formulate-the-request"></a>表述请求

本部分逐步介绍语义搜索所需的查询参数。

#### <a name="step-1-set-querytype-and-querylanguage"></a>步骤 1：设置 queryType 和 queryLanguage

向其余部分添加以下参数。 这两个参数都是必需的。

```json
"queryType": "semantic",
"queryLanguage": "en-us",
```

queryLanguage 必须与分配给索引架构中的字段定义的任何[语言分析器](index-add-language-analyzers.md)一致。 如果 queryLanguage 为“en-us”，则任何语言分析器也必须是英语变体（“en.microsoft”或“en.lucene”）。 任何与语言无关的分析器（如 keyword 或 simple）都不与 queryLanguage 值冲突。

在查询请求中，如果还使用[拼写更正](speller-how-to-add.md)，则设置的 queryLanguage 同样适用于拼写检查、答案和标题。 对于单个部分，不存在重写。 

尽管搜索索引中的内容可以用多种语言撰写，但查询输入很可能用一种语言。 搜索引擎不检查 queryLanguage、语言分析器以及撰写内容所用的语言的兼容性，因此请确保对查询范围进行相应的限定以避免产生不正确的结果。

<a name="searchfields"></a>

#### <a name="step-2-set-searchfields"></a>步骤 2：设置 searchFields

searchFields 参数用于识别要对查询进行“语义相似性”评估的段落。 对于预览，不建议将 searchFields 留空，因为模型需要提示哪些字段是需要处理的最重要的字段。

searchFields 的顺序非常重要。 如果已在现有代码中使用 searchFields 进行简单或完整的 Lucene 查询，请在切换到语义查询类型时重新访问此参数以检查字段顺序。

对于两个或更多个 searchFields：

+ 仅在集合中包含字符串字段和顶级字符串字段。 如果在集合中包含非字符串字段或较低级别的字段，不会出现错误，但这些字段不会用于语义排名。

+ 第一个字段应保持简洁（例如标题或名称），最好不超过 25 个词。

+ 如果索引的一个 URL 字段是文本性质的（用户可读的，如 `www.domain.com/name-of-the-document-and-other-details`）而不是以计算机为中心的（如 `www.domain.com/?id=23463&param=eis`），则将其放在列表的第二位（如果没有简洁标题字段，则放在第一位）。

+ 这些字段后跟描述性字段，可在其中找到语义查询答案，比如文档的主要内容。

如果只指定了一个字段，则使用可在其中找到语义查询答案的描述性字段，比如文档的主要内容。 

#### <a name="step-3-remove-orderby-clauses"></a>步骤 3：删除 orderBy 子句

删除任何 orderBy 子句（如果它们存在于现有请求中）。 语义分数用于对结果进行排序，如果包括显式排序逻辑，则返回 HTTP 400 错误。

#### <a name="step-4-add-answers"></a>步骤 4：添加答案

（可选）如果要包含提供答案的其他处理，请添加“答案”。 答案（和标题）是在 searchFields 列出的字段内找到的段落中提取的。 请确保在 searchFields 中包含内容丰富的字段，以便在响应中获得最佳答案。 有关更多信息，请参见[如何返回语义答案](semantic-answers.md)。

#### <a name="step-5-add-other-parameters"></a>步骤 5：添加其他参数

在请求中设置所需的任何其他参数。 诸如 [speller](speller-how-to-add.md)、[select](search-query-odata-select.md) 和 count 等参数会提高请求的质量和响应的可读性。

还可以自定义应用于标题的突出显示样式。 标题对文档中汇总响应的关键段落应用突出显示格式。 默认值为 `<em>`。 若要指定格式类型（例如，黄色背景），可以设置 highlightPreTag 和 highlightPostTag。

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
