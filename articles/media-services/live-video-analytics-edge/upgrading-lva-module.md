---
title: 将 IoT Edge 上的实时视频分析升级到1.0 到2。0
description: 本文介绍了在 Azure IoT Edge 模块上升级实时视频分析 (LVA) 时要考虑的区别和不同之处。
author: naiteeks
ms.topic: how-to
ms.author: naiteeks
ms.date: 12/14/2020
ms.openlocfilehash: aa8657550c6475afd9f893acf8985c50cec0f199
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2021
ms.locfileid: "98119452"
---
# <a name="upgrading-live-video-analytics-on-iot-edge-from-10-to-20"></a>将 IoT Edge 上的实时视频分析升级到1.0 到2。0

本文介绍了在 Azure IoT Edge 模块上升级实时视频分析 (LVA) 时要考虑的区别和不同之处。

## <a name="change-list"></a>更改列表

> [!div class="mx-tdCol4BreakAll"]
> |Title|实时视频分析1。0|实时视频分析2。0|描述|
> |-------------|----------|---------|---------|
> |容器映像|mcr.microsoft.com/media/live-video-analytics:1|mcr.microsoft.com/media/live-video-analytics:2|Microsoft 发布的 docker 映像，适用于 Azure IoT Edge 上的实时视频分析|
> |**MediaGraph 节点** |    |   |   |
> |源|:::image type="icon" source="./././media/upgrading-lva/check.png"::: RTSP 源 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: IoT 中心消息源 |:::image type="icon" source="./././media/upgrading-lva/check.png"::: RTSP 源 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: IoT 中心消息源 | 充当媒体引入和消息源的 MediaGraph 节点。|
> |Processors|:::image type="icon" source="./././media/upgrading-lva/check.png"::: 运动检测处理器 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 帧速率筛选器处理器 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Http 扩展处理器 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Grpc 扩展处理器 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 信号入口处理器 |:::image type="icon" source="./././media/upgrading-lva/check.png"::: 运动检测处理器 </br>:::image type="icon" source="./././media/upgrading-lva/remove.png"::: **帧速率筛选器处理器**</br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Http 扩展处理器 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Grpc 扩展处理器 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 信号入口处理器 | 使你能够在发送到 AI 推理服务器之前格式化媒体的 MediaGraph 节点。|
> |接收器|:::image type="icon" source="./././media/upgrading-lva/check.png"::: 资产接收器 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 文件接收器 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: IoT 中心消息接收器|:::image type="icon" source="./././media/upgrading-lva/check.png"::: 资产接收器 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 文件接收器 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: IoT 中心消息接收器| 用于存储已处理媒体的 MediaGraph 节点。|
> |**支持的 MediaGraphs** |    |   |   |
> |拓扑|:::image type="icon" source="./././media/upgrading-lva/check.png"::: 连续视频录制 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 动作分析和连续视频录制 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 外部分析和连续视频录制 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 基于事件的动作录制 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: AI 上基于事件的录制</br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 外部事件上基于事件的记录 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 运动分析 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 运动分析后跟 AI 推断 |:::image type="icon" source="./././media/upgrading-lva/check.png"::: 连续视频录制 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 动作分析和连续视频录制 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 外部分析和连续视频录制 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 基于事件的动作录制 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: AI 上基于事件的录制</br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 外部事件上基于事件的记录 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 运动分析 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 运动分析后跟 AI 推断 </br>:::image type="icon" source="./././media/upgrading-lva/check.png":::**AI 组合** </br>:::image type="icon" source="./././media/upgrading-lva/check.png":::**音频和视频录制** </br>  | MediaGraph 拓扑，使你能够定义图形的蓝图，并使用参数作为值的占位符。|

## <a name="upgrading-the-live-video-analytics-on-iot-edge-module-from-10-to-20"></a>将 IoT Edge 模块上的实时视频分析从1.0 升级到2。0
在 IoT Edge 模块上升级实时视频分析时，请确保更新以下信息。
### <a name="container-image"></a>容器映像
在部署模板中，在节点下的 IoT Edge 模块上查找实时视频分析， `modules` 并将 `image` 字段更新为：
```
"image": "mcr.microsoft.com/media/live-video-analytics:2"
```
> [!TIP]
如果你尚未在 IoT Edge 模块上修改实时视频分析的名称，请 `lvaEdge` 在 module 节点下查找。

### <a name="topology-file-changes"></a>拓扑文件更改
在拓扑文件中，确保将 **`apiVersion`** 设置为2。0

### <a name="mediagraph-node-changes"></a>MediaGraph 节点更改


* 相机源的音频现在可以连同视频一起传递到下游。 既可以将 **音频** 和 **视频、视频或****音频和视频** 传递 **`outputSelectors`** 到任何图形节点的帮助。 例如，如果想要从 RTSP 源中只选择 "视频" 并将其传递到下游，请将以下内容添加到 RTSP 源节点：
    ```
    "outputSelectors": [
      {
        "property": "mediaType",
        "operator": "is",
        "value": "video"
      }
    ```
>[!NOTE]
>**`outputSelectors`** 是一个可选属性。 如果未使用此功能，则 media graph 会传递音频 (如果从 RTSP 的下游相机启用了) 和视频。 

