---
title: 如何调用读取 API
titleSuffix: Azure Cognitive Services
description: 了解如何调用读取 API 并详细配置其行为。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: pafarley
ms.openlocfilehash: d5a1da6bbe251e6200cd3a64117748e9ebbfae2a
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112298108"
---
# <a name="call-the-read-api"></a>调用读取 API

在本指南中，你将了解如何通过调用读取 API 从图像中提取文本。 你将了解此 API 行为的不同配置方式以满足你的需求。

本指南假设你已经<a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="创建计算机视觉资源"  target="_blank">创建计算机视觉资源 </a> 并获取订阅密钥和终结点 URL。 如果没有，请按照[快速入门](../quickstarts-sdk/client-library.md)中的说明开始操作。

## <a name="submit-data-to-the-service"></a>提交服务数据

可向读取 API 提交本地映像或远程映像。 就本地映像而言，请将二进制图像数据放在 HTTP 请求正文中。 就远程映像而言，请通过设置请求正文的格式来指定图像的 URL，如下所示：`{"url":"http://example.com/images/test.jpg"}`。

读取 API 的[读取调用](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005)采用图像或 PDF 文档作为输入，以异步方式提取文本。

`https://{endpoint}/vision/v3.2/read/analyze[?language][&pages][&readingOrder]`

该调用返回时将包含一个名为 `Operation-Location` 的响应头字段。 `Operation-Location` 值是一个 URL，其中包含要在下一步骤中使用的操作 ID。

|响应标头| 示例值 |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/vision/v3.2/read/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

> [!NOTE]
> **Billing** 
>
> [计算机视觉定价](https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/)页面包含了读取 API 的定价层。 分析的每个图像或页面均为一个事务。 如果对包含 100 个页面的 PDF 或 TIFF 文档调用该操作，则读取操作会将其计为 100 个事务，而你需要按 100 个事务付费。 如果对该操作发出了 50 次调用，而每次调用提交了包含 100 个页面的文档，则你需要按照 50 X 100 = 5000 个事务付费。

## <a name="determine-how-to-process-the-data"></a>确定如何处理数据

### <a name="language-specification"></a>语言规范

[读取](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005) 调用包含可选语言请求参数。 “读取”支持自动语言识别和多语言文档，因此，如果你要强制将文档作为一种特定语言进行处理，只需提供该语言的代码即可。

### <a name="natural-reading-order-output-latin-languages-only"></a>自然读取顺序输出（仅限拉丁语）

使用 `readingOrder` 查询参数指定文本行的输出顺序。 使用 `natural` 可提供更易于阅读的读取顺序输出，如以下示例中所示。 此功能仅支持拉丁语。

:::image type="content" source="../Images/ocr-reading-order-example.png" alt-text="OCR 读取顺序示例" border="true" :::

### <a name="select-pages-or-page-ranges-for-text-extraction"></a>选择页面或页面范围以进行文本提取

对于较大的多页文档，请使用 `pages` 查询参数指定页码或页面范围，以便仅从这些页面提取文本。 以下示例演示了一个包含 10 个页面的文档，将按照所有页面 (1-10) 和选定页面 (3-6) 这两种条件提取文本。

:::image type="content" source="../Images/ocr-select-pages.png" alt-text="选定页面输出" border="true" :::

## <a name="get-results-from-the-service"></a>获取服务结果

第二个步骤是调用[获取读取结果](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d9869604be85dee480c8750)操作。 此操作采用读取操作创建的操作 ID 作为输入。

`https://{endpoint}/vision/v3.2/read/analyzeResults/{operationId}`

此操作返回一个 JSON 响应，其中包含具有以下可能值的 **status** 字段。

|值 | 含义 |
|:-----|:----|
| `notStarted`| 尚未启动操作。 |
| `running`| 正在处理操作。 |
| `failed`| 操作失败。 |
| `succeeded`| 操作已成功执行。 |

可以不断地以迭代方式调用此操作，直到它返回 **succeeded** 值为止。 使用 1 到 2 秒的间隔可以避免超过每秒请求数 (RPS) 的速率限制。

> [!NOTE]
> 免费层将请求速率限制为每分钟 20 次调用。 付费层允许的每秒请求数 (RPS) 为 10 个，该限制可按请求提高。 注意你的 Azure 资源标识符和区域，并打开 Azure 支持票证或联系帐户团队，请求更高的每秒请求数 (RPS) 速率。

当 **status** 字段的值为 `succeeded` 时，JSON 响应将包含从图像或文档提取的文本内容。 JSON 响应会维护已识别单词的原始分组。 其中包括提取的文本行及其边界框坐标。 每个文本行都包含所有提取的单词及其坐标和可信度分数。

> [!NOTE]
> 提交到 `Read` 操作的数据将暂时加密并静态存储较短的一段时间，然后被删除。 这样，应用程序便可以检索提取的文本作为服务响应的一部分。

### <a name="sample-json-output"></a>示例 JSON 输出

参阅下面的成功 JSON 响应示例：

```json
{
  "status": "succeeded",
  "createdDateTime": "2021-02-04T06:32:08.2752706+00:00",
  "lastUpdatedDateTime": "2021-02-04T06:32:08.7706172+00:00",
  "analyzeResult": {
    "version": "3.2",
    "readResults": [
      {
        "page": 1,
        "angle": 2.1243,
        "width": 502,
        "height": 252,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              58,
              42,
              314,
              59,
              311,
              123,
              56,
              121
            ],
            "text": "Tabs vs",
            "appearance": {
              "style": {
                "name": "handwriting",
                "confidence": 0.96
              }
            },
            "words": [
              {
                "boundingBox": [
                  68,
                  44,
                  225,
                  59,
                  224,
                  122,
                  66,
                  123
                ],
                "text": "Tabs",
                "confidence": 0.933
              },
              {
                "boundingBox": [
                  241,
                  61,
                  314,
                  72,
                  314,
                  123,
                  239,
                  122
                ],
                "text": "vs",
                "confidence": 0.977
              }
            ]
          }
        ]
      }
    ]
  }
}
```

### <a name="handwritten-classification-for-text-lines-latin-languages-only"></a>文本行手写分类（仅限拉丁语）

响应将分类说明每个文本行是否为手写体，同时包括置信度评分。 此功能仅支持拉丁语。 以下示例演示了图像中文本的手写分类。

:::image type="content" source="../Images/ocr-handwriting-classification.png" alt-text="OCR 手写分类示例" border="true" :::

## <a name="next-steps"></a>后续步骤

若要试用 REST API，请转到[读取 API 参考](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005)。
