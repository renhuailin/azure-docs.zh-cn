---
title: 使用自己的模型 (HTTP) 分析实时视频
description: 本快速入门介绍如何借助视频分析器使用自己的模型 (HTTP) 分析实时视频。
ms.service: azure-video-analyzer
ms.topic: quickstart
ms.date: 06/01/2021
zone_pivot_groups: video-analyzer-programming-languages
ms.openlocfilehash: a587191a0e5fd80174b3d288d1a9a1d8fd1d2e82
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114605228"
---
# <a name="quickstart-analyze-live-video-with-your-own-model---http"></a>快速入门：使用自己的模型分析实时视频 - HTTP

本快速入门介绍如何使用 Azure 视频分析器分析来自（模拟）IP 相机的实时视频源。 你将了解如何应用计算机视觉模型来检测对象。 实时视频源中的一部分帧被发送到推理服务。 结果将发送到 IoT Edge 中心。

本快速入门将 Azure VM 用作 IoT Edge 设备，并使用模拟的实时视频流。 本文以[检测运动并发出事件](detect-motion-emit-events-quickstart.md)快速入门为基础。

## <a name="prerequisites"></a>先决条件

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/analyze-live-video-use-your-model-http/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/analyze-live-video-use-your-model-http/python/prerequisites.md)]
::: zone-end

## <a name="review-the-sample-video"></a>观看示例视频

设置 Azure 资源时，一个高速公路车流量短视频被复制到 Azure 中用作 IoT Edge 设备的 Linux VM 上。 此快速入门使用视频文件来模拟实时流。

打开一个应用程序，例如 [VLC 媒体播放器](https://www.videolan.org/vlc/)。 选择 Ctrl+N，然后粘贴指向[高速公路交叉口示例视频](https://lvamedia.blob.core.windows.net/public/camera-300s.mkv)的链接以开始播放。 可以看到许多车辆在高速公路上行驶的镜头。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4LTY4]

## <a name="create-and-deploy-the-livepipeline"></a>创建和部署 livePipeline

### <a name="examine-and-edit-the-sample-files"></a>检查和编辑示例文件

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/analyze-live-video-use-your-model-http/csharp/sample-files.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/analyze-live-video-use-your-model-http/python/sample-files.md)]
::: zone-end

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>生成并部署 IoT Edge 部署清单

1. 右键单击 src/edge/deployment.yolov3.template.json 文件，然后选择“生成 IoT Edge 部署清单”。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/analyze-live-video-use-your-model-http/generate-deployment-manifest.png" alt-text="生成 IoT Edge 部署清单的屏幕截图":::

1. 随即将在 src/edge/config 文件夹中创建一个清单文件 deployment.yolov3.amd64.json 。
1. 右键单击 src/edge/config/deployment.yolov3.amd64.json，然后选择“为单个设备创建部署”。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/analyze-live-video-use-your-model-http/deployment-single-device.png" alt-text= "Screenshot of Create Deployment for Single Device":::

1. 如果系统提示你选择 IoT 中心设备，请选择“ava-sample-iot-edge-device”。
1. 大约 30 秒后，在该窗口的左下角刷新 Azure IoT 中心。 边缘设备现在显示以下已部署的模块：

   - 视频分析器模块（名称为“avaedge”）。
   - rtspsim 模块，可模拟 RTSP 服务器，充当实时视频源的源。
   - avaextension 模块，它是 YoloV3 对象检测模型，该模型将计算机视觉应用于图像并返回对象类型的多个类

     > [!div class="mx-imgBorder"]
     > :::image type="content" source="./media/vscode-common-screenshots/avaextension.png" alt-text= "Screenshot of YoloV3 object detection model":::

## <a name="run-the-sample-program"></a>运行示例程序

1. ::: zone pivot="programming-language-csharp"
    [!INCLUDE [header](includes/common-includes/csharp-run-program.md)]
    ::: zone-end

    ::: zone pivot="programming-language-python"
    [!INCLUDE [header](includes/common-includes/python-run-program.md)]
    ::: zone-end
1. operations.json 代码首先调用直接方法 `pipelineTopologyList` 和 `livePipelineList`。 如果你在完成先前的快速入门后清理了资源，则该过程将返回空列表，然后暂停。 若要继续，请选择 Enter 键。

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

