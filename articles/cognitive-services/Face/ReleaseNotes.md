---
title: Azure 人脸服务有哪些新增功能？
titleSuffix: Azure Cognitive Services
description: 随时了解 Azure 人脸服务的最新版本和更新。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 09/27/2021
ms.author: pafarley
ms.custom: contperf-fy21q3, contperf-fy22q1
ms.openlocfilehash: f727846a13566832d8549b5fa76ae5011a1be2ee
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129351856"
---
# <a name="whats-new-in-azure-face-service"></a>Azure 人脸服务有哪些新增功能？

Azure 人脸服务会不断更新。 使用本文了解最新的功能、增强功能、修复和文档更新。

## <a name="april-2021"></a>2021 年 4 月

### <a name="persondirectory-data-structure"></a>PersonDirectory 数据结构

* 为执行人脸识别操作（如识别和查找相似对象），人脸 API 客户需要创建一份 Person 对象的分类列表。 新的 PersonDirectory 是一种数据结构，其中包含添加到目录的每个人员标识的唯一 ID、可选名称字符串和可选用户元数据字符串 。 目前，人脸 API 提供 LargePersonGroup 结构，该结构具有相似功能，但仅限添加 100,0000 个标识。 PersonDirectory 结构最多可扩展到 750,00000 个标识。 
* PersonDirectory 和之前的数据结构之间还存在一个重大差异，即在将人脸添加到 Person 对象后，便无需再执行任何定型调用&mdash;因为更新进程会自动发生 。 有关更多详细信息，请参阅[使用 PersonDirectory 结构](Face-API-How-to-Topics/use-persondirectory.md)。


## <a name="february-2021"></a>2021 年 2 月

### <a name="new-face-api-detection-model"></a>新的人脸 API 检测模型
* 新的“检测 03”模型是当前可用的最准确的检测模型。 如果你是新客户，建议使用此模型。 “检测 03”提高了图像（64x64 像素）中找到的较小人脸的召回率和精准率。 其他改进包括：全面降低了误报、改善了对转动的人脸方向的检测。 将“检测 03”与新的“识别 04”模型相结合，还可以提高识别准确度。 有关更多详细信息，请参阅[指定人脸检测模型](./face-api-how-to-topics/specify-detection-model.md)。
### <a name="new-detectable-face-attributes"></a>新的可检测人脸属性
* 随最新的“检测 03”模型提供了 `faceMask` 属性以及另一个属性 `"noseAndMouthCovered"`（用于检测是否按预期佩戴了面罩，覆盖了鼻子和嘴）。 若要使用最新的面罩检测功能，用户需要在 API 请求中指定检测模型：通过 _detectionModel_ 参数将模型版本指定为 `detection_03`。 有关更多详细信息，请参阅[指定人脸检测模型](./face-api-how-to-topics/specify-detection-model.md)。
### <a name="new-face-api-recognition-model"></a>新的人脸 API 识别模型
* 新的“识别 04”模型是当前可用的最准确的识别模型。 如果你是新客户，建议使用此模型进行验证和识别。 它针对“识别 03”的准确度进行了改进，包括改进对佩戴面部遮盖物（外科口罩、N95 口罩、布口罩）的已注册用户的识别。 现在，客户可以构建安全且无缝的用户体验，以便使用最新的“检测 03”模型来检测已注册的用户是否佩戴了面部遮盖物，然后使用最新的“识别 04”模型来识别他们的身份。 有关更多详细信息，请参阅[指定人脸识别模型](./face-api-how-to-topics/specify-recognition-model.md)。


## <a name="january-2021"></a>2021 年 1 月
### <a name="mitigate-latency"></a>缓解延迟
* 人脸团队发布了一篇新文章，其中详细介绍了使用该服务时的可能的延迟原因和可能的缓解策略。 请参阅[使用人脸服务时减少延迟](./face-api-how-to-topics/how-to-mitigate-latency.md)。

