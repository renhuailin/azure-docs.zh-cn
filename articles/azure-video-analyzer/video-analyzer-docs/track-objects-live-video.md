---
title: 使用 Azure 视频分析器跟踪实时视频中的对象
description: 本快速入门介绍如何使用 Azure 视频分析器 Edge 模块跟踪来自（模拟）IP 相机的实时视频源中的对象。 你将了解如何应用计算机视觉模型来检测实时视频源中部分帧的对象。 然后，你可以使用对象跟踪器节点来跟踪其他帧中的对象。
ms.topic: quickstart
ms.date: 06/01/2021
ms.openlocfilehash: 7ea51dbb59cba95825afb059e6a1845b4bbdcb95
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121745693"
---
# <a name="quickstart-track-objects-in-a-live-video"></a>快速入门：跟踪实时视频中的对象

本快速入门介绍如何使用 Azure 视频分析器 Edge 模块跟踪来自（模拟）IP 相机的实时视频源中的对象。 你将了解如何应用计算机视觉模型来检测实时视频源中部分帧的对象。 然后，你可以使用对象跟踪器节点来跟踪其他帧中的对象。

如果你需要检测每个帧中的对象，但边缘设备不具有所需的计算能力，无法将视觉模型应用于每个帧，那么使用此对象跟踪器就会带来很多便利。 如果假设实时视频源为每秒 30 帧，你只能每隔 15 帧运行一次计算机视觉模型，则对象跟踪器可从某个此类帧中提取结果，然后使用[光流](https://en.wikipedia.org/wiki/Optical_flow)技术为第 2 帧、第 3 帧、…、第 14 帧生成结果，直到再次对下一帧应用该模型。

此快速入门将 Azure VM 用作 IoT Edge 设备，并使用模拟的实时视频流。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [prerequisites](./includes/common-includes/csharp-prerequisites.md)]

## <a name="set-up-azure-resources"></a>设置 Azure 资源

