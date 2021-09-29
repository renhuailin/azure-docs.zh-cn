---
title: 消息复制和跨区域联合 - Azure 服务总线 | Microsoft Docs
description: 本文概述了使用 Azure 服务总线的事件复制和跨区域联合。
ms.topic: article
ms.date: 09/28/2021
ms.openlocfilehash: 0f0d2b5f96ea65a6d0fd0f5664ef98948dc46c19
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129211860"
---
# <a name="message-replication-and-cross-region-federation"></a>消息复制和跨区域联合

在命名空间中，Azure 服务总线支持[使用自动转发创建链接队列和主题订阅的拓扑](service-bus-auto-forwarding.md)，以支持各种路由模式的实现。 例如，可以为合作伙伴提供其具有发送或接收权限的专用队列，如果需要，可以暂时将其暂停，并灵活地将它们连接到应用程序专用的其他实体。 还可以创建复杂的多阶段路由拓扑，或创建邮箱样式的队列，该队列将清空类似于队列的主题订阅，并为每个订阅服务器提供更多的存储容量。 

许多复杂的解决方案还要求跨命名空间边界复制消息，以便实现这些模式和其他模式。 消息可能需要在与多个不同应用程序租户相关的命名空间之间流动，或者跨多个不同的 Azure 区域流动。 

