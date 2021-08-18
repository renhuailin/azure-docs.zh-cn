---
title: 使用人脸服务时如何降低延迟
titleSuffix: Azure Cognitive Services
description: 了解使用人脸服务时如何降低延迟。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 1/5/2021
ms.author: pafarley
ms.openlocfilehash: f4d3e61fdfc629a0d9051a066fd861d3e8013e25
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121744770"
---
# <a name="how-to-mitigate-latency-when-using-the-face-service"></a>操作说明：使用人脸服务时降低延迟

使用人脸服务时你可能会遇到延迟。 延迟是指通过网络通信时发生的任何类型的耽搁。 通常情况下，延迟的可能原因包括：
- 每个数据包从源传输到目标时必须通过的物理距离过长。
- 传输介质出现问题。
- 传输路径中的路由器或交换机出现错误。
- 防病毒应用程序、防火墙和其他安全机制检查数据包需要一定的时间。
- 客户端或服务器应用程序中出现故障。

本主题讨论了使用 Azure 认知服务时出现延迟的可能原因，以及如何缓解这些原因。

> [!NOTE]
> Azure 认知服务未提供与延迟有关的任何服务级别协议 (SLA)。

## <a name="possible-causes-of-latency"></a>延迟的可能原因

### <a name="slow-connection-between-the-cognitive-service-and-a-remote-url"></a>认知服务与远程 URL 之间的连接速度缓慢

某些 Azure 认知服务提供了从你提供的远程 URL 获取数据的方法。 例如，当调用人脸服务的 [DetectWithUrlAsync 方法](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync#Microsoft_Azure_CognitiveServices_Vision_Face_FaceOperationsExtensions_DetectWithUrlAsync_Microsoft_Azure_CognitiveServices_Vision_Face_IFaceOperations_System_String_System_Nullable_System_Boolean__System_Nullable_System_Boolean__System_Collections_Generic_IList_System_Nullable_Microsoft_Azure_CognitiveServices_Vision_Face_Models_FaceAttributeType___System_String_System_Nullable_System_Boolean__System_String_System_Threading_CancellationToken_)时，你可以指定该服务尝试在其中检测人脸的图像的 URL。

```csharp
var faces = await client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1MzAyNzYzOTgxNTE0NTEz/john-f-kennedy---mini-biography.jpg");
```

然后，人脸服务必须从远程服务器下载该图像。 如果从人脸服务到远程服务器的连接速度缓慢，则会影响 Detect 方法的响应时间。

若要缓解此情况，请考虑[将图像存储在 Azure 高级 Blob 存储](../../../storage/blobs/storage-upload-process-images.md?tabs=dotnet)中。 例如：

``` csharp
var faces = await client.Face.DetectWithUrlAsync("https://csdx.blob.core.windows.net/resources/Face/Images/Family1-Daughter1.jpg");
```

### <a name="large-upload-size"></a>上传大小很大

某些 Azure 认知服务提供了从你上传的文件获取数据的方法。 例如，当调用人脸服务的 [DetectWithStreamAsync 方法](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync#Microsoft_Azure_CognitiveServices_Vision_Face_FaceOperationsExtensions_DetectWithStreamAsync_Microsoft_Azure_CognitiveServices_Vision_Face_IFaceOperations_System_IO_Stream_System_Nullable_System_Boolean__System_Nullable_System_Boolean__System_Collections_Generic_IList_System_Nullable_Microsoft_Azure_CognitiveServices_Vision_Face_Models_FaceAttributeType___System_String_System_Nullable_System_Boolean__System_String_System_Threading_CancellationToken_)时，你可以上传该服务尝试在其中检测人脸的图像。

```csharp
using FileStream fs = File.OpenRead(@"C:\images\face.jpg");
System.Collections.Generic.IList<DetectedFace> faces = await client.Face.DetectWithStreamAsync(fs, detectionModel: DetectionModel.Detection02);
```

如果要上传的文件很大，则会影响 `DetectWithStreamAsync` 方法的响应时间，原因如下：
- 上传文件需要花费较长的时间。
- 服务需要花费较长的时间（与文件大小成正比）来处理文件。

缓解措施：
- 请考虑[将图像存储在 Azure 高级 Blob 存储中](../../../storage/blobs/storage-upload-process-images.md?tabs=dotnet)。 例如：
``` csharp
var faces = await client.Face.DetectWithUrlAsync("https://csdx.blob.core.windows.net/resources/Face/Images/Family1-Daughter1.jpg");
```
- 考虑上传较小的文件。
    - 请参阅有关[用于人脸检测的输入数据](../concepts/face-detection.md#input-data)和[用于人脸识别的输入数据](../concepts/face-recognition.md#input-data)的准则。
    - 对于人脸检测，使用检测模型 `DetectionModel.Detection01` 时，减小图像文件大小会提高处理速度。 如果使用检测模型 `DetectionModel.Detection02`，则只有当图像文件小于 1920x1080 时，减小图像文件大小才会提高处理速度。
    - 对于人脸识别，将人脸大小减小到 200x200 像素不会影响识别模型的准确度。
    - `DetectWithUrlAsync` 和 `DetectWithStreamAsync` 方法的性能还取决于图像中有多少人脸。 对于每个图像，人脸服务最多可以返回 100 张人脸。 人脸将根据人脸矩形大小按从大到小的顺序排序。
    - 如果你需要调用多个服务方法，请考虑并行调用这些方法（如果你的应用程序设计允许这样做）。 例如，如果你需要检测两个图像中的人脸进行人脸比较：
```csharp
var faces_1 = client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1MzAyNzYzOTgxNTE0NTEz/john-f-kennedy---mini-biography.jpg");
var faces_2 = client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1NDY3OTIxMzExNzM3NjE3/john-f-kennedy---debating-richard-nixon.jpg");
Task.WaitAll (new Task<IList<DetectedFace>>[] { faces_1, faces_2 });
IEnumerable<DetectedFace> results = faces_1.Result.Concat (faces_2.Result);
```

### <a name="slow-connection-between-your-compute-resource-and-the-face-service"></a>你的计算资源与人脸服务之间的连接速度缓慢

如果你的计算机到人脸服务的连接速度缓慢，则会影响服务方法的响应时间。

缓解措施：
- 创建人脸订阅时，请确保选择最靠近应用程序托管位置的区域。
- 如果你需要调用多个服务方法，请考虑并行调用这些方法（如果你的应用程序设计允许这样做）。 有关示例，请参阅上一部分。
- 如果长时间延迟影响用户体验，请在重试 API 调用之前选择超时阈值（例如最长 5 秒）。

## <a name="next-steps"></a>后续步骤

在本指南中，你已了解使用人脸服务时如何降低延迟。 接下来，请了解如何从现有 PersonGroup 和 FaceList 对象分别纵向扩展到 LargePersonGroup 和 LargeFaceList 对象。

> [!div class="nextstepaction"]
> [示例：使用大规模使用的功能](how-to-use-large-scale.md)

## <a name="related-topics"></a>相关主题

- [参考文档 (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [参考文档 (.NET SDK)](/dotnet/api/overview/azure/cognitiveservices/face-readme)
