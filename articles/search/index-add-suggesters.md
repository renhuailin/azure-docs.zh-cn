---
title: 创建建议器
titleSuffix: Azure Cognitive Search
description: 通过创建建议器并构建调用自动完成或自动建议的查询字词的请求，在 Azure 认知搜索中启用自动提示查询操作。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/24/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 4390291eb96c11b8fb7fdb48eb92abaf802b80c0
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96030775"
---
# <a name="create-a-suggester-to-enable-autocomplete-and-suggested-results-in-a-query"></a>创建建议器，以在查询中启用“自动完成”和“建议结果”功能

在 Azure 认知搜索中，通过 *建议器* 启用 "搜索即用类型"。 建议器是包含字段集合的内部数据结构。 字段会经历其他词汇切分，生成前缀序列以支持部分术语的匹配。

例如，如果建议器包含 City 字段，则会为术语 "西雅图" 创建 "海平面"、"座位"、"seatt" 和 "seattl" 的前缀组合。 前缀存储在逆索引中，每个字段对应于建议器字段集合中指定的每个字段。

## <a name="typeahead-experiences-in-cognitive-search"></a>认知搜索中的 Typeahead 体验

建议器支持两种体验： *自动完成功能*，它完成整个术语查询的部分输入，并 *建议* 邀请单击到特定的匹配项。 “自动完成”生成查询。 “建议”生成匹配的文档。

