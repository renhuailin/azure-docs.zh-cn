---
title: 调用检测 API - 人脸
titleSuffix: Azure Cognitive Services
description: 本指南演示如何使用人脸检测功能从给定图像中提取年龄、情绪或头部姿势等特性。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 08/04/2021
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 1fe1cddcbd194ec4b8ca25edfc4907631cb18df1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121744826"
---
# <a name="call-the-detect-api"></a>调用检测 API

本指南演示如何使用人脸检测 API 从给定图像中提取年龄、情绪或头部姿势等特性。 你将了解此 API 行为的不同配置方式以满足你的需求。

本指南中的代码片段是使用 Azure 认知服务人脸客户端库以 C# 编写的。 同样的功能通过 [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) 提供。


## <a name="setup"></a>设置

本指南假设你已使用人脸订阅密钥和终结点 URL 构造了名为 `faceClient` 的 [FaceClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient) 对象。 有关如何设置此功能的说明，请按照其中一个快速入门操作。

## <a name="submit-data-to-the-service"></a>提交服务数据

要找到人脸并获得它们在图像中的位置，请调用 [DetectWithUrlAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync) 或 [DetectWithStreamAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync) 方法。 DetectWithUrlAsync 接受一个 URL 字符串作为输入，DetectWithStreamAsync 接受图像的原始字节流作为输入。

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="basic1":::

可在返回的 [DetectedFace](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface) 对象中查询其唯一 ID，矩形提供了人脸的像素坐标。 这样，你就可以知道哪个人脸 ID 映射到原始图像中的哪个人脸。

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="basic2":::

有关如何分析人脸位置和维度的信息，请参阅 [FaceRectangle](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle)。 通常情况下，此矩形包含眼睛、眉毛、鼻子和嘴巴， 不一定包括头顶、耳朵和下巴。 若要使用人脸矩形来裁剪整个头部或获取中景肖像，应朝每个方向拉伸矩形。

## <a name="determine-how-to-process-the-data"></a>确定如何处理数据

本指南重点介绍有关检测调用的具体信息，例如，可以传递哪些参数，以及可对返回的数据执行哪些操作。 建议仅查询所需功能。 每个操作需要更多时间才能完成。

### <a name="get-face-landmarks"></a>获取人脸特征点

[人脸特征点](../concepts/face-detection.md#face-landmarks)是人脸上的一组易于查找的点，例如瞳孔或鼻尖。 将 detectionModel 参数设置为 `DetectionModel.Detection01`，returnFaceLandmarks 参数设置为 `true`，可获取人脸特征点数据。

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="landmarks1":::

### <a name="get-face-attributes"></a>获取人脸特性

除了人脸矩形和特征点以外，人脸检测 API 还可以分析人脸的几个概念特性。 如需完整列表，请参阅[人脸属性](../concepts/face-detection.md#attributes)概念部分。

要分析人脸特性，将 detectionModel 参数设置为 `DetectionModel.Detection01`，returnFaceAttributes 参数设置为 [FaceAttributeType Enum](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype) 值列表。

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="attributes1":::


## <a name="get-results-from-the-service"></a>获取服务结果

### <a name="face-landmark-results"></a>人脸特征点结果

以下代码演示如何检索鼻子和瞳孔的位置：

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="landmarks2":::

人脸特征点数据还可用于准确计算人脸的方向。 例如，可以将人脸的旋转角定义为从嘴巴中心到眼睛中心的矢量。 以下代码计算此矢量：

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="direction":::

知道人脸的方向后，可以旋转矩形人脸框，使人脸更适当地对齐。 若要裁剪图像中的人脸，可以通过编程方式旋转图像，使人脸始终正面显示。


### <a name="face-attribute-results"></a>人脸特性结果

下面的代码演示如何检索在原始调用中请求的人脸特性数据。

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="attributes2":::

若要详细了解每个属性，请参阅[人脸检测和属性](../concepts/face-detection.md)概念指南。

## <a name="next-steps"></a>后续步骤

本指南介绍了如何使用人脸检测和分析的各项功能。 接下来，将这些功能集成到应用中，以添加用户的人脸数据。

- [教程：向人脸服务添加用户](../enrollment-overview.md)

## <a name="related-articles"></a>相关文章

- [参考文档 (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [参考文档 (.NET SDK)](/dotnet/api/overview/azure/cognitiveservices/face-readme)
