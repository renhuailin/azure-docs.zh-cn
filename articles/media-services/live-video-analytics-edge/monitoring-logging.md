---
title: 监视和日志记录 - Azure
description: 本文概述了 IoT Edge 上的实时视频分析中的监视和日志记录。
ms.topic: reference
ms.date: 04/27/2020
ms.openlocfilehash: 6a7251b62421642ad9f5dba4f4c2a15ce74cd5cf
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97900869"
---
# <a name="monitoring-and-logging"></a>监视和日志记录

本文介绍如何从 IoT Edge 模块上的实时视频分析接收远程监视的事件。 

你还将了解如何控制模块生成的日志。

## <a name="taxonomy-of-events"></a>事件的分类

IoT Edge 上的实时视频分析根据以下分类发出事件或遥测数据：

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/telemetry-schema/taxonomy.png" alt-text="显示事件分类的关系图。":::

* 操作：用户的操作或[媒体图形](media-graph-concept.md)执行过程中生成的事件
   
   * 卷：预计 (几分钟，甚至更少) 
   * 示例:

      - 记录已启动 (下面的示例中所示) 
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

   * 卷：每分钟可以 (多次) 
   * 示例:
   
      -  (下面的示例中所示的 RTSP [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) 信息)  
      - 传入视频源中的间隙

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
* 分析：作为视频分析的一部分生成的事件

   * 卷：一分钟或更长时间 (几次) 
   * 示例:
      
      - 检测到的动作 (下面的示例中所示)  
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

模块发出的事件将发送到 [IoT Edge 中心](../../iot-edge/iot-edge-runtime.md#iot-edge-hub)。 它们可以从源路由到其他目标。 

### <a name="timestamps-in-analytic-events"></a>分析事件中的时间戳

如前文所述，作为视频分析的一部分生成的事件具有与之关联的时间戳。 如果您将 [实时视频记录](video-recording-concept.md) 为图形拓扑的一部分，则这些时间戳可帮助您找到所录制视频中特定事件发生的位置。 下面是有关如何将分析事件中的时间戳映射到记录到 [Azure 媒体服务资产](terminology.md#asset)的视频时间线的准则。

首先，提取 `eventTime` 值。 在[时间范围筛选器](playback-recordings-how-to.md#time-range-filters)中使用此值检索录制的适当部分。 例如，你可能想要检索在之前30秒开始的视频 `eventTime` 并在其之后结束30秒。 对于上一个示例，其中， `eventTime` 为 2020-05-12T23：33： 09.381 z，在30秒前后对 HLS 清单的请求 `eventTime` 将类似于以下请求：

```
https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2020-05-12T23:32:39Z,endTime=2020-05-12T23:33:39Z).m3u8
```

上述 URL 将返回包含用于媒体播放列表的 Url 的 [主播放列表](https://developer.apple.com/documentation/http_live_streaming/example_playlists_for_http_live_streaming) 。 媒体播放列表包含如下所示的条目：

```
...
#EXTINF:3.103011,no-desc
Fragments(video=143039375031270,format=m3u8-aapl)
...
```
前面的条目报告了从值开始的视频片段 `timestamp` `143039375031270` 。 `timestamp`分析事件中的值使用与媒体播放列表相同的时间刻度。 它可用于标识相关视频片段并查找正确的帧。

有关详细信息，请参阅有关 HLS 中的 [帧准确查找的文章](https://www.bing.com/search?q=frame+accurate+seeking+in+HLS) 。

## <a name="controlling-events"></a>控制事件

你可以使用以下模块克隆属性控制 IoT Edge 模块上的实时视频分析所发布的操作和诊断事件。 [模块克隆 JSON 架构](module-twin-configuration-schema.md)中介绍了这些属性。

- `diagnosticsEventsOutputName`：若要从模块获取诊断事件，请包括此属性，并为其提供任何值。 省略它或将其保留为空，以停止模块发布诊断事件。
   
- `operationalEventsOutputName`：若要从模块获取操作事件，请包括此属性，并为其提供任何值。 省略它或将其保留为空，以使模块停止发布操作事件。
   
分析事件由移动检测处理器或 HTTP 扩展处理器等节点生成。 IoT 中心接收器用于将它们发送到 IoT Edge 中心。 

您可以通过使用部署清单中的模块克隆的属性来控制 [前面所有事件的路由](../../iot-edge/module-composition.md#declare-routes) `desired` `$edgeHub` ：

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

在上述 JSON 中， `lvaEdge` 是 IoT Edge 模块上的实时视频分析的名称。 路由规则遵循在 [Declare 路由](../../iot-edge/module-composition.md#declare-routes)中定义的架构。

> [!NOTE]
> 若要确保 analytics 事件到达 IoT Edge 中心，需要在任何运动检测处理器节点和/或任何 HTTP 扩展处理器节点下游使用 IoT 中心接收器节点。

## <a name="event-schema"></a>事件架构

事件源自边缘设备，并可在边缘或云中使用。 IoT Edge 上的实时视频分析所生成的事件符合 Azure IoT 中心建立的 [流式处理消息模式](../../iot-hub/iot-hub-devguide-messages-construct.md) 。 模式包括系统属性、应用程序属性和正文。

### <a name="summary"></a>总结

通过 IoT 中心观察到的每个事件都具有一组通用属性：

|属性   |属性类型| 数据类型   |说明|
|---|---|---|---|
|`message-id`   |system |GUID|  唯一的事件 ID。|
|`topic`|   applicationProperty |string|    Azure 媒体服务帐户的 azure 资源管理器路径。|
|`subject`| applicationProperty |string|    发出事件的实体的子路径。|
|`eventTime`|   applicationProperty|    string| 生成事件的时间。|
|`eventType`|   applicationProperty |string|    事件类型标识符。  (参阅以下部分。 ) |
|`body`|body    |object|    特定事件数据。|
|`dataVersion`  |applicationProperty|   string  |{Major}.{Minor}|

### <a name="properties"></a>属性

#### <a name="message-id"></a>message-id

事件 (GUID) 的全局唯一标识符。

#### <a name="topic"></a>主题

表示与关系图关联的 Azure Media Services 帐户。

`/subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.Media/mediaServices/{accountName}`

#### <a name="subject"></a>subject

发出事件的实体：

`/graphInstances/{graphInstanceName}`<br/>
`/graphInstances/{graphInstanceName}/sources/{sourceName}`<br/>
`/graphInstances/{graphInstanceName}/processors/{processorName}`<br/>
`/graphInstances/{graphInstanceName}/sinks/{sinkName}`

`subject`属性允许您将一般事件映射到生成模块。 例如，对于无效的 RTSP 用户名或密码，生成的事件将出现 `Microsoft.Media.Graph.Diagnostics.ProtocolError` 在 `/graphInstances/myGraph/sources/myRtspSource` 节点上。

#### <a name="event-types"></a>事件类型

事件类型根据此架构分配给命名空间：

`Microsoft.Media.Graph.{EventClass}.{EventType}`

#### <a name="event-classes"></a>事件类

|类名|说明|
|---|---|
|分析  |在内容分析过程中生成的事件。|
|诊断    |有助于诊断问题和性能的事件。|
|可运行    |在资源操作过程中生成的事件。|

事件类型特定于每个事件类。

示例:

* `Microsoft.Media.Graph.Analytics.Inference`
* `Microsoft.Media.Graph.Diagnostics.AuthorizationError`
* `Microsoft.Media.Graph.Operational.GraphInstanceStarted`

### <a name="event-time"></a>事件时间

事件时间格式为 ISO 8601 字符串。 它表示事件发生的时间。

### <a name="azure-monitor-collection-via-telegraf"></a>通过 Telegraf 进行 Azure Monitor 收集

将从 IoT Edge 模块上的实时视频分析报告这些指标：  

|指标名称|类型|Label|说明|
|-----------|----|-----|-----------|
|lva_active_graph_instances|仪表|iothub、edge_device、module_name graph_topology|每个拓扑的活动曲线图总数。|
|lva_received_bytes_total|计数器|iothub、edge_device、module_name、graph_topology、graph_instance、graph_node|节点收到的总字节数。 仅支持 RTSP 源。|
|lva_data_dropped_total|计数器|iothub、edge_device、module_name、graph_topology、graph_instance、graph_node、data_kind|任何已删除数据 (事件、媒体等) 上的计数器。|

> [!NOTE]
> [Prometheus 终结点](https://prometheus.io/docs/practices/naming/)在容器端口9600处公开。 如果在 IoT Edge 模块 "lvaEdge" 上命名实时视频分析，他们将能够通过将 GET 请求发送到来访问指标 http://lvaEdge:9600/metrics 。   

按照以下步骤在 IoT Edge 模块上的实时视频分析中启用指标收集：

1. 在开发计算机上创建一个文件夹，并中转到该文件夹。

1. 在文件夹中，创建 `telegraf.toml` 包含以下配置的文件：
    ```
    [agent]
        interval = "30s"
        omit_hostname = true

    [[inputs.prometheus]]
      metric_version = 2
      urls = ["http://edgeHub:9600/metrics", "http://edgeAgent:9600/metrics", "http://{LVA_EDGE_MODULE_NAME}:9600/metrics"]

    [[outputs.azure_monitor]]
      namespace_prefix = ""
      region = "westus"
      resource_id = "/subscriptions/{SUBSCRIPTON_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.Devices/IotHubs/{IOT_HUB_NAME}"
    ```
    > [!IMPORTANT]
    > 请确保替换 toml 文件中的变量。 变量由大括号表示 (`{}`) 。

1. 在相同的文件夹中，创建一个 `.dockerfile` 包含以下命令的：
    ```
        FROM telegraf:1.15.3-alpine
        COPY telegraf.toml /etc/telegraf/telegraf.conf
    ```

1. 使用 Docker CLI 命令生成 Docker 文件，并将该映像发布到 Azure 容器注册表。
    
   有关使用 Docker CLI 推送到容器注册表的详细信息，请参阅 [推送和拉取 Docker 映像](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-docker-cli)。 有关 Azure 容器注册表的其他信息，请参阅 [文档](https://docs.microsoft.com/azure/container-registry/)。


1. 推送到 Azure 容器注册表完成后，将以下节点添加到部署清单文件中：
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
    > 请确保替换清单文件中的变量。 变量由大括号表示 (`{}`) 。


   可以 [通过服务主体](https://github.com/influxdata/telegraf/blob/master/plugins/outputs/azure_monitor/README.md#azure-authentication)对 Azure Monitor 进行身份验证。
        
   Azure Monitor Telegraf 插件公开 [多种身份验证方法](https://github.com/influxdata/telegraf/blob/master/plugins/outputs/azure_monitor/README.md#azure-authentication)。 

  1. 若要使用服务主体身份验证，请设置以下环境变量：  
     `AZURE_TENANT_ID`：指定要向其进行身份验证的租户。  
     `AZURE_CLIENT_ID`：指定要使用的应用客户端 ID。  
     `AZURE_CLIENT_SECRET`：指定要使用的应用密钥。  
     
     >[!TIP]
     > 您可以为服务主体指定 **监视指标发布者** 角色。

1. 部署模块后，指标将显示在单个命名空间下的 Azure Monitor 中。 指标名称将与 Prometheus 所发出的名称相匹配。 

   在这种情况下，请在 Azure 门户中，切换到 IoT 中心，并在左窗格中选择 " **指标** "。 应该会看到指标。

## <a name="logging"></a>日志记录

对于其他 IoT Edge 模块，还可以在边缘设备上 [检查容器日志](../../iot-edge/troubleshoot.md#check-container-logs-for-issues) 。 你可以使用 [以下模块](module-twin-configuration-schema.md) 克隆属性配置写入日志的信息：

* `logLevel`

   * 允许使用的值为 `Verbose`、`Information`、`Warning`、`Error` 和 `None`。
   * 默认值为 `Information`。 日志将包含 "错误"、"警告" 和 "信息" 消息。
   * 如果将值设置为 `Warning` ，则日志将包含错误消息和警告消息。
   * 如果将值设置为 `Error` ，则日志将只包含错误消息。
   * 如果将值设置为 `None` ，则不会生成任何日志。  (我们不建议此配置。 ) 
   * `Verbose`仅当需要与 Azure 支持共享日志来诊断问题时才使用。

* `logCategories`

   * 以下一个或多个值的逗号分隔列表： `Application` 、 `Events` 、 `MediaPipeline` 。
   * 默认值为 `Application, Events`。
   * `Application`：模块中的高级信息，如模块启动消息、环境错误和直接方法调用。
   * `Events`：本文前面所述的所有事件。
   * `MediaPipeline`：在解决问题时，可能会提供一些低级别的日志，例如与支持 RTSP 的相机建立连接相关的问题。
   
### <a name="generating-debug-logs"></a>生成调试日志

在某些情况下，若要帮助 Azure 支持人员解决问题，可能需要生成比之前介绍的日志更详细的日志。 生成这些日志：

1. 通过将[模块存储链接到设备存储](../../iot-edge/how-to-access-host-storage-from-module.md#link-module-storage-to-device-storage) `createOptions` 。 如果你查看快速入门中的 [部署清单模板](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp/blob/master/src/edge/deployment.template.json) ，你将看到以下代码：

   ```
   "createOptions": {
     …
     "Binds": [
       "/var/local/mediaservices/:/var/lib/azuremediaservices/"
     ]
    }
   ```

   此代码允许边缘模块将日志写入设备存储路径 `/var/local/mediaservices/` 。 

 1. 将以下 `desired` 属性添加到模块：

    `"debugLogsDirectory": "/var/lib/azuremediaservices/debuglogs/",`

现在，该模块将以二进制格式将调试日志写入设备存储路径 `/var/local/mediaservices/debuglogs/` 。 可以与 Azure 支持共享这些日志。

## <a name="faq"></a>FAQ

如有疑问，请参阅 [监视和指标常见问题解答](faq.md#monitoring-and-metrics)。

## <a name="next-steps"></a>后续步骤

[连续视频录制](continuous-video-recording-tutorial.md)
