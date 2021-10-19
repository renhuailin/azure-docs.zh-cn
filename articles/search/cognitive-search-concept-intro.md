---
title: AI 扩充概念
titleSuffix: Azure Cognitive Search
description: 通过预定义的认知技能和自定义 AI 算法，使用内容提取、自然语言处理 (NLP) 和图像处理在 Azure 认知搜索索引中创建可搜索的内容。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/10/2021
ms.custom: references_regions
ms.openlocfilehash: 7d3f76777b717051b7524585abf593f57fedc47d
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129707915"
---
# <a name="ai-enrichment-in-azure-cognitive-search"></a>Azure 认知搜索中的 AI 扩充

在 Azure 认知搜索中，AI 扩充是指内置认知技能和自定义技能，用于在索引编制过程中添加内容转换和生成。 扩充创建以前不存在的新信息：从图像中提取信息，从文本中检测情感、关键短语和实体等等。 扩充还向无差别文本中添加结构。 所有这些过程将产生使全文搜索更有效的文档。 在许多情况下，扩充的文档可用于除搜索以外的方案，例如知识挖掘。

扩充由附加到[索引器](search-indexer-overview.md)的[技能组](cognitive-search-working-with-skillsets.md)定义。 索引器将提取内容并进行设置，而技能组从映像、Blob 和其他非结构化数据源中识别、分析和创建新信息和结构。 扩充管道的输出是[搜索索引](search-what-is-an-index.md)或[知识存储](knowledge-store-concept-intro.md)。

![扩充管道关系图](./media/cognitive-search-intro/cogsearch-architecture.png "扩充管道概述")

技能组可以包含认知搜索中的内置技能或 [*自定义技能*](cognitive-search-create-custom-skill-example.md)中提供的嵌入外部处理。 自定义技能的示例可能包括面向特定领域（例如金融、科技出版或医疗）的自定义实体模块或文档分类器。

内置技能分为以下类别：

+ “自然语言处理”技能包括[实体识别](cognitive-search-skill-entity-recognition-v3.md)[语言检测](cognitive-search-skill-language-detection.md)[关键短语提取](cognitive-search-skill-keyphrases.md)操作、[情绪检测（包括观点挖掘）](cognitive-search-skill-sentiment-v3.md)和 [PII 检测](cognitive-search-skill-pii-detection.md)。 通过这些技能，非结构化文本在索引中映射为可搜索和可筛选的字段。

+ “图像处理”技能包括 [光学字符识别 (OCR)](cognitive-search-skill-ocr.md) 和[视觉特征](cognitive-search-skill-image-analysis.md)标识，例如面部检测、图像解释、图像识别（名人和地标）或属性（例如图像方向）。 这些技能创建图像内容的文本表示形式，这样就可以使用 Azure 认知搜索的查询功能来搜索这些内容了。

Azure 认知搜索中的内置技能基于认知服务 API 中预先训练的机器学习模型：[计算机视觉](../cognitive-services/computer-vision/index.yml)和[文本分析](../cognitive-services/text-analytics/overview.md)。 若要在内容处理期间利用这些资源，可以附加认知服务资源。

数据引入阶段应用了自然语言和图形处理，其结果会成为 Azure 认知搜索的可搜索索引中文档撰写内容的一部分。 数据作为 Azure 数据集的来源，然后使用任意所需的[内置技能](cognitive-search-predefined-skills.md)通过索引管道进行推送。  

## <a name="feature-availability"></a>功能可用性

