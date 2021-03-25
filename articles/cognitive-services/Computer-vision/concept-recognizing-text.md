---
title: 光学字符识别 (OCR) - 计算机视觉
titleSuffix: Azure Cognitive Services
description: 与使用计算机视觉 API 对包含印刷和手写文本的图像及文档进行光学字符识别 (OCR) 相关的概念。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: pafarley
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: e0247560afa8229f4fa5c25ec7dfbbca4f7defb2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102486092"
---
# <a name="optical-character-recognition-ocr"></a>光学字符识别 (OCR)

Azure 的计算机视觉 API 包含用于从图像中提取印刷文本或手写文本的光学字符识别 (OCR) 功能。 可以从图像（例如带有序列号的牌照或容器的照片）中，以及文档（发票、账单、财务报表、文章等）中提取文本。

## <a name="read-api"></a>读取 API 

计算机视觉[读取 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) 是 Azure 最新的 OCR 技术（[了解新功能](./whats-new.md)），可从图像和多页 PDF 文档中提取印刷文本（多种语言）、手写文本（仅英语）、数字和货币符号。 该技术经过优化，可以从多文本图像中提取文本，也可从包含混合语言的多页 PDF 文档中提取文本。 它支持检测同一图像或文档中的印刷文本和手写文本。

![OCR 如何将图像和文档转换为带有提取文本的结构化输出](./Images/how-ocr-works.svg)

## <a name="input-requirements"></a>输入要求
**读取** 调用采用图像和文档作为输入。 这些输入需满足以下要求：

* 支持的文件格式：JPEG、PNG、BMP、PDF 和 TIFF
* 对于 PDF 和 TIFF 文件，最多处理 2000 个页面（对于免费层，只处理前两个页面）。
* 文件大小必须小于 50 MB（对于免费层，则为 4 MB），且尺寸介于 50 x 50 和 10000 x 10000 像素之间。 
* PDF 尺寸必须为 17 x 17 英寸或更小，对应于 Legal 或 A3 纸张尺寸及更小。

> [!NOTE]
> **语言输入** 
>
> 对于语言，[读取调用](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005)具有一个可选的请求参数。 “读取”支持自动语言识别和多语言文档，因此，如果你要强制将文档作为一种特定语言进行处理，只需提供该语言的代码即可。

## <a name="ocr-demo-examples"></a>OCR 演示（示例）

![OCR 演示](./Images/ocr-demo.gif)

## <a name="step-1-the-read-operation"></a>步骤 1：读取操作

读取 API 的[读取调用](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005)采用图像或 PDF 文档作为输入，以异步方式提取文本。 该调用返回时将包含一个名为 `Operation-Location` 的响应头字段。 `Operation-Location` 值是一个 URL，其中包含要在下一步骤中使用的操作 ID。

|响应标头| 结果 URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/vision/v3.1/read/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

> [!NOTE]
> **Billing** 
>
> [计算机视觉定价](https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/)页面包含了读取 API 的定价层。 分析的每个图像或页面均为一个事务。 如果对包含 100 个页面的 PDF 或 TIFF 文档调用该操作，则读取操作会将其计为 100 个事务，而你需要按 100 个事务付费。 如果对该操作发出了 50 次调用，而每次调用提交了包含 100 个页面的文档，则你需要按照 50 X 100 = 5000 个事务付费。

## <a name="step-2-the-get-read-results-operation"></a>步骤 2：获取读取结果操作

第二个步骤是调用[获取读取结果](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d9869604be85dee480c8750)操作。 此操作采用读取操作创建的操作 ID 作为输入。 此操作返回一个 JSON 响应，其中包含具有以下可能值的 **status** 字段。 可以不断地以迭代方式调用此操作，直到它返回 **succeeded** 值为止。 使用 1 到 2 秒的间隔可以避免超过每秒请求数 (RPS) 的速率限制。

|字段| 类型 | 可能值 |
|:-----|:----:|:----|
|status | 字符串 | notStarted：操作尚未启动。 |
| |  | running：正在处理操作。 |
| |  | failed：操作失败。 |
| |  | succeeded：操作成功。 |

