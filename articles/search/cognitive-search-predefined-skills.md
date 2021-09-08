---
title: 编制索引期间的内置文本和图像处理
titleSuffix: Azure Cognitive Search
description: 数据提取、自然语言和图像处理技能将语义和结构添加到 Azure 认知搜索扩充管道中的原始内容。
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: 161813b7da42a5697fdb595e6a7050923e28fca2
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123038887"
---
# <a name="built-in-skills-for-text-and-image-processing-during-indexing-azure-cognitive-search"></a>在编制索引期间用于文本和图像处理的内置技能（Azure 认知搜索）

本文介绍了 Azure 认知搜索提供的技能，你可以将这些技能包含在一个[技能组](cognitive-search-working-with-skillsets.md)中，用于从原始的非结构化文本和图像文件中提取内容和结构。 技能是以某种方式转换内容的原子操作。 该操作通常为识别或提取文字，但也可以是一种重新定义已经创建的扩充的实用技能。 通常，输出是基于文本的，因此可以在全文查询中使用。 

## <a name="built-in-skills"></a>内置技能

内置技能基于 Microsoft 的预定型模型，这意味着你无法使用自己的训练数据来训练模型。 调用认知资源 API 的技能依赖于这些服务，并在[附加资源](cognitive-search-attach-cognitive-services.md)时按认知服务即用即付价格计费。 其他技能通过 Azure 认知搜索计费，或是免费提供的实用技能。

下表枚举并描述了内置技能。

| OData 类型  | 说明 | 计费方式 |
|-------|-------------|-------------|
|[Microsoft.Skills.Text.CustomEntityLookupSkill](cognitive-search-skill-custom-entity-lookup.md) | 在用户自定义的单词和短语列表中查找文本。| Azure 认知搜索（[定价](https://azure.microsoft.com/pricing/details/search/)） |
| [Microsoft.Skills.Text.KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md) | 此技能使用预定型模型来检测基于术语放置、语言规则、与其他术语的接近度以及该术语在源数据内的异常程度的重要短语。 | 认知服务（[定价](https://azure.microsoft.com/pricing/details/cognitive-services/)） | 
| [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)  | 该技能使用预定型模型来检测使用语言的语言类型（每个文档一个语言 ID）。 在同一文本段中使用多种语言时，输出是主要使用的语言的 LCID。 | 认知服务（[定价](https://azure.microsoft.com/pricing/details/cognitive-services/)） | 
| [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md) | 将字段集合中的文本合并到单个字段中。  | 不适用 |
| [Microsoft.Skills.Text.V3.EntityLinkingSkill](cognitive-search-skill-entity-linking-v3.md) | 此技能使用预先训练的模型为已识别的实体生成指向 Wikipedia 文章的链接。 | 认知服务（[定价](https://azure.microsoft.com/pricing/details/cognitive-services/)） | 
| [Microsoft.Skills.Text.V3.EntityRecognitionSkill](cognitive-search-skill-entity-recognition-v3.md) | 此技能使用预定型模型为一组固定的类别构建实体：`"Person"`、`"Location"`、`"Organization"`、`"Quantity"`、`"DateTime"`、`"URL"`、`"Email"`、`"PersonType"`、`"Event"`、`"Product"`、`"Skill"`、`"Address"`、`"Phone Number"` 和 `"IP Address"` 字段。 | 认知服务（[定价](https://azure.microsoft.com/pricing/details/cognitive-services/)） | 
| [Microsoft.Skills.Text.PIIDetectionSkill](cognitive-search-skill-pii-detection.md)  | 此技能使用预先训练的模型从给定文本中提取个人信息。 此技能还提供了用于过滤文本中检测到的个人信息实体的各种选项。  | 认知服务（[定价](https://azure.microsoft.com/pricing/details/cognitive-services/)） | 
| [Microsoft.Skills.Text.V3.SentimentSkill](cognitive-search-skill-sentiment-v3.md)  | 此技能使用预先训练的模型，从而根据服务在句子和文档级别逐条记录找到的最高置信度分数来分配情绪标签（例如“消极”、“中立”和“积极”）。 | 认知服务（[定价](https://azure.microsoft.com/pricing/details/cognitive-services/)） | 
| [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md) | 将文本拆分为多页，以便以增量方式扩充或增加内容。 | 不适用 |
| [Microsoft.Skills.Text.TranslationSkill](cognitive-search-skill-text-translation.md) | 此技能使用预先训练的模型将输入文本转换为各种语言，以用于规范化或本地化用例。 | 认知服务（[定价](https://azure.microsoft.com/pricing/details/cognitive-services/)） | 
| [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md) | 此技能使用图像检测算法来识别图像的内容并生成文本说明。 | 认知服务（[定价](https://azure.microsoft.com/pricing/details/cognitive-services/)） | 
| [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md) | 光学字符识别。 | 认知服务（[定价](https://azure.microsoft.com/pricing/details/cognitive-services/)） |
| [Microsoft.Skills.Util.ConditionalSkill](cognitive-search-skill-conditional.md) | 允许根据条件进行筛选、分配默认值和合并数据。 | 不适用 |
| [Microsoft.Skills.Util.DocumentExtractionSkill](cognitive-search-skill-document-extraction.md) | 从扩充管道内的文件中提取内容。 | Azure 认知搜索（[定价](https://azure.microsoft.com/pricing/details/search/)）
| [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md) | 将输出映射到复杂类型（多部分数据类型，可用于全名、多行地址或姓氏和个人标识符的组合）。 | 不适用 |

## <a name="custom-skills"></a>自定义技能

[自定义技能](cognitive-search-custom-skill-web-api.md)是设计、开发和部署到 Web 的模块。 然后你可以从技能组内调用模块作为自定义技能。

| 类型  | 说明 | 计费方式 |
|-------|-------------|-------------|
| [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md) | 通过对自定义 Web API 执行 HTTP 调用，实现 AI 扩充管道的可扩展性 | 无，除非你的解决方案使用计费的 Azure 服务 |
| [Microsoft.Skills.Custom.AmlSkill](cognitive-search-aml-skill.md) | 允许使用 Azure 机器学习模型扩展 AI 扩充管道 | 无，除非你的解决方案使用计费的 Azure 服务 |

有关创建自定义技能的指导，请参阅[定义自定义界面](cognitive-search-custom-skill-interface.md)和[示例：为 AI 扩充创建自定义技能](cognitive-search-create-custom-skill-example.md)。

## <a name="see-also"></a>另请参阅

+ [如何定义技能集](cognitive-search-defining-skillset.md)
+ [自定义技能接口定义](cognitive-search-custom-skill-interface.md)
+ [教程：使用 AI 扩充的索引](cognitive-search-tutorial-blob.md)
