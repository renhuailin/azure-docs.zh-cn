---
title: 文本拆分认知技能
titleSuffix: Azure Cognitive Search
description: 基于 Azure 认知搜索中 AI 扩充管道中的长度，将文本分解为区块或文本页。
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: e5b907b89491721d2529f2caa303fc9e77d47169
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121862578"
---
# <a name="text-split-cognitive-skill"></a>文本拆分认知技能

文本拆分技能将文本分解为文本区块。 你可以指定是要将文件分解为句子还是特定长度的页面。 当其他技能下游有最大文本长度要求时，此技能尤其有用。 

> [!NOTE]
> 此技能不绑定到认知服务。 它不计费，并且无认知服务关键要求。

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SplitSkill 

## <a name="skill-parameters"></a>技能参数

参数区分大小写。

| 参数名称     | 说明 |
|--------------------|-------------|
| `textSplitMode`    | `pages` 或 `sentences` | 
| `maximumPageLength` | 仅当将 `textSplitMode` 设置为 `pages` 时才适用。 它指 `String.Length` 测量的最大页面长度（以字符为单位）。 最小值为 300，最大值为 100000，默认值为 10000。  此算法会尽可能在句子边界断开文本，所以每个区块大小可能略小于 `maximumPageLength`。 | 
| `defaultLanguageCode` | （可选）以下语言代码之一：`am, bs, cs, da, de, en, es, et, fr, he, hi, hr, hu, fi, id, is, it, ja, ko, lv, no, nl, pl, pt-PT, pt-BR, ru, sk, sl, sr, sv, tr, ur, zh-Hans` 默认为英语 (en)。 注意事项：<ul><li>提供语言代码有助于避免将非空格的语言（例如，中文、日语和韩语）的单词一分为二。</li><li>如果你不知道语言（例如，需要将输入的文本拆分为 [LanguageDetectionSkill](cognitive-search-skill-language-detection.md)），则默认的英语 (en) 应该已足够。 </li></ul>  |


## <a name="skill-inputs"></a>技能输入

| 参数名称       | 说明      |
|----------------------|------------------|
| `text`    | 要拆分为子字符串的文本。 |
| `languageCode`    | （可选）文档的语言代码。 如果你不知道语言（例如，需要将输入的文本拆分为 [LanguageDetectionSkill](cognitive-search-skill-language-detection.md)），则可以放心地删除此输入。 如果此语言不在上述 `defaultLanguageCode` 参数的支持列表中，则会发出警告，且不会拆分文本。  |

## <a name="skill-outputs"></a>技能输出 

| 参数名称     | 说明 |
|--------------------|-------------|
| `textItems`   | 提取的子字符串数组。 |


##  <a name="sample-definition"></a>示例定义

```json
{
    "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
    "textSplitMode" : "pages", 
    "maximumPageLength": 1000,
    "defaultLanguageCode": "en",
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
            "name": "textItems",
            "targetName": "mypages"
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
                "text": "This is a the loan application for Joe Romero, a Microsoft employee who was born in Chile and who then moved to Australia…",
                "languageCode": "en"
            }
        },
        {
            "recordId": "2",
            "data": {
                "text": "This is the second document, which will be broken into several pages...",
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
                "textItems": [
                    "This is the loan…",
                    "On the second page we…"
                ]
            }
        },
        {
            "recordId": "2",
            "data": {
                "textItems": [
                    "This is the second document...",
                    "On the second page of the second doc…"
                ]
            }
        }
    ]
}
```

## <a name="error-cases"></a>错误案例
如果某语言不受支持，则会生成警告。

## <a name="see-also"></a>另请参阅

+ [内置技能](cognitive-search-predefined-skills.md)
+ [如何定义技能集](cognitive-search-defining-skillset.md)
