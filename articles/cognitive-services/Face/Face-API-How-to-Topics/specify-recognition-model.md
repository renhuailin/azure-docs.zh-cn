---
title: 如何指定识别模型 - 人脸
titleSuffix: Azure Cognitive Services
description: 本文介绍如何选择要对 Azure 人脸应用程序使用的识别模型。
services: cognitive-services
author: longli0
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/05/2021
ms.author: longl
ms.custom: devx-track-csharp
ms.openlocfilehash: 92e9c22712fdbfae5ab13a23cf72e282a225288a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733241"
---
# <a name="specify-a-face-recognition-model"></a>指定人脸识别模型

本指南介绍如何指定识别模型，以使用 Azure 人脸服务进行人脸检测、识别和相似性搜索。

人脸服务使用机器学习模型对图像中的人脸执行运算。 我们将会根据客户反馈以及研究成果不断改进模型的准确度，并作为模型更新交付改进结果。 开发人员可以选择指定想要使用的人脸识别模型版本；他们可以选择最适合其用例的模型。

Azure 人脸服务具有四种可用的识别模型。 我们将继续支持模型 recognition_01（2017 年发布）、recognition_02（2019 年发布）和 recognition_03（2020 年发布），以确保为使用通过这些模型创建的 FaceList 或 PersonGroup 的客户提供后向兼容性  。 FaceList 或 Persongroup 将始终使用创建它时所用的识别模型，并且将新的人脸添加到其中时，它们将与此模型相关联 。 创建后无法更改此设置，客户需要将相应的识别模型与相应的 FaceList 或 PersonGroup 一起使用 。

你可以根据自己的需要转到较新的识别模型；但是，你将需要使用所选的识别模型来创建新的 FaceList 和 PersonGroup。

recognition_04 模型（2021 年发布）是当前可用的最准确的模型。 如果你是新客户，建议使用此模型。 Recognition_04 将在相似性比较和人员匹配比较方面提供更高的准确性。 Recognition_04 改进了对佩戴面部遮盖物（外科口罩、N95 口罩、布口罩）的已注册用户的识别。 现在，你可以构建安全且无缝的用户体验，以便使用最新的 detection_03 模型来检测已注册的用户是否佩戴了面部遮盖物，然后使用最新的 recognition_04 模型来识别他们的身份 。 请注意，每个模型都独立于其他模型运行，并且为一个模型设置的置信度阈值不应在其他识别模型之间进行比较。