取自[在 C# 中创建第一个应用](tutorial-csharp-type-ahead-and-suggestions.md)的以下屏幕截图演示了这两种体验。 自动完成可预测潜在字词，并使用“in”来补充“tw”。 建议是极其精简的搜索结果，其中的字段（例如酒店名称）表示索引中匹配的酒店搜索文档。 对于建议，可以呈现任何提供描述性信息的字段。

![自动完成和建议查询的直观比较](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "自动完成和建议查询的直观比较")

可以单独使用这些功能，或将它们一起使用。 有一个索引和查询组件可在 Azure 认知搜索中实现这些行为。 

+ 将建议器添加到搜索索引定义。 本文的余下内容重点介绍如何创建建议器。

+ 使用 [下面列出](#how-to-use-a-suggester)的一个 api，以建议请求或自动完成请求的形式调用启用了建议器的查询。

对于字符串字段，可按字段启用“键入时搜索”支持。 若要获得屏幕截图中所示的类似体验，可以在同一搜索解决方案中实现这两种自动提示行为。 这两个请求针对特定索引的文档集合，在用户提供至少包含三个字符的输入字符串后，将返回响应。

## <a name="how-to-create-a-suggester"></a>如何创建建议器

若要创建建议器，请将其添加到 [索引定义](/rest/api/searchservice/create-index)中。 建议器获取启用了 typeahead 体验的名称和字段的集合。 并 [设置每个属性](#property-reference)。 创建建议器的最佳时间是还要定义使用建议器的字段时。

+ 仅使用字符串字段。

+ 如果字符串字段是复杂类型的一部分 (例如，地址) 内的 "城市" 字段，请在字段中包含父级： `"Address/City"` (REST 和 c # 和 Python) ，或 `["Address"]["City"]` (JavaScript) 。

+ 在字段上使用默认的标准 Lucene 分析器 (`"analyzer": null`) 或 [语言分析器](index-add-language-analyzers.md) (例如 `"analyzer": "en.Microsoft"`) 。

如果尝试使用预先存在的字段创建建议器，API 将不允许这样做。 在编制索引期间，如果两个或更多个字符的组合中的部分字词连同完整字词一起标记化，则会生成前缀。 如果现有字段已标记化，而你想要将其添加到建议器，则必须重新生成索引。 有关详细信息，请参阅[如何重新生成 Azure 认知搜索索引](search-howto-reindex.md)。

### <a name="choose-fields"></a>选择字段

虽然建议器有多个属性，但它主要是需要为其启用“键入时搜索”体验的字符串字段集合。 每个索引都有一个建议器，因此建议器列表必须包含所有为“建议”和“自动完成”贡献内容的字段。

自动完成可获益于更大的字段池，因为额外的内容可更好地帮助补充和形成完整词语。

另一方面，当字段为可选时，“建议”能生成更好的结果。 请记住，“建议”是搜索文档的代理，因此需要最能代表单个结果的字段。 用于区分多个匹配项的名称、标题或其他唯一字段最合适。 如果字段包含重复值，则建议会包含相同的结果，因此用户不知道要单击哪条建议。

为实现“边键入边搜索”的体验，请添加“自动完成”所需的所有字段，然后使用 $select、$top、$filter 和 searchFields 来控制建议的结果   。

### <a name="choose-analyzers"></a>选择分析器

所选的分析器决定了如何标记化字段并随后指定其前缀。 例如，对于带连字符的字符串（例如“context-sensitive”），使用语言分析器会生成以下标记组合：“context”、“sensitive”、“context-sensitive”。 如果使用的是标准 Lucene 分析器，则带连字符的字符串不存在。 

评估分析器时，请考虑使用 [分析文本 API](/rest/api/searchservice/test-analyzer) 来了解如何处理术语。 生成索引后，可以尝试对字符串运行各种分析器，以查看标记输出。

使用[自定义分析器](index-add-custom-analyzers.md)或[预定义分析器](index-add-custom-analyzers.md#predefined-analyzers-reference)（标准 Lucene 除外）的字段被明确禁止，这样是为了防止结果不佳。

> [!NOTE]
> 如果需要解决分析器约束，例如，如果需要为某些查询方案使用某个关键字或 ngram 分析器，应对同一内容使用两个单独的字段。 这样，就可以在其中一个字段中使用建议器，并使用自定义分析器配置来设置其他字段。

## <a name="create-using-the-portal"></a>使用门户创建

使用 " **添加索引** " 或 " **导入数据** " 向导创建索引时，可以选择启用建议器：

1. 在索引定义中，输入建议器的名称。

1. 在新字段的每个字段定义中，选择 "建议器" 列中的复选框。 仅对字符串字段提供复选框。 

如前所述，analyzer 选项会影响已标记的和前缀。 启用建议器时，请考虑整个字段定义。 

## <a name="create-using-rest"></a>使用 REST 进行创建

在 REST API 中，通过[创建索引](/rest/api/searchservice/create-index)或[更新索引](/rest/api/searchservice/update-index)添加建议器。 

  ```json
  {
    "name": "hotels-sample-index",
    "fields": [
      . . .
          {
              "name": "HotelName",
              "type": "Edm.String",
              "facetable": false,
              "filterable": false,
              "key": false,
              "retrievable": true,
              "searchable": true,
              "sortable": false,
              "analyzer": "en.microsoft",
              "indexAnalyzer": null,
              "searchAnalyzer": null,
              "synonymMaps": [],
              "fields": []
          },
    ],
    "suggesters": [
      {
        "name": "sg",
        "searchMode": "analyzingInfixMatching",
        "sourceFields": ["HotelName"]
      }
    ],
    "scoringProfiles": [
      . . .
    ]
  }
  ```

## <a name="create-using-net"></a>使用 .NET 进行创建

在 c # 中，定义一个 [SearchSuggester 对象](/dotnet/api/azure.search.documents.indexes.models.searchsuggester)。 `Suggesters` 是 SearchIndex 对象上的集合，但它只能是一个项。 将建议器添加到索引定义。

```csharp
private static void CreateIndex(string indexName, SearchIndexClient indexClient)
{
    FieldBuilder fieldBuilder = new FieldBuilder();
    var searchFields = fieldBuilder.Build(typeof(Hotel));

    var definition = new SearchIndex(indexName, searchFields);

    var suggester = new SearchSuggester("sg", new[] { "HotelName", "Category", "Address/City", "Address/StateProvince" });
    definition.Suggesters.Add(suggester);

    indexClient.CreateOrUpdateIndex(definition);
}
```

## <a name="property-reference"></a>属性参考

|属性      |说明      |
|--------------|-----------------|
|`name`        | 在建议器定义中指定，但也在自动完成或建议请求上调用。 |
|`sourceFields`| 在建议器定义中指定。 它是索引中作为建议内容源的一个或多个字段的列表。 字段的类型必须是 `Edm.String` 和 `Collection(Edm.String)`。 如果对该字段指定了分析器，则它必须是 [此列表](/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzername) 中的命名词法分析器， (不是自定义分析器) 。<p/> 作为最佳做法，请仅指定有助于生成预期相应响应的字段，无论该响应是搜索栏还是下拉列表中的已完成字符串。<p/>酒店名称就是一很好的候选项，因为它很精确。 说明和注释等详细字段过于密集。 同样，类别和标记等重复性字段的效率较低。 在示例中，我们仍然包含了“category”来演示可以包含多个字段。 |
|`searchMode`  | 仅 REST 参数，在门户中可见。 此参数在 .NET SDK 中不可用。 它指示用于搜索候选短语的策略。 目前支持的唯一模式是 `analyzingInfixMatching`，该模式目前匹配字词的开头。|

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester"></a>使用建议器

在查询中使用建议器。 创建建议器后，请调用以下 API 之一来实现“键入时搜索”体验：

+ [建议 REST API](/rest/api/searchservice/suggestions)
+ [自动完成 REST API](/rest/api/searchservice/autocomplete)
+ [SuggestAsync 方法](/dotnet/api/azure.search.documents.searchclient.suggestasync)
+ [AutocompleteAsync 方法](/dotnet/api/azure.search.documents.searchclient.autocompleteasync)

在搜索应用程序中，客户端代码应利用 [jQuery UI Autocomplete](https://jqueryui.com/autocomplete/) 之类的库来收集部分查询并提供匹配项。 有关此任务的详细信息，请参阅[将“自动完成”或“建议结果”功能添加到客户端代码](search-autocomplete-tutorial.md)。

以下对自动完成 REST API 的调用演示了 API 的用法。 此示例有两个要点。 首先，与所有查询一样，操作是针对索引的文档集合执行的，查询包含一个 search 参数，在本例中该参数提供部分查询。 其次，必须将 suggesterName 添加到请求。 如果未在索引中定义建议器，对自动完成或建议的调用将会失败。

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2020-06-30
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

## <a name="sample-code"></a>代码示例

+ [在 c # 中创建第一个应用 (第3课-添加搜索即类型) ](tutorial-csharp-type-ahead-and-suggestions.md) 示例演示建议的查询、自动完成和分面导航。 此代码示例在沙箱 Azure 认知搜索服务上运行，并使用预加载的已创建建议器的酒店索引，你只需按 F5 运行该应用程序。 无需订阅或登录。

## <a name="next-steps"></a>后续步骤

建议参阅以下文章来详细了解如何请求表述。

> [!div class="nextstepaction"]
> [向客户端代码添加“自动完成”和“建议”功能](search-autocomplete-tutorial.md)