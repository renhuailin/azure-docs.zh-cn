---
title: 表单识别器名片模型
titleSuffix: Azure Applied AI Services
description: 有关使用预生成的名片模型进行数据提取和分析的概念
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 10/05/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: b9517dcab3f748283c7dace99c0e49796dae7f7f
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129716246"
---
<!-- markdownlint-disable MD033 -->

# <a name="form-recognizer-business-card-model"></a>表单识别器名片模型

## <a name="overview"></a>概述

名片模型将强大的光学字符识别 (OCR) 功能与深度学习模型相结合，可从名片图像中分析和提取关键信息。 该 API 可分析打印的名片，提取名字、姓氏、公司名称、电子邮件地址和电话号码等关键信息，并返回结构化的 JSON 数据表示形式。

使用[表单识别器示例标记工具](https://fott-2-1.azurewebsites.net/)处理的示例名片：

:::image type="content" source="./media/overview-business-card.jpg" alt-text="名片示例" lightbox="./media/overview-business-card.jpg":::

## <a name="try-form-recognizer-studio-preview"></a>试用表单识别器工作室（预览版）

* 表单识别器工作室随附了预览版 (v3.0) API。

* 使用我们的表单识别器工作室名片功能提取姓名、职务、地址、电子邮件、公司名称等信息：

> [!div class="nextstepaction"]
> [试用表单识别器工作室](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=businessCard)

## <a name="try-it-sample-labeling-tool"></a>试用：示例标记工具

可以通过试用我们的示例标记工具来了解如何提取名片数据。 需要准备好以下各项：

* 一个 Azure 订阅 — 可以[免费创建一个](https://azure.microsoft.com/free/cognitive-services/)

* Azure 门户中的一个[表单识别器实例](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer)。 可以使用免费定价层 (`F0`) 来试用该服务。 部署资源后，单击“转到资源”以获取 API 密钥和终结点。

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="屏幕截图：Azure 门户中的密钥和终结点位置。":::

* 一个名片文档。 可以使用我们的[示例名片文档](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/businessCard.png)。

> [!div class="nextstepaction"]
  > [试用](https://fott-2-1.azurewebsites.net/prebuilts-analyze)

  在表单识别器 UI 中：

  1. 选择“使用预生成模型来获取数据”。
  1. 从“表单类型”下拉菜单中选择“名片” ：

  :::image type="content" source="media/try-business-card.png" alt-text="屏幕截图：示例标记工具中的预生成模型选择下拉菜单。":::

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

>[!NOTE]
 > 无需指定区域设置。 这是一个可选参数。 表单识别器深度学习技术将自动检测图像中的文本语言。

| 型号 | 语言 - 区域设置代码 | 默认 |
|--------|:----------------------|:---------|
|名片| <ul><li>英语（美国）- en-US</li><li> 英语（澳大利亚）- en-AU</li><li>英语（加拿大）- en-CA</li><li>英语（英国）- en-GB</li><li>英语（印度）- en-IN</li></ul>  | 自动检测 |

## <a name="key-value-pair-extraction"></a>键值对提取

|名称| 类型 | 说明 |标准化输出 |
|:-----|:----|:----|:----:|
| ContactNames | 对象数组 | 联系人姓名 |  |
| FirstName | 字符串 | 联系人的名字 |  |
| LastName | 字符串 | 联系人的姓氏 |  |
| CompanyNames | 字符串数组 | 公司名称|  |
| Departments | 字符串数组 | 联系人所在的部门或组织 |  |
| JobTitles | 字符串数组 | 列出的联系人职务 |  |
| 电子邮件 | 字符串数组 | 联系人电子邮件地址 |  |
| 网站 | 字符串数组 | 公司网站 |  |
| 地址 | 字符串数组 | 从名片中提取的地址 | |
| MobilePhones | 电话号码数组 | 从名片中提取的移动电话号码 |+1 xxx xxx xxxx |
| 传真 | 电话号码数组 | 名片中的传真号码 | +1 xxx xxx xxxx |
| WorkPhones | 电话号码数组 | 名片中的工作电话号码 | +1 xxx xxx xxxx | 
| OtherPhones     | 电话号码数组 | 名片中的其他电话号码 | +1 xxx xxx xxxx |

## <a name="form-recognizer-preview-v30"></a>表单识别器预览版 v3.0

 表单识别器预览版引入了几个新特性和功能。

* 请参阅[表单识别器 v3.0 迁移指南](v3-migration-guide.md)，了解如何在应用程序和工作流中使用预览版。

* 请浏览 [REST API（预览版）](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)来详细了解预览版和新功能。

## <a name="next-steps"></a>后续步骤

* 完成表单识别器快速入门：

  > [!div class="nextstepaction"]
  > [表单识别器快速入门](quickstarts/try-sdk-rest-api.md)

* 探索 REST API：

    > [!div class="nextstepaction"]
    > [表单识别器 API v2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeBusinessCardAsync)
