---
title: 通过用于空间分析的计算机视觉分析实时视频 - Azure
description: 本教程介绍如何结合使用 Azure 视频分析器和 Azure 认知服务中的计算机视觉空间分析 AI 功能，分析来自（模拟）IP 相机的实时视频源。
author: Juliako
ms.author: juliako
ms.service: azure-video-analyzer
ms.topic: tutorial
ms.date: 06/01/2021
ms.openlocfilehash: 0f0ee0a7288a3ef07f0aa8fa3c04660cac1ad0b5
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114604167"
---
# <a name="tutorial-live-video-with-computer-vision-for-spatial-analysis-preview"></a>教程：使用计算机视觉空间分析（预览版）功能分析实时视频

本教程介绍如何结合使用 Azure 视频分析器和 [Azure 认知服务中的计算机视觉空间分析 AI 服务](../../cognitive-services/computer-vision/intro-to-spatial-analysis-public-preview.md)，分析来自（模拟）IP 相机的实时视频源。 将介绍如何使用此推理服务器来分析流式处理视频，以便了解人员之间的空间关系和物理空间中的移动。 视频源中的一部分帧将发送到此推理服务器，结果将发送到 IoT Edge 中心。在满足某些条件时，将录制视频短片并以视频形式将其存储在视频分析器帐户中。

在本教程中，将：

> [!div class="checklist"]
>
> - 设置资源。
> - 检查代码。
> - 运行示例代码。
> - 监视事件。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>建议的读前准备

在开始之前，请阅读以下文章：

- [视频分析器概述](overview.md)
- [视频分析器术语](terminology.md)
- [管道概念](pipeline.md)
- [基于事件的视频录制](record-event-based-live-video.md)
- [教程：开发 IoT Edge 模块](../../iot-edge/tutorial-develop-for-linux.md)

## <a name="prerequisites"></a>先决条件

下面是将空间分析模块连接到 Azure 视频分析器模块的先决条件。

