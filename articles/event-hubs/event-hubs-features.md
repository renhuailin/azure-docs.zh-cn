---
title: Azure 事件中心功能概述 | Microsoft Docs
description: 本文详细介绍 Azure 事件中心的功能和术语。
ms.topic: overview
ms.date: 08/27/2021
ms.openlocfilehash: b06ce04a7b2fd4fbb55cbe1b3ac8c2510305e781
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123226339"
---
# <a name="features-and-terminology-in-azure-event-hubs"></a>Azure 事件中心的功能和术语

Azure 事件中心是可缩放的事件处理服务，它引入并处理大量事件和数据，具有低延迟和高可靠性。 有关简要概述，请参阅[什么是事件中心？](./event-hubs-about.md)。

本文基于[概述文章](./event-hubs-about.md)中的信息编写，并提供有关事件中心组件和功能的技术和实现详细信息。

> [!TIP]
> [**Apache Kafka** 客户端的协议支持](event-hubs-for-kafka-ecosystem-overview.md)（版本 >= 1.0）提供网络终结点，这些终结点使生成的应用程序可以使用 Apache Kafka，并使任何客户端可以使用事件中心。 可以方便地将大多数现有 Kafka 应用程序重新配置为指向事件中心命名空间而不是 Kafka 群集启动服务器。 
>
>从成本、运营工作量和可靠性的角度看，Azure 事件中心不仅能够很好地替代部署和操作你自己的 Kafka 和 Zookeeper 群集的做法，而且还能完美替代非 Azure 原生的“Kafka 即服务”产品。 
>
> 除了能够获得 Apache Kafka 中介的核心功能以外，你还可以访问 Azure 事件中心的各项功能，例如，通过[事件中心捕获](event-hubs-capture-overview.md)进行自动批处理和存档、自动缩放和均衡、灾难恢复、成本中和的可用性区域支持、灵活且安全的网络集成，以及多协议支持（包括防火墙友好的基于 WebSocket 的 AMQP 协议）。


## <a name="namespace"></a>命名空间
事件中心命名空间是事件中心（在 Kafka 术语中称为“主题”）的管理容器。 事件中心命名空间提供 DNS 集成网络终结点与一系列的访问控制和网络集成管理功能（例如 [IP 筛选](event-hubs-ip-filtering.md)、[虚拟网络服务终结点](event-hubs-service-endpoints.md)和[专用链接](private-link-service.md)）。

:::image type="content" source="./media/event-hubs-features/namespace.png" alt-text="显示事件中心命名空间的图像":::

## <a name="event-publishers"></a>事件发布者

任何向事件中心发送数据的实体都是事件发布者（与“事件生成者”同义） 。 事件发布者可以使用 HTTPS、AMQP 1.0 或 Kafka 协议发布事件。 事件发布者会将基于 Azure Active Directory 的授权与 OAuth2 颁发的 JWT 令牌或特定于事件中心的共享访问签名 (SAS) 令牌配合使用，从而获取发布访问权限。

### <a name="publishing-an-event"></a>发布事件

