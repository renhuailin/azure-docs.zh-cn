---
title: 相似性和评分概述
titleSuffix: Azure Cognitive Search
description: 说明相似性和评分的概念，以及开发人员可以执行哪些操作来自定义评分结果。
author: puneet-hariharan-MSFT
ms.author: puhariharan
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: cacf6097234a5a2c7ec5261623fe653e509b8d9f
ms.sourcegitcommit: 7c44970b9caf9d26ab8174c75480f5b09ae7c3d7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2021
ms.locfileid: "112981423"
---
# <a name="similarity-and-scoring-in-azure-cognitive-search"></a>Azure 认知搜索中的相似性和评分

本文介绍 Azure 认知搜索用于确定哪些匹配文档与查询最相关的两种相似度排名算法。 本文还引入了两种相关功能：计分概要文件（调整搜索分数的条件）和 featuresMode 参数（打开搜索分数以显示更多详细信息） 。

> [!NOTE]
> 第三种[语义重新排名算法](semantic-ranking.md)目前为公共预览版。 若要了解详细信息，请从[语义搜索概述](semantic-search-overview.md)开始。

## <a name="similarity-ranking-algorithms"></a>相似性排名算法

Azure 认知搜索支持两种相似性排名算法：

| 算法 | Score | 可用性 |
|-----------|-------|--------------|
| ClassicSimilarity | @search.score | 在 2020 年 7 月 15 日之前被所有搜索服务使用。 |
| BM25Similarity | @search.score | 由 7 月 15 日之后创建的所有搜索服务使用。 默认情况下，使用经典算法的旧服务可以[选择加入 BM25](index-ranking-similarity.md)。 |

“经典”和“BM25”都是类似于 TF-IDF 的检索函数，它们使用词频 (TF) 和逆向文档频率 (IDF) 作为变量来计算每个文档查询对的相关性分数，然后将其用于排名。“BM25”虽然在概念上与“经典”相似，但它以概率信息检索为基础进行改进。 BM25 还提供高级自定义选项，例如，允许用户确定如何根据匹配字词的字词频率调整相关性评分。

以下视频段快进到了 Azure 认知搜索中使用的正式版排名算法的说明。 你可以观看完整视频以了解更多背景知识。