> [!NOTE]
> 免费层将请求速率限制为每分钟 20 次调用。 付费层允许的每秒请求数 (RPS) 为 10 个，该限制可按请求提高。 可以联系 Azure 支持渠道或帐户团队来请求更高的每秒请求数 (RPS) 速率。

当 **status** 字段的值为 **succeeded** 时，JSON 响应将包含从图像或文档提取的文本内容。 JSON 响应会维护已识别单词的原始分组。 其中包括提取的文本行及其边界框坐标。 每个文本行都包含所有提取的单词及其坐标和可信度分数。

> [!NOTE]
> 提交到 `Read` 操作的数据将暂时加密并静态存储较短的一段时间，然后被删除。 这样，应用程序便可以检索提取的文本作为服务响应的一部分。

## <a name="sample-json-output"></a>示例 JSON 输出

参阅下面的成功 JSON 响应示例：

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

## <a name="natural-reading-order-output-latin-only"></a>自然读取顺序输出（仅限拉丁语）
使用[读取 3.2 预览版 API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005) 时，请使用 `readingOrder` 查询参数指定文本行的输出顺序。 使用 `natural` 可提供更易于阅读的读取顺序输出，如以下示例中所示。 此功能仅支持拉丁语。

:::image border type="content" source="./Images/ocr-reading-order-example.png" alt-text="OCR 读取顺序示例":::

## <a name="handwritten-classification-for-text-lines-latin-only"></a>文本行的手写分类（仅限拉丁语）
[读取 3.2 预览版 API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005) 响应将分类说明每个文本行是否为手写体，同时包括置信度评分。 此功能仅支持拉丁语。 以下示例演示了图像中文本的手写分类。

:::image border type="content" source="./Images/ocr-handwriting-classification.png" alt-text="OCR 手写分类示例":::

## <a name="select-pages-or-page-ranges-for-text-extraction"></a>选择页面或页面范围以进行文本提取
使用[读取 3.2 预览版 API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005) 时，对于较大的多页面文档，请使用 `pages` 查询参数指定页码或页面范围，以便仅从这些页面提取文本。 以下示例演示了一个包含 10 个页面的文档，将按照所有页面 (1-10) 和选定页面 (3-6) 这两种条件提取文本。

:::image border type="content" source="./Images/ocr-select-pages.png" alt-text="选定页面输出":::

## <a name="supported-languages"></a>支持的语言
对于印刷体文本，读取 API 总共支持 73 种语言。 请参阅 [OCR 支持的语言](./language-support.md#optical-character-recognition-ocr)完整列表。 手写体 OCR 仅支持英语。

## <a name="use-the-cloud-api-or-deploy-on-premise"></a>使用云 API 或本地部署
大多数客户都青睐读取 3.x 云 API，因为它易于集成，而且其现成可用的性质能够提高工作效率。 Azure 和计算机视觉服务将处理缩放、性能、数据安全与合规需求，你只需将工作重心放在满足客户需求上。

对于本地部署，可以使用[读取 Docker 容器（预览版）](./computer-vision-how-to-install-containers.md)在你自己的本地环境中部署新的 OCR 功能。 容器非常适合用于满足特定的安全性和数据管理要求。

## <a name="ocr-api"></a>OCR API

[OCR API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f20d) 使用较旧的识别模式，仅支持图像，且以同步方式执行，并立即返回检测到的文本。 请先参阅 [OCR 支持的语言](./language-support.md#optical-character-recognition-ocr)，然后再了解读取 API。

> [!NOTE]
> 计算机视觉 2.0 RecognizeText 操作即将弃用，将由本文中所述的新的读取 API 所取代。 现有客户应[改用读取操作](upgrade-api-versions.md)。

## <a name="next-steps"></a>后续步骤

- 参考[计算机视觉 REST API 或客户端库快速入门](./quickstarts-sdk/client-library.md)开始使用。
- 了解[读取 3.1 REST API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005)。
- 了解总共支持 73 种语言的[读取 3.2 公共预览版 REST API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005)。
