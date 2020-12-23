---
title: 同义词 C# 示例
titleSuffix: Azure Cognitive Search
description: 本 C# 示例介绍如何向 Azure 认知搜索中的索引添加同义词功能。 同义词映射是等效术语的列表。 具有同义词支持的字段可扩展查询以包括用户提供的术语和所有相关的同义词。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/05/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 4b97b223ac180df7f8eb07ad8eaab66847f50776
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422988"
---
# <a name="example-add-synonyms-for-azure-cognitive-search-in-c"></a>示例：使用 C# 为 Azure 认知搜索添加同义词

使用同义词进行搜索时，在语义上被视为与输入字词等效的字词也属于匹配项，从而扩展了查询的范围。 例如，使用“car”进行搜索时，也可将包含“automobile”或“vehicle”字词的文档视为匹配项。 

在 Azure 认知搜索中，同义词在“同义词映射”中通过可将等效字词关联在一起的“映射规则”进行定义。   本示例介绍了用于添加同义词并将其与现有索引一起使用的基本步骤。

在此示例中，您将学习如何：

> [!div class="checklist"]
> * 使用 [SynonymMap 类](/dotnet/api/azure.search.documents.indexes.models.synonymmap)创建同义词映射。 
> * 设置应支持通过同义词进行查询扩展的字段上的 [SynonymMapsName 属性](/dotnet/api/azure.search.documents.indexes.models.searchfield.synonymmapnames) 。

可以照常查询启用了同义词的字段。 访问同义词不需其他的查询语法。

可以创建多个同义词映射，将其发布为在服务范围内可供任何索引使用的资源，然后引用要在字段级别使用的资源。 在查询时，除了搜索索引，Azure 认知搜索还会查看同义词映射（如果在查询所用的字段上指定了该映射）。

> [!NOTE]
> 可以通过编程方式创建同义词，但不能在门户中进行。

## <a name="prerequisites"></a>先决条件

教程要求如下：

