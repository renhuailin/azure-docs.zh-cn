---
title: 连续视频录制和播放教程 - Azure 视频分析器
description: 本教程介绍如何使用 Azure 视频分析器将视频连续录制到云中并播放该录制内容。
ms.topic: tutorial
ms.date: 06/01/2021
ms.openlocfilehash: 2f3fc2421a2341974aa7ea7bdafeaf0123ea983e
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114602921"
---
# <a name="tutorial-continuous-video-recording-and-playback"></a>教程：连续视频录制和播放

本教程介绍如何使用 Azure 视频分析器将[视频连续录制](continuous-video-recording.md) (CVR) 到云，并播放录制内容。 此功能对于安全性和合规性等方案很有用，因为这些方案需要将相机中的素材存档保存数天、数周、数月，甚至数年。 

在本教程中，将：

> [!div class="checklist"]
> * 设置相关资源。
> * 检查执行 CVR 的代码。
> * 运行示例代码。
> * 检查结果并查看视频。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>建议的读前准备  

在开始之前，请阅读以下文章：

* [视频分析器概述](overview.md)
* [视频分析器术语](terminology.md)
* [视频分析器管道概念](pipeline.md) 
* [连续视频录制方案](continuous-video-recording.md)

## <a name="prerequisites"></a>先决条件

本教程的先决条件如下：[!INCLUDE [prerequisites](./includes/common-includes/csharp-prerequisites.md)]

## <a name="set-up-azure-resources"></a>设置 Azure 资源

