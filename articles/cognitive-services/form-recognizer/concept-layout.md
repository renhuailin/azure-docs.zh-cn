---
title: 布局-窗体识别器
titleSuffix: Azure Cognitive Services
description: 了解与布局分析相关的概念，格式识别器 API-用法和限制。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: pafarley
ms.openlocfilehash: 60ef490bbba4dca4f01b82967ad6ea8636e7bd4e
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353691"
---
# <a name="form-recognizer-layout-service"></a>表单识别器布局服务

Azure 窗体识别器可以使用其布局服务从文档中提取文本、表、选择标记和结构信息。 布局 API 使客户能够以各种格式获取文档，并返回文档的结构化数据和表示形式。 它结合了强大的 [光学字符识别 (OCR) ](../computer-vision/concept-recognizing-text.md) 功能，并提供了文档理解深度学习模型来提取文本、表、选择标记和文档的结构。 

## <a name="what-does-the-layout-service-do"></a>Layout 服务有什么作用？

布局 API 从具有异常准确性的文档中提取文本、表、选择标记和结构信息，并将其返回到组织的结构化 JSON 响应中。 文档可以是各种格式和质量，包括手机捕获的图像、扫描的文档和数字 Pdf。 布局 API 将从所有这些文档中提取结构化的输出。

![布局示例](./media/layout-tool-example.JPG)

## <a name="try-it-out"></a>试试看

若要试用窗体识别器布局服务，请访问联机示例 UI 工具：

> [!div class="nextstepaction"]
> [示例 UI](https://fott-preview.azurewebsites.net/)

需要一个 Azure 订阅 (免费) [创建一个](https://azure.microsoft.com/free/cognitive-services) ，并使用 [窗体识别器资源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) 终结点和密钥来试用表单识别器布局 API。 

![示例 UI 屏幕快照;分析文档的文本、表格和选择标记](./media/analyze-layout.png)

### <a name="input-requirements"></a>输入要求 

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-layout-operation"></a>分析布局操作

" [分析布局](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeLayoutAsync) " 操作采用文档 (图像、TIFF 或 PDF 文件) 作为输入，并提取文档的文本、表、选择标记和结构。 调用返回一个名为的响应标头字段 `Operation-Location` 。 `Operation-Location`该值是一个 URL，其中包含要在下一步中使用的结果 ID。

|响应标头| 结果 URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/layout/analyzeResults/44a436324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="the-get-analyze-layout-result-operation"></a>获取分析布局结果操作

第二步是调用 " [获取分析布局" 结果](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeLayoutResult) 操作。 此操作采用分析布局操作创建的结果 ID 作为输入。 它将返回一个 JSON 响应，该响应包含具有以下可能值的 **状态** 字段。 

|字段| 类型 | 可能值 |
|:-----|:----:|:----|
|状态 | 字符串 | `notStarted`：分析操作尚未开始。<br /><br />`running`：正在进行分析操作。<br /><br />`failed`：分析操作失败。<br /><br />`succeeded`：分析操作已成功。|

此操作以迭代方式调用，直到其返回 `succeeded` 值。 使用3到5秒的间隔，以避免超出每秒 (RPS) 速率的请求数。

当 " **状态** " 字段的 `succeeded` 值为时，JSON 响应将包含提取的布局提取结果、文本、表和选定标记。 提取的数据包含提取的文本线条和单词、边界框、文本外观的手写指示、表和选择标记，指示已选中/未选择。 

### <a name="sample-json-output"></a>示例 JSON 输出

对 Get 分析布局结果操作的响应将是已提取所有信息的文档的结构化表示形式。 有关 [示例文档文件](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-layout.pdf) 及其结构化输出 [示例布局输出](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-layout-output.json)，请参阅此处。

JSON 输出分为两部分： 
* `"readResults"` 节点包含所有已识别的文本和选定标记。 文本按页，然后按行，然后按单个单词进行组织。 
* `"pageResults"` 节点包含通过其边界框、置信度以及对 "readResults" 中的行和词的引用提取的表和单元格。

## <a name="example-output"></a>示例输出

### <a name="text"></a>文本

布局从文档中提取文本 (PDF、TIFF) 和图像 (jpg、png、bmp) ，其中包含不同的文本角度、颜色、角度、文档照片、传真、打印、手写 (仅限英语) 和混合模式。 文本用有关行、单词、边界框、置信度分数和样式的信息进行提取 (手写或其他) 。 所有文本信息都包含在 `"readResults"` JSON 输出的部分中。 

### <a name="tables"></a>表

布局从文档中提取表 (PDF、TIFF) 和图像 (jpg、png、bmp) 。 可以扫描、拍摄或数字化文档。 表可以是包含合并单元或列的复杂表，带有或不带边框以及奇数角。 提取的表包括列数和行数、行跨度和列跨度。 每个单元格都用其边界框进行提取，并引用部分中提取的文本 `"readResults"` 。 表信息位于 `"pageResults"` JSON 输出的部分。 

![表示例](./media/tables-example.jpg)

### <a name="selection-marks"></a>选择标记

布局还从文档中提取选择标记。 提取的选择标记包括边界框、置信度和状态 (选中/未选择) 。 在 JSON 输出的部分提取选择标记信息 `"readResults"` 。 

## <a name="next-steps"></a>后续步骤

- 使用[窗体识别器示例 UI](https://fott-preview.azurewebsites.net/)尝试您自己的布局提取
- 完成 [表单识别器客户端库快速入门](quickstarts/client-library.md) ，开始使用所选的语言提取布局。
- 或者，按照 [提取布局数据](./QuickStarts/python-layout.md) 快速入门中的步骤，使用 Python 和 REST API 来实现布局数据提取。

## <a name="see-also"></a>请参阅

* [什么是表单识别器？](./overview.md)
* [REST API 参考文档](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeLayoutAsync)