---
title: 语义搜索
titleSuffix: Azure Cognitive Search
description: 了解认知搜索如何使用必应的深度学习语义搜索模型来使搜索结果更加直观。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/25/2021
ms.custom: references_regions
ms.openlocfilehash: c55ca73d63fb380dece0350de482dcc9087bd315
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111744802"
---
# <a name="semantic-search-in-azure-cognitive-search"></a>Azure 认知搜索中的语义搜索

> [!IMPORTANT]
> 语义搜索目前处于公共预览阶段，在预览版 REST API 和门户可用。 根据[使用条款补充](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，预览版功能按原样提供，不保证与正式版实现的功能相同。 这些功能将计费。 有关详细信息，请参阅[可用性和定价](semantic-search-overview.md#availability-and-pricing)。

语义搜索是与查询相关的功能的集合，将语义相关性和语言理解添加到搜索结果。 本文简要综合地介绍了语义搜索，其中描述了每个功能以及它们是如何协同工作的。 嵌入的视频介绍了此项技术，结尾部分介绍了可用性和定价。

语义搜索是一项高级功能。 建议阅读本文以了解背景信息，但如果希望立即开始使用，请执行以下步骤：

1. [检查区域性和服务层要求](#availability-and-pricing)。
1. [注册预览版计划](https://aka.ms/SemanticSearchPreviewSignup)。 最多可能需要两个工作日来处理请求。
1. 在接受后，创建或修改查询，以返回[语义字幕和亮点](semantic-how-to-query-request.md)。
1. 添加更多查询属性，以便也返回[语义答案](semantic-answers.md)。
1. 还可以选择包含[拼写检查](speller-how-to-add.md)属性，以最大程度地提高精准率和召回率。

## <a name="what-is-semantic-search"></a>什么是语义搜索？

语义搜索是与查询相关的 AI 的可选层。 在搜索服务中启用时，它通过两种方式来扩展传统的查询执行管道。 首先，它会添加了一个可选的语义排名模型；其次，它会在响应中返回字幕和答案。

语义排名查找词语之间的上下文和相关性，从而提高使查询更有意义的匹配度。 语言理解在你的内容中找到摘要或字幕以及答案，并将它们包括在响应中，然后，这些内容可以呈现在搜索结果页面上，以获得更高效的搜索体验 。

先进的预训练模型将用于汇总和排名。 为了保持用户预期的搜索速度，语义汇总和排名仅适用于根据[默认相似性评分算法](index-similarity-and-scoring.md#similarity-ranking-algorithms)评分的前 50 个结果。 将这些结果用作文档语料库，语义排名会根据匹配项的语义强度对这些结果重新评分。

基础技术来自必应和 Microsoft Research，并作为附加功能集成到认知搜索基础结构。 有关用于支持语义搜索的研究和 AI 投入的详细信息，请参阅[必应的 AI 功能如何为 Azure 认知搜索提供支持（Microsoft Research 博客）](https://www.microsoft.com/research/blog/the-science-behind-semantic-search-how-ai-from-bing-is-powering-azure-cognitive-search/)。

以下视频概要介绍功能。

> [!VIDEO https://www.youtube.com/embed/yOf0WfVd_V0]

## <a name="feature-description-and-workflow"></a>功能说明和工作流

语义搜索通过添加以下功能提高精准率和召回率：

| 功能 | 说明 |
|---------|-------------|
| [拼写检查](speller-how-to-add.md) | 在查询词语到达搜索引擎之前纠正拼写错误。 |
| [语义排名](semantic-ranking.md) | 使用上下文或语义含义计算相关性分数。 |
| [语义标题和突出显示](semantic-how-to-query-request.md) | 在文档中，突出显示对内容进行最佳概括的句子和短语中的关键片段，以便于扫描。 当单个内容字段在结果页中过于密集时，汇总结果的标题就显得非常有用。 突出显示的文本表明最相关的词语和短语，以便用户可以快速确定为何匹配项被视为相关。 |
| [语义答案](semantic-answers.md) | 从语义查询返回的可选的附加子结构。 它为与问题类似的查询提供直接答案。 |

### <a name="order-of-operations"></a>运算顺序

语义搜索的组件在两个方向扩展现有查询执行管道。 如果启用拼写更正，拼写[检查](speller-how-to-add.md)器会在词语到达搜索引擎之前，以及查询开始时纠正拼写错误。

:::image type="content" source="media/semantic-search-overview/semantic-workflow.png" alt-text="查询执行中的语义组件" border="true":::

查询执行照常进行，包括词语解析、分析和扫描倒排索引。 引擎使用标记匹配检索文档，并使用[默认相似性评分算法](index-similarity-and-scoring.md#similarity-ranking-algorithms)对结果进行评分。 分数根据查询词语和索引中的匹配词语之间的语言相似性来计算。 如果已对其进行定义，此阶段还会应用计分概要文件。 然后将结果传递到语义搜索子系统。

在准备步骤中，将在句子和段落级别对从初始结果集中返回的文档语料库进行分析，以查找汇总每个文档的段落。 与关键字搜索不同，此步骤使用机器阅读和理解来评估内容。 通过此内容处理阶段，语义查询将返回[标题](semantic-how-to-query-request.md)和[答案](semantic-answers.md)。 为了构建标题，语义搜索使用语言表示形式提取和突出显示对结果进行最佳概括的关键片段。 如果搜索查询是一个问题，并且要求回答，则响应将包括一个可以最好地回答该问题的文本段落，如搜索查询所示。 

将现有文本用于构建标题和答案。 语义模型无法根据现有内容撰写新句子或短语，也无法应用得出新结论的逻辑。 简而言之，该系统永远不会返回不存在的内容。

然后，根据查询词语的[概念相似性](semantic-ranking.md)重新对结果评分。

要在查询中使用语义功能，需要对[搜索请求](semantic-how-to-query-request.md)进行少量修改，但是无需额外的配置或重新索引。

## <a name="semantic-capabilities-and-limitations"></a>语义功能和限制

语义搜索是一项较新的技术，因此，对于它能够和无法实现的效果，必须设定预期。

它通过两种方式来改善搜索结果的质量。 首先，在语义上更接近原始查询意图的文档升级是一个显著优点。 其次，在字幕和潜在的答案都出现在页面上时，可以更加直接地使用结果。 在任何时候，该引擎都会处理现有的内容。 语义搜索中使用的语言模型旨在提取看似答案的完整字符串，而不会尝试编写新字符串来作为某个查询的答案，或作为某个匹配文档的字幕。

语义搜索不是逻辑引擎，并且不会从文档或文档语料库内的不同内容片段中推断信息。 例如，如果“沙漠中的度假酒店”查询缺少地理输入，则引擎将不会生成亚利桑那或内华达的酒店的匹配项，虽然这两个州都有沙漠。 同样，如果查询包含子句“过去 5 年中”，则引擎不会根据要返回的当前日期计算时间间隔。

在认知搜索中，对上述情形可能有帮助的机制包括[同义词映射](search-synonyms.md)或[日期筛选器](search-query-odata-filter.md)，前者可用于在表面上不同的术语间生成关联，后者可指定为 OData 表达式。

## <a name="availability-and-pricing"></a>可用性和定价

语义搜索可通过[注册登记](https://aka.ms/SemanticSearchPreviewSignup)获得。 在 3 月 2 日推出预览版到 7 月初之间，语义功能免费提供。

| 功能 | 层 | 区域 | 注册 | 预计定价 |
|---------|------|--------|---------------------|-------------------|
| 语义搜索（字幕、亮点、答案） | 标准层 (S1、S2、S3) | 美国中北部、美国西部、美国西部 2、美国东部 2、北欧、西欧 | 必须 | 从 7 月初开始，预期定价为每月 500 美元，适用于前 250,000 个查询，每增加 1,000 个查询收费 2 美元。  |
| 拼写检查 | 任意 | 美国中北部、美国西部、美国西部 2、美国东部 2、北欧、西欧 | 必须 | 无（免费） |

语义功能和拼写检查都有一个[注册登记](https://aka.ms/SemanticSearchPreviewSignup)。 

没有语义搜索也可免费使用拼写检查。 如果查询请求包括 `queryType=semantic` 并且搜索字符串不为空（例如 `search=pet friendly hotels in new york`），则会对语义搜索收费。 空搜索（`search=*` 的查询）不收费。

[认知搜索定价页面](https://azure.microsoft.com/pricing/details/search/)和[估算和管理成本](search-sku-manage-costs.md)中将会介绍最终的定价信息。

## <a name="next-steps"></a>后续步骤

在符合上一部分所述层级和区域要求的搜索服务上[注册](https://aka.ms/SemanticSearchPreviewSignup)预览版。

最多可能需要两个工作日来处理请求。 在服务准备就绪后，请[创建语义查询](semantic-how-to-query-request.md)来查看语义排名的运行状况。
