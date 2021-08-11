---
title: 检测运动并在边缘设备上录制视频 - Azure
description: 使用 Azure 视频分析器分析来自（模拟）IP 相机的实时视频源。 本快速入门介绍如何检测是否存在任何运动，如果存在，则将 MP4 视频剪辑录制到边缘设备上的本地文件系统中。 本快速入门将 Azure VM 用作 IoT Edge 设备，并且还使用模拟的实时视频流。
ms.topic: quickstart
ms.date: 06/01/2021
zone_pivot_groups: video-analyzer-programming-languages
ms.openlocfilehash: e79a2aa9027309838fd0a0fcc79096e27651e7be
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114601627"
---
# <a name="quickstart-detect-motion-and-record-video-on-edge-devices"></a>快速入门：检测运动并在边缘设备上录制视频

本快速入门介绍如何使用 Azure 视频分析器分析来自（模拟）IP 相机的实时视频源。 本快速入门介绍如何检测是否存在任何运动，如果存在，则将 MP4 视频剪辑录制到边缘设备上的本地文件系统中。 本快速入门将 Azure VM 用作 IoT Edge 设备，并且还使用模拟的实时视频流。

## <a name="prerequisites"></a>先决条件

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/detect-motion-record-video-edge-devices/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/detect-motion-record-video-edge-devices/python/prerequisites.md)]
::: zone-end

## <a name="review-the-sample-video"></a>观看示例视频

在为此快速入门设置 Azure 资源时，一个停车场短视频被复制到 Azure 中用作 IoT Edge 设备的 Linux VM 上。 本教程将使用此视频文件模拟实时流。

打开 [VLC 媒体播放器](https://www.videolan.org/vlc/)等应用程序，选择 Ctrl+N，然后将[此链接](https://lvamedia.blob.core.windows.net/public/lots_015.mkv)粘贴到停车场视频中并开始播放。 大约在 5 秒的时候，一辆白色汽车在停车场间移动。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4LUbN]

完成以下步骤，以使用视频分析器检测汽车的运动，并从大约第 5 秒的标记处开始录制视频剪辑。

## <a name="examine-and-edit-the-sample-files"></a>检查和编辑示例文件

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/detect-motion-record-video-edge-devices/csharp/sample-files.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/detect-motion-record-video-edge-devices/python/sample-files.md)]
::: zone-end

## <a name="generate-and-deploy-the-deployment-manifest"></a>生成并部署部署清单

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/detect-motion-record-video-edge-devices/csharp/generate-deploy-deployment-manifest.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/detect-motion-record-video-edge-devices/python/generate-deploy-deployment-manifest.md)]
::: zone-end

## <a name="run-the-sample-program"></a>运行示例程序

1. 在 Visual Studio Code 中，打开“扩展”选项卡（或按 Ctrl+Shift+X），然后搜索“Azure IoT 中心” 。
1. 右键单击并选择“扩展设置”。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/vscode-common-screenshots/extension-settings.png" alt-text= "Extension settings":::

1. 搜索并启用“显示详细消息”。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/vscode-common-screenshots/verbose-message.png" alt-text= "Show Verbose Message":::

1.  ::: zone pivot="programming-language-csharp"
    [!INCLUDE [header](includes/common-includes/csharp-run-program.md)]
    ::: zone-end

    ::: zone pivot="programming-language-python"
    [!INCLUDE [header](includes/common-includes/python-run-program.md)]
    ::: zone-end
1. operations.json 代码调用直接方法 `pipelineTopologyList` 和 `livePipelineList`。 如果在学完先前的快速入门后清理了资源，则该过程将返回空列表，然后暂停。 按 Enter 键。

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

   “终端”窗口将显示下一组直接方法调用：

   - 对 `pipelineTopologySet` 的调用，该调用使用 pipelineTopologyUrl
   - 对 `livePipelineSet` 的调用，该调用使用以下正文：

     ```
     {
       "@apiVersion": "1.0",
       "name": "Sample-Pipeline-1",
       "properties": {
         "topologyName": "EVRToFilesOnMotionDetection",
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

   - 对 `livePipelineActivate` 的调用，用于启动实时管道和视频流。
   - 对 `livePipelineList` 的第二次调用，显示实时管道处于运行状态。

1. “终端”窗口中的输出暂停并显示“按 Enter 继续”。 暂时不要选择 Enter。 向上滚动，查看调用的直接方法的 JSON 响应有效负载。
1. 切换到 Visual Studio Code 中的“输出”窗口。 可看到视频分析器 Edge 模块正发送到 IoT 中心的消息。 本快速入门中的以下部分将讨论这些消息。
1. 管道拓扑将继续运行并输出结果。 RTSP 模拟器不断循环源视频。 若要停止管道拓扑，请返回“终端”窗口，并选择 Enter。

接下来的一系列调用会清理资源：

- 调用 `livePipelineDeactivate` 停用实时管道。
- 调用 `livePipelineDelete` 删除实时管道。
- 调用 `pipelineTopologyDelete` 删除拓扑。
- 最后一次调用 `pipelineTopologyList` 显示该列表现在为空。

## <a name="interpret-results"></a>解释结果

运行管道拓扑时，来自运动检测器处理器节点的结果将通过 IoT 中心接收器节点发送到 IoT 中心。 Visual Studio Code 的“输出”窗口中显示的消息包含 body 部分和 applicationProperties 部分。 有关详细信息，请参阅[创建和读取 IoT 中心消息](../../iot-hub/iot-hub-devguide-messages-construct.md)。

在下面的消息中，视频分析器 Edge 模块定义了应用程序属性和正文内容。

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished 事件

对管道拓扑进行实例化后，RTSP 源节点尝试连接到在 rtspsim-live555 容器上运行的 RTSP 服务器。 如果连接成功，则打印以下事件。

```
[IoTHubMonitor] [9:42:18 AM] Message received from [ava-sample-device/avaadge]:
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

