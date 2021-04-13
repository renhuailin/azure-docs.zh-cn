---
title: 为自定义视觉项目选择域 - 计算机视觉
titleSuffix: Azure Cognitive Services
description: 本文介绍如何在自定义视觉服务中为项目选择域。
services: cognitive-services
author: shonohs
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: shono
ms.openlocfilehash: 8aba6f13957d37f843114572f001029baf41ded6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104889342"
---
# <a name="select-a-domain-for-a-custom-vision-project"></a>为自定义视觉项目选择域

可以从自定义视觉项目的设置选项卡中为项目选择域。 请选择最接近你的方案的域。 如果通过客户端库或 REST API 访问自定义视觉，则需要在创建项目时指定域 ID。 可以使用[获取域](https://westus2.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeab)来获取域 ID 的列表，也可以使用下表。

## <a name="image-classification"></a>图像分类

|Domain|目的|
|---|---|
|__常规__| 针对各种图像分类任务进行优化。 如果其他特定域都不合适，或者不确定要选择哪个域，请选择常规域之一。 ID：`ee85a74c-405e-4adc-bb47-ffa8ca0c9f31`|
|常规 [A1]| 经过了优化，可在推理时间与常规域类似的情况下获得更好的准确度。 建议用于较大型的数据集或较困难的用户方案。 此域需要较多的训练时间。 ID：`a8e3c40f-fb4a-466f-832a-5e457ae4a344`|
|常规 [A2]| 经过了优化，可在推理速度比常规 [A1] 域和常规域更快的情况下获得更好的准确度。 建议用于大多数数据集。 此域需要的训练时间比常规域和常规 [A1] 域要少。 ID：`2e37d7fb-3a54-486a-b4d6-cfc369af0018` |
|__食物__|针对餐厅菜肴的照片进行优化。 如果要对各种水果或蔬菜的照片进行分类，请使用“食品”域。 ID：`c151d5b5-dd07-472a-acc8-15d29dea8518`|
|__特征点__|针对可识别的自然和人造地标进行优化。 在照片中的地标清晰可见的情况下，该域效果最佳。 即使照片中的人物稍微遮挡了地标，该域仍然有效。 ID：`ca455789-012d-4b50-9fec-5bb63841c793`|
|__零售__|针对购物目录或购物网站中的图像进行优化。 如果需要对裙装、裤装和衬衫进行非常精准的分类，请使用此域。 ID：`b30a91ae-e3c1-4f73-a81e-c270bff27c39`|
|__压缩域__| 针对边缘设备上实时分类的约束进行了优化。|


> [!NOTE]
> 常规 [A1] 域和常规 [A2] 域可以用于非常广泛的场景，并且在准确度方面进行了优化。 使用常规 [A2] 模型会提高推理速度和缩短训练时间。 对于较大的数据集，你可能需要使用常规 [A1] 来获得比常规 [A2] 更好的准确度，不过这样会需要更多的训练和推理时间。 常规模型需要的推理时间比常规 [A1] 和常规 [A2] 都多。

## <a name="object-detection"></a>对象检测

|Domain|目的|
|---|---|
|__常规__| 已针对各种对象检测任务进行优化。 如果其他域都不合适，或者不确定要选择哪个域，请选择常规域。 ID：`da2e3a8a-40a5-4171-82f4-58522f70fbc1`|
|常规 [A1]| 经过了优化，可在推理时间与常规域类似的情况下获得更好的准确度。 建议用于需要更精确区域定位的情形、更大的数据集，或更困难的用户方案。 此域需要更多的训练时间，并且结果不是确定性的：预期会存在 +-1% 的平均精度均值 (mAP) 差异（与提供的同一批训练数据相比）。 ID：`9c616dff-2e7d-ea11-af59-1866da359ce6`|
|__徽标__|已针对在图像中查找品牌徽标进行优化。 ID：`1d8ffafe-ec40-4fb2-8f90-72b3b6cecea4`|
|__货架上的产品__|已针对检测和分类货架上的产品进行优化。 ID：`3780a898-81c3-4516-81ae-3a139614e1f3`|
|__压缩域__| 针对边缘设备上实时物体检测的约束进行了优化。|

## <a name="compact-domains"></a>压缩域

可导出压缩域生成的模型在本地运行。 在自定义视觉 3.4 公共预览版 API 中，可以通过调用 GetDomains API 来获取压缩域的可导出平台的列表。

模型性能因所选域而异。 在下表中，我们报告了 Intel 桌面设备 CPU 和 NVidia GPU \[1\] 上的模型大小和推理时间。 这些数值未包括预处理和后处理时间。

|任务|Domain|ID|模型大小|CPU 推理时间|GPU 推理时间|
|---|---|---|---|---|---|
|分类|常规(精简)|`0732100f-1a38-4e49-a514-c9b44c697ab5`|6 MB|10 毫秒|5 ms|
|分类|常规（压缩）[S1]|`a1db07ca-a19a-4830-bae8-e004a42dc863`|43 MB|50 毫秒|5 ms|
|对象检测|常规(精简)|`a27d5ca5-bb19-49d8-a70a-fec086c47f5b`|45 MB|35 毫秒|5 ms|
|对象检测|常规（压缩）[S1]|`7ec2ac80-887b-48a6-8df9-8b1357765430`|14 MB|27 毫秒|7 毫秒|

>[!NOTE]
>用于物体检测的常规（压缩）域需要特殊的后处理逻辑。 有关详细信息，请参阅导出的 zip 包中的示例脚本。 如果需要没有后处理逻辑的模型，请使用常规（压缩）[S1]。

>[!IMPORTANT]
>导出的模型不一定会提供与云上的预测 API 完全相同的结果。 运行平台或预处理实现中的细微差异可能会导致模型输出中出现较大差异。 有关预处理逻辑的详细信息，请参阅[此文档](quickstarts/image-classification.md)。

\[1\] Intel Xeon E5-2690 CPU 和 NVIDIA Tesla M60
