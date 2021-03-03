---
title: 构建语义响应
titleSuffix: Azure Cognitive Search
description: 介绍认知搜索中的语义排名算法，以及如何从结果集中构建 "语义答案" 和 "语义标题"。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.custom: references_regions
ms.openlocfilehash: febbfd0f3def8e681107ef78d9478463b1c2a872
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679184"
---
# <a name="semantic-ranking-and-responses-in-azure-cognitive-search"></a>Azure 认知搜索中的语义排名和响应

> [!IMPORTANT]
> 语义排名算法和语义式答案/标题处于公共预览中，仅通过预览版 REST API 提供。 在 [补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)下，预览版功能按原样提供。

语义分级使用语义排名模型，通过对用自然语言（而不是关键字）表示的查询进行定型，来 reranking 搜索结果的精度。

本文介绍语义排名算法以及语义响应的形状。 响应包括纯文本和突出显示的标题，并提供 (可选) 的答案。

+ 语义标题是与从搜索结果中提取的查询相关的文本段落。 当各个内容字段对于结果页来说太大时，它们有助于汇总结果。 标题功能语义突出显示，使用户能够快速浏览查询结果以查找最相关的文档，从而提高总体用户体验。

+ 语义应答使用必应的机器学习模型来阐明类似问题的查询的答案。 从与查询最相关的段落列表中选择答案（从查询结果集中的顶部文档中提取）。 答案作为独立的顶级对象在查询响应负载中返回，您可以选择在搜索页面上呈现搜索结果。

## <a name="prerequisites"></a>先决条件

+ 使用语义查询类型表述的查询。 有关详细信息，请参阅 [创建语义查询](semantic-how-to-query-request.md)。

## <a name="understanding-a-semantic-response"></a>了解语义响应

语义响应包含用于分数、标题和答案的新属性。 语义响应是通过标准响应生成的，使用 [全文搜索引擎](search-lucene-query-architecture.md)返回的前50个结果，然后使用语义 ranker 重新排名。 如果指定了超过50，则返回额外的结果，但不会在语义上重新排列。

与所有查询一样，响应由标记为可检索的所有字段或只是 select 语句中列出的字段组成。 它还包括 "答案" 字段和 "标题"。

+ 默认情况下，对于每个语义结果，都有一个 "应答"，返回为可选择在搜索页中呈现的不同字段。 最多可以指定五个。 答案的表述是自动的：通读初始结果中的所有文档，运行 extractive 汇总，然后执行计算机读取理解，最后直接升级答案字段中用户的问题。

+ "Caption" 是文档内容的提取汇总，以纯文本形式或使用突出显示形式返回。 标题是自动包含的，不能被取消。 使用计算机读取理解来应用突出显示，以确定应强调哪些字符串。 突出显示了对最相关的章节的注意力，使你可以快速扫描结果页以找到正确的文档。

语义重新排名的结果集会按值对结果进行排序 @search.rerankerScore 。 如果添加 OrderBy 子句，将返回 HTTP 400 错误，因为语义查询中不支持该子句。

## <a name="example"></a>示例

与 @search.rerankerScore 标准一起存在 @search.score ，用于对结果进行排序。

给定以下查询：

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
{
    "search": "newer hotel near the water with a great restaurant",
    "queryType": "semantic",
    "queryLanguage": "en-us",
    "answers": "none",
    "searchFields": "HotelName,Category,Description",
    "select": "HotelId,HotelName,Description,Category",
    "count": true
}
```

您可能会看到下面的结果，代表语义响应。 这些结果只显示前三个响应，如 "" 所示 @search.rerankerScore 。 请注意，Oceanside 滑雪场现在排名第一。 在默认排名 "" 下 @search.score ，将在轨迹结束后的第二个位置返回此结果。

```http
{
    "@odata.count": 31,
    "@search.answers": [],
    "value": [
        {
            "@search.score": 1.8920634,
            "@search.rerankerScore": 1.1091284966096282,
            "@search.captions": [
                {
                    "text": "Oceanside Resort. Budget. New Luxury Hotel.  Be the first to stay. Bay views from every room, location near the piper, rooftop pool, waterfront dining & more.",
                    "highlights": "<em>Oceanside Resort.</em> Budget. New Luxury Hotel.  Be the first to stay.<em> Bay views</em> from every room, location near the piper, rooftop pool, waterfront dining & more."
                }
            ],
            "HotelId": "18",
            "HotelName": "Oceanside Resort",
            "Description": "New Luxury Hotel.  Be the first to stay. Bay views from every room, location near the piper, rooftop pool, waterfront dining & more.",
            "Category": "Budget"
        },
        {
            "@search.score": 2.5204072,
            "@search.rerankerScore": 1.0731962407007813,
            "@search.captions": [
                {
                    "text": "Trails End Motel. Luxury. Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport.",
                    "highlights": "<em>Trails End Motel.</em> Luxury. Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport."
                }
            ],
            "HotelId": "40",
            "HotelName": "Trails End Motel",
            "Description": "Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport.",
            "Category": "Luxury"
        },
        {
            "@search.score": 1.4104348,
            "@search.rerankerScore": 1.06992666143924,
            "@search.captions": [
                {
                    "text": "Winter Panorama Resort. Resort and Spa. Newly-renovated with large rooms, free 24-hr airport shuttle & a new restaurant. Rooms/suites offer mini-fridges & 49-inch HDTVs.",
                    "highlights": "<em>Winter Panorama Resort</em>. Resort and Spa. Newly-renovated with large rooms, free 24-hr airport shuttle & a new restaurant. Rooms/suites offer mini-fridges & 49-inch HDTVs."
                }
            ],
            "HotelId": "12",
            "HotelName": "Winter Panorama Resort",
            "Description": "Newly-renovated with large rooms, free 24-hr airport shuttle & a new restaurant. Rooms/suites offer mini-fridges & 49-inch HDTVs.",
            "Category": "Resort and Spa"
        }
```

## <a name="next-steps"></a>后续步骤

+ [语义搜索概述](semantic-search-overview.md)
+ [相似性算法](index-ranking-similarity.md)
+ [创建语义查询](semantic-how-to-query-request.md)
+ [创建基本查询](search-query-create.md)