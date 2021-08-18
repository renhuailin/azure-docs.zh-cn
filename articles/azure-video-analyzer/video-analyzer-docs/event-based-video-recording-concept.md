---
title: Azure 视频分析器基于事件的视频录制 - Azure
description: Azure 视频分析器基于事件的视频录制 (EVR) 是指由事件触发时的视频录制过程。 相关事件可能源于视频信号本身的处理（例如检测到运动时），也可能来自独立源（例如门传感器信号表明门已被打开）。 本文介绍了一些与 EVR 有关的用例。
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 2386e45c3d2cde881436e86eb267365355d652ba
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114601382"
---
# <a name="event-based-video-recording"></a>基于事件的视频录制  

基于事件的视频录制 (EVR) 是指由事件触发的视频录制过程。 相关事件可能源于视频信号本身的处理（例如检测到运动时），也可能来自独立源（例如门传感器信号表明门已被打开）。 本文介绍了一些与 EVR 有关的用例。

## <a name="suggested-pre-reading"></a>建议的读前准备  

* [连续视频录制](continuous-video-recording.md)
* [播放录制的内容](playback-recordings-how-to.md)
* [管道概念](pipeline.md)

## <a name="overview"></a>概述 

可以通过两种方式使用视频分析器执行 EVR：
* 将来自支持 RTSP 的给定 IP 相机的输入录制到云中的给定视频资源，其中每个新事件将追加到该视频资源中可用的录制内容。
* 录制以将 MP4 文件分隔到 IoT Edge 设备的本地存储 - 每个事件都将导致新的 MP4 文件。

本文介绍了一些与基于事件的视频录制有关的用例。

### <a name="video-recording-triggered-by-motion-detection"></a>由运动检测触发的视频录制  

在此用例中，只能在视频中检测到动作时录制视频片段，并在生成此类视频片段时收到警报。 这可能与涉及关键基础结构保护的商业安全场景相关。 EVR 有助于降低存储成本。

下图显示了用于处理此用例的管道的图形表示形式。 可在[此处](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/evr-motion-video-sink/topology.json)找到此类管道的管道拓扑的 JSON 表示形式。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/motion-detection.png" alt-text="检测到运动时基于事件的实时视频录制。":::

在该图中，RTSP 源节点捕获了来自相机的实时视频源，并将其传递到[动作检测处理器](pipeline.md#motion-detection-processor)节点。 在检测到实时视频中的动作后，动作检测处理器节点将生成事件，该事件将到达[信号入口处理器](pipeline.md#signal-gate-processor)节点以及 IoT 中心消息接收器节点。 后一个节点将事件发送到 IoT Edge 中心，从那里可以将事件路由到其他目标以触发警报。 

来自运动检测器节点的事件还会触发信号入口处理器节点，打开它，让实时视频源从 RTSP 源节点流到[视频接收器节点](pipeline.md#video-sink)。 如果没有后续此类动作检测事件，入口将在配置的时间范围之后关闭。 这将确定追加到视频资源的录制视频的持续时间。

### <a name="video-recording-based-on-events-from-other-sources"></a>基于来自其他源的事件的视频录制  

在此用例中，可以使用来自另一个 IoT 传感器的信号触发视频录制。 下图显示了用于处理此用例的管道的图形表示形式。 可在[此处](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/evr-hubMessage-file-sink/topology.json)找到此类管道的管道拓扑的 JSON 表示形式。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/other-sources.png" alt-text="外部源发出信号时基于事件的实时视频录制。":::

在该图中，外部传感器将事件发送到 IoT Edge 中心。 然后，事件通过 [IoT 中心消息源](pipeline.md#iot-hub-message-source)节点路由到信号入口处理器节点。 信号入口处理器节点的行为与之前的用例相同 - 当事件触发它时，它将打开并让实时视频源从 RTSP 源节点流到文件接收器节点。 每次打开入口时，都会将新 MP4 文件写入 IoT Edge 设备的本地存储。

### <a name="video-recording-based-on-an-external-inferencing-module"></a>基于外部推理模块的视频录制 

在此用例中，可以根据来自外部逻辑系统的信号录制视频。 此类用例的一个示例是，只有在公路上的交通视频源中检测到卡车时才将视频录制到云中。 下图显示了用于处理此用例的管道的图形表示形式。 [可在此处找到](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/evr-hubMessage-video-sink/topology.json)此类管道的管道拓扑的 JSON 表示形式。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/external-inferencing-module.png" alt-text="外部推理模块发出信号时基于事件的实时视频录制。":::

在该图中，RTSP 源节点捕获了来自相机的实时视频源，并将其传递到两个分支：一个具有[信号入口处理器](pipeline.md#signal-gate-processor)节点，另一个使用 [HTTP 扩展](pipeline.md#http-extension-processor)节点将数据发送到外部逻辑模块。 HTTP 扩展节点允许管道通过 REST 将图像帧（以 JPEG、BMP 或 PNG 格式）发送到外部推理服务。 此信号路径通常仅支持低帧速率（小于 5fps）。 可以使用 HTTP 扩展处理器节点来降低发送到外部推理模块的视频的帧速率。

来自外部推理服务的结果由 HTTP 扩展节点检索，并通过 IoT 中心消息接收器节点中继到 IoT Edge 中心，外部逻辑模块可以在此处对这些结果进行进一步处理。 例如，如果推理服务能够检测车辆，则逻辑模块可以查找特定的车辆（例如卡车）。 当逻辑模块检测到相关对象时，它可以通过将事件通过 IoT Edge 中心发送到管道中的 IoT 中心消息源节点来触发信号入口处理器节点。 信号入口的输出将显示为转到视频接收器节点。 每次检测到卡车时，视频将录制到云中（追加到视频资源）。

增强此示例的一种方法是，在抵达 HTTP 扩展处理器节点之前使用动作检测器处理器。 这将减少推理服务的负担，例如夜间公路上可能会有很长一段时间没有车辆经过。 

## <a name="next-steps"></a>后续步骤

[教程：基于事件的视频录制](record-event-based-live-video.md)
