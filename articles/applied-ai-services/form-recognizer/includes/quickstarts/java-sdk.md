---
title: 快速入门：适用于 Java 的表单识别器客户端库
description: 使用表单识别器 Java 客户端库创建一个表单处理应用，该应用从自定义文档中提取键/值对和表数据。
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 05/25/2021
ms.custom: devx-track-java
ms.author: lajanuar
ms.openlocfilehash: fdb92ae88ceec485557cc7bccd7a62a5b873362f
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129715485"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->

> [!IMPORTANT]
>
> * 本快速入门使用 SDK 版本 3.1.1 并针对 API 版本 2.1。 
>
> * 为了简单起见，本文中的代码使用了同步方法和不受保护的凭据存储。

[参考文档](/java/api/overview/azure/ai-formrecognizer-readme) | [库源代码](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src) | [包 (Maven)](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer) | [示例](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services)
* 最新版的 [Java 开发工具包](https://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK)
* [Gradle 生成工具](https://gradle.org/install/)，或其他依赖项管理器。
* 拥有 Azure 订阅后，在 Azure 门户中<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="创建表单识别器资源"  target="_blank">创建表单识别器资源 </a>，获取密钥和终结点。 部署后，单击“转到资源”。
  * 需要从创建的资源获取密钥和终结点，以便将应用程序连接到表单识别器 API。 你稍后会在快速入门中将密钥和终结点粘贴到下方的代码中。
  * 可以使用免费定价层 (`F0`) 试用该服务，然后再升级到付费层进行生产。
* 包含一组训练数据的 Azure 存储 Blob。 有关整理训练数据集的提示和选项，请参阅[为自定义模型生成训练数据集](../../build-training-data-set.md)。 对于本快速入门，可以使用[示例数据集](https://go.microsoft.com/fwlink/?linkid=2090451)（下载并提取 sample_data.zip）的“训练”文件夹下的文件。

## <a name="setting-up"></a>设置

### <a name="create-a-new-gradle-project"></a>创建新的 Gradle 项目

在控制台窗口（例如 cmd、PowerShell 或 Bash）中，为应用创建一个新目录并导航到该目录。

```console
mkdir myapp && cd myapp
```

从工作目录运行 `gradle init` 命令。 此命令将创建 Gradle 的基本生成文件，包括 *build.gradle.kts*，在运行时将使用该文件创建并配置应用程序。

```console
gradle init --type basic
```

当提示你选择一个 **DSL** 时，选择 **Kotlin**。

### <a name="install-the-client-library"></a>安装客户端库

本快速入门使用 Gradle 依赖项管理器。 可以在 [Maven 中央存储库](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer)中找到客户端库以及其他依赖项管理器的信息。

在项目的 build.gradle.kts 文件中，以 `implementation` 语句的形式包含客户端库及所需的插件和设置。

```kotlin
plugins {
    java
    application
}
application {
    mainClass.set("FormRecognizer")
}
repositories {
    mavenCentral()
}
dependencies {
    implementation(group = "com.azure", name = "azure-ai-formrecognizer", version = "3.1.1")
}
```

### <a name="create-a-java-file"></a>创建 Java 文件

在工作目录中运行以下命令：

```console
mkdir -p src/main/java
```

导航到新的文件夹，创建一个名为 FormRecognizer.java 的文件。 在喜好的编辑器或 IDE 中打开该文件并添加以下 `import` 语句：

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_imports)]

> [!TIP]
> 如果你想查看包含此快速入门中的代码示例的整个文件，可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/FormRecognizer/FormRecognizer.java) 上找到它。

在应用程序的 FormRecognizer 类中，为资源的密钥和终结点创建变量。

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_creds)]

> [!IMPORTANT]
> 转到 Azure 门户。 如果在“先决条件”部分中创建的表单识别器资源已成功部署，请单击“后续步骤”下的“转到资源”按钮  。 在资源的“密钥和终结点”页的“资源管理”下可以找到密钥和终结点 。
>
> 完成后，请记住将密钥从代码中删除，并且永远不要公开发布该密钥。 对于生产环境，请使用安全方法来存储和访问凭据。 有关详细信息，请参阅认知服务[安全性](../../../../cognitive-services/cognitive-services-security.md)文章。

