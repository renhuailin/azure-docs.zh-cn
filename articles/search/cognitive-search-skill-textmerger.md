---
title: 文本合并认知技能
titleSuffix: Azure Cognitive Search
description: 将字段集合中的文本合并到合并字段中。 在 Azure 认知搜索中的 AI 扩充管道中使用此认知技能。
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: 4ea7681a28cf8f17c53e42e9ad05ddf12b5d2f9b
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121860547"
---
#   <a name="text-merge-cognitive-skill"></a>文本合并认知技能

文本合并  技能会将字段集合中的文本合并到单个字段中。 

> [!NOTE]
> 此技能不限于认知服务。 它不可计费，并且没有认知服务密钥要求。

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.MergeSkill

## <a name="skill-parameters"></a>技能参数

参数区分大小写。

| 参数名称     | 说明 |
|--------------------|-------------|
| `insertPreTag`    | 每次插入之前要包含的字符串。 默认值为 `" "`。 要忽略空格，请将值设置为 `""`。  |
| `insertPostTag`   | 每次插入后要包含的字符串。 默认值为 `" "`。 要忽略空格，请将值设置为 `""`。  |


##  <a name="sample-input"></a>示例输入
为此技能提供可用输入的 JSON 文档有：

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
      {
        "text": "The brown fox jumps over the dog",
        "itemsToInsert": ["quick", "lazy"],
        "offsets": [3, 28]
      }
    }
  ]
}
```

##  <a name="sample-output"></a>示例输出
此示例显示之前输入的输出，假设将 insertPreTag 设置为 `" "`并将 insertPostTag 设置为 `""`。 

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
      {
        "mergedText": "The quick brown fox jumps over the lazy dog"
      }
    }
  ]
}
```

## <a name="extended-sample-skillset-definition"></a>扩展的示例技能集定义

使用文本合并的一个常见场景是将图像的文本表示形式（OCR 技能中的文本或图像的描述文字）合并到文档的内容字段中。 

以下示例技能使用 OCR 技能从文档中嵌入的图像中提取文本。 接下来，它会创建 merged_text 字段以包含每个图像的原始和 OCRed 文本。 可在[此处](./cognitive-search-skill-ocr.md)了解有关 OCR 技能的详细信息。

```json
{
  "description": "Extract text from images and merge with content text to produce merged_text",
  "skills":
  [
    {
      "description": "Extract text (plain and structured) from image.",
      "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
      "context": "/document/normalized_images/*",
      "defaultLanguageCode": "en",
      "detectOrientation": true,
      "inputs": [
        {
          "name": "image",
          "source": "/document/normalized_images/*"
        }
      ],
      "outputs": [
        {
          "name": "text"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
      "description": "Create merged_text, which includes all the textual representation of each image inserted at the right location in the content field.",
      "context": "/document",
      "insertPreTag": " ",
      "insertPostTag": " ",
      "inputs": [
        {
          "name":"text", 
          "source": "/document/content"
        },
        {
          "name": "itemsToInsert", 
          "source": "/document/normalized_images/*/text"
        },
        {
          "name":"offsets", 
          "source": "/document/normalized_images/*/contentOffset" 
        }
      ],
      "outputs": [
        {
          "name": "mergedText", 
          "targetName" : "merged_text"
        }
      ]
    }
  ]
}
```
以上示例假设存在规范化的图像字段。 要获取规范化的图像字段，请将索引器定义中的 imageAction 配置设置为 generateNormalizedImages，如下所示：

```json
{
  //...rest of your indexer definition goes here ...
  "parameters":{
    "configuration":{
        "dataToExtract":"contentAndMetadata",
        "imageAction":"generateNormalizedImages"
    }
  }
}
```

## <a name="see-also"></a>另请参阅

+ [内置技能](cognitive-search-predefined-skills.md)
+ [如何定义技能集](cognitive-search-defining-skillset.md)
+ [创建索引器 (REST)](/rest/api/searchservice/create-indexer)