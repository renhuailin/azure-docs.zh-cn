---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 12/09/2020
ms.topic: include
ms.openlocfilehash: cc022470bf9ff799d6396f871f4a6c224d01a295
ms.sourcegitcommit: bd1a4e4df613ff24e954eb3876aebff533b317ae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2021
ms.locfileid: "107931721"
---
自定义视觉 REST API 入门。 请按照以下步骤调用 API 和创建图像分类模型。 你将创建一个项目，添加标记，训练该项目，并使用该项目的预测终结点 URL 以编程方式对其进行测试。 使用此示例作为模板来构建你自己的图像识别应用。

> [!NOTE]
> 要使用自定义视觉，最简单的方法是通过客户端库 SDK 或[浏览器特定指南](../../get-started-build-detector.md)。

使用适用于 .NET 的自定义视觉客户端库可以：

* 创建新的自定义视觉项目
* 将标记添加到项目中
* 上传和标记图像
* 定型项目
* 发布当前迭代
* 测试预测终结点

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services/)
* 拥有 Azure 订阅后，在 Azure 门户中<a href="https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision"  title="创建自定义视觉资源"  target="_blank">创建自定义视觉资源</a>，以创建训练和预测资源并获取密钥和终结点。 等待其部署并单击“转到资源”按钮。
    * 需要从创建的资源获取密钥和终结点，以便将应用程序连接到自定义视觉。 你稍后会在快速入门中将密钥和终结点粘贴到下方的代码中。
    * 可以使用免费定价层 (`F0`) 试用该服务，然后再升级到付费层进行生产。
* [PowerShell 6.0 及以上版本](/powershell/scripting/install/installing-powershell-core-on-windows)，或类似的命令行应用程序。


## <a name="create-a-new-custom-vision-project"></a>创建新的自定义视觉项目

你将使用如下所示的命令来创建图像分类项目。 创建的项目将显示在[自定义视觉网站](https://customvision.ai/)上。


:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="createproject":::

将命令复制到文本编辑器，并进行以下更改：

* 将 `{subscription key}` 替换为有效的人脸订阅密钥。
* 将 `{endpoint}` 替换为与订阅密钥对应的终结点。
   [!INCLUDE [subdomains-note](../../../../../includes/cognitive-services-custom-subdomains-note.md)]
* 将 `{name}` 替换为项目的名称。
* （可选）设置其他 URL 参数来配置项目将使用哪种类型的模型。 相关选项，请参阅 [CreatProject API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeae)。

你将收到如下所示的 JSON 响应。 将项目的 `"id"` 值保存到临时位置。

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "description": "string",
  "settings": {
    "domainId": "00000000-0000-0000-0000-000000000000",
    "classificationType": "Multiclass",
    "targetExportPlatforms": [
      "CoreML"
    ],
    "useNegativeSet": true,
    "detectionParameters": "string",
    "imageProcessingSettings": {
      "augmentationMethods": {}
    }
  },
  "created": "string",
  "lastModified": "string",
  "thumbnailUri": "string",
  "drModeEnabled": true,
  "status": "Succeeded"
}
```

## <a name="add-tags-to-the-project"></a>将标记添加到项目中

使用以下命令定义将在其上训练模型的标签。

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="createtag":::

* 再次插入你自己的密钥和终结点 URL。
* 将 `{projectId}` 替换为你自己的项目 ID。
* 将 `{name}` 替换为你想要使用的标签的名称。

对你要在项目中使用的所有标签重复此过程。 如果使用的是已提供的示例图像，请添加标签 `"Hemlock"` 和 `"Japanese Cherry"`。

你将收到如下所示的 JSON 响应。 将每个标签的 `"id"` 值保存到临时位置。

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "description": "string",
  "type": "Regular",
  "imageCount": 0
}
```

## <a name="upload-and-tag-images"></a>上传和标记图像

接下来，下载此项目的示例图像。 将[示例图像文件夹](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ImageClassification/Images)的内容保存到本地设备。

