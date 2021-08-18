---
title: 向 Azure 事件中心中的事件添加自定义数据
description: 本文介绍如何向 Azure 事件中心中的事件添加自定义数据。
ms.topic: how-to
ms.date: 03/19/2021
ms.openlocfilehash: 2e3f94228c6f2fbe5d533635a01062c701aa9664
ms.sourcegitcommit: 6ea4d4d1cfc913aef3927bef9e10b8443450e663
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2021
ms.locfileid: "113297525"
---
# <a name="add-custom-data-to-events-in-azure-event-hubs"></a>向 Azure 事件中心中的事件添加自定义数据
由于事件主要包含一组不透明的字节，因此这些事件的使用者很难就如何处理它们做出明智的决策。 为了允许事件发布者为使用者提供更好的上下文，事件可能还包含自定义元数据，其形式为一组键-值对。 包含元数据的一种常见方案是提供有关事件所包含的数据类型的提示，以便使用者了解其格式，并可以对其进行适当反序列化。

> [!NOTE]
> 事件中心服务不会使用此元数据，它对事件中心服务没有任何意义；它仅用于在事件发布者和使用者之间进行协调。 

以下部分介绍如何使用不同的编程语言将自定义数据添加到事件中。 

## <a name="net"></a>.NET 

```csharp
var eventBody = new BinaryData("Hello, Event Hubs!");
var eventData = new EventData(eventBody);
eventData.Properties.Add("EventType", "com.microsoft.samples.hello-event");
eventData.Properties.Add("priority", 1);
eventData.Properties.Add("score", 9.0);
```

有关完整的代码示例，请参阅[发布带有自定义元数据的事件](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/samples/Sample04_PublishingEvents.md#publishing-events-with-custom-metadata)。

## <a name="java"></a>Java

```java
EventData firstEvent = new EventData("EventData Sample 1".getBytes(UTF_8));
firstEvent.getProperties().put("EventType", "com.microsoft.samples.hello-event");
firstEvent.getProperties().put("priority", 1);
firstEvent.getProperties().put("score", 9.0);
```

有关完整的代码示例，请参阅[发布带有自定义元数据的事件](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs/PublishEventsWithCustomMetadata.java)。


## <a name="python"></a>Python

```python
event_data = EventData('Message with properties')
event_data.properties = {'event-type': 'com.microsoft.samples.hello-event', 'priority': 1, "score": 9.0}
```

有关完整的代码示例，请参阅[批量发送带有属性的事件数据](https://github.com/Azure/azure-sdk-for-python/blob/azure-eventhub_5.3.1/sdk/eventhub/azure-eventhub/samples/async_samples/send_async.py)。

## <a name="javascript"></a>JavaScript

```javascript
let eventData = { body: "First event", properties: { "event-type": "com.microsoft.samples.hello-event", "priority": 1, "score": 9.0  } };
```


## <a name="next-steps"></a>后续步骤
请参阅以下快速入门和示例。 

- 快速入门：[.NET](event-hubs-dotnet-standard-getstarted-send.md)、[Java](event-hubs-java-get-started-send.md)、[Python](event-hubs-python-get-started-send.md)、[JavaScript](event-hubs-node-get-started-send.md)
- GitHub 上的示例：[.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples)、[Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples)、[Python](https://github.com/Azure/azure-sdk-for-python/blob/azure-eventhub_5.3.1/sdk/eventhub/azure-eventhub/samples)、[JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/eventhub/event-hubs/samples/v5/javascript)、[TypeScript](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/eventhub/event-hubs/samples/v5/typescript)
