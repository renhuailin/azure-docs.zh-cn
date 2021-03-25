---
title: 语义搜索
titleSuffix: Azure Cognitive Search
description: 了解认知搜索如何使用必应的深度学习语义搜索模型来使搜索结果更加直观。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/05/2021
ms.custom: references_regions
ms.openlocfilehash: 19b7f9bc19bec989e524dce7172037025e2fe4fd
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2021
ms.locfileid: "102432973"
---
# <a name="semantic-search-in-azure-cognitive-search"></a>Azure 认知搜索中的语义搜索

> [!IMPORTANT]
> 语义搜索功能以公共预览版提供，仅可通过预览版 REST API 使用。 根据[补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，预览功能按原样提供。

语义搜索是与查询相关的功能的集合，提供质量更高更自然的查询体验。 功能包括搜索结果的语义性重新排名，以及生成带有语义性突出显示的标题和答案。 对[全文搜索引擎](search-lucene-query-architecture.md)返回的前 50 个结果进行重新排名，以找到最相关的匹配项。

基础技术来自必应和 Microsoft Research，并已集成到认知搜索基础结构中。 有关用于支持语义搜索的研究和 AI 投入的详细信息，请参阅[必应的 AI 功能如何为 Azure 认知搜索提供支持（Microsoft Research 博客）](https://www.microsoft.com/research/blog/the-science-behind-semantic-search-how-ai-from-bing-is-powering-azure-cognitive-search/)。

要在查询中使用语义搜索，需要对搜索请求进行少量修改，但是不需要额外的配置或重新索引。

公共预览版功能包括：

+ 使用上下文或语义含义来计算相关性分数的语义排名模型
+ 语义标题，用于汇总结果中的关键段落，以便于扫描
+ 查询的语义答案（如果查询是一个问题）
+ 突出关键短语和术语的语义要点
+ 拼写检查，用于在查询术语到达搜索引擎之前纠正拼写错误

## <a name="availability-and-pricing"></a>可用性和定价

通过[登录注册](https://aka.ms/SemanticSearchPreviewSignup)，将在以下某个区域中标准层（S1、S2、S3）上创建的搜索服务中提供语义排名：美国中北部、美国西部、美国西部 2、美国东部 2、北欧、西欧。 拼写纠错可在同一区域使用，但没有层级限制。 如果现有服务符合层级和区域标准，则只需注册即可使用。

在 3 月 2 日至 4 月 1 日预览版发布期间，将免费提供拼写纠错和语义排名功能。 在 4 月 1 日之后，将对运行此功能的计算进行计费。 250,000 个查询的所需成本约为 500 美元/月。 可以在[“认知搜索定价”页面](https://azure.microsoft.com/pricing/details/search/)和[估算和管理成本](search-sku-manage-costs.md)中找到详细的成本信息。

## <a name="semantic-search-architecture"></a>语义搜索体系结构

语义搜索的组件位于现有查询执行管道的顶层。 拼写纠错（图中未显示）可通过更正各个查询术语中的错别字来提高召回率。 完成解析和分析后，搜索引擎将检索与查询匹配的文档，并根据创建服务的时间使用[默认评分算法](index-similarity-and-scoring.md#similarity-ranking-algorithms)（BM25 或经典）对其进行评分。 此阶段还会应用计分概要文件。

收到前 50 个匹配项后，[语义排名模型](semantic-how-to-query-response.md)将重新评估文档集。 结果可以包含超过 50 个匹配项，但是仅对前 50 个匹配项进行重新排名。 在排名时，该模型将结合使用机器学习和迁移学习来基于每个文档与查询意图的匹配程度对文档进行重新评分。

为了创建标题和答案，语义搜索使用语言表示形式来提取和突出显示最能概括结果的关键段落。 如果搜索查询是一个问题，并且要求回答，则响应将包括一个答案最佳的文本段，如搜索查询所示。

:::image type="content" source="media/semantic-search-overview/semantic-query-architecture.png" alt-text="查询管道中的语义组件" border="true":::

## <a name="next-steps"></a>后续步骤

新的查询类型启用语义搜索的相关性排名和响应结构。

若要开始使用，请[创建一个语义查询](semantic-how-to-query-request.md)， 或查看以下任何一篇文章，以了解相关信息。

+ [在查询术语中添加拼写检查](speller-how-to-add.md)
+ [语义排名和响应（答案和标题）](semantic-how-to-query-response.md)