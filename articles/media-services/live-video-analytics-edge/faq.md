---
title: IoT Edge 常见问题的实时视频分析-Azure
description: 本文解答了有关 IoT Edge 上的实时视频分析的常见问题。
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: f9ef26b9b64bd8a0bad7c83960f2d235ed6461cb
ms.sourcegitcommit: 799f0f187f96b45ae561923d002abad40e1eebd6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/24/2020
ms.locfileid: "97762870"
---
# <a name="live-video-analytics-on-iot-edge-faq"></a>IoT Edge 常见问题的实时视频分析

本文解答了有关 Azure IoT Edge 上的实时视频分析的常见问题。

## <a name="general"></a>常规

**图形拓扑定义中可以使用哪些系统变量？**

| 变量   |  说明  | 
| --- | --- | 
| [System.DateTime](/dotnet/framework/data/adonet/sql/linq/system-datetime-methods) | 表示 UTC 时间的瞬间，通常用以下格式表示为日期和时间：<br>*yyyyMMddTHHmmssZ* | 
| PreciseDateTime | 用以下格式表示采用 ISO8601 文件相容格式（以毫秒为单位）的协调世界时 (UTC) 日期-时间实例：<br>*yyyyMMddTHHmmss. Ss.fffz* | 
| System.GraphTopologyName   | 表示 media graph 拓扑，并保存关系图的蓝图。 | 
| System.GraphInstanceName |    表示 media graph 实例，保存参数值，并引用拓扑。 | 

## <a name="configuration-and-deployment"></a>配置和部署

**是否可以将媒体边缘模块部署到 Windows 10 设备？**

是。 有关详细信息，请参阅 [Windows 10 上的 Linux 容器](/virtualization/windowscontainers/deploy-containers/linux-containers)。

## <a name="capture-from-ip-camera-and-rtsp-settings"></a>从 IP 相机和 RTSP 设置捕获

**是否需要在设备上使用特殊的 SDK 来发送视频流？**

不能，IoT Edge 上的实时视频分析支持通过使用 RTSP (实时流式处理协议) 为视频流式处理捕获媒体，这在大多数 IP 照相机上都受支持。

**能否使用 Real-Time 消息协议 (RTMP) 或平滑流式处理协议 (（如媒体服务实时事件) ）将媒体推送到实时视频分析 IoT Edge？**

不能，实时视频分析仅支持从 IP 照相机捕获视频的 RTSP。 任何支持通过 TCP/HTTP 进行 RTSP 流式处理的相机都应正常工作。 

**能否在图形实例中重置或更新 RTSP 源 URL？**

是，当图形实例处于 *非活动* 状态时。  

**是否可以在测试和开发期间使用 RTSP 模拟器？**

是的，可以在快速入门和教程中使用 [RTSP 模拟器](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) 边缘模块来支持学习过程。 此模块是最大努力，可能并非始终可用。 我们强烈建议你 *不要* 使用模拟器超过几个小时。 在规划生产部署之前，应使用实际的 RTSP 源来投入测试。

**你们是否支持 ONVIF 在边缘发现 IP 照相机？**

不，我们不支持打开网络视频界面论坛 (ONVIF) 设备上的设备发现。

## <a name="streaming-and-playback"></a>流式处理和播放

**能否使用 HLS 或短线等流式处理技术从边缘播放记录到 Azure 媒体服务的资产？**

是。 你可以像处理 Azure 媒体服务中的任何其他资产那样流式传输记录的资产。 要流式传输内容，你必须创建一个流式处理终结点，并且该终结点处于 "正在运行" 状态。 使用标准流式处理定位符创建过程，你可以访问 Apple HTTP Live Streaming (HLS) 或动态自适应流式处理通过 HTTP (破折号（也称为 MPEG-短线) 清单），用于流式传输到任何支持的播放器框架。 有关创建和发布 HLS 或虚线清单的详细信息，请参阅 [动态打包](../latest/dynamic-packaging-overview.md)。

**是否可以在存档资产上使用媒体服务的标准内容保护和 DRM 功能？**

是。 所有标准动态加密内容保护和数字版权管理 (DRM) 功能可用于从 media graph 记录的资产。

**我可以使用哪些播放器来查看录制的资产中的内容？**

支持符合 HLS 版本3或版本4的所有标准播放机。 此外，还支持任何支持符合 MPEG-短划线的播放机。

建议用于测试的播放器包括：

