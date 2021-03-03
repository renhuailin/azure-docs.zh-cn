---
title: 配置排名相似性算法
titleSuffix: Azure Cognitive Search
description: 如何设置相似性算法，以试用新的相似性算法进行排名
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 9f806b512ae8e118fca8f32115c8be3b493fd681
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101677791"
---
# <a name="configure-ranking-algorithms-in-azure-cognitive-search"></a>在 Azure 中配置排名算法认知搜索

Azure 认知搜索支持两个相似性排名算法：

+ *典型的相似性* 算法，所有搜索服务都在2020年7月15日之前使用。
+ *OKAPI BM25* 算法的实现，在7月15日之后创建的所有搜索服务中使用。

BM25 排名是新的默认值，因为它往往会生成搜索排名，使其更好地满足用户的期望。 它还启用了根据文档大小等因素优化结果的配置选项。 对于 2020 年 7 月 15 日之后创建的新服务，将自动使用 BM25，并且这是唯一的相似性算法。 如果尝试在新服务上设置 ClassicSimilarity 的相似性，将返回 HTTP 400 错误，因为服务不支持该算法。

对于在2020年7月15日之前创建的旧服务，经典相似性仍为默认算法。 旧版服务可以设置搜索索引的属性以调用 BM25，如下所述。 如果从经典模型切换到 BM25，则可能会出现一些与搜索结果的排序方式不同的区别。

> [!NOTE]
> 语义搜索是附加的语义重新排名算法，用于缩小预期和结果之间的差距。 不同于其他算法，它是一项可循环访问现有结果集的附加功能。 若要使用预览语义搜索算法，您必须创建一个新服务，并且必须指定 [语义查询类型](semantic-how-to-query-request.md)。 有关详细信息，请参阅 [语义搜索概述](semantic-search-overview.md)。

## <a name="create-a-search-index-for-bm25-scoring"></a>为 BM25 评分创建搜索索引

如果运行的是在2020年7月15日之前创建的搜索服务，则可以在索引定义中将相似性属性设置为 BM25Similarity 或 ClassicSimilarity。 如果 similarity 属性被省略或设置为 null，则索引将使用经典算法。

只能在创建索引时设置相似性算法。 但是，使用 BM25 创建索引后，可以更新现有索引以设置或修改 BM25 参数。

| 客户端库 | 相似性属性 |
|----------------|---------------------|
| .NET  | [SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex.similarity) |
| Java | [SearchIndex.setSimilarity](/java/api/com.azure.search.documents.indexes.models.searchindex.setsimilarity) |
| JavaScript | [SearchIndex](/javascript/api/@azure/search-documents/searchindex#similarity) |
| Python | [SearchIndex 上的相似性属性](/python/api/azure-search-documents/azure.search.documents.indexes.models.searchindex) |

### <a name="rest-example"></a>REST 示例

你还可以使用 [REST API](/rest/api/searchservice/create-index)，如下面的示例所示：

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

## <a name="bm25-similarity-parameters"></a>BM25 相似性参数

BM25 相似性添加了两个用户可自定义参数来控制计算出的相关性评分。 如果在创建索引期间指定了 BM25 算法，则可在创建索引期间设置 BM25 参数，或将其设置为索引更新。

| 属性 | 类型 | 说明 |
|----------|------|-------------|
| k1 | 数字 | 控制每个匹配字词的字词频率与文档查询对的最终关联分数之间的缩放功能。 值通常为0.0 到3.0，默认值为1.2。 </br></br>如果值为0.0，则表示 "二进制模型"，其中，对于所有匹配的文档，单个匹配术语的贡献相同，而无论该字词在文本中出现的次数如何，使用较大的版 k1 值可以在文档中找到同一术语的更多实例时继续增加。 </br></br>如果我们预期要在搜索查询中包含多个字词，则使用较大的 k1 值可能很重要。 在这种情况下，我们可能倾向于使用与多个不同的搜索查询字词匹配的文档，而不使用只与单个字词匹配多次的文档。 例如，在查询包含术语 "Apollo Spaceflight" 的文档的索引时，我们可能希望将包含术语 "Apollo" 的一篇文章的得分降低了几次，而不提及 "Spaceflight"，而另一篇文章只是显式提到了 "Apollo" 和 "Spaceflight"。 |
| b | 数字 | 控制文档长度对相关性分数的影响。 值介于0和1之间，默认值为0.75。 </br></br>值 0.0 表示文档的长度不会影响评分，而值 1.0 则表示将按文档长度规范化字词频率对相关性评分的影响。 </br></br>如果我们想要惩罚较长的文档，则按文档长度规范化字词频率会很有用。 在某些情况下，与短得多的文档相比，较长的文档（例如整篇小说）更有可能包含很多不相关的字词。 |

### <a name="setting-k1-and-b-parameters"></a>设置 k1 和 b 参数

若要设置或修改 b 或版 k1 值，请将它们添加到 BM25 相似性对象。 设置或更改现有索引的这些值将使索引脱机至少几秒钟，从而导致活动索引和查询请求失败。 因此，应设置更新请求的 "allowIndexDowntime = true" 参数：

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