在应用程序的 main 方法中，添加对本快速入门中使用的方法的调用。 稍后将对这些调用进行定义。 还需要为训练和测试数据添加对 URL 的引用。

* [!INCLUDE [get SAS URL](../../includes/sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="SAS URL 检索":::
* 若要获取要测试的表单的 URL，可以使用上述步骤获取 blob 存储中单个文档的 SAS URL。 或者获取位于其他位置的文档的 URL。
* 使用上述方法还可获取回执图像的 URL。
<!-- markdownlint-disable MD024 -->

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_mainvars)]

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_maincalls)]

## <a name="object-model"></a>对象模型

使用表单识别器，可以创建两种不同的客户端类型。 第一种是 `FormRecognizerClient`，用于查询服务以识别表单域和内容。 第二种是 `FormTrainingClient`，用于创建和管理可改进识别的自定义模型。

### <a name="formrecognizerclient"></a>FormRecognizerClient

`FormRecognizerClient` 提供操作以实现以下目的：

* 使用为了分析自定义表单而训练的自定义模型，来识别表单字段和内容。  这些值在 `RecognizedForm` 对象的集合中返回。 请参阅示例[分析自定义表单](#analyze-forms-with-a-custom-model)。
* 无需训练模型即可识别表单内容，包括表格、行和单词。  表单内容在 `FormPage` 对象的集合中返回。 请参阅示例[分析布局](#analyze-layout)。
* 使用表单识别器服务上预先训练的模型识别美国收据、名片、发票和 ID 文档中的常见字段。

### <a name="formtrainingclient"></a>FormTrainingClient

`FormTrainingClient` 提供操作以实现以下目的：

* 训练自定义模型，以分析在自定义表单中找到的所有字段和值。  将会返回一个 `CustomFormModel`，它指示模型将分析的表单类型，以及将为每种表单类型提取的字段。
* 训练自定义模型，以分析通过标记自定义表单指定的特定字段和值。  返回一个 `CustomFormModel`，它指示模型将提取的字段以，及每个字段的估计准确度。
* 管理在帐户中创建的模型。
* 将自定义模型从一个表单识别器资源复制到另一个资源。

> [!NOTE]
> 还可以使用图形用户界面（例如[表单识别器标记工具](../../label-tool.md)）来训练模型。

## <a name="authenticate-the-client"></a>验证客户端

在 main 方法顶部添加以下代码。 此处，你将使用前面定义的订阅变量对两个客户端对象进行身份验证。 你将使用 AzureKeyCredential 对象，因此，如果需要，你可以更新 API 密钥而无需创建新的客户端对象。

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_auth)]

## <a name="analyze-layout"></a>分析布局

可以使用表单识别器分析文档中的表格、线条和单词，而无需训练模型。 有关布局提取的详细信息，请参阅[布局概念指南](../../concept-layout.md)。

若要分析位于给定 URL 的文件的内容，请使用 beginRecognizeContentFromUrl 方法。

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_getcontent_call)]

> [!TIP]
> 还可从本地文件中获取内容。 请参阅 [FormRecognizerClient](/java/api/com.azure.ai.formrecognizer.formrecognizerclient) 方法，例如 beginRecognizeContent。 或者，请参阅 [GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) 上的示例代码，了解涉及本地图像的方案。

返回的值是 FormPage 对象的集合：提交的文档中的每一页对应一个对象。 下面的代码循环访问这些对象，并输出提取的键/值对和表数据。

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_getcontent_print)]

### <a name="output"></a>输出

```console
Get form content...
----Recognizing content ----
Has width: 8.500000 and height: 11.000000, measured with unit: inch.
Table has 2 rows and 6 columns.
Cell has text Invoice Number.
Cell has text Invoice Date.
Cell has text Invoice Due Date.
Cell has text Charges.
Cell has text VAT ID.
Cell has text 458176.
Cell has text 3/28/2018.
Cell has text 4/16/2018.
Cell has text $89,024.34.
Cell has text ET.
```

