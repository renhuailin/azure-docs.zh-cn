---
title: PII 检测认知技能
titleSuffix: Azure Cognitive Search
description: 在 Azure 认知搜索中从扩充管道中的文本提取和屏蔽个人信息。
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: 71bd45fae729efed6d76ab65fc4ea45944998f45
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121862694"
---
# <a name="pii-detection-cognitive-skill"></a>PII 检测认知技能

“PII 检测”技能从输入文本中提取个人信息，并提供屏蔽该信息的选项。 此技能使用认知服务中的[文本分析](../cognitive-services/text-analytics/overview.md)提供的机器学习模型。

> [!NOTE]
> 此技能与 Azure 认知服务绑定，对于超过每个索引器每天 20 个文档的事务，需要[一个计费资源](cognitive-search-attach-cognitive-services.md)。 内置技能执行按现有[认知服务即用即付价格](https://azure.microsoft.com/pricing/details/cognitive-services/)计费。
>

## <a name="odatatype"></a>@odata.type

Microsoft.Skills.Text.PIIDetectionSkill

## <a name="data-limits"></a>数据限制

记录的最大大小应为 50,000 个字符，通过 [`String.Length`](/dotnet/api/system.string.length) 进行测量。 如果在将数据发送到技能之前需要将其分块，请考虑使用[文本拆分技能](cognitive-search-skill-textsplit.md)。

## <a name="skill-parameters"></a>技能参数

这些参数区分大小写并且都是可选的。

| 参数名称     | 说明 |
|--------------------|-------------|
| `defaultLanguageCode` | （可选）要应用到未显式指定语言的文档的语言代码。  如果未指定默认语言代码，会将英语 (en) 用作默认语言代码。 <br/> 请参阅[支持的语言的完整列表](../cognitive-services/text-analytics/language-support.md?tabs=pii)。 |
| `minimumPrecision` | 一个介于 0.0 和 1.0 之间的值。 如果置信度分数（在 `piiEntities` 输出中）低于所设置的 `minimumPrecision` 值，则不会返回或屏蔽该实体。 默认值为 0.0。 |
| `maskingMode` | 一个参数，提供各种方法来屏蔽在输入文本中检测到的个人信息。 可以使用以下选项： <ul><li>`none`（默认值）：不会发生屏蔽，并且不会返回 `maskedText` 输出。 </li><li> `replace`：使用 `maskingCharacter` 参数中给定的字符替换检测到的实体。 将重复该字符，直至达到检测到的实体的长度，以便偏移量与输入文本和输出 `maskedText` 都正确对应。</li></ul> <br/> 在 PIIDetectionSkill 预览版中，还支持 `maskingMode` 的 `redact` 选项，这允许完全删除检测到的实体，不进行替换。 `redact` 选项已被弃用，以后的技能中将不再支持此选项。 |
| `maskingCharacter` | 当 `maskingMode` 参数设置为 `replace` 时用来屏蔽文本的字符。 支持以下选项：`*`（默认）。 如果 `maskingMode` 未设置为 `replace`，则此参数只能为 `null`。 <br/><br/> 在 PIIDetectionSkill 预览版中，还支持 `maskingCharacter` 选项 `X` 和 `#`。 `X` 和 `#` 选项已被弃用，以后的技能中将不再支持此选项。 |
| `domain`   | （可选）字符串值（如果指定）将 PII 域设置为仅包含实体类别的子集。 可能的值包括：`phi`（仅检测机密运行状况信息）、`none`。 |
| `piiCategories`   | （可选）如要指定将检测并返回哪些实体，请使用可选的 `piiCategories` 参数（定义为字符串列表）指定相应的实体类别。 此参数还可以检测默认情况下未为文档语言启用的实体。 有关可用的类别列表，请参阅 [TextAnalytics 文档](../cognitive-services/text-analytics/named-entity-types.md?tabs=personal)。  |
| `modelVersion`   | （可选）调用文本分析服务时要使用的模型版本。 如果未指定，将默认为最新版本。 建议不要指定此值，除非绝对必要。 有关详细信息，请参阅[文本分析 API 中的模型版本控制](../cognitive-services/text-analytics/concepts/model-versioning.md)。 |

## <a name="skill-inputs"></a>技能输入

| 输入名称      | 说明                   |
|---------------|-------------------------------|
| `languageCode`    | 表示记录的语言的字符串。 如果未指定此参数，将使用默认语言代码分析记录。 <br/>请参阅[支持的语言的完整列表](../cognitive-services/text-analytics/language-support.md?tabs=pii)  |
| `text`          | 要分析的文本。          |

## <a name="skill-outputs"></a>技能输出

| 输出名称      | 说明                   |
|---------------|-------------------------------|
| `piiEntities` | 复杂类型的数组，包含以下字段： <ul><li>text（提取的实际 PII）</li> <li>type</li><li>subType</li><li>score（值越高意味着它越有可能是一个真实的实体）</li><li>offset（输入文本中）</li><li>length</li></ul> </br> [可在此处找到可能的类型和子类型。](../cognitive-services/text-analytics/named-entity-types.md?tabs=personal) |
| `maskedText` | 如果 `maskingMode` 设置为 `none` 以外的值，则此输出将是对由所选 `maskingMode` 描述的输入文本执行屏蔽后的字符串结果。  如果 `maskingMode` 设置为 `none`，则不会提供此输出。 |

## <a name="sample-definition"></a>示例定义

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.PIIDetectionSkill",
    "defaultLanguageCode": "en",
    "minimumPrecision": 0.5,
    "maskingMode": "replace",
    "maskingCharacter": "*",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "piiEntities"
      },
      {
        "name": "maskedText"
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
             "text": "Microsoft employee with ssn 859-98-0987 is using our awesome API's."
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
        "piiEntities":[ 
           { 
              "text":"859-98-0987",
              "type":"U.S. Social Security Number (SSN)",
              "subtype":"",
              "offset":28,
              "length":11,
              "score":0.65
           }
        ],
        "maskedText": "Microsoft employee with ssn *********** is using our awesome API's."
      }
    }
  ]
}
```

在此技能的输出中，针对实体返回的偏移量是直接从[文本分析 API](../cognitive-services/text-analytics/overview.md) 返回的，这意味着如果使用这些偏移量为原始字符串编制索引，则应使用 .NET 中的 [StringInfo](/dotnet/api/system.globalization.stringinfo) 类来提取正确的内容。  [此处提供了更多详细信息。](../cognitive-services/text-analytics/concepts/text-offsets.md)

## <a name="errors-and-warnings"></a>错误和警告

如果文档的语言代码不受支持，则会返回警告，并且不提取任何实体。
如果文本为空，则返回警告。
如果文本大于 50,000 个字符，只会分析前 50,000 个字符，并会发出警告。

如果此技能返回警告，则输出 `maskedText` 可能为空，这可能会影响任何需要该输出的下游技能。 因此，在编写技能组定义时，一定要调查所有与缺少输出有关的警告。

## <a name="see-also"></a>另请参阅

+ [内置技能](cognitive-search-predefined-skills.md)
+ [如何定义技能集](cognitive-search-defining-skillset.md)