---
title: 使用 Apache Kafka 应用中的事件中心 - Azure 事件中心 | Microsoft Docs
description: 本文介绍有关 Azure 事件中心提供的 Apache Kafka 支持的信息。
ms.topic: article
ms.date: 09/25/2020
ms.openlocfilehash: 5402769b00a142551672098829dcf8f3ef6ea670
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122321774"
---
# <a name="use-azure-event-hubs-from-apache-kafka-applications"></a>使用 Apache Kafka 应用程序中的 Azure 事件中心
事件中心提供与 Apache Kafka® 生成者和使用者 API 兼容的终结点，大多数现有 Apache Kafka 客户端应用程序可以使用这些 API 作为运行你自己的 Apache Kafka 群集的替代方法。 事件中心 1.0 及更高版本支持 Apache Kafka 的生成者和使用者 API 客户端。


## <a name="what-does-event-hubs-for-kafka-provide"></a>适用于 Kafka 的事件中心可提供什么？

适用于 Apache Kafka 的事件中心功能在 Azure 事件中心的基础上提供了一个协议头，该协议与为 Apache Kafka 服务器 1.0 及更高版本生成的 Apache Kafka 客户端兼容，并支持从与 Apache Kafka 主题等效的事件中心进行读取以及向其中进行写入。 

你通常可以从应用程序中使用事件中心 Kafka 终结点，与现有的 Kafka 设置相比，无需更改代码，只需修改配置：更新配置中的连接字符串，使之指向事件中心公开的 Kafka 终结点，而不是指向 Kafka 群集。 然后，可以开始将使用 Kafka 协议的应用程序中的事件流式传输到事件中心。 

从概念上讲，Kafka 和事件中心非常类似：它们都是为流式处理数据生成的已分区日志，因此，客户端可以控制它要读取保留的日志的哪一部分。 下表映射 Kafka 和事件中心之间的概念。

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Kafka 和事件中心概念映射

| Kafka 概念 | 事件中心概念|
| --- | --- |
| 群集 | 命名空间 |
| 主题 | 事件中心 |
| 分区 | 分区|
| 使用者组 | 使用者组 |
| Offset | Offset|

### <a name="key-differences-between-apache-kafka-and-event-hubs"></a>Apache Kafka 和事件中心之间的主要区别

