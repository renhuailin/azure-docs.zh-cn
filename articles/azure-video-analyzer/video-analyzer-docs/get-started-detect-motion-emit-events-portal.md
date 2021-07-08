---
title: 通过 Azure 门户开始使用 Azure 视频分析器 - Azure
description: 本快速入门将引导你完成通过 Azure 门户开始使用 Azure 视频分析器的步骤。
ms.topic: quickstart
ms.date: 05/25/2021
ms.openlocfilehash: 47290a269686cd3727c009742c502bd9c57b579b
ms.sourcegitcommit: 3bb9f8cee51e3b9c711679b460ab7b7363a62e6b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2021
ms.locfileid: "112079528"
---
# <a name="quickstart-get-started-with-azure-video-analyzer"></a>快速入门：开始使用 Azure 视频分析器
本快速入门将引导你完成开始使用 Azure 视频分析器的步骤。 你将使用 Azure 门户创建一个 Azure 视频分析器帐户及其附带的资源。
创建视频分析器帐户后，将视频分析器边缘模块和 RTSP 摄像头模拟器模块部署到 IoT Edge 设备

完成设置步骤后，你将能通过管道运行模拟实时视频流，该管道可检测和报告该流中的任何运动。 下图显示了该管道的图形表示形式。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/get-started-detect-motion-emit-events/motion-detection.svg" alt-text="检测运动":::

## <a name="prerequisites"></a>先决条件

