---
ms.openlocfilehash: c4c58724f8ba2accd827e1a1b3819e40210aa9c0
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2020
ms.locfileid: "97486875"
---
> [!div class="mx-imgBorder"]
> :::image type="content" source="../../../media/quickstarts/overview-qs5.svg" alt-text="信号流":::

此图显示本快速入门中信号的流动方式。 [Edge 模块](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)模拟托管实时流式处理协议 (RTSP) 服务器的 IP 相机。 [RTSP 源](../../../media-graph-concept.md#rtsp-source)节点从该服务器拉取视频源，并将视频帧发送到 [HTTP 扩展处理器](../../../media-graph-concept.md#http-extension-processor)节点。 

HTTP 扩展节点扮演代理的角色。 它对 `samplingOptions` 字段设置的传入视频帧采样，还会将视频帧转换为指定的图像类型。 然后，它将图像通过 REST 转发到另一个 Edge 模块，该模块在 HTTP 终结点后运行一个 AI 模型。 在此示例中，使用 [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) 模型构建该 Edge 模块，该模型能够检测多种类型的对象。 HTTP 扩展处理器节点收集检测结果并将事件发布到 [IoT 中心接收器](../../../media-graph-concept.md#iot-hub-message-sink)节点。 然后该节点将这些事件发送到 [IoT Edge 中心](../../../../../iot-edge/iot-edge-glossary.md#iot-edge-hub)。

在本快速入门中，请执行以下操作：

1. 创建并部署媒体图。
1. 解释结果。
1. 清理资源。
