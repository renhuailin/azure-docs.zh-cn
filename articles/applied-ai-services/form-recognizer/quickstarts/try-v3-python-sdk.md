---
title: 快速入门：表单识别器 Python SDK v3.0 | 预览版
titleSuffix: Azure Applied AI Services
description: 使用表单识别器 Python 客户端库 SDK v3.0（预览版）处理表单和文档、提取数据和执行分析
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 10/07/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: be4f6f7e2b1bc8e7999ee47467e89ef7625b2acf
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129716618"
---
# <a name="quickstart-form-recognizer-python-client-library-sdks-v30--preview"></a>快速入门：表单识别器 Python 客户端库 SDK v3.0 | 预览版

开始通过 Python 编程语言来使用 Azure 表单识别器。 Azure 表单识别器是一种 [Azure 应用的 AI 服务](../../../applied-ai-services/index.yml)云服务，可让用户使用机器学习技术构建自动化数据处理软件。 可通过 REST API 或 SDK 使用表单识别器。 我们建议你在学习该技术时使用免费服务。 请记住，每月的免费页数限于 500。

>[!NOTE]
> 表单识别器 v3.0 当前为公共预览版。 某些功能可能不受支持或受限。 

[参考文档](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-formrecognizer/latest/azure.ai.formrecognizer.html) | [库源代码](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/formrecognizer/azure-ai-formrecognizer/azure/ai/formrecognizer) | [包 (PyPi)](https://pypi.org/project/azure-ai-formrecognizer/) | [示例](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/formrecognizer/azure-ai-formrecognizer/samples)

Azure 认知服务表单识别器是一种云服务，它使用机器学习从文档中提取和分析表单域、文本和表。 可通过将客户端库 SDK 集成到工作流和应用程序，轻松调用表单识别器模型。

### <a name="form-recognizer-models"></a>表单识别器模型

Python SDK 支持以下模型和功能：

* 🆕通用文档 - 分析和提取文本、表、结构、键值对和命名实体。
* 布局 - 在无需训练模型的情况下分析和提取表、行、字和选择标记（如表单文档中的单选按钮和复选框）。
* 自定义 - 通过使用你自己的表单类型训练的模型，分析和提取自定义表单中的表单域和其他内容。
* 发票 - 使用预先训练的发票模型分析和提取发票中的常见字段。
* 收据 - 使用预先训练的收据模型分析和提取收据中的常见字段。
* ID 文档 - 使用预先训练的 ID 文档模型分析和提取 ID 文档（如护照或驾驶证）中的常见字段。
* 名片 - 使用预先训练的名片模型分析和提取名片中的常见字段。

在本快速入门中，你将使用以下功能来分析和提取表单及文档中的数据和值：

* [通用文档](#try-it-general-document-model)

* [**Layout**](#try-it-layout-model)

* [预生成的发票](#try-it-prebuilt-invoice-model)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services)

* [Python 3.x](https://www.python.org/)

  * 你的 Python 安装应包含 [pip](https://pip.pypa.io/en/stable/)。 可以通过在命令行上运行 `pip --version` 来检查是否安装了 pip。 通过安装最新版本的 Python 获取 pip。

* 认知服务或表单识别器资源。 拥有 Azure 订阅后，在 Azure 门户中创建[单服务](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer)或[多服务](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne)表单识别器资源，以获取密钥和终结点。 可以使用免费定价层 (`F0`) 试用该服务，然后再升级到付费层进行生产。

> [!TIP]
> 如果计划在一个终结点/密钥下访问多个认知服务，请创建认知服务资源。 若只访问表单识别器，则创建表单识别器资源。 请注意，若要使用 [Azure Active Directory 身份验证](/azure/active-directory/authentication/overview-authentication)，则需要单一服务资源。

* 部署资源后，单击“转到资源”。 需要从创建的资源获取密钥和终结点，以便将应用程序连接到表单识别器 API。 稍后在快速入门中将密钥和终结点粘贴到下方的代码中：

  :::image type="content" source="../media/containers/keys-and-endpoint.png" alt-text="屏幕截图：Azure 门户中的密钥和终结点位置。":::

## <a name="set-up"></a>设置

在本地环境中打开一个终端窗口，并使用 pip 安装适用于 Python 的 Azure 表单识别器客户端库：

```console
pip install azure-ai-formrecognizer --pre

```

### <a name="create-a-new-python-application"></a>创建新的 Python 应用程序

在首选编辑器或 IDE 中创建一个名为 form_recognizer_quickstart.py 的新 Python 应用程序。 然后导入以下库：

```python
import os
from azure.core.exceptions import ResourceNotFoundError
from azure.ai.formrecognizer import DocumentAnalysisClient
from azure.core.credentials import AzureKeyCredential
```

### <a name="create-variables-for-your-azure-resource-endpoint-and-key"></a>为 Azure 资源终结点和密钥创建变量

```python
endpoint = "YOUR_FORM_RECOGNIZER_ENDPOINT"
key = "YOUR_FORM_RECOGNIZER_SUBSCRIPTION_KEY"
```

此时，Python 应用程序应包含以下代码行：

```python
import os
from azure.core.exceptions import ResourceNotFoundError
from azure.ai.formrecognizer import DocumentAnalysisClient
from azure.core.credentials import AzureKeyCredential

endpoint = "YOUR_FORM_RECOGNIZER_ENDPOINT"
key = "YOUR_FORM_RECOGNIZER_SUBSCRIPTION_KEY"

```

## <a name="try-it-general-document-model"></a>**试用**：通用文档模型

> [!div class="checklist"]
>
> * 对于此示例，需要一个 URI 上的表单文档文件。 在本快速入门中，可使用[示例表单文档](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf)。
> * 将文件 URI 值添加到文件顶部附近的 `formUrl` 变量中。

### <a name="add-the-following-code-to-your-general-document-application-on-the-line-below-the-key-variable"></a>将以下代码添加到通用文档应用程序的 `key` 变量下方的行中

```python

def analyze_document():
    # sample form document
    formUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf"

    document_analysis_client = DocumentAnalysisClient(
        endpoint=endpoint, credential=AzureKeyCredential(key)
    )

    poller = document_analysis_client.begin_analyze_document(
        "prebuilt-document", formUrl
    )
    result = poller.result()

    for style in result.styles:
        if style.is_handwritten:
            print "Document contains handwritten content: "
            print ",".join([result.content[span.offset:span.offset
                           + span.length] for span in style.spans])
    for page in result.pages:
        print "----Analyzing document from page #{}----".format(page.page_number)
        print "Page has width: {} and height: {}, measured with unit: {}".format(
            page.width, page.height, page.unit
        )

        for (line_idx, line) in enumerate(page.lines):
            print "...Line # {} has text content '{}' within bounding box '{}'".format(
                line_idx, line.content, format_bounding_box(line.bounding_box)
            )

        for word in page.words:
            print "...Word '{}' has a confidence of {}".format(
                word.content, word.confidence
            )

        for selection_mark in page.selection_marks:
            print "...Selection mark is '{}' within bounding box '{}' and has a confidence of {}".format(
                selection_mark.state,
                format_bounding_box(selection_mark.bounding_box),
                selection_mark.confidence,
            )

    for (table_idx, table) in enumerate(result.tables):
        print "Table # {} has {} rows and {} columns".format(
            table_idx, table.row_count, table.column_count
        )

        for region in table.bounding_regions:
            print "Table # {} location on page: {} is {}".format(
                table_idx, region.page_number, format_bounding_box(region.bounding_box)
            )

        for cell in table.cells:
            print "...Cell[{}][{}] has content '{}'".format(
                cell.row_index, cell.column_index, cell.content
            )

            for region in cell.bounding_regions:
                print "...content on page {} is within bounding box '{}'\n".format(
                    region.page_number, format_bounding_box(region.bounding_box)
                )

    print "----Entities found in document----"
    for entity in result.entities:
        print "Entity of category '{}' with sub-category '{}'".format(
            entity.category, entity.sub_category
        )
        print "...has content '{}'".format(entity.content)
        print "...within '{}' bounding regions".format(
            format_bounding_region(entity.bounding_regions)
        )
        print "...with confidence {}\n".format(entity.confidence)

    print "----Key-value pairs found in document----"
    for kv_pair in result.key_value_pairs:
        if kv_pair.key:
            print "Key '{}' found within '{}' bounding regions".format(
                kv_pair.key.content,
                format_bounding_region(kv_pair.key.bounding_regions),
            )

        if kv_pair.value:
            print "Value '{}' found within '{}' bounding regions\n".format(
                kv_pair.value.content,
                format_bounding_region(kv_pair.value.bounding_regions),
            )

    print "----------------------------------------"

    analyze_document()
```

## <a name="try-it-layout-model"></a>**试用**：布局模型

> [!div class="checklist"]
>
> * 对于此示例，需要一个 URI 上的表单文档文件。 在本快速入门中，可使用[示例表单文档](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf)。
> * 将文件 URI 值添加到文件顶部附近的 `formUrl` 变量中。

### <a name="add-the-following-code-to-your-layout-application-on-the-line-below-the-key-variable"></a>将以下代码添加到布局应用程序的 `key` 变量下方的行中

```python

def format_bounding_box(bounding_box):
    if not bounding_box:
        return "N/A"
    return ", ".join(["[{}, {}]".format(p.x, p.y) for p in bounding_box])


def analyze_layout():
    # sample form document
    formUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf"

    document_analysis_client = DocumentAnalysisClient(
        endpoint=endpoint, credential=AzureKeyCredential(key)
    )

    poller = document_analysis_client.begin_analyze_document("prebuilt-layout", formUrl)
    result = poller.result()

    for idx, style in enumerate(result.styles):
        print(
            "Document contains {} content".format(
                "handwritten" if style.is_handwritten else "no handwritten"
            )
        )

    for page in result.pages:
        print("----Analyzing layout from page #{}----".format(page.page_number))
        print(
            "Page has width: {} and height: {}, measured with unit: {}".format(
                page.width, page.height, page.unit
            )
        )

        for line_idx, line in enumerate(page.lines):
            print(
                "...Line # {} has text content '{}' within bounding box '{}'".format(
                    line_idx,
                    line.content,
                    format_bounding_box(line.bounding_box),
                )
            )

        for word in page.words:
            print(
                "...Word '{}' has a confidence of {}".format(
                    word.content, word.confidence
                )
            )

        for selection_mark in page.selection_marks:
            print(
                "...Selection mark is '{}' within bounding box '{}' and has a confidence of {}".format(
                    selection_mark.state,
                    format_bounding_box(selection_mark.bounding_box),
                    selection_mark.confidence,
                )
            )

        for table_idx, table in enumerate(result.tables):
            print(
                "Table # {} has {} rows and {} columns".format(
                    table_idx, table.row_count, table.column_count
                )
            )
            for region in table.bounding_regions:
                print(
                    "Table # {} location on page: {} is {}".format(
                        table_idx,
                        region.page_number,
                        format_bounding_box(region.bounding_box),
                    )
                )
            for cell in table.cells:
                print(
                    "...Cell[{}][{}] has content '{}'".format(
                        cell.row_index,
                        cell.column_index,
                        cell.content,
                    )
                )
                for region in cell.bounding_regions:
                    print(
                        "...content on page {} is within bounding box '{}'".format(
                            region.page_number,
                            format_bounding_box(region.bounding_box),
                        )
                    )

        print("----------------------------------------")

    analyze_layout()

```

## <a name="try-it-prebuilt-invoice-model"></a>**试用**：预生成的发票模型

本示例演示了以发票为例，如何使用预先训练的模型来分析某些类型的通用文档中的数据。 请参阅预先构建的概念页面，获取[发票键值对](../concept-invoice.md#key-value-pair-extraction)的完整列表

> [!div class="checklist"]
>
> * 对于此示例，需要 URI 中的一个发票文档文件。 在本快速入门中，可使用[示例发票文档](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf)。
> * 将文件 URI 值添加到 Main 方法顶部的 `string fileUri` 变量。

### <a name="choose-the-invoice-prebuilt-model-id"></a>选择发票预生成模型 ID

并不局限于发票 - 有多个预生成模型可供选择，每个模型都有其自己的支持字段集。 用于分析操作的模型取决于要分析的文档类型。 下面是表单识别器服务当前支持的预生成模型的模型 ID：

* **prebuilt-invoice**：从发票中提取文本、选择标记、表、键值对和密钥信息。
* **prebuilt-businessCard**：从名片中提取文本和密钥信息。
* **prebuilt-idDocument**：从驾驶证和国际护照中提取文本和密钥信息。
* **prebuilt-receipt**：从发票中提取文本和密钥信息。

### <a name="add-the-following-code-to-your-prebuilt-invoice-application-below-the-document_analysis_client-variable"></a>将以下代码添加到预生成发票应用程序的 `document_analysis_client` 变量下方

```python

def analyze_invoice():
    # sample form document
    formUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf"

    document_analysis_client = DocumentAnalysisClient(endpoint=endpoint, credential=AzureKeyCredential(key))

    poller = document_analysis_client.begin_analyze_document("prebuilt-invoice", formUrl, locale="en-US")
    invoices = poller.result()

    for idx, invoice in enumerate(invoices.documents):
        print("--------Recognizing invoice #{}--------".format(idx + 1))
        vendor_name = invoice.fields.get("VendorName")
        if vendor_name:
            print(
                "Vendor Name: {} has confidence: {}".format(
                    vendor_name.value, vendor_name.confidence
                )
            )
        vendor_address = invoice.fields.get("VendorAddress")
        if vendor_address:
            print(
                "Vendor Address: {} has confidence: {}".format(
                    vendor_address.value, vendor_address.confidence
                )
            )
        vendor_address_recipient = invoice.fields.get("VendorAddressRecipient")
        if vendor_address_recipient:
            print(
                "Vendor Address Recipient: {} has confidence: {}".format(
                    vendor_address_recipient.value, vendor_address_recipient.confidence
                )
            )
        customer_name = invoice.fields.get("CustomerName")
        if customer_name:
            print(
                "Customer Name: {} has confidence: {}".format(
                    customer_name.value, customer_name.confidence
                )
            )
        customer_id = invoice.fields.get("CustomerId")
        if customer_id:
            print(
                "Customer Id: {} has confidence: {}".format(
                    customer_id.value, customer_id.confidence
                )
            )
        customer_address = invoice.fields.get("CustomerAddress")
        if customer_address:
            print(
                "Customer Address: {} has confidence: {}".format(
                    customer_address.value, customer_address.confidence
                )
            )
        customer_address_recipient = invoice.fields.get("CustomerAddressRecipient")
        if customer_address_recipient:
            print(
                "Customer Address Recipient: {} has confidence: {}".format(
                    customer_address_recipient.value,
                    customer_address_recipient.confidence,
                )
            )
        invoice_id = invoice.fields.get("InvoiceId")
        if invoice_id:
            print(
                "Invoice Id: {} has confidence: {}".format(
                    invoice_id.value, invoice_id.confidence
                )
            )
        invoice_date = invoice.fields.get("InvoiceDate")
        if invoice_date:
            print(
                "Invoice Date: {} has confidence: {}".format(
                    invoice_date.value, invoice_date.confidence
                )
            )
        invoice_total = invoice.fields.get("InvoiceTotal")
        if invoice_total:
            print(
                "Invoice Total: {} has confidence: {}".format(
                    invoice_total.value, invoice_total.confidence
                )
            )
        due_date = invoice.fields.get("DueDate")
        if due_date:
            print(
                "Due Date: {} has confidence: {}".format(
                    due_date.value, due_date.confidence
                )
            )
        purchase_order = invoice.fields.get("PurchaseOrder")
        if purchase_order:
            print(
                "Purchase Order: {} has confidence: {}".format(
                    purchase_order.value, purchase_order.confidence
                )
            )
        billing_address = invoice.fields.get("BillingAddress")
        if billing_address:
            print(
                "Billing Address: {} has confidence: {}".format(
                    billing_address.value, billing_address.confidence
                )
            )
        billing_address_recipient = invoice.fields.get("BillingAddressRecipient")
        if billing_address_recipient:
            print(
                "Billing Address Recipient: {} has confidence: {}".format(
                    billing_address_recipient.value,
                    billing_address_recipient.confidence,
                )
            )
        shipping_address = invoice.fields.get("ShippingAddress")
        if shipping_address:
            print(
                "Shipping Address: {} has confidence: {}".format(
                    shipping_address.value, shipping_address.confidence
                )
            )
        shipping_address_recipient = invoice.fields.get("ShippingAddressRecipient")
        if shipping_address_recipient:
            print(
                "Shipping Address Recipient: {} has confidence: {}".format(
                    shipping_address_recipient.value,
                    shipping_address_recipient.confidence,
                )
            )
        print("Invoice items:")
        for idx, item in enumerate(invoice.fields.get("Items").value):
            print("...Item #{}".format(idx + 1))
            item_description = item.value.get("Description")
            if item_description:
                print(
                    "......Description: {} has confidence: {}".format(
                        item_description.value, item_description.confidence
                    )
                )
            item_quantity = item.value.get("Quantity")
            if item_quantity:
                print(
                    "......Quantity: {} has confidence: {}".format(
                        item_quantity.value, item_quantity.confidence
                    )
                )
            unit = item.value.get("Unit")
            if unit:
                print(
                    "......Unit: {} has confidence: {}".format(unit.value, unit.confidence)
                )
            unit_price = item.value.get("UnitPrice")
            if unit_price:
                print(
                    "......Unit Price: {} has confidence: {}".format(
                        unit_price.value, unit_price.confidence
                    )
                )
            product_code = item.value.get("ProductCode")
            if product_code:
                print(
                    "......Product Code: {} has confidence: {}".format(
                        product_code.value, product_code.confidence
                    )
                )
            item_date = item.value.get("Date")
            if item_date:
                print(
                    "......Date: {} has confidence: {}".format(
                        item_date.value, item_date.confidence
                    )
                )
            tax = item.value.get("Tax")
            if tax:
                print("......Tax: {} has confidence: {}".format(tax.value, tax.confidence))
            amount = item.value.get("Amount")
            if amount:
                print(
                    "......Amount: {} has confidence: {}".format(
                        amount.value, amount.confidence
                    )
                )
        subtotal = invoice.fields.get("SubTotal")
        if subtotal:
            print(
                "Subtotal: {} has confidence: {}".format(
                    subtotal.value, subtotal.confidence
                )
            )
        total_tax = invoice.fields.get("TotalTax")
        if total_tax:
            print(
                "Total Tax: {} has confidence: {}".format(
                    total_tax.value, total_tax.confidence
                )
            )
        previous_unpaid_balance = invoice.fields.get("PreviousUnpaidBalance")
        if previous_unpaid_balance:
            print(
                "Previous Unpaid Balance: {} has confidence: {}".format(
                    previous_unpaid_balance.value, previous_unpaid_balance.confidence
                )
            )
        amount_due = invoice.fields.get("AmountDue")
        if amount_due:
            print(
                "Amount Due: {} has confidence: {}".format(
                    amount_due.value, amount_due.confidence
                )
            )
        service_start_date = invoice.fields.get("ServiceStartDate")
        if service_start_date:
            print(
                "Service Start Date: {} has confidence: {}".format(
                    service_start_date.value, service_start_date.confidence
                )
            )
        service_end_date = invoice.fields.get("ServiceEndDate")
        if service_end_date:
            print(
                "Service End Date: {} has confidence: {}".format(
                    service_end_date.value, service_end_date.confidence
                )
            )
        service_address = invoice.fields.get("ServiceAddress")
        if service_address:
            print(
                "Service Address: {} has confidence: {}".format(
                    service_address.value, service_address.confidence
                )
            )
        service_address_recipient = invoice.fields.get("ServiceAddressRecipient")
        if service_address_recipient:
            print(
                "Service Address Recipient: {} has confidence: {}".format(
                    service_address_recipient.value,
                    service_address_recipient.confidence,
                )
            )
        remittance_address = invoice.fields.get("RemittanceAddress")
        if remittance_address:
            print(
                "Remittance Address: {} has confidence: {}".format(
                    remittance_address.value, remittance_address.confidence
                )
            )
        remittance_address_recipient = invoice.fields.get("RemittanceAddressRecipient")
        if remittance_address_recipient:
            print(
                "Remittance Address Recipient: {} has confidence: {}".format(
                    remittance_address_recipient.value,
                    remittance_address_recipient.confidence,
                )
            )

    analyze_invoice()
```

## <a name="run-your-application"></a>运行应用程序

1. 导航到 form_recognizer_quickstart.py 文件所在的文件夹。

1. 在终端中键入以下内容：

```python
python form_recognizer_quickstart.py
```

恭喜！ 在本快速入门中，你使用了表单识别器 Python SDK 以不同方式分析各种表单。 接下来，请浏览参考文档来详细了解表单识别器 v3.0 API。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [REST API v3.0 参考文档](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)
