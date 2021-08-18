---
title: 配置相似性算法
titleSuffix: Azure Cognitive Search
description: 了解如何在旧的搜索服务上启用 BM25，以及如何修改 BM25 参数以更好地适应索引内容。
author: puneet-hariharan-MSFT
ms.author: puhariharan
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: dc046516b154ac1e3b7a1361e78e157902b6bff6
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2021
ms.locfileid: "112968692"
---
# <a name="configure-the-similarity-ranking-algorithm-in-azure-cognitive-search"></a>在 Azure 认知搜索中配置相似性排名算法

Azure 认知搜索支持两种相似性排名算法：

+ 经典相似性算法，在 2020 年 7 月 15 日之前由所有搜索服务使用。
+ *Okapi BM25* 算法的一种实现，在 7 月 15 日之后创建的所有搜索服务中使用。

BM25 排名是新的默认算法，因为它往往能够生成更符合用户预期的搜索排名。 它附带了用于根据文档大小等因素优化结果的[参数](#set-bm25-parameters)。 

对于 2020 年 7 月 15 日之后创建的新服务，将自动使用 BM25，并且这是唯一的相似性算法。 如果你尝试在新服务上将相似性算法设置为 ClassicSimilarity，则会返回 HTTP 400 错误，因为服务不支持该算法。

对于在 2020 年 7 月 15 日之前创建的旧服务，经典相似性仍然是默认算法。 旧服务可以按索引升级到 BM25，如下所述。 如果从经典算法切换到 BM25，搜索结果的排序方式预期会出现一些差异。

> [!NOTE]
> 语义排名（目前对于选定区域中的标准服务以预览版提供）是有助于生成更相关结果的附加步骤。 与其他算法不同，它是循环访问现有结果集的附加功能。 有关详细信息，请参阅[语义搜索概述](semantic-search-overview.md)和[语义排名](semantic-ranking.md)。

## <a name="enable-bm25-scoring-on-older-services"></a>在旧服务上启用 BM25 评分

如果你运行的搜索服务是在 2020 年 7 月 15 日之前创建的，则可以通过在新索引上设置 Similarity 属性来启用 BM25。 该属性仅在新索引上公开，因此，如果需要在现有索引上启用 BM25，则必须删除该索引，然后在将新的 Similarity 属性设置为“Microsoft.Azure.Search.BM25Similarity”的情况下[重新生成索引](search-howto-reindex.md)。

存在具有 Similarity 属性的索引后，可以在 BM25Similarity 或 ClassicSimilarity 之间切换。 

以下链接介绍了 Azure SDK 中的 Similarity 属性。 

| 客户端库 | Similarity 属性 |
|----------------|---------------------|
| .NET  | [SearchIndex.Similarity](/dotnet/api/azure.search.documents.indexes.models.searchindex.similarity) |
| Java | [SearchIndex.setSimilarity](/java/api/com.azure.search.documents.indexes.models.searchindex.setsimilarity) |
| JavaScript | [SearchIndex.Similarity](/javascript/api/@azure/search-documents/searchindex#similarity) |
| Python | [SearchIndex 上的 similarity 属性](/python/api/azure-search-documents/azure.search.documents.indexes.models.searchindex) |

### <a name="rest-example"></a>REST 示例

还可以使用 [REST API](/rest/api/searchservice/create-index)，如下面的示例所示：

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=2020-06-30
{
    "name": "indexName",
    "fields": [
        {
            "name": "id",
            "type": "Edm.String",
            "key": true
        },
        {
            "name": "name",
            "type": "Edm.String",
            "searchable": true,
            "analyzer": "en.lucene"
        },
        ...
    ],
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity"
    }
}
```

## <a name="set-bm25-parameters"></a>设置 BM25 参数

BM25 相似性添加了两个用户可自定义参数来控制计算出的相关性评分。 可以在创建索引期间设置 BM25 参数；如果在创建索引期间指定了 BM25 算法，则还可以将 BM25 参数设置为索引更新。

| 属性 | 类型 | 说明 |
|----------|------|-------------|
| k1 | 数字 | 控制每个匹配字词的字词频率与文档-查询对的最终相关性分数之间的调整函数。 值通常为 0.0 到 3.0，默认值为 1.2。 </br></br>值 0.0 表示“二元模型”，其中，单个匹配字词的贡献对于所有匹配文档是相同的，而不管该字词在文本中出现多少次；使用较大的 k1 值可让评分随着在文档中找到同一字词的更多实例而不断提高。 </br></br>如果我们预期要在搜索查询中包含多个字词，则使用较大的 k1 值可能很重要。 在这种情况下，我们可能倾向于使用与多个不同的搜索查询字词匹配的文档，而不使用只与单个字词匹配多次的文档。 例如，在索引中查询包含字词“阿波罗宇宙飞行”的文档时，面对一篇有关希腊神话的、几十次提到字词“阿波罗”但未提到“宇宙飞行”的文章，以及一篇明确提到了字词“阿波罗”和“宇宙飞行”，但只是提到了几次的文章，我们可能希望降低前一篇文章的评分。 |
| b | 数字 | 控制文档长度如何影响相关性分数。 值介于 0 和 1 之间，默认值为 0.75。 </br></br>值 0.0 表示文档的长度不会影响评分，而值 1.0 则表示将按文档长度规范化字词频率对相关性评分的影响。 </br></br>如果我们想要惩罚较长的文档，则按文档长度规范化字词频率会很有用。 在某些情况下，与短得多的文档相比，较长的文档（例如整篇小说）更有可能包含很多不相关的字词。 |

### <a name="setting-k1-and-b-parameters"></a>设置 k1 和 b 参数

若要设置或修改 b 或 k1 值，请将它们添加到 BM25 相似性对象。 设置或更改现有索引的这些值会使索引至少脱机几秒钟，导致处于活动状态的索引和查询请求失败。 因此，应设置更新请求的“allowIndexDowntime=true”参数：

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=2020-06-30&allowIndexDowntime=true
{
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity",
        "b" : 0.5,
        "k1" : 1.3
    }
}
```

## <a name="see-also"></a>另请参阅  

+ [REST API 参考](/rest/api/searchservice/)
+ [将计分概要文件添加到索引](index-add-scoring-profiles.md)
+ [创建索引 API](/rest/api/searchservice/create-index)
+ [Azure 认知搜索 .NET SDK](/dotnet/api/overview/azure/search)