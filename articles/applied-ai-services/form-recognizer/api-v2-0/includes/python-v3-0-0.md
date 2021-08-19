---
title: 快速入门：适用于 Python 的表单识别器客户端库
description: 使用适用于 Python 的表单识别器客户端库创建一个表单处理应用，该应用从自定义文档中提取键值对和表数据。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 05/25/2021
ms.author: lajanuar
ms.openlocfilehash: f28f6a2e3daffbbeb604c4aa707bf0ffda813316
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122326281"
---
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->

[参考文档](/python/api/azure-ai-formrecognizer) | [库源代码](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/azure/ai/formrecognizer) | [包 (PyPi)](https://pypi.org/project/azure-ai-formrecognizer/) | [示例](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
  * 你的 Python 安装应包含 [pip](https://pip.pypa.io/en/stable/)。 可以通过在命令行上运行 `pip --version` 来检查是否安装了 pip。 通过安装最新版本的 Python 获取 pip。
* 包含一组训练数据的 Azure 存储 Blob。 有关整理训练数据集的提示和选项，请参阅[为自定义模型生成训练数据集](../../build-training-data-set.md)。 对于本快速入门，可以使用[示例数据集](https://go.microsoft.com/fwlink/?linkid=2090451)（下载并提取 sample_data.zip）的“训练”文件夹下的文件。
* 拥有 Azure 订阅后，在 Azure 门户中<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="创建表单识别器资源"  target="_blank">创建表单识别器资源 </a>，获取密钥和终结点。 部署后，单击“转到资源”。
  * 需要从创建的资源获取密钥和终结点，以便将应用程序连接到表单识别器 API。 你稍后会在快速入门中将密钥和终结点粘贴到下方的代码中。
  * 可以使用免费定价层 (`F0`) 试用该服务，然后再升级到付费层进行生产。

## <a name="setting-up"></a>设置

### <a name="install-the-client-library"></a>安装客户端库

安装 Python 后，可以使用以下内容安装最新版本的表单识别器客户端库：

```console
pip install azure-ai-formrecognizer
```

> [!NOTE]
> 表单识别器 3.0.0 SDK 反映了 API v2.0

### <a name="create-a-new-python-application"></a>创建新的 Python 应用程序

在首选编辑器或 IDE 中创建新的 Python 应用程序。 然后导入以下库。

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_imports)]

> [!TIP]
> 如果你想查看包含此快速入门中的代码示例的整个文件，可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/FormRecognizerQuickstart.py) 上找到它。

为资源的 Azure 终结点和密钥创建变量。

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_creds)]

## <a name="object-model"></a>对象模型

使用表单识别器，可以创建两种不同的客户端类型。 第一种是 `form_recognizer_client`，用于查询服务以识别表单域和内容。 第二种是 `form_training_client`，用于创建和管理可用于改进识别的自定义模型。

### <a name="formrecognizerclient"></a>FormRecognizerClient

`form_recognizer_client` 提供操作以实现以下目的：

* 使用为了分析自定义表单而训练的自定义模型，来识别表单字段和内容。
* 无需训练模型即可识别表单内容，包括表格、行和单词。
* 使用表单识别器服务上预先训练的回执模型，识别回执中的常见字段。

### <a name="formtrainingclient"></a>FormTrainingClient

`form_training_client` 提供操作以实现以下目的：