## <a name="analyze-receipts"></a>分析回执

本部分演示如何使用预先训练的收据模型分析和提取美国收据中的常见字段。 有关收据分析的详细信息，请参阅[收据概念指南](../../concept-receipts.md)。

若要分析位于某个 URI 的收据，请使用 beginRecognizeReceiptsFromUrl 方法。

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_receipts_call)]

> [!TIP]
> 你还可以分析本地收据图像。 请参阅 [FormRecognizerClient](/java/api/com.azure.ai.formrecognizer.formrecognizerclient) 方法，例如 beginRecognizeReceipts。 或者，请参阅 [GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) 上的示例代码，了解涉及本地图像的方案。

返回的值是 RecognizedReceipt 对象的集合：提交的文档中的每一页对应一个对象。 接下来的代码块会循环访问回执，并将其详细信息输出到控制台。

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_receipts_print)]

接下来的代码块会循环访问在回执上检测到的各个项，并将其详细信息输出到控制台。

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_receipts_print_items)]

### <a name="output"></a>输出

```console
Analyze receipt...
----------- Recognized Receipt page 0 -----------
Merchant Name: Contoso Contoso, confidence: 0.62
Merchant Address: 123 Main Street Redmond, WA 98052, confidence: 0.99
Transaction Date: 2020-06-10, confidence: 0.90
Receipt Items:
Name: Cappuccino, confidence: 0.96s
Quantity: null, confidence: 0.957s]
Total Price: 2.200000, confidence: 0.95
Name: BACON & EGGS, confidence: 0.94s
Quantity: null, confidence: 0.927s]
Total Price: null, confidence: 0.93
```

## <a name="analyze-business-cards"></a>分析名片

本部分演示如何使用预先训练的模型分析和提取英文名片中的常见字段。 有关名片分析的详细信息，请参阅[名片概念指南](../../concept-business-cards.md)。

若要分析位于某个 URL 的名片，请使用 `beginRecognizeBusinessCardsFromUrl` 方法。

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_bc_call)]

> [!TIP]
> 你还可以分析本地名片图像。 请参阅 [FormRecognizerClient](/java/api/com.azure.ai.formrecognizer.formrecognizerclient) 方法，例如 beginRecognizeBusinessCards。 或者，请参阅 [GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) 上的示例代码，了解涉及本地图像的方案。

返回的值是 RecognizedForm 对象的集合：文档中的每一张卡片都对应一个对象。 以下代码处理给定 URI 的名片，并将主字段和值输出到控制台。

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_bc_print)]

## <a name="analyze-invoices"></a>分析发票

本部分演示如何使用预先训练的模型分析和提取销售发票中的常见字段。 有关发票分析的详细信息，请参阅[发票概念指南](../../concept-invoices.md)。

若要分析位于某个 URL 的发票，请使用 `beginRecognizeInvoicesFromUrl` 方法。

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_invoice_call)]

> [!TIP]
> 你还可以分析本地发票。 请参阅 [FormRecognizerClient](/java/api/com.azure.ai.formrecognizer.formrecognizerclient) 方法，例如 beginRecognizeInvoices。 或者，请参阅 [GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) 上的示例代码，了解涉及本地图像的方案。

返回的值是 RecognizedForm 对象的集合：文档中的每一张发票都对应一个对象。 以下代码处理给定 URI 的发票，并将主字段和值输出到控制台。

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_invoice_print)]

## <a name="analyze-id-documents"></a>分析 ID 文档

本部分演示如何使用表单识别器预生成的 ID 模型，分析和提取政府颁发的标识文档（全球护照和美国驾照）中的关键信息。 有关 ID 文档分析的详细信息，请参阅[预生成的标识模型概念指南](../../concept-identification-cards.md)。

若要从 URI 分析 ID 文档，请使用 `beginRecognizeIdentityDocumentsFromUrl` 方法。

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_id_call)]

> [!TIP]
> 还可分析本地 ID 文档图像。 请参阅 [FormRecognizerClient](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient) 方法，例如 beginRecognizeIdentityDocuments。 也可参阅 [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) 上的示例代码，了解涉及本地图像的方案。

