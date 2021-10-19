---
title: 布局 - 表单识别器
titleSuffix: Azure Applied AI Services
description: 使用表单识别器 API 了解与布局分析相关的概念 - 使用和限制。
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: 425a5cb9ab332076ddd0d745bba7b914e5601917
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129707148"
---
# <a name="form-recognizer-layout-model"></a>表单识别器布局模型

Azure 表单识别器的布局 API 可从文档（PDF 和 TIFF）和图像（JPG、PNG 和 BMP）提取文本、表、选择标记和结构信息。 它使客户能够使用各种格式的文档，并返回文档的结构化数据表示形式。 它结合了强大的增强版[光学字符识别 (OCR)](../../cognitive-services/computer-vision/overview-ocr.md) 功能与深度学习模型，能够提取文本、表、选择标记和文档结构。

##### <a name="sample-form-processed-with-form-recognizer-sample-labeling-tool--layout-feature"></a>使用[表单识别器示例标记工具](https://fott-2-1.azurewebsites.net/)布局功能处理的示例表单：

:::image type="content" source="media/layout-demo.gif" alt-text="{alt-text}":::

数据提取功能

| **布局模型**   | 文本提取   | 选定标记   | **表**  |
| --- | --- | --- | --- |
| Layout  | ✓  | ✓  | ✓  |

## <a name="try-form-recognizer-studio-preview"></a>试用表单识别器工作室（预览版）

* 预览版 (v3.0) API 随附了表单识别器工作室。

* 使用我们的表单识别器工作室的布局功能，从表单和文档中提取表格、复选框和文本：

