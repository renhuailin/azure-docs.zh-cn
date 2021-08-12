---
title: 通过文本分析 REST API 执行情绪分析和观点挖掘
titleSuffix: Azure Cognitive Services
description: 本文介绍如何通过 Azure 认知服务文本分析 API 检测文本中的情绪并挖掘观点。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 07/07/2021
ms.author: aahi
ms.openlocfilehash: 00ffe7d9911e10dad26976b36954be5fe61c3474
ms.sourcegitcommit: cc099517b76bf4b5421944bd1bfdaa54153458a0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2021
ms.locfileid: "113549681"
---
# <a name="how-to-sentiment-analysis-and-opinion-mining"></a>如何：情绪分析和观点挖掘

文本分析 API 的情绪分析功能提供了两种方法来检测积极和消极情绪。 如果发送情绪分析请求，API 会在句子和文档级别返回情绪标签（如“消极”、“中性”和“积极”）和置信度分数。 还可使用情绪分析终结点发送观点挖掘请求，它精细地描述了对文本中某些字（例如产品或服务的属性）的观点。

API 使用的 AI 模型由该服务提供，只需发送内容即可进行分析。

## <a name="sentiment-analysis-versions-and-features"></a>情绪分析版本和功能

| Feature                                   | 情绪分析 v3.0 | 情绪分析 v3.1 |
|-------------------------------------------|-----------------------|-----------------------------------|
| 用于单个请求和批量请求的方法    | X                     | X                                 |
| 情绪分析分数和标记             | X                     | X                                 |
| 基于 Linux 的 [Docker 容器](text-analytics-how-to-install-containers.md) | X  |  |
| 观点挖掘                            |                       | X                                 |

## <a name="sentiment-analysis"></a>情绪分析

情绪分析 3.x 版本将情绪标签应用于文本，然后在句子和文档级别返回标签，每个标签都有一个置信度分数。 

标签为积极、消极和中性。 在文档级别，还可能返回混合情绪标签。 文档的情绪由以下内容确定：

| 句子情绪                                                                            | 返回的文档标签 |
|-----------------------------------------------------------------------------------------------|-------------------------|
| 文档中至少有一个 `positive` 句子。 剩下的句子为 `neutral`。 | `positive`              |
| 文档中至少有一个 `negative` 句子。 剩下的句子为 `neutral`。 | `negative`              |
| 文档中至少有一个 `negative` 句子和一个 `positive` 句子。    | `mixed`                 |
| 文档中的所有句子为 `neutral`。                                                  | `neutral`               |

置信度分数范围介于 1 到 0 之间。 分数越接近于 1 表示标签分类的置信度越高，分数越低表示置信度越低。 对于每个文档或每个句子，与标签（积极、消极和中性）关联的预测分数总和为 1。 有关详细信息，请参阅[文本分析透明度备注](/legal/cognitive-services/text-analytics/transparency-note?context=/azure/cognitive-services/text-analytics/context/context)。 

## <a name="opinion-mining"></a>观点挖掘

观点挖掘是情绪分析的一项功能，从版本 3.1 开始提供。 此功能在自然语言处理 (NLP) 中也称为基于特性的情绪分析，它更加精细地描述了对文本中某些产品或服务属性的观点。 API 将观点作为目标（名词或动词）和评估（形容词）。

例如，如果客户评论某家酒店，例如“房间很好，但员工不友好”，观点挖掘将查找文本中的目标（方面）及其相关的评估（观点）和情绪。 情绪分析可能只报告消极情绪。

:::image type="content" source="../media/how-tos/opinion-mining.png" alt-text="观点挖掘示例图" lightbox="../media/how-tos/opinion-mining.png":::

若要在结果中获取观点挖掘，必须在情绪分析请求中包含 `opinionMining=true` 标志。 观点挖掘结果将包含在情绪分析响应中。 观点挖掘是情绪分析的扩展，包含在你当前的[定价层](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)中。


## <a name="sending-a-rest-api-request"></a>发送 REST API 请求 

