---
title: 为自定义视觉项目选择域-计算机视觉
titleSuffix: Azure Cognitive Services
description: 本文将演示如何在自定义影像服务中为项目选择一个域。
services: cognitive-services
author: shonohs
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: shono
ms.openlocfilehash: 0dbd6ea13069b72e6bca5c065af92568a5c7cdb8
ms.sourcegitcommit: 5ef018fdadd854c8a3c360743245c44d306e470d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/01/2021
ms.locfileid: "97844932"
---
# <a name="select-a-domain-for-a-custom-vision-project"></a>为自定义视觉项目选择域

在自定义视觉项目的 "设置" 选项卡中，可以为项目选择一个域。 选择最接近你的方案的域。 如果要通过客户端库或 REST API 访问自定义视觉，则在创建项目时需要指定域 ID。 可以获取包含 " [获取域](https://westus2.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeab)" 的域 id 列表，也可以使用下表。

## <a name="image-classification"></a>图像分类

|域|目的|
|---|---|
|__常规__| 针对各种图像分类任务进行优化。 如果其他域均不合适，或者您不确定要选择哪个域，请选择 "常规" 域。 ID：`ee85a74c-405e-4adc-bb47-ffa8ca0c9f31`|
|__食物__|针对餐厅菜肴的照片进行优化。 如果要对各种水果或蔬菜的照片进行分类，请使用“食品”域。 ID：`c151d5b5-dd07-472a-acc8-15d29dea8518`|
|__特征点__|针对可识别的自然和人造地标进行优化。 在照片中的地标清晰可见的情况下，该域效果最佳。 即使照片中的人物稍微遮挡了地标，该域仍然有效。 ID：`ca455789-012d-4b50-9fec-5bb63841c793`|
|__零售__|针对购物目录或购物网站中的图像进行优化。 若想对连衣裙、裤子和衬衫进行精准分类，请使用此域。 ID：`b30a91ae-e3c1-4f73-a81e-c270bff27c39`|
|__压缩域__| 针对边缘设备上的实时分类约束进行了优化。|

## <a name="object-detection"></a>对象检测

|域|目的|
|---|---|
|__常规__| 已针对各种对象检测任务进行优化。 如果其他域均不合适，或者您不确定要选择哪个域，请选择 "常规" 域。 ID：`da2e3a8a-40a5-4171-82f4-58522f70fbc1`|
|__徽标__|已针对在图像中查找品牌徽标进行优化。 ID：`1d8ffafe-ec40-4fb2-8f90-72b3b6cecea4`|
|__货架上的产品__|已针对检测和分类货架上的产品进行优化。 ID：`3780a898-81c3-4516-81ae-3a139614e1f3`|
|__压缩域__| 针对边缘设备上的实时对象检测的约束进行了优化。|

## <a name="compact-domains"></a>压缩域

可导出压缩域生成的模型在本地运行。 在自定义视觉3.4 公共预览版 API 中，可以通过调用 GetDomains API 获取精简域的可导出平台列表。

根据所选的域，模型性能会有所不同。 在下表中，我们报告了有关 Intel 桌面 CPU 和 NVidia GPU 1 的模型大小和推断时间 \[ \] 。 这些数字不包括预处理和后处理时间。

|任务|域|ID|模型大小|CPU 推导时间|GPU 推导时间|
|---|---|---|---|---|---|
|分类|常规(精简)|`0732100f-1a38-4e49-a514-c9b44c697ab5`|5 MB|13 毫秒|5 ms|
|对象检测|常规(精简)|`a27d5ca5-bb19-49d8-a70a-fec086c47f5b`|45 MB|35 ms|5 ms|
|对象检测|常规 (compact) [S1]|`7ec2ac80-887b-48a6-8df9-8b1357765430`|14 MB|27毫秒|7 毫秒|

>[!NOTE]
>用于对象检测的 __常规 (compact)__ 域需要特殊的后处理逻辑。 有关详细信息，请参阅导出的 zip 包中的示例脚本。 如果需要没有后处理逻辑的模型，请使用 __常规 (compact) [S1]__。

>[!IMPORTANT]
>不保证导出的模型提供与云上的预测 API 完全相同的结果。 在运行平台或预处理实现中略有不同，这可能会导致模型输出中出现较大的差异。 有关预处理逻辑的详细信息，请参阅 [此文档](quickstarts/image-classification.md)。

\[1 \] Intel 强 2690 CPU 和 NVIDIA Tesla M60