以下代码会处理给定 URI 的 ID 文档，并将主字段和值输出到控制台。

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_id_print)]

## <a name="train-a-custom-model"></a>训练自定义模型

本部分演示如何使用自己的数据训练模型。 训练的模型可以输出结构化数据，其中包含原始形式的文档中的键/值关系。 训练模型后，可对其进行测试和重新训练，并最终使用它来根据需求提取其他表单中的数据。

> [!NOTE]
> 你还可以使用图形用户界面（例如[表单识别器示例标记工具](../../label-tool.md)）来训练模型。

### <a name="train-a-model-without-labels"></a>不使用标签训练模型

训练自定义模型可分析在自定义表单中找到的所有字段和值，无需手动标记训练文档。

下面的方法使用给定文档集训练模型，并将该模型的状态输出到控制台。

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_train_call)]

返回的 CustomFormModel 对象包含该模型可以分析的表单类型以及它可以从每种表单类型中提取的字段的相关信息。 下面的代码块将此信息输出到控制台。

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_train_print)]

最后，此方法返回模型的唯一 ID。

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_train_return)]

### <a name="output"></a>输出

```console
Train Model with training data...
Model Id: 20c3544d-97b4-49d9-b39b-dc32d85f1358
Model Status: ready
Training started on: 2020-08-31T16:52:09Z
Training completed on: 2020-08-31T16:52:23Z

Recognized Fields:
The subModel has form type form-0
The model found field 'field-0' with label: Address:
The model found field 'field-1' with label: Charges
The model found field 'field-2' with label: Invoice Date
The model found field 'field-3' with label: Invoice Due Date
The model found field 'field-4' with label: Invoice For:
The model found field 'field-5' with label: Invoice Number
The model found field 'field-6' with label: VAT ID
```

### <a name="train-a-model-with-labels"></a>使用标签训练模型

你还可以通过手动标记训练文档来训练自定义模型。 在某些情况下，使用标签训练可改善效果。 若要使用标签训练，你需要在 blob 存储容器中添加特殊标签信息文件 (\<filename\>.pdf.labels.json) 和训练文档。 [表单识别器示例标记工具](../../label-tool.md)提供了可帮助你创建这些标签文件的 UI。 获得它们后，可以调用 beginTraining 方法，并将 useTrainingLabels 参数设置为 `true`。

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_trainlabels_call)]

返回的 CustomFormModel 指示模型可以提取的字段，以及每个字段中的估计准确度。 下面的代码块将此信息输出到控制台。

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_trainlabels_print)]

### <a name="output"></a>输出

```console
Train Model with training data...
Model Id: 20c3544d-97b4-49d9-b39b-dc32d85f1358
Model Status: ready
Training started on: 2020-08-31T16:52:09Z
Training completed on: 2020-08-31T16:52:23Z

Recognized Fields:
The subModel has form type form-0
The model found field 'field-0' with label: Address:
The model found field 'field-1' with label: Charges
The model found field 'field-2' with label: Invoice Date
The model found field 'field-3' with label: Invoice Due Date
The model found field 'field-4' with label: Invoice For:
The model found field 'field-5' with label: Invoice Number
The model found field 'field-6' with label: VAT ID
```

## <a name="analyze-forms-with-a-custom-model"></a>使用自定义模型分析表单

本部分演示如何通过你使用自己的表单训练的模型，从自定义表单类型中提取键/值信息和其他内容。

> [!IMPORTANT]
> 若要实现此方案，必须已训练好一个模型，以便可以将其 ID 传递到下面的方法中。 请参阅[训练模型](#train-a-model-without-labels)部分。

你将使用 beginRecognizeCustomFormsFromUrl 方法。

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_analyze_call)]

> [!TIP]
> 还可分析本地文件。 请参阅 [FormRecognizerClient](/java/api/com.azure.ai.formrecognizer.formrecognizerclient) 方法，例如 beginRecognizeCustomForms。 或者，请参阅 [GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) 上的示例代码，了解涉及本地图像的方案。

