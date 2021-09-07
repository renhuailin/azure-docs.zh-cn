---
title: Azure 服务总线消息传送队列、主题和订阅
description: 本文概述了 Azure 服务总线消息实体（队列、主题和订阅）。
ms.topic: conceptual
ms.date: 08/27/2021
ms.openlocfilehash: 8756da7f4000f5ef3b478567934ba99a90b0ab4c
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123102262"
---
# <a name="service-bus-queues-topics-and-subscriptions"></a>服务总线队列、主题和订阅
Azure 服务总线支持一组基于云的、面向消息的中间件技术，包括可靠的消息队列和持久发布/订阅消息。 这些中转消息传送功能可被视为分离式消息传送功能，支持使用服务总线消息传送工作负载的发布-订阅、临时分离和负载均衡方案。 分离式通信具有很多优点。 例如，客户端和服务器可以根据需要进行连接并以异步方式执行其操作。

构成服务总线消息传送功能核心的消息传送实体包括队列、主题和订阅以及规则/操作 。

## <a name="queues"></a>队列
队列为一个或多个竞争使用方提供 **先入先出** (FIFO) 消息传递方式。 也就是说，接收方通常会按照消息添加到队列中的顺序来接收并处理消息。 并且每条消息仅由一个消息使用方接收并处理。 使用队列的主要优点是实现应用程序组件的临时分离。 换句话说，创建方（发送方）和使用方（接收方）不必同时发送和接收消息。 这是因为消息已持久存储在队列中。 此外，创建方不必等待使用方的答复即可继续处理并发送更多消息。

相关的优点是“负载分级”，它允许创建方和使用方以不同速率发送和接收消息。 在许多应用程序中，系统负载随时间而变化。 但是，每个工作单元所需的处理时间通常为常量。 使用队列在消息创建方与使用方之间中继意味着，消费应用程序只需能够处理平均负载而非峰值负载。 队列深度将随传入负载的变化而加大和减小。 此功能会直接根据为应用程序负载提供服务所需的基础结构的数目来节省资金。 随着负载增加，可添加更多的工作进程以从队列中读取。 每条消息仅由一个辅助进程处理。 另外，可通过基于拉取的该负载均衡，以最合理的方式使用辅助计算机，即使具有处理能力的辅助计算机以其最大速率拉取消息也是如此。 此模式通常称为 **使用者竞争** 模式。

使用队列在消息创建方与使用方之间中继可在各组件之间提供固有的松散耦合。 由于创建方和使用方互不相识，因此，可升级使用方，而不会对创建方产生任何影响。

