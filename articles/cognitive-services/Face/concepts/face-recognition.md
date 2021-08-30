---
title: 人脸识别的概念
titleSuffix: Azure Cognitive Services
description: 本文介绍人脸识别的概念、其相关操作和基础数据结构。
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pafarley
ms.openlocfilehash: 6f22e48c869ebc2cf4101127f3d87cc7836da35d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747544"
---
# <a name="face-recognition-concepts"></a>人脸识别的概念

本文介绍人脸识别的概念、其相关操作和基础数据结构。 从广义上讲，人脸识别是指使用人脸验证或识别个人的方法。 验证是一对一匹配，即取两张人脸并返回它们是否是同一张脸，而识别是一对多匹配，即取一张人脸作为输入并返回一组匹配的候选项。 人脸识别对于实现身份验证方案非常重要，企业和应用使用该方案来验证（远程）用户的身份。

## <a name="related-data-structures"></a>相关数据结构

识别操作主要使用以下数据结构。 这些对象存储在云中，可按其 ID 字符串引用。 ID 字符串在订阅中始终唯一。 名称字段可以重复。

|名称|说明|
|:--|:--|
|DetectedFace| 此单个人脸表示形式通过[人脸检测](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)操作检索。 其 ID 在创建 24 小时后过期。|
|PersistedFace| 将 DetectedFace 对象添加到某个组（例如 FaceList 或 Person）后，它们将变成 PersistedFace 对象。 它们可以随时[检索](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c)，且不会过期。|
|[FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b) 或 [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)| 此数据结构是 PersistedFace 对象的分类列表。 FaceList 具有唯一的 ID、名称字符串，以及（可选的）用户数据字符串。|
|[Person](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)| 此数据结构是属于同一个人的 PersistedFace 对象列表。 它具有唯一的 ID、名称字符串，以及（可选的）用户数据字符串。|
|[PersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) 或 [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)| 此数据结构是 Person 对象的分类列表。 它具有唯一的 ID、名称字符串，以及（可选的）用户数据字符串。 PersonGroup 必须事先经过[训练](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249)才能在识别操作中使用。|

## <a name="recognition-operations"></a>识别操作

本部分详细介绍基础操作如何使用前面所述的数据结构来识别和验证人脸。

### <a name="persongroup-creation-and-training"></a>PersonGroup 创建和训练

需要创建 [PersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) 或 [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d) 来存储要匹配的人员集。 PersonGroup 保存 [Person](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) 对象，每个对象表示单个人员，并保存属于该人员的一组人脸数据。

[训练](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249)操作准备要用于人脸数据比较的数据集。

### <a name="identification"></a>标识

[识别](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)操作采用一个或多个源人脸 ID（来自 DetectedFace 或 PersistedFace 对象）和一个 PersonGroup 或 LargePersonGroup。 它返回每个源人脸可能属于的 Person 对象的列表。 返回的 Person 对象包装为具有预测置信度值的 Candidate 对象。


### <a name="verification"></a>验证

[验证](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)操作采用单个人脸 ID（来自 DetectedFace 或 PersistedFace 对象）和一个 Person 对象。 它确定人脸是否属于同一人员。 验证是一对一匹配，可用于对识别 API 调用的结果进行最终检查。 但是，可以选择传入候选 Person 所属的 PersonGroup 以提高 API 性能。


## <a name="input-data"></a>输入数据

使用以下提示来确保输入的图像提供最准确的识别结果：

* 支持的输入图像格式为 JPEG、PNG、GIF（第一帧）和 BMP。
* 图像文件不得大于 6 MB。
* 创建 Person 对象时，请使用具有不同类型的角度和照明效果的照片。
* 某些人脸会因技术难题而识别不到，例如：
  * 具有极端照明（例如严重的背光）的图像。
  * 有障碍物挡住了一只或两只眼睛。
  * 发型或胡须的差异。
  * 年龄使面貌发生变化。
  * 极端的面部表情。

## <a name="next-steps"></a>后续步骤

熟悉人脸识别的概念后，接下来请编写一个可以针对训练的 PersonGroup 识别人脸的脚本。

* [人脸客户端库快速入门](../Quickstarts/client-libraries.md)