[![部署到 Azure](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)
[!INCLUDE [resources](./includes/common-includes/azure-resources.md)]

## <a name="concepts"></a>概念

如[本文](pipeline.md)所述，使用视频分析器管道可以定义：

- 应从何处捕获媒体。
- 应如何处理媒体。
- 应将结果交付到何处。 
 
 若要完成 CVR，需要从支持 RTSP 的相机中捕获视频，并将其连续录制到[视频资源](terminology.md#video)。 下图显示了该管道的图形表示形式。 

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/continuous-video-recording/continuous-video-recording-overview.svg" alt-text="用于 CVR 的视频分析器管道":::
<!-- ./media/continuous-video-recording-tutorial/continuous-video-recording-overview.svg -->

在本教程中，你将通过一个使用 [Live555 Media Server](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555) 生成的 Edge 模块来模拟 RTSP 相机。 在管道中，你将使用 [RTSP 源](pipeline.md#rtsp-source)节点获取实时源，并将该视频发送到[视频接收器节点](pipeline.md#video-sink)，后者会将视频录制到视频分析器帐户中。 本教程中将使用的视频是[公路交叉口示例视频](https://lvamedia.blob.core.windows.net/public/camera-300s.mkv)。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LTY4]

在本教程中，将：

1. 设置开发环境。
1. 部署所需的 Edge 模块。
1. 创建和部署实时管道。
1. 解释结果。
1. 清理资源。

## <a name="set-up-your-development-environment"></a>设置开发环境
[!INCLUDE [setup development environment](./includes/set-up-dev-environment/csharp/csharp-set-up-dev-env.md)]
  

## <a name="examine-the-sample-files"></a>检查示例文件

在 Visual Studio Code 中，浏览到 src/cloud-to-device-console-app 文件夹。 在这里，你将看到创建的 appsettings.json 文件以及其他一些文件：

* c2d-console-app.csproj：Visual Studio Code 的项目文件。
* operations.json：该文件列出了你将运行的不同操作。
* Program.cs：执行以下操作的示例程序代码：
    * 加载应用设置。
    * 调用视频分析器 Edge 模块所公开的直接方法。 可以通过调用模块的[直接方法](direct-methods.md)来使用该模块分析实时视频流。
    * 暂停以检查“终端”窗口中程序的输出以及 OUTPUT 窗口中模块生成的事件 。
    * 调用直接方法以清理资源。

## <a name="run-the-program"></a>运行程序 

1. 在 Visual Studio Code 中，转到 src/cloud-to-device-console-app/operations.json。
1. 在 pipelineTopologySet 节点下，编辑以下内容：

    `"pipelineTopologyUrl" : "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/cvr-video-sink/topology.json" `
1. 接下来，在 livePipelineSet 和 pipelineTopologyDelete 节点下，确保 topologyName 的值与上述管道拓扑中的 name 属性的值匹配   ：

    `"topologyName" : "CVRToVideoSink"`  
1. 在浏览器中打开[管道拓扑](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/cvr-video-sink/topology.json)，查看 videoName（硬编码为 `sample-cvr-video`）。 对于教程来说，这是可以接受的。 在生产环境中，应注意确保每个唯一的 RTSP 相机都会录制到一个具有唯一名称的视频资源中。
1. 选择 F5 以启动调试会话。 在“终端”窗口中，你将看到一些输出的消息。
1. operations.json 文件首先调用 `pipelineTopologyList` 和 `livePipelineList`。 如果在先前的快速入门或教程后清理了资源，此操作会返回空列表，然后暂停以便你能够选择 Enter，如下所示：

    ```
    --------------------------------------------------------------------------
    Executing operation pipelineTopologyList
    -----------------------  Request: pipelineTopologyList  --------------------------------------------------
    {
      "@apiVersion": "1.0"
    }
    ---------------  Response: pipelineTopologyList - Status: 200  ---------------
    {
      "value": []
    }
    --------------------------------------------------------------------------
    Executing operation WaitForInput
    Press Enter to continue
    ```

1. 在“终端”窗口中选择 Enter 后，会执行下一组直接方法调用 ：
   * 使用上述 `topologyUrl` 对 `pipelineTopologySet` 的调用
   * 使用以下正文对 `livePipelineSet` 的调用
     
     ```
     {
       "@apiVersion": "1.0",
       "name": "Sample-Pipeline-1",
       "properties": {
         "topologyName": "CVRToVideoSink",
         "description": "Sample pipeline description",
         "parameters": [
           {
             "name": "rtspUrl",
             "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
           },
           {
             "name": "rtspUserName",
             "value": "testuser"
           },
           {
             "name": "rtspPassword",
             "value": "testpassword"
           }
         ]
       }
     }
     ```
   * 对 `livePipelineActivate` 的调用，用于启动实时管道和视频流
   * 对 `livePipelineList` 的第二次调用，显示实时管道处于运行状态 
1. “终端”窗口中的输出现在会在出现“按 Enter 继续”提示时暂停 。 此时请勿选择 Enter。 向上滚动，查看调用的直接方法的 JSON 响应有效负载。
1. 如果现在切换到 Visual Studio Code 中的“输出”窗口，将看到视频分析器 Edge 模块向 IoT 中心发送的消息。


   下节中讨论了这些消息。
1. 实时管道将继续运行并录制视频。 RTSP 模拟器不断循环源视频。 若要停止录制，请返回到“终端”窗口并选择 Enter 。 接下来会使用以下方法执行一系列调用，以清理资源：

   * 调用 `livePipelineDeactivate` 停用实时管道。
   * 调用 `livePipelineDelete` 删除实时管道。
   * 调用 `pipelineTopologyDelete` 删除拓扑。
   * 对 `pipelineTopologyList` 的最后一次调用显示该列表现为空。

## <a name="interpret-the-results"></a>解释结果 

运行实时管道时，视频分析器 Edge 模块会将某些诊断和操作事件发送到 IoT Edge 中心。 这些事件即你在 Visual Studio Code 的“输出”窗口中看到的消息。 这些事件包含 `body` 部分和 `applicationProperties` 部分。 要了解这些部分表示的内容，请参阅[创建和读取 IoT 中心消息](../../iot-hub/iot-hub-devguide-messages-construct.md)。

在下面的消息中，视频分析器 Edge 模块定义了应用程序属性和正文内容。


## <a name="diagnostics-events"></a>诊断事件 

### <a name="mediasession-established-event"></a>MediaSession 建立的事件

激活实时管道后，RTSP 源节点尝试连接到在 rtspsim 模块中运行的 RTSP 服务器。 如果成功，它将输出此事件：

```
[IoTHubMonitor] [9:42:18 AM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/videoanalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sources/rtspSource",
    "eventType": "Microsoft.VideoAnalyzers.Diagnostics.MediaSessionEstablished",
    "eventTime": "2021-04-09T09:42:18.1280000Z"
  }
}
```

* 此消息是一个诊断事件 (Microsoft.VideoAnalyzers.Diagnostics.MediaSessionEstablished)。 它指示 RTSP 源节点（使用者）建立了与 RTSP 模拟器的连接，并开始接收（模拟的）实时馈送。
* applicationProperties 中的 subject 部分引用生成消息的管道拓扑中的节点。 在本例中，该消息来自 RTSP 源节点。
* applicationProperties 中的 eventType 部分指示这是诊断事件。
* eventTime 部分指示事件发生的时间。
* body 部分包含有关诊断事件的数据，在本例中是 [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) 的详细信息。

## <a name="operational-events"></a>操作事件 

### <a name="recordingstarted-event"></a>RecordingStarted 事件

当视频接收器节点开始录制媒体时，将发出此事件，其类型为 Microsoft.VideoAnalyzers.Pipeline.OperationalRecordingStarted：

```
[IoTHubMonitor] [9:42:38 AM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "outputType": "videoName",
    "outputLocation&quot;: &quot;sample-cvr-video"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/videoAnalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sinks/videoSink",
    "eventType": "Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingStarted",
    "eventTime": "2021-04-09T09:42:38.1280000Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

applicationProperties 中的 subject 部分引用实时管道中的视频接收器节点，该节点生成此消息。

body 部分包含有关输出位置的信息。 在本例中，即要将视频录制到的视频分析器资源的名称。

### <a name="recordingavailable-event"></a>RecordingAvailable 事件

顾名思义，RecordingStarted 事件在录制开始时发送，但媒体数据可能尚未上传到视频资源。 视频接收器节点上传媒体后，将发出类型为 Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingAvailable 的事件：

```
[IoTHubMonitor] [[9:43:38 AM] Message received from [ava-sample-device/avaedge]:
{
  "body": {
    "outputType": "videoName",
    "outputLocation&quot;: &quot;sample-cvr-video"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/videoAnalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sinks/videoSink",
    "eventType": "Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingAvailable",
    "eventTime": "2021-04-09T09:43:38.1280000Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

此事件指示已向视频资源写入足够的数据，播放器或客户端可以开始播放视频。

applicationProperties 中的 subject 部分引用实时管道中的视频接收器节点，该节点生成此消息。

body 部分包含有关输出位置的信息。 在本例中，即要将视频录制到的视频分析器资源的名称。

### <a name="recordingstopped-event"></a>RecordingStopped 事件

停用实时管道后，视频接收器节点会停止录制媒体。 它发出类型为 Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingStopped 的此事件：

```
[IoTHubMonitor] [11:33:31 PM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "outputType": "videoName",
    "outputLocation&quot;: &quot;sample-cvr-video"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/videoAnalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sinks/videoSink",
    "eventType": "Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingStopped",
    "eventTime": "2021-04-10T11:33:31.051Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

此事件指示录制已停止。

applicationProperties 中的 subject 部分引用实时管道中的视频接收器节点，该节点生成此消息。

body 部分包含有关输出位置的信息，在本例中是视频录制到的视频分析器资源的名称。

## <a name="playing-back-the-recording"></a>播放录制内容

可通过登录到 Azure 门户并观看视频来检查实时管道创建的视频分析器视频资源。

1. 打开 Web 浏览器，并转到 [Azure 门户](https://portal.azure.com/)。 输入登录到门户所需的凭据。 默认视图是服务仪表板。
1. 在订阅资源中找到视频分析器帐户，并打开“帐户”窗格。
1. 在“视频分析器”部分中选择“视频” 。
1. 你会发现以名称 `sample-cvr-video` 列出的视频。 这是在管道拓扑文件中选择的名称。
1. 选择视频。
1. 视频“详细信息”页随即将打开并自动开始播放。

[!INCLUDE [activate-deactivate-pipeline](./includes/common-includes/activate-deactivate-pipeline.md)]

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [prerequisites](./includes/common-includes/clean-up-resources.md)]

## <a name="next-steps"></a>后续步骤

* 使用支持 RTSP 的 [IP 相机](https://en.wikipedia.org/wiki/IP_camera)，而不是使用 RTSP 模拟器。 可以在 [ONVIF 一致性产品页](https://www.onvif.org/conformant-products/)上查找符合配置文件 G、S 或 T 的设备来搜索支持 RTSP 的 IP 照相机。
* 使用 AMD64 或 X64 Linux 设备（与使用 Azure Linux VM 相比）。 此设备必须与 IP 相机位于同一网络中。 按照[在 Linux 上安装 Azure IoT Edge 运行时](../../iot-edge/how-to-install-iot-edge.md)中的说明进行操作。 然后按照[将首个 IoT Edge 模块部署到虚拟 Linux 设备](../../iot-edge/quickstart-linux.md)快速入门中的说明进行操作，将设备注册到 Azure IoT 中心。
