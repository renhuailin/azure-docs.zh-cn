---
title: 事件复制任务模式 - Azure 事件中心 | Microsoft Docs
description: 本文为实现特定的事件复制任务模式提供了详细指导
ms.topic: article
ms.date: 09/28/2021
ms.openlocfilehash: f4fb2a166a5dfd50f3035e5efe5462313716daef
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129217403"
---
# <a name="event-replication-tasks-patterns"></a>事件复制任务模式

[联合概述](event-hubs-federation-overview.md)和[复制器函数概述](event-hubs-federation-replicator-functions.md)说明了复制任务的基本原理和基本元素，建议你在继续本文之前先熟悉它们。

本文详细介绍了概述部分中重点介绍的几种模式的实现指南。

## <a name="replication"></a>复制

复制模式将事件从一个事件中心复制到下一个事件中心，或者从事件中心复制到其他某个目标（例如服务总线队列）。 事件将被转发，而不对事件有效负载进行任何修改。

[事件中心之间的事件复制](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopy)和[事件中心与服务总线之间的事件复制](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus)示例，以及适用于将数据从 Apache Kafka 中介复制到事件中心的特定案例的[将 Apache Kafka MirrorMaker 与事件中心配合使用](event-hubs-kafka-mirror-maker-tutorial.md)教程，介绍了此模式的实现。

### <a name="streams-and-order-preservation"></a>流和顺序保留

