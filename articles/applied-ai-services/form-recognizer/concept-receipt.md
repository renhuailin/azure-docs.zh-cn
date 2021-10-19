---
title: 表单识别器收据模型
titleSuffix: Azure Applied AI Services
description: 包含使用预构建收据模型进行数据提取和分析的概念
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 10/05/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: 48ebbb4975e57db34fe080db2a61328cef65ca80
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129716268"
---
<!-- markdownlint-disable MD033 -->

# <a name="form-recognizer-receipt-model"></a>表单识别器收据模型

收据模型将强大的光学字符识别 (OCR) 功能与深度学习模型相结合，可从销售收据中分析和提取关键信息。 收据的格式和质量各有不同，包括印刷和手写收据。 该 API 提取关键信息，例如商家名称、商家电话号码、交易日期、税金和交易总额，并返回结构化 JSON 数据表示形式。

##### <a name="sample-receipt-processed-with-form-recognizer-sample-labeling-tool"></a>使用[表单识别器示例标签工具](https://fott-2-1.azurewebsites.net/)处理的样本收据：

:::image type="content" source="./media/overview-receipt.jpg" alt-text="收据示例" lightbox="./media/overview-receipt.jpg":::

## <a name="try-form-recognizer-studio-preview"></a>试用表单识别器工作室（预览版）

* 预览版 (v3.0) API 随附了表单识别器工作室。

* 使用我们的表单识别器工作室的收据功能提取交易时间和日期、商家信息、总金额等：

> [!div class="nextstepaction"]
> [试用表单识别器工作室](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=receipt)

## <a name="try-it-sample-labeling-tool"></a>试用：示例标记工具

可以通过试用示例标签工具来了解如何提取收据数据。 将需要以下各项：

* Azure 订阅，可以[免费创建一个](https://azure.microsoft.com/free/cognitive-services/)

* Azure 门户中的[表单识别器实例](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer)。 可以使用免费定价层 (`F0`) 来试用该服务。 部署资源后，单击“转到资源”以获取 API 密钥和终结点。

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="屏幕截图：Azure 门户中的密钥和终结点位置。":::

* 收据文档。 可以使用我们的[示例收据文档](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/contoso-receipt.png)。

> [!div class="nextstepaction"]
  > [试用](https://fott-2-1.azurewebsites.net/prebuilts-analyze)

在表单识别器 UI 中：

  1. 选择“使用预生成模型来获取数据”。
  1. 从“表单类型”下拉菜单中选择“收据”： 

  :::image type="content" source="media/try-receipt.png" alt-text="屏幕截图：示例标记工具下拉预构建模型选择菜单。":::

## <a name="input-requirements"></a>输入要求

* 为获得最佳结果，请针对每个文档提供一张清晰的照片或高质量的扫描件。
* 支持的文件格式：JPEG、PNG、BMP、TIFF 和 PDF（文本嵌入或扫描式）。 最好是使用文本嵌入式 PDF，这可以避免在提取和定位字符时出错。
* 对于 PDF 和 TIFF，最多可以处理 2000 页（对于免费订阅层级，仅处理前两页）。
* 文件大小必须小于 50 MB。
* 图像尺寸必须介于 50 x 50 像素与 10000 x 10000 像素之间。
* PDF 尺寸最大为 17 x 17 英寸，相当于法律专用纸或 A3 纸张尺寸或更小。
* 训练数据的总大小不得超过 500 页。
* 如果 PDF 是密码锁定的文件，则必须先删除锁，然后才能提交它们。
* 对于非监督式学习（没有已标记的数据）：
  * 数据必须包含键和值。
  * 键可以显示在值的上方或左侧，而不能显示在下方或右侧。

## <a name="supported-languages-and-locales-v21"></a>支持的语言和区域设置 v2.1

>[!NOTE]
 > 无需指定区域设置。 这是一个可选参数。 表单识别器深度学习技术将自动检测图像中的文本语言。

| 型号 | 语言 - 区域设置代码 | 默认 |
|--------|:----------------------|:---------|
|回执| <ul><li>英语（美国）—en-US</li><li> 英语（澳大利亚）—en-AU</li><li>英语（加拿大）—en-CA</li><li>英语（英国）—en-GB</li><li>英语（印度）—en-IN</li></ul>  | 自动检测 |

## <a name="key-value-pair-extraction"></a>键值对提取

|名称| 类型 | 说明 | 标准化输出 |
|:-----|:----|:----|:----|
| ReceiptType | 字符串 | 销售收据类型 |  Itemized |
| MerchantName | 字符串 | 开具收据的商家的名称 |  |
| MerchantPhoneNumber | phoneNumber | 列出的商户电话号码 | +1 xxx xxx xxxx |
| MerchantAddress | 字符串 | 列出的商家地址 |   |
| TransactionDate | date | 开具收据的日期 | yyyy-mm-dd |
| TransactionTime | time | 开具收据的时间 | hh-mm-ss（24 小时）  |
| 总计 | 数字（美元）| 全部交易收据总额 | 两位小数浮点数|
| 小计 | 数字（美元） | 收据小计，通常在纳税前 | 两位小数浮点数|
| 税款 | 数字（美元） | 收据上的税金（通常为销售税或等价税款） | 两位小数浮点数 |
| 提示 | 数字（美元） | 买家提供的小费 | 两位小数浮点数|
| 项 | 对象数组 | 提取的行项，其中包含名称、数量、单价和提取的总价格 | |
| 名称 | 字符串 | 项名称 | |
| 数量 | 数字 | 每个项的数量 | integer |
| 价格 | 数字 | 每个项单位的单独价格| 两位小数浮点数 |
| 总价 | 数字 | 行项总价 | 两位小数浮点数 |

## <a name="form-recognizer-preview-v30"></a>表单识别器 v3.0

 表单识别器预览版引入了几个新特性和功能：

* **收据** 模型支持处理单页酒店收据处理。

    ### <a name="hotel-receipt-key-value-pair-extraction"></a>酒店收据键值对提取

    |名称| 类型 | 说明 | 标准化输出 |
    |:-----|:----|:----|:----|
    | ArrivalDate | date | 到达日期 | yyyy-mm-dd |
    | 货币 | currency | 收据金额的货币单位。 例如，USD、EUR，如果找到多个值，则为 MIXED ||
    | DepartureDate | date | 出发日期 | yyyy-mm-dd |
    | 项目 | 数组 | | |
    | 项.*.类别 | string | 项类别，例如房间、税务等。 |  |
    | 项.*.日期 | date | 项日期 | yyyy-mm-dd |
    | 项.*.说明 | string | 项的说明 | |
    | 项.*.总价 |  数字 | 项的总价 | integer |
    | Locale | string | 收据区域设置，例如 en-US。 | ISO 语言国家/地区代码   |
    | MerchantAddress | 字符串 | 列出的商家地址 | |
    | MerchantAliases | array| | |
    | MerchantAliases.* | string | 商家的备用名称 |  |
    | MerchantName | 字符串 | 开具收据的商家的名称 | |
    | MerchantPhoneNumber | phoneNumber | 列出的商户电话号码 | +1 xxx xxx xxxx|
    | ReceiptType | 字符串 | 收据类型，例如酒店、明细 | |
    | 总计 | 数字 | 全部交易收据总额 | 两位小数浮点数 |

    ### <a name="hotel-receipt-supported-languages-and-locales"></a>酒店收据支持的语言和区域设置

    | 型号 | 语言 - 区域设置代码 | 默认 |
    |--------|:----------------------|:---------|
    |收据（酒店） | <ul><li>英语（美国）—en-US</li></ul>| 英语（美国）—en-US|

* 遵循我们的[表单识别器 v3.0 迁移指南](v3-migration-guide.md)，了解如何在您的应用程序和工作流中使用预览版。

* 探索我们的 [REST API（预览版）](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)以了解有关预览版和新功能的更多信息。

## <a name="next-steps"></a>后续步骤

* 完成表单识别器快速入门：

  > [!div class="nextstepaction"]
  > [表单识别器快速入门](quickstarts/try-sdk-rest-api.md)

* 探索我们的 REST API：

    > [!div class="nextstepaction"]
    > [表单识别器 API v2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeReceiptAsync)