* 训练自定义模型，以分析在自定义表单中找到的所有字段和值。 有关创建定型数据集的更详细说明，请参阅[关于无标签模型训练的服务文档](#train-a-model-without-labels)。
* 训练自定义模型，以分析通过标记自定义表单指定的特定字段和值。 有关将标签应用于定型数据集的更详细说明，请参阅[关于带标签的模型训练的服务文档](#train-a-model-with-labels)。
* 管理在帐户中创建的模型。
* 将自定义模型从一个表单识别器资源复制到另一个资源。

> [!NOTE]
> 还可以使用图形用户界面（例如[表单识别器标记工具](../../label-tool.md)）来训练模型。

## <a name="code-examples"></a>代码示例

这些代码片段演示如何使用适用于 Python 的表单识别器客户端库执行以下任务：
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->

* [对客户端进行身份验证](#authenticate-the-client)
* [分析布局](#analyze-layout)
* [分析回执](#analyze-receipts)
* [训练自定义模型](#train-a-custom-model)
* [使用自定义模型分析表单](#analyze-forms-with-a-custom-model)
* [管理自定义模型](#manage-your-custom-models)

## <a name="authenticate-the-client"></a>验证客户端

此处，你将使用前面定义的订阅变量对两个客户端对象进行身份验证。 你将使用 AzureKeyCredential 对象，因此，如果需要，你可以更新 API 密钥而无需创建新的客户端对象。

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_auth)]

## <a name="get-assets-for-testing"></a>获取用于测试的资产

需要为训练和测试数据添加对 URL 的引用。

* [!INCLUDE [get SAS URL](../../includes/sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="SAS URL 检索":::

* 使用以下示例中包含的示例和回执图像（也可以在 [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms) 上获得），或使用上述步骤来获取 blob 存储中单个文档的 SAS URL。

> [!NOTE]
> 本指南中的代码片段使用通过 URL 访问的远程表单。 如果要改为处理本地表单文档，请参阅[参考文档](/python/api/azure-ai-formrecognizer)中的相关方法和[示例](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples)。

## <a name="analyze-layout"></a>分析布局

可以使用表单识别器分析文档中的表格、线条和单词，而无需训练模型。 有关布局提取的详细信息，请参阅[布局概念指南](../../concept-layout.md)。

若要分析位于给定 URL 的文件的内容，请使用 `begin_recognize_content_from_url` 方法。 返回的值是 `FormPage` 对象的集合：提交的文档中的每一页对应一个对象。 下面的代码循环访问这些对象，并输出提取的键/值对和表数据。

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_getcontent)]

> [!TIP]
> 还可从本地映像中获取内容。 请参阅 [FormRecognizerClient](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient) 方法，例如 `begin_recognize_content`。 或者，请参阅 [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) 上的示例代码，了解涉及本地图像的方案。

### <a name="output"></a>输出

```console
Table found on page 1:
Cell text: Invoice Number
Location: [Point(x=0.5075, y=2.8088), Point(x=1.9061, y=2.8088), Point(x=1.9061, y=3.3219), Point(x=0.5075, y=3.3219)]
Confidence score: 1.0

Cell text: Invoice Date
Location: [Point(x=1.9061, y=2.8088), Point(x=3.3074, y=2.8088), Point(x=3.3074, y=3.3219), Point(x=1.9061, y=3.3219)]
Confidence score: 1.0

Cell text: Invoice Due Date
Location: [Point(x=3.3074, y=2.8088), Point(x=4.7074, y=2.8088), Point(x=4.7074, y=3.3219), Point(x=3.3074, y=3.3219)]
Confidence score: 1.0

Cell text: Charges
Location: [Point(x=4.7074, y=2.8088), Point(x=5.386, y=2.8088), Point(x=5.386, y=3.3219), Point(x=4.7074, y=3.3219)]
Confidence score: 1.0
...

```

## <a name="analyze-receipts"></a>分析回执

本部分演示如何使用预先训练的收据模型分析和提取美国收据中的常见字段。 有关收据分析的详细信息，请参阅[收据概念指南](../../concept-receipts.md)。 若要分析位于某个 URL 的收据，请使用 `begin_recognize_receipts_from_url` 方法。

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_receipts)]

> [!TIP]
> 你还可以分析本地收据图像。 请参阅 [FormRecognizerClient](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient) 方法，例如 `begin_recognize_receipts`。 或者，请参阅 [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) 上的示例代码，了解涉及本地图像的方案。

### <a name="output"></a>输出

```console
ReceiptType: Itemized has confidence 0.659
MerchantName: Contoso Contoso has confidence 0.516
MerchantAddress: 123 Main Street Redmond, WA 98052 has confidence 0.986
MerchantPhoneNumber: None has confidence 0.99
TransactionDate: 2019-06-10 has confidence 0.985
TransactionTime: 13:59:00 has confidence 0.968
Receipt Items:
...Item #1
......Name: 8GB RAM (Black) has confidence 0.916
......TotalPrice: 999.0 has confidence 0.559
...Item #2
......Quantity: None has confidence 0.858
......Name: SurfacePen has confidence 0.858
......TotalPrice: 99.99 has confidence 0.386
Subtotal: 1098.99 has confidence 0.964
Tax: 104.4 has confidence 0.713
Total: 1203.39 has confidence 0.774
```

## <a name="train-a-custom-model"></a>训练自定义模型

本部分演示如何使用自己的数据训练模型。 训练的模型可以输出结构化数据，其中包含原始形式的文档中的键/值关系。 训练模型后，可对其进行测试和重新训练，并最终使用它来根据需求提取其他表单中的数据。

> [!NOTE]
> 你还可以使用图形用户界面（例如[表单识别器示例标记工具](../../label-tool.md)）来训练模型。

### <a name="train-a-model-without-labels"></a>不使用标签训练模型

训练自定义模型可分析在自定义表单中找到的所有字段和值，无需手动标记训练文档。

下面的代码借助训练客户端和 `begin_training` 函数，使用给定文档集训练模型。 返回的 `CustomFormModel` 对象包含该模型可以分析的表单类型以及它可以从每种表单类型中提取的字段的相关信息。 下面的代码块将此信息输出到控制台。

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_train)]

### <a name="output"></a>输出

这是使用 [Python SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/training) 中提供的训练数据进行训练的模型的输出。

```console
Model ID: 628739de-779c-473d-8214-d35c72d3d4f7
Status: ready
Training started on: 2020-08-20 23:16:51+00:00
Training completed on: 2020-08-20 23:16:59+00:00

Recognized fields:
The submodel with form type 'form-0' has recognized the following fields: Additional Notes:, Address:, Company Name:, Company Phone:, Dated As:, Details, Email:, Hero Limited, Name:, Phone:, Purchase Order, Purchase Order #:, Quantity, SUBTOTAL, Seattle, WA 93849 Phone:, Shipped From, Shipped To, TAX, TOTAL, Total, Unit Price, Vendor Name:, Website:
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_4.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_5.jpg
Document status: succeeded
Document page count: 1
Document errors: []
```

### <a name="train-a-model-with-labels"></a>使用标签训练模型

你还可以通过手动标记训练文档来训练自定义模型。 在某些情况下，使用标签训练可改善效果。 返回的 `CustomFormModel` 指示模型可以提取的字段，以及每个字段中的估计准确度。 下面的代码块将此信息输出到控制台。

> [!IMPORTANT]
> 若要使用标签训练，你需要在 blob 存储容器中添加特殊标签信息文件 (`\<filename\>.pdf.labels.json`) 和训练文档。 [表单识别器示例标记工具](../../label-tool.md)提供了可帮助你创建这些标签文件的 UI。 获得它们后，可以调用 `begin_training` 函数，并将 use_training_labels 参数设置为 `true`。

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_trainlabels)]

### <a name="output"></a>输出

这是使用 [Python SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/training) 中提供的训练数据进行训练的模型的输出。

```console
Model ID: ae636292-0b14-4e26-81a7-a0bfcbaf7c91

Status: ready
Training started on: 2020-08-20 23:20:56+00:00
Training completed on: 2020-08-20 23:20:57+00:00

Recognized fields:
The submodel with form type 'form-ae636292-0b14-4e26-81a7-a0bfcbaf7c91' has recognized the following fields: CompanyAddress, CompanyName, CompanyPhoneNumber, DatedAs, Email, Merchant, PhoneNumber, PurchaseOrderNumber, Quantity, Signature, Subtotal, Tax, Total, VendorName, Website
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_4.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_5.jpg
Document status: succeeded
Document page count: 1
Document errors: []
```

## <a name="analyze-forms-with-a-custom-model"></a>使用自定义模型分析表单

本部分演示如何通过你使用自己的表单训练的模型，从自定义表单类型中提取键/值信息和其他内容。

> [!IMPORTANT]
> 若要实现此方案，必须已训练好一个模型，以便可以将其 ID 传递到下面的方法中。 请参阅[训练模型](#train-a-model-without-labels)部分。

你将使用 `begin_recognize_custom_forms_from_url` 方法。 返回的值是 `RecognizedForm` 对象的集合：提交的文档中的每一页对应一个对象。 以下代码将分析结果输出到控制台。 它将输出每个识别的字段和相应的值以及置信度分数。

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_analyze)]

> [!TIP]
> 还可分析本地映像。 请参阅 [FormRecognizerClient](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient) 方法，例如 `begin_recognize_custom_forms`。 或者，请参阅 [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) 上的示例代码，了解涉及本地图像的方案。

### <a name="output"></a>输出

使用前面示例中的模型，它提供了以下输出。