1. “终端”窗口将显示下一组直接方法调用：

   - 对 `pipelineTopologySet` 的调用，该调用使用前面的 pipelineTopologyUrl。
   - 对 `livePipelineSet` 的调用，该调用使用以下正文：

     ```
     {
       "@apiVersion": "1.0",
       "name": "Sample-Pipeline-1",
       "properties": {
         "topologyName": "InferencingWithHttpExtension",
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

   - 对 livePipelineActivate 的调用，用于启动实时管道和视频流。
   - 对 `livePipelineList` 的第二次调用，显示实时管道处于运行状态。

1. “终端”窗口中的输出暂停并显示“按 Enter 继续”提示 。 暂时不要选择 Enter。 向上滚动，查看调用的直接方法的 JSON 响应有效负载。
1. 切换到 Visual Studio Code 中的“输出”窗口。 可看到视频分析器模块正发送到 IoT 中心的消息。 本快速入门中的以下部分将讨论这些消息。
1. 管道将继续运行并输出结果。 RTSP 模拟器不断循环源视频。 若要停止管道，请返回“终端”窗口，并选择 Enter。

   接下来会执行一系列调用，以清理资源：

   - 调用 `livePipelineDeactivate` 停用实时管道。
   - 调用 `livePipelineDelete` 删除实时管道。
   - 调用 `pipelineTopologyDelete` 删除拓扑。
   - 对 `pipelineTopologyList` 的最后一次调用显示该列表为空。

## <a name="interpret-results"></a>解释结果

运行实时管道时，来自 HTTP 扩展处理器节点的结果将通过 IoT 中心消息接收器节点发送到 IoT 中心。 “输出”窗口中显示的消息包含正文部分和 applicationProperties 部分。 有关详细信息，请参阅[创建和读取 IoT 中心消息](../../iot-hub/iot-hub-devguide-messages-construct.md)。

在下面的消息中，视频分析器模块定义了应用程序属性和正文内容。

**MediaSessionEstablished 事件**

对管道进行实例化后，RTSP 源节点尝试连接到在 rtspsim-live555 容器上运行的 RTSP 服务器。 如果连接成功，则打印以下事件。 事件类型为 Microsoft.VideoAnalyzer.Diagnostics.MediaSessionEstablished。

```
[IoTHubMonitor] [9:42:18 AM] Message received from [avasampleiot-edge-device/avaedge]:
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

在此消息中，请注意以下详细信息：

- 消息为诊断事件。 MediaSessionEstablished 指示 RTSP 源节点（使用者）已与 RTSP 模拟器连接，并已开始接收（模拟的）实时馈送。
- 在 applicationProperties 中，subject 指示消息是从管道中的 RTSP 源节点生成的。
- 在 applicationProperties 中，eventType 指示此事件是一个诊断事件。
- eventTime 指示事件的发生时间。
- 正文包含有关诊断事件的数据。 在本例中，数据包含[会话描述协议 (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) 详细信息。

### <a name="inference-event"></a>推理事件

HTTP 扩展处理器节点从 yolov3 模块接收推理结果。 然后，它通过 IoT 中心消息接收器节点将结果作为推理事件发出。

在这些事件中，类型设置为 entity 以指示它是实体，例如汽车或卡车。 eventTime 值为检测到对象时的 UTC 时间。

在以下示例中，在同一视频帧检测到两辆汽车，置信度各不相同。

```
[IoTHubMonitor] [1:48:04 PM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "timestamp": 145589011404622,
  "inferences": [
    {
      "type": "entity",
      "entity": {
        "tag": {
          "value": "car",
          "confidence": 0.97052866
        },
        "box": {
          "l": 0.40896654,
          "t": 0.60390747,
          "w": 0.045092657,
          "h": 0.029998193
        }
      }
    },
    {
      "type": "entity",
      "entity": {
        "tag": {
          "value": "car",
          "confidence": 0.9547283
        },
        "box": {
          "l": 0.20050547,
          "t": 0.6094412,
          "w": 0.043425046,
          "h": 0.037724357
        }
      }
    },
    {
      "type": "entity",
      "entity": {
        "tag": {
          "value": "car",
          "confidence": 0.94567955
        },
        "box": {
          "l": 0.55363107,
          "t": 0.5320657,
          "w": 0.037418623,
          "h": 0.027014252
        }
      }
    },
    {
      "type": "entity",
      "entity": {
        "tag": {
          "value": "car",
          "confidence": 0.8916893
        },
        "box": {
          "l": 0.6642384,
          "t": 0.581689,
          "w": 0.034349587,
          "h": 0.027812533
        }
      }
    },
    {
      "type": "entity",
      "entity": {
        "tag": {
          "value": "car",
          "confidence": 0.8547814
        },
        "box": {
          "l": 0.584758,
          "t": 0.60079926,
          "w": 0.07082855,
          "h": 0.034121
        }
      }
    }
  ]
}
```

在消息中，请注意以下详细信息：

- body 部分包含有关分析事件的数据。 在本例中，该事件是推理事件，因此正文包含推理数据。
- inferences 部分指示类型为实体。 本部分包含有关实体的其他数据。
- timestamp 值指示接收事件的时间。

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [prerequisites](./includes/common-includes/clean-up-resources.md)]

## <a name="next-steps"></a>后续步骤

查看高级用户面临的其他挑战：

- 使用支持 RTSP 的 [IP 相机](https://en.wikipedia.org/wiki/IP_camera)，而不是使用 RTSP 模拟器。 可以在 [ONVIF 符合标准的产品](https://www.onvif.org/conformant-products/)页面上搜索支持 RTSP 的 IP 摄像机。 查找符合配置文件 G、S 或 T 的设备。
- 使用 AMD64 或 X64 Linux 设备，而不是 Azure Linux VM。 此设备必须与 IP 相机位于同一网络中。 可以按照[在 Linux 上安装 Azure IoT Edge 运行时](../../iot-edge/how-to-install-iot-edge.md?view=iotedge-2018-06&preserve-view=true)中的说明进行操作。 然后按照[将首个 IoT Edge 模块部署到虚拟 Linux 设备](../../iot-edge/quickstart-linux.md?view=iotedge-2018-06&preserve-view=true)中的说明，将设备注册到 Azure IoT 中心。
