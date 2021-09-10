---
title: 实体链接认知技能
titleSuffix: Azure Cognitive Search
description: 在 Azure 认知搜索中从扩充管道中的文本提取各种链接实体。
manager: jennmar
author: ayokande
ms.author: aakande
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: 4b052e6ac5746d771e7725b39c13fd57b20facb8
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123038458"
---
# <a name="entity-linking-cognitive-skill"></a>实体链接认知技能

实体链接技能返回已识别实体的列表，其中包含指向知名知识库 (Wikipedia) 中文章的链接。

> [!NOTE]
> 此技能与 Azure 认知服务[文本分析](../cognitive-services/text-analytics/overview.md)绑定，对于超过每个索引器每天 20 个文档的事务，需要[一个计费资源](cognitive-search-attach-cognitive-services.md)。 内置技能执行按现有[认知服务即用即付价格](https://azure.microsoft.com/pricing/details/cognitive-services/)计费。
>

## <a name="odatatype"></a>@odata.type

Microsoft.Skills.Text.V3.EntityLinkingSkill

## <a name="data-limits"></a>数据限制

记录的最大大小应为 50,000 个字符，通过 [`String.Length`](/dotnet/api/system.string.length) 进行测量。 如果在将数据发送到实体链接技能之前需要对其进行拆分，请考虑使用[文本拆分技能](cognitive-search-skill-textsplit.md)。

## <a name="skill-parameters"></a>技能参数

参数名称区分大小写并且都是可选的。

| 参数名称     | 说明 |
|--------------------|-------------|
| `defaultLanguageCode` |    输入文本的语言代码。 如果未指定默认语言代码，会将英语 (en) 用作默认语言代码。 <br/> 请参阅[支持的语言的完整列表](../cognitive-services/text-analytics/language-support.md)。 |
| `minimumPrecision` | 一个介于 0 和 1 之间的值。 如果置信度分数（在 `entities` 输出中）低于此值，则不会返回该实体。 默认值为 0。 |
| `modelVersion` | （可选）调用文本分析服务时要使用的模型版本。 如果未指定，将默认为最新可用版本。 建议不要指定此值，除非绝对必要。 有关详细信息，请参阅[文本分析 API 中的模型版本控制](../cognitive-services/text-analytics/concepts/model-versioning.md)。|

## <a name="skill-inputs"></a>技能输入

| 输入名称      | 说明                   |
|---------------|-------------------------------|
| `languageCode`    | 表示记录的语言的字符串。 如果未指定此参数，将使用默认语言代码分析记录。 <br/>请参阅[支持的语言的完整列表](../cognitive-services/text-analytics/language-support.md)。 |
| `text`          | 要分析的文本。          |

## <a name="skill-outputs"></a>技能输出

| 输出名称      | 说明                   |
|---------------|-------------------------------|
| `entities` | 复杂类型的数组，包含以下字段： <ul><li>name（文本中显示的实际实体名称）</li> <li>id </li> <li>language（由技能决定的文本语言）</li> <li>url（此实体的链接 URL）</li> <li>bingId（此链接实体的 bingId）</li> <li>dataSource（与 URL 关联的数据源） </li> <li>matches（复杂类型的数组，包含 `text`、`offset`、`length` 和 `confidenceScore`）</li></ul>|

## <a name="sample-definition"></a>示例定义

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.V3.EntityLinkingSkill",
    "context": "/document",
    "defaultLanguageCode": "en", 
    "minimumPrecision": 0.5, 
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
            "name": "entities", 
            "targetName": "entities" 
        }
    ]
}
```

## <a name="sample-input"></a>示例输入

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Microsoft is liked by many.",
             "languageCode": "en"
           }
      }
    ]
}
```

## <a name="sample-output"></a>示例输出

```json
{
  "values": [
    {
      "recordId": "1",
      "data" : 
      {
        "entities": [
          {
            "name": "Microsoft", 
            "id": "Microsoft",
            "language": "en", 
            "url": "https://en.wikipedia.org/wiki/Microsoft", 
            "bingId": "a093e9b9-90f5-a3d5-c4b8-5855e1b01f85", 
            "dataSource": "Wikipedia", 
            "matches": [
                {
                    "text": "Microsoft", 
                    "offset": 0, 
                    "length": 9, 
                    "confidenceScore": 0.13 
                }
            ]
          }
        ],
      }
    }
  ]
}
```

请注意，在此技能的输出中，针对实体返回的偏移量是直接从[文本分析 API](../cognitive-services/text-analytics/overview.md) 返回的，这意味着如果使用这些偏移量为原始字符串编制索引，则应使用 .NET 中的 [StringInfo](/dotnet/api/system.globalization.stringinfo) 类来提取正确的内容。  [此处提供了更多详细信息。](../cognitive-services/text-analytics/concepts/text-offsets.md)

## <a name="warning-cases"></a>警告情况

如果文档的语言代码不受支持，则会返回警告，并且不提取任何实体。

## <a name="see-also"></a>另请参阅

+ [内置技能](cognitive-search-predefined-skills.md)
+ [如何定义技能集](cognitive-search-defining-skillset.md)