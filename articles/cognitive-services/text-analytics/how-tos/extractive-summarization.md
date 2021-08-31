---
title: 用文本分析提取式摘要 API 汇总文本
titleSuffix: Azure Cognitive Services
description: 本文介绍如何通过 Azure 认知服务文本分析提取式摘要 API 汇总文本。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 08/05/2021
ms.author: aahi
ms.openlocfilehash: f8db386da9890be2a07f201243dbbb4beaf5c499
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122444074"
---
# <a name="how-to-summarize-text-with-text-analytics-preview"></a>操作说明：使用文本分析（预览版）汇总文本

> [!IMPORTANT] 
> 文本分析提取式摘要是一项预览功能，其“按原样”提供并在“不保证商品没有缺点”情况下提供。 因此，不应在任何生产用途中实施或部署文本分析提取式摘要（预览版）。 客户独自负责对文本分析提取式摘要的任何使用。 

通常，自动文本摘要有两种方法：提取式和抽象型。 文本分析 API 从版本 `3.2-preview.1` 开始提供提取式摘要

提取式摘要是 Azure 文本分析中的一项功能，它通过提取共同表示原始内容中最重要或相关信息的句子，生成一个摘要。

此功能旨在缩短用户认为太长而无法阅读的内容。 提取式摘要将文章、论文或文档压缩为关键句子。

API 使用的 AI 模型由该服务提供，只需发送内容即可进行分析。

## <a name="extractive-summarization-and-features"></a>提取式摘要和功能

文本分析中的提取式摘要功能使用自然语言处理技术在非结构化文本文档中查找关键句子。 这些句子共同传达文档的主要理念。

提取式摘要返回排名分数作为系统响应的一部分，并返回提取的句子及其在原始文档中的位置。 排名分数是指确定句子相对于文档主要理念的相关程度的指标。 该模型为每个句子提供 0 到 1（含 0 和 1）之间的分数，并按请求返回分数最高的句子。 例如，如果请求一个包含三个句子的摘要，则该服务将返回分数最高的三个句子。

文本分析中还有另一项功能[关键短语提取](./text-analytics-how-to-keyword-extraction.md)，其可提取关键信息。 当在关键短语提取和提取式摘要之间做出决策时，请考虑以下事项：
* 关键短语提取返回短语，而提取式摘要返回句子
* 提取式摘要返回句子以及排名分数。 将按请求返回排名最高的句子

## <a name="sending-a-rest-api-request"></a>发送 REST API 请求 

