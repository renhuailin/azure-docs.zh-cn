---
title: include 文件
description: include 文件
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/14/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 2c49bfee8f3b694ea635c836e06bfbe99ba9d758
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2021
ms.locfileid: "112425942"
---
[Durable Functions](../articles/azure-functions/durable/durable-functions-overview.md) 的配置设置。

> [!NOTE]
> Azure Functions 运行时的所有版本均支持 Durable Functions 的所有主要版本。 但是，根据 Azure Functions 运行时的版本和使用的 Durable Functions 扩展版本，host json 配置的架构略有不同。 以下示例可与 Azure Functions 2.0 和3.0 一起使用。 在这两个示例中，如果使用 Azure Functions 1.0，则可用设置是相同的，但 host.json 的“durableTask”部分应位于 host. json 配置的根目录中，而不是作为“extension”下的字段。

### <a name="durable-functions-2x"></a><a name="durable-functions-2-0-host-json"></a>Durable Functions 2.x

```json
{
 "extensions": {
  "durableTask": {
    "hubName": "MyTaskHub",
    "storageProvider": {
      "connectionStringName": "AzureWebJobsStorage",
      "controlQueueBatchSize": 32,
      "controlQueueBufferThreshold": 256,
      "controlQueueVisibilityTimeout": "00:05:00",
      "maxQueuePollingInterval": "00:00:30",
      "partitionCount": 4,
      "trackingStoreConnectionStringName": "TrackingStorage",
      "trackingStoreNamePrefix": "DurableTask",
      "useLegacyPartitionManagement": true,
      "workItemQueueVisibilityTimeout": "00:05:00",
    },
    "tracing": {
      "traceInputsAndOutputs": false,
      "traceReplayEvents": false,
    },
    "notifications": {
      "eventGrid": {
        "topicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
        "keySettingName": "EventGridKey",
        "publishRetryCount": 3,
        "publishRetryInterval": "00:00:30",
        "publishEventTypes": [
          "Started",
          "Pending",
          "Failed",
          "Terminated"
        ]
      }
    },
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10,
    "extendedSessionsEnabled": false,
    "extendedSessionIdleTimeoutInSeconds": 30,
    "useAppLease": true,
    "useGracefulShutdown": false
  }
 }
}

```

### <a name="durable-functions-1x"></a>Durable Functions 1.x

```json
{
  "extensions": {
    "durableTask": {
      "hubName": "MyTaskHub",
      "controlQueueBatchSize": 32,
      "partitionCount": 4,
      "controlQueueVisibilityTimeout": "00:05:00",
      "workItemQueueVisibilityTimeout": "00:05:00",
      "maxConcurrentActivityFunctions": 10,
      "maxConcurrentOrchestratorFunctions": 10,
      "maxQueuePollingInterval": "00:00:30",
      "azureStorageConnectionStringName": "AzureWebJobsStorage",
      "trackingStoreConnectionStringName": "TrackingStorage",
      "trackingStoreNamePrefix": "DurableTask",
      "traceInputsAndOutputs": false,
      "logReplayEvents": false,
      "eventGridTopicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
      "eventGridKeySettingName":  "EventGridKey",
      "eventGridPublishRetryCount": 3,
      "eventGridPublishRetryInterval": "00:00:30",
      "eventGridPublishEventTypes": ["Started", "Completed", "Failed", "Terminated"]
    }
  }
}
```

任务中心名称必须以字母开头且只能包含字母和数字。 如果未指定，则函数应用的默认任务中心名称是 **DurableFunctionsHub**。 有关详细信息，请参阅[任务中心](../articles/azure-functions/durable/durable-functions-task-hubs.md)。

