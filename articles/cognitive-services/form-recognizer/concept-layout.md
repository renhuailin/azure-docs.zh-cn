---
title: 布局 - 表单识别器
titleSuffix: Azure Cognitive Services
description: 使用表单识别器 API 了解与布局分析相关的概念 - 使用和限制。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: cc19d5652f416657cbcd339de61379265587b4e7
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2021
ms.locfileid: "106505376"
---
# <a name="form-recognizer-layout-service"></a>表单识别器布局服务

Azure 表单识别器可以使用其布局服务从文档中提取文本、表、选定标记和结构信息。 通过布局 API，客户能够以各种格式获取文档，并返回文档的结构化数据表示形式。 它结合了强大的[光学字符识别 (OCR)](../computer-vision/overview-ocr.md) 功能与深度学习模型，能够提取文本、表、选定标记和文档结构。 

## <a name="what-does-the-layout-service-do"></a>布局服务有什么作用？

布局 API 从具有异常准确性的文档中提取文本、表、选定标记和结构信息，并返回组织的结构化 JSON 响应。 文档可以是各种格式和质量，包括手机拍摄的图像、扫描文档和数字 PDF。 布局 API 将从所有这些文档中准确提取结构化输出。

![布局示例](./media/layout-tool-example.JPG)

## <a name="try-it-out"></a>试试看

若要试用表单识别器布局服务，请转到联机 UI 工具示例：

> [!div class="nextstepaction"]
> [尝试使用表单识别器](https://fott-preview.azurewebsites.net)

若要试用表单识别器布局 API，你将需要一个 Azure 订阅（[免费创建一个](https://azure.microsoft.com/free/cognitive-services)）和一个[表单识别器资源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer)终结点和密钥。 

![示例 UI 屏幕截图；分析文档的文本、表和选定标记](./media/analyze-layout.png)

### <a name="input-requirements"></a>输入要求 

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-layout-operation"></a>分析布局操作

首先，调用[“分析布局”](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeLayoutAsync)操作。 分析布局采用文档（图像、TIFF 或 PDF 文件）作为输入，并提取文本、表、选定标记和文档的结构。 该调用返回一个名为 `Operation-Location` 的响应标头字段。 `Operation-Location` 值是一个 URL，其中包含要在下一步骤中使用的结果 ID。

|响应标头| 结果 URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.3/layout/analyzeResults/44a436324-fc4b-4387-aa06-090cfbf0064f` |

### <a name="natural-reading-order-output-latin-only"></a>自然读取顺序输出（仅限拉丁语）

你可以使用 `readingOrder` 查询参数指定文本行的输出顺序。 使用 `natural` 可提供更易于阅读的读取顺序输出，如以下示例中所示。 此功能仅支持拉丁语。

:::image type="content" source="../Computer-vision/Images/ocr-reading-order-example.png" alt-text="布局读取顺序示例" lightbox="../Computer-vision/Images/ocr-reading-order-example.png":::

### <a name="select-page-numbers-or-ranges-for-text-extraction"></a>选择页码或页面范围以进行文本提取

对于较大的多页文档，请使用 `pages` 查询参数指示用于文本提取的特定页码或页面范围。 以下示例演示了一个包含 10 个页面的文档，将按照所有页面 (1-10) 和选定页面 (3-6) 这两种条件提取文本。

:::image type="content" source="../Computer-vision/Images/ocr-select-pages.png" alt-text="布局选定页面输出":::

## <a name="the-get-analyze-layout-result-operation"></a>Get Analyze Layout Result 操作

第二步是调用 [Get Analyze Layout Result](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeLayoutResult) 操作。 此操作采用分析布局操作创建的结果 ID 作为输入。 此操作返回一个 JSON 响应，其中包含具有以下可能值的 **status** 字段。 

|字段| 类型 | 可能值 |
|:-----|:----:|:----|
|status | 字符串 | `notStarted`：分析操作尚未开始。<br /><br />`running`：分析操作正在进行。<br /><br />`failed`：分析操作失败。<br /><br />`succeeded`：分析操作成功。|

可以不断地以迭代方式调用此操作，直到它返回 `succeeded` 值为止。 使用 3 到 5 秒的间隔可以避免超过每秒请求数 (RPS) 的速率限制。

当“status”字段的值为 `succeeded` 时，JSON 响应将包含提取的布局、文本、表和选定标记。 提取的数据包括提取的文本行和单词、边界框、带有手写指示的文本外观、表以及所指示的选定/未选择的选定标记。 

### <a name="handwritten-classification-for-text-lines-latin-only"></a>文本行的手写分类（仅限拉丁语）

响应将分类说明每个文本行是否为手写体，同时包括置信度评分。 此功能仅支持拉丁语。 以下示例演示了图像中文本的手写分类。

:::image type="content" source="../Computer-vision/Images/ocr-handwriting-classification.png" alt-text="手写分类示例":::

### <a name="sample-json-output"></a>示例 JSON 输出

对 Get Analyze Layout Result 操作的响应是文档的结构化表示形式，其中提取了所有信息。 有关[示例文档文件](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-layout.pdf)及其结构化输出[示例布局输出](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-layout-output.json)，请参阅此处。

JSON 输出分为两个部分：

* `readResults` 节点包含所有已识别的文本和选定标记。 文本按页，然后按行，然后按单个单词进行组织。 
* `pageResults` 节点包含提取的表和单元格及其边界框、置信度以及对“readResults”中的行和词的引用。

## <a name="example-output"></a>示例输出

### <a name="text"></a>文本

布局 API 从文档（PDF、TIFF）和具有多个文本角度和颜色的图像（JPG、PNG、BMP）中提取文本。 它接受文档、传真、打印和/或手写（仅英文）文本和混合模式的照片。 提取文本时会提供有关行、词、边界框、置信度评分和样式（手写体或其他样式）的信息。 所有文本信息都包含在 JSON 输出的 `readResults` 部分中。 

### <a name="tables"></a>表

布局 API 从文档（PDF、TIFF）和图像（JPG、PNG、BMP）中提取表。 可以扫描、拍摄或数字化文档。 表可能会比较复杂，有的带合并单元格或列，有的带边框，有的不带边框，还有的带奇角。 提取的表信息包括列数和行数、行高和列高。 每个单元格都用其边界框进行提取，并引用 `readResults` 部分中提取的文本。 表信息位于 JSON 输出的 `pageResults` 部分。 

![表示例](./media/tables-example.jpg)

### <a name="selection-marks"></a>选定标记

布局 API 还从文档中提取选定标记。 提取的选定标记包含边界框、置信度和状态（选择/取消选择）。 在 JSON 输出的 `readResults` 部分提取选定标记信息。 

## <a name="next-steps"></a>后续步骤

* 使用[表单识别器示例 UI 工具](https://fott-preview.azurewebsites.net/)尝试自己进行布局提取
* 完成[表单识别器快速入门](quickstarts/client-library.md)，开始以你选择的开发语言提取布局。

## <a name="see-also"></a>请参阅

* [什么是表单识别器？](./overview.md)
* [REST API 参考文档](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeLayoutAsync)