可以通过 AMQP 1.0、Kafka 协议或 HTTPS 发布事件。 事件中心服务提供 [REST API](/rest/api/eventhub/)、[.NET](event-hubs-dotnet-standard-getstarted-send.md)、[Java](event-hubs-java-get-started-send.md)、[Python](event-hubs-python-get-started-send.md)、[JavaScript](event-hubs-node-get-started-send.md) 和 [Go](event-hubs-go-get-started-send.md) 客户端库，用于将事件发布到事件中心。 对于其他运行时和平台，可以使用任何 AMQP 1.0 客户端，例如 [Apache Qpid](https://qpid.apache.org/)。 

是要使用 AMQP 还 HTTPS 根据具体的使用方案而定。 AMQP 除了需要使用传输级别安全 (TLS) 或 SSL/TLS 以外，还需要建立持久的双向套接字。 初始化会话时，AMQP 具有较高的网络成本，但是 HTTPS 需要为每个请求使用额外的 TLS 开销。 对于频繁进行发布的发布者，AMQP 的性能明显更高，并且，在将 AMQP 与异步发布代码配合使用时，延迟时间可能会大大降低。

可以逐个或者成批发送事件。 单个发布的限制为 1 MB，不管它是单个事件还是一批事件。 发布大于此阈值的事件将被拒绝。 

事件中心吞吐量是通过使用分区和吞吐量单位分配进行缩放的（请参阅下文）。 发布者最好不要知道为事件中心选择的特定分区模型，而只是指定用于一致地将相关事件分配给同一分区的分区键。

![分区键](./media/event-hubs-features/partition_keys.png)

事件中心确保共享分区键值的所有事件存储在一起，并按到达顺序进行传递。 如果将分区键与发布者策略结合使用，则发布者的标识与分区键的值必须匹配。 否则会出错。

### <a name="event-retention"></a>事件保留

根据可配置的基于时间的保留策略从事件中心删除已发布的事件。 下面是一些要点：

- “默认”值和可能的“最短”保留期为“1 天(24 小时)”  。
- 对于事件中心“标准”层，最长保留期为“7 天” 。 
- 对于“高级”和“专用”事件中心，最长保留期为 90 天。
- 如果更改保留期，该更改会应用于所有消息，包括事件中心内已有的消息。 

事件中心在配置的保留时间内保留事件，该时间适用于所有分区。 达到保持期后，事件自动被删除。 如果指定的保留期为一天，则该事件将在得到接受后的 24 小时后变为不可用。 你无法显式地删除事件。 

如果需要将事件存档到超过允许的保留期，可以通过打开“[事件中心捕获](event-hubs-capture-overview.md)”功能将事件自动存储在 Azure 存储或 Azure Data Lake 中。 如果需要搜索或分析此类深层存档，可以轻松地将其导入 [Azure Synapse](store-captured-data-data-warehouse.md) 或其他类似的存储和分析平台。 

事件中心的数据保留限制以时间为基础，其原因是为了防止大量的历史客户数据被捕获到仅由时间戳索引且仅允许顺序访问的深层存储中。 此处的体系结构理念是，历史数据需要比事件中心或 Kafka 提供的实时事件接口更丰富的索引和更直接的访问。 事件流引擎不太适合充当用于事件溯源的数据湖或长期存档。 
 

> [!NOTE]
> 事件中心是一个实时事件流引擎，其设计意图并不是用于代替数据库以及/或者用作无限期保存的事件流的永久存储。 
> 
> 事件流的历史记录越久远，查找给定流的特定历史切片所需的辅助索引就越多。 检查事件负载和索引不在事件中心（或 Apache Kafka）的功能范围内。 因此，数据库以及专用分析存储和引擎（如 [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)、[Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-overview.md) 和 [Azure Synapse](../synapse-analytics/overview-what-is.md)）明显更适合用于存储历史事件。
>
> [事件中心捕获](event-hubs-capture-overview.md)直接与 Azure Blob 存储和 Azure Data Lake Storage 集成，通过这种集成，还可以实现[直接将事件传入 Azure Synapse](store-captured-data-data-warehouse.md)。
>
> 如果你要对应用程序使用[事件溯源](/azure/architecture/patterns/event-sourcing)模式，应使快照策略与事件中心的保留限制保持一致。 请不要计划利用从时间起点开始的原始事件重新生成具体化视图。 如果你的应用程序投入生产一段时间并且使用情况良好，而你的投影生成器需要在不断经历多年变化事件的同时又要尝试及时了解最新的和正在进行的更改，这时你肯定会对这样的策略感到后悔。 


### <a name="publisher-policy"></a>发布者策略

使用事件中心可以通过 *发布者策略* 对事件发布者进行精细控制。 发布者策略是运行时功能，旨在为大量的独立事件发布者提供方便。 借助发布者策略，每个发布者在使用以下机制将事件发布到事件中心时可以使用自身的唯一标识符：

```http
//<my namespace>.servicebus.windows.net/<event hub name>/publishers/<my publisher name>
```

不需要提前创建发布者名称，但它们必须与发布事件时使用的 SAS 令牌匹配，以确保发布者标识保持独立。 使用发布者策略时，**PartitionKey** 值设置为发布者名称。 若要正常工作，这些值必须匹配。

## <a name="capture"></a>捕获

使用[事件中心捕获](event-hubs-capture-overview.md)，可以自动捕获事件中心的流式处理数据，并将其保存到所选 Blob 存储帐户或 Azure Data Lake 存储帐户。 可以从 Azure 门户启用捕获，并指定大小上限和时间范围以执行捕获。 使用事件中心捕获，用户可以指定自己的 Azure Blob 存储帐户和容器或 Azure Data Lake 存储帐户（其中之一用于存储捕获的数据）。 捕获的数据以 Apache Avro 格式编写。

:::image type="content" source="./media/event-hubs-features/capture.png" alt-text="显示将事件中心数据捕获到 Azure 存储或 Azure Data Lake Storage 中的图像":::

事件中心捕获生成的文件具有以下 Avro 架构：

:::image type="content" source="./media/event-hubs-capture-overview/event-hubs-capture3.png" alt-text="显示捕获数据的结构的图像":::

## <a name="partitions"></a>分区
[!INCLUDE [event-hubs-partitions](./includes/event-hubs-partitions.md)]


## <a name="sas-tokens"></a>SAS 令牌

事件中心使用在命名空间和事件中心级别提供的共享访问签名。 SAS 令牌是从 SAS 密钥生成的，它是以特定格式编码的 URL 的 SHA 哈希。 事件中心可以使用密钥（策略）的名称和令牌重新生成哈希，以便对发送者进行身份验证。 通常，为事件发布者创建的 SAS 令牌只对特定的事件中心具有“发送”权限。 此 SAS 令牌 URL 机制是“发布者策略”中介绍的发布者标识的基础。 有关使用 SAS 的详细信息，请参阅[使用服务总线进行共享访问签名身份验证](../service-bus-messaging/service-bus-sas.md)。

## <a name="event-consumers"></a>事件使用者

从事件中心读取事件数据的任何实体称为“事件使用者”。 所有事件中心使用者都通过 AMQP 1.0 会话进行连接，事件会在可用时通过该会话传送。 客户端不需要轮询数据可用性。

### <a name="consumer-groups"></a>使用者组

事件中心的发布/订阅机制通过“使用者组”启用。 使用者组是整个事件中心视图（状态、位置或偏移量）。 使用者组使多个消费应用程序都有各自独立的事件流视图，并按自身步调和偏移量独立读取流。

在流处理体系结构中，每个下游应用程序相当于一个使用者组。 如果要将事件数据写入长期存储，则该存储写入器应用程序就是一个使用者组。 然后，复杂的事件处理可由另一个独立的使用者组执行。 只能通过使用者组访问分区。 事件中心内始终有一个默认使用者组，你最多可以为相应的定价层创建[最大数量的使用者组](event-hubs-quotas.md)。 

每个使用者组的分区上最多可以有 5 个并发读取者，但是建议每个使用者组的分区上只有一个活动接收者。 在单个分区中，每个读取者接收所有消息。 如果在同一分区上有多个读取者，则处理重复消息。 需在代码中处理此问题，这并非易于处理的。 但是，在某些情况下，这是一种有效的方法。

Azure SDK 提供的某些客户端是智能使用者代理，可以自动管理详细信息，以确保每个分区都有一个读取者，并确保正在读取事件中心的所有分区。 这样，你的代码的处理范围便可集中于从事件中心读取的事件，从而可以忽略分区的许多细节。 有关详细信息，请参阅[连接到分区](#connect-to-a-partition)。

以下示例显示了使用者组 URI 约定：

```http
//<my namespace>.servicebus.windows.net/<event hub name>/<Consumer Group #1>
//<my namespace>.servicebus.windows.net/<event hub name>/<Consumer Group #2>
```

下图显示了事件中心流处理体系结构：

![事件中心体系结构](./media/event-hubs-about/event_hubs_architecture.svg)

### <a name="stream-offsets"></a>流偏移量

偏移量  是事件在分区中的位置。 可以将偏移量视为客户端游标。 偏移量是事件的字节编号。 有了该偏移量，事件使用者（读取者）便可以在事件流中指定要从其开始读取事件的点。 可以时间戳或者偏移量值的形式指定偏移量。 使用者负责在事件中心服务的外部存储其自身的偏移量值。 在分区中，每个事件都包含一个偏移量。

![分区偏移](./media/event-hubs-features/partition_offset.png)

### <a name="checkpointing"></a>检查点

*检查点* 是读取者在分区事件序列中标记或提交其位置时执行的过程。 检查点操作由使用者负责，并在使用者组中的每个分区上进行。 这种责任意味着，对于每个使用者组而言，每个分区读取者必须跟踪它在事件流中的当前位置，当它认为数据流已完成时，可以通知服务。

如果读取者与分区断开连接，当它重新连接时，将开始读取前面由该使用者组中该分区的最后一个读取者提交的检查点。 当读取者建立连接时，它会将此偏移量传递给事件中心，以指定要从其开始读取数据的位置。 这样，用户便可以使用检查点将事件标记为已由下游应用程序“完成”，并且在不同计算机上运行的读取者之间发生故障转移时，还可以提供弹性。 可通过在此检查点过程中指定较低偏移量，返回到较旧的数据。 借助此机制，检查点可以实现故障转移复原和事件流重放。

> [!IMPORTANT]
> 偏移量由事件中心服务提供。 处理事件时，由使用者负责提供检查点。

> [!NOTE]
> 如果你在一个环境中使用 Azure Blob 存储作为检查点存储，该环境支持与 Azure 上通常可用的存储 Blob SDK 版本不同的版本，那么你需要使用代码将存储服务 API 版本更改为该环境支持的特定版本。 例如，如果在 [Azure Stack Hub 版本 2002 上运行事件中心](/azure-stack/user/event-hubs-overview)，则存储服务的最高可用版本为 2017-11-09。 在这种情况下，需要使用代码将存储服务 API 版本设定为 2017-11-09。 如需通过示例来了解如何以特定的存储 API 版本为目标，请参阅“GitHub 上的这些示例”： 
> - [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/) 
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/)
> - [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/eventhub/eventhubs-checkpointstore-blob/samples/v1/javascript) 或 [TypeScript](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/eventhub/eventhubs-checkpointstore-blob/samples/v1/typescript)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/)

