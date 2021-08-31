---
title: 返回语义答案
titleSuffix: Azure Cognitive Search
description: 介绍语义答案的组成部分，以及如何从结果集获取答案。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/27/2021
ms.openlocfilehash: 3a8c00dcb2bf016cf70f02dcfdebc5e55bf5d66c
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122178128"
---
# <a name="return-a-semantic-answer-in-azure-cognitive-search"></a>在 Azure 认知搜索中返回语义答案

> [!IMPORTANT]
> 根据[补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，语义搜索为预览版。 它可通过 Azure 门户、预览版 REST API 和 beta 版本的 SDK 获得。 这些功能将计费。 有关详细信息，请参阅[可用性和定价](semantic-search-overview.md#availability-and-pricing)。

调用[语义排名和标题](semantic-how-to-query-request.md)时，可选择性地从最匹配的文档中直接提取能够“回答”查询的内容。 可以在响应中包含一个或多个答案，然后可以在搜索页上呈现这些答案，以改善应用的用户体验。

本文介绍如何请求语义答案、解包响应，以及哪些内容特征最有利于生成高质量的答案。

## <a name="prerequisites"></a>先决条件

适用于[语义查询](semantic-how-to-query-request.md#prerequisites)的所有先决条件（包括[服务层和区域](semantic-search-overview.md#availability-and-pricing)）同样适用于答案。

+ 查询逻辑必须包括语义查询参数“queryType=semantic”和“answers”参数。 本文将介绍所需的参数。

+ 用户输入的查询字符串必须可识别为一个问题（什么、何处、何时、如何）。

+ 索引中的搜索文档必须包含具有回答特征的文本，并且该文本必须存在于“searchFields”中列出的某个字段内。 例如，给定查询“what is a hash table”，如果没有一个 searchFields 包含其中有“A hash table is ...”的段落，则不太可能返回答案。

## <a name="what-is-a-semantic-answer"></a>什么是语义答案？

语义答案是[语义查询响应](semantic-how-to-query-request.md)的子结构。 它由搜索文档中的一个或多个字面段落构成，表述为对类似于提问的查询给出的答案。 若要使答案能够返回，短语或句子必须存在于具有回答语言特征的搜索文档中，并且查询本身必须以提问的形式表示。

认知搜索使用机器阅读理解模型来选取最佳答案。 该模型根据可用的内容生成一组潜在答案，在达到足够高的置信度级别后，它便会给出一个答案。

答案将在查询响应有效负载中作为独立的顶级对象返回，你可以选择在搜索页上将此对象与搜索结果一起呈现。 从结构上讲，它是响应中的一个数组元素，由文本、文档键和置信度分数组成。

<a name="query-params"></a>

## <a name="how-to-specify-answers-in-a-query-request"></a>如何在查询请求中指定“answers”

若要返回语义答案，查询中必须包含“queryType”、“queryLanguage”、“searchFields”和“answers”语义参数。 指定“answers”参数并不保证会获得答案，但如果要在任何情况下都调用回答处理过程，请求中必须包含此参数。

“searchFields”参数对于返回内容和顺序方面的高质量答案至关重要（见下）。 

```json
{
    "search": "how do clouds form",
    "queryType": "semantic",
    "queryLanguage": "en-us",
    "searchFields": "title,locations,content",
    "answers": "extractive|count-3",
    "count": "true"
}
```

+ 查询字符串不得为 null，并且应表述为提问形式。

+ “queryType”必须设置为“semantic”。

+ “queryLanguage”必须是[支持的语言列表 (REST API)](/rest/api/searchservice/preview-api/search-documents#queryLanguage) 中的值之一。

+ “searchFields”确定哪些字符串字段向提取模型提供标记。 生成标题的字段也会生成答案。 有关如何设置此字段以使其同时适用于标题和答案的精确指导，请参阅[设置 searchFields](semantic-how-to-query-request.md#searchfields)。 

+ 对于“answers”，参数构造为 `"answers": "extractive"`，其中，返回的默认答案数为 1。 可通过添加 `count`（如上例所示）来增加答案的数量（最多 10 个）。  是否需要多个答案取决于应用的用户体验以及呈现结果的方式。

## <a name="deconstruct-an-answer-from-the-response"></a>解构响应中的答案

答案在 @search.answers 数组中提供，该数组在响应中最先出现。 如果答案是不确定的，则响应将显示为 `"@search.answers": []`。 设计包含答案的搜索结果页时，请确保处理找不到答案的情况。

在 @search.answers 中，“key”是匹配项的文档键或 ID。 获取文档键后，可以使用[查找文档](/rest/api/searchservice/lookup-document) API 来检索要包含在搜索页或详细信息页中的搜索文档的任何部分或所有部分。

“text”和“highlights”以纯文本格式和突出显示形式提供相同的内容。 默认情况下，突出显示的样式为 `<em>`，你可以使用现有的 highlightPreTag 和 highlightPostTag 参数替代此样式。 如其他部分中所述，答案实质上是搜索文档中的字面内容。 提取模型将查找答案的特征以查找相应的内容，但不会在响应中撰写新语言。

“score”是置信度分数，用于反映答案的可信度。 如果响应中有多个答案，此分数将用于确定顺序。 可以从不同的搜索文档派生最可信的答案和最可信的标题，其中，最可信的答案来自一个文档，最可信的标题来自另一个文档，但一般情况下，你会在每个数组中的最靠前位置看到相同的文档。

答案后接“value”数组，该数组始终包含分数、标题以及默认可检索的任何字段。 如果指定了 select 参数，则“value”数组限制为指定的字段。 有关响应中的项的详细信息，请参阅[创建语义查询](semantic-how-to-query-request.md)。

假设查询为“how do clouds form”，响应中将返回以下答案：

```json
{
    "@search.answers": [
        {
            "key": "4123",
            "text": "Sunlight heats the land all day, warming that moist air and causing it to rise high into the   atmosphere until it cools and condenses into water droplets. Clouds generally form where air is ascending (over land in this case),   but not where it is descending (over the river).",
            "highlights": "Sunlight heats the land all day, warming that moist air and causing it to rise high into the   atmosphere until it cools and condenses into water droplets. Clouds generally form<em> where air is ascending</em> (over land in this case),   but not where it is<em> descending</em> (over the river).",
            "score": 0.94639826
        }
    ],
    "value": [
        {
            "@search.score": 0.5479723,
            "@search.rerankerScore": 1.0321671911515296,
            "@search.captions": [
                {
                    "text": "Like all clouds, it forms when the air reaches its dew point—the temperature at which an air mass is cool enough for its water vapor to condense into liquid droplets. This false-color image shows valley fog, which is common in the Pacific Northwest of North America.",
                    "highlights": "Like all<em> clouds</em>, it<em> forms</em> when the air reaches its dew point—the temperature at    which an air mass is cool enough for its water vapor to condense into liquid droplets. This false-color image shows valley<em> fog</em>, which is common in the Pacific Northwest of North America."
                }
            ],
            "title": "Earth Atmosphere",
            "content": "Fog is essentially a cloud lying on the ground. Like all clouds, it forms when the air reaches its dew point—the temperature at  \n\nwhich an air mass is cool enough for its water vapor to condense into liquid droplets.\n\nThis false-color image shows valley fog, which is common in the Pacific Northwest of North America. On clear winter nights, the \n\nground and overlying air cool off rapidly, especially at high elevations. Cold air is denser than warm air, and it sinks down into the \n\nvalleys. The moist air in the valleys gets chilled to its dew point, and fog forms. If undisturbed by winds, such fog may persist for \n\ndays. The Terra satellite captured this image of foggy valleys northeast of Vancouver in February 2010.\n\n\n",
            "locations": [
                "Pacific Northwest",
                "North America",
                "Vancouver"
            ]
    ]
        }
}

```

## <a name="tips-for-producing-high-quality-answers"></a>有关生成高质量答案的提示

为获得最佳结果，应返回文档集中具有以下特征的语义答案：

+ “searchFields”必须提供可提供足够文本的字段，这样就可能在其中找到答案。 只有文档中的逐字文本才能显示为答案。

+ 查询字符串不得为 null (search=`*`)，并且字符串应具有提问的特征，这与关键字搜索（任意字词或短语的顺序列表）完全不同。 如果查询字符串看上去不是答案，则会跳过答案处理，即使请求指定了“answers”作为查询参数，也是如此。

+ 语义提取和汇总对于可以及时分析的每个文档中的标记数有限制。 在实践中，如果你的大型文档包含数百个页面，则你应该首先尝试将内容分解为较小的文档。

## <a name="next-steps"></a>后续步骤

+ [语义搜索概述](semantic-search-overview.md)
+ [语义排名算法](semantic-ranking.md)
+ [相似性排名算法](index-ranking-similarity.md)
+ [创建语义查询](semantic-how-to-query-request.md)
