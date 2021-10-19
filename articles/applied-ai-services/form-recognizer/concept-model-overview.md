---
title: 表单识别器模型
titleSuffix: Azure Applied AI Services
description: 有关使用预生成模型进行数据提取和分析的概念
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: f2e2ef59d4c3608065edab4ffd1d1ec55122f2ad
ms.sourcegitcommit: af303268d0396c0887a21ec34c9f49106bb0c9c2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2021
ms.locfileid: "129754608"
---
<!-- markdownlint-disable MD033 -->

# <a name="form-recognizer-models"></a>表单识别器模型

 使用 Azure 表单识别器预生成模型，你可以在应用和流中添加智能表单处理，而无需训练和生成自己的模型。 预生成模型使用与深度学习模型相结合的光学字符识别 (OCR) 来识别和提取特定表单和文档类型共有的预定义文本与数据字段。 表单识别器提取并分析表单和文档数据，然后返回经过整理的结构化 JSON 响应。 表单识别器 v2.1 支持发票、收据、身份文档和名片模型。

## <a name="model-overview"></a>模型概述

| **Model**   | **描述**   |
| --- | --- |
| [常规文档（预览版）](#general-document-preview) | 提取文本、表、结构、键值对和命名实体。  |
| [布局](#layout)  | 从文档中提取文本和布局信息。  |
| [发票](#invoice)  | 从英语版发票中提取关键信息。  |
| [回执](#receipt)  | 从英语版收据中提取关键信息。  |
| [身份文档](#id-document)  | 从美国驾照和国际护照中提取关键信息。  |
| [名片](#business-card)  | 从英语版名片中提取关键信息。  |
| [自定义](#custom) |  从特定于企业的表单和文档中提取数据。 自定义模型针对不同的数据和用例进行训练。 |

### <a name="general-document-preview"></a>常规文档（预览版）

* 通用文档 API 支持大多数表单类型，可以分析文档，并将它所发现的表中的值关联到键和条目。 它非常适合用于从文档中提取通用键值对。 可以使用常规文档模型作为[训练不带标签的自定义模型](compose-custom-models.md#train-without-labels)的替代方法。

* 常规文档是一个已预先训练的模型，可以直接通过 REST API 调用。

* 常规文档模型支持多个实体类别的命名实体识别 (NER)。 NER 是指识别文本中不同实体，并将其分类为预定义的类或类型（例如：人员、位置、事件、产品和组织）的功能。 如果你要验证所提取的值，提取实体可能很有帮助。 实体是从整个内容中提取的。

##### <a name="sample-document-processed-in-the-form-recognizer-studio"></a>在[表单识别器工作室](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=document)中处理的示例文档：

:::image type="content" source="media/general-document-analyze.png" alt-text="屏幕截图：表单识别器工作室中的常规文档分析。":::

> [!div class="nextstepaction"]
> [详细了解：常规文档模型](concept-general-document.md)

### <a name="layout"></a>Layout

布局 API 可以分析和提取文档中的文本、表、标题、选择标记和结构信息，并返回结构化的 JSON 数据表示形式。

##### <a name="sample-form-processed-with-form-recognizer-sample-labeling-tool--layout-feature"></a>使用[表单识别器示例标记工具](https://fott-2-1.azurewebsites.net/)布局功能处理的示例表单：

:::image type="content" source="media/overview-layout.png" alt-text="{alt-text}":::

> [!div class="nextstepaction"]
> [详细了解：布局模型](concept-layout.md)

### <a name="invoice"></a>发票

发票模型可以分析和提取销售发票中的关键信息。 该 API 可分析各种格式的发票，提取客户姓名、帐单邮寄地址、截止日期和未付金额等关键信息，并返回结构化的 JSON 数据表示形式。

##### <a name="sample-invoice-processed-with-form-recognizer-sample-labeling-tool"></a>使用[表单识别器示例标记工具](https://fott-2-1.azurewebsites.net/)处理的示例发票：

:::image type="content" source="./media/overview-invoices.jpg" alt-text="发票示例" lightbox="./media/overview-invoices.jpg":::

> [!div class="nextstepaction"]
> [详细了解：发票模型](concept-invoice.md)

### <a name="receipt"></a>回执

收据模型可以分析和提取销售收据中的关键信息。 该 API 可分析打印和手写的收据，提取商家名称、商家电话号码、交易日期、税金和交易总额等关键信息，并返回结构化的 JSON 数据表示形式。

##### <a name="sample-receipt-processed-with-form-recognizer-sample-labeling-tool"></a>使用[表单识别器示例标记工具](https://fott-2-1.azurewebsites.net/)处理的示例收据：

:::image type="content" source="./media/overview-receipt.jpg" alt-text="收据示例" lightbox="./media/overview-receipt.jpg":::

> [!div class="nextstepaction"]
> [详细了解：收据模型](concept-receipt.md)

### <a name="id-document"></a>身份文档

身份文档模型可分析和提取美国驾照（所有 50 个州和哥伦比亚特区）和国际护照个人资料页（不包括签证和其他旅行文档）中的关键信息。 该 API 可分析身份证件，提取名字、姓氏、地址和出生日期等关键信息，并返回结构化的 JSON 数据表示形式。

##### <a name="sample-us-drivers-license-processed-with-form-recognizer-sample-labeling-tool"></a>使用[表单识别器示例标记工具](https://fott-2-1.azurewebsites.net/)处理的示例美国驾照：

:::image type="content" source="./media/id-example-drivers-license.jpg" alt-text="示例身份卡" lightbox="./media/overview-id.jpg":::

> [!div class="nextstepaction"]
> [详细了解：身份文档模型](concept-id-document.md)

### <a name="business-card"></a>名片

名片模型可分析和提取名片图像中的关键信息。 该 API 可分析打印的名片，提取名字、姓氏、公司名称、电子邮件地址和电话号码等关键信息，并返回结构化的 JSON 数据表示形式。

##### <a name="sample-business-card-processed-with-form-recognizer-sample-labeling-tool"></a>使用[表单识别器示例标记工具](https://fott-2-1.azurewebsites.net/)处理的示例名片：

:::image type="content" source="./media/overview-business-card.jpg" alt-text="名片示例" lightbox="./media/overview-business-card.jpg":::

> [!div class="nextstepaction"]
> [详细了解：名片模型](concept-business-card.md)

### <a name="custom"></a>自定义

自定义模型可以分析和提取特定于企业的表单和文档中的数据。 该 API 是已经过训练的机器学习程序，可识别不同内容中的表单字段并提取键值对和表数据。 你只需要五个相同类型的表单示例即可开始，自定义模型可以使用或不使用标记数据集进行训练。

##### <a name="sample-custom-form-processed-with-form-recognizer-sample-labeling-tool"></a>使用[表单识别器示例标记工具](https://fott-2-1.azurewebsites.net/)处理的示例自定义表单：

:::image type="content" source="media/analyze.png" alt-text="屏幕快照：表单识别器工具分析自定义表单窗口。":::

> [!div class="nextstepaction"]
> [详细了解：自定义模型](concept-custom.md)

## <a name="data-extraction"></a>数据提取

 | **Model**   | 文本提取 |键值对 |选择标记   | **表**   |**实体** |
  | --- | :---: |:---:| :---: | :---: |:---: |
  |常规文档  | ✓  |  ✓ | ✓  | ✓  | ✓  |
  | Layout  | ✓  |   | ✓  | ✓  |   |
  | 发票  | ✓ | ✓  | ✓  | ✓ ||
  |回执  | ✓  |   ✓ |   |  ||
  | 身份文档 | ✓  |   ✓  |   |   ||
  | 名片    | ✓  |   ✓ |   |   ||
  | 自定义             |✓  |  ✓ | ✓  | ✓  | ✓  |

## <a name="input-requirements"></a>输入要求

* 为获得最佳结果，请针对每个文档提供一张清晰的照片或高质量的扫描件。
* 支持的文件格式：JPEG、PNG、BMP、TIFF 和 PDF（文本嵌入或扫描式）。 最好是使用文本嵌入式 PDF，这可以避免在提取和定位字符时出错。
* 对于 PDF 和 TIFF，最多可以处理 2000 页（对于免费层订阅，仅处理前两页）。
* 文件大小必须小于 50 MB。
* 图像尺寸必须介于 50 x 50 像素与 10000 x 10000 像素之间。
* PDF 尺寸最大为 17 x 17 英寸，相当于法律专用纸或 A3 纸张尺寸或更小。
* 训练数据的总大小不超过 500 页。
* 如果 PDF 是密码锁定的文件，则必须先删除锁，然后才能提交它们。
* 对于非监督式学习（没有已标记的数据）：
  * 数据必须包含键和值。
  * 键可以显示在值的上方或左侧，而不能显示在下方或右侧。

> [!NOTE]
> [示例标记工具](https://fott-2-1.azurewebsites.net/)不支持 BMP 文件格式。 这是工具限制，而非表单识别器服务限制。

## <a name="form-recognizer-preview-v30"></a>表单识别器预览版 v3.0

  表单识别器 v3.0（预览版）引入了几个新特性和功能：

* [常规文档（预览版）](concept-general-document.md)模型是一个新的 API，它使用预先训练的模型从表单和文档中提取文本、表、结构、键值对和命名实体。
* [收据（预览版）](concept-receipt.md)模型支持单页酒店收据的处理。
* [身份文档（预览版）](concept-id-document.md)模型支持从美国驾照提取准驾车型、限制和车辆分类信息。
* [自定义模型 API（预览版）](concept-custom.md)支持自定义表单的签名检测。

### <a name="version-migration"></a>版本迁移

请参阅[表单识别器 v3.0 迁移指南](v3-migration-guide.md)，了解如何在应用程序中使用表单识别器 v3.0

## <a name="next-steps"></a>后续步骤

* 了解如何使用[表单识别器示例工具](https://fott-2-1.azurewebsites.net/)来[处理你自己的表单和文档](quickstarts/try-sample-label-tool.md)

* 完成[表单识别器快速入门](quickstarts/try-sdk-rest-api.md)，并使用你选择的开发语言开始创建表单处理应用。
