---
title: Azure 服务总线消息传送队列、主题和订阅
description: 本文概述了 Azure 服务总线消息实体（队列、主题和订阅）。
ms.topic: article
ms.date: 11/04/2020
ms.openlocfilehash: 54b6a1fd2d4e8e5ef5bb6522374646257213e4b4
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2020
ms.locfileid: "95791607"
---
# <a name="service-bus-queues-topics-and-subscriptions"></a>服务总线队列、主题和订阅
Azure 服务总线支持一组基于云的、面向消息的中间件技术，包括可靠的消息队列和持久发布/订阅消息。 这些中转消息传送功能可被视为分离式消息传送功能，支持使用服务总线消息传送工作负载的发布-订阅、临时分离和负载均衡方案。 分离式通信具有很多优点。 例如，客户端和服务器可以根据需要进行连接并以异步方式执行操作。

在服务总线中构成消息传送功能核心的消息传送实体是 **队列**、 **主题和订阅** 以及规则/操作。

## <a name="queues"></a>队列
队列为一个或多个竞争使用方提供 **先入先出** (FIFO) 消息传递方式。 也就是说，接收方通常会按照消息添加到队列中的顺序来接收和处理消息。 而且，只有一个消息使用者接收和处理每条消息。 使用队列的主要优点是实现 **应用程序组件的暂时分离**。 换句话说， (发送方) 和使用者 (接收方) 不必同时发送和接收消息。 这是因为消息已持久存储在队列中。 此外，制造者不必等待使用方的答复即可继续处理和发送消息。

相关的优点是 **负载分级**，它允许创建方和使用方以不同速率发送和接收消息。 在许多应用程序中，系统负载随时间而变化。 但是，每个工作单元所需的处理时间通常为常量。 之间中继意味着消息生成者和使用队列的使用者意味着使用的应用程序只需能够处理平均负载而非峰值负载。 队列深度将随传入负载的变化而加大和减小。 此功能会直接根据为应用程序加载提供服务所需的基础结构的数目来节省成本。 随着负载增加，可添加更多的工作进程以从队列中读取。 每条消息仅由一个辅助进程处理。 此外，这种基于拉取的负载平衡还可以最大程度地使用辅助计算机，即使使用其最大速率处理能力请求消息的辅助计算机也是如此。 此模式通常称为 **使用者竞争** 模式。

使用队列在消息创建方与使用方之间中继可在各组件之间提供固有的松散耦合。 由于制造者和使用者彼此之间并不彼此了解，因此可以升级使用者，而不会对生成者产生任何影响。

