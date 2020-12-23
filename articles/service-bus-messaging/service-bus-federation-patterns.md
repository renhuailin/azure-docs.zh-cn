---
title: 消息复制任务模式-Azure 服务总线 |Microsoft Docs
description: 本文提供了有关实现特定消息复制任务模式的详细指南
ms.topic: article
ms.date: 12/12/2020
ms.openlocfilehash: d823ee7ccd4f53bfc3e10211a4f44908273a110d
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657366"
---
# <a name="message-replication-tasks-patterns"></a>消息复制任务模式

[联合概述](service-bus-federation-overview.md)和复制器[函数概述](service-bus-federation-replicator-functions.md)介绍了复制任务的基本元素以及复制任务的基本元素，并建议你先熟悉它们，然后再继续阅读本文。

本文详细介绍了 "概述" 部分中突出显示的几个模式的实施指南。 

## <a name="replication"></a>复制 

复制模式将消息从一个队列或主题复制到下一个队列或主题，或从队列或主题复制到其他目标（如事件中心）。 消息将被转发，而不会对消息有效负载做出任何修改。 

[与 Azure 服务总线的 "复制到 Azure" 和 "来自 Azure 服务总线的消息](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopy)" 示例涵盖了此模式的实现。

### <a name="sequences-and-order-preservation"></a>序列和顺序保存

复制模型不会将源队列或主题的绝对消息顺序保存到目标队列或主题中，而是在需要时，保留应用程序所需的消息相对顺序。 应用程序通过为源实体启用会话支持并使用同一 [会话密钥](message-sessions.md)对相关消息进行分组来实现此功能。

能够识别会话的预生成复制函数确保将具有相同会话 id 的消息序列作为原始序列中的批提交到目标队列或主题，并使用相同的会话 id。 

### <a name="service-assigned-metadata"></a>服务分配的元数据 

从源队列或主题获取的消息的服务分配的元数据（原始排队时间和序列号）将替换为目标队列或主题中的新服务分配的值，但使用我们的示例中提供的默认复制任务，原始值将保留在用户属性中： `repl-enqueue-time` (ISO8601 字符串) 和 `repl-sequence` 。

这些属性的类型为字符串，并且包含各自原始属性的字符串化值。  如果多次转发消息，则会将直接源的服务分配的元数据追加到已存在的属性中，其值用分号分隔。 

### <a name="failover"></a>故障转移

如果使用复制进行灾难恢复，则在服务总线服务中防止区域可用性消息，或针对网络中断，任何此类故障方案都需要执行从一个队列或主题到下一个队列或主题的故障转移，告诉发生器和/或使用者使用辅助终结点。

对于所有故障转移方案，假定命名空间所需的元素的结构相同，这意味着队列和主题的名称相同，并且以相同的方式设置共享访问签名规则和/或基于角色的访问控制规则。 可以按照 [移动命名空间](move-across-regions.md) 和省略清理步骤的指导来创建) 辅助命名空间的 (和更新。

若要强制生产者和使用者进行切换，需要在易于访问和更新的位置提供有关要使用的命名空间的信息。 如果创建者或使用者遇到频繁或持久错误，则他们应该咨询该位置并调整其配置。 共享该配置的方法有很多，但在以下各项中指出了两种方法： DNS 和文件共享。

#### <a name="dns-based-failover-configuration"></a>基于 DNS 的故障转移配置

一种备选方法是在所控制的 DNS 中保存 DNS SRV 记录中的信息，并指向相应的队列或主题终结点。 请注意，消息中心不允许其终结点直接化名为 CNAME 记录，这意味着将使用 DNS 作为终结点地址的弹性查找机制，而不是直接解析 IP 地址信息。 

假设您拥有域， `example.com` 并为您的应用程序提供了一个区域 `test.example.com` 。 对于两个备用的服务总线，你现在将创建两个更进一步的嵌套区域，其中每个都有一个 SRV 记录。 

SRV 记录遵循常见约定，以 `_azure_servicebus._amqp` 为前缀并保存两个终结点记录：一个用于端口5671上的 AMQP-OVER TLS，另一个用于端口443上的 AMQP 的服务总线终结点。

| 区域                 | SRV 记录
|----------------------|-------------------------------------------------------------
| `sb1.test.example.com` | **`_azure_servicebus._amqp.sb1.test.example.com`**<br>`1 1 5671 sb1-test-example-com.servicebus.windows.net`<br>`2 2 443 sb1-test-example-com.servicebus.windows.net`
| `sb2.test.example.com` | **`_azure_servicebus._amqp.sb1.test.example.com`**<br>`1 1 5671 sb2-test-example-com.servicebus.windows.net`<br>`2 2 443 sb2-test-example-com.servicebus.windows.net`

在应用程序的区域中，你将创建一个 CNAME 条目，该条目指向与你的主队列或主题对应的从属区域：

| CNAME 记录                 | Alias
|------------------------------|-------------------------------------------------------------
| `servicebus.test.example.com`  | `test1.test.example.com`

如果使用 DNS 客户端来明确地 (Java 和 .NET 的内置客户端使用 Java 和 .NET 的内置客户端，只允许) 的名称到 IP 地址的简单解析，则可以解析所需的终结点。 例如，对于 [DnsClient.NET](https://dnsclient.michaco.net/)，lookup 函数为：

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

该函数返回为当前分区为 CNAME 的区域的端口5671注册的目标主机名，如下所示。 

执行故障转移需要编辑 CNAME 记录，并将其指向备用区域。 

使用 DNS 并专门 [Azure DNS](../dns/dns-overview.md)的优点是： Azure DNS 信息是全局复制的，因此可针对单一区域停机进行恢复。

此过程类似于 [服务总线异地灾难恢复](service-bus-geo-dr.md) 的工作原理，但它完全在你自己的控制之下，并且还适用于主动/主动方案。

#### <a name="file-share-based-failover-configuration"></a>基于文件共享的故障转移配置

使用 DNS 来共享终结点信息的最简单替代方法是将主终结点的名称放入纯文本文件，并为该文件提供一个可靠的基础结构，使其能够抵御中断，同时仍然允许更新。 

如果已运行具有全局可用性和内容复制功能的高可用性网站基础结构，请在其中添加此类文件并重新发布文件（如果需要交换机）。

## <a name="merge"></a>合并

合并模式有一个或多个指向一个目标的复制任务，可能与常规发生器同时向同一目标发送消息。 

此模式的变体为：
- 两个或更多复制函数同时从不同的源获取消息，并将这些消息发送到同一目标。
- 还有一个复制函数从源获取消息，而目标也直接由创建者使用。 
- 以前的模式，但在两个或多个主题之间镜像的消息会导致这些主题包含相同的消息，而不管消息是在何处生成的。

前两种模式变化很简单，并且与普通复制任务不同。

最后一个方案要求不包括已复制的消息，因而无法再次复制。 本技术在活动/活动示例中进行了演示和说明。

## <a name="editor"></a>编辑器

编辑器模式以 [复制](#replication) 模式为基础，但在转发消息之前会对其进行修改。 此类修改的示例如下：

- **_转码_* _-如果消息内容 (也称为 "主体" 或 "负载"，则 ) 从使用 _Apache Avro * 格式或某种专用序列化格式的源到达编码。但是，拥有目标的系统的目标是对内容进行 *JSON* 编码，转换复制任务首先将有效负载从 *Apache Avro* 反序列化为内存中对象图形，然后将该图形序列化为要转发的消息的 *JSON* 格式。 转码还包括 **内容压缩** 和解压缩任务。
- **_转换_* _-包含结构化数据的消息可能需要对这些数据进行重构，以便下游使用者更方便地使用这些数据。 这可能涉及到诸如平展嵌套结构、修剪无关数据元素，或将有效负载调整为与给定架构完全匹配这样的工作。
- _*_批处理_*_ -在单个传输) 从源接收多个消息 (多条消息，但必须将其单独转发到目标，反之亦然。 因此，任务可以根据单个输入消息传输转发多个消息，或聚合一组随后传输的消息。 
- _*_验证_*_ -通常需要检查外部源中的消息数据是否符合一组规则，然后才能转发这些数据。 这些规则可以使用架构或代码表示。 发现不符合合规性的消息可能会被删除，并且在日志中记录了问题，也可能转发到特殊的目标目标以进一步处理它们。   
- _*_扩充_*_ -来自某些源的消息数据可能需要扩充，使其能够在目标系统中使用。 这可能涉及到查找引用数据并将该数据嵌入消息，或添加有关复制任务已知的源的信息，但不包含在消息中。 
- _*_筛选_*_ -可能需要根据某些规则从目标中预扣从源传入的某些消息。 如果消息与规则不匹配，筛选器将根据规则测试消息并删除消息。 通过观察某些条件并删除具有相同值的后续消息，筛选出重复的消息是一种筛选形式。
- _*_路由和分区_*_ -某些复制任务可能允许两个或更多备用目标，并定义基于该消息的元数据或内容为任何特定消息选择的复制目标的规则。 一种特殊形式的路由是分区，其中的任务根据规则在一个复制目标中显式分配分区。
- _*_加密_*_ -复制任务可能必须解密从源传入的内容和/或加密传递到目标的内容，并且/或者必须验证内容和元数据的完整性是否与消息中传送的签名相关，或者附加此类签名。 
- _*_证明_*_ -复制任务可能会将元数据（可能受数字签名保护）附加到一条消息，该消息证实已经通过特定通道或在特定时间接收了消息。     
- _ *_链接_**-复制任务可能会将签名应用到消息序列，以便可以检测到序列的完整性，并可检测到缺少的消息。

所有这些模式都可以使用 Azure Functions 实现，使用 [消息中心触发器](../azure-functions/functions-bindings-service-bus-trigger.md) 获取消息，并使用 [队列或主题输出绑定](../azure-functions/functions-bindings-service-bus-output.md) 来传递它们。 

## <a name="routing"></a>路由

路由模式以 [复制](#replication) 模式为基础，但复制任务具有多个目标，此处以 c # 阐释：

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

路由函数将考虑消息元数据和/或消息负载，然后选取要发送到的可用目标之一。

## <a name="next-steps"></a>后续步骤

- [Azure Functions 中的消息复制器应用程序][1]
- [在服务总线之间复制消息][2]
- [将消息复制到 Azure 事件中心][3]

[1]: service-bus-federation-replicator-functions.md
[2]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopy
[3]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopyToEventHub