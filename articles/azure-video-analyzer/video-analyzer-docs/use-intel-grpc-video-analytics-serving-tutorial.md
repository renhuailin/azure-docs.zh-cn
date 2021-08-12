---
title: 将 Intel OpenVINO™ DL Streamer Edge AI 扩展与 Azure 视频分析器搭配使用并通过 gRPC 分析实时视频
description: 本教程介绍如何使用由 Intel 提供的 Intel OpenVINO™ DL Streamer Edge AI 扩展来分析（模拟）IP 相机中的实时视频源。
ms.topic: tutorial
ms.service: azure-video-analyzer
ms.date: 06/01/2021
ms.openlocfilehash: f666ac772d85fb1501c54001511e25cd941ee7c3
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114604776"
---
# <a name="tutorial-analyze-live-video-with-intel-openvino-dl-streamer--edge-ai-extension"></a>教程：使用 Intel OpenVINO™ DL Streamer Edge AI 扩展来分析实时视频 

本教程介绍如何使用由 Intel 提供的 Intel OpenVINO™ DL Streamer Edge AI 扩展来分析（模拟）IP 相机中的实时视频源。 你将了解如何使用此推理服务器访问用于检测对象（人员、车辆或自行车）、对象分类（车辆归属）和对象跟踪（人员、车辆和自行车）的不同模型。 与 gRPC 模块的集成让你能够将视频帧发送到 AI 推理服务器。 结果随后会发送到 IoT Edge 中心。 在与 Azure 视频分析器相同的计算节点上运行此推理服务时，可利用通过共享内存发送视频数据的功能。 这样，你就能以实时视频源的帧速率（例如 30 帧/秒）运行推理。 

本教程将 Azure VM 用作模拟 IoT Edge 设备，并使用模拟的实时视频流。 它基于用 C# 编写的示例代码，并以[检测运动并发出事件](detect-motion-emit-events-quickstart.md)快速入门为基础。

[!INCLUDE [use-x86-64](./includes/common-includes/use-x86-64.md)]

## <a name="prerequisites"></a>先决条件

[!INCLUDE [prerequisites](./includes/common-includes/csharp-prerequisites.md)]

## <a name="set-up-azure-resources"></a>设置 Azure 资源