* [Azure Media Player](../latest/use-azure-media-player.md)
* [HLS.js](https://hls-js.netlify.app/demo/)
* [Video.js](https://videojs.com/)
* [Dash.js](https://github.com/Dash-Industry-Forum/dash.js/wiki)
* [Shaka Player](https://github.com/google/shaka-player)
* [ExoPlayer](https://github.com/google/ExoPlayer)
* [Apple 本机 HTTP Live Streaming](https://developer.apple.com/streaming/)
* Edge、Chrome 或 Safari 内置 HTML5 视频播放器
* 支持 HLS 或 DASH 播放的商业播放器

**对媒体图形资产进行流式处理存在哪些限制？**

从媒体图形流式传输实时或记录的资产使用相同的高缩放基础结构和流式处理终结点，媒体服务支持媒体 & 娱乐的按需和实时视频流，以及热门 (OTT) 和广播客户。 这意味着，你可以快速轻松地启用 Azure 内容交付网络、Verizon 或 Akamai，以将你的内容发送给一个或多个查看者，具体取决于你的方案。

可以使用 Apple HLS 或 MPEG 破折号传递内容。

## <a name="design-your-ai-model"></a>设计 AI 模型 

**我有多个在 Docker 容器中封装的 AI 模型。如何在实时视频分析中使用它们？** 

解决方案根据推断服务器用来与实时视频分析通信的通信协议而有所不同。 以下各节介绍了每个协议的工作原理。

*使用 HTTP 协议*：

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

   然后，在实时视频分析部署中实例化图形时，为每个实例设置推理服务器 URL，如下所示： 

   第一个实例：推理服务器 URL =`http://lvaExtension:44000/score/face_detection`<br/>
   第二个实例：推理服务器 URL =`http://lvaExtension:44000/score/vehicle_detection`  
   
    > [!NOTE]
    > 或者，你可以在不同的端口上公开 AI 模型，并在实例化关系图时调用它们。  

* 多个容器： 

   每个容器都部署了不同的名称。 以前，在实时视频分析文档集中，我们介绍了如何部署名为 *lvaExtension* 的扩展。 现在，你可以开发两个不同的容器，每个容器都具有相同的 HTTP 接口，这意味着它们具有相同的 `/score` 终结点。 用不同的名称部署这两个容器，并确保两个容器都在 *不同的端口* 上侦听。 

   例如，名为的容器 `lvaExtension1` 正在侦听端口 `44000` ，另一个名为的容器 `lvaExtension2` 正在侦听端口 `44001` 。 

   在实时视频分析拓扑中，您可以用不同的推理 Url 来实例化两个关系图，如下所示： 

   第一个实例：推理服务器 URL = `http://lvaExtension1:44001/score`    
   第二个实例：推理服务器 URL = `http://lvaExtension2:44001/score`
   
*使用 gRPC 协议*： 

* 使用实时视频分析模块1.0，当你使用 (gRPC) 协议的常规用途远程过程调用时，执行此操作的唯一方法是 gRPC 服务器通过不同端口公开不同的 AI 模型。 在 [此代码示例](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtension/topology.json)中，一个端口44000将公开所有 yolo 模型。 理论上，可以重写 yolo gRPC 服务器，以在端口44000和端口45000上的其他模型中公开一些模型。 

* 使用实时视频分析模块2.0，会将新属性添加到 gRPC 扩展节点。 此属性 **extensionConfiguration** 是可用作 gRPC 协定的一部分的可选字符串。 如果在单个推理服务器中打包了多个 AI 模型，则无需为每个 AI 模型公开节点。 相反，对于 graph 实例，你作为扩展提供程序，可以定义如何使用 **extensionConfiguration** 属性来选择不同的 AI 模型。 在执行期间，实时视频分析将此字符串传递给推断服务器，该服务器可以使用它来调用所需的 AI 模型。 

**我正在围绕 AI 模型构建 gRPC 服务器，并希望能够支持多个照相机或图形实例的使用。如何生成服务器？** 

 首先，请确保您的服务器可以一次处理多个请求或处理并行线程。 

例如，在以下 [实时视频分析 gRPC 示例](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/notebooks/Yolo/yolov3/yolov3-grpc-icpu-onnx/lvaextension/server/server.py)中设置了默认数量的并行通道： 

```
server = grpc.server(futures.ThreadPoolExecutor(max_workers=3)) 
```

在上述 gRPC 服务器实例化中，服务器一次只能打开每个相机或每个图形拓扑实例三个通道。 不要尝试将三个以上的实例连接到服务器。 如果尝试打开三个以上的通道，请求将处于挂起状态，直到存在现有通道。  

上面的 gRPC 服务器实现用于 Python 示例。 作为开发人员，您可以实现自己的服务器或使用前面的默认实现来增加辅助角色编号，将其设置为用于视频源的相机数。 

若要设置和使用多个照相机，可以实例化多个图形拓扑实例，每个实例指向相同或不同的推理服务器 (例如，上一段中提到的服务器) 。 

**我希望在做出推断决定之前，能够从上游接收多个帧。如何启用此功能？** 

当前的 [默认示例](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis) 在 *无状态* 模式下工作。 它们不会保留以前调用的状态，甚至不会保留调用者的状态。 这意味着多个拓扑实例可能调用同一推理服务器，但服务器无法区分正在调用的人员或每个调用方的状态。 

*使用 HTTP 协议*：

若要保持状态、每个调用方或图形拓扑实例，请使用调用方唯一的 HTTP 查询参数调用推断服务器。 例如，每个实例的推理服务器 URL 地址如下所示：  

第一个拓扑实例 = `http://lvaExtension:44000/score?id=1`<br/>
第二个拓扑实例 = `http://lvaExtension:44000/score?id=2`

… 

在服务器端，分数路由知道谁在调用。 如果 ID = 1，则它可以为该调用方或图形拓扑实例保持单独的状态。 然后，你可以将接收的视频帧保存到缓冲区中。 例如，使用数组或带有 DateTime 键的字典，并且值为帧。 然后，你可以定义服务器以在接收到 *x* 个帧数后处理 (推断) 。 

*使用 gRPC 协议*： 

对于 gRPC 扩展，每个会话都适用于单个相机源，因此不需要提供 ID。 现在，使用 extensionConfiguration 属性，您可以将视频帧存储在缓冲区中，并定义服务器来处理 (在接收 *x* 个帧后) 推理出的服务器。 

**特定容器上的所有 ProcessMediaStreams 是否都运行相同的 AI 模型？** 

不是。 开始或停止从图形实例中的最终用户进行的调用构成会话，或者可能存在相机断开连接或重新连接。 如果照相机是流式处理视频，则目标是保持一个会话。 

* 两个发送视频进行处理的摄像机会创建两个会话。 
* 一个摄像机转到一个图形，其中有两个 gRPC 扩展节点创建两个会话。 

每个会话都是实时视频分析与 gRPC 服务器之间的全双工连接，每个会话都可以有不同的模型或管道。 

> [!NOTE]
> 如果照相机断开连接或重新连接，并且相机在超出容差范围内处于脱机状态，则实时视频分析将打开与 gRPC 服务器的新会话。 服务器不需要在这些会话间跟踪状态。 

实时视频分析还为图形实例中的单个照相机添加了对多个 gRPC 扩展的支持。 您可以使用这些 gRPC 扩展以并行方式或二者的组合执行 AI 处理。 

> [!NOTE]
> 并行运行多个扩展将影响硬件资源。 在选择适合你的计算需求的硬件时，请记住这一点。 

**同时 ProcessMediaStreams 的最大数量是多少？** 

实时视频分析不会对此数字应用任何限制。  

**如何确定我的推断服务器应该使用 CPU、GPU 还是任何其他硬件加速器？** 

您的决定取决于开发的 AI 模型的复杂性以及您希望如何使用 CPU 和硬件加速器。 在开发 AI 模型时，可以指定模型应使用的资源以及应执行的操作。 

**是否在处理后如何实现用边界框存储图像？** 

目前，我们仅将边界框坐标作为推理消息提供。 可以生成可使用这些消息并覆盖视频帧上的边界框的自定义 MJPEG 流。  

## <a name="grpc-compatibility"></a>gRPC 兼容性 

**我如何知道媒体流描述符的必填字段是什么？** 

对于任何未提供值的字段，都将为其提供 [默认值，由 gRPC 指定](https://developers.google.com/protocol-buffers/docs/proto3#default)。  

实时视频分析使用协议缓冲区语言的 *proto3* 版本。 实时视频分析合同所使用的所有协议缓冲区数据都在 [协议缓冲区文件](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc)中提供。 

**如何确保我使用的是最新的协议缓冲区文件？** 

你可以在 " [协定文件" 站点](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc)上获取最新的协议缓冲区文件。 每当我们更新合同文件时，它们就会位于此位置。 无立即计划更新协议文件，因此，请在文件顶部查找包名称以了解版本。 此内容应为： 

```
microsoft.azure.media.live_video_analytics.extensibility.grpc.v1 
```

对这些文件的任何更新都将递增名称末尾的 "v 值"。 

> [!NOTE]
> 由于实时视频分析使用语言的 proto3 版本，因此字段是可选的，版本是向后和向前兼容的。 

**可以通过实时视频分析使用哪些 gRPC 功能？哪些功能是必需的，哪些是可选的？** 

如果) 协定 (了协议缓冲区，则可以使用任何服务器端 gRPC 功能。 

## <a name="monitoring-and-metrics"></a>监视和指标

**能否使用 Azure 事件网格监视边缘上的媒体图形？**

是。 你可以使用 Prometheus 度量值并将其发布到事件网格。 

**是否可以使用 Azure Monitor 查看云端或边缘的媒体图形的运行状况、指标和性能？**

是的，我们支持此方法。 若要了解详细信息，请参阅 [Azure Monitor 指标概述](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics)。

**是否有任何工具可用于简化监视媒体服务 IoT Edge 模块？**

Visual Studio Code 支持 Azure IoT 工具扩展，可用于轻松监视 LVAEdge 模块终结点。 可以使用此工具快速开始监视 IoT 中心的 "事件" 内置终结点，并查看从边缘设备路由到云的推理消息。 

此外，还可以使用此扩展编辑 LVAEdge 模块的模块克隆，以修改 media graph 设置。

有关详细信息，请参阅[监视和日志记录](monitoring-logging.md)一文。

## <a name="billing-and-availability"></a>计费和可用性

**如何对 IoT Edge 上的实时视频分析计费？**

有关计费的详细信息，请参阅 [媒体服务定价](https://azure.microsoft.com/pricing/details/media-services/)。

## <a name="next-steps"></a>后续步骤

[快速入门： IoT Edge 上的实时视频分析入门](get-started-detect-motion-emit-events-quickstart.md)
