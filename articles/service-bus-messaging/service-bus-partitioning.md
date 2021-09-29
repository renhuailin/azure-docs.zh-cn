---
title: 创建分区的 Azure 服务总线队列和主题 | Microsoft 文档
description: 介绍如何使用多个消息中转站对服务总线队列和主题进行分区。
ms.topic: article
ms.date: 09/21/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: b6e0c0473ea5bf50ed64bf7abee2c66ab5789840
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128616073"
---
# <a name="partitioned-queues-and-topics"></a>分区队列和主题

Azure 服务总线使用多个消息中转站处理消息，并使用多个消息传送存储存储消息。 传统的队列或主题由单个消息中转站进行处理并存储在一个消息存储中。 服务总线 *分区* 允许跨多个消息中转站和消息存储对队列和主题或 *消息实体* 进行分区。 分区意味着分区实体的总吞吐量不再受单个消息中转站或消息存储的性能所限制。 此外，消息传送存储的临时中断不会导致分区的队列或主题不可用。 分区队列和主题可以包含所有先进的服务总线功能，如事务和会话支持。

> [!NOTE]
> 分区在为基本或标准 SKU 中的所有队列和主题创建实体时可用。 它不可再用于高级消息传送 SKU，但高级命名空间中任何先前已存在（从受支持时起）的分区实体将继续按预期方式工作。
 
无法更改任何现有队列或主题中的分区选项，只能在创建实体时设置此选项。

## <a name="how-it-works"></a>工作原理

每个分区队列或主题由多个分区构成。 每个分区存储在不同的消息传送存储中并由不同的消息中转站进行处理。 当向分区的队列或主题发送消息时，服务总线会将该消息分配到其中一个分区。 通过服务总线或使用发送方可指定的分区键随机进行选择。

客户端要从分区队列或从分区主题的订阅接收消息时，服务总线将查询所有分区以获取消息，并将从任何消息存储获取的第一条消息返回到接收方。 服务总线缓存其他消息并在收到其他接收请求时将它们返回。 接收客户端无法识别分区；分区队列或主题的面向客户端的行为（例如，读取、完成、延迟、死信、预提取）与常规实体行为相同。

在未分区实体上进行的速览操作始终返回最旧的消息，但在已分区实体上进行操作时情况有所不同， 它只返回其消息中转站首先响应的分区之一中的最旧消息。 不保证返回的消息是所有分区中最旧的消息。 

向分区队列或主题发送一条消息，或从分区队列或主题接收消息时无需额外付费。

> [!NOTE]
> 速览操作根据消息的序列号返回分区中最旧的消息。 对于已分区实体，序列号是相对于分区进行分发。 有关详细信息，请参阅[消息序列化和时间戳](../service-bus-messaging/message-sequencing.md)。

## <a name="use-of-partition-keys"></a>使用分区键

当一条消息在分区队列或主题中排队时，服务总线检查是否存在分区键。 如果找到，它会选择基于该键的分区。 如果找不到分区键，它会选择基于内部算法的分区。

### <a name="using-a-partition-key"></a>使用分区键

某些应用场景（例如会话或事务）要求将消息存储在特定的分区中。 所有这些应用场景都需要使用分区键。 使用相同的分区键的所有消息都分配到同一分区中。 如果该分区暂时不可用，服务总线返回一个错误。

根据应用场景，将不同的消息属性用作分区键：

SessionId：如果消息已设置会话 ID 属性，则服务总线会将其用作分区键。 这样一来，属于同一会话的所有消息都由同一消息中转站处理。 会话使服务总线得以保证消息顺序以及会话状态的一致性。

PartitionKey：如果消息已设置分区键属性但未设置会话 ID 属性，则服务总线将分区键属性值用作分区键。 如果消息同时设置了会话 ID 和分区键属性，则这两个属性必须相同。 如果分区键属性设置为与会话 ID 属性不同的值，则服务总线返回无效操作异常。 如果发送方发送非会话感知事务消息，应使用分区键属性。 分区键可确保事务中所发送的所有消息都由同一个消息传送中转站处理。

MessageId：如果队列或主题是通过[重复检测功能](duplicate-detection.md)创建的，并且未设置会话 ID 或分区键属性，则消息 ID 属性值将用作分区键。 （如果发送应用程序不分配消息 ID，Microsoft 客户端库会自动分配。）在这种情况下，同一消息的所有副本都由同一消息中转站处理。 此 ID 使服务总线能够检测并消除重复的消息。 如果未启用重复检测功能，则服务总线不会将消息 ID 属性视为分区键。

### <a name="not-using-a-partition-key"></a>不使用分区键

如果没有分区键，服务总线以轮循机制形式将消息分发到分区队列或主题的所有分区。 如果所选的分区不可用，服务总线会将消息分配给不同的分区。 这样一来，尽管消息传送存储暂时不可用，发送操作仍可成功。 但是，无法实现分区键提供的保证排序。

有关可用性（没有分区键）和一致性（使用分区键）之间的权衡的更深入讨论，请参阅[事件中心中的可用性和一致性](../event-hubs/event-hubs-availability-and-consistency.md)。 除了向用户公开的分区 ID 之外，此信息同样适用于已分区服务总线实体。

要给服务总线足够的时间将消息排入不同分区的队列中，客户端指定的发送消息的超时值必须大于 15 秒。 建议使用默认值 60 秒。

分区键会将消息“固定”到特定分区。 如果保存此分区的消息存储不可用，则服务总线会返回一个错误。 如果没有分区键，服务总线可以选择其他分区且操作会成功。 因此，建议如非必需不要提供分区键。