## <a name="december-2020"></a>2020 年 12 月
### <a name="customer-configuration-for-face-id-storage"></a>人脸 ID 存储的客户配置
* 尽管人脸服务不存储客户图像，但已提取的人脸特征将存储在服务器上。 人脸 ID 是人脸特征的标识符，并且将在[人脸 - 识别](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)、[人脸 - 验证](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)和[人脸 - 查找相似人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)中使用。 在进行原始检测调用之后的 24 小时，存储的人脸特征将过期并将会被删除。 客户现在可以决定这些人脸 ID 的缓存时间长度。 最大值仍为 24 小时，但现在可以设置最小值 60 秒。 缓存人脸 ID 的新时间范围是介于 60 秒和 24 小时之间的任何值。 有关详细信息，请参阅 [人脸 - 检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API 参考（*faceIdTimeToLive* 参数）。

## <a name="november-2020"></a>2020 年 11 月
### <a name="sample-face-enrollment-app"></a>示例人脸注册应用
* 该团队发布了一个示例人脸注册应用，用于演示通过高质量注册确立有意义的同意和创建高准确度人脸识别系统的最佳做法。 可在[生成注册应用](build-enrollment-app.md)指南中和 [GitHub](https://github.com/Azure-Samples/cognitive-services-FaceAPIEnrollmentSample) 上找到开放源代码的示例，供开发人员部署或自定义。 

## <a name="august-2020"></a>2020 年 8 月
### <a name="customer-managed-encryption-of-data-at-rest"></a>客户管理的静态数据加密
* 人脸服务在将数据保存到云时会自动加密数据。 人脸服务加密可以保护数据，以便帮助组织履行在安全性与合规性方面做出的承诺。 默认情况下，订阅使用 Microsoft 托管的加密密钥。 还有一个新选项可供你通过你自己的密钥（称为客户管理的密钥 (CMK)）管理订阅。 有关更多详细信息，请参阅[客户管理的密钥](./encrypt-data-at-rest.md)。

## <a name="april-2020"></a>2020 年 4 月
### <a name="new-face-api-recognition-model"></a>新的人脸 API 识别模型
* 新的“识别 03”模型是当前可用的最准确的模型。 如果你是新客户，建议使用此模型。 “识别 03”将在相似性比较和人员匹配比较方面提供更高的准确性。 有关更多详细信息，请参阅[指定人脸识别模型](./face-api-how-to-topics/specify-recognition-model.md)。

## <a name="june-2019"></a>2019 年 6 月

### <a name="new-face-api-detection-model"></a>新的人脸 API 检测模型
* 新的“检测 02”模型功能提高了较小的、侧面的、遮蔽的和模糊的人脸的检测准确度。 通过[人脸 - 检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)、[FaceList - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)、[LargeFaceList - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)、[PersonGroup Person - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)和 [LargePersonGroup Person - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42)来使用它，只需在 `detectionModel` 参数中指定新的人脸检测模型名称 `detection_02` 即可。 [如何指定检测模型](Face-API-How-to-Topics/specify-detection-model.md)中提供了更多详细信息。

## <a name="april-2019"></a>2019 年 4 月

### <a name="improved-attribute-accuracy"></a>提高了属性准确度
* 提高了 `age` 和 `headPose` 属性的总体准确性。 此外还更新了 `headPose` 属性，`pitch` 值现已启用。 使用这些属性的方法是在[人脸 - 检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` 参数的 `returnFaceAttributes` 参数中指定它们。 
### <a name="improved-processing-speeds"></a>提高了处理速度
* 提高了[人脸 - 检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)、[FaceList - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)、[LargeFaceList - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)、[PersonGroup 人员 - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)和 [LargePersonGroup 人员 - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42)操作的速度。

## <a name="march-2019"></a>2019 年 3 月

### <a name="new-face-api-recognition-model"></a>新的人脸 API 识别模型
* “识别 02”模型的准确度已提高。 通过[人脸 - 检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)、[FaceList - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b)、[LargeFaceList - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)、[PersonGroup - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)和 [LargePersonGroup - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)来使用它，只需在 `recognitionModel` 参数中指定新的人脸识别模型名称 `recognition_02` 即可。 [如何指定识别模型](Face-API-How-to-Topics/specify-recognition-model.md)中提供了更多详细信息。

## <a name="january-2019"></a>2019 年 1 月

### <a name="face-snapshot-feature"></a>人脸快照功能
* 此功能使服务可以支持跨订阅进行数据迁移：[快照](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/snapshot-get)。 如需更多详细信息，请参阅[如何将人脸数据迁移到其他人脸订阅](Face-API-How-to-Topics/how-to-migrate-face-data.md)。

## <a name="october-2018"></a>2018 年 10 月

### <a name="api-messages"></a>API 消息
* 在 [PersonGroup - 获取训练状态](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247)、[LargePersonGroup - 获取训练状态](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae32c6ac60f11b48b5aa5)和 [LargeFaceList - 获取训练状态](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a1582f8d2de3616c086f2cf)中完善了对 `status`、`createdDateTime`、`lastActionDateTime` 和 `lastSuccessfulTrainingDateTime` 的说明。

## <a name="may-2018"></a>2018 年 5 月

### <a name="improved-attribute-accuracy"></a>提高了属性准确度
* 显着改进了 `gender` 属性，还改进了 `age`、`glasses`、`facialHair`、`hair`、`makeup` 属性。 通过[人脸 - 检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` 参数使用这些属性。
### <a name="increased-file-size-limit"></a>增加了文件大小限制
* 在以下部分中将输入图像文件大小限制从 4 MB 增加到了 6 MB：[人脸 - 检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)、[FaceList - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)、[LargeFaceList - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)、[PersonGroup 人员 - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)和 [LargePersonGroup人员 - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42)。

## <a name="march-2018"></a>2018 年 3 月

### <a name="new-data-structure"></a>新的数据结构
* [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc) 和 [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)。 有关更多详细信息，请参阅[如何使用大规模功能](Face-API-How-to-Topics/how-to-use-large-scale.md)。
* 将[人脸 - 识别](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) `maxNumOfCandidatesReturned` 参数从 [1, 5] 增加到了 [1, 100]，默认为 10。

## <a name="may-2017"></a>2017 年 5 月

### <a name="new-detectable-face-attributes"></a>新的可检测人脸属性
* 在[人脸 - 检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` 参数中添加了 `hair`、`makeup`、`accessory`、`occlusion`、`blur`、`exposure` 和 `noise` 属性。
* 在 PersonGroup 和[人脸 - 识别](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)中支持 10K 人员。
* 在带可选参数 `start` 和 `top` 的 [PersonGroup 人员 - 列表](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241)中支持分页。
* 支持在针对 PersonGroup 中的不同 FaceLists 和不同人员添加/删除人脸时的并发性。

## <a name="march-2017"></a>2017 年 3 月

### <a name="new-detectable-face-attribute"></a>新的可检测人脸属性
* 在[人脸 - 检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` 参数中添加了 `emotion` 属性。
### <a name="fixed-issues"></a>已修复的问题
* 无法使用从[人脸 - 检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)返回的矩形重新将人脸检测为 [FaceList - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)和 [PersonGroup 人员 - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)中的 `targetFace`。
* 设置了可检测的人脸大小，以确保它一定在 36x36 到 4096x4096 像素之间。

## <a name="november-2016"></a>2016 年 11 月
### <a name="new-subscription-tier"></a>新的订阅层
* 添加了人脸存储标准订阅以在使用 [PersonGroup 人员 - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)或 [FaceList - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)进行标识或相似性匹配时存储额外的持久性人脸。 存储的图像按每 1000 张人脸 0.5 美元收费，每天以此费率按比例计费。 免费层订阅的总人数仍限制为 1,000 人。

## <a name="october-2016"></a>2016 年 10 月
### <a name="api-messages"></a>API 消息
* 在 [FaceList - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)和 [PersonGroup 人员 - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)中将 `targetFace` 中多个人脸的错误消息从“There are more than one face in the image”更改为“There is more than one face in the image”。

## <a name="july-2016"></a>2016 年 7 月
### <a name="new-features"></a>新增功能
* 支持在[人脸 - 验证](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)中进行人脸到人员对象身份验证。
* 添加了可选的 `mode` 参数，可以选择两种工作模式：[人脸 - 查找相似](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)中的 `matchPerson` 和 `matchFace`，默认值为 `matchPerson`。
* 为用户添加了可选的 `confidenceThreshold` 参数，以设置一个人脸是否属于[人脸 - 识别](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)中的 Person 对象的阈值。
* 在 [PersonGroup - 列表](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395248)中添加了可选的 `start` 和 `top` 参数，使用户能够指定要列出的起点和总 PersonGroup 数。

## <a name="v10-changes-from-v0"></a>从 V0 进行的 V1.0 更改

* 将服务根终结点从 ```https://westus.api.cognitive.microsoft.com/face/v0/``` 更改为 ```https://westus.api.cognitive.microsoft.com/face/v1.0/```。 更改已应用于：[人脸 - 检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)、[人脸 - 识别](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)、[人脸 - 查找相似](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)和[人脸 - 组](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238)。
* 将最小可检测人脸大小更新为 36x36 像素。 将不会检测到小于 36x36 像素的人脸。
* 已弃用人脸 V0 中的 PersonGroup 和 Person 数据。 使用人脸 V1.0 服务无法访问这些数据。
* 于 2016 年 6 月 30 日弃用了人脸 API 的 V0 终结点。