---
title: 空间分析相机放置
titleSuffix: Azure Cognitive Services
description: 了解如何设置用于空间分析的相机
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 06/08/2021
ms.author: pafarley
ms.openlocfilehash: f25854c93318cf581843e2d484a4265475441271
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111744352"
---
# <a name="camera-placement-guide"></a>相机放置指南

本文提供有关用于空间分析（公共预览版）的相机放置建议。 其中包括一般指导原则，以及高度、角度和相机至焦点距离方面的，与全部所含操作相关的具体建议。 

> [!NOTE]
> 本指南适用于 Axis M3045-V 相机。 此相机使用 1920x1080 分辨率、106 度水平视场、59 度垂直视场和固定的 2.8 毫米焦距。 以下原则适用于所有相机，不过，在对其他相机运用时，需要在相机高度和相机至焦点距离方面调整具体的指导原则。 

## <a name="general-guidelines"></a>一般指南

放置用于空间分析的相机时，请考虑以下一般指导原则：

* **照明高度。** 将相机放置在照明装置的下方，以免装置挡住相机。
* **阻碍物。** 为了避免阻挡相机视场，请留意照相杆、招牌、架子、墙壁和现有的 LP 相机等阻碍碍物。
* **环境背光。** 室外背光会影响相机图像质量。 为了避免出现严重的背光状况，请勿将相机对准通外的窗户和玻璃门。
* **当地隐私规则和法规。** 当地法规可能会限制相机可以拍摄的内容。 在放置相机之前，请务必了解当地规则和法规。
* **建筑物结构。** HVAC（暖通空调）、灭火器和现有布线可能会限制相机的硬件安装。
* **线缆管理。** 确保能够将网线从规划好的相机安装位置连接到以太网供电 (PoE) 交换机。

## <a name="height-focal-point-distance-and-angle"></a>高度、焦点距离和角度

在确定如何安装用于空间分析的相机时，你需要考虑三个点：
- 相机高度
- 相机至焦点的距离
- 相机相对于地平面的角度

在可能的情况下，还需要知道大部分人相对于相机视场的行走方向（人员行走方向）。 此方向对于系统性能而言非常重要。

![人员行走方向插图](./media/spatial-analysis/person-walking-direction.png)

下图显示了人员行走方向的立视图。

![立视平面图](./media/spatial-analysis/person-walking-direction-diagram.png)

## <a name="camera-height"></a>相机高度

一般情况下，相机应安装在距离地面 12-14 英尺的位置。 要进行面罩检测，我们建议将相机安装在距离地面 8-12 英尺的位置。 在此范围内规划安装相机时，请考虑到可能影响相机视场的阻碍物（例如架子、吊灯、吊装招牌和显示器），然后根据需要调整高度。

## <a name="camera-to-focal-point-distance"></a>相机至焦点的距离

相机至焦点的距离是从焦点（或相机图像中心）到相机的线性距离，在地面上测量。

![相机至焦点的距离](./media/spatial-analysis/camera-focal-point.png)

此距离在地平面上测量。

![如何从地面测量相机至焦点的距离](./media/spatial-analysis/camera-focal-point-floor-plane.png)

从上方来看，测量方式如下：

![如何从上方测量相机至焦点的距离](./media/spatial-analysis/camera-focal-point-above.png)

参考下表，以便根据特定的安装高度确定相机与焦点之间的距离。 参考这些距离可以实现最佳放置。 请注意，该表提供了针对 12-14 英尺以下的高度的指导建议，因为某些天花板可能会限制高度。 要进行面罩检测，在相机高度为 8-12 英尺的情况下，建议的相机至焦点距离（最小值/最大值）为 4-10 英尺。

| 相机高度 | 相机至焦点的距离（最小值/最大值） |  
| ------------- | ---------------------------------------- |  
| 8 英尺            | 4.6-8 英尺                                  |  
| 10 英尺           | 5.8-10 英尺                                 |  
| 12 英尺           | 7-12 英尺                                   |  
| 14 英尺           | 8-14 英尺                                  |  
| 16 英尺           | 9.2-16 英尺                                 |  
| 20 英尺           | 11.5-20 英尺                                |  

下图模拟了最近和最远相机至焦点距离的相机视场。

| 最近                                      | 最远                                      |  
| -------------------------------------------- | --------------------------------------------- |  
| ![最近的相机至焦点距离](./media/spatial-analysis/focal-point-closest.png) | ![最远的相机至焦点距离](./media/spatial-analysis/focal-point-farthest.png) |  