### <a name="preparation"></a>准备工作

当为情绪分析提供较少的文本时，会得到更高质量的结果。 这与关键短语提取相反，关键短语提取在处理较大的文本块时表现更好。 要从两个操作获取最佳结果，请考虑相应地重建输入。

必须拥有以下格式的 JSON 文档：ID、文本和语言 情绪分析支持多种语言。 有关详细信息，请参阅[支持的语言](../language-support.md)。

每个文档的大小必须少于 5,120 个字符， 对于集合中允许的最大文档数，请参阅“概念”下的[数据限制](../concepts/data-limits.md?tabs=version-3)一文。 集合在请求正文中提交。

## <a name="structure-the-request"></a>构造请求

创建 POST 请求。 可[使用 Postman](text-analytics-how-to-call-api.md) 或以下参考链接中的“API 测试控制台”来快速构建并发送请求。 

#### <a name="version-31"></a>[版本 3.1](#tab/version-3-1)

[情绪分析 v3.1 参考](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1/operations/Sentiment)

#### <a name="version-30"></a>[版本 3.0](#tab/version-3)

[情绪分析 v3 参考](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Sentiment)

---

### <a name="request-endpoints"></a>请求终结点

使用 Azure 上的文本分析资源或实例化的[文本分析容器](text-analytics-how-to-install-containers.md)设置 HTTPS 终结点，以便进行情绪分析。 必须包括要使用的版本的正确 URL。 例如：

> [!NOTE]
> 可以在 Azure 门户上找到文本分析资源的密钥和终结点。 它们将位于资源的“快速启动”页上的“资源管理”下。 

#### <a name="version-31"></a>[版本 3.1](#tab/version-3-1)

**情绪分析**

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1/sentiment`

**观点挖掘**

若要获取观点挖掘结果，必须包含 `opinionMining=true` 参数。 例如：

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1/sentiment?opinionMining=true`

默认情况下，此参数设置为 `false`。 

#### <a name="version-30"></a>[版本 3.0](#tab/version-3)

**情绪分析**

在 v3.0，唯一可用的终结点适用于情绪分析。
 
`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/sentiment`

---

发送请求标头以包括文本分析 API 密钥。 在请求正文中，提供为此分析准备的 JSON 文档集合。

### <a name="example-request-for-sentiment-analysis-and-opinion-mining"></a>情绪分析和观点挖掘的请求示例  

下面是可能提交用于情绪分析的内容示例。 `v3.0` 和 `v3.1` 的请求格式相同。
    
```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "The restaurant had great food and our waiter was friendly."
    }
  ]
}
```

### <a name="post-the-request"></a>发布请求

