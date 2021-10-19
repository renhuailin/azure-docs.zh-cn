---
title: 表单识别器发票模型
titleSuffix: Azure Applied AI Services
description: 有关使用预生成发票模型进行数据提取和分析的概念
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: 4b660d464a8615886be9b466fd2e9de808ef3bd9
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129716294"
---
<!-- markdownlint-disable MD033 -->

# <a name="form-recognizer-invoice-model"></a>表单识别器发票模型

 发票模型将强大的光学字符识别 (OCR) 功能与深度学习模型相结合，可从销售发票中分析和提取关键字段与行项。  发票可以采用各种格式和质量，包括手机拍摄的图像、扫描文档和数字 PDF。 该 API 可分析发票文本，提取客户姓名、帐单邮寄地址、截止日期和未付金额等关键信息，并返回结构化的 JSON 数据表示形式。

##### <a name="sample-invoice-processed-with-form-recognizer-sample-labeling-tool"></a>使用[表单识别器示例标记工具](https://fott-2-1.azurewebsites.net/)处理的示例发票：

:::image type="content" source="media/overview-invoices.jpg" alt-text="发票示例" lightbox="media/overview-invoices-big.jpg":::

## <a name="try-form-recognizer-studio-preview"></a>试用表单识别器工作室（预览版）

* 表单识别器工作室随附了预览版 (v3.0) API。

* 使用我们的表单识别器工作室发票功能提取客户和供应商详细信息、行项等：

> [!div class="nextstepaction"]
> [试用表单识别器工作室](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=invoice)

## <a name="try-it-sample-labeling-tool"></a>试用：示例标记工具

可以通过试用示例标记工具来了解如何提取发票数据。 需要准备好以下各项：

* 一个 Azure 订阅 — 可以[免费创建一个](https://azure.microsoft.com/free/cognitive-services/)

* Azure 门户中的一个[表单识别器实例](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer)。 可以使用免费定价层 (`F0`) 来试用该服务。 部署资源后，单击“转到资源”以获取 API 密钥和终结点。

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="屏幕截图：Azure 门户中的密钥和终结点位置。":::

* 一个账单文档。 可以使用[示例发票文档](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf)。

> [!div class="nextstepaction"]
  > [试用](https://fott-2-1.azurewebsites.net/prebuilts-analyze)

  在表单识别器 UI 中：

  1. 选择“使用预生成模型来获取数据”。
  1. 从“表单类型”下拉菜单中选择“发票” ：

  :::image type="content" source="media/try-invoice.png" alt-text="屏幕截图：示例标记工具中的预生成模型选择下拉菜单。":::

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

| 型号 | 语言 - 区域设置代码 | 默认 |
|--------|:----------------------|:---------|
|发票| <ul><li>英语（美国）- en-US</li></ul>| 英语（美国）- en-US|

## <a name="key-value-pair-extraction"></a>键值对提取

|名称| 类型 | 说明 | 标准化输出 |
|:-----|:----|:----|:---:|
| CustomerName | 字符串 | 发票客户| |
| CustomerId | 字符串 | 客户参考 ID | |
| PurchaseOrder | 字符串 | 采购订单参考编号 | |
| InvoiceId | 字符串 | 此特定发票的 ID（通常为“发票编号”） | |
| InvoiceDate | date | 开具发票的日期 | yyyy-mm-dd|
| DueDate | date | 此发票的支付截止日期 | yyyy-mm-dd|
| VendorName | 字符串 | 供应商名称 |  |
| VendorAddress | 字符串 |  供应商邮寄地址|  |
| VendorAddressRecipient | 字符串 | 与 VendorAddress 关联的名称 |  |
| CustomerAddress | 字符串 | 客户的邮件地址 | |
| CustomerAddressRecipient | 字符串 | 与 CustomerAddress 关联的名称 | |
| BillingAddress | 字符串 | 客户的具体帐单邮寄地址 |  |
| BillingAddressRecipient | 字符串 | 与 BillingAddress 关联的名称 | |
| ShippingAddress | 字符串 | 客户的具体送货地址 | |
| ShippingAddressRecipient | 字符串 | 与 ShippingAddress 关联的名称 |  |
| SubTotal | 数字 | 此发票上标识的小计字段 | integer |
| TotalTax | 数字 | 此发票上标识的总税款字段 | integer |
| InvoiceTotal | 数字（美元） | 与此发票关联的新费用总计 | integer |
| AmountDue |  数字（美元） | 应支付给供应商的总金额 | integer |
| ServiceAddress | 字符串 | 客户的具体服务地址或房产地址 | |
| ServiceAddressRecipient | 字符串 | 与 ServiceAddress 关联的名称 |  |
| RemittanceAddress | 字符串 | 客户的明确汇款或付款地址 |   |
| RemittanceAddressRecipient | 字符串 | 与 RemittanceAddress 关联的名称 |  |
| ServiceStartDate | date | 服务时段的开始日期（例如，公用事业帐单服务期间） | yyyy-mm-dd |
| ServiceEndDate | date | 服务时段的结束日期（例如，公用事业帐单服务期间） | yyyy-mm-dd|
| PreviousUnpaidBalance | 数字 | 先前未付的具体余额 | integer |

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
    > [表单识别器 API v2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9843c2794cbb1a96291)
