---
title: 语义排名
titleSuffix: Azure Cognitive Search
description: 了解语义排名算法在 Azure 认知搜索中的工作原理。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: bf311eb2b2d0ff7a9c17380d2e384bc05c6f05f3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105562029"
---
# <a name="semantic-ranking-in-azure-cognitive-search"></a>Azure 认知搜索中的语义排名

> [!IMPORTANT]
> 语义搜索功能目前为公共预览版，仅可通过预览版 REST API 和门户使用。 根据[使用条款补充](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，预览版功能按原样提供，不保证与正式版实现的功能相同。 这些功能是计费功能。 有关详细信息，请参阅[可用性和定价](semantic-search-overview.md#availability-and-pricing)。

语义排名是查询执行管道的一个扩展，可通过对初始结果集最匹配的项进行重新排名来提升精准率。 语义排名由基于大型转换器的网络提供支持，它被训练用来捕获查询字词的语义，而不是针对关键字进行语言匹配。 与[默认的相似性排名算法](index-ranking-similarity.md)相比，语义排名程序使用字词的上下文和含义来确定相关性。

语义排名既耗费资源又耗费时间。 为了在查询操作的预期延迟内完成处理，请整合并减少向语义排名程序提供的输入，以便可以尽快完成基础的摘要和重新排名步骤。

## <a name="pre-processing"></a>预处理

在对相关性进行评分之前，必须将内容减少到易管理的输入数量，以便语义排名程序能够高效地处理这些输入。

1. 首先，让我们从用于关键字搜索的默认[相似性排名算法](index-ranking-similarity.md)返回的初始结果集开始减少内容。 对于任何给定的查询，结果可能是少量文档，最多不超过 1,000 个文档。 因为处理大量的匹配项需要的时间过长，所以只有前 50 个匹配项才会进行语义排名。

   无论文档计数是什么，不管是 1 还是 50，初始结果集都会建立文档语料库的第一次迭代以进行语义排序。

1. 接下来，将在语料库中提取“searchFields”中的每个字段的内容并将其组合成一个长字符串。

1. 在字符串合并之后，任何过长的字符串都会被剪裁，以确保总长度满足摘要步骤的输入要求。

   由于存在此剪裁操作，因此必须首先在“searchFields”中定位简明字段以确保它们包含在字符串中。 如果你的文档非常大，其中的字段包含大量文本，则会忽略超过最大限制的任何内容。

现在，每个文档都表示为一个长字符串。

> [!NOTE]
> 字符串由标记组成，不含字符或单词。 词汇切分部分取决于分析器在可搜索字段上进行的分配。 如果你使用的是专门的分析器，例如 nGram 或 EdgeNGram，则可能需要从 searchFields 中排除该字段。 若要了解字符串是如何切分的，可以使用[测试分析器 REST API](/rest/api/searchservice/test-analyzer) 查看分析器的标记输出。

## <a name="extraction"></a>提取

进行字符串缩减之后，现在可以通过机器阅读理解和语言表示模型来传递减少后的输入，以根据查询确定哪些句子和短语能够最好地对文档进行概括。 此阶段从字符串中提取将发送到语义排名程序的内容。

为摘要提供的输入是在准备阶段为每个文档获取的长字符串。 摘要模型会从每个字符串中查找最具代表性的段落。 此段落还构成文档的[语义标题](semantic-how-to-query-request.md)。 每个标题都有普通文本版本和突出显示版本，在每个文档中通常少于 200 字。

当指定了“answers”参数时，如果查询是作为问题提出的，并且在长字符串中可以找到可能提供了问题答案的段落，则还会返回[语义答案](semantic-answers.md)。

## <a name="semantic-ranking"></a>语义排名

1. 根据所提供的查询，对标题的概念和语义相关性进行评估。

   下图说明了“语义相关性”的含义。 考虑一下“capital”这个词，它可能用在财务、法律、地理或语法上下文中。 如果查询包含来自同一向量空间的字词（例如，“capital”和“investment”），则同时包含同一群集中的标记的文档将比不包含此类标记的文档得分更高。

   :::image type="content" source="media/semantic-search-overview/semantic-vector-representation.png" alt-text="上下文的向量表示形式" border="true":::

1. @search.rerankerScore 根据标题的语义相关性分配给每个文档。

1. 对所有文档进行评分后，它们将按分数降序列出，包含在查询响应有效负载中。 有效负载包括答案、普通文本标题和突出显示的标题，以及标记为可检索的任何字段或在 select 子句中指定的任何字段。

## <a name="next-steps"></a>后续步骤

语义排名在特定区域中的标准层上提供。 有关可用性和注册的详细信息，请参阅[可用性和定价](semantic-search-overview.md#availability-and-pricing)。 一个新的查询类型可实现语义搜索的排名和响应结构。 若要开始，请[创建一个语义查询](semantic-how-to-query-request.md)。

或者，查看以下有关默认排名的文章。 语义排名依赖于相似性排名程序来返回初始结果。 通过了解查询执行和排名，你可以全面了解整个过程的工作方式。

+ [Azure 认知搜索中的全文搜索](search-lucene-query-architecture.md)
+ [Azure 认知搜索中的相似性和评分](index-similarity-and-scoring.md)
+ [Azure 认知搜索中用于文本处理的分析器](search-analyzers.md)