---
title: 多站点和多区域联合-Azure 事件中心 |Microsoft Docs
description: 本文概述了 Azure 事件中心的多站点和多区域联合。
ms.topic: article
ms.date: 12/12/2020
ms.author: spelluru
ms.openlocfilehash: 12ef895c8b16fe18ed02ebf01d17624ac71c2f3e
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "97861458"
---
# <a name="multi-site-and-multi-region-federation"></a>多站点和多区域联合

许多复杂的解决方案需要在多个位置提供相同的事件流，或者需要在多个位置收集事件流，然后将其合并到特定位置以供使用。 还需要在单个区域和解决方案中丰富或减少事件流或进行事件格式转换。

实际上，这意味着解决方案将维护多个事件中心（通常在不同的区域和事件中心命名空间中），然后在它们之间复制事件。 你还可以将事件与源和目标（如 [Azure 服务总线](../service-bus-messaging/service-bus-messaging-overview.md)、 [azure IoT 中心](../iot-fundamentals/iot-introduction.md)或 [Apache Kafka](https://kafka.apache.org)）交换。 

在不同区域中维护多个活动的事件中心还允许客户端在其内容正在合并时选择并在它们之间切换，这使整个系统能够更灵活地应对区域可用性问题。

此 "联合" 一章介绍了联合模式，以及如何使用无服务器 [Azure 流分析](../stream-analytics/stream-analytics-introduction.md) 或 [Azure Functions][1] 运行时实现这些模式，并且可以选择在事件流路径中拥有自己的扩充代码。 

## <a name="federation-patterns"></a>联合模式

对于可能需要在不同事件中心或其他源和目标之间移动事件的原因，有很多可能的动机，我们将枚举此部分中最重要的模式，同时链接到相应模式的更详细指南。 

- [针对区域可用性事件的复原能力](#resiliency-against-regional-availability-events)
- [延迟优化](#latency-optimization)
- [验证、减少和扩充](#validation-reduction-and-enrichment)
- [与 analytics services 集成](#integration-with-analytics-services)
- [事件流的合并和规范化](#consolidation-and-normalization-of-event-streams)
- [拆分和路由事件流](#splitting-and-routing-of-event-streams)
- [日志预测](#log-projections)
  
### <a name="resiliency-against-regional-availability-events"></a>针对区域可用性事件的复原能力 

![区域可用性](media/event-hubs-federation-overview/regional-availability.jpg)

尽管最大可用性和可靠性是事件中心的顶级操作优先级，但仍有许多方法可让制造者或使用者与分配的 "主" 事件中心进行通信，因为存在网络或名称解析问题，或者事件中心确实暂时停止响应或返回错误。 

这种情况并不是 "严重"，因此你需要完全放弃区域部署，就像在 [灾难恢复](event-hubs-geo-dr.md) 情况下一样，但是，一些应用程序的业务方案可能已受到最近不超过几分钟甚至几秒钟的可用性事件的影响。 

可以通过两种基本模式来处理此类方案：

- [复制][4]模式与将主事件中心的内容复制到辅助事件中心有关，这种情况下，应用程序通常会使用主事件中心来生成和使用事件，而辅助副本可作为回退选项，以防主事件中心变为不可用。 由于复制是单向的，从主站点到辅助副本，从不可用的主站点到辅助副本的创建者和使用者将会导致旧的主节点不再接收新的事件，因此它不再是最新的。
  因此，纯复制仅适用于单向故障转移方案。 执行故障转移后，旧的主数据库会被放弃，并需要在不同的目标区域中创建新的辅助事件中心。
- [合并][5]模式通过执行连续合并两个或更多事件中心的内容来扩展复制模式。 最初生成到方案中包含的事件中心之一的每个事件都将复制到其他事件中心。 在复制事件时，将对其进行批注，以便复制目标的复制进程随后忽略这些事件。 使用合并模式的结果是两个或更多事件中心，它们将以最终一致的方式包含相同的事件集。 
  
在任一情况下，事件中心的内容将不相同。 来自任何一个制造者并按相同分区键分组的事件将按最初提交的相对顺序显示，但事件的绝对顺序可能会有所不同。 对于源事件中心和目标事件中心的分区计数不同的情况，尤其如此，这对于此处所述的几种扩展模式是理想的。 [拆分器或路由器](#splitting-and-routing-of-event-streams)可能会将具有数百个分区和漏斗图的更大事件中心切片到一个较小的事件中心，其中包含少量分区，更适合用于处理资源有限的子集。 相反， [合并](#consolidation-and-normalization-of-event-streams) 可能会将多个较小的事件中心的数据分解成一个更大的事件中心，该中心具有更多分区，以应对合并的吞吐量和处理需求。

将事件保持在一起的标准是分区键，而不是原始分区 ID。 [复制][4]模式说明中讨论了有关相对顺序的其他注意事项，以及如何执行从一个事件中心到下一个事件中心的故障转移，而无需依赖相同的流偏移范围。


指南： 
- [复制模式][4]
- [合并模式][5]

### <a name="latency-optimization"></a>延迟优化 

![延迟优化](media/event-hubs-federation-overview/latency-optimization.jpg)  

事件流由发生器写入一次，但可以由事件使用者多次读取。 对于某个区域中的事件流由多个使用者共享的情况，需要在位于不同区域的分析处理过程中反复访问这些方案，或在枯竭并发使用者的整个需求中放置事件流的副本，这可能会很有利，目的是将事件流的副本放在分析处理器附近，以减少往返延迟。 

更好的示例是，在跨区域远程使用事件时，应优先使用复制，这种情况特别适用于其中的区域非常远的情况，例如，欧洲和澳大利亚几乎 antipodes，而地理位置和网络延迟可能会在任何往返行程中轻松超过 250 ms。
不能加快速度速度，但是可以减少要与数据进行交互的高延迟往返次数。

指南： 
- [复制模式][4]

### <a name="validation-reduction-and-enrichment"></a>验证、减少和扩充

![验证，缩减，扩充](media/event-hubs-federation-overview/validation-enrichment.jpg)  

事件流可能由你自己的解决方案外部的客户端提交到事件中心。 此类事件流可能要求在外部提交的事件中检查是否符合给定的架构，以及要删除的不符合事件。 

如果客户端的带宽受到了极大的限制，但在多个 "物联网" 方案中，使用的是按流量计费的，或者事件最初是通过约束的数据包大小通过非 IP 网络发送的，则可能必须使用引用数据来增强事件，以便添加更多上下文，使其可供下游事件处理器使用。

在其他情况下，尤其是在合并流时，可能需要通过省略一些细节来降低事件数据的复杂性或大小。

其中的任何操作都可能作为复制、合并或合并流的一部分出现。 

指南： 
- [编辑器模式][6]

### <a name="integration-with-analytics-services"></a>与 analytics services 集成

![与 analytics services 集成](media/event-hubs-federation-overview/integration.jpg)

Azure 的多个云本机分析服务（如 Azure 流分析或 Azure Synapse）最适用于从 Azure 事件中心提供的流式处理或预先批处理数据，Azure 事件中心还可以与多个开源分析包（如 Apache Samza、Apache Flink、Apache Spark 和 Apache Storm）集成。 

如果你的解决方案主要使用服务总线或事件网格，则可通过此类分析系统轻松访问这些事件，并在将它们加入事件中心时使用事件中心捕获进行存档。 事件网格可以通过其 [事件中心集成](../event-grid/handler-event-hubs.md)来实现此操作，对于服务总线，请遵循 [服务总线复制指南](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopyToEventHub)。

Azure 流分析 [直接与事件中心集成](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-event-hubs)。

指南： 
- [复制模式][4]

### <a name="consolidation-and-normalization-of-event-streams"></a>事件流的合并和规范化

![事件流的合并和规范化](media/event-hubs-federation-overview/consolidation.jpg)

全局解决方案通常由在很大程度上独立的区域痕迹组成，其中包括拥有自己的分析功能，但 supra 的区域和全局分析透视图需要集成的透视图，这就是为什么在本地透视的各个区域的不同区域中评估相同事件流的集中式合并的原因。 

规范化是合并方案的一种风格，这种情况下，两个或更多传入事件流具有相同类型的事件，但具有不同的结构或不同的编码，并且在使用之前转码或转换的事件。 

规范化还可能包括加密工作，如解密端对端加密的负载，并使用不同的密钥和算法对下游使用者受众重新加密。 

指南： 
- [合并模式][5]
- [编辑器模式][6]

### <a name="splitting-and-routing-of-event-streams"></a>拆分和路由事件流

![拆分和路由事件流](media/event-hubs-federation-overview/splitting.jpg)

Azure 事件中心偶尔用于 "发布-订阅" 样式方案，其中传入 torrent 引入事件远远超过了 Azure 服务总线或 Azure 事件网格的容量，这两者都具有本机发布-订阅筛选和分发功能，并且是此模式的首选。 

虽然真正的 "发布-订阅" 功能使订阅者能够选择他们想要的事件，但拆分模式具有由预定义的分布模型将事件映射到分区的生成者，并指定的使用者从 "其" 分区独占拉取。 随着事件中心缓冲总体流量，特定分区的内容（表示原始吞吐量卷的一小部分）可以复制到队列中，以便于可靠、事务性、争用的使用者消耗。

许多情况下，事件中心主要用于在某个区域内的应用程序中移动事件，有些情况下，只需从单个分区中选择事件，也可以在其他位置使用。 此方案类似于拆分方案，但可能使用可缩放的路由器，该路由器会考虑到达事件中心的所有消息，而挑拣只选择一些消息进行后路由，并可能通过事件元数据或内容区分路由目标。 

指南：
- [路由模式][7]

### <a name="log-projections"></a>日志预测 

![日志投影](media/event-hubs-federation-overview/log-projection.jpg)

在某些情况下，你将希望能够访问为事件的任何子流发送的最新值，并经常通过分区键来区分。 在 Apache Kafka 中，这通常是通过启用主题的 "日志压缩" 来实现的，这会丢弃除标记有任何唯一键的最新事件之外的所有事件。 日志压缩方法有三个复利缺点： 

- 压缩需要重新组织日志，这是针对仅限追加工作负荷进行优化的 broker 的开销过大的操作。 
- 压缩具有破坏性，不允许对同一流进行压缩和非压缩透视。
- 压缩的流仍具有顺序访问模型，这意味着在日志中查找所需的值需要在最坏的情况下读取整个日志，这通常会导致实现此处所述的确切模式的优化：将日志内容投影到数据库或缓存中。 

最终，压缩的日志是键-值存储，因此这是此类存储的最差可能实现选项。 对于查找和查询，以更高效的方法来创建日志，并将日志永久投影到正确的键值存储区或其他数据库。 

由于事件是不可变的，并且订单始终保留在日志中，因此日志到键值存储中的任何投影对于同一范围的事件始终是相同的，这意味着，你保持更新的投影将始终提供一个权威视图，并且永远不会有任何充分的理由在生成后从日志内容重新生成它。 

指南：
- [日志投影][8]

## <a name="replication-application-technologies"></a>复制应用程序技术

实现上述模式需要为要配置和运行的复制任务提供可缩放且可靠的执行环境。 在 Azure 上，最适合此类任务的运行时环境是无状态任务，是用于有状态流复制任务的 [Azure 流分析](../stream-analytics/stream-analytics-introduction.md) 和无状态复制任务 [Azure Functions](../azure-functions/functions-overview.md) 。

### <a name="stateful-replication-applications-in-azure-stream-analytics"></a>Azure 流分析中的有状态复制应用程序

对于需要考虑事件间关系、创建组合事件、丰富事件或减少事件、创建数据聚合和转换事件负载的有状态复制应用程序， [Azure 流分析](../stream-analytics/stream-analytics-introduction.md) 是最佳的实现选项。

在 Azure 流分析中，你将创建用于集成[输入](../stream-analytics/stream-analytics-add-inputs.md)和[输出](../stream-analytics/stream-analytics-define-outputs.md)的[作业](../stream-analytics/stream-analytics-quick-create-portal.md)，并通过[查询](/stream-analytics-query/stream-analytics-query-language-reference)来集成输入中的数据，从而使输出上提供结果。

查询基于 [SQL 查询语言](/stream-analytics-query/stream-analytics-query-language-reference) ，可用于在一段时间内轻松地对流式处理数据进行筛选、排序、聚合和联接。 还可以通过 [JavaScript](../stream-analytics/stream-analytics-javascript-user-defined-functions.md) 和 [c # 用户定义函数 (udf) ](../stream-analytics/stream-analytics-edge-csharp-udf-methods.md)来扩展此 SQL 语言。 通过简单的语言构造和/或配置执行聚合操作时，可以轻松地调整事件排序选项和时段的持续时间。

每个作业都有一个或多个转换后数据的输出，你可以针对所要分析的信息控制过程。 例如，你能够：

- 将数据发送到 Azure Functions、服务总线主题或队列等服务，以触发下游的通信或自定义工作流。
- 将数据发送到 Power BI 仪表板进行实时仪表板操作。
- 将数据存储在其他 Azure 存储服务中 (例如，Azure Data Lake、Azure Synapse Analytics 等，) 根据历史数据的非常大的索引池来执行批处理分析或训练机器学习模型。
- 存储投影 (也称为 "具体化视图" ) 数据库 ([SQL Database](../stream-analytics/sql-database-output.md)中， [Cosmos DB](../stream-analytics/azure-cosmos-db-output.md) ) 。

### <a name="stateless-replication-applications-in-azure-functions"></a>Azure Functions 中的无状态复制应用程序

对于希望在不考虑事件的有效负载的情况下转发事件或单独处理事件的无状态复制任务，无需考虑事件的关系 (除了其相对顺序) 之外，还可以使用 Azure Functions，这提供了极大的灵活性。

Azure Functions 为 [Azure 事件中心](../azure-functions/functions-bindings-event-hubs.md)、 [azure IoT 中心](../azure-functions/functions-bindings-event-iot.md)、 [Azure 服务总线](../azure-functions/functions-bindings-service-bus.md)、 [azure 事件网格](../azure-functions/functions-bindings-event-grid.md)和 [azure 队列存储](../azure-functions/functions-bindings-storage-queue.md)预生成、可缩放的触发器和输出绑定，以及用于 [RabbitMQ](https://github.com/azure/azure-functions-rabbitmq-extension)的自定义扩展和 [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)。 大多数触发器会根据所记录的指标，根据所记录的指标，根据所记录的指标，增加和减少并发执行实例的数量，从而动态调整吞吐量需求 

对于构建日志投影，Azure Functions 支持 [Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2-output.md) 和 [Azure 表存储](../azure-functions/functions-bindings-storage-table-output.md)的输出绑定。

Azure Functions 可以在 [Azure 托管标识](../active-directory/managed-identities-azure-resources/overview.md) 下运行，因此，它可以在 [Azure Key Vault](../key-vault/general/overview.md)内包含严格访问控制的存储凭据的配置值。

Azure Functions 还允许复制任务直接与 Azure 虚拟网络和所有 Azure 消息传递服务的 [服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md) 集成，并且可以与 [Azure Monitor](../azure-monitor/overview.md)轻松集成。

使用 Azure Functions 消耗计划，预先生成的触发器甚至可以向下扩展到零，而不会有可用于复制的消息，这意味着，使配置准备好进行扩展的情况下不会产生任何费用;使用消耗计划的关键不足之处在于，与此状态的复制任务 "正在唤醒" 的延迟明显高于使用基础结构保持运行的主机计划。  

与所有此类功能相比，用于消息传递和事件的最常见的复制引擎（如 Apache Kafka 的 [MirrorMaker](http://kafka.apache.org/documentation/#basic_ops_mirror_maker) ）需要你提供主机环境并自行缩放复制引擎。 这包括配置和集成安全和网络功能，并推动监视数据的流动，然后你仍没有机会将自定义复制任务注入到流中。 

### <a name="choosing-between-azure-functions-and-azure-stream-analytics"></a>在 Azure Functions 和 Azure 流分析之间进行选择

当你需要在复制事件时处理事件的负载时，Azure 流分析 (ASA) 是最佳选择。 ASA 可以逐个复制事件，也可以创建聚合，以便在转发事件流之前对其信息进行紧缩。 它可以轻松地针对保存在 Azure Blob 存储或 Azure SQL 数据库中的 [引用数据进行补充](../stream-analytics/stream-analytics-use-reference-data.md) ，无需将此类数据导入到流中。

借助 ASA，你可以轻松地在超大规模数据库中创建流的持久性具体化视图。 它是笨 "日志压缩" 模型 Apache Kafka 的一种更高级的方法，即 Kafka 流的易失性的客户端表投影。 

ASA 可以轻松处理具有 [CSV、JSON 和 Apache Avro 格式](../stream-analytics/stream-analytics-parsing-json.md) 编码的负载的事件，并且可以为任何其他格式插入 [自定义反](../stream-analytics/custom-deserializer.md) 。

对于想要 "按原样" 复制事件流且不触及负载的所有复制任务，或者，如果需要实现路由器、执行加密操作、更改负载编码，或者如果需要对数据流内容进行完全控制，Azure Functions 是最佳选项。

## <a name="next-steps"></a>后续步骤

本文介绍了一系列联合模式，并说明了在 Azure 中 Azure Functions 作为事件和消息复制运行时的角色。

接下来，你可能想要了解如何使用 Azure 流分析或 Azure Functions 来设置复制器应用程序，以及如何在事件中心与其他各种事件和消息传递系统之间复制事件流：

- [事件复制任务模式][10]
- [通过 Azure 流分析处理数据][9]
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