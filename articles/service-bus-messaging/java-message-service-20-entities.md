---
title: Azure 服务总线消息传送-Java 消息服务实体
description: 本文概述了可通过 Java 消息服务 API 访问的 Azure 服务总线消息传递实体。
ms.topic: article
ms.date: 07/20/2020
ms.openlocfilehash: ee4e0124dced16b86d5292c647e129aa87645f22
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652575"
---
# <a name="java-message-service-jms-20-entities"></a>Java 消息服务 (JMS) 2.0 实体

连接到 Azure 服务总线高级版和使用 [Azure 服务总线 JMS 库](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms) 的客户端应用程序可以使用以下实体。

## <a name="queues"></a>队列

JMS 中的队列在语义上与传统的[服务总线队列](service-bus-queues-topics-subscriptions.md#queues)类似。

若要创建队列，请在类中使用以下方法 `JMSContext` -

```java
Queue createQueue(String queueName)
```

## <a name="topics"></a>主题

JMS 中的主题在语义上与传统的[服务总线主题](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions)类似。

若要创建主题，请在类中使用以下方法 `JMSContext` -

```java
Topic createTopic(String topicName)
```

## <a name="temporary-queues"></a>临时队列

如果客户端应用程序需要在应用程序的生存期内存在的临时实体，则可以使用临时队列。 这些实体用于 [请求-答复](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html) 模式。

若要创建临时队列，请在类中使用以下方法 `JMSContext` -

```java
TemporaryQueue createTemporaryQueue()
```

## <a name="temporary-topics"></a>临时主题

就像临时队列一样，可以使用临时主题通过在应用程序生存期内存在的临时实体来启用发布/订阅。

若要创建临时主题，请在类中使用以下方法 `JMSContext` -

```java
TemporaryTopic createTemporaryTopic()
```

## <a name="java-message-service-jms-subscriptions"></a>Java 消息服务 (JMS) 订阅

尽管它们在语义上类似于 [订阅](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) (即，存在于主题上并启用发布/订阅语义) ，但 Java 消息服务规范介绍了给定订阅的 **共享**、非 **共享**、* * 持久性和 **非持久** 属性的概念。

> [!NOTE]
> 以下订阅适用于使用 [Azure 服务总线 JMS 库](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms)连接到 Azure 服务总线的客户端应用程序的 Azure 服务总线高级层。
>
> 只有持久订阅才能使用 Azure 门户创建。
>

### <a name="shared-durable-subscriptions"></a>共享持久订阅

当某个主题上发布的所有消息都要由某个应用程序接收并处理时，可使用共享持久订阅，无论该应用程序是否任何时候都主动使用该订阅中的消息。

从服务总线接收身份验证的任何应用程序都可以从共享持久订阅接收。

若要创建共享持久订阅，请使用 `JMSContext` 类中的以下方法：

```java
JMSConsumer createSharedDurableConsumer(Topic topic, String name)

JMSConsumer createSharedDurableConsumer(Topic topic, String name, String messageSelector)
```

除非使用 `JMSContext` 类上的 `unsubscribe` 方法删除了共享持久订阅，否则该订阅将一直存在。

```java
void unsubscribe(String name)
```

### <a name="unshared-durable-subscriptions"></a>非共享持久订阅

与共享持久订阅一样，当某个主题上发布的所有消息都要由应用程序接收并处理时，将使用非共享持久订阅，而不管应用程序是否正在积极地从订阅中使用。

不过，由于这是一个非共享的订阅，因此只有创建该订阅的应用程序才能从其中接收消息。

若要创建非共享持久订阅，请使用 `JMSContext` 类中的以下方法： 

```java
JMSConsumer createDurableConsumer(Topic topic, String name)

JMSConsumer createDurableConsumer(Topic topic, String name, String messageSelector, boolean noLocal)
```

> [!NOTE]
> `noLocal` 功能当前不受支持，将被忽略。
>

除非使用 `JMSContext` 类上的 `unsubscribe` 方法删除了非共享持久订阅，否则该订阅将一直存在。

```java
void unsubscribe(String name)
```

### <a name="shared-non-durable-subscriptions"></a>共享非持久订阅

当多个客户端应用程序需要接收和处理来自单个订阅的消息时，会使用共享非持久订阅，直到它们主动使用/接收其中的消息为止。

由于订阅不是持久的，因此不会持久保存。 在没有主动使用者的情况下，此订阅不会接收消息。

若要创建共享非持久订阅，请创建一个 `JmsConsumer`，如 `JMSContext` 类中的以下方法中所示：

```java
JMSConsumer createSharedConsumer(Topic topic, String sharedSubscriptionName)

JMSConsumer createSharedConsumer(Topic topic, String sharedSubscriptionName, String messageSelector)
```

只要有主动使用者从中接收消息，共享非持久订阅就会一直存在。

### <a name="unshared-non-durable-subscriptions"></a>非共享非持久订阅

当客户端应用程序需要接收和处理来自订阅的消息时，会使用非共享非持久订阅，直到它主动使用其中的消息为止。 此订阅上只能有一个使用者，即创建了该订阅的客户端。

由于订阅不是持久的，因此不会持久保存。 在没有主动使用者的情况下，此订阅不会接收消息。

若要创建非共享非持久订阅，请创建一个 `JMSConsumer`，如 `JMSContext` 类中的以下方法中所示：

```java
JMSConsumer createConsumer(Destination destination)

JMSConsumer createConsumer(Destination destination, String messageSelector)

JMSConsumer createConsumer(Destination destination, String messageSelector, boolean noLocal)
```

> [!NOTE]
> `noLocal` 功能当前不受支持，将被忽略。
>

只要有主动使用者从中接收消息，非共享非持久订阅就会一直存在。

### <a name="message-selectors"></a>消息选择器

正如常规服务总线订阅存在 **筛选器和操作** 一样，JMS 订阅存在 **消息选择器**。

消息选择器可在每个 JMS 订阅上设置，并作为消息标头属性的筛选条件存在。 只会传递其标头属性与消息选择器表达式匹配的消息。 如果值为 null 或空字符串，则表示不存在与 JMS 订阅/使用者对应的消息选择器。

## <a name="additional-concepts-for-java-message-service-jms-20-subscriptions"></a>Java 消息服务 (JMS) 2.0 订阅的其他概念

### <a name="client-scoping"></a>客户端范围

在 Java 消息服务 (JMS) 2.0 API 中指定的订阅，不一定会 *作用于特定的客户端应用程序/* (，用适当的 `clientId`) 标识。

一旦对订阅进行了限定，就只能从具有相同客户端 id 的客户端应用程序 **访问** 它。 

尝试访问特定客户端 id 范围内的订阅的任何访问权限 () 说到 clientId2) 的应用程序中具有另一个客户端 (ID 的应用程序将导致创建另一个作用域为其他客户端 ID (clientId2) 的订阅。

> [!NOTE]
> 客户端 ID 可以为 null 或为空，但它必须与 JMS 客户端应用程序上设置的客户端 ID 匹配。 从 Azure 服务总线角度来看，空的客户端 ID 和空的客户端 id 具有相同的行为。
>
> 如果客户端 ID 设置为 null 或为空，则仅客户端应用程序的客户端应用程序也被设置为 null 或空。
>

### <a name="shareability"></a>可共享性

**共享** 订阅允许多个客户端/使用者 (也就是说，JMSConsumer 对象) 从它们接收消息。

>[!NOTE]
> 作用域为特定客户端 ID 的共享订阅仍可由多个客户端/使用者访问 (例如 JMSConsumer 对象) ，但是每个客户端应用程序必须具有相同的客户端 ID。
>
 

**非共享** 订阅只允许使用单个客户端/使用者 (也就是说，) 接收消息的 JMSConsumer 对象。 如果 `JMSConsumer` 在非共享订阅上创建了，而该订阅已具有活动的 `JMSConsumer` 侦听消息，则 `JMSException` 会引发。


### <a name="durability"></a>持续性

即使应用程序 () 使用消息，**持久** 订阅仍可保留并继续从主题收集消息 `JMSConsumer` 。

无论应用程序 () 使用消息，**非持久** 订阅都不会持久保存并从主题收集消息 `JMSConsumer` 。 

## <a name="representation-of-client-scoped-subscriptions"></a>客户端范围的订阅的表示形式

假定客户端范围 (JMS) 订阅必须与现有的 [订阅](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions)共存，则会用以下格式表示客户端范围 (JMS) 订阅的方式。

   * **\<SUBSCRIPTION-NAME\>**$**\<CLIENT-ID\>**$**D** (持久订阅) 
   * **\<SUBSCRIPTION-NAME\>**$**\<CLIENT-ID\>**$非持久订阅的 **ND** () 

此处 **$** 为分隔符。

## <a name="next-steps"></a>后续步骤

有关使用服务总线消息传送的详细信息和示例，请参阅以下高级主题：

* [服务总线消息传送概述](service-bus-messaging-overview.md)
* [将 Java 消息服务 2.0 API 与 Azure 服务总线高级版配合使用](how-to-use-java-message-service-20.md)



