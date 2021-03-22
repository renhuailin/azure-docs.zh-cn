---
title: 创建语义查询
titleSuffix: Azure Cognitive Search
description: 设置语义查询类型，将深度学习模型附加到查询处理，将意图和上下文推断作为搜索排名和相关性的一部分。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/05/2021
ms.openlocfilehash: 7f7a09b9e20b461a8a1e448bf4a7b0747a35fbb1
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2021
ms.locfileid: "102487132"
---
# <a name="create-a-semantic-query-in-cognitive-search"></a>在认知搜索中创建语义查询

> [!IMPORTANT]
> 语义查询类型为公共预览版，可通过预览版 REST API 和 Azure 门户提供。 根据[使用条款补充](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，预览功能按原样提供。 初始预览版发布期间，语义搜索不收取任何费用。 有关详细信息，请参阅[可用性和定价](semantic-search-overview.md#availability-and-pricing)。

本文介绍如何表述使用语义排名并生成语义标题和答案的搜索请求。

语义查询最适用于基于大量文本内容（如 PDF 或包含大量文本的文档）构建的搜索索引。

## <a name="prerequisites"></a>先决条件

+ 标准层（S1、S2、S3）中的搜索服务，位于以下区域之一：美国中北部、美国西部、美国西部 2、美国东部 2、欧洲北部、欧洲西部。 如果在其中一个区域中拥有一个现有的 S1 或更高层级的服务，则无需创建新服务即可请求访问。

+ 访问语义搜索预览：[注册](https://aka.ms/SemanticSearchPreviewSignup)

+ 包含英文内容的现有搜索索引

+ 用于发送查询的搜索客户端

  搜索客户端必须支持查询请求的预览 REST API。 你可以使用已修改的 [Postman](search-get-started-rest.md)、[Visual Studio Code](search-get-started-vs-code.md) 或代码，以便对预览 API 进行 REST 调用。 还可以使用 Azure 门户中的[搜索资源管理器](search-explorer.md)提交语义查询。

+ 使用语义选项和本文中所述的其他参数的[搜索文档](/rest/api/searchservice/preview-api/search-documents)请求。

## <a name="whats-a-semantic-query"></a>什么是语义查询？

在认知搜索中，查询是一个参数化请求，它确定查询处理和响应的形状。 语义查询添加了调用语义重新排序模型的参数，该模型可评估匹配结果的上下文和含义，将更相关的匹配项提升到顶部，并返回语义答案和标题。

以下请求代表基本语义查询（没有答案）。

```http
POST https://[service name].search.windows.net/indexes/[index name]/docs/search?api-version=2020-06-30-Preview      
{    
    "search": " Where was Alan Turing born?",    
    "queryType": "semantic",  
    "searchFields": "title,url,body",  
    "queryLanguage": "en-us"  
}
```

与认知搜索中的所有查询一样，请求以单个索引的文档集合为目标。 而且，和非语义查询一样，语义查询会经历相同的解析、分析和扫描序列。 不同之处在于如何计算相关性。 正如在此预览版本中定义的那样，语义查询是指使用高级算法重新处理其结果的查询，提供了一种方法来显示语义排名程序认为最相关的匹配，而不是由默认相似性排名算法分配的分数。 

只有最初结果中的前 50 个匹配项可以进行语义排序，并且所有匹配项都在响应中包含标题。 还可以在请求上指定 `answer` 参数，以提取潜在的答案。 此模型阐明了针对查询的最多 5 个潜在答案，你可以选择在搜索页面顶部呈现这些答案。

## <a name="query-using-rest-apis"></a>使用 REST API 进行查询

REST API 的完整规范可在[搜索文档（REST 预览）](/rest/api/searchservice/preview-api/search-documents)中找到。

语义查询会自动提供标题和突出显示。 如果希望响应包括答案，可以在请求中添加可选 `answer` 参数。 此参数和查询字符串本身的构造将在响应中生成一个答案。

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

此参数可选，因为如果将其省略，不会出现错误，但强烈建议为标题和答案提供已排序的字段列表。

searchFields 参数用于识别要对查询进行“语义相似性”评估的段落。 对于预览，不建议将 searchFields 留空，因为模型需要提示哪些字段是需要处理的最重要的字段。

searchFields 的顺序非常重要。 如果已在现有的简单或完整的 Lucene 查询中使用 searchFields，请确保在切换到语义查询类型时重新访问此参数。

当指定两个或多个 searchFields 时，请遵循以下准则以确保获得最佳结果：

+ 仅在集合中包含字符串字段和顶级字符串字段。 如果在集合中包含非字符串字段或较低级别的字段，不会出现错误，但这些字段不会用于语义排名。

+ 第一个字段应保持简洁（例如标题或名称），最好不超过 25 个词。

+ 如果索引有一个 URL 字段是文本性质的（用户可读的，如 `www.domain.com/name-of-the-document-and-other-details`）而不是以计算机为中心（如 `www.domain.com/?id=23463&param=eis`），则将其放在列表的第二位（如果没有简洁标题字段，则放在第一位）。

+ 这些字段后跟描述性字段，可在其中找到语义查询答案，比如文档的主要内容。

如果只指定了一个字段，则使用描述性字段，可在其中找到语义查询答案，比如文档的主要内容。 选择提供足够内容的字段。

#### <a name="step-3-remove-orderby-clauses"></a>步骤 3：删除 orderBy 子句

删除任何 orderBy 子句（如果它们存在于现有请求中）。 语义分数用于对结果进行排序，如果包括显式排序逻辑，则返回 HTTP 400 错误。

#### <a name="step-4-add-answers"></a>步骤 4：添加答案

（可选）如果要包含提供答案的其他处理，请添加“答案”。 答案（和标题）是在 searchFields 中列出的字段中找到的段落中阐明的。 请确保在 searchFields 中包含内容丰富的字段，以便在响应中获得最佳答案和标题。

有显式和隐式条件可以产生答案。 

+ 显示条件包括添加“answers=extractive”。 此外，若要指定在整个响应中返回的答案数，请添加“count”后跟一个数字：`"answers=extractive|count=3"`。  默认值为 1。 最大值为 5。

+ 隐式条件包括一个查询字符串构造，它自行提供答案。 “哪个酒店有绿色房间”这样的问题比“拥有豪华内饰的酒店”这样的问题更有可能获得“答案”。 正如你所料，查询不能是未指定的，也不能为 null。

需要注意的一点是，如果查询看起来不像是问题，即使设置了“answers”参数，也会跳过答案处理。

#### <a name="step-5-add-other-parameters"></a>步骤 5：添加其他参数

在请求中设置所需的任何其他参数。 诸如 [speller](speller-how-to-add.md)、[select](search-query-odata-select.md) 和 count 等参数会提高请求的质量和响应的可读性。

还可以自定义应用于标题的突出显示样式。 标题对文档中汇总响应的关键段落应用突出显示格式。 默认值为 `<em>`。 若要指定格式类型（例如，黄色背景），可以设置 highlightPreTag 和 highlightPostTag。

### <a name="review-the-response"></a>检查响应

上述查询的响应将返回以下匹配项作为首选项。 标题自动返回，包含纯文本和突出显示版本。 有关语义响应的详细信息，请参阅[语义排名和响应](semantic-how-to-query-response.md)。

```json
"@odata.count": 29,
"value": [
    {
        "@search.score": 1.8920634,
        "@search.rerankerScore": 1.1091284966096282,
        "@search.captions": [
            {
                "text": "Oceanside Resort. Budget. New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
                "highlights": "<strong>Oceanside Resort.</strong> Budget. New Luxury Hotel. Be the first to stay.<strong> Bay views</strong> from every room, location near the pier, rooftop pool, waterfront dining & more."
            }
        ],
        "HotelId": "18",
        "HotelName": "Oceanside Resort",
        "Description": "New Luxury Hotel.  Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
        "Category": "Budget"
    },
```

### <a name="parameters-used-in-a-semantic-query"></a>语义查询中使用的参数

下表汇总了语义查询中使用的查询参数，以便你可以全面地查看这些参数。 有关所有参数的列表，请参阅[搜索文档（REST 预览）](/rest/api/searchservice/preview-api/search-documents)

| 参数 | 类型 | 说明 |
|-----------|-------|-------------|
| queryType | 字符串 | 有效值包括简单值、完整值和语义值。 语义查询需要“语义”值。 |
| queryLanguage | 字符串 | 语义查询必须提供。 目前只实现了“en-us”。 |
| searchFields | 字符串 | 可搜索字段的逗号分隔列表。 可选，但建议提供。 指定发生语义排名的字段。 </br></br>与简单查询类型和完整查询类型不同，字段的列出顺序决定了优先级。|
| answers |字符串 | 可选字段，指定结果中是否包含语义答案。 目前只实现了“extractive”。 可以将答案配置为最多返回 5 个。 默认值为 1。 此示例显示了三个答案的计数：“extractive\|count3”`。 |

## <a name="query-with-search-explorer"></a>使用搜索浏览器查询

以下查询的目标是内置的酒店示例索引，使用的 API 版本为 2020-06-30-Preview，并在搜索资源管理器中运行。 `$select` 子句将结果限制为几个字段，以便在搜索资源管理器中更轻松地在详细 JSON 中进行扫描。

### <a name="with-querytypesemantic"></a>对于 queryType=semantic

```json
search=nice hotel on water with a great restaurant&$select=HotelId,HotelName,Description,Tags&queryType=semantic&queryLanguage=english&searchFields=Description,Tags
```

前几个结果如下所示。

```json
{
    "@search.score": 0.38330218,
    "@search.rerankerScore": 0.9754053303040564,
    "HotelId": "18",
    "HotelName": "Oceanside Resort",
    "Description": "New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
    "Tags": [
        "view",
        "laundry service",
        "air conditioning"
    ]
},
{
    "@search.score": 1.8920634,
    "@search.rerankerScore": 0.8829904259182513,
    "HotelId": "36",
    "HotelName": "Pelham Hotel",
    "Description": "Stunning Downtown Hotel with indoor Pool. Ideally located close to theatres, museums and the convention center. Indoor Pool and Sauna and fitness centre. Popular Bar & Restaurant",
    "Tags": [
        "view",
        "pool",
        "24-hour front desk service"
    ]
},
{
    "@search.score": 0.95706713,
    "@search.rerankerScore": 0.8538530203513801,
    "HotelId": "22",
    "HotelName": "Stone Lion Inn",
    "Description": "Full breakfast buffet for 2 for only $1.  Excited to show off our room upgrades, faster high speed WiFi, updated corridors & meeting space. Come relax and enjoy your stay.",
    "Tags": [
        "laundry service",
        "air conditioning",
        "restaurant"
    ]
},
```

### <a name="with-querytype-default"></a>对于 queryType（默认值）

为了进行比较，请运行与上面相同的查询，删除 `&queryType=semantic&queryLanguage=english&searchFields=Description,Tags`。 请注意，这些结果中没有 `"@search.rerankerScore"`，并且不同的酒店显示在前三个位置。

```json
{
    "@search.score": 8.633856,
    "HotelId": "3",
    "HotelName": "Triple Landscape Hotel",
    "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
    "Tags": [
        "air conditioning",
        "bar",
        "continental breakfast"
    ]
},
{
    "@search.score": 6.407289,
    "HotelId": "40",
    "HotelName": "Trails End Motel",
    "Description": "Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport.",
    "Tags": [
        "continental breakfast",
        "view",
        "view"
    ]
},
{
    "@search.score": 5.843788,
    "HotelId": "14",
    "HotelName": "Twin Vertex Hotel",
    "Description": "New experience in the Making.  Be the first to experience the luxury of the Twin Vertex. Reserve one of our newly-renovated guest rooms today.",
    "Tags": [
        "bar",
        "restaurant",
        "air conditioning"
    ]
},
```

## <a name="next-steps"></a>后续步骤

回忆一下，语义排名和响应是建立在初始结果集的基础之上。 任何能够提高初始结果质量的逻辑都将应用到语义搜索。 下一步，查看有助于推进初始结果的功能，包括影响如何对字符串进行标记的分析器、可以优化结果的计分概要文件以及默认的关联算法。

+ [用于文本处理的分析器](search-analyzers.md)
+ [认知搜索中的相似性和评分](index-similarity-and-scoring.md)
+ [添加评分配置文件](index-add-scoring-profiles.md)
+ [语义搜索概述](semantic-search-overview.md)
+ [在查询术语中添加拼写检查](speller-how-to-add.md)
