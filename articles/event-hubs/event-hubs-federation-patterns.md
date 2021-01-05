---
title: 事件复制任务模式-Azure 事件中心 |Microsoft Docs
description: 本文提供了有关实现特定事件复制任务模式的详细指南
ms.topic: article
ms.date: 12/12/2020
ms.openlocfilehash: 494de442b636d535fa1ed6fdeeeda28db9783952
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "97861365"
---
# <a name="event-replication-tasks-patterns"></a>事件复制任务模式

[联合概述](event-hubs-federation-overview.md)和复制器[函数概述](event-hubs-federation-replicator-functions.md)介绍了复制任务的基本元素以及复制任务的基本元素，并建议你先熟悉它们，然后再继续阅读本文。

本文详细介绍了 "概述" 部分中突出显示的几个模式的实施指南。

## <a name="replication"></a>复制

复制模式将事件从一个事件中心复制到下一个事件中心，或从事件中心复制到其他目标（如服务总线队列）。 转发事件，而不会对事件负载进行任何修改。

事件 [中心之间](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopy) 的事件复制涵盖了此模式的实现，事件中心 [与服务总线示例之间](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus) 的事件复制以及 [使用事件中心的 Apache Kafka MirrorMaker](event-hubs-kafka-mirror-maker-tutorial.md) 教程适用于将数据从 Apache Kafka broker 复制到事件中心的特定情况。

### <a name="streams-and-order-preservation"></a>流和顺序保存

