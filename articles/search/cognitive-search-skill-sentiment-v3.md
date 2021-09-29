---
title: 情绪认知技能 (V3)
titleSuffix: Azure Cognitive Search
description: 在 Azure 认知搜索中的 AI 扩充管道中，为文本提供情绪标签。
manager: jennmar
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: d42eb099091840ed5dbab61abbad47e087504ab3
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129214515"
---
# <a name="sentiment-cognitive-skill-v3"></a>情绪认知技能 (V3)

V3 **情绪** 技能评估非结构化文本，对于每条记录，将根据服务在句子和文档级别找到的最高置信度分数来提供情绪标签（例如“消极”、“中立”和“积极”）。 此技能使用认知服务中的[文本分析](../cognitive-services/text-analytics/overview.md)版本 3 提供的机器学习模型。 它还公开[文本分析 API 的观点挖掘功能](../cognitive-services/text-analytics/how-tos/text-analytics-how-to-sentiment-analysis.md#opinion-mining)，提供有关文本中产品或服务的属性的观点的更精细的信息。

> [!NOTE]
> 此技能与 Azure 认知服务绑定，对于超过每个索引器每天 20 个文档的事务，需要[一个计费资源](cognitive-search-attach-cognitive-services.md)。 内置技能执行按现有[认知服务即用即付价格](https://azure.microsoft.com/pricing/details/cognitive-services/)计费。
>

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.V3.SentimentSkill

## <a name="data-limits"></a>数据限制
记录的最大大小应为 5000 个字符，通过 [`String.Length`](/dotnet/api/system.string.length) 进行测量。 如果需要在将数据发送到情绪技能之前拆分数据，请使用[文本拆分技能](cognitive-search-skill-textsplit.md)。

## <a name="skill-parameters"></a>技能参数

参数区分大小写。

| 参数名称 | 说明 |
|----------------|----------------------|
| `defaultLanguageCode` | （可选）要应用到未显式指定语言的文档的语言代码。 <br/> 请参阅[支持的语言的完整列表](../cognitive-services/text-analytics/language-support.md) |
| `modelVersion`   | （可选）调用文本分析服务时要使用的模型版本。 如果未指定，将默认为最新版本。 建议不要指定此值，除非绝对必要。 有关详细信息，请参阅[文本分析 API 中的模型版本控制](../cognitive-services/text-analytics/concepts/model-versioning.md)。 |
| `includeOpinionMining` | 如果设置为 `true`，则启用 [TextAnalytics 的意见挖掘功能](../cognitive-services/text-analytics/how-tos/text-analytics-how-to-sentiment-analysis.md#opinion-mining)，允许在输出结果中包含基于方面的情绪分析。 默认为 `false`。 |

## <a name="skill-inputs"></a>技能输入 

| 输入名称 | 说明 |
|--------------------|-------------|
| `text` | 要分析的文本。|
| `languageCode`    |  （可选）表示记录的语言的字符串。 如果未指定此属性，则默认值为“en”。 <br/>请参阅[支持的语言的完整列表](../cognitive-services/text-analytics/language-support.md)。|

## <a name="skill-outputs"></a>技能输出

| 输出名称 | 说明 |
|--------------------|-------------|
| `sentiment` | 一个字符串值，表示完整分析文本的情绪标签（积极、中立、消极）。 |
| `confidenceScores` | 一种复杂类型，有三个双精度值，一个值用于积极评级，一个值用于中立评级，另一个值用于消极评级。 值介于 0 到 1.00 之间，1.00 表示给定标签分配中最高可能的置信度。 |
| `sentences` | 一组复杂类型，可以逐句细分文本的情绪。 如果 `includeOpinionMining` 设置为 `true`，也会以目标和评估的形式返回观点挖掘结果。|


##  <a name="sample-definition"></a>示例定义

```json
{
    "@odata.type": "#Microsoft.Skills.Text.V3.SentimentSkill",
    "context": "/document",
    "includeOpinionMining": true,
    "inputs": [
        {
            "name": "text",
            "source": "/document/content"
        },
        {
            "name": "languageCode",
            "source": "/document/languageCode"
        }
    ],
    "outputs": [
        {
            "name": "sentiment",
            "targetName": "sentiment"
        },
        {
            "name": "confidenceScores",
            "targetName": "confidenceScores"
        },
        {
            "name": "sentences",
            "targetName": "sentences"
        }
    ]
}
```

##  <a name="sample-input"></a>示例输入

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "text": "I had a terrible time at the hotel. The staff was rude and the food was awful.",
                "languageCode": "en"
            }
        }
    ]
}
```

##  <a name="sample-output"></a>示例输出

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "sentiment": "negative",
                "confidenceScores": {
                    "positive": 0.0,
                    "neutral": 0.0,
                    "negative": 1.0
                },
                "sentences": [
                    {
                        "text": "I had a terrible time at the hotel.",
                        "sentiment": "negative",
                        "confidenceScores": {
                            "positive": 0.0,
                            "neutral": 0.0,
                            "negative": 1.0
                        },
                        "offset": 0,
                        "length": 35,
                        "targets": [],
                        "assessments": [],
                    },
                    {
                        "text": "The staff was rude and the food was awful.",
                        "sentiment": "negative",
                        "confidenceScores": {
                            "positive": 0.0,
                            "neutral": 0.0,
                            "negative": 1.0
                        },
                        "offset":36,
                        "length": 42,
                        "targets": [
                            {
                                "text": "staff",
                                "sentiment": "negative",
                                "confidenceScores": {
                                    "positive": 0.0,
                                    "neutral": 0.0,
                                    "negative": 1.0
                                },
                                "offset": 40,
                                "length": 5,
                                "relations": [
                                    {
                                        "relationType": "assessment",
                                        "ref": "#/documents/0/sentences/1/assessments/0",
                                    }
                                ]
                            },
                            {
                                "text": "food",
                                "sentiment": "negative",
                                "confidenceScores": {
                                    "positive": 0.0,
                                    "neutral": 0.0,
                                    "negative": 1.0
                                },
                                "offset": 63,
                                "length": 4,
                                "relations": [
                                    {
                                        "relationType": "assessment",
                                        "ref": "#/documents/0/sentences/1/assessments/1",
                                    }
                                ]
                            }
                        ],
                        "assessments": [
                            {
                                "text": "rude",
                                "sentiment": "negative",
                                "confidenceScores": {
                                    "positive": 0.0,
                                    "neutral": 0.0,
                                    "negative": 1.0
                                },
                                "offset": 50,
                                "length": 4,
                                "isNegated": false
                            },
                            {
                                "text": "awful",
                                "sentiment": "negative",
                                "confidenceScores": {
                                    "positive": 0.0,
                                    "neutral": 0.0,
                                    "negative": 1.0
                                },
                                "offset": 72,
                                "length": 5,
                                "isNegated": false
                            }
                        ],
                    }
                ]
            }
        }
    ]
}
```

## <a name="warning-cases"></a>警告情况
如果文本为空，则会生成警告，且不返回情绪结果。
如果某语言不受支持，则会生成警告，且不返回情绪结果。

## <a name="see-also"></a>另请参阅

+ [内置技能](cognitive-search-predefined-skills.md)
+ [如何定义技能集](cognitive-search-defining-skillset.md)
