---
title: 发票-窗体识别器
titleSuffix: Azure Cognitive Services
description: 使用格式识别器 API （使用和限制）了解与发票分析相关的概念。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: pafarley
ms.openlocfilehash: 5c8af8ddb7a0870de37b73cbe09965ee63c88ba1
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353742"
---
# <a name="form-recognizer-prebuilt-invoice-model"></a>窗体识别器预生成发票型号

Azure 窗体识别器可以使用其预生成的发票模型分析和提取销售发票中的信息。 发票 API 使客户能够以各种格式拍摄发票，并返回结构化数据来自动完成发票处理。 它结合了强大的 [光学字符识别 (OCR) ](../computer-vision/concept-recognizing-text.md) 功能与发票了解深度学习模型，以便从发票中提取重要信息（英语）。 它提取文本、表和信息，如客户、供应商、发票 ID、发票截止日期、总计、发票金额、应付金额、寄送到、帐单到，等等。 预生成的发票 API 公开提供，格式为识别器2.1 版预览版。

## <a name="what-does-the-invoice-service-do"></a>发票服务有什么作用？

发票 API 提取发票中的密钥字段，并将它们以组织的结构化 JSON 响应返回。 发票可以是各种格式和质量，包括手机捕获的图像、扫描的文档和数字 Pdf。 发票 API 将从所有这些发票中提取结构化的输出。 

![Contoso 发票示例](./media/invoice-example.jpg)

## <a name="try-it-out"></a>试试看

若要试用窗体识别器发票服务，请访问联机示例 UI 工具：

