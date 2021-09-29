---
author: areddish
ms.author: areddish
ms.service: cognitive-services
ms.date: 10/26/2020
ms.custom: devx-track-js
ms.openlocfilehash: af1c2637258a298488107fea9af23b38f4dcda49
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128908389"
---
本指南提供说明和示例代码，以帮助你开始使用适用于 Node.js 的自定义视觉客户端库来构建对象检测模型。 你将创建一个项目，添加标记，训练该项目，并使用该项目的预测终结点 URL 以编程方式对其进行测试。 使用此示例作为模板来构建你自己的图像识别应用。

> [!NOTE]
> 若要在不编写代码的情况下构建和训练对象检测模型，请改为参阅[基于浏览器的指南](../../get-started-build-detector.md)。

使用适用于 .NET 的自定义视觉客户端库可以：

* 创建新的自定义视觉项目
* 将标记添加到项目中
* 上传和标记图像
* 定型项目
* 发布当前迭代
* 测试预测终结点

参考文档[（训练）](/javascript/api/@azure/cognitiveservices-customvision-training/) [（预测）](/javascript/api/@azure/cognitiveservices-customvision-prediction/) | 库源代码[（训练）](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-customvision-training) [（预测）](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-customvision-prediction)| 包 (npm) [（训练）](https://www.npmjs.com/package/@azure/cognitiveservices-customvision-training) [（预测）](https://www.npmjs.com/package/@azure/cognitiveservices-customvision-prediction) | [示例](/samples/browse/?products=azure&terms=custom%20vision&languages=javascript)


## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services/)
* 最新版本的 [Node.js](https://nodejs.org/)
* 拥有 Azure 订阅后，在 Azure 门户中<a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision"  title="创建自定义视觉资源"  target="_blank">创建自定义视觉资源</a>，以创建训练和预测资源并获取密钥和终结点。 等待其部署并单击“转到资源”按钮。
    * 需要从创建的资源获取密钥和终结点，以便将应用程序连接到自定义视觉。 你稍后会在快速入门中将密钥和终结点粘贴到下方的代码中。
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

### <a name="install-the-client-library"></a>安装客户端库

若要使用适用于 Node.js 的自定义视觉编写图像分析应用，需要自定义视觉 NPM 包。 若要安装它们，请在 PowerShell 中运行以下命令：

```shell
npm install @azure/cognitiveservices-customvision-training
npm install @azure/cognitiveservices-customvision-prediction
```

应用的 `package.json` 文件将使用依赖项进行更新。

创建一个名为 `index.js` 的文件，并导入以下库：

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_imports)]

> [!TIP]
> 想要立即查看整个快速入门代码文件？ 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js) 上找到它，其中包含此快速入门中的代码示例。

为资源的 Azure 终结点和密钥创建变量。 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_creds)]

> [!IMPORTANT]
> 转到 Azure 门户。 如果在“先决条件”部分中创建自定义视觉资源已成功部署，请单击“后续步骤”下的“转到资源”按钮  。 在资源的“密钥和终结点”页的“资源管理”下可以找到密钥和终结点 。 你需要获取培训资源和预测资源这两者的密钥，以及适用于培训资源的 API 终结点。
>
> 完成后，请记住将密钥从代码中删除，并且永远不要公开发布该密钥。 对于生产环境，请考虑使用安全的方法来存储和访问凭据。 有关详细信息，请参阅[认知服务安全性](../../../cognitive-services-security.md)一文。

此外，为项目名称添加字段，并为异步调用添加超时参数。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_vars)]

## <a name="object-model"></a>对象模型

|名称|说明|
|---|---|
|[TrainingAPIClient](/javascript/api/@azure/cognitiveservices-customvision-training/trainingapiclient) | 此类处理模型的创建、训练和发布。 |
|[PredictionAPIClient](/javascript/api/@azure/cognitiveservices-customvision-prediction/predictionapiclient)| 此类处理用于对象检测预测的模型查询。|
|[预测](/javascript/api/@azure/cognitiveservices-customvision-prediction/prediction)| 此接口定义对单一图像的单一预测。 它包括对象 ID 和名称的属性，以及可信度分数。|

## <a name="code-examples"></a>代码示例

这些代码片段演示如何使用适用于 JavaScript 的自定义视觉客户端库执行以下任务：