### <a name="create-queues"></a>创建队列
可以使用 [Azure 门户](service-bus-quickstart-portal.md)、 [PowerShell](service-bus-quickstart-powershell.md)、 [CLI](service-bus-quickstart-cli.md)或 [资源管理器模板](service-bus-resource-manager-namespace-queue.md)来创建队列。 然后，使用以 [c #](service-bus-dotnet-get-started-with-queues.md)、 [Java](service-bus-java-how-to-use-queues.md)、 [Python](service-bus-python-how-to-use-queues.md)、 [JavaScript](service-bus-nodejs-how-to-use-queues.md)、 [PHP](service-bus-php-how-to-use-queues.md)和 [Ruby](service-bus-ruby-how-to-use-queues.md)编写的客户端发送和接收消息。 

### <a name="receive-modes"></a>接收模式
可以指定服务总线接收消息所用的两种不同模式：ReceiveAndDelete  或 PeekLock  。 在 [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) 模式下，当服务总线接收来自使用者的请求时，它会将该消息标记为 "已使用" 并将其返回给使用者应用程序。 此模式是最简单的模型。 它最适合在发生故障时应用程序可以容忍不处理消息的情况。 为了理解此方案，可以考虑这样一种情形：使用方发出接收请求，但在处理该请求前发生了崩溃。 由于服务总线会将消息标记为 "已使用"，因此，应用程序会在重新启动时开始使用消息。 它会丢失在发生崩溃前使用的消息。

在 [PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode) 模式下，接收操作将变成两阶段，这使得能够支持无法容忍遗漏消息的应用程序。 当服务总线收到请求时，它将执行以下操作：

1. 查找要使用的下一条消息。
1. 将其锁定以防止其他使用者接收它。
1. 然后，将该消息返回到应用程序。 

在应用程序处理完消息或将其可靠地存储起来以供将来处理后，它将通过对消息调用来完成接收过程的第二个阶段 [`CompleteAsync`](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) 。 当 Service Bus 接收 **CompleteAsync** 请求时，它会将消息标记为 "已使用"。

如果应用程序出于某种原因无法处理消息，它可以对 [`AbandonAsync`](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) 消息调用方法 (而不是 [`CompleteAsync`](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync)) 。 此方法可使服务总线解锁消息并使其能够重新被同一个使用方或其他竞争使用方接收。 其次，存在与锁定关联的超时。 如果应用程序无法在锁定超时到期之前处理消息，服务总线将解锁该消息并使其可再次被接收。

如果应用程序在处理消息之后，但在调用之前发生崩溃 [`CompleteAsync`](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) ，则在应用程序重新启动时，服务总线会将该消息 redelivers 到应用程序。 此过程通常称为 **至少一次** 处理。 也就是说，每条消息至少被处理一次。 但是，在某些情况下，同一消息可能会被重新传送。 如果方案无法容忍重复处理，请在应用程序中添加其他逻辑来检测重复项。 可以通过使用消息的 [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) 属性来实现此目的，该属性在多次传送尝试中保持不变。 此功能被称为 **只是一次** 处理。

## <a name="topics-and-subscriptions"></a>主题和订阅
队列允许单个使用者对消息进行处理。 与队列不同，主题和订阅在 **发布和订阅** 模式下提供了一对多的通信形式。 这对于扩展到大量收件人很有用。 每个已发布的消息都可用于向该主题注册的每个订阅。 发布者向主题发送一条消息，并且一个或多个订阅服务器将接收该消息的副本，具体取决于对这些订阅设置的筛选规则。 此订阅可以使用其他筛选器来限制其想要接收的消息。 发布者将消息发送到主题的方式与将消息发送到队列的方式相同。 但使用者不会直接从主题接收消息。 相反，使用者从该主题的订阅接收消息。 主题订阅类似于接收发送至该主题的消息副本的虚拟队列。 使用者从订阅接收消息的方式与从队列接收消息的方式相同。

队列的消息发送功能直接映射到主题，而其消息接收功能映射到订阅。 此外，此功能意味着订阅支持本部分中前面有关队列所述的相同模式：竞争使用者、临时分离、负荷量和负载均衡。

### <a name="create-topics-and-subscriptions"></a>创建主题和订阅
创建主题与创建队列类似，如前一部分中所述。 你可以使用 [Azure 门户](service-bus-quickstart-topics-subscriptions-portal.md)、 [PowerShell](service-bus-quickstart-powershell.md)、 [CLI](service-bus-tutorial-topics-subscriptions-cli.md)或 [资源管理器模板](service-bus-resource-manager-namespace-topic.md)创建主题和订阅。 然后，使用以 [c #](service-bus-dotnet-how-to-use-topics-subscriptions.md)、 [Java](service-bus-java-how-to-use-topics-subscriptions.md)、 [Python](service-bus-python-how-to-use-topics-subscriptions.md)、 [JavaScript](service-bus-nodejs-how-to-use-topics-subscriptions.md)、 [PHP](service-bus-php-how-to-use-topics-subscriptions.md)和 [Ruby](service-bus-ruby-how-to-use-topics-subscriptions.md)编写的客户端将消息发送到主题并接收来自订阅的消息。 

### <a name="rules-and-actions"></a>规则和操作
在许多情况下，必须以不同方式处理具有特定特征的消息。 若要启用此处理，可配置订阅以找到具有所需属性的消息，并对这些属性执行某些修改。 虽然服务总线订阅可以看到发送到主题的所有消息，但你仅可以将这些消息的一个子集复制到虚拟订阅队列。 可使用订阅筛选器完成此筛选。 此类修改称为筛选器操作  。 创建订阅后，您可以提供一个对消息属性进行操作的筛选器表达式。 这些属性可以是系统属性 (例如， **标签**) 和自定义应用程序属性 (例如 ) ，在这种 **情况下，** SQL 筛选器表达式是可选的。 如果没有 SQL 筛选器表达式，则在订阅上定义的任何筛选器操作都将对该订阅的所有消息执行。

有关完整的工作示例，请参阅GitHub上的 [TopicSubscriptionWithRuleOperationsSample 示例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/TopicSubscriptionWithRuleOperationsSample)。

有关可能的筛选器值的详细信息，请参阅文档 [SqlFilter](/dotnet/api/microsoft.azure.servicebus.sqlfilter) 和 [SqlRuleAction](/dotnet/api/microsoft.azure.servicebus.sqlruleaction) 类。

## <a name="java-message-service-jms-20-entities-preview"></a>Java 消息实体 (JMS) 2.0 实体（预览版）
可以通过 Java 消息服务 (JMS) 2.0 API 访问以下实体。

  * 临时队列
  * 临时主题
  * 共享持久订阅
  * 非共享持久订阅
  * 共享非持久订阅
  * 非共享非持久订阅

了解有关 [JMS 2.0 实体](java-message-service-20-entities.md) 以及如何 [使用它们](how-to-use-java-message-service-20.md)的详细信息。

## <a name="next-steps"></a>后续步骤

有关使用服务总线消息传送的详细信息和示例，请参阅以下高级主题：

* [服务总线消息传送概述](service-bus-messaging-overview.md)
* [快速入门：使用 Azure 门户和 .NET 发送和接收消息](service-bus-quickstart-portal.md)
* [教程：使用 Azure 门户和主题/订阅更新清单](service-bus-tutorial-topics-subscriptions-portal.md)


