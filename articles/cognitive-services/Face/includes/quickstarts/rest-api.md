---
title: 人脸 REST API 快速入门
description: 使用人脸 REST API 和 cURL 来检测人脸、查找相似的人脸（按图像进行人脸搜索）、识别人脸（人脸识别搜索）并迁移人脸数据。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 12/06/2020
ms.author: pafarley
ms.openlocfilehash: 6520c9514ee8fbf950437026c1d2712683e58151
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122442390"
---
开始使用人脸 REST API 进行人脸识别。 通过人脸服务，可以访问用于检测和识别图像中的人脸的高级算法。

使用人脸 REST API 执行以下操作：

* [检测和分析人脸](#detect-and-analyze-faces)
* [查找相似人脸](#find-similar-faces)

> [!NOTE]
> 此快速入门使用 cURL 命令来调用 REST API。 也可以使用编程语言调用 REST API。 请参阅 GitHub 示例，查看 [C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/Face/rest)、[Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/Face/rest)、[Java](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/java/Face/rest)、[JavaScript](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/Face/rest) 和 [Go](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/go/Face/rest) 的相关示例。

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services/)
* [!INCLUDE [contributor-requirement](../../../includes/quickstarts/contributor-requirement.md)]
* 拥有 Azure 订阅后，在 Azure 门户中<a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="创建人脸资源"  target="_blank">创建人脸资源 </a>，获取密钥和终结点。 部署后，单击“转到资源”。
    * 需要从创建的资源获取密钥和终结点，以便将应用程序连接到人脸 API。 你稍后会在快速入门中将密钥和终结点粘贴到下方的代码中。
    * 可以使用免费定价层 (`F0`) 试用该服务，然后再升级到付费层进行生产。
* [PowerShell 6.0 及以上版本](/powershell/scripting/install/installing-powershell-core-on-windows)，或类似的命令行应用程序。


## <a name="detect-and-analyze-faces"></a>检测和分析人脸

将使用如下所示的命令来调用人脸 API 并获取图像中的人脸属性数据。 首先，将代码复制到文本编辑器中&mdash;在运行它之前，需对命令的某些部分进行更改。

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="detection_model_3":::

进行以下更改：
1. 将 `Ocp-Apim-Subscription-Key` 分配到有效的人脸订阅密钥。
1. 需更改该查询 URL 的第一部分，使之与订阅密钥的相应终结点匹配。
   [!INCLUDE [subdomains-note](../../../../../includes/cognitive-services-custom-subdomains-note.md)]
1. 可以选择更改请求正文中的 URL 以指向不同的映像。

进行更改以后，请打开命令提示符并输入新的命令。 

### <a name="examine-the-results"></a>检查结果

应该会在控制台窗口中看到显示为 JSON 数据的人脸信息。 例如：

```json
[
  {
    "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f",
    "faceRectangle": {
      "top": 131,
      "left": 177,
      "width": 162,
      "height": 162
    }
  }
]  
```

### <a name="get-face-attributes"></a>获取人脸特性
 
若要提取人脸属性，请再次调用检测 API，但将 `detectionModel` 设置为 `detection_01`。 同时，添加 `returnFaceAttributes` 查询参数。 该命令现在应如下所示。 与之前一样，请插入你的人脸订阅密钥和终结点。

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="detection_model_1":::

### <a name="examine-the-results"></a>检查结果

返回的人脸信息现在包含人脸属性。 例如：

```json
[
  {
    "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f",
    "faceRectangle": {
      "top": 131,
      "left": 177,
      "width": 162,
      "height": 162
    },
    "faceAttributes": {
      "smile": 0,
      "headPose": {
        "pitch": 0,
        "roll": 0.1,
        "yaw": -32.9
      },
      "gender": "female",
      "age": 22.9,
      "facialHair": {
        "moustache": 0,
        "beard": 0,
        "sideburns": 0
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0,
        "disgust": 0,
        "fear": 0,
        "happiness": 0,
        "neutral": 0.986,
        "sadness": 0.009,
        "surprise": 0.005
      },
      "blur": {
        "blurLevel": "low",
        "value": 0.06
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.67
      },
      "noise": {
        "noiseLevel": "low",
        "value": 0
      },
      "makeup": {
        "eyeMakeup": true,
        "lipMakeup": true
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0,
        "invisible": false,
        "hairColor": [
          {
            "color": "brown",
            "confidence": 1
          },
          {
            "color": "black",
            "confidence": 0.87
          },
          {
            "color": "other",
            "confidence": 0.51
          },
          {
            "color": "blond",
            "confidence": 0.08
          },
          {
            "color": "red",
            "confidence": 0.08
          },
          {
            "color": "gray",
            "confidence": 0.02
          }
        ]
      }
    }
  }
]
```

## <a name="find-similar-faces"></a>查找相似人脸

此操作采用检测到的单个人脸（源），并搜索其他一组人脸（目标），以找到匹配项（按图像进行人脸搜索）。 找到匹配项后，它会将匹配的人脸的 ID 输出到控制台。

### <a name="detect-faces-for-comparison"></a>检测人脸以进行比较

首先，需要先检测图像中的人脸，然后再进行比较。 如在[检测和分析](#detect-and-analyze-faces)部分中所做的那样，运行此命令。 此检测方法针对比较操作进行了优化。 它不会如以上部分所示提取详细人脸属性，而是使用另一个检测模型。

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="detect_for_similar":::

查找 JSON 响应中的 `"faceId"` 值，并将其保存到一个临时位置。 然后，为其他这些图像 URL 再次调用上述命令，并保存各自的面部 ID。 你将使用这些 ID 作为目标人脸组，从中查找相似的人脸。

:::code source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="similar_group":::

最后，检测要用于匹配的单一源人脸，并保存其 ID。 将此 ID 与其他 ID 分开。

:::code source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="similar_matcher":::

### <a name="find-matches"></a>查找匹配项

将以下命令复制到文本编辑器。

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="similar":::

然后，进行以下更改：
1. 将 `Ocp-Apim-Subscription-Key` 分配到有效的人脸订阅密钥。
1. 需更改该查询 URL 的第一部分，使之与订阅密钥的相应终结点匹配。

对于 `body` 值，使用以下 JSON 内容：

:::code language="JSON" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="similar_body":::

1. 对于 `"faceId"`，使用源人脸 ID。
1. 将其他人脸 ID 作为项粘贴到 `"faceIds"` 数组中。

### <a name="examine-the-results"></a>检查结果

你将收到一个 JSON 响应，它列出了与你的查询人脸匹配的面部的 ID。

```json
[
    {
        "persistedFaceId" : "015839fb-fbd9-4f79-ace9-7675fc2f1dd9",
        "confidence" : 0.82
    },
    ...
] 
```

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除认知服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已了解如何使用人脸 REST API 来执行基本人脸识别任务。 接下来，了解不同的人脸检测模型，学习如何为你的用例指定适当的模型。

> [!div class="nextstepaction"]
> [指定人脸检测模型版本](../../Face-API-How-to-Topics/specify-detection-model.md)

* [什么是人脸服务？](../../overview.md)