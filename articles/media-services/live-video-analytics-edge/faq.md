---
title: IoT Edge 上的实时视频分析常见问题解答 - Azure
description: 本主题解答了有关 IoT Edge 上的实时视频分析的常见问题。
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: 521cd0e4f5fc8232a000e10520298a979ba1c14c
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2020
ms.locfileid: "97401570"
---
# <a name="frequently-asked-questions-faqs"></a>常见问题 (FAQ)

本主题解答了有关 IoT Edge 上的实时视频分析的常见问题。

## <a name="general"></a>常规

### <a name="what-are-the-system-variables-that-can-be-used-in-graph-topology-definition"></a>图形拓扑定义中可以使用哪些系统变量？

|变量   |说明|
|---|---|
|[System.DateTime](/dotnet/framework/data/adonet/sql/linq/system-datetime-methods)|表示 UTC 时间的即时，通常以日期和时间表示， (基本表示法 yyyyMMddTHHmmssZ) 。|
|PreciseDateTime|表示 UTC 日期时间实例，采用 ISO8601 文件兼容格式，以毫秒为单位 (基本表示 yyyyMMddTHHmmss。 Ss.fffz) 。|
|System.GraphTopologyName   |表示媒体图形拓扑，保存图形的蓝图。|
|System.GraphInstanceName|  表示媒体图形实例，保存参数值并引用拓扑。|

## <a name="configuration-and-deployment"></a>配置和部署

### <a name="can-i-deploy-the-media-edge-module-to-a-windows-10-device"></a>是否可以将媒体边缘模块部署到 Windows 10 设备？

是的。 请参阅有关 [Windows 10 上的 Linux 容器](/virtualization/windowscontainers/deploy-containers/linux-containers) 的文章。

## <a name="capture-from-ip-camera-and-rtsp-settings"></a>从 IP 相机和 RTSP 设置捕获

### <a name="do-i-need-to-use-a-special-sdk-on-my-device-to-send-in-a-video-stream"></a>是否需要在设备上使用特殊的 SDK 来发送视频流？

否。 IoT Edge 上的实时视频分析支持使用 RTSP 视频流式处理协议（大多数 IP 相机均支持此协议）来捕获媒体。

### <a name="can-i-push-media-to-live-video-analytics-on-iot-edge-using-rtmp-or-smooth-like-a-media-services-live-event"></a>是否可以使用 RTMP 或 Smooth（如媒体服务实时事件）将媒体推送到 IoT Edge 上的实时视频分析？

* 否。 实时视频分析仅支持通过 IP 照相机捕获视频。
* 任何支持通过 TCP/HTTP 进行 RTSP 流式处理的相机都应正常工作。 

### <a name="can-i-reset-or-update-the-rtsp-source-url-on-a-graph-instance"></a>是否可以在图形实例上重置或更新 RTSP 源 URL？

可以，在图形实例处于非活动状态时即可进行重置或更新。  

### <a name="is-there-a-rtsp-simulator-available-to-use-during-testing-and-development"></a>是否有可以在测试和开发期间使用的 RTSP 模拟器？

是的。 可以在快速入门和教程中使用 [RTSP 模拟器](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)边缘模块来助力学习过程。 此模块是我们尽最大努力提供的，可能并非随时都可用。 强烈建议使用时间不要超过几个小时。 在制定生产部署计划之前，应投入实际的 RTSP 源进行测试。

### <a name="do-you-support-onvif-discovery-of-ip-cameras-at-the-edge"></a>你们是否支持 ONVIF 在边缘发现 IP 照相机？

否，不支持 ONVIF 在边缘发现设备。

## <a name="streaming-and-playback"></a>流式处理和播放

### <a name="can-assets-recorded-to-ams-from-the-edge-be-played-back-using-media-services-streaming-technologies-like-hls-or-dash"></a>是否可以使用媒体服务流式处理技术（例如 HLS 或 DASH）播放从边缘录制到 AMS 的资产？

是的。 可以像处理 Azure 媒体服务中的任何其他资产一样，对录制的资产进行流式处理。 要流式处理内容，必须创建流式处理终结点，并使其处于“正在运行”状态。 通过使用标准流式处理定位符创建过程，你可以访问 HLS 或 DASH 清单，以便将其流式传输到任何支持的播放器框架。 有关创建发布 HLS 或 DASH 清单的详细信息，请参阅[动态打包](../latest/dynamic-packaging-overview.md)。

### <a name="can-i-use-the-standard-content-protection-and-drm-features-of-media-services-on-an-archived-asset"></a>是否可以在存档资产上使用媒体服务的标准内容保护和 DRM 功能？

是的。 所有标准动态加密内容保护和 DRM 功能都可用于从媒体图形录制的资产。

### <a name="what-players-can-i-use-to-view-content-from-the-recorded-assets"></a>我可以使用哪些播放器来查看录制的资产中的内容？

支持兼容的 Apple HTTP Live Streaming (HLS) 版本 3 或版本 4 的所有标准播放器均受支持。 此外，还支持兼容 MPEG-DASH 播放的任何播放器。

建议用于测试的播放器包括：

* [Azure Media Player](../latest/use-azure-media-player.md)
* [HLS.js](https://hls-js.netlify.app/demo/)
* [Video.js](https://videojs.com/)
* [Dash.js](https://github.com/Dash-Industry-Forum/dash.js/wiki)
* [Shaka Player](https://github.com/google/shaka-player)
* [ExoPlayer](https://github.com/google/ExoPlayer)
* [Apple 本机 HTTP Live Streaming](https://developer.apple.com/streaming/)
* HTML5 视频播放器中内置的 Microsoft Edge、Chrome 或 Safari
* 支持 HLS 或 DASH 播放的商业播放器

### <a name="what-are-the-limits-on-streaming-a-media-graph-asset"></a>对媒体图形资产进行流式处理存在哪些限制？

从媒体图形流式处理实时资产或录制资产会使用大规模基础结构和流式处理终结点，媒体服务支持相同的大规模基础结构和流式处理终结点，以便对媒体和娱乐、OTT 以及广播客户进行按需和实时流式处理。 这意味着你可以快速轻松地启用 Azure CDN、Verizon 或 Akamai，以将你的内容传递给受众，受众规模可以少至几个观众，也可多达数百万个观众，具体取决于你的场景。

可以使用 Apple HTTP Live Streaming (HLS) 或 MPEG-DASH 来传递内容。

## <a name="design-your-ai-model"></a>设计 AI 模型 

### <a name="i-have-multiple-ai-models-wrapped-in-a-docker-container-how-should-i-use-them-with-live-video-analytics"></a>我有多个在 docker 容器中封装的 AI 模型。 如何在实时视频分析中使用它们？ 

解决方案不同，具体取决于推断服务器用于与实时视频分析通信的通信协议。 下面是执行此操作的一些方法。

#### <a name="http-protocol"></a>HTTP 协议：

* 单个容器 (单个 lvaExtension) ：  

   在推断服务器中，可以对不同的 AI 模型使用单个端口，但使用不同的终结点。 例如，对于 Python 示例，可以对 `route` 每个模型使用不同的，如下所示： 

   ```
   @app.route('/score/face_detection', methods=['POST']) 
   … 
   Your code specific to face detection model… 

   @app.route('/score/vehicle_detection', methods=['POST']) 
   … 
   Your code specific to vehicle detection model 
   … 
   ```

   然后，在实时视频分析部署中实例化图形时，请将每个实例的推理服务器 URL 设置为： 

   第一个实例：推理服务器 URL =`http://lvaExtension:44000/score/face_detection`<br/>
   第二个实例：推理服务器 URL =`http://lvaExtension:44000/score/vehicle_detection`  
    > [!NOTE]
    > 此外，还可以在不同端口上公开 AI 模型，并在实例化关系图时调用它们。  

* 多个容器： 

   每个容器都部署了不同的名称。 目前，在实时视频分析文档集中，我们演示了如何使用以下名称部署扩展： **lvaExtension**。 现在，你可以开发两个不同的容器。 每个容器都具有相同的 HTTP 接口 (表示相同 `/score` 的终结点) 。 用不同的名称部署这两个容器，并确保两个容器都在 **不同的端口** 上侦听。 

   例如，一个名称为的容器 `lvaExtension1` 正在侦听端口 `44000` ，其他名称为的容器 `lvaExtension2` 正在侦听该端口 `44001` 。 

   在实时视频分析拓扑中，可以实例化两个图形，其中包含不同的推理 Url，如下所示： 

   第一个实例：推理服务器 URL = `http://lvaExtension1:44001/score`    
   第二个实例：推理服务器 URL = `http://lvaExtension2:44001/score`
   
#### <a name="grpc-protocol"></a>GRPC 协议： 

使用实时视频分析模块1.0 时，如果 gRPC 服务器通过不同端口公开了不同的 AI 模型，则唯一的方法是使用 gRPC 协议。 在 [此示例](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtension/topology.json)中，有一个端口44000，该端口公开所有 yolo 模型。 理论上，可以重写 yolo gRPC 服务器，以在44000处公开一些模型，并在 45000 .。。 

使用实时视频分析模块2.0，会将新属性添加到 gRPC 扩展节点。 此属性称为 **extensionConfiguration** ，它是可用作 gRPC 协定的一部分的可选字符串。 如果在单个推理服务器中打包了多个 AI 模型，则无需为每个 AI 模型公开节点。 相反，对于 graph 实例，你) 的扩展提供程序 (可以定义如何使用 **extensionConfiguration** 属性选择不同的 AI 模型，并在执行过程中，实时视频分析将此字符串传递到推断服务器，该服务器可以使用此字符串来调用所需的 AI 模型。 

### <a name="i-am-building-a-grpc-server-around-an-ai-model-and-want-to-be-able-to-support-being-used-by-multiple-camerasgraph-instances-how-should-i-build-my-server"></a>我正在围绕 AI 模型构建 gRPC 服务器，并希望能够支持多个照相机/图形实例。 如何生成服务器？ 

 首先，请确保您的服务器一次可以处理多个请求。 或者确保服务器在并行线程中工作。 

例如，在 [实时视频分析 GRPC 示例](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/notebooks/Yolo/yolov3/yolov3-grpc-icpu-onnx/lvaextension/server/server.py)中，有一个默认数目的并行通道集。 请参阅： 

```
server = grpc.server(futures.ThreadPoolExecutor(max_workers=3)) 
```

在上述 gRPC 服务器实例化中，服务器一次只能打开每个相机 (三个通道，每个图形拓扑实例) 。 不应尝试将三个以上的实例连接到服务器。 如果尝试打开三个以上的通道，请求将被挂起，直到有一个被丢弃。  

上面的 gRPC 服务器实现用于 Python 示例。 开发人员可以实现自己的服务器，或者在上述默认实现中，将工作线程数增加到用于获取视频源的相机数。 

若要设置和使用多个照相机，开发人员可以实例化多个图形拓扑实例，其中每个实例指向相同或不同的推理服务器 (例如，上述段落中提到的服务器) 。 

### <a name="i-want-to-be-able-to-receive-multiple-frames-from-upstream-before-i-make-an-inferencing-decision-how-can-i-enable-that"></a>我希望在做出推断决定之前，能够从上游接收多个帧。 如何启用此功能？ 

当前 [默认示例](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis) 在 "无状态" 模式下工作。 这些示例不会保留以前的调用的状态，甚至在调用 (意味着多个拓扑实例可以调用相同的推理服务器，并且服务器将无法区分正在调用的人员和每个调用方的状态)  

#### <a name="http-protocol"></a>HTTP 协议

使用 HTTP 协议时： 

为了保持状态，每个调用方 (graph 拓扑实例) 将调用推断服务器，并将 HTTP 查询参数与调用方唯一。 例如，的推理服务器 URL 地址  

第一个拓扑实例 = `http://lvaExtension:44000/score?id=1`<br/>
第二个拓扑实例 = `http://lvaExtension:44000/score?id=2`

… 

在服务器端，分数路由将知道谁在调用。 如果 ID = 1，则它可以为该调用方单独保存状态 (图形拓扑实例) 。 然后，你可以将接收的视频帧保留在缓冲区中 (例如，数组或包含日期时间键的字典，值为帧) ，然后你可以定义服务器以在收到 x 帧后处理 (推断) 。 

#### <a name="grpc-protocol"></a>GRPC 协议 

使用 gRPC 协议时： 

对于 gRPC 扩展，每个会话都适用于单个相机源，因此不需要提供 ID。 现在，使用 extensionConfiguration 属性，您可以将视频帧存储在缓冲区中，并定义服务器以在接收 x 帧后处理 (推断) 。 

### <a name="do-all-processmediastreams-on-a-particular-container-run-the-same-ai-model"></a>特定容器上的所有 ProcessMediaStreams 是否都运行相同的 AI 模型？ 

不是。  

开始/停止从图形实例上的最终用户进行的调用构成会话，或者可能存在相机断开连接/重新连接。 如果照相机是流式处理视频，则目标是保持一个会话。 

* 两个照相机发送视频进行处理，会创建两个会话。 
* 一个摄像机转到一个图表，其中有两个 gRPCExtension 节点创建两个会话。 

每个会话都是实时视频分析和 gRPC 服务器之间的全双工连接，每个会话都可以有不同的模型/管道。 

> [!NOTE]
> 如果照相机断开连接/重新连接 (与相机处于脱机状态的时间超过容差限制) ，实时视频分析将打开与 gRPC 服务器的新会话。 服务器不需要在这些会话间跟踪状态。 

实时视频分析还为 graph 实例中的单个照相机添加了多个 gRPC 扩展支持。 你将能够使用这些 gRPC 扩展来按顺序或并行执行 AI 处理，甚至可以同时使用两者的组合。 

> [!NOTE]
> 并行运行多个扩展将影响你的硬件资源，因此，在选择适合你的计算需求的硬件时，必须考虑这一点。 

### <a name="what-is-the-max--of-simultaneous-processmediastreams"></a>同时 ProcessMediaStreams 的最大数量是多少？ 

实时视频分析没有适用的限制。  

### <a name="how-should-i-decide-if-my-inferencing-server-should-use-cpu-or-gpu-or-any-other-hardware-accelerator"></a>如何确定我的推断服务器应该使用 CPU、GPU 还是任何其他硬件加速器？ 

这完全取决于 AI 模型的开发复杂度以及开发人员想要如何使用 CPU 和硬件加速器。 开发 AI 模型时，开发人员可以指定模型应使用哪些资源来执行哪些操作。 

### <a name="how-do-i-store-images-with-bounding-boxes-post-processing"></a>如何在处理后如何实现存储图像？ 

目前，我们仅将边界框坐标作为推理消息提供。 开发人员可以构建可使用这些消息并覆盖视频帧上的边界框的自定义 MJPEG 流。  

## <a name="grpc-compatibility"></a>gRPC 兼容性 

### <a name="how-will-i-know-what-the-mandatory-fields-for-the-media-stream-descriptor-are"></a>我如何知道媒体流描述符的必填字段是什么？ 

任何未提供的字段值将被指定 [为 gRPC 指定](https://developers.google.com/protocol-buffers/docs/proto3#default)的默认值。  

实时视频分析使用协议缓冲区语言的 **proto3** 版本。 实时视频分析合同使用的所有协议缓冲区数据都在 [此处定义](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc)的协议缓冲区文件中可用。 

### <a name="how-should-i-ensure-that-i-am-using-the-latest-protocol-buffer-files"></a>我应该如何确保我使用的是最新的协议缓冲区文件？ 

可在 [此处获取](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc)最新的协议缓冲区文件。 每当我们更新合同文件时，它们就会出现在此位置。 尽管没有立即计划更新协议文件，但请在文件顶部查找包名称以了解版本。 此内容应为： 

```
microsoft.azure.media.live_video_analytics.extensibility.grpc.v1 
```

对这些文件的任何更新都将递增名称末尾的 "v 值"。 

> [!NOTE]
> 由于实时视频分析使用语言的 proto3 版本，因此字段是可选的，这使得它向后和向前兼容。 

### <a name="what-grpc-features-are-available-for-me-to-use-with-live-video-analytics-which-features-are-mandatory-and-which-ones-are-optional"></a>可以通过实时视频分析使用哪些 gRPC 功能？ 哪些功能是必需的，哪些是可选的？ 

如果 protobuf 协定已完成，则可以使用任何服务器端 gRPC 功能。 

## <a name="monitoring-and-metrics"></a>监视和指标

### <a name="can-i-monitor-the-media-graph-on-the-edge-using-event-grid"></a>是否可以使用事件网格监视边缘上的媒体图形？

是的。 您可以使用 prometheus 度量值并将其发布到事件网格。 

### <a name="can-i-use-azure-monitor-to-view-the-health-metrics-and-performance-of-my-media-graphs-in-the-cloud-or-on-the-edge"></a>是否可以使用 Azure Monitor 查看云端或边缘的媒体图形的运行状况、指标和性能？

是的。 这是受支持的。 了解有关 [如何使用 Azure Monitor 度量值](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics)的详细信息。

### <a name="are-there-any-tools-to-make-it-easier-to-monitor-the-media-services-iot-edge-module"></a>是否有任何工具可用于简化监视媒体服务 IoT Edge 模块？

Visual Studio Code 支持“Azure IoT Tools”扩展，该扩展有助于轻松监视 LVAEdge 模块终结点。 可以使用此工具快速开始监视“事件”的 IoT 中心内置终结点，并查看从边缘设备路由到云的推理消息。 

此外，还可以使用此扩展编辑 LVAEdge 模块的模块孪生，以修改媒体图形设置。

有关详细信息，请参阅[监视和日志记录](monitoring-logging.md)一文。

## <a name="billing-and-availability"></a>计费和可用性

### <a name="how-is-live-video-analytics-on-iot-edge-billed"></a>如何对 IoT Edge 上的实时视频分析计费？

请参阅[定价页面](https://azure.microsoft.com/pricing/details/media-services/)了解详细信息。

## <a name="next-steps"></a>后续步骤

[快速入门：入门 - IoT Edge 上的实时视频分析](get-started-detect-motion-emit-events-quickstart.md)
