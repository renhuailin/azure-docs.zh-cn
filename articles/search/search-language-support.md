---
title: 用于非英语搜索查询的多语言索引
titleSuffix: Azure Cognitive Search
description: 创建一个支持多语言内容的索引，然后创建其范围限定于该内容的查询。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: 627ec77af4e492b4f22404972729cecdb1c40f06
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801598"
---
# <a name="how-to-create-an-index-for-multiple-languages-in-azure-cognitive-search"></a>如何在 Azure 认知搜索中为多种语言创建索引

多语言搜索应用程序的一项关键要求是能够按用户自己的语言搜索和检索结果。 在 Azure 认知搜索中，满足多语言应用的语言要求的一种方法是创建专用字段来存储特定语言的字符串，然后在查询时将全文搜索的范围限制为这些字段。

+ 在字段定义中，设置一个会调用目标语言的语言规则的语言分析器。 若要查看支持的分析器的完整列表，请参阅[添加语言分析器](index-add-language-analyzers.md)。

+ 在查询请求中设置参数，以便将全文搜索的范围限定于特定字段，然后，对于其提供的内容与你要提供的搜索体验不兼容的任何字段，请剪裁其结果。

此方法能够成功取决于字段内容的完整性。 在查询执行过程中，Azure 认知搜索不会转换字符串或执行语言检测。 你负责确保字段包含预期的字符串。

## <a name="define-fields-for-content-in-different-languages"></a>为采用不同语言的内容定义字段

在 Azure 认知搜索中，查询以单个索引为目标。 想要在单个搜索体验中提供特定于语言的字符串的开发人员通常会定义专用字段来存储值：一个字段用于存储英语字符串，一个字段用于存储法语字符串，等等。

字段定义中的“analyzer”属性用于设置[语言分析器](index-add-language-analyzers.md)。 它将同时用于编制索引和执行查询。

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

编写查询之前的一个中间步骤（也许是众所周知的步骤）是[生成并填充索引](search-get-started-dotnet.md)。 为了保持内容完整，此处阐述了此步骤。 确定索引可用性的一种方法是在[门户](https://portal.azure.com)中查看索引列表。

> [!TIP]
> 通过 [AI 扩充](cognitive-search-concept-intro.md)和[技能组](cognitive-search-working-with-skillsets.md)在数据引入期间支持语言检测和文本翻译。 如果你有一个包含混合语言内容的 Azure 数据源，则可以使用[导入数据向导](cognitive-search-quickstart-blob.md)尝试语言检测和翻译功能。

## <a name="constrain-the-query-and-trim-results"></a>约束查询和修剪结果

查询中的参数用于将搜索范围限制为特定的字段，然后修剪对方案无用的任何字段的结果。 

| 参数 | 目的 |
|-----------|--------------|
| **searchFields** | 将全文搜索限制为命名字段的列表。 |
| **$select** | 修剪响应，以便只包含指定的字段。 默认情况下，会返回所有可检索字段。 使用 **$Select** 参数可以选择要返回哪些字段。 |

假设目标是将搜索范围限定于包含法语字符串的字段，则可以使用 **searchFields** 将查询目标指定为包含法语字符串的字段。

没有必要在查询请求中指定分析器。 在查询处理过程中，将始终使用基于字段定义的语言分析器。 对于其指定的多个字段会调用不同语言分析器的查询，词语或短语将由为每个字段分配的分析器各自处理。

默认情况下，搜索会返回标记为可检索的所有字段。 因此，可能需要排除不符合想要提供的特定于语言的搜索体验的字段。 具体而言，如果将搜索范围限制为包含法语字符串的字段，也许需要从结果中排除包含英语字符串的字段。 使用 **$select** 查询参数可以控制要将哪些字段返回到调用应用程序。

#### <a name="example-in-rest"></a>REST 中的示例

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "animaux acceptés",
    "searchFields": "Tags, Description_fr",
    "select": "HotelName, Description_fr, Address/City, Address/StateProvince, Tags",
    "count": "true"
}
```

#### <a name="example-in-c"></a>C# 中的示例

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

有时，发出查询的代理的语言未知，在此情况下，可以针对所有字段同时发出查询。 可以使用[计分概要文件](index-add-scoring-profiles.md)来定义采用特定语言的结果的首选项。 在以下示例中，与其他语言的匹配项相比，在英文说明中找到的匹配项分数更高：

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

你可以随后在搜索请求中包含计分概要文件：

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

+ [语言分析器](index-add-language-analyzers.md)
+ [Azure 认知搜索中全文搜索的工作原理](search-lucene-query-architecture.md)
+ [搜索文档 REST API](/rest/api/searchservice/search-documents)
+ [AI 扩充概述](cognitive-search-concept-intro.md)
+ [技能组概述](cognitive-search-working-with-skillsets.md)