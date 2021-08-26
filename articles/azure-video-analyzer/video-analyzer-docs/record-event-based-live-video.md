---
title: 将基于事件的视频录制到云中并从云播放教程 - Azure
description: 本教程介绍如何使用 Azure 视频分析器将基于事件的视频录制到云中并从云中播放。
ms.topic: tutorial
ms.date: 06/01/2021
ms.openlocfilehash: 018e01e781e67ebe58d1337443813e1ca973a9e0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733308"
---
# <a name="tutorial-event-based-video-recording-and-playback"></a>教程：基于事件的视频录制和播放

在本教程中，你将了解如何使用 Azure 视频分析器选择性地将实时视频源的部分内容录制到云中的视频分析器。 此用例在本教程中称为[基于事件的视频录制](event-based-video-recording-concept.md) (EVR)。 要录制实时视频的部分内容，你将使用对象检测 AI 模型在视频中查找对象，并仅在检测到某种类型的对象时录制视频剪辑。 你还将了解如何使视频分析器播放录制的视频剪辑。 对于需要保留所需视频剪辑的各种场景，此功能非常有用。 



[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>建议的读前准备  

在开始之前，请阅读以下文章：

* [Azure 视频分析器概述](overview.md)
* [Azure 视频分析器术语](terminology.md)
* [视频分析器管道概念](pipeline.md) 
* [基于事件的视频录制](event-based-video-recording-concept.md)
* [教程：开发 IoT Edge 模块](../../iot-edge/tutorial-develop-for-linux.md)
* [如何编辑 deployment.*.template.json](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)
* 关于[如何在 IoT Edge 部署清单中声明路由](../../iot-edge/module-composition.md#declare-routes)的部分

## <a name="prerequisites"></a>先决条件

本教程的先决条件如下：
* 包含活动订阅的 Azure 帐户。 如果没有帐户，可免费[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

    [!INCLUDE [azure-subscription-permissions](./includes/common-includes/azure-subscription-permissions.md)]
* 在计算机上[安装 Docker](https://docs.docker.com/desktop/#download-and-install)。
* 包含以下扩展的 [Visual Studio Code](https://code.visualstudio.com/)：
    * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)。

## <a name="set-up-azure-resources"></a>设置 Azure 资源

[![部署到 Azure](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)
[!INCLUDE [resources](./includes/common-includes/azure-resources.md)]

## <a name="overview"></a>概述

基于事件的视频录制是指由事件触发的视频录制过程。 可以从以下内容生成该事件：
- 视频信号本身的处理，例如在视频中检测到移动的对象时。
- 独立源，例如打开门。 

或者，可以仅在推理服务检测到特定事件发生时触发录制。 本教程将使用在高速公路上移动的车辆的视频，并在检测到卡车时录制视频剪辑。

> [!div class="mx-imgBorder"]
> 管道

该图以图画形式呈现了[管道](pipeline.md)以及用于完成所需方案的其他模块。 共涉及四个 IoT Edge 模块：

* IoT Edge 上的视频分析器模块。
* Edge 模块，它在 HTTP 终结点后面运行 AI 模型。 此 AI 模块使用 [YOLOv3](https://github.com/Azure/video-analyzer/tree/main/edge-modules/extensions/yolo/yolov3) 模型，该模型能够检测许多类型的对象。
* 一个用于筛选对象并对其进行计数的自定义模块，该模块在图中称为对象计数器。 在本教程中，你将生成一个对象计数器并对其进行部署。
* [RTSP 模拟器模块](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555)用于模拟 RTSP 摄像机。
    
如图所示，你将使用管道中的 [RTSP 源](pipeline.md#rtsp-source)节点捕获模拟的实时视频（高速公路上的交通流视频），并将该视频发送到两条路径：

* 第一个路径是 HTTP 扩展节点。 该节点对视频帧进行采样，结果将作为使用 `samplingOptions` 字段设置的值，然后将这些帧作为图像中继到 AI 模块 YOLOv3，这是一个对象检测器。 该节点接收结果，这些结果是模型检测到的对象（交通流中的车辆）。 然后，HTTP 扩展节点会通过 IoT 中心消息接收器节点向 IoT Edge 中心发布结果。

* 设置了 objectCounter 模块，目的是从 IoT Edge 中心接收消息，其中包括对象检测结果（交通流中的车辆）。 该模块将检查这些消息，并查找通过设置进行配置的特定类型的对象。 当找到此类对象时，此模块将向 IoT Edge 中心发送消息。 然后，系统将这些“找到对象”消息路由到管道的 IoT 中心源节点。 接收到此类消息后，管道中的 IoT 中心源节点会触发[信号入口处理器](pipeline.md#signal-gate-processor)节点。 然后，信号入口处理器节点会在配置的时间内处于开启状态。 在此持续时间内，视频流会经过入口到达视频接收器节点。 然后，实时流的这一部分将通过[视频接收器](pipeline.md#video-sink)节点被记录到视频分析器帐户中的[视频](terminology.md#video)内。 本教程中将使用的视频是[公路交叉口示例视频](https://lvamedia.blob.core.windows.net/public/camera-300s.mkv)。

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

打开 src/edge/deployment.objectCounter.template.json。 “模块”部分下有四个条目，对应于前面“概述”部分中列出的项目

* **avaedge**：这是视频分析器模块。
* yolov3：这是使用 YOLO v3 模型构建的 AI 模块。
* rtspsim：这是 RTSP 模拟器。
* objectCounter：这是在来自 yolov3 的结果中查找特定对象的模块。

对于 objectCounter 模块，请参阅用于“image”值的字符串 (${MODULES.objectCounter})。 其基础是有关开发 IoT Edge 模块的[教程](../../iot-edge/tutorial-develop-for-linux.md)。 Visual Studio Code 将自动发现 objectCounter 模块的代码位于 src/edge/modules/objectCounter 下。 

阅读[本部分](../../iot-edge/module-composition.md#declare-routes)，了解如何在 IoT Edge 部署清单中声明路由。 然后检查模板 JSON 文件中的路由。 请注意：

> [!NOTE]
> 检查 objectCounter 模块的所需属性，这些属性设置为查找标记为“卡车”的对象，并且可信度至少为 50%。

接下来，浏览到 src/cloud-to-device-console-app 文件夹。 在这里，你将看到创建的 appsettings.json 文件以及其他一些文件：

* c2d-console-app.csproj：Visual Studio Code 的项目文件。
* operations.json：该文件列出了你将运行的不同操作。
* Program.cs：执行以下操作的示例程序代码：
    * 加载应用设置。

    * 调用视频分析器模块所公开的直接方法。 可以通过调用模块的[直接方法](direct-methods.md)来使用该模块分析实时视频流。

    * 暂停以检查“终端”窗口中程序的输出以及 OUTPUT 窗口中模块生成的事件 。
    * 调用直接方法以清理资源。

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>生成并部署 IoT Edge 部署清单 

部署清单定义要部署到边缘设备的模块以及这些模块的配置设置。 请按照以下步骤从模板文件生成清单，然后将其部署到边缘设备。

使用 Visual Studio Code，按照[以下说明](../../iot-edge/tutorial-develop-for-linux.md#build-and-push-your-solution)登录到 Docker。 然后选择“生成并推送 IoT Edge 解决方案”。 在此步骤中使用 src/edge/deployment.objectCounter.template.json。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/build-push.png" alt-text="生成并推送 IoT Edge 解决方案":::

此操作会生成用于对象计数的 objectCounter 模块，并将图像推送到 Azure 容器注册表。

* 检查确认已在 .env 文件中定义 CONTAINER_REGISTRY_USERNAME_myacr 和 CONTAINER_REGISTRY_PASSWORD_myacr 环境变量。

此步骤会在 src/edge/config/deployment.objectCounter.amd64.json 创建 IoT Edge 部署清单。 右键单击该文件，然后选择“为单个设备创建部署”。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/create-deployment-single-device.png" alt-text="为单个设备创建部署":::

如果这是你第一次接触有关视频分析器的教程，Visual Studio Code 将提示你输入 IoT 中心连接字符串。 可以从 appsettings.json 文件中复制字符串。

[!INCLUDE [provide-builtin-endpoint](./includes/common-includes/provide-builtin-endpoint.md)]

接下来，Visual Studio Code 会要求你选择 IoT 中心设备。 选择 IoT Edge 设备（应为 avasample-iot-edge-device）。

在此阶段，将启动将边缘模块部署到 IoT Edge 设备的过程。
大约 30 秒后，在 Visual Studio Code 的左下部分刷新 Azure IoT 中心。 你应该会看到已部署六个模块，它们的名称分别为 $edgeAgent、$edgeHub avaedge、rtspsim、yolov3 和 objectCounter。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/modules.png" alt-text="部署的 4 个模块":::

## <a name="run-the-program"></a>运行程序

1. 在 Visual Studio Code 中，打开“扩展”选项卡（或按 Ctrl+Shift+X），然后搜索“Azure IoT 中心”。
1. 右键单击并选择“扩展设置”。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="扩展设置":::
1. 搜索并启用“显示详细消息”。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="显示详细消息":::
1. 转到 src/cloud-to-device-console-app/operations.json。
1. 在 pipelineTopologySet 节点下，编辑以下内容：


    `"pipelineTopologyUrl" : "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/evr-hubMessage-video-sink/topology.json" `
    
1. 接下来，在 livePipelineSet 和 pipelineTopologyDelete 节点下，确保 topologyName 的值与上述管道拓扑中的 name 属性的值匹配   ：

    `"pipelineTopologyName" : "EVRtoVideoSinkOnObjDetect"`
1. 在浏览器中打开[管道拓扑](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/evr-hubMessage-video-sink/topology.json)，查看 videoName（硬编码为 `sample-evr-video`）。 对于教程来说，这是可以接受的。 在生产环境中，应注意确保每个唯一的 RTSP 相机都会录制到一个具有唯一名称的视频资源中。
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
            "topologyName": "EVRtoVideoSinkOnObjDetect",
            "description": "Sample topology description",
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
    
   * 对 livePipelineActivate 的调用，用于启动实时管道和视频流
   * 对 livePipelineList 的第二次调用，用于显示实时管道处于运行状态 
     
1. “终端”窗口中的输出现在会在出现“按 Enter 继续”提示时暂停 。 此时请勿选择 Enter。 向上滚动，查看调用的直接方法的 JSON 响应有效负载。
1. 如果现在切换到 Visual Studio Code 中的“输出”窗口，则将看到视频分析器模块向 IoT 中心发送的消息。

   下节中讨论了这些消息。
1. 实时管道将继续运行并录制视频。 RTSP 模拟器不断循环源视频。 若要停止录制，请返回到“终端”窗口并选择 Enter 。 接下来会使用以下方法执行一系列调用，以清理资源：

   * 调用 livePipelineDeactivate 以停用实时管道。
   * 调用 livePipelineDelete 以删除实时管道。
   * 调用 pipelineTopologyDelete 以删除拓扑。
   * 最后调用 GraphTopologyList 以显示列表现在为空。

## <a name="interpret-the-results"></a>解释结果 

运行实时管道时，视频分析器模块会将某些诊断和操作事件发送到 IoT Edge 中心。 这些事件即你在 Visual Studio Code 的“输出”窗口中看到的消息。 这些消息包含 body 部分和 applicationProperties 部分。 要了解这些部分表示的内容，请参阅[创建和读取 IoT 中心消息](../../iot-hub/iot-hub-devguide-messages-construct.md)。

在下面的消息中，应用程序属性和正文内容由视频分析器模块进行定义。

## <a name="diagnostics-events"></a>诊断事件

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished 事件 

激活实时管道后，RTSP 源节点尝试连接到在 RTSP 模拟器容器上运行的 RTSP 服务器。 如果成功，它将输出此事件：

```
[IoTHubMonitor] [9:42:18 AM] Message received from [avasample-iot-edge-device/avaadge]:
{
  "body": {
    "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/videoanalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sources/rtspSource",
    "eventType": "Microsoft.VideoAnalyzers.Diagnostics.MediaSessionEstablished",
    "eventTime": "2021-04-09T09:42:18.1280000Z"
  }
}
```


* 此消息是一个诊断事件 (MediaSessionEstablished)。 它指示 RTSP 源节点（使用者）建立了与 RTSP 模拟器的连接，并开始接收（模拟的）实时馈送。
* applicationProperties 中的 subject 部分引用生成消息的管道拓扑中的节点。 在本例中，该消息来自 RTSP 源节点。
* applicationProperties 中的 eventType 部分指示这是诊断事件。
* eventTime 部分指示事件发生的时间。
* body 部分包含有关诊断事件的数据，在本例中是 [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) 的详细信息。

## <a name="operational-events"></a>操作事件

管道运行一段时间后，最终将从 objectCounter 模块获取事件。 

```
[IoTHubMonitor] [5:53:44 PM] Message received from [avasample-iot-edge-device/objectCounter]:
{
  "body": {
    "count": 2
  },
  "applicationProperties": {
    "eventTime&quot;: &quot;2020-05-17T17:53:44.062Z"
  }
}
```

applicationProperties 部分包含事件时间。 这是 objectCounter 模块观察到来自 yolov3 模块的结果包含相关对象（卡车）的时间。

随着在视频中检测到其他卡车，你会看到显示出更多的此类事件。

### <a name="recordingstarted-event"></a>RecordingStarted 事件

对象计数器发送事件后，你随即会看到一个类型为 Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingStarted 的事件：

```
[IoTHubMonitor] [5:53:46 PM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "outputType": "videoName",
    "outputLocation&quot;: &quot;sample-evr-video"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/videoanalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sinks/videoSink",
    "eventType": "Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingStarted",
    "eventTime": "2021-04-09T09:42:38.1280000Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

applicationProperties 中的 subject 部分引用实时管道中的视频接收器节点，该节点生成此消息。 body 部分包含有关输出位置的信息。 在本例中，即要将视频录制到的视频分析器视频资源的名称。

### <a name="recordingavailable-event"></a>RecordingAvailable 事件

顾名思义，RecordingStarted 事件在录制开始时发送，但媒体数据可能尚未上传到视频资源。 视频接收器节点上传媒体后，将发出类型为 Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingAvailable 的事件：

```
[IoTHubMonitor] [[9:43:38 AM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "outputType": "videoName",
    "outputLocation&quot;: &quot;sample-evr-video"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/videoanalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sinks/videoSink",
    "eventType": "Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingAvailable",
    "eventTime": "2021-04-09T09:43:38.1280000Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

此事件指示已向视频资源写入足够的数据，播放器或客户端可以开始播放视频。 applicationProperties 中的 subject 部分引用实时管道中的视频接收器节点，该节点生成此消息。 body 部分包含有关输出位置的信息。 在本例中，即要将视频录制到的视频分析器资源的名称。

### <a name="recordingstopped-event"></a>RecordingStopped 事件

停用实时管道后，视频接收器节点会停止录制媒体。 它发出类型为 Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingStopped 的此事件：

```
[IoTHubMonitor] [11:33:31 PM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "outputType": "videoName",
    "outputLocation&quot;: &quot;sample-evr-video"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/videoanalyzers/{ava-account-name}",
    "subject": "/livePipelines/Sample-Pipeline-1/sinks/videoSink",
    "eventType": "Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingStopped",
    "eventTime": "2021-04-10T11:33:31.051Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

此事件指示录制已停止。 applicationProperties 中的 subject 部分引用实时管道中的视频接收器节点，该节点生成此消息。 body 部分包含有关输出位置的信息。 在本例中，即要将视频录制到的视频分析器资源的名称。

## <a name="playing-back-the-recording"></a>播放录制内容

可通过登录到 Azure 门户并观看视频来检查实时管道创建的视频分析器视频资源。
1. 打开 Web 浏览器，并转到 [Azure 门户](https://portal.azure.com/)。 输入登录到门户所需的凭据。 默认视图是服务仪表板。
1. 在订阅资源中找到视频分析器帐户，并打开“帐户”窗格。
1. 在“视频分析器”列表中选择“视频” 。
1. 你会发现以名称 `sample-evr-video` 列出的视频。 这是在管道拓扑文件中选择的名称。
1. 选择视频。
1. 视频“详细信息”页随即将打开并自动开始播放。

    <!--TODO: add image -- ![Video playback]() TODO: new screenshot is needed here -->

[!INCLUDE [activate-deactivate-pipeline](./includes/common-includes/activate-deactivate-pipeline.md)]

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [clean-up-resources](./includes/common-includes/clean-up-resources.md)]

## <a name="next-steps"></a>后续步骤

* 使用支持 RTSP 的 [IP 相机](https://en.wikipedia.org/wiki/IP_camera)，而不是使用 RTSP 模拟器。 可以在 [ONVIF 一致性产品页](https://www.onvif.org/conformant-products/)上查找符合配置文件 G、S 或 T 的设备来搜索支持 RTSP 的 IP 照相机。
* 使用 AMD64 或 X64 Linux 设备（与使用 Azure Linux VM 相比）。 此设备必须与 IP 相机位于同一网络中。 按照[在 Linux 上安装 Azure IoT Edge 运行时](../../iot-edge/how-to-install-iot-edge.md)中的说明进行操作。 然后按照[将首个 IoT Edge 模块部署到虚拟 Linux 设备](../../iot-edge/quickstart-linux.md)快速入门中的说明进行操作，将设备注册到 Azure IoT 中心。
