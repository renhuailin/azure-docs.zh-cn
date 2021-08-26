---
title: 在不录制的情况下分析实时视频 - Azure
description: 使用管道拓扑，可以仅从实时视频流中提取分析，而不在边缘或云中进行录制。 本文讨论了这一概念。
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 8444759d231153419873679624645a96e07058ce
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2021
ms.locfileid: "122515700"
---
# <a name="analyzing-live-videos-without-recording"></a>无需录制即可分析实时视频

## <a name="suggested-pre-reading"></a>建议的读前准备 

* [管道概念](pipeline.md)
* [管道扩展概念](pipeline-extension.md)
* [基于事件的视频录制概念](event-based-video-recording-concept.md)

## <a name="overview"></a>概述  

可以使用管道拓扑来分析实时视频，而不将视频的任何部分录制到文件或资产。 下方显示的管道拓扑与[基于事件的视频录制](event-based-video-recording-concept.md)一文中的拓扑类似，但不包含视频接收器节点或文件接收器节点。

### <a name="motion-detection"></a>动作检测

下面显示的管道拓扑包括一个 [RTSP](pipeline.md#rtsp-source) 源节点、一个[运动检测处理器](pipeline.md#motion-detection-processor)节点和一个 [IoT 中心消息接收器](pipeline.md#iot-hub-message-sink)节点，你可以看到其 [JSON 表示形式](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/motion-detection/topology.json)中使用的设置。 根据此拓扑，你可以检测传入实时视频流中的运动，并通过 IoT 中心消息接收器节点将动作事件中继到其他应用和服务。 外部应用或服务可以触发警报，或向相应人员发送通知。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/get-started-detect-motion-emit-events/motion-detection.svg" alt-text="检测实时视频中的运动":::

### <a name="analyzing-video-using-a-custom-vision-model"></a>使用自定义视觉模型分析视频 

根据下方显示的管道拓扑，你可以使用打包在不同模块中的自定义视觉模型来分析实时视频流。 可以看到其 [JSON 表示形式](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/httpExtension/topology.json)中使用的设置。 还有其他[示例](https://github.com/Azure/video-analyzer/tree/main/edge-modules/extensions)可用于将模型包装到作为推理服务运行的 IoT Edge 模块中。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video-without-recording/motion-detected-frames.svg" alt-text="使用自定义视觉模块分析实时视频":::

在此管道拓扑中，RTSP 源的视频输入将发送到 [HTTP 扩展处理器](pipeline.md#http-extension-processor)节点，该节点将图像帧（采用 JPEG、BMP 或 PNG 格式）通过 REST 发送到外部推理服务。 来自外部推理服务的结果由 HTTP 扩展节点检索，并通过 IoT 中心消息接收器节点中继到 IoT Edge 中心。 这种类型的管道拓扑可用于为各种场景构建解决方案，例如了解路口车辆的时序分布、了解零售商店中的消费者流量模式等。

>[!TIP]
> 你可以使用“`samplingOptions`字段管理 HTTP 扩展处理器节点内的帧速率，然后将其发送到下游。

增强此示例的一种方法是，在抵达 HTTP 扩展处理器节点之前使用动作检测器处理器。 这将减少推理服务上的负载，因为仅在视频中有运动活动时使用它。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video-without-recording/custom-model.svg" alt-text="在运动帧上使用自定义视觉模块分析实时视频":::

## <a name="next-steps"></a>后续步骤

[快速入门：使用自己的模型分析实时视频 - HTTP](analyze-live-video-use-your-model-http.md)