通过 Azure Functions 或 Azure 流分析进行复制并不是为了确保将源事件中心的准确1:1 克隆到目标事件中心，而是侧重于保留应用程序需要的事件的相对顺序。 应用程序通过对具有相同分区键的相关事件进行分组，并 [在同一分区中按顺序排列具有相同分区键的消息](event-hubs-features.md#partitions)，从而进行通信。

> [!IMPORTANT] 
> 每个事件中心的 "偏移量" 信息都是唯一的，事件中心实例之间的相同事件的偏移量会有所不同。 若要查找复制的事件流中的位置，请使用基于时间的偏移，并引用 [传播服务分配的元数据](#service-assigned-metadata)。
>
> 基于时间的偏移在特定时间点启动接收方：
> - *EventPosition. FromStart ( # B1* -再次读取所有保留的数据。
> - *EventPosition. FromEnd ( # B1* -读取连接时的所有新数据。
> - *EventPosition. FromEnqueuedTime (dateTime)* -从给定日期和时间开始的所有数据。
>
> 在 EventProcessor 中，通过 EventProcessorOptions 上的 InitialOffsetProvider 设置位置。 使用其他接收方 Api，将通过构造函数传递位置。 


预生成的复制函数帮助程序作为 Azure Functions 中使用的 [示例提供](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication) ，以确保从源分区检索到的分区键相同的事件流作为原始流中的批提交到目标事件中心，并使用相同的分区键。

如果源和目标事件中心的分区计数相同，则目标中的所有流都将映射到与源中相同的分区。
如果分区计数不同（这涉及到下面所述的一些进一步的模式），映射将有所不同，但流始终按顺序保存在一起。

属于不同流的事件或不包含目标分区中的分区键的独立事件的相对顺序可能始终与源分区不同。

### <a name="service-assigned-metadata"></a>服务分配的元数据

从源事件中心获取的事件的服务分配的元数据（原始排队时间、序列号和偏移量）被目标事件中心中的新服务分配值替换，但使用 [helper 函数](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication)、在我们的示例中提供的复制任务，原始值将保留在用户属性中： `repl-enqueue-time` (ISO8601 字符串) ， `repl-sequence` ， `repl-offset` 。

这些属性的类型为字符串，并且包含各自原始属性的字符串化值。 如果多次转发事件，则会将直接源的服务分配的元数据追加到已存在的属性中，其值用分号分隔。

### <a name="failover"></a>故障转移

如果使用复制进行灾难恢复，则在事件中心服务中防止出现区域可用性事件，或防止网络中断时，任何此类故障方案都需要执行从一个事件中心到下一个事件中心的故障转移，告诉发生器和/或使用者使用辅助终结点。

对于所有故障转移方案，假定命名空间所需的元素的结构相同，这意味着事件中心和使用者组的名称相同，并且以相同的方式设置共享访问签名规则和/或基于角色的访问控制规则。 可以按照 [移动命名空间](move-across-regions.md) 和省略清理步骤的指导来创建) 辅助命名空间的 (和更新。

若要强制生产者和使用者进行切换，需要在易于访问和更新的位置提供有关要使用的命名空间的信息。 如果创建者或使用者遇到频繁或持久错误，则他们应该咨询该位置并调整其配置。 共享该配置的方法有很多，但在以下各项中指出了两种方法： DNS 和文件共享。

#### <a name="dns-based-failover-configuration"></a>基于 DNS 的故障转移配置

一种备选方法是在所控制的 DNS 中保存 DNS SRV 记录中的信息，并指向相应的事件中心终结点。 

> [!IMPORTANT] 
> 请注意，事件中心不允许其终结点直接化名为 CNAME 记录，这意味着，你将使用 DNS 作为终结点地址的弹性查找机制，而不是直接解析 IP 地址信息。

假设您拥有域， `example.com` 并为您的应用程序提供了一个区域 `test.example.com` 。 对于两个备用事件中心，你现在将创建两个更进一步的嵌套区域，其中每个都有一个 SRV 记录。

SRV 记录遵循常见约定，以 `_azure_eventhubs._amqp` 为前缀并保存两个终结点记录：一个用于端口5671上的 AMQP-OVER TLS，另一个用于端口443上的 AMQP 的事件中心终结点。

| 区域                   | SRV 记录                                                                                                                                                            |
| ---------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `eh1.test.example.com` | **`_azure_servicebus._amqp.eh1.test.example.com`**<br>`1 1 5671 eh1-test-example-com.servicebus.windows.net`<br>`2 2 443 eh1-test-example-com.servicebus.windows.net` |
| `eh2.test.example.com` | **`_azure_servicebus._amqp.eh2.test.example.com`**<br>`1 1 5671 eh2-test-example-com.servicebus.windows.net`<br>`2 2 443 eh2-test-example-com.servicebus.windows.net` |

在应用程序的区域中，你将创建一个 CNAME 条目，该条目指向与你的主事件中心对应的从属区域：

| CNAME 记录                | Alias                    |
| --------------------------- | ------------------------ |
| `eventhub.test.example.com` | `test1.test.example.com` |

如果使用 DNS 客户端来明确地 (Java 和 .NET 的内置客户端使用 Java 和 .NET 的内置客户端，只允许) 的名称到 IP 地址的简单解析，则可以解析所需的终结点。 例如，对于 [DnsClient.NET](https://dnsclient.michaco.net/)，lookup 函数为：

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

该函数返回为当前分区为 CNAME 的区域的端口5671注册的目标主机名，如下所示。

执行故障转移需要编辑 CNAME 记录，并将其指向备用区域。

使用 DNS 并专门 [Azure DNS](../dns/dns-overview.md)的优点是： Azure DNS 信息是全局复制的，因此可针对单一区域停机进行恢复。

此过程类似于 [事件中心异地灾难恢复](event-hubs-geo-dr.md) 的工作原理，但它完全在你自己的控制之下，并且还适用于主动/主动方案。

#### <a name="file-share-based-failover-configuration"></a>基于文件共享的故障转移配置

使用 DNS 来共享终结点信息的最简单替代方法是将主终结点的名称放入纯文本文件，并为该文件提供一个可靠的基础结构，使其能够抵御中断，同时仍然允许更新。

如果已运行具有全局可用性和内容复制功能的高可用性网站基础结构，请在其中添加此类文件并重新发布文件（如果需要交换机）。

> [!CAUTION]
> 你应仅以这种方式发布终结点名称，而不是包含机密的完整连接字符串。

#### <a name="extra-considerations-for-failing-over-consumers"></a>故障转移使用者的其他注意事项

对于事件中心使用者，故障转移策略的更多注意事项取决于事件处理器的需求。

如果发生灾难，需要重新生成系统（包括数据库、备份数据），并且直接从事件中心的事件中的事件中进行中间处理，则将还原备份，然后想要从创建数据库备份的那一刻开始将事件重播到系统中，而不是从原始系统被销毁那一刻开始。

如果故障只影响系统的一个扇区，或只是一个无法访问的事件中心，则您可能希望继续处理来自处理中断的相同位置的事件。

要实现这两种方案，并使用各自 Azure SDK 的事件处理器， [你将创建新的检查点存储](event-processor-balance-partition-load.md#checkpointing) ，并根据你要从中恢复处理的 _时间戳_ 来提供初始分区位置。

如果仍有权访问正在切换的事件中心的检查点存储，则上述 [传播的元数据](#service-assigned-metadata) 将帮助你跳过已处理的事件，并从上次停止的位置精确恢复。

## <a name="merge"></a>合并

合并模式有一个或多个指向一个目标的复制任务，可能与常规发生器同时发送事件到同一目标。

这些模式的变体如下：

- 两个或更多复制函数同时从不同的源获取事件并将其发送到相同的目标。
- 还有一个复制函数从源获取事件，同时目标也直接由创建者使用。
- 以前的模式，但在两个或更多事件中心之间进行镜像，导致这些事件中心包含相同的流，无论在何处生成事件。

前两种模式变化很简单，并且与普通复制任务不同。

最后一个方案要求不再复制已复制的事件。 此方法在 [EventHubToEventHubMerge](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/code/EventHubMerge) 示例中进行了演示和说明。

## <a name="editor"></a>编辑器

编辑器模式以 [复制](#replication) 模式为基础，但在转发消息之前会对其进行修改。 

此类修改的示例如下：

- **_转码_** -如果事件内容 (也称为 "主体" 或 "有效负载" ) 从使用 _Apache Avro_ 格式或某种专有序列化格式的源到达编码，但是，拥有目标的系统的目标是对内容进行 _JSON_ 编码，转换复制任务首先将有效负载从 _Apache Avro_ 反序列化为内存中对象图形，然后将该图形序列化为要转发的事件的 _JSON_ 格式。 转码还包括 **内容压缩** 和解压缩任务。
- **_转换_** -包含结构化数据的事件可能需要对这些数据进行重构，以便下游使用者更方便地使用。 这可能涉及到诸如平展嵌套结构、修剪无关数据元素，或将有效负载调整为与给定架构完全匹配这样的工作。
- **_批处理_** -可以批量接收事件 (单个传输) 中的多个事件，但必须将其单独转发到目标，反之亦然。 因此，任务可能会基于单个输入事件传输转发多个事件，或聚合随后传输的一组事件。
- 通常需要检查外部源的 **_验证_** 事件数据是否符合一组规则，然后才能将这些数据转发。 这些规则可以使用架构或代码表示。 发现不符合合规性的事件可能会被删除，并且在日志中记录了问题，或者可能转发到特殊的目标目标以进一步处理它们。
- **_扩充_** -来自某些源的事件数据可能需要扩充，使其能够在目标系统中使用。 这可能涉及到查找引用数据并将该数据嵌入到事件中，或添加有关复制任务已知但不包含在事件中的源的信息。
- **_筛选_** -可能需要根据某些规则从目标中预扣从源传入的某些事件。 筛选器根据规则测试事件，并在事件与规则不匹配时删除事件。 通过观察某些条件并删除具有相同值的后续事件，筛选出重复事件是一种筛选形式。
- **_加密_** -复制任务可能必须解密从源传入的内容和/或加密传递到目标的内容，并且/或者必须验证内容和元数据的完整性是否与事件中传送的签名相关，或者附加此类签名。
- **_证明_** -复制任务可能会将元数据（可能受数字签名保护）附加到事件，该事件可证实已通过特定通道或在特定时间接收到事件。
- **_链接_** -复制任务可能会将签名应用于事件流，以便可以检测到流的完整性并检测缺少的事件。

通常，在 [Azure 流分析](../stream-analytics/stream-analytics-introduction.md) 作业中实现转换、批处理和扩充模式。 

所有这些模式都可以使用 Azure Functions 实现，使用 [事件中心触发器](../azure-functions/functions-bindings-event-hubs-trigger.md) 获取事件，并使用 [事件中心输出绑定](../azure-functions/functions-bindings-event-hubs-output.md) 来传递它们。

## <a name="routing"></a>路由

路由模式以 [复制](#replication) 模式为基础，但复制任务具有多个目标，此处以 c # 阐释：

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

路由函数将考虑消息元数据和/或消息负载，然后选取要发送到的可用目标之一。

在 Azure 流分析中，可以通过定义多个输出，然后针对每个输出执行查询来实现相同的目的。

```sql
select * into dest1Output from inputSource where Info = 1
select * into dest2Output from inputSource where Info = 2
```


## <a name="log-projection"></a>日志投影

日志投影模式将事件流平展到索引的数据库中，并在数据库中将事件变为记录。 通常情况下，事件被添加到同一个集合或表中，事件中心分区键会成为主键的参与方，从而使记录成为唯一记录。

日志投影可生成事件数据的时间序列 historian 或压缩视图，因此每个分区键仅保留最新的事件。 目标数据库的形状最终取决于您和您的应用程序的需求。 此模式也称为 "事件源"。

> [!TIP]
> 你可以轻松地在 azure 流分析中创建日志投影 [，并将](../stream-analytics/sql-database-output.md) 其 [Azure Cosmos DB](../stream-analytics/azure-cosmos-db-output.md) 在 azure 流分析中，你应首选该选项。

以下 Azure 函数将事件中心的内容压缩到 Azure CosmosDB 集合。

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
