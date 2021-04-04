---
title: 将 IoT Edge 上的实时视频分析从 1.0 升级为 2.0
description: 本文介绍升级“Azure IoT Edge 上的实时视频分析 (LVA)”模块时需要考虑的差异和不同事项。
author: naiteeks
ms.topic: how-to
ms.author: naiteeks
ms.date: 12/14/2020
ms.openlocfilehash: 49c17946203bc6c3655b1aaf7b04a1ee3ea67388
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98955643"
---
# <a name="upgrading-live-video-analytics-on-iot-edge-from-10-to-20"></a>将 IoT Edge 上的实时视频分析从 1.0 升级为 2.0

本文介绍升级“Azure IoT Edge 上的实时视频分析 (LVA)”模块时需要考虑的差异和不同事项。

## <a name="change-list"></a>更改列表

> [!div class="mx-tdCol4BreakAll"]
> |标题|实时视频分析 1.0|实时视频分析 2.0|说明|
> |-------------|----------|---------|---------|
> |容器映像|mcr.microsoft.com/media/live-video-analytics:1|mcr.microsoft.com/media/live-video-analytics:2|Microsoft 发布的适用于 Azure IoT Edge 上的实时视频分析的 docker 映像|
> |**MediaGraph 节点** |    |   |   |
> |源|:::image type="icon" source="./././media/upgrading-lva/check.png"::: RTSP 源 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: IoT 中心消息源 |:::image type="icon" source="./././media/upgrading-lva/check.png"::: RTSP 源 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: IoT 中心消息源 | 充当媒体引入源和消息源的 MediaGraph 节点。|
> |Processors|:::image type="icon" source="./././media/upgrading-lva/check.png"::: 运动检测处理器 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 帧速率筛选器处理器 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: HTTP 扩展处理器 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Grpc 扩展处理器 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 信号门处理器 |:::image type="icon" source="./././media/upgrading-lva/check.png"::: 运动检测处理器 </br>:::image type="icon" source="./././media/upgrading-lva/remove.png"::: **帧速率筛选器处理器**</br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: HTTP 扩展处理器 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Grpc 扩展处理器 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 信号门处理器 | 能够实现在发送到 AI 推理服务器之前格式化媒体的 MediaGraph 节点。|
> |接收器|:::image type="icon" source="./././media/upgrading-lva/check.png"::: 资产接收器 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 文件接收器 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: IoT 中心消息接收器|:::image type="icon" source="./././media/upgrading-lva/check.png"::: 资产接收器 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 文件接收器 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: IoT 中心消息接收器| 能够实现存储已处理媒体的 MediaGraph 节点。|
> |**支持的 MediaGraphs** |    |   |   |
> |拓扑|:::image type="icon" source="./././media/upgrading-lva/check.png"::: 连续视频录制 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 运动分析和连续视频录制 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 外部分析和连续视频录制 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 基于事件的运动录制 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 基于事件的 AI 录制</br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 基于事件的外部事件录制 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 运动分析 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 运动分析后进行 AI 推理 |:::image type="icon" source="./././media/upgrading-lva/check.png"::: 连续视频录制 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 运动分析和连续视频录制 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 外部分析和连续视频录制 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 基于事件的运动录制 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 基于事件的 AI 录制</br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 基于事件的外部事件录制 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 运动分析 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 运动分析后进行 AI 推理 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: **AI 组合** </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: **音频和视频录制** </br>  | 能够实现定义图形蓝图的 MediaGraph 拓扑，其中参数作为值的占位符。|

## <a name="upgrading-the-live-video-analytics-on-iot-edge-module-from-10-to-20"></a>将“IoT Edge 上的实时视频分析”模块从 1.0 升级为 2.0
升级“IoT Edge 上的实时视频分析”模块时，请确保更新以下信息。
### <a name="container-image"></a>容器映像
在部署模板中，在 `modules` 节点下查找“IoT Edge 上的实时视频分析”模块，并将 `image` 字段更新为：
```
"image": "mcr.microsoft.com/media/live-video-analytics:2"
```
> [!TIP]
> 如果尚未修改“IoT Edge 上的实时视频分析”模块的名称，请在模块节点下查找 `lvaEdge`。

### <a name="topology-file-changes"></a>拓扑文件更改
在拓扑文件中，确保将 **`apiVersion`** 设置为 2.0

### <a name="mediagraph-node-changes"></a>MediaGraph 节点更改


* 相机源中的音频现在可以连同视频一起传递到下游。 在 **`outputSelectors`** 的帮助下，可以将单纯的音频、单纯的视频或音频和视频一起传递给任何图形节点  。 例如，如果想要从 RTSP 源中只选择视频并将其传递到下游，请将以下内容添加到 RTSP 源节点：
    ```
    "outputSelectors": [
      {
        "property": "mediaType",
        "operator": "is",
        "value": "video"
      }
    ```
>[!NOTE]
>**`outputSelectors`** 是一个可选属性。 如果未使用此属性，则媒体图将来自 RTSP 相机的音频（如果启用）和视频传递到下游。 

* 在 `MediaGraphHttpExtension` 和 `MediaGraphGrpcExtension` 处理器中，请注意以下更改：  
    #### <a name="image-properties"></a>映像属性
    * `MediaGraphImageFormatEncoded` 不再受支持。 
      * 请改用 **`MediaGraphImageFormatBmp`** 、 **`MediaGraphImageFormatJpeg`** 或 **`MediaGraphImageFormatPng`** 。 例如，应用于对象的
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
        > pixelFormat 的可能值包括：`yuv420p`、`rgb565be`、`rgb565le`、`rgb555be`、`rgb555le`、`rgb24`、`bgr24`、`argb`、`rgba`、`abgr`、`bgra`  

    #### <a name="extensionconfiguration-for-grpc-extension-processor"></a>Grpc 扩展处理器的 extensionConfiguration  
    * `MediaGraphGrpcExtension` 处理器中提供了一个名为 **`extensionConfiguration`** 的新属性，该属性为可选字符串，可用于 gRPC 协定中。 此字段可用于将任何数据传递到推理服务器，你可以定义推理服务器如何使用这些数据。  
    当你在单个推理服务器中打包多个 AI 模型时，需要使用此属性。 使用此属性时，无需为每个 AI 模型公开节点。 相反，对于图形实例，扩展提供商可以使用 **`extensionConfiguration`** 属性定义如何选择不同的 AI 模型，并且在执行期间，LVA 会将此字符串传递给推断服务器，该服务器可以使用此字符串来调用所需的 AI 模型。  

    #### <a name="ai-composition"></a>AI 组合
    * 实时视频分析2.0 现在支持在拓扑中使用多个媒体图形扩展处理器。 可以将来自 RTSP 相机的媒体帧按顺序、并行或两者结合的方式传递给不同的 AI 模型。 请参阅样本拓扑，其中显示了两个按顺序使用的 AI 模型。

### <a name="disk-space-management-with-sink-nodes"></a>具有接收器节点的磁盘空间管理
* 在“文件接收器”节点中，现在可以指定“IoT Edge 上的实时视频分析”模块可以使用多少磁盘空间来存储已处理的映像。 为此，请将 **`maximumSizeMiB`** 字段添加到“文件接收器”节点。 样本“文件接收器”节点如下所示：
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
* 在“资产接收器”节点中，可以指定“IoT Edge 上的实时视频分析”模块可以使用多少磁盘空间来存储已处理的映像。 为此，请将 **`localMediaCacheMaximumSizeMiB`** 字段添加到“资产接收器”节点。 样本“资产接收器”节点如下所示：
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
    >  “文件接收器”路径拆分为基本目录路径和文件名模式，而“资产接收器”路径仅包括基本目录路径 。  

### <a name="frame-rate-management"></a>帧速率管理
* IoT Edge 上的实时视频分析 2.0 模块中已弃用 **`MediaGraphFrameRateFilterProcessor`** 。
    * 若要对传入视频进行采样以用于处理，请将 **`samplingOptions`** 属性添加到 MediaGraph 扩展处理器（`MediaGraphHttpExtension` 或 `MediaGraphGrpcExtension`）  
     ```
          "samplingOptions": 
          {
            "skipSamplesWithoutAnnotation": "false",  // If true, limits the samples submitted to the extension to only samples which have associated inference(s) 
            "maximumSamplesPerSecond": "20"   // Maximum rate of samples submitted to the extension
          },
    ```
### <a name="module-metrics-in-the-prometheus-format-using-telegraf"></a>使用 Telegraf 的 Prometheus 格式的模块指标
在此版本中，可以使用 Telegraf 将指标发送到 Azure Monitor。 指标可能会从那里定向到 Log Analytics 或事件中心。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/telemetry-schema/telegraf.png" alt-text="事件的分类":::

使用 docker 可以轻松生成带自定义配置的 Telegraf 映像。 请访问[监视和日志记录](monitoring-logging.md#azure-monitor-collection-via-telegraf)页，了解详细信息。

## <a name="next-steps"></a>后续步骤

[IoT Edge 上的实时视频分析入门](get-started-detect-motion-emit-events-quickstart.md)