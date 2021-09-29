---
title: 发票 - 表单识别器
titleSuffix: Azure Applied AI Services
description: 使用表单识别器 API 了解与发票分析相关的概念 - 使用和限制。
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/09/2021
ms.author: lajanuar
ms.openlocfilehash: 92f20d9275aad08a47e20202e1daab621d1a4578
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128646980"
---
# <a name="form-recognizer-prebuilt-invoice-model"></a>表单识别器预生成的发票模型

Azure 表单识别器可以使用其预生成的发票模型分析和提取销售发票中的信息。 发票 API 使客户能够获取各种格式的发票，并返回结构化数据来自动完成发票处理。 它结合了强大的[光学字符识别 (OCR)](../../cognitive-services/computer-vision/overview-ocr.md) 功能与发票理解深度学习模型，可从英语发票中提取重要信息。 可提取文本、表和信息，如客户、供应商、发票 ID、发票到期日期、总计、发票应付金额、税金、送货地址、帐单地址、行项目等等。 预生成发票 API 在表单识别器 v2.1 中公开提供。

## <a name="what-does-the-invoice-service-do"></a>发票服务有哪些功能？

发票 API 从发票中提取关键字段和行项目，并在组织的结构化 JSON 响应中返回它们。 发票可以采用各种格式和质量，包括手机拍摄的图像、扫描文档和数字 PDF。 发票 API 将从所有这些发票中提取结构化输出。

![Contoso 发票示例](./media/invoice-example-new.jpg)

## <a name="try-it&quot;></a>试用

若要试用表单识别器发票服务，请转到联机 UI 工具示例：