### <a name="create-queues"></a>创建队列
可以使用 [Azure 门户](service-bus-quickstart-portal.md)、[PowerShell](service-bus-quickstart-powershell.md)、[CLI](service-bus-quickstart-cli.md) 或[资源管理器模板](service-bus-resource-manager-namespace-queue.md)创建队列。 然后，使用以 [C#](service-bus-dotnet-get-started-with-queues.md)、[Java](service-bus-java-how-to-use-queues.md)、[Python](service-bus-python-how-to-use-queues.md) 和 [JavaScript](service-bus-nodejs-how-to-use-queues.md) 编写的客户端发送和接收消息。 

### <a name="receive-modes"></a>接收模式
可以指定服务总线接收消息所用的两种不同模式。

- 接收并删除。 在此模式下，当服务总线收到来自使用者的请求时，它会将该消息标记为“正在使用”，并将其返回给使用者应用程序。 此模式是最简单的模型。 它最适合应用程序允许在出现故障时不处理消息的方案。 为了理解此方案，可以考虑这样一种情形：使用方发出接收请求，但在处理该请求前发生了崩溃。 由于服务总线会将消息标记为“正在使用”，应用程序会在重新启动后开始使用消息。 它会丢失在发生故障前使用的消息。
- 速览锁定。 在此模式下，接收操作分成了两步，从而有可能支持无法容忍遗漏消息的应用程序。 
    1. 查找要使用的下一条消息，将其锁定以防其他使用者接收它，然后，将该消息返回到应用程序。 
    1. 在应用程序处理完该消息后，它会请求服务总线服务完成接收过程的第二阶段。 然后，该服务会将消息标记为已使用。 

        如果应用程序出于某种原因无法处理该消息，它可以请求服务总线服务放弃 该消息。 服务总线会解锁该消息并使其能够再次被同一个使用者或其他竞争使用者接收。 其次，有一个与该锁定关联的超时。 如果应用程序无法在锁定超时期满前处理消息，服务总线会解锁消息，使其再次可供接收。

        如果应用程序在处理消息之后（但在请求服务总线服务完成该消息之前）崩溃，服务总线会在应用程序重启时将该消息重新传送到该应用程序。 此过程通常称为“至少一次”处理。 也就是说，每条消息至少处理一次。 但是，在某些情况下，同一消息可能会被重新传送。 如果方案不容许重复处理，请在应用程序中添加其他逻辑来检测重复项。 有关详细信息，请参阅[重复检测](duplicate-detection.md)。 此功能称为“仅一次”处理。

        > [!NOTE]
        > 有关这两种模式的详细信息，请参阅[安排接收操作](message-transfers-locks-settlement.md#settling-receive-operations)。

## <a name="topics-and-subscriptions"></a>主题和订阅
队列允许单个使用方处理消息。 与队列不同，主题和订阅以“发布和订阅”模式提供一对多的通信形式。 这对于扩展到大量接收方而言十分有用。 每个发布的消息均可用于向该主题注册的每个订阅。 发布方将消息发送到主题，一个或多个订阅服务器将接收该消息的副本，具体取决于对这些订阅设置的筛选规则。 此订阅可以使用其他筛选器来限制其想要接收的消息。 发布方将消息发送到主题的方式与将消息发送到队列的方式相同。 但使用方不会直接从主题接收消息。 相反，使用方从该主题的订阅接收消息。 主题订阅类似于接收发送至该主题的消息副本的虚拟队列。 使用方从订阅接收消息的方式与从队列接收消息的方式相同。

队列的消息发送功能直接映射到主题，而其消息接收功能映射到订阅。 另外，此功能意味着订阅支持本部分前面所述的有关队列的相同模式：竞争使用者、临时分离、负载调节和负载均衡。

### <a name="create-topics-and-subscriptions"></a>创建主题和订阅
创建主题与创建队列类似，如前一部分中所述。 可以使用 [Azure 门户](service-bus-quickstart-topics-subscriptions-portal.md)、[PowerShell](service-bus-quickstart-powershell.md)、[CLI](service-bus-tutorial-topics-subscriptions-cli.md) 或[资源管理器模板](service-bus-resource-manager-namespace-topic.md)创建主题和订阅。 然后，使用以 [C#](service-bus-dotnet-how-to-use-topics-subscriptions.md)、[Java](service-bus-java-how-to-use-topics-subscriptions.md)、[Python](service-bus-python-how-to-use-topics-subscriptions.md) 和 [JavaScript](service-bus-nodejs-how-to-use-topics-subscriptions.md) 编写的客户端将消息发送到主题并从订阅接收消息。 

### <a name="rules-and-actions"></a>规则和操作
在许多情况下，必须以不同方式处理具有特定特征的消息。 若要启用此处理，可配置订阅以找到具有所需属性的消息，并对这些属性执行某些修改。 虽然服务总线订阅可以看到发送到主题的所有消息，但你仅可以将这些消息的一个子集复制到虚拟订阅队列。 可使用订阅筛选器完成此筛选。 此类修改称为筛选器操作  。 创建订阅后，你可以提供对消息属性进行操作的筛选表达式。 这些属性可以是系统属性（例如“标签”），也可以是自定义应用程序属性（例如“StoreName” 。）SQL 筛选器表达式在此示例中为可选。 如果没有 SQL 筛选器表达式，会对该订阅的所有消息执行在订阅上定义的任何筛选器操作。

有关完整的工作示例，请参阅 GitHub 上的 [TopicFilters 示例](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples/TopicFilters)。

有关筛选器的详细信息，请参阅[主题筛选器和操作](topic-filters.md)。

## <a name="java-message-service-jms-20-entities"></a>Java 消息服务 (JMS) 2.0 实体
以下实体可通过 Java 消息服务 (JMS) 2.0 API 进行访问。

  * 临时队列
  * 临时主题
  * 共享持久订阅
  * 非共享持久订阅
  * 共享非持久订阅
  * 非共享非持久订阅

详细了解 [JMS 2.0 实体](java-message-service-20-entities.md)和如何[使用它们](how-to-use-java-message-service-20.md)。

## <a name="next-steps"></a>后续步骤

尝试采用所选语言的示例，了解 Azure 服务总线功能。 

- [适用于 .NET（最新）的 Azure 服务总线客户端库示例](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/)
- [适用于 Java（最新）的 Azure 服务总线客户端库示例](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [适用于 Python 的 Azure 服务总线客户端库示例](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [适用于 JavaScript 的 Azure 服务总线客户端库示例](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [适用于 TypeScript 的 Azure 服务总线客户端库示例](/samples/azure/azure-sdk-for-js/service-bus-typescript/)

在下面查找早期 .NET 和 Java 客户端库示例：
- [适用于 .NET（旧版）的 Azure 服务总线客户端库示例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)
- [适用于 Java（旧版）的 Azure 服务总线客户端库示例](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)