通过 Azure Functions 或 Azure 流分析进行复制并不旨在确保创建源事件中心到目标事件中心的完全一对一克隆，而是注重保留应用程序所需的事件相对顺序。 为了传达这一需求，应用程序会将具有相同分区键的相关事件分组到一起，而[事件中心则会将具有相同分区键的消息按顺序排列到同一分区中](event-hubs-features.md#partitions)。

> [!IMPORTANT] 
> 这种“偏移”信息对于每个事件中心是唯一的，相同事件的偏移在不同的事件中心实例之间各不相同。 若要在复制的事件流中查找某个位置，请使用基于时间的偏移，并参考[传播的服务分配元数据](#service-assigned-metadata)。
>
> 基于时间的偏移在特定时间点启动接收器：
> - EventPosition.FromStart() - 再次读取所有保留的数据。
> - EventPosition.FromEnd() - 读取自连接后生成的所有新数据。
> - EventPosition.FromEnqueuedTime(dateTime) - 从给定日期和时间开始的所有数据。
>
> 在 EventProcessor 中，可以通过 EventProcessorOptions 中的 InitialOffsetProvider 设置位置。 使用其他接收器 API 时，将通过构造函数传递位置。 


在基于 Azure Functions 的指导中使用的[作为示例提供的](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication)预生成复制函数帮助程序可确保从源分区检索到的、具有相同分区键的事件流，以批的形式提交到原始流中具有相同分区键的目标事件中心。

如果源和目标事件中心的分区计数相同，则目标中的所有流将与在源中一样映射到相同的分区。
如果分区计数不同（这涉及到下面所述的其他一些模式），则映射也会不同，但流始终按顺序保留在一起。

目标分区中属于不同流的事件或者没有分区键的独立事件的相对顺序可能始终与源分区不同。

### <a name="service-assigned-metadata"></a>已分配服务的元数据

从源事件中心获取的事件的服务分配元数据以及原始排队时间、序列号和偏移量，将在目标事件中心内替换为新的服务分配值，但如果使用[帮助程序函数](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication)（示例中提供的复制任务），原始值将保留在用户属性中：`repl-enqueue-time`（ISO8601 字符串）、`repl-sequence` 和 `repl-offset`。

这些属性的类型为字符串，并包含相应原始属性的字符串化值。 如果多次转发事件，则直接来源的服务分配元数据将追加到已存在的属性，并以分号分隔值。

### <a name="failover"></a>故障转移

如果将复制用于灾难恢复目的、防范事件中心服务中发生区域性的可用性事件或防范网络中断，在出现任何此类情景时，都需要执行从一个事件中心到下一个事件中心的故障转移，并告知生成者和/或使用者使用次要终结点。

所有故障转移方案假设命名空间的所需元素在结构上相同，这意味着，事件中心和使用者组的名称相同，并且共享访问签名规则和/或基于角色的访问控制规则的设置方式相同。 可以遵循[有关移动命名空间的指导](move-across-regions.md)并省略清理步骤，来创建（和更新）次要命名空间。

若要强制生产者和使用者进行切换，你需要在一个易于访问和更新的位置提供有关使用哪个命名空间进行查找的信息。 如果生产者或使用者遇到频繁或持续的错误，他们应该咨询该位置并调整其配置。 有许多方法可以共享该配置，但我们指出以下两种方法：DNS 和文件共享。

#### <a name="dns-based-failover-configuration"></a>基于 DNS 的故障转移配置

一种备选方法是在你控制的 DNS 中的 DNS SRV 记录内保存信息，并指向相应的事件中心终结点。 

> [!IMPORTANT] 
> 请注意，事件中心不允许其终结点直接与 CNAME 记录混淆，这意味着你将使用 DNS 作为终结点地址的弹性查找机制，而不是直接解析 IP 地址信息。

假设你拥有域 `example.com`，而你的应用程序拥有区域 `test.example.com`。 对于两个备用事件中心，你现在将创建另外两个嵌套区域，并在每个区域中创建一个 SRV 记录。

SRV 记录遵循通用约定，以 `_azure_eventhubs._amqp` 为前缀，并包含两个终结点记录：一个用于端口 5671 上的AMQP-over-TLS，另一个用于端口 443 上的 AMQP-over-WebSockets，都指向与区域对应的命名空间的事件中心终结点。

| 区域                   | SRV 记录                                                                                                                                                            |
| ---------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `eh1.test.example.com` | **`_azure_servicebus._amqp.eh1.test.example.com`**<br>`1 1 5671 eh1-test-example-com.servicebus.windows.net`<br>`2 2 443 eh1-test-example-com.servicebus.windows.net` |
| `eh2.test.example.com` | **`_azure_servicebus._amqp.eh2.test.example.com`**<br>`1 1 5671 eh2-test-example-com.servicebus.windows.net`<br>`2 2 443 eh2-test-example-com.servicebus.windows.net` |

在应用程序的区域中，你将创建一个 CNAME 条目，该条目指向与主要事件中心相对应的从属区域：

| CNAME 记录                | Alias                    |
| --------------------------- | ------------------------ |
| `eventhub.test.example.com` | `eh1.test.example.com`   |

使用允许显式查询 CNAME 和 SRV 记录的 DNS 客户端（Java 和 .NET 的内置客户端只允许简单地将名称解析为 IP 地址），然后可以解析所需的终结点。 例如，对于 [DnsClient.NET](https://dnsclient.michaco.net/)，lookup 函数为：

```C#
static string GetEventHubName(string aliasName)
{
    const string SrvRecordPrefix = "_azure_eventhub._amqp.";
    LookupClient lookup = new LookupClient();

    return (from CNameRecord alias in (lookup.Query(aliasName, QueryType.CNAME).Answers)
            from SrvRecord srv in lookup.Query(SrvRecordPrefix + alias.CanonicalName, QueryType.SRV).Answers
            where srv.Port == 5671
            select srv.Target).FirstOrDefault()?.Value.TrimEnd('.');
}
```

该函数返回为当前别名为 CNAME 的区域的端口 5671 注册的目标主机名，如上所示。

执行故障转移需要编辑 CNAME 记录并将其指向备用区域。

使用 DNS，特别是 [Azure DNS](../dns/dns-overview.md) 的优点是，Azure DNS 信息是全局复制的，因此能够抵御单区域中断。

此过程类似于[事件中心异地灾难恢复](event-hubs-geo-dr.md)的工作方式，但完全在你自己的控制下，并且也适用于主动/主动方案。

#### <a name="file-share-based-failover-configuration"></a>基于文件共享的故障转移配置

使用 DNS 共享终结点信息的最简单的替代方法是将主要终结点的名称放入纯文本文件中，并从一个能够抵御中断并且仍然允许更新的基础结构中提供该文件。

如果已经运行了具有全局可用性和内容复制的高可用性网站基础结构，请在其中添加这样的文件，并在需要切换时重新发布该文件。

> [!CAUTION]
> 应该只以这种方式发布终结点名称，而不要使用包含机密的完整连接字符串来发布。

#### <a name="extra-considerations-for-failing-over-consumers"></a>有关故障转移使用者的其他注意事项

对于事件中心使用者，故障转移策略的其他注意事项取决于事件处理程序的需求。

如果发生灾难后需要基于备份数据重新构建系统（包括数据库），而数据库中的数据是直接馈送的，或者是通过对事件中心内保存的事件进行中间处理来馈送的，那么，你可以还原备份，然后开始将事件重播到系统，这些事件是创建数据库备份的那一刻，而不是从销毁原始系统的那一刻生成的。

如果故障只影响到系统的某个切片或者确实只影响了一个不可访问的事件中心，则你可以从处理中断的大致位置继续处理事件。

若要实现这两种方案并使用相应 Azure SDK 的事件处理程序，请[创建新的检查点存储](event-processor-balance-partition-load.md#checkpointing)，并根据你要从中恢复处理的时间戳提供初始分区位置。

如果你仍可以访问从中切换出去的事件中心的检查点存储，则上述[传播的元数据](#service-assigned-metadata)将帮助你跳过已处理的事件，并从上次离开的精确位置恢复处理。

## <a name="merge"></a>合并

合并模式有一个或多个指向一个目标的复制任务，可能与常规生成者同时向同一目标发送事件。

这些模式的变体如下：

- 两个或更多个复制函数，它们从不同的源并发获取事件并将其发送到同一目标。
- 另一个复制函数，它在目标也由生成者直接使用时从源获取事件。
- 先前的模式，但已在两个或更多个事件中心之间镜像，导致这些事件中心包含相同的流，而不管事件在何处生成。

前两种模式变化很小，与普通复制任务没有区别。

最后一个方案要求排除已复制的事件，防止将其再次复制。 [EventHubToEventHubMerge](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/code/EventHubMerge) 示例中演示并解释了相关方法。

## <a name="editor"></a>编辑器

编辑器模式建立在[复制](#replication)模式的基础上，但是在转发之前会修改消息。 

此类修改的示例如下：

- 转码 - 如果事件内容（也称为“正文”或“有效负载”）来自使用 Apache Avro 格式或某些专有序列化格式编码的源，但是拥有目标的系统期望内容是 JSON 编码的，转码复制任务将首先将有效负载从 Apache Avro 反序列化为内存中的对象图，然后将该图序列化为转发事件的 JSON 格式   。 转码还包括“内容压缩”和解压缩任务。
- 转换 - 包含结构化数据的事件可能需要重新调整该数据的结构，以便于下游使用者使用。 这可能包括平展嵌套结构、修剪无关数据元素或重塑有效负载以完全适合给定架构等工作。
- 批处理 - 事件可以从一个源分批接收（在一次传输中接收多个事件），但必须单独转发到目标，反之亦然。 因此，任务可以基于单个输入事件传输转发多个事件，或者聚合一组随后一起传输的事件。
- 验证 - 在转发来自外部源的事件数据之前，通常需要检查它们是否符合一组规则。 这些规则可以使用架构或代码来表示。 发现不合规的事件可以删除，并在日志中记录问题，或者可以转发到特殊的目标以进一步处理。
- 扩充 - 来自某些源的事件数据可能需要进一步扩充上下文，以便在目标系统中可用。 这可能涉及查找引用数据并将该数据嵌入到事件中，或者添加有关复制任务已知但未包含在事件中的源的信息。
- 筛选 - 根据某些规则，来自源的某些事件可能必须在目标中保留。 筛选器根据规则测试事件，如果该事件与规则不匹配，则删除该事件。 通过观察某些条件筛选掉重复的事件并删除具有相同值的后续事件是一种筛选形式。
- 加密 - 复制任务可能必须对来自源的内容和/或加密转发到目标的内容进行解密，和/或必须验证内容和元数据相对于事件中携带的签名的完整性，或附加此类签名。
- 证明 - 复制任务可以将可能受数字签名保护的元数据附加到事件，该事件证明自身已通过特定通道或在特定时间接收到。
- 链接 - 复制任务可对事件流应用签名，从而保护流的完整性，并且可以检测丢失的消息。

一般情况下，最好是使用 [Azure 流分析](../stream-analytics/stream-analytics-introduction.md)作业实现转换、批处理和扩充模式。 

所有这些模式都可以使用 Azure Functions 来实现，可以使用[事件中心触发器](../azure-functions/functions-bindings-event-hubs-trigger.md)获取事件，并使用[事件中心输出绑定](../azure-functions/functions-bindings-event-hubs-output.md)来传递消息。

## <a name="routing"></a>路由

路由模式建立在[复制](#replication)模式的基础上，但是复制任务不是只有一个源和一个目标，而是有多个目标，如 C#：

```csharp
[FunctionName("EH2EH")]
public static async Task Run(
    [EventHubTrigger("source", Connection = "EventHubConnectionAppSetting")] EventData[] events,
    [EventHub("dest1", Connection = "EventHubConnectionAppSetting")] EventHubClient output1,
    [EventHub("dest2", Connection = "EventHubConnectionAppSetting")] EventHubClient output2,
    ILogger log)
{
    foreach (EventData eventData in events)
    {
        // send to output1 and/or output2 based on criteria
        EventHubReplicationTasks.ConditionalForwardToEventHub(input, output1, log, (eventData) => {
            return ( inputEvent.SystemProperties.SequenceNumber%2==0 ) ? inputEvent : null;
        });
        EventHubReplicationTasks.ConditionalForwardToEventHub(input, output2, log, (eventData) => {
            return ( inputEvent.SystemProperties.SequenceNumber%2!=0 ) ? inputEvent : null;
        });
    }
}
```

路由功能将考虑消息元数据和/或消息有效负载，然后选择要发送到的可用目标之一。

在 Azure 流分析中，可以通过定义多个输出，然后针对每个输出执行查询来实现相同的模式。

```sql
select * into dest1Output from inputSource where Info = 1
select * into dest2Output from inputSource where Info = 2
```


## <a name="log-projection"></a>日志投影

日志投影模式将事件流平展到已编制索引的数据库，流中的事件将变成数据库中的记录。 通常，事件将添加到同一个集合或表中，事件中心分区键将变成旨在使记录保持唯一的主键的一部分。

日志投影可以生成事件数据时序历史图或精简视图，从而只为每个分区键保留最新事件。 目标数据库的形状最终取决于你和你的应用程序的需求。 此模式也称为“事件溯源”。

> [!TIP]
> 可以使用 Azure 流分析在 [Azure SQL 数据库](../stream-analytics/sql-database-output.md)和 [Azure Cosmos DB](../stream-analytics/azure-cosmos-db-output.md) 中轻松创建日志投影，这也是建议的首选做法。

以下 Azure 函数将一个精简事件中心的内容投影到 Azure CosmosDB 集合中。

```C#
[FunctionName("Eh1ToCosmosDb1Json")]
[ExponentialBackoffRetry(-1, "00:00:05", "00:05:00")]
public static async Task Eh1ToCosmosDb1Json(
    [EventHubTrigger("eh1", ConsumerGroup = "Eh1ToCosmosDb1", Connection = "Eh1ToCosmosDb1-source-connection")] EventData[] input,
    [CosmosDB(databaseName: "SampleDb", collectionName: "foo", ConnectionStringSetting = "CosmosDBConnection")] IAsyncCollector<object> output,
    ILogger log)
{
    foreach (var ev in input)
    {
        if (!string.IsNullOrEmpty(ev.SystemProperties.PartitionKey))
        {
            var record = new
            {
                id = ev.SystemProperties.PartitionKey,
                data = JsonDocument.Parse(ev.Body),
                properties = ev.Properties
            };
            await output.AddAsync(record);
        }
    }
}
```

## <a name="next-steps"></a>后续步骤

- [Azure Functions 中的事件复制器应用程序][1]
- [在事件中心之间复制事件][2]
- [将事件复制到 Azure 服务总线][3]

[1]: event-hubs-federation-replicator-functions.md
[2]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopy
[3]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus
