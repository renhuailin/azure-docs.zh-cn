---
title: 人脸检测和属性的概念
titleSuffix: Azure Cognitive Services
description: 人脸检测是在图像中查找人脸，并选择地返回不同类型人脸相关数据的操作。
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: pafarley
ms.openlocfilehash: 3fe90f5c9038c37e3ac3e9fba357ea27ca089679
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747556"
---
# <a name="face-detection-and-attributes"></a>人脸检测和属性

本文解释人脸检测和人脸属性数据的概念。 人脸检测是在图像中查找人脸，并选择地返回不同类型人脸相关数据的操作。

可以使用[人脸 - 检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)操作在图像中检测人脸。 最起码，检测到的每个人脸对应于响应中的 faceRectangle 字段。 这组像素坐标（左边缘、顶部、宽度、高度）标记所找到的人脸。 使用这些坐标可以获取人脸的位置及其大小。 在 API 响应中，人脸按照从大到小的顺序列出。

## <a name="face-id"></a>人脸 ID

人脸 ID 是在图像中检测到的每个人脸的唯一标识符字符串。 可以在[人脸 - 检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API 调用中请求人脸 ID。

## <a name="face-landmarks"></a>人脸特征点

人脸特征点是人脸上的一组易于查找的点，例如瞳孔或鼻尖。 默认情况下，有 27 个预定义的特征点。 下图显示了所有 27 个点：

![标有所有 27 个特征点的人脸插图](../Images/landmarks.1.jpg)

以像素为单位返回的点坐标。

## <a name="attributes"></a>属性

属性是可由[人脸 - 检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API 选择性地检测到的一组特征。 以下属性可以检测到：

* 配饰。 给定的人脸是否戴有配饰。 此属性会返回可能的配饰，包括头饰、眼镜和口罩，每个配饰的置信度分数介于 0 到 1 之间。
* **Age**： 特定人脸的估计年龄（岁）。
* **Blur**： 图像中人脸的模糊度。 此属性返回 0 到 1 的值，以及非正式分级：low、medium 或 high。
* **Emotion**： 给定人脸的情感列表及其检测置信度。 置信度分数会进行标准化，所有情感的分数加起来后得到一个总的分数。 返回的情感包括快乐、悲伤、中性、愤怒、蔑视、厌恶、惊讶、恐惧。
* **Exposure**： 图像中人脸的曝光度。 此属性返回 0 到 1 的值，以及非正式的分级：underExposure、goodExposure 或 overExposure。
* **Facial hair**： 给定人脸的胡须状态和长度。
* **Gender**： 给定人脸的估计性别。 可能的值为 male、female 和 genderless。
* **Glasses**： 给定的人脸是否戴有眼镜。 可能的值为 NoGlasses、ReadingGlasses、Sunglasses 和 Swimming Goggles。
* **Hair**： 人脸的发型。 此属性显示头发是否可见、是否检测到秃顶，以及检测到了哪种发色。
* **Head pose**： 人脸在 3D 空间中的摆向。 此属性由按[右手规则](https://en.wikipedia.org/wiki/Right-hand_rule)定义的翻滚角、偏航角和俯仰角（以度为单位）描述。 三角度的顺序是翻滚角-偏航角-俯仰角，每个角度的值范围是从 -180 度到 180 度。 按翻滚角、偏航角、俯仰角的顺序估算面部的三维朝向。 有关角度映射，请参阅以下示意图：

    ![标有俯仰、翻滚和偏航轴的头部](../Images/headpose.1.jpg)
* **Makeup**： 人脸是否有化妆。 此值返回 eyeMakeup 和 lipMakeup 的布尔值。
* 口罩。  人脸是否佩戴口罩。 此属性会返回可能的口罩类型，以及一个布尔值，用于指示鼻子和嘴巴是否被覆盖。
* **Noise**： 在人脸图像中检测到的视觉噪点。 此属性返回 0 到 1 的值，以及非正式分级：low、medium 或 high。
* **Occlusion**： 是否存在遮挡人脸部位的物体。 此属性返回 eyeOccluded、foreheadOccluded 和 mouthOccluded 的布尔值。
* **Smile**： 给定人脸的微笑表情。 此值介于 0（未微笑）与 1（明确的微笑）之间。

> [!IMPORTANT]
> 人脸属性是使用统计算法预测的， 不一定准确。 根据特性数据做出决策时请小心。

## <a name="input-data"></a>输入数据

使用以下提示来确保输入的图像提供最准确的检测结果：

* 支持的输入图像格式为 JPEG、PNG、GIF（第一帧）和 BMP。
* 图像文件不得大于 6 MB。
* 在不大于 1920 x 1080 像素的图像中，最小可检测人脸大小是 36 x 36 像素。 在大于 1920 x 1080 像素的图像中，最小人脸大小相应更大。 减小人脸大小可能会导致无法检测到某些人脸，即使它们大于最小可检测人脸大小也是如此。
* 最大可检测人脸大小为 4096 x 4096 像素。
* 大小在 36 x 36 至 4096 x 4096 像素大小范围之外的人脸将不会被检测到。
* 某些人脸会因技术难题而检测不到。 极端的人脸角度（头部姿势）或人脸遮挡物（太阳镜或遮挡人脸部位的手等物体）可能会影响检测。 正面和接近正面的人脸可提供最佳结果。

输入包含方向信息的数据：
* JPEG 格式的某些输入图像可能包含可交换图像文件格式 (Exif) 的方向信息元数据。 如果 Exif 方向可用，则在发送面部检测之前，图像将自动旋转到正确的方向。 将基于旋转的图像估算检测到的每张人脸的面部矩形、特征点和头部姿势。
* 若要正确显示人脸矩形和特征点，需要确保图像旋转正确。 默认情况下，大多数图像可视化工具会根据其 Exif 方向自动旋转图像。 对于其他工具，可能需要使用自己的代码来应用旋转。 下面的示例演示旋转图像（左）和位旋转图像（右）上的面部矩形。

![带/不带旋转的两面图像](../Images/image-rotation.png)

若要检测视频源中的人脸，则可通过调整视频摄像头上的某些设置来改进性能：

* **平滑处理**：许多视频摄像头应用平滑处理效果。 在可能的情况下，应将此关闭，因为它会在帧之间产生模糊，降低清晰度。
* **快门速度**：更快的快门速度会减少帧之间的移动量，使每个帧更清晰。 建议将快门速度设置为 1/60 秒或更快。
* **快门角度**：某些摄像头指定快门角度而不是快门速度。 应该尽可能使用较低的快门角度。 这会增加视频帧的清晰度。

    >[!NOTE]
    > 摄像头的快门角度较低时，每个帧收到的光线较少，因此图像会更黑。 需确定适合使用的级别。

## <a name="next-steps"></a>后续步骤

熟悉人脸检测的概念后，接下来请了解如何编写一个可在给定图像中检测人脸的脚本。

* [调用检测 API](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