> [!div class="nextstepaction"]
> [试用表单识别器工作室](https://formrecognizer.appliedai.azure.com/studio/layout)

## <a name="try-form-recognizer-sample-labeling-tool"></a>试用表单识别器的示例标签工具

可以通过试用示例标签工具来了解如何提取布局数据。 将需要以下各项：

* Azure 订阅，可以[免费创建一个](https://azure.microsoft.com/free/cognitive-services/)

* Azure 门户中的[表单识别器实例](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer)。 可以使用免费定价层 (`F0`) 来试用该服务。 部署资源后，单击“转到资源”以获取 API 密钥和终结点。

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="屏幕截图：Azure 门户中的密钥和终结点位置。":::

* 表单文档。 可以使用我们的[示例表单文档](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf)。

> [!div class="nextstepaction"]
  > [试用](https://fott-2-1.azurewebsites.net/prebuilts-analyze)

  在表单识别器 UI 中：

  1. 选择“使用‘布局’来获取文本、表和选择标记”。
  1. 从下拉菜单中选择“本地文件”。
  1. 上传文件并选择“运行布局”

  :::image type="content" source="media/try-layout.png" alt-text="屏幕截图：示例标签工具下拉布局文件源选择菜单。":::

## <a name="input-requirements"></a>输入要求

* 为获得最佳结果，请针对每个文档提供一张清晰的照片或高质量的扫描件。
* 支持的文件格式：JPEG、PNG、BMP、TIFF 和 PDF（文本嵌入或扫描式）。 最好是使用文本嵌入式 PDF，这可以避免在提取和定位字符时出错。
* 对于 PDF 和 TIFF，最多可以处理 2000 页（对于免费层订阅，仅处理前两页）。
* 文件大小必须小于 50 MB。
* 图像尺寸必须介于 50 x 50 像素与 10000 x 10000 像素之间。
* PDF 尺寸最大为 17 x 17 英寸，相当于法律专用纸或 A3 纸张尺寸或更小。
* 训练数据的总大小不得超过 500 页。
* 如果 PDF 是密码锁定的文件，则必须先删除锁，然后才能提交它们。
* 对于非监督式学习（没有已标记的数据）：
  * 数据必须包含键和值。
  * 键可以显示在值的上方或左侧，而不能显示在下方或右侧。

> [!NOTE]
> [示例标记工具](https://fott-2-1.azurewebsites.net/)不支持 BMP 文件格式。 这是工具限制，而非表单识别器服务限制。

## <a name="supported-languages-and-locales"></a>支持的语言和区域设置

 表单识别器预览版为布局模型引入了额外的语言支持。 有关支持的手写和打印文本的完整列表，请参阅我们的[语言支持](language-support.md#layout-and-custom-model)。

## <a name="features"></a>功能

### <a name="tables-and-table-headers"></a>表和表头

布局 API 可提取 JSON 输出的 `pageResults` 节中的表。 可以扫描、拍摄或数字化文档。 表可能会比较复杂，有的带合并单元格或列，有的带边框，有的不带边框，还有的带奇角。 提取的表信息包括列数和行数、行高和列高。 系统会输出带有边界框的每个单元格，以及指明单元格是否识别为表头一部分的信息。 模型预测的表头单元格可以跨多行，并且不一定是表中的第一行。 它们也适用于经过旋转的表。 每个表单元格还包含完整文本，这些文本引用 `readResults` 节中的单个字词。

:::image type="content" source="./media/layout-table-headers-example.png" alt-text="布局表头输出":::

### <a name="selection-marks"></a>选定标记

布局 API 还从文档中提取选定标记。 提取的选定标记包含边界框、置信度和状态（选择/取消选择）。 在 JSON 输出的 `readResults` 部分提取选定标记信息。

:::image type="content" source="./media/layout-selection-marks.png" alt-text="布局选择标记输出":::

### <a name="text-lines-and-words"></a>文本行和字词

布局 API 可从具有多种文本角度和颜色的文档和图像提取文本。 它接受文档、传真、打印和/或手写（仅英文）文本和混合模式的照片。 提取文本时会提供有关行、词、边界框、置信度评分和样式（手写体或其他样式）的信息。 所有文本信息都包含在 JSON 输出的 `readResults` 部分中。

:::image type="content" source="./media/layout-text-extraction.png" alt-text="布局文本提取输出":::

### <a name="natural-reading-order-for-text-lines-latin-only"></a>文本行的自然阅读顺序（仅限拉丁语）

你可以使用 `readingOrder` 查询参数指定文本行的输出顺序。 使用 `natural` 可提供更易于阅读的读取顺序输出，如以下示例中所示。 此功能仅支持拉丁语。

:::image type="content" source="./media/layout-reading-order-example.png" alt-text="布局读取顺序示例" lightbox="../../cognitive-services/Computer-vision/Images/ocr-reading-order-example.png":::

### <a name="handwritten-classification-for-text-lines-latin-only"></a>文本行的手写分类（仅限拉丁语）

响应将分类说明每个文本行是否为手写体，同时包括置信度评分。 此功能仅支持拉丁语。 以下示例演示了图像中文本的手写分类。

:::image type="content" source="./media/layout-handwriting-classification.png" alt-text="手写分类示例":::

### <a name="select-page-numbers-or-ranges-for-text-extraction"></a>选择页码或页面范围以进行文本提取

对于较大的多页文档，请使用 `pages` 查询参数指示用于文本提取的特定页码或页面范围。 以下示例演示了一个包含 10 个页面的文档，将按照所有页面 (1-10) 和选定页面 (3-6) 这两种条件提取文本。

:::image type="content" source="./media/layout-select-pages-for-text.png" alt-text="布局选定页面输出":::

## <a name="form-recognizer-preview-v30"></a>表单识别器预览版 v3.0

 表单识别器预览版引入了几个新特性和功能。

* 遵循我们的[表单识别器 v3.0 迁移指南](v3-migration-guide.md)，了解如何在你的应用程序和工作流中使用预览版。

* 探索我们的 [REST API（预览版）](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)以了解有关预览版和新功能的更多信息。

## <a name="next-steps"></a>后续步骤

* 完成表单识别器快速入门：

  > [!div class="nextstepaction"]
  > [表单识别器快速入门](quickstarts/try-sdk-rest-api.md)

* 探索我们的 REST API：

    > [!div class="nextstepaction"]
    > [表单识别器 API v2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeLayoutAsync)