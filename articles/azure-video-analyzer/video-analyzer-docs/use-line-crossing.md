---
title: 使用 Azure 视频分析器检测对象在实时视频中越过虚拟线的时间
description: 本快速入门介绍如何使用 Azure 视频分析器检测对象在来自（模拟）IP 相机的实时视频源中越线的时间。
ms.topic: tutorial
ms.date: 06/01/2021
ms.openlocfilehash: 7257562626b17c8f61479eb1ba4d51fea52d3c91
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123185950"
---
# <a name="tutorial-detect-when-objects-cross-a-virtual-line-in-a-live-video"></a>教程：检测对象在实时视频中越过虚拟线的时间

本教程介绍如何使用 Azure 视频分析器检测对象在来自（模拟）IP 相机的实时视频源中越过虚拟线的时间。 你将了解如何应用计算机视觉模型来检测实时视频源的一部分帧中的对象。 然后，你可以使用对象跟踪器节点来跟踪其他帧中的对象，并将结果发送到越线节点。

通过越线节点，可以检测对象越过虚拟线的时间。 这些事件包含方向（顺时针、逆时针）和每个方向的总计数。  

本教程将 Azure VM 用作 IoT Edge 设备，并使用模拟的实时视频流。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [prerequisites](./includes/common-includes/csharp-prerequisites.md)]

## <a name="set-up-azure-resources"></a>设置 Azure 资源