> [!VIDEO https://www.youtube.com/embed/Y_X6USgvB1g?version=3&start=322&end=643]

## <a name="relevance-scoring"></a>相关性评分

评分是指针对全文搜索查询的搜索结果中返回的每个项计算搜索评分。 分数指示某一项在当前查询上下文中的相关性。 分数越高，项的相关度就越高。 在搜索结果中，根据为每项计算的搜索分数，按从高到低的顺序排列各项。 对于每个文档，分数将在响应中以“@search.score”的形式返回。

默认情况下会在响应中返回前 50 个结果，但你可以使用 $top 参数返回更少或更多的项（单个响应中最多可以包含 1000 个项），并可以使用 $skip 获取下一个结果集。

根据数据和查询的统计属性计算搜索分数。 Azure 认知搜索会查找与搜索词匹配的文档（与部分搜索词或全部搜索词匹配，具体取决于 [searchMode](/rest/api/searchservice/search-documents#query-parameters)），并优先列出包含该搜索词多个实例的文档。 如果搜索词在数据索引中很少见，但在文档中很常见，搜索分数仍升至更高。 这种计算相关性的方法的基本原理称为 TF-IDF（字词频率-逆向文档频率）。

搜索分数值可以在整个结果集中重复。 当多个命中具有相同的搜索评分时，相同评分项的顺序就不会是明确、稳定的。 再次运行查询，你可能会看到项偏移位置，尤其是使用免费服务或包含多个副本的可计费服务时。 对于具有相同分数的两项，无法保证先显示哪一个。

如果要打破重复评分之间的平局，可以添加一个 $orderby 子句以便先按评分排序，然后按另一个可排序字段（例如 `$orderby=search.score() desc,Rating desc`）排序。 有关详细信息，请参阅 [$orderby](search-query-odata-orderby.md)。

> [!NOTE]
> `@search.score = 1.00` 指示未评分或未排名的结果集。 评分在所有结果中是统一的。 如果查询形式是模糊搜索、通配符或正则表达式查询，或者采用了 $filter 表达式，则会出现未评分的结果。

<a name="scoring-statistics"></a>

## <a name="scoring-statistics-and-sticky-sessions"></a>评分统计信息和粘滞会话

为了实现可伸缩性，Azure 认知搜索会通过分片过程横向分布每个索引，这意味着，[索引的某些部分在物理上是独立的](search-capacity-planning.md#concepts-search-units-replicas-partitions-shards)。

默认情况下，文档的评分是根据分片中数据的统计属性计算的。 此方法对于大型数据集而言通常不会造成问题，与基于所有分片中的信息计算评分相比，此方法可提供更好的性能。 也就是说，使用这种性能优化可能会导致两个非常相似的文档（甚至相同的文档）最终可能出现不同的相关性评分（如果这些文档出现在不同的分片中）。

如果你偏向于基于所有分片中的统计属性计算评分，可以添加 scoringStatistics=global 作为[查询参数](/rest/api/searchservice/search-documents)（或者添加 "scoringStatistics": "global" 作为[查询请求](/rest/api/searchservice/search-documents)的正文参数）来执行此操作。

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs?scoringStatistics=global&api-version=2020-06-30&search=[search term]
  Content-Type: application/json
  api-key: [admin or query key]  
```

使用 scoringStatistics 可确保同一副本中的所有分片提供相同的结果。 也就是说，不同的副本相互之间可能略有不同，因为它们始终会随着索引的最新更改而更新。 在某些情况下，你可能希望用户在“查询会话”期间获得更一致的结果。 在这种情况下，可以提供 `sessionId` 作为查询的一部分。 `sessionId` 是你创建的用于引用唯一用户会话的唯一字符串。

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs?sessionId=[string]&api-version=2020-06-30&search=[search term]
  Content-Type: application/json
  api-key: [admin or query key]  
```

只要使用相同的 `sessionId`，就会尽力而为地以同一副本为目标，从而提高用户看到的结果的一致性。 

> [!NOTE]
> 反复使用相同的 `sessionId` 值可能会干扰跨副本对请求进行负载均衡，并对搜索服务的性能产生负面影响。 用作 sessionId 的值不能以“_”字符开头。

## <a name="scoring-profiles"></a>为配置文件评分

可以通过定义“计分概要文件”来自定义不同字段的排名方式。 借助计分概要文件，可以更好地控制搜索结果中的项排名。 例如，建议根据创收能力提升项、提升新项或提升库存时间太长的项。 

计分概要文件属于索引定义的一部分，由加权字段、函数和参数组成。 有关定义计分概要文件的详细信息，请参阅[计分概要文件](index-add-scoring-profiles.md)。

<a name="featuresMode-param"></a>

## <a name="featuresmode-parameter-preview"></a>featuresMode 参数（预览版）

[搜索文档](/rest/api/searchservice/preview-api/search-documents)请求有一个新的 [featuresMode](/rest/api/searchservice/preview-api/search-documents#featuresmode) 参数，该参数可以在字段级别提供有关相关性的其他详细信息。 尽管 `@searchScore` 是针对整个文档计算的（该文档在此查询的上下文中的相关性如何），但你可以通过 featuresMode 获取有关各个字段的信息，以 `@search.features` 结构表示。 该结构包含查询中使用的所有字段（查询中通过 searchFields 指定的特定字段，或索引中属性为“可搜索”的所有字段）。 对于每个字段，可获得以下值：

+ 在字段中找到的唯一标记数
+ 相似性得分，即字段内容相对于查询项的相似程度的度量
+ 字词频率，即在字段中找到查询项的次数

对于以“description”和“title”字段为目标的查询，包含 `@search.features` 的响应可能如下所示：

```json
"value": [
 {
    "@search.score": 5.1958685,
    "@search.features": {
        "description": {
            "uniqueTokenMatches": 1.0,
            "similarityScore": 0.29541412,
            "termFrequency" : 2
        },
        "title": {
            "uniqueTokenMatches": 3.0,
            "similarityScore": 1.75451557,
            "termFrequency" : 6
        }
```

可以使用[自定义评分解决方案](https://github.com/Azure-Samples/search-ranking-tutorial)中的这些数据点或使用相关信息来调试搜索相关性问题。

## <a name="see-also"></a>另请参阅

+ [计分配置文件](index-add-scoring-profiles.md)
+ [REST API 参考](/rest/api/searchservice/)
+ [搜索文档 API](/rest/api/searchservice/search-documents)
+ [Azure 认知搜索 .NET SDK](/dotnet/api/overview/azure/search)