---
title: 快速入门：使用 Azure REST API 和 C# 检测图像中的人脸
titleSuffix: Azure Cognitive Services
description: 在本快速入门中，请使用 Azure 人脸 REST API 和 C# 检测图像中的人脸。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 11/23/2020
ms.author: pafarley
ms.openlocfilehash: f15fbc340aafa325f772a1fd50a2129fa5044c47
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2020
ms.locfileid: "96020868"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-c"></a>快速入门：使用人脸 REST API 和 C# 检测图像中的人脸

本快速入门将通过 C# 使用 Azure 人脸 REST API 来检测图像中的人脸。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/cognitive-services/)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services/)
* 拥有 Azure 订阅后，在 Azure 门户中<a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="创建人脸资源"  target="_blank">创建人脸资源 <span class="docon docon-navigate-external x-hidden-focus"></span></a>，获取密钥和终结点。 部署后，单击“转到资源”。
    * 需要从创建的资源获取密钥和终结点，以便将应用程序连接到人脸 API。 你稍后会在快速入门中将密钥和终结点粘贴到下方的代码中。
    * 可以使用免费定价层 (`F0`) 试用该服务，然后再升级到付费层进行生产。
- 任何版本的 [Visual Studio](https://www.visualstudio.com/downloads/)。

## <a name="create-the-visual-studio-project"></a>创建 Visual Studio 项目

1. 在 Visual Studio 中创建新的 **控制台应用 (.NET Framework)** 项目并将其命名为 **FaceDetection**。
1. 如果解决方案中有其他项目，请将此项目选为单一启动项目。

## <a name="add-face-detection-code"></a>添加人脸检测代码

打开新项目的 *Program.cs* 文件。 在这里，请添加加载图像和检测人脸所需的代码。

### <a name="include-namespaces"></a>包括命名空间

将以下 `using` 语句添加到 *Program.cs* 文件顶部。

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/rest/detect.cs" id="dependencies":::

### <a name="add-essential-fields"></a>添加必要的字段

添加包含以下字段的 **Program** 类。 该数据指定如何连接到人脸服务，以及在何处获取输入数据。 需使用订阅密钥的值更新 `subscriptionKey` 字段，并且可能需要更改 `uriBase` 字符串，使之包含资源终结点字符串。

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/rest/detect.cs" id="environment":::

### <a name="receive-image-input"></a>接收图像输入

将以下代码添加到 **Program** 类的 **Main** 方法。 此代码会将一个提示写入到控制台，要求用户输入其本地图像文件路径。 然后，它会调用另一方法 (**MakeAnalysisRequest**)，在该位置处理图像。

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/rest/detect.cs" id="main":::

### <a name="call-the-face-detection-rest-api"></a>调用人脸检测 REST API

将以下方法添加到 **Program** 类。 它构造一个针对人脸 API 的 REST 调用，以便检测远程图像（`requestParameters` 字符串指定要检索的人脸属性）中的人脸信息。 然后，它将输出数据写入到 JSON 字符串。

将在以下步骤中定义帮助程序方法。

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/rest/detect.cs" id="request":::

### <a name="process-the-input-image-data"></a>处理输入图像数据

将以下方法添加到 **Program** 类。 此方法会将指定文件路径中的图像转换为字节数组。

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/rest/detect.cs" id="getimage":::

### <a name="parse-the-json-response"></a>分析 JSON 响应

将以下方法添加到 **Program** 类。 此方法会格式化 JSON 输入，使之更加易于读取。 应用会将该字符串数据写入到控制台。 然后即可关闭类和命名空间。

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/rest/detect.cs" id="print":::

## <a name="run-the-app"></a>运行应用

成功的响应会以易于读取的 JSON 格式显示人脸数据。 例如：

```json
[
   {
      "faceId": "f7eda569-4603-44b4-8add-cd73c6dec644",
      "faceRectangle": {
         "top": 131,
         "left": 177,
         "width": 162,
         "height": 162
      }
   }
]
```

## <a name="extract-face-attributes"></a>提取人脸属性
 
若要提取人脸属性，请使用检测模型 1 并添加 `returnFaceAttributes` 查询参数。

```csharp
string requestParameters = "detectionModel=detection_01&returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise";
```

响应现在包含人脸属性。 例如：

```json
[
   {
      "faceId": "f7eda569-4603-44b4-8add-cd73c6dec644",
      "faceRectangle": {
         "top": 131,
         "left": 177,
         "width": 162,
         "height": 162
      },
      "faceAttributes": {
         "smile": 0.0,
         "headPose": {
            "pitch": 0.0,
            "roll": 0.1,
            "yaw": -32.9
         },
         "gender": "female",
         "age": 22.9,
         "facialHair": {
            "moustache": 0.0,
            "beard": 0.0,
            "sideburns": 0.0
         },
         "glasses": "NoGlasses",
         "emotion": {
            "anger": 0.0,
            "contempt": 0.0,
            "disgust": 0.0,
            "fear": 0.0,
            "happiness": 0.0,
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
            "value": 0.0
         },
         "makeup": {
            "eyeMakeup": true,
            "lipMakeup": true
         },
         "accessories": [

         ],
         "occlusion": {
            "foreheadOccluded": false,
            "eyeOccluded": false,
            "mouthOccluded": false
         },
         "hair": {
            "bald": 0.0,
            "invisible": false,
            "hairColor": [
               {
                  "color": "brown",
                  "confidence": 1.0
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

## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了一个简单的 .NET 控制台应用程序，该应用程序将 REST 调用与 Azure 人脸服务配合使用，以便检测图像中的人脸并返回其属性。 接下来，请浏览人脸 API 参考文档，详细了解支持的方案。

> [!div class="nextstepaction"]
> [人脸 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
