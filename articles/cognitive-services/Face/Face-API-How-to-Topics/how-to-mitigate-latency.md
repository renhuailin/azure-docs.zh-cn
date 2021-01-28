---
title: 如何在使用人脸服务时减少延迟
titleSuffix: Azure Cognitive Services
description: 了解如何在使用人脸服务时减少延迟。
services: cognitive-services
author: v-jaswel
manager: chrhoder
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 1/5/2021
ms.author: v-jawe
ms.openlocfilehash: e7f4293955772697ddeea5fce9daac4b04755274
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98937290"
---
# <a name="how-to-mitigate-latency-when-using-the-face-service"></a>如何：在使用人脸服务时减少延迟

使用人脸服务时可能会遇到延迟。 延迟是指在通过网络进行通信时发生的任何类型的延迟。 通常情况下，延迟的可能原因包括：
- 每个数据包必须从源传输到目标的物理距离。
- 传输媒体出现问题。
- 路由器或交换机中的错误沿传输路径。
- 防病毒应用程序、防火墙和其他安全机制检查数据包所需的时间。
- 客户端或服务器应用程序中的故障。

本主题讨论了使用 Azure 认知服务时特定延迟的可能原因，以及如何缓解这些原因。

> [!NOTE]
> Azure 认知服务不会提供任何服务级别协议 (SLA) 与延迟有关。

## <a name="possible-causes-of-latency"></a>延迟的可能原因

### <a name="slow-connection-between-the-cognitive-service-and-a-remote-url"></a>认知服务与远程 URL 之间的慢速连接

某些 Azure 认知服务提供了从提供的远程 URL 中获取数据的方法。 例如，当您调用人脸服务的 [DetectWithUrlAsync 方法](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync#Microsoft_Azure_CognitiveServices_Vision_Face_FaceOperationsExtensions_DetectWithUrlAsync_Microsoft_Azure_CognitiveServices_Vision_Face_IFaceOperations_System_String_System_Nullable_System_Boolean__System_Nullable_System_Boolean__System_Collections_Generic_IList_System_Nullable_Microsoft_Azure_CognitiveServices_Vision_Face_Models_FaceAttributeType___System_String_System_Nullable_System_Boolean__System_String_System_Threading_CancellationToken_) 时，可以指定该服务尝试检测人脸的图像的 URL。

```csharp
var faces = await client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1MzAyNzYzOTgxNTE0NTEz/john-f-kennedy---mini-biography.jpg");
```

然后，面部服务必须从远程服务器下载映像。 如果从人脸服务到远程服务器的连接速度较慢，将影响检测方法的响应时间。

若要缓解此情况，请考虑 [将映像存储在 Azure 高级 Blob 存储中](https://docs.microsoft.com/azure/storage/blobs/storage-upload-process-images?tabs=dotnet)。 例如：

``` csharp
var faces = await client.Face.DetectWithUrlAsync("https://csdx.blob.core.windows.net/resources/Face/Images/Family1-Daughter1.jpg");
```

### <a name="large-upload-size"></a>大上载大小

某些 Azure 认知服务提供了从上传的文件中获取数据的方法。 例如，当你调用人脸服务的 [DetectWithStreamAsync 方法](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync#Microsoft_Azure_CognitiveServices_Vision_Face_FaceOperationsExtensions_DetectWithStreamAsync_Microsoft_Azure_CognitiveServices_Vision_Face_IFaceOperations_System_IO_Stream_System_Nullable_System_Boolean__System_Nullable_System_Boolean__System_Collections_Generic_IList_System_Nullable_Microsoft_Azure_CognitiveServices_Vision_Face_Models_FaceAttributeType___System_String_System_Nullable_System_Boolean__System_String_System_Threading_CancellationToken_) 时，你可以上传服务试图检测人脸的映像。

```csharp
using FileStream fs = File.OpenRead(@"C:\images\face.jpg");
System.Collections.Generic.IList<DetectedFace> faces = await client.Face.DetectWithStreamAsync(fs, detectionModel: DetectionModel.Detection02);
```

如果要上传的文件很大，将会影响该方法的响应时间 `DetectWithStreamAsync` ，原因如下：
- 需要较长的时间来上传文件。
- 它需要更长的时间来处理文件，与文件大小成正比。

缓解措施：
- 请考虑 [在 Azure 高级 Blob 存储中存储图像](https://docs.microsoft.com/azure/storage/blobs/storage-upload-process-images?tabs=dotnet)。 例如：
``` csharp
var faces = await client.Face.DetectWithUrlAsync("https://csdx.blob.core.windows.net/resources/Face/Images/Family1-Daughter1.jpg");
```
- 请考虑上载较小的文件。
    - 请参阅有关人 [脸检测的输入数据](https://docs.microsoft.com/azure/cognitive-services/face/concepts/face-detection#input-data) 和人 [脸识别的输入数据](https://docs.microsoft.com/azure/cognitive-services/face/concepts/face-recognition#input-data)的准则。
    - 对于面部检测，使用检测模型时 `DetectionModel.Detection01` ，减少图像文件大小会提高处理速度。 使用检测模型时 `DetectionModel.Detection02` ，如果图像文件小于1920x1080，则减小图像文件大小仅会提高处理速度。
    - 对于面部识别，将字体大小减小到200x200 大小像素不会影响识别模式的准确性。
    - 和方法的性能 `DetectWithUrlAsync` `DetectWithStreamAsync` 也取决于图像中的人脸。 人脸服务最多可为图像返回100面。 面部按从大到小的面部矩形大小进行排序。
    - 如果需要调用多个服务方法，请考虑在应用程序设计允许时并行调用这些方法。 例如，如果您需要检测两个图像中的人脸来执行人脸比较：
```csharp
var faces_1 = client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1MzAyNzYzOTgxNTE0NTEz/john-f-kennedy---mini-biography.jpg");
var faces_2 = client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1NDY3OTIxMzExNzM3NjE3/john-f-kennedy---debating-richard-nixon.jpg");
Task.WaitAll (new Task<IList<DetectedFace>>[] { faces_1, faces_2 });
IEnumerable<DetectedFace> results = faces_1.Result.Concat (faces_2.Result);
```

### <a name="slow-connection-between-your-compute-resource-and-the-face-service"></a>计算资源与面部服务之间的连接缓慢

如果你的计算机连接到面部服务时速度较慢，则会影响服务方法的响应时间。

缓解措施：
- 创建面部订阅时，请确保选择最接近你的应用程序托管位置的区域。
- 如果需要调用多个服务方法，请考虑在应用程序设计允许时并行调用这些方法。 有关示例，请参阅上一节。

## <a name="next-steps"></a>后续步骤

本指南介绍了如何在使用人脸服务时减少延迟。 接下来，了解如何分别从现有的 Person group 和 FaceList 对象扩展到 LargePersonGroup 和 LargeFaceList 对象。

> [!div class="nextstepaction"]
> [示例：使用大规模使用的功能](how-to-use-large-scale.md)

## <a name="related-topics"></a>相关主题

- [参考文档 (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [参考文档 (.NET SDK)](/dotnet/api/overview/azure/cognitiveservices/client/faceapi)
