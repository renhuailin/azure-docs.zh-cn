---
title: 消息复制和跨区域联合-Azure 服务总线 |Microsoft Docs
description: 本文概述了与 Azure 服务总线之间的事件复制和跨区域联合。
ms.topic: article
ms.date: 12/12/2020
ms.openlocfilehash: d5153820feab8b7901356838ec435d992aa82116
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/29/2020
ms.locfileid: "97803266"
---
# <a name="message-replication-and-cross-region-federation"></a>消息复制和跨区域联合

在命名空间中，Azure 服务总线支持 [使用自动转发创建链式队列和主题订阅的拓扑](service-bus-auto-forwarding.md) ，以允许实现各种路由模式。 例如，你可以为合作伙伴提供具有 "发送" 或 "接收" 权限的专用队列，如果需要，可以暂时将其挂起，并灵活地将它们连接到应用程序专用的其他实体。 你还可以创建复杂的多阶段路由拓扑，也可以创建邮箱样式的队列，该队列将清空类似于队列的订阅，并为每个订阅服务器提供更多的存储容量。 

许多复杂的解决方案还要求跨命名空间边界复制消息，以便实现这些模式和其他模式。 消息可能必须在与多个不同的应用程序租户关联的命名空间之间，或跨多个不同的 Azure 区域。 

解决方案将在不同区域中维护多个服务总线命名空间，并在队列和主题之间复制消息，并/或将消息与源和目标（如 [Azure 事件中心](../event-hubs/event-hubs-about.md)、 [azure IoT 中心](../iot-fundamentals/iot-introduction.md)或 [Apache Kafka](https://kafka.apache.org)）交换。 

本文重点介绍了这些方案。 

## <a name="federation-patterns"></a>联合模式

出于一些原因，可能需要在服务总线实体（例如队列或主题）之间，或者在服务总线与其他源和目标之间移动消息。 

与 [事件中心](../service-bus-messaging/service-bus-federation-overview.md)的一组相似模式相比，与队列类似的实体的联合更为复杂，因为消息队列承诺其使用者在任何单个消息上独占所有权，因此应在消息传递中保留到达顺序，并让 broker 协调 [竞争使用者](/azure/architecture/patterns/competing-consumers)之间的消息分布。 

存在一些切实可行的障碍，其中包括 [CAP 定理](https://en.wikipedia.org/wiki/CAP_theorem)的约束，这使得在多个区域中同时提供队列的统一视图非常困难，并允许突破分散的 [竞争使用者](/azure/architecture/patterns/competing-consumers) 对消息进行独占访问。 此类异地分布式队列需要完全一致的复制消息，还需要每条消息的传递状态，然后才能将消息提供给使用者。 假设的突破分布式队列的完全一致性的目标是与所有 Azure 服务总线客户在考虑联合方案时具有的关键目标直接冲突：其解决方案的最大可用性和可靠性。 

此处所示的模式将重点放在可用性和可靠性上，同时还能最好地避免信息丢失和消息的重复处理。 

### <a name="resiliency-against-regional-availability-events"></a>针对区域可用性事件的复原能力 

尽管最大可用性和可靠性是服务总线的顶级操作优先级，但仍有许多方法可以防止制造者或使用者通过网络或名称解析问题与分配的 "主" 服务总线通信，或者服务总线实体可能会暂时暂时停止响应或返回错误。 指定的消息处理程序也可能会变得不可用。

这种情况并不是 "灾难性" 的，因此你需要完全放弃区域部署，就像在灾难恢复情况下一样，但是某些应用程序的业务方案可能已受到过去不超过几分钟甚至几秒钟的可用性事件的影响。 Azure 服务总线通常用于混合云环境以及驻留在网络边缘的客户端，例如零售商店、餐馆、银行分支、生产站点、物流设施和机场。 网络路由或拥塞问题可能会影响任何一个站点访问其分配的服务总线终结点的能力，而另一个区域中的辅助终结点可能可访问。 同时，处理从这些站点到达的消息的系统可能仍无法访问主服务总线终结点和辅助服务总线终结点。 

有很多有关此类混合云和边缘应用程序的示例，这些应用程序的业务容差和服务总线实体的暂时性可用性问题的影响较低。 这包括在零售地点处理付款、在机场门上参加以及在餐厅中的移动电话订单，所有这些都可以即时完成，只要可靠的通信路径不可用，就可以完成停滞。

在此类别中，我们讨论了三种不同的分布式模式： "全部活动" 复制、"主动-被动" 和 "溢出" 复制。 

#### <a name="all-active-replication"></a>All-Active 复制

使用 "全部活动" 复制模式，可以在多个命名空间 (和区域) 中提供同一逻辑主题 (或队列) 的活动副本，而不管它们在何处排队都可用。 模式通常会保留消息相对于任何发布者的顺序。 

![所有活动模式](media/service-bus-federation-overview/mirrored-topics.jpg)

如图所示，模式通常倾向服务总线主题。 应参与复制方案的每个命名空间的一个主题。 其中每个主题都有一个 "复制订阅"，适用于应该将消息复制到其中的任何其他主题。 在上图中，我们只是提供了一对主题，因此是针对各自其他主题的单个复制订阅。 在包含三个命名空间 *{n1，n2，n3}* 的方案中，命名空间 *n1* 中的主题将具有两个复制订阅，一个用于 *n2* 中的相应主题，另一个用于 *n3* 中的相应主题。 

每个复制订阅都有一条规则，该规则将 SQL 筛选器表达式 (`replicated <> 1`) 和 () 的 sql 操作组合在一起 `set replicated = 1` 。 规则的筛选器可确保只有自定义属性未设置或没有值的消息才 `replication` `1` 会成为此订阅的符合条件，并且操作随后会将该确切属性设置为 `1` 每个选定消息的值。 这样做的结果是，在将消息复制到相应主题时，它不再符合复制的条件，因此，我们避免了在副本之间弹跳的消息。

可以使用如下所示的 Azure CLI 轻松地将具有相应规则的订阅添加到任何主题。

```azurecli

az servicebus topic subscription rule create --resource-group myresourcegroup \
   --namespace mynamespace --topic-name mytopic 
   --subscription-name replication --name replication \
   --action-sql-expression "set replication = 1" \
   --filter-sql-expression "replication IS NULL"
```

若要对队列进行建模，每个主题仅限一个常规订阅 (除了所有使用者共享) 的复制订阅。

> "所有活动的复制" 模型将发送到任何主题的每条消息的副本放入每个主题中。 这意味着每个区域中的应用程序代码将会看到并处理所有消息。
> 此模式适用于以下情况：数据被共享到多个区域或通常需要冗余处理。 如果只需处理每条消息一次，就像使用常规队列一样，则需要考虑以下两种模式之一。  

#### <a name="active-passive-replication"></a>Active-Passive 复制

"主动-被动" 复制模式是之前模式的变体，其中只有一个 ("主要" ) 的主题在应用程序用于发送和接收消息，并在主主题可能变为不可用或无法访问时，将消息复制到次要主题。 

![主动被动模式](media/service-bus-federation-overview/mirrored-topics-passive.jpg)

此模式与以前的模式之间的主要区别在于，从主要主题到次要主题的复制是单向的。 辅助主题绝不会成为主要主题，但当主要主题暂时不可用时，它是一个备份选项。 

使用此模式的优点是，它会尝试帮助最大程度地减少重复处理。 在复制过程中，将 `TimeToLive` 消息属性设置为复制的消息的持续时间，这些消息反映了主副本发生故障时的预期时间将导致故障转移。 例如，如果用例方案需要在最多1分钟的时间内从主启动中检索消息时，将使用者切换到辅助副本，但在问题出现之前，辅助数据库应将所有可用的消息都设置为不能访问的最小消息数。 如果在 `TimeToLive` "规则操作") 中将复制 (设置为两次，则 `set sys.TimeToLive = '0:2:0'` 辅助数据库将只保留2分钟的消息，并放弃旧的消息。 这意味着当接收方切换到辅助副本时，它可以快速读取和丢弃比它处理的最后一条消息更早的消息，然后从它尚未查看的第一条消息处理。 实际保持期将取决于特定的用例，并快速地在应用程序中切换到辅助副本。 此 `TimeToLive` 设置的范围是从几秒钟到几秒钟。 

当应用程序使用辅助副本时，它还可以直接发布到次要主题，该主题将作为任何常规主题。 在切换到辅助副本后，使用者将看到混合的复制消息和直接发布到辅助副本的消息。 因此，应用程序应该首先将发布切换回主数据库，并在将使用者切换回辅助使用者之前，仍允许排出本地发布的消息。 由于在主副本再次可用时，复制会自动恢复，因此，使用者还会在该时间段内收到发布到主副本的新消息，尽管延迟略高。 

> 此模式适用于应仅处理一次消息的情况。 应用程序需要密切合作，以跟踪它从主数据库中处理的消息，因为它会在辅助数据库的故障转移时段内发现重复项，并在切换回来时再次查找重复项。
> 重复数据删除条件最好是应用程序提供的 `MessageId` 。 `EnqueuedTimeUtc`该值还适用于水印指示器，但应用程序需要允许在主和辅助数据库之间 (几秒钟) 与任何分布式系统相同的时钟偏移量。


#### <a name="spillover-replication"></a>溢出复制

"溢出" 复制模式允许在多个区域中主动/主动地使用多个服务总线实体，以处理服务总线处于正常状态的情况，但 *使用者* 会大量占用挂起消息或完全不可用。 原因在于，支持使用者进程的数据库可能会变慢或不可用。 此模式适用于纯队列和主题订阅。  

![溢出复制](media/service-bus-federation-overview/spillover.jpg)  

如图所示，溢出复制模式将消息从队列或订阅的关联 [死信队列](service-bus-dead-letter-queues.md) 复制到另一个命名空间中的配对队列或主题。 

如果没有失败的情况，则会并行使用这两个命名空间，每个命名空间都接收一部分整体消息流量及其关联的使用者处理该子集。 一旦某个使用者启动了高故障率或完全停止后，相应的消息将在死信队列中最后结束，因为它超过了传递计数或过期。 然后，复制任务会选取它们并将其重新排队到配对队列中，然后将它们显示到 presumable 的正常使用者。 

如果必须在某个截止时间内进行处理，则 `TimeToLive` 应将队列和/或消息的设置为，以使处理仍可由溢出辅助数据库发生，例如， `TimeToLive` 可能会设置为允许时间的一半。

与 " [全部活动" 模式](#all-active-replication)一样，应用程序可以将指示器添加到消息中，指示消息是否已被复制一次，以便它们不会在队列对之间来回传递，而是发送到充当复合模式的死信队列的辅助队列。

> 此模式适用于以下方案：最重要的问题是应对消费者或使用者所依赖的资源中的可用性问题进行防御，还可以在其中一个配对队列上重新分发流量高峰。 如果使用者从两个队列读取，但过期施加的复制延迟 `TimeToLive` 可能导致该时间窗口内的消息在不可用的命名空间中一端，则对其中一个命名空间的保护也会变得不可用。 

### <a name="latency-optimization"></a>延迟优化 

主题用于将信息分发给多个使用者。 在某些情况下，尤其是具有宽地理分布的使用者，将消息从主题复制到离使用者更近的辅助命名空间中的主题可能是有益的。

![延迟优化](media/service-bus-federation-overview/latency-optimization.jpg)  

例如，在区域、大陆中心之间共享数据时，只需在中心之间传输信息一次，并让使用者从这些中心获取数据副本会更有效。 

复制传输可以分批完成，而使用者通常会逐个获取和结算消息。 如果基本网络延迟为100毫秒，如北美和欧洲，则每条消息将在两次往返到远程实体时花费200毫秒，以获取和结算消息（与同一区域中的实体相比）。 

### <a name="validation-reduction-and-enrichment"></a>验证、减少和扩充

消息可以由自己的解决方案外部的客户端提交到服务总线队列或主题。

![验证，缩减，扩充](media/service-bus-federation-overview/validation.jpg)  

此类消息可能需要检查给定架构的符合性，以及要删除或死信的非相容消息。 某些消息可能需要通过省略数据来降低复杂性，某些可能需要通过基于引用数据查找添加数据来进行充实。 可以通过复制任务中的自定义功能来执行这些操作。 

### <a name="stream-to-queue-replication"></a>流式传输到队列复制

Azure 事件中心是用于处理大量传入事件的理想解决方案。 不过，事件中心和类似的引擎（如 Apache Kafka）都不提供服务管理的 [竞争使用者](/azure/architecture/patterns/competing-consumers) 模型，在该模型中，多个使用者可以同时处理来自同一源的消息，而不会造成重复处理的风险，最后在处理完这些消息后将其 

![集成](media/service-bus-federation-overview/hub-to-queue.jpg)

用于队列复制的流将单个事件中心分区的内容或完整事件中心的内容传输到服务总线队列，从该队列中，可以安全地处理消息，并使用竞争使用者处理消息。 此复制还支持对这些消息使用所有其他服务总线功能，包括使用主题和基于会话的 demultiplexing 进行路由。 

### <a name="consolidation-and-normalization"></a>合并和规范化 

全局解决方案通常由在很大程度上独立的区域痕迹组成，其中包括拥有自己的处理能力，但 supra 的区域和全局透视图将需要数据集成，因此，对同一消息数据进行集中合并，这些数据是在本地透视图的各个区域的不同区域中进行评估的。 

![合并](media/service-bus-federation-overview/merge.jpg)

规范化是合并方案的一种风格，这种情况下，两个或更多传入的消息序列携带相同种类的信息，但具有不同的结构或不同的编码，并且消息必须转码或转换才能使用。 

规范化还可能包括加密工作，如解密端对端加密的负载，并使用不同的密钥和算法对下游使用者受众重新加密。 

### <a name="splitting-and-routing"></a>拆分和路由

服务总线主题及其订阅规则通常用于筛选特定受众的消息流，并随后从订阅获取筛选集。 

![拆分](media/service-bus-federation-overview/split.jpg)

在全局系统中，如果这些消息的受众是全局分布的，或者属于不同的应用程序，则可以使用复制将来自此类订阅的消息从使用该订阅的其他命名空间中的队列或主题传输到不同的命名空间中。

### <a name="replication-applications-in-azure-functions"></a>Azure Functions 中的复制应用程序

实现上述模式需要为要配置和运行的复制任务提供可缩放且可靠的执行环境。 在 Azure 上， [Azure Functions](../azure-functions/functions-overview.md)最适合无状态任务的运行时环境。 

Azure Functions 可以在 [Azure 托管标识](../active-directory/managed-identities-azure-resources/overview.md) 下运行，以便复制任务可以与源服务和目标服务的基于角色的访问控制规则集成，而无需通过复制路径管理机密。 对于需要显式凭据的复制源和目标，Azure Functions 可以在 [Azure Key Vault](../key-vault/general/overview.md)中严格访问控制的存储中包含这些凭据的配置值。

Azure Functions 还允许复制任务直接与 Azure 虚拟网络和 [服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md) 集成，以实现所有 azure 消息传递服务，并与 [Azure Monitor](../azure-monitor/overview.md)轻松集成。

最重要的是，Azure Functions 为 [Azure 事件中心](../azure-functions/functions-bindings-service-bus.md)、 [azure IoT 中心](../azure-functions/functions-bindings-event-iot.md)、 [Azure 服务总线](../azure-functions/functions-bindings-service-bus.md)、 [azure 事件网格](../azure-functions/functions-bindings-event-grid.md)、 [azure 队列存储](/azure/azure-functions/functions-bindings-storage-queue)、 [RabbitMQ](https://github.com/azure/azure-functions-rabbitmq-extension)的自定义扩展和 [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)预构建、可缩放的触发器和输出绑定。 大多数触发器会根据所记录的指标，根据所记录的指标，根据所记录的指标，增加和减少并发执行实例的数量，从而动态调整吞吐量需求 

使用 Azure Functions 消耗计划，预先生成的触发器甚至可以向下扩展到零，而不会有可用于复制的消息，这意味着，将配置准备好进行扩展，不会产生任何费用。 使用消耗计划的关键不足之处在于，与此状态的复制任务 "正在唤醒" 的延迟明显高于使用基础结构保持运行的主机计划。  

与所有此类功能相比，用于消息传递和事件的最常见的复制引擎（如 Apache Kafka 的 [MirrorMaker](http://kafka.apache.org/documentation/#basic_ops_mirror_maker) ）需要你提供主机环境并自行缩放复制引擎。 这包括配置和集成安全和网络功能，并推动监视数据的流动，然后你仍没有机会将自定义复制任务注入到流中。 

## <a name="next-steps"></a>后续步骤

本文介绍了一系列联合模式，并说明了在 Azure 中 Azure Functions 作为事件和消息复制运行时的角色。 

接下来，你可能想要了解如何使用 Azure Functions 设置复制器应用程序，以及如何在事件中心与其他各种事件和消息传递系统之间复制事件流：

- [Azure Functions 中的复制应用程序](service-bus-federation-replicator-functions.md)
- [在服务总线实体之间复制事件](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopy)
- [将事件路由到 Azure 事件中心](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopyToEventHub)
- [从 Azure 事件中心获取事件](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus)

[1]: ./media/service-bus-auto-forwarding/IC628632.gif 
