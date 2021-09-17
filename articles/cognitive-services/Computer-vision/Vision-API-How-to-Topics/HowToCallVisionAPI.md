---
title: 调用图像分析 API
titleSuffix: Azure Cognitive Services
description: 了解如何调用图像分析 API 并配置其行为。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 0014297d45c26d855a2df8c2a3fdccb2f0c047d2
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122822902"
---
# <a name="call-the-image-analysis-api"></a>调用图像分析 API

本文演示如何调用图像分析 API 以返回有关图像的视觉特征的信息。

本指南假设你已经<a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="创建计算机视觉资源"  target="_blank">创建计算机视觉资源 </a> 并获取订阅密钥和终结点 URL。 如果没有，请按照[快速入门](../quickstarts-sdk/image-analysis-client-library.md)中的说明开始操作。
  
## <a name="submit-data-to-the-service"></a>将数据提交到服务

可向分析 API 提交本地映像或远程映像。 就本地映像而言，请将二进制图像数据放在 HTTP 请求正文中。 就远程映像而言，请通过设置请求正文的格式来指定图像的 URL，如下所示：`{"url":"http://example.com/images/test.jpg"}`。

## <a name="determine-how-to-process-the-data"></a>确定如何处理数据

###  <a name="select-visual-features"></a>选择视觉特征

使用[分析 API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f21b) 可以访问所有服务的图像分析特征。 需要设置 URL 查询参数来指定要使用的特征。 参数可以具有多个值（用逗号分隔）。 指定的每项特征都需要额外的计算时间，因此只需指定所需的特征。

|URL 参数 | 值 | 说明|
|---|---|--|
|`visualFeatures`|`Adult` | 检测图片是否具有色情性质（描绘裸体或性行为），以及是否具有血腥内容（描绘极端暴力或血腥）。 还会检测性暗示内容（也称为不雅内容）。|
||`Brands` | 检测图像中的各种品牌，包括大致位置。 品牌参数仅以英语提供。|
||`Categories` | 根据文档中定义的分类对图像内容进行分类。 这是 `visualFeatures` 的默认值。|
||`Color` | 确定主题色、主色以及图像是否为黑白。|
||`Description` | 用受支持的语言以完整的句子描述图像内容。|
||`Faces` | 检测人脸是否存在。 如果存在，则生成位置、性别和年龄。|
||`ImageType` | 检测图像是剪贴画还是素描。|
||`Objects` | 检测图像中的各种对象，包括大致位置。 Objects 参数仅以英语提供。|
||`Tags` | 使用与图像内容相关字词的详细列表来标记图像。|
|`details`| `Celebrities` | 识别在图像中检测到的名人。|
||`Landmarks` |识别在图像中检测到的地标。|

填充的 URL 可能如下所示：

`https://{endpoint}/vision/v2.1/analyze?visualFeatures=Description,Tags&details=Celebrities`

### <a name="specify-languages"></a>指定语言

还可以指定返回的数据的语言。 以下 URL 查询参数指定语言。 默认值为 `en`。

|URL 参数 | 值 | 说明|
|---|---|--|
|`language`|`en` | 英语|
||`es` | 西班牙语|
||`ja` | 日语|
||`pt` | 葡萄牙语|
||`zh` | 简体中文|

填充的 URL 可能如下所示：

`https://{endpoint}/vision/v2.1/analyze?visualFeatures=Description,Tags&details=Celebrities&language=en`

> [!NOTE]
> **范围内 API 调用**
>
> 可以直接调用图像分析中的某些特征，也可以通过分析 API 调用来调用。 例如，你可以向 `https://{endpoint}/vision/v3.2/tag` 发出请求来执行仅限图像标记的作用域分析。 有关可单独调用的其他特征，请参阅[参考文档](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f21b)。

## <a name="get-results-from-the-service"></a>获取服务结果

服务返回 `200` HTTP 响应，正文包含 JSON 字符串形式的返回数据。 以下是一个 JSON 响应示例。

```json
{  
  "tags":[  
    {  
      "name":"outdoor",
      "score":0.976
    },
    {  
      "name":"bird",
      "score":0.95
    }
  ],
  "description":{  
    "tags":[  
      "outdoor",
      "bird"
    ],
    "captions":[  
      {  
        "text":"partridge in a pear tree",
        "confidence":0.96
      }
    ]
  }
}
```

有关此示例中的字段的说明，请参阅下表：

字段 | 类型 | 内容
------|------|------|
Tags  | `object` | 标记数组的顶级对象。
tags[].Name | `string`    | 标记分类器中的关键字。
tags[].Score    | `number`    | 置信度评分，介于 0 和 1 之间。
description     | `object`    | 图像说明的顶级对象。
description.tags[] |    `string`    | 标记列表。 如果置信度不足，因此无法生成标题，则标记可能是可供调用方使用的唯一信息。
description.captions[].text    | `string`    | 描述图像的短语。
description.captions[].confidence    | `number`    | 短语的置信度评分。

### <a name="error-codes"></a>错误代码

请参阅以下可能出现的错误及其原因的列表：

* 400
    * InvalidImageUrl - 图片 URL 格式不正确或无法访问。
    * InvalidImageFormat - 输入数据不是有效的图像。
    * InvalidImageSize - 输入的图像太大。
    * NotSupportedVisualFeature - 指定的特征类型无效。
    * NotSupportedImage - 不支持的图像，例如儿童色情。
    * InvalidDetails - 不支持的 `detail` 参数值。
    * NotSupportedLanguage - 指定的语言不支持请求的操作。
    * BadArgument - 错误消息中提供了更多详细信息。
* 415 - 不支持的媒体类型。 Content-Type 类型不在允许的类型中：
    * 对于图像 URL：Content-Type 应为 application/json
    * 对于二进制图像数据：Content-Type 应为 application/octet-stream 或 multipart/form-data
* 500
    * FailedToProcess
    * Timeout - 图像处理超时。
    * InternalServerError

> [!TIP]
> 使用计算机视觉时，可能会遇到服务强制实施的[速率限制](https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/)所导致的暂时性错误，或者网络中断等其他暂时性问题。 有关如何处理此类故障的信息，请参阅云设计模式指南中的[重试模式](https://docs.microsoft.com/azure/architecture/patterns/retry)，以及相关的[断路器模式](https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker)。


## <a name="next-steps"></a>后续步骤

试用 REST API，请转到[图像分析 API 参考](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f21b)。
