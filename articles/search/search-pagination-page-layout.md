---
title: 如何使用搜索结果
titleSuffix: Azure Cognitive Search
description: 对搜索结果进行组织和排序、获取记录计数并将内容导航添加到 Azure 认知搜索中的搜索结果。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/06/2021
ms.openlocfilehash: e810a5d3a94ad4e356d0b44a422b757b5710a781
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122182276"
---
# <a name="how-to-work-with-search-results-in-azure-cognitive-search"></a>如何在 Azure 认知搜索中使用搜索结果

本文介绍了如何在 Azure 认知搜索中规划查询响应。 响应的结构由查询中的参数决定：REST API 中的[搜索文档](/rest/api/searchservice/Search-Documents)，或 .NET SDK 中的 [SearchResults 类](/dotnet/api/azure.search.documents.models.searchresults-1)。 可通过以下方式使用查询上的参数来调整结果集的结构：

+ 对结果中的文档数量（默认为 50 个）进行限制或分批
+ 选择结果中要包含的字段
+ 对结果进行排序
+ 在搜索结果正文中突出显示完整匹配或部分匹配字词

## <a name="result-composition"></a>结果的构成

尽管搜索文档可能由大量的字段组成，但通常只需少量的几个字段就能表示结果集中的每个文档。 在查询请求中，追加 `$select=<field list>` 可以指定要在响应中显示的字段。 必须在索引中通过某个属性将字段指定为“可检索的”，才能在结果中包含该字段。 

最合适的字段包括能够对比和区分文档，并提供足够的信息来邀请用户一端做出点击响应的字段。 在电子商务网站上，这些字段可能是产品名称、说明、品牌、颜色、尺寸、价格和评级。 对于 hotels-sample-index 内置示例，它们可能是以下示例中的字段：

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30 
    {  
      "search": "sandy beaches",
      "select": "HotelId, HotelName, Description, Rating, Address/City"
      "count": true
    }
