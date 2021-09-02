---
title: 使用视频记录并流式传输推理元数据 - Azure 视频分析器
description: 本教程介绍如何使用 Azure 视频分析器将视频和推理元数据录制到云中，并随视觉推理元数据一起播放录制内容。
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: 3122ea07fdab20c93ed4720d0c43f180cce5306d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778306"
---
# <a name="tutorial-record-and-stream-inference-metadata-with-video"></a>教程：使用视频记录并流式传输推理元数据
  
本教程介绍如何使用 Azure 视频分析器将实时视频和推理元数据录制到云中，并随视觉推理元数据一起播放录制内容。 在此用例中，你将连续录制视频，同时使用自定义模型来检测对象 (yoloV3) 并使用视频分析器处理器（对象跟踪器）来跟踪对象 。 随着视频连续进行录制，正在检测和跟踪的对象推理元数据也将被录制。 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>建议的读前准备  

在开始之前，请阅读以下文章：

* [IoT Edge 上的 Azure 视频分析器概述](overview.md)
* [IoT Edge 上的 Azure 视频分析器术语](terminology.md)
* [视频分析器管道概念](pipeline.md) 
* [连续视频录制](continuous-video-recording.md)
* [快速入门：使用自己的模型分析实时视频 - HTTP](analyze-live-video-use-your-model-http.md)
* [快速入门：跟踪实时视频中的对象](track-objects-live-video.md)

## <a name="prerequisites"></a>先决条件

本教程的先决条件如下：[!INCLUDE [prerequisites](./includes/common-includes/csharp-prerequisites.md)]

## <a name="set-up-azure-resources"></a>设置 Azure 资源