```console
Form type: form-ae636292-0b14-4e26-81a7-a0bfcbaf7c91
Field 'Merchant' has label 'Merchant' with value 'Invoice For:' and a confidence score of 0.116
Field 'CompanyAddress' has label 'CompanyAddress' with value '1 Redmond way Suite 6000 Redmond, WA' and a confidence score of 0.258
Field 'Website' has label 'Website' with value '99243' and a confidence score of 0.114
Field 'VendorName' has label 'VendorName' with value 'Charges' and a confidence score of 0.145
Field 'CompanyPhoneNumber' has label 'CompanyPhoneNumber' with value '$56,651.49' and a confidence score of 0.249
Field 'CompanyName' has label 'CompanyName' with value 'PT' and a confidence score of 0.245
Field 'DatedAs' has label 'DatedAs' with value 'None' and a confidence score of None
Field 'Email' has label 'Email' with value 'None' and a confidence score of None
Field 'PhoneNumber' has label 'PhoneNumber' with value 'None' and a confidence score of None
Field 'PurchaseOrderNumber' has label 'PurchaseOrderNumber' with value 'None' and a confidence score of None
Field 'Quantity' has label 'Quantity' with value 'None' and a confidence score of None
Field 'Signature' has label 'Signature' with value 'None' and a confidence score of None
Field 'Subtotal' has label 'Subtotal' with value 'None' and a confidence score of None
Field 'Tax' has label 'Tax' with value 'None' and a confidence score of None
Field 'Total' has label 'Total' with value 'None' and a confidence score of None
```

## <a name="manage-your-custom-models"></a>管理自定义模型

本部分演示如何管理帐户中存储的自定义模型。

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>检查 FormRecognizer 资源帐户中的模型数

下面的代码块会检查表单识别器帐户中保存的模型数，并将其与帐户限制进行比较。

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_count)]

### <a name="output"></a>输出

```console
Our account has 5 custom models, and we can have at most 5000 custom models
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>列出资源帐户中当前存储的模型

下面的代码块会列出帐户中的当前模型，并将这些模型的详细信息输出到控制台。 它还保存对第一个模型的引用。

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_list)]

### <a name="output"></a>输出

这是测试帐户的示例输出。

```console
We have models with the following ids:
453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e
628739de-779c-473d-8214-d35c72d3d4f7
ae636292-0b14-4e26-81a7-a0bfcbaf7c91
b4b5df77-8538-4ffb-a996-f67158ecd305
c6309148-6b64-4fef-aea0-d39521452699
```

### <a name="get-a-specific-model-using-the-models-id"></a>使用模型 ID 获取特定模型

下面的代码块使用上一节中保存的模型 ID 并使用它来检索有关模型的详细信息。

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_getmodel)]

### <a name="output"></a>输出

这是在前面的示例中创建的自定义模型的示例输出。

```console
Model ID: ae636292-0b14-4e26-81a7-a0bfcbaf7c91
Status: ready
Training started on: 2020-08-20 23:20:56+00:00
Training completed on: 2020-08-20 23:20:57+00:00
```

### <a name="delete-a-model-from-the-resource-account"></a>从资源帐户中删除模型

还可以通过引用模型的 ID 从帐户中删除该模型。 此代码删除在上一节中使用的模型。

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_delete)]

## <a name="run-the-application"></a>运行应用程序

在快速入门文件中使用 `python` 命令运行应用程序。

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除认知服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。

* [Portal](../../../../cognitive-services/cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../../cognitive-services/cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>疑难解答

### <a name="general"></a>常规

表单识别器客户端库将引发在 [Azure Core](https://aka.ms/azsdk-python-azure-core) 中定义的异常。

### <a name="logging"></a>日志记录

此库使用[标准日志记录库](https://docs.python.org/3/library/logging.html)进行日志记录。 有关 HTTP 会话（URL、标头等）的基本信息记录在信息级别。

在客户端上使用 `logging_enable` 关键字参数可启用详细的调试级别日志记录（包括请求/响应正文和未编辑的标头）：

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerLogging.py?name=snippet_logging)]

同样，即使没有为客户端启用详细日志记录，`logging_enable` 也可以为单个操作启用：

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerLogging.py?name=snippet_example)]

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已使用表单识别器 Python 客户端库以不同的方式训练模型和分析表单。 接下来，请了解如何创建更好的训练数据集并生成更准确的模型。

> [!div class="nextstepaction"]
> [生成训练数据集](../../build-training-data-set.md)

* [什么是表单识别器？](../../overview.md)
* 有关本指南中使用的示例代码，可访问 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/FormRecognizerQuickstart.py)。
