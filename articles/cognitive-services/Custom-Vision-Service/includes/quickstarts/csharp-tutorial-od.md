---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 09/15/2020
ms.openlocfilehash: 6a09d435613a715d4ae4e162f42f59402d0bc5cd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102444280"
---
适用于 .NET 的自定义视觉客户端库入门。 请按照以下步骤安装包并试用用于生成对象检测模型的示例代码。 你将创建一个项目，添加标签，针对示例图像训练该项目，并使用该项目的预测终结点 URL 以编程方式对其进行测试。 使用此示例作为模板来构建你自己的图像识别应用。

> [!NOTE]
> 若要在不编写代码的情况下构建和训练对象检测模型，请改为参阅[基于浏览器的指南](../../get-started-build-detector.md)。

使用适用于 .NET 的自定义视觉客户端库可以：

* 创建新的自定义视觉项目
* 将标记添加到项目中
* 上传和标记图像
* 定型项目
* 发布当前迭代
* 测试预测终结点

[参考文档](/dotnet/api/overview/azure/cognitiveservices/client/customvision) | 库源代码[（训练）](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.CustomVision.Training)[（预测）](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.CustomVision.Prediction)| 包 (NuGet)[（训练）](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)[（预测）](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/) | [示例](/samples/browse/?products=azure&term=vision&terms=vision)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services/)
* [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) 或最新版本的 [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)。
* 拥有 Azure 订阅后，在 Azure 门户中<a href="https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision"  title="创建自定义视觉资源"  target="_blank">创建自定义视觉资源</a>，以创建训练和预测资源并获取密钥和终结点。 等待其部署并单击“转到资源”按钮。
    * 需要从创建的资源获取密钥和终结点，以便将应用程序连接到自定义视觉。 你稍后会在快速入门中将密钥和终结点粘贴到下方的代码中。
    * 可以使用免费定价层 (`F0`) 试用该服务，然后再升级到付费层进行生产。

## <a name="setting-up"></a>设置

### <a name="create-a-new-c-application"></a>新建 C# 应用程序

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

使用 Visual Studio 创建新的 .NET Core 应用程序。 

### <a name="install-the-client-library"></a>安装客户端库 

创建新项目后，右键单击“解决方案资源管理器”中的项目解决方案，然后选择“管理 NuGet 包”，以安装客户端库 。 在打开的包管理器中，选择“浏览”，选中“包括预发行版”并搜索 `Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training` 和 `Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction` 。 选择最新版本，然后选择“安装”。 

#### <a name="cli"></a>[CLI](#tab/cli)

在控制台窗口（例如 cmd、PowerShell 或 Bash）中，使用 `dotnet new` 命令创建名为 `custom-vision-quickstart` 的新控制台应用。 此命令将创建包含单个源文件的简单“Hello World”C# 项目：*program.cs*。 

```console
dotnet new console -n custom-vision-quickstart
```

将目录更改为新创建的应用文件夹。 可使用以下代码生成应用程序：

```console
dotnet build
```

生成输出不应包含警告或错误。 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>安装客户端库 