[![部署到 Azure](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

[!INCLUDE [resources](./includes/common-includes/azure-resources.md)]

## <a name="overview"></a>概述

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/track-objects-live-video/object-tracker-topology.svg" alt-text="跟踪实时视频中的对象":::

此图显示本快速入门中信号的流动方式。 [Edge 模块](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555)模拟托管实时流式处理协议 (RTSP) 服务器的 IP 相机。 [RTSP 源](pipeline.md#rtsp-source)节点从该服务器拉取视频源，并将视频帧发送到 [HTTP 扩展处理器](pipeline.md#http-extension-processor)节点。

HTTP 扩展节点扮演代理的角色。 它将每第 15 个视频帧转换为指定的图像类型。 然后，它将图像通过 HTTP 转发到另一个 Edge 模块，该模块在 HTTP 终结点后运行一个 AI 模型。 在此示例中，该 Edge 模块使用 [YOLOv3](https://github.com/Azure/video-analyzer/tree/main/edge-modules/extensions/yolo/yolov3) 模型，该模型能够检测多种类型的对象。 HTTP 扩展处理器节点接收检测结果，并将这些结果和所有视频帧（不只是第 15 帧）发送到[对象跟踪器](pipeline.md#object-tracker-processor)节点。 对象跟踪器节点使用光流技术来跟踪 14 帧中未应用 AI 模型的对象。 跟踪器节点将其结果发布到 IoT 中心消息接收器节点。 然后，此 [IoT 中心消息接收器](pipeline.md#iot-hub-message-sink)节点将这些事件发送到 [IoT Edge 中心](../../iot-fundamentals/iot-glossary.md?view=iotedge-2020-11&preserve-view=true#iot-edge-hub)。

> [!NOTE]
> 你应该查看有关使用[对象跟踪器](pipeline.md#object-tracker-processor)节点时在准确性和处理能力之间进行权衡的讨论内容。

在本快速入门中，请执行以下操作：

1. 设置开发环境。
1. 部署所需的 Edge 模块。
1. 创建和部署实时管道。
1. 解释结果。
1. 清理资源。

## <a name="set-up-your-development-environment"></a>设置开发环境
[!INCLUDE [setup development environment](./includes/set-up-dev-environment/csharp/csharp-set-up-dev-env.md)]

## <a name="deploy-the-required-modules"></a>部署所需的模块

1. 在 Visual Studio Code 中，右键单击 src/edge/deployment.yolov3.template.json 文件，然后选择“生成 IoT Edge 部署清单”。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/analyze-live-video-use-your-model-http/generate-deployment-manifest.png" alt-text="生成 IoT Edge 部署清单":::
1. 随即将在 src/edge/config 文件夹中创建一个清单文件 deployment.yolov3.amd64.json 。
1. 右键单击 src/edge/config/deployment.yolov3.amd64.json，然后选择“为单个设备创建部署”。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/analyze-live-video-use-your-model-http/deployment-single-device.png" alt-text= "Create Deployment for Single Device":::
1. 如果系统提示你选择 IoT 中心设备，请选择“avasample-iot-edge-device”。
1. 大约 30 秒后，在该窗口的左下角刷新 Azure IoT 中心。 边缘设备现在显示以下已部署的模块：

    * 视频分析器 Edge 模块（名称为“avaedge”）。
    * rtspsim 模块，可模拟 RTSP 服务器，充当实时视频源的源。 
    * yolov3 模块，该模块使用 YOLOV3 模型检测各种对象

        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/track-objects-live-video/object-tracker-modules.png" alt-text= "List of deployed IoT Edge modules":::


## <a name="create-and-deploy-the-live-pipeline"></a>创建和部署实时管道

### <a name="examine-and-edit-the-sample-files"></a>检查和编辑示例文件

在 Visual Studio Code 中，浏览到 src/cloud-to-device-console-app 文件夹。 在这里，你将看到创建的 appsettings.json 文件以及其他一些文件：

* c2d-console-app.csproj：Visual Studio Code 的项目文件。
* operations.json：该文件列出了你将运行的不同操作。
* Program.cs：执行以下操作的示例程序代码：
    * 加载应用设置。
    * 调用视频分析器模块所公开的直接方法。 可以通过调用模块的[直接方法](direct-methods.md)来使用该模块分析实时视频流。
    * 暂停以检查“终端”窗口中程序的输出以及 OUTPUT 窗口中模块生成的事件 。
    * 调用直接方法以清理资源。

1. 编辑 operations.json 文件：
    
    * 将链接更改为管道拓扑：
    * "pipelineTopologyUrl" : "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/object-tracking/topology.json"
    * 在 livePipelineSet 下，编辑管道拓扑的名称以匹配前面链接中的值：
    * "topologyName" : "ObjectTrackingWithHttpExtension"
    * 在 pipelineTopologyDelete 下，编辑名称：
    * "name" : "ObjectTrackingWithHttpExtension"
    
在浏览器中打开管道拓扑的 URL，并检查 HTTP 扩展节点的设置。

```
   "samplingOptions":{
       "skipSamplesWithoutAnnotation":"false",
       "maximumSamplesPerSecond":"2"
   }
```

这里，`skipSamplesWithoutAnnotation` 设置为 `false`，因为扩展节点需要将所有帧（无论它们是否具有推理结果）传递到下游对象跟踪器节点。 对象跟踪器能够跟踪大约超过 15 帧的对象。 AI 模型具有用于处理的最大 FPS，这是 `maximumSamplesPerSecond` 应设置为的最大值。
    
## <a name="run-the-sample-program"></a>运行示例程序

1. 若要启动调试会话，请选择 F5 键。 你可在“终端”窗口中看到打印的消息。
1. operations.json 代码首先调用直接方法 `pipelineTopologyList` 和 `livePipelineList`。 如果你在完成先前的快速入门后清理了资源，则该过程将返回空列表，然后暂停。 若要继续，请选择 Enter 键。
    
    ```
    -------------------------------Executing operation pipelineTopologyList-----------------------  
    Request: pipelineTopologyList  --------------------------------------------------
    {
    "@apiVersion": "1.0"
    }
    ---------------  
    Response: pipelineTopologyList - Status: 200  ---------------
    {
    "value": []
    }
    --------------------------------------------------------------------------
    Executing operation WaitForInput
    
    Press Enter to continue
    ```
    
    “终端”窗口将显示下一组直接方法调用：
    
    * 对 `pipelineTopologySet` 的调用，该调用使用 `pipelineTopologyUrl` 的内容
    * 对 `livePipelineSet` 的调用，该调用使用以下正文：
        
    ```json
    {
      "@apiVersion": "1.0",
      "name": "Sample-Pipeline-1",
      "properties": {
        "topologyName": "ObjectTrackingWithHttpExtension",
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
    * 对 `livePipelineActivate` 的调用，用于激活实时管道和视频流。
    * 对 `livePipelineList` 的第二次调用，显示实时管道处于运行状态。
1. “终端”窗口中的输出暂停并显示“按 Enter 继续”提示。 目前不要按 Enter。 向上滚动，查看调用的直接方法的 JSON 响应有效负载。
1. 切换到 Visual Studio Code 中的“输出”窗口。 可看到视频分析器 Edge 模块正发送到 IoT 中心的消息。 本快速入门中的以下部分将讨论这些消息。
1. 实时管道将继续运行并输出结果。 RTSP 模拟器不断循环源视频。 若要停止实时管道，请返回“终端”窗口，并选择 Enter。
1. 接下来会执行一系列调用，以清理资源：

    * 调用 `livePipelineDeactivate` 停用实时管道。
    * 调用 `livePipelineDelete` 删除实时管道。
    * 调用 `pipelineTopologyDelete` 删除管道拓扑。
    * 对 `pipelineTopologyList` 的最后一次调用显示该列表为空。
    
## <a name="interpret-results"></a>解释结果

运行实时管道时，来自 HTTP 扩展处理器节点的结果将通过 IoT 中心消息接收器节点发送到 IoT 中心。 在“输出”窗口中看到的消息包含 `body` 和 `applicationProperties` 部分。 有关详细信息，请参阅[创建和读取 IoT 中心消息](../../iot-hub/iot-hub-devguide-messages-construct.md)。

在下面的消息中，视频分析器模块定义了应用程序属性和正文内容。

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished 事件

激活实时管道后，RTSP 源节点尝试连接到在 rtspsim-live555 容器上运行的 RTSP 服务器。 如果连接成功，则打印以下事件。 事件类型为 MediaSessionEstablished。

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

* 消息为诊断事件。 MediaSessionEstablished 指示 RTSP 源节点 (subject) 已与 RTSP 模拟器连接，并已开始接收（模拟的）实时源。
* 在 applicationProperties 中，subject 指示消息是从实时管道中的 RTSP 源节点生成的。
* 在 applicationProperties 中，eventType 指示此事件是一个诊断事件。
* eventTime 指示事件的发生时间。
* 正文包含有关诊断事件的数据。 在本例中，数据包含[会话描述协议 (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) 详细信息。

## <a name="object-tracking-events"></a>对象跟踪事件

HTTP 扩展处理器节点将第 0 帧、第 15 帧、第 30 帧… 发送到 yolov3 模块，并接收推理结果。 然后，它将这些结果以及所有视频帧发送到对象跟踪器节点。 假设在第 0 帧上检测到对象，对象跟踪器将为该对象分配唯一的 `sequenceId`。 然后，在第 1 帧、第 2 帧、…、第 14 帧，如果它可以跟踪该对象，它将输出具有相同 `sequenceId` 的结果。 在结果的以下代码片段中，请注意 `sequenceId` 是重复的，但边界框的位置随着对象移动发生了变化。

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

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [prerequisites](./includes/common-includes/clean-up-resources.md)]

## <a name="next-steps"></a>后续步骤

* 尝试[检测对象在实时视频中越过虚拟线的时间](use-line-crossing.md)。