|properties  |默认 | 说明 |
|---------|---------|----------|
|hubName|DurableFunctionsHub|可以使用备用[任务中心](../articles/azure-functions/durable/durable-functions-task-hubs.md)名称将多个 Durable Functions 应用程序彼此隔离，即使这些应用程序使用同一存储后端。|
|controlQueueBatchSize|32|要从控制队列中一次性拉取的消息数。|
|controlQueueBufferThreshold| 适用于 Python 的消耗计划：32 <br> 适用于 JavaScript 和 C# 的消耗计划：128 <br> **专用/高级计划**：256 |一次可以在内存中缓冲的控制队列消息数，此时调度程序将等待，然后再将任何其他消息出队。|
|partitionCount |4|控制队列的分区计数。 可以是 1 到 16 之间的正整数。|
|controlQueueVisibilityTimeout |5 分钟|已取消排队的控制队列消息的可见性超时。|
|workItemQueueVisibilityTimeout |5 分钟|已取消排队的工作项队列消息的可见性超时。|
|maxConcurrentActivityFunctions | **消耗计划**：10 <br> **专用/高级计划**：当前计算机上的处理器数的 10 倍|可以在单个主机实例上并发处理的活动函数的最大数目。|
|maxConcurrentOrchestratorFunctions | **消耗计划**：5 <br> **专用/高级计划**：当前计算机上的处理器数的 10 倍 |可以在单个主机实例上并发处理的业务流程协调程序函数的最大数目。|
|maxQueuePollingInterval|30 秒|最大的控制和工作项队列轮询时间间隔，采用 *hh:mm:ss* 格式。 值越高，可能导致的消息处理延迟也越高。 值越低，可能导致的存储成本会越高，因为存储事务数增高。|
|connectionStringName (2.x)<br/>azureStorageConnectionStringName (1.x) |AzureWebJobsStorage|应用设置的名称，其中的 Azure 存储连接字符串用于管理基础的 Azure 存储资源。|
|trackingStoreConnectionStringName||连接字符串的名称，用于“历史记录”和“实例”表。 如果未指定，则使用 `connectionStringName` (Durable 2.x) 或 `azureStorageConnectionStringName` (Durable 1.x) 连接。|
|trackingStoreNamePrefix||指定 `trackingStoreConnectionStringName` 时用于“历史记录”和“实例”表的前缀。 如果未设置，则默认前缀值为 `DurableTask`。 如果 `trackingStoreConnectionStringName` 未指定，则“历史记录”和“实例”表会使用 `hubName` 值作为其前缀，`trackingStoreNamePrefix` 的任何设置都会被忽略。|
|traceInputsAndOutputs |false|一个指示是否跟踪函数调用的输入和输出的值。 跟踪函数执行事件时的默认行为是在函数调用的序列化输入和输出中包括字节数。 此行为提供的有关输入和输出情况的信息是最少的，不会导致日志膨胀，也不会无意中将敏感信息公开。 将此属性设置为 true 会导致默认函数日志记录将函数输入和输出的整个内容都记录下来。|
|logReplayEvents|false|一个值，该值指示是否将业务流程重播事件写入到 Application Insights。|
|eventGridTopicEndpoint ||Azure 事件网格自定义主题终结点的 URL。 设置此属性后，业务流程生命周期通知事件就会发布到此终结点。 此属性支持应用设置解析。|
|eventGridKeySettingName ||应用设置的名称，该设置包含的密钥用于在 `EventGridTopicEndpoint` 上通过 Azure 事件网格自定义主题进行身份验证。|
|eventGridPublishRetryCount|0|发布到事件网格主题失败时要重试的次数。|
|eventGridPublishRetryInterval|5 分钟|事件网格发布重试间隔（采用 *hh:mm:ss* 格式）。|
|eventGridPublishEventTypes||要发布到事件网格的事件类型列表。 如果未指定，则将发布所有事件类型。 允许的值包括 `Started`、`Completed`、`Failed`、`Terminated`。|
|useAppLease|是|如果设置为 `true`，应用将要求在处理任务中心消息之前获取应用级别 blob 租约。 有关详细信息，请参阅[灾难恢复和地理分布](../articles/azure-functions/durable/durable-functions-disaster-recovery-geo-distribution.md)文档。 从 v2.3.0 开始可用。
|useLegacyPartitionManagement|false|如果设置为 `false`，则将使用分区管理算法，该算法可减少在横向扩展时重复执行函数的可能性。从 v2.3.0 开始可用。|
|useGracefulShutdown|false|（预览）启用正常关闭以减少主机关闭导致进程内函数执行失败的机会。|

许多此类设置用于优化性能。 有关详细信息，请参阅[性能和规模](../articles/azure-functions/durable/durable-functions-perf-and-scale.md)。
