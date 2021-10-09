---
title: Azure 服务总线中事务处理概述
description: 本文概要介绍了 Azure 服务总线中的事务处理和“发送方式”功能。
ms.topic: article
ms.date: 09/21/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 5eb3bf6eef551fd13788f7659eb8becede8e250d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128666205"
---
# <a name="overview-of-service-bus-transaction-processing"></a>服务总线事务处理概述

本文将讨论 Microsoft Azure 服务总线的事务功能。 很多讨论已在[服务总线中的 AMQP 事务示例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TransactionsAndSendVia/TransactionsAndSendVia/AMQPTransactionsSendVia)中进行了说明。 本文仅限于概述服务总线中的事务处理和发送方式  功能，虽然原子事务示例在作用域内更广泛且更复杂。

> [!NOTE]
> 基本层的服务总线不支持事务。 标准层和高级层支持事务。 有关这些层之间的差异，请参阅[服务总线定价](https://azure.microsoft.com/pricing/details/service-bus/)。

## <a name="transactions-in-service-bus"></a>服务总线中的事务

一个 *事务* 将两个或更多操作组合成执行作用域  。 就本质而言，此类事务必须确保所有操作属于给定的操作组，无论联合成功还是失败。 在这方面，事务作为一个单元进行操作，通常称为原子性  。

服务总线是事务性消息代理，并确保针对其消息存储的所有内部操作的事务完整性。 服务总线内部的所有消息传输，如将消息移到[死信队列](service-bus-dead-letter-queues.md)或在实体之间[自动转发](service-bus-auto-forwarding.md)消息，都是事务性的。 因此，如果服务总线接受一条消息，则该消息已存储并标有一个序列号。 从那时起，服务总线内的任何消息传输都是实体之间协调的操作，将从不会导致消息丢失（源成功而目标失败）或重复（源失败而目标成功）。

服务总线支持对事务作用域内的消息传送实体（队列、主题、订阅）执行分组操作。 例如，可以从事务作用域内将多条消息发送到一个队列，在事务成功完成时，这些消息将仅提交到该队列的日志。

## <a name="operations-within-a-transaction-scope"></a>事务作用域内的操作

可以在事务作用域内执行的操作如下所示：

- 发送
- 完成
- 放弃
- 死信
- 延迟
- 续订锁

接收操作不包括在内，因为它假定应用程序使用 peek-lock 模式在某个接收循环内或使用回调获取消息，然后才打开用于处理消息的事务作用域。

然后，消息的处置（完成、放弃、死信、延迟）会在事务作用域内进行，并依赖于在事务处理的整体结果。

## <a name="transfers-and-send-via"></a>传输和“发送方式”

若要启用将数据从队列或主题到处理器，然后到另一个队列或主题的事务性移交，服务总线支持传输。 在传输操作中，发送方先将消息发送到“传输队列或主题”，然后传输队列或主题立即使用自动转发功能所依赖的同一强大传输实现将消息移到预期的目标队列或主题。 消息永远不会以对传输队列或主题的使用者可见的方式提交到传输队列或主题的日志中。

当传输队列或主题本身是发送方的输入消息的源时，此事务功能的优势越明显。 换而言之，服务总线可以“通过”传输队列或主题将消息传输到目标队列或主题中，同时对输入消息执行完成（或延迟/死信）操作，所有这一切都通过一个原子操作完成。 

如果需要从主题订阅接收，然后将其发送到相同事务中的队列或主题，则传输实体必须是主题。 在此方案中，启动主题上的事务范围，从事务范围内的订阅接收，并通过传输主题发送到队列或主题目标。 

### <a name="see-it-in-code"></a>在代码中查看它

若要设置此类传输，需创建通过传输队列以目标队列为目标的消息发送方。 还将设置接收方，以便从该同一队列拉取消息。 例如：

简单事务处理随后使用这些元素，如以下示例所示。 若要参考完整示例，请参阅 [GitHub 上的源代码](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/servicebus/Azure.Messaging.ServiceBus/samples/Sample06_Transactions.md#transactions-across-entities)：

```csharp
var options = new ServiceBusClientOptions { EnableCrossEntityTransactions = true };
await using var client = new ServiceBusClient(connectionString, options);

ServiceBusReceiver receiverA = client.CreateReceiver("queueA");
ServiceBusSender senderB = client.CreateSender("queueB");

ServiceBusReceivedMessage receivedMessage = await receiverA.ReceiveMessageAsync();

using (var ts = new TransactionScope(TransactionScopeAsyncFlowOption.Enabled))
{
    await receiverA.CompleteMessageAsync(receivedMessage);
    await senderB.SendMessageAsync(new ServiceBusMessage());
    ts.Complete();
}
```


## <a name="timeout"></a>超时
事务在 2 分钟后超时。 事务计时器在事务中的第一个操作启动时启动。 

## <a name="next-steps"></a>后续步骤

有关服务总线队列的详细信息，请参阅以下文章：

* [如何使用服务总线队列](service-bus-dotnet-get-started-with-queues.md)
* [使用自动转发链接服务总线实体](service-bus-auto-forwarding.md)
* [自动转发示例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AutoForward)
* [服务总线中的原子事务示例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AtomicTransactions)
* [Azure 队列和服务总线队列比较](service-bus-azure-and-service-bus-queues-compared-contrasted.md)