请继续阅读，了解如何在不同的人脸操作中指定选定的模型，同时避免模型冲突。 如果你是高级用户，想要确定是否应该切换到最新的模型，可以转到[评估不同的模型](#evaluate-different-models)部分来评估新模型，并使用当前数据集比较结果。


## <a name="prerequisites"></a>先决条件

读者应该熟悉 AI 人脸检测和识别的概念。 否则请先阅读以下指南：

* [人脸检测的概念](../concepts/face-detection.md)
* [人脸识别的概念](../concepts/face-recognition.md)
* [调用检测 API](HowtoDetectFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>使用指定的模型检测人脸

人脸检测可以识别人脸的视觉特征点，并查找其边界框位置。 它还能提取人脸的特征，并将其存储以在识别时使用。 所有这些信息构成了人脸的表示形式。

提取人脸特征时将使用识别模型，因此，可以在执行检测操作时指定模型版本。

使用 [Face - Detect] API 时，可以使用 `recognitionModel` 参数分配模型版本。 可用值有：
* recognition_01
* recognition_02
* recognition_03
* recognition_04


或者，可以指定 _returnRecognitionModel_ 参数（默认为 **false**）来指示是否应在响应中返回 _recognitionModel_。 因此，[Face - Detect] REST API 的请求 URL 将如下所示：

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel]&subscription-key=<Subscription key>`

如果使用客户端库，则可以通过传递一个表示版本的字符串来分配 `recognitionModel` 的值。 如果不分配该值，将使用 `recognition_01` 的默认模型版本。 请查看适用于 .NET 客户端库的以下代码示例。

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, recognitionModel: "recognition_01", returnRecognitionModel: true);
```

## <a name="identify-faces-with-specified-model"></a>使用指定的模型识别人脸

人脸服务可以从图像中提取人脸数据，并将其与 Person 对象相关联（例如，通过 [Add face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API 调用），多个 Person 对象可以一起存储在 PersonGroup 中  。 然后，可以根据 **PersonGroup** 比较新的人脸（使用 [Face - Identify] 调用），该组中匹配的人员将被识别。

**PersonGroup** 中的所有 **Person** 应有一个唯一的识别模型，在创建该组（使用 [PersonGroup - Create] 或 [LargePersonGroup - Create]）时，可以使用 `recognitionModel` 参数指定此模型。 如果不指定此参数，则会使用原始的 `recognition_01` 模型。 某个组始终使用创建它时所用的识别模型，将新的人脸添加到该组时，它们将与此模型相关联；创建组后无法更改此行为。 若要查看 **PersonGroup** 是使用哪个模型配置的，请结合设置为 **true** 的 _returnRecognitionModel_ 参数使用 [PersonGroup - Get] API。

请查看适用于 .NET 客户端库的以下代码示例。

```csharp
// Create an empty PersonGroup with "recognition_04" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_04");
```

在此代码中，创建了 ID 为 `mypersongroupid` 的 PersonGroup，它设置为使用 recognition_04 模型提取人脸特征。

相应地，需要指定在检测人脸以根据此 **PersonGroup** 进行比较（通过 [人脸 - 检测] API）时要使用的模型。 使用的模型应始终为 **PersonGroup** 的配置相一致；否则，操作将会由于模型不兼容而失败。

无需在 [Face - Identify] API 中进行更改；只需在检测中指定模型版本。

## <a name="find-similar-faces-with-specified-model"></a>使用指定的模型查找类似的人脸

还可以指定一个识别模型来进行类似性搜索。 可以在使用 [FaceList - Create] API 或 [LargeFaceList - Create] 创建 FaceList 时，使用 `recognitionModel` 来分配模型版本。 如果不指定此参数，则默认使用 `recognition_01` 模型。 FaceList 始终使用创建它时所用的识别模型，并且将新的人脸添加到该列表时，它们将与此模型相关联；创建后无法对此进行更改。 若要查看 FaceList 是使用哪个模型配置的，请结合设置为 true 的 returnRecognitionModel 参数使用 [FaceList - Get] API。

请查看适用于 .NET 客户端库的以下代码示例。

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_04");
```

此代码使用 recognition_04 模型来提取特征，创建名为 `My face collection` 的 FaceList。 在此 FaceList 中搜索与新检测到的人脸类似的人脸时，该人脸必须已使用 recognition_04 模型检测到 ([Face - Detect])。 如上一部分所述，模型需要保持一致。

无需在 [Face - Find Similar] API 中进行更改；只需在检测中指定模型版本。

## <a name="verify-faces-with-specified-model"></a>使用指定的模型验证人脸

[Face - Verify] API 会检查两张人脸是否属于同一个人。 无需在 Verify API 中进行识别模型方面的更改，但只能比较使用同一模型检测到的人脸。

## <a name="evaluate-different-models"></a>评估不同的模型

如果要基于自己的数据比较不同识别模型的性能，则需要：
1. 分别使用 recognition_01、recognition_02、recognition_03 和 recognition_04 创建四个 PersonGroup   。
1. 使用图像数据来检测人脸，并将这些人脸注册到这四个 PersonGroup 中的 Person 。 
1. 使用 PersonGroup - Train API 训练 PersonGroup。
1. 使用“人脸 - 识别”对全部四个 PersonGroup 都进行测试，然后比较结果。


如果正常指定了置信度阈值（介于 0 与 1 之间的值，确定模型必须有多大的置信度来识别人脸），可能需要对不同的模型使用不同的阈值。 一个模型的阈值不能与另一个模型共享，并且不一定生成相同的结果。

## <a name="next-steps"></a>后续步骤

本文已介绍如何指定要与不同人脸服务 API 配合使用的识别模型。 接下来，请按照快速入门开始使用人脸检测。

* [人脸 .NET SDK](../quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)
* [人脸 Python SDK](../quickstarts/client-libraries.md?pivots=programming-language-python%253fpivots%253dprogramming-language-python)
* [人脸 Go SDK](../quickstarts/client-libraries.md?pivots=programming-language-go%253fpivots%253dprogramming-language-go)

[人脸 - 检测]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[人脸 - 查找相似人脸]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[人脸 - 标识]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Face - Verify]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a（人脸 - 验证）
[PersonGroup - 创建]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[PersonGroup - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup - Train]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup - 创建]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[LargeFaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc
