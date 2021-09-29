---
title: 消息复制任务模式 - Azure 服务总线 | Microsoft Docs
description: 本文为实现特定的消息复制任务模式提供了详细的指导
ms.topic: article
ms.date: 09/28/2021
ms.openlocfilehash: 4effcb9f51532cb2ef87b18b264789c526b57585
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129211784"
---
# <a name="message-replication-tasks-patterns"></a>消息复制任务模式

[联合概述](service-bus-federation-overview.md)和[复制器函数概述](service-bus-federation-replicator-functions.md)说明了复制任务的基本原理和基本元素，建议你在继续本文之前先熟悉它们。

本文详细介绍了概述部分中重点介绍的几种模式的实现指南。 

## <a name="replication"></a>复制 

复制模式将消息从一个队列或主题复制到下一个队列或主题，或从一个队列或主题复制到其他目标，如事件中心。 消息将被转发，而不对消息有效负载进行任何修改。 

[与 Azure 服务总线之间的消息复制](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopy)示例介绍了此模式的实现。

### <a name="sequences-and-order-preservation"></a>序列和顺序保存

复制模型的目标不是将源队列或主题的消息的绝对顺序保存到目标队列或主题中，而是在需要时将重点放在保存应用程序需要的消息的相对顺序上。 应用程序通过为源实体启用会话支持并使用相同的[会话密钥](message-sessions.md)对相关消息进行分组来实现这一点。

会话感知的预生成复制功能可确保将从源实体检索到的具有相同会话 id 的消息序列作为原始序列中的批提交到目标队列或主题中，并且具有相同的会话 id。 

### <a name="service-assigned-metadata"></a>已分配服务的元数据 

从源队列或主题获得的消息的已分配服务的元数据（原始排队时间和序列号）将由目标队列或主题中新的已分配服务的值替换，但对于我们的示例中提供的默认复制任务，原始值将保留在用户属性中：`repl-enqueue-time`（ISO8601 字符串）和 `repl-sequence`。

这些属性的类型为字符串，并包含相应原始属性的字符串化值。  如果多次转发消息，则直接来源的已分配服务的元数据将附加到已存在的属性中，并用分号分隔值。 

### <a name="failover"></a>故障转移

如果将复制用于灾难恢复目的、保护服务总线服务中的区域可用性消息或防止网络中断，则任何此类故障方案都需要执行从一个队列或主题到下一个队列或主题的故障转移，并告知生产者和/或使用者使用辅助终结点。

对于所有故障转移方案，假定命名空间的必需元素在结构上相同，这意味着队列和主题的名称相同，并且共享访问签名规则和/或基于角色的访问控制规则的设置方式相同。 可以遵循[有关移动命名空间的指导](move-across-regions.md)并省略清理步骤，来创建（和更新）次要命名空间。

若要强制生产者和使用者进行切换，你需要在一个易于访问和更新的位置提供有关使用哪个命名空间进行查找的信息。 如果生产者或使用者遇到频繁或持续的错误，他们应该咨询该位置并调整其配置。 有许多方法可以共享该配置，但我们指出以下两种方法：DNS 和文件共享。

#### <a name="dns-based-failover-configuration"></a>基于 DNS 的故障转移配置

一种备选方法是在你控制的 DNS 中保存 DNS SRV 记录中的信息，并指向相应的队列或主题终结点。 请注意，消息中心不允许其终结点直接与 CNAME 记录混淆，这意味着你将使用 DNS 作为终结点地址的弹性查找机制，而不是直接解析 IP 地址信息。 

假设你拥有域 `example.com`，而你的应用程序拥有区域 `test.example.com`。 对于两个备用服务总线，你现在将创建另外两个嵌套区域，并在每个区域中创建一个 SRV 记录。 

SRV 记录遵循常见约定，以 `_azure_servicebus._amqp` 为前缀，并包含两个终结点记录：一个用于端口 5671 上的AMQP-over-TLS，另一个用于端口 443 上的 AMQP-over-WebSockets，都指向与区域对应的命名空间的服务总线终结点。

| 区域                 | SRV 记录
|----------------------|-------------------------------------------------------------
| `sb1.test.example.com` | **`_azure_servicebus._amqp.sb1.test.example.com`**<br>`1 1 5671 sb1-test-example-com.servicebus.windows.net`<br>`2 2 443 sb1-test-example-com.servicebus.windows.net`
| `sb2.test.example.com` | **`_azure_servicebus._amqp.sb1.test.example.com`**<br>`1 1 5671 sb2-test-example-com.servicebus.windows.net`<br>`2 2 443 sb2-test-example-com.servicebus.windows.net`

在应用程序的区域中，你将创建一个 CNAME 条目，该条目指向与主队列或主题相对应的从属区域：

| CNAME 记录                 | Alias
|------------------------------|-------------------------------------------------------------
| `servicebus.test.example.com`  | `sb1.test.example.com`

使用允许显式查询 CNAME 和 SRV 记录的 DNS 客户端（Java 和 .NET 的内置客户端只允许简单地将名称解析为 IP 地址），然后可以解析所需的终结点。 例如，对于 [DnsClient.NET](https://dnsclient.michaco.net/)，lookup 函数为：

``` C#
static string GetServiceBusName(string aliasName)
{
    const string SrvRecordPrefix = "_azure_servicebus._amqp.";
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

此过程类似于[服务总线 Geo-DR](service-bus-geo-dr.md) 的工作方式，但完全在你自己的控制下，并且也适用于主动/主动方案。

#### <a name="file-share-based-failover-configuration"></a>基于文件共享的故障转移配置

使用 DNS 共享终结点信息的最简单的替代方法是将主要终结点的名称放入纯文本文件中，并从一个能够抵御中断并且仍然允许更新的基础结构中提供该文件。 

如果已经运行了具有全局可用性和内容复制的高可用性网站基础结构，请在其中添加这样的文件，并在需要切换时重新发布该文件。

## <a name="merge"></a>合并

合并模式有一个或多个指向一个目标的复制任务，可能与常规生产者同时向同一目标发送消息。 

此模式的变体为：
- 两个或多个复制函数同时从不同的源获取消息并将它们发送到同一目标。
- 另一个复制函数从源获取消息，而目标也由生产者直接使用。 
- 先前的模式，但消息在两个或多个主题之间进行了镜像，从而导致这些主题包含相同的消息，而不管消息在何处生成。

前两种模式变化很小，与普通复制任务没有区别。

最后一个方案要求将已复制的消息从再次复制中排除。 在主动/主动示例中对该技术进行了演示和说明。

## <a name="editor"></a>编辑器

编辑器模式建立在[复制](#replication)模式的基础上，但是在转发之前会修改消息。 此类修改的示例如下：

- 转码 - 如果消息内容（也称为“正文”或“有效负载”）来自使用 Apache Avro 格式或某些专有序列化格式编码的源，但是拥有目标的系统期望内容是由 JSON 编码，则转码复制任务将首先将有效负载从 Apache Avro 反序列化为内存中的对象图，然后将该图序列化为转发消息的 JSON 格式。 转码还包括“内容压缩”和解压缩任务。
- 转换 - 包含结构化数据的消息可能需要重新调整该数据的结构，以便于下游使用者使用。 这可能包括平展嵌套结构、修剪无关数据元素或重塑有效负载以完全适合给定架构等工作。
- ***批处理*** - 消息可以从一个源分批接收（在一次传输中接收多个消息），但必须单独转发到目标，反之亦然。 因此，任务可以基于单个输入消息传输转发多个消息，或者聚合一组随后一起传输的消息。 
- ***验证*** - 在转发来自外部源的消息数据之前，通常需要检查它们是否符合一组规则。 这些规则可以使用架构或代码来表示。 发现不符合符合性的消息可以删除，并在日志中记录问题，或者可以转发到特殊的目标目的地以进一步处理它们。   
- ***扩充*** - 来自某些源的消息数据可能需要进一步扩充上下文，以便在目标系统中可用。 这可能涉及查找引用数据并将该数据嵌入到消息中，或者添加有关复制任务已知但未包含在消息中的源的信息。 
- ***筛选*** - 根据某些规则，来自源的某些消息可能必须从目标中保留。 筛选器根据规则测试消息，如果消息与规则不匹配，则删除该消息。 通过观察某些条件筛选掉重复的消息并删除具有相同值的后续消息是一种筛选形式。
- ***路由和分区*** - 某些复制任务可能允许两个或多个备用目标，并根据消息的元数据或内容定义规则，为任何特定消息选择复制目标。 一种特殊的路由形式是分区，任务根据规则在一个复制目标中显式地分配分区。
- ***加密*** - 复制任务可能必须对来自源的内容和/或加密转发到目标的内容进行解密，和/或必须验证内容和元数据相对于消息中携带的签名的完整性，或附加此类签名。 
- ***证明*** - 复制任务可以将可能受数字签名保护的元数据附加到消息，该消息证明消息已通过特定通道或在特定时间接收到。     
- 链接 - 复制任务可以对消息序列应用签名，从而保护序列的完整性，并且可以检测丢失的消息。

所有这些模式都可以使用 Azure Functions 实现，使用[消息中心触发器](../azure-functions/functions-bindings-service-bus-trigger.md)获取消息，使用[队列或主题输出绑定](../azure-functions/functions-bindings-service-bus-output.md)传递消息。 

## <a name="routing"></a>路由

路由模式建立在[复制](#replication)模式的基础上，但是复制任务不是只有一个源和一个目标，而是有多个目标，如 C#：

``` csharp
[FunctionName("SBRouter")]
public static async Task Run(
    [ServiceBusTrigger("source", Connection = "serviceBusConnectionAppSetting")] Message[] messages,
    [ServiceBus("dest1", Connection = "serviceBusConnectionAppSetting")] QueueClient output1,
    [ServiceBus("dest2", Connection = "serviceBusConnectionAppSetting")] QueueClient output2,
    ILogger log)
{
    foreach (Message messageData in messages)
    {
        // send to output1 or output2 based on criteria 
    }
}
```

路由功能将考虑消息元数据和/或消息有效负载，然后选择要发送到的可用目标之一。

## <a name="next-steps"></a>后续步骤

- [Azure Functions 中的消息复制程序应用程序][1]
- [在服务总线之间复制消息][2]
- [将消息复制到 Azure 事件中心][3]

[1]: service-bus-federation-replicator-functions.md
[2]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopy
[3]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopyToEventHub
