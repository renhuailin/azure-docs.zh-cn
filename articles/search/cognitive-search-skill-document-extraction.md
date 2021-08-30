---
title: 文档提取认知技能
titleSuffix: Azure Cognitive Search
description: 从扩充管道内的文件中提取内容。
manager: nitinme
author: careyjmac
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/12/2021
ms.author: chalton
ms.openlocfilehash: 02d3431ecc7a5c460be75885fd786b3b4c4d276f
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121860569"
---
# <a name="document-extraction-cognitive-skill"></a>文档提取认知技能

**文档提取** 技能从扩充管道内的文件中提取内容。 这使你可以利用文档提取步骤，该步骤通常在技能组执行之前对可能由其他技能生成的文件执行。

> [!NOTE]
> 此技能不受限于认知服务，也没有认知服务密钥要求。
> 此技能可提取文本和图像。 文本提取是免费的。 图像提取[由 Azure 认知搜索计量](https://azure.microsoft.com/pricing/details/search/)。 在免费搜索服务中，每个索引器每天 20 个事务的成本已吸收，因此你可以免费完成快速入门、教程和小型搜索。 对于基本、标准及以上的服务，图像提取是收费的。
>

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.DocumentExtractionSkill

## <a name="supported-document-formats"></a>支持的文档格式

DocumentExtractionSkill 可从以下文档格式提取文本：

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="skill-parameters"></a>技能参数

参数区分大小写。

| 输入 | 允许的值 | 说明 |
|-----------------|----------------|-------------|
| `parsingMode`   | `default` <br/> `text` <br/> `json`  | 对于从非纯文本或 json 文件进行的文档提取，请设置为 `default`。 若要提高针对纯文本文件的性能，请设置为 `text`。 若要从 json 文件中提取结构化内容，请设置为 `json`。 如果未显式定义 `parsingMode`，则它将设置为 `default`。 |
| `dataToExtract` | `contentAndMetadata` <br/> `allMetadata` | 若要提取每个文件的所有元数据和文本内容，请设置为 `contentAndMetadata`。 若要仅提取[内容类型的元数据属性](search-blob-metadata-properties.md)（例如，仅 .png 文件独有的元数据），请设置为 `allMetadata`。 如果未显式定义 `dataToExtract`，则它将设置为 `contentAndMetadata`。 |
| `configuration` | 请参阅下文。 | 可选参数的字典，这些参数用于调整文档提取执行方式。 有关支持的配置属性的说明，请参阅下表。 |

| 配置参数   | 允许的值 | 说明 |
|-------------------------|----------------|-------------|
| `imageAction`           | `none`<br/> `generateNormalizedImages`<br/> `generateNormalizedImagePerPage` | 若要忽略数据集中的嵌入图像或图像文件，请设置为 `none`。 这是默认值。 <br/>对于[使用认知技能的图像分析](cognitive-search-concept-image-scenarios.md)，设置为 `generateNormalizedImages` 可使技能在[文档破解](search-indexer-overview.md#document-cracking)过程中创建规范化图像的数组。 此操作需要将 `parsingMode` 设置为 `default`，将 `dataToExtract` 设置为 `contentAndMetadata`。 规范化的图像是指在视觉搜索结果中包含图像时，对图像进行额外的处理，使图像的输出一致，并通过调整大小和旋转方向使图像在呈现时更一致（例如，使图像控件中的照片大小一致，如 [JFK 演示](https://github.com/Microsoft/AzureSearch_JFK_Files)中所示）。 当使用此选项时，将为每个图像生成此信息。  <br/>如果设置为 `generateNormalizedImagePerPage`，则将以不同的方式对待 PDF 文件，将不会提取嵌入的图像，而是将每个页面呈现为图像并相应地规范化。  对待非 PDF 文件类型的方式与设置了 `generateNormalizedImages` 时相同。
| `normalizedImageMaxWidth` | 一个介于 50 和 10000 之间的整数 | 生成的规范化图像的最大宽度（以像素为单位）。 默认为 2000。 | 
| `normalizedImageMaxHeight` | 一个介于 50 和 10000 之间的整数 | 生成的规范化图像的最大高度（以像素为单位）。 默认为 2000。 |

> [!NOTE]
> 将规范化图像的最大宽度和高度默认设置为 2000 像素是考虑到 [OCR 技术](cognitive-search-skill-ocr.md)所能够支持的最大大小以及[图像分析技术](cognitive-search-skill-image-analysis.md)。 [OCR 技能](cognitive-search-skill-ocr.md)支持非英语语言的最大宽度和高度为 4200，支持英语语言的最大宽度和高度为 10000。  如果增加最大限制，则根据技能组定义和文档语言，对较大的图像进行处理可能会失败。 
## <a name="skill-inputs"></a>技能输入

| 输入名称     | 说明 |
|--------------------|-------------|
| `file_data` | 应从其中提取内容的文件。 |

“file_data”必须是按如下方式定义的一个对象：

```json
{
  "$type": "file",
  "data": "BASE64 encoded string of the file"
}
```

或

```json
{
  "$type": "file",
  "url": "URL to download file",
  "sasToken": "OPTIONAL: SAS token for authentication if the URL provided is for a file in blob storage"
}
```

可以通过以下三种方式之一生成此文件引用对象：

 - 在你的索引器定义中将 `allowSkillsetToReadFileData` 参数设置为“true”。  这将创建路径 `/document/file_data`，该路径是一个对象，表示从 blob 数据源下载的原始文件数据。 此参数仅适用于 Blob 存储中的数据。

 - 在你的索引器定义中将 `imageAction` 参数设置为 `none` 之外的值。  这将创建一个图像数组，该数组将遵循此技能的输入在逐个传递的情况下所需的约定（即 `/document/normalized_images/*`）。

 - 让自定义技能返回严格如上所述定义的 json 对象。  `$type` 参数必须精确设置为 `file`，`data` 参数必须是文件内容的 Base64 编码字节数组数据，或者 `url` 参数必须是格式正确的 URL，有权在该位置下载文件。

## <a name="skill-outputs"></a>技能输出

| 输出名称    | 说明 |
|--------------|-------------|
| `content` | 文档的文本内容。 |
| `normalized_images`   | 将 `imageAction` 设置为 `none` 以外的值后，新的 *normalized_images* 字段将包含一个图像数组。 有关每个图像的输出格式的更多详细信息，请参阅[有关图像提取的文档](cognitive-search-concept-image-scenarios.md)。 |

##  <a name="sample-definition"></a>示例定义

```json
 {
    "@odata.type": "#Microsoft.Skills.Util.DocumentExtractionSkill",
    "parsingMode": "default",
    "dataToExtract": "contentAndMetadata",
    "configuration": {
        "imageAction": "generateNormalizedImages",
        "normalizedImageMaxWidth": 2000,
        "normalizedImageMaxHeight": 2000
    },
    "context": "/document",
    "inputs": [
      {
        "name": "file_data",
        "source": "/document/file_data"
      }
    ],
    "outputs": [
      {
        "name": "content",
        "targetName": "extracted_content"
      },
      {
        "name": "normalized_images",
        "targetName": "extracted_normalized_images"
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
        "file_data": {
          "$type": "file",
          "data": "aGVsbG8="
        }
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
        "content": "hello",
        "normalized_images": []
      }
    }
  ]
}
```

## <a name="see-also"></a>另请参阅

+ [内置技能](cognitive-search-predefined-skills.md)
+ [如何定义技能集](cognitive-search-defining-skillset.md)
+ [如何在认知搜索方案中处理和提取图像中的信息](cognitive-search-concept-image-scenarios.md)