---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 10/25/2020
ms.openlocfilehash: 1fb4ca4b12c97c010e6a77d3e00acb095789f6b5
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/10/2021
ms.locfileid: "100106171"
---
适用于 Python 的自定义视觉客户端库入门。 请按照以下步骤安装包并试用用于生成对象检测模型的示例代码。 你将创建一个项目，添加标记，训练该项目，并使用该项目的预测终结点 URL 以编程方式对其进行测试。 使用此示例作为模板来构建你自己的图像识别应用。

> [!NOTE]
> 若要在不编写代码的情况下构建和训练对象检测模型，请改为参阅[基于浏览器的指南](../../get-started-build-detector.md)。

使用适用于 Python 的自定义视觉客户端库可以：

* 创建新的自定义视觉项目
* 将标记添加到项目中
* 上传和标记图像
* 定型项目
* 发布当前迭代
* 测试预测终结点

[参考文档](/python/api/overview/azure/cognitiveservices/customvision) | [库源代码](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-customvision/azure/cognitiveservices/vision/customvision) | [包 (PyPI)](https://pypi.org/project/azure-cognitiveservices-vision-customvision/) | [示例](/samples/browse/?languages=python&products=azure&term=vision&terms=vision)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
* 拥有 Azure 订阅后，在 Azure 门户中<a href="https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision"  title="创建自定义视觉资源"  target="_blank">创建自定义视觉资源<span class="docon docon-navigate-external x-hidden-focus"></span></a>，以创建训练和预测资源并获取密钥和终结点。 等待其部署并单击“转到资源”按钮。
    * 需要从创建的资源获取密钥和终结点，以便将应用程序连接到自定义视觉。 你稍后会在快速入门中将密钥和终结点粘贴到下方的代码中。
    * 可以使用免费定价层 (`F0`) 试用该服务，然后再升级到付费层进行生产。

## <a name="setting-up"></a>设置

### <a name="install-the-client-library"></a>安装客户端库

若要使用适用于 Python 的自定义视觉来编写图像分析应用，需要自定义视觉客户端库。 安装 Python 后，在 PowerShell 或控制台窗口中运行以下命令：

```powershell
pip install azure-cognitiveservices-vision-customvision
```

### <a name="create-a-new-python-application"></a>创建新的 Python 应用程序

创建新的 Python 文件并导入以下库。

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_imports)]

> [!TIP]
> 想要立即查看整个快速入门代码文件？ 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py) 上找到它，其中包含此快速入门中的代码示例。

为资源的 Azure 终结点和订阅密钥创建变量。

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_creds)]


> [!IMPORTANT]
> 转到 Azure 门户。 如果在“先决条件”部分中创建自定义视觉资源已成功部署，请单击“后续步骤”下的“转到资源”按钮  。 在资源的“密钥和终结点”页的“资源管理”下可以找到密钥和终结点 。 需要获取培训和预测密钥，以及培训资源的终结点。
>
> 可以在资源的“概览”选项卡上找到列为“订阅 ID”的预测资源 ID 值 。
>
> 请记住在完成后将密钥从代码中删除，永远不要公开发布这些密钥。 对于生产环境，请考虑使用安全的方法来存储和访问凭据。 有关详细信息，请参阅认知服务[安全性](../../../../cognitive-services/cognitive-services-security.md)文章。

## <a name="object-model"></a>对象模型

|名称|说明|
|---|---|
|[CustomVisionTrainingClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient) | 此类处理模型的创建、训练和发布。 |
|[CustomVisionPredictionClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient)| 此类处理用于对象检测预测的模型查询。|
|[ImagePrediction](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.prediction.models.imageprediction)| 此类定义单一图像上的单一对象预测。 其中包括对象 ID 和名称的属性、对象的边界框位置以及可信度分数。|

## <a name="code-examples"></a>代码示例

这些代码片段演示如何使用适用于 Python 的自定义视觉客户端库执行以下任务：

