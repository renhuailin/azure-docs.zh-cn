---
title: 参考：适用于 JavaScript 的表单识别器客户端库 3.1.1
description: 使用适用于 JavaScript 的表单识别器客户端库创建一个表单处理应用，该应用从自定义文档中提取键值对和表数据。
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 05/25/2021
ms.author: lajanuar
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: cec68dd2fe938cea22bbc9a34c9227f2988307b5
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128641727"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->

[参考文档](../../index.yml) | [库源代码](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/formrecognizer/ai-form-recognizer/) | [包 (npm)](https://www.npmjs.com/package/@azure/ai-form-recognizer) | [示例](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services)
* 最新版本的 [Node.js](https://nodejs.org/)
* 包含一组训练数据的 Azure 存储 Blob。 有关整理训练数据集的提示和选项，请参阅[为自定义模型生成训练数据集](../../build-training-data-set.md)。 对于本快速入门，可以使用[示例数据集](https://go.microsoft.com/fwlink/?linkid=2090451)（下载并提取 sample_data.zip）的“训练”文件夹下的文件。
* 拥有 Azure 订阅后，在 Azure 门户中<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="创建表单识别器资源"  target="_blank">创建表单识别器资源 </a>，获取密钥和终结点。 部署后，选择”转到资源”。
  * 需要从创建的资源获取密钥和终结点，以便将应用程序连接到表单识别器 API。 你稍后会在快速入门中将密钥和终结点粘贴到下方的代码中。
  * 可以使用免费定价层 (`F0`) 试用该服务，然后再升级到付费层进行生产。

## <a name="setting-up"></a>设置

### <a name="create-a-new-nodejs-application"></a>创建新的 Node.js 应用程序

在控制台窗口（例如 cmd、PowerShell 或 Bash）中，为应用创建一个新目录并导航到该目录。

```console
mkdir myapp && cd myapp
```

运行 `npm init` 命令以使用 `package.json` 文件创建一个 node 应用程序。

```console
npm init
```

> [!NOTE]
> 表单识别器 3.0.0 SDK 面向 API v2.0

### <a name="install-the-client-library"></a>安装客户端库

安装 `ai-form-recognizer` NPM 包：

```console
npm install @azure/ai-form-recognizer
```

应用的 `package.json` 文件将使用依赖项进行更新。

创建一个名为 `index.js` 的文件，将其打开，并导入以下库：

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_imports)]

为资源的 Azure 终结点和密钥创建变量。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_creds)]

> [!IMPORTANT]
> 转到 Azure 门户。 如果在“先决条件”部分中创建的表单识别器资源已成功部署，请单击“后续步骤”下的“转到资源”按钮  。 在资源的“密钥和终结点”页的“资源管理”下可以找到密钥和终结点 。
>
> 完成后，请记住将密钥从代码中删除，并且永远不要公开发布该密钥。 对于生产环境，请使用安全方法来存储和访问凭据。 有关详细信息，请参阅认知服务[安全性](../../../../cognitive-services/cognitive-services-security.md)文章。

## <a name="object-model"></a>对象模型

使用表单识别器，可以创建两种不同的客户端类型。 第一种是 `FormRecognizerClient`，用于查询服务以识别表单域和内容。 第二种是 `FormTrainingClient`，用于创建和管理可用于改进识别的自定义模型。

### <a name="formrecognizerclient"></a>FormRecognizerClient

`FormRecognizerClient` 提供操作以实现以下目的：

* 使用为了分析自定义表单而训练的自定义模型，来识别表单字段和内容。 这些值在 `RecognizedForm` 对象的集合中返回。
* 无需训练模型即可识别表单内容，包括表格、行和单词。 表单内容在 `FormPage` 对象的集合中返回。
* 使用表单识别器服务上预先训练的模型，识别美国回执、名片、发票和标识文件中的常见字段。

### <a name="formtrainingclient"></a>FormTrainingClient

`FormTrainingClient` 提供操作以实现以下目的：

