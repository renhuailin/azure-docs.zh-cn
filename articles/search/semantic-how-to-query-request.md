---
title: 创建语义查询
titleSuffix: Azure Cognitive Search
description: 设置语义查询类型，将深度学习模型附加到查询处理，将意图和上下文推断为搜索排名和相关性的一部分。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 0af868f62f9bc62ee6b4b2a10d16f8eed632b6d3
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679177"
---
# <a name="create-a-semantic-query-in-cognitive-search"></a>在认知搜索中创建语义查询

> [!IMPORTANT]
> 语义查询类型为公共预览版，可通过预览版 REST API 和 Azure 门户提供。 在 [补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)下，预览版功能按原样提供。 初始预览版发布期间，语义搜索不收取任何费用。 有关详细信息，请参阅 [可用性和定价](semantic-search-overview.md#availability-and-pricing)。

本文介绍如何表述使用语义排名的搜索请求，并生成语义标题和答案。

## <a name="prerequisites"></a>先决条件

+ 标准层中的搜索服务 (S1，S2，S3) ，位于以下区域之一：美国中北部、美国西部、美国西部2、美国东部2、北欧西欧。 如果在其中一个区域中拥有一个现有的 S1 或更高的服务，则无需创建新服务即可请求访问。

+ 访问语义搜索预览： [注册](https://aka.ms/SemanticSearchPreviewSignup)

+ 包含英文内容的现有搜索索引

+ 用于发送查询的搜索客户端

  搜索客户端必须支持查询请求的预览 REST Api。 你可以使用已修改的 [Postman](search-get-started-rest.md)、 [Visual Studio Code](search-get-started-vs-code.md)或代码，以便对预览 api 进行 REST 调用。 你还可以使用 Azure 门户中的 [搜索资源管理器](search-explorer.md) 提交语义查询。

+ 使用语义选项和本文中所述的其他参数的 [搜索文档](/rest/api/searchservice/preview-api/search-documents) 请求。

## <a name="whats-a-semantic-query"></a>什么是语义查询？

在认知搜索中，查询是一个参数化请求，用于确定查询处理和响应的形状。 *语义查询* 添加参数，这些参数可调用语义 reranking 算法，该算法可评估匹配结果的上下文和含义，并将更多相关匹配项提升到顶部。

以下请求代表基本语义查询 (没有答案) 。

```http
POST https://[service name].search.windows.net/indexes/[index name]/docs/search?api-version=2020-06-30-Preview      
{    
    "search": " Where was Alan Turing born?",    
    "queryType": "semantic",  
    "searchFields": "title,url,body",  
    "queryLanguage": "en-us",  
}
```

与认知搜索中的所有查询一样，请求以单个索引的文档集合为目标。 而且，语义查询会经历相同的分析、分析和扫描序列作为非语义查询。 不同之处在于如何计算相关性。 正如在此预览版本中定义的那样，语义查询是指使用高级算法重新处理其 *结果* 的查询，提供一种方式来呈现语义 ranker 最相关的匹配项，而不是由默认的相似性排名算法分配的分数。 

只有前50与初始结果的匹配项可以进行语义排名，并在响应中包含所有标题。 （可选）可以 **`answer`** 在请求上指定参数，以便提取潜在的答案。 此模型形成查询的最多5个潜在答案，您可以选择在 "搜索" 页面顶部呈现。

## <a name="query-using-rest-apis"></a>使用 REST Api 进行查询

REST API 的完整规范可在 [ (REST 预览) 的搜索文档 ](/rest/api/searchservice/preview-api/search-documents)中找到。

语义查询适用于 "pollinators 的最佳植物" 或 "如何 fry 鸡蛋" 等开放问题。 如果希望响应包括答案，可以在请求中添加可选 **`answer`** 参数。

下面的示例使用宾馆示例索引创建具有语义答案和标题的语义查询请求：

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

### <a name="formulate-the-request"></a>制订请求

1. 将 "queryType" 设置为 "语义"，将 "queryLanguage" 设置为 "en-us"。 这两个参数都是必需的。

   QueryLanguage 必须与分配给索引架构中的字段定义的任何 [语言分析器](index-add-language-analyzers.md) 一致。 如果 queryLanguage 为 "en-us"，则任何语言分析器也必须是英语变体 ( "" 或 "en-us" ) 。 任何与语言无关的分析器（如关键字或 simple）都不与 queryLanguage 值冲突。

   在查询请求中，如果还使用 [拼写更正](speller-how-to-add.md)，则设置的 queryLanguage 同样适用于拼写检查器、解答和标题。 对于单个部分，不存在重写。 

   尽管搜索索引中的内容可以用多种语言撰写，但查询输入最有可能出现在其中。 搜索引擎不检查 queryLanguage、语言分析器以及内容所用的语言的兼容性，因此请确保对查询进行相应的限定以避免产生不正确的结果。

1. 可选，但建议设置 "searchFields"。

   在语义查询中，"searchFields" 字段的顺序反映语义分级中字段的优先级或相对重要性。 仅将使用顶级字符串字段 (独立或集合) 。 由于 searchFields 在简单的 Lucene 查询 (中有其他行为，没有隐含的优先级顺序) ，因此任何非字符串字段和子字段都不会导致错误，但也不会用于语义排名。

   指定 searchFields 时，请遵循以下准则：

   + 简单字段（如 HotelName 或标题）应在详细字段（如 Description）之前。

   + 如果索引的 URL 字段是文本 (人可读的，如 `www.domain.com/name-of-the-document-and-other-details` ，而不是计算机焦点 `www.domain.com/?id=23463&param=eis` ，如) ，则将其放在列表中 (如果没有简明的标题) 字段，则将其置于列表中第二个。

   + 如果只指定了一个字段，则将其视为文档的语义排名的描述性字段。  

   + 如果未指定字段，则会将所有可搜索字段视为文档的语义排名。 但是，不建议这样做，因为它可能不会从搜索索引中产生最佳结果。

1. 删除 "orderBy" 子句（如果它们存在于现有请求中）。 语义分数用于对结果进行排序，如果包括显式排序逻辑，则返回 HTTP 400 错误。

1. （可选）将 "解答" 设置为 "extractive"，并指定要超过1个答案的答案数。

1. （可选）自定义应用于字幕的突出显示样式。 标题对文档中汇总响应的关键章节应用突出显示格式。 默认值为 `<em>`。 若要指定格式设置的类型 (例如，黄色背景) ，可以设置 highlightPreTag 和 highlightPostTag。

1. 指定请求中所需的任何其他参数。 诸如 [拼写检查](speller-how-to-add.md)、 [select](search-query-odata-select.md)和 count 等参数会提高请求的质量和响应的可读性。

### <a name="review-the-response"></a>检查响应

上述查询的响应将返回以下匹配项作为 top 选取。 标题以纯文本和突出显示的版本自动返回。 有关语义响应的详细信息，请参阅 [语义排名和响应](semantic-how-to-query-response.md)。

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

下表汇总了语义查询中使用的查询参数，以便您可以看到这些参数整体。 有关所有参数的列表，请参阅 [搜索文档 (REST 预览版) ](/rest/api/searchservice/preview-api/search-documents)

| 参数 | 类型 | 说明 |
|-----------|-------|-------------|
| queryType | 字符串 | 有效值包括 "简单"、"完整" 和 "语义"。 语义查询需要值 "语义"。 |
| queryLanguage | 字符串 | 语义查询所必需的。 目前仅实现 "en-us"。 |
| searchFields | 字符串 | 可搜索字段的逗号分隔列表。 可选，但建议这样做。 指定发生语义排名的字段。 </br></br>与简单查询类型和完整查询类型相比，字段的列出顺序决定了优先顺序。|
| 响应 |字符串 | 用于指定是否在结果中包括语义答案的可选字段。 目前仅实现 "extractive"。 可以将答案配置为最多返回5个。 此示例 "extractive|count3 "" 显示了三个答案的计数。 默认值为 1。|

## <a name="query-with-search-explorer"></a>使用搜索浏览器查询

下面的查询使用 API 版本 2020-06-30-Preview 并在搜索资源管理器中运行，以内置酒店样品索引为目标。 `$select`子句将结果限制为只有几个字段，以便在搜索资源管理器中更轻松地在详细 JSON 中进行扫描。

### <a name="with-querytypesemantic"></a>With queryType = 语义

```json
search=I want a nice hotel on the water with a great restaurant&$select=HotelId,HotelName,Description,Tags&queryType=semantic&queryLanguage=english&searchFields=Description,Tags
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

### <a name="with-querytype-default"></a>对于 queryType (默认值) 

为了进行比较，请运行与上面相同的查询，并删除 `&queryType=semantic&queryLanguage=english&searchFields=Description,Tags` 。 请注意 `"@search.rerankerScore"` ，这些结果中没有，不同的酒店显示在前三个位置中。

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

回忆一下，语义排名和响应是通过初始结果集生成的。 改善初始结果质量的任何逻辑都将携带语义搜索。 作为下一步，请查看涉及初始结果的功能，包括影响如何对字符串进行标记的分析器、可以优化结果的计分配置文件以及默认的关联算法。

+ [用于文本处理的分析器](search-analyzers.md)
+ [认知搜索中的相似性和评分](index-similarity-and-scoring.md)
+ [添加评分配置文件](index-add-scoring-profiles.md)
+ [语义搜索概述](semantic-search-overview.md)
+ [向查询字词添加拼写检查](speller-how-to-add.md)