[![部署到 Azure](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

[!INCLUDE [resources](./includes/common-includes/azure-resources.md)]

## <a name="overview"></a>概述

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/track-objects-live-video/line-crossing-tracker-topology.png" alt-text="检测对象在实时视频中越过虚拟线的时间。":::

此图显示了本教程中的信号如何流动。 [Edge 模块](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555)模拟托管实时流式处理协议 (RTSP) 服务器的 IP 相机。 [RTSP 源](pipeline.md#rtsp-source)节点从该服务器拉取视频源，并将视频帧发送到 [HTTP 扩展处理器](pipeline.md#http-extension-processor)节点。

HTTP 扩展节点扮演代理的角色。 它将每第 10 个视频帧转换为指定的图像类型。 然后，它将图像通过 HTTP 转发到另一个 Edge 模块，该模块在 HTTP 终结点后运行一个 AI 模型。 在此示例中，使用 [YOLOv3](https://github.com/Azure/video-analyzer/tree/main/edge-modules/extensions/yolo/yolov3) 模型构建该 Edge 模块，该模型能够检测多种类型的对象。 HTTP 扩展处理器节点收集检测结果，并将这些结果和所有视频帧（不只是第 10 帧）发送到对象跟踪器节点。 对象跟踪器节点使用光流技术来跟踪 9 帧中未应用 AI 模型的对象。 跟踪器节点将其结果发布到 IoT 中心消息接收器节点。 然后，此 [IoT 中心消息接收器](pipeline.md#iot-hub-message-sink)节点将这些事件发送到 [IoT Edge 中心](../../iot-fundamentals/iot-glossary.md?view=iotedge-2020-11&preserve-view=true#iot-edge-hub)。

越线节点将接收来自上游对象跟踪器节点的结果。 对象跟踪器节点的输出包含检测到的对象的坐标。 越线节点根据线坐标评估这些坐标。 对象越线时，越线节点将发出事件。 事件将发送到 IoT Edge 中心消息接收器。 

在本教程中，将：

1. 设置开发环境。
1. 部署所需的 Edge 模块。
1. 创建和部署实时管道。
1. 解释结果。
1. 了解如何计算坐标。
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
    * avaextension 模块，它是 YOLOv3 对象检测模型，该模型将计算机视觉应用于图像并返回对象类型的多个类

        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/vscode-common-screenshots/avaextension.png" alt-text= "YoloV3 object detection model":::


## <a name="create-and-deploy-the-live-pipeline"></a>创建和部署实时管道

### <a name="review-sample-video"></a>查看示例视频

设置 Azure 资源时，一个高速公路车流量短视频被复制到 Azure 中用作 IoT Edge 设备的 Linux VM 上。 此快速入门使用视频文件来模拟实时流。

打开一个应用程序，例如 [VLC 媒体播放器](https://www.videolan.org/vlc/)。 选择 Ctrl+N，然后粘贴指向[高速公路交叉口示例视频](https://lvamedia.blob.core.windows.net/public/camera-300s.mkv)的链接以开始播放。 可以看到许多车辆在高速公路上行驶的镜头。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4LTY4]

### <a name="examine-and-edit-the-sample-files"></a>检查和编辑示例文件

在 Visual Studio Code 中，浏览到 src/cloud-to-device-console-app 文件夹。 在这里，你将看到创建的 appsettings.json 文件以及其他一些文件：

* c2d-console-app.csproj：Visual Studio Code 的项目文件。
* operations.json：该文件列出了你将运行的不同操作。
* Program.cs：执行以下操作的示例程序代码：
    * 加载应用设置。
    * 调用视频分析器 Edge 模块所公开的直接方法。 可以通过调用模块的[直接方法](direct-methods.md)来使用该模块分析实时视频流。
    * 暂停以检查“终端”窗口中程序的输出以及 OUTPUT 窗口中模块生成的事件 。
    * 调用直接方法以清理资源。

1. 编辑 operations.json 文件：
    
    * 将链接更改为管道拓扑：
    * `"pipelineTopologyUrl" : "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/line-crossing/topology.json"`
    * 在 `livePipelineSet` 下，编辑拓扑的名称，使其与上一个链接中的值匹配：
    * `"topologyName" : "LineCrossingWithHttpExtension"`
    * 在 `pipelineTopologyDelete` 下，编辑名称：
    * `"name" : "LineCrossingWithHttpExtension"`
    
在浏览器中打开管道拓扑的 URL，并检查 HTTP 扩展节点的设置。

```
   "samplingOptions":{
       "skipSamplesWithoutAnnotation":"false",
       "maximumSamplesPerSecond":"2"
   }
```

这里，`skipSamplesWithoutAnnotation` 设置为 `false`，因为扩展节点需要将所有帧（无论它们是否具有推理结果）传递到下游对象跟踪器节点。 对象跟踪器能够跟踪大约超过 15 帧的对象。 AI 模型具有用于处理的最大 FPS，这是 `maximumSamplesPerSecond` 应设置为的最大值。

还应查看越线叉节点参数占位符 `linecrossingName` 和 `lineCoordinates`。 我们为这些参数提供了默认值，但你应使用 operations.js 文件覆盖它们。 查看如何将来自 operations.json 文件的其他参数传递给拓扑（例如 rtsp url）。  

 
## <a name="run-the-sample-program"></a>运行示例程序

1. 若要启动调试会话，请选择 F5 键。 你可在“终端”窗口中看到打印的消息。
1. operations.json 代码首先调用直接方法 `pipelineTopologyList` 和 `livePipelineList`。 如果你在完成先前的快速入门/教程后清理了资源，则该过程将返回空列表，然后暂停。 若要继续，请选择 Enter 键。
    
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
      "topologyName": "LineCrossingWithHttpExtension",
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
    * 对 `livePipelineActivate` 的调用，用于启动实时管道和视频流。
    * 对 `livePipelineList` 的第二次调用，显示实时管道处于运行状态。
1. “终端”窗口中的输出暂停并显示“按 Enter 继续”提示。 暂时不要选择 Enter。 向上滚动，查看调用的直接方法的 JSON 响应有效负载。
1. 切换到 Visual Studio Code 中的“输出”窗口。 可看到视频分析器模块正发送到 IoT 中心的消息。 本教程的以下部分将讨论这些消息。
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
    "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 nnn.nn.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/cafetaria-01.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=Z00AKeKQCgC3YC3AQEBpB4kRUA==,aO48gA==\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/videoAnalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sources/rtspSource",
    "eventType": "Microsoft.VideoAnalyzer.Diagnostics.MediaSessionEstablished",
    "eventTime": "2021-05-06T17:33:09.554Z",
    "dataVersion": "1.0"
  },
  }
}
```

在此消息中，请注意以下详细信息：

* 消息为诊断事件。 MediaSessionEstablished 指示 RTSP 源节点 (subject) 已与 RTSP 模拟器连接，并已开始接收（模拟的）实时源。
* 在 `applicationProperties` 中，“subject”指示消息是从实时管道中的 RTSP 源节点生成的。
* 在 `applicationProperties` 中，“eventType”指示此事件是诊断事件。
* “eventTime”指示事件的发生时间。
* 正文包含有关诊断事件的数据。 在本例中，数据包含[会话描述协议 (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) 详细信息。

## <a name="line-crossing-events"></a>越线事件

HTTP 扩展处理器节点将第 0 帧、第 15 帧、第 30 帧… 发送到 avaextension 模块，并接受推理结果。 然后，它将这些结果以及所有视频帧发送到对象跟踪器节点。 对象跟踪器生成所有帧（0、1、2…）的推理结果，然后越线节点根据拓扑中指定的线坐标对其进行检查。 对象越过这些坐标时，将触发事件。 该事件如下所示：
```
{
  "body": {
    "timestamp": 145865319410261,
    "inferences": [
      {
        "type": "event",
        "subtype": "lineCrossing",
        "inferenceId": "8f4f7b25d6654536908bcfe34374a15e",
        "relatedInferences": [
          "c9ea5decdd6a487089ded249c748cf5b"
        ],
        "event": {
          "name": "LineCrossing1",
          "properties": {
            "counterclockwiseTotal": "35",
            "direction": "counterclockwise",
            "total": "38",
            "clockwiseTotal": "3"
          }
        }
      }
    ]
  },
```
在此消息中，请注意以下详细信息：
* 类型为 `event`，子类型为 `lineCrossing`。
* 该事件包含越过的线条的拓扑中指定的 `name`。
* 任意方向的越线 `total` 次数。
* 越线的 `clockwiseTotal` 次数。
* 越线的 `counterclockwiseTotal` 次数。
* `direction` 包含此事件的方向。

> [!NOTE] 
> 如果使用本教程的一键式部署部署了 Azure 资源，则会创建一个标准 DS1 虚拟机。 但是，要从资源密集型 AI 模型（如 YOLO）获得准确的结果，可能需要增加 VM 的大小。 [调整 VM 大小](../../virtual-machines/windows/resize-vm.md)，以根据要求增加 vcpu 和内存数。 然后，重新激活实时管道以查看推理。

## <a name="customize-for-your-own-environment"></a>请根据你自己的环境进行自定义

本教程将使用提供的示例视频，我们已为其计算了线条的正确线坐标。 检查拓扑文件时，你会看到 `lineCoordinates` 参数包含以下值：`[[0.5,0.1], [0.5,0.9]]`

这些值代表什么意思？ 如果要在 2D 图像上绘制一条线，就需要两个点（A 和 B），连接这两点将得到一条虚线。 每个点都有自己的 x 坐标和 y 坐标，用于确定它相对于完整图像分辨率的位置。 在本例中，A 点为 `[0.5,0.1]`，B 点为 `[0.5,0.9]`。 下方直观地显示了这条线：
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/track-objects-live-video/line-crossing-visual-example.png" alt-text="显示图片上的横穿线条的图像示例。":::

在此图中，可看到 A 和 B 点之间的线。如果任何对象移动经过这条线，都将创建一个具有其属性（例如方向）的事件，如本教程前面所述。 另请注意左下角的 x 轴和 y 轴。 这只是为了说明如何将坐标规范化为越线节点的预期值。 

下面是一个示例计算：假设视频分辨率为 1920 x 1080。 1920 和 1080 分别表示沿 x 轴和 y 轴的像素数。
根据计划使用的视频帧创建图像。 接下来，在图像编辑器程序（例如 MSPaint）中打开该图像。 将光标移动到要指定 A 点的位置。在左下角，你将看到光标位置的 x 和 y 坐标。
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/track-objects-live-video/line-crossing-mspaint-coordinates.png" alt-text="使用 MSPaint 绘制的横穿线条的图像示例。":::

记下这些值，并对 B 点重复相同的步骤，并记下相同的值。 至此，你应该已经得到了 A 点和 B 点的 x 和 y 值。例如：A 点：x=1024，y=96；B 点：x=1024，y=960。这些值看起来不像是越线节点中使用的值，因为我们需要介于 0 到 1 之间的数字。 若要计算该数字，请应用以下公式：

`x coordinate / image resolution along x axis`，在本例中为 `1024/1920 = 0.5`。 接下来对 y 执行相同的操作：`96/1080=0.1`。 这些是 A 点的标准化坐标。对 B 点重复此操作，最终将得到一个介于 0 到 1 之间的值数组 `[[0.5,0.1], [0.5,0.9]]`，如本教程前面所示。

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [prerequisites](./includes/common-includes/clean-up-resources.md)]

## <a name="next-steps"></a>后续步骤

* 尝试通过实时管道运行不同的视频。
