---
title: 多站点和多区域联合 - Azure 事件中心 |Microsoft Docs
description: 本文概述了 Azure 事件中心的多站点和多区域联合。
ms.topic: article
ms.date: 09/28/2021
ms.author: spelluru
ms.openlocfilehash: 7311854b95250f94aa51bdc954b53743fbfde3a1
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129217857"
---
# <a name="multi-site-and-multi-region-federation"></a>多站点和多区域联合

许多复杂的解决方案需要相同的事件流供多个位置使用，或者需要在多个位置收集事件流，然后合并到特定位置以供使用。 此外，也需要在单个区域和解决方案中扩充或减少事件流，或者转换事件格式。

实际上，这表示你的解决方案将维护多个事件中心（这些事件中心通常位于不同的区域和事件中心命名空间中），然后在中心之间复制事件。 你还可以将事件与源和目标（如 [Azure 服务总线](../service-bus-messaging/service-bus-messaging-overview.md)、[Azure IoT 中心](../iot-fundamentals/iot-introduction.md)或 [Apache Kafka](https://kafka.apache.org)）进行交换。 

在不同区域维护多个活动的事件中心时，客户端可以在其内容正在合并期间，选择事件中心并在它们之间切换，这使整个系统能够更灵活地应对区域可用性问题。

本章内容介绍了联合模式，以及如何使用无服务器 [Azure 流分析](../stream-analytics/stream-analytics-introduction.md) 或 [Azure Functions][1] 运行时来实现这些模式，并且可以选择在事件流路径中使用自己的转换或扩充代码。 

## <a name="federation-patterns"></a>联合模式

你可能会因为很多潜在原因而需要在不同的事件中心或其他源和目标之间移动事件，我们在本节中将列举了最重要的模式，同时链接到对各个模式更详细的指导。 

- [针对区域可用性事件的复原能力](#resiliency-against-regional-availability-events)
- [延迟优化](#latency-optimization)
- [验证、减少和扩充](#validation-reduction-and-enrichment)
- [与分析服务集成](#integration-with-analytics-services)
- [事件流的合并和规范化](#consolidation-and-normalization-of-event-streams)
- [拆分和路由事件流](#splitting-and-routing-of-event-streams)
- [日志投影](#log-projections)
  
### <a name="resiliency-against-regional-availability-events"></a>针对区域可用性事件的复原能力 

![区域可用性](media/event-hubs-federation-overview/regional-availability.jpg)

虽然最大限度的可用性和可靠性是事件中心的最高操作优先级，但在某些情况下，创建器或使用者可能由于网络或名称解析问题而无法与其分配的“主要”事件中心通信，或者事件中心确实可能暂时不响应或返回错误。 

这种情况并不是“灾难性的”，你不会像在[灾难恢复](event-hubs-geo-dr.md)情况下那样想要完全放弃区域部署，但是一些应用程序的业务场景可能已经受到持续时间不超过几分钟甚至几秒钟的可用性事件的影响。 

可以通过两种基本模式来处理此类方案：

- [复制][4]模式就是将主事件中心的内容复制到辅助事件中心，其中主事件中心通常供应用程序来生成和使用事件，而辅助事件中心则作为主事件中心不可用时的备选方案。 由于复制是从主中心到辅助中心单向复制的，创建器和使用者从不可用的主中心切换到辅助中心，将会导致旧的主中心不再接收新的事件，因此它将不再是最新的。
  因此，纯复制仅适用于单向故障转移方案。 一旦进行故障转移后，就会放弃旧的主事件中心，并且需要在另一个目标区域中创建新的辅助事件中心。
- [合并][5]模式将连续合并两个或更多事件中心的内容，扩展了复制模式。 最初生成到方案中的其中一个事件中心的每个事件都将复制到其他的事件中心。 在复制事件时，将为事件加上批注，以便复制目标的复制进程随后略过这些事件。 使用合并模式的结果是两个或更多事件中心，将以最终一致的方式包含相同的事件集。 
  
无论是哪种情况，事件中心的内容都将不相同。 来自任何一个创建器并按相同分区键分组的事件将按最初提交的相对顺序显示，但事件的绝对顺序可能会有所不同。 对于源事件中心和目标事件中心的分区计数不同的情况，尤其如此，这对于此处所述的几种扩展模式而言是理想的。 [拆分器或路由器](#splitting-and-routing-of-event-streams)可能会获得具有数百个分区和漏斗图的更大事件中心的一小部分，并将它放到较小的事件中心，其中只包含少量分区，更适合用于处理资源有限的子集。 相反，[合并](#consolidation-and-normalization-of-event-streams)可能会将多个较小事件中心的数据汇集成一个更大的单一事件中心，该中心具有更多分区，以应对合并的吞吐量和处理需求。

将事件保持在一起的标准是分区键，而不是原始分区 ID。 [复制][4]模式说明中讨论了有关相对顺序的其他注意事项，以及如何执行从一个事件中心到下一个事件中心的故障转移，而不依赖相同的流偏移范围。


指南： 
- [复制模式][4]
- [合并模式][5]

### <a name="latency-optimization"></a>延迟优化 

![延迟优化](media/event-hubs-federation-overview/latency-optimization.jpg)  

事件流由创建器写入一次，但事件使用者可以读取任意次数。 对于某个区域中的事件流由多个使用者共享，并且需要在位于不同区域的分析处理过程中反复访问的情况，或者在可能耗尽并行使用者的整个需求中，将事件流的副本放在分析处理器附近以减少往返延迟，这样做可能是比较好的做法。 

在跨区域远程使用事件时，应优先使用复制，这特别适合区域非常远的情况，例如，欧洲和澳大利亚在地球上的地理位置几乎是跨轴相对的，而且网络延迟在任何往返行程中很容易就超过 250 毫秒。
你无法加快光的速度，但是可以减少与数据进行交互的高延迟往返次数。

指南： 
- [复制模式][4]

### <a name="validation-reduction-and-enrichment"></a>验证、减少和扩充

![验证、减少、扩充](media/event-hubs-federation-overview/validation-enrichment.jpg)  

事件流可能由你自己的解决方案外部的客户端提交到事件中心。 此类事件流可能要求检查外部提交的事件是否符合指定的架构，以及要删除不符合规范的事件。 

许多“物联网”方案中使用的是按流量计费方式而使客户端的带宽极度受限，或者事件最初是通过数据包大小受限的非 IP 网络发送，在这些情况中，则可能必须使用引用数据来扩充事件，以便添加更多上下文，使其可供下游事件处理器使用。

在其他情况下，尤其是在合并流时，可能需要通过省略一些细节来降低事件数据的复杂性或大小。

在复制、合并或合并流时，可能会需要进行以上一些操作。 

指南： 
- [编辑器模式][6]

### <a name="integration-with-analytics-services"></a>与分析服务集成

![与分析服务集成](media/event-hubs-federation-overview/integration.jpg)

Azure 的多个云原生分析服务（如 Azure 流分析或 Azure Synapse）最适用于从 Azure 事件中心提供的流式处理或预先批处理数据，Azure 事件中心还可以与多个开源分析包（如 Apache Samza、Apache Flink、Apache Spark 和 Apache Storm）集成。 

如果你的解决方案主要使用服务总线或事件网格，则可通过此类分析系统轻松访问这些事件，并在将它们汇集到事件中心时使用事件中心捕获进行存档。 事件网格可以通过其[事件中心集成](../event-grid/handler-event-hubs.md)，以原生方式来实现此操作，对于服务总线，请遵循[服务总线复制指南](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopyToEventHub)。

Azure 流分析[直接与事件中心集成](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-event-hubs)。

指南： 
- [复制模式][4]

### <a name="consolidation-and-normalization-of-event-streams"></a>事件流的合并和规范化

![事件流的合并和规范化](media/event-hubs-federation-overview/consolidation.jpg)

全球解决方案通常由独立性较大的区域占用（包括具备独立的处理能力）组成，但是从区域和全球的角度来看需要集成，因此需要对相同的事件流进行中央合并，并在各自的区域占用中从本地视角进行评估。 

规范化是合并方案的一种形式，其中两个或多个传入的事件流携带相同类型的事件，但具有不同的结构或不同的编码，并且必须先对事件进行转码或转换，然后才能使用它们。 

规范化还可以包括加密工作，例如解密端到端的加密有效负载，并使用不同的密钥和算法为下游使用者受众进行重新加密。 

指南： 
- [合并模式][5]
- [编辑器模式][6]

### <a name="splitting-and-routing-of-event-streams"></a>拆分和路由事件流

![拆分和路由事件流](media/event-hubs-federation-overview/splitting.jpg)

Azure 事件中心偶尔用于“发布-订阅”式方案，其中引入事件的传入量远远超过了 Azure 服务总线或 Azure 事件网格的容量，这两者都具有原生发布-订阅筛选和分发功能，并且是此模式的首选。 

虽然真正的“发布-订阅”功能使订阅者能够选择事件，但拆分模式则是让创建器通过预定义的分布模型将事件映射到分区，然后指定的使用者从“他们”的分区以专有方式提取。 事件中心缓冲总体流量时，特定分区的内容（表示原始吞吐量卷的一小部分）可以复制到队列中，以供可靠的、事务性的、竞争性的使用者使用。

许多方案中，事件中心主要用于在某个区域内的应用程序中移动事件，其中在某些情况下，只需从单个分区中选择事件，也可以在其他位置使用。 此方案类似于拆分方案，但可能会使用可扩展的路由器，该路由器将考虑到达事件中心的所有消息，而只挑选少部分消息进行后路由，并可能通过事件元数据或内容区分路由目标。 

指南：
- [路由模式][7]

### <a name="log-projections"></a>日志投影 

![日志投影](media/event-hubs-federation-overview/log-projection.jpg)

在某些方案中，你会想要访问为事件的任何子流发送的最新值，并经常通过分区键来区分。 在 Apache Kafka 中，这通常通过启用主题的“日志压缩”实现，除标记有任何唯一键的最新事件之外，其他所有事件都将丢弃。 日志压缩方法有三个复合缺点： 

- 压缩需要连续重组日志，这样的操作对于一个经过优化后仅限追加工作负荷的中转站来说过于昂贵。 
- 压缩具有破坏性，不允许对同一流进行压缩和非压缩。
- 压缩的流仍具有顺序访问模型，这表示在最坏的情况下，在日志中找到所需的值需要读取整个日志，但是，这种情况通常会得到优化，实现这里提出的精确模式：将日志内容投射到数据库或缓存中。 

最终，压缩的日志是键-值存储，因此它是这种存储最不理想的实现方案。 对于查找和查询来说，更有效的方式是创建一个永久的日志投影到一个适当的键-值存储或其他数据库上并使用它。 

由于事件是不可变的，并且顺序始终保留在日志中，因此日志到键-值存储中的任何投影对于同一范围的事件始终是相同的，这意味着，一直保持更新的投影将始终具有权威性，并且永远不会在投影生成后从日志内容重新生成。 

指南：
- [日志投影][8]

## <a name="replication-application-technologies"></a>复制应用程序技术

实现上述模式需要为要配置和运行的复制任务提供可缩放且可靠的执行环境。 在 Azure 上，最适合此类任务的运行时环境是 [Azure 流分析](../stream-analytics/stream-analytics-introduction.md)（用于有状态流复制任务）和 [Azure Functions](../azure-functions/functions-overview.md)（用于无状态复制任务）。

### <a name="stateful-replication-applications-in-azure-stream-analytics"></a>Azure 流分析中的有状态复制应用程序

对于需要考虑事件间关系、创建复合事件、扩充事件或减少事件、创建数据聚合和转换事件负载的有状态复制应用程序，[Azure 流分析](../stream-analytics/stream-analytics-introduction.md)是最佳的选择。

在 Azure 流分析中，你将[创建作业](../stream-analytics/stream-analytics-quick-create-portal.md)用于集成[输入](../stream-analytics/stream-analytics-add-inputs.md)和[输出](../stream-analytics/stream-analytics-define-outputs.md)，并通过[查询](/stream-analytics-query/stream-analytics-query-language-reference)来集成输入中的数据，从而在输出上提供结果。

基于 [SQL 查询语言](/stream-analytics-query/stream-analytics-query-language-reference)的查询可用于对某个时段的流数据进行筛选、排序、聚合和联接。 此外，还可以使用 [JavaScript](../stream-analytics/stream-analytics-javascript-user-defined-functions.md) 和 [C# 用户定义函数 (UDF)](../stream-analytics/stream-analytics-edge-csharp-udf-methods.md) 扩展此 SQL 语言。 通过简单的语言构造和/或配置执行聚合操作时，可以轻松地调整事件排序选项和时段的持续时间。

每个作业都有一个或多个转换后数据的输出，你可以针对所要分析的信息控制过程。 例如，你能够：

- 将数据发送到 Azure Functions、服务总线主题或队列等服务，以触发下游的通信或自定义工作流。
- 将数据发送到 Power BI 仪表板进行实时仪表板操作。
- 将数据存储到其他 Azure 存储服务（例如 Azure Data Lake、Azure Synapse Analytics 等）中，基于历史数据庞大的索引池执行批处理分析或训练机器学习模型。
- 将投影（也称为“具体化视图”）存储到数据库（[SQL Database](../stream-analytics/sql-database-output.md)、[Cosmos DB](../stream-analytics/azure-cosmos-db-output.md)）中。

### <a name="stateless-replication-applications-in-azure-functions"></a>Azure Functions 中的无状态复制应用程序

对于无状态复制任务，如果你想转发事件而不考虑它们的有效负载，或者单独处理它们而不必考虑事件的关系（除了它们的相对顺序外），那么你可以使用具有高灵活性的 Azure Functions。

Azure Functions 为 [Azure 事件中心](../azure-functions/functions-bindings-event-hubs.md)、[Azure IoT 中心](../azure-functions/functions-bindings-event-iot.md)、[Azure 服务总线](../azure-functions/functions-bindings-service-bus.md)、[Azure 事件网格](../azure-functions/functions-bindings-event-grid.md)和 [Azure 队列存储](../azure-functions/functions-bindings-storage-queue.md)提供了预生成、可扩展的触发器和输出绑定，为 [RabbitMQ](https://github.com/azure/azure-functions-rabbitmq-extension) 和[Apache Kafka](https://github.com/azure/azure-functions-kafka-extension) 提供了自定义扩展。 大多数触发器将根据记录的指标通过纵向扩展和缩减并发执行实例的数量来动态适应吞吐量需求。 

对于生成日志投影，Azure Functions 支持 [Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2-output.md) 和 [Azure 表存储](../azure-functions/functions-bindings-storage-table-output.md)的输出绑定。

Azure Functions 可以在 [Azure 托管标识](../active-directory/managed-identities-azure-resources/overview.md) 下运行，因此，它可以在 [Azure 密钥保管库](../key-vault/general/overview.md)内访问控制严格的存储中保存凭据的配置值。

Azure Functions 还允许复制任务直接与 Azure 虚拟网络和所有 Azure 消息传送服务的[服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)集成，且 Azure Functions 已与 [Azure Monitor](../azure-monitor/overview.md) 集成。

通过 Azure Functions 使用计划，预生成的触发器甚至可以在没有消息可供复制的情况下缩减为零，这意味着你不需要为使配置准备好回缩负担任何成本；使用计划的关键不足在于，从这种状态下“唤醒”复制任务的延迟明显高于保持基础设施运行的托管计划。  

与所有这些不同的是，用于消息传递和事件处理的最常见的复制引擎（例如 Apache Kafka 的 [MirrorMaker](http://kafka.apache.org/documentation/#basic_ops_mirror_maker)）需要你提供一个主机环境并自行缩放复制引擎。 这包括配置和集成安全性和网络功能以及辅助监视数据流，你仍然没有机会将自定义复制任务注入到流中。 

### <a name="choosing-between-azure-functions-and-azure-stream-analytics"></a>在 Azure Functions 和 Azure 流分析之间进行选择

每当你需要在复制事件的同时处理事件的有效负载时，Azure 流分析 (ASA) 都是最佳选择。 ASA 可以逐个复制事件，也可以创建聚合，以便在转接事件流之前对其信息进行紧缩。 它可以[依赖 Azure Blob 存储或Azure SQL 数据库中保存的补充引用数据](../stream-analytics/stream-analytics-use-reference-data.md)，而不必将此类数据导入流中。

借助 ASA，你可以轻松地在超大规模数据库中创建流的具体化视图，体现流的持续性。 相对于 Apache Kafka 笨拙的“日志压缩”模型和 Kafka Streams 不稳定的客户端表投影来说，这是一种更为优越的方法。 

ASA 可以很容易地处理具有以 [CSV、JSON 和 Apache Avro 格式](../stream-analytics/stream-analytics-parsing-json.md)编码的有效负载的事件，并且你可以插入任何其他格式的[自定义反序列器](../stream-analytics/custom-deserializer.md)。

Azure Functions 非常适合以下类型的复制任务：想要“按原样”复制事件流而不触及有效负载的复制任务；需要操作路由器、执行加密工作、更改有效负载编码的复制任务；需要完全控制数据流内容的复制任务。

## <a name="next-steps"></a>后续步骤

本文介绍了一系列联合模式，并解释了 Azure Functions 在 Azure 中作为事件和消息复制运行时的作用。

接下来，需要了解如何使用 Azure 流分析或 Azure Functions 设置复制器应用程序，以及如何在事件中心和各种其他事件和消息传递系统之间复制事件流：

- [事件复制任务模式][10]
- [使用 Azure 流分析处理数据][9]
- [Azure Functions 中的事件复制器应用程序][1]
- [在事件中心之间复制事件][2]
- [将事件复制到 Azure 服务总线][3]
- [将 Apache Kafka MirrorMaker 与事件中心配合使用][11] 

[1]: event-hubs-federation-replicator-functions.md
[2]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopy
[3]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus
[4]: event-hubs-federation-patterns.md#replication
[5]: event-hubs-federation-patterns.md#merge
[6]: event-hubs-federation-patterns.md#editor
[7]: event-hubs-federation-patterns.md#routing
[8]: event-hubs-federation-patterns.md#log-projection
[9]: process-data-azure-stream-analytics.md
[10]: event-hubs-federation-patterns.md#replication
[11]: event-hubs-kafka-mirror-maker-tutorial.md