### <a name="common-consumer-tasks"></a>常见的使用者任务

所有事件中心使用者都通过 AMQP 1.0 会话，一种状态感知型双向信道进行连接。 每个分区都提供一个 AMQP 1.0 会话，方便传输按分区隔离的事件。

#### <a name="connect-to-a-partition"></a>连接到分区

在连接到分区时，常见的做法是使用租用机制来协调读取者与特定分区的连接。 这样，便可以做到一个使用者组中每分区只有一个活动的读取者。 使用事件中心 SDK 中的客户端（充当智能使用者代理）可以简化检查点、租用和管理读取者的操作。 它们是：

- 适用于 .NET 的 [EventProcessorClient](/dotnet/api/azure.messaging.eventhubs.eventprocessorclient)
- 适用于 Java 的 [EventProcessorClient](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/main/java/com/azure/messaging/eventhubs/EventProcessorClient.java)
- 适用于 Python 的 [EventHubConsumerClient](/python/api/azure-eventhub/azure.eventhub.aio.eventhubconsumerclient)
- 适用于 JavaScript/TypeScript 的 [EventHubConsumerClient](/javascript/api/@azure/event-hubs/eventhubconsumerclient)

#### <a name="read-events"></a>读取事件

为特定分区建立 AMQP 1.0 会话和链接后，事件中心服务会将事件传送到 AMQP 1.0 客户端。 与 HTTP GET 等基于提取的机制相比，此传送机制可以实现更高的吞吐量和更低的延迟。 将事件发送到客户端时，每个事件数据实例包含重要的元数据，例如，用于简化对事件序列执行的检查点操作的偏移量和序列号。

事件数据：
* Offset
* 序列号
* 正文
* 用户属性
* 系统属性

管理偏移量由用户负责。

## <a name="next-steps"></a>后续步骤

有关事件中心的详细信息，请访问以下链接：

- 事件中心入门
    - [.NET](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-node-get-started-send.md)
* [事件中心编程指南](event-hubs-programming-guide.md)
* [事件中心中的可用性和一致性](event-hubs-availability-and-consistency.md)
* [事件中心常见问题](event-hubs-faq.yml)
* [事件中心示例](event-hubs-samples.md)
