---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 32494c60e3189c9369bafe37289d01e248e4a678
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2021
ms.locfileid: "122263433"
---
### <a name="functions-2x-and-higher"></a>Functions 2.x 及更高版本

```json
{
    "version": "2.0",
    "extensions": {
        "eventHubs": {
            "batchCheckpointFrequency": 5,
            "eventProcessorOptions": {
                "maxBatchSize": 256,
                "prefetchCount": 512
            },
            "initialOffsetOptions": {
                "type": "fromStart",
                "enqueuedTimeUtc": ""
            }
        }
    }
}  
```

|属性  |默认 | 说明 |
|---------|---------|---------|
|batchCheckpointFrequency|1|创建 EventHub 游标检查点之前要处理的事件批数。|
|eventProcessorOptions/maxBatchSize|10|每个接收循环收到的最大事件计数。|
|eventProcessorOptions/prefetchCount|300|基础 `EventProcessorHost` 使用的默认预提取计数。 允许的最小值为 10。|
|initialOffsetOptions/type<sup>1</sup>|fromStart|当存储中不存在检查点时，在事件流中开始处理事件的位置。 选项有 `fromStart`、`fromEnd` 或 `fromEnqueuedTime`。 `fromEnd` 处理在函数应用开始运行后排队的新事件。 应用于所有分区。  请参阅 [EventProcessorOptions 文档](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider)了解详细信息。|
|initialOffsetOptions/enqueuedTimeUtc<sup>1</sup>|空值| 指定在事件流中开始处理的事件的排队时间。 如果 `initialOffsetOptions/type` 配置为 `fromEnqueuedTime`，则必需此设置。 支持 [DateTime.Parse()](/dotnet/standard/base-types/parsing-datetime) 支持的任何格式的时间，如 `2020-10-26T20:31Z`。 为清楚起见，还应指定时区。 如果未指定时区，Functions 将假设是运行函数应用的计算机的本地时区，即在 Azure 上运行时的 UTC。 请参阅 [EventProcessorOptions 文档](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider)了解详细信息。|

<sup>1</sup> 对 `initialOffsetOptions` 的支持始于 [EventHubs v4.2.0](https://github.com/Azure/azure-functions-eventhubs-extension/releases/tag/v4.2.0)。

> [!NOTE]
> 有关 Azure Functions 2.x 及更高版本中的 host.json 参考，请参阅 [Azure Functions 的 host.json 参考](../articles/azure-functions/functions-host-json.md)。

### <a name="functions-1x"></a>Functions 1.x

```json
{
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    }
}
```

|属性  |默认 | 说明 |
|---------|---------|---------| 
|maxBatchSize|64|每个接收循环收到的最大事件计数。|
|prefetchCount|不适用|基础 `EventProcessorHost` 将使用的默认预提取。| 
|batchCheckpointFrequency|1|创建 EventHub 游标检查点之前要处理的事件批数。| 

> [!NOTE]
> 有关 Azure Functions 1.x 中 host.json 的参考，请参阅 [Azure Functions 1.x 的 host.json 参考](../articles/azure-functions/functions-host-json-v1.md)。