* 训练自定义模型，以分析在自定义表单中找到的所有字段和值。 将会返回一个 `CustomFormModel`，它指示模型将分析的表单类型，以及将为每种表单类型提取的字段。 有关更多详细信息，请参阅[关于无标签模型训练的服务文档](#train-a-model-without-labels)。
* 训练自定义模型，以分析通过标记自定义表单指定的特定字段和值。 返回一个 `CustomFormModel`，它指示模型将提取的字段以，及每个字段的估计准确度。 有关将标签应用于定型数据集的更详细说明，请参阅[关于带标签的模型训练的服务文档](#train-a-model-with-labels)。
* 管理在帐户中创建的模型。
* 将自定义模型从一个表单识别器资源复制到另一个资源。

> [!NOTE]
> 还可以使用图形用户界面（例如[表单识别器标记工具](../../label-tool.md)）来训练模型。

## <a name="code-examples"></a>代码示例

这些代码片段演示如何使用适用于 JavaScript 的表单识别器客户端库执行以下任务：

* [对客户端进行身份验证](#authenticate-the-client)
* [分析布局](#analyze-layout)
* [分析回执](#analyze-receipts)
* [训练自定义模型](#train-a-custom-model)
* [使用自定义模型分析表单](#analyze-forms-with-a-custom-model)
* [管理自定义模型](#manage-your-custom-models)

## <a name="authenticate-the-client"></a>验证客户端

使用定义的订阅变量对客户端对象进行身份验证。 你将使用 `AzureKeyCredential` 对象，因此，如果需要，你可以更新 API 密钥而无需创建新的客户端对象。 你还将创建训练客户端对象。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_auth)]

## <a name="get-assets-for-testing"></a>获取用于测试的资产

还需要为训练和测试数据添加对 URL 的引用。

* [!INCLUDE [get SAS URL](../../includes/sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="SAS URL 检索":::
* 使用以下示例中包含的示例和回执图像（也可以在 [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/assets) 上获得），或使用上述步骤来获取 blob 存储中单个文档的 SAS URL。

## <a name="analyze-layout"></a>分析布局

可以使用表单识别器分析文档中的表格、线条和单词，而无需训练模型。 有关布局提取的详细信息，请参阅[布局概念指南](../../concept-layout.md)。 若要分析位于给定 URI 的文件的内容，请使用 `beginRecognizeContentFromUrl` 方法。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_getcontent)]

> [!TIP]
> 还可从本地文件中获取内容。 请参阅 [FormRecognizerClient](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient) 方法，例如 beginRecognizeContent。 或者，请参阅 [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) 上的示例代码，了解涉及本地图像的方案。

### <a name="output"></a>输出

```console
Page 1: width 8.5 and height 11 with unit inch
cell [0,0] has text Invoice Number
cell [0,1] has text Invoice Date
cell [0,2] has text Invoice Due Date
cell [0,3] has text Charges
cell [0,5] has text VAT ID
cell [1,0] has text 34278587
cell [1,1] has text 6/18/2017
cell [1,2] has text 6/24/2017
cell [1,3] has text $56,651.49
cell [1,5] has text PT
```

## <a name="analyze-receipts"></a>分析回执

本部分演示如何使用预先训练的收据模型分析和提取美国收据中的常见字段。 有关收据分析的详细信息，请参阅[收据概念指南](../../concept-receipts.md)。

若要分析位于某个 URI 的收据，请使用 `beginRecognizeReceiptsFromUrl` 方法。 下面的代码处理给定 URI 的回执，并将主字段和值输出到控制台。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_receipts)]

> [!TIP]
> 你还可以分析本地收据图像。 请参阅 [FormRecognizerClient](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient) 方法，例如 beginRecognizeReceipts。 或者，请参阅 [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) 上的示例代码，了解涉及本地图像的方案。

### <a name="output"></a>输出

```console
status: notStarted
status: running
status: succeeded
First receipt:
  Receipt Type: 'Itemized', with confidence of 0.659
  Merchant Name: 'Contoso Contoso', with confidence of 0.516
  Transaction Date: 'Sun Jun 09 2019 17:00:00 GMT-0700 (Pacific Daylight Time)', with confidence of 0.985
    Item Name: '8GB RAM (Black)', with confidence of 0.916
    Item Name: 'SurfacePen', with confidence of 0.858
  Total: '1203.39', with confidence of 0.774
```

## <a name="train-a-custom-model"></a>训练自定义模型

本部分演示如何使用自己的数据训练模型。 训练的模型可以输出结构化数据，其中包含原始形式的文档中的键/值关系。 训练模型后，可对其进行测试和重新训练，并最终使用它来根据需求提取其他表单中的数据。

> [!NOTE]
> 你还可以使用图形用户界面（例如[表单识别器示例标记工具](../../label-tool.md)）来训练模型。

### <a name="train-a-model-without-labels"></a>不使用标签训练模型

训练自定义模型可分析在自定义表单中找到的所有字段和值，无需手动标记训练文档。

下面的函数使用给定文档集训练模型，并将该模型的状态输出到控制台。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_train)]

### <a name="output"></a>输出

这是使用 [JavaScript SDK](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer) 中提供的训练数据进行训练的模型的输出。 为了提高可读性，此示例输出已截断。

```console
training status: creating
training status: ready
Model ID: 9d893595-1690-4cf2-a4b1-fbac0fb11909
Status: ready
Training started on: Thu Aug 20 2020 20:27:26 GMT-0700 (Pacific Daylight Time)
Training completed on: Thu Aug 20 2020 20:27:37 GMT-0700 (Pacific Daylight Time)
We have recognized the following fields
The model found field 'field-0'
The model found field 'field-1'
The model found field 'field-2'
The model found field 'field-3'
The model found field 'field-4'
The model found field 'field-5'
The model found field 'field-6'
The model found field 'field-7'
...
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors:
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors:
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors:
...
```

### <a name="train-a-model-with-labels"></a>使用标签训练模型

你还可以通过手动标记训练文档来训练自定义模型。 在某些情况下，使用标签训练可改善效果。 若要使用标签训练，你需要在 blob 存储容器中添加特殊标签信息文件 (`\<filename\>.pdf.labels.json`) 和训练文档。 [表单识别器示例标记工具](../../label-tool.md)提供了可帮助你创建这些标签文件的 UI。 获得它们后，可以调用 `beginTraining` 方法，并将 `uselabels` 参数设置为 `true`。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_trainlabels)]

