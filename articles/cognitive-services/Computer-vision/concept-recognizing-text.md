---
title: 光学字符识别 (OCR) - 计算机视觉
titleSuffix: Azure Cognitive Services
description: 与光学字符识别相关的概念 (使用计算机视觉 API 打印和手写文本的图像和文档的 OCR) 。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: pafarley
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 1d633b1a9f5fee0a5cceb48f2b37aaec2092069f
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979517"
---
# <a name="optical-character-recognition-ocr"></a>光学字符识别 (OCR)

Azure 的计算机视觉 API 包含光学字符识别 (OCR) 功能，这些功能可从图像中提取打印文本或手写文本。 你可以从图像中提取文本，如许可证印版照片或带有序列号的容器，以及文档-发票、帐单、财务报表、文章等。

## <a name="read-api"></a>读取 API 

计算机视觉 [读取 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) 是 Azure 的最新 OCR 技术 ([了解哪些新增功能](./whats-new.md)) ，该功能提取了几种语言的打印文本 () ，手写文本仅限图像和多页面 PDF 文档中 (、数字和货币符号。 它经过优化，可以从带有混合语言的文本繁重图像和多页面 PDF 文档中提取文本。 它支持在同一图像或文档中检测打印文本和手写文本。

![OCR 如何将图像和文档转换为带有提取文本的结构化输出](./Images/how-ocr-works.svg)

## <a name="input-requirements"></a>输入要求
**读取** 调用采用图像和文档作为其输入。 它们具有以下要求：

* 支持的文件格式：JPEG、PNG、BMP、PDF 和 TIFF
* 对于 PDF 和 TIFF 文件，最多2000个页面只 (处理免费层) 的前两页。
* 对于免费层，文件大小必须小于 50 MB (4 MB) 和至少 50 x 50 像素到 10000 x 10000 像素的维度。 
* PDF 尺寸必须为 17 x 17 英寸或更小，对应于 Legal 或 A3 纸张尺寸及更小。

> [!NOTE]
> **语言输入** 
>
> [Read 调用](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005)具有适用于 language 的可选请求参数。 Read 支持自动语言标识和多语言文档，因此，如果想要强制将文档作为该特定语言进行处理，只需提供语言代码。

## <a name="ocr-demo-examples"></a>OCR 演示 (示例) 

![OCR 演示](./Images/ocr-demo.gif)

## <a name="step-1-the-read-operation"></a>步骤1：读取操作

读取 API 的 [读取调用](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) 采用图像或 PDF 文档作为输入，并以异步方式提取文本。 调用返回并带有一个名为的响应标头字段 `Operation-Location` 。 `Operation-Location`该值是一个 URL，其中包含要在下一步中使用的操作 ID。

|响应标头| 结果 URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/vision/v3.1/read/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

> [!NOTE]
> **Billing** 
>
> [计算机视觉定价](https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/)页面包含用于读取的定价层。 每个分析的图像或页面都是一个事务。 如果使用包含100页的 PDF 或 TIFF 文档调用该操作，则读取操作会将其计为100个事务，并将为100个交易计费。 如果对操作发出了50个调用，并且每次调用提交了包含100页的文档，则会向你收取 50 X 100 = 5000 事务的费用。

## <a name="step-2-the-get-read-results-operation"></a>步骤2：获取读取结果操作

第二步是调用 [获取读取结果](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d9869604be85dee480c8750) 操作。 此操作使用读取操作创建的操作 ID 作为输入。 它将返回一个 JSON 响应，该响应包含具有以下可能值的 **状态** 字段。 此操作以迭代方式调用，直到它返回 **成功** 值。 使用1到2秒的间隔，以避免超出每秒 (RPS) 速率的请求数。

|字段| 类型 | 可能值 |
|:-----|:----:|:----|
|状态 | string | notStarted：操作尚未开始。 |
| |  | 正在运行：正在处理操作。 |
| |  | failed：操作失败。 |
| |  | succeeded：操作成功。 |

> [!NOTE]
> 免费层将请求速率限制为每分钟20个调用。 付费层允许每秒10个请求 (RPS) ，可以根据请求增加。 使用 Azure 支持渠道或你的帐户团队 (RPS) 速率请求一个更高的请求。

当 " **状态** " 字段的值为 " **成功** " 时，JSON 响应包含图像或文档中提取的文本内容。 JSON 响应会维护已识别单词的原始分组。 其中包括提取的文本行及其边界框坐标。 每个文本行都包含所有提取的单词及其坐标和可信度分数。

> [!NOTE]
> 提交给操作的数据 `Read` 将暂时加密并存储在短期内，然后会被删除。 这样，应用程序就可以在服务响应中检索提取的文本。

## <a name="sample-json-output"></a>示例 JSON 输出

请参阅以下成功的 JSON 响应示例：

```json
{
  "status": "succeeded",
  "createdDateTime": "2020-05-28T05:13:21Z",
  "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
  "analyzeResult": {
    "version": "3.1.0",
    "readResults": [
      {
        "page": 1,
        "angle": 0.8551,
        "width": 2661,
        "height": 1901,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              67,
              646,
              2582,
              713,
              2580,
              876,
              67,
              821
            ],
            "text": "The quick brown fox jumps",
            "words": [
              {
                "boundingBox": [
                  143,
                  650,
                  435,
                  661,
                  436,
                  823,
                  144,
                  824
                ],
                "text": "The",
                "confidence": 0.958
              }
            ]
          }
        ]
      }
    ]
  }
}
```

## <a name="natural-reading-order-output"></a>自然读取顺序输出
对于 [读取3.2 预览 API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-2/operations/5d986960601faab4bf452005)，请指定文本行与查询参数的输出顺序 `readingOrder` 。 使用 `natural` 更友好的读取顺序输出，如以下示例中所示。

:::image border type="content" source="./Images/ocr-reading-order-example.png" alt-text="OCR 阅读顺序示例":::

## <a name="handwritten-classification-for-text-lines-latin-only"></a>文本行的手写分类仅 (拉丁语) 
[阅读3.2 预览 API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-2/operations/5d986960601faab4bf452005)响应包括对每个文本行是否为手写样式，同时还包括置信度分数。 此功能仅支持拉丁语。 下面的示例演示了图像中文本的手写分类。

:::image border type="content" source="./Images/ocr-handwriting-classification.png" alt-text="OCR 手写分类示例":::

## <a name="select-pages-or-page-ranges-for-text-extraction"></a>选择页面 (s) 或页面范围进行文本提取
使用 [Read 3.2 预览 API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-2/operations/5d986960601faab4bf452005)，对于大的多页文档，使用 `pages` query 参数指定页码或页面范围以便仅从这些页面提取文本。 以下示例显示了一个包含10个页面的文档，同时为这两种情况提取了文本-所有页面 (1-10) ，所选页面 (3-6) 。

:::image border type="content" source="./Images/ocr-select-pages.png" alt-text="所选页面输出":::

## <a name="supported-languages"></a>支持的语言
对于打印样式文本，读取 Api 支持总共73种语言。 请参阅 [OCR 支持的语言](./language-support.md#optical-character-recognition-ocr)的完整列表。 手写样式 OCR 仅支持英语。

## <a name="use-the-cloud-api-or-deploy-on-premise"></a>使用云 API 或本地部署
对于大多数客户而言，读取的1.x 云 Api 是首选选项，因为它可以轻松集成，并可实现快速工作效率。 当你集中精力满足客户需求时，Azure 和计算机视觉服务可处理规模、性能、数据安全和合规性需求。

对于本地部署， [读取 Docker 容器 (预览) ](./computer-vision-how-to-install-containers.md) 使你能够在自己的本地环境中部署新的 OCR 功能。 容器非常适合用于满足特定的安全性和数据管理要求。

## <a name="ocr-api"></a>OCR API

[OCR API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f20d) 使用较旧的识别模式，仅支持图像，且以同步方式执行，并立即返回检测到的文本。 请参阅 [OCR 支持的语言](./language-support.md#optical-character-recognition-ocr) ，然后阅读 API。

> [!NOTE]
> 计算机 Vison 2.0 RecognizeText 操作被弃用，以支持本文涵盖的新读取 API。 现有客户应 [使用读取操作转换为](upgrade-api-versions.md)。

## <a name="next-steps"></a>后续步骤

- [计算机视觉 REST API 或客户端库快速入门教程](./quickstarts-sdk/client-library.md)入门。
- 了解 [Read 3.1 REST API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005)。
- 了解 [Read 3.2 公开预览版 REST API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-2/operations/5d986960601faab4bf452005) ，支持总共73种语言。
