---
title: Azure 服务总线 - 消息浏览
description: 通过浏览和查看服务总线消息，Azure 服务总线客户端可以枚举队列或订阅中的所有消息。
ms.topic: article
ms.date: 11/11/2020
ms.openlocfilehash: c52c9c967d4eada1a931e188ed4d25f7691cfb91
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94553635"
---
# <a name="message-browsing"></a>消息浏览

通过消息浏览（或查看），服务总线客户端可以枚举队列或订阅中的所有消息，以便进行诊断和调试。

队列上的速览操作返回队列中的所有消息，而不是仅返回可供使用或循环立即获取的消息 `Receive()` `OnMessage()` 。 每个消息的 `State` 属性指明消息是有效（可供接收）、[延迟](message-deferral.md)还是[已计划](message-sequencing.md)。 订阅上的速览操作返回订阅消息日志中的所有消息（计划消息除外）。 

已使用和过期的消息通过异步 "垃圾回收" 运行进行清理。 在消息过期后，此步骤可能不一定立即发生。 因此， `Peek` 可能会返回已过期的消息。 下一次对队列或订阅调用接收操作时，这些消息将被删除或死信。 尝试从队列中恢复延迟的消息时，请记住此行为。 过期的消息不再有资格通过任何其他方式进行常规检索，即使它是通过速览返回的也是如此。 按设计方式返回这些消息是为了使速览成为反映日志当前状态的诊断工具。

速览还会返回已锁定并且当前正由其他接收方处理的消息。 但是，因为速览返回断开连接的快照，所以无法在扫视消息上观察消息的锁定状态。 当应用程序尝试读取 [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.lockeduntilutc) 和 [LockToken](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.locktoken#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_LockToken) 属性时，该属性将引发 [InvalidOperationException](/dotnet/api/system.invalidoperationexception) 。

## <a name="peek-apis"></a>Peek API

[速览/PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_PeekAsync)和 [PeekBatch/PeekBatchAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatchasync#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatchAsync_System_Int64_System_Int32_)方法存在于 .net 和 Java 客户端库以及接收方对象上： **MessageReceiver** 、 **MessageSession** 。 速览适用于队列、订阅及其各自的死信队列。

重复调用时， **速览** 按顺序枚举队列或订阅日志中的所有消息，顺序从最小的可用序列号到最高。 这是消息的排队顺序，而不是最终检索消息的顺序。

[PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) 可以检索多个消息，并以枚举形式返回它们。 如果没有消息可返回，枚举对象为空，而不是 NULL。

还可以通过 [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) 使用方法的重载，以便在其中启动，然后调用无参数方法重载来进行进一步的枚举。 PeekBatch 的运作方式基本相同，不同之处在于它是一次性检索一组消息。

## <a name="next-steps"></a>后续步骤

若要了解有关服务总线消息传送的详细信息，请参阅以下主题：

* [服务总线队列、主题和订阅](service-bus-queues-topics-subscriptions.md)
* [服务总线队列入门](service-bus-dotnet-get-started-with-queues.md)
* [如何使用服务总线主题和订阅](service-bus-dotnet-how-to-use-topics-subscriptions.md)