* 一个有效的 Azure 订阅。 如果没有帐户，请[创建一个免费帐户](https://azure.microsoft.com/free/)。  
[!INCLUDE [the video analyzer account and storage account must be in the same subscription and region](./includes/note-account-storage-same-subscription.md)]
* 一个 IoT Edge 设备，你在其上拥有管理员特权
   * [部署到 IoT Edge 设备](deploy-iot-edge-device.md)
   * [部署到 IoT Edge for Linux on Windows](deploy-iot-edge-linux-on-windows.md)
* 包含以下扩展的 [Visual Studio Code](https://code.visualstudio.com/)：

    * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

> [!TIP] 
> 安装 Azure IoT Tools 扩展时，系统可能会提示你安装 Docker。 可以忽略此提示。

## <a name="preparing-your-iot-edge-device"></a>准备 IoT Edge 设备
Azure 视频分析器模块应配置为使用非特权本地用户帐户在 IoT Edge 设备上运行。 该模块需要创建某些本地文件夹来存储应用程序配置数据。 RTSP 摄像头模拟器模块需要通过视频文件来合成实时视频源。

https://aka.ms/ava/prepare-device  
  
在 IoT Edge 设备上运行以下命令  
`bash -c "$(curl -sL https://aka.ms/ava-edge/prep_device)"`

上面使用的准备设备脚本会自动执行以下任务：创建输入文件夹和配置文件夹、下载视频输入文件以及创建具有正确特权的用户帐户。 成功完成该命令后，应会显示边缘设备上创建的以下文件夹。 

* `/home/localedgeuser/samples`
* `/home/localedgeuser/samples/input`
* `/var/lib/videoanalyzer`
* `/var/media`

    请注意，/home/localedgeuser/samples/input 文件夹中的视频文件（“*.mkv”）用于模拟实时视频。 
## <a name="creating-azure-resources"></a>创建 Azure 资源
下一步是创建所需的 Azure 资源（视频分析器帐户、存储帐户、用户分配的托管标识）、创建可选的容器注册表，并向视频分析器帐户注册视频分析器边缘模块

创建 Azure 视频分析器帐户时，必须将一个 Azure 存储帐户与其关联。 如果使用视频分析器录制某个摄像机上的实时视频，则该数据将以 Blob 形式存储在存储帐户的容器中。 必须使用托管标识向视频分析器帐户授予对存储帐户的适当访问权限，如下所示。


   [!INCLUDE [the video analyzer account and storage account must be in the same subscription and region](./includes/note-account-storage-same-subscription.md)]

### <a name="create-a-video-analyzer-account-in-the-azure-portal"></a>在 Azure 门户中创建视频分析器帐户

1. 登录到 [Azure 门户](https://portal.azure.com/)。
1. 在顶部的搜索栏中输入“视频分析器”。
1. 单击“服务”下的“视频分析器” 。
1. 单击“添加”。
1. 在“创建视频分析器帐户”部分中输入所需的值。
    - 订阅：选择要在其下创建视频分析器帐户的订阅。
    - 资源组：选择要在其中创建视频分析器帐户的资源组，或者单击“新建”以创建新的资源组 。
    - 视频分析器帐户名称：这是视频分析器帐户的名称。 该名称必须由全小写字母或数字构成（不含空格），长度为 3 到 24 个字符。
    - 位置：选择要部署视频分析器帐户的位置，例如“美国西部 2” 。
    - 存储帐户：创建新的存储帐户。 建议选择[标准常规用途 v2](../../storage/common/storage-account-overview.md#types-of-storage-accounts) 存储帐户。
    - 用户标识：创建新的用户分配的托管标识并为其命名。

1. 在窗体底部单击“查看 + 创建”。

### <a name="create-a-container-registry"></a>创建容器注册表
1. 选择“创建资源” > “容器” > “容器注册表”。  
1. 在“基本信息”选项卡中，输入“资源组”的值（使用前面部分中的同一资源组）和“注册表名称”的值   。 注册表名称在 Azure 中必须唯一，并且包含 5-50 个字母数字字符。
1. 对于剩余的设置，请接受默认值。 然后选择“查看 + 创建”。 查看设置后，选择“创建”。

## <a name="deploying-edge-modules"></a>部署边缘模块

### <a name="deploying-video-analyzer-edge-module"></a>部署视频分析器边缘模块

1. 导航到你的视频分析器帐户
1. 在“边缘”边栏选项卡下选择“边缘模块” 
1. 选择“添加边缘模块”，输入“avaedge”作为新边缘模块的名称，然后选择“添加” 
1. 屏幕右侧将显示“复制预配令牌”屏幕
1. 复制“推荐用于 IoT 模块部署的属性”下的代码片段，因为在稍后的步骤中需要用到它
    ```JSON
      {
          "applicationDataDirectory": "/var/lib/videoanalyzer",
          "ProvisioningToken": "XXXXXXX",
          "diagnosticsEventsOutputName": "diagnostics",
          "operationalEventsOutputName": "operational",
          "logLevel": "information",
          "LogCategories": "Application,Events",
          "allowUnsecuredEndpoints": true,
          "telemetryOptOut": false
      }
    ```
1. 导航到你的 IoT 中心
1. 在“自动设备管理”下选择“IoT Edge” 
1. 选择你的 IoT Edge 设备的设备 ID
1. 选择“设置模块”
1. 选择“添加”，然后从下拉菜单中选择“IoT Edge 模块” 
1. 在“IoT Edge 模块名称”中输入“avaedge” 
1. 将以下行复制并粘贴到“映像 URI”字段中：`mcr.microsoft.com/media/video-analyzer:1`
1. 选择“环境变量” 
1. 在“名称”下输入“LOCAL_USER_ID”，在“值”下输入“1010”   
1. 在“名称”下的第二行中输入“LOCAL_GROUP_ID”，在“值”下输入“1010”   
1. 选择“容器创建选项”，然后复制并粘贴以下行：  
    ```json
            {
                "HostConfig": {
                    "LogConfig": {
                        "Type": "",
                        "Config": {
                            "max-size": "10m",
                            "max-file": "10"
                        }
                    },
                    "Binds": [
                        "/var/media/:/var/media/",
                        "/var/lib/videoanalyzer/:/var/lib/videoanalyzer"
                    ],
                    "IpcMode": "host",
                    "ShmSize": 1536870912
                }
            }
    ```
1. 选择“模块孪生设置”，然后粘贴前面从视频分析器帐户中的“复制预配令牌”页复制的代码片段 
    ```JSON
      {
          "applicationDataDirectory": "/var/lib/videoanalyzer",
          "ProvisioningToken": "XXXXXXX",
          "diagnosticsEventsOutputName": "diagnostics",
          "operationalEventsOutputName": "operational",
          "logLevel": "information",
          "LogCategories": "Application,Events",
          "allowUnsecuredEndpoints": true,
          "telemetryOptOut": false
      }
    ```
1. 选择屏幕底部的“添加”
1. 选择“路由”
1. 在“名称”下输入“AVAToHub”，在“值”下输入“FROM /messages/modules/avaedge/outputs/* INTO $upstream”  
1. 选择“查看 + 创建”，然后选择“创建”，随即会部署 avaedge 边缘模块  

### <a name="deploying-rtsp-camera-simulator-edge-module"></a>部署 RTSP 摄像头模拟器边缘模块
1. 导航到你的 IoT 中心
1. 在“自动设备管理”下选择“IoT Edge” 
1. 选择你的 IoT Edge 设备的设备 ID
1. 选择“设置模块”
1. 选择“添加”，然后从下拉菜单中选择“IoT Edge 模块” 
1. 在“IoT Edge 模块名称”中输入“rtspsim” 
1. 将以下行复制并粘贴到“映像 URI”字段中：`mcr.microsoft.com/lva-utilities/rtspsim-live555:1.2`
1. 选择“容器创建选项”，然后复制并粘贴以下行：  
    ```json
            {
                "HostConfig": {
                    "Binds": [
                         "/home/localedgeuser/samples/input:/live/mediaServer/media"
                    ]
                }
            }
    ```
1. 选择屏幕底部的“添加”
1. 选择“查看 + 创建”，然后选择“创建”，随即会部署 rtspsim 边缘模块  

### <a name="verify-your-deployment"></a>验证部署

在设备详细信息页上，验证 avaedge 和 rtspsim 模块是否已列为“已在部署中指定”和“已由设备报告”   。  

可能需要等待一段时间，模块才会在设备上启动并向 IoT 中心发回报告。 刷新页面以查看更新的状态。
状态代码：200 -OK 表示 [IoT Edge 运行时](../../iot-edge/iot-edge-runtime.md)状况良好并且运行正常。  

![屏幕截图显示了 IoT Edge 运行时的状态值。](./media/deploy-iot-edge-device/status.png)


## <a name="set-up-your-development-environment"></a>设置开发环境

### <a name="obtain-your-iot-hub-connection-string"></a>获取 IoT 中心连接字符串

1. 在 Azure 门户中，导航到你的
1. 在左侧导航栏中找到“共享访问策略”选项，然后单击此处。
1. 单击名为“iothubowner”的策略
1. 复制“主连接字符串”，它类似于 `HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX`

### <a name="connect-to-the-iot-hub"></a>连接到 IoT 中心

1. 打开 Visual Studio Code，选择“视图” > “资源管理器” 。 或选择 Ctrl+Shift+E。
1. 在“资源管理器”选项卡的左下角，选择“Azure IoT 中心”。
1. 选择“更多选项”图标以查看上下文菜单。 然后选择“设置 IoT 中心连接字符串”。
1. 输入框出现时，在其中输入 IoT 中心连接字符串。
1. 大约 30 秒后，在左下部分刷新 Azure IoT 中心。 应会看到你的设备 ID，它已部署了以下模块：
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
1. 右键单击你的设备 ID，然后选择“开始监视内置事件终结点” 。

    > [!NOTE]
    > 系统可能会要求你提供 IoT 中心的内置终结点信息。 若要获取此信息，请在 Azure 门户中导航到 IoT 中心，然后在左侧导航窗格中查找“内置终结点”选项。 单击此处，在“与事件中心兼容的终结点”部分下查找“与事件中心兼容的终结点” 。 复制并使用框中的文本。 终结点将如下所示：  
        ```
        Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
        ```

## <a name="use-direct-method-calls"></a>使用直接方法调用

现在，可调用视频分析器边缘模块公开的直接方法来分析实时视频流。 阅读[视频分析器直接方法](direct-methods.md)，检查模块提供的所有直接方法。 

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
[DirectMethod] Invoking Direct Method [pipelineTopologyList] to [deviceId/avaedge] ...
[DirectMethod] Response from [deviceId/avaedge]:
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
