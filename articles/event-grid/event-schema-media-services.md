---
title: 充当事件网格源的 Azure 媒体服务
description: 介绍为 Azure 事件网格中的媒体服务事件提供的属性
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: c3b40f1a40cd0a5ee611a00f2f0361a8a522d3ad
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122014571"
---
# <a name="azure-media-services-as-an-event-grid-source"></a>充当事件网格源的 Azure 媒体服务

本文介绍媒体服务事件的架构和属性。

## <a name="job-related-event-types"></a>作业相关事件类型

媒体服务会发出如下所述的作业相关事件类型。 作业相关事件有两个类别：“监视作业状态更改”和“监视作业输出状态更改”。 

可通过订阅 JobStateChange 事件来注册所有事件。 或者，可以只订阅特定事件（例如，JobErrored、JobFinished 和 JobCanceled 等最终状态）。   

### <a name="monitoring-job-state-changes"></a>监视作业状态更改

| 事件类型 | 说明 |
| ---------- | ----------- |
| Microsoft.Media.JobStateChange| 获取所有作业状态更改的事件。 |
| Microsoft.Media.JobScheduled| 获取当作业转换为已计划状态时的事件。 |
| Microsoft.Media.JobProcessing| 获取当作业转换为正在处理状态时的事件。 |
| Microsoft.Media.JobCanceling| 获取当作业转换为正在取消状态时的事件。 |
| Microsoft.Media.JobFinished| 获取当作业转换为已完成状态时的事件。 这是包含作业输出的最终状态。|
| Microsoft.Media.JobCanceled| 获取当作业转换为已取消状态时的事件。 这是包含作业输出的最终状态。|
| Microsoft.Media.JobErrored| 获取当作业转换为错误状态时的事件。 这是包含作业输出的最终状态。|

