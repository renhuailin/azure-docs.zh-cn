---
title: 情绪认知技能
titleSuffix: Azure Cognitive Search
description: 在 Azure 认知搜索中从 AI 扩充管道中的文本提取积极-消极情绪分数。
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/17/2021
ms.openlocfilehash: b750e99d30c0b540a381ce9e9b32b228b4dceaf8
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129210941"
---
# <a name="sentiment-cognitive-skill"></a>情绪认知技能

情绪技能可沿着一个积极-消极连续统评估非结构化文本，对于每个记录，会返回一个介于 0 和 1 之间的数字评分。 评分接近 1 代表积极的情绪，评分接近 0 代表消极的情绪。 此技能使用认知服务中的[文本分析](../cognitive-services/text-analytics/overview.md)提供的机器学习模型。

> [!IMPORTANT]
> 现在，情绪技能已由 [Microsoft.Skills.Text.V3.SentimentSkill](cognitive-search-skill-sentiment-v3.md) 取代。 按照[已弃用的认知搜索技能](cognitive-search-skill-deprecated.md)中的建议，迁移到支持的技能。

> [!NOTE]
> 通过增大处理频率、添加更多文档或添加更多 AI 算法来扩大范围时，需要[附加可计费的认知服务资源](cognitive-search-attach-cognitive-services.md)。 调用认知服务中的 API 以及在 Azure 认知搜索中的文档破解阶段提取图像时，会产生费用。 提取文档中的文本不会产生费用。
>
> 内置技能执行按现有[认知服务即用即付价格](https://azure.microsoft.com/pricing/details/cognitive-services/)计费。 图像提取定价如 [Azure 认知搜索定价页](https://azure.microsoft.com/pricing/details/search/)所述。


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SentimentSkill

## <a name="data-limits"></a>数据限制
记录的最大大小应为 5000 个字符，通过 [`String.Length`](/dotnet/api/system.string.length) 进行测量。 如果需要在将数据发送到情绪分析器之前拆分数据，请使用[文本拆分技能](cognitive-search-skill-textsplit.md)。


## <a name="skill-parameters"></a>技能参数

参数区分大小写。

| 参数名称 | 说明 |
|----------------|----------------------|
| `defaultLanguageCode` | （可选）要应用到未显式指定语言的文档的语言代码。 <br/> 请参阅[支持的语言的完整列表](../cognitive-services/text-analytics/language-support.md) |

## <a name="skill-inputs"></a>技能输入 

| 输入名称 | 说明 |
|--------------------|-------------|
| `text` | 要分析的文本。|
| `languageCode`    |  （可选）表示记录的语言的字符串。 如果未指定此属性，则默认值为“en”。 <br/>请参阅[支持的语言的完整列表](../cognitive-services/text-analytics/language-support.md)。|

## <a name="skill-outputs"></a>技能输出

| 输出名称 | 说明 |
|--------------------|-------------|
| `score` | 介于 0 和 1 之间的值，表示所分析的文本的情绪。 值接近 0 代表消极情绪，接近 0.5 代表中性情绪，接近 1 代表积极的情绪。|


##  <a name="sample-definition"></a>示例定义

```json
{
    "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
    "inputs": [
        {
            "name": "text",
            "source": "/document/content"
        },
        {
            "name": "languageCode",
            "source": "/document/languagecode"
        }
    ],
    "outputs": [
        {
            "name": "score",
            "targetName": "mySentiment"
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
                "score": 0.01
            }
        }
    ]
}
```

## <a name="warning-cases"></a>警告情况
如果文本为空，则会生成警告，且不返回情绪分数。
如果某语言不受支持，则会生成警告，且不返回情绪分数。

## <a name="see-also"></a>另请参阅

+ [内置技能](cognitive-search-predefined-skills.md)
+ [如何定义技能集](cognitive-search-defining-skillset.md)
+ [情绪技能 (V3)](cognitive-search-skill-sentiment-v3.md)