### <a name="output"></a>输出

这是使用 [JavaScript SDK](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) 中提供的训练数据进行训练的模型的输出。 为了提高可读性，此示例输出已截断。

```console
training status: creating
training status: ready
Model ID: 789b1b37-4cc3-4e36-8665-9dde68618072
Status: ready
Training started on: Thu Aug 20 2020 20:30:37 GMT-0700 (Pacific Daylight Time)
Training completed on: Thu Aug 20 2020 20:30:43 GMT-0700 (Pacific Daylight Time)
We have recognized the following fields
The model found field 'CompanyAddress'
The model found field 'CompanyName'
The model found field 'CompanyPhoneNumber'
The model found field 'DatedAs'
...
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: undefined
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: undefined
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: undefined
...
```

## <a name="analyze-forms-with-a-custom-model"></a>使用自定义模型分析表单

本部分演示如何通过你使用自己的表单训练的模型，从自定义表单类型中提取键/值信息和其他内容。

> [!IMPORTANT]
> 若要实现此方案，必须已训练好一个模型，以便可以将其 ID 传递到下面的方法中。 请参阅[训练模型](#train-a-model-without-labels)部分。

你将使用 `beginRecognizeCustomFormsFromUrl` 方法。 返回的值是 `RecognizedForm` 对象的集合：提交的文档中的每一页对应一个对象。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_analyze)]

> [!TIP]
> 还可以分析本地文件。 请参阅 [FormRecognizerClient](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient) 方法，例如 beginRecognizeCustomForms。 或者，请参阅 [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) 上的示例代码，了解涉及本地图像的方案。

### <a name="output"></a>输出

```console
status: notStarted
status: succeeded
Forms:
custom:form, page range: [object Object]
Pages:
Page number: 1
Tables
cell (0,0) Invoice Number
cell (0,1) Invoice Date
cell (0,2) Invoice Due Date
cell (0,3) Charges
cell (0,5) VAT ID
cell (1,0) 34278587
cell (1,1) 6/18/2017
cell (1,2) 6/24/2017
cell (1,3) $56,651.49
cell (1,5) PT
Fields:
Field Merchant has value 'Invoice For:' with a confidence score of 0.116
Field CompanyPhoneNumber has value '$56,651.49' with a confidence score of 0.249
Field VendorName has value 'Charges' with a confidence score of 0.145
Field CompanyAddress has value '1 Redmond way Suite 6000 Redmond, WA' with a confidence score of 0.258
Field CompanyName has value 'PT' with a confidence score of 0.245
Field Website has value '99243' with a confidence score of 0.114
Field DatedAs has value 'undefined' with a confidence score of undefined
Field Email has value 'undefined' with a confidence score of undefined
Field PhoneNumber has value 'undefined' with a confidence score of undefined
Field PurchaseOrderNumber has value 'undefined' with a confidence score of undefined
Field Quantity has value 'undefined' with a confidence score of undefined
Field Signature has value 'undefined' with a confidence score of undefined
Field Subtotal has value 'undefined' with a confidence score of undefined
Field Tax has value 'undefined' with a confidence score of undefined
Field Total has value 'undefined' with a confidence score of undefined
```

