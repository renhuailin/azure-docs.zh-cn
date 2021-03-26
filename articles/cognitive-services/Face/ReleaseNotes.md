---
title: 人脸服务中有哪些新功能？
titleSuffix: Azure Cognitive Services
description: 人脸服务的发行说明包括各个版本的版本更改历史记录。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/05/2021
ms.author: pafarley
ms.openlocfilehash: 9293d47d8aade11f4cc3ee55bb32574f125dfafc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102428280"
---
# <a name="whats-new-in-face-service"></a>人脸服务中有哪些新功能？

Azure 人脸服务会不断更新。 使用本文可了解最新的功能增强、修复和文档更新。

## <a name="february-2021"></a>2021 年 2 月

* 新的人脸 API 检测模型：新的“检测 03”模型是当前可用的最准确的检测模型。 如果你是新客户，建议使用此模型。 “检测 03”提高了图像（64x64 像素）中找到的较小人脸的召回率和精准率。 其他改进包括：全面降低了误报、改善了对转动的人脸方向的检测。 将“检测 03”与新的“识别 04”相结合，还可以提高识别准确度。 有关更多详细信息，请参阅[指定人脸检测模型](https://docs.microsoft.com/azure/cognitive-services/face/face-api-how-to-topics/specify-detection-model)。
* 面罩属性：随最新的检测 03 模型提供了面罩属性以及另一个属性 `"noseAndMouthCovered"`，该属性检测是否按预期佩戴了面罩（覆盖了鼻子和嘴）。 若要使用最新的面罩检测功能，用户需要在 API 请求中指定检测模型：通过 _detectionModel_ 参数将模型版本指定为 `detection_03`。 有关更多详细信息，请参阅[指定人脸检测模型](https://docs.microsoft.com/azure/cognitive-services/face/face-api-how-to-topics/specify-detection-model)。
* 新的人脸 API 识别模型：新的“识别 04”模型是当前可用的最准确的识别模型。 如果你是新客户，建议使用此模型进行验证和识别。 它针对“识别 03”的准确度进行了改进，包括改进对佩戴面部遮盖物（外科口罩、N95 口罩、布口罩）的已注册用户的识别。 现在，客户可以构建安全且无缝的用户体验，以便使用最新的“检测 03”模型来检测已注册的用户是否佩戴了面部遮盖物，然后使用最新的“识别 04”模型来识别他们的身份。 有关更多详细信息，请参阅[指定人脸识别模型](https://docs.microsoft.com/azure/cognitive-services/face/face-api-how-to-topics/specify-recognition-model)。


## <a name="january-2021"></a>2021 年 1 月
* 使用人脸 API 时减少延迟：人脸团队发布了一篇新文章，其中详细介绍了使用该服务时的可能的延迟原因和可能的缓解策略。 请参阅[使用人脸服务时减少延迟](./face-api-how-to-topics/how-to-mitigate-latency.md)。

## <a name="december-2020"></a>2020 年 12 月
* 人脸 ID 存储的客户配置：尽管人脸服务不存储客户图像，但已提取的人脸特征将存储在服务器上。 人脸 ID 是人脸特征的标识符，并且将在[人脸 - 识别](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)、[人脸 - 验证](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)和[人脸 - 查找相似人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)中使用。 在进行原始检测调用之后的 24 小时，存储的人脸特征将过期并将会被删除。 客户现在可以决定这些人脸 ID 的缓存时间长度。 最大值仍为 24 小时，但现在可以设置最小值 60 秒。 缓存人脸 ID 的新时间范围是介于 60 秒和 24 小时之间的任何值。 有关详细信息，请参阅 [人脸 - 检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API 参考（*faceIdTimeToLive* 参数）。

## <a name="november-2020"></a>2020 年 11 月
* 发布了一个示例人脸注册应用，用于演示通过高质量注册确立有意义的同意和创建高准确度人脸识别系统的最佳做法。 可在[生成注册应用](build-enrollment-app.md)指南中和 [GitHub](https://github.com/Azure-Samples/cognitive-services-FaceAPIEnrollmentSample) 上找到开放源代码的示例，供开发人员部署或自定义。 

## <a name="august-2020"></a>2020 年 8 月
* 客户管理的静态数据加密：在将数据保存到云时，人脸服务会自动加密你的数据。 人脸服务加密可以保护数据，以便帮助组织履行在安全性与合规性方面做出的承诺。 默认情况下，订阅使用 Microsoft 托管的加密密钥。 还有一个新选项可供你通过你自己的密钥（称为客户管理的密钥 (CMK)）管理订阅。 有关更多详细信息，请参阅[客户管理的密钥](./encrypt-data-at-rest.md)。

## <a name="april-2020"></a>2020 年 4 月
* 新的人脸 API 识别模型：新的“识别 03”模型是当前可用的最准确的模型。 如果你是新客户，建议使用此模型。 “识别 03”将在相似性比较和人员匹配比较方面提供更高的准确性。 有关更多详细信息，请参阅[指定人脸识别模型](./face-api-how-to-topics/specify-recognition-model.md)。

## <a name="june-2019"></a>2019 年 6 月

* 添加了一个新的人脸检测模型，提高了对小脸、侧视图、遮挡和模糊人脸的检测准确性。 通过[人脸 - 检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)、[FaceList - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)、[LargeFaceList - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)、[PersonGroup Person - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)和 [LargePersonGroup Person - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42)来使用它，只需在 `detectionModel` 参数中指定新的人脸检测模型名称 `detection_02` 即可。 [如何指定检测模型](Face-API-How-to-Topics/specify-detection-model.md)中提供了更多详细信息。

## <a name="april-2019"></a>2019 年 4 月

* 提高了 `age` 和 `headPose` 属性的总体准确性。 此外还更新了 `headPose` 属性，`pitch` 值现已启用。 使用这些属性的方法是在[人脸 - 检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` 参数的 `returnFaceAttributes` 参数中指定它们。 

* 提高了[人脸 - 检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)、[FaceList - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)、[LargeFaceList - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)、[PersonGroup 人员 - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)和 [LargePersonGroup 人员 - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42)的速度。

## <a name="march-2019"></a>2019 年 3 月

* 添加了新的人脸识别模型，提高了准确度。 通过[人脸 - 检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)、[FaceList - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b)、[LargeFaceList - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)、[PersonGroup - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)和 [LargePersonGroup - 创建](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)来使用它，只需在 `recognitionModel` 参数中指定新的人脸识别模型名称 `recognition_02` 即可。 [如何指定识别模型](Face-API-How-to-Topics/specify-recognition-model.md)中提供了更多详细信息。

## <a name="january-2019"></a>2019 年 1 月

* 添加了快照功能以支持跨订阅的数据迁移：[快照](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/snapshot-get)。 如需更多详细信息，请参阅[如何将人脸数据迁移到其他人脸订阅](Face-API-How-to-Topics/how-to-migrate-face-data.md)。

## <a name="october-2018"></a>2018 年 10 月

* 在 [PersonGroup - 获取训练状态](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247)、[LargePersonGroup - 获取训练状态](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae32c6ac60f11b48b5aa5)和 [LargeFaceList - 获取训练状态](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a1582f8d2de3616c086f2cf)中完善了对 `status`、`createdDateTime`、`lastActionDateTime` 和 `lastSuccessfulTrainingDateTime` 的说明。

## <a name="may-2018"></a>2018 年 5 月

* 显着改进了 `gender` 属性，还改进了 `age`、`glasses`、`facialHair`、`hair`、`makeup` 属性。 通过[人脸 - 检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` 参数使用这些属性。 

* 在以下部分中将输入图像文件大小限制从 4 MB 增加到了 6 MB：[人脸 - 检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)、[FaceList - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)、[LargeFaceList - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)、[PersonGroup 人员 - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)和 [LargePersonGroup人员 - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42)。

## <a name="march-2018"></a>2018 年 3 月

* 添加了百万规模容器：[LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc) 和 [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)。 有关更多详细信息，请参阅[如何使用大规模功能](Face-API-How-to-Topics/how-to-use-large-scale.md)。

* 将[人脸 - 识别](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) `maxNumOfCandidatesReturned` 参数从 [1, 5] 增加到了 [1, 100]，默认为 10。

## <a name="may-2017"></a>2017 年 5 月

* 在[人脸 - 检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` 参数中添加了 `hair`、`makeup`、`accessory`、`occlusion`、`blur`、`exposure` 和 `noise` 属性。

* 在 PersonGroup 和[人脸 - 识别](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)中支持 10K 人员。

* 在带可选参数 `start` 和 `top` 的 [PersonGroup 人员 - 列表](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241)中支持分页。

* 支持在针对 PersonGroup 中的不同 FaceLists 和不同人员添加/删除人脸时的并发性。

## <a name="march-2017"></a>2017 年 3 月
* 在[人脸 - 检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` 参数中添加了 `emotion` 属性。

* 修复了无法使用从[人脸 - 检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)返回的矩形重新将人脸检测为 [FaceList - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)和 [PersonGroup 人员 - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)中的 `targetFace`。

* 修复了可检测的人脸大小，以确保它严格在 36x36 到 4096x4096 像素之间。

## <a name="november-2016"></a>2016 年 11 月
* 添加了人脸存储标准订阅以在使用 [PersonGroup 人员 - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)或 [FaceList - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)进行标识或相似性匹配时存储额外的持久性人脸。 存储的图像按每 1000 张人脸 0.5 美元收费，每天以此费率按比例计费。 免费层订阅的总人数仍限制为 1,000 人。

## <a name="october-2016"></a>2016 年 10 月
* 在 [FaceList - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)和 [PersonGroup 人员 - 添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)中将 targetFace 中多个人脸的错误消息从“There are more than one face in the image”更改为“There is more than one face in the image”。

## <a name="july-2016"></a>2016 年 7 月
* 支持在[人脸 - 验证](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)中进行人脸到人员对象身份验证。

* 添加了可选的 `mode` 参数，可以选择两种工作模式：[人脸 - 查找相似](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)中的 `matchPerson` 和 `matchFace`，默认值为 `matchPerson`。

* 为用户添加了可选的 `confidenceThreshold` 参数，以设置一个人脸是否属于[人脸 - 识别](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)中的 Person 对象的阈值。

* 在 [PersonGroup - 列表](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395248)中添加了可选的 `start` 和 `top` 参数，使用户能够指定要列出的起点和总 PersonGroup 数。

## <a name="v10-changes-from-v0"></a>从 V0 进行的 V1.0 更改

* 将服务根终结点从 ```https://westus.api.cognitive.microsoft.com/face/v0/``` 更改为 ```https://westus.api.cognitive.microsoft.com/face/v1.0/```。 更改已应用于：[人脸 - 检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)、[人脸 - 识别](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)、[人脸 - 查找相似](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)和[人脸 - 组](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238)。

* 将最小可检测人脸大小更新为 36x36 像素。 将不会检测到小于 36x36 像素的人脸。

* 已弃用人脸 V0 中的 PersonGroup 和 Person 数据。 使用人脸 V1.0 服务无法访问这些数据。

* 于 2016 年 6 月 30 日弃用了人脸 API 的 V0 终结点。