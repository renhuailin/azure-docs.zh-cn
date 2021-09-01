---
title: 语言检测认知技能
titleSuffix: Azure Cognitive Search
description: 在 Azure 认知搜索中的 AI 扩充管道中，计算非结构化的文本，并针对每个文本，返回语言标识符和表示分析强度的得分。
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: 01b6449baa7c5d13b041f886074425d78e037579
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121862104"
---
#   <a name="language-detection-cognitive-skill"></a>语言检测认知技能

语言检测  技能检测输入文本的语言，并报告在请求中提交的每个文档的单一语言代码。 语言代码配有表示分析长度的得分。 此技能使用认知服务中的[文本分析](../cognitive-services/text-analytics/overview.md)提供的机器学习模型。

当需要提供文本的语言作为其他技能（例如，[情绪分析技能](cognitive-search-skill-sentiment-v3.md)或[文本拆分技能](cognitive-search-skill-textsplit.md)）的输入时，此功能尤其有用。

语言检测利用必应的自然语言处理库，此类库超出为文本分析列出的[受支持的语言和区域](../cognitive-services/text-analytics/language-support.md)的数目。 语言的具体列表未发布，但包含所有广泛传播的语言，以及变体、方言和某些区域性的和文化性的语言。 如果你的内容是采用不常用的语言表达的，可以[尝试语言检测 API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages)，看是否会返回一个代码。 无法检测到的语言的响应为 `(Unknown)`。

> [!NOTE]
> 此技能与 Azure 认知服务绑定，对于超过每个索引器每天 20 个文档的事务，需要[一个计费资源](cognitive-search-attach-cognitive-services.md)。 内置技能执行按现有[认知服务即用即付价格](https://azure.microsoft.com/pricing/details/cognitive-services/)计费。
>

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.LanguageDetectionSkill

## <a name="data-limits"></a>数据限制
记录的最大大小应为 50,000 个字符，通过 [`String.Length`](/dotnet/api/system.string.length) 进行测量。 如果在将数据发送到语言检测技能之前需要拆分数据，可以使用[文本拆分技能](cognitive-search-skill-textsplit.md)。

## <a name="skill-parameters"></a>技能参数

参数区分大小写。

| 输入 | 说明 |
|---------------------|-------------|
| `defaultCountryHint` | （可选）ISO 3166-1 alpha-2 双字母国家/地区代码如果不能区分语言，则可以作为语言检测模型的提示使用。 有关详细信息，请参阅本主题的[文本分析文档](../cognitive-services/text-analytics/how-tos/text-analytics-how-to-language-detection.md#ambiguous-content)。 具体而言，`defaultCountryHint` 参数与未明确指定 `countryHint` 输入的文档一起使用。  |
| `modelVersion`   | （可选）调用文本分析服务时要使用的模型版本。 如果未指定，将默认为最新可用版本。 建议不要指定此值，除非绝对必要。 有关详细信息，请参阅[文本分析 API 中的模型版本控制](../cognitive-services/text-analytics/concepts/model-versioning.md)。 |

## <a name="skill-inputs"></a>技能输入

参数区分大小写。

| 输入     | 说明 |
|--------------------|-------------|
| `text` | 要分析的文本。|
| `countryHint` | ISO 3166-1 alpha-2 双字母国家/地区代码如果不能区分语言，则可以作为语言检测模型的提示使用。 有关详细信息，请参阅本主题的[文本分析文档](../cognitive-services/text-analytics/how-tos/text-analytics-how-to-language-detection.md#ambiguous-content)。 |

## <a name="skill-outputs"></a>技能输出

| 输出名称    | 说明 |
|--------------------|-------------|
| `languageCode` | 标识语言的 ISO 6391 语言代码。 例如，“en”。 |
| `languageName` | 语言的名称。 例如，“英语”。 |
| `score` | 一个介于 0 和 1 之间的值。 正确标识语言的可能性。 如果句子中有混合语言，得分可能会低于 1。  |

##  <a name="sample-definition"></a>示例定义

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      },
      {
        "name": "countryHint",
        "source": "/document/countryHint"
      }
    ],
    "outputs": [
      {
        "name": "languageCode",
        "targetName": "myLanguageCode"
      },
      {
        "name": "languageName",
        "targetName": "myLanguageName"
      },
      {
        "name": "score",
        "targetName": "myLanguageScore"
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
        "data":
           {
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. "
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Estamos muy felices de estar con ustedes."
           }
      },
      {
        "recordId": "3",
        "data":
           {
             "text": "impossible",
             "countryHint": "fr"
           }
      }
    ]
```


##  <a name="sample-output"></a>示例输出

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
            {
              "languageCode": "en",
              "languageName": "English",
              "score": 1,
            }
      },
      {
        "recordId": "2",
        "data":
            {
              "languageCode": "es",
              "languageName": "Spanish",
              "score": 1,
            }
      },
      {
        "recordId": "3",
        "data":
            {
              "languageCode": "fr",
              "languageName": "French",
              "score": 1,
            }
      }
    ]
}
```

## <a name="see-also"></a>另请参阅

+ [内置技能](cognitive-search-predefined-skills.md)
+ [如何定义技能集](cognitive-search-defining-skillset.md)