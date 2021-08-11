---
title: 使用 OpenVINO™ Model Server（由 Intel 提供的 AI 扩展）分析实时视频
description: 在本教程中，你将使用由 Intel 提供的 AI 模型服务器和预训练的模型来分析（模拟的）IP 相机中的实时视频源。
ms.service: azure-video-analyzer
ms.topic: tutorial
ms.date: 06/01/2021
titleSuffix: Azure
ms.openlocfilehash: a36de6ac835b3ed9f4188b6f8d2bf3b5178be087
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114604724"
---
# <a name="tutorial-analyze-live-video-using-openvino-model-server--ai-extension-from-intel"></a>教程：使用 OpenVINO™ Model Server（由 Intel 提供的 AI 扩展）分析实时视频 

本教程介绍如何使用 [OpenVINO™ Model Server（由 Intel 提供的 AI 扩展）](https://aka.ms/ava-intel-ovms)来分析（模拟的）IP 相机中的实时视频源。 你将了解此推理服务器如何允许你访问用于检测物体（人、车辆或自行车）的模型以及用于车辆分类的模型。 实时视频源中的一部分帧会被发送到此推理服务器，并且结果会被发送到 IoT Edge 中心。

本教程将 Azure VM 用作 IoT Edge 设备，并使用模拟的实时视频流。 它基于用 C# 编写的示例代码。

[!INCLUDE [use-x86-64](./includes/common-includes/use-x86-64.md)]

## <a name="prerequisites"></a>先决条件

[!INCLUDE [prerequisites](./includes/common-includes/csharp-prerequisites.md)]

## <a name="set-up-azure-resources"></a>设置 Azure 资源

[![部署到 Azure](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

[!INCLUDE [resources](./includes/common-includes/azure-resources.md)]

## <a name="about-openvino-model-server--ai-extension-from-intel"></a>关于 OpenVINO™ Model Server（由 Intel 提供的 AI 扩展）

Intel® 分发版 [OpenVINO™ 工具套件](https://software.intel.com/content/www/us/en/develop/tools/openvino-toolkit.html)（开放式视觉推理和神经网络优化）是一个免费的软件包，可帮助开发人员和数据科学家提高计算机视觉工作负载、简化深度学习推理和部署，以及实现从边缘到云的跨 Intel® 平台的简单异类执行。 它包括配备了模型优化器和推理引擎的 Intel® 深度学习部署工具套件以及具有超过 40 个经过优化的预训练模型的 [Open Model Zoo](https://github.com/openvinotoolkit/open_model_zoo) 存储库。

为了构建复杂、高性能的视频分析解决方案，视频分析器模块应与功能强大的推理引擎（可在边缘使用缩放功能）配合使用。 在本教程中，推理请求会发送到 [OpenVINO™ Model Server（由 Intel 提供的 AI 扩展）](https://aka.ms/ava-intel-ovms)，这是一个 Edge 模块，旨在与视频分析器搭配使用。 此推理服务器模块包含 OpenVINO™ Model Server (OVMS)，这是一种由 OpenVINO™ 工具套件提供支持的推理服务器，它针对计算机视觉工作负载进行了高度优化，并针对 Intel® 体系结构进行了开发。 已将扩展添加到 OVMS，以便在推理服务器与视频分析器之间轻松交换视频帧和推理结果，从而使你能够运行任何 OpenVINO™ 工具包支持的模型（可通过修改[代码](https://github.com/openvinotoolkit/model_server/tree/master/extras/ams_wrapper)来自定义推理服务器模块）。 可进一步选择 Intel® 硬件提供的各种加速机制。 这些包括 CPU（Atom、Core、Xeon）、FPGA、VPU。

在此推理服务器的初始版本中，你可以访问以下[模型](https://github.com/openvinotoolkit/model_server/tree/master/extras/ams_models)：

- 车辆检测（推理 URL： http://{module-name}:4000/vehicleDetection）
- 人/车辆/自行车检测（推理 URL： http://{module-name}:4000/personVehicleBikeDetection）
- 车辆分类（推理 URL： http://{module-name}:4000/vehicleClassification）
- 人脸检测（推理 URL： http://{module-name}:4000/faceDetection）

> [!NOTE]
> 通过下载和使用 Edge 模块：OpenVINO™ Model Server（由 Intel 提供的 AI 扩展），以及包含的软件，即表示你同意[许可协议](https://www.intel.com/content/www/us/en/legal/terms-of-use.html)下的条款和条件。
> Intel 致力于尊重人权，避免参与任何侵犯人权的行为。 阅读 [Intel 的全球人权原则](https://www.intel.com/content/www/us/en/policy/policy-human-rights.html)。 Intel 的产品和软件仅适用于不导致或不构成侵犯国际公认人权的应用程序。

## <a name="overview"></a>概述

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/use-intel-openvino-tutorial/http-extension-with-vino.png" alt-text="概述":::

此图显示本快速入门中信号的流动方式。 [Edge 模块](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555)模拟托管实时流式处理协议 (RTSP) 服务器的 IP 相机。 [RTSP 源](pipeline.md#rtsp-source)节点从该服务器拉取视频源，并将视频帧发送到 [HTTP 扩展处理器](pipeline-extension.md#http-extension-processor)节点。 

HTTP 扩展处理器节点充当代理的角色。 它选择一部分传入的视频帧，并将这些帧转换为图像。 然后，它将图像通过 REST 转发到另一个 Edge 模块，该模块在 HTTP 终结点后运行 AI 模型。 在本例中，该 Edge 模块是 OpenVINO™ Model Server（由 Intel 提供的 AI 扩展）。 HTTP 扩展处理器节点收集检测结果并将事件发布到 [IoT 中心消息接收器节点](pipeline.md#iot-hub-message-sink)。 然后，后一个节点将这些事件发送到 [IoT Edge 中心](../../iot-fundamentals/iot-glossary.md#iot-edge-hub)。

在本教程中，将：

1. 设置开发环境。
1. 部署所需的 Edge 模块。
1. 创建和部署实时管道。
1. 解释结果。
1. 清理资源。

## <a name="set-up-your-development-environment"></a>设置开发环境
[!INCLUDE [setup development environment](./includes/set-up-dev-environment/csharp/csharp-set-up-dev-env.md)]

### <a name="review-the-sample-video"></a>观看示例视频

设置 Azure 资源时，会将[停车场的短视频](https://lvamedia.blob.core.windows.net/public/lots_015.mkv)复制到 Azure 中用作 IoT Edge 设备的 Linux VM 上。 此快速入门使用视频文件来模拟实时流。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LUbN]

## <a name="deploy-the-required-modules"></a>部署所需的模块

作为先决条件的一部分，请将示例代码下载到一个文件夹中。 请遵照以下步骤部署所需模板。

1. 在 Visual Studio Code 中，右键单击 src/edge/deployment.openvino.template.json 文件，然后选择“生成 IoT Edge 部署清单”。   
1. deployment.openvino.amd64.json 清单文件是在 src/edge/config 文件夹中创建的 。
1. 右键单击 src/edge/config/deployment.openvino.amd64.json，然后选择“为单个设备创建部署”。
1. 如果系统提示你选择 IoT 中心设备，请选择“avasample-iot-edge-device”。
1. 大约 30 秒后，在该窗口的左下角刷新 Azure IoT 中心。 边缘设备现在显示以下已部署的模块：
    * 视频分析器 Edge 模块（名称为“avaedge”）。
    * rtspsim 模块，可模拟 RTSP 服务器，充当实时视频源的源。 
    * openvino 模块，即 OpenVINO™ Model Server（由 Intel 提供的 AI 扩展）。
 
## <a name="create-and-activate-the-live-pipeline"></a>创建并激活实时管道

### <a name="edit-the-sample-code"></a>编辑示例代码
1. 在 Visual Studio Code 中，浏览到 src/cloud-to-device-console-app 并编辑 operations.json 文件 ：
    * 将链接更改为实时管道拓扑：

        `"pipelineTopologyUrl" : "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/httpExtensionOpenVINO/topology.json"`

    * 在 `livePipelineSet` 下，编辑实时管道拓扑的名称，使其匹配前面链接中的值：

      `"topologyName" : "InferencingWithOpenVINO"`

    * 在 `pipelineTopologyDelete` 下，编辑名称：

      `"name": "InferencingWithOpenVINO"`

### <a name="run-the-sample-program-to-detect-vehicles"></a>运行示例程序以检测车辆
如果在浏览器中打开本教程的管道拓扑 (`pipelineTopologyUrl`)，你将看到 `inferencingUrl` 的值已设置为 `http://openvino:4000/vehicleDetection`，这意味着推理服务器将尝试检测实时视频中的车辆。

1. 若要启动调试会话，请选择 F5 键。 你可在“终端”窗口中看到打印的消息。
1. operations.json 代码首先调用直接方法 `pipelineTopologyList` 和 `livePipelineList`。 如果在完成先前的快速入门后清理了资源，则该过程将返回空列表。 “终端”窗口将显示下一组直接方法调用：

     * 对 `pipelineTopologySet` 的调用，该调用使用前面的 `pipelineTopologyUrl`
     * 对 `livePipelineSet` 的调用，该调用使用以下正文：

         ```json
            {
              "@apiVersion": "1.0",
              "name": "Sample-Pipeline-1",
              "properties": {
                "topologyName": "InferencingWithOpenVINO",
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

     * 对 `livePipelineActivate` 的调用，用于启动管道和视频流
1. “终端”窗口中的输出会在出现 `Press Enter to continue` 提示时暂停。 暂时不要选择 Enter。 向上滚动，查看调用的直接方法的 JSON 响应有效负载。
1. 切换到 Visual Studio Code 中的“输出”窗口。 可看到视频分析器模块正发送到 IoT 中心的消息。 本快速入门中的以下部分将讨论这些消息。
1. 实时管道将继续运行并输出结果。 RTSP 模拟器不断循环源视频。 若要停止实时管道，请返回“终端”窗口，并选择 Enter。 

    接下来会执行一系列调用，以清理资源：
      * 调用 `livePipelineDeactivate` 停用管道。
      * 调用 `livePipelineDelete` 删除管道。
      * 调用 `pipelineTopologyDelete` 删除拓扑。
      * 对 `pipelineTopologyList` 的最后一次调用显示该列表为空。

## <a name="interpret-results"></a>解释结果

运行实时管道时，来自 HTTP 扩展处理器节点的结果将通过 IoT 中心消息接收器节点发送到 IoT 中心。 在“输出”窗口中看到的消息包含 `body` 部分。 有关详细信息，请参阅[创建和读取 IoT 中心消息](../../iot-hub/iot-hub-devguide-messages-construct.md)。

在下面的消息中，视频分析器模块定义了应用程序属性和正文内容。 

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished 事件

激活实时管道后，RTSP 源节点尝试连接到在 rtspsim-live555 容器上运行的 RTSP 服务器。 如果连接成功，“输出”窗口中会显示包含以下内容的事件。

```
[IoTHubMonitor] [9:42:18 AM] Message received from [ava-sample-device/avaadge]:
{
  "body": {
    "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
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

* 此消息是一个诊断事件 (MediaSessionEstablished)。 它指示 RTSP 源节点（使用者）与 RTSP 模拟器连接，并已开始接收（模拟的）实时馈送。
* `sdp` 部分包含有关诊断事件的数据。 在本例中，数据包含[会话描述协议 (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) 详细信息。

### <a name="inference-event"></a>推理事件

HTTP 扩展处理器节点从 OpenVINO™ Model Server（AI 扩展模块）接收推理结果。 然后，它通过 IoT 中心消息接收器节点将结果作为推理事件发出。 

在这些事件中，类型设置为 `entity`，用于指示它是实体，如汽车或卡车等。 在此类事件的 `body` 的以下示例中，检测到一辆置信度值高于 0.9 的车。

```json
{
  "timestamp": 145819820073974,
  "inferences": [
    {
      "type": "entity",
      "subtype": "vehicleDetection",
      "entity": {
        "tag": {
          "value": "vehicle",
          "confidence": 0.9147264
        },
        "box": {
          "l": 0.6853116,
          "t": 0.5035262,
          "w": 0.04322505,
          "h": 0.03426218
        }
      }
    }
```

在消息中，请注意以下详细信息：

* `body` 部分包含有关分析事件的数据。 在本例中，该事件是推理事件，因此正文包含 `inferences` 数据。
* `inferences` 部分指示 `type` 为 `entity`。 本部分包含有关实体的其他数据。

## <a name="run-the-sample-program-to-detect-persons-vehicles-or-bikes"></a>运行示例程序以检测人员、车辆或自行车
若要使用不同的模型，则需要修改管道拓扑和 `operations.json` 文件。

将管道拓扑（`pipelineTopologyUrl` 中使用的 URL）复制到本地文件（如 `C:\TEMP\topology.json`）。 打开该副本，然后将 `inferencingUrl` 的值编辑为 `http://openvino:4000/personVehicleBikeDetection`。

下一步，在 Visual Studio Code 中，转到 src/cloud-to-device-console-app 文件夹并打开 `operations.json` 文件。 将包含 `pipelineTopologyUrl` 的行编辑为：

```
      "pipelineTopologyFile" : "C:\\TEMP\\topology.json" 
```
现在可以重复上述步骤，使用新拓扑再次运行示例程序。 推理结果将与车辆检测模型的结果相似（在架构中），只是将 `subtype` 设置为 `personVehicleBikeDetection`。

## <a name="run-the-sample-program-to-classify-vehicles"></a>运行示例程序将车辆分类
在 Visual Studio Code 中，打开上一步中 `topology.json` 的本地副本，然后将 `inferencingUrl` 的值编辑为 `http://openvino:4000/vehicleClassification`。 如果已运行前面的示例来检测人、车辆或自行车，则无需再次修改 `operations.json` 文件。

现在可以重复上述步骤，使用新拓扑再次运行示例程序。 示例分类结果如下所示。

```json
{
  "timestamp": 145819896480527,
  "inferences": [
    {
      "type": "classification",
      "subtype": "color",
      "classification": {
        "tag": {
          "value": "gray",
          "confidence": 0.683415
        }
      }
    },
    {
      "type": "classification",
      "subtype": "type",
      "classification": {
        "tag": {
          "value": "truck",
          "confidence": 0.9978394
        }
      }
    }
  ]
}

```

## <a name="run-the-sample-program-to-detect-faces"></a>运行示例程序以检测人脸
在 Visual Studio Code 中，打开上一步中 `topology.json` 的本地副本，然后将 `inferencingUrl` 的值编辑为 `http://openvino:4000/faceDetection`。 如果已运行前面的示例来检测人、车辆或自行车，则无需再次修改 `operations.json` 文件。

现在可以重复上述步骤，使用新拓扑再次运行示例程序。 示例检测结果如下所示（注意：上面使用的停车场视频不包含任何可检测的人脸，你应该使用其他视频以试用此模型）。

```json
{
  "body": {
    "inferences": [
      {
        "type": "entity",
        "subtype": "faceDetection",
        "entity": {
          "tag": {
            "value": "face",
            "confidence": 0.9997053742408752
          },
          "box": {
            "l": 0.2559490501880646,
            "t": 0.03403960168361664,
            "w": 0.17685115337371826,
            "h": 0.45835764706134796
          }
        }
      }
    ]
  }
```
## <a name="clean-up-resources"></a>清理资源

如果计划学习其他快速入门或教程，请保留创建的资源。 否则，请转到 Azure 门户，再转到资源组，选择运行本教程所用的资源组，并删除所有资源。

## <a name="next-steps"></a>后续步骤

查看高级用户面临的其他挑战：

* 使用支持 RTSP 的 [IP 相机](https://en.wikipedia.org/wiki/IP_camera)，而不是使用 RTSP 模拟器。 可以在 [ONVIF 符合标准的产品](https://www.onvif.org/conformant-products/)页面上搜索支持 RTSP 的 IP 摄像机。 查找符合配置文件 G、S 或 T 的设备。
* 使用本地 x64 Linux 设备，而不是 Azure Linux VM。 此设备必须与 IP 相机位于同一网络中。 可以按照[在 Linux 上安装 Azure IoT Edge 运行时](../../iot-edge/how-to-install-iot-edge.md)中的说明进行操作。 然后按照[将首个 IoT Edge 模块部署到虚拟 Linux 设备](../../iot-edge/quickstart-linux.md)中的说明，将设备注册到 Azure IoT 中心。
