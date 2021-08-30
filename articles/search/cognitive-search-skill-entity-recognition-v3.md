---
title: 实体识别 (V3) 认知技能
titleSuffix: Azure Cognitive Search
description: 在 Azure 认知搜索中使用文本分析 V3 从扩充管道中的文本提取不同类型的实体。
manager: jennmar
author: ayokande
ms.author: aakande
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: 14811591feea9df735bf41e23a81e3a96faa2662
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121862668"
---
# <a name="entity-recognition-cognitive-skill-v3"></a>实体识别认知技能 (V3)

**实体识别** 技能从文本中提取各种类型的实体。 这些实体分为 14 个不同类别，范围从人员和组织到 URL 和电话号码。 此技能使用认知服务中的[文本分析](../cognitive-services/text-analytics/overview.md)提供的机器学习模型。

> [!NOTE]
> 此技能与 Azure 认知服务绑定，对于超过每个索引器每天 20 个文档的事务，需要[一个计费资源](cognitive-search-attach-cognitive-services.md)。 内置技能执行按现有[认知服务即用即付价格](https://azure.microsoft.com/pricing/details/cognitive-services/)计费。
>

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.V3.EntityRecognitionSkill

## <a name="data-limits"></a>数据限制
记录的最大大小应为 50,000 个字符，通过 [`String.Length`](/dotnet/api/system.string.length) 进行测量。 如果在将数据发送到 EntityRecognition 技能之前需要进行分解，请考虑使用[文本拆分技能](cognitive-search-skill-textsplit.md)。

## <a name="skill-parameters"></a>技能参数

参数区分大小写并且都是可选的。

| 参数名称     | 说明 |
|--------------------|-------------|
| `categories`    | 应提取的类别的数组。  可能的类别类型：`"Person"`、`"Location"`、`"Organization"`、`"Quantity"`、`"DateTime"`、`"URL"`、`"Email"`、`"PersonType"`、`"Event"`、`"Product"`、`"Skill"`、`"Address"`、`"Phone Number"`、`"IP Address"`。 如果不提供类别，则返回所有类型。|
| `defaultLanguageCode` |    输入文本的语言代码。 如果未指定默认语言代码，会将英语 (en) 用作默认语言代码。 <br/> 请参阅[支持的语言的完整列表](../cognitive-services/text-analytics/language-support.md)。 并非所有实体类别都支持所有语言；请参阅下文中的说明。|
| `minimumPrecision` | 一个介于 0 和 1 之间的值。 如果置信度分数（在 `namedEntities` 输出中）低于此值，则不会返回该实体。 默认值为 0。 |
| `modelVersion` | （可选）调用文本分析服务时要使用的模型版本。 如果未指定，将默认为最新可用版本。 建议不要指定此值，除非绝对必要。 有关详细信息，请参阅[文本分析 API 中的模型版本控制](../cognitive-services/text-analytics/concepts/model-versioning.md)。|


## <a name="skill-inputs"></a>技能输入

| 输入名称      | 说明                   |
|---------------|-------------------------------|
| `languageCode`    | 表示记录的语言的字符串。 如果未指定此参数，将使用默认语言代码分析记录。 <br/>请参阅[支持的语言的完整列表](../cognitive-services/text-analytics/language-support.md)。 |
| `text`          | 要分析的文本。          |

## <a name="skill-outputs"></a>技能输出

> [!NOTE]
>并非所有实体类别都支持所有语言。 请参阅[文本分析 API v3 中支持的实体类别](../cognitive-services/text-analytics/named-entity-types.md)，了解将要使用的语言支持哪些实体类别。

| 输出名称      | 说明                   |
|---------------|-------------------------------|
| `persons`       | 一个字符串数组，其中，一个字符串表示一个人员名称。 |
| `locations`  | 一个字符串数组，其中，一个字符串表示一个位置。 |
| `organizations`  | 一个字符串数组，其中，一个字符串表示一个组织。 |
| `quantities`  | 一个字符串数组，其中，每个字符串都表示一个数量。 |
| `dateTimes`  | 一个字符串数组，其中，每个字符串都表示一个日期时间（因为它以文本形式显示）值。 |
| `urls` | 一个字符串数组，其中，每个字符串都表示一个 URL |
| `emails` | 一个字符串数组，其中，每个字符串都表示一个电子邮件地址 |
| `personTypes` | 一个字符串数组，其中，每个字符串都表示一个人员类型 |
| `events` | 一个字符串数组，其中，每个字符串都表示一个事件 |
| `products` | 一个字符串数组，其中，每个字符串都表示一个产品 |
| `skills` | 一个字符串数组，其中，每个字符串都表示一个技能 |
| `addresses` | 一个字符串数组，其中，每个字符串都表示一个地址 |
| `phoneNumbers` | 一个字符串数组，其中，每个字符串都表示一个电话号码 |
| `ipAddresses` | 一个字符串数组，其中，每个字符串都表示一个 IP 地址 |
| `namedEntities` | 复杂类型的数组，包含以下字段： <ul><li>category</li> <li>子类别</li> <li>置信度（值越高意味着它越有可能是一个真实的实体）</li> <li>长度（此实体的长度（字符数））</li> <li>偏移（在文本中找到它的位置）</li> <li>文本（文本中显示的实际实体名称）</li></ul> |


##    <a name="sample-definition"></a>示例定义

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.V3.EntityRecognitionSkill",
    "context": "/document",
    "categories": [ "Person", "Email"],
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
            "name": "persons", 
            "targetName": "people"
        },
        {
            "name": "emails", 
            "targetName": "emails"
        },
        {
            "name": "namedEntities", 
            "targetName": "namedEntities"
        }
    ]
  }
```
##    <a name="sample-input"></a>示例输入

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Contoso Corporation was founded by Jean Martin. They can be reached at contact@contoso.com",
             "languageCode": "en"
           }
      }
    ]
}
```

##    <a name="sample-output"></a>示例输出

```json
{
  "values": [
    {
      "recordId": "1",
      "data" : 
      {
        "people": [ "Jean Martin"],
        "emails":["contact@contoso.com"],
        "namedEntities": 
        [
          {
            "category": "Person",
            "subcategory": null,
            "length": 11,
            "offset": 35,
            "confidenceScore": 0.98,
            "text": "Jean Martin"
          },
          {
            "category": "Email",
            "subcategory": null,
            "length": 19,
            "offset": 71,
            "confidenceScore": 0.8,
            "text": "contact@contoso.com"
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