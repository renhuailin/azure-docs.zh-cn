---
title: 使用 Azure 视频分析器录制视频进行播放
description: 本文讨论如何使用 Azure 视频分析器录制视频进行播放。
ms.service: azure-video-analyzer
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 4c5d0121df90791d8eb77782fb6d949548a0185e
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114604352"
---
# <a name="record-video-for-playback"></a>录制视频进行播放

在 CCTV 相机的视频管理系统上下文中，视频录制是指从相机捕获视频并将其录制下来以供随后通过移动应用和浏览器应用进行观看的过程。 视频录制可分为连续视频录制和基于事件的视频录制两类。

## <a name="continuous-video-recording"></a>连续视频录制

连续视频录制 (CVR) 是指连续录制从视频源捕获的所有视频的过程。 CVR 可确保所需的视频时长（由[录制策略](#recording-policy)决定），以便在稍后的某个时间点进行分析和/或审核。


## <a name="event-based-video-recording"></a>基于事件的视频录制

基于事件的视频录制 (EVR) 是指由事件触发的视频录制过程。 相关事件可能源于视频信号本身的处理（例如，检测运动），也可能来自独立源（例如，来自门传感器）。 EVR 可以节省存储空间，但需要额外的组件来生成事件并触发视频录制。 换句话说，EVR 需要对那些应触发视频录制的事件以及视频录制的持续时间做出附加决策。 例如：从事件发生前 30 秒开始录制 2 分钟的视频。

## <a name="choosing-recording-modes"></a>选择录制模式

选择使用 CVR 还是使用 EVR 取决于业务目标。 Azure 视频分析器 (AVA) 为 CVR 和 EVR 提供平台功能。 有关详细信息，可参阅 [CVR](continuous-video-recording.md) 和 [EVR](event-based-video-recording-concept.md) 方案文章。

## <a name="recording-policy"></a>录制策略

录制策略是指规定保留的视频录制的长度或持续时间的策略。 录制策略使你能够均衡存储成本和业务需求。 对于 CVR，录制策略定义应存储多少天的视频（例如，过去 7 天的视频）。 有关详细信息，可参阅 [CVR](continuous-video-recording.md) 方案文章。

## <a name="next-steps"></a>后续步骤

- [了解 EVR 方案](event-based-video-recording-concept.md)
- [了解 CVR 方案](continuous-video-recording.md)
