---
title: 什么是 Azure 应用 AI 服务？
titleSuffix: Azure Applied AI Services
description: 应用 AI 服务说明。
keywords: 应用 ai 服务, 人工智能, 应用 ai, ai 服务, 认知搜索, 应用 ai 功能
author: jeffme
manager: nitinme
ms.service: applied-ai-services
ms.subservice: ''
ms.topic: overview
ms.date: 05/04/2021
ms.author: jeffme
ms.openlocfilehash: 102e8717b1bb6072eb0a52efc6ac3ecdd3e9c165
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124837340"
---
# <a name="what-are-azure-applied-ai-services"></a>什么是 Azure 应用 AI 服务？

Azure 应用 AI 服务是一项高级服务，旨在让开发人员能够将 AI 应用于关键业务场景，从而快速释放数据价值。  Azure 应用 AI 服务基于 Azure 认知服务的 AI API 而构建，针对关键任务进行了优化，这些任务包括监视和诊断指标异常、从文档中挖掘知识、通过听录分析增强客户体验、提高课堂读写能力、文档理解，等等。 以往，公司必须协调多项 AI 技能、添加业务逻辑，并创建 UI，才能根据其场景进行开发和部署（所有操作都会耗费时间、专业知识和资源）；这些“特定于方案”的服务可让开发人员立即获享这些好处。

## <a name="azure-form-recognizer"></a>Azure 表单识别器

使所有行业的组织都能使用文档中的隐藏信息来提高工作效率，实现自动化的业务流程并生成知识和见解。  Azure 表单识别器是一项可让你使用机器学习技术构建自动化数据处理软件的服务。 标识并提取文档中的文本、键/值对、选择标记、表和结构。 该服务会输出结构化数据，其中包含原始文件中的关系、边界框、置信度，等等。 可以快速获取根据特定内容定制的准确结果，无需进行繁琐的手动干预，也不需要具备丰富的数据科学专业知识。 使用表单识别器可自动在应用程序中输入数据，并扩充文档搜索功能。  Azure 表单识别器使用 Azure 认知服务中的 OCR、文本分析和自定义文本构建而成。

表单识别器由自定义文档处理模型、预生成的发票、收据、身份证和名片模型以及布局模型组成。 

[详细了解 Azure 表单识别器](./form-recognizer/index.yml)

## <a name="azure-metrics-advisor"></a>Azure 指标顾问

让组织能够根据来自业务、服务和物理资产指标的情报做出正确决策，从而保护组织的发展。  Azure 指标顾问使用 AI 在时序数据中执行数据监视和异常情况检测。 该服务自动执行将模型应用于数据的过程，并提供了一组 API 和基于 Web 的工作区，用于数据引入、异常检测和诊断，无需了解机器学习即可使用。 开发人员可以在服务之上构建 AIOps、预测性维护和业务监视应用程序。  Azure 指标顾问使用 Azure 认知服务中的异常检测器构建而成。

[详细了解 Azure 指标顾问](./metrics-advisor/index.yml)

## <a name="azure-cognitive-search"></a>Azure 认知搜索

可释放所有内容中有价值的隐藏信息，以便采取措施或做出决策。  Azure 认知搜索是唯一一种具有内置 AI 功能的云搜索服务，可扩充所有类型的信息来帮助你大规模识别并探索相关内容。 使用视觉、语言和语音认知技能，或使用自定义机器学习模型从所有类型的内容中挖掘见解。 Azure 认知搜索还提供语义搜索功能，该功能使用高级机器学习技术来了解用户意向并根据上下文对相关度最高的搜索结果进行排名。 增加用于创新的时间，减少用于维护复杂云搜索解决方案的时间。  Azure 认知搜索使用 Azure 认知服务中的计算机视觉和文本分析构建而成。

[详细了解 Azure 认知搜索](../search/index.yml)

## <a name="azure-immersive-reader"></a>Azure 沉浸式阅读器

使用 AI 提高阅读理解和阅读成就。 Azure 沉浸式阅读器是一款经过广泛设计的工具，它实现了成熟的技术，旨在提高新的读者、语言学习者和有学习差异（如阅读障碍）的用户的阅读理解能力。 通过沉浸式阅读器客户端库，你可利用 Microsoft Word 和 Microsoft OneNote 中所用的同种技术来改进 Web 应用程序。 Azure 沉浸式阅读器使用 Azure 认知服务中的翻译和文本转语音构建而成。

[详细了解 Azure 沉浸式阅读器](./immersive-reader/index.yml)

## <a name="azure-bot-service"></a>Azure 机器人服务

使用预先构建的对话组件快速创建可自定义的高级对话体验，从而立即实现业务价值。  Azure 机器人服务 Composer 是一个开源视觉对象创作画布，供开发人员和多学科团队构建机器人。 Composer 集成了语言理解服务（如 LUIS 和 QnA Maker），支持使用语言生成功能精心撰写机器人回复。 Azure 机器人服务使用 Azure 认知服务中的语音/电话服务、LUIS 和 QnA Maker 构建而成。

[详细了解 Azure 机器人服务](/composer/)

## <a name="azure-video-analyzer"></a>Azure 视频分析器 

使企业无需成为视频或 AI 专家，就能构建由视频智能提供支持的自动化应用。  Azure 视频分析器是一项用于构建基于 AI 的视频解决方案和应用程序的服务。 你可从视频流中生成实时业务见解、处理源附近的数据并应用所选 AI。 记录边缘或云中感兴趣的视频，并将它们与其他数据结合起来，协助你做出业务决策。  Azure 视频分析器使用 Azure 认知服务中的空间分析构建而成。  Azure 视频分析器媒体版使用 Azure 认知服务中的人脸、语音、翻译、文本分析、自定义视觉和文本内容审核构建而成。  

[详细了解 Azure 视频分析](../azure-video-analyzer/index.yml)

## <a name="certifications-and-compliance"></a>认证和合规性

应用 AI 服务已获得 CSA STAR 认证、FedRAMP Moderate 和 HIPAA BAA 等认证。 可以[下载](/samples/browse/?redirectedfrom=TechNet-Gallery "下载")认证进行自己的审核和安全评审。

若要了解隐私和数据管理，请访问[信任中心](https://servicetrust.microsoft.com/ "信任中心")。

## <a name="support"></a>支持

应用 AI 服务提供若干支持选项，可帮助你在创建智能应用程序方面取得进展。 应用 AI 服务还有一个强大的开发人员社区，可帮助解答特定问题。 若要查看可用选项的完整列表，请参阅：

- [在 UserVoice 上提交反馈](https://aka.ms/AppliedAIUserVoice)
- [在 Microsoft Q&A 上发布问题](/answers/topics/azure-applied-ai-services.html)
- [在 StackOverflow 上进行故障排除](https://aka.ms/AppliedAIStackOverflow)