---
title: Azure 视频分析器配额和限制 - Azure
description: 本文介绍 Azure 视频分析器配额和限制。
ms.service: azure-video-analyzer
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: a94ebd36728519b7ae73d9cc48c82097dcfdbb21
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114602082"
---
# <a name="video-analyzer-quotas-and-limitations"></a>视频分析器配额和限制

本文介绍 Azure 视频分析器配额和限制。

## <a name="quotas-and-limitations---edge-module"></a>配额和限制 - 边缘模块

本部分列举了视频分析器边缘模块的配额和限制。

### <a name="maximum-period-of-disconnected-use"></a>在已断开连接的情况下使用的最长期限

边缘模块可承受 Internet 连接暂时断开的情况。 如果模块保持已断开连接的状态超过了 36 小时，它将停用任何正在运行的实时管道。 并将阻止所有进一步的直接方法调用。

若要将边缘模块恢复到可操作状态，则必须重新建立 Internet 连接，使此模块能够与 Azure 视频分析器帐户成功通信。

### <a name="maximum-number-of-live-pipelines"></a>最大实时管道数

每个模块（通过 `livePipelineSet` 创建）最多支持 1000 个实时管道。

### <a name="maximum-number-of-pipeline-topologies"></a>最大管道拓扑数

每个模块（通过 `pipelineTopologySet` 创建）最多支持 50 个管道拓扑。

### <a name="limitations-on-pipeline-topologies"></a>管道拓扑的限制

以下是在管道拓扑中不同节点相互连接方式的限制。

* RTSP 源
   * 每个管道拓扑仅允许有一个 RTSP 源。
* 运动检测处理器
   * 必须立即成为 RTSP 源中的下游。
   * 不能从 HTTP 或 gRPC 扩展处理器的下游使用。
* 信号门处理器
   * 必须立即成为 RTSP 源中的下游。
   * 不能从 HTTP 或 gRPC 扩展处理器的上游使用。
* 对象跟踪器处理器
   * 必须直接是 HTTP 或 gRPC 扩展处理器的下游。 扩展处理器不应该对实时视频的每一帧都应用 AI 模型。
* 跨线处理器
   * 必须直接是对象跟踪器处理器的下游。
* 视频接收器 
   * 必须立即成为 RTSP 源或信号门处理器中的下游。
* 文件接收器
   * 必须立即成为信号门处理器中的下游。
   * 不能直接是 HTTP 或 gRPC 扩展处理器或运动检测处理器的下游。
* IoT 中心接收器
   * 不能直接是 IoT 中心源的下游。

### <a name="supported-cameras"></a>支持的照相机
只能使用支持 RTSP 协议的 IP 相机。 可以在 [ONVIF 一致性](https://www.onvif.org/conformant-products)产品页上找到支持 RTSP 的 IP 相机。 查找符合配置文件 G、S 或 T 的设备。

应将这些相机配置为使用 H.264 视频和 AAC 音频。 当前不支持其他编解码器。

视频分析器仅支持对[交错 RTP 流]( https://datatracker.ietf.org/doc/html/rfc2326#section-10.12)使用 RTSP。 在此模式下，RTP 流量通过 RTSP TCP 连接进行隧道传输。 不支持 RTP 流量通过 UDP。

### <a name="support-for-video-ai"></a>支持视频 AI
HTTP 或 gRPC 扩展处理器仅支持通过外部 AI 模块发送图像/视频帧数据。 因此，不支持对音频数据运行推断。 因此，将 RTSP 源节点作为 `inputs` 之一的管道拓扑中的处理器节点还使用 `outputSelectors` 属性，以确保只将视频传递到处理器中。 请参阅此示例[拓扑](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/evr-grpcExtension-video-sink/topology.json)。

## <a name="quotas-and-limitations---service"></a>配额和限制 - 服务

本部分列举了视频分析器帐户的配额和限制。

### <a name="limitations-on-service-operations-at-preview"></a>预览版中服务操作的限制

发布预览版时，视频分析器服务不支持以下功能：

* 在不中断的情况下将媒体服务帐户从一个订阅迁移到另一个订阅的功能。
* 将多个存储帐户用于帐户的功能。
* 使用与视频分析器帐户位于不同区域的存储帐户的功能。

### <a name="limits-on-video-resources"></a>对视频资源的限制
发布预览版时，每个视频分析器帐户最多只能有 10,000 个视频资源。 如果业务需要更高的限制，请在 Azure 门户中打开支持票证。

### <a name="limits-on-access-policies"></a>对访问策略的限制
发布预览版时，每个视频分析器帐户最多只能有 20 个访问策略。

### <a name="limits-on-registered-edge-modules"></a>对已注册的边缘模块的限制
发布预览版时，每个视频分析器帐户最多可以注册 1,000 个边缘模块。 如果业务需要更高的限制，请在 Azure 门户中打开支持票证。

### <a name="limits-on-client-api-calls"></a>对客户端 API 调用的限制
以下限制适用于预览版的客户端 API：

* 10 秒内最多 50 个请求
* 每小时最多 600 个请求

## <a name="limitations---video-analyzer-player-widgets"></a>限制 - 视频分析器播放机小组件

发布预览版时，不支持在 iOS 设备上播放。

## <a name="next-steps"></a>后续步骤

[概述](overview.md)
