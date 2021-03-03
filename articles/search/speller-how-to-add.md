---
title: 添加拼写检查
titleSuffix: Azure Cognitive Search
description: 将拼写更正附加到查询管道，在执行查询之前修复查询字词的键入错误。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.custom: references_regions
ms.openlocfilehash: da172e9a7605876711e4a4f32bf4fac698b35109
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101694795"
---
# <a name="add-spell-check-to-queries-in-cognitive-search"></a>在认知搜索中向查询添加拼写检查

> [!IMPORTANT]
> 拼写更正处于公开预览中，仅可通过预览版 REST API 提供。 在 [补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)下，预览版功能按原样提供。 在初始预览启动过程中，拼写检查器不收取任何费用。 有关详细信息，请参阅 [可用性和定价](semantic-search-overview.md#availability-and-pricing)。

可以通过在搜索查询词到达搜索引擎之前对其进行拼写更正来改善召回。 所有查询类型都支持 **拼写检查** 器参数： " [简单](query-simple-syntax.md)"、" [完整](query-lucene-syntax.md)" 和 "新建" [语义](semantic-how-to-query-request.md) 选项当前为公共预览版。

## <a name="prerequisites"></a>先决条件

+ 包含英文内容的现有搜索索引

+ 用于发送查询的搜索客户端

  搜索客户端必须支持查询请求的预览 REST Api。 你可以使用已修改的 [Postman](search-get-started-rest.md)、 [Visual Studio Code](search-get-started-vs-code.md)或代码，以便对预览 api 进行 REST 调用。

+ 使用拼写更正的[查询请求](/rest/api/searchservice/preview-api/search-documents)具有 "api 版本 = 2020-06-30"、"拼写检查器 = 词典" 和 "queryLanguage = en-us"。

  QueryLanguage 是拼写检查器所必需的，当前 "en-us" 是唯一有效的值。

> [!Note]
> 拼写检查器参数在提供语义搜索的同一区域中的所有层上都可用。 无需注册即可访问此预览功能。 有关详细信息，请参阅 [可用性和定价](semantic-search-overview.md#availability-and-pricing)。

## <a name="spell-correction-with-simple-search"></a>简单搜索的拼写更正

下面的示例使用内置的酒店样品索引在简单的自由格式文本查询中演示拼写更正。 如果没有拼写更正，查询将返回零个结果。 对于更正，该查询将为 Johnson 的面向族的滑雪场返回一个结果。

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

## <a name="spell-correction-with-full-lucene"></a>完整的 Lucene 拼写更正

拼写更正发生在进行分析的单个查询字词上，这就是为什么你可以使用带有某些 Lucene 查询的拼写检查器参数，而不能使用其他的。

+ 绕过文本分析的不兼容查询窗体包括：通配符、正则表达式、模糊
+ 兼容的查询窗体包括：现场搜索、邻近性、术语提升

此示例对类别字段使用完整的 Lucene 语法和拼写错误的查询术语，使用现场搜索。 通过包含拼写检查器，"Suiite" 中的拼写错误得以更正，查询将成功。

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

## <a name="spell-correction-with-semantic-search"></a>用语义搜索更正拼写

此查询在每个术语（一个除外）进行拼写检查，以返回相关结果。 若要了解详细信息，请参阅 [创建语义查询](semantic-how-to-query-request.md)。

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

拼写检查器所需的 queryLanguage 参数必须与分配给索引架构中的字段定义的任何 [语言分析器](index-add-language-analyzers.md) 一致。 

+ queryLanguage 确定哪些词典用于拼写检查，如果使用的是 "queryType = 语义"，也可将其用作 [语义排名算法](semantic-how-to-query-response.md) 的输入。

+ 语言分析器在索引和查询执行过程中使用，以在搜索索引中查找匹配的文档。 使用语言分析器的字段定义的一个示例是 `"name": "Description", "type": "Edm.String", "analyzer": "en.microsoft"` 。

若要在使用拼写检查器时获得最佳结果，如果 queryLanguage 为 "en-us"，则任何语言分析器也必须是英语变体， ( "" 或 "en-us" ) 。

> [!NOTE]
> 与语言无关的分析器 (例如关键字、简单、标准、停止、空白或 `standardasciifolding.lucene`) 不会与 queryLanguage 设置冲突。

在查询请求中，你设置的 queryLanguage 同样适用于拼写检查器、解答和标题。 对于单个部分，不存在重写。

尽管搜索索引中的内容可以用多种语言撰写，但查询输入最有可能出现在其中。 搜索引擎不检查 queryLanguage、语言分析器以及内容所用的语言的兼容性，因此请确保对查询进行相应的限定以避免产生不正确的结果。

## <a name="next-steps"></a>后续步骤

+ [创建语义查询](semantic-how-to-query-request.md)
+ [创建基本查询](search-query-create.md)
+ [使用完整的 Lucene 查询语法](query-Lucene-syntax.md)
+ [使用简单查询语法](query-simple-syntax.md)
+ [语义搜索概述](semantic-search-overview.md)