> [!TIP]
> 你还可以使用最新的客户端库预览版来使用提取式摘要。 在 GitHub 上参阅以下示例。 
> * [.NET](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/textanalytics/Azure.AI.TextAnalytics/samples/Sample8_ExtractSummary.md)
> * [Java](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics/lro/AnalyzeExtractiveSummarization.java)
> * [Python](https://github.com/Azure/azure-sdk-for-python/blob/main/sdk/textanalytics/azure-ai-textanalytics/samples/sample_extract_summary.py)

### <a name="preparation"></a>准备工作

与其他文本分析功能不同，提取式摘要功能是仅异步操作，可通过 /analyze 终结点进行访问。 JSON 请求数据应遵循[对 /analyze 终结点的异步请求](./text-analytics-how-to-call-api.md?tabs=asynchronous#api-request-formats)中所述的格式。

提取式摘要支持使用多种语言进行文档输入。 有关详细信息，请参阅[支持的语言](../language-support.md)。

每个文档的大小必须少于 125,000 个字符。 对于集合中允许的最大文档数，请参阅[数据限制](../concepts/data-limits.md?tabs=version-3)一文。 集合在请求正文中提交。

### <a name="structure-the-request"></a>构造请求

创建 POST 请求。 可[使用 Postman](text-analytics-how-to-call-api.md) 或以下参考链接中的“API 测试控制台”来快速构建并发送请求。 

[文本摘要引用](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-2-preview-1/operations/Analyze)

### <a name="request-endpoints"></a>请求终结点

在 Azure 上使用文本分析资源为提取式摘要设置 HTTPS 终结点。 例如：

> [!NOTE]
> 可以在 Azure 门户上找到文本分析资源的密钥和终结点。 它们将位于资源的“密钥和终结点”页。 

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.2-preview.1/analyze`

发送请求标头以包括文本分析 API 密钥。 在请求正文中，提供为此分析准备的 JSON 文档集合。

### <a name="example-request"></a>示例请求 

下面是可以提交汇总的内容的示例，该示例是使用[集成式 AI 的整体表示形式](https://www.microsoft.com/research/blog/a-holistic-representation-toward-integrative-ai/)提取的，用于演示目的。 提取式摘要 API 可以接受更长的输入文本。 有关文本分析 API 的数据限制的详细信息，请参阅[数据限制](../Concepts/data-limits.md)。

一个请求可包含多个文档。 

每个文档都有以下参数
* `id`，用于标识文档。 
* `language`，用于指示文档的源语言，默认值为 `en`
* `text`，用于附加文档文本。

一个请求中的所有文档共享以下参数。 可以在请求的 `tasks` 定义中指定这些参数。
* `model-version`，用于指定要使用的模型版本，默认值为 `latest`。 有关详细信息，请参阅[模型版本](../concepts/model-versioning.md) 
* `sentenceCount`，用于指定将返回的句子数，默认值为 `3`。 范围为 1 到 20。
* `sortBy`，用于指定将返回的提取句子的顺序。 `sortBy` 的接受值为 `Offset` 和 `Rank`，默认值为 `Offset`。 值 `Offset` 是句子在原始文档中的开始位置。    

```json
{
  "analysisInput": {
    "documents": [
      {
        "language": "en",
        "id": "1",
        "text": "At Microsoft, we have been on a quest to advance AI beyond existing techniques, by taking a more holistic, human-centric approach to learning and understanding. As Chief Technology Officer of Azure AI Cognitive Services, I have been working with a team of amazing scientists and engineers to turn this quest into a reality. In my role, I enjoy a unique perspective in viewing the relationship among three attributes of human cognition: monolingual text (X), audio or visual sensory signals, (Y) and multilingual (Z). At the intersection of all three, there’s magic—what we call XYZ-code as illustrated in Figure 1—a joint representation to create more powerful AI that can speak, hear, see, and understand humans better. We believe XYZ-code will enable us to fulfill our long-term vision: cross-domain transfer learning, spanning modalities and languages. The goal is to have pretrained models that can jointly learn representations to support a broad range of downstream AI tasks, much in the way humans do today. Over the past five years, we have achieved human performance on benchmarks in conversational speech recognition, machine translation, conversational question answering, machine reading comprehension, and image captioning. These five breakthroughs provided us with strong signals toward our more ambitious aspiration to produce a leap in AI capabilities, achieving multisensory and multilingual learning that is closer in line with how humans learn and understand. I believe the joint XYZ-code is a foundational component of this aspiration, if grounded with external knowledge sources in the downstream AI tasks."
      }
    ]
  },
  "tasks": {
    "extractiveSummarizationTasks": [
      {
        "parameters": {
          "model-version": "latest",
          "sentenceCount": 3,
          "sortBy": "Offset"
        }
      }
    ]
  }
}
```

### <a name="post-the-request"></a>发布请求

提取式摘要 API 在收到请求时执行。 有关每分钟和每秒可以发送的请求的大小和数量的信息，请参阅概述中的[数据限制](../overview.md#data-limits)部分。

文本分析提取式摘要 API 是一个异步 API，因此响应对象中没有任何文本。 但是，需要响应标头中 `operation-location` 键的值，才能发出 GET 请求来检查作业的状态和输出。  下面是 POST 请求的响应标头中操作位置键的值的示例：

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.2-preview.1/analyze/jobs/<jobID>`

若要检查作业状态，请在 POST 响应的操作位置键标头的值中向 URL 发出 GET 请求。  以下状态用于反映作业的状态：`NotStarted`、`running`、`succeeded`、`failed` 或 `rejected`。  

如果作业成功，将在 GET 请求的正文中返回 API 的输出。 


### <a name="view-the-results"></a>查看结果

以下是 GET 请求的响应示例。  在 `expirationDateTime`（创建作业 24 小时后）已过之前，输出可供检索；在此时间之后，输出将被清除。 由于多语言和表情符号支持，响应可能包含文本偏移。 有关详细信息，请参阅[如何处理偏移](../concepts/text-offsets.md)。

### <a name="example-response"></a>示例响应
  
提取式摘要功能返回 

```json
{
    "jobId": "be437134-a76b-4e45-829e-9b37dcd209bf",
    "lastUpdateDateTime": "2021-06-11T05:43:37Z",
    "createdDateTime": "2021-06-11T05:42:32Z",
    "expirationDateTime": "2021-06-12T05:42:32Z",
    "status": "succeeded",
    "errors": [],
    "results": {
        "documents": [
            {
                "id": "1",
                "sentences": [
                    {
                        "text": "At Microsoft, we have been on a quest to advance AI beyond existing techniques, by taking a more holistic, human-centric approach to learning and understanding.",
                        "rankScore": 0.7673416137695312,
                        "Offset": 0,
                        "length": 160
                    },
                    {
                        "text": "In my role, I enjoy a unique perspective in viewing the relationship among three attributes of human cognition: monolingual text (X), audio or visual sensory signals, (Y) and multilingual (Z).",
                        "rankScore": 0.7644073963165283,
                        "Offset": 324,
                        "length": 192
                    },
                    {
                        "text": "At the intersection of all three, there’s magic—what we call XYZ-code as illustrated in Figure 1—a joint representation to create more powerful AI that can speak, hear, see, and understand humans better.",
                        "rankScore": 0.7623870968818665,
                        "Offset": 517,
                        "length": 203
                    }    
                ],
                "warnings": []
            }
        ],
        "errors": [],
        "modelVersion": "2021-08-01"
    }
}
```

## <a name="summary"></a>“摘要”

本文介绍了使用文本分析提取式摘要 API 进行提取式摘要的概念和工作流。 你可能想要使用提取式摘要来执行以下操作：

* 帮助处理文档以提高效率。
* 从较长的文档、报表和其他文本表单中提取关键信息。
* 突出显示文档中的关键语句。
* 快速浏览库中的文档。
* 生成新闻源内容。

## <a name="see-also"></a>另请参阅

* [文本分析概述](../overview.md)
* [新增功能](../whats-new.md)
* [模型版本](../concepts/model-versioning.md)
