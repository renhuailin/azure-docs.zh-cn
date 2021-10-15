---
title: 添加拼写检查
titleSuffix: Azure Cognitive Search
description: 将拼写更正附加到查询管道，以便在执行查询之前修复查询字词中的拼写错误。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/29/2021
ms.custom: references_regions
ms.openlocfilehash: 98aec7fc2a3857ac50125e7e49c5f9ab105a49d7
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129535915"
---
# <a name="add-spell-check-to-queries-in-cognitive-search"></a>在认知搜索中向查询添加拼写检查

> [!IMPORTANT]
> 拼写更正根据[补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)处于公开预览状态。 仅可通过 Azure 门户和预览版 REST API 获得。

在单个搜索查询字词进入搜索引擎之前，可以通过对其进行拼写更正来改善召回率。 所有查询类型都支持 **拼写检查器** 参数：[简单](query-simple-syntax.md)、[完整](query-lucene-syntax.md)和新的 [语义](semantic-how-to-query-request.md)选项当前为公共预览版。

拼写检查器与[语义搜索预览版](semantic-search-overview.md)一起发布，并共享 queryLanguage 参数，但在其他方面是一个独立的功能，具有自身的先决条件。 使用此功能无需注册，也不会产生额外的费用。

## <a name="prerequisites"></a>先决条件

若要使用拼写检查，需要以下各项：

+ 在任何区域的基本层或更高层的搜索服务。

+ 现有搜索索引，其中包含使用[支持的语言](#supported-languages)的内容。

+ 具有“speller=lexicon”且“queryLanguage”设置为[支持的语言](#supported-languages)的[查询请求](/rest/api/searchservice/preview-api/search-documents)。 拼写检查适用于在“search”参数中传递的字符串。 筛选器不支持此功能。

对查询请求使用支持预览版 API 的搜索客户端。 对于 REST，你可以使用 [Postman](search-get-started-rest.md)、[Visual Studio Code](search-get-started-vs-code.md) 或已修改的代码对预览版 API 发出 REST 调用。 你还可以使用 Azure SDK 的 beta 版本。

| 客户端库 | 版本 |
|----------|----------|
| REST API | [2021-04-30-Preview](/rest/api/searchservice/index-preview) 或 2020-06-30-Preview |
| 用于 .NET 的 Azure SDK | [版本 11.3.0-beta.2](https://www.nuget.org/packages/Azure.Search.Documents/11.3.0-beta.2) | 
| 用于 Java 的 Azure SDK |  [版本 11.4.0-beta.2](https://search.maven.org/artifact/com.azure/azure-search-documents/11.4.0-beta.2/jar) |
| 用于 JavaScript 的 Azure SDK | [版本 11.2.0-beta.2](https://www.npmjs.com/package/@azure/search-documents/v/11.2.0-beta.2) |
| 用于 Python 的 Azure SDK | [版本 11.2.0b3](https://pypi.org/project/azure-search-documents/11.2.0b3/) |

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

拼写更正是针对接受文本分析的单个查询字词进行的，正因如此，你可以对某些 Lucene 查询（但不能是其他查询）使用拼写检查器参数。

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

## <a name="supported-languages"></a>支持的语言

可以在下表中找到 queryLanguage 的有效值，也可以从[支持的语言](/rest/api/searchservice/preview-api/search-documents#queryLanguage)列表（REST API 参考）中进行复制。

| 语言 | queryLanguage |
|----------|---------------|
| 英语 [EN] | EN、EN-US（默认） |
| 西班牙语 [ES] | ES、ES-ES（默认）|
| 法语 [FR] | FR、FR-FR（默认） |
| 德语 [DE] | DE、DE-DE（默认） |
| 荷兰语 [NL] | NL、NL-BE、NL-NL（默认值） |

### <a name="querylanguage-considerations"></a>queryLanguage 注意事项

如前所述，查询请求只能有一个 queryLanguage 参数，但该参数由多个功能共享，每个功能都支持不同的语言队列。 如果只是使用拼写检查，则上表中支持的语言列表是完整列表。 

### <a name="language-analyzer-considerations"></a>语言分析器注意事项

包含非英语内容的索引通常在非英语字段上使用[语言分析器](index-add-language-analyzers.md)来应用本机语言的语言规则。

如果现在向也在接受文本分析的内容添加拼写检查，如果在索引编制和查询处理的每一步使用相同的语言，则可以获得更好的结果。 例如，如果字段内容的索引是使用“fr.microsoft”语言分析器编制的，则查询、拼写检查、语义标题和语义答案均应使用某种形式的法语词典或语言库。

回顾如何在认知搜索中使用语言库：

+ 语言分析器可以在索引编制和查询执行期间调用，可以是 Apache Lucene（例如“de.lucene”）或 Microsoft（“de.microsoft”）。

+ 拼写检查期间调用的语言词典是使用上表中的语言代码之一指定的。

在查询请求中，分配给 queryLanguage 的值以同等方式应用于拼写检查器、[答案](semantic-answers.md)和标题。 

> [!NOTE]
> 仅在使用语言分析器时才需注意各属性值之间的语言一致性。 如果使用与语言无关的分析器（例如关键字、简单、标准、停止、空格或 `standardasciifolding.lucene`），则 queryLanguage 值可以是所需的任意值。

尽管搜索索引中的内容可以用多种语言撰写，但查询输入很可能用一种语言。 搜索引擎不检查 queryLanguage、语言分析器以及撰写内容所用的语言的兼容性，因此请确保对查询范围进行相应的限定以避免产生不正确的结果。

## <a name="next-steps"></a>后续步骤

+ [调用语义排名和标题](semantic-how-to-query-request.md)
+ [创建基本查询](search-query-create.md)
+ [使用完整的 Lucene 查询语法](query-Lucene-syntax.md)
+ [使用简单查询语法](query-simple-syntax.md)
+ [语义搜索概述](semantic-search-overview.md)