请参阅后面的[架构示例](#event-schema-examples)。

### <a name="monitoring-job-output-state-changes"></a>监视作业输出状态更改

一个作业可能包含多个作业输出（如果将转换配置为具有多个作业输出。）如果要跟踪单个作业输出的详细信息，请侦听作业输出更改事件。

每个 Job  的级别高于 JobOutput  的级别，因此作业输出事件会在相应的作业内触发。 

`JobFinished`、`JobCanceled`、`JobError` 中的错误消息输出各个作业输出的聚合结果（当所有作业都完成时）， 而作业输出事件会在每个任务完成时触发。 例如，如果你有一个编码输出，后跟一个视频分析输出，则在使用聚合数据触发最终 JobFinished 事件之前，你将得到两个作为作业输出事件触发的事件。

| 事件类型 | 说明 |
| ---------- | ----------- |
| Microsoft.Media.JobOutputStateChange| 获取所有作业输出状态更改的事件。 |
| Microsoft.Media.JobOutputScheduled| 获取当作业输出转换为已计划状态时的事件。 |
| Microsoft.Media.JobOutputProcessing| 获取当作业输出转换为正在处理状态时的事件。 |
| Microsoft.Media.JobOutputCanceling| 获取当作业输出转换为正在取消状态时的事件。|
| Microsoft.Media.JobOutputFinished| 获取当作业输出转换为已完成状态时的事件。|
| Microsoft.Media.JobOutputCanceled| 获取当作业输出转换为已取消状态时的事件。|
| Microsoft.Media.JobOutputErrored| 获取当作业输出转换为错误状态时的事件。|

请参阅后面的[架构示例](#event-schema-examples)。

### <a name="monitoring-job-output-progress"></a>监视作业输出进度

| 事件类型 | 说明 |
| ---------- | ----------- |
| Microsoft.Media.JobOutputProgress| 此事件反映了作业处理进度，从 0% 到 100%。 如果进度值增加了 5% 或更多，或者自上次事件（检测信号）以来已超过 30 秒，则服务会尝试发送事件。 无法保证进度值从 0% 开始或达到 100%，也无法保证其随时间推移而以恒定速率增加。 此事件不应用于确定是否已经完成处理 – 要实现此目的，请改用状态更改事件。|

请参阅后面的[架构示例](#event-schema-examples)。

## <a name="live-event-types"></a>实时事件类型

媒体服务也会发出如下所述的 **实时** 事件类型。 **实时** 事件有两种类别：流级事件和轨迹级事件。 

### <a name="stream-level-events"></a>流级事件

按流或连接引发流级事件。 每个事件具有一个用于标识连接或流的 `StreamId` 参数。 每个流或连接具有一个或多个不同类型的轨迹。 例如，来自编码器的一个连接可能具有一个音频轨迹和四个视频轨迹。 流事件类型包括：

| 事件类型 | 说明 |
| ---------- | ----------- |
| Microsoft.Media.LiveEventConnectionRejected | 编码器的连接尝试被拒绝。 |
| Microsoft.Media.LiveEventEncoderConnected | 编码器与直播活动建立连接。 |
| Microsoft.Media.LiveEventEncoderDisconnected | 编码器断开连接。 |

请参阅后面的[架构示例](#event-schema-examples)。

### <a name="track-level-events"></a>轨迹级事件

按轨迹引发轨迹级事件。 

> [!NOTE]
> 所有轨迹级事件都在连接实时编码器后引发。

轨迹级事件类型包括：

| 事件类型 | 说明 |
| ---------- | ----------- |
| Microsoft.Media.LiveEventIncomingDataChunkDropped | 媒体服务器删除了数据区块，因为该区块的抵达时间过迟，或者带有重叠的时间戳（新数据区块的时间戳小于前一数据区块的结束时间）。 |
| Microsoft.Media.LiveEventIncomingStreamReceived | 媒体服务器收到流或连接中每个轨迹的第一个数据区块。 |
| Microsoft.Media.LiveEventIncomingStreamsOutOfSync | 媒体服务器检测到音频和视频流不同步。用作警告，因为用户体验可能不受影响。 |
| Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync | 媒体服务器检测到来自外部编码器的任意两个视频流不同步。用作警告，因为用户体验可能不受影响。 |
| Microsoft.Media.LiveEventIngestHeartbeat | 当直播活动正在运行时，每隔 20 秒为每个轨迹发布。 提供引入运行状况摘要。<br/><br/>在编码器最初连接后，无论编码器是否仍然处于连接状态，检测信号事件都会继续按 20 秒 1 次的频率发出。 |
| Microsoft.Media.LiveEventTrackDiscontinuityDetected | 媒体服务器检测到传入轨迹中存在不连续的情况。 |

请参阅后面的[架构示例](#event-schema-examples)。

## <a name="event-schema-examples"></a>事件架构示例

### <a name="jobstatechange"></a>JobStateChange

# <a name="event-grid-event-schema"></a>[事件网格事件架构](#tab/event-grid-event-schema)

以下示例显示 **JobStateChange** 事件的架构： 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
    "eventType": "Microsoft.Media.JobStateChange",
    "eventTime": "2018-04-20T21:26:13.8978772",
    "id": "b9d38923-9210-4c2b-958f-0054467d4dd7",
    "data": {
      "previousState": "Processing",
      "state": "Finished"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

# <a name="cloud-event-schema"></a>[云事件架构](#tab/cloud-event-schema)

以下示例显示 **JobStateChange** 事件的架构： 

```json
[
  {
    "source": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
    "type": "Microsoft.Media.JobStateChange",
    "time": "2018-04-20T21:26:13.8978772",
    "id": "b9d38923-9210-4c2b-958f-0054467d4dd7",
    "data": {
      "previousState": "Processing",
      "state": "Finished"
    },
    "specversion": "1.0"
  }
]
```

---

数据对象具有以下属性：

| 属性 | 类型 | 说明 |
| -------- | ---- | ----------- |
| `previousState` | string | 事件发生前的作业状态。 |
| `state` | string | 此事件中通知的作业的新状态。 例如，“已计划：作业已准备就绪”或“已完成：作业已完成”。|

作业状态可以是以下任何一个值：已排队、已计划、正在处理、已完成、错误、已取消、正在取消

> [!NOTE]
> 已排队仅出现在 previousState 属性中，不出现在 state 属性中    。

### <a name="jobscheduled-jobprocessing-jobcanceling"></a>JobScheduled、JobProcessing、JobCanceling

# <a name="event-grid-event-schema"></a>[事件网格事件架构](#tab/event-grid-event-schema)

对于每个非最终作业状态更改（例如 JobScheduled、JobProcessing、JobCanceling），示例架构类似于以下内容：

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobProcessing",
  "eventTime": "2018-10-12T16:12:18.0839935",
  "id": "a0a6efc8-f647-4fc2-be73-861fa25ba2db",
  "data": {
    "previousState": "Scheduled",
    "state": "Processing",
    "correlationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="jobfinished-jobcanceled-joberrored"></a>JobFinished、JobCanceled、JobErrored

对于每个最终作业状态更改（例如 JobFinished、JobCanceled、JobErrored），示例架构类似于以下内容：

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobFinished",
  "eventTime": "2018-10-12T16:25:56.4115495",
  "id": "9e07e83a-dd6e-466b-a62f-27521b216f2a",
  "data": {
    "outputs": [
      {
        "@odata.type": "#Microsoft.Media.JobOutputAsset",
        "assetName": "output-7640689F",
        "error": null,
        "label": "VideoAnalyzerPreset_0",
        "progress": 100,
        "state": "Finished"
      }
    ],
    "previousState": "Processing",
    "state": "Finished",
    "correlationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

# <a name="cloud-event-schema"></a>[云事件架构](#tab/cloud-event-schema)

对于每个非最终作业状态更改（例如 JobScheduled、JobProcessing、JobCanceling），示例架构类似于以下内容：

```json
[{
  "source": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "type": "Microsoft.Media.JobProcessing",
  "time": "2018-10-12T16:12:18.0839935",
  "id": "a0a6efc8-f647-4fc2-be73-861fa25ba2db",
  "data": {
    "previousState": "Scheduled",
    "state": "Processing",
    "correlationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "specversion": "1.0"
}]
```

### <a name="jobfinished-jobcanceled-joberrored"></a>JobFinished、JobCanceled、JobErrored

对于每个最终作业状态更改（例如 JobFinished、JobCanceled、JobErrored），示例架构类似于以下内容：

```json
[{
  "source": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "type": "Microsoft.Media.JobFinished",
  "time": "2018-10-12T16:25:56.4115495",
  "id": "9e07e83a-dd6e-466b-a62f-27521b216f2a",
  "data": {
    "outputs": [
      {
        "@odata.type": "#Microsoft.Media.JobOutputAsset",
        "assetName": "output-7640689F",
        "error": null,
        "label": "VideoAnalyzerPreset_0",
        "progress": 100,
        "state": "Finished"
      }
    ],
    "previousState": "Processing",
    "state": "Finished",
    "correlationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "specversion": "1.0"
}]
```

---


数据对象具有以下属性：

| 属性 | 类型 | 说明 |
| -------- | ---- | ----------- |
| `outputs` | Array | 获取作业输出。|

### <a name="joboutputstatechange"></a>JobOutputStateChange

以下示例展示了 **JobOutputStateChange** 事件的架构：

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobOutputStateChange",
  "eventTime": "2018-10-12T16:25:56.0242854",
  "id": "dde85f46-b459-4775-b5c7-befe8e32cf90",
  "data": {
    "previousState": "Processing",
    "output": {
      "@odata.type": "#Microsoft.Media.JobOutputAsset",
      "assetName": "output-7640689F",
      "error": null,
      "label": "VideoAnalyzerPreset_0",
      "progress": 100,
      "state": "Finished"
    },
    "jobCorrelationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="joboutputscheduled-joboutputprocessing-joboutputfinished-joboutputcanceling-joboutputcanceled-joboutputerrored"></a>JobOutputScheduled、JobOutputProcessing、JobOutputFinished、JobOutputCanceling、JobOutputCanceled、JobOutputErrored

对于每个 JobOutput 状态更改，示例架构类似于以下内容：

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobOutputProcessing",
  "eventTime": "2018-10-12T16:12:18.0061141",
  "id": "f1fd5338-1b6c-4e31-83c9-cd7c88d2aedb",
  "data": {
    "previousState": "Scheduled",
    "output": {
      "@odata.type": "#Microsoft.Media.JobOutputAsset",
      "assetName": "output-7640689F",
      "error": null,
      "label": "VideoAnalyzerPreset_0",
      "progress": 0,
      "state": "Processing"
    },
    "jobCorrelationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```
### <a name="joboutputprogress"></a>JobOutputProgress

示例架构类似如下：

 ```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/belohGroup/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/job-5AB6DE32",
  "eventType": "Microsoft.Media.JobOutputProgress",
  "eventTime": "2018-12-10T18:20:12.1514867",
  "id": "00000000-0000-0000-0000-000000000000",
  "data": {
    "jobCorrelationData": {
      "TestKey1": "TestValue1",
      "testKey2": "testValue2"
    },
    "label": "VideoAnalyzerPreset_0",
    "progress": 86
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

以下示例显示 **LiveEventConnectionRejected** 事件的架构： 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "eventType": "Microsoft.Media.LiveEventConnectionRejected",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "b303db59-d5c1-47eb-927a-3650875fded1",
    "data": { 
      "streamId":"Mystream1",
      "ingestUrl": "http://abc.ingest.isml",
      "encoderIp": "118.238.251.xxx",
      "encoderPort": 52859,
      "resultCode": "MPE_INGEST_CODEC_NOT_SUPPORTED"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

数据对象具有以下属性：

| 属性 | 类型 | 说明 |
| -------- | ---- | ----------- |
| `streamId` | string | 流或连接的标识符。 编码器或客户负责在引入 URL 中添加此 ID。 |  
| `ingestUrl` | string | 直播活动提供的引入 URL。 |  
| `encoderIp` | string | 编码器的 IP。 |
| `encoderPort` | string | 此流的来源编码器的端口。 |
| `resultCode` | string | 拒绝连接的原因。 下表中列出了结果代码。 |

可在[实时事件错误代码](../media-services/latest/live-event-error-codes-reference.md)中找到错误结果代码。

### <a name="liveeventencoderconnected"></a>LiveEventEncoderConnected

# <a name="event-grid-event-schema"></a>[事件网格事件架构](#tab/event-grid-event-schema)

以下示例显示 **LiveEventEncoderConnected** 事件的架构： 

```json
[
  { 
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventEncoderConnected",
    "eventTime": "2018-08-07T23:08:09.1710643",
    "id": "<id>",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml",
      "streamId": "15864-stream0",
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

# <a name="cloud-event-schema"></a>[云事件架构](#tab/cloud-event-schema)

以下示例显示 **LiveEventEncoderConnected** 事件的架构： 

```json
[
  { 
    "source": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "type": "Microsoft.Media.LiveEventEncoderConnected",
    "time": "2018-08-07T23:08:09.1710643",
    "id": "<id>",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml",
      "streamId": "15864-stream0",
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485"
    },
    "specversion": "1.0"
  }
]
```

---

数据对象具有以下属性：

| 属性 | 类型 | 说明 |
| -------- | ---- | ----------- |
| `streamId` | string | 流或连接的标识符。 编码器或客户负责在引入 URL 中提供此 ID。 |
| `ingestUrl` | string | 直播活动提供的引入 URL。 |
| `encoderIp` | string | 编码器的 IP。 |
| `encoderPort` | string | 此流的来源编码器的端口。 |

### <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

# <a name="event-grid-event-schema"></a>[事件网格事件架构](#tab/event-grid-event-schema)

以下示例显示 **LiveEventEncoderDisconnected** 事件的架构： 

```json
[
  { 
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventEncoderDisconnected",
    "eventTime": "2018-08-07T23:08:09.1710872",
    "id": "<id>",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml",
      "streamId": "15864-stream0",
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485",
      "resultCode": "S_OK"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

# <a name="cloud-event-schema"></a>[云事件架构](#tab/cloud-event-schema)

以下示例显示 **LiveEventEncoderDisconnected** 事件的架构： 

```json
[
  { 
    "source": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "type": "Microsoft.Media.LiveEventEncoderDisconnected",
    "time": "2018-08-07T23:08:09.1710872",
    "id": "<id>",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml",
      "streamId": "15864-stream0",
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485",
      "resultCode": "S_OK"
    },
    "specversion": "1.0"
  }
]
```

---

数据对象具有以下属性：

| 属性 | 类型 | 说明 |
| -------- | ---- | ----------- |
| `streamId` | string | 流或连接的标识符。 编码器或客户负责在引入 URL 中添加此 ID。 |  
| `ingestUrl` | string | 直播活动提供的引入 URL。 |  
| `encoderIp` | string | 编码器的 IP。 |
| `encoderPort` | string | 此流的来源编码器的端口。 |
| `resultCode` | string | 编码器断开连接的原因。 可能是正常断开连接，或者是由于出错而断开连接。 下表中列出了结果代码。 |

可在[实时事件错误代码](../media-services/latest/live-event-error-codes-reference.md)中找到错误结果代码。

正常断开连接结果代码为：

| 结果代码 | 说明 |
| ----------- | ----------- |
| S_OK | 编码器已成功断开连接。 |
| MPE_CLIENT_TERMINATED_SESSION | 编码器已断开连接 (RTMP)。 |
| MPE_CLIENT_DISCONNECTED | 编码器已断开连接 (FMP4)。 |
| MPI_REST_API_CHANNEL_RESET | 收到通道重置命令。 |
| MPI_REST_API_CHANNEL_STOP | 收到通道停止命令。 |
| MPI_REST_API_CHANNEL_STOP | 正在维护通道。 |
| MPI_STREAM_HIT_EOF | 编码器已发送 EOF 流。 |

### <a name="liveeventincomingdatachunkdropped"></a>LiveEventIncomingDataChunkDropped

# <a name="event-grid-event-schema"></a>[事件网格事件架构](#tab/event-grid-event-schema)

以下示例显示 **LiveEventIncomingDataChunkDropped** 事件的架构： 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "eventType": "Microsoft.Media.LiveEventIncomingDataChunkDropped",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "03da9c10-fde7-48e1-80d8-49936f2c3e7d",
    "data": { 
      "trackType": "Video",
      "trackName": "Video",
      "bitrate": 300000,
      "timestamp": 36656620000,
      "timescale": 10000000,
      "resultCode": "FragmentDrop_OverlapTimestamp"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

# <a name="cloud-event-schema"></a>[云事件架构](#tab/cloud-event-schema)

以下示例显示 **LiveEventIncomingDataChunkDropped** 事件的架构： 

```json
[
  {
    "source": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "type": "Microsoft.Media.LiveEventIncomingDataChunkDropped",
    "time": "2018-01-16T01:57:26.005121Z",
    "id": "03da9c10-fde7-48e1-80d8-49936f2c3e7d",
    "data": { 
      "trackType": "Video",
      "trackName": "Video",
      "bitrate": 300000,
      "timestamp": 36656620000,
      "timescale": 10000000,
      "resultCode": "FragmentDrop_OverlapTimestamp"
    },
    "specversion": "1.0"
  }
]
```

---

数据对象具有以下属性：

| 属性 | 类型 | 说明 |
| -------- | ---- | ----------- |
| `trackType` | string | 轨道类型（音频/视频）。 |
| `trackName` | string | 轨道名称。 |
| `bitrate` | integer | 轨道的比特率。 |
| `timestamp` | string | 已删除的数据区块的时间戳。 |
| `timescale` | string | 时间戳的时间刻度。 |
| `resultCode` | string | 删除数据区块的原因。 **FragmentDrop_OverlapTimestamp** 或 **FragmentDrop_NonIncreasingTimestamp**。 |

### <a name="liveeventincomingstreamreceived"></a>LiveEventIncomingStreamReceived

# <a name="event-grid-event-schema"></a>[事件网格事件架构](#tab/event-grid-event-schema)

以下示例显示 **LiveEventIncomingStreamReceived** 事件的架构： 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIncomingStreamReceived",
    "eventTime": "2018-08-07T23:08:10.5069288Z",
    "id": "7f939a08-320c-47e7-8250-43dcfc04ab4d",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml/Streams(15864-stream0)15864-stream0",
      "trackType": "video",
      "trackName": "video",
      "bitrate": 2962000,
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485",
      "timestamp": "15336831655032322",
      "duration": "20000000",
      "timescale": "10000000"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

# <a name="cloud-event-schema"></a>[云事件架构](#tab/cloud-event-schema)

以下示例显示 **LiveEventIncomingStreamReceived** 事件的架构： 

```json
[
  {
    "source": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "type": "Microsoft.Media.LiveEventIncomingStreamReceived",
    "time": "2018-08-07T23:08:10.5069288Z",
    "id": "7f939a08-320c-47e7-8250-43dcfc04ab4d",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml/Streams(15864-stream0)15864-stream0",
      "trackType": "video",
      "trackName": "video",
      "bitrate": 2962000,
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485",
      "timestamp": "15336831655032322",
      "duration": "20000000",
      "timescale": "10000000"
    },
    "specversion": "1.0"
  }
]
```

---

数据对象具有以下属性：

| 属性 | 类型 | 说明 |
| -------- | ---- | ----------- |
| `trackType` | string | 轨道类型（音频/视频）。 |
| `trackName` | string | 轨迹的名称（由编码器提供；对于 RTMP，由服务器以 *TrackType_Bitrate* 格式生成）。 |
| `bitrate` | integer | 轨道的比特率。 |
| `ingestUrl` | string | 直播活动提供的引入 URL。 |
| `encoderIp` | string  | 编码器的 IP。 |
| `encoderPort` | string | 此流的来源编码器的端口。 |
| `timestamp` | string | 收到数据区块的第一个时间戳。 |
| `timescale` | string | 用于表示时间戳的时间刻度。 |

### <a name="liveeventincomingstreamsoutofsync"></a>LiveEventIncomingStreamsOutOfSync

# <a name="event-grid-event-schema"></a>[事件网格事件架构](#tab/event-grid-event-schema)

以下示例显示 **LiveEventIncomingStreamsOutOfSync** 事件的架构： 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIncomingStreamsOutOfSync",
    "eventTime": "2018-08-10T02:26:20.6269183Z",
    "id": "b9d38923-9210-4c2b-958f-0054467d4dd7",
    "data": {
      "minLastTimestamp": "319996",
      "typeOfStreamWithMinLastTimestamp": "Audio",
      "maxLastTimestamp": "366000",
      "typeOfStreamWithMaxLastTimestamp": "Video",
      "timescaleOfMinLastTimestamp": "10000000", 
      "timescaleOfMaxLastTimestamp": "10000000"       
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

# <a name="cloud-event-schema"></a>[云事件架构](#tab/cloud-event-schema)

以下示例显示 **LiveEventIncomingStreamsOutOfSync** 事件的架构： 

```json
[
  {
    "source": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "type": "Microsoft.Media.LiveEventIncomingStreamsOutOfSync",
    "time": "2018-08-10T02:26:20.6269183Z",
    "id": "b9d38923-9210-4c2b-958f-0054467d4dd7",
    "data": {
      "minLastTimestamp": "319996",
      "typeOfStreamWithMinLastTimestamp": "Audio",
      "maxLastTimestamp": "366000",
      "typeOfStreamWithMaxLastTimestamp": "Video",
      "timescaleOfMinLastTimestamp": "10000000", 
      "timescaleOfMaxLastTimestamp": "10000000"       
    },
    "specversion": "1.0"
  }
]
```

---

数据对象具有以下属性：

| 属性 | 类型 | 说明 |
| -------- | ---- | ----------- |
| `minLastTimestamp` | string | 所有轨迹（音频或视频）中最后一个时间戳的最小值。 |
| `typeOfTrackWithMinLastTimestamp` | string | 最后一个时间戳最小的轨迹的类型（音频或视频）。 |
| `maxLastTimestamp` | string | 所有轨迹（音频或视频）中所有时间戳的最大值。 |
| `typeOfTrackWithMaxLastTimestamp` | string | 最后一个时间戳最大的轨迹的类型（音频或视频）。 |
| `timescaleOfMinLastTimestamp`| string | 获取用于表示“MinLastTimestamp”的时间刻度。|
| `timescaleOfMaxLastTimestamp`| string | 获取用于表示“MaxLastTimestamp”的时间刻度。|

### <a name="liveeventincomingvideostreamsoutofsync"></a>LiveEventIncomingVideoStreamsOutOfSync

# <a name="event-grid-event-schema"></a>[事件网格事件架构](#tab/event-grid-event-schema)

以下示例显示 **LiveEventIncomingVideoStreamsOutOfSync** 事件的架构： 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/LiveEvent1",
    "eventType": "Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "6dd4d862-d442-40a0-b9f3-fc14bcf6d750",
    "data": {
      "firstTimestamp": "2162058216",
      "firstDuration": "2000",
      "secondTimestamp": "2162057216",
      "secondDuration": "2000",
      "timescale": "10000000"      
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

# <a name="cloud-event-schema"></a>[云事件架构](#tab/cloud-event-schema)

以下示例显示 **LiveEventIncomingVideoStreamsOutOfSync** 事件的架构： 

```json
[
  {
    "source": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/LiveEvent1",
    "type": "Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync",
    "time": "2018-01-16T01:57:26.005121Z",
    "id": "6dd4d862-d442-40a0-b9f3-fc14bcf6d750",
    "data": {
      "firstTimestamp": "2162058216",
      "firstDuration": "2000",
      "secondTimestamp": "2162057216",
      "secondDuration": "2000",
      "timescale": "10000000"      
    },
    "specversion": "1.0"
  }
]
```

---

数据对象具有以下属性：

| 属性 | 类型 | 说明 |
| -------- | ---- | ----------- |
| `firstTimestamp` | string | 收到视频类型的某个轨迹/质量级别的时间戳。 |
| `firstDuration` | string | 具有第一个时间戳的数据区块的持续时间。 |
| `secondTimestamp` | string  | 收到视频类型的其他某个轨迹/质量级别的时间戳。 |
| `secondDuration` | string | 具有第二个时间戳的数据区块的持续时间。 |
| `timescale` | string | 时间戳和持续时间的时间刻度。|

### <a name="liveeventingestheartbeat"></a>LiveEventIngestHeartbeat

# <a name="event-grid-event-schema"></a>[事件网格事件架构](#tab/event-grid-event-schema)

以下示例显示 **LiveEventIngestHeartbeat** 事件的架构： 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIngestHeartbeat",
    "eventTime": "2021-05-14T23:50:00.324",
    "id": "7f450938-491f-41e1-b06f-c6cd3965d786",
    "data": {
      "trackType":"video",
      "trackName":"video",
      "bitrate":2500000,
      "incomingBitrate":2462597,
      "lastTimestamp":"106999",
      "timescale":"1000",
      "overlapCount":0,
      "discontinuityCount":0,
      "nonincreasingCount":0,
      "unexpectedBitrate":false,
      "state":"Running",
      "healthy":true,
      "lastFragmentArrivalTime":"2021-05-14T23:50:00.324",
      "ingestDriftValue":"0",
      "transcriptionState":"",
      "transcriptionLanguage":""
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

# <a name="cloud-event-schema"></a>[云事件架构](#tab/cloud-event-schema)


以下示例显示 **LiveEventIngestHeartbeat** 事件的架构： 

```json
[
  {
    "source": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "type": "Microsoft.Media.LiveEventIngestHeartbeat",
    "time": "2018-08-07T23:17:57.4610506",
    "id": "7f450938-491f-41e1-b06f-c6cd3965d786",
    "data": {
      "trackType": "audio",
      "trackName": "audio",
      "bitrate": 160000,
      "incomingBitrate": 155903,
      "lastTimestamp": "15336837535253637",
      "timescale": "10000000",
      "overlapCount": 0,
      "discontinuityCount": 0,
      "nonincreasingCount": 0,
      "unexpectedBitrate": false,
      "state": "Running",
      "healthy": true
    },
    "specversion": "1.0"
  }
]
```

---

数据对象具有以下属性：

| 属性 | 类型 | 说明 |
| -------- | ---- | ----------- |
| `trackType` | string | 轨道类型（音频/视频）。 |
| `trackName` | string | 轨迹的名称（由编码器提供；对于 RTMP，由服务器以 *TrackType_Bitrate* 格式生成）。 |
| `bitrate` | integer | 轨道的比特率。 |
| `incomingBitrate` | integer | 基于来自编码器的数据区块计算出的比特率。 |
| `lastTimestamp` | string | 在过去 20 秒收到的轨迹的最新时间戳。 |
| `timescale` | string | 用于表示时间戳的时间刻度。 |
| `overlapCount` | integer | 在过去 20 秒数据区块数目包含重叠的时间戳。 |
| `discontinuityCount` | integer | 在过去 20 秒观察到的不连续性数目。 |
| `nonIncreasingCount` | integer | 在过去 20 秒收到的具有以往时间戳的数据区块数。 |
| `unexpectedBitrate` | bool | 在过去 20 秒，预期和实际比特率之差是否超过了允许的限制。 当且仅当 incomingBitrate >= 2* 比特率，或者 incomingBitrate <= 比特率/2，或者 IncomingBitrate = 0 时，此属性的值才为 true。 |
| `state` | string | 直播活动的状态。 |
| `healthy` | bool | 指示引入是否正常（基于计数和标志判断）。 如果 overlapCount = 0 并且 discontinuityCount = 0 并且 nonIncreasingCount = 0 并且 unexpectedBitrate = false，则 Healthy 为 true。 |
| `lastFragmentArrivalTime` | string |片段到达引入终结点的最后一个时间戳 (UTC)。 示例日期格式为“2020-11-11 12:12:12:888999” |
| `ingestDriftValue` | string | 指示最后一分钟内传入音频或视频数据的延迟速度，单位为秒/分钟。 如果在最后一分钟内数据到达实时事件的速度比预期的慢，则该值大于零；如果数据没有延迟到达，则为零；如果没有收到音频或视频数据，则为“n/a”。 例如，如果有一个贡献编码器发送实时内容，并且由于处理问题或网络延迟而速度变慢，则它可能在一分钟内只能提供总共 58 秒的音频或视频。 这将报告为偏移 2 秒/分钟。 如果编码器能够跟上并且每分钟发送全部 60 秒或更多数据，则此值将报告为 0。 如果与编码器断开连接或不连续，该值可能仍显示为 0，因为它不考虑数据（仅限在时间戳中延迟的数据）中断。|
| `transcriptionState` | string | 如果启用了实时听录，则音轨信号的值为“开”，否则将出现一个空字符串。 此状态仅适用于实时听录的“音频”轨道类型。 所有其他轨道的值都为空。|
| `transcriptionLanguage` | string  | 听录语言的语言代码（采用 BCP-47 格式）。 例如“de-de”表示德语（德国）。 对于视频轨道信号，或在关闭了实时听录时，该值为空。 |


### <a name="liveeventtrackdiscontinuitydetected"></a>LiveEventTrackDiscontinuityDetected

# <a name="event-grid-event-schema"></a>[事件网格事件架构](#tab/event-grid-event-schema)

以下示例显示 **LiveEventTrackDiscontinuityDetected** 事件的架构： 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventTrackDiscontinuityDetected",
    "eventTime": "2018-08-07T23:18:06.1270405Z",
    "id": "5f4c510d-5be7-4bef-baf0-64b828be9c9b",
    "data": {
      "trackName": "video",
      "previousTimestamp": "15336837615032322",
      "trackType": "video",
      "bitrate": 2962000,
      "newTimestamp": "15336837619774273",
      "discontinuityGap": "575284",
      "timescale": "10000000"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

# <a name="cloud-event-schema"></a>[云事件架构](#tab/cloud-event-schema)

以下示例显示 **LiveEventTrackDiscontinuityDetected** 事件的架构： 

```json
[
  {
    "source": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "type": "Microsoft.Media.LiveEventTrackDiscontinuityDetected",
    "time": "2018-08-07T23:18:06.1270405Z",
    "id": "5f4c510d-5be7-4bef-baf0-64b828be9c9b",
    "data": {
      "trackName": "video",
      "previousTimestamp": "15336837615032322",
      "trackType": "video",
      "bitrate": 2962000,
      "newTimestamp": "15336837619774273",
      "discontinuityGap": "575284",
      "timescale": "10000000"
    },
    "specversion": "1.0"
  }
]
```

---

数据对象具有以下属性：

| 属性 | 类型 | 说明 |
| -------- | ---- | ----------- |
| `trackType` | string | 轨道类型（音频/视频）。 |
| `trackName` | string | 轨迹的名称（由编码器提供；对于 RTMP，由服务器以 *TrackType_Bitrate* 格式生成）。 |
| `bitrate` | integer | 轨道的比特率。 |
| `previousTimestamp` | string | 前一个片段的时间戳。 |
| `newTimestamp` | string | 当前片段的时间戳。 |
| `discontinuityGap` | string | 上面两个时间戳之间的差距。 |
| `timescale` | string | 用于表示时间戳和非连续性差距的时间刻度。 |

### <a name="common-event-properties"></a>自定义事件属性

# <a name="event-grid-event-schema"></a>[事件网格事件架构](#tab/event-grid-event-schema)

事件具有以下顶级数据：

| 属性 | 类型 | 说明 |
| -------- | ---- | ----------- |
| `topic` | 字符串 | 事件网格主题。 此属性包含媒体服务帐户的资源 ID。 |
| `subject` | string | 媒体服务帐户下媒体服务通道的资源路径。 连接主题和使用者可以获得作业的资源 ID。 |
| `eventType` | string | 此事件源的一个注册事件类型。 例如，“Microsoft.Media.JobStateChange”。 |
| `eventTime` | string | 基于提供程序 UTC 时间的事件生成时间。 |
| `id` | string | 事件的唯一标识符。 |
| `data` | object | 媒体服务事件数据。 |
| `dataVersion` | string | 数据对象的架构版本。 发布者定义架构版本。 |
| `metadataVersion` | string | 事件元数据的架构版本。 事件网格定义顶级属性的架构。 事件网格提供此值。 |

# <a name="cloud-event-schema"></a>[云事件架构](#tab/cloud-event-schema)

事件具有以下顶级数据：

| 属性 | 类型 | 说明 |
| -------- | ---- | ----------- |
| `source` | 字符串 | 事件网格主题。 此属性包含媒体服务帐户的资源 ID。 |
| `subject` | string | 媒体服务帐户下媒体服务通道的资源路径。 连接主题和使用者可以获得作业的资源 ID。 |
| `type` | string | 此事件源的一个注册事件类型。 例如，“Microsoft.Media.JobStateChange”。 |
| `time` | string | 基于提供程序 UTC 时间的事件生成时间。 |
| `id` | string | 事件的唯一标识符。 |
| `data` | object | 媒体服务事件数据。 |
| `specversion` | 字符串 | CloudEvents 架构规范版本。 |


---

## <a name="next-steps"></a>后续步骤

[注册作业状态更改事件](../media-services/latest/monitoring/job-state-events-cli-how-to.md)

## <a name="see-also"></a>另请参阅

- [包含媒体服务事件的 EventGrid .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [媒体服务事件的定义](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)
- [实时事件错误代码](../media-services/latest/live-event-error-codes-reference.md)