> [!div class=&quot;nextstepaction&quot;]
> [试用发票模型](https://aka.ms/fott-2.1-ga &quot;首先使用预生成模型从发票中提取数据。")

若要试用表单识别器发票服务，你将需要一个 Azure 订阅（[免费创建一个](https://azure.microsoft.com/free/cognitive-services)）和一个[表单识别器资源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer)终结点和密钥。

:::image type="content" source="media/analyze-invoice-new.png" alt-text="已分析的发票示例" lightbox="media/analyze-invoice-new.png":::

### <a name="input-requirements"></a>输入要求

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="supported-locales"></a>支持的区域设置

预生成的 Invoice v2.1 支持采用 en-us 区域设置的发票 。

## <a name="analyze-invoice"></a>分析发票

[分析发票](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9843c2794cbb1a96291)操作将发票的图像或 PDF 作为输入，并提取相关值。 该调用返回一个名为 `Operation-Location` 的响应标头字段。 `Operation-Location` 值是一个 URL，其中包含要在下一步骤中使用的结果 ID。

|响应标头| 结果 URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1/prebuilt/invoice/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="get-analyze-invoice-result"></a>获取分析发票结果

第二步是调用 [Get Analyze Invoice Result](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9acb78c40a2533aee83) 操作。 此操作采用分析发票操作创建的结果 ID 作为输入。 此操作返回一个 JSON 响应，其中包含具有以下可能值的 **status** 字段。 可以不断地以迭代方式调用此操作，直到它返回 **succeeded** 值为止。 使用 3 到 5 秒的间隔可以避免超过每秒请求数 (RPS) 的速率限制。

|字段| 类型 | 可能值 |
|:-----|:----:|:----|
|status | 字符串 | notStarted：分析操作尚未启动。<br /><br />running：分析操作正在进行。<br /><br />failed：分析操作失败。<br /><br />succeeded：分析操作成功。|

当“状态”字段的值为“succeeded”时，JSON 响应将包括发票理解结果、提取的表和可选的文本识别结果（如果请求）。 发票理解结果组织成一个命名字段值字典，其中每个值都包含提取的文本、规范化值、边界框、置信度和相应的词元素。 它还包括提取的行项目，其中每个行项目都包含数量、说明、单价、数量等。文本识别结果组织成由行和词组成的层次结构，包含文本、边界框和置信度信息。

### <a name="sample-json-output"></a>示例 JSON 输出

对 Get Analyze Invoice Result 操作的响应将是已提取所有信息的发票的结构化表示形式。
有关[示例发票文件](media/sample-invoice.jpg)及其结构化输出[示例发票输出](media/invoice-example-new.jpg)，请参阅此处。

JSON 输出分为三个部分：
* `"readResults"` 节点包含所有已识别的文本和选定标记。 文本按页，然后按行，然后按单个单词进行组织。
* `"pageResults"` 节点包含提取的表和单元格及其边界框、置信度以及对“readResults”中的行和词的引用。
* `"documentResults"` 节点包含模型发现的特定于发票的值和行项目。 可以在此处找到发票中的所有字段，如发票 ID、送货地址、帐单地址、客户、总计、行项目等等。

## <a name="example-output"></a>示例输出

发票服务将提取文本、表和 26 个发票字段。 下面是在 JSON 输出响应中从发票中提取的字段（下面的输出使用此[示例发票](media/sample-invoice.jpg)）。

### <a name="key-value-pairs"></a>键值对 

|名称| 类型 | 说明 | 文本 | 值（标准化输出） |
|:-----|:----|:----|:----| :----|
| CustomerName | 字符串 | 正在开票的客户 | Microsoft Corp |  |
| CustomerId | 字符串 | 客户的引用 ID | CID-12345 |  |
| PurchaseOrder | 字符串 | 采购订单参考编号 | PO-3333 | |
| InvoiceId | 字符串 | 此特定发票的 ID（通常为“发票编号”） | INV-100 | |
| InvoiceDate | date | 开具发票的日期 | 2019 年 11 月 15 日 | 2019-11-15 |
| DueDate | date | 此发票的支付截止日期 | 2019/12/15 | 2019-12-15 |
| VendorName | 字符串 | 创建此发票的供应商 | CONTOSO LTD. | |
| VendorAddress | 字符串 | 供应商的邮件地址 | 123 456th St New York, NY, 10001 | |
| VendorAddressRecipient | 字符串 | 与 VendorAddress 关联的名称 | Contoso 总部 | |
| CustomerAddress | 字符串 | 客户的邮件地址 | 123 Other St, Redmond WA, 98052 | |
| CustomerAddressRecipient | 字符串 | 与 CustomerAddress 关联的名称 | Microsoft Corp | |
| BillingAddress | 字符串 | 客户的具体帐单邮寄地址 | 123 Bill St, Redmond WA, 98052 | |
| BillingAddressRecipient | 字符串 | 与 BillingAddress 关联的名称 | Microsoft 服务 | |
| ShippingAddress | 字符串 | 客户的具体送货地址 | 123 Ship St, Redmond WA, 98052 | |
| ShippingAddressRecipient | 字符串 | 与 ShippingAddress 关联的名称 | Microsoft 交付 | |
| SubTotal | 数字 | 此发票上标识的小计字段 | 100.00 美元 | 100 |
| TotalTax | 数字 | 此发票上标识的总税款字段 | $10.00 | 10 |
| InvoiceTotal | 数字 | 与此发票关联的新费用总计 | 110.00 美元 | 110 |
| AmountDue |  数字 | 应支付给供应商的总金额 | 610.00 美元 | 610 |
| ServiceAddress | 字符串 | 客户的具体服务地址或房产地址 | 123 Service St, Redmond WA, 98052 | |
| ServiceAddressRecipient | 字符串 | 与 ServiceAddress 关联的名称 | Microsoft 服务 | |
| RemittanceAddress | 字符串 | 客户的明确汇款或付款地址 | 123 Remit St New York, NY, 10001 |  |
| RemittanceAddressRecipient | 字符串 | 与 RemittanceAddress 关联的名称 | Contoso 计费 |  |
| ServiceStartDate | date | 服务时段的开始日期（例如，公用事业帐单服务期间） | 2019/10/14 | 2019-10-14 |
| ServiceEndDate | date | 服务时段的结束日期（例如，公用事业帐单服务期间） | 2019/11/14 | 2019-11-14 |
| PreviousUnpaidBalance | 数字 | 先前未付的具体余额 | 500.00 美元 | 500 |

### <a name="line-items"></a>行项目

下面是在 JSON 输出响应中从发票中提取的行项目（下面的输出使用此[示例发票](./media/sample-invoice.jpg)）

|名称| 类型 | 说明 | 文本（行项目 #1） | 值（标准化输出） |
|:-----|:----|:----|:----| :----|
| 项 | 字符串 | 行项目的完整字符串文本行 | 2021/3/4 A123 咨询服务 2 小时 30.00 美元 10% 60.00 美元 | |
| 金额 | 数字 | 行项目的数量 | 60.00 美元 | 100 |
| 说明 | 字符串 | 发票行项目的文本说明 | 咨询服务 | 咨询服务 |
| 数量 | 数字 | 此发票行项目的数量 | 2 | 2 |
| 单价 | 数字 | 此项目一个单位的净价或毛价（具体取决于发票的发票总额设置） | 30.00 美元 | 30 |
| ProductCode | 字符串| 与特定行项目关联的产品代码、产品编号或 SKU | A123 | |
| 计价单位 | 字符串| 行项目单位，如公斤、磅等。 | 小时 | |
| 日期 | date| 对应于每个行项目的日期。 这通常是行项目的发货日期 | 2021/3/4| 2021-03-04 |
| 税款 | 数字 | 与每个行项目关联的税款。 可能的值包括税金、税款 % 和税款 Y/N | 10% | |

提取的发票键值对和行项目位于 JSON 输出的 documentResults 部分。 

## <a name="next-steps"></a>后续步骤

- 在[表单识别器示例 UI](https://aka.ms/fott-2.1-ga) 中尝试你自己的发票和示例。
- 完成[表单识别器快速入门](quickstarts/client-library.md)，开始使用表单识别器以你选择的开发语言编写发票处理应用。

## <a name="see-also"></a>请参阅

* [什么是表单识别器？](./overview.md)
* [REST API 参考文档](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9843c2794cbb1a96291)