解决方案将在不同区域中维护多个服务总线命名空间，并在队列和主题之间复制消息，并且/或者你将与源和目标（如 [Azure 事件中心](../event-hubs/event-hubs-about.md)、[Azure IoT 中心](../iot-fundamentals/iot-introduction.md)或 [Apache Kafka](https://kafka.apache.org)）交换消息。 

本文重点介绍了这些场景。 

## <a name="federation-patterns"></a>联合模式

出于多种可能的动机，你可能想要在服务总线实体（如队列或主题）之间或服务总线与其他源和目标之间移动消息。 

与[事件中心](../service-bus-messaging/service-bus-federation-overview.md)的类似模式相比，类似队列的实体的联合更复杂，因为消息队列承诺其使用者对任何单个消息具有独占所有权，且应在消息传递中保持到达顺序，并使代理在[竞争性使用者](/azure/architecture/patterns/competing-consumers)之间协调消息的公平分发。 

存在一些实际的障碍，包括 [CAP 定理](https://en.wikipedia.org/wiki/CAP_theorem)的约束，因而很难提供在多个区域同时可用的队列的统一视图，并且允许区域分布式[竞争性使用者](/azure/architecture/patterns/competing-consumers)获得消息的独占所有权。 此类异地分布式队列不仅要求对消息进行完全一致的复制，而且要求对每个消息的传递状态进行完全一致的复制，然后才能将消息提供给使用者。 假设的区域分布式队列的完全一致性目标与所有 Azure 服务总线客户在考虑联合场景时的关键目标存在直接冲突：其解决方案的最大可用性和可靠性。 

此处所示的模式侧重于可用性和可靠性，同时也旨在最好地避免信息丢失和消息的重复处理。 

### <a name="resiliency-against-regional-availability-events"></a>针对区域可用性事件的复原能力 

尽管最大可用性和可靠性是服务总线的首要工作优先事项，但是由于网络或名称解析问题，或者服务总线实体可能确实暂时没有响应或返回错误，有许多因素会阻止生产者或使用者与其分配的“主要”服务总线通信。 指定的消息处理器也可能变得不可用。

这种情况并不是“灾难性的”，你不会像在灾难恢复情况下那样完全放弃区域部署，但是一些应用程序的业务场景可能已经受到持续时间不超过几分钟甚至几秒钟的可用性事件的影响。 Azure 服务总线通常用于混合云环境以及驻留在网络边缘的客户端，例如零售商店、餐馆、银行分行、制造厂、物流设施和机场。 网络路由或拥塞问题可能会影响任何一个站点访问其分配的服务总线终结点的能力，而另一个区域中的辅助终结点可能是可访问的。 与此同时，处理来自这些站点的消息的系统可能仍然可以不受阻碍地访问主服务总线终结点和辅助服务总线终结点。 

有许多这样的混合云和边缘应用程序的实际示例，它们对网络路由问题或服务总线实体的临时可用性问题所带来影响的业务容忍度较低。 这些示例包括在零售店处理付款、在机场登机口登机、在餐馆用手机点菜等，当可靠的通信路径不可用时，这一切都会立即完全停止。

在此类别中，我们讨论了三种不同的分布式模式：“全主动”复制、“主动-被动”复制和“溢出”复制。 

#### <a name="all-active-replication"></a>全主动复制

使用“全主动”复制模式，可在多个命名空间（和区域）中使用同一逻辑主题（或队列）的活动副本，并且可在所有副本中使用全部消息，而不管它们在何处排队。 模式通常会保留消息相对于任何发布者的顺序。 

![全主动模式](media/service-bus-federation-overview/mirrored-topics.jpg)

如图所示，该模式通常依赖于服务总线主题。 每个应参与复制方案的命名空间都有一个主题。 每个主题都有一个“复制订阅”，用于应将消息复制到的任何其他主题。 在上图中，我们只有一对主题，因此对于各自的另一个主题有一个复制订阅。 在有三个命名空间 {n1，n2，n3} 的场景中，命名空间 n1 中的一个主题将具有两个复制订阅，一个用于 n2 中的相应主题，另一个用于 n3 中的相应主题   。 

每个复制订阅都有一条结合了 SQL 筛选器表达式 (`replicated <> 1`) 和 SQL 操作 (`set replicated = 1`) 的规则。 该规则的筛选器确保只有未设置自定义属性 `replication` 或不具有值 `1` 的消息才符合此订阅的条件，并且该操作随后将每个选定消息的确切属性设置为值 `1`。 这样做的结果是，消息复制到相应的主题时，它不再符合在相反的方向上进行复制的条件，因此可以避免消息在副本之间弹跳。

使用 Azure CLI，可以像这样轻松地将具有相应规则的订阅添加到任何主题中。

```azurecli

az servicebus topic subscription rule create --resource-group myresourcegroup \
   --namespace mynamespace --topic-name mytopic 
   --subscription-name replication --name replication \
   --action-sql-expression "set replication = 1" \
   --filter-sql-expression "replication IS NULL"
```

为了对队列建模，每个主题仅限于所有使用者共享的一个常规订阅（复制订阅除外）。

> 全主动复制模型将发送到任何主题的每个消息的副本放入每个主题。 这意味着每个区域中的应用程序代码将会看到并处理所有消息。
> 此模式适用于将数据共享到多个区域或通常需要进行冗余处理的场景。 如果只需要像常规队列那样处理每条消息一次，需要考虑以下两种模式之一。  

#### <a name="active-passive-replication"></a>主动-被动复制

“主动-被动”复制模式是前一个模式的变体，在这种模式下，应用程序仅主动使用一个主题（“主要主题”）来发送和接收消息，并在主要主题可能变为不可用或无法访问时将消息复制到次要主题。 

![主动-被动模式](media/service-bus-federation-overview/mirrored-topics-passive.jpg)

此模式与前一个模式之间的主要区别在于，从主要主题到次要主题的复制是单向的。 次要主题永远不会成为主要主题，而是当主要主题暂时不可用时的后备选项。 

使用此模式的好处是它会尝试最大程度地减少重复处理。 在复制期间，`TimeToLive` 消息属性被设置为复制消息上的持续时间，该持续时间反映主要主题故障将导致故障转移的预期时间。 例如，如果用例场景要求在从主要主题检索消息开始出现问题后最多 1 分钟内将使用者切换到次要主题，则理想情况下，次要主题应拥有你在主要主题中无法访问的所有消息，但是在问题出现之前，你已经从主要主题处理了最少数量的消息。 如果在复制过程中将 `TimeToLive` 设置为该时间段的两倍（即 2 分钟）（规则操作中的 `set sys.TimeToLive = '0:2:0'`），则次要主题将仅保留消息 2 分钟，并丢弃较旧的消息。 这意味着当接收方切换到次要主题时，它可以快速读取并丢弃早于其处理的最后一条消息的消息，然后从它尚未看到的第一条消息开始进行处理。 实际的保留期将取决于特定的用例和你希望并且可以在应用程序中切换到次要主题的速度。 `TimeToLive` 设置的范围从几秒到几天不等。 

当应用程序使用次要主题时，它还可以直接发布到次要主题，然后该主题就可以充当任何常规主题。 在切换到次要主题后，使用者将看到复制消息和直接发布到次要主题的消息的混合。 因此，应用程序应首先将发布切换回主要主题，并仍允许在将使用者切换回次要主题之前清空本地发布的消息。 由于一旦主要主题再次可用，复制就会自动恢复，因此使用者也会获得这段时间内发布到主要主题的新消息，尽管延迟稍高一些。 

> 此模式适用于消息应仅处理一次的场景。 应用程序需要协作来跟踪它从主要主题处理的消息，因为它将在次要主题的故障转移时段找到重复的消息，并且在切换回主要主题时，它将再次找到重复的消息。
> 重复数据删除条件最好是应用程序提供的 `MessageId`。 `EnqueuedTimeUtc` 值还适合作为水印指示器，但应用程序需要允许主要主题和次要主题之间存在一定程度的时钟偏移（几秒钟），就像任何分布式系统一样。


#### <a name="spillover-replication"></a>溢出复制

“溢出”复制模式支持主动/主动使用多个区域中的多个服务总线实体，以处理以下情况：服务总线运行正常，但使用者因待处理的消息数量过多而不堪重负或完全不可用。 原因可能是支持使用者进程的数据库可能很慢或不可用。 此模式适用于纯队列和主题订阅。  

![溢出复制](media/service-bus-federation-overview/spillover.jpg)  

如图所示，溢出复制模式将消息从队列或订阅的关联[死信队列](service-bus-dead-letter-queues.md)复制到其他命名空间中的成对队列或主题。 

在不出现故障的情况下，这两个命名空间是并行使用的，每个命名空间接收总体消息流量的某个子集，并由它们的关联使用者处理该子集。 一旦某个使用者开始表现出高故障率或完全停止，则相应的消息将由于其超过传递计数或过期而最终进入死信队列。 然后，复制任务会选取它们并将它们重新排队到配对队列中，然后将它们显示给可能正常运行的使用者。 

如果必须在一定期限内进行处理，则应设置队列和/或消息的 `TimeToLive`，以使溢出次要主题仍可以及时进行处理，例如，`TimeToLive` 可以设置为允许时间的一半。

与[全主动模式](#all-active-replication)一样，应用程序可以向消息中添加一个指示器，指示消息是否已经被复制过一次，这样它们就不会在队列对之间传递，而是被发布到一个辅助队列中，该队列充当复合模式的死信队列。

> 此模式适用于以下场景：最需要关注的是防范使用者或使用者所依赖的资源中的可用性问题，以及在其中一个成对队列上重新分配流量峰值。 它还提供了保护，以防止如果使用者从两个队列中读取数据，则其中一个命名空间将变得不可用，但是 `TimeToLive` 到期所施加的复制滞后可能会导致该时段内的消息滞留在不可用的命名空间中。 

### <a name="latency-optimization"></a>延迟优化 

主题用于将信息分发给多个使用者。 在某些情况下，尤其是使用者具有广泛地理分布的情况，将主题中的消息复制到更靠近使用者的辅助命名空间中的主题可能会有利。

![延迟优化](media/service-bus-federation-overview/latency-optimization.jpg)  

例如，在区域性、大陆性中心之间共享数据时，在中心之间只传输一次信息并让使用者从这些中心获取数据副本会更高效。 

复制传输可以分批完成，而使用者通常会逐个获取并处理消息。 例如，北美和欧洲之间的基本网络延迟为 100 ms，与位于同一区域的实体相比，在两次往返于远程实体以获取和处理消息的过程中，处理每个消息的时间要多 200 ms。 

### <a name="validation-reduction-and-enrichment"></a>验证、减少和扩充

消息可以由你自己的解决方案外部的客户端提交到服务总线队列或主题。

![验证、减少、扩充](media/service-bus-federation-overview/validation.jpg)  

此类消息可能要求检查以确定是否符合给定架构，并检查是否有需要删除或确定为死信的非合规消息。 某些消息可能需要通过省略数据来降低复杂性，某些可能需要通过基于参考数据查找添加数据来进行充实。 可以通过复制任务中的自定义功能来执行这些操作。 

### <a name="stream-to-queue-replication"></a>流到队列复制

Azure 事件中心是用于处理大量传入事件的理想解决方案。 但是事件中心和类似的引擎（如 Apache Kafka）都不提供服务托管的[竞争性使用者](/azure/architecture/patterns/competing-consumers)模型，在该模型中，多个使用者可以并发地处理来自同一源的消息，而不冒重复处理的风险，并在处理完这些消息后最终解决这些消息。 

![集成](media/service-bus-federation-overview/hub-to-queue.jpg)

队列复制流将单个事件中心分区的内容或完整事件中心的内容传输到服务总线队列，在该队列中消息可以与竞争性使用者一起安全地、以事务方式处理。 通过此复制，还可以将所有其他服务总线功能用于这些消息，包括按主题进行路由和基于会话的多路分解。 

### <a name="consolidation-and-normalization"></a>合并和规范化 

全球解决方案通常由很大程度上独立（包括具有自己的处理能力）的区域占地组成，但是从区域和全球的角度来看需要数据集成，因此需要对相同的消息数据进行中央合并，并在各自的区域占地中从本地视角进行评估。 

![合并](media/service-bus-federation-overview/merge.jpg)

规范化是合并场景的一种形式，其中两个或多个传入的消息序列携带相同类型的信息，但具有不同的结构或不同的编码，并且必须先对消息进行转码或转换，然后才能使用它们。 

规范化还可以包括加密工作，例如解密端到端的加密有效负载，并使用不同的密钥和算法为下游使用者受众进行重新加密。 

### <a name="splitting-and-routing"></a>拆分和路由

服务总线主题及其订阅规则通常用于筛选特定受众的消息流，然后该受众从订阅获取筛选集。 

![拆分](media/service-bus-federation-overview/split.jpg)

在全球系统中，这些消息的受众是全球分布的，或者属于不同的应用程序，可以使用复制将消息从此类订阅传输到使用消息的不同命名空间中的队列或主题。

### <a name="replication-applications-in-azure-functions"></a>Azure Functions 中的复制应用程序

实现上述模式需要为要配置和运行的复制任务提供可缩放且可靠的执行环境。 在 Azure 上，最适合无状态任务的运行时环境是 [Azure Functions](../azure-functions/functions-overview.md)。 

Azure Functions 可以在 [Azure 托管标识](../active-directory/managed-identities-azure-resources/overview.md)下运行，这样复制任务就可以与源和目标服务的基于角色的访问控制规则集成，而不必沿复制路径管理机密。 对于需要显式凭据的复制源和目标，Azure Functions 可以将这些凭据的配置值保存在 [Azure Key Vault](../key-vault/general/overview.md) 内部受严格访问控制的存储中。

Azure Functions 还允许复制任务直接与 Azure 虚拟网络和所有 Azure 消息传送服务的[服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)集成，且 Azure Functions 已与 [Azure Monitor](../azure-monitor/overview.md) 集成。

最重要的是，Azure Functions 为 [Azure 事件中心](../azure-functions/functions-bindings-service-bus.md)、[Azure IoT 中心](../azure-functions/functions-bindings-event-iot.md)、[Azure 服务总线](../azure-functions/functions-bindings-service-bus.md)、[Azure 事件网格](../azure-functions/functions-bindings-event-grid.md)和 [Azure 队列存储](../azure-functions/functions-bindings-storage-queue.md)提供了预生成、可扩展的触发器和输出绑定，为 [RabbitMQ](https://github.com/azure/azure-functions-rabbitmq-extension) 和[Apache Kafka](https://github.com/azure/azure-functions-kafka-extension) 提供了自定义扩展。 大多数触发器将根据记录的指标通过纵向扩展和缩减并发执行实例的数量来动态适应吞吐量需求。 

使用 Azure Functions 消耗计划，当没有消息可供复制时，预生成的触发器甚至可以纵向缩减至零，这意味着使配置处于纵向扩展准备状态不会产生任何费用。 使用消耗计划的主要缺点是，从此状态“唤醒”复制任务的延迟明显高于保持基础结构运行的托管计划的延迟。  

与所有这些不同的是，用于消息传递和事件处理的最常见的复制引擎（例如 Apache Kafka 的 [MirrorMaker](http://kafka.apache.org/documentation/#basic_ops_mirror_maker)）需要你提供一个主机环境并自行缩放复制引擎。 这包括配置和集成安全性和网络功能以及辅助监视数据流，你仍然没有机会将自定义复制任务注入到流中。 

## <a name="next-steps"></a>后续步骤

本文介绍了一系列联合模式，并解释了 Azure Functions 在 Azure 中作为事件和消息复制运行时的作用。 

接下来，需要了解如何使用 Azure Functions 设置复制器应用程序，以及如何在事件中心和各种其他事件和消息传递系统之间复制事件流：

- [Azure Functions 中的复制应用程序](service-bus-federation-replicator-functions.md)
- [复制服务总线实体之间的事件](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopy)
- [将事件路由到 Azure 事件中心](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopyToEventHub)
- [从 Azure 事件中心获取事件](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus)

[1]: ./media/service-bus-auto-forwarding/IC628632.gif