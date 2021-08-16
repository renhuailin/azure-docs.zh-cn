---
title: 从图像中提取文本
titleSuffix: Azure Cognitive Search
description: 处理和提取 Azure 认知搜索管道中的图像中的文本和其他信息。
manager: nitinme
author: LuisCabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 68186c5294c0a3a2f376a93ef1902307780f48bb
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2021
ms.locfileid: "111538284"
---
# <a name="how-to-process-and-extract-information-from-images-in-ai-enrichment-scenarios"></a>如何处理和提取 AI 扩充方案中的图像中的信息

Azure 认知搜索有多项适用于图像和图像文件的功能。 在文档破解过程中，可以使用 *imageAction* 参数从包含字母数字文本的照片或图片中提取文本，例如停车标志中的“停”字样。 其他场景包括：生成图像的文本表示形式，例如代表蒲公英照片的“蒲公英”字样，或者“黄色”颜色。 还可以提取图像的元数据，例如其大小。

本文详细介绍图像处理，并提供了在 AI 扩充管道中处理图像的指南。

<a name="get-normalized-images"></a>

## <a name="get-normalized-images"></a>获取规范化的图像

在文档破解过程中，可以使用新的一组索引器配置参数来处理图像文件或嵌入文件中的图像。 这些参数用于将图像规范化，以便进行进一步的下游处理。 规范化图像可以使图像更统一。 可以根据最大高度和宽度来重设大型图像的大小，使之可用。 对于提供方向元数据的图像，可以调整图像旋转，使之适合垂直加载。 元数据调整项在为每个图像创建的复杂类型中捕获。 

无法关闭图像规范化功能。 循环访问图像的技术需要规范化的图像。 在索引器上启用图像规范化需要将技能组附加到该索引器。

| 配置参数 | 说明 |
|--------------------|-------------|
| imageAction   | 如果在遇到嵌入图像或图像文件时无需执行任何操作，请将此项设置为 "none"。 <br/>设置为 "generateNormalizedImages" 会在文档破解过程中生成一系列规范化的图像。<br/>设置为“generateNormalizedImagePerPage”，以生成一系列规范化的图像，对于数据源中的 PDF 文件，每一页呈现为一个输出图像。  对于非 PDF 文件类型，该功能与“generateNormalizedImages”相同。<br/>对于任何不是“none”的选项，这些图像会在 *normalized_images* 字段中公开。 <br/>默认为 "none"。 将 "dataToExtract" 设置为 "contentAndMetadata" 时，此配置仅与 Blob 数据源相关。 <br/>将从给定文档中提取最多 1000 个图像。 如果在文档中有超过 1000 个图像，则将提取前 1000 个，并将生成警告。 |
|  normalizedImageMaxWidth | 生成的规范化图像的最大宽度（以像素为单位）。 默认为 2000。 允许的最大值为 10000。 | 
|  normalizedImageMaxHeight | 生成的规范化图像的最大高度（以像素为单位）。 默认为 2000。 允许的最大值为 10000。|

> [!NOTE]
> 如果将 *imageAction* 属性设置为 "none" 之外的其他值，则只能将 *parsingMode* 属性设置为 "default"。  在索引器配置中，只能将这两个属性中的一个设置为非默认值。

将 **parsingMode** 参数设置为 `json`（将每个 Blob 作为单个文档进行索引编制）或 `jsonArray`（如果 Blob 包含 JSON 数组，且需要将数组的每个元素视为单独的文档）。

将规范化图像的最大宽度和高度默认设置为 2000 像素是考虑到 [OCR 技术](cognitive-search-skill-ocr.md)所能够支持的最大大小以及[图像分析技术](cognitive-search-skill-image-analysis.md)。 [OCR 技能](cognitive-search-skill-ocr.md)支持非英语语言的最大宽度和高度为 4200，支持英语语言的最大宽度和高度为 10000。  如果增加最大限制，则根据技能组定义和文档语言，对较大的图像进行处理可能会失败。 

可以指定[索引器定义](/rest/api/searchservice/create-indexer)中所述的 imageAction，如下所示：

```json
{
  //...rest of your indexer definition goes here ...
  "parameters":
  {
    "configuration": 
    {
        "dataToExtract": "contentAndMetadata",
        "imageAction": "generateNormalizedImages"
    }
  }
}
```

将 *imageAction* 设置为“none”以外的值后，新的 *normalized_images* 字段会包含一系列图像。 每个图像都是一个包含以下成员的复杂类型：

| 图像成员       | 说明                             |
|--------------------|-----------------------------------------|
| 数据               | JPEG 格式的规范化图像的 BASE64 编码字符串。   |
| width              | 规范化图像的宽度（以像素为单位）。 |
| 高度             | 规范化图像的高度（以像素为单位）。 |
| originalWidth      | 图像在规范化之前的原始宽度。 |
| originalHeight      | 图像在规范化之前的原始高度。 |
| rotationFromOriginal |  在创建规范化图像过程中进行的逆时针旋转（以度为单位）。 值的范围为 0 度到 360 度。 此步骤从图像读取由照相机或扫描仪生成的元数据。 通常为 90 度的倍数。 |
| contentOffset | 从其提取图像的内容字段中的字符偏移。 此字段仅适用于包含嵌入图像的文件。 |
| pageNumber | 如果图像是从 PDF 提取或呈现的，则此字段包含从中提取或呈现图像的 PDF 中的页码（从 1 开始）。  如果图像不是来自 PDF，则此字段将为 0。  |

 *normalized_images* 的示例值：
```json
[
  {
    "data": "BASE64 ENCODED STRING OF A JPEG IMAGE",
    "width": 500,
    "height": 300,
    "originalWidth": 5000,  
    "originalHeight": 3000,
    "rotationFromOriginal": 90,
    "contentOffset": 500,
    "pageNumber": 2
  }
]
```

## <a name="image-related-skills"></a>图像相关技能

若要接受图像作为输入，可以使用自定义技能或内置技能。 内置技能包括 [OCR](cognitive-search-skill-ocr.md) 和[图像分析](cognitive-search-skill-image-analysis.md)。 

目前，这些技术仅适用于通过文档破解步骤生成的图像。 因此，唯一支持的输入为 `"/document/normalized_images"`。

### <a name="image-analysis-skill"></a>图像分析技术

[图像分析技术](cognitive-search-skill-image-analysis.md)根据图像内容提取丰富的视觉特征。 例如，可从图像生成标题栏、生成标记或识别名人和地标。

### <a name="ocr-skill"></a>OCR 技术

[OCR 技术](cognitive-search-skill-ocr.md)可从图像文件（例如 JPG、PNG、位图）中提取文本。 它可以提取文本和布局信息。 布局信息为每个确定的字符串提供边框。

### <a name="custom-skills"></a>自定义技能

还可以将图像传入自定义技能，以及从自定义技能返回图像。 技能集对要传入到自定义技能的图像进行 base64 编码。 若要在自定义技能内使用图像，请将 `/document/normalized_images/*/data` 设置为自定义技能的输入。 在自定义技能代码中，先对字符串进行 base64 解码，然后再将其转换为图像。 若要将图像返回到技能集，请先对图像进行 base64 编码，然后再将图像返回到技能组。

 图像作为具有以下属性的对象返回。

```json
 { 
  "$type": "file", 
  "data": "base64String" 
 }
```

