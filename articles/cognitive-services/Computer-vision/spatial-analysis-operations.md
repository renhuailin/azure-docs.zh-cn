---
title: 空间分析操作
titleSuffix: Azure Cognitive Services
description: 空间分析操作。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 06/08/2021
ms.author: pafarley
ms.openlocfilehash: 814bd3d143be5eb295fe8e354537de8fc1d2934b
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113005919"
---
# <a name="spatial-analysis-operations"></a>空间分析操作

空间分析支持分析来自照相机设备的实时流式处理视频。 对于配置的每个相机设备，空间分析的操作会生成发送到 Azure IoT 中心实例的 JSON 消息输出流。 

空间分析容器实现以下操作：

| 操作标识符| 说明|
|---------|---------|
| cognitiveservices.vision.spatialanalysis-personcount | 计算照相机视野中指定区域的人数。 区域必须完全由一个相机覆盖，PersonCount 才能准确记录总人数。 <br> 发送初始 _personCountEvent_ 事件，然后在计数发生更改时发送 _personCountEvent_ 事件。  |
| cognitiveservices.vision.spatialanalysis-personcrossingline | 跟踪人员在相机视野中越过指定线的时间。 <br>当有人越线时发送 _personLineEvent_ 事件，并提供方向信息。 
| cognitiveservices.vision.spatialanalysis-personcrossingpolygon | 当有人进入或离开区域时发送 _personZoneEnterExitEvent_ 事件，并提供方向信息以及已越过区域的边号。 当有人离开区域时发送 _personZoneDwellTimeEvent_，并提供方向信息以及人员在该区域内停留的毫秒数。 |
| cognitiveservices.vision.spatialanalysis-persondistance | 跟踪人员违反距离规则的时间。 <br> 定期发送 _personDistanceEvent_，包含每次违反距离规则的位置。 |
| cognitiveservices.vision.spatialanalysis | 可用于运行上述所有方案的常规操作。 当你想要在同一相机上运行多个方案或想要更有效地使用系统资源（例如 GPU）时，此选项更加有用。 |

所有上述操作在 `.debug` 版本中也可用，该版本可以在处理视频帧时将其可视化。 需要 `xhost +` 在主机计算机上运行，才能实现视频帧和事件的可视化。

| 操作标识符| 说明|
|---------|---------|
| cognitiveservices.vision.spatialanalysis-personcount.debug | 计算照相机视野中指定区域的人数。 <br> 发送初始 _personCountEvent_ 事件，然后在计数发生更改时发送 _personCountEvent_ 事件。  |
| cognitiveservices.vision.spatialanalysis-personcrossingline.debug | 跟踪人员在相机视野中越过指定线的时间。 <br>当有人越线时发送 _personLineEvent_ 事件，并提供方向信息。 
| cognitiveservices.vision.spatialanalysis-personcrossingpolygon.debug | 当有人进入或离开区域时发送 _personZoneEnterExitEvent_ 事件，并提供方向信息以及已越过区域的边号。 当有人离开区域时发送 _personZoneDwellTimeEvent_，并提供方向信息以及人员在该区域内停留的毫秒数。 |
| cognitiveservices.vision.spatialanalysis-persondistance.debug | 跟踪人员违反距离规则的时间。 <br> 定期发送 _personDistanceEvent_，包含每次违反距离规则的位置。 |
| cognitiveservices.vision.spatialanalysis.debug | 可用于运行上述所有方案的常规操作。 当你想要在同一相机上运行多个方案或想要更有效地使用系统资源（例如 GPU）时，此选项更加有用。 |

空间分析还可以通过[实时视频分析](../../media-services/live-video-analytics-edge/spatial-analysis-tutorial.md)作为其视频 AI 模块运行。 

<!--more details on the setup can be found in the [LVA Setup page](LVA-Setup.md). Below is the list of the operations supported with Live Video Analytics. -->

| 操作标识符| 说明|
|---------|---------|
| cognitiveservices.vision.spatialanalysis-personcount.livevideoanalytics | 计算照相机视野中指定区域的人数。 <br> 发送初始 _personCountEvent_ 事件，然后在计数发生更改时发送 _personCountEvent_ 事件。  |
| cognitiveservices.vision.spatialanalysis-personcrossingline.livevideoanalytics | 跟踪人员在相机视野中越过指定线的时间。 <br>当有人越线时发送 _personLineEvent_ 事件，并提供方向信息。 
| cognitiveservices.vision.spatialanalysis-personcrossingpolygon.livevideoanalytics | 当有人进入或离开区域时发送 _personZoneEnterExitEvent_ 事件，并提供方向信息以及已越过区域的边号。 当有人离开区域时发送 _personZoneDwellTimeEvent_，并提供方向信息以及人员在该区域内停留的毫秒数。  |
| cognitiveservices.vision.spatialanalysis-persondistance.livevideoanalytics | 跟踪人员违反距离规则的时间。 <br> 定期发送 _personDistanceEvent_，包含每次违反距离规则的位置。 |
| cognitiveservices.vision.spatialanalysis.livevideoanalytics | 可用于运行上述所有方案的常规操作。 当你想要在同一相机上运行多个方案或想要更有效地使用系统资源（例如 GPU）时，此选项更加有用。 |

实时视频分析操作还可用于 `.debug` 版本（例如 cognitiveservices.vision.spatialanalysis-personcount.livevideoanalytics.debug），它可以在处理视频帧将其可视化。 需要在主机计算机上运行 `xhost +`，才能实现视频帧和事件的可视化

> [!IMPORTANT]
> 计算机视觉 AI 模型通过围绕人体的边界框检测并定位视频素材和输出中的人员存在情况。 AI 模型不会尝试发现个人的身份或人口统计信息。

下面是每个空间分析操作所需的参数。

| 操作参数| 说明|
|---------|---------|
| Operation ID | 上表中的操作标识符。|
| enabled | 布尔值：true 或 false|
| VIDEO_URL| 照相机设备的 RTSP url（示例：`rtsp://username:password@url`）。 空间分析通过 RTSP、http 或 mp4 支持 H.264 编码流。 Video_URL 可以使用 AES 加密，作为经过模糊处理的 base64 字符串值提供，如果视频 URL 经过模糊处理，则 `KEY_ENV` 和 `IV_ENV` 需要作为环境变量提供。 有关生成密钥和加密的实用工具示例，请参阅[此处](/dotnet/api/system.security.cryptography.aesmanaged)。 |
| VIDEO_SOURCE_ID | 照相机设备或视频流的友好名称。 这将随事件 JSON 输出一起返回。|
| VIDEO_IS_LIVE| 对于照相机设备为 true，对于录制的视频为 false。|
| VIDEO_DECODE_GPU_INDEX| 用于解码视频帧的 GPU。 默认为 0。 在其他节点配置（如 `VICA_NODE_CONFIG`、`DETECTOR_NODE_CONFIG`）中应与 `gpu_index` 相同。|
| INPUT_VIDEO_WIDTH | 输入视频/流的帧宽度（例如 1920）。 这是可选字段，如果提供，帧将扩展到此维度，同时仍保留纵横比。|
| DETECTOR_NODE_CONFIG | JSON，指示要在其上运行检测程序节点的 GPU。 该值应采用以下格式：`"{ \"gpu_index\": 0 }",`|
| CAMERA_CONFIG | JSON，指示多个相机的校准相机参数。 如果你使用的技能需要校准并且你已有相机参数，可以使用此配置直接提供它们。 应采用以下格式：`"{ \"cameras\": [{\"source_id\": \"endcomputer.0.persondistancegraph.detector+end_computer1\", \"camera_height\": 13.105561256408691, \"camera_focal_length\": 297.60003662109375, \"camera_tiltup_angle\": 0.9738943576812744}] }"`其中 `source_id` 用于标识每个相机。 可以从我们发布的事件的 `source_info` 获取它。 仅当 `do_calibration=false` 在 `DETECTOR_NODE_CONFIG` 中时生效。|
| TRACKER_NODE_CONFIG | JSON，指示是否在跟踪器节点中计算速度。 该值应采用以下格式：`"{ \"enable_speed\": false }",`|
| SPACEANALYTICS_CONFIG | 区域和线的 JSON 配置，如下所述。|
| ENABLE_FACE_MASK_CLASSIFIER | `True` 表示启用检测视频流中佩戴口罩的人数，`False` 表示禁用该功能。 默认情况下，此功能处于禁用状态。 口罩检测要求输入视频宽度参数为 1920 `"INPUT_VIDEO_WIDTH": 1920`。 如果检测到的人没有面向照相机或距离太远，则不会返回口罩属性。 有关详细信息，请参阅[相机定位](spatial-analysis-camera-placement.md)指南 |

### <a name="detector-node-parameter-settings"></a>检测器节点参数设置
下面是所有空间分析操作的 DETECTOR_NODE_CONFIG 参数的示例。

```json
{
"gpu_index": 0,
"do_calibration": true,
"enable_recalibration": true,
"calibration_quality_check_frequency_seconds": 86400,
"calibration_quality_check_sample_collect_frequency_seconds": 300,
"calibration_quality_check_one_round_sample_collect_num": 10,
"calibration_quality_check_queue_max_size": 1000,
"calibration_event_frequency_seconds": -1
}
```

| 名称 | 类型| 说明|
|---------|---------|---------|
| `gpu_index` | string| 此操作将在其上运行的 GPU 索引。|
| `do_calibration` | string | 指示已启用校准。 若要使 **cognitiveservices account** 正常工作，`do_calibration` 必须为 true。 默认情况下，do_calibration 设置为 True。 |
| `enable_recalibration` | bool | 指示是否启用自动重新校准。 默认值为 `true`。|
| `calibration_quality_check_frequency_seconds` | int | 每次质量检查之间的最小秒数，用于确定是否需要重新校准。 默认值为 `86400`（24 小时）。 仅当 `enable_recalibration=True` 时使用。|
| `calibration_quality_check_sample_collect_frequency_seconds` | int | 收集新数据样本进行重新校准和质量检查之间相隔的最小秒数。 默认值为 `300`（5 分钟）。 仅当 `enable_recalibration=True` 时使用。|
| `calibration_quality_check_one_round_sample_collect_num` | int | 每轮样本收集要收集的最小新数据样本数。 默认值为 `10`。 仅当 `enable_recalibration=True` 时使用。|
| `calibration_quality_check_queue_max_size` | int | 校准相机模型时要存储的最大数据样本数。 默认值为 `1000`。 仅当 `enable_recalibration=True` 时使用。|
| `calibration_event_frequency_seconds` | int | 相机校准事件输出频率（秒）。 值为 `-1` 指示不应发送相机校准，除非相机校准信息已更改。 默认值为 `-1`。|
| `enable_breakpad`| bool | 指示是否要启用 breakpad，后者用于生成故障转储以便调试使用。 默认情况下，它为 `false`。 如果将其设置为 `true`，则还需要在容器 `createOptions` 的 `HostConfig` 部分添加 `"CapAdd": ["SYS_PTRACE"]`。 默认情况下，故障转储会上传到 [RealTimePersonTracking](https://appcenter.ms/orgs/Microsoft-Organization/apps/RealTimePersonTracking/crashes/errors?version=&appBuild=&period=last90Days&status=&errorType=all&sortCol=lastError&sortDir=desc) AppCenter 应用。如果要将故障转储上传到自己的 AppCenter 应用，可以使用自己应用的应用机密来替代环境变量 `RTPT_APPCENTER_APP_SECRET`。
| `enable_orientation` | bool | 指示是否要计算检测到的人物的方向。 `enable_orientation` 默认设置为 False。 |


### <a name="camera-calibration-output"></a>相机校准输出
下面是相机校准输出（如果已启用）的示例。 省略号表示列表中的更多相同类型的对象。
```
{
  "type": "cameraCalibrationEvent",
  "sourceInfo": {
    "id": "camera1",
    "timestamp": "2021-04-20T21:15:59.100Z",
    "width": 640,
    "height": 360,
    "frameId": 531,
    "cameraCalibrationInfo": {
      "status": "Calibrated",
      "cameraHeight": 13.294151306152344,
      "focalLength": 372.0000305175781,
      "tiltupAngle": 0.9581864476203918,
      "lastCalibratedTime": "2021-04-20T21:15:59.058"
    }
  },
  "zonePlacementInfo": {
    "optimalZoneRegion": {
      "type": "POLYGON",
       "points": [
        {
          "x": 0.8403755868544601,
          "y": 0.5515320334261838
        },
        {
          "x": 0.15805946791862285,
          "y": 0.5487465181058496
        },
        ...
      ],
      "name": "optimal_zone_region"
    },
    "fairZoneRegion": {
      "type": "POLYGON",
      "points": [
        {
          "x": 0.7871674491392802,
          "y": 0.7437325905292479
        },
        {
          "x": 0.22065727699530516,
          "y": 0.7325905292479109
        },
        ...
      ],
      "name": "fair_zone_region"
    },
    "uniformlySpacedPersonBoundingBoxes": [
      {
        "type": "RECTANGLE",
        "points": [
          {
            "x": 0.0297339593114241,
            "y": 0.0807799442896936
          },
          {
            "x": 0.10015649452269171,
            "y": 0.2757660167130919
          }
        ]
      },
      ...
    ],
    "personBoundingBoxGroundPoints": [
      {
        "x": -22.944068908691406,
        "y": 31.487680435180664
      },
      ...
    ]
  }
}
```

请参阅[空间分析操作输出](#spatial-analysis-operation-output)详细了解 `source_info`。

| ZonePlacementInfo 字段名称 | 类型| 说明|
|---------|---------|---------|
| `optimalZonePolygon` | object| 相机图像中的多边形，可在其中放置操作线或区域以获得最佳结果。 <br/> 每个值对表示多边形顶点的 x、y。 多边形表示跟踪或统计其中人数的区域，多边形点基于标准化坐标 (0-1)，其中左上角为 (0.0, 0.0)，右下角为 (1.0, 1.0)。|
| `fairZonePolygon` | object| 相机图像中的多边形，可在其中放置操作线或区域，以获得良好但可能并非最佳的结果。 <br/> 请参阅上面的 `optimalZonePolygon` 以深入了解其内容。 |
| `uniformlySpacedPersonBoundingBoxes` | list | 相机图像中在真实空间中均匀分布的人员的边框列表。 值基于规范化坐标 (0-1)。|
| `personBoundingBoxGroundPoints` | list | 地板平面上相对于相机的坐标列表。 每个坐标对应于 `uniformlySpacedPersonBoundingBoxes` 中具有相同索引的边框的右下角。 <br/> 请参阅 `centerGroundPoint`cognitiveservices.vision.spatialanalysis-persondistance AI 见解的 JSON 格式[部分下的 ](#json-format-for-cognitiveservicesvisionspatialanalysis-persondistance-ai-insights) 字段，以详细了解如何计算地平面上的坐标。 |

视频帧上可视化的区域放置信息输出示例：![区域放置信息可视化](./media/spatial-analysis/zone-placement-info-visualization.png)

区域放置信息提供配置建议，但为了获得最佳结果，仍必须遵循[相机配置](#camera-configuration)中的准则。

### <a name="speed-parameter-settings"></a>速度参数设置
可以通过跟踪器节点参数设置来配置速度计算。
```
{
"enable_speed": true,
}
```
| 名称 | 类型| 描述|
|---------|---------|---------|
| `enable_speed` | bool | 指示是否要计算检测到的人物的速度。 `enable_speed` 默认设置为 false。 强烈建议同时启用速度和方向，以获得最佳估计值 |


## <a name="spatial-analysis-operations-configuration-and-output"></a>空间分析操作配置和输出
### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-personcount"></a>cognitiveservices.vision.spatialanalysis-personcount 的区域配置

 下面是一个配置区域的 SPACEANALYTICS_CONFIG 参数的 JSON 输入示例。 你可以为此操作配置多个区域。

```json
{
"zones":[{
       "name": "lobbycamera",
       "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
       "events":[{
              "type": "count",
              "config":{
                     "trigger": "event",
            "threshold": 16.00,
            "focus": "footprint"
      }
       }]
}
```

| 名称 | 类型| 说明|
|---------|---------|---------|
| `zones` | list| 区域列表。 |
| `name` | string| 此区域的友好名称。|
| `polygon` | list| 每个值对表示多边形顶点的 x、y。 多边形表示跟踪或统计其中人数的区域，多边形点基于标准化坐标 (0-1)，其中左上角为 (0.0, 0.0)，右下角为 (1.0, 1.0)。   
| `threshold` | FLOAT| 当人员在区域内的像素数大于此像素数时，将发出事件。 |
| `type` | string| 对于 **cognitiveservices.vision.spatialanalysis-personcount**，此值应为 `count`。|
| `trigger` | string| 发送事件的触发器的类型。 在计数发生更改时，支持的值为 `event`，表示发送事件；或者为 `interval`，表示定期发送事件，而不考虑计数是否发生更改。
| `output_frequency` | int | 发出事件的比率。 当 `output_frequency` = X 时，表示每 X 个事件发出一次，例如 `output_frequency` = 2，表示每隔一个事件输出一次。 `output_frequency` 适用于 `event` 和 `interval`。 |
| `focus` | string| 人员边界框内的点位置用于计算事件数。 焦点的值可以是 `footprint`（人员足迹）、`bottom_center`（人员边界框的底部中心）、`center`（人员边界框的中心）。|

### <a name="line-configuration-for-cognitiveservicesvisionspatialanalysis-personcrossingline"></a>cognitiveservices.vision.spatialanalysis-personcrossingline 的线配置

下面是一个配置线的 SPACEANALYTICS_CONFIG 参数的 JSON 输入示例。 你可以为此操作配置多条交叉线。

```json
{
   "lines": [
       {
           "name": "doorcamera",
           "line": {
               "start": {
                   "x": 0,
                   "y": 0.5
               },
               "end": {
                   "x": 1,
                   "y": 0.5
               }
           },
           "events": [
               {
                   "type": "linecrossing",
                   "config": {
                       "trigger": "event",
                       "threshold": 16.00,
                       "focus": "footprint"
                   }
               }
           ]
       }
   ]
}
```

| 名称 | 类型| 说明|
|---------|---------|---------|
| `lines` | list| 线列表。|
| `name` | string| 此线的友好名称。|
| `line` | list| 线的定义。 这是一条方向线，让你可了解“进入”与“离开”。|
| `start` | 值对| 线条起点的 x、y 坐标。 Float 值表示顶点相对于左上角的位置。 要计算 x、y 的绝对值，可以用这些值乘以帧大小。 |
| `end` | 值对| 线条终结点的 x、y 坐标。 Float 值表示顶点相对于左上角的位置。 要计算 x、y 的绝对值，可以用这些值乘以帧大小。 |
| `threshold` | FLOAT| 当人员在区域内的像素数大于此像素数时，将发出事件。 默认值为 16。 这是实现最大准确性的推荐值。 |
| `type` | string| 对于 **cognitiveservices.vision.spatialanalysis-personcrossingline**，此值应为 `linecrossing`。|
|`trigger`|string|发送事件的触发器的类型。<br>支持的值：“事件”：当有人越线时触发。|
| `focus` | string| 人员边界框内的点位置用于计算事件数。 焦点的值可以是 `footprint`（人员足迹）、`bottom_center`（人员边界框的底部中心）、`center`（人员边界框的中心）。 默认值为 footprint。|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-personcrossingpolygon"></a>cognitiveservices.vision.spatialanalysis-personcrossingpolygon 的区域配置

下面是一个配置区域的 SPACEANALYTICS_CONFIG 参数的 JSON 输入示例。 你可以为此操作配置多个区域。

 ```json
{
"zones":[
   {
       "name": "queuecamera",
       "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
       "events":[{
           "type": "zonecrossing",
           "config":{
               "trigger": "event",
               "threshold": 48.00,
               "focus": "footprint"
               }
           }]
   },
   {
       "name": "queuecamera1",
       "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
       "events":[{
           "type": "zonedwelltime",
           "config":{
               "trigger": "event",
               "threshold": 16.00,
               "focus": "footprint"
               }
           }]
   }]
}
```

| 名称 | 类型| 说明|
|---------|---------|---------|
| `zones` | list| 区域列表。 |
| `name` | string| 此区域的友好名称。|
| `polygon` | list| 每个值对表示多边形顶点的 x、y。 多边形表示跟踪或统计其中人数的区域。 Float 值表示顶点相对于左上角的位置。 要计算 x、y 的绝对值，可以用这些值乘以帧大小。 
| `target_side` | int| 指定由 `polygon` 定义的区域的一侧，以测量人物在该区域中面向该侧的时长。 “dwellTimeForTargetSide”将输出该估计的时间。 每一侧都是表示区域的多边形两个顶点之间的编号边界。 例如，多边形前两个顶点之间的边缘表示第一侧，'side'=1。 `target_side` 的值介于 `[0,N-1]` 之间，其中 `N` 是 `polygon` 的边数。 这是一个可选字段。  |
| `threshold` | FLOAT| 当人员在区域内的像素数大于此像素数时，将发出事件。 当类型为 zonecrossing 时，默认值为 48；当时间为 DwellTime 时，默认值为 16。 这些都是实现最大准确性的推荐值。  |
| `type` | string| 对于 **cognitiveservices.vision.spatialanalysis-personcrossingpolygon**，此值应为 `zonecrossing` 或 `zonedwelltime`。|
| `trigger`|string|发送事件的触发器的类型<br>支持的值：“事件”：有人进入或离开区域时触发。|
| `focus` | string| 人员边界框内的点位置用于计算事件数。 焦点的值可以是 `footprint`（人员足迹）、`bottom_center`（人员边界框的底部中心）、`center`（人员边界框的中心）。 默认值为 footprint。|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-persondistance"></a>cognitiveservices.vision.spatialanalysis-persondistance 的区域配置

下面是一个为 **cognitiveservices.vision.spatialanalysis-persondistance** 配置区域的 SPACEANALYTICS_CONFIG 参数的 JSON 输入示例。 你可以为此操作配置多个区域。

```json
{
"zones":[{
   "name": "lobbycamera",
   "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
   "events":[{
       "type": "persondistance",
       "config":{
           "trigger": "event",
           "output_frequency":1,
           "minimum_distance_threshold":6.0,
           "maximum_distance_threshold":35.0,
        "aggregation_method": "average"
           "threshold": 16.00,
           "focus": "footprint"
                   }
          }]
   }]
}
```

| 名称 | 类型| 说明|
|---------|---------|---------|
| `zones` | list| 区域列表。 |
| `name` | string| 此区域的友好名称。|
| `polygon` | list| 每个值对表示多边形顶点的 x、y。 多边形表示统计其中人数的区域，并度量人员之间的距离。 Float 值表示顶点相对于左上角的位置。 要计算 x、y 的绝对值，可以用这些值乘以帧大小。 
| `threshold` | FLOAT| 当人员在区域内的像素数大于此像素数时，将发出事件。 |
| `type` | string| 对于 **cognitiveservices.vision.spatialanalysis-persondistance**，此值应为 `people_distance`。|
| `trigger` | string| 发送事件的触发器的类型。 在计数发生更改时，支持的值为 `event`，表示发送事件；或者为 `interval`，表示定期发送事件，而不考虑计数是否发生更改。
| `output_frequency` | int | 发出事件的比率。 当 `output_frequency` = X 时，表示每 X 个事件发出一次，例如 `output_frequency` = 2，表示每隔一个事件输出一次。 `output_frequency` 适用于 `event` 和 `interval`。|
| `minimum_distance_threshold` | FLOAT| 以英尺为单位的距离，当人员之间小于该间距时，将触发“TooClose”事件。|
| `maximum_distance_threshold` | FLOAT| 以英尺为单位的距离，当人员之间大于该间距时，将触发“TooFar”事件。|
| `aggregation_method` | string| 聚合 persondistance 结果的方法。 aggregation_method 适用于 `mode` 和 `average`。|
| `focus` | string| 人员边界框内的点位置用于计算事件数。 焦点的值可以是 `footprint`（人员足迹）、`bottom_center`（人员边界框的底部中心）、`center`（人员边界框的中心）。|

### <a name="configuration-for-cognitiveservicesvisionspatialanalysis"></a>cognitiveservices.vision.spatialanalysis 的配置
下面是一个为 cognitiveservices.vision.spatialanalysis 配置线和区域的 SPACEANALYTICS_CONFIG 参数的 JSON 输入示例。 可为此操作配置多个线/区域，每个线/区域可以有不同的事件。

 ```
{
  "lines": [
    {
      "name": "doorcamera",
      "line": {
        "start": {
          "x": 0,
          "y": 0.5
        },
        "end": {
          "x": 1,
          "y": 0.5
        }
      },
      "events": [
        {
          "type": "linecrossing",
          "config": {
            "trigger": "event",
            "threshold": 16.00,
            "focus": "footprint"
          }
        }
      ]
    }
  ],
  "zones": [
    {
      "name": "lobbycamera",
      "polygon": [[0.3, 0.3],[0.3, 0.9],[0.6, 0.9],[0.6, 0.3],[0.3, 0.3]],
      "events": [
        {
          "type": "persondistance",
          "config": {
            "trigger": "event",
            "output_frequency": 1,
            "minimum_distance_threshold": 6.0,
            "maximum_distance_threshold": 35.0,
            "threshold": 16.00,
            "focus": "footprint"
          }
        },
        {
          "type": "count",
          "config": {
            "trigger": "event",
            "output_frequency": 1,
            "threshold": 16.00,
            "focus": "footprint"
          }
        },
        {
          "type": "zonecrossing",
          "config": {
            "threshold": 48.00,
            "focus": "footprint"
          }
        },
        {
          "type": "zonedwelltime",
          "config": {
            "threshold": 16.00,
            "focus": "footprint"
          }
        }
      ]
    }
  ]
}
```
## <a name="camera-configuration"></a>相机配置

请参阅[相机定位](spatial-analysis-camera-placement.md)指导原则，详细了解如何配置区域和线。

## <a name="spatial-analysis-operation-output"></a>空间分析操作输出

每个操作的事件都将以 JSON 格式发送到 Azure IoT 中心。

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcount-ai-insights"></a>cognitiveservices.vision.spatialanalysis-personcount AI 见解的 JSON 格式

此操作输出事件的 JSON 示例。

```json
{
    "events": [
        {
            "id": "b013c2059577418caa826844223bb50b",
            "type": "personCountEvent",
            "detectionIds": [
                "bc796b0fc2534bc59f13138af3dd7027",
                "60add228e5274158897c135905b5a019"
            ],
            "properties": {
                "personCount": 2
            },
            "zone": "lobbycamera",
            "trigger": "event"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:06:57.224Z",
        "width": 608,
        "height": 342,
        "frameId": "1400",
        "cameraCalibrationInfo": {
            "status": "Calibrated",
            "cameraHeight": 10.306597709655762,
            "focalLength": 385.3199462890625,
            "tiltupAngle": 1.0969393253326416
        },
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "bc796b0fc2534bc59f13138af3dd7027",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.612683747944079,
                        "y": 0.25340268765276636
                    },
                    {
                        "x": 0.7185954043739721,
                        "y": 0.6425260577285499
                    }
                ]
            },
            "confidence": 0.9559211134910583,
            "centerGroundPoint": {
                "x": 0.0,
                "y": 0.0
            },
            "metadata": {
            "attributes": {
                "face_mask": 0.99
            }
        }
        },
        {
            "type": "person",
            "id": "60add228e5274158897c135905b5a019",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.22326200886776573,
                        "y": 0.17830915618361087
                    },
                    {
                        "x": 0.34922296122500773,
                        "y": 0.6297955429344847
                    }
                ]
            },
            "confidence": 0.9389744400978088,
            "centerGroundPoint": {
                "x": 0.0,
                "y": 0.0
            },
            "metadata":{
            "attributes": {
            "face_nomask": 0.99
            }
            }
       }
    ],
    "schemaVersion": "1.0"
}
```

| 事件字段名称 | 类型| 说明|
|---------|---------|---------|
| `id` | string| 事件 ID|
| `type` | string| 事件类型|
| `detectionsId` | array| 触发了此事件的人员检测的唯一标识符数组，大小为 1|
| `properties` | collection| 值的集合|
| `trackinId` | string| 检测到的人员的唯一标识符|
| `zone` | string | 多边形的“名称”字段，多边形代表已越过区域|
| `trigger` | string| 触发器类型为“事件”或“间隔”，具体取决于 SPACEANALYTICS_CONFIG 中 `trigger` 的值|

| 检测字段名称 | 类型| 说明|
|---------|---------|---------|
| `id` | string| 检测 ID|
| `type` | string| 检测类型|
| `region` | collection| 值的集合|
| `type` | string| 区域类型|
| `points` | collection| 当区域类型为矩形时，表示左上角和右下角的点 |
| `confidence` | FLOAT| 算法置信度|
| `face_mask` | FLOAT | 属性置信度值在 (0-1) 范围内，表示检测到的人员戴着口罩 |
| `face_nomask` | FLOAT | 属性置信度值在 (0-1) 范围内，表示检测到的人员 **未** 佩戴口罩 |

| SourceInfo 字段名称 | 类型| 说明|
|---------|---------|---------|
| `id` | string| 相机 ID|
| `timestamp` | date| 发出 JSON 有效负载时的 UTC 日期|
| `width` | int | 视频帧宽度|
| `height` | int | 视频帧高度|
| `frameId` | int | 帧标识符|
| `cameraCallibrationInfo` | collection | 值的集合|
| `status` | string | 校准的状态，格式为 `state[;progress description]`。 状态可以为 `Calibrating`、`Recalibrating`（如果已启用重新校准）或 `Calibrated`。 进度说明部分仅在处于 `Calibrating` 和 `Recalibrating` 状态时有效，后者用于显示当前校准过程的进度。|
| `cameraHeight` | FLOAT | 相机高于地面的高度，以英尺为单位。 该信息根据自动校准推断得出。 |
| `focalLength` | FLOAT | 照相机的焦距，以像素为单位。 该信息根据自动校准推断得出。 |
| `tiltUpAngle` | FLOAT | 相机自垂直状态的倾斜角度。 该信息根据自动校准推断得出。|


### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcrossingline-ai-insights"></a>cognitiveservices.vision.spatialanalysis-personcrossingline AI 见解的 JSON 格式

此操作输出检测的 JSON 示例。

```json
{
    "events": [
        {
            "id": "3733eb36935e4d73800a9cf36185d5a2",
            "type": "personLineEvent",
            "detectionIds": [
                "90d55bfc64c54bfd98226697ad8445ca"
            ],
            "properties": {
                "trackingId": "90d55bfc64c54bfd98226697ad8445ca",
                "status": "CrossLeft"
            },
            "zone": "doorcamera"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:06:53.261Z",
        "width": 608,
        "height": 342,
        "frameId": "1340",
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "90d55bfc64c54bfd98226697ad8445ca",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.491627341822574,
                        "y": 0.2385801348769874
                    },
                    {
                        "x": 0.588894994635331,
                        "y": 0.6395559924387793
                    }
                ]
            },
            "confidence": 0.9005028605461121,
            "metadata": {
            "attributes": {
                "face_mask": 0.99
            }
        }
        }
    ],
    "schemaVersion": "1.0"
}
```
| 事件字段名称 | 类型| 说明|
|---------|---------|---------|
| `id` | string| 事件 ID|
| `type` | string| 事件类型|
| `detectionsId` | array| 触发了此事件的人员检测的唯一标识符数组，大小为 1|
| `properties` | collection| 值的集合|
| `trackinId` | string| 检测到的人员的唯一标识符|
| `status` | string| 越线方向：“CrossLeft”或“CrossRight”。 方向是基于站在线的“起点”朝向“终点”的想象得出的。 CrossRight 表示从左侧跨越到右侧。 CrossLeft 表示从右侧跨越到左侧。|
| `orientationDirection` | 字符串| 检测到的人物在跨过线后的方向。 此值可以是 "Left"、"Right" 或 "Straight"。 如果 `DETECTOR_NODE_CONFIG` 在中将 `enable_orientation` 设置为 `True`，则此值为输出 |
| `zone` | 字符串 | 跨越线的“名称”字段|

| 检测字段名称 | 类型| 说明|
|---------|---------|---------|
| `id` | string| 检测 ID|
| `type` | string| 检测类型|
| `region` | collection| 值的集合|
| `type` | string| 区域类型|
| `points` | collection| 当区域类型为矩形时，表示左上角和右下角的点 |
| `groundOrientationAngle` | FLOAT| 人物的方向在推理的地面上的顺时针弧度角度 |
| `mappedImageOrientation` | FLOAT| 人物的方向在 2D 图像空间上的顺时针弧度角度 |
| `speed` | FLOAT| 检测到的人物的估计速度。 单位为 `foot per second (ft/s)`|
| `confidence` | FLOAT| 算法置信度|
| `face_mask` | FLOAT | 属性置信度值在 (0-1) 范围内，表示检测到的人员戴着口罩 |
| `face_nomask` | FLOAT | 属性置信度值在 (0-1) 范围内，表示检测到的人员 **未** 佩戴口罩 |

| SourceInfo 字段名称 | 类型| 说明|
|---------|---------|---------|
| `id` | string| 相机 ID|
| `timestamp` | date| 发出 JSON 有效负载时的 UTC 日期|
| `width` | int | 视频帧宽度|
| `height` | int | 视频帧高度|
| `frameId` | int | 帧标识符|


> [!IMPORTANT]
> AI 模型可检测人员，不考虑人员是面向还是远离照相机。 AI 模型不会运行面部识别，也不会发出任何生物识别信息。 

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcrossingpolygon-ai-insights"></a>cognitiveservices.vision.spatialanalysis-personcrossingpolygon AI 见解的 JSON 格式

此操作使用 `zonecrossing` 类型 SPACEANALYTICS_CONFIG 输出检测的 JSON 示例。

```json
{
    "events": [
        {
            "id": "f095d6fe8cfb4ffaa8c934882fb257a5",
            "type": "personZoneEnterExitEvent",
            "detectionIds": [
                "afcc2e2a32a6480288e24381f9c5d00e"
            ],
            "properties": {
                "trackingId": "afcc2e2a32a6480288e24381f9c5d00e",
                "status": "Enter",
                "side": "1"
            },
            "zone": "queuecamera"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:15:09.680Z",
        "width": 608,
        "height": 342,
        "frameId": "428",
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "afcc2e2a32a6480288e24381f9c5d00e",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.8135572734631991,
                        "y": 0.6653949670624315
                    },
                    {
                        "x": 0.9937645761590255,
                        "y": 0.9925406829655519
                    }
                ]
            },
            "confidence": 0.6267998814582825,
        "metadata": {
        "attributes": {
        "face_mask": 0.99
        }
        }
           
        }
    ],
    "schemaVersion": "1.0"
}
```

此操作使用 `zonedwelltime` 类型 SPACEANALYTICS_CONFIG 输出检测的 JSON 示例。

```json
{
    "events": [
        {
            "id": "f095d6fe8cfb4ffaa8c934882fb257a5",
            "type": "personZoneDwellTimeEvent",
            "detectionIds": [
                "afcc2e2a32a6480288e24381f9c5d00e"
            ],
            "properties": {
                "trackingId": "afcc2e2a32a6480288e24381f9c5d00e",
                "status": "Exit",
                "side": "1",
                      "dwellTime": 7132.0,
                      "dwellFrames": 20            
            },
            "zone": "queuecamera"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:15:09.680Z",
        "width": 608,
        "height": 342,
        "frameId": "428",
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "afcc2e2a32a6480288e24381f9c5d00e",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.8135572734631991,
                        "y": 0.6653949670624315
                    },
                    {
                        "x": 0.9937645761590255,
                        "y": 0.9925406829655519
                    }
                ]
            },
            "confidence": 0.6267998814582825,
            "metadataType": "",
             "metadata": { 
                     "groundOrientationAngle": 1.2,
                     "mappedImageOrientation": 0.3,
                     "speed": 1.2
               },
        }
    ],
    "schemaVersion": "1.0"
}
```

| 事件字段名称 | 类型| 说明|
|---------|---------|---------|
| `id` | string| 事件 ID|
| `type` | string| 事件类型。 该值可以是 _personZoneDwellTimeEvent_ 或 _personZoneEnterExitEvent_|
| `detectionsId` | array| 触发了此事件的人员检测的唯一标识符数组，大小为 1|
| `properties` | collection| 值的集合|
| `trackinId` | string| 检测到的人员的唯一标识符|
| `status` | string| 越过多边形的方向：“进入”或“离开”|
| `side` | int| 人员所跨过的多边形的边号。 每一侧都是表示区域的多边形两个顶点之间的编号边界。 多边形前两个顶点之间的边缘表示第一侧。 当事件由于遮挡而不与特定一侧关联时，“Side”为空。 例如，当某人消失，但未看到他（她）跨越区域的一侧时，则表示发生了 exit（出去）事件；当某个出现在区域中，但未看到他（她）跨越一侧时，则表示发生了 enter（进入）事件。|
| `dwellTime` | FLOAT | 表示人员在区域中停留时间的毫秒数。 当事件类型为 personZoneDwellTimeEvent 时，提供此字段|
| `dwellFrames` | int | 人物在该区域中停留的帧数。 当事件类型为 personZoneDwellTimeEvent 时，提供此字段|
| `dwellTimeForTargetSide` | FLOAT | 表示人物在该区域中停留且面向 `target_side` 的时间的毫秒数。 当 `DETECTOR_NODE_CONFIG ` 中的 `True` 为 `enable_orientation`，并且在 `SPACEANALYTICS_CONFIG` 中设置了 `target_side` 的值时，将提供此字段|
| `avgSpeed` | FLOAT| 人物在该区域中的平均速度。 单位为 `foot per second (ft/s)`|
| `minSpeed` | FLOAT| 人物在该区域中的最低速度。 单位为 `foot per second (ft/s)`|
| `zone` | 字符串 | 多边形的“名称”字段，多边形代表已越过区域|

| 检测字段名称 | 类型| 说明|
|---------|---------|---------|
| `id` | string| 检测 ID|
| `type` | string| 检测类型|
| `region` | collection| 值的集合|
| `type` | string| 区域类型|
| `points` | collection| 当区域类型为矩形时，表示左上角和右下角的点 |
| `groundOrientationAngle` | FLOAT| 人物的方向在推理的地面上的顺时针弧度角度 |
| `mappedImageOrientation` | FLOAT| 人物的方向在 2D 图像空间上的顺时针弧度角度 |
| `speed` | FLOAT| 检测到的人物的估计速度。 单位为 `foot per second (ft/s)`|
| `confidence` | FLOAT| 算法置信度|
| `face_mask` | FLOAT | 属性置信度值在 (0-1) 范围内，表示检测到的人员戴着口罩 |
| `face_nomask` | FLOAT | 属性置信度值在 (0-1) 范围内，表示检测到的人员 **未** 佩戴口罩 |

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-persondistance-ai-insights"></a>cognitiveservices.vision.spatialanalysis-persondistance AI 见解的 JSON 格式

此操作输出检测的 JSON 示例。

```json
{
    "events": [
        {
            "id": "9c15619926ef417aa93c1faf00717d36",
            "type": "personDistanceEvent",
            "detectionIds": [
                "9037c65fa3b74070869ee5110fcd23ca",
                "7ad7f43fd1a64971ae1a30dbeeffc38a"
            ],
            "properties": {
                "personCount": 5,
                "averageDistance": 20.807043981552123,
                "minimumDistanceThreshold": 6.0,
                "maximumDistanceThreshold": "Infinity",
                "eventName": "TooClose",
                "distanceViolationPersonCount": 2
            },
            "zone": "lobbycamera",
            "trigger": "event"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:17:25.309Z",
        "width": 608,
        "height": 342,
        "frameId": "1199",
        "cameraCalibrationInfo": {
            "status": "Calibrated",
            "cameraHeight": 12.9940824508667,
            "focalLength": 401.2800598144531,
            "tiltupAngle": 1.057669997215271
        },
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "9037c65fa3b74070869ee5110fcd23ca",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.39988183975219727,
                        "y": 0.2719132942065858
                    },
                    {
                        "x": 0.5051516984638414,
                        "y": 0.6488402517218339
                    }
                ]
            },
            "confidence": 0.948630690574646,
            "centerGroundPoint": {
                "x": -1.4638760089874268,
                "y": 18.29732322692871
            },
            "metadataType": ""
        },
        {
            "type": "person",
            "id": "7ad7f43fd1a64971ae1a30dbeeffc38a",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.5200299714740954,
                        "y": 0.2875368218672903
                    },
                    {
                        "x": 0.6457497446160567,
                        "y": 0.6183311060855263
                    }
                ]
            },
            "confidence": 0.8235412240028381,
            "centerGroundPoint": {
                "x": 2.6310102939605713,
                "y": 18.635927200317383
            },
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```

| 事件字段名称 | 类型| 说明|
|---------|---------|---------|
| `id` | string| 事件 ID|
| `type` | string| 事件类型|
| `detectionsId` | array| 触发了此事件的人员检测的唯一标识符数组，大小为 1|
| `properties` | collection| 值的集合|
| `personCount` | int| 发出事件时检测到的人员数|
| `averageDistance` | FLOAT| 检测到的所有人员之间的平均距离，以英尺为单位|
| `minimumDistanceThreshold` | FLOAT| 以英尺为单位的距离，当人员之间小于该间距时，将触发“TooClose”事件。|
| `maximumDistanceThreshold` | FLOAT| 以英尺为单位的距离，当人员之间大于该间距时，将触发“TooFar”事件。|
| `eventName` | string| 违反 `minimumDistanceThreshold` 时，事件名称为 `TooClose`；违反 `maximumDistanceThreshold` 时为 `TooFar`；自动校准尚未完成时为 `unknown`|
| `distanceViolationPersonCount` | int| 违反 `minimumDistanceThreshold` 或 `maximumDistanceThreshold` 时检测到的人员数|
| `zone` | string | 多边形的“名称”字段，多边形表示监控人员之间距离的区域|
| `trigger` | string| 触发器类型为“事件”或“间隔”，具体取决于 SPACEANALYTICS_CONFIG 中 `trigger` 的值|

| 检测字段名称 | 类型| 说明|
|---------|---------|---------|
| `id` | string| 检测 ID|
| `type` | string| 检测类型|
| `region` | collection| 值的集合|
| `type` | string| 区域类型|
| `points` | collection| 当区域类型为矩形时，表示左上角和右下角的点 |
| `confidence` | FLOAT| 算法置信度|
| `centerGroundPoint` | 2 个 float 值| `x`、`y` 值，包含推断的人员在地面上位置（以英尺为单位）的坐标。 `x` 和 `y` 是地平面上的坐标，假设地面为水平。 照相机的位置为原点。 |

计算 `centerGroundPoint` 时，`x` 是沿与相机图像平面垂直的线从相机到人员之间的距离。 `y` 是沿与相机图像平面平行的线从相机到人员之间的距离。 

![地面中心点示例](./media/spatial-analysis/x-y-chart.png) 

在本示例中，`centerGroundPoint` 状态为 `{x: 4, y: 5}`。 这表示，有一个人在距离相机 4 英尺、距离右侧 5 英尺的位置从上向下查看房间。


| SourceInfo 字段名称 | 类型| 说明|
|---------|---------|---------|
| `id` | string| 相机 ID|
| `timestamp` | date| 发出 JSON 有效负载时的 UTC 日期|
| `width` | int | 视频帧宽度|
| `height` | int | 视频帧高度|
| `frameId` | int | 帧标识符|
| `cameraCallibrationInfo` | collection | 值的集合|
| `status` | string | 校准的状态，格式为 `state[;progress description]`。 状态可以为 `Calibrating`、`Recalibrating`（如果已启用重新校准）或 `Calibrated`。 进度说明部分仅在处于 `Calibrating` 和 `Recalibrating` 状态时有效，后者用于显示当前校准过程的进度。|
| `cameraHeight` | FLOAT | 相机高于地面的高度，以英尺为单位。 该信息根据自动校准推断得出。 |
| `focalLength` | FLOAT | 照相机的焦距，以像素为单位。 该信息根据自动校准推断得出。 |
| `tiltUpAngle` | FLOAT | 相机自垂直状态的倾斜角度。 该信息根据自动校准推断得出。|

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-ai-insights"></a>cognitiveservices.vision.spatialanalysis AI 见解的 JSON 格式

此操作的输出取决于配置的 `events`，例如，如果为此操作配置了 `zonecrossing` 事件，则输出将与 `cognitiveservices.vision.spatialanalysis-personcrossingpolygon` 相同。

## <a name="use-the-output-generated-by-the-container"></a>使用容器生成的输出

你可能想要将空间分析检测或事件集成到应用程序中。 下面这些想法值得考虑： 

* 使用适用于所选编程语言的 Azure 事件中心 SDK 连接到 Azure IoT 中心终结点，并接收事件。 有关详细信息，请参阅[从内置终结点读取设备到云的消息](../../iot-hub/iot-hub-devguide-messages-read-builtin.md)。 
* 在 Azure IoT 中心设置“消息路由”，以将事件发送到其他终结点，或者将事件保存到数据存储。 有关详细信息，请参阅 [IoT 中心消息路由](../../iot-hub/iot-hub-devguide-messages-d2c.md)。 
* 设置 Azure 流分析作业，以实时处理事件，并创建可视化效果。 

## <a name="deploying-spatial-analysis-operations-at-scale-multiple-cameras"></a>大规模部署空间分析操作（多个相机）

为了获得最佳性能和充分利用 GPU，可以使用图形实例在多个相机上部署任何空间分析操作。 下面是在十五个相机上运行 `cognitiveservices.vision.spatialanalysis-personcrossingline` 操作的示例。

```json
  "properties.desired": {
      "globalSettings": {
          "PlatformTelemetryEnabled": false,
          "CustomerTelemetryEnabled": true
      },
      "graphs": {
        "personzonelinecrossing": {
        "operationId": "cognitiveservices.vision.spatialanalysis-personcrossingline",
        "version": 1,
        "enabled": true,
        "sharedNodes": {
            "shared_detector0": {
                "node": "PersonCrossingLineGraph.detector",
                "parameters": {
                    "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0, \"batch_size\": 7, \"do_calibration\": true}",
                }
            },
            "shared_detector1": {
                "node": "PersonCrossingLineGraph.detector",
                "parameters": {
                    "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0, \"batch_size\": 8, \"do_calibration\": true}",
                }
            }
        },
        "parameters": {
            "VIDEO_DECODE_GPU_INDEX": 0,
            "VIDEO_IS_LIVE": true
        },
        "instances": {
            "1": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 1>",
                    "VIDEO_SOURCE_ID": "camera 1",
                    "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"queue\",\"polygon\":[[0,0],[1,0],[0,1],[1,1],[0,0]]}]}"
                }
            },
            "2": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 2>",
                    "VIDEO_SOURCE_ID": "camera 2",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "3": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 3>",
                    "VIDEO_SOURCE_ID": "camera 3",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "4": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 4>",
                    "VIDEO_SOURCE_ID": "camera 4",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "5": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 5>",
                    "VIDEO_SOURCE_ID": "camera 5",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "6": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 6>",
                    "VIDEO_SOURCE_ID": "camera 6",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "7": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 7>",
                    "VIDEO_SOURCE_ID": "camera 7",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "8": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 8>",
                    "VIDEO_SOURCE_ID": "camera 8",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "9": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 9>",
                    "VIDEO_SOURCE_ID": "camera 9",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "10": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 10>",
                    "VIDEO_SOURCE_ID": "camera 10",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "11": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 11>",
                    "VIDEO_SOURCE_ID": "camera 11",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "12": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 12>",
                    "VIDEO_SOURCE_ID": "camera 12",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "13": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 13>",
                    "VIDEO_SOURCE_ID": "camera 13",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "14": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 14>",
                    "VIDEO_SOURCE_ID": "camera 14",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "15": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 15>",
                    "VIDEO_SOURCE_ID": "camera 15",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            }
          }
        },
      }
  }
  ```
| 名称 | 类型| 说明|
|---------|---------|---------|
| `batch_size` | int | 如果所有相机都具有相同的分辨率，则将 `batch_size` 设置为该操作中要使用的相机数，或者将 `batch_size` 设置为 1，或将其保留为默认值 (1)，这表示不支持批处理。 |

## <a name="next-steps"></a>后续步骤

* [部署人员计数 Web 应用程序](spatial-analysis-web-app.md)
* [日志记录和故障排除](spatial-analysis-logging.md)
* [相机放置指南](spatial-analysis-camera-placement.md)
* [区域和线条放置指南](spatial-analysis-zone-line-placement.md)