## <a name="camera-angle-mounting-ranges"></a>相机角度安装范围

本部分介绍可接受的相机角度安装范围。 这些安装范围展示了可接受的最佳放置范围。

### <a name="line-configuration"></a>视线配置

要进行 **cognitiveservices.vision.spatialanalysis-personcrossingline** 操作，+/-5 度是最佳的相机安装角度，这可以最大程度地提高准确度。

要进行面罩检测，在相机高度为 8-12 英尺的情况下，+/-30 度是最佳的相机安装角度。

下图模拟了按照最左侧 (-) 和最右侧 (+) 安装角度建议，使用 **cognitiveservices account spatialanalysis-personcrossingline** 在门口执行入室人数统计时的相机视场。

| 最左侧视场                | 最右侧视场                |  
| ---------------------------- | ----------------------------- |  
| ![最左侧相机角度](./media/spatial-analysis/camera-angle-left.png) | ![最右侧相机角度](./media/spatial-analysis/camera-angle-right.png) |  

下图显示了相机放置和安装角度的鸟瞰图。

![鸟瞰视图](./media/spatial-analysis/camera-angle-top.png)

### <a name="zone-configuration"></a>区域配置

建议将相机放置在距离地面 10 英尺或更高的位置，以保证覆盖足够大的区域。 

如果区域位于墙壁或货架等障碍物的旁边，请将相机安装在与目标相距指定距离的位置，其可接受的角度范围为 120 度，如下图所示。

![可接受的相机角度](./media/spatial-analysis/camera-angle-acceptable.png)

下图模拟了位于货架旁边的区域的左侧和右侧相机视场。

| 左侧视场        | 右侧视场        |  
| ---------------- | ----------------- |  
| ![左侧视场](./media/spatial-analysis/end-cap-left.png) | ![右侧视场](./media/spatial-analysis/end-cap-right.png) |  

#### <a name="queues"></a>队列

可以使用 **cognitiveservices.vision.spatialanalysis-personcount**、**cognitiveservices.vision.spatialanalysis-persondistance** 和 **cognitiveservices.vision.spatialanalysis-personcrossingpolygon** 技能来监视队列。 为确保最佳的队列数据质量，最好是使用可回卷拉带栅栏来尽量减少人员在队列中的阻塞情况，确保队列位置在不同的时间都能保持一致。

![可回卷拉带队列](./media/spatial-analysis/retractable-belt-queue.png)

与不透明的栅栏相比，这种栅栏能够更好地保持队形，可以最大程度地提高系统的识别准确度。

有两种类型的队列：线性和之字形。

下图显示了有关线性队列的建议：

![线性队列建议](./media/spatial-analysis/camera-angle-linear-queue.png)

下图模拟了线性队列的左侧和右侧相机视场。 请注意，可将相机安装在队列的另一侧。

| 左侧视场                          | 右侧视场                          |  
| ---------------------------------- | ----------------------------------- |  
| ![线性队列的左侧角度](./media/spatial-analysis/camera-angle-linear-left.png) | ![线性队列的右侧角度](./media/spatial-analysis/camera-angle-linear-right.png) |  

对于之字形队列，最好是避免将相机放置在直接朝向队形方向的位置，如下图所示。 请注意，插图中四个示例相机位置均提供了理想的视场，每个方向可接受 +/-15 度的偏差。

下图模拟了针对之字形队列将相机放置在理想位置时的视场。

| 视场 1        | 视场 2        |  
| ------------- | ------------- |  
| ![视场 1](./media/spatial-analysis/camera-angle-ideal-location-right.png) | ![视场 2](./media/spatial-analysis/camera-angle-ideal-location-left.png) |  

| 视场 3 |  视场 4 |  
| ---- | ----  |
| ![视场 3](./media/spatial-analysis/camera-angle-ideal-location-back.png) |  ![视场 4](./media/spatial-analysis/camera-angle-ideal-location-back-left.png) | 

##### <a name="organic-queues"></a>有机队列

有机队列是按有机方式构成的。 如果排队人数不超过 2-3 人，并且队形符合区域定义，则可以接受此队列样式。 如果队列长度通常大于 2-3 人，则我们建议使用可回卷拉带栅栏来帮助引导队列方向，并确保队形符合区域定义。

## <a name="next-steps"></a>后续步骤

* [部署人员计数 Web 应用程序](spatial-analysis-web-app.md)
* [配置空间分析操作](./spatial-analysis-operations.md)
* [日志记录和故障排除](spatial-analysis-logging.md)
* [区域和线条放置指南](spatial-analysis-zone-line-placement.md)
