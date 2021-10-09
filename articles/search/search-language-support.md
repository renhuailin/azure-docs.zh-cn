---
title: 用于非英语搜索查询的多语言索引
titleSuffix: Azure Cognitive Search
description: 创建支持多语言内容的索引，然后创建范围限定为该内容的查询。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/08/2021
ms.openlocfilehash: 6511764697adcd6c8743efe41d276ccef3ff1bb0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124823903"
---
# <a name="create-an-index-for-multiple-languages-in-azure-cognitive-search"></a>在 Azure 认知搜索中为多语言创建索引

多语言搜索应用程序的一项关键要求是能够按用户自己的语言搜索和检索结果。 在 Azure 认知搜索中，满足多语言应用语言要求的方法之一是创建按特定语言存储字符串的专用字段，然后在查询时将全文搜索限定于这些字段。

+ 在字段定义中，[指定一个语言分析器](index-add-language-analyzers.md)，用于调用目标语言的语言规则。 

+ 在查询请求中，将 `searchFields` 参数设置为将全文搜索的范围设置为特定字段，然后使用 `select` 仅返回具有兼容内容的字段。

此方法能够成功取决于字段内容的完整性。 在执行查询的过程中，Azure 认知搜索不会翻译字符串或执行语言检测。 你负责确保字段包含预期的字符串。

## <a name="define-fields-for-content-in-different-languages"></a>为采用不同语言的内容定义字段

在 Azure 认知搜索中，查询以单个索引为目标。 想要在单个搜索体验中提供特定于语言的字符串的开发人员通常会定义专用字段来存储值：一个字段用于存储英语字符串，一个字段用于存储法语字符串，等等。

字段定义中的“analyzer”属性用于设置[语言分析器](index-add-language-analyzers.md)。 它将用于索引编制和查询执行。

```JSON
{
  "name": "hotels-sample-index",
  "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": "en.microsoft"
    },
    {
      "name": "Description_fr",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": "fr.microsoft"
    },
```

## <a name="build-and-load-an-index"></a>生成和加载索引

编写查询之前的一个中间步骤（也许是众所周知的步骤）是[生成并填充索引](search-get-started-dotnet.md)。 为了保持内容完整，此处阐述了此步骤。 确定索引是否可用的一种方法是在[门户](https://portal.azure.com)中查看索引列表。

> [!TIP]
> 通过 [AI 扩充](cognitive-search-concept-intro.md)和[技能集](cognitive-search-working-with-skillsets.md)，可以在数据引入期间检测语言并翻译文本。 如果 Azure 数据源包含混合的语言内容，则可以使用[“导入数据”向导](cognitive-search-quickstart-blob.md)尝试语言检测和翻译功能。

## <a name="constrain-the-query-and-trim-results"></a>约束查询和修剪结果

查询中的参数用于将搜索范围限制为特定的字段，然后修剪对方案无用的任何字段的结果。 

| 参数 | 目的 |
|-----------|--------------|
| **searchFields** | 将全文搜索限制为命名字段的列表。 |
| **$select** | 修剪响应，以便只包含指定的字段。 默认情况下，会返回所有可检索字段。 使用 **$Select** 参数可以选择要返回哪些字段。 |

假设目标是将搜索范围限定于包含法语字符串的字段，则可以使用 **searchFields** 将查询目标指定为包含法语字符串的字段。

不需要在查询请求中指定分析器。 在查询处理过程中，将始终使用字段定义中的语言分析器。 对于指定多个调用不同语言分析器的字段的查询，将由指定的分析器为每个字段单独处理这些词条或短语。

默认情况下，搜索会返回标记为可检索的所有字段。 因此，可能需要排除不符合想要提供的特定于语言的搜索体验的字段。 具体而言，如果将搜索范围限制为包含法语字符串的字段，也许需要从结果中排除包含英语字符串的字段。 使用 **$select** 查询参数可以控制要将哪些字段返回到调用应用程序。

#### <a name="example-in-rest"></a>REST 示例

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "animaux acceptés",
    "searchFields": "Tags, Description_fr",
    "select": "HotelName, Description_fr, Address/City, Address/StateProvince, Tags",
    "count": "true"
}
```

#### <a name="example-in-c"></a>C# 示例

```csharp
private static void RunQueries(SearchClient srchclient)
{
    SearchOptions options;
    SearchResults<Hotel> response;

    options = new SearchOptions()
    {
        IncludeTotalCount = true,
        Filter = "",
        OrderBy = { "" }
    };

    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Description_fr");
    options.SearchFields.Add("Tags");
    options.SearchFields.Add("Description_fr");

    response = srchclient.Search<Hotel>("*", options);
    WriteDocuments(response);
}
```

## <a name="boost-language-specific-fields"></a>提升语言特定的字段

有时，发出查询的代理的语言未知，在此情况下，可以针对所有字段同时发出查询。 可以使用[计分概要文件](index-add-scoring-profiles.md)来定义采用特定语言的结果首选项。 在下面的示例中，与其他语言的匹配项相比，英文描述中提供的匹配项的评分更高：

```JSON
  "scoringProfiles": [
    {
      "name": "englishFirst",
      "text": {
        "weights": { "description": 2 }
      }
    }
  ]
```

然后，将计分概要文件添加到搜索请求中：

```http
POST /indexes/hotels/docs/search?api-version=2020-06-30
{
  "search": "pets allowed",
  "searchFields": "Tags, Description",
  "select": "HotelName, Tags, Description",
  "scoringProfile": "englishFirst",
  "count": "true"
}
```

## <a name="next-steps"></a>后续步骤

+ [添加语言分析器](index-add-language-analyzers.md)
+ [Azure 认知搜索中全文搜索的工作原理](search-lucene-query-architecture.md)
+ [搜索文档 REST API](/rest/api/searchservice/search-documents)
+ [AI 扩充概述](cognitive-search-concept-intro.md)
+ [技能集概述](cognitive-search-working-with-skillsets.md)