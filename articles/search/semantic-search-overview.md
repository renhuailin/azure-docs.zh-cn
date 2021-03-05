---
title: 语义搜索
titleSuffix: Azure Cognitive Search
description: 了解认知搜索如何使用来自必应的深度学习语义搜索模型，使搜索结果更加直观。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.custom: references_regions
ms.openlocfilehash: e9cbb7daf61397064bd79f30d851d96fdf63f5a0
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102203226"
---
# <a name="semantic-search-in-azure-cognitive-search"></a>Azure 认知搜索中的语义搜索

> [!IMPORTANT]
> 语义搜索功能处于公共预览中，仅可通过预览版 REST API 提供。 在 [补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)下，预览版功能按原样提供。

语义搜索是查询相关功能的集合，支持更高质量、更自然的查询体验。 功能包括搜索结果的语义 reranking，以及带有语义突出显示的标题和答案生成。 从 [全文搜索引擎](search-lucene-query-architecture.md) 返回的排名靠前的50的结果是 reranked 的，用于找出最相关的匹配项。

基础技术利用必应和 Microsoft Research 的投资，并集成到认知搜索基础结构中。 若要使用它，需要对搜索请求进行少量修改，但不需要进行额外的配置或重新编制索引。

公共预览功能包括：

+ 语义排名模型，根据搜索查询词的上下文或语义含义对结果进行评分
+ 突出显示相关章节的语义标题
+ 查询的语义答案，还从结果制定
+ 拼写检查，在查询字词到达搜索引擎之前更正拼写错误

## <a name="semantic-search-architecture"></a>语义搜索体系结构

语义搜索的组件位于现有查询执行管道的顶层。  (不在关系图中显示拼写更正) 通过更正各个查询词中的录入项来改善召回。 完成所有分析和分析后，搜索引擎将检索与查询匹配的文档，并使用 [默认计分算法](index-similarity-and-scoring.md#similarity-ranking-algorithms)（BM25 或经典）对其进行评分，具体取决于创建服务的时间。 计分配置文件也在此阶段应用。 

收到 top 50 匹配项后， [语义排名算法](semantic-how-to-query-response.md) 将重新计算文档语料库。 结果可能包含超过50个匹配项，但只有第一个50会被 reranked。 对于排名，该算法将使用机器学习和传输学习来对文档重新评分，使其根据每个文档与查询意图的匹配程度。

若要创建标题和答案，将使用语言表示模型。 在标题中，该算法使用必应开发的语言模型，以最大程度地汇总查询结果的突出显示字幕。 如果搜索查询是一个问题，并请求答案，则类似的语言模型会标识最能回答搜索查询表示的问题的文本段落。

:::image type="content" source="media/semantic-search-overview/semantic-query-architecture.png" alt-text="查询管道中的语义组件" border="true":::

## <a name="availability-and-pricing"></a>可用性和定价

语义排名可通过 [注册注册](https://aka.ms/SemanticSearchPreviewSignup)获得，该搜索服务在以下某个区域的标准层 (S1，S2，S3) 上创建：美国中北部、美国西部、美国西部2、美国东部2、北欧西欧。 位于所述区域中 S1 或更高版本的现有搜索服务符合预览版 (无需) 创建新服务。

拼写更正在同一区域中可用，但没有层限制，且无需注册。 

在3月2日到4月1日从预览版开始，拼写更正和语义排名免费提供。 在4月1日之后，运行此功能的计算成本将成为计费事件。 预期成本为250000个查询大约 USD $ 500/月。 可以在 " [认知搜索定价" 页](https://azure.microsoft.com/pricing/details/search/) 和 " [估计和管理成本](search-sku-manage-costs.md)" 中找到详细的成本信息。

## <a name="next-steps"></a>后续步骤

新的查询类型启用语义搜索的相关性排名和响应结构。 [创建一个开始的语义查询](semantic-how-to-query-request.md) 。 或者，查看以下任一文章了解相关信息。

+ [向查询字词添加拼写检查](speller-how-to-add.md)
+ [语义排名和响应](semantic-how-to-query-response.md)