> [!div class="nextstepaction"]
> [试用预生成模型](https://fott-preview.azurewebsites.net/)

需要一个 Azure 订阅 (免费) [创建一个](https://azure.microsoft.com/free/cognitive-services) ，并使用 [窗体 Recognzier 资源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) 终结点和密钥来试用表单识别器发票服务。 

![已分析发票示例](./media/analyze-invoice.png)


### <a name="input-requirements"></a>输入要求 

[!INCLUDE [input reqs](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-invoice-operation"></a>分析发票操作

" [分析发票](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeInvoiceAsync) " 操作使用发票的图像或 PDF 作为输入，并提取相关值。 调用返回一个名为的响应标头字段 `Operation-Location` 。 `Operation-Location`该值是一个 URL，其中包含要在下一步中使用的结果 ID。

|响应标头| 结果 URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/invoice/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="the-get-analyze-invoice-result-operation"></a>获取分析发票结果操作

第二步是调用 [Get 分析发票结果](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeInvoiceResult) 操作。 此操作采用 "分析发票" 操作创建的结果 ID 作为输入。 它将返回一个 JSON 响应，该响应包含具有以下可能值的 **状态** 字段。 此操作以迭代方式调用，直到它返回 **成功** 值。 使用3到5秒的间隔，以避免超出每秒 (RPS) 速率的请求数。

|字段| 类型 | 可能值 |
|:-----|:----:|:----|
|状态 | 字符串 | notStarted：分析操作尚未开始。<br /><br />正在运行：分析操作正在进行。<br /><br />失败：分析操作失败。<br /><br />succeeded：分析操作成功。|

当 " **状态** " 字段的值为 " **成功** " 时，JSON 响应将包括发票理解结果、提取的表和可选的文本识别结果（如果请求）。 发票理解结果组织为命名字段值的字典，其中每个值都包含提取的文本、规范化值、边界框、置信度和对应的单词元素。 文本识别结果组织为带有文本、边界框和置信度信息的线条和单词的层次结构。

### <a name="sample-json-output"></a>示例 JSON 输出

对 Get 分析发票结果操作的响应将是已提取所有信息的发票的结构化表示形式。 有关 [示例发票文件](./media/sample-invoice.jpg) 及其结构化输出 [示例发票输出](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-invoice-output.json)，请参阅此处。

JSON 输出包含三个部分： 
* `"readResults"` 节点包含所有已识别的文本和选定标记。 文本按页，然后按行，然后按单个单词进行组织。 
* `"pageResults"` 节点包含通过其边界框、置信度以及对 "readResults" 中的行和词的引用提取的表和单元格。
* `"documentResults"` 节点包含模型发现的特定于发票的值。 您可以在此处找到发票中的所有字段，如发票 ID、发货发件人、帐单发往、客户、总数以及更多内容。

## <a name="example-output"></a>示例输出

发票服务将提取 "文本"、"表" 和 "26 个发票" 字段。 下面是在 JSON 输出响应中从发票中提取的字段 (下面的输出使用此 [示例发票](./media/sample-invoice.jpg))   

|名称| 类型 | 说明 | 文本 | 值 (标准化输出)  |
|:-----|:----|:----|:----| :----|
| CustomerName | 字符串 | 正在开票的客户 | Microsoft Corp |  |
| CustomerId | 字符串 | 客户的引用 ID | CID-12345 |  |
| PurchaseOrder | 字符串 | 采购订单参考编号 | PO-3333 | |  |
| InvoiceId | 字符串 | 此特定发票的 Id (通常为 "发票编号" )  | INV-100 | |  |
| InvoiceDate | date | 发出发票的日期 | 2019 年 11 月 15 日 | 
| DueDate | date | 此发票的日期支付截止日期 | 12/15/2019 | 2019-12-15 | 2019-11-15 |
| VendorName | 字符串 | 已创建此发票的供应商 | CONTOSO 有限公司。 | |
| VendorAddress | 字符串 | 供应商的邮件地址 | 123 456th 圣纽约，纽约州，10001 | |
| VendorAddressRecipient | 字符串 | 与 VendorAddress 关联的名称 | Contoso 总部 | |
| CustomerAddress | 字符串 | 客户的邮件地址 | 123其他 St，Redmond WA，98052 | |
| CustomerAddressRecipient | 字符串 | 与 CustomerAddress 关联的名称 | Microsoft Corp | |
| BillingAddress | 字符串 | 客户的明确帐单地址 | 123帐单 St，Redmond WA，98052 | |
| BillingAddressRecipient | 字符串 | 与 BillingAddress 关联的名称 | Microsoft 服务 | |
| ShippingAddress | 字符串 | 客户的显式寄送地址 | 123发货 St，Redmond WA，98052 | |
| ShippingAddressRecipient | 字符串 | 与 ShippingAddress 关联的名称 | Microsoft 交付 | |
| SubTotal | number | 此发票上标识的小计字段 | $100.00 | 100 | 
| TotalTax | number | 此发票上标识的总税款字段 | $10.00 | 10 |
| InvoiceTotal | number | 与此发票关联的新费用总计 | $110.00 | 110 |
| AmountDue |  number | 供应商导致的总金额 | $610.00 | 610 |
| Serviceaddress.uri | 字符串 | 客户的显式服务地址或属性地址 | 123服务 St，Redmond WA，98052 | |
| ServiceAddressRecipient | 字符串 | 与 Serviceaddress.uri 关联的名称 | Microsoft 服务 | |
| RemittanceAddress | 字符串 | 客户的显式汇款或付款地址 | 123汇款圣纽约，纽约州，10001 |  |
| RemittanceAddressRecipient | 字符串 | 与 RemittanceAddress 关联的名称 | Contoso 计费 |  |
| ServiceStartDate | date | 服务期间的第一个日期 (例如，公用事业帐单服务期间)  | 2019/10/14 | 2019-10-14 |
| ServiceEndDate | date | 服务时段的结束日期 (例如，公用事业帐单服务期间)  | 11/14/2019 | 2019-11-14 |
| PreviousUnpaidBalance | number | 明确的以前未付余额 | $500.00 | 500 |


## <a name="next-steps"></a>后续步骤

- 在 [窗体识别器示例 UI](https://fott-preview.azurewebsites.net/)中尝试自己的发票和示例。
- 完成 [表单识别器客户端库快速入门](quickstarts/client-library.md) ，开始使用所选语言的窗体识别器编写发票处理应用。
- 或者，按照 [提取发票数据](./quickstarts/python-invoices.md) 快速入门，使用 Python 和 REST API 来实现发票数据提取。
## <a name="see-also"></a>请参阅

* [什么是表单识别器？](./overview.md)
* [REST API 参考文档](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeInvoiceAsync)