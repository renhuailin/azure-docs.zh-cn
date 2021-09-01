---
title: 关键短语提取认知技能
titleSuffix: Azure Cognitive Search
description: 在 Azure 认知搜索中的 AI 扩充管道中，计算非结构化的文本，并针对每个记录返回关键短语列表。
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: 7d364cd73a866bfa3e40542329ddfc5a6dffcf13
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121862000"
---
#   <a name="key-phrase-extraction-cognitive-skill"></a>关键短语提取认知技能

关键短语提取技能可以计算非结构化的文本，并针对每个记录返回关键短语列表。 此技能使用认知服务中的[文本分析](../cognitive-services/text-analytics/overview.md)提供的机器学习模型。

如果你需要快速确定记录中的谈话要点，此功能十分有用。 例如，给定输入文本“The food was delicious and there were wonderful staff”，服务会返回“food”和“wonderful staff”。

> [!NOTE]
> 此技能绑定到认知服务，对于超过每个索引器每天 20 个文档的事务，需要[一个计费资源](cognitive-search-attach-cognitive-services.md)。 内置技能执行按现有[认知服务即用即付价格](https://azure.microsoft.com/pricing/details/cognitive-services/)计费。
>

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.KeyPhraseExtractionSkill 

## <a name="data-limits"></a>数据限制
记录的最大大小应为 50,000 个字符，通过 [`String.Length`](/dotnet/api/system.string.length) 进行测量。 如果在将数据发送到关键短语提取器之前需要拆分数据，请使用[文本拆分技能](cognitive-search-skill-textsplit.md)。

## <a name="skill-parameters"></a>技能参数

参数区分大小写。

| 输入 | 说明 |
|---------------------|-------------|
| `defaultLanguageCode` | （可选）要应用到未显式指定语言的文档的语言代码。  如果未指定默认语言代码，会将英语 (en) 用作默认语言代码。 <br/> 请参阅[支持的语言的完整列表](../cognitive-services/text-analytics/language-support.md)。 |
| `maxKeyPhraseCount`   | （可选）要生成的关键短语的最大数量。 |
| `modelVersion`   | （可选）调用文本分析服务时要使用的模型版本。 如果未指定，将默认为最新可用版本。 建议不要指定此值，除非绝对必要。 有关详细信息，请参阅[文本分析 API 中的模型版本控制](../cognitive-services/text-analytics/concepts/model-versioning.md)。 |

## <a name="skill-inputs"></a>技能输入

| 输入  | 说明 |
|--------------------|-------------|
| `text` | 要分析的文本。|
| `languageCode`    |  表示记录的语言的字符串。 如果未指定此参数，将使用默认语言代码分析记录。 <br/>请参阅[支持的语言的完整列表](../cognitive-services/text-analytics/language-support.md)|

## <a name="skill-outputs"></a>技能输出

| 输出     | 说明 |
|--------------------|-------------|
| `keyPhrases` | 从输入文本中提取的关键短语的列表。 关键短语按重要性顺序返回。 |


##  <a name="sample-definition"></a>示例定义

考虑具有以下字段的 SQL 记录：

```json
{
    "content": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. They accumulate ice from snowfall and lose it through melting. As global temperatures have risen, many of the world’s glaciers have already started to shrink and retreat. Continued warming could see many iconic landscapes – from the Canadian Rockies to the Mount Everest region of the Himalayas – lose almost all their glaciers by the end of the century.",
    "language": "en"
}
```

然后，技能定义可能会如下所示：

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      },
      {
        "name": "languageCode",
        "source": "/document/language" 
      }
    ],
    "outputs": [
      {
        "name": "keyPhrases",
        "targetName": "myKeyPhrases"
      }
    ]
  }
```

##  <a name="sample-output"></a>示例输出

对于上面的示例，技能输出将写入到扩充树中名为“document/myKeyPhrases”的新节点，因为这是我们指定的 `targetName`。 如果未指定 `targetName`，则为“document/keyPhrases”。

#### <a name="documentmykeyphrases"></a>document/myKeyPhrases 
```json
            [
              "world’s glaciers", 
              "huge rivers of ice", 
              "Canadian Rockies", 
              "iconic landscapes",
              "Mount Everest region",
              "Continued warming"
            ]
```

可以使用“document/myKeyPhrases”作为其他技能的输入，或者将其作为[输出字段映射](cognitive-search-output-field-mapping.md)的源。

## <a name="warnings"></a>警告
如果提供了不支持的语言代码，会生成警告且不提取关键短语。
如果你的文本为空，则不会生成警告。
如果文本大于 50,000 个字符，只会分析前 50,000 个字符，并会发出警告。

## <a name="see-also"></a>另请参阅

+ [内置技能](cognitive-search-predefined-skills.md)
+ [如何定义技能集](cognitive-search-defining-skillset.md)
+ [如何定义输出字段映射](cognitive-search-output-field-mapping.md)