AI 扩充在 Azure 认知服务也可用的区域中提供。 可在[各区域的 Azure 产品可用性](https://azure.microsoft.com/global-infrastructure/services/?products=search)页上检查 AI 扩充当前的可用性。 AI 扩充在所有支持的区域提供，但以下区域除外：

+ 澳大利亚东南部
+ 中国北部 2
+ 挪威东部
+ 德国中西部

如果搜索服务位于其中一个区域，你将无法创建和使用技能组，但所有其他搜索服务功能都可用且完全受支持。

## <a name="when-to-use-ai-enrichment"></a>何时使用 AI 扩充

如果原始内容为非结构化文本、图像内容或需要语言检测和翻译的内容，则应考虑扩充。 通过内置认知技能应用 AI，可以对此内容进行解锁，在搜索和数据科学应用中提高其价值和实用性。 

此外，如果你有要集成到管道中的开源、第三方或第一方代码，则可以考虑添加自定义技能。 标识各种文档类型的突出特征的分类模型属于此类别，但可以使用将值添加到内容的任何包。

### <a name="use-cases-for-built-in-skills"></a>内置技能用例

使用内置技能组合起来的[技能组](cognitive-search-defining-skillset.md)非常适合以下应用场景：

+ 识别扫描文档 (JPEG) 中的字样和手写文本的[光学字符识别 (OCR)](cognitive-search-skill-ocr.md) 可能是最常用的技能。 附加 OCR 技能将从 JPEG 文件中识别、提取和引入文本。

+ 多语言内容的[文本翻译](cognitive-search-skill-text-translation.md)是另一种常用的技能。 语言检测内置于文本翻译中，但如果只需要语料库中内容的语言代码，则还可以单独运行[语言检测](cognitive-search-skill-language-detection.md)。

+ 组合使用图像和文本的 PDF。 PDF 中的文本可以在索引期间提取，不需使用扩充步骤，但在添加图像并进行自然语言处理的情况下，所产生的结果通常比标准索引提供的结果要好。

+ 非结构化或半结构化的文档，其中包含的内容有固有的含义，或者其上下文隐藏在更大的文档中。 

  具体说来，Blob 通常包含大量的内容，这些内容打包到单个“字段”中。 将图像和自然语言处理技能附加到索引器以后，即可创建新信息，该信息存在于原始内容中，但在其他情况下并不显示为非重复字段。 一些对你有帮助的可用内置认知技能：[关键短语提取](cognitive-search-skill-keyphrases.md)和[实体识别](cognitive-search-skill-entity-recognition-v3.md)（例如人员、组织和位置）。

  另外，内置技能还可以用来通过文本拆分、合并和形状操作来重新构造内容。

### <a name="use-cases-for-custom-skills"></a>自定义技能用例

自定义技能可以支持更复杂的方案，例如识别表单，或者使用你提供的模型进行自定义实体检测，以及在[自定义技能 Web 界面](cognitive-search-custom-skill-interface.md)中进行包装。 自定义技能的一些示例：[表单识别器](../applied-ai-services/form-recognizer/overview.md)、集成[必应实体搜索 API](./cognitive-search-create-custom-skill-example.md)、[自定义实体识别](https://github.com/Microsoft/SkillsExtractorCognitiveSearch)。

## <a name="enrichment-steps"></a>扩充步骤 <a name="enrichment-steps"></a>

扩充管道由具有[技能组](cognitive-search-working-with-skillsets.md)的[索引器](search-indexer-overview.md)组成 。 技能组定义扩充步骤，索引器驱动技能组。 配置索引器时，可以包含将扩充内容发送到[搜索索引](search-what-is-an-index.md)的输出字段映射或定义[知识存储](knowledge-store-concept-intro.md)中数据结构的投影等属性。

编制索引后，可以通过搜索请求，使用所有[Azure 认知搜索支持的查询类型](search-query-overview.md)来访问内容。

### <a name="step-1-connection-and-document-cracking-phase"></a>步骤 1：连接和文档破解阶段

索引器使用索引器数据源中提供的信息连接到外部源。 当索引器连接到资源时，它将[“破解文档”](search-indexer-overview.md#document-cracking)以提取文本和图像。 图像内容可以路由到执行图像处理的技能，而文本内容则排队进行文本处理。 

![文档破解阶段](./media/cognitive-search-intro/document-cracking-phase-blowup.png "文档破解")

此步骤汇总将进行 AI 扩充的所有初始或原始内容。 对于每个文档，都将创建一个扩充树。 最初，树只是一个根节点表示形式，但它将在技能组执行期间增长并获取结构。

### <a name="step-2-skillset-enrichment-phase"></a>步骤 2：技能组扩充阶段

技能组定义在每个文档上执行的原子操作。 例如，对于从 PDF 中提取的文本和图像，技能组可能应用实体识别、语言检测或关键短语提取，以便在索引中生成原本未在源代码中提供的新字段。 

![扩充阶段](./media/cognitive-search-intro/enrichment-phase-blowup.png "扩充阶段")

 技能组既可以很精简，也可以是高度复杂的，它不仅确定处理的类型，而且还确定操作顺序。 大多数技能组包含大约三到五个技能。

技能组以及[输出字段映射](cognitive-search-output-field-mapping.md)（定义为索引器一部分）全面指定扩充管道。 有关将所有组成部分一起提取的详细信息，请参阅[定义技能集](cognitive-search-defining-skillset.md)。

在内部，管道生成扩充文档的集合。 可以确定要将扩充文档的哪些部分映射到搜索索引中可编制索引的字段。 例如，如果应用了关键短语提取和实体识别技能，则这些新字段将成为扩充文档的一部分，并可以映射到索引中的字段。 请参阅[注释](cognitive-search-concept-annotations-syntax.md)详细了解输入/输出的形成。

### <a name="step-3-indexing"></a>步骤 3：索引编制

索引编制是一个过程，其中原始和扩充的内容作为搜索索引中的字段引入，如果还要创建知识存储，则作为[投影](knowledge-store-projection-overview.md)。 使用隐式或显式字段映射将内容发送到正确的字段，这两者中可能会出现相同的扩充内容。

扩充内容在技能组执行期间生成，除非进行保存操作，否则内容只是临时的。 为使扩充内容显示在搜索索引中，索引器必须具有映射信息，使它可以将扩充内容发送到搜索索引中的字段。 [输出字段映射](cognitive-search-output-field-mapping.md)设置这些关联。

## <a name="saving-enriched-output"></a>保存扩充输出

在 Azure 认知搜索中，索引器保存它创建的输出。

[可搜索索引](search-what-is-an-index.md)是始终由索引器创建的输出之一。 索引规范是索引器要求，附加技能组时，技能组的输出以及从源直接映射的任何字段都将用于填充索引。 通常，特定技能的输出（例如关键短语或情绪评分）将引入到为此目的而创建的字段的索引中。

[知识存储](knowledge-store-concept-intro.md)是一个可选输出，用于下游应用（例如 知识挖掘）。 知识存储在技能组中定义。 其定义决定了你的已扩充文档是被投影为表还是对象（文件或 blob）。 表格投影非常适用于 Power BI 等工具中的交互式分析，而文件和 blob 通常用于数据科学或类似的流程中。

最后，索引器可以在 Azure Blob 存储中[缓存扩充文档](cognitive-search-incremental-indexing-conceptual.md)，以在后续技能组执行中重复使用。 缓存供内部使用。 缓存扩充将由稍后重新运行的同一技能组所使用。 如果技能组包括图像分析或 OCR，并且你希望避免重新处理图像文件所花费的时间和费用，则高速缓存是有帮助的。

索引和知识存储完全相互独立。 虽然必须附加索引以满足索引器要求，但如果唯一目标是知识存储，则填充后可以忽略该索引。 不过，请避免将其删除。 如果要重新运行索引器与技能组，则需要索引才能运行索引器。

## <a name="using-enriched-content"></a>使用扩充内容

处理完成后，你将获得由扩充文档组成的[搜索索引](search-what-is-an-index.md)，这些文档在 Azure 认知搜索中支持全文搜索。 开发者和用户可以通过[查询索引](search-query-overview.md)来访问管道生成的扩充内容。 该索引与可为 Azure 认知搜索创建的其他任何索引类似：可以使用自定义分析器补充文本分析、调用模糊搜索查询、添加筛选器，或尝试使用计分概要文件来调整搜索相关性。

你可能还具有一个[知识存储](knowledge-store-concept-intro.md)。 知识存储包含可在分析或机器学习等知识挖掘场景中使用的数据。 可以使用[存储资源管理器](knowledge-store-view-storage-explorer.md)、[Power BI](knowledge-store-connect-power-bi.md) 或连接到 Azure 存储的任何应用。

## <a name="checklist-a-typical-workflow"></a>清单：典型工作流

1. 在开始一个项目时，从一部分数据着手是有帮助的。 索引器和技能组设计是一个迭代过程，如果使用具有代表性的小型数据集，则可以更快地进行迭代。

1. 创建一个指定数据的连接的[数据源](/rest/api/searchservice/create-data-source)。

1. 创建一个[技能组](/rest/api/searchservice/create-skillset)以添加扩充。

1. 创建一个定义搜索索引的[索引架构](/rest/api/searchservice/create-index)。

1. 创建一个[索引器](/rest/api/searchservice/create-indexer)，将上述所有组件组合在一起。 创建或运行索引器将检索数据、运行技能组并加载索引。

1. 运行查询以评估结果，并修改代码以更新技能集、架构或索引器配置。

若要遍历以上步骤，请在重新生成管道之前[重置索引器](search-howto-reindex.md)，或者在每次运行时删除并重新创建对象（建议在使用免费层时采用此方式）。 还应[启用扩充缓存](cognitive-search-incremental-indexing-conceptual.md)以尽可能重复使用现有扩充。

## <a name="next-steps"></a>后续步骤

+ [快速入门：创建文本翻译和实体技能组](cognitive-search-quickstart-blob.md)
+ [快速入门：创建 OCR 图像技能组](cognitive-search-quickstart-ocr.md)
+ [教程：了解 AI 扩充 REST API](cognitive-search-tutorial-blob.md)
+ [技能组概念](cognitive-search-working-with-skillsets.md)
+ [知识存储概念](knowledge-store-concept-intro.md)
+ [创建技能集](cognitive-search-defining-skillset.md)
+ [创建知识存储](knowledge-store-create-rest.md)