[Azure 搜索 python 示例](https://github.com/Azure-Samples/azure-search-python-samples)存储库包含一个使用 Python 实现的完整示例，该示例包含一个可扩充图像的自定义技能。

## <a name="embedded-image-scenario"></a>嵌入图像场景

常见的场景包括通过执行以下步骤，创建单个包含所有文件内容的字符串，既有文本，又有图像原点文本：  

1. [提取 normalized_images](#get-normalized-images)
1. 运行 OCR 技术，使用 `"/document/normalized_images"` 作为输入
1. 将这些图像的文本表示形式与从文件提取的原始文本合并。 可以使用[文本合并](cognitive-search-skill-textmerger.md)技术将两个文本区块合并成单个大型字符串。

以下示例技术集会创建的 merged_text 字段包含文档的文本内容， 以及每个嵌入图像中的 OCR 化文本。 

### <a name="request-body-syntax"></a>请求正文语法

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
          "name":"text", "source": "/document/content"
        },
        {
          "name": "itemsToInsert", "source": "/document/normalized_images/*/text"
        },
        {
          "name":"offsets", "source": "/document/normalized_images/*/contentOffset" 
        }
      ],
      "outputs": [
        {
          "name": "mergedText", "targetName" : "merged_text"
        }
      ]
    }
  ]
}
```

有了 merged_text 字段以后，即可将其映射为索引器定义中的可搜索字段。 文件的所有内容（包括图像的文本）将均可搜索。

## <a name="visualize-bounding-boxes-of-extracted-text"></a>将已提取文本的边框可视化

另一个常见场景是将搜索结果布局信息可视化。 例如，可能需要突出显示在搜索结果的图像中找到一段文本的位置。

由于 OCR 步骤是在规范化图像上执行的，因此布局坐标位于规范化图像空间中。 显示规范化图像时，存在坐标通常不是一个问题，但在某些情况下，可能需要显示原始图像。 在这种情况下，请将布局中的每个坐标点转换为原始图像坐标系统。 

提示：如果需要将规范化的坐标转换为原始坐标空间，可以使用以下算法：

```csharp
        /// <summary>
        ///  Converts a point in the normalized coordinate space to the original coordinate space.
        ///  This method assumes the rotation angles are multiples of 90 degrees.
        /// </summary>
        public static Point GetOriginalCoordinates(Point normalized,
                                    int originalWidth,
                                    int originalHeight,
                                    int width,
                                    int height,
                                    double rotationFromOriginal)
        {
            Point original = new Point();
            double angle = rotationFromOriginal % 360;

            if (angle == 0 )
            {
                original.X = normalized.X;
                original.Y = normalized.Y;
            } else if (angle == 90)
            {
                original.X = normalized.Y;
                original.Y = (width - normalized.X);
            } else if (angle == 180)
            {
                original.X = (width -  normalized.X);
                original.Y = (height - normalized.Y);
            } else if (angle == 270)
            {
                original.X = height - normalized.Y;
                original.Y = normalized.X;
            }

            double scalingFactor = (angle % 180 == 0) ? originalHeight / height : originalHeight / width;
            original.X = (int) (original.X * scalingFactor);
            original.Y = (int)(original.Y * scalingFactor);

            return original;
        }
```

## <a name="passing-images-to-custom-skills"></a>将图像传递到自定义技能

对于需要自定义技能来处理图像的情况，可以将图像传递到自定义技能，并使其返回文本或图像。 [Python 示例](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Image-Processing)图像处理演示了工作流。 以下技能组来自该示例。

以下技能组接受（在文档破解时获取的）规范化图像，并输出图像的切片。

### <a name="sample-skillset"></a>示例技能组

```json
{
  "description": "Extract text from images and merge with content text to produce merged_text",
  "skills":
  [
    {
          "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
          "name": "ImageSkill",
          "description": "Segment Images",
          "context": "/document/normalized_images/*",
          "uri": "https://your.custom.skill.url",
          "httpMethod": "POST",
          "timeout": "PT30S",
          "batchSize": 100,
          "degreeOfParallelism": 1,
          "inputs": [
            {
              "name": "image",
              "source": "/document/normalized_images/*"
            }
          ],
          "outputs": [
            {
              "name": "slices",
              "targetName": "slices"
            }
          ],
          "httpHeaders": {}
        }
  ]
}
```

### <a name="custom-skill"></a>自定义技能

自定义技能本身在技能组的外部。 在本例中，是 Python 代码首先循环使用自定义技能格式的一批请求记录，然后将 base64 编码的字符串转换为图像。

```python
# deserialize the request, for each item in the batch
for value in values:
  data = value['data']
  base64String = data["image"]["data"]
  base64Bytes = base64String.encode('utf-8')
  inputBytes = base64.b64decode(base64Bytes)
  # Use numpy to convert the string to an image
  jpg_as_np = np.frombuffer(inputBytes, dtype=np.uint8)
  # you now have an image to work with
```

同样，若要返回图像，则在 `$type` 属性为 `file` 的 JSON 对象内返回 base64 编码的字符串。

```python
def base64EncodeImage(image):
    is_success, im_buf_arr = cv2.imencode(".jpg", image)
    byte_im = im_buf_arr.tobytes()
    base64Bytes = base64.b64encode(byte_im)
    base64String = base64Bytes.decode('utf-8')
    return base64String

 base64String = base64EncodeImage(jpg_as_np)
 result = { 
  "$type": "file", 
  "data": base64String 
}
```

## <a name="see-also"></a>另请参阅

+ [创建索引器 (REST)](/rest/api/searchservice/create-indexer)
+ [图像分析技术](cognitive-search-skill-image-analysis.md)
+ [OCR 技术](cognitive-search-skill-ocr.md)
+ [文本合并技术](cognitive-search-skill-textmerger.md)
+ [如何定义技能集](cognitive-search-defining-skillset.md)
+ [如何映射扩充的域](cognitive-search-output-field-mapping.md)
+ [如何将图像传递到自定义技能](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Image-Processing)