- 开发计算机上的 [Visual Studio Code](https://code.visualstudio.com/)。 请确保具有 [Azure IoT Tools 扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)。
  - 确保开发计算机连接到的网络允许基于端口 5671 的高级消息队列协议。 此设置使 Azure IoT Tools 可以与 Azure IoT 中心通信。
- 用于空间分析的 [Azure 认知服务计算机视觉容器](../../cognitive-services/computer-vision/intro-to-spatial-analysis-public-preview.md)。
  若要使用此容器，你必须有一个计算机视觉资源，以获取关联的 **API 密钥** 和 **终结点 URI**。 可以从 Azure 门户的计算机视觉“概览”页和“密钥”页获取 API 密钥。 密钥和终结点是启动容器所必需的。

## <a name="set-up-azure-resources"></a>设置 Azure 资源

1. 若要运行空间分析容器，需要一台搭载 [NVIDIA Tesla T4 GPU](https://www.nvidia.com/en-us/data-center/tesla-t4/) 的计算设备。 建议使用具有 GPU 加速功能的 [Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/)，但该容器可在主机上安装了 [Ubuntu Desktop 18.04 LTS](http://releases.ubuntu.com/18.04/) 的任何其他台式机上运行。

   #### <a name="azure-stack-edge-device"></a>[Azure Stack Edge 设备](#tab/azure-stack-edge)

   Azure Stack Edge 是一个硬件即服务解决方案，也是一台具有网络数据传输功能的支持 AI 的边缘计算设备。 有关详细准备工作和设置说明，请参阅 [Azure Stack Edge 文档](../../databox-online/azure-stack-edge-deploy-prep.md)。

   #### <a name="desktop-machine"></a>[台式机](#tab/desktop-machine)

   #### <a name="minimum-hardware-requirements"></a>最低硬件需求

   - 4 GB 系统 RAM
   - 4 GB GPU RAM
   - 8 核 CPU
   - 1 个 NVIDIA Tesla T4 GPU
   - 20 GB 机械硬盘 (HDD) 空间

   #### <a name="recommended-hardware"></a>推荐硬件

   - 32 GB 系统 RAM
   - 16 GB GPU RAM
   - 8 核 CPU
   - 2 个 NVIDIA Tesla T4 GPU
   - 50 GB 固态硬盘 (SSD) 空间

   在本文中，你将下载并安装以下软件包。 主计算机必须能够运行以下各项（请参阅后面的说明）：

   - [NVIDIA 图形驱动程序](https://docs.nvidia.com/datacenter/tesla/tesla-installation-notes/index.html)和 [NVIDIA CUDA 工具包](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html)
   - [NVIDIA MPS](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf)（多进程服务）的配置。
   - [Docker CE](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-engine---community-1) 和 [NVIDIA-Docker2](https://github.com/NVIDIA/nvidia-docker)
   - [Azure IoT Edge](../../iot-edge/how-to-install-iot-edge.md) 运行时。

   #### <a name="azure-vm-with-gpu"></a>[带 GPU 的 Azure VM](#tab/virtual-machine)

   你可以使用带有一个 K80 GPU 的 [NC 系列 VM](../../virtual-machines/nc-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

   1. 连接到 VM，在终端中键入以下命令：

        `bash -c "$(curl -sL https://aka.ms/ava-edge/prep_device)"`
    
        Azure 视频分析器模块使用非特权本地用户帐户在边缘设备上运行。 此外，它还需要某些本地文件夹来存储应用程序配置数据。 最后，对于本操作指南，我们将利用 [RTSP 模拟器](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555)，该模拟器将视频源实时中继到 AVA 模块进行分析。 此模拟器使用输入目录中的预录制视频文件作为输入。 
    
        上面使用的准备设备脚本会自动执行这些任务，因此，你可以运行一个命令，并通过无缝创建的特权拥有所有相关的输入和配置文件夹、视频输入文件以及用户帐户。 成功完成该命令后，应会显示边缘设备上创建的以下文件夹。 
    
        * `/home/localedgeuser/samples`
        * `/home/localedgeuser/samples/input`
        * `/var/lib/videoanalyzer`
        * `/var/media`
    
        请注意，/home/localedgeuser/samples/input 文件夹中的视频文件 (*.mkv) 会用作要分析的输入文件。 

1. [设置边缘设备](../../cognitive-services/computer-vision/spatial-analysis-container.md#set-up-the-host-computer)

1. 接下来，部署其他 Azure 资源。

   [![部署到 Azure](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

   > [!NOTE]
   > 上面的按钮将创建并使用没有 NVIDIA GPU 的默认虚拟机。 当系统在 Azure 资源管理器 (ARM) 模板中询问你时，请选择"使用现有边缘设备"选项，并使用上述步骤中的 IoT 中心和设备信息。
   > :::image type="content" source="./media/spatial-analysis/use-existing-device.png" alt-text="使用现有设备":::

   [!INCLUDE [resources](./includes/common-includes/azure-resources.md)]

## <a name="overview"></a>概述

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis/overview.png" alt-text="空间分析概述":::

此图显示了本教程中的信号如何流动。 [Edge 模块](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555)模拟托管实时流式处理协议 (RTSP) 服务器的 IP 相机。 [RTSP 源](pipeline.md#rtsp-source)节点从该服务器拉取视频源，并将视频帧发送到`CognitiveServicesVisionProcessor`节点。

`CognitiveServicesVisionProcessor` 节点扮演代理的角色。 它将视频帧转换为指定的图像类型。 然后，它将图像通过 **共享内存** 中继到另一个 Edge 模块，该模块在 gRPC 终结点后运行 AI 操作。 在此示例中，该 Edge 模块是空间分析模块。 `CognitiveServicesVisionProcessor` 节点将执行两项操作：

- 它收集结果，并将事件发布到 [IoT 中心接收器](pipeline.md#iot-hub-message-sink)节点。 然后该节点将这些事件发送到 [IoT Edge 中心](../../iot-fundamentals/iot-glossary.md#iot-edge-hub)。
- 它还使用[信号入口处理器](pipeline.md#signal-gate-processor)从 RTSP 源捕获 30 秒视频短片，并将其存储为视频文件。

## <a name="create-the-computer-vision-resource"></a>创建计算机视觉资源

你需要通过 [Azure 门户](../../iot-edge/how-to-deploy-modules-portal.md)或 Azure CLI 创建计算机视觉类型的 Azure 资源。 

### <a name="gathering-required-parameters"></a>收集必需的参数

所有认知服务容器（包括 spatialanalysis 容器）都需要三个主要参数。 最终用户许可协议 (EULA) 的值必须为 accept。 此外，终结点 URI 和 API 密钥都是必需的。

### <a name="keys-and-endpoint-uri"></a>密钥和终结点 URI

此密钥用于启动空间分析容器，在相应认知服务资源的 Azure 门户的 `Keys and Endpoint` 页上提供。 导航到该页并找到密钥和终结点 URI。  

部署清单文件中需要此密钥和终结点 URI 才能部署 spatialanalysis 容器。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis/keys-endpoint.png" alt-text="终结点 URI":::

## <a name="set-up-your-development-environment"></a>设置开发环境

1. 从此位置克隆存储库：[https://github.com/Azure-Samples/azure-video-analyzer-iot-edge-csharp](https://github.com/Azure-Samples/azure-video-analyzer-iot-edge-csharp)。
1. 在 Visual Studio Code 中，打开下载的存储库所在的文件夹。
1. 在 Visual Studio Code 中，转到 src/cloud-to-device-console-app 文件夹。 在该文件中，创建一个文件并将其命名为 appsettings.json。 该文件将包含运行程序所需的设置。
1. 从 Azure 门户复制 appsettings.json 文件的内容。 文本应类似于以下代码。
   ```json
   {
     "IoThubConnectionString": "HostName=<IoTHubName>.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=<SharedAccessKey>",
     "deviceId": "<your Edge Device name>",
     "moduleId": "avaedge"
   }
   ```

1. 转到 src/edge 文件夹并创建一个名为 .env 的文件 。
1. 从 Azure 门户复制 env.txt 文件的内容。 文本应类似于以下代码。

   ```env
   SUBSCRIPTION_ID="<Subscription ID>"
   RESOURCE_GROUP="<Resource Group>"
   AVA_PROVISIONING_TOKEN="<The provisioning token>"
   VIDEO_INPUT_FOLDER_ON_DEVICE="/home/localedgeuser/samples/input"
   VIDEO_OUTPUT_FOLDER_ON_DEVICE="/var/media"
   APPDATA_FOLDER_ON_DEVICE="/var/lib/videoanalyzer"
   CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
   CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry password>"
   ```

## <a name="set-up-deployment-template"></a>设置部署模板

在 /src/edge/deployment.spatialAnalysis.template.json 中查找部署文件。 模板中有 avaedge 模块、rtspsim 模块和 spatialanalysis 模块。

在部署模板文件中，需要注意以下事项：

1. 在 `spatialanalysis` 模块中设置端口绑定。

   ```
   "PortBindings": {
       "50051/tcp": [
           {
               "HostPort": "50051"
           }
       ]
   }
   ```

1. `avaedge` 和 `spatialanalysis` 模块 createOptions 中的 `IpcMode` 应当相同并设置为 host。
1. 若要使 RTSP 模拟器正常工作，请确保在使用 Azure Stack Edge 设备时已设置“卷边界”。

   1. [连接到 SMB 共享](../../databox-online/azure-stack-edge-deploy-add-shares.md#connect-to-an-smb-share)并将[示例楼梯井视频文件](https://lvamedia.blob.core.windows.net/public/2018-03-05.10-27-03.10-30-01.admin.G329.mkv)复制到本地共享。

      > [!VIDEO https://www.microsoft.com/videoplayer/embed/RWDRJd]

   1. 可以看到 rtspsim 模块包含以下配置：
      ```
      "createOptions": {
                          "HostConfig": {
                            "Mounts": [
                              {
                                "Target": "/live/mediaServer/media",
                                "Source": "<your Local Docker Volume Mount name>",
                                "Type": "volume"
                              }
                            ],
                            "PortBindings": {
                              "554/tcp": [
                                {
                                  "HostPort": "554"
                                }
                              ]
                            }
                          }
                        }
      ```
## <a name="generate-and-deploy-the-deployment-manifest"></a>生成并部署部署清单

部署清单定义要部署到边缘设备的模块。 它还定义了这些模块的配置设置。

请按照以下步骤从模板文件生成清单，然后将其部署到边缘设备。

1. 打开 Visual Studio Code。
1. 在 `AZURE IOT HUB` 窗格旁，选择“更多操作”图标，设置 IoT 中心连接字符串。 可以从 `src/cloud-to-device-console-app/appsettings.json` 文件中复制字符串。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/vscode-common-screenshots/set-connection-string.png" alt-text="空间分析：连接字符串":::

1. 右键单击 `src/edge/deployment.spatialAnalysis.template.json` 并选择“生成 IoT Edge 部署清单”。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/spatial-analysis/generate-deployment-manifest.png" alt-text="空间分析：部署 amd64 json":::

   此操作应在 src/edge/config 文件夹中创建一个名为 `deployment.spatialAnalysis.amd64.json` 的清单文件。

1. 右键单击 `src/edge/config/deployment.spatialAnalysis.amd64.json`，选择“为单个设备创建部署”，然后选择边缘设备的名称。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/spatial-analysis/deployment-single-device.png" alt-text="空间分析：部署到单个设备":::

1. 在页面顶部，系统会提示你选择 IoT 中心设备，请从下拉菜单中选择边缘设备名。
1. 大约 30 秒后，在该窗口的左下角刷新 Azure IoT 中心窗格。 边缘设备现在显示以下已部署的模块：

   - Azure 视频分析器（模块名称为 avaedge）。
   - 实时流式处理协议 (RTSP) 模拟器（模块名称为 rtspsim）。
   - 空间分析（模块名称为 spatialanalysis）。

成功部署后，“输出”窗口中会显示一条消息，如下所示：

```
[Edge] Start deployment to device [<edge device name>]
[Edge] Deployment succeeded.
```

然后，你可在“设备”/“模块”下查找 `avaedge`、`spatialanalysis` 和 `rtspsim` 模块，其状态应当为“正在运行”。

## <a name="prepare-to-monitor-events"></a>准备监视事件

若要查看这些事件，请执行以下步骤：

1. 在 Visual Studio Code 中，打开“扩展”选项卡（或按 Ctrl+Shift+X），然后搜索“Azure IoT 中心”。
1. 右键单击并选择“扩展设置”。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/vscode-common-screenshots/extension-settings.png" alt-text="扩展设置":::

1. 搜索并启用“显示详细消息”。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/vscode-common-screenshots/verbose-message.png" alt-text="显示详细消息":::

1. 打开“资源管理器”窗格，找到左下角的“Azure IoT 中心”，右键单击并选择“开始监视内置事件终结点”。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/vscode-common-screenshots/start-monitoring.png" alt-text="空间分析：启动监视":::

## <a name="run-the-program"></a>运行程序

有一个 program.cs 将调用 `src/cloud-to-device-console-app/operations.json` 中的直接方法。 我们需要设置 operations.json 并提供一个 pipelineTopology 供管道使用。

在 operations.json 中：

- 按如下所示设置 pipelineTopology：

  ```json
  {
      "opName": "pipelineTopologySet",
      "opParams": {
          "pipelineTopologyUrl": "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-count-operation-topology.json"
      }
  },
  ```

- 按如下所示创建 livePipeline，在下面的 pipelineTopology 中设置参数：

  ```json
  {
      "opName": "livePipelineSet",
      "opParams": {
          "name": "Sample-Pipeline-1",
          "properties": {
              "topologyName": "InferencingWithCVExtension",
              "description": "Sample pipeline description",
              "parameters": [
                  {
                      "name": "rtspUrl",
                      "value": " rtsp://rtspsim:554/media/2018-03-05.10-27-03.10-30-01.admin.G329.mkv"
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
  },
  ```

  > [!Note]
  > 查看是否使用 `CognitiveServicesVisionExtension` 连接到 spatialanalysis 模块。 将 ${grpcUrl} 设置为 tcp://spatialAnalysis:<PORT_NUMBER>，如 tcp://spatialAnalysis:50051

  ```json
  {
          "@type": "#Microsoft.VideoAnalyzer.CognitiveServicesVisionProcessor",
          "name": "computerVisionExtension",
          "endpoint": {
            "@type": "#Microsoft.VideoAnalyzer.UnsecuredEndpoint",
            "url": "${grpcUrl}",
            "credentials": {
              "@type": "#Microsoft.VideoAnalyzer.UsernamePasswordCredentials",
              "username": "${spatialanalysisusername}",
              "password": "${spatialanalysispassword}"
            }
          },
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": [
                {
                  "property": "mediaType",
                  "operator": "is",
                  "value": "video"
                }
              ]
            }
          ],
          "operation": {
            "@type": "#Microsoft.VideoAnalyzer.SpatialAnalysisPersonCountOperation",
            "zones": [
              {
                "zone": {
                  "@type": "#Microsoft.VideoAnalyzer.NamedPolygonString",
                  "polygon": "[[0.37,0.43],[0.48,0.42],[0.53,0.56],[0.34,0.57],[0.34,0.46]]",
                  "name": "stairlanding"
                },
                "events": [
                  {
                    "trigger": "event",
                    "outputFrequency": "1",
                    "threshold": "16",
                    "focus": "bottomCenter"
                  }
                ]
              }
            ]
          }
        }
      ],
  ```

按下 F5 运行调试会话并按照“终端”说明进行操作。它将设置 pipelineTopology、设置 livePipeline、激活 livePipeline，并最终删除资源。

## <a name="interpret-results"></a>解释结果

实例化 pipelineTopology 时，应会看到“MediaSessionEstablished”事件。此处是一个[示例 MediaSessionEstablished 事件](detect-motion-emit-events-quickstart.md#mediasessionestablished-event)。

spatialanalysis 模块还会将 AI 见解事件发送到 Azure 视频分析器，然后再发送到 IoT 中心。它还会显示在“输出”窗口中。 ENTITY 是检测对象，EVENT 是空间分析事件。 此输出将传递到 Azure 视频分析器。

personZoneEvent 的输出示例（来自 `SpatialAnalysisPersonZoneCrossingOperation` 操作）：

```
{
  "timestamp": 145666725597833,
  "inferences": [
    {
      "type": "entity",
      "inferenceId": "8724dff43d3c4716936aa6c9a808ee2e",
      "entity": {
        "tag": {
          "value": "person",
          "confidence": 0.9980469
        },
        "box": {
          "l": 0.3848941,
          "t": 0.28569314,
          "w": 0.092775516,
          "h": 0.3819766
        }
      },
      "extensions": {
        "footprintX": "inf",
        "centerGroundPointY": "0.0",
        "trackingId": "80eeb5bbb6f542b89b5b16a1e37c53c4",
        "footprintY": "inf",
        "centerGroundPointX": "0.0"
      }
    },
    {
      "type": "event",
      "inferenceId": "0b0de3d228b640488fc0624950a6c9e8",
      "relatedInferences": [
        "8724dff43d3c4716936aa6c9a808ee2e"
      ],
      "event": {
        "name": "personZoneEnterExitEvent",
        "properties": {
          "trackingId": "80eeb5bbb6f542b89b5b16a1e37c53c4",
          "status": "Enter",
          "zone": "door"
        }
      }
    }
  ]
}
```

## <a name="operations"></a>操作：

### <a name="person-zone-crossing"></a>人员越过区域

#### <a name="parameters"></a>参数：

| 名称                      | 类型    | 说明                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ------------------------- | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| zones                     | list    | 区域列表。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| name                      | string  | 此区域的友好名称。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| polygon                   | 字符串  | 每个值对表示多边形顶点的 x、y。 多边形表示跟踪或统计其中人数的区域。 Float 值表示顶点相对于左上角的位置。 要计算 x、y 的绝对值，可以用这些值乘以帧大小。 当人员在区域内的像素数大于此像素数时，将发出 threshold float 事件。 当类型为 zonecrossing 时，默认值为 48；当时间为 DwellTime 时，默认值为 16。 这些都是实现最大准确性的推荐值。 |
| eventType                 | 字符串  | 对于 cognitiveservices.vision.spatialanalysis-personcrossingpolygon，此值应为 zonecrossing 或 zonedwelltime。                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| 触发器                   | 字符串  | 发送事件的触发器的类型。 支持的值：“事件”：有人进入或离开区域时触发。                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| 焦点                     | 字符串  | 人员边界框内的点位置用于计算事件数。 焦点的值可以是 footprint（人员足迹）、bottom_center（人员边界框的底部中心）、center（人员边界框的中心）。 默认值为 footprint。                                                                                                                                                                                                                                                                                               |
| enableFaceMaskClassifier  | boolean | true 表示启用检测视频流中佩戴口罩的人员的功能，false 表示禁用该功能。 默认情况下，此功能处于禁用状态。 口罩检测要求输入视频的宽度参数为 1920 "INPUT_VIDEO_WIDTH": 1920。 不会返回口罩属性。                                                                                                                                                                                                                                                                                                        |
| detectorNodeConfiguration | 字符串  | 所有空间分析操作的 DETECTOR_NODE_CONFIG 参数。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |

#### <a name="output"></a>输出：

```json
{
  "timestamp": 145666725597833,
  "inferences": [
    {
      "type": "entity",
      "inferenceId": "8724dff43d3c4716936aa6c9a808ee2e",
      "entity": {
        "tag": {
          "value": "person",
          "confidence": 0.9980469
        },
        "box": {
          "l": 0.3848941,
          "t": 0.28569314,
          "w": 0.092775516,
          "h": 0.3819766
        }
      },
      "extensions": {
        "footprintX": "inf",
        "centerGroundPointY": "0.0",
        "trackingId": "80eeb5bbb6f542b89b5b16a1e37c53c4",
        "footprintY": "inf",
        "centerGroundPointX": "0.0"
      }
    },
    {
      "type": "event",
      "inferenceId": "0b0de3d228b640488fc0624950a6c9e8",
      "relatedInferences": ["8724dff43d3c4716936aa6c9a808ee2e"],
      "event": {
        "name": "personZoneEnterExitEvent",
        "properties": {
          "trackingId": "80eeb5bbb6f542b89b5b16a1e37c53c4",
          "status": "Enter",
          "zone": "door"
        }
      }
    }
  ]
}
```

### <a name="more-operations"></a>更多操作：
`spatialAnalysis` 模块提供不同的操作：

- **personCount**
- **personDistance**
- **personCrossingLine**
- **personZoneCrossing**
- **customOperation**
<br></br>
<details>
  <summary>单击进行扩展，并查看适用于每个操作的不同配置选项。</summary>

### <a name="person-line-crossing"></a>人员越线

#### <a name="parameters"></a>参数：

| 名称                      | 类型    | 说明                                                                                                                                                                                                                                                                   |
| ------------------------- | ------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| lines                     | list    | 线列表。                                                                                                                                                                                                                                                                |
| name                      | string  | 此线的友好名称。                                                                                                                                                                                                                                                  |
| line                      | 字符串  | 每个值对表示线条的起点和终点。 Float 值表示顶点相对于左上角的位置。 要计算 x、y 的绝对值，可以用这些值乘以帧大小。                            |
| outputFrequency           | int     | 发出事件的比率。 如果 output_frequency = X，则每 X 个事件发出一次，例如 output_frequency = 2 表示每隔一个事件输出一次。 output_frequency 适用于 event 和 interval。                                                       |
| 焦点                     | 字符串  | 人员边界框内的点位置用于计算事件数。 焦点的值可以是 footprint（人员足迹）、bottom_center（人员边界框的底部中心）、center（人员边界框的中心）。 默认值为 footprint。 |
| 阈值                 | FLOAT   | 当人员在区域内的像素数大于此像素数时，将发出事件。 默认值为 16。 这是实现最大准确性的推荐值。                                                                                                |
| enableFaceMaskClassifier  | boolean | true 表示启用检测视频流中佩戴口罩的人员的功能，false 表示禁用该功能。 默认情况下，此功能处于禁用状态。 口罩检测要求输入视频的宽度参数为 1920 "INPUT_VIDEO_WIDTH": 1920。 不会返回口罩属性。          |
| detectorNodeConfiguration | 字符串  | 所有空间分析操作的 DETECTOR_NODE_CONFIG 参数。                                                                                                                                                                                                      |

#### <a name="output"></a>输出：

```json
{
  "timestamp": 145666620394490,
  "inferences": [
    {
      "type": "entity",
      "inferenceId": "2d3c7c7d6c0f4af7916eb50944523bdf",
      "entity": {
        "tag": {
          "value": "person",
          "confidence": 0.38330078
        },
        "box": {
          "l": 0.5316645,
          "t": 0.28169397,
          "w": 0.045862257,
          "h": 0.1594377
        }
      },
      "extensions": {
        "centerGroundPointX": "0.0",
        "centerGroundPointY": "0.0",
        "footprintX": "inf",
        "trackingId": "ac4a79a29a67402ba447b7da95907453",
        "footprintY": "inf"
      }
    },
    {
      "type": "event",
      "inferenceId": "2206088c80eb4990801f62c7050d142f",
      "relatedInferences": ["2d3c7c7d6c0f4af7916eb50944523bdf"],
      "event": {
        "name": "personLineEvent",
        "properties": {
          "trackingId": "ac4a79a29a67402ba447b7da95907453",
          "status": "CrossLeft",
          "zone": "door"
        }
      }
    }
  ]
}
```

### <a name="person-distance"></a>人员距离

#### <a name="parameters"></a>参数：

| 名称                      | 类型    | 说明                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ------------------------- | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| zones                     | list    | 区域列表。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| name                      | string  | 此区域的友好名称。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| polygon                   | 字符串  | 每个值对表示多边形顶点的 x、y。 多边形表示跟踪或统计其中人数的区域。 Float 值表示顶点相对于左上角的位置。 要计算 x、y 的绝对值，可以用这些值乘以帧大小。 当人员在区域内的像素数大于此像素数时，将发出 threshold float 事件。 当类型为 zonecrossing 时，默认值为 48；当时间为 DwellTime 时，默认值为 16。 这些都是实现最大准确性的推荐值。 |
| outputFrequency           | int     | 发出事件的比率。 如果 output_frequency = X，则每 X 个事件发出一次，例如 output_frequency = 2 表示每隔一个事件输出一次。 output_frequency 适用于 event 和 interval。                                                                                                                                                                                                                                                                                                                                                     |
| 焦点                     | 字符串  | 人员边界框内的点位置用于计算事件数。 焦点的值可以是 footprint（人员足迹）、bottom_center（人员边界框的底部中心）、center（人员边界框的中心）。 默认值为 footprint。                                                                                                                                                                                                                                                                                               |
| 阈值                 | FLOAT   | 当人员在区域内的像素数大于此像素数时，将发出事件。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| outputFrequency           | int     | 发出事件的比率。 如果 output_frequency = X，则每 X 个事件发出一次，例如 output_frequency = 2 表示每隔一个事件输出一次。 output_frequency 适用于 event 和 interval。                                                                                                                                                                                                                                                                                                                                                     |
| minimumDistanceThreshold  | FLOAT   | 以英尺为单位的距离，当人员之间小于该间距时，将触发“TooClose”事件。                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| maximumDistanceThreshold  | FLOAT   | 以英尺为单位的距离，当人员之间大于该间距时，将触发“TooFar”事件。                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| aggregationMethod         | 字符串  | 聚合 persondistance 结果的方法。 aggregationMethod 适用于 mode 和 average。                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| enableFaceMaskClassifier  | boolean | true 表示启用检测视频流中佩戴口罩的人员的功能，false 表示禁用该功能。 默认情况下，此功能处于禁用状态。 口罩检测要求输入视频的宽度参数为 1920 "INPUT_VIDEO_WIDTH": 1920。 不会返回口罩属性。                                                                                                                                                                                                                                                                                                        |
| detectorNodeConfiguration | 字符串  | 所有空间分析操作的 DETECTOR_NODE_CONFIG 参数。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |

#### <a name="output"></a>输出：

```json
{
  "timestamp": 145666613610297,
  "inferences": [
    {
      "type": "event",
      "inferenceId": "85a5fc4936294a3bac90b9c43876741a",
      "event": {
        "name": "personDistanceEvent",
        "properties": {
          "maximumDistanceThreshold": "14.5",
          "personCount": "0.0",
          "eventName": "Unknown",
          "zone": "door",
          "averageDistance": "0.0",
          "minimumDistanceThreshold": "1.5",
          "distanceViolationPersonCount": "0.0"
        }
      }
    }
  ]
}
```

### <a name="person-count"></a>人员计数

#### <a name="parameters"></a>参数：

| 名称                      | 类型    | 说明                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ------------------------- | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| zones                     | list    | 区域列表。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| name                      | string  | 此区域的友好名称。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| polygon                   | 字符串  | 每个值对表示多边形顶点的 x、y。 多边形表示跟踪或统计其中人数的区域。 Float 值表示顶点相对于左上角的位置。 要计算 x、y 的绝对值，可以用这些值乘以帧大小。 当人员在区域内的像素数大于此像素数时，将发出 threshold float 事件。 当类型为 zonecrossing 时，默认值为 48；当时间为 DwellTime 时，默认值为 16。 这些都是实现最大准确性的推荐值。 |
| outputFrequency           | int     | 发出事件的比率。 如果 output_frequency = X，则每 X 个事件发出一次，例如 output_frequency = 2 表示每隔一个事件输出一次。 output_frequency 适用于 event 和 interval。                                                                                                                                                                                                                                                                                                                                                     |
| 触发器                   | 字符串  | 发送事件的触发器的类型。 在计数发生更改时，支持的值为 event，表示发送事件；或者为 interval，表示定期发送事件，而不考虑计数是否发生更改。                                                                                                                                                                                                                                                                                                                                                           |
| 焦点                     | 字符串  | 人员边界框内的点位置用于计算事件数。 焦点的值可以是 footprint（人员足迹）、bottom_center（人员边界框的底部中心）、center（人员边界框的中心）。 默认值为 footprint。                                                                                                                                                                                                                                                                                               |
| 阈值                 | FLOAT   | 当人员在区域内的像素数大于此像素数时，将发出事件。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| enableFaceMaskClassifier  | boolean | true 表示启用检测视频流中佩戴口罩的人员的功能，false 表示禁用该功能。 默认情况下，此功能处于禁用状态。 口罩检测要求输入视频的宽度参数为 1920 "INPUT_VIDEO_WIDTH": 1920。 不会返回口罩属性。                                                                                                                                                                                                                                                                                                        |
| detectorNodeConfiguration | 字符串  | 所有空间分析操作的 DETECTOR_NODE_CONFIG 参数。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |

#### <a name="output"></a>输出：

```json
{
  "timestamp": 145666599533564,
  "inferences": [
    {
      "type": "entity",
      "inferenceId": "5b8076753b8c47bba8c72a7e0f7c5cc0",
      "entity": {
        "tag": {
          "value": "person",
          "confidence": 0.9458008
        },
        "box": {
          "l": 0.474487,
          "t": 0.26522297,
          "w": 0.066929355,
          "h": 0.2828749
        }
      },
      "extensions": {
        "centerGroundPointX": "0.0",
        "centerGroundPointY": "0.0",
        "footprintX": "inf",
        "footprintY": "inf"
      }
    },
    {
      "type": "event",
      "inferenceId": "fb309c9285f94f268378540b5fbbf5ad",
      "relatedInferences": ["5b8076753b8c47bba8c72a7e0f7c5cc0"],
      "event": {
        "name": "personCountEvent",
        "properties": {
          "personCount": "1.0",
          "zone": "demo"
        }
      }
    }
  ]
}
```

### <a name="custom"></a>自定义

#### <a name="parameters"></a>参数：

| 名称                   | 类型   | 说明                           |
| ---------------------- | ------ | ------------------------------------- |
| extensionConfiguration | 字符串 | 操作的 JSON 表示形式。 |

#### <a name="output"></a>输出：

```json
{
  "timestamp": 145666599533564,
  "inferences": [
    {
      "type": "entity",
      "inferenceId": "5b8076753b8c47bba8c72a7e0f7c5cc0",
      "entity": {
        "tag": {
          "value": "person",
          "confidence": 0.9458008
        },
        "box": {
          "l": 0.474487,
          "t": 0.26522297,
          "w": 0.066929355,
          "h": 0.2828749
        }
      },
      "extensions": {
        "centerGroundPointX": "0.0",
        "centerGroundPointY": "0.0",
        "footprintX": "inf",
        "footprintY": "inf"
      }
    },
    {
      "type": "event",
      "inferenceId": "fb309c9285f94f268378540b5fbbf5ad",
      "relatedInferences": ["5b8076753b8c47bba8c72a7e0f7c5cc0"],
      "event": {
        "name": "personCountEvent",
        "properties": {
          "personCount": "1.0",
          "zone": "demo"
        }
      }
    }
  ]
}
```

</details>

## <a name="playing-back-the-recording"></a>播放录制内容

可通过登录到 Azure 门户并观看视频来检查实时管道创建的视频分析器视频资源。

1. 打开 Web 浏览器，并转到 [Azure 门户](https://portal.azure.com/)。 输入登录到门户所需的凭据。 默认视图是服务仪表板。
1. 在订阅资源中找到视频分析器帐户，并打开“帐户”窗格。
1. 在“视频分析器”列表中选择“视频” 。
1. 你会发现以名称 `personcount` 列出的视频。 这是在管道拓扑文件中选择的名称。
1. 选择视频。
1. 在视频“详细信息”页上，单击“播放”图标

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/spatial-analysis/sa-video-playback.png" alt-text="视频播放的屏幕截图":::
   
1. 要在视频中以边界框的形式查看推理元数据，请单击“边界框”图标
   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/record-stream-inference-data-with-video/bounding-box.png" alt-text="“边界框”图标":::

[!INCLUDE [activate-deactivate-pipeline](./includes/common-includes/activate-deactivate-pipeline.md)]

## <a name="troubleshooting"></a>故障排除

spatialanalysis 是一个大型容器，其启动时间最多可能需要 30 秒。 spatialanalysis 容器启动并运行后，它将开始发送推理事件。

```JSON
[IoTHubMonitor] [3:37:28 PM] Message received from [ase03-edge/avaedge]:
{
  "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1620671848135494 1 IN IP4 172.27.86.122\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/cafeteria.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.08.19\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.066\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/cafeteria.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=640028;sprop-parameter-sets=Z2QAKKzZQHgCHoQAAAMABAAAAwDwPGDGWA==,aOvssiw=\r\na=control:track1\r\n"
}
[IoTHubMonitor] [3:37:30 PM] Message received from [ase03-edge/avaedge]:
{
  "type": "video",
  "location": "/videos/customoperation-05102021",
  "startTime&quot;: &quot;2021-05-10T18:37:27.931Z"
}
[IoTHubMonitor] [3:37:40 PM] Message received from [ase03-edge/avaedge]:
{
  "state&quot;: &quot;initializing"
}
[IoTHubMonitor] [3:37:50 PM] Message received from [ase03-edge/avaedge]:
{
  "state&quot;: &quot;initializing"
}
[IoTHubMonitor] [3:38:18 PM] Message received from [ase03-edge/avaedge]:
{
  "type": "video",
  "location": "/videos/customoperation-05102021",
  "startTime&quot;: &quot;2021-05-10T18:37:27.931Z"
}
[IoTHubMonitor] [3:38:42 PM] Message received from [ase03-edge/avaedge]:
{
  "timestamp": 145860472980260,
  "inferences": [
    {
      "type": "entity",
      "inferenceId": "647aacf9d8bc47078a1ed31d1c459c24",
      "entity": {
        "tag": {
          "value": "person",
          "confidence": 0.7583008
        },
        "box": {
          "l": 0.48213565,
          "t": 0.21217245,
          "w": 0.056364775,
          "h": 0.29961595
        }
      },
      "extensions": {
        "centerGroundPointY": "0.0",
        "centerGroundPointX": "0.0",
        "footprintX": "0.5087100982666015",
        "footprintY&quot;: &quot;0.49634415356080924"
      }
    },
    {
      "type": "event",
      "inferenceId": "dae6c2b742634196b615c128654845dc",
      "relatedInferences": [
        "647aacf9d8bc47078a1ed31d1c459c24"
      ],
      "event": {
        "name": "personCountEvent",
        "properties": {
          "personCount": "1.0",
          "zone&quot;: &quot;stairlanding"
        }
      }
    }
  ]
}

```

> [!NOTE]
> 你可能会看到“正在初始化”消息。 这些消息在 spatialAnalysis 模块启动时显示，可能需要 60 秒才能进入运行状态。 请耐心等待，你应该会看到推理事件流通过。

## <a name="next-steps"></a>后续步骤

要尝试 `spatialAnalysis` 模块提供的不同操作，请参阅以下 pipelineTopologies：

- [personCount](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-count-operation-topology.json)
- [personDistance](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-distance-operation-topology.json)
- [personCrossingLine](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-line-crossing-operation-topology.json)
- [personZoneCrossing](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-zone-crossing-operation-topology.json)
- [customOperation](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/custom-operation-topology.json)

> [!Tip]
> 使用帧中有多个人的[示例视频文件](https://lvamedia.blob.core.windows.net/public/2018-03-07.16-50-00.16-55-00.school.G421.mkv)。