## <a name="manage-your-custom-models"></a>管理自定义模型

本部分演示如何管理帐户中存储的自定义模型。 例如，下面的代码在单个函数中执行所有模型管理任务。

### <a name="get-number-of-models"></a>获取模型数量

以下代码块获取目前在帐户中的模型的数量。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_count)]

### <a name="get-list-of-models-in-account"></a>获取帐户中模型的列表

以下代码块可提供帐户中可用模型的完整列表，其中包括有关模型的创建时间及其当前状态的信息。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_list)]

### <a name="output"></a>输出

```console
model 0:
{
  modelId: '453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e',
  status: 'invalid',
  trainingStartedOn: 2020-08-20T22:28:52.000Z,
  trainingCompletedOn: 2020-08-20T22:28:53.000Z
}
model 1:
{
  modelId: '628739de-779c-473d-8214-d35c72d3d4f7',
  status: 'ready',
  trainingStartedOn: 2020-08-20T23:16:51.000Z,
  trainingCompletedOn: 2020-08-20T23:16:59.000Z
}
model 2:
{
  modelId: '789b1b37-4cc3-4e36-8665-9dde68618072',
  status: 'ready',
  trainingStartedOn: 2020-08-21T03:30:37.000Z,
  trainingCompletedOn: 2020-08-21T03:30:43.000Z
}
model 3:
{
  modelId: '9d893595-1690-4cf2-a4b1-fbac0fb11909',
  status: 'ready',
  trainingStartedOn: 2020-08-21T03:27:26.000Z,
  trainingCompletedOn: 2020-08-21T03:27:37.000Z
}
```

### <a name="get-list-of-model-ids-by-page"></a>按页获取模型 ID 列表

此代码块提供了模型和模型 ID 的分页列表。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_listpages)]

### <a name="output"></a>输出

```console
model 1: 453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e
model 2: 628739de-779c-473d-8214-d35c72d3d4f7
model 3: 789b1b37-4cc3-4e36-8665-9dde68618072
```

### <a name="get-model-by-id"></a>按 ID 获取模型

下面的函数获取模型 ID 和匹配的模型对象。 默认情况下不会调用此函数。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_getmodel)]

### <a name="delete-a-model-from-the-resource-account"></a>从资源帐户中删除模型

还可以通过引用模型的 ID 从帐户中删除该模型。 此函数删除具有给定 ID 的模型。 默认情况下不会调用此函数。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_delete)]

### <a name="output"></a>输出

```console
Model with id 789b1b37-4cc3-4e36-8665-9dde68618072 has been deleted
```

## <a name="run-the-application"></a>运行应用程序

在快速入门文件中使用 `node` 命令运行应用程序。

```console
node index.js
```

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除认知服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。

* [Portal](../../../../cognitive-services/cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../../cognitive-services/cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>疑难解答

### <a name="enable-logs"></a>启用日志

使用此库时，可以设置以下环境变量来查看调试日志。

```console
export DEBUG=azure*
```

有关如何启用日志的详细说明，请参阅 [@azure/logger 包文档](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/core/logger)。

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已使用表单识别器 JavaScript 客户端库以不同的方式训练模型和分析表单。 接下来，请了解如何创建更好的训练数据集并生成更准确的模型。

> [!div class="nextstepaction"]
> [生成训练数据集](../../build-training-data-set.md)

## <a name="see-also"></a>请参阅

* [什么是表单识别器？](../../overview.md)
* 有关本指南中使用的示例代码，可访问 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/FormRecognizer/FormRecognizerQuickstart.js)。
