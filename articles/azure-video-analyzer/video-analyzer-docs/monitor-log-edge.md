---
title: 监视和日志记录 - Azure
description: 本文概述 Azure 视频分析器中的监视和日志记录。
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: 7938a68272378cf592fff17be0c4dfef2ca0e3f3
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114604870"
---
# <a name="monitor-and-log-on-iot-edge"></a>IoT Edge 上的监视和日志

本文介绍如何从 Azure 视频分析器 IoT Edge 模块接收事件以进行远程监视。 

此外还介绍如何控制模块生成的日志。

## <a name="taxonomy-of-events"></a>事件的分类

基于 IoT Edge 构建的视频分析器根据以下分类发出事件或遥测数据：

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/telemetry-schema/taxonomy.png" alt-text="显示事件分类的关系图。":::

* 操作：由用户操作生成的或者在执行[管道](pipeline.md)期间生成的事件
  
   * 数量：应较低（每分钟几次，甚至更低）
   * 示例:

      - 实时管道已激活
      
      - 实时管道已停用
      
      示例操作事件
      
      ```json
      {
         "body": {
             "outputType": "filePath",
             "outputLocation": "/var/media/Sample-1-fileSink/sampleFilesFromEVR-motion-fileSinkOutput-20210426T181911Z.mp4"
           },
           "properties": {
             "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/Microsoft.Media/videoAnalyzers/<account-name>",
             "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sinks/fileSink",
             "eventType": "Microsoft.VideoAnalyzer.Operational.RecordingStarted",
             "eventTime": "2021-04-26T18:19:13.298Z",
             "dataVersion": "1.0"
          },
      }
      ```
* 诊断：有助于诊断性能问题的事件

   * 数量：可能很高（每分钟几次）
   * 示例:
   
      - RTSP [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) 信息（如以下示例所示）       
      - 与摄像头或 AI 扩展等组件连接失败时的错误
      
    示例诊断事件
  
    ```json
    {
      "body": {
        "sdp": "SDP:\nv=0\r\no=- 1589326384077235 1 IN IP4 XXX.XX.XX.XXX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-73.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=640028;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
      },
      "applicationProperties": {
        "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/Microsoft.Media/videoAnalyzers/<account-name>",
        "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sources/rtspSource",
        "eventType": "Microsoft.VideoAnalyzer.Diagnostics.MediaSessionEstablished",
        "eventTime": "2021-04-26T18:15:13.298Z",
        "dataVersion": "1.0"
      }
    }
    ```
* 分析：由于进行视频分析而生成的事件

   * 数量：可能很高（每分钟几次或更高）
   * 示例:
     
      - 检测到动作（如以下示例所示） 
      
      - 推理结果
     
      示例分析事件
        
      ```json
      {
        "body": {
          "timestamp": 143039375044290,
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
        "applicationProperties": {
          "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/Microsoft.Media/videoAnalyzers/<account-name>",
          "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/processors/md",
          "eventType": "Microsoft.VideoAnalyzer.Analytics.Inference",
          "eventTime": "2021-04-26T18:15:13.298Z",
          "dataVersion": "1.0"
        }
      }
      ```
        