尽管 [Apache Kafka](https://kafka.apache.org/) 是你通常需要安装和操作的软件，但事件中心是一种完全托管的云原生服务。 没有需要管理和监视的服务器、磁盘或网络，也没有需要考虑或配置的中转站。 你需要创建一个命名空间（即具有完全限定的域名的终结点），然后在该命名空间中创建事件中心（主题）。 

有关事件中心和命名空间的详细信息，请参阅[事件中心功能](event-hubs-features.md#namespace)。 作为云服务，事件中心使用单一稳定的虚拟 IP 地址作为终结点，因此客户端无需了解群集中代理或计算机的情况。 尽管事件中心实现了同一协议，但此差异意味着所有分区的所有 Kafka 流量都可通过这一个终结点以可预测方式进行路由，而无需对群集的所有中转站进行防火墙访问。   

事件中心的规模由你购买的[吞吐量单位 (TU)](event-hubs-scalability.md#throughput-units) 或[处理单位](event-hubs-scalability.md#processing-units)的数量控制。 如果为标准层命名空间启用[自动扩充](event-hubs-auto-inflate.md)功能，则当你达到吞吐量限制时，事件中心会自动纵向扩展 TU。 此功能也适用于 Apache Kafka 协议支持。 对于高级层命名空间，可以增加分配给命名空间的处理单位的数量。 

### <a name="is-apache-kafka-the-right-solution-for-your-workload"></a>Apache Kafka 是适合你的工作负载的解决方案吗？

Apache Kafka 源自于使用 Apache Kafka 构建应用程序，也有助于理解 Azure 事件中心是一系列服务的一部分，这些服务还包括 [Azure 服务总线](../service-bus-messaging/service-bus-messaging-overview.md)和 [Azure 事件网格](../event-grid/overview.md)。 

虽然某些 Apache Kafka 的商业分发提供商可能会指出 Apache Kafka 是所有消息平台需求的一站式商店，但事实是 Apache Kafka 还没有实现，例如，[竞争使用者](/azure/architecture/patterns/competing-consumers)队列模式在某个级别不支持[发布-订阅](/azure/architecture/patterns/publisher-subscriber)，而“发布-订阅”允许订阅者根据服务器评估的规则（而不是纯偏移量）访问传入消息，并且没有任何工具可以跟踪消息启动的作业的生命周期，或将错误消息 sideline（暂存）到死信队列中，这些都是许多企业消息传递方案的基础功能。

要了解模式之间的差异以及哪种模式最适合哪种服务，请查看 [Azure 中的异步消息传递选项](/azure/architecture/guide/technology-choices/messaging)指南。 作为 Apache Kafka 的用户，你可能会发现，利用事件网格或服务总线，不仅可以实现目前通过 Kafka 能够实现通信路径，而且基本复杂性降低，功能更强大。 

如果需要 Apache Kafka 的特定功能，而 Apache Kafka 接口的事件中心未提供这些功能，或者如果你的实施模式超过了[事件中心配额](event-hubs-quotas.md)，则还可以运行 [Azure HDInsight 中的本机 Apache Kafka 群集](../hdinsight/kafka/apache-kafka-introduction.md)。  

## <a name="security-and-authentication"></a>安全性和身份验证
每次你发布或使用来自用于 Kafka 的事件中心的事件时，客户端都会尝试访问事件中心资源。 你希望确保使用已授权的实体来访问资源。 在客户端上使用 Apache Kafka 协议时，可以使用 SASL 机制设置用于身份验证和加密的配置。 使用用于 Kafka 的事件中心时，需要进行 TLS 加密（因为使用事件中心传输的所有数据均经过 TLS 加密）。 可以在配置文件中指定 SASL_SSL 选项来完成此设置。 

Azure 事件中心提供了多个选项来授予对安全资源的访问权限。 

- OAuth 2.0
- 共享访问签名 (SAS)

#### <a name="oauth-20"></a>OAuth 2.0
事件中心会与 Azure Active Directory (Azure AD) 集成，后者提供了与 OAuth 2.0 兼容的集中式授权服务器。 使用 Azure AD，你可以通过 Azure 基于角色的访问控制 (Azure RBAC) 向客户端标识授予细粒度权限。 可以指定“SASL_SSL”作为协议，并指定“OAUTHBEARER”作为机制，通过这种方式将此功能用于 Kafka 客户端。 有关 Azure 角色和范围访问级别的详细信息，请参阅[使用 Azure AD 授予访问权限](authorize-access-azure-active-directory.md)。

```properties
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=OAUTHBEARER
sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler
```

#### <a name="shared-access-signature-sas"></a>共享访问签名 (SAS)
事件中心还提供了共享访问签名 (SAS)，方便你对用于 Kafka 的事件中心资源进行委派访问。 与 SAS 相比，使用 OAuth 2.0 基于令牌的机制授予访问权限具有更好的安全性和易用性。 内置角色还可以消除基于 ACL 的授权（用户必须对其进行维护和管理）的需要。 可以指定“SASL_SSL”作为协议，并指定“PLAIN”作为机制，通过这种方式将此功能用于 Kafka 客户端。 

```properties
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

> [!IMPORTANT]
> 将 `{YOUR.EVENTHUBS.CONNECTION.STRING}` 替换为事件中心命名空间的连接字符串。 有关获取连接字符串的说明，请参阅[获取事件中心连接字符串](event-hubs-get-connection-string.md)。 下面是一个配置示例：`sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`

> [!NOTE]
> 对 Kafka 客户端使用 SAS 身份验证时，在重新生成 SAS 密钥时，已建立的连接不会断开。 


#### <a name="samples"></a>示例 
有关创建事件中心并使用 SAS 或 OAuth 对其进行访问的分步说明教程，请参阅[快速入门：使用 Kafka 协议通过事件中心进行数据流式传输](event-hubs-quickstart-kafka-enabled-event-hubs.md)。

如需更多示例来演示如何在用于 Kafka 的事件中心使用 OAuth，请参阅 [GitHub 上的示例](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth)。

## <a name="other-event-hubs-features"></a>其他事件中心功能 

适用于 Apache Kafka 的事件中心功能是在 Azure 事件中心并发提供的三个协议之一，用于补充 HTTP 和 AMQP。 你可以使用这些协议中的任何一种进行写入，也可以使用任何一种进行读取，以便你当前的 Apache Kafka 生成者可以继续通过 Apache Kafka 进行发布，但是你的读取者可以受益于与事件中心的 AMQP 接口的原生集成，例如 Azure 流分析或 Azure Functions。 反过来，你可以轻松地将 Azure 事件中心作为目标终结点集成到 AMQP 路由网络中，但仍通过 Apache Kafka 集成读取数据。  

此外，事件中心功能（例如[捕获](event-hubs-capture-overview.md)）可通过 Azure Blob 存储和 Azure Data Lake Storage 实现极具成本效益的长期存档，并且[异地灾难恢复](event-hubs-geo-dr.md)也与适用于 Kafka 的事件中心功能一起使用。

## <a name="apache-kafka-feature-differences"></a>Apache Kafka 功能差异 

适用于 Kafka 的事件中心的目标是为锁定到 Apache Kafka API 的应用程序提供对 Azure 事件中心功能的访问权限，否则必须由 Apache Kafka 群集提供支持。 

如[上面](#is-apache-kafka-the-right-solution-for-your-workload)所述，Azure 消息传递团队为许多消息传递场景提供了丰富而强大的覆盖范围。尽管当前无法借助事件中心对 Apache Kafka API 的支持来支持以下功能，但我们指出了在何处以何种方式获得所需的功能。

### <a name="transactions"></a>事务

[Azure 服务总线](../service-bus-messaging/service-bus-transactions.md)具有强大的事务支持，该事务支持允许在事务的一致性保护下接收并处理消息和会话，同时将消息处理过程中产生的出站消息发送到多个目标实体。 此功能集不仅允许对序列中的每条消息进行恰好一次处理，而且还避免了其他使用者无意中重新处理相同消息的风险，就像 Apache Kafka 的情况一样。 对于事务性消息工作负荷，建议使用服务总线服务。

### <a name="compression"></a>压缩

Apache Kafka 的客户端[压缩](https://cwiki.apache.org/confluence/display/KAFKA/Compression)功能可将一批（多条）消息在生成者端压缩为单条消息，然后在使用者端解压该批。 Apache Kafka 中转站将批视为一条特殊消息。

此功能与 Azure 事件中心的多协议模型存在根本的不同，多协议模型允许从中转站通过任何协议单独检索消息（甚至那些成批发送的消息）。 

任何事件中心事件的有效负载都是一个字节流，内容可以使用你选择的算法进行压缩。 Apache Avro 编码格式本身支持压缩。

### <a name="log-compaction"></a>日志压缩

Apache-Kafka 日志压缩是如下所述的一项功能：它允许从分区中逐出每个键除了最后一条记录之外的所有记录，这实际上会将 Apache Kafka 主题转换为一个键-值存储，其中最后添加的值将替代前一个。 Azure 事件中心目前未实现此功能。 键-值存储模式，即使频繁进行更新，在由 [Azure Cosmos DB](../cosmos-db/introduction.md) 之类的数据库服务提供支持时也比其他支持方式要好得多。 有关更多详细信息，请参阅事件中心联合指南中的[日志投影](event-hubs-federation-overview.md#log-projections)主题。 

### <a name="kafka-streams"></a>Kafka Stream

Kafka Streams 是用于流分析的一个客户端库，它是 Apache Kafka 开源项目的一部分，但独立于 Apache Kafka 事件流中转站。 

Azure 事件中心客户要求提供 Kafka Streams 支持的最常见原因是，他们对 Confluent 的“ksqlDB”产品感兴趣。 “ksqlDB”是一个具有专利的共享源项目，它的[许可要求](https://github.com/confluentinc/ksql/blob/master/LICENSE)如下：任何“提供软件即服务、平台即服务、基础设施即服务或其他与 Confluent 产品或服务竞争的类似在线服务”的供应商都不允许使用或提供“ksqlDB”支持。 实际上，如果你使用 ksqlDB，你必须亲自运营 Kafka，或者必须使用 Confluent 的云产品/服务。 许可条款还可能会影响为许可证排除的用途提供服务的 Azure 客户。

Kafka Streams 独立且没有 ksqlDB，其功能与许多替代框架和服务相比较少，它们的大多数都具有内置的流式处理 SQL 接口，目前全部与 Azure 事件中心集成：

- [Azure 流分析](../stream-analytics/stream-analytics-introduction.md)
- [Azure Synapse Analytics（使用事件中心捕获）](../event-grid/event-grid-event-hubs-integration.md)
- [Azure Databricks](/azure/databricks/scenarios/databricks-stream-from-eventhubs)
- [Apache Samza](https://samza.apache.org/learn/documentation/latest/connectors/eventhubs)
- [Apache Storm](event-hubs-storm-getstarted-receive.md)
- Apache Spark
- [Apache Flink](event-hubs-kafka-flink-tutorial.md)
- [Akka Streams](event-hubs-kafka-akka-streams-tutorial.md)

列出的服务和框架通常可以通过适配器直接从不同的源集中获取事件流和参考数据。 Kafka Streams 只能从 Apache Kafka 获取数据，因此你的分析项目会被锁定到 Apache Kafka 中。 若要使用来自其他源的数据，需要先使用 Kafka Connect 框架将数据导入到 Apache Kafka 中。

如果你必须使用 Azure 上的 Kafka Streams 框架，[Apache Kafka on HDInsight](../hdinsight/kafka/apache-kafka-introduction.md) 会提供该选项。 Apache Kafka on HDInsight 提供对 Apache Kafka 的所有配置方面的完全控制，同时与 Azure 平台的各个方面（从故障/更新域放置到网络隔离再到监视集成）完全集成。 

## <a name="next-steps"></a>后续步骤
本文介绍了适用于 Kafka 的事件中心。 若要了解详细信息，请参阅[针对 Azure 事件中心的 Apache Kafka 开发人员指南](apache-kafka-developer-guide.md)。