* [对客户端进行身份验证](#authenticate-the-client)
* [创建新的自定义视觉项目](#create-a-new-custom-vision-project)
* [将标记添加到项目中](#add-tags-to-the-project)
* [上传和标记图像](#upload-and-tag-images)
* [定型项目](#train-the-project)
* [发布当前迭代](#publish-the-current-iteration)
* [测试预测终结点](#test-the-prediction-endpoint)

## <a name="authenticate-the-client"></a>验证客户端

使用终结点和密钥实例化客户端对象。 使用你的密钥创建 ApiKeyCredentials 对象，并将其用于终结点，以创建 [TrainingAPIClient](/javascript/api/@azure/cognitiveservices-customvision-training/trainingapiclient) 和 [PredictionAPIClient](/javascript/api/@azure/cognitiveservices-customvision-prediction/predictionapiclient) 对象。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_auth)]

## <a name="add-helper-function"></a>添加帮助程序函数

添加以下函数以帮助进行多个异步调用。 稍后将使用它。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_auth)]

## <a name="create-a-new-custom-vision-project"></a>创建新的自定义视觉项目

编写新函数，使其包含所有自定义视觉函数调用。 添加以下代码以创建新的自定义视觉服务项目。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_create)]

## <a name="upload-and-tag-images"></a>上传和标记图像

首先，下载此项目的示例图像。 将[示例图像文件夹](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ObjectDetection/Images)的内容保存到本地设备。

> [!NOTE]
> 是否需要一组范围更广的图像来完成训练？ Trove 是一个 Microsoft Garage 项目，可用于收集和购买图像集以便进行训练。 收集图像后，可以通过一般方式下载映像，然后将其导入到自定义视觉项目。 若要了解详细信息，请访问 [Trove 页面](https://www.microsoft.com/ai/trove?activetab=pivot1:primaryr3)。

要将示例图像添加到项目，请在创建标记后插入以下代码。 此代码会上传具有相应标记的每个图像。 在对象检测项目中标记图像时，需要使用标准化坐标指定每个标记对象的区域。 在本教程中，区域使用代码进行内联硬编码。 区域在标准化坐标中指定边界框，坐标按以下顺序给定：左部、顶部、宽度、高度。 最多可以在单个批次中上传 64 个图像。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_upload)]


> [!IMPORTANT]
> 需根据下载认知服务 Python SDK 示例存储库的位置更改图像 (`sampleDataRoot`) 的路径。

> [!NOTE]
> 如果没有用于标记区域坐标的单击并拖动实用工具，则可以使用 [Customvision.ai](https://www.customvision.ai/) 的 Web UI。 在此示例中，已提供坐标。


## <a name="train-the-project"></a>定型项目

此代码用于创建预测模型的第一次迭代。 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_train)]


## <a name="publish-the-current-iteration"></a>发布当前迭代

此代码用于将训练好的迭代发布到预测终结点。 为发布的迭代起的名称可用于发送预测请求。 在发布迭代之前，迭代在预测终结点中不可用。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_publish)]


## <a name="test-the-prediction-endpoint"></a>测试预测终结点

若要将图像发送到预测终结点并检索预测，请将以下代码添加到你的函数。 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_test)]

然后，关闭你的自定义视觉函数并调用它。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_function_close)]

## <a name="run-the-application"></a>运行应用程序

在快速入门文件中使用 `node` 命令运行应用程序。

```shell
node index.js
```

应用程序的输出应显示在控制台中。 然后，可以验证是否已正确标记（在 **\<sampleDataRoot\>/Test/** 中找到的）测试图像，并验证检测区域是否正确。 也可返回到[自定义视觉网站](https://customvision.ai)，查看新创建项目的当前状态。

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [clean-od-project](../../includes/clean-od-project.md)]

## <a name="next-steps"></a>后续步骤

现在，你已在代码中完成了对象检测过程的每一步。 此示例执行单次训练迭代，但通常需要多次训练和测试模型，以使其更准确。 以下指南涉及图像分类，但其原理与对象检测类似。

> [!div class="nextstepaction"]
> [测试和重新训练模型](../../test-your-model.md)

* 什么是自定义视觉？
* 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js) 上找到此示例的源代码
* [SDK 参考文档（训练）](/javascript/api/@azure/cognitiveservices-customvision-training/)
* [SDK 参考文档（预测）](/javascript/api/@azure/cognitiveservices-customvision-prediction/)
