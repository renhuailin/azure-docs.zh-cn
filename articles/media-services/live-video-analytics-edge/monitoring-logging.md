---
title: 实时视频分析中的监视和日志记录 - Azure
description: 本文概述了 IoT Edge 上的 Azure 实时视频分析中的监视和日志记录。
ms.topic: reference
ms.date: 04/27/2020
ms.openlocfilehash: 0eb6f63b139a9a0ca9fc65c80dd6284d1a476f6d
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114297083"
---
# <a name="monitoring-and-logging-in-live-video-analytics-on-iot-edge"></a>IoT Edge 上的实时视频分析中的监视和日志记录

[!INCLUDE [redirect to Azure Video Analyzer](./includes/redirect-video-analyzer.md)]

本文介绍如何从 IoT Edge 模块上的实时视频分析接收事件以进行远程监视。 

此外还介绍如何控制模块生成的日志。

## <a name="taxonomy-of-events"></a>事件的分类

IoT Edge 上的实时视频分析根据以下分类发出事件或遥测数据：

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/telemetry-schema/taxonomy.png" alt-text="显示事件分类的关系图。":::

* 操作：通过用户操作生成的事件，或在执行[媒体图](media-graph-concept.md)期间生成的事件
   
   * 数量：应较低（每分钟几次，甚至更低）
   * 示例:

      - 记录已开始（如以下示例所示）
      - 记录已停止
      
      ```
      {
        "body": {
          "outputType": "assetName",
          "outputLocation": "sampleAssetFromEVR-LVAEdge-20200512T233309Z"
        },
        "applicationProperties": {
          "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
          "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
          "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
          "eventTime": "2020-05-12T23:33:10.392Z",
          "dataVersion": "1.0"
        }
      }
      ```
* 诊断：有助于诊断性能问题的事件

   * 数量：可能很高（每分钟几次）
   * 示例:
   
      - RTSP [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) 信息（如以下示例所示） 
      - 传入视频源中的间隔

      ```
      {
        "body": {
          "sdp": "SDP:\nv=0\r\no=- 1589326384077235 1 IN IP4 XXX.XX.XX.XXX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-73.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=640028;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
        },
        "applicationProperties": {
          "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
          "subject": "/graphInstances/Sample-Graph-2/sources/rtspSource",
          "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
          "eventTime": "2020-05-12T23:33:04.077Z",
          "dataVersion": "1.0"
        }
      }
      ```
* 分析：在视频分析过程中生成的事件

   * 数量：可能很高（每分钟几次或更高）
   * 示例:
      
      - 检测到动作（如以下示例所示） 
      - 推理结果

   ```      
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
       "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
       "subject": "/graphInstances/Sample-Graph-2/processors/md",
       "eventType": "Microsoft.Media.Graph.Analytics.Inference",
       "eventTime": "2020-05-12T23:33:09.381Z",
       "dataVersion": "1.0"
     }
   }
   ```

