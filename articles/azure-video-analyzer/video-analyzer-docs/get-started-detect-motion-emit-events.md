---
title: 开始使用 Azure 视频分析器 - Azure
description: 本快速入门将引导你完成开始使用 Azure 视频分析器的步骤。 它将 Azure VM 用作 IoT Edge 设备和模拟的实时视频流。
ms.service: azure-video-analyzer
ms.topic: quickstart
ms.date: 06/01/2021
ms.openlocfilehash: 001b1bd2e1f2f3350ad9e9cef28bb3ec682332ef
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748684"
---
# <a name="quickstart-get-started-with-azure-video-analyzer"></a>快速入门：开始使用 Azure 视频分析器

本快速入门将引导你完成开始使用 Azure 视频分析器的步骤。 它将 Azure VM 用作 IoT Edge 设备和模拟的实时视频流。

完成设置步骤后，你将能通过管道运行模拟实时视频流，该管道可检测和报告该流中的任何运动。 下图显示了该管道的图形表示形式。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/get-started-detect-motion-emit-events/motion-detection.svg" alt-text="检测运动":::

## <a name="prerequisites"></a>先决条件

* 具有活动订阅的 Azure 帐户。 如果没有帐户，可[免费创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

    [!INCLUDE [azure-subscription-permissions](./includes/common-includes/azure-subscription-permissions.md)]
* 包含以下扩展的 [Visual Studio Code](https://code.visualstudio.com/)：

    * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

[!INCLUDE [install-docker-prompt](./includes/common-includes/install-docker-prompt.md)]

## <a name="set-up-azure-resources"></a>设置 Azure 资源

[![部署到 Azure](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

部署过程大约需要 20 分钟。 完成部署后，Azure 订阅中会部署某些 Azure 资源，包括：
1. **视频分析器帐户** - 此 [云服务](overview.md)用于注册视频分析器边缘模块，并用于播放录制的视频和视频分析。
1. **存储帐户** - 用于存储录制的视频和视频分析。
1. **托管标识** - 这是用户分配的 [托管标识](../../active-directory/managed-identities-azure-resources/overview.md)，用于管理对上述存储帐户的访问。
1. **虚拟机** - 这是将用作模拟边缘设备的虚拟机。
1. **IoT 中心** - 这充当消息中心，用于在 IoT 应用程序、IoT Edge 模块以及它管理的设备之间进行双向通信。

<!-- TODO: provide a link to the readme.md in github.com/azure-video-analyzer/setup/readme.md where we can list out all resources like virtual network etc. -->

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

## <a name="use-direct-method-calls"></a>使用直接方法调用

现在，可调用视频分析器边缘模块公开的直接方法来分析实时视频流。 阅读[视频分析器直接方法](direct-methods.md)，检查模块提供的所有直接方法。 可在[此处](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/videoanalyzer/data-plane/VideoAnalyzer.Edge/preview/1.0.0/AzureVideoAnalyzerSdkDefinitions.json)找到直接方法的架构。

### <a name="enumerate-pipeline-topologies"></a>枚举管道拓扑

此步骤将枚举模块中的所有[管道拓扑](pipeline.md)。

1. 右键单击“avaedge”模块，然后从上下文菜单中选择“调用模块直接方法”。
1. 你将看到一个编辑框在 Visual Studio Code 窗口的顶部中间弹出。 在编辑框中输入 `pipelineTopologyList`，然后按 Enter。
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

使用与上面相同的步骤，可调用 `pipelineTopologySet` 来设置管道拓扑，使用以下 JSON 作为有效负载。 你将创建一个名为“MotionDetection”的管道拓扑。


```json
{
    "@apiVersion": "1.0",
    "name": "MotionDetection",
    "properties": {
        "description": "Analyzing live video to detect motion and emit events",
        "parameters": [
            {
                "name": "rtspUrl",
                "type": "string",
                "description&quot;: &quot;rtspUrl"
            },
            {
                "name": "rtspUserName",
                "type": "string",
                "description": "rtspUserName",
                "default&quot;: &quot;dummyUserName"
            },
            {
                "name": "rtspPassword",
                "type": "string",
                "description": "rtspPassword",
                "default&quot;: &quot;dummypw"
            }
        ],
        "sources": [
            {
                "@type": "#Microsoft.VideoAnalyzer.RtspSource",
                "name": "rtspSource",
                "transport": "tcp",
                "endpoint": {
                    "@type": "#Microsoft.VideoAnalyzer.UnsecuredEndpoint",
                    "credentials": {
                        "@type": "#Microsoft.VideoAnalyzer.UsernamePasswordCredentials",
                        "username": "${rtspUserName}",
                        "password&quot;: &quot;${rtspPassword}"
                    },
                    "url&quot;: &quot;${rtspUrl}"
                }
            }
        ],
        "processors": [
            {
                "@type": "#Microsoft.VideoAnalyzer.MotionDetectionProcessor",
                "sensitivity": "medium",
                "name": "motionDetection",
                "inputs": [
                    {
                        "nodeName": "rtspSource",
                        "outputSelectors": []
                    }
                ]
            }
        ],
        "sinks": [
            {
                "hubOutputName": "inferenceOutput",
                "@type": "#Microsoft.VideoAnalyzer.IotHubMessageSink",
                "name": "iotHubSink",
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

此 JSON 有效负载会创建一个定义三个参数的拓扑，其中两个参数具有默认值。 此拓扑具有一个源节点（[RTSP 源](pipeline.md#rtsp-source)）、一个处理器节点（[运动检测处理器](pipeline.md#motion-detection-processor)）和一个接收器节点（[IoT 中心消息接收器](pipeline.md#iot-hub-message-sink)）。 拓扑的直观呈现形式如下所示。

几秒钟内，“输出”窗口中显示以下响应。

```json
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2021-03-21T18:16:46.491Z",
      "lastModifiedAt": "2021-03-21T18:16:46.491Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
      "parameters": [
        {
          "name": "rtspPassword",
          "type": "string",
          "description": "rtspPassword",
          "default": "dummypw"
        },
        {
          "name": "rtspUrl",
          "type": "string",
          "description": "rtspUrl"
        },
        {
          "name": "rtspUserName",
          "type": "string",
          "description": "rtspUserName",
          "default": "dummyUserName"
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
              "password": "${rtspPassword}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.VideoAnalyzer.MotionDetectionProcessor",
          "sensitivity": "medium",
          "eventAggregationWindow": "PT1S",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.VideoAnalyzer.IotHubMessageSink",
          "hubOutputName": "inferenceOutput",
          "name": "iotHubSink",
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

返回的状态为 201。 此状态指示已创建了新拓扑。

请尝试以下后续步骤：

1. 再次调用 `pipelineTopologySet`。 返回的状态代码为 200。 此代码指示已成功更新现有拓扑。
1. 再次调用 `pipelineTopologySet`，但更改描述字符串。 返回的状态代码为 200，且描述已更新为新值。
1. 调用 `pipelineTopologyList`，如前一部分所述。 现在，可以在返回的有效负载中查看“MotionDetection”拓扑。

### <a name="read-the-pipeline-topology"></a>查看管道拓扑

使用以下有效负载调用 `pipelineTopologyGet`。

```json
{
    "@apiVersion" : "1.0",
    "name" : "MotionDetection"
}
```

几秒钟内，“输出”窗口中显示以下响应：

```json
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2021-03-21T18:16:46.491Z",
      "lastModifiedAt": "2021-03-21T18:16:46.491Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
      "parameters": [
        {
          "name": "rtspPassword",
          "type": "string",
          "description": "rtspPassword",
          "default": "dummypw"
        },
        {
          "name": "rtspUrl",
          "type": "string",
          "description": "rtspUrl"
        },
        {
          "name": "rtspUserName",
          "type": "string",
          "description": "rtspUserName",
          "default": "dummyUserName"
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
              "password": "${rtspPassword}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.VideoAnalyzer.MotionDetectionProcessor",
          "sensitivity": "medium",
          "eventAggregationWindow": "PT1S",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.VideoAnalyzer.IotHubMessageSink",
          "hubOutputName": "inferenceOutput",
          "name": "iotHubSink",
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

在响应有效负载中，请注意以下详细信息：

* 状态代码为 200，表示成功。
* 有效负载包含 `createdAt` 时间戳和 `lastModifiedAt` 时间戳。

### <a name="create-a-live-pipeline-using-the-topology"></a>使用拓扑创建实时管道

接下来，创建引用上述管道拓扑的实时管道。 使用以下有效负载调用 `livePipelineSet` 直接方法：

```json
{
    "@apiVersion" : "1.0",
    "name": "mdpipeline1",
    "properties": {
        "topologyName": "MotionDetection",
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

请注意，此有效负载：

* 上述有效负载指定实时管道将要使用的拓扑（“MotionDetection”）。
* 有效负载包含 `rtspUrl` 的参数值，该参数在拓扑有效负载中没有默认值。 此值是指向以下示例视频的链接：

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4LTY4]


几秒钟内，“输出”窗口中显示以下响应：

```json
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2021-03-21T18:27:41.639Z",
      "lastModifiedAt": "2021-03-21T18:27:41.639Z"
    },
    "name": "mdpipeline1",
    "properties": {
      "state": "Inactive",
      "description": "Sample pipeline description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspPassword",
          "value": "testpassword"
        },
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        },
        {
          "name": "rtspUserName",
          "value": "testuser"
        }
      ]
    }
  }
}
```

在响应有效负载中，请注意：

* 状态代码为 201，指示已创建新的实时管道。
* 状态为“非活动”，表示实时管道已创建但未激活。 有关详细信息，请参阅[管道状态](pipeline.md#pipeline-states)。

请尝试在后续步骤中使用以下直接方法：

* 使用相同的有效负载再次调用 `livePipelineSet`，并注意返回的状态代码现在是否为 200。
* 再次调用 `livePipelineSet`，但使用不同的说明，并注意响应有效负载中更新的说明，指示实时管道已成功更新。
* 调用 `livePipelineSet`，但将名称更改为“mdpipeline2”并将 `rtspUrl` 更改为“rtsp://rtspsim:554/media/lots_015.mkv”。 在响应有效负载中，请注意新创建的实时管道（即状态代码 201）。
    > [!NOTE]
    > 如[管道拓扑](pipeline.md#pipeline-topologies)中所述，可创建多个实时管道，使用同一管道拓扑分析来自多个相机的实时视频流。 如果创建了其他实时管道，请注意在清理步骤中将其删除。

### <a name="activate-the-live-pipeline"></a>激活实时管道

接下来，可激活实时管道，这样将通过管道启动（模拟的）实时视频流。 使用以下有效负载调用直接方法 `livePipelineActivate`：

```json
{
    "@apiVersion" : "1.0",
    "name" : "mdpipeline1"
}
```

几秒钟内，“输出”窗口中显示以下响应。

```json
{
  "status": 200,
  "payload": null
}
```

状态代码 200 指示实时管道已成功激活。

### <a name="check-the-state-of-the-live-pipeline"></a>检查实时管道的状态

现在，使用以下有效负载调用 `livePipelineGet` 直接方法：

```json
{
    "@apiVersion" : "1.0",
    "name" : "mdpipeline1"
}
```

几秒钟内，“输出”窗口中显示以下响应。

```json
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2021-03-21T18:27:41.639Z",
      "lastModifiedAt": "2021-03-21T18:27:41.639Z"
    },
    "name": "mdpipeline1",
    "properties": {
      "state": "Active",
      "description": "Sample pipeline description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspPassword",
          "value": "testpassword"
        },
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        },
        {
          "name": "rtspUserName",
          "value": "testuser"
        }
      ]
    }
  }
}
```

在响应有效负载中，请注意以下详细信息：

* 状态代码为 200，表示成功。
* 状态为“活动”，指示实时管道现在处于活动状态。

## <a name="observe-results"></a>观察结果

上面创建和激活的实时管道使用运动检测处理器节点来检测传入实时视频流中的运动，并将事件发送到 IoT 中心接收器。 这些事件随后以消息形式转送到 IoT 中心，现在可以观察到这一点。 可在“输出”窗口看到具有以下“正文”的消息：


```json
{
  "timestamp": 145471641211899,
  "inferences": [
    {
      "type": "motion",
      "motion": {
        "box": {
          "l": 0.514644,
          "t": 0.574627,
          "w": 0.3375,
          "h": 0.096296
        }
      }
    }
  ]
}
```

请注意此详细信息：

*   推理部分指示类型为运动。 其中提供了有关运动事件的其他数据，并为检测到运动的视频帧区域（在给定时间戳）提供边界框。

    
## <a name="invoke-additional-direct-method-calls-to-clean-up"></a>调用其他直接方法调用进行清理

接下来，你可调用直接方法来停用和删除实时管道（按该顺序执行操作）。

### <a name="deactivate-the-live-pipeline"></a>停用实时管道

使用以下有效负载调用 `livePipelineDeactivate` 直接方法：

```json
{
    "@apiVersion" : "1.0",
    "name" : "mdpipeline1"
}
```

几秒钟内，“输出”窗口中显示以下响应：

```json
{
  "status": 200,
  "payload": null
}
```

状态代码 200 指示实时管道已成功停用。 

接下来，尝试按本文前面所示来调用 `livePipelineGet`。 观察状态值。

### <a name="delete-the-live-pipeline"></a>删除实时管道

使用以下有效负载调用直接方法 `livePipelineDelete`

```json
{
    "@apiVersion" : "1.0",
    "name" : "mdpipeline1"
}
```

几秒钟内，“输出”窗口中显示以下响应：

```json
{
  "status": 200,
  "payload": null
}
```
状态代码 200 指示实时管道已成功删除。

如果还创建了名为“mdpipeline2”的管道，那么要删除管道拓扑，必须先删除此附加管道。 再次使用以下有效负载调用直接方法 `livePipelineDelete`：

```
{
    "@apiVersion" : "1.0",
    "name" : "mdpipeline2"
}
```

几秒钟内，“输出”窗口中显示以下响应：

```json
{
  "status": 200,
  "payload": null
}
```

状态代码 200 指示实时管道已成功删除。

可以使用与 `pipelineTopologyList` 相同的有效负载调用 `livePipelineList`。 观察到未枚举任何实时管道。

### <a name="delete-the-pipeline-topology"></a>删除管道拓扑

删除所有实时管道后，可以使用以下有效负载调用 `pipelineTopologyDelete` 直接方法：

```json
{
    "@apiVersion" : "1.0",
    "name" : "MotionDetection"
}
```

几秒钟内，“输出”窗口中显示以下响应。

```json
{
  "status": 200,
  "payload": null
}
```

状态代码 200 指示拓扑已成功删除。

可以尝试调用 `pipelineTopologyList`，并观察到模块不包含拓扑。

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [prerequisites](./includes/common-includes/clean-up-resources.md)]

## <a name="next-steps"></a>后续步骤

* 尝试使用[在检测到运动时将视频录制到云端快速入门](detect-motion-record-video-clips-cloud.md)
* 尝试使用[实时视频分析快速入门](analyze-live-video-use-your-model-http.md)
* 详细了解[诊断消息](monitor-log-edge.md) 