返回的值是 RecognizedForm 对象的集合：提交的文档中的每一页对应一个对象。以下代码将分析结果输出到控制台。 它将输出每个识别的字段和相应的值以及置信度分数。

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_analyze_print)]

### <a name="output"></a>输出

```console
Analyze PDF form...
----------- Recognized custom form info for page 0 -----------
Form type: form-0
Field 'field-0' has label 'Address:' with a confidence score of 0.91.
Field 'field-1' has label 'Invoice For:' with a confidence score of 1.00.
Field 'field-2' has label 'Invoice Number' with a confidence score of 1.00.
Field 'field-3' has label 'Invoice Date' with a confidence score of 1.00.
Field 'field-4' has label 'Invoice Due Date' with a confidence score of 1.00.
Field 'field-5' has label 'Charges' with a confidence score of 1.00.
Field 'field-6' has label 'VAT ID' with a confidence score of 1.00.
```

## <a name="manage-custom-models"></a>管理自定义模型

本部分演示如何管理帐户中存储的自定义模型。 例如，下面的代码在单个方法中执行所有模型管理任务。 首先，复制以下方法签名：

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage)]

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>检查 FormRecognizer 资源帐户中的模型数

下面的代码块会检查表单识别器帐户中保存的模型数，并将其与帐户限制进行比较。

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage_count)]

#### <a name="output"></a>输出

```console
The account has 12 custom models, and we can have at most 250 custom models
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>列出资源帐户中当前存储的模型

下面的代码块会列出帐户中的当前模型，并将这些模型的详细信息输出到控制台。

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage_list)]

#### <a name="output"></a>输出

为了提高可读性，此响应已被截断。

```console
We have following models in the account:
Model Id: 0b048b60-86cc-47ec-9782-ad0ffaf7a5ce
Model Id: 0b048b60-86cc-47ec-9782-ad0ffaf7a5ce
Model Status: ready
Training started on: 2020-06-04T18:33:08Z
Training completed on: 2020-06-04T18:33:10Z
Custom Model Form type: form-0b048b60-86cc-47ec-9782-ad0ffaf7a5ce
Custom Model Accuracy: 1.00
Field Text: invoice date
Field Accuracy: 1.00
Field Text: invoice number
Field Accuracy: 1.00
...
```

### <a name="delete-a-model-from-the-resource-account"></a>从资源帐户中删除模型

还可以通过引用模型的 ID 从帐户中删除该模型。

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage_delete)]

## <a name="run-the-application"></a>运行应用程序

导航回主项目目录。 然后，使用以下命令生成应用：

```console
gradle build
```

使用 `run` 目标运行应用程序：

```console
gradle run
```

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除认知服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。

* [门户](../../../../cognitive-services/cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../../cognitive-services/cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>疑难解答

从识别器客户端引发 `ErrorResponseException` 异常。 例如，如果尝试提供一个无效的文件源 URL，则会引发 `ErrorResponseException`，并会出现一个指出失败原因的错误。 在以下代码片段中，通过捕获异常并显示有关错误的其他信息来妥善处理该错误。

```java Snippet:FormRecognizerBadRequest
try {
    formRecognizerClient.beginRecognizeContentFromUrl("invalidSourceUrl");
} catch (ErrorResponseException e) {
    System.out.println(e.getMessage());
}
```

### <a name="enable-client-logging"></a>启用客户端日志记录

适用于 Java 的 Azure SDK 提供了一致的日志记录案例，有助于排查应用程序错误和加快解决速度。 生成的日志会在到达终端状态之前捕获应用程序的流，以帮助查找根本问题。 查看[日志记录 wiki](https://github.com/Azure/azure-sdk-for-java/wiki/Logging-with-Azure-SDK)，以获取有关启用日志记录的指南。

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已使用表单识别器 Java 客户端库以不同的方式训练模型和分析表单。 接下来，请了解如何创建更好的训练数据集并生成更准确的模型。

> [!div class="nextstepaction"]
> [生成训练数据集](../../build-training-data-set.md)

* [什么是表单识别器？](../../overview.md)
* 在 [GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples) 上可以找到本指南中使用的示例代码（以及更多代码）。