在以上脚本中：

- 此消息是一个诊断事件 (MediaSessionEstablished)。 它指示 RTSP 源节点（使用者）建立了与 RTSP 模拟器的连接，并开始接收（模拟的）实时馈送。
- 在 applicationProperties 中，subject 引用生成消息的管道拓扑中的节点。 在本例中，该消息来自 RTSP 源节点。
- 在 applicationProperties 中，eventType 指示此事件是一个诊断事件。
- eventTime 值为事件发生的时间。
- body 部分包含有关诊断事件的数据。 在本例中，数据包含会话描述协议 (SDP) 详细信息。

### <a name="recordingstarted-event"></a>RecordingStarted 事件

检测到运动时，将激活信号入口处理器节点，管道拓扑中的文件接收器节点将开始写入 MP4 文件。 文件接收器节点发送操作事件。 将 type 设置为 motion，指示它是运动检测处理器的结果。 eventTime 值是运动发生的 UTC 时间。 有关该过程的详细信息，请参阅本快速入门中的[概述](detect-motion-record-video-edge-devices.md#overview)部分。

以下是此消息的示例：

```
[IoTHubMonitor] [05:37:27 AM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "outputType": "filePath",
    "outputLocation&quot;: &quot;/var/media/sampleFilesFromEVR-filesinkOutput-20210511T053726Z.mp4"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/videoAnalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sinks/fileSink",
    "eventType": "Microsoft.VideoAnalyzer.Operational.RecordingStarted",
    "eventTime": "2021-05-11T05:37:27.713Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

在上面的消息中：

- 在 applicationProperties 中，subject 引用生成消息的管道中的节点。 在本例中，该消息来自文件接收器节点。
- 在 applicationProperties 中，eventType 指示此事件是一个操作事件。
- eventTime 值为事件发生的时间。 此时间为 MediaSessionEstablished 之后且视频开始播放后的 5 到 6 秒。 该时间对应于[汽车开始驶入](#review-the-sample-video)停车场时的第 5-6 秒标记处。
- body 部分包含有关操作事件的数据。 在本例中，数据包含 outputType 和 outputLocation。
- outputType 变量表示此信息与文件路径有关。
- outputLocation 值是 MP4 文件在 Edge 模块中的位置。

### <a name="recordingstopped-and-recordingavailable-events"></a>RecordingStopped 和 RecordingAvailable 事件

如果在[管道拓扑](pipeline.md)中检查信号入口处理器节点的属性，你会看到激活时间设置为 5 秒。 因此，大约在收到 RecordingStarted 事件后的第 5 秒钟，你会收到以下事件：

- RecordingStopped 事件，指示已停止录制。
- RecordingAvailable 事件，指示现在可查看 MP4 文件。

这两个事件的发出时间通常彼此相隔数秒。

## <a name="play-the-mp4-clip"></a>播放 MP4 文件剪辑

通过使用 VIDEO_OUTPUT_FOLDER_ON_DEVICE 密钥，将 MP4 文件写入在 .env 文件中配置的边缘设备上的目录中。 如果使用了默认值，则结果应位于 /var/media/ 文件夹中。

播放 MP4 文件剪辑：

1. 转到资源组，找到 VM，然后连接到 VM。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/detect-motion-record-video-edge-devices/sample-iot-edge-device.png" alt-text= "Edge device":::

1. 使用设置 Azure 资源时生成的凭据登录。
1. 在命令提示符下，转到相关目录。 默认位置为 /var/media。 应会在目录中看到 MP4 文件。

1. 使用[安全复制 (SCP)](../../virtual-machines/linux/copy-files-to-linux-vm-using-scp.md) 将文件复制到本地计算机。
1. 使用 [VLC 媒体播放器](https://www.videolan.org/vlc/)或任何其他 MP4 文件播放器播放这些文件。

## <a name="clean-up-resources"></a>清理资源

如果想学习其他快速入门，请保留所创建的资源。 否则，请在 Azure 门户中，转到资源组，选择运行本快速入门所用的资源组，然后删除所有资源。

## <a name="next-steps"></a>后续步骤

- 按照[对自己的模型运行 Azure 视频分析器](analyze-live-video-use-your-model-http.md)快速入门操作，将 AI 应用于实时视频源。
- 查看高级用户面临的其他挑战：

  - 使用支持 RTSP 的 [IP 相机](https://en.wikipedia.org/wiki/IP_camera)，而不是使用 RTSP 模拟器。 可以在 [ONVIF 一致性](https://www.onvif.org/conformant-products/)产品页上找到支持 RTSP 的 IP 相机。 查找符合配置文件 G、S 或 T 的设备。
  - 使用 AMD64 或 x64 Linux 设备，而不使用 Azure 中的 Linux VM。 此设备必须与 IP 相机位于同一网络中。 按照[在 Linux 上安装 Azure IoT Edge 运行时](../../iot-edge/how-to-install-iot-edge.md?view=iotedge-2020-11&preserve-view=true)中的说明进行操作。 然后按照[将首个 IoT Edge 模块部署到虚拟 Linux 设备](../../iot-edge/quickstart-linux.md?view=iotedge-2020-11&preserve-view=true)中的说明进行操作，将设备注册到 Azure IoT 中心。