在收到请求时执行分析。 有关每分钟和每秒可以发送的请求的大小和数量的信息，请参阅概述中的[数据限制](../overview.md#data-limits)部分。

文本分析 API 是无状态的。 不会在帐户中存储数据，结果会立即在响应中返回。


### <a name="view-the-results"></a>查看结果

系统会立即返回输出。 可将结果流式传输到接受 JSON 的应用程序，或者将输出保存到本地系统上的文件中。 然后，将输出导入到可以用来对数据进行排序、搜索和操作的应用程序。 由于多语言和表情符号支持，响应可能包含文本偏移。 有关详细信息，请参阅[如何处理偏移](../concepts/text-offsets.md)。

#### <a name="version-31"></a>[版本 3.1](#tab/version-3-1)

### <a name="sentiment-analysis-and-opinion-mining-example-response"></a>情绪分析和观点挖掘示例响应

> [!IMPORTANT]
> 下面是 API v3.1 中提供的有关将观点挖掘和情绪分析结合使用的 JSON 示例。 如果不请求观点挖掘，则 API 响应将与“版本 3.0”选项卡相同。  

情绪分析 v3.1 可以返回情绪分析和观点挖掘的响应对象。
  
情绪分析为整个文档以及其中的每个句子返回情绪标签和置信度分数。 分数越接近于 1 表示标签分类的置信度越高，分数越低表示置信度越低。 一个文档可以有多个句子，每个文档或句子的置信度分数合计为 1。

观点挖掘将查找文本中的目标（名词或动词）及其相关的评估（形容词）。 在下面的回复中，*餐厅的食物很好，并且服务员很友好* 这句话包括两个目标：*食物* 和 *服务员*。 每个目标的`relations`属性都包含一个`ref`值，其中包含对相关的`documents`、`sentences`和`assessments`对象的 URI 引用。

API 返回观点作为目标（名词或动词）和评估（形容词）。

```json
{
  "documents": [
    {
      "id": "1",
      "sentiment": "positive",
      "confidenceScores": {
        "positive": 1,
        "neutral": 0,
        "negative": 0
      },
      "sentences": [
        {
          "sentiment": "positive",
          "confidenceScores": {
            "positive": 1,
            "neutral": 0,
            "negative": 0
          },
          "offset": 0,
          "length": 58,
          "text": "The restaurant had great food and our waiter was friendly.",
          "targets": [
            {
              "sentiment": "positive",
              "confidenceScores": {
                "positive": 1,
                "negative": 0
              },
              "offset": 25,
              "length": 4,
              "text": "food",
              "relations": [
                {
                  "relationType": "assessment",
                  "ref": "#/documents/0/sentences/0/assessments/0"
                }
              ]
            },
            {
              "sentiment": "positive",
              "confidenceScores": {
                "positive": 1,
                "negative": 0
              },
              "offset": 38,
              "length": 6,
              "text": "waiter",
              "relations": [
                {
                  "relationType": "assessment",
                  "ref": "#/documents/0/sentences/0/assessments/1"
                }
              ]
            }
          ],
          "assessments": [
            {
              "sentiment": "positive",
              "confidenceScores": {
                "positive": 1,
                "negative": 0
              },
              "offset": 19,
              "length": 5,
              "text": "great",
              "isNegated": false
            },
            {
              "sentiment": "positive",
              "confidenceScores": {
                "positive": 1,
                "negative": 0
              },
              "offset": 49,
              "length": 8,
              "text": "friendly",
              "isNegated": false
            }
          ]
        }
      ],
      "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-04-01"
}
```

#### <a name="version-30"></a>[版本 3.0](#tab/version-3)

### <a name="sentiment-analysis-example-response"></a>情绪分析示例响应

情绪分析为整个文档以及其中的每个句子返回情绪标签和置信度分数。 分数越接近于 1 表示标签分类的置信度越高，分数越低表示置信度越低。 一个文档可以有多个句子，每个文档或句子的置信度分数合计为 1。

情绪分析 v3 的响应包含每个已分析句子和文档的情绪标签和分数。

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "confidenceScores": {
                "positive": 1.0,
                "neutral": 0.0,
                "negative": 0.0
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "confidenceScores": {
                        "positive": 1.0,
                        "neutral": 0.0,
                        "negative": 0.0
                    },
                    "offset": 0,
                    "length": 58,
                    "text": "The restaurant had great food and our waiter was friendly."
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2020-04-01"
}
```

---

## <a name="summary"></a>总结

本文介绍了使用文本分析 API 进行情绪分析的概念和工作流。 综上所述：

+ 情绪分析和观点挖掘适用于选定的语言。
+ 请求正文中的 JSON 文档包括 ID、文本和语言代码。
+ POST 请求的目标是 `/sentiment` 终结点，方法是使用对订阅有效的个性化[访问密钥和终结点](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)。
+ 在情绪分析请求中使用 `opinionMining=true` 来获取观点挖掘结果。
+ 响应输出包含每个文档 ID 的情绪得分，可流式传输到接受 JSON 的任何应用。 例如，Excel 和 Power BI。

## <a name="see-also"></a>另请参阅

* [文本分析概述](../overview.md)
* [使用文本分析客户端库](../quickstarts/client-libraries-rest-api.md)
* [新增功能](../whats-new.md)
* [模型版本](../concepts/model-versioning.md)