模块发出的事件会发送到 [IoT Edge 中心](../../iot-edge/iot-edge-runtime.md#iot-edge-hub)， 然后可以从那里路由到其他目标。 

### <a name="timestamps-in-analytic-events"></a>分析事件中的时间戳

如上所示，在视频分析过程中生成的事件具有关联的时间戳。 如果[将实时视频录制](video-recording-concept.md)为图拓扑的一部分，则这些时间戳有助于你定位录制的视频中发生特定事件的位置。 以下是有关如何将分析事件中的时间戳映射到已录制成 [Azure 媒体服务资产](terminology.md#asset)的视频的时间线的指导原则。

首先，提取 `eventTime` 值。 在[时间范围筛选器](playback-recordings-how-to.md#time-range-filters)中使用此值检索录制的适当部分。 例如，你可能希望检索在 `eventTime` 之前 30 秒开始并在其之后 30 秒结束的视频。 对于上一示例（其中的 `eventTime` 为 2020-05-12T23:33:09.381Z），对 `eventTime` 之前和之后 30 秒的 HLS 清单的请求与以下请求类似：

```
https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2020-05-12T23:32:39Z,endTime=2020-05-12T23:33:39Z).m3u8
```

上面的 URL 会返回一个[主播放列表](https://developer.apple.com/documentation/http_live_streaming/example_playlists_for_http_live_streaming)，其中包含媒体播放列表的 URL。 媒体播放列表会包含如下所示的条目：

```
...
#EXTINF:3.103011,no-desc
Fragments(video=143039375031270,format=m3u8-aapl)
...
```
上面的条目报告有一个从 `timestamp` 值 `143039375031270` 开始的视频片段。 分析事件中的 `timestamp` 值使用与媒体播放列表相同的时间刻度， 可用于标识相关的视频片段，以及查找正确的帧。

有关详细信息，请参阅[有关在 HLS 中进行精确到帧的查找的文章](https://www.bing.com/search?q=frame+accurate+seeking+in+HLS)。

## <a name="controlling-events"></a>控制事件

你可以使用以下模块孪生属性，控制由 IoT Edge 模块上的实时视频分析发布的操作和诊断事件。 [模块孪生 JSON 架构](module-twin-configuration-schema.md)中介绍了这些属性。

- `diagnosticsEventsOutputName`：若要从模块中获取诊断事件，请包括此属性并为它提供任意值。 忽略它或将它留空可阻止模块发布诊断事件。
   
- `operationalEventsOutputName`：若要从模块中获取操作事件，请包括此属性并为它提供任意值。 忽略它或将它留空可阻止模块发布操作事件。
   
分析事件由动作检测处理器或 HTTP 扩展处理器等节点生成。 IoT 中心接收器用于将它们发送到 IoT Edge 中心。 

可以通过使用部署清单中 `$edgeHub` 模块孪生的 `desired` 属性控制[以上所有事件的路由](../../iot-edge/module-composition.md#declare-routes)：

```
 "$edgeHub": {
   "properties.desired": {
     "schemaVersion": "1.0",
     "routes": {
       "moduleToHub": "FROM /messages/modules/lvaEdge/outputs/* INTO $upstream"
     },
     "storeAndForwardConfiguration": {
       "timeToLiveSecs": 7200
     }
   }
 }
```

在上面的 JSON 中，`lvaEdge` 是 IoT Edge 模块上实时视频分析的名称。 路由规则遵循[声明路由](../../iot-edge/module-composition.md#declare-routes)中定义的架构。

> [!NOTE]
> 为了确保分析事件到达 IoT Edge 中心，在任何动作检测处理器节点和/或任何 HTTP 扩展处理器节点的下游都需要有一个 IoT 中心接收器节点。

## <a name="event-schema"></a>事件架构

事件来自边缘设备，并且可以在边缘或云中使用。 由 IoT Edge 上的实时视频分析生成的事件遵循由 Azure IoT 中心建立的[流式处理消息传送模式](../../iot-hub/iot-hub-devguide-messages-construct.md)。 此模式包含系统属性、应用程序属性和正文。

### <a name="summary"></a>总结

每个事件（在通过 IoT 中心观察到时）都有一组共同属性：

|properties   |属性类型| 数据类型   |说明|
|---|---|---|---|
|`message-id`   |system |GUID|  唯一的事件 ID。|
|`topic`|   applicationProperty |string|    Azure 媒体服务帐户的 Azure 资源管理器路径。|
|`subject`| applicationProperty |string|    发出事件的实体的子路径。|
|`eventTime`|   applicationProperty|    string| 生成事件的时间。|
|`eventType`|   applicationProperty |string|    事件类型标识符。 （请参阅以下部分。）|
|`body`|body    |object|    特定事件数据。|
|`dataVersion`  |applicationProperty|   string  |{Major}.{Minor}|

### <a name="properties"></a>属性

#### <a name="message-id"></a>message-id

事件的全局唯一标识符 (GUID)。

#### <a name="topic"></a>主题

表示与图关联的 Azure 媒体服务帐户。

`/subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.Media/mediaServices/{accountName}`

#### <a name="subject"></a>subject

发出事件的实体：

`/graphInstances/{graphInstanceName}`<br/>
`/graphInstances/{graphInstanceName}/sources/{sourceName}`<br/>
`/graphInstances/{graphInstanceName}/processors/{processorName}`<br/>
`/graphInstances/{graphInstanceName}/sinks/{sinkName}`

`subject` 属性用于将一般事件映射到生成模块。 例如，对于无效 RTSP 用户名或密码，生成的事件将是 `/graphInstances/myGraph/sources/myRtspSource` 节点上的 `Microsoft.Media.Graph.Diagnostics.ProtocolError`。

#### <a name="event-types"></a>事件类型

事件类型根据以下架构分配给命名空间：

`Microsoft.Media.Graph.{EventClass}.{EventType}`

#### <a name="event-classes"></a>事件类

|类名|说明|
|---|---|
|分析  |在内容分析过程中生成的事件。|
|诊断    |有助于对问题和性能进行诊断的事件。|
|可运行    |在资源操作过程中生成的事件。|

事件类型特定于每个事件类。

示例:

* `Microsoft.Media.Graph.Analytics.Inference`
* `Microsoft.Media.Graph.Diagnostics.AuthorizationError`
* `Microsoft.Media.Graph.Operational.GraphInstanceStarted`

### <a name="event-time"></a>事件时间

事件时间采用 ISO 8601 字符串格式。 它表示事件的发生时间。

### <a name="azure-monitor-collection-via-telegraf"></a>通过 Telegraf 进行 Azure Monitor 收集

这些指标将通过 IoT Edge 模块上的实时视频分析进行报告：  

|指标名称|类型|Label|说明|
|-----------|----|-----|-----------|
|lva_active_graph_instances|仪表|iothub、edge_device、module_name、graph_topology|每个拓扑的活动图形总数。|
|lva_received_bytes_total|计数器|iothub、edge_device、module_name、graph_topology、graph_instance、graph_node|一个节点接收的字节总数。 仅支持用于 RTSP 源。|
|lva_data_dropped_total|计数器|iothub、edge_device、module_name、graph_topology、graph_instance、graph_node、data_kind|任何已删除数据（事件、媒体等）的计数器。|

> [!NOTE]
> 在容器的端口 9600 上公开了 [Prometheus 终结点](https://prometheus.io/docs/practices/naming/)。 如果你将 IoT Edge 模块上的实时视频分析命名为“lvaEdge”，则它们能够通过向 http://lvaEdge:9600/metrics 发送 GET 请求来访问指标。   

请按照以下步骤，从 IoT Edge 模块上的实时视频分析启用指标收集：

1. 在开发计算机上创建一个文件夹，并转到该文件夹。

1. 在该文件夹中创建一个包含以下配置的 `telegraf.toml` 文件：
    ```
    [agent]
        interval = "30s"
        omit_hostname = true

    [[inputs.prometheus]]
      metric_version = 2
      urls = ["http://edgeHub:9600/metrics", "http://edgeAgent:9600/metrics", "http://{LVA_EDGE_MODULE_NAME}:9600/metrics"]

    [[outputs.azure_monitor]]
      namespace_prefix = "lvaEdge"
      region = "westus"
      resource_id = "/subscriptions/{SUBSCRIPTON_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.Devices/IotHubs/{IOT_HUB_NAME}"
    ```
    > [!IMPORTANT]
    > 请务必替换 .toml 文件中的变量。 变量由大括号 (`{}`) 表示。

1. 在同一文件夹中创建包含以下命令的 Dockerfile：
    ```
        FROM telegraf:1.15.3-alpine
        COPY telegraf.toml /etc/telegraf/telegraf.conf
    ```

1. 使用 Docker CLI 命令生成 Docker 文件，并将该映像发布到 Azure 容器注册表。
    
   若要详细了解如何使用 Docker CLI 将映像推送到容器注册表，请参阅[推送和拉取 Docker 映像](../../container-registry/container-registry-get-started-docker-cli.md)。 有关 Azure 容器注册表的其他信息，请参阅[文档](../../container-registry/index.yml)。


1. 推送到 Azure 容器注册表以后，请将以下节点添加到部署清单文件：
    ```
    "telegraf": 
    {
      "settings": 
        {
            "image": "{AZURE_CONTAINER_REGISTRY_LINK_TO_YOUR_TELEGRAF_IMAGE}"
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
     > 可为服务主体提供“监视指标发布者”角色。 按照[创建服务主体](../../azure-arc/data/upload-metrics-and-logs-to-azure-monitor.md?pivots=client-operating-system-macos-and-linux#create-service-principal)中的步骤创建服务主体并分配角色。

1. 部署模块后，指标会显示在 Azure Monitor 中的单个命名空间下。 指标名称将与 Prometheus 发出的名称匹配。 

   这种情况下，请在 Azure 门户中转到 IoT 中心，并在左窗格中选择“指标”。 你应会在那里看到指标。

### <a name="log-analytics-metrics-collection"></a>Log Analytics 指标集合
使用 [Prometheus 终结点](https://prometheus.io/docs/practices/naming/)和 [Log Analytics](../../azure-monitor/logs/log-analytics-tutorial.md)，可以生成和[监视指标](../../azure-monitor/essentials/metrics-supported.md)，例如使用的 CPUPercent、MemoryUsedPercent 等。   

> [!NOTE]
> 下面的配置不会收集日志，只收集指标。 可以扩展收集器模块来同时收集和上传日志。

[ ![使用 Log Analytics 显示指标集合的关系图。](./media/telemetry-schema/log-analytics.png)](./media/telemetry-schema/log-analytics.png#lightbox)

1. 了解如何[收集指标](https://github.com/Azure/iotedge/tree/master/edge-modules/MetricsCollector)
1. 使用 Docker CLI 命令生成 [Docker 文件](https://github.com/Azure/iotedge/tree/master/edge-modules/MetricsCollector/docker/linux)，并将该映像发布到 Azure 容器注册表。
    
   若要详细了解如何使用 Docker CLI 将映像推送到容器注册表，请参阅[推送和拉取 Docker 映像](../../container-registry/container-registry-get-started-docker-cli.md)。 有关 Azure 容器注册表的其他信息，请参阅[文档](../../container-registry/index.yml)。

1. 推送到 Azure 容器注册表以后，以下内容将插入到部署清单文件中：
    ```json
    "azmAgent": {
      "settings": {
        "image": "{AZURE_CONTAINER_REGISTRY_LINK_TO_YOUR_METRICS_COLLECTOR}"
      },
      "type": "docker",
      "version": "1.0",
      "status": "running",
      "restartPolicy": "always",
      "env": {
        "LogAnalyticsWorkspaceId": { "value": "{YOUR_LOG_ANALYTICS_WORKSPACE_ID}" },
        "LogAnalyticsSharedKey": { "value": "{YOUR_LOG_ANALYTICS_WORKSPACE_SECRET}" },
        "LogAnalyticsLogType": { "value": "IoTEdgeMetrics" },
        "MetricsEndpointsCSV": { "value": "http://edgeHub:9600/metrics,http://edgeAgent:9600/metrics,http://lvaEdge:9600/metrics" },
        "ScrapeFrequencyInSecs": { "value": "30 " },
        "UploadTarget": { "value": "AzureLogAnalytics" }
      }
    }
    ```
    > [!NOTE]
    > 模块 `edgeHub`、`edgeAgent` 和 `lvaEdge` 是部署清单文件中定义的模块的名称。 请确保模块的名称匹配。   

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
    | where module_name in ("lvaEdge","yolov3","tinyyolov3")
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
   * 如果将值设置为“`None`”，则不会生成任何日志。 （不建议采用此配置。）
   * 只有在需要与 Azure 支持共享日志以诊断问题时，才使用“`Verbose`”。

* `logCategories`

   * 以逗号分隔的以下一个或多个值的列表：`Application`、`Events`、`MediaPipeline`。
   * 默认值为 `Application, Events`。
   * `Application`：来自模块的概要信息，例如模块启动消息、环境错误和直接方法调用。
   * `Events`：本文前面介绍的所有事件。
   * `MediaPipeline`：低级别的日志，可在你排查问题（例如，与支持 RTSP 的相机建立连接时遇到困难）时提供见解。
   
### <a name="generating-debug-logs"></a>生成调试日志

在某些情况下，可能需要生成比上述日志更详细的日志才能帮助 Azure 支持解决问题。 若要生成这些日志，请执行以下操作：

1. 通过 `createOptions` [将模块存储链接到设备存储](../../iot-edge/how-to-access-host-storage-from-module.md#link-module-storage-to-device-storage)。 如果你查看快速入门中的[部署清单模板](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp/blob/master/src/edge/deployment.template.json)，则会看到以下代码：

   ```
   "createOptions": {
     …
     "Binds": [
       "/var/local/mediaservices/:/var/lib/azuremediaservices/"
     ]
    }
   ```

   此代码让 Edge 模块将日志写入设备存储路径 `/var/local/mediaservices/`。 

 1. 将以下 `desired` 属性添加到模块：

    `"debugLogsDirectory": "/var/lib/azuremediaservices/debuglogs/",`

现在，该模块会以二进制格式将调试日志写入到设备存储路径 `/var/local/mediaservices/debuglogs/`。 你可以与 Azure 支持共享这些日志。

## <a name="faq"></a>常见问题解答

如有问题，请参阅[监视和指标常见问题解答](/azure/media-services/live-video-analytics-edge/faq#monitoring-and-metrics)。

## <a name="next-steps"></a>后续步骤

[连续视频录制](continuous-video-recording-tutorial.md)