[![部署到 Azure](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)
[!INCLUDE [resources](./includes/common-includes/azure-resources.md)]

## <a name="overview"></a>概述
> [!div class="mx-imgBorder"]
> 管道

该图以图画形式呈现了[管道](pipeline.md)以及用于完成所需方案的其他模块。 共涉及三个 IoT Edge 模块：
* 视频分析器模块。
* Edge 模块，它在 HTTP 终结点后面运行 AI 模型。 此 AI 模块使用 [YOLOv3](https://github.com/Azure/video-analyzer/tree/main/edge-modules/extensions/yolo/yolov3) 模型，该模型能够检测许多类型的对象。
* [RTSP 模拟器模块](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555)用于模拟 RTSP 摄像机。

如图所示，你将使用管道中的 [RTSP 源](pipeline.md#rtsp-source)节点捕获模拟的实时视频（高速公路上的交通流视频），并将该视频发送到两条路径：

* 第一个路径是 HTTP 扩展节点。 HTTP 扩展节点扮演代理的角色。 它将每第 10 个视频帧转换为指定的图像类型。 然后，它将图像通过 HTTP 转发到另一个 Edge 模块，该模块在 HTTP 终结点后运行一个 AI 模型。 在此示例中，使用 YOLOv3 模型构建该 Edge 模块，该模型能够检测多种类型的对象。 HTTP 扩展处理器节点收集检测结果，并将这些结果和所有视频帧（不只是第 10 帧）发送到对象跟踪器节点。 对象跟踪器节点使用光流技术来跟踪 9 帧中未应用 AI 模型的对象。 跟踪器节点将其结果发布到视频接收器节点和 IoT 中心接收器节点。 [视频接收器](pipeline.md#video-sink)节点将使用随录制的视频一起播放的对象跟踪器节点中的推理元数据。 然后 [IoT 中心消息接收器](pipeline.md#iot-hub-message-sink)节点将这些事件发送到 [IoT Edge 中心](../../iot-fundamentals/iot-glossary.md#iot-edge-hub)。

* 第二条路径是直接从 RTSP 源到视频接收器节点来完成连续视频录制。 本教程中将使用的视频是[公路交叉口示例视频](https://lvamedia.blob.core.windows.net/public/camera-300s.mkv)。

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

在 Visual Studio Code 中，浏览到 src/edge。 你将看到创建的 .env 文件以及一些部署模板文件。 该模板定义了要部署到边缘设备 (Azure Linux VM) 的边缘模块。 .env 文件包含这些模板中所用变量的值，例如视频分析器凭据。

打开 src/edge/deployment.yolov3.template.json。 “模块”部分下有三个条目，对应于前面“概述”部分中列出的项目

* **avaedge** - 这是 IoT Edge 上的视频分析器模块。
* yolov3：这是使用 YOLO v3 模型构建的 AI 模块。
* rtspsim：这是 RTSP 模拟器。


接下来，浏览到 src/cloud-to-device-console-app 文件夹。 在这里，你将看到创建的 appsettings.json 文件以及其他一些文件：

* c2d-console-app.csproj：Visual Studio Code 的项目文件。
* operations.json：该文件列出了你将运行的不同操作。
* Program.cs：执行以下操作的示例程序代码：
    * 加载应用设置。

    * 调用 IoT Edge 上的视频分析器模块所公开的直接方法。 可以通过调用模块的[直接方法](direct-methods.md)来使用该模块分析实时视频流。

    * 暂停以检查“终端”窗口中程序的输出以及 OUTPUT 窗口中模块生成的事件 。
    * 调用直接方法以清理资源。

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>生成并部署 IoT Edge 部署清单 

1. 右键单击 src/edge/deployment.yolov3.template.json 文件，然后选择“生成 IoT Edge 部署清单”。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/analyze-live-video-use-your-model-http/generate-deployment-manifest.png" alt-text="生成 IoT Edge 部署清单的屏幕截图":::

    * 随即将在 src/edge/config 文件夹中创建一个清单文件 deployment.yolov3.amd64.json 。
1. 右键单击 src/edge/config/deployment.yolov3.amd64.json，然后选择“为单个设备创建部署”。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/analyze-live-video-use-your-model-http/deployment-single-device.png" alt-text= "Screenshot of Create Deployment for Single Device":::

1. 如果系统提示你选择 IoT 中心设备，请选择“ava-sample-iot-edge-device”。
1. 大约 30 秒后，在该窗口的左下角刷新 Azure IoT 中心。 边缘设备现在显示以下已部署的模块：

   - avaedge 模块，它是视频分析器模块。
   - rtspsim 模块，可模拟 RTSP 服务器，充当实时视频源的源。
   - yolov3 模块，它是 YoloV3 对象检测模型，该模型将计算机视觉应用于图像并返回对象类型的多个类

     > [!div class="mx-imgBorder"]
     > :::image type="content" source="./media/vscode-common-screenshots/avaextension.png" alt-text= "Screenshot of YoloV3 object detection model":::
     
## <a name="create-and-deploy-the-live-pipeline"></a>创建和部署实时管道

### <a name="edit-the-sample-files"></a>编辑示例文件

1. 在 Visual Studio Code 中，打开“扩展”选项卡（或按 Ctrl+Shift+X），然后搜索“Azure IoT 中心”。
1. 右键单击并选择“扩展设置”。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="扩展设置":::
1. 搜索并启用“显示详细消息”。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="显示详细消息":::
1. 转到 src/cloud-to-device-console-app/operations.json。
1. 在 pipelineTopologySet 节点下，编辑以下内容
 
    `"pipelineTopologyUrl" : "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/cvr-with-httpExtension-and-objectTracking/topology.json" `
    
1. 接下来，在 livePipelineSet 和 pipelineTopologyDelete 节点下，确保 topologyName 的值与上述管道拓扑中的 name 属性的值匹配   ：

    `"pipelineTopologyName" : "CVRHttpExtensionObjectTracking"`
1. 在浏览器中打开[管道拓扑](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/cvr-with-httpExtension-and-objectTracking/topology.json)，查看 videoName（硬编码为 `sample-cvr-with-inference-metadata`）。 对于教程来说，这是可以接受的。 在生产环境中，应注意确保每个唯一的 RTSP 相机都会录制到一个具有唯一名称的视频资源中。  

1. 检查 HTTP 扩展节点的设置。

  ```=
     "samplingOptions":{
         "skipSamplesWithoutAnnotation":"false",
         "maximumSamplesPerSecond":"2"
    }
  ```

这里，`skipSamplesWithoutAnnotation` 设置为 `false`，因为扩展节点需要将所有帧（无论它们是否具有推理结果）传递到下游对象跟踪器节点。 对象跟踪器能够跟踪大约超过 15 帧的对象。 AI 模型具有用于处理的最大 FPS，这是 `maximumSamplesPerSecond` 应设置为的最大值。


## <a name="run-the-sample-program"></a>运行示例程序

1. 选择 F5 以启动调试会话。 在“终端”窗口中，你将看到一些输出的消息。
1. operations.json 文件首先调用 pipelineTopologyList 和 livePipelineList。 如果在先前的快速入门或教程后清理了资源，此操作会返回空列表，然后暂停以便你能够选择 Enter，如下所示：
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
   * 使用上述 pipelinetopologyUrl 对 GraphTopologySet 的调用
   * 使用以下正文对 livePipelineSet 的调用
     
        ```
        {
          "@apiVersion": "1.0",
          "name": "Sample-Pipeline-1",
          "properties": {
            "topologyName": "CVRHttpExtensionObjectTracking",
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
   * 对 livePipelineActivate 的调用，用于启动实时管道和视频流。
   * 对 livePipelineList 的第二个调用，显示实时管道处于运行状态。
1. “终端”窗口中的输出暂停并显示“按 Enter 继续”提示。 暂时不要选择 Enter。 向上滚动，查看调用的直接方法的 JSON 响应有效负载。
1. 切换到 Visual Studio Code 中的“输出”窗口。 可看到 IoT Edge 上的视频分析器模块正发送到 IoT 中心的消息。
1. 管道将继续运行并输出结果。 RTSP 模拟器不断循环源视频。 若要停止实时管道，请返回“终端”窗口，并选择 Enter。
1. 接下来会执行一系列调用，以清理资源：

    * 调用 livePipelineDeactivate 停用实时管道。
    * 调用 livePipelineDelete 删除实时管道。
    * 调用 pipelineTopologyDelete 删除管道拓扑。
    * 对 pipelineTopologyList 的最后一次调用将显示空列表。       

## <a name="interpret-results"></a>解释结果

运行实时管道时，来自 HTTP 扩展处理器节点的结果将通过对象跟踪器节点，再经过 IoT 中心接收器节点传递到 IoT 中心。 “输出”窗口中显示的消息包含正文部分和 applicationProperties 部分。 有关详细信息，请参阅[创建和读取 IoT 中心消息](../../iot-hub/iot-hub-devguide-messages-construct.md)。

在下面的消息中，视频分析器模块定义了应用程序属性和正文内容。

## <a name="diagnostics-events"></a>诊断事件
### <a name="mediasessionestablished-event"></a>MediaSessionEstablished 事件

激活实时管道后，RTSP 源节点尝试连接到在 rtspsim-live555 容器上运行的 RTSP 服务器。 如果连接成功，则打印以下事件。 事件类型为 Microsoft.VideoAnalyzer.Diagnostics.MediaSessionEstablished。

```
[IoTHubMonitor] [9:42:18 AM] Message received from [avasample-iot-edge-device/avaedge]:
{  "body": {
    "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 nnn.nn.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=Z00AKeKQCgC3YC3AQEBpB4kRUA==,aO48gA==\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/videoAnalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sources/rtspSource",
    "eventType": "Microsoft.VideoAnalyzer.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-04-09T16:42:18.1280000Z"
  }
}
```

在此消息中，请注意以下详细信息：

* 消息为诊断事件。 MediaSessionEstablished 指示 RTSP 源节点（使用者）已与 RTSP 模拟器连接，并已开始接收（模拟的）实时馈送。
* 在 applicationProperties 中，subject 指示消息是从实时管道中的 RTSP 源节点生成的。
* 在 applicationProperties 中，eventType 指示此事件是一个诊断事件。
* eventTime 指示事件的发生时间。
* 正文包含有关诊断事件的数据。 在本例中，数据包含[会话描述协议 (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) 详细信息。

## <a name="operational-events"></a>操作事件

### <a name="object-tracking-events"></a>对象跟踪事件

HTTP 扩展处理器节点将第 0 帧、第 15 帧、第 30 帧… 发送到 yolov3 模块，并接收推理结果。 然后，它将这些结果以及所有视频帧发送到对象跟踪器节点。 假设在第 0 帧上检测到对象，对象跟踪器将为该对象分配唯一的 sequenceId。 然后，在第 1 帧、第 2 帧、…、第 14 帧，如果它可以跟踪该对象，它将输出具有相同 sequenceId 的结果。 在结果的以下代码片段中，请注意 `sequenceId` 是重复的，但边界框的位置随着对象移动发生了变化。

从第 M 帧：

```json
  {
    "type": "entity",
    "subtype": "objectDetection",
    "inferenceId": "4d325fc4dc7a43b2a781bf7d6bdb3ff0",
    "sequenceId": "0999a1dde5b241c3a0b2db025f87ab32",
    "entity": {
      "tag": {
        "value": "car",
        "confidence": 0.95237225
      },
      "box": {
        "l": 0.0025893003,
        "t": 0.550063,
        "w": 0.1086607,
        "h": 0.12116724
      }
    }
  },
```

从第 N 帧：

```json
{
  "type": "entity",
  "subtype": "objectDetection",
  "inferenceId": "317aafdab7e940388be1e4c4cc58c366",
  "sequenceId": "0999a1dde5b241c3a0b2db025f87ab32",
  "entity": {
    "tag": {
      "value": "car",
      "confidence": 0.95237225
    },
    "box": {
      "l": 0.0027777778,
      "t": 0.54901963,
      "w": 0.108333334,
      "h": 0.12009804
    }
  }
},
```

### <a name="recordingstarted-event"></a>RecordingStarted 事件
当视频接收器节点开始录制媒体时，将发出此事件，其类型为 Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingStarted：

```
[IoTHubMonitor] [9:42:38 AM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "outputType": "videoName",
    "outputLocation&quot;: &quot;sample-cvr-with-inference-metadata"
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
    "outputLocation&quot;: &quot;sample-cvr-with-inference-metadata"
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
[IoTHubMonitor] [11:33:31 PM] Message received from [ava-sample-device/avaedge]:
{
  "body": {
    "outputType": "videoName",
    "outputLocation&quot;: &quot;sample-cvr-with-inference-metadata"
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

## <a name="streaming-the-recording-with-visual-inference-metadata"></a>流式传输录制内容和视觉推理元数据

可通过登录到 Azure 门户并观看视频来检查实时管道创建的视频分析器视频资源。

1. 打开 Web 浏览器，并转到 [Azure 门户](https://portal.azure.com/)。 输入登录到门户所需的凭据。 默认视图是服务仪表板。
1. 在订阅资源中找到视频分析器帐户，并打开“帐户”窗格。
1. 在“视频分析器”列表中选择“视频” 。
1. 你会发现以名称 `sample-cvr-with-inference-metadata` 列出的视频。 这是在管道拓扑文件中选择的名称。
1. 选择视频。
1. 在视频“详细信息”页上，单击“播放”图标
1. 要在视频中以边界框的形式查看推理元数据，请单击“边界框”图标（用红色圈出）
   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/record-stream-inference-data-with-video/video-playback.png" alt-text="视频播放的屏幕截图":::

[!INCLUDE [activate-deactivate-pipeline](./includes/common-includes/activate-deactivate-pipeline.md)]

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [prerequisites](./includes/common-includes/clean-up-resources.md)]

## <a name="next-steps"></a>后续步骤

* 使用支持 RTSP 的 [IP 相机](https://en.wikipedia.org/wiki/IP_camera)，而不是使用 RTSP 模拟器。 可以在 [ONVIF 一致性产品页](https://www.onvif.org/conformant-products/)上查找符合配置文件 G、S 或 T 的设备来搜索支持 RTSP 的 IP 照相机。
* 使用 AMD64 或 X64 Linux 设备（与使用 Azure Linux VM 相比）。 此设备必须与 IP 相机位于同一网络中。 按照[在 Linux 上安装 Azure IoT Edge 运行时](../../iot-edge/how-to-install-iot-edge.md)中的说明进行操作。 然后按照[将首个 IoT Edge 模块部署到虚拟 Linux 设备](../../iot-edge/quickstart-linux.md)快速入门中的说明进行操作，将设备注册到 Azure IoT 中心。
 