## <a name="advanced-topics"></a>高级主题

### <a name="use-transactions-with-partitioned-entities"></a>对分区实体使用事务

作为事务一部分发送的消息必须指定分区键。 键可以是以下属性之一：会话 ID、分区键或消息 ID。 所有作为同一事务一部分发送的消息必须指定相同的分区键。 如果尝试在事务中发送一条没有分区键的消息，服务总线会返回无效操作异常。 如果尝试在同一事务中发送多条具有不同分区键的消息，服务总线会返回无效操作异常。 例如：

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    ServiceBusMessage msg = new ServiceBusMessage("This is a message");
    msg.PartitionKey = "myPartitionKey";
    await sender.SendMessageAsync(msg); 
    await ts.CompleteAsync();
}
committableTransaction.Commit();
```

如果设置了任何作为分区键的属性，服务总线会将消息固定到特定分区。 无论是否使用事务，该行为都会发生。 建议如非必须不要指定分区键。

### <a name="use-transactions-in-sessions-with-partitioned-entities"></a>将会话中的事务与已分区的实体配合使用

要将事务消息发送到会话感知的主题或队列，消息必须设置会话 ID 属性。 如果还指定了分区键属性，该属性必须与会话 ID 属性相同。 如果它们不同，服务总线会返回无效操作异常。

与常规（非分区）的队列或主题不同，不可能使用单一事务来将多条消息发送到不同会话。 如果进行尝试，服务总线返回无效操作异常。 例如：

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    ServiceBusMessage msg = new ServiceBusMessage("This is a message");
    msg.SessionId = "mySession";
    await sender.SendMessageAsync(msg); 
    await ts.CompleteAsync();
}
committableTransaction.Commit();
```

### <a name="automatic-message-forwarding-with-partitioned-entities"></a>使用分区实体自动进行消息转发

服务总线支持从分区实体、向分区实体或在分区实体之间进行消息自动转发。 创建或更新队列和订阅时，可以启用此功能。 有关详细信息，请参阅[启用消息转发](enable-auto-forward.md)。 如果消息指定分区键（会话 ID、分区键或消息 ID），则该分区键用于目标实体。

## <a name="considerations-and-guidelines"></a>注意事项和指南
* **高度一致性功能**：如果实体使用会话、重复检测或显式控制分区键等功能，则消息传送操作一定会路由至特定的分区。 如果任何分区遇到过高的流量，或基础存储处于不正常状态，这些操作将失败，可用性会降低。 整体来说，一致性仍然远高于非分区实体；只有一部分流量会遇到问题，而不是所有流量。 有关详细信息，请参阅此处[对可用性和一致性的讨论](../event-hubs/event-hubs-availability-and-consistency.md)。
* **管理**：必须对实体的所有分区执行创建、更新及删除等操作。 如果任何分区处于不正常状态，可能会导致这些操作失败。 以“获取”操作来说，必须汇总来自所有分区的信息，例如消息计数。 如果任何分区处于不正常状态，则实体可用性状态会报告为受限制。
* **少量消息的情况**：对于这类情况，尤其是使用 HTTP 协议时，可能必须执行多次接收操作，才能获取所有消息。 对于接收请求，前端会在所有分区上执行接收，并缓存所有收到的响应。 相同连接上的后续接收请求将受益于此缓存，而且接收延迟会缩短。 不过，如果有多个连接或使用 HTTP，则会针对每个请求建立新的连接。 因此，不保证抵达相同的节点。 如果现有的所有消息均被锁定，而且在另一个前端中缓存，则接收操作返回 **null**。 消息最后会到期，可以再次接收它们。 建议使用 HTTP 保持连接。 在低容量场景中使用分区时，接收操作所用时间可能比预期的长。 因此，我们建议不要在这些场景中使用分区。 删除任何现有的分区实体，并在禁用分区的情况下重新创建它们，以提高性能。
* **浏览/速览消息**：速览操作不会始终返回要求的消息数。 此行为有两个常见的原因。 其中一个原因是消息集合的汇总大小超过设置的 256 KB 上限。 另一个原因是，在分区队列或主题中，分区可能没有足够的消息来返回请求的消息数。 一般情况下，如果应用程序要速览/浏览特定数目的消息，则应该重复调用速览操作，直到获得该数目的消息，或者已没有更多消息可速览为止。 有关详细信息（包括代码示例），请参阅[消息浏览](message-browsing.md)。

## <a name="partitioned-entities-limitations"></a>分区实体限制
当前，服务总线对分区队列和主题施加以下限制：

* 高级消息传送层不支持分区队列和主题。 使用 SessionId 在高级层中支持会话。 
* 分区队列和主题不支持在单个事务中发送属于不同会话的消息。
* 服务总线当前允许为每个命名空间最多创建 100 个分区队列或主题。 每个分区队列或主题都将计入每个命名空间的 10,000 个实体的配额中（不适用于高级层）。


## <a name="next-steps"></a>后续步骤
可使用 Azure 门户、PowerShell、CLI、资源管理器模板、.NET、Java、Python 和 JavaScript 来启用分区。 有关详细信息，请参阅[启用分区](enable-partitions.md)。 

在 [AMQP 1.0 协议指南](service-bus-amqp-protocol-guide.md)中了解 AMQP 1.0 消息传送规范的核心概念。

[Azure portal]: https://portal.azure.com
[QueueDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[TopicDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablepartitioning
[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto
[AMQP 1.0 support for Service Bus partitioned queues and topics]: ./service-bus-amqp-protocol-guide.md
