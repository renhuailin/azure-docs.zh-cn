---
title: 操作指南 — 自定义模型和组合模型
titleSuffix: Azure Applied AI Services
description: 了解如何创建、使用和管理表单识别器自定义模型和组合模型
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 10/07/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: abd279b8464e4477a99aa0da8be480844bf18563
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129716300"
---
# <a name="how-to-use-custom-and-composed-models"></a>操作指南：使用自定义模型和组合模型

表单识别器使用高级机器学习技术检测和提取文档图像的信息，并在结构化 JSON 输出中返回提取的数据。 使用表单识别器，可以训练独立的自定义模型，或者组合自定义模型来创建组合模型。

* 自定义模型。 通过表单识别器自定义模型可以分析和提取特定于业务的表单和文档中的数据。 自定义模型针对不同的数据和用例进行训练。

* 组合模型。 组合模型的创建方式是采用自定义模型的集合并将它们分配给包含表单类型的单个模型。 将文档提交到组合模型时，服务会执行分类步骤，以确定哪个自定义模型可准确表示呈现用于分析的表单。

本文介绍如何使用[表单识别器示例标记工具](label-tool.md)、[REST API](quickstarts/client-library.md?branch=main&pivots=programming-language-rest-api#train-a-custom-model) 或[客户端库 SDK](quickstarts/client-library.md?branch=main&pivots=programming-language-csharp#train-a-custom-model) 来创建表单识别器自定义模型和组合模型。

## <a name="try-it-sample-labeling-tool"></a>试用：示例标记工具

可以通过试用示例标记工具来了解如何从自定义表单提取数据。 需要准备好以下各项：

* 一个 Azure 订阅 — 可以[免费创建一个](https://azure.microsoft.com/free/cognitive-services/)

* Azure 门户中的一个[表单识别器实例](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer)。 可以使用免费定价层 (`F0`) 来试用该服务。 部署资源后，单击“转到资源”以获取 API 密钥和终结点。

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="屏幕截图：Azure 门户中的密钥和终结点位置。":::

> [!div class="nextstepaction"]
> [试用](https://fott-2-1.azurewebsites.net/projects/create)

在表单识别器 UI 中：

1. 选择“使用‘自定义’通过标签训练模型和获取键值对”。
  
      :::image type="content" source="media/label-tool/fott-use-custom.png" alt-text="屏幕截图：“自定义”选项的 fott 工具选择。":::

1. 在下一个窗口中选择“新建项目”：

    :::image type="content" source="media/label-tool/fott-new-project.png" alt-text="屏幕截图：“新建项目”的 fott 工具选择。"::: 

## <a name="create-your-models"></a>创建模型

生成、训练和使用自定义模型和组合模型的步骤如下所示：

* [收集训练数据集](#assemble-your-training-dataset)
* [将训练集上传到 Azure blob 存储](#upload-your-training-dataset)
* [训练自定义模型](#train-your-custom-model)
* [组合自定义模型](#create-a-composed-model)
* [分析文档](#analyze-documents-with-your-custom-or-composed-model)
* [**管理自定义模型**](#manage-your-custom-models)

## <a name="assemble-your-training-dataset"></a>收集训练数据集

生成自定义模型从建立训练数据集开始。 对于示例数据集，至少需要五个相同类型的已完成表单。 它们可以是不同的文件类型（jpg、png、pdf、tiff），并且包含文本和手写内容。 表单必须遵循表单识别器的[输入要求](build-training-data-set.md#custom-model-input-requirements)。

## <a name="upload-your-training-dataset"></a>上传训练数据集

需要[将训练数据上传到 Azure blob 存储容器](build-training-data-set.md#upload-your-training-data)。 如果不知道如何使用容器创建 Azure 存储帐户，请参阅 [Azure 门户的 Azure 存储快速入门](../../storage/blobs/storage-quickstart-blobs-portal.md)。 可以使用免费定价层 (F0) 试用该服务，然后再升级到付费层进行生产。

## <a name="train-your-custom-model"></a>训练自定义模型

你可以在使用或不使用标记数据集的情况下[训练模型](quickstarts/client-library.md#train-a-custom-model)。 未标记的数据集仅依赖于[布局 API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeLayoutAsync) 来检测和识别关键信息，而无需添加人工输入。 标记的数据集也依赖于布局 API，但包含了附加的人工输入，例如特定的标签和字段位置。 若要同时使用标记和未标记的数据，请从标记训练数据的至少五个相同类型的已完成表单开始，然后将未标记的数据添加到所需数据集。

### <a name="train-without-labels"></a>在没有标签的情况下训练

表单识别器使用非监督式学习来理解表单中字段与项之间的布局和关系。 当你提交了输入表单后，算法会按类型聚集表单，发现现有的键和表，以及将值关联到键，并将条目关联到表。 无标签训练并不要求手动标记数据，也不要求进行繁琐的编码和维护，因此建议你先尝试此方法。

有关如何收集训练文档的提示，请参见[生成训练数据集](./build-training-data-set.md)。

### <a name="train-with-labels"></a>使用标签进行训练

当你使用带标签数据进行训练时，该模型会通过监督式学习使用提供的带标签表单提取关注的值。 带标签的数据会生成表现更好的模型，并且可以生成处理复杂表单或所含值没有键的表单的模型。

表单识别器使用[布局 API](concept-layout.md) 来习得印刷体和手写体文本元素的预期大小和位置并提取表。 然后，它使用用户指定的标签来习得文档中的键/值关联和表。 建议使用五个手动标记的相同类型（相同结构）的表单，这样就可以在训练新模型时入门，并根据需要添加更多标记的数据以提高模型准确性。 借助表单识别器可以训练一个模型，以使用监督学习功能提取键值对和表。

[开始使用标签进行训练](label-tool.md)

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Azure-Form-Recognizer/player]

## <a name="create-a-composed-model"></a>创建组合模型

> [!NOTE]
> 模型组合仅适用于使用标签训练的自定义模型。 尝试组合未标记模型会生成错误。

通过模型组合操作，最多可将 100 个经过训练的自定义模型分配给单个模型 ID。 使用组合模型 ID 调用“分析”时，表单识别器首先将对提交的表单进行分类，选择最佳匹配的已分配模型，然后返回该模型的结果。 当传入的表单可能属于多个模板中的一个模板时，此操作非常有用。

使用表单识别器示例标记工具、REST API 或客户端库 SDK，按照以下步骤设置组合模型：

1. [收集自定义模型 ID](#gather-your-custom-model-ids)
1. [组合自定义模型](#compose-your-custom-models)

#### <a name="gather-your-custom-model-ids"></a>收集自定义模型 ID

成功完成训练过程后，将为自定义模型分配模型 ID。 可以按如下所示检索模型 ID：

### <a name="form-recognizer-sample-labeling-tool"></a>[表单识别器示例标记工具](#tab/fott)

使用[表单识别器示例标记工具](https://fott-2-1.azurewebsites.net/)训练模型时，模型 ID 位于“训练结果”窗口中：

:::image type="content" source="media/fott-training-results.png" alt-text="屏幕截图：训练结果窗口。":::

### <a name="rest-api"></a>[**REST API**](#tab/rest-api)

[REST API](quickstarts/client-library.md?pivots=programming-language-rest-api#train-a-custom-model) 会返回包含“位置”标头的 `201 (Success)` 响应。 此标头中最后一个参数的值是新训练的模型的模型 ID：

:::image type="content" source="media/model-id.png" alt-text="屏幕截图：返回的位置标头，其中包含模型 ID。":::

### <a name="client-library-sdks"></a>[客户端库 SDK](#tab/sdks)

 [客户端库 SDK](quickstarts/client-library.md?pivots=programming-language-csharp#train-a-custom-model) 返回一个模型对象，可查询该对象以返回训练的模型 ID：

* C\#  | [CustomFormModel 类](/dotnet/api/azure.ai.formrecognizer.training.customformmodel?view=azure-dotnet&preserve-view=true#properties "用于 .NET 的 Azure SDK")

* Java | [CustomFormModelInfo 类](/java/api/com.azure.ai.formrecognizer.training.models.customformmodelinfo?view=azure-java-stable&preserve-view=true#methods "用于 Java 的 Azure SDK")

* JavaScript | [CustomFormModelInfo 接口](/javascript/api/@azure/ai-form-recognizer/customformmodelinfo?view=azure-node-latest&preserve-view=true&branch=main#properties "用于 JavaScript 的 Azure SDK")

* Python | [CustomFormModelInfo 类](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.customformmodelinfo?view=azure-python&preserve-view=true&branch=main#variables "用于 Python 的 Azure SDK")

---

#### <a name="compose-your-custom-models"></a>组合自定义模型

收集与单个窗体类型对应的自定义模型后，可以将它们组合到单个模型中。

### <a name="form-recognizer-sample-labeling-tool"></a>[表单识别器示例标记工具](#tab/fott)

使用示例标记工具可以快速开始训练模型，并将它们组合为单个模型 ID。

完成训练后，按如下所示组合模型：

1. 在左侧滑轨菜单上，选择“模型组合”图标（合并箭头）。

1. 在主窗口中，选择要分配给单个模型 ID 的模型。 带有箭头图标的模型是已组合的模型。

1. 选择左上角的“组合”按钮。

1. 在弹出窗口中，为新组合的模型命名，然后选择“组合”。

操作完成后，新组合的模型会出现在列表中。

  :::image type="content" source="media/custom-model-compose.png" alt-text="屏幕截图：模型组合窗口。" lightbox="media/custom-model-compose-expanded.png":::

### <a name="rest-api"></a>[**REST API**](#tab/rest-api)

使用 REST API 可以提出[组合自定义模型](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/Compose)请求，从现有模型创建单个组合模型。 请求正文需要进行组合的 `modelIds` 的字符串数组，可以选择定义 `modelName`。  请参阅[组合异步模型](/rest/api/formrecognizer/2.1preview2/compose-custom-models-async/compose-custom-models-async)。

### <a name="client-library-sdks"></a>[客户端库 SDK](#tab/sdks)

使用选择的编程语言代码创建将使用单个模型 ID 进行调用的组合模型。 下面是演示如何从现有自定义模型创建组合模型的代码示例的链接：

* [C#/.NET](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/Sample8_ModelCompose.md)。

* [Java](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/java/com/azure/ai/formrecognizer/CreateComposedModel.java)。

* [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/formrecognizer/ai-form-recognizer/samples/v3/javascript/createComposedModel.js)。

* [**Python**](https://github.com/Azure/azure-sdk-for-python/blob/main/sdk/formrecognizer/azure-ai-formrecognizer/samples/v3.2-beta/sample_create_composed_model.py)

---

## <a name="analyze-documents-with-your-custom-or-composed-model"></a>使用自定义模型或组合模型分析文档

 自定义表单“分析”操作要求在表单识别器调用中提供 `modelID`。 可以为 `modelID` 参数提供单个自定义模型 ID 或组合模型 ID。

### <a name="form-recognizer-sample-labeling-tool"></a>[表单识别器示例标记工具](#tab/fott)

1. 在工具的左窗格菜单上，选择“分析”图标（灯泡）。

1. 选择要分析的本地文件或图像 URL。

1. 选择“运行分析”按钮。

1. 该工具将在边界框中应用标记，并报告每个标记的置信度百分比。

:::image type="content" source="media/analyze.png" alt-text="屏幕快照：表单识别器工具分析自定义表单窗口。":::

### <a name="rest-api"></a>[**REST API**](#tab/rest-api)

使用 REST API 可以提出[分析表单](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm)请求，以分析文档并提取键值对和表数据。

### <a name="client-library-sdks"></a>[客户端库 SDK](#tab/sdks)

使用所选的编程语言，通过自定义模型或组合模型分析表单或文档。 你会需要表单识别器终结点、API 密钥和模型 ID。

* [**C#/.NET**](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/Sample8_ModelCompose.md#recognize-a-custom-form-using-a-composed-model)

* [**Java**](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/java/com/azure/ai/formrecognizer/RecognizeCustomFormsFromUrl.java)

* [**JavaScript**](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/formrecognizer/ai-form-recognizer/samples/v3/javascript/recognizeCustomForm.js)

* [**Python**](https://github.com/Azure/azure-sdk-for-python/blob/main/sdk/formrecognizer/azure-ai-formrecognizer/samples/v3.1/sample_recognize_custom_forms.py)

---

通过[分析不属于训练数据集的表单](quickstarts/client-library.md#analyze-forms-with-a-custom-model)来测试新训练的模型。 可能需要执行进一步的训练来改进模型，具体取决于报告的准确度。 可以继续执行进一步的训练以[改进结果](label-tool.md#improve-results)。

## <a name="manage-your-custom-models"></a>管理自定义模型

可以查看订阅下的[所有自定义模型列表](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetCustomModels)、检索有关[特定自定义模型](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetCustomModel)的信息以及从帐户中[删除自定义模型](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/DeleteCustomModel)，从而在整个生命周期中[管理自定义模型](quickstarts/client-library.md#manage-custom-models)。

很好！ 你已学习了创建自定义模型和组合模型并在表单识别器项目和应用程序中使用它们的步骤。

## <a name="next-steps"></a>后续步骤

浏览我们的 API 参考文档，详细了解表单识别器客户端库。

> [!div class="nextstepaction"]
> [表单识别器 API 参考](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm)
>

