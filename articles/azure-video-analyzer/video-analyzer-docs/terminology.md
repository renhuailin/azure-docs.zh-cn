---
title: Azure 视频分析器术语
description: 本文概述了 Azure 视频分析器术语。
ms.service: azure-video-analyzer
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 59ed8f85f1196328b6ab477faae77ed35d054223
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114601862"
---
# <a name="azure-video-analyzer-terminology"></a>Azure 视频分析器术语

本文概述了与 [Azure 视频分析器](overview.md)相关的术语。

## <a name="pipeline-topology"></a>管道拓扑

通过使用[管道拓扑](pipeline.md)，可以定义应从何处捕获媒体、应如何处理媒体以及应将结果交付到何处。 你借助它可以定义一个管道，该管道由用于构建实时视频分析应用程序的源、处理器和接收器节点组成。 

## <a name="rtsp"></a>RTSP

[RTSP](https://tools.ietf.org/html/rfc2326) 是指实时流式处理协议。 它是一种应用程序级别的协议，用于控制数据与实时属性的传送。 RTSP 提供了一个可扩展的框架，用于实现实时数据（如音频和视频）的受控、按需交付。 视频分析器[支持](pipeline.md#rtsp-source)从支持 RTSP 的 IP 相机捕获、分析和录制视频。


## <a name="recording"></a>记录

在安全相机的视频管理系统上下文中，视频录制是指从相机捕获视频并存储视频以供随后通过移动应用和浏览器应用观看的过程。 视频录制可分类为[连续视频录制](continuous-video-recording.md)和[基于事件的视频录制](event-based-video-recording-concept.md)。 如需详细了解这方面的内容，请参阅[视频录制](video-recording.md)概念页面。

## <a name="video"></a>视频

创建视频分析器帐户时，必须将一个 Azure 存储帐户与其关联。 可以使用视频分析器从相机中录制实时视频，并将该数据保存到磁盘或云存储中。 在后一种情况下，数据将以 blob 的形式存储在存储帐户的容器中。 视频是视频分析器帐户中映射到此类 blob 容器的资源。 与此类视频资源关联的所有内容以 blob 的形式存储在相应的容器中，而视频分析器将保留元数据（例如名称、描述、创建时间）。

你可以使用视频分析器创建视频资源，并将数据添加到现有视频。 这可以实现连续和基于事件的视频录制和播放（在边缘设备上捕获视频、录制到视频分析器，并通过视频分析器流式处理功能播放）。

## <a name="grpc"></a>gRPC

[gRPC](https://grpc.io/docs/guides/) 是一种与语言无关的高性能远程过程调用 (RPC) 框架。 它通过[协议缓冲区 3](https://developers.google.com/protocol-buffers/docs/proto3) 使用基于会话的结构化架构作为其通信的基础消息交换格式。

## <a name="streaming"></a>流式处理

可以使用视频分析器通过行业标准的基于 HTTP 的媒体流式处理协议（例如 [HTTP Live Streaming (HLS)](https://developer.apple.com/streaming/) 和 [MPEG-DASH](https://dashif.org/about/)）将视频录制内容流式传输到客户端。 可以使用 [Azure 视频分析器播放器小组件](https://github.com/Azure/video-analyzer/blob/main/widgets/readme.md)（Web 组件）播放视频资源。 此外，[JW Player](https://www.jwplayer.com/)、[hls.js](https://github.com/video-dev/hls.js/)、[VideoJS](https://videojs.com/)、[Google 的 Shaka Player](https://github.com/google/shaka-player) 等网络播放器都支持 HLS，或者可以使用 Android 的 [Exoplayer](https://github.com/google/ExoPlayer) 和 iOS 的 [AV Foundation](https://developer.apple.com/av-foundation/) 在移动应用中以本机方式呈现。 [此页面上的客户端列表](https://dashif.org/clients/)同样支持 MPEG-DASH。

## <a name="vms"></a>VMS

[VMS](https://en.wikipedia.org/wiki/Video_management_system) 是指视频管理系统。 此类系统用于配置和控制 CCTV 相机，捕获并录制其中的视频。 这些系统还提供客户端应用程序来播放录制的视频。

## <a name="next-steps"></a>后续步骤

- 请参阅[管道](pipeline.md)。
