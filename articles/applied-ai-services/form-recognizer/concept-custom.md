---
title: 表单识别器自定义模型和组合模型
titleSuffix: Azure Applied AI Services
description: 了解如何创建、使用和管理表单识别器自定义模型和组合模型。
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: 181d96d2e722d7f9b79c47285bab417bf0337133
ms.sourcegitcommit: af303268d0396c0887a21ec34c9f49106bb0c9c2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2021
ms.locfileid: "129754665"
---
# <a name="form-recognizer-custom-and-composed-models"></a>表单识别器自定义模型和组合模型

表单识别器使用高级机器学习技术检测和提取文档图像的信息，并在结构化 JSON 输出中返回提取的数据。 使用表单识别器，你可以训练独立的自定义模型，或者组合自定义模型来创建组合模型。

* 自定义模型。 通过表单识别器自定义模型可以分析和提取特定于业务的表单和文档中的数据。 自定义模型针对不同的数据和用例进行训练。

* 组合模型。 组合模型的创建方式是采用自定义模型的集合并将它们分配给包含表单类型的单个模型。 将文档提交到组合模型时，服务会执行分类步骤，以确定哪个自定义模型可准确表示呈现用于分析的表单。

:::image type="content" source="media/analyze.png" alt-text="屏幕快照：表单识别器工具分析自定义表单窗口。":::


## <a name="what-is-a-custom-model"></a>什么是自定义模型？

自定义模型是一种经过训练的机器学习程序，可识别不同内容中的表单字段并提取键值对和表数据。 你只需要五个相同类型的表单示例即可开始，自定义模型可以使用或不使用标记数据集进行训练。

## <a name="what-is-a-composed-model"></a>什么是组合模型？

借助组合模型，可以将多个自定义模型分配给使用单个模型 ID 调用的组合模型。 当你训练了多个模型并且要对它们进行分组以分析相似表单类型时，这非常有用。 例如，组合模型可能包含为分析供应、设备和家具采购订单进行训练的自定义模型。 可以使用组合模型为每次分析和提取确定适当的自定义模型，而不是手动尝试选择适当的模型。

## <a name="try-form-recognizer-studio-preview"></a>试用表单识别器工作室（预览版）

* 表单识别器工作室随附了预览版 (v3.0) API。

* 使用表单识别器工作室的自定义表单功能分析特定或唯一类型的表单：

> [!div class="nextstepaction"]
> [试用表单识别器工作室](https://formrecognizer.appliedai.azure.com/studio/customform/projects)

## <a name="try-form-recognizer-sample-labeling-tool"></a>试用表单识别器的示例标签工具

可以通过试用示例标记工具来了解如何从自定义表单提取数据。 需要准备好以下各项：

* 一个 Azure 订阅（可以[免费创建一个](https://azure.microsoft.com/free/cognitive-services/)）

* Azure 门户中的一个[表单识别器实例](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer)。 可以使用免费定价层 (`F0`) 来试用该服务。 部署资源后，单击“转到资源”以获取 API 密钥和终结点。

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="Azure 门户中的密钥和终结点位置屏幕截图。":::

> [!div class="nextstepaction"]
> [试用](https://fott-2-1.azurewebsites.net/projects/create)

在表单识别器 UI 中：

1. 选择“使用‘自定义’通过标签训练模型和获取键值对”。

      :::image type="content" source="media/label-tool/fott-use-custom.png" alt-text="“自定义”选项的 FOTT 工具选择屏幕截图。":::

1. 在下一个窗口中，选择“新建项目”：

    :::image type="content" source="media/label-tool/fott-new-project.png" alt-text="FOTT 工具选择新项目屏幕截图。":::

## <a name="input-requirements"></a>输入要求

* 为获得最佳结果，请针对每个文档提供一张清晰的照片或高质量的扫描件。
* 支持的文件格式：JPEG、PNG、BMP、TIFF 和 PDF（文本嵌入或扫描式）。 最好是使用文本嵌入式 PDF，这可以避免在提取和定位字符时出错。
* 对于 PDF 和 TIFF，最多可处理 2,000 页（对于免费层订阅，仅处理前两页）。
* 文件大小必须小于 50 MB。
* 图像尺寸必须介于 50 x 50 像素与 10000 x 10000 像素之间。
* PDF 尺寸不超过 17 x 17 英寸，相当于法律专用纸或 A3 纸张尺寸或更小尺寸。
* 训练数据的总大小不得超过 500 页。
* 如果 PDF 是密码锁定的文件，则必须先删除锁，然后才能提交文件。
* 对于非监督式学习（不含已标记的数据）：
  * 数据必须包含键和值。
  * 键必须显示在值的上方或左侧，不能显示在下方或右侧。

  > [!TIP]
  > 训练数据
  >
  >* 如果可以，请使用基于文本的 PDF 文档而不是基于图像的文档。 扫描的 PDF 作为图像处理。
  > * 对于填充表单，请使用填充了所有字段的示例。
  > * 在每个字段中使用具有不同值的表格。
  >* 如果表单图像质量较低，请使用较大的数据集（例如 10-15 张图像）。

> [!NOTE]
> [示例标记工具](https://fott-2-1.azurewebsites.net/)不支持 BMP 文件格式。 此为工具限制，而非表单识别器服务限制。

## <a name="supported-languages-and-locales"></a>支持的语言和区域设置

 表单识别器预览版为自定义模型引入了其他语言支持。 有关支持的手写和打印文本的完整列表，请参阅我们的[语言支持](language-support.md#layout-and-custom-model)。

## <a name="form-recognizer-preview-v30"></a>表单识别器预览版 v3.0

 表单识别器 v3.0（预览版）引入了几个新特性和功能：

* 自定义模型 API (v3.0) 支持适用于自定义表单的签名检测。 训练自定义模型时，可以将某些字段指定为签名。  使用自定义模型分析文档时，它将指示是否已检测到签名。

* 请参阅我们的[表单识别器 v3.0 迁移指南](v3-migration-guide.md)，了解如何在应用程序和工作流中使用预览版。

* 请浏览 [REST API（预览版）](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)，以详细了解预览版和新功能。

### <a name="try-signature-detection"></a>尝试签名检测

1. [构建训练数据集](build-training-data-set.md#custom-model-input-requirements)。

1. 导航到[表单识别器示例标记工具](https://fott-preview-private.azurewebsites.net)，然后选择“使用‘自定义’通过标签训练模型和获取键值对”：

    :::image type="content" source="media/label-tool/fott-use-custom.png" alt-text="自定义选项的 FOTT 工具选择屏幕截图。":::

1. 在下一个窗口中，选择“新建项目”：

    :::image type="content" source="media/label-tool/fott-new-project.png" alt-text="FOTT 工具选择新项目屏幕截图。":::

1. 遵循[自定义模型输入要求](build-training-data-set.md#custom-model-input-requirements)。

1. 创建类型为 Signature 的标签。

1. 跟踪文档。  对于签名字段，建议使用区域标记以提高准确性。

1. 标记训练集后，即可训练自定义模型并使用它来分析文档。 签名字段将指定是否检测到签名。

## <a name="next-steps"></a>后续步骤

* 完成表单识别器快速入门：

  > [!div class="nextstepaction"]
  > [表单识别器快速入门](quickstarts/try-sdk-rest-api.md)

* 探索 REST API：

    > [!div class="nextstepaction"]
    > [表单识别器 API v2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm)
