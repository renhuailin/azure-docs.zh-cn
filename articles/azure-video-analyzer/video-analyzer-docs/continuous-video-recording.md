---
title: 从边缘连续录制视频 - Azure 视频分析器
description: 连续视频录制 (CVR) 是指从实时视频源连续录制视频的过程。 本主题讨论 CVR 是什么，以及如何将其与 Azure 视频分析器配合使用。
ms.service: azure-video-analyzer
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: a7909fab420302fe8246e8f1ce2cd050d1f854f8
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114603981"
---
# <a name="continuous-video-recording"></a>连续视频录制    

连续视频录制 (CVR) 是指从视频源连续录制视频的过程。 Azure 视频分析器支持通过包含 RTSP 源节点和视频接收器节点的视频处理[管道拓扑](pipeline.md)，从 CCTV 摄像头全天候连续录制视频。 下图显示了此类管道的图形表示形式。 可以在本[文档](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/cvr-video-sink/topology.json)中找到拓扑的 JSON 表示形式。 可以使用此类拓扑来创建任意长度的录制内容（长达数年的内容），并且可以基于 UTC 时间浏览录制内容。  

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/continuous-video-recording/continuous-video-recording-overview.svg" alt-text="连续视频录制":::

上述管道拓扑的实例可以在边缘设备上运行，视频接收器将录制到视频分析器[视频资源](terminology.md#video)。 只要管道保持激活状态，就会继续录制视频。 由于视频录制为视频资源，因此可以使用视频分析器的流式传输功能来播放视频。 有关更多详细信息，请参阅[播放视频录制内容](playback-recordings-how-to.md)。

## <a name="suggested-pre-reading"></a>建议的读前准备  

建议先阅读以下文章，然后再继续操作。

* [管道拓扑概念](pipeline.md)
* [视频录制概念](video-recording.md) 
 
## <a name="resilient-recording"></a>可复原的录制

边缘设备偶尔可能会与云断开连接，或者可能会出现可用带宽下降的情况，在这些情况下，视频分析器也能正常运行。 为此，来自源的视频将录制到本地缓存中，并定期自动与视频资源同步。 如果检查[管道拓扑](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/cvr-video-sink/topology.json)，将看到定义了以下属性：

```
"segmentLength": "PT30S",
"localMediaCacheMaximumSizeMiB": "2048",
"localMediaCachePath": "/var/lib/videoanalyzer/tmp/",
```

后两个属性与可复原的录制相关（两者也都是视频接收器节点的必需属性）。 `localMediaCachePath` 属性可指示视频接收器在上传到云之前，使用该文件夹路径来缓存媒体数据。 可以在[此](../../iot-edge/how-to-access-host-storage-from-module.md)文章中了解边缘模块如何利用设备的本地存储。 `localMediaCacheMaximumSizeMiB` 属性用于定义视频接收器可用作缓存的磁盘空间量（1 MiB = 1024 * 1024 字节）。 

如果边缘模块长时间断开连接，并且缓存文件夹中存储的内容达到 `localMediaCacheMaximumSizeMiB` 值，则视频接收器将开始从缓存中丢弃数据，从最早的数据开始。 例如，如果设备在上午 10 点断开连接，而缓存在下午 6 点达到最大限制，则视频接收器将开始删除在上午 10 点录制的数据。 

当网络连接恢复时，视频接收器将开始从缓存上传数据，也是从最早的数据开始。 在上面的示例中，假设在连接恢复前（例如在下午 6:02）必须从缓存中丢弃 5 分钟的视频，则视频接收器将从上午 10:05 标记处开始上传数据。

录制内容可能会出现间断，例如，如果出于任何原因重启管道。 还可以停止管道并稍后重启，只要摄像头设置未更改，就可以继续录制到同一个视频分析器视频资源。

## <a name="segmented-recording"></a>分段录制  

上面显示的 `segmentLength` 属性有助于控制与将数据写入到正在录制视频资源的存储帐户相关的写入事务成本。 例如，如果将上传时间从 30 秒增加到 5 分钟，那么存储事务的数量将下降 10 倍 (5*60/30)。

`segmentLength` 属性可确保边缘模块最多每 `segmentLength` 秒上传一次视频。 此属性的最小值为 30 秒（也是默认值），可以按 30 秒递增，最大值为 5 分钟。

> [!NOTE]
> 有关 `segmentLength` 对播放的影响，请参阅[播放视频录制内容](playback-recordings-how-to.md)一文。

## <a name="see-also"></a>另请参阅

* [基于事件的视频录制](event-based-video-recording-concept.md) 
* [播放视频录制内容](playback-recordings-how-to.md) 

## <a name="next-steps"></a>后续步骤

[教程：连续视频录制](use-continuous-video-recording.md) 

<!-- links 
[pipeline-cvr-json]: https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/cvr-asset
[terminology-video]: terminology.md#video
[concept-pipeline]: pipeline.md
[concept-video-playback]: playback-recordings-how-to.md
[concept-recording]: video-recording-concept.md
-->