> [!NOTE]
> 是否需要一组范围更广的图像来完成训练？ Trove 是一个 Microsoft Garage 项目，可用于收集和购买图像集以便进行训练。 收集图像后，可以通过一般方式下载映像，然后将其导入到自定义视觉项目。 若要了解详细信息，请访问 [Trove 页面](https://www.microsoft.com/ai/trove?activetab=pivot1:primaryr3)。

使用以下命令上传图像并应用标签；对“Hemlock”图像使用一次，并分别用于“Japan Cherry”图像。 有关更多选项，请参阅[从数据创建图像](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb5) API。

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="uploadimages":::

* 再次插入你自己的密钥和终结点 URL。
* 将 `{projectId}` 替换为你自己的项目 ID。
* 将 `{tagArray}` 替换为标签的 ID。
* 然后，使用你要标记的图像的二进制数据填充请求的正文。

## <a name="train-the-project"></a>定型项目

此方法会在你上传的带标签图像上训练模型，并返回当前项目迭代的 ID。

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="trainproject":::

* 再次插入你自己的密钥和终结点 URL。
* 将 `{projectId}` 替换为你自己的项目 ID。
* 将 `{tagArray}` 替换为标签的 ID。
* 然后，使用你要标记的图像的二进制数据填充请求的正文。
* （可选）使用其他 URL 参数。 相关选项，请参阅[训练项目](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee1) API。

> [!TIP]
> 使用选定标记进行训练
>
> 可以选择只对应用的标记的子集进行训练。 如果你还没有应用足够多的特定标记，但是你确实有足够多的其他标记，则可能需要这样做。 将可选 JSON 内容添加到请求的正文中。 用你要使用的标签的 ID 填充 `"selectedTags"` 数组。
> ```json
> {
>   "selectedTags": [
>     "00000000-0000-0000-0000-000000000000"
>   ]
> }
> ```

JSON 响应包含你训练的项目的相关信息，包括迭代 ID（`"id"`）。 请保存其值供下一步骤使用。

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "status": "string",
  "created": "string",
  "lastModified": "string",
  "trainedAt": "string",
  "projectId": "00000000-0000-0000-0000-000000000000",
  "exportable": true,
  "exportableTo": [
    "CoreML"
  ],
  "domainId": "00000000-0000-0000-0000-000000000000",
  "classificationType": "Multiclass",
  "trainingType": "Regular",
  "reservedBudgetInHours": 0,
  "trainingTimeInMinutes": 0,
  "publishName": "string",
  "originalPublishResourceId": "string"
}
```

## <a name="publish-the-current-iteration"></a>发布当前迭代

此方法使模型的当前迭代可用于查询。 可以将返回的模型名称用作发送预测请求的引用。 

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="publish":::

* 再次插入你自己的密钥和终结点 URL。
* 将 `{projectId}` 替换为你自己的项目 ID。
* 将 `{iterationId}` 替换为上一步骤返回的 ID。
* 将 `{publishedName}` 替换为你要分配给预测模型的名称。
* 将 `{predictionId}` 替换为你自己的预测资源 ID。 可以在 Azure 门户中资源的“属性”选项卡上找到列为“资源 ID”的预测资源 ID 。
* （可选）使用其他 URL 参数。 请参阅[发布迭代](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fdded5) API。

## <a name="test-the-prediction-endpoint"></a>测试预测终结点

最后，使用此命令通过上传新图像来测试已训练的模型，对标签进行分类。 可在前面下载的示例文件的“测试”文件夹中使用该图像。

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="publish":::

* 再次插入你自己的密钥和终结点 URL。
* 将 `{projectId}` 替换为你自己的项目 ID。
* 将 `{publishedName}` 替换为上一步使用的名称。
* 将本地图像的二进制数据添加到请求正文中。
* （可选）使用其他 URL 参数。 请参阅[对图像进行分类](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.1/operations/5eb37d24548b571998fde5f3) API。

返回的 JSON 响应将列出模型应用于图像的每个标签，以及每个标签的概率分数。 

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "project": "00000000-0000-0000-0000-000000000000",
  "iteration": "00000000-0000-0000-0000-000000000000",
  "created": "string",
  "predictions": [
    {
      "probability": 0.0,
      "tagId": "00000000-0000-0000-0000-000000000000",
      "tagName": "string",
      "boundingBox": {
        "left": 0.0,
        "top": 0.0,
        "width": 0.0,
        "height": 0.0
      },
      "tagType": "Regular"
    }
  ]
}
```

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>后续步骤

现在，你已使用 REST API 完成了图像分类过程的每一步。 此示例执行单次训练迭代，但通常需要多次训练和测试模型，以使其更准确。

> [!div class="nextstepaction"]
> [测试和重新训练模型](../../test-your-model.md)

* 什么是自定义视觉？
* [API 参考文档（训练）](/dotnet/api/overview/azure/cognitiveservices/client/customvision)
* [API 参考文档（预测）](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeae)