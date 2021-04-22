---
title: Azure 服务总线 - 消息浏览
description: 通过浏览和速览服务总线消息，Azure 服务总线客户端可以枚举队列或订阅中的所有消息。
ms.topic: article
ms.date: 03/29/2021
ms.openlocfilehash: f4943685f03eccb1c3b8da079973cf083bdcc416
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2021
ms.locfileid: "106090301"
---
# <a name="message-browsing"></a>消息浏览

通过消息浏览或速览，服务总线客户端可以枚举队列或订阅中的所有消息，以用于诊断和调试。

对队列执行速览操作会返回队列中的所有消息，而不仅仅是可以立即获取的消息。 对订阅执行速览操作会返回订阅消息日志中除计划消息之外的所有消息。 

已使用的消息和过期的消息通过异步“垃圾回收”运行进行清理。 此步骤未必会在消息过期后立即执行。 这就是为何速览操作可能会返回已过期的消息。 下一次对队列或订阅调用接收操作时，这些消息将被删除或设为死信。 请在尝试从队列中恢复延迟的消息时谨记此行为。 过期的消息无法再通过其他任何方式定期检索，即使是 Peek 返回的消息，也不例外。 返回这些消息是设计使然，因为 Peek 是反映日志当前状态的诊断工具。

Peek 还会返回以前已锁定且当前由其他接收程序处理的消息。 但是，因为 Peek 返回的是离线快照，所以无法在速览的消息上观察到消息的锁状态。

## <a name="peek-apis"></a>Peek API
## <a name="azuremessagingservicebus"></a>[Azure.Messaging.ServiceBus](#tab/dotnet)
[PeekMessageAsync](/dotnet/api/azure.messaging.servicebus.servicebusreceiver.peekmessageasync) 和 [PeekMessagesAsync](/dotnet/api/azure.messaging.servicebus.servicebusreceiver.peekmessagesasync) 方法位于接收方对象上：`ServiceBusReceiver`、`ServiceBusSessionReceiver`。 Peek 适用于队列、订阅及其对应的死信队列。

如果重复调用，`PeekMessageAsync` 方法会按顺序（从最低可用序号到最高可用序号）枚举队列或订阅日志中的所有消息。 这是消息的排队顺序，而不是消息的最终检索顺序。
PeekMessagesAsync 可以检索多个消息，并以枚举形式返回它们。 如果没有消息可返回，枚举对象为空，而不是 NULL。

你还可使用表示起始数的某个 SequenceNumber 来填充 [fromSequenceNumber](/dotnet/api/microsoft.servicebus.messaging.eventposition.fromsequencenumber) 参数，然后再次调用该方法，而不指定要进一步枚举的参数。 PeekBatch 的运作方式基本相同，不同之处在于它是一次性检索一组消息。


## <a name="microsoftazureservicebus"></a>[Microsoft.Azure.ServiceBus](#tab/dotnetold)
[Peek/PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_PeekAsync) 和 [PeekBatch/PeekBatchAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatchasync#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatchAsync_System_Int64_System_Int32_) 方法位于接受程序对象上：`MessageReceiver`、`MessageSession`。 Peek 适用于队列、订阅及其对应的死信队列。

如果重复调用，`Peek` 方法会按顺序（从最低可用序号到最高可用序号）枚举队列或订阅日志中的所有消息。 这是消息的排队顺序，并不是消息的最终检索顺序。

[PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) 可以检索多个消息，并以枚举形式返回它们。 如果没有消息可返回，枚举对象为空，而不是 NULL。

也可以使用带 [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber)（表示开始位置）的方法的重载，然后调用无参数方法重载来进一步枚举。 PeekBatch 的运作方式基本相同，不同之处在于它是一次性检索一组消息。


---

## <a name="next-steps"></a>后续步骤

若要了解有关服务总线消息传送的详细信息，请参阅以下主题：

* [服务总线队列、主题和订阅](service-bus-queues-topics-subscriptions.md)
* [服务总线队列入门](service-bus-dotnet-get-started-with-queues.md)
* [如何使用服务总线主题和订阅](service-bus-dotnet-how-to-use-topics-subscriptions.md)