* 在 `MediaGraphHttpExtension` 和 `MediaGraphGrpcExtension` 处理器中，请注意以下更改：  
    * **映像属性**
        * `MediaGraphImageFormatEncoded` 不再受支持。 
        * 请改用 **`MediaGraphImageFormatBmp`** 或 **`MediaGraphImageFormatJpeg`** **`MediaGraphImageFormatPng`** 。 例如，应用于对象的
        ```
        "image": {
                "scale": 
                {
                    "mode": "preserveAspectRatio",
                    "width": "416",
                    "height": "416"
                },
                "format": 
                {
                    "@type": "#Microsoft.Media.MediaGraphImageFormatJpeg"
                }
            }
        ```
        * 如果要使用原始映像，请使用 **`MediaGraphImageFormatRaw`** 。 为此，请将映像节点更新为：
        ```
        "image": {
                "scale": 
                {
                    "mode": "preserveAspectRatio",
                    "width": "416",
                    "height": "416"
                },
                "format": 
                {
                    "@type": "#Microsoft.Media.MediaGraphImageFormatRaw",
                    "pixelFormat": "rgba"
                }
            }
        ```
        >[!NOTE]
        > 可能的 pixelFormat 值包括： `yuv420p` 、 `rgb565be` 、 `rgb565le` 、 `rgb555be` 、 `rgb555le` 、 `rgb24` `bgr24` `argb` `rgba` 、、、、 `abgr` 、 `bgra`  

    * **extensionConfiguration for Grpc extension processor**  
        * 在 `MediaGraphGrpcExtension` 处理器中，有一个名为的新属性 **`extensionConfiguration`** 是可用的，它是可用作 gRPC 协定一部分的可选字符串。 此字段可用于将任何数据传递到推理服务器，并且您可以定义推理服务器如何使用这些数据。  
        此属性的一个用例是在单个推理服务器中打包了多个 AI 模型。 使用此属性时，无需为每个 AI 模型公开节点。 相反，对于 graph 实例，作为扩展提供程序，您可以定义如何使用属性选择不同的 AI 模型， **`extensionConfiguration`** 并在执行过程中，LVA 会将此字符串传递到推断服务器，该服务器可使用此字符串调用所需的 AI 模型。  

    * **AI 组合**
        * 实时视频分析2.0 现在支持在拓扑中使用多个 media graph 扩展处理器。 可以按顺序将媒体帧从 RTSP 相机传递到不同的 AI 模型，也可以并行或二者结合使用。 请参阅示例拓扑，其中显示两个按顺序使用的 AI 模型。


* 现在，你可以在 **文件接收器** 节点中指定 IoT Edge 模块上的实时视频分析可用于存储已处理图像的磁盘空间量。 为此，请将 **`maximumSizeMiB`** 字段添加到 "FileSink" 节点。 示例文件接收器节点如下所示：
    ```
    "sinks": [
      {
        "@type": "#Microsoft.Media.MediaGraphFileSink",
        "name": "fileSink",
        "inputs": [
          {
            "nodeName": "signalGateProcessor",
            "outputSelectors": [
              {
                "property": "mediaType",
                "operator": "is",
                "value": "video"
              }
            ]
          }
        ],
        "fileNamePattern": "sampleFiles-${System.DateTime}",
        "maximumSizeMiB":"512",
        "baseDirectoryPath":"/var/media"
      }
    ]
    ```
* 在 " **资产接收器** " 节点中，可以指定 IoT Edge 模块上的实时视频分析可用于存储已处理图像的磁盘空间量。 为此，请将 **`localMediaCacheMaximumSizeMiB`** 字段添加到 "资产接收器" 节点。 示例资产接收器节点如下所示：
    ```
    "sinks": [
      {
        "@type": "#Microsoft.Media.MediaGraphAssetSink",
        "name": "AssetSink",
        "inputs": [
          {
            "nodeName": "signalGateProcessor",
            "outputSelectors": [
              {
                "property": "mediaType",
                "operator": "is",
                "value": "video"
              }
            ]
          }
        ],
        "assetNamePattern": "sampleAsset-${System.GraphInstanceName}",
        "segmentLength": "PT30S",
        "localMediaCacheMaximumSizeMiB":"200",
        "localMediaCachePath":"/var/lib/azuremediaservices/tmp/"
      }
    ]
    ```
    >[!NOTE]
    >  **文件接收器** 路径被拆分为基目录路径和文件名模式，而 **资产接收器** 路径包含基目录路径。  

* **`MediaGraphFrameRateFilterProcessor`****IoT Edge 2.0 模块上的实时视频分析** 中已弃用。
    * 若要采样传入视频进行处理，请将 **`samplingOptions`** 属性添加到 MediaGraph 扩展处理器 (`MediaGraphHttpExtension` 或 `MediaGraphGrpcExtension`)   
     ```
          "samplingOptions": 
          {
            "skipSamplesWithoutAnnotation": "false",  // If true, limits the samples submitted to the extension to only samples which have associated inference(s) 
            "maximumSamplesPerSecond": "20"   // Maximum rate of samples submitted to the extension
          },
    ```
### <a name="module-metrics-in-the-prometheus-format-using-telegraf"></a>使用 Telegraf 的 Prometheus 格式的模块指标
在此版本中，可以使用 Telegraf 将指标发送到 Azure Monitor。 在这里，指标可能会定向到 Log Analytics 或事件中心。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/telemetry-schema/telegraf.png" alt-text="事件的分类":::

可以使用 docker 轻松生成带有自定义配置的 Telegraf 映像。 有关详细信息，请参阅 [监视和日志记录](monitoring-logging.md#azure-monitor-collection-via-telegraf) 页。

## <a name="next-steps"></a>后续步骤

[IoT Edge 上的实时视频分析入门](get-started-detect-motion-emit-events-quickstart.md)