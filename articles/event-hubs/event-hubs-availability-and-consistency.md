---
title: 可用性和一致性 - Azure 事件中心 | Microsoft Docs
description: 如何使用分区为 Azure 事件中心提供最大程度的可用性和一致性。
ms.topic: article
ms.date: 03/15/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 02f26aaf369db177d77d27f3274b34fe1c400419
ms.sourcegitcommit: aba63ab15a1a10f6456c16cd382952df4fd7c3ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/25/2021
ms.locfileid: "107988939"
---
# <a name="availability-and-consistency-in-event-hubs"></a>事件中心内的可用性和一致性
本文提供了有关 Azure 事件中心支持的可用性和一致性的信息。 

## <a name="availability"></a>可用性
Azure 事件中心分散了跨数据中心内多个故障域的群集中单个甚至计算机整个机架发生灾难性故障的风险。 它实现了透明的故障检测和故障转移机制，使服务将继续在有保证的服务级别内运行，并且在发生此类故障时通常不会出现明显的中断。 

如果在启用了[可用性区域](../availability-zones/az-overview.md)的情况下已创建了事件中心命名空间，则中断风险会进一步分散到三个物理上分离的设备，并且该服务有足够的容量预留，可立即应对整个设施的全部灾难性损失。 有关详细信息，请参阅 [Azure 事件中心 - 异地灾难恢复](event-hubs-geo-dr.md)。

如果客户端应用程序将事件发送到事件中心，但未指定分区，则事件会自动在事件中心内的各个分区之间分布。 如果某个分区由于某种原因而不可用，则会在其余分区之间分布事件。 此行为可实现最大运行时间量。 对于需要最大运行时间的用例，此模型是首选模型，而不是将事件发送到特定分区。 

## <a name="consistency"></a>一致性
在某些方案中，事件的排序可能十分重要。 例如，可能希望后端系统先处理更新命令，再处理删除命令。 在此方案中，客户端应用程序将事件发送到特定分区，以便保留排序。 当使用者应用程序从分区使用这些事件时，将按顺序读取它们。 

使用此配置，请记住，如果发送到的特定分区不可用，则会收到错误响应。 作为对比，如果未与单个分区关联，则事件中心服务会将事件发送到下一个可用分区。

因此，如果高可用性最为重要，则不要以特定分区为目标（使用分区 ID/分区键）。 使用分区 ID/键会将事件中心的可用性降级到分区级别。 在这种情况下，需在可用性（无分区 ID/键）和一致性（将事件固定到特定分区）之间做出明确选择。 有关事件中心内分区的详细信息，请参阅[分区](event-hubs-features.md#partitions)。

## <a name="appendix"></a>附录

### <a name="send-events-without-specifying-a-partition"></a>发送事件时不指定分区
我们建议在不设置分区信息的情况下将事件发送到事件中心，以允许事件中心服务在分区之间均衡负载。 请参阅以下快速入门，了解如何以不同编程语言实现此目的。 

- [使用 .NET 发送事件](event-hubs-dotnet-standard-getstarted-send.md)
- [使用 Java 发送事件](event-hubs-java-get-started-send.md)
- [使用 JavaScript 发送事件](event-hubs-python-get-started-send.md)
- [使用 Python 发送事件](event-hubs-python-get-started-send.md)


### <a name="send-events-to-a-specific-partition"></a>将事件发送到特定分区
本部分介绍如何使用不同的编程语言将事件发送到特定分区。 

### <a name="net"></a>[.NET](#tab/dotnet)
若要将事件发送到特定分区，请使用 [EventHubProducerClient.CreateBatchAsync](/dotnet/api/azure.messaging.eventhubs.producer.eventhubproducerclient.createbatchasync#Azure_Messaging_EventHubs_Producer_EventHubProducerClient_CreateBatchAsync_Azure_Messaging_EventHubs_Producer_CreateBatchOptions_System_Threading_CancellationToken_) 方法通过在 [CreateBatchOptions](/dotnet/api/azure.messaging.eventhubs.producer.createbatchoptions) 中指定 `PartitionId` 或 `PartitionKey` 来创建批处理。 以下代码通过指定分区键将一批事件发送到特定分区。 事件中心确保共享分区键值的所有事件存储在一起，并按到达顺序进行传递。

```csharp
var batchOptions = new CreateBatchOptions { PartitionKey = "cities" };
using var eventBatch = await producer.CreateBatchAsync(batchOptions);
```

还可以通过在 [SendEventOptions](/dotnet/api/azure.messaging.eventhubs.producer.sendeventoptions) 中指定 PartitionId 或 PartitionKey 来使用 [EventHubProducerClient.SendAsync](/dotnet/api/azure.messaging.eventhubs.producer.eventhubproducerclient.sendasync#Azure_Messaging_EventHubs_Producer_EventHubProducerClient_SendAsync_System_Collections_Generic_IEnumerable_Azure_Messaging_EventHubs_EventData__Azure_Messaging_EventHubs_Producer_SendEventOptions_System_Threading_CancellationToken_) 方法 。

```csharp
var sendEventOptions  = new SendEventOptions { PartitionKey = "cities" };
// create the events array
producer.SendAsync(events, sendOptions)
```


### <a name="java"></a>[Java](#tab/java)
若要将事件发送到特定分区，请使用 [createBatch](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/main/java/com/azure/messaging/eventhubs/EventHubProducerClient.java) 方法通过在 [createBatchOptions](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/main/java/com/azure/messaging/eventhubs/models/CreateBatchOptions.java) 中指定分区 ID 或分区键来 。 以下代码通过指定分区键将一批事件发送到特定分区。 

```java
CreateBatchOptions batchOptions = new CreateBatchOptions();
batchOptions.setPartitionKey("cities");
```

还可以通过在 [SendOptions](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/main/java/com/azure/messaging/eventhubs/models/SendOptions.java) 中指定分区 ID 或分区键来使用 [EventHubProducerClient.send](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/main/java/com/azure/messaging/eventhubs/EventHubProducerClient.java) 方法 。

```java
List<EventData> events = Arrays.asList(new EventData("Melbourne"), new EventData("London"), new EventData("New York"));
SendOptions sendOptions = new SendOptions();
sendOptions.setPartitionKey("cities");
producer.send(events, sendOptions);
```


### <a name="python"></a>[Python](#tab/python) 
若要将事件发送到特定分区，请在使用 [`EventHubProducerClient.create_batch`](/python/api/azure-eventhub/azure.eventhub.eventhubproducerclient#create-batch---kwargs-) 方法创建批处理时，指定 `partition_id` 或 `partition_key`。 然后，使用 [`EventHubProducerClient.send_batch`](/python/api/azure-eventhub/azure.eventhub.aio.eventhubproducerclient#send-batch-event-data-batch--typing-union-azure-eventhub--common-eventdatabatch--typing-list-azure-eventhub-) 方法将批处理发送到事件中心的分区。 

```python
event_data_batch = await producer.create_batch(partition_key='cities')
```

还可以通过指定 `partition_id` 或 `partition_key` 参数的值来使用 [EventHubProducerClient.send_batch](/python/api/azure-eventhub/azure.eventhub.eventhubproducerclient#send-batch-event-data-batch----kwargs-) 方法。

```python
producer.send_batch(event_data_batch, partition_key="cities")
```

### <a name="javascript"></a>[JavaScript](#tab/javascript)
若要将事件发送到特定分区，请通过指定 `partitionId` 或 `partitionKey` 使用 [EventHubProducerClient.CreateBatchOptions](/javascript/api/@azure/event-hubs/eventhubproducerclient#createBatch_CreateBatchOptions_) 对象来[创建批处理](/javascript/api/@azure/event-hubs/eventhubproducerclient#createBatch_CreateBatchOptions_)。 然后，使用 [EventHubProducerClient.SendBatch](/javascript/api/@azure/event-hubs/eventhubproducerclient#sendBatch_EventDataBatch__OperationOptions_) 方法将批处理发送到事件中心。 

请参阅以下示例。

```javascript
const batchOptions = { partitionKey = "cities"; };
const batch = await producer.createBatch(batchOptions);
```

还可以通过在 [SendBatchOptions](/javascript/api/@azure/event-hubs/sendbatchoptions) 中指定分区 ID 或分区键来使用 [EventHubProducerClient.sendBatch](/javascript/api/@azure/event-hubs/eventhubproducerclient#sendBatch_EventData____SendBatchOptions_) 方法 。

```javascript
const sendBatchOptions = { partitionKey = "cities"; };
// prepare events
producer.sendBatch(events, sendBatchOptions);
```

---



## <a name="next-steps"></a>后续步骤
访问以下链接可以了解有关事件中心的详细信息：

- [事件中心服务概述](./event-hubs-about.md)
- [事件中心术语](event-hubs-features.md)
