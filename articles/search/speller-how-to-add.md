---
title: 添加拼写检查
titleSuffix: Azure Cognitive Search
description: 将拼写更正附加到查询管道，以便在执行查询之前修复查询字词中的拼写错误。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/26/2021
ms.custom: references_regions
ms.openlocfilehash: 52ac3ee4ea2f71e285d21c7b6d082e84fa090da1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105625902"
---
# <a name="add-spell-check-to-queries-in-cognitive-search"></a>在认知搜索中向查询添加拼写检查

> [!IMPORTANT]
> 拼写更正以公共预览版提供，只能通过预览版 REST API 使用。 根据[补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，预览功能按原样提供。 在初始预览版推出期间，拼写检查器不收取任何费用。 有关详细信息，请参阅[可用性和定价](semantic-search-overview.md#availability-and-pricing)。

在单个搜索查询字词进入搜索引擎之前，可以通过对其进行拼写更正来改善召回率。 所有查询类型都支持 **拼写检查器** 参数：[简单](query-simple-syntax.md)、[完整](query-lucene-syntax.md)和新的[语义](semantic-how-to-query-request.md)选项当前为公共预览版。

## <a name="prerequisites"></a>先决条件

+ 包含英文内容的现有搜索索引。 目前，拼写更正不适用于[同义词](search-synonyms.md)。 请避免在任何字段定义中指定同义词映射的索引上使用它。

+ 用于发送查询的搜索客户端

  搜索客户端必须支持查询请求的预览 REST API。 你可以使用已修改的 [Postman](search-get-started-rest.md)、[Visual Studio Code](search-get-started-vs-code.md) 或代码，以便对预览 API 进行 REST 调用。

+ 使用拼写更正的[查询请求](/rest/api/searchservice/preview-api/search-documents)具有 "api-version=2020-06-30-Preview"、"speller=lexicon" 和 "queryLanguage=en-us"。

  queryLanguage 是拼写检查器所必需的，当前“en-us”是唯一有效的值。

> [!Note]
> 拼写检查器参数在提供语义搜索的同一区域中的所有层上都可用。 你不需要注册即可访问此预览功能。 有关详细信息，请参阅[可用性和定价](semantic-search-overview.md#availability-and-pricing)。

## <a name="spell-correction-with-simple-search"></a>对简单搜索的拼写更正

下面的示例使用内置的 hotels-sample 索引演示如何对一个简单自由格式文本查询进行拼写更正。 在不进行拼写更正的情况下，该查询返回零个结果。 更正后，该查询返回一条结果（Johnson 家庭度假村）。

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
{
    "search": "famly acitvites",
    "speller": "lexicon",
    "queryLanguage": "en-us",
    "queryType": "simple",
    "select": "HotelId,HotelName,Description,Category,Tags",
    "count": true
}
```

## <a name="spell-correction-with-full-lucene"></a>对完整 Lucene 的拼写更正

拼写更正是针对接受分析的单个查询字词进行的，正因如此，你可以对某些 Lucene 查询（但不能是其他查询）使用拼写检查器参数。

+ 绕过文本分析的不兼容查询格式包括：通配符、正则表达式、模糊
+ 兼容的查询格式包括：字段搜索、接近性、字词提升

此示例对采用完整 Lucene 语法并包含一个拼错的查询字词的“类别”字段使用字段搜索。 通过包含拼写检查器，“Suiite”中的拼写错误已得到更正，查询将会成功。

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
{
    "search": "Category:(Resort and Spa) OR Category:Suiite",
    "queryType": "full",
    "speller": "lexicon",
    "queryLanguage": "en-us",
    "select": "Category",
    "count": true
}
```

## <a name="spell-correction-with-semantic-search"></a>对语义搜索进行拼写更正

此查询只有一个字词拼写正确，其他每个字词都有拼写错误，它将接受拼写更正以返回相关结果。 若要了解详细信息，请参阅[创建语义查询](semantic-how-to-query-request.md)。

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview     
{
    "search": "hisotoric hotell wiht great restrant nad wiifi",
    "queryType": "semantic",
    "speller": "lexicon",
    "queryLanguage": "en-us",
    "searchFields": "HotelName,Tags,Description",
    "select": "HotelId,HotelName,Description,Category,Tags",
    "count": true
}
```

## <a name="language-considerations"></a>语言注意事项

拼写检查器所需的 queryLanguage 参数必须与分配给索引架构中的字段定义的任何[语言分析器](index-add-language-analyzers.md)一致。 

+ queryLanguage 确定哪些词典用于拼写检查，如果使用了“queryType=semantic”，则它还用作[语义排名算法](semantic-answers.md)的输入。

+ 在编制索引和执行查询期间将使用语言分析器在搜索索引中查找匹配的文档。 使用语言分析器的字段定义的示例是 `"name": "Description", "type": "Edm.String", "analyzer": "en.microsoft"`。

为了在使用拼写检查器时获得最佳结果，如果 queryLanguage 为“en-us”，则任何语言分析器也必须是英语变体（“en.microsoft”或“en.lucene”）。

> [!NOTE]
> 与语言无关的分析器（例如关键字、简单、标准、非索引字、空白或 `standardasciifolding.lucene` 分析器）不会与 queryLanguage 设置冲突。

在查询请求中，你设置的 queryLanguage 以同等方式应用于拼写检查器、答案和标题。 对于单个部分，不存在重写。

尽管搜索索引中的内容可以用多种语言撰写，但查询输入很可能用一种语言。 搜索引擎不检查 queryLanguage、语言分析器以及撰写内容所用的语言的兼容性，因此请确保对查询范围进行相应的限定以避免产生不正确的结果。

## <a name="next-steps"></a>后续步骤

+ [创建语义查询](semantic-how-to-query-request.md)
+ [创建基本查询](search-query-create.md)
+ [使用完整的 Lucene 查询语法](query-Lucene-syntax.md)
+ [使用简单查询语法](query-simple-syntax.md)
+ [语义搜索概述](semantic-search-overview.md)