[![部署到 Azure](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

[!INCLUDE [resources](./includes/common-includes/azure-resources.md)]

## <a name="review-the-sample-video"></a>观看示例视频

设置 Azure 资源时，会将停车场的短视频复制到 Azure 中用作 IoT Edge 设备的 Linux VM 上。 此快速入门使用视频文件来模拟实时流。

打开一个应用程序，例如 [VLC 媒体播放器](https://www.videolan.org/vlc/)。 选择“Ctrl+N”，然后粘贴[视频](https://lvamedia.blob.core.windows.net/public/lots_015.mkv)的链接以开始播放。 可以看到停车场中车辆的视频片段，大多数是停着的，只有一辆在移动。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4LUbN]

在本教程中，你将使用视频分析器和 Intel 提供的 Intel OpenVINO™ DL Streamer Edge AI 扩展来检测车辆等对象，从而对车辆进行分类，或跟踪车辆、人员或自行车。 将生成的推理事件发布到 IoT Edge 中心。

## <a name="overview"></a>概述

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/use-intel-openvino-tutorial/grpc-vas-extension-with-vino.png" alt-text="结合使用 Azure 视频分析器管道与 Intel DL Streamer Edge AI 模块的概述。":::

此图显示了本教程中的信号如何流动。 [Edge 模块](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555)模拟托管实时流式处理协议 (RTSP) 服务器的 IP 相机。 [RTSP 源](pipeline.md#rtsp-source)节点会从该服务器拉取视频源，并将视频帧发送到 [gRPC 扩展处理器](pipeline.md#grpc-extension-processor)节点。 

此 gRPC 扩展处理器节点以解码的视频帧作为输入，并将此类帧中继到 gRPC 服务器公开的 [gRPC](terminology.md#grpc) 终结点。 节点支持使用[共享内存](https://en.wikipedia.org/wiki/Shared_memory)传输数据，或将内容直接嵌入 gRPC 消息的正文中。 此外，此节点具有内置的图像格式化程序，用于在视频帧中继到 gRPC 终结点之前对它们进行缩放和编码。 缩放程序可以对图像纵横比进行保留、填充或拉伸。 图像编码器支持 jpeg、png 或 bmp 格式。 请在[此处](pipeline.md#grpc-extension-processor)详细了解处理器。

## <a name="about-intel-openvino-dl-streamer--edge-ai-extension-module"></a>关于 Intel OpenVINO™ DL Streamer Edge AI 扩展模块


OpenVINO™ DL Streamer Edge AI 扩展模块是一项基于 Intel 视频分析服务（VA 服务）的微服务，它为使用 OpenVINO™ DL Streamer 构建的视频分析管道提供服务。 开发人员可将已解码的视频帧发送到 AI 扩展模块，该模块执行检测、分类或跟踪并返回结果。 AI 扩展模块会公开与视频分析平台（如 Microsoft 的 Azure 视频分析器）兼容的 gRPC API。

为了构建复杂、高性能的视频分析解决方案，Azure 视频分析器模块应与功能强大的推理引擎（可在边缘使用缩放功能）配合使用。 在本教程中，推理请求会发送到 Intel OpenVINO™ DL Streamer Edge AI 扩展，这是一个 Edge 模块，旨在与 Azure 视频分析器搭配使用。

在此推理服务器的初始版本中，你可以访问以下[模型](https://aka.ms/intel-dlstreamer-docs)：

- object_detection for person_vehicle_bike_detection ![用于车辆的对象检测](./media/use-intel-openvino-tutorial/object-detection.png)

- object_classification for vehicle_attributes_recognition ![用于车辆的对象分类](./media/use-intel-openvino-tutorial/object-classification.png)

- object_tracking for person_vehicle_bike_tracking ![用于人员车辆的对象跟踪](./media/use-intel-openvino-tutorial/object-tracking.png)

它使用预先加载的对象检测、对象分类和对象跟踪管道来快速启动。 此外，它还附带预先加载的 [person-vehicle-bike-detection-crossroad-0078](https://github.com/openvinotoolkit/open_model_zoo/blob/master/models/intel/person-vehicle-bike-detection-crossroad-0078/README.md) 和 [vehicle-attributes-recognition-barrier-0039 models](https://github.com/openvinotoolkit/open_model_zoo/blob/master/models/intel/vehicle-attributes-recognition-barrier-0039/README.md)。

> [!NOTE]
> 下载和使用 Edge 模块（Intel 提供的 OpenVINO™ DL Streamer Edge AI 扩展）及包含的软件，即表示你同意[许可协议](https://www.intel.com/content/www/us/en/legal/terms-of-use.html)下的条款和条件。
> Intel 致力于尊重人权，避免参与任何侵犯人权的行为。 阅读 [Intel 的全球人权原则](https://www.intel.com/content/www/us/en/policy/policy-human-rights.html)。 Intel 的产品和软件仅适用于不导致或不构成侵犯国际公认人权的应用程序。

只需更改部署模板中的管道环境变量，即可针对你的特定用例灵活地使用不同的管道。 这样，你就能够快速更改管道模型，而在与 Azure 视频分析器结合使用时，只需几秒即可更改媒体管道和推理模型。

在本教程中，将：

1. 设置开发环境。
1. 部署所需的 Edge 模块。
1. 创建和部署实时管道。
1. 解释结果。
1. 清理资源。

## <a name="set-up-your-development-environment"></a>设置开发环境
[!INCLUDE [setup development environment](./includes/set-up-dev-environment/csharp/csharp-set-up-dev-env.md)]

## <a name="deploy-the-required-modules"></a>部署所需的模块

1. 在 Visual Studio Code 中，右键单击 src/edge/deployment.openvino.grpc.template.json 文件，然后选择“生成 IoT Edge 部署清单”。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/analyze-live-video-use-your-model-http/generate-deployment-manifest.png" alt-text="生成 IoT Edge 部署清单":::
1. deployment.openvino.grpc.amd64.json 清单文件是在 src/edge/config 文件夹中创建的 。
1. 右键单击 src/edge/config/deployment.openvino.grpc.amd64.json，然后选择“为单个设备创建部署”。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/analyze-live-video-use-your-model-http/deployment-single-device.png" alt-text= "Create Deployment for a Single Device":::
1. 如果系统提示你选择 IoT 中心设备，请选择“avasample-iot-edge-device”。
1. 大约 30 秒后，在该窗口的左下角刷新 Azure IoT 中心。 边缘设备现在显示以下已部署的模块：

    * 视频分析器 Edge 模块（名称为“avaedge”）。
    * rtspsim 模块，可模拟 RTSP 服务器，充当实时视频源的源。 
    * avaextension 模块，它是 Intel OpenVINO DL Streamer 模块，具有对象检测、分类和跟踪模型，该模型将计算机视觉应用于图像并返回对象类型的多个类。

        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/vscode-common-screenshots/avaextension.png" alt-text= "OpenVINO object detection model":::

> [!NOTE]
> 我们还包含了一个 deployment.openvino.grpc.xpu.template.json 模板，它为 Intel OpenVINO DL Streamer Edge AI 扩展模块提供 CPU、VPU 和 GPU 支持。 这些模板指向 Intel 的 Docker 中心映像。

### <a name="prepare-to-monitor-events"></a>准备监视事件

右键单击 Azure 视频分析器设备，并选择“开始监视内置事件终结点”。 需要执行此步骤，以在 Visual Studio Code 的“输出”窗口中监视 IoT 中心事件。

![开始监视](./media/quickstarts/start-monitoring-iot-hub-events.png) 

### <a name="run-the-sample-program-to-detect-vehicles-persons-or-bike"></a>运行示例程序以检测人员、车辆或自行车
如果在浏览器中打开本教程的[管道拓扑](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/grpcExtensionOpenVINO/topology.json)，你将看到 `grpcExtensionAddress` 的值已设置为 `tcp://avaExtension:5001`，与 httpExtensionOpenVINO 教程相比，无需将 URL 更改为 gRPC 服务器， 而是指示模块通过操作文件中的 `extensionConfiguration` 运行特定管道。 如果未提供，则默认为“object_detection" for "person_vehicle_bike_detection”。 可尝试使用其他支持的管道。

1. 编辑 operations.json 文件：
    * 将链接更改为实时管道拓扑：

        `"pipelineTopologyUrl" : "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/grpcExtensionOpenVINO/topology.json"`

    * 在 `pipelineTopologySet` 下，编辑实时管道拓扑的名称，使其匹配前面链接中的值：

      `"topologyName" : "InferencingWithOpenVINOgRPC"`

    * 在 `pipelineTopologyDelete` 下，编辑名称：

      `"name": "InferencingWithOpenVINOgRPC"`
    
    * 在 `rtspUrl` 下，将 URL 编辑为：`"value": "rtsp://rtspsim:554/media/lots_015.mkv"`

在 rtsp 参数后添加以下 extensionConfiguration：

```
{
    "name": "extensionConfiguration",
    "value": "{\"pipeline\":{\"name\":\"object_detection\",\"version\":\"person_vehicle_bike_detection\"}}"
}
```


`operations.json` 现在应如下所示：
```
{
  "apiVersion": "1.0",
  "operations": [
      {
          "opName": "pipelineTopologyList",
          "opParams": {}
      },
      {
          "opName": "WaitForInput",
          "opParams": {
              "message": "Press Enter to continue."
          }
      },
      {
          "opName": "livePipelineList",
          "opParams": {}
      },
      {
          "opName": "WaitForInput",
          "opParams": {
              "message": "Press Enter to continue. This is start the activating steps."
          }
      },
      {
          "opName": "pipelineTopologySet",
          "opParams": {
              "pipelineTopologyUrl": "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/grpcExtensionOpenVINO/topology.json"
          }
      },
      {
          "opName": "livePipelineSet",
          "opParams": {
              "name": "Sample-Pipeline-1",
              "properties": {
                  "topologyName": "InferencingWithOpenVINOgRPC",
                  "description": "Sample pipeline description",
                  "parameters": [
                      {
                          "name": "rtspUrl",
                          "value": "rtsp://rtspsim:554/media/lots_015.mkv"
                      },
                      {
                          "name": "rtspUserName",
                          "value": "testuser"
                      },
                      {
                          "name": "rtspPassword",
                          "value": "testpassword"
                      },
                      {
                        "name": "extensionConfiguration",
                        "value": "{\"pipeline\":{\"name\":\"object_detection\",\"version\":\"person_vehicle_bike_detection\"}}"
                      }
                  ]
              }
          }
      },
      {
          "opName": "livePipelineActivate",
          "opParams": {
              "name": "Sample-Pipeline-1"
          }
      },
      {
          "opName": "WaitForInput",
          "opParams": {
              "message": "Press Enter to continue. This is start the deactivating steps."
          }
      },
      {
          "opName": "livePipelineDeactivate",
          "opParams": {
              "name": "Sample-Pipeline-1"
          }
      },
      {
          "opName": "livePipelineDelete",
          "opParams": {
              "name": "Sample-Pipeline-1"
          }
      },
      {
          "opName": "livePipelineList",
          "opParams": {}
      },
      {
          "opName": "pipelineTopologyDelete",
          "opParams": {
              "name": "InferencingWithOpenVINOgRPC"
          }
      },
      {
          "opName": "pipelineTopologyList",
          "opParams": {}
      }
  ]
}
```

使用 `extensionConfiguration`，可将数据字符串传递到 Intel DL Streamer Edge AI 模块。 你可使用正确的数据指示 Edge AI 模块对每个管道使用特定模型。 例如，可将上面使用的“object_detection”模型替换为其他受支持的模型，如分类或跟踪。 为此，请使用以下示例：

分类：
```
{
    "name": "extensionConfiguration",
    "value": "{\"pipeline\":{\"name\":\"object_classification\",\"version\":\"vehicle_attributes_recognition\"}}"
}
```

跟踪：
```
{
    "name": "extensionConfiguration",
    "value": "{\"pipeline\":{\"name\":\"object_tracking\",\"version\":\"person_vehicle_bike_tracking\"}}"
}
```

1. 在 Visual Studio Code 中，打开“扩展”选项卡（或按 Ctrl+Shift+X），然后搜索“Azure IoT 中心”。
1. 右键单击并选择“扩展设置”。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="扩展设置":::
1. 搜索并启用“显示详细消息”。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="显示详细消息":::
1. 若要启动调试会话，请选择 F5 键。 你可在“终端”窗口中看到打印的消息。
1. operations.json 代码首先调用直接方法 `pipelineTopologyList` 和 `livePipelineList`。 如果你在完成先前的快速入门/教程后清理了资源，则该过程将返回空列表，然后暂停。 若要继续，请选择 Enter 键。

    “终端”窗口将显示下一组直接方法调用：

     * 对 `pipelineTopologySet` 的调用，该调用使用前面的 `topologyUrl`
     * 对 `livePipelineSet` 的调用，该调用使用以下正文：

         ```
         {
           "@apiVersion": "1.0",
           "name": "Sample-Pipeline-1",
           "properties": {
             "topologyName": "InferencingWithGrpcExtension",
             "description": "Sample pipeline description",
             "parameters": [
               {
                 "name": "rtspUrl",
                 "value": "rtsp://rtspsim:554/media/lots_015.mkv"
               },
               {
                 "name": "rtspUserName",
                 "value": "testuser"
               },
               {
                 "name": "rtspPassword",
                 "value": "testpassword"
               },
               {
                 "name": "grpcExtensionAddress",
                 "value": "tcp://avaextension:5001"
               },
               {
                 "name": "extensionConfiguration",
                 "value": "{\"pipeline\":{\"name\":\"object_detection\",\"version\":\"person_vehicle_bike_detection\"}}"
               }
             ]
           }
         }
         ```

     * 对 `livePipelineActivate` 的调用，用于启动管道和视频流
1. “终端”窗口中的输出会在出现 `Press Enter to continue` 提示时暂停。 暂时不要选择 Enter。 向上滚动，查看调用的直接方法的 JSON 响应有效负载。
1. 切换到 Visual Studio Code 中的“输出”窗口。 可看到视频分析器模块正发送到 IoT 中心的消息。 本教程的以下部分将讨论这些消息。
1. 实时管道将继续运行并输出结果。 RTSP 模拟器不断循环源视频。 若要停止实时管道，请返回“终端”窗口，并选择 Enter。 

    接下来会执行一系列调用，以清理资源：
      * 调用 `livePipelineDeactivate` 停用管道。
      * 调用 `livePipelineDelete` 删除管道。
      * 调用 `pipelineTopologyDelete` 删除拓扑。
      * 对 `pipelineTopologyList` 的最后一次调用显示该列表为空。

## <a name="interpret-results"></a>解释结果

运行实时管道时，来自 gRPC 扩展处理器节点的结果将通过 IoT 中心消息接收器节点发送到 IoT 中心。 在“输出”窗口中看到的消息包含 `body` 部分。 有关详细信息，请参阅[创建和读取 IoT 中心消息](../../iot-hub/iot-hub-devguide-messages-construct.md)。

在下面的消息中，视频分析器模块定义了正文内容。 

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished 事件

激活实时管道后，RTSP 源节点尝试连接到在 rtspsim-live555 容器上运行的 RTSP 服务器。 如果连接成功，则打印以下事件。

```
[IoTHubMonitor] [10:51:34 AM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1620204694595500 1 IN IP4 xxx.xxx.xxx.xxx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.08.19\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=Z00AKeKQCgC3YC3AQEBpB4kRUA==,aO48gA==\r\na=control:track1\r\n"
}
```

在以上脚本中： 

* 消息为诊断事件 `MediaSessionEstablished`。 它指示 RTSP 源节点（使用者）与 RTSP 模拟器连接，并已开始接收（模拟的）实时馈送。
* `sdp` 部分包含有关诊断事件的数据。 在本例中，数据包含[会话描述协议 (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) 详细信息。

### <a name="inference-event"></a>推理事件

gRPC 扩展处理器节点从 Intel OpenVINO™ DL Streamer Edge AI 扩展接收推理结果。 然后，它通过 IoT 中心消息接收器节点将结果作为推理事件发出。 

在这些事件中，类型设置为 `entity`，用于指示它是实体，如汽车或卡车等。 

在以下示例中，检测到一辆置信度值高于 0.9 的车。

```
[IoTHubMonitor] [3:10:23 PM] Message received from [avasample-iot-edge-device/avaedge]:
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

## <a name="clean-up-resources"></a>清理资源

如果计划学习其他快速入门或教程，请保留创建的资源。 否则，请转到 Azure 门户，再转到资源组，选择运行本教程所用的资源组，并删除所有资源。

## <a name="next-steps"></a>后续步骤

查看高级用户面临的其他挑战：

* 使用支持 RTSP 的 [IP 相机](https://en.wikipedia.org/wiki/IP_camera)，而不是使用 RTSP 模拟器。 可以在 [ONVIF 符合标准的产品](https://www.onvif.org/conformant-products/)页面上搜索支持 RTSP 的 IP 摄像机。 查找符合配置文件 G、S 或 T 的设备。
* 使用 Intel x64 Linux 设备，而不是 Azure Linux VM。 此设备必须与 IP 相机位于同一网络中。 可以按照[在 Linux 上安装 Azure IoT Edge 运行时](../../iot-edge/how-to-install-iot-edge.md)中的说明进行操作。 然后按照[将首个 IoT Edge 模块部署到虚拟 Linux 设备](../../iot-edge/quickstart-linux.md)中的说明，将设备注册到 Azure IoT 中心。