在应用程序目录中，使用以下命令安装适用于 .NET 的自定义视觉客户端库：

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training --version 2.0.0
dotnet add package Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction --version 2.0.0
```

---

> [!TIP]
> 想要立即查看整个快速入门代码文件？ 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/CustomVision/ObjectDetection/Program.cs) 上找到它，其中包含此快速入门中的代码示例。

从项目目录中，打开 Program.cs 文件，并添加以下 `using` 指令：

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_imports)]

在应用程序的“Main”方法中，为资源的密钥和终结点创建变量。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_creds)]

> [!IMPORTANT]
> 转到 Azure 门户。 如果在“先决条件”部分中创建自定义视觉资源已成功部署，请单击“后续步骤”下的“转到资源”按钮  。 在资源的“密钥和终结点”页的“资源管理”下可以找到密钥和终结点 。 需要获取培训和预测密钥，以及培训资源的终结点。
>
> 请记住在完成后将密钥从代码中删除，永远不要公开发布这些密钥。 对于生产环境，请考虑使用安全的方法来存储和访问凭据。 有关详细信息，请参阅认知服务[安全性](../../../cognitive-services-security.md)文章。

在应用程序的“Main”方法中，添加对本快速入门中使用的方法的调用。 稍后将实现这些操作。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_maincalls)]

## <a name="object-model"></a>对象模型

|名称|说明|
|---|---|
|[CustomVisionTrainingClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient) | 此类处理模型的创建、训练和发布。 |
|[CustomVisionPredictionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient)| 此类处理用于对象检测预测的模型查询。|
|[PredictionModel](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.models.predictionmodel)| 此类定义单一图像上的单一对象预测。 其中包括对象 ID 和名称的属性、对象的边界框位置以及可信度分数。|

## <a name="code-examples"></a>代码示例

这些代码片段演示如何使用适用于 .NET 的自定义视觉客户端库执行以下任务：

* [对客户端进行身份验证](#authenticate-the-client)
* [创建新的自定义视觉项目](#create-a-new-custom-vision-project)
* [将标记添加到项目中](#add-tags-to-the-project)
* [上传和标记图像](#upload-and-tag-images)
* [定型项目](#train-the-project)
* [发布当前迭代](#publish-the-current-iteration)
* [测试预测终结点](#test-the-prediction-endpoint)

## <a name="authenticate-the-client"></a>验证客户端

在新方法中，使用终结点和密钥来实例化训练和预测客户端。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_auth)]

## <a name="create-a-new-custom-vision-project"></a>创建新的自定义视觉项目

下一个方法将创建对象检测项目。 创建的项目将显示在[自定义视觉网站](https://customvision.ai/)上。 请查看 [CreateProject](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__&preserve-view=true) 方法，以在创建项目时指定其他选项（在[生成检测器](../../get-started-build-detector.md) Web 门户指南中进行了说明）。  

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_create)]

## <a name="add-tags-to-the-project"></a>将标记添加到项目中

此方法定义要针对其训练模型的标签。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_tags)]

## <a name="upload-and-tag-images"></a>上传和标记图像

首先，下载此项目的示例图像。 将[示例图像文件夹](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ObjectDetection/Images)的内容保存到本地设备。

> [!NOTE]
> 是否需要一组范围更广的图像来完成训练？ Trove 是一个 Microsoft Garage 项目，可用于收集和购买图像集以便进行训练。 收集图像后，可以通过一般方式下载映像，然后将其导入到自定义视觉项目。 若要了解详细信息，请访问 [Trove 页面](https://www.microsoft.com/ai/trove?activetab=pivot1:primaryr3)。

在对象检测项目中标记图像时，需要使用标准化坐标指定每个标记对象的区域。 以下代码将每个示例图像与其标记的区域相关联。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_upload_regions)]

> [!NOTE]
> 对于你自己的项目，如果没有用于标记区域坐标的单击并拖动实用工具，则可以使用[自定义视觉网站](https://www.customvision.ai/)的 Web UI。 在此示例中，已提供坐标。

然后，使用此关联映射上传每个示例图像及其区域坐标。 最多可以在单个批次中上传 64 个图像。 可能需要更改 `imagePath` 值以指向正确的文件夹位置。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_upload)]

至此，你已上传了所有示例图像，并使用关联的像素矩形标记了每个示例图像（**叉子** 或 **剪刀**）。

## <a name="train-the-project"></a>定型项目

此方法将在项目中创建第一个训练迭代。 它将查询服务，直到训练完成。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_train)]

> [!TIP]
> 使用选定标记进行训练
>
> 可以选择只对应用的标记的子集进行训练。 如果你还没有应用足够多的特定标记，但是你确实有足够多的其他标记，则可能需要这样做。 在 [TrainProject](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.trainproject#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_TrainProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_Guid_System_String_System_Nullable_System_Int32__System_Nullable_System_Boolean__System_String_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_Models_TrainingParameters_&preserve-view=true) 调用中，使用 trainingParameters 参数。 构造一个 [TrainingParameters](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.models.trainingparameters)，并将其 SelectedTags 属性设置为要使用的标记的 ID 列表。 模型将训练成只识别该列表中的标记。

## <a name="publish-the-current-iteration"></a>发布当前迭代

此方法使模型的当前迭代可用于查询。 可以将模型名称用作发送预测请求的引用。 需要为 `predictionResourceId` 输入自己的值。 可以在 Azure 门户中资源的“概述”选项卡上找到列为“订阅 ID”的预测资源 ID 。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_publish)]


## <a name="test-the-prediction-endpoint"></a>测试预测终结点

此方法用于加载测试图像、查询模型终结点，以及将预测数据输出到控制台。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_prediction)]

## <a name="run-the-application"></a>运行应用程序

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

单击 IDE 窗口顶部的“调试”按钮，运行应用程序。

#### <a name="cli"></a>[CLI](#tab/cli)

从应用程序目录使用 `dotnet run` 命令运行应用程序。

```dotnet
dotnet run
```

---

应用程序运行时，会打开一个控制台窗口并写入以下输出：

```console
Creating new project:
        Training
Done!

Making a prediction:
        fork: 98.2% [ 0.111609578, 0.184719115, 0.6607002, 0.6637112 ]
        scissors: 1.2% [ 0.112389535, 0.119195729, 0.658031344, 0.7023591 ]
```

然后，可以验证测试图像（在 **Images/Test/** 中找到）是否已正确标记，并验证检测区域是否正确。 此时，可以按任意键退出应用程序。

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [clean-od-project](../../includes/clean-od-project.md)]

## <a name="next-steps"></a>后续步骤

现在，你已在代码中完成了对象检测过程的每一步。 此示例执行单次训练迭代，但通常需要多次训练和测试模型，以使其更准确。 以下指南涉及图像分类，但其原理与对象检测类似。

> [!div class="nextstepaction"]
> [测试和重新训练模型](../../test-your-model.md)

* 什么是自定义视觉？
* 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/CustomVision/ObjectDetection/Program.cs) 上找到此示例的源代码
* [SDK 参考文档](/dotnet/api/overview/azure/cognitiveservices/client/customvision)
