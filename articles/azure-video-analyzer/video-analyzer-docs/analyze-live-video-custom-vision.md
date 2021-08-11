---
title: Azure 视频分析器入门
description: 本教程将指导你完成使用 IoT Edge 上的 Azure 视频分析器和 Azure 自定义视觉来分析实时视频的步骤。
ms.topic: tutorial
ms.date: 06/01/2021
zone_pivot_groups: video-analyzer-programming-languages
ms.openlocfilehash: efb89b8ac28ca2d4ddfb72c75d420ace705d92ba
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114603399"
---
# <a name="tutorial-analyze-live-video-with-azure-video-analyzer-on-iot-edge-and-azure-custom-vision"></a>教程：使用 IoT Edge 上的 Azure 视频分析器和 Azure 自定义视觉分析实时视频

本教程将介绍如何使用 Azure [自定义视觉](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/)构建可检测玩具卡车的容器化模型，并使用 Azure IoT Edge 上的 Azure 视频分析器的 [AI 扩展功能](analyze-live-video-without-recording.md#analyzing-video-using-a-custom-vision-model)在边缘上部署该模型，以便从实时视频流中检测玩具卡车。

我们将向你展示如何通过上传和标记一些映像，结合自定义视觉的能力来构建和训练计算机视觉模型。 你不需要了解数据科学、机器学习或 AI。 你还将了解视频分析器的功能以及如何轻松地在边缘上将自定义模型部署为容器并分析模拟的实时视频源。

::: zone pivot="programming-language-csharp"
[!INCLUDE [header](includes/analyze-live-video-custom-vision/csharp/header.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [header](includes/analyze-live-video-custom-vision/python/header.md)]
::: zone-end

本教程介绍如何：

- 设置相关资源。
- 在云中构建用于检测玩具卡车的自定义视觉模型，并将其部署到边缘。
- 创建以 HTTP 为扩展名的管道并将其部署到自定义视觉模型。
- 运行示例代码。
- 查看并解释结果。

如果还没有 [Azure 订阅](../../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

## <a name="suggested-pre-reading"></a>建议的读前准备

在开始之前，我们建议你通读以下文章：

- [IoT Edge 上的视频分析器概述](overview.md)
- [Azure 自定义视觉概述](../../cognitive-services/custom-vision-service/overview.md)
- [IoT Edge 上的视频分析器术语](terminology.md)
- [管道概念](pipeline.md)
- [在不录制视频的情况下使用视频分析器](analyze-live-video-without-recording.md)
- [教程：开发 IoT Edge 模块](../../iot-edge/tutorial-develop-for-linux.md)
- [如何编辑 deployment.*.template.json](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)

## <a name="prerequisites"></a>先决条件

* 在计算机上[安装 Docker](https://docs.docker.com/desktop/#download-and-install)。

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/analyze-live-video-custom-vision/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/analyze-live-video-custom-vision/python/prerequisites.md)]
::: zone-end


## <a name="review-the-sample-video"></a>观看示例视频

本教程使用[玩具汽车推理视频](https://lvamedia.blob.core.windows.net/public/t2.mkv)文件来模拟实时流。 可以通过某个应用程序（例如 [VLC 媒体播放器](https://www.videolan.org/vlc/)）来观看视频。 选择“Ctrl+N”，然后粘贴[玩具汽车推理视频](https://lvamedia.blob.core.windows.net/public/t2.mkv)的链接开始播放。 观看视频时，请注意，在 36 秒标记处，玩具卡车出现在视频中。 自定义模型已经过训练，可以检测到这一特定玩具卡车。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4LPwK]

在本教程中，你将使用 IoT Edge 上的视频分析器来检测此类玩具卡车并将关联的推理事件发布到 IoT Edge 中心。

## <a name="overview"></a>概述

![显示自定义视觉概述的图。](./media/custom-vision/topology-custom-vision.svg)

此图显示了本教程中的信号如何流动。 [Edge 模块](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555)模拟托管实时流式处理协议 (RTSP) 服务器的 IP 相机。 [RTSP 源](pipeline.md#rtsp-source)节点从该服务器拉取视频源，并将视频帧发送到 [HTTP 扩展处理器](pipeline.md#http-extension-processor)节点。

HTTP 扩展节点扮演代理的角色。 它对使用 `samplingOptions` 字段设置的传入视频帧采样，还会将视频帧转换为指定的图像类型。 然后，它将图像转发到使用自定义视觉构建的玩具卡车检测器模型。 HTTP 扩展处理器节点收集检测结果并将事件发布到 [Azure IoT 中心消息接收器](pipeline.md#iot-hub-message-sink)节点，然后该节点将这些事件发送到 [IoT Edge 中心](../../iot-fundamentals/iot-glossary.md#iot-edge-hub)。

## <a name="build-and-deploy-a-custom-vision-toy-detection-model"></a>构建并部署自定义视觉玩具检测模型

顾名思义，你可以使用自定义视觉在云中构建你自己的自定义对象检测器或分类器。 它提供了一个简单易用的直观界面，用于构建可通过容器部署在云或边缘上的自定义视觉模型。

若要构建玩具卡车检测器，请按照[快速入门：使用自定义视觉网站生成对象检测器](../../cognitive-services/custom-vision-service/get-started-build-detector.md)中的步骤操作。

> [!IMPORTANT]
> 此自定义视觉模块仅支持 Intel x86 和 amd64 体系结构。 请在继续操作之前检查边缘设备的体系结构。

其他说明：

- 对于本教程，请不要使用快速入门文章的[先决条件部分](../../cognitive-services/custom-vision-service/get-started-build-detector.md#prerequisites)提供的示例图像。 相反，我们使用了一个特定的图像集来构建玩具检测器自定义视觉模型。 当你需要在[快速入门](../../cognitive-services/custom-vision-service/get-started-build-detector.md)中[选择训练图像](../../cognitive-services/custom-vision-service/get-started-build-detector.md#choose-training-images)时，请使用[这些图像](https://lvamedia.blob.core.windows.net/public/ToyCarTrainingImages.zip)。
- 在快速入门的“标记图像”部分中，请确保使用标记“delivery truck”来标记图片中看到的玩具卡车。
- 创建自定义视觉项目时，请确保选择“常规(压缩)”作为域选项

完成后，可使用“性能”选项卡上的“导出”按钮将模型导出到 Docker 容器 。请确保选择 Linux 作为容器平台类型。 这是供容器在其上运行的平台。 你下载容器的计算机既可以是 Windows，也可以是 Linux。 以下说明基于下载到 Windows 计算机上的容器文件。

![显示选中 Dockerfile 的屏幕。](./media/custom-vision/docker-file.png)

1. 你应当已在本地计算机上下载了一个名为 `<projectname>.DockerFile.Linux.zip` 的 zip 文件。
2. 检查是否已安装 Docker。 如果未安装，请安装适用于 Windows 桌面的 [Docker](https://docs.docker.com/get-docker/)。
3. 将下载的文件解压缩到所选位置。 使用命令行转到解压缩的文件夹目录。 应看到以下两个文件 - app\labels.txt 和 app\model.pb
4. 克隆[视频分析器存储库](https://github.com/Azure/video-analyzer)并使用命令行转到 edge-modules\extensions\customvision\avaextension 文件夹
5. 将步骤 3 中的 labels.txt 和 model.pb 文件复制到 edge-modules\extensions\customvision\avaextension 文件夹中。 在同一文件夹中，

   运行以下命令：

   1. `docker build -t cvtruck .`

      此命令会下载许多包、生成 Docker 映像，并将其标记为 `cvtruck:latest`。

      > [!NOTE]
      > 如果成功，你应该会看到以下消息：`Successfully built <docker image id>` 和 `Successfully tagged cvtruck:latest`。 如果生成命令失败，请重试。 第一次运行命令时，有时不会下载依赖项包。
   2. `docker image ls`

      此命令检查新映像是否在本地注册表中。
   
## <a name="set-up-your-development-environment"></a>设置开发环境

::: zone pivot="programming-language-csharp"
[!INCLUDE [setup development environment](./includes/set-up-dev-environment/csharp/csharp-set-up-dev-env.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [setup development environment](./includes/set-up-dev-environment/python/python-set-up-dev-env.md)]
::: zone-end

## <a name="examine-the-sample-files"></a>检查示例文件

::: zone pivot="programming-language-csharp"
[!INCLUDE [examine-sample-files](includes/analyze-live-video-custom-vision/csharp/examine-sample-files.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [examine-sample-files](includes/analyze-live-video-custom-vision/python/examine-sample-files.md)]
::: zone-end

## <a name="generate-and-deploy-the-deployment-manifest"></a>生成并部署部署清单

1. 在 Visual Studio Code 中，转到 src/cloud-to-device-console-app/operations.json。
2. 在 `pipelineTopologySet` 下，确保满足以下条件：<br/>
   `"pipelineTopologyUrl" : "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/httpExtension/topology.json"`
3. 在 `livePipelineSet` 下，确保：

   1. `"topologyName" : "InferencingWithHttpExtension"`
   2. 在 parameters 数组的顶部，添加以下内容：`{"name": "inferencingUrl","value": "http://cv/score"},`
   3. 将 `rtspUrl` 参数值更改为 `"rtsp://rtspsim:554/media/t2.mkv"`。
4. 在 `pipelineTopologyDelete` 下，确保 `"name": "InferencingWithHttpExtension"`。
5. 右键单击“src/edge/ deployment.customvision.template.json”文件，然后选择“生成 IoT Edge 部署清单”。

   ![显示生成 IoT Edge 部署清单的屏幕截图。](./media/custom-vision/deployment-template-json.png)

   此操作应当会在 src/edge/config 文件夹中创建一个名为“deployment.customvision.amd64.json”的清单文件。
6. 打开“src/edge/ deployment.customvision.template.json”文件并找到 `registryCredentials` JSON 块。 在此块中，你会找到 Azure 容器注册表的地址及其用户名和密码。
7. 在命令行上执行以下步骤，将本地自定义视觉容器推送到你的 Azure 容器注册表实例中：

   1. 通过执行以下命令登录到注册表：

      `docker login <address>`

      如果要求进行身份验证，请键入用户名和密码。

      > [!NOTE]
      > 密码在命令行上不可见。
   2. 使用以下命令标记映像：<br/>
      `docker tag cvtruck <address>/cvtruck`.
   3. 使用以下命令推送映像：<br/>
      `docker push <address>/cvtruck`.

      如果成功，你应该会在命令行上看到 `Pushed` 以及该图像的 SHA。
   4. 还可以通过在 Azure 门户上检查 Azure 容器注册表实例进行确认。 此处会显示存储库的名称以及标记。
8. 选择左下角“AZURE IOT 中心”窗格旁边的“更多操作”图标，设置 IoT 中心连接字符串 。 可以从 appsettings.json 文件中复制字符串。 （还有一种建议的方法，可确保你通过[选择 IoT 中心命令](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub)在 Visual Studio Code 中配置正确的 IoT 中心。）

   ![显示设置 IoT 中心连接字符串的屏幕截图。](./media/custom-vision/connection-string.png)
9. 接下来，右键单击“ src/edge/config/ deployment.customvision.amd64.json”，然后选择“为单个设备创建部署”。

   ![显示“为单个设备创建部署”的屏幕截图。](./media/custom-vision/deployment-amd64-json.png)
10. 然后，系统会要求你选择 IoT 中心设备。 从下拉列表中选择“ava-sample-iot-edge-device”。
11. 大约 30 秒后，在左下部分刷新 Azure IoT 中心。 应该会得到已部署以下模块的边缘设备：

    - IoT Edge 上的视频分析器模块（名称为“`avaedge`”）。
    - 名为 `rtspsim` 的模块，可模拟 RTSP 服务器（充当实时视频源的源）。
    - 一个名为 `cv` 的模块。顾名思义，这是自定义视觉玩具卡车检测模型，它向图像应用自定义视觉，并返回多个标记类型。 （我们的模型仅使用一个标记（即“delivery truck”）进行了训练）。



## <a name="run-the-sample-program"></a>运行示例程序

如果在浏览器中打开本教程的拓扑，将看到 `inferencingUrl` 的值已设置为 `http://cv/score`。 此设置表示推理服务器在实时视频中检测到玩具卡车后，将返回结果。

1. 在 Visual Studio Code 中，打开“扩展”选项卡（或选择 Ctrl+Shift+X），然后搜索“Azure IoT 中心” 。
2. 右键单击并选择“扩展设置”。

   ![显示“扩展设置”的屏幕截图。](./media/custom-vision/extensions-tab.png)
3. 搜索并启用“显示详细消息”。

   ![显示“显示详细消息”的屏幕截图。](./media/custom-vision/show-verbose-message.png)
4.  ::: zone pivot="programming-language-csharp"
    [!INCLUDE [header](includes/common-includes/csharp-run-program.md)]
    ::: zone-end

    ::: zone pivot="programming-language-python"
    [!INCLUDE [header](includes/common-includes/python-run-program.md)]
    ::: zone-end  

5. operations.json 代码首先调用直接方法 `livePipelineList` 和 `livePipelineList`。 如果你在完成先前的快速入门后清理了资源，则该过程将返回空列表，然后暂停。 若要继续，请选择 Enter 键。

   “终端”窗口将显示下一组直接方法调用：

   - 对 `pipelineTopologySet` 的调用，该调用使用前面的 `pipelineTopologyUrl`。
   - 对 `livePipelineSet` 的调用，该调用使用以下正文：

   ```
        {
          "@apiVersion": "2.0",
          "name": "Sample-Pipeline-1",
          "properties": {
            "topologyName": "CustomVisionWithHttpExtension",
            "description": "Sample pipeline description",
            "parameters": [
              { 
                "name": "inferencingUrl",
                "value": "http://cv/score"
              },
              {
                "name": "rtspUrl",
                "value": "rtsp://rtspsim:554/media/t2.mkv"
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

   - 对 `livePipelineActivate` 的调用，用于激活管道和视频流。
   - 对 `livePipelineList` 的第二次调用，用于显示活动管道。

6. “终端”窗口中的输出暂停并显示“按 Enter 继续”提示 。 暂时不要选择 Enter。 向上滚动，查看调用的直接方法的 JSON 响应有效负载。
7. 切换到 Visual Studio Code 中的“输出”窗口。 可看到 IoT Edge 上的视频分析器模块正发送到 IoT 中心的消息。 本教程的以下部分将讨论这些消息。
8. 管道将继续运行并输出结果。 RTSP 模拟器不断循环源视频。 若要停止管道，请返回“终端”窗口，并选择 Enter 。 接下来会执行一系列调用，以清理资源：

   - 调用 `livePipelineDeactivate` 停用管道。
   - 调用 `livePipelineDelete` 删除管道。
   - 调用 `pipelineTopologyDelete` 删除拓扑。
   - 对 `pipelineTopologyList` 的最后一次调用显示该列表为空。

## <a name="interpret-the-results"></a>解释结果

运行管道时，来自 HTTP 扩展处理器节点的结果将通过 IoT 中心消息接收器节点发送到 IoT 中心。 在“输出”窗口中看到的消息包含 body 部分和 `applicationProperties` 部分。 有关详细信息，请参阅[创建和读取 IoT 中心消息](../../iot-hub/iot-hub-devguide-messages-construct.md)。

在下面的消息中，视频分析器模块定义了应用程序属性和正文内容。

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished 事件

对管道进行实例化后，RTSP 源节点尝试连接到在 rtspsim-live555 容器上运行的 RTSP 服务器。 如果连接成功，则打印以下事件。


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

在此消息中，请注意以下详细信息：

- 消息为诊断事件。 `MediaSessionEstablished` 指示 RTSP 源节点（使用者）与 RTSP 模拟器连接，并已开始接收模拟的实时馈送。
- 在 `properties` 中，`subject` 指示消息是从管道的 RTSP 源节点生成的。
- 在 `properties` 中，event time 指示此事件是诊断事件。
- event time 指示事件的发生时间。
- 正文包含有关诊断事件的数据。 在本例中，数据包含[会话描述协议 (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) 详细信息。

### <a name="inference-event"></a>推理事件

HTTP 扩展处理器节点从自定义视觉容器接收推理结果，并通过 IoT 中心消息接收器节点将其作为推理事件发出。


```
{
  "body": {
    "timestamp": 145892470449324,
    "inferences": [
      {
        "type": "entity",
        "entity": {
          "tag": {
            "value": "delivery truck",
            "confidence": 0.20541823
          },
          "box": {
            "l": 0.6826309,
            "t": -0.01415127,
            "w": 0.3135161,
            "h": 0.94683206
          }
        }
      },
      {
        "type": "entity",
        "entity": {
          "tag": {
            "value": "delivery truck",
            "confidence": 0.14967085
          },
          "box": {
            "l": 0.33310884,
            "t": 0.03174839,
            "w": 0.13532706,
            "h": 0.54967254
          }
        }
      },
      {
        "type": "entity",
        "entity": {
          "tag": {
            "value": "delivery truck",
            "confidence": 0.1352181
          },
          "box": {
            "l": 0.48884687,
            "t": 0.44746214,
            "w": 0.025887,
            "h": 0.05414263
          }
        }
      }
    ]
  },
  "properties": {
    "topic": "/subscriptions/...",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/processors/httpExtension",
    "eventType": "Microsoft.VideoAnalyzer.Analytics.Inference",
    "eventTime": "2021-05-14T21:24:09.436Z",
    "dataVersion": "1.0"
  },
  "systemProperties": {
    "iothub-connection-device-id": "avasample-iot-edge-device",
    "iothub-connection-module-id": "avaedge",
    "iothub-connection-auth-method": "{\"scope\":\"module\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id": "637563926153483223",
    "iothub-enqueuedtime": 1621027452077,
    "iothub-message-source": "Telemetry",
    "messageId": "96f7f0b5-728d-4e3e-a7bb-4e3198c58726",
    "contentType": "application/json",
    "contentEncoding": "utf-8"
  }
```

请注意前面消息中的以下信息：

- `properties` 中的 subject 引用生成消息的管道中的节点。 在本例中，该消息源自 HTTP 扩展处理器。
- `properties` 中的 event type 指示这是一个分析推理事件。
- event time 指示事件的发生时间。
- body 包含有关分析事件的数据。 在本例中，该事件是推理事件，因此，body 包含一个名为“predictions”的推理数组。
- inferences 部分包含在帧中找到玩具送货卡车的预测列表（标记为“送货卡车”）。 回顾一下，“delivery truck”是你为玩具卡车的自定义训练模型提供的自定义标记。 模型将推断并标识输入视频中的玩具卡车（使用不同的概率置信度分数）。

## <a name="clean-up-resources"></a>清理资源

如果想学习其他教程或快速入门，请保留创建的资源。 如果不想，请转到 Azure 门户，浏览到资源组，选择在本教程中使用的资源组，然后删除所有资源。

## <a name="next-steps"></a>后续步骤

查看高级用户面临的其他挑战：

- 使用支持 RTSP 的 [IP 相机](https://en.wikipedia.org/wiki/IP_camera)，而不是使用 RTSP 模拟器。 可以在 [ONVIF 符合标准的产品](https://www.onvif.org/conformant-products/)页面上搜索支持 RTSP 的 IP 摄像机。 查找符合配置文件 G、S 或 T 的设备。
- 使用 AMD64 或 X64 Linux 设备，而不是 Azure Linux VM。 此设备必须与 IP 相机位于同一网络中。 可以按照[在 Linux 上安装 Azure IoT Edge 运行时](../../iot-edge/how-to-install-iot-edge.md)中的说明进行操作。

然后按照[将首个 IoT Edge 模块部署到虚拟 Linux 设备](../../iot-edge/quickstart-linux.md)中的说明，将设备注册到 Azure IoT 中心。  

