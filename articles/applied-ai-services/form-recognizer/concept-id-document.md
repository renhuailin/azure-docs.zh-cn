---
title: 表单识别器 ID 文档模型
titleSuffix: Azure Applied AI Services
description: 有关使用预生成 ID 文档模型进行数据提取和分析的概念
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: 4b73526ad245d9364b0ada997fc014e0dd16df40
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129716251"
---
<!-- markdownlint-disable MD033 -->

# <a name="form-recognizer-id-document-model"></a>表单识别器 ID 文档模型

ID 文档模型将强大的光学字符识别 (OCR) 功能与深度学习模型相结合，以分析和提取美国驾照（所有 50 个州和哥伦比亚特区）以及国际护照个人资料页（不包括 Visa 和其他旅行文件）中的重要信息。 API 分析标识文档；提取姓氏、名字、地址和出生日期等重要信息；以及返回结构化 JSON 数据表示形式。

***使用[表单识别器示例标记工具](https://fott-2-1.azurewebsites.net/)处理的美国驾照示例：***

:::image type="content" source="./media/id-example-drivers-license.jpg" alt-text="示例身份卡" lightbox="./media/overview-id.jpg":::

## <a name="try-form-recognizer-studio-preview"></a>试用表单识别器工作室（预览版）

* 预览版 (v3.0) API 随附了表单识别器工作室。

* 使用表单识别器工作室 ID 文档功能提取名称、计算机可读区域、到期日期：

> [!div class="nextstepaction"]
> [试用表单识别器工作室](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=idDocument)

## <a name="try-it-sample-labeling-tool"></a>试用：示例标记工具

可以通过试用示例标记工具来了解如何提取 ID 文档数据。 将需要以下各项：

* Azure 订阅，可以[免费创建一个](https://azure.microsoft.com/free/cognitive-services/)

* Azure 门户中的[表单识别器实例](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer)。 可以使用免费定价层 (`F0`) 来试用该服务。 部署资源后，单击“转到资源”以获取 API 密钥和终结点。

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="屏幕截图：Azure 门户中的密钥和终结点位置。":::

* ID 文档。 可以使用[示例 ID 文档](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/id-license.jpg)。

> [!div class="nextstepaction"]
  > [试用](https://fott-2-1.azurewebsites.net/prebuilts-analyze)

在表单识别器 UI 中：

  1. 选择“使用预生成模型来获取数据”。
  1. 从“表单类型”下拉菜单中选择“收据”： 

  :::image type="content" source="media/try-id-document.png" alt-text="屏幕截图：示例标记工具下拉预生成模型选择菜单。":::

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
  * 键必须显示在值的上方或左侧，而不能显示在下方或右侧。

> [!NOTE]
> [示例标记工具](https://fott-2-1.azurewebsites.net/)不支持 BMP 文件格式。 这是工具限制，而非表单识别器服务限制。

## <a name="supported-languages-and-locales-v21"></a>支持的语言和区域设置 v2.1

| 型号 | 语言 - 区域设置代码 | 默认 |
|--------|:----------------------|:---------|
|ID 文档| <ul><li>英语（美国）—en-US（驾照）</li><li>国际护照的个人资料页</br> （不包括 Visa 和其他旅行文件）</li></ul></br>|英语（美国）—en-US|

## <a name="key-value-pair-extraction"></a>键值对提取

|名称| 类型 | 说明 | 标准化输出|
|:-----|:----|:----|:----|
|  CountryRegion | countryRegion | 符合 ISO 3166 标准的国家/地区或区域代码 |  |
|  DateOfBirth | date | DOB | yyyy-mm-dd |
|  DateOfExpiration | date | 到期日期 DOB | yyyy-mm-dd |
|  DocumentNumber | 字符串 | 相关护照号、驾照编号等。 |  |
|  FirstName | 字符串 | 提取的名字和中间名（如适用） |  |
|  LastName | 字符串 | 提取的姓氏 |  |
|  国家/地区 | countryRegion | 符合 ISO 3166 标准的国家或地区代码（仅限护照） |  |
|  Sex | 字符串 | 可能的提取值包括“M”、“F”和“X” | |
|  MachineReadableZone | 对象 (object) | 提取的护照 MRZ 包括两行，每行 44 个字符 | “P<USABROOKS<<JENNIFER<<<<<<<<<<<<<<<<<<<<<<< 3400200135USA8001014F1905054710000307<715816” |
|  DocumentType | 字符串 | 文档类型，例如护照、驾照 | “passport” |
|  地址 | 字符串 | 提取的地址（仅限驾照） ||
|  区域 | 字符串 | 提取的区域、州、省/直辖市/自治区等（仅限驾照） |  |

## <a name="form-recognizer-preview-v30"></a>表单识别器预览版 v3.0

 表单识别器预览版引入了几个新特性和功能：

* ID 文档 (v3.0) 模型支持从美国驾照提取认可、限制和车辆分类信息。

    ### <a name="id-document-preview-key-value-pair-extraction"></a>ID 文档预览版键值对提取

    |名称| 类型 | 说明 | 标准化输出|
    |:-----|:----|:----|:----|
    | 🆕 认可 | string | 授予给驾驶者的其他驾驶特权（如摩托车或校车）。  | |
    | 🆕 限制 | string | 已暂停或已吊销驾照适用的受限驾驶特权。| |
    | 🆕VehicleClassification | string | 驾驶者可以驾驶的车辆类型。 ||
    |  CountryRegion | countryRegion | 符合 ISO 3166 标准的国家/地区或区域代码 |  |
    |  DateOfBirth | date | DOB | yyyy-mm-dd |
    |  DateOfExpiration | date | 到期日期 DOB | yyyy-mm-dd |
    |  DocumentNumber | 字符串 | 相关护照号、驾照编号等。 |  |
    |  FirstName | 字符串 | 提取的名字和中间名（如适用） |  |
    |  LastName | 字符串 | 提取的姓氏 |  |
    |  国家/地区 | countryRegion | 符合 ISO 3166 标准的国家或地区代码（仅限护照） |  |
    |  Sex | 字符串 | 可能的提取值包括“M”、“F”和“X” | |
    |  MachineReadableZone | 对象 (object) | 提取的护照 MRZ 包括两行，每行 44 个字符 | “P<USABROOKS<<JENNIFER<<<<<<<<<<<<<<<<<<<<<<< 3400200135USA8001014F1905054710000307<715816” |
    |  DocumentType | 字符串 | 文档类型，例如护照、驾照 | “passport” |
    |  地址 | 字符串 | 提取的地址（仅限驾照） ||
    |  区域 | 字符串 | 提取的区域、州、省/直辖市/自治区等（仅限驾照） |  |

* 请参阅[表单识别器 v3.0 迁移指南](v3-migration-guide.md)，了解如何在应用程序和工作流中使用预览版。

* 请探索 [REST API（预览版）](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)，了解有关预览版和新功能的更多信息。

## <a name="next-steps"></a>后续步骤

* 完成表单识别器快速入门：

  > [!div class="nextstepaction"]
  > [表单识别器快速入门](quickstarts/try-sdk-rest-api.md)

* 探索 REST API：

    > [!div class="nextstepaction"]
    > [表单识别器 API v2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5f74a7738978e467c5fb8707)