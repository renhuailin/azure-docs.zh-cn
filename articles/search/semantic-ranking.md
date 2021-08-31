---
title: 语义排名
titleSuffix: Azure Cognitive Search
description: 了解语义排名算法在 Azure 认知搜索中的工作原理。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2021
ms.openlocfilehash: 61fb6de73567b7a83e2c4db2010c717a160aebe0
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114473981"
---
# <a name="semantic-ranking-in-azure-cognitive-search"></a>Azure 认知搜索中的语义排名

> [!IMPORTANT]
> 根据[补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，语义搜索为预览版。 它可通过 Azure 门户、预览版 REST API 和 beta 版本的 SDK 获得。 这些功能将计费。 有关详细信息，请参阅[可用性和定价](semantic-search-overview.md#availability-and-pricing)。

语义排名是查询执行管道的扩展，通过对初始结果集最匹配的项进行重新排名来提升精准率。 语义排名由基于转换器的大型网络提供支持，并为了捕获查询词的语义（不是关键字的语言匹配）而进行训练。 与[默认的相似性排名算法](index-ranking-similarity.md)相比，语义排名程序使用字词的上下文和含义来确定相关性。

语义排名既耗费资源又耗费时间。 要在查询操作的预期延迟内完成处理，将合并并减少语义排名程序的输入，以便尽快完成基础汇总和重新排名步骤。

## <a name="pre-processing"></a>预处理

在为相关性评分之前，必须将内容减少为可以由语义排名程序有效处理的可管理输入量。

1. 首先，内容缩减从用于关键字搜索的默认[相似性排名算法](index-ranking-similarity.md)返回的初始结果集开始。 对于任何给定查询，结果都可能是少量文档，其最大限制为 1000 个。 由于处理大量匹配项会花费很长时间，因此只有前 50 个匹配项才会进入语义排名。

   无论文档数为 1 还是 50，初始结果集都会建立文档语料库的首次迭代，用于进行语义排名。

1. 接下来在整个语料库中，将提取“searchFields”中每个字段的内容，并将其合并为一个长字符串。

1. 合并字符串后，任何过长的字符串都将被剪裁，确保总长度满足摘要汇总步骤的输入要求。

   此裁剪练习是为何在“searchFields”中首先定位简洁字段的原因，即确保这些字段包含在字符串中。 如果有大量文档，且文档中的字段包含大量文本，则将忽略超过最大限制的任何内容。

每个文档现在都由单个长字符串表示。

字符串由标记，而非字符或字词组成。 最大标记数为 128 个唯一标记。 出于估算目的，可以假定 128 个标记大致等效于长度为 128 个单词的字符串。 

> [!NOTE]
>标记化由可搜索字段上的分析器分配部分决定。 如果使用的是 nGram 或 EdgeNGram 等专用分析器，则可能需要从 searchFields 中排除该字段。 要深入了解如何标记字符串，请使用[测试分析器 REST API](/rest/api/searchservice/test-analyzer) 查看分析器的标记输出。

## <a name="extraction"></a>提取

缩减字符串后，现在可以通过计算机读取理解和语言表示模型传递减少的输入，以确定相对于查询，哪些句子和短语可以最准确地汇总文档。 此阶段从将要进入语义排名程序的字符串中提取内容。

汇总的输入是在准备阶段中为每个文档获取的长字符串。 汇总模型在每个字符串中都会找到最具代表性的一段。 此段还构成文档的[语义标题](semantic-how-to-query-request.md)。 每个标题都有纯文本版本和高亮版本，并且每个文档的标题通常少于 200 字。

如果指定了“answers”参数，如果以问题的形式提出查询，或者如果在长字符串中找到可能提供问题答案的一段，则还会返回[语义答案](semantic-answers.md)。

## <a name="semantic-ranking"></a>语义排名

1. 相对于提供的查询，标题会在概念和语义相关性方面进行评估。

   下图说明了“语义相关性”的含义。 以在金融、法律、地理或语法的语境中可以使用的术语“资本”为例。 如果一个查询包含来自同一矢量空间的术语（如“资本”和“投资”），则在同一群集中包含标记的文档比不包含标记的文档的评分更高。

   :::image type="content" source="media/semantic-search-overview/semantic-vector-representation.png" alt-text="上下文的向量表示形式" border="true":::

1. 根据标题的语义相关性，将 @search.rerankerScore 分配到每个文档。

1. 在对所有文档进行评分后，它们将按照分数列出，并包含在查询响应负载中。 有效负载包括答案、纯文本和突出显示的标题，以及在 select 子句中标记为可检索或指定的任何字段。

## <a name="next-steps"></a>后续步骤

语义排名在特定区域中的标准层上提供。 有关可用性和注册的详细信息，请参阅[可用性和定价](semantic-search-overview.md#availability-and-pricing)。 新的查询类型启用语义搜索的排名和响应结构。 要开始使用，请[创建一个语义查询](semantic-how-to-query-request.md)。

或查看有关默认排名的以下文章。 语义排名根据相似性排名程序返回初始结果。 了解查询执行和排名，有助于全面理解整个工作过程。

+ [Azure 认知搜索中的全文搜索](search-lucene-query-architecture.md)
+ [Azure 认知搜索中的相似性和评分](index-similarity-and-scoring.md)
+ [Azure 认知搜索中用于文本处理的分析器](search-analyzers.md)