```

> [!NOTE]
> 若要在结果中包括图像文件（例如产品照片或徽标），请将其存储在 Azure 认知搜索外部，但在索引中包含一个字段，以引用搜索文档中的图像 URL。 支持在结果中包含图像的示例索引包括此[快速入门](search-create-app-portal.md)中专门介绍的 realestate-sample-us 演示，以及[纽约市工作岗位演示应用](https://aka.ms/azjobsdemo)。

### <a name="tips-for-unexpected-results"></a>意外结果提示

有时可能会出现预料外的结果内容（而不是结构）。 如果查询结果不符合预期，可以尝试对查询进行以下修改，然后查看结果是否有所改善：

+ 将 **`searchMode=any`** （默认）更改为 **`searchMode=all`** 可获取符合所有条件而不是某个条件的匹配项。 在查询包含布尔运算符时更应如此。

+ 使用不同的词法分析器或自定义分析器进行试验，看它是否改变了查询结果。 默认分析器会分解包含连字符的单词并将单词缩减为词根形式，这通常可以提高查询响应的稳定性。 但是，如果需要保留连字符，或者字符串中包含特殊字符，则你可能需要配置自定义分析器，以确保索引包含正确格式的标记。 有关详细信息，请参阅[部分字词搜索和包含特殊字符（连接符、通配符、正则表达式、模式）的模式](search-query-partial-matching.md)。

## <a name="paging-results"></a>分页结果

默认情况下，搜索引擎最多返回前 50 个匹配项。如果查询为全文搜索，将按搜索评分确定返回的结果；对于完全匹配查询，则按任意顺序返回结果。

若要返回不同数量的匹配文档，请将 `$top` 和 `$skip` 参数添加到查询请求。 以下列表解释了相关逻辑。

+ 添加 `$count=true` 可获取索引中匹配文档的总计数。

+ 返回第一组 15 个匹配文档，以及匹配项总计数：`GET /indexes/<INDEX-NAME>/docs?search=<QUERY STRING>&$top=15&$skip=0&$count=true`

+ 跳过前 15 个匹配文档，返回第二组 15 个文档：`$top=15&$skip=15`。 跳过前两组匹配文档，返回第三组 15 个匹配文档：`$top=15&$skip=30`

如果基础索引会发生变化，则无法保证分页查询的结果稳定。 分页会更改每页的 `$skip` 值，但每个查询是独立的，并针对查询时存在于索引中的当前数据视图运行（换言之，对于在常规用途数据库等位置中发现的结果，不存在结果缓存或快照）。
 
以下示例展示了如何获取重复项。 假设某个索引包含四个文档：

```text
{ "id": "1", "rating": 5 }
{ "id": "2", "rating": 3 }
{ "id": "3", "rating": 2 }
{ "id": "4", "rating": 1 }
```
 
现在假设你希望每次返回按评级排序的两个结果。 你将执行此查询来获取第一页结果：`$top=2&$skip=0&$orderby=rating desc`，将生成以下结果：

```text
{ "id": "1", "rating": 5 }
{ "id": "2", "rating": 3 }
```
 
在服务上，假设在两次查询调用之间将第五个文档添加到索引中：`{ "id": "5", "rating": 4 }`。  片刻之后，你执行查询来提取第二页：`$top=2&$skip=2&$orderby=rating desc`，将获得以下结果：

```text
{ "id": "2", "rating": 3 }
{ "id": "3", "rating": 2 }
```
 
请注意，文档 2 提取了两次。 这是因为，新文档 5 的评级值较大，因此它排在文档 2 的前面，并出现在第一页中。 尽管这种行为可能让人意外，但它却是搜索引擎的典型行为。

## <a name="ordering-results"></a>对结果排序

对于全文搜索查询，结果将按照搜索评分自动排名，搜索评分是根据文档中的字词频率和邻近性计算的（派生自 [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)），根据搜索字词，匹配项越多或者匹配程度越高的文档，其评分越高。 

搜索评分表达了对相关性的总体认知，反映了相对于同一结果集中的其他文档的匹配强度。 但是，评分在不同的查询之间并非始终一致，因此，在处理查询时，你可能会注意到搜索文档的排序方式存在细微差别。 下面对发生这种情况的可能原因给出了几条解释。

| 原因 | 说明 |
|-----------|-------------|
| 数据易变性 | 添加、修改或删除文档时，索引内容会发生变化。 在处理索引更新的过程中，字词频率会发生变化，从而影响了匹配文档的搜索评分。 |
| 多个副本 | 对于使用多个副本的服务，将并行针对每个副本发出查询。 用于计算搜索评分的索引统计信息是根据每个副本计算的，结果将在查询响应中合并和排序。 副本基本上是彼此的镜像，但由于状态存在细微差别，因此统计信息可能有所不同。 例如，一个副本可能删除了对统计信息有影响的文档，这些统计信息由其他副本合并而来。 通常，按副本的统计信息的差异在较小索引中更明显。 |
| 相同的评分 | 如果多个文档具有相同的评分，其中的任何一个文档都可能会首先出现。  |

### <a name="how-to-get-consistent-ordering"></a>如何获得一致的排序

如果一致的排序是一项应用程序要求，则可以在字段上显式定义 [`$orderby` 表达式](query-odata-filter-orderby-syntax.md)。 只有在编制索引时设置为 `sortable` 的字段才可用于对结果排序。 如果你指定了 `orderby` 参数的值来包括字段名称并且针对地理空间值调用了 [`geo.distance()` 函数](query-odata-filter-orderby-syntax.md)，则 `$orderby` 中常用的字段包括评级、日期和位置。  

提高一致性的另一种方法是使用[自定义评分配置文件](index-add-scoring-profiles.md)。 使用评分配置文件可以提高在特定字段中有匹配项的项的分数，从而可以让你更好地控制搜索结果中各个项的排名。 这一附加的评分逻辑有助于覆盖副本之间的细微差异，因为每个文档的搜索评分会在更大程度上拉开差距。 我们建议对此方法使用[排名算法](index-ranking-similarity.md)。

## <a name="hit-highlighting"></a>突出显示

命中项突出显示是指对结果中的匹配字词应用文本格式设置（例如粗体或黄色突出显示），以便轻松找到匹配项。 [查询请求](/rest/api/searchservice/search-documents)上提供了命中词突出显示说明。 

若要启用“命中词突出显示”，请添加 `highlight=[comma-delimited list of string fields]` 以指定将要使用突出显示的字段。 突出显示适合用于较长的内容字段（如“说明”字段），因为在这些字段中，匹配内容不是立即就能看到。 只有定义被特性化为“可搜索”的字段才适用“命中词突出显示”。

默认情况下，Azure 认知搜索最多为每个字段返回五处突出显示。 你可以通过在字段后面附加一个破折号并后跟一个整数来调整此数字。 例如，`highlight=Description-10` 在“说明”字段中返回最多 10 个匹配内容的突出显示。

格式设置应用于整个字词查询。 格式设置类型由标记 `highlightPreTag` 和 `highlightPostTag` 决定，代码负责完成响应过程（例如，应用粗体字体或黄色背景）。

在以下示例中，在 Description 字段中找到的字词“sandy”、“sand”、“beaches”和“beach”已标记为将突出显示。 在引擎中触发查询扩展的查询（例如模糊搜索和通配符搜索）对命中项突出显示的支持有限。

```http
GET /indexes/hotels-sample-index/docs/search=sandy beaches&highlight=Description?api-version=2020-06-30 
```

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30 
    {  
      "search": "sandy beaches",  
      "highlight": "Description"
    }
```

### <a name="new-behavior-starting-july-15"></a>新行为（从 7 月 15 日开始）

在 2020 年 7 月 15 日之后创建的服务将提供不同的突出显示体验。 在该日期之前创建的服务的突出显示行为不会有变化。 

新行为：

+ 仅返回与完整短语查询匹配的短语。 查询短语“super bowl”将返回如下所示的突出显示结果：

  ```json
  "@search.highlights": {
      "sentence": [
          "The <em>super</em> <em>bowl</em> is super awesome with a bowl of chips"
     ]
  ```

  请注意，“super”和“bowl”的其他实例没有任何突出显示结果，因为这些实例与完整短语不匹配。

编写实现命中项突出显示的客户端代码时，请注意此项更改。 请注意，除非创建全新的搜索服务，否则你不会受到影响。

## <a name="next-steps"></a>后续步骤

若要快速为客户端生成搜索页面，请考虑以下选项：

+ 门户中的[应用程序生成器](search-create-app-portal.md)可以创建带有搜索栏、分面导航和包含图像的结果区域的 HTML 页面。
+ [使用 C# 创建你的第一个应用](tutorial-csharp-create-first-app.md)教程介绍了如何构建正常运行的客户端。 示例代码演示了分页查询、命中项突出显示和排序。

多个代码示例包含一个 Web 前端接口，相关内容可参阅：[New York City Jobs 演示应用](https://aka.ms/azjobsdemo) 或 [JavaScript 示例代码（具有实时演示站点）](https://github.com/liamca/azure-search-javascript-samples)。