* [Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure 认知搜索服务](search-create-service-portal.md)
* [Azure.Search.Documents 包](https://www.nuget.org/packages/Azure.Search.Documents/)

如果不熟悉 .NET 客户端库，请参阅 [如何在 .net 中使用 Azure 认知搜索](search-howto-dotnet-sdk.md)。

## <a name="sample-code"></a>代码示例

可在 [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms)上找到本示例中使用的示例应用程序的完整源代码。

## <a name="overview"></a>概述

使用前后查询来演示同义词的值。 在此示例中，示例应用程序执行查询并返回一个示例 "酒店" 索引，其中填充了两个文档。 首先，应用程序使用索引中未显示的字词和短语执行搜索查询。 其次，代码启用同义词功能，然后重新发出相同的查询，这一次基于同义词映射中的匹配项返回结果。 

以下代码演示了整个流程。

```csharp
static void Main(string[] args)
{
   SearchIndexClient indexClient = CreateSearchIndexClient();

   Console.WriteLine("Cleaning up resources...\n");
   CleanupResources(indexClient);

   Console.WriteLine("Creating index...\n");
   CreateHotelsIndex(indexClient);

   SearchClient searchClient = indexClient.GetSearchClient("hotels");

   Console.WriteLine("Uploading documents...\n");
   UploadDocuments(searchClient);

   SearchClient searchClientForQueries = CreateSearchClientForQueries();

   RunQueriesWithNonExistentTermsInIndex(searchClientForQueries);

   Console.WriteLine("Adding synonyms...\n");
   UploadSynonyms(indexClient);

   Console.WriteLine("Enabling synonyms in the test index...\n");
   EnableSynonymsInHotelsIndexSafely(indexClient);
   Thread.Sleep(10000); // Wait for the changes to propagate

   RunQueriesWithNonExistentTermsInIndex(searchClientForQueries);

   Console.WriteLine("Complete.  Press any key to end application...\n");

   Console.ReadKey();
}
```

## <a name="before-queries"></a>“启用前”查询

在 `RunQueriesWithNonExistentTermsInIndex` 中，使用“five star”、“internet”和“economy AND hotel”发出搜索查询。

```csharp
Console.WriteLine("Search the entire index for the phrase \"five star\":\n");
results = searchClient.Search<Hotel>("\"five star\"", searchOptions);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'internet':\n");
results = searchClient.Search<Hotel>("internet", searchOptions);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the terms 'economy' AND 'hotel':\n");
results = searchClient.Search<Hotel>("economy AND hotel", searchOptions);
WriteDocuments(results);
```

两个索引文档中的两个都不包含这些词，因此我们从第一个中获得以下输出 `RunQueriesWithNonExistentTermsInIndex` ：  **没有匹配的文档** 。

## <a name="enable-synonyms"></a>启用同义词

运行 "之前" 查询后，示例代码启用同义词。 启用同义词是一个两步过程。 首先，定义并上载同义词规则。 然后，将字段配置为使用它们。 `UploadSynonyms` 和 `EnableSynonymsInHotelsIndex` 中概述了此过程。

1. 将同义词映射添加到搜索服务。 在 `UploadSynonyms` 中，我们在同义词映射“desc-synonymmap”中定义了四条规则，并将其上传到服务。

   ```csharp
   private static void UploadSynonyms(SearchIndexClient indexClient)
   {
      var synonymMap = new SynonymMap("desc-synonymmap", "hotel, motel\ninternet,wifi\nfive star=>luxury\neconomy,inexpensive=>budget");

      indexClient.CreateOrUpdateSynonymMap(synonymMap);
   }
   ```

1. 将可搜索字段配置为允许在索引定义中使用同义词映射。 在 `AddSynonymMapsToFields` 中，我们对`category`和`tags`这两个字段启用了同义词功能，方法是将 `SynonymMapNames` 属性设置为新上传的同义词映射的名称。

   ```csharp
   private static SearchIndex AddSynonymMapsToFields(SearchIndex index)
   {
      index.Fields.First(f => f.Name == "category").SynonymMapNames.Add("desc-synonymmap");
      index.Fields.First(f => f.Name == "tags").SynonymMapNames.Add("desc-synonymmap");
      return index;
   }
   ```

   添加同义词映射时，索引重建不是必需的。 可以向服务添加同义词映射，并对任意索引中的现有字段定义进行修正，使之使用新的同义词映射。 添加新属性不影响索引可用性。 这同样适用于禁用字段同义词功能的情况。 可以直接将 `SynonymMapNames` 属性设置为空列表。

   ```csharp
   index.Fields.First(f => f.Name == "category").SynonymMapNames.Add("desc-synonymmap");
   ```

## <a name="after-queries"></a>“启用后”查询

上传同义词映射并对索引进行更新，允许其使用同义词映射以后，再次调用 `RunQueriesWithNonExistentTermsInIndex` 会获得如下输出：

```
Search the entire index for the phrase "five star":

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the term 'internet':

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the terms 'economy' AND 'hotel':

Name: Roach Motel       Category: Budget        Tags: [motel, budget]
```

第一个查询根据规则 `five star=>luxury` 查找文档。 第二个查询使用 `internet,wifi` 扩展了搜索，第三个查询在查找匹配的文档时同时使用 `hotel, motel` 和 `economy,inexpensive=>budget`。

添加同义词完全改观了搜索体验。 在本示例中，初始查询并没有返回有意义的结果，虽然索引中的文档是相关的。 启用同义词可以扩展索引，使之包括常用字词，但不更改索引中的基础数据。

## <a name="clean-up-resources"></a>清理资源

完成本示例后，最快的清理方式是删除包含 Azure 认知搜索服务的资源组。 现在，可以删除资源组以永久删除其中的所有内容。 在门户中，资源组名称显示在 Azure 认知搜索服务的“概述”页上。

## <a name="next-steps"></a>后续步骤

本示例使用 C# 代码演示了同义词功能，创建并发布了映射规则，然后在查询中调用同义词映射。 可以在 [.NET SDK](/dotnet/api/overview/azure/search.documents-readme) 和 [REST API](/rest/api/searchservice/) 参考文档中找到更多信息。

> [!div class="nextstepaction"]
> [如何在 Azure 认知搜索中使用同义词](search-synonyms.md)