* [对客户端进行身份验证](#authenticate-the-client)
* [创建新的自定义视觉项目](#create-a-new-custom-vision-project)
* [将标记添加到项目中](#add-tags-to-the-project)
* [上传和标记图像](#upload-and-tag-images)
* [定型项目](#train-the-project)
* [发布当前迭代](#publish-the-current-iteration)
* [测试预测终结点](#test-the-prediction-endpoint)

## <a name="authenticate-the-client"></a>验证客户端

使用终结点和密钥来实例化训练和预测客户端。 使用密钥创建 ApiKeyServiceClientCredentials 对象，并将它们与终结点一起使用以创建 [CustomVisionTrainingClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient) 和 [CustomVisionPredictionClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient) 对象。

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_auth)]


## <a name="create-a-new-custom-vision-project"></a>创建新的自定义视觉项目

将以下代码添加到脚本中以创建新的自定义视觉服务项目。 

请查看 [create_project](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.operations.customvisiontrainingclientoperationsmixin#create-project-name--description-none--domain-id-none--classification-type-none--target-export-platforms-none--custom-headers-none--raw-false----operation-config-&preserve-view=true) 方法，以在创建项目时指定其他选项（在[生成检测器](../../get-started-build-detector.md) Web 门户指南中进行了说明）。  

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_create)]


## <a name="add-tags-to-the-project"></a>将标记添加到项目中

若要在项目中创建对象标记，请添加以下代码：

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_tags)]


## <a name="upload-and-tag-images"></a>上传和标记图像

首先，下载此项目的示例图像。 将[示例图像文件夹](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ObjectDetection/Images)的内容保存到本地设备。

> [!NOTE]
> 是否需要一组范围更广的图像来完成训练？ Trove 是一个 Microsoft Garage 项目，可用于收集和购买图像集以便进行训练。 收集图像后，可以通过一般方式下载映像，然后将其导入到自定义视觉项目。 若要了解详细信息，请访问 [Trove 页面](https://www.microsoft.com/ai/trove?activetab=pivot1:primaryr3)。

在对象检测项目中标记图像时，需要使用标准化坐标指定每个标记对象的区域。 以下代码将每个示例图像与其标记的区域相关联。 区域在标准化坐标中指定边界框，坐标按以下顺序给定：左部、顶部、宽度、高度。

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_tagging)]

> [!NOTE]
> 如果没有用于标记区域坐标的单击并拖动实用工具，则可以使用 [Customvision.ai](https://www.customvision.ai/) 的 Web UI。 在此示例中，已提供坐标。

然后，使用此关联映射上传每个样本图像及其区域坐标（最多可以在单个批次中上传 64 个图像）。 添加以下代码。


[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_upload)]

> [!NOTE]
> 需根据此前下载认知服务 Python SDK 示例存储库的位置更改图像的路径。

## <a name="train-the-project"></a>定型项目

此代码用于创建预测模型的第一次迭代。 

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_train)]

> [!TIP]
> 使用选定标记进行训练
>
> 可以选择只对应用的标记的子集进行训练。 如果你还没有应用足够多的特定标记，但是你确实有足够多的其他标记，则可能需要这样做。 在 [train_project](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.operations.customvisiontrainingclientoperationsmixin#train-project-project-id--training-type-none--reserved-budget-in-hours-0--force-train-false--notification-email-address-none--selected-tags-none--custom-headers-none--raw-false----operation-config-&preserve-view=true) 调用中，将可选参数 selected_tags 设置为要使用的标记的 ID 字符串列表。 模型将训练成只识别该列表中的标记。

## <a name="publish-the-current-iteration"></a>发布当前迭代

在发布迭代之前，迭代在预测终结点中不可用。 以下代码使模型的当前迭代可用于查询。 

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_publish)]

## <a name="test-the-prediction-endpoint"></a>测试预测终结点

若要将图像发送到预测终结点并检索预测，请将以下代码添加到文件末尾：

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py?name=snippet_test)]


## <a name="run-the-application"></a>运行应用程序

运行 CustomVisionQuickstart.py。

```powershell
python CustomVisionQuickstart.py
```

应用程序的输出应显示在控制台中。 然后，可以验证测试图像（在 <base_image_location>/images/Test 中）是否已正确标记，并验证检测区域是否正确。 也可返回到[自定义视觉网站](https://customvision.ai)，查看新创建项目的当前状态。

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [clean-od-project](../../includes/clean-od-project.md)]

## <a name="next-steps"></a>后续步骤

现在，你已在代码中完成了对象检测过程的每一步。 此示例执行单次训练迭代，但通常需要多次训练和测试模型，以使其更准确。 以下指南涉及图像分类，但其原理与对象检测类似。

> [!div class="nextstepaction"]
> [测试和重新训练模型](../../test-your-model.md)

* 什么是自定义视觉？
* 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/CustomVision/ObjectDetection/CustomVisionQuickstart.py) 上找到此示例的源代码
* [SDK 参考文档](/python/api/overview/azure/cognitiveservices/customvision)
