---
title: 检测运动，使用 Azure 视频分析器录制视频
description: 本快速入门介绍如何使用 Azure 视频分析器边缘模块来检测实时视频流中的运动，并将视频录制到视频分析器帐户。
ms.topic: quickstart
ms.date: 06/01/2021
ms.openlocfilehash: 7772a32a529eeba36378e9ffb7568912ae66c7a2
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114601500"
---
# <a name="quickstart-detect-motion-record-video-to-video-analyzer"></a>快速入门：检测运动，将视频录制到视频分析器

本文逐步讲解如何使用 Azure 视频分析器边缘模块进行[基于事件的录制](event-based-video-recording-concept.md)。 它将 Azure 中的 Linux VM 用作 IoT Edge 设备并使用模拟的实时视频流。 分析此视频流以了解是否存在移动物体。 检测到运动时，该模块会将事件发送到 Azure IoT 中心，并将视频流的相关部分录制为视频分析器帐户中的[视频资源](terminology.md#video)。

## <a name="prerequisites"></a>先决条件

* 包含活动订阅的 Azure 帐户。 如果没有帐户，可免费[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

    [!INCLUDE [azure-subscription-permissions](./includes/common-includes/azure-subscription-permissions.md)]
* 包含以下扩展的 [Visual Studio Code](https://code.visualstudio.com/)：
    * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

### <a name="set-up-azure-resources"></a>设置 Azure 资源

[![部署到 Azure](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

部署过程大约需要 20 分钟。 完成部署后，Azure 订阅中会部署某些 Azure 资源，包括：
1. **视频分析器帐户** - 此 [云服务](overview.md)用于注册视频分析器边缘模块，并用于播放录制的视频和视频分析。
1. **存储帐户** - 用于存储录制的视频和视频分析。
1. **托管标识** - 这是分配给用户的 [托管标识]../../active-directory/managed-identities-azure-resources/overview.md)，用于管理对上述存储帐户的访问。
1. **虚拟机** - 这是将用作模拟边缘设备的虚拟机。
1. **IoT 中心** - 这充当消息中心，用于在 IoT 应用程序、IoT Edge 模块以及它管理的设备之间进行双向通信。

你可在[此处](https://github.com/Azure/video-analyzer/tree/main/setup)获取更多详细信息。

## <a name="review-the-sample-video"></a>观看示例视频

上述部署过程创建的虚拟机中有多个 MKV 文件。  其中一个文件称为 `lots_015.mkv`。 在以下步骤中，我们将使用此视频文件来模拟本教程中的实时传送流。

可以使用 [VLC Player](https://www.videolan.org/vlc/) 等应用程序，启动它，按 `Ctrl+N`，然后粘贴[停车场视频示例](https://lvamedia.blob.core.windows.net/public/lots_015.mkv)链接以开始播放。 大约在 5 秒的时候，一辆白色汽车在停车场间移动。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4LUbN]

完成以下步骤时，你将使用视频分析器边缘模块检测汽车的运动，并从大约第 5 秒的标记处开始录制视频短片。 

下图直观呈现了整个过程。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/detect-motion-record-video-clips-cloud/topology.png" alt-text="根据运动事件将基于事件的视频录制到视频资源":::

## <a name="set-up-your-development-environment"></a>设置开发环境

### <a name="obtain-your-iot-hub-connection-string"></a>获取 IoT 中心连接字符串

1. 在 Azure 门户中，导航到在上述设置步骤中创建的 IoT 中心
1. 在左侧导航栏中找到“共享访问策略”选项，然后单击此处。
1. 单击名为“iothubowner”的策略
1. 复制“主连接字符串”，它类似于 `HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX`

### <a name="connect-to-the-iot-hub"></a>连接到 IoT 中心

1. 打开 Visual Studio Code，选择“视图” > “资源管理器” 。 或选择 Ctrl+Shift+E。
1. 在“资源管理器”选项卡的左下角，选择“Azure IoT 中心”。
1. 选择“更多选项”图标以查看上下文菜单。 然后选择“设置 IoT 中心连接字符串”。
1. 输入框出现时，在其中输入 IoT 中心连接字符串。
1. 大约 30 秒后，在左下部分刷新 Azure IoT 中心。 你应会看到已部署以下模块的边缘设备 `avasample-iot-edge-device`：
    * 视频分析器边缘模块（模块名称 avaedge）
    * RTSP 模拟器（模块名称为“rtspsim”）


> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/get-started-detect-motion-emit-events/modules-node.png" alt-text="展开模块节点":::

> [!TIP]
> 如果你自己已在边缘设备（例如 ARM64 设备）上[手动部署了视频分析器](deploy-iot-edge-device.md)，则会在 Azure IoT 中心看到模块显示在该设备下。 你可以选择该模块，然后执行下面的剩余步骤。

### <a name="prepare-to-monitor-the-modules"></a>准备监视模块 

运行本快速入门时，事件将被发送到 IoT 中心。 若要查看这些事件，请执行以下步骤：

1. 在 Visual Studio Code 中，打开“扩展”选项卡（或按 Ctrl+Shift+X），然后搜索“Azure IoT 中心” 。
1. 右键单击并选择“扩展设置”。
 
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/get-started-detect-motion-emit-events/extension-settings.png" alt-text="选择“扩展设置”":::
1. 搜索并启用“显示详细消息”。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/get-started-detect-motion-emit-events/verbose-message.png" alt-text="显示详细消息":::
1. 在 Visual Studio Code 中打开“资源管理器”窗格，然后在左下角查找“Azure IoT 中心”。
1. 展开“设备”节点。
1. 右键单击 `avasample-iot-edge-device`，然后选择“开始监视内置事件终结点”。

    [!INCLUDE [provide-builtin-endpoint](./includes/common-includes/provide-builtin-endpoint.md)]

## <a name="use-direct-method-calls-to-analyze-live-video"></a>使用直接方法调用来分析实时视频

现在，你可调用视频分析器边缘模块公开的直接方法来分析实时视频流。 阅读[视频分析器直接方法](direct-methods.md)，检查模块提供的所有直接方法。 

### <a name="enumerate-pipeline-topologies"></a>枚举管道拓扑

此步骤将枚举模块中的所有[管道拓扑](pipeline.md)。

1. 右键单击“avaedge”模块，然后从上下文菜单中选择“调用模块直接方法”。
1. 你将看到一个编辑框在 Visual Studio Code 窗口的顶部中间弹出。 在编辑框中输入“pipelineTopologyList”，然后按 Enter。
1. 接下来，复制以下 JSON 有效负载并将其粘贴到编辑框中，然后按 Enter。
   
```json
{
    "@apiVersion" : "1.0"
}
```

几秒钟内，“输出”窗口中显示以下响应：
    
```
[DirectMethod] Invoking Direct Method [pipelineTopologyList] to [avasample-iot-edge-device/avaedge] ...
[DirectMethod] Response from [avasample-iot-edge-device/avaedge]:
{
  "status": 200,
  "payload": {
    "value": []
  }
}
```

由于没有创建管道拓扑，所以预期会出现上述响应。

### <a name="set-a-pipeline-topology"></a>设置管道拓扑

使用上述的相同步骤，可调用 `pipelineTopologySet` 来设置将以下 JSON 用作有效负载的管道拓扑。 你将创建一个名为“EVRtoVideoSinkOnMotionDetection”的管道拓扑。

> [!NOTE]
> 在下面的有效负载中，`videoName` 属性设置为“sample-motion-video-camera001”，这是视频分析器帐户中创建的视频资源的名称。 对于录制的每个实时视频源，此资源名称必须是唯一的。 应根据需要编辑下面的 `videoName` 属性以确保唯一性。

```
{
  "@apiVersion": "1.0",
  "name": "EVRtoVideoSinkOnMotionDetection",
  "properties": {
    "description": "Event-based video recording to Video Sink based on motion events",
    "parameters": [
      {
        "name": "rtspUserName",
        "type": "String",
        "description": "rtsp source user name.",
        "default&quot;: &quot;dummyUserName"
      },
      {
        "name": "rtspPassword",
        "type": "String",
        "description": "rtsp source password.",
        "default&quot;: &quot;dummyPassword"
      },
      {
        "name": "rtspUrl",
        "type": "String",
        "description&quot;: &quot;rtsp Url"
      },
      {
        "name": "motionSensitivity",
        "type": "String",
        "description": "motion detection sensitivity",
        "default&quot;: &quot;medium"
      },
      {
        "name": "hubSinkOutputName",
        "type": "String",
        "description": "hub sink output name",
        "default&quot;: &quot;inferenceOutput"
      }
    ],
    "sources": [
      {
        "@type": "#Microsoft.VideoAnalyzer.RtspSource",
        "name": "rtspSource",
        "endpoint": {
          "@type": "#Microsoft.VideoAnalyzer.UnsecuredEndpoint",
          "url": "${rtspUrl}",
          "credentials": {
            "@type": "#Microsoft.VideoAnalyzer.UsernamePasswordCredentials",
            "username": "${rtspUserName}",
            "password&quot;: &quot;${rtspPassword}"
          }
        }
      }
    ],
    "processors": [
      {
        "@type": "#Microsoft.VideoAnalyzer.MotionDetectionProcessor",
        "name": "motionDetection",
        "sensitivity": "${motionSensitivity}",
        "inputs": [
          {
            "nodeName": "rtspSource",
            "outputSelectors": [
              {
                "property": "mediaType",
                "operator": "is",
                "value&quot;: &quot;video"
              }
            ]
          }
        ]
      },
      {
        "@type": "#Microsoft.VideoAnalyzer.SignalGateProcessor",
        "name": "signalGateProcessor",
        "inputs": [
          {
            "nodeName&quot;: &quot;motionDetection"
          },
          {
            "nodeName": "rtspSource",
            "outputSelectors": [
              {
                "property": "mediaType",
                "operator": "is",
                "value&quot;: &quot;video"
              }
            ]
          }
        ],
        "activationEvaluationWindow": "PT1S",
        "activationSignalOffset": "PT0S",
        "minimumActivationTime": "PT30S",
        "maximumActivationTime&quot;: &quot;PT30S"
      }
    ],
    "sinks": [
      {
        "@type": "#Microsoft.VideoAnalyzer.VideoSink",
        "name": "videoSink",
        "videoName": "sample-motion-video-camera001",
        "inputs": [
          {
            "nodeName": "signalGateProcessor",
            "outputSelectors": [
              {
                "property": "mediaType",
                "operator": "is",
                "value&quot;: &quot;video"
              }
            ]
          }
        ],
        "videoCreationProperties": {
          "title": "sample-motion-video-camera001",
          "description": "Motion-detection based recording of clips to a video resource",
          "segmentLength&quot;: &quot;PT30S"
        },
        "localMediaCachePath": "/var/lib/videoanalyzer/tmp/",
        "localMediaCacheMaximumSizeMiB&quot;: &quot;2048"
      },
      {
        "@type": "#Microsoft.VideoAnalyzer.IoTHubMessageSink",
        "name": "hubSink",
        "hubOutputName": "${hubSinkOutputName}",
        "inputs": [
          {
            "nodeName&quot;: &quot;motionDetection"
          }
        ]
      }
    ]
  }
}
```

上述 JSON 有效负载的结果是创建一个定义了五个参数（其中四个参数具有默认值）的管道拓扑。 拓扑有一个源节点（[RTSP 源](pipeline.md#rtsp-source)）、两个处理器节点（[运动检测处理器](pipeline.md#motion-detection-processor)和[信号入口处理器](pipeline.md#signal-gate-processor)），以及两个接收器节点（IoT 中心接收器和[视频接收器](pipeline.md#video-sink)）。 拓扑的直观呈现形式如下所示。

几秒钟内，“输出”窗口中显示以下响应。

```
[DirectMethod] Invoking Direct Method [pipelineTopologySet] to [avasample-iot-edge-device/avaedge] ...
[DirectMethod] Response from [avasample-iot-edge-device/avaedge]:
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2021-05-03T15:17:53.483Z",
      "lastModifiedAt&quot;: &quot;2021-05-03T15:17:53.483Z"
    },
    "name": "EVRtoVideoSinkOnMotionDetection",
    "properties": {
      "description": "Event-based video recording to Video Sink based on motion events",
      "parameters": [
        {
          "name": "hubSinkOutputName",
          "type": "string",
          "description": "hub sink output name",
          "default&quot;: &quot;inferenceOutput"
        },
        {
          "name": "motionSensitivity",
          "type": "string",
          "description": "motion detection sensitivity",
          "default&quot;: &quot;medium"
        },
        {
          "name": "rtspPassword",
          "type": "string",
          "description": "rtsp source password.",
          "default&quot;: &quot;dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "string",
          "description&quot;: &quot;rtsp Url"
        },
        {
          "name": "rtspUserName",
          "type": "string",
          "description": "rtsp source user name.",
          "default&quot;: &quot;dummyUserName"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.VideoAnalyzer.RtspSource",
          "name": "rtspSource",
          "transport": "tcp",
          "endpoint": {
            "@type": "#Microsoft.VideoAnalyzer.UnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.VideoAnalyzer.UsernamePasswordCredentials",
              "username": "${rtspUserName}",
              "password&quot;: &quot;${rtspPassword}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.VideoAnalyzer.MotionDetectionProcessor",
          "sensitivity": "${motionSensitivity}",
          "eventAggregationWindow": "PT1S",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": [
                {
                  "property": "mediaType",
                  "operator": "is",
                  "value&quot;: &quot;video"
                }
              ]
            }
          ]
        },
        {
          "@type": "#Microsoft.VideoAnalyzer.SignalGateProcessor",
          "activationEvaluationWindow": "PT1S",
          "activationSignalOffset": "PT0S",
          "minimumActivationTime": "PT30S",
          "maximumActivationTime": "PT30S",
          "name": "signalGateProcessor",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            },
            {
              "nodeName": "rtspSource",
              "outputSelectors": [
                {
                  "property": "mediaType",
                  "operator": "is",
                  "value&quot;: &quot;video"
                }
              ]
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.VideoAnalyzer.VideoSink",
          "localMediaCachePath": "/var/lib/videoanalyzer/tmp/",
          "localMediaCacheMaximumSizeMiB": "2048",
          "videoName": "sample-motion-video-camera001",
          "videoCreationProperties": {
            "title": "sample-motion-video-camera001",
            "description": "Motion-detection based recording of clips to a video resource",
            "segmentLength&quot;: &quot;PT30S"
          },
          "name": "videoSink",
          "inputs": [
            {
              "nodeName": "signalGateProcessor",
              "outputSelectors": [
                {
                  "property": "mediaType",
                  "operator": "is",
                  "value&quot;: &quot;video"
                }
              ]
            }
          ]
        },
        {
          "@type": "#Microsoft.VideoAnalyzer.IotHubMessageSink",
          "hubOutputName": "${hubSinkOutputName}",
          "name": "hubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        }
      ]
    }
  }
}
```



返回的状态为 201，表示已创建新管道拓扑。 请尝试在后续步骤中使用以下直接方法：

* 再次调用 `pipelineTopologySet`，并检查返回的状态代码是否为 200。 状态代码 200 指示已成功更新现有管道拓扑。
* 再次调用 `pipelineTopologySet`，但更改说明字符串。 检查响应中的状态代码是否为 200，说明是否更新为新值。
* 调用先前步骤中概述的 `pipelineTopologyList`，并检查现在是否看到返回的有效负载中列出了“EVRtoVideoSinkOnMotionDetection”拓扑。

### <a name="read-the-pipeline-topology"></a>读取管道拓扑

现在，使用以下有效负载调用 `pipelineTopologyGet`
```

{
    "@apiVersion" : "1.0",
    "name&quot; : &quot;EVRtoVideoSinkOnMotionDetection"
}
```

几秒钟内，你应该可在“输出”窗口中看到以下响应

```
[DirectMethod] Invoking Direct Method [pipelineTopologyGet] to [avasample-iot-edge-device/avaedge] ...
[DirectMethod] Response from [avasample-iot-edge-device/avaedge]:
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2021-05-03T15:17:53.483Z",
      "lastModifiedAt&quot;: &quot;2021-05-03T15:17:53.483Z"
    },
    "name": "EVRtoVideoSinkOnMotionDetection",
    "properties": {
      "description": "Event-based video recording to Video Sink based on motion events",
      "parameters": [
        {
          "name": "hubSinkOutputName",
          "type": "string",
          "description": "hub sink output name",
          "default&quot;: &quot;inferenceOutput"
        },
        {
          "name": "motionSensitivity",
          "type": "string",
          "description": "motion detection sensitivity",
          "default&quot;: &quot;medium"
        },
        {
          "name": "rtspPassword",
          "type": "string",
          "description": "rtsp source password.",
          "default&quot;: &quot;dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "string",
          "description&quot;: &quot;rtsp Url"
        },
        {
          "name": "rtspUserName",
          "type": "string",
          "description": "rtsp source user name.",
          "default&quot;: &quot;dummyUserName"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.VideoAnalyzer.RtspSource",
          "name": "rtspSource",
          "transport": "tcp",
          "endpoint": {
            "@type": "#Microsoft.VideoAnalyzer.UnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.VideoAnalyzer.UsernamePasswordCredentials",
              "username": "${rtspUserName}",
              "password&quot;: &quot;${rtspPassword}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.VideoAnalyzer.MotionDetectionProcessor",
          "sensitivity": "${motionSensitivity}",
          "eventAggregationWindow": "PT1S",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": [
                {
                  "property": "mediaType",
                  "operator": "is",
                  "value&quot;: &quot;video"
                }
              ]
            }
          ]
        },
        {
          "@type": "#Microsoft.VideoAnalyzer.SignalGateProcessor",
          "activationEvaluationWindow": "PT1S",
          "activationSignalOffset": "PT0S",
          "minimumActivationTime": "PT30S",
          "maximumActivationTime": "PT30S",
          "name": "signalGateProcessor",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            },
            {
              "nodeName": "rtspSource",
              "outputSelectors": [
                {
                  "property": "mediaType",
                  "operator": "is",
                  "value&quot;: &quot;video"
                }
              ]
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.VideoAnalyzer.IotHubMessageSink",
          "hubOutputName": "${hubSinkOutputName}",
          "name": "hubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        },
        {
          "@type": "#Microsoft.VideoAnalyzer.VideoSink",
          "localMediaCachePath": "/var/lib/videoanalyzer/tmp/",
          "localMediaCacheMaximumSizeMiB": "2048",
          "videoName": "sample-motion-video-camera001",
          "videoCreationProperties": {
            "title": "sample-motion-video-camera001",
            "description": "Motion-detection based recording of clips to a video resource",
            "segmentLength&quot;: &quot;PT30S"
          },
          "name": "videoSink",
          "inputs": [
            {
              "nodeName": "signalGateProcessor",
              "outputSelectors": [
                {
                  "property": "mediaType",
                  "operator": "is",
                  "value&quot;: &quot;video"
                }
              ]
            }
          ]
        }
      ]
    }
  }
}
```

请注意响应有效负载中的以下属性：

* 状态代码为 200，表示成功。
* 有效负载包含 `createdAt` 时间戳和 `lastModifiedAt` 时间戳。

### <a name="create-a-live-pipeline-using-the-topology"></a>使用拓扑创建实时管道

接下来，创建引用上述管道拓扑的实时管道。 使用以下有效负载调用 `livePipelineSet` 直接方法：

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Pipeline-1",
    "properties" : {
        "topologyName" : "EVRtoVideoSinkOnMotionDetection",
        "description" : "Sample pipeline description",
        "parameters" : [
            { "name" : "rtspUrl", "value" : "rtsp://rtspsim:554/media/lots_015.mkv" }
        ]
    }
}
```

注意以下事项：

* 上述有效负载指定实时管道将要使用的拓扑（“EVRtoVideoSinkOnMotionDetection”）。
* 有效负载包含 `rtspUrl` 的参数值，该参数在拓扑有效负载中没有默认值。

几秒钟内，你可在“输出”窗口中看到以下响应：

```
[DirectMethod] Invoking Direct Method [livePipelineSet] to [avasample-iot-edge-device/avaedge] ...
[DirectMethod] Response from [avasample-iot-edge-device/avaedge]:
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2021-05-03T15:20:29.023Z",
      "lastModifiedAt&quot;: &quot;2021-05-03T15:20:29.023Z"
    },
    "name": "Sample-Pipeline-1",
    "properties": {
      "state": "Inactive",
      "description": "Sample pipeline description",
      "topologyName": "EVRtoVideoSinkOnMotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value&quot;: &quot;rtsp://rtspsim:554/media/lots_015.mkv"
        }
      ]
    }
  }
}
```

请注意响应有效负载中的以下属性：

* 状态代码为 201，表示已创建新管道。
* 状态为“非活动”，表示实时管道已创建但未激活。 有关详细信息，请参阅[管道状态](pipeline.md#pipeline-states)。

请尝试在后续步骤中使用以下直接方法：

* 使用相同的有效负载再次调用 `livePipelineSet`，并注意返回的状态代码现在是否为 200。
* 再次调用 `livePipelineSet`，但使用不同的说明，并注意响应有效负载中更新的说明，它指示实时管道已成功更新。

    > [!NOTE]
    > 如[管道拓扑](pipeline.md#pipeline-topologies)中所述，你可创建多个实时管道，使用同一管道拓扑分析来自多个相机的实时视频流。 但这个特定的拓扑对 `videoName` 的值进行硬编码。 由于只能将一个实时视频源录制到视频分析器视频资源，因此切勿使用此特定拓扑创建其他实时管道。

### <a name="activate-the-live-pipeline"></a>激活实时管道

接下来，可激活实时管道，这样将通过管道启动（模拟的）实时视频流。 使用以下有效负载调用直接方法 `livePipelineActivate`：

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Pipeline-1"
}
```

几秒钟内，你应该可在“输出”窗口中看到以下响应

```
[DirectMethod] Invoking Direct Method [livePipelineActivate] to [avasample-iot-edge-device/avaedge] ...
[DirectMethod] Response from [avasample-iot-edge-device/avaedge]:
{
  "status": 200,
  "payload": null
}
```

响应有效负载中的状态代码 200 指示实时管道已成功激活。

### <a name="check-the-state-of-the-live-pipeline"></a>检查实时管道的状态

现在使用以下有效负载调用 `livePipelineGet` 直接方法：

```
{
    "@apiVersion" : "1.0",
    "name&quot; : &quot;Sample-Pipeline-1"
}
```

几秒钟内，你应该可在“输出”窗口中看到以下响应

```
[DirectMethod] Invoking Direct Method [livePipelineGet] to [avasample-iot-edge-device/avaedge] ...
[DirectMethod] Response from [avasample-iot-edge-device/avaedge]:
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2021-05-03T14:21:21.750Z",
      "lastModifiedAt&quot;: &quot;2021-05-03T14:21:21.750Z"
    },
    "name": "Sample-Pipeline-1",
    "properties": {
      "state": "Active",
      "description": "Sample pipeline description",
      "topologyName": "EVRtoVideoSinkOnMotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value&quot;: &quot;rtsp://rtspsim:554/media/lots_015.mkv"
        }
      ]
    }
  }
}
```

请注意响应有效负载中的以下属性：

* 状态代码为 200，表示成功。
* 状态为“活动”，表示实时管道现处于“活动”状态。

## <a name="observe-results"></a>观察结果

上面创建和激活的实时管道使用运动检测处理器节点来检测传入实时视频流中的运动，并将事件发送到 IoT 中心接收器。 这些事件随后以消息形式转送到 IoT 中心，你现可观察到这一点。 可在“输出”窗口看到以下消息

```
[IoTHubMonitor] [1:22:53 PM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1620066173760872 1 IN IP4 172.18.0.4\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.08.19\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-73.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=640028;sprop-parameter-sets=Z2QAKKzZQHgCJoQAAAMABAAAAwDwPGDGWA==,aOvhEsiw\r\na=control:track1\r\n"
  },
  "properties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/videoAnalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sources/rtspSource",
    "eventType": "Microsoft.VideoAnalyzer.Diagnostics.MediaSessionEstablished",
    "eventTime": "2021-05-03T18:22:53.761Z",
    "dataVersion": "1.0"
  },
  "systemProperties": {
    "iothub-connection-device-id": "avasample-iot-edge-device",
    "iothub-connection-module-id": "avaedge",
    "iothub-connection-auth-method": "{\"scope\":\"module\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id": "637556611958535962",
    "iothub-enqueuedtime": 1620066173816,
    "iothub-message-source": "Telemetry",
    "messageId": "c2de6a40-1e0a-45ef-9449-599fc5680d05",
    "contentType": "application/json",
    "contentEncoding": "utf-8"
  }
}
[IoTHubMonitor] [1:22:59 PM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "timestamp": 145805956115743,
    "inferences": [
      {
        "type": "motion",
        "motion": {
          "box": {
            "l": 0.48954,
            "t": 0.140741,
            "w": 0.075,
            "h": 0.058824
          }
        }
      }
    ]
  },
  "properties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/videoAnalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/processors/motionDetection",
    "eventType": "Microsoft.VideoAnalyzer.Analytics.Inference",
    "eventTime": "2021-05-03T18:22:59.063Z",
    "dataVersion&quot;: &quot;1.0"
  },
  "systemProperties": {
    "iothub-connection-device-id": "avasample-iot-edge-device",
    "iothub-connection-module-id": "avaedge",
    "iothub-connection-auth-method": "{\"scope\":\"module\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id": "637556611958535962",
    "iothub-enqueuedtime": 1620066179359,
    "iothub-message-source": "Telemetry",
    "messageId": "9ccfab80-2993-42c7-9452-92e21df96413",
    "contentType": "application/json",
    "contentEncoding&quot;: &quot;utf-8"
  }
}

```

请注意上述消息中的以下属性：

* 每条消息都包含 `body` 部分和 `properties` 部分。 若要理解这些部分的内容，请阅读[创建和读取 IoT 中心消息](../../iot-hub/iot-hub-devguide-messages-construct.md)一文。
* 第一条消息是 MediaSessionEstablished，指示 RTSP 源节点（使用者）能够与 RTSP 模拟器建立连接，并开始接收（模拟的）实时源。
* `subject` 引用生成消息的实时管道中的节点。 在本例中，该消息来自 RTSP 源节点。
* `eventType` 指示此事件是诊断事件。
* `eventTime` 指示事件发生的时间。
* `body` 包含有关该事件的数据，它是 [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) 消息。
* 第二条消息是推理事件。 你可查看到在 MediaSessionEstablished 消息后大约 5 秒发送了该消息，这对应于视频开始时到汽车穿过停车场的时间之间的延迟。
* `subject` 引用管道中生成此消息的运动检测处理器节点
* 这是分析事件，因此 `body` 包含 `timestamp` 和 `inferences` 数据。
* `inferences` 部分指示 `type` 为“motion”，并且具有关于“motion”事件的其他数据。

请注意正文中的 `messageId` 部分为“9ccfab80-2993-42c7-9452-92e21df96413”。 它显示在以下操作事件中：

```
[IoTHubMonitor] [1:23:31 PM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "outputType": "video",
    "outputLocation&quot;: &quot;sample-motion-video-camera001"
  },
  "properties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/videoAnalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sinks/videoSink",
    "eventType": "Microsoft.VideoAnalyzer.Operational.RecordingStarted",
    "eventTime": "2021-05-03T18:23:31.319Z",
    "dataVersion&quot;: &quot;1.0"
  },
  "systemProperties": {
    "iothub-connection-device-id": "avasample-iot-edge-device",
    "iothub-connection-module-id": "avaedge",
    "iothub-connection-auth-method": "{\"scope\":\"module\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id": "637556611958535962",
    "iothub-enqueuedtime": 1620066211373,
    "iothub-message-source": "Telemetry",
    "messageId": "c7cbb363-7cc7-4169-936f-55de5fae111c",
    "correlationId": "9ccfab80-2993-42c7-9452-92e21df96413",
    "contentType": "application/json",
    "contentEncoding&quot;: &quot;utf-8"
  }
}
```
这条 RecordingStarted 消息指示视频录制已开始。 注意，“9ccfab80-2993-42c7-9452-92e21df96413”的 `correlationId` 值与推理消息的 `messageId` 匹配，这样你便可以跟踪触发录制的事件。 下一个操作事件如下：

```
[IoTHubMonitor] [1:24:00 PM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "outputType": "video",
    "outputLocation&quot;: &quot;sample-motion"
  },
  "properties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/videoAnalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sinks/videoSink",
    "eventType": "Microsoft.VideoAnalyzer.Operational.RecordingAvailable",
    "eventTime": "2021-05-03T18:24:00.686Z",
    "dataVersion&quot;: &quot;1.0"
  },
  "systemProperties": {
    "iothub-connection-device-id": "avasample-iot-edge-device",
    "iothub-connection-module-id": "avaedge",
    "iothub-connection-auth-method": "{\"scope\":\"module\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id": "637556611958535962",
    "iothub-enqueuedtime": 1620066240741,
    "iothub-message-source": "Telemetry",
    "messageId": "5b26aa88-e037-4834-af34-a6a4df3c42c2",
    "correlationId": "9ccfab80-2993-42c7-9452-92e21df96413",
    "contentType": "application/json",
    "contentEncoding&quot;: &quot;utf-8"
  }
}
[IoTHubMonitor] [1:24:00 PM] Message received from [avasample-iot-edge-device/avaedge]:
{
```

此 RecordingAvailable 事件指示媒体数据现已录制到视频资源。 注意，`correlationId` 相同：“9ccfab80-2993-42c7-9452-92e21df96413”。 此消息链（具有相同的 `correlationId`）的最后一个操作事件如下所示：

```
[IoTHubMonitor] [1:24:00 PM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "outputType": "video",
    "outputLocation&quot;: &quot;sample-motion"
  },
  "properties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/videoAnalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sinks/videoSink",
    "eventType": "Microsoft.VideoAnalyzer.Operational.RecordingStopped",
    "eventTime": "2021-05-03T18:24:00.710Z",
    "dataVersion&quot;: &quot;1.0"
  },
  "systemProperties": {
    "iothub-connection-device-id": "avasample-iot-edge-device",
    "iothub-connection-module-id": "avaedge",
    "iothub-connection-auth-method": "{\"scope\":\"module\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id": "637556611958535962",
    "iothub-enqueuedtime": 1620066240766,
    "iothub-message-source": "Telemetry",
    "messageId": "f3dbd5d5-3176-4d5b-80d8-c67de85bc619",
    "correlationId": "9ccfab80-2993-42c7-9452-92e21df96413",
    "contentType": "application/json",
    "contentEncoding&quot;: &quot;utf-8"
  }
}
```
此 RecordingStopped 事件指示信号入口已关闭，并且已录制传入的实时视频的相关部分。 注意，`correlationId` 相同：“9ccfab80-2993-42c7-9452-92e21df96413”。

在拓扑中，为信号入口处理器节点配置的激活时间为 30 秒，这意味着，管道拓扑大约会录制 30 秒的视频。  录制视频时，运动检测处理器节点会继续发出推理事件，这些事件将显示在 RecordingAvailable 和 RecordingStopped 事件之间的“输出”窗口中  。

如果允许实时管道继续运行，RTSP 模拟器将到达视频文件的末尾并停止/断开连接。 然后，RTSP 源节点会重新连接到模拟器，此过程将重复。
    
## <a name="invoke-additional-direct-method-calls-to-clean-up"></a>调用其他直接方法调用进行清理

接下来，你可调用直接方法来停用和删除实时管道（按该顺序执行操作）。

### <a name="deactivate-the-live-pipeline"></a>停用实时管道

使用以下有效负载调用 `livePipelineDeactivate` 直接方法：

```
{
    "@apiVersion" : "1.0",
    "name&quot; : &quot;Sample-Pipeline-1"
}
```

几秒钟内，你应该可在“输出”窗口中看到以下响应。

```
[DirectMethod] Invoking Direct Method [livePipelineDeactivate] to [avasample-iot-edge-device/avaedge] ...
[DirectMethod] Response from [avasample-iot-edge-device/avaedge]:
{
  "status": 200,
  "payload": null
}
```

状态代码 200 指示实时管道已成功停用。


### <a name="delete-the-live-pipeline"></a>删除实时管道

使用以下有效负载调用直接方法 `livePipelineDelete`

```
{
    "@apiVersion" : "1.0",
    "name&quot; : &quot;Sample-Pipeline-1"
}
```

几秒钟内，你应该可在“输出”窗口中看到以下响应：

```
[DirectMethod] Invoking Direct Method [livePipelineDelete] to [avasample-iot-edge-device/avaedge] ...
[DirectMethod] Response from [avasample-iot-edge-device/avaedge]:
{
  "status": 200,
  "payload": null
}
```

响应中的状态代码 200 指示实时管道已成功删除。


### <a name="delete-the-pipeline-topology"></a>删除管道拓扑

使用以下有效负载调用 `pipelineTopologyDelete` 直接方法：

```
{
    "@apiVersion" : "1.0",
    "name&quot; : &quot;EVRtoVideoSinkOnMotionDetection"
}
```

几秒钟内，你应该可在“输出”窗口中看到以下响应

```
[DirectMethod] Invoking Direct Method [pipelineTopologyDelete] to [avasample-iot-edge-device/avaedge] ...
[DirectMethod] Response from [avasample-iot-edge-device/avaedge]:
{
  "status": 200,
  "payload": null
}
```

状态代码 200 指示管道拓扑已成功删除。

## <a name="playing-back-the-recording"></a>播放录制内容

可通过登录到 Azure 门户并观看视频来检查实时管道创建的视频分析器视频资源。
1. 打开 Web 浏览器，并转到 [Azure 门户](https://portal.azure.com/)。 输入登录到门户所需的凭据。 默认视图是服务仪表板。
1. 在订阅资源中找到视频分析器帐户，并打开“帐户”窗格。
1. 在“视频分析器”列表中选择“视频” 。
1. 你会发现以名称 `sample-motion-video-camera001` 列出的视频。 这是在管道拓扑文件中选择的名称。
1. 选择视频。
1. 视频“详细信息”页随即将打开并自动开始播放。

    <!--TODO: add image -- ![Video playback]() TODO: new screenshot is needed here -->


[!INCLUDE [activate-deactivate-pipeline](./includes/common-includes/activate-deactivate-pipeline.md)]    

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [prerequisites](./includes/common-includes/clean-up-resources.md)]

## <a name="next-steps"></a>后续步骤

* 了解如何[播放录制的视频](playback-recordings-how-to.md)
* 试用[实时视频分析快速入门](analyze-live-video-use-your-model-http.md)