模块发出的事件会发送到 [IoT Edge 中心](../../iot-edge/iot-edge-runtime.md#iot-edge-hub)， 可从此位置将这些事件路由到其他目标。 

### <a name="events-and-video-playback"></a>事件和视频播放

如上所述，作为视频分析的一部分生成的事件具有与其相关联的 `eventTime`。 如果[录制了实时视频](video-recording.md)作为管道拓扑的一部分，则这些时间戳有助于你查找录制的视频中发生特定事件的位置。 可以在[视频分析器播放器小组件](player-widget.md)中加载视频录制内容，并使用其控件查找到所需的日期和时间。 如果管道涉及到使用 AI 模型生成推理结果，则你应该连同视频一起录制推理数据。 这样，便可以按照[此教程](record-stream-inference-data-with-video.md)中所述，连同视频一起播放推理元数据。


## <a name="routing-events"></a>路由事件

可使用以下模块孪生属性来路由视频分析器模块发布的操作和诊断事件。 [模块孪生 JSON 架构](module-twin-configuration-schema.md)中介绍了这些属性。

- `diagnosticsEventsOutputName`：若要从模块中获取诊断事件，请包括此属性并为它提供任意值。 忽略它或将它留空可阻止模块发布诊断事件。
  
- `operationalEventsOutputName`：若要从模块中获取操作事件，请包括此属性并为它提供任意值。 忽略它或将它留空可阻止模块发布操作事件。

诊断和操作事件由运动检测处理器或扩展处理器等节点自动发出。 分析事件必须在管道中路由到 IoT 中心消息接收器，才能发送到 IoT Edge 中心。 

可以通过使用部署清单中 `$edgeHub` 模块孪生的 `desired` 属性控制[以上所有事件的路由](../../iot-edge/module-composition.md#declare-routes)：

```json
 "$edgeHub": {
   "properties.desired": {
     "schemaVersion": "1.0",
     "routes": {
       "moduleToHub": "FROM /messages/modules/avaedge/outputs/* INTO $upstream"
     },
     "storeAndForwardConfiguration": {
       "timeToLiveSecs": 7200
     }
   }
 }
```

在上面的 JSON 中，`avaedge` 是视频分析器模块的名称。 路由规则遵循[声明路由](../../iot-edge/module-composition.md#declare-routes)中定义的架构。

> [!NOTE]
> 为了确保分析事件到达 IoT Edge 中心，在任何运动检测处理器节点和/或任何扩展处理器节点的下游必须有一个 IoT 中心消息接收器节点。

## <a name="event-schema"></a>事件架构

可以在边缘或云中使用源自边缘设备的事件。 视频分析器生成的事件遵循由 Azure IoT 中心建立的[流式处理消息传送模式](../../iot-hub/iot-hub-devguide-messages-construct.md)。 此模式包含系统属性、应用程序属性和正文。

### <a name="summary"></a>总结

每个事件（在通过 IoT 中心观察到时）都有一组共同属性：

| properties      | 属性类型       | 数据类型 | 说明                                                  |
| ------------- | ------------------- | --------- | ------------------------------------------------------------ |
| `message-id`  | system              | GUID      | 唯一的事件 ID。                                             |
| `topic`       | applicationProperty | string    | Azure 视频分析器帐户的 Azure 资源管理器路径。 |
| `subject`     | applicationProperty | string    | 发出事件的实体的子路径。                    |
| `eventTime`   | applicationProperty | string    | 生成事件的时间。                                |
| `eventType`   | applicationProperty | string    | 事件类型标识符。 （请参阅以下部分。）          |
| `body`        | body                | object    | 特定事件数据。                                       |
| `dataVersion` | applicationProperty | string    | {Major}.{Minor}                                              |

### <a name="properties"></a>属性

#### <a name="message-id"></a>message-id

事件的全局唯一标识符 (GUID)。

#### <a name="topic"></a>主题

表示生成事件的视频分析器边缘模块。 示例：

`/subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.Media/videoAnalyzers/{accountName}`

#### <a name="subject"></a>subject

发出事件的实体。 示例:

`/edgeModules/avaedge/livePipelines/{livePipelineName}`<br/>
`/edgeModules/avaedge/livePipelines/{livePipelineName}/sources/{nodeName}`<br/>
`/edgeModules/avaedge/livePipelines/{livePipelineName}/processors/{nodeName}`<br/>
`/edgeModules/avaedge/livePipelines/{livePipelineName}/sinks/{nodeName}`

使用 `subject` 属性可将泛型事件映射到实时管道中发出事件的节点。 例如，对于无效 RTSP 用户名或密码，生成的事件将是 `/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sources/rtspSource` 节点发出的 `Microsoft.VideoAnalyzer.Diagnostics.ProtocolError`。

#### <a name="eventtype"></a>eventType

事件类型根据以下架构分配给命名空间：

`Microsoft.VideoAnalyzer.{EventClass}.{EventType}`

事件类可以是下列项之一：

| 类名  | 说明                                                  |
| ----------- | ------------------------------------------------------------ |
| 分析   | 在内容分析过程中生成的事件。                |
| 诊断 | 有助于对问题和性能进行诊断的事件。 |
| 可运行 | 在资源操作过程中生成的事件。              |

示例:

* `Microsoft.VideoAnalyzer.Analytics.Inference`
* `Microsoft.VideoAnalyzer.Diagnostics.AuthorizationError`
* `Microsoft.VideoAnalyzer.Operational.RecordingStarted`

#### <a name="eventtime"></a>EventTime

事件时间采用 ISO 8601 字符串格式。 它表示事件的发生时间。

示例：

`2021-04-26T18:15:13.298Z`

## <a name="metrics"></a>指标

将通过模块端口 9600 上运行的 [Prometheus 终结点](https://prometheus.io/docs/instrumenting/exposition_formats/#text-based-format)从视频分析器模块报告这些指标。

| 指标名称                           | 类型    | 标签                                                                              | 说明                                                  |
| ------------------------------------- | ------- | ----------------------------------------------------------------------------------- | ------------------------------------------------------------ |
| va_active_live_pipelines              | 仪表   | iothub、edge_device、module_name、pipeline_topology                                 | 每个拓扑的活动实时管道总数。          |
| va_cumulative_latency_seconds_average | 仪表   | iothub、edge_device、module_name、pipeline_topology、live_pipeline、node            | 在过去一分钟，从某个视频帧由源接收，到该视频帧离开节点所发生的平均延迟。 如果没有帧，则不报告此延迟。 |
| va_cumulative_latency_seconds_max     | 仪表   | iothub、edge_device、module_name、pipeline_topology、live_pipeline、node            | 在过去一分钟，从某个视频帧由源接收，到该视频帧离开节点所发生的最大延迟。 如果没有帧，则不报告此延迟。 |
| va_data_dropped_total                 | 计数器 | iothub、edge_device、module_name、pipeline_topology、live_pipeline、node、data_kind | 任何已删除数据（事件、媒体等）的计数器。      |
| va_received_bytes_total               | 计数器 | iothub、edge_device、module_name、pipeline_topology、live_pipeline、node            | 一个节点接收的字节总数。 仅支持用于 RTSP 源。 |


> [!NOTE]
> 如果将视频分析器模块命名为 `avaedge`，则可以通过向 `http://avaedge:9600/metrics` 发送 GET 请求来访问指标。 根据具体的部署，可能需要将此端口映射到设备才能使此端口可供访问。

### <a name="azure-monitor-collection-via-telegraf"></a>通过 Telegraf 进行 Azure Monitor 收集 

请遵循以下步骤来启用从视频分析器模块收集指标。 指标将由 [Telegraf](https://github.com/influxdata/telegraf) 收集，然后作为[自定义指标](../../azure-monitor/essentials/metrics-custom-overview.md)上传到 [Azure Monitor](../../azure-monitor/overview.md)。 在 Telegraf 中，日志将由 [Prometheus](https://github.com/influxdata/telegraf/tree/master/plugins/inputs/prometheus) 插件抓取，并由 [azure_monitor](https://github.com/influxdata/telegraf/tree/master/plugins/outputs/azure_monitor) 插件上传。

可以使用代理从公开 Prometheus 终结点的任何模块收集指标。

1. 在开发计算机上创建一个文件夹，并转到该文件夹。
1. 在该文件夹中创建一个包含以下配置的 `telegraf.toml` 文件：
    ```toml
    [agent]
        interval = "30s"
        omit_hostname = true

    [[inputs.prometheus]]
      metric_version = 2
      urls = ["http://edgeHub:9600/metrics", "http://edgeAgent:9600/metrics", "http://{AVA_EDGE_MODULE_NAME}:9600/metrics"]

    [[outputs.azure_monitor]]
      namespace_prefix = "{AVA_EDGE_MODULE_NAME}"
      region = "westus2"
      resource_id = "/subscriptions/{SUBSCRIPTON_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.Devices/IotHubs/{IOT_HUB_NAME}"
    ```
    > [!IMPORTANT]
    > 请务必替换 .toml 文件中的变量。 变量由大括号 (`{}`) 表示。 此外，请更新 `region` 的值。
1. 在同一文件夹中创建包含以下命令的 Dockerfile：
    ```docker
    FROM telegraf:1.15.3-alpine
    COPY telegraf.toml /etc/telegraf/telegraf.conf
    ```
1. 使用 Docker CLI 命令生成 Docker 文件，并将该映像发布到 Azure 容器注册表。
   
   若要详细了解如何使用 Docker CLI 将映像推送到容器注册表，请参阅[推送和拉取 Docker 映像](../../container-registry/container-registry-get-started-docker-cli.md)。 有关 Azure 容器注册表的信息，请参阅[文档](../../container-registry/index.yml)。

   ```bash
   # Insert your own container image URL
   docker build . -t myregistry.azurecr.io/telegraf_agent
   docker push myregistry.azurecr.io/telegraf_agent
   ```

1. 推送到 Azure 容器注册表以后，请将以下节点添加到部署清单文件：
    ```json
    "telegraf": 
    {
      "settings": 
        {
            "image": "{AZURE_CONTAINER_REGISTRY_URL_OF_YOUR_TELEGRAF_IMAGE}"
        },
        "type": "docker",
        "version": "1.0",
        "status": "running",
        "restartPolicy": "always",
        "env": 
        {
            "AZURE_TENANT_ID": { "value": "{YOUR_TENANT_ID}" },
            "AZURE_CLIENT_ID": { "value": "{YOUR CLIENT_ID}" },
            "AZURE_CLIENT_SECRET": { "value": "{YOUR_CLIENT_SECRET}" }
        }
    }
    ```
    > [!IMPORTANT]
    > 请务必替换清单文件中的变量。 变量由大括号 (`{}`) 表示。


   Azure Monitor 可[通过服务主体进行身份验证](https://github.com/influxdata/telegraf/blob/master/plugins/outputs/azure_monitor/README.md#azure-authentication)。
        
   Azure Monitor Telegraf 插件公开了[多种身份验证方法](https://github.com/influxdata/telegraf/blob/master/plugins/outputs/azure_monitor/README.md#azure-authentication)。 
  1. 若要使用服务主体身份验证，请设置以下环境变量：  
     `AZURE_TENANT_ID`：指定要向其进行身份验证的租户。  
     `AZURE_CLIENT_ID`：指定要使用的应用客户端 ID。  
     `AZURE_CLIENT_SECRET`：指定要使用的应用机密。  
     
     >[!TIP]
     >可为服务主体提供“监视指标发布者”角色。 按照[创建服务主体](../../azure-arc/data/upload-metrics-and-logs-to-azure-monitor.md?pivots=client-operating-system-macos-and-linux#create-service-principal)中的步骤创建服务主体并分配角色。
1. 部署模块后，指标会显示在 Azure Monitor 中的单个命名空间下。 指标名称将与 Prometheus 发出的名称匹配。 

   这种情况下，请在 Azure 门户中转到 IoT 中心，并在左窗格中选择“指标”。 你应会在那里看到指标。 

### <a name="log-analytics-metrics-collection"></a>Log Analytics 指标集合

使用 [Prometheus 终结点](https://prometheus.io/docs/practices/naming/)和 [Log Analytics](../../azure-monitor/logs/log-analytics-tutorial.md)，可以生成和[监视指标](../../azure-monitor/essentials/metrics-supported.md)，例如使用的 CPUPercent、MemoryUsedPercent 等。   

> [!NOTE]
> 下面的配置不会收集日志，只收集指标。 可以扩展收集器模块来同时收集和上传日志。

[![显示如何使用 Log Analytics 收集指标的示意图。](./media/telemetry-schema/log-analytics.svg)](./media/telemetry-schema/log-analytics.svg#lightbox)

1. 了解如何[收集指标](https://github.com/Azure/iotedge/tree/master/edge-modules/MetricsCollector)
1. 使用 Docker CLI 命令生成 [Docker 文件](https://github.com/Azure/iotedge/tree/master/edge-modules/MetricsCollector/docker/linux)，并将该映像发布到 Azure 容器注册表。
   
   若要详细了解如何使用 Docker CLI 将映像推送到容器注册表，请参阅[推送和拉取 Docker 映像](../../container-registry/container-registry-get-started-docker-cli.md)。 有关 Azure 容器注册表的其他信息，请参阅[文档](../../container-registry/index.yml)。
1. 推送到 Azure 容器注册表以后，以下内容将插入到部署清单文件中：
    ```json
    "azmAgent": {
      "settings": {
        "image": "{AZURE_CONTAINER_REGISTRY_URL_OF_YOUR_METRICS_COLLECTOR}"
      },
      "type": "docker",
      "version": "1.0",
      "status": "running",
      "restartPolicy": "always",
      "env": {
        "LogAnalyticsWorkspaceId": { "value": "{YOUR_LOG_ANALYTICS_WORKSPACE_ID}" },
        "LogAnalyticsSharedKey": { "value": "{YOUR_LOG_ANALYTICS_WORKSPACE_SECRET}" },
        "LogAnalyticsLogType": { "value": "IoTEdgeMetrics" },
        "MetricsEndpointsCSV": { "value": "http://edgeHub:9600/metrics,http://edgeAgent:9600/metrics,http://avaedge:9600/metrics" },
        "ScrapeFrequencyInSecs": { "value": "30 " },
        "UploadTarget": { "value": "AzureLogAnalytics" }
      }
    }
    ```
    > [!NOTE]
    > 模块 `edgeHub`、`edgeAgent` 和 `avaedge` 是部署清单文件中定义的模块的名称。 请确保模块的名称匹配。   

    可以通过以下步骤获取 `LogAnalyticsWorkspaceId` 和 `LogAnalyticsSharedKey` 值：
    1. 转到 Azure 门户
    1. 查找 Log Analytics 工作区
    1. 找到 Log Analytics 工作区后，导航到左侧导航窗格中的 `Agents management` 选项。
    1. 你将找到可使用的工作区 ID 和密钥。

1. 接下来，通过单击左侧导航窗格中的 `Workbooks` 选项卡来创建工作簿。
1. 使用 Kusto 查询语言可以编写如下查询，并获得 IoT Edge 模块使用的 CPU 百分比。
    ```kusto
    let cpu_metrics = IoTEdgeMetrics_CL
    | where Name_s == "edgeAgent_used_cpu_percent"
    | extend dimensions = parse_json(Tags_s)
    | extend module_name = tostring(dimensions.module_name)
    | where module_name in ("avaedge","yolov3","tinyyolov3")
    | summarize cpu_percent = avg(Value_d) by bin(TimeGenerated, 5s), module_name;
    cpu_metrics
    | summarize cpu_percent = sum(cpu_percent) by TimeGenerated
    | extend module_name = "Total"
    | union cpu_metrics
    ```

    [ ![示意图显示使用 Kusto 查询的指标。](./media/telemetry-schema/metrics.png)](./media/telemetry-schema/metrics.png#lightbox)
## <a name="logging"></a>日志记录

与其他 IoT Edge 模块一样，你也可以[检查边缘设备上的容器日志](../../iot-edge/troubleshoot.md#check-container-logs-for-issues)。 可以通过使用[以下模块孪生](module-twin-configuration-schema.md)属性来配置写入到日志中的信息：

* `logLevel`

   * 允许使用的值为 `Verbose`、`Information`、`Warning`、`Error` 和 `None`。
   * 默认值为 `Information`。 日志将包含错误消息、警告消息和信息性消息。
   * 如果将值设置为“`Warning`”，则日志将包含错误消息和警告消息。
   * 如果将值设置为“`Error`”，则日志将仅包含错误消息。
   * 如果将值设置为“`None`”，则不会生成任何日志。 不建议这样做。
   * 只有在需要与 Azure 支持共享日志以诊断问题时，才使用“`Verbose`”。

* `logCategories`

   * 以逗号分隔的以下一个或多个值的列表：`Application`、`Events`、`MediaPipeline`。
   * 默认值为 `Application, Events`。
   * `Application`：来自模块的概要信息，例如模块启动消息、环境错误和直接方法调用。
   * `Events`：本文前面介绍的所有事件。
   * `MediaPipeline`：低级别的日志，可在你排查问题（例如，与支持 RTSP 的相机建立连接时遇到困难）时提供见解。
   
### <a name="generating-debug-logs"></a>生成调试日志
在某些情况下，可能需要生成比上述日志更详细的日志才能帮助 Azure 支持解决问题。 若要生成这些日志，请执行以下操作：  

1. 登录到 [Azure 门户](https://portal.azure.com)，并转到 IoT 中心。
1. 在左窗格中，选择“IoT Edge”。
1. 在设备列表中，选择目标设备的 ID。
1. 在窗格顶部，选择“设置模块”。

   ![Azure 门户中“设置模块”按钮的屏幕截图。](media/troubleshoot/set-modules.png)

1. 在“IoT Edge 模块”部分中，查找并选择“avaedge”。 
1. 选择“模块标识孪生体”。 此时将打开一个可编辑窗格。
1. 在“所需的密钥”下，添加以下键/值对：

   `"DebugLogsDirectory": "/var/lib/videoanalyzer/logs"`

   > [!NOTE]
   > 此命令将绑定边缘设备和容器之间的日志文件夹。 如果要在设备上的其他位置收集日志：
   >
   > 1. 在“绑定”部分中，为调试日志位置创建一个绑定，将 $DEBUG_LOG_LOCATION_ON_EDGE_DEVICE 和 $DEBUG_LOG_LOCATION 替换为想要使用的位置：`/var/$DEBUG_LOG_LOCATION_ON_EDGE_DEVICE:/var/$DEBUG_LOG_LOCATION`  
   > 2. 使用以下命令，将 $DEBUG_LOG_LOCATION 替换为上一步中使用的位置：`"DebugLogsDirectory": "/var/$DEBUG_LOG_LOCATION"`

1. 选择“保存”

现在，该模块会以二进制格式将调试日志写入到设备存储路径 `/var/local/videoAnalyzer/debuglogs/`。 你可以与 Azure 支持共享这些日志。  

可以通过将“模块标识孪生”中的值设置为 NULL 来停止日志收集。 返回“模块标识孪生”页，并将以下参数更新为：

   `"DebugLogsDirectory": ""`

## <a name="faq"></a>常见问题解答

如有问题，请参阅[监视和指标常见问题解答](faq-edge.yml#monitoring-and-metrics)。

## <a name="next-steps"></a>后续步骤

[排查 Azure 视频分析器的问题](troubleshoot.md)
