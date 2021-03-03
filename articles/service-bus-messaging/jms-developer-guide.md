---
title: Azure 服务总线 JMS 2.0 开发人员指南
description: 如何使用 Java 消息服务 (JMS) 2.0 API 与 Azure 服务总线通信
ms.topic: article
ms.date: 01/17/2021
ms.openlocfilehash: 492da46a5ce4f5955a72972d91a8c56d54c385bb
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101726948"
---
# <a name="azure-service-bus-jms-20-developer-guide"></a>Azure 服务总线 JMS 2.0 开发人员指南

本指南包含的详细信息可帮助你成功使用 Java 消息服务 (JMS) 2.0 API 与 Azure 服务总线进行通信。

作为 Java 开发人员，如果不熟悉 Azure 服务总线，请参阅以下文章。

| 入门 | 概念 |
|----------------|-------|
| <ul> <li> [什么是 Azure 服务总线](service-bus-messaging-overview.md) </li> <li> [队列、主题和订阅](service-bus-queues-topics-subscriptions.md) </li> </ul> | <ul> <li> [Azure 服务总线-高级层](service-bus-premium-messaging.md) </li> </ul> |

## <a name="java-message-service-jms-programming-model"></a>Java 消息服务 (JMS) 编程模型

Java 消息服务 API 编程模型如下所示- 

> [!NOTE]
>
>**Azure 服务总线高级层** 支持 jms 1.1 和 jms 2.0。
> <br> <br>
> **Azure 服务总线-标准** 层支持有限的 JMS 1.1 功能。 有关更多详细信息，请参阅此 [文档](service-bus-java-how-to-use-jms-api-amqp.md)。
>

# <a name="jms-20-programming-model"></a>[JMS 2.0 编程模型](#tab/JMS-20)

:::image type="content" source="./media/jms-developer-guide/java-message-service-20-programming-model.png "alt-text="显示 JMS 2.0 编程模型的关系图。" border="false":::

# <a name="jms-11-programming-model"></a>[JMS 1.1 编程模型](#tab/JMS-11)

:::image type="content" source="./media/jms-developer-guide/java-message-service-11-programming-model.png "alt-text="显示 JMS 1.1 编程模型的关系图。" border="false":::

---

## <a name="jms---building-blocks"></a>JMS 构建基块

以下构建基块可用于与 JMS 应用程序通信。

> [!NOTE]
> 以下指南适用于适用于 [Java 消息服务 (JMS 的 Oracle JAVA EE 6 教程) ](https://docs.oracle.com/javaee/6/tutorial/doc/bnceh.html)
>
> 建议参考本教程，以便更好地了解 (JMS) 的 Java 消息服务。
>

### <a name="connection-factory"></a>连接工厂
客户端使用连接工厂对象与 JMS 提供程序进行连接。 连接工厂封装由管理员定义的一组连接配置参数。

每个连接工厂是 `ConnectionFactory` `QueueConnectionFactory` 或接口的实例 `TopicConnectionFactory` 。

为了简化与 Azure 服务总线的连接，这些接口是通过 `ServiceBusJmsConnectionFactory` 和分别实现的 `ServiceBusJmsQueueConnectionFactory` `ServiceBusJmsTopicConnectionFactory` 。 可以用以下参数实例化连接工厂- 
   * 连接字符串-Azure 服务总线高级层命名空间的连接字符串。
   * ServiceBusJmsConnectionFactorySettings 属性包，其中包含
      * connectionIdleTimeoutMS-空闲连接超时（以毫秒为单位）。
      * traceFrames-用于收集用于调试的 AMQP 跟踪帧的布尔型标志。
      * *其他配置参数*

可以按如下所示创建工厂。 连接字符串是必需参数，但其他属性是可选的。

```java
ConnectionFactory factory = new ServiceBusJmsConnectionFactory(SERVICE_BUS_CONNECTION_STRING, null);
```

> [!IMPORTANT]
> 利用 JMS 2.0 API 的 Java 应用程序必须仅使用连接字符串连接到 Azure 服务总线。 目前，仅支持使用连接字符串对 JMS 客户端进行身份验证。
>
> 当前不支持 Azure active directory (AAD) 支持的身份验证。
>

### <a name="jms-destination"></a>JMS 目标

"目标" 是客户端用来指定其生成的消息的目标以及它所使用的消息源的对象。

目标映射到 Azure 服务总线中的实体-队列 (点到点方案中) 和主题 (在发布-子方案) 中。

### <a name="connections"></a>连接

连接使用 JMS 提供程序封装虚拟连接。 使用 Azure 服务总线，这表示应用程序与 Azure 服务总线通过 AMQP 之间有状态连接。

从连接工厂创建连接，如下所示。

```java
Connection connection = factory.createConnection();
```

### <a name="sessions"></a>会话

会话是用于生成和使用消息的单线程上下文。 它可以用来创建消息、消息生成者和使用者，但它还提供事务上下文以允许将发送和接收分组到原子工作单元中。

可以通过连接对象创建会话，如下所示。

```java
Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
```

#### <a name="session-modes"></a>会话模式

可以使用以下任一模式创建会话。

| 会话模式 | 行为 |
| ----------------- | -------- |
|**Session.AUTO_ACKNOWLEDGE** | 当会话从接收的调用成功返回时，或当会话已调用以成功返回处理消息的消息侦听器时，会话会自动确认客户端的消息的接收方。|
|**Session.CLIENT_ACKNOWLEDGE** |客户端通过调用消息的确认方法来确认已使用的消息。|
|**Session.DUPS_OK_ACKNOWLEDGE**|此确认模式指示会话延迟确认消息送达。| 
|**Session.SESSION_TRANSACTED**|此值可以作为参数传递给 Connection 对象上的方法 createSession (int sessionMode) ，以指定会话应使用本地事务。| 

如果未指定会话模式，则默认情况下选取 **Session.AUTO_ACKNOWLEDGE** 。

### <a name="jmscontext"></a>JMSContext

> [!NOTE]
> JMSContext 定义为 JMS 2.0 规范的一部分。
>

JMSContext 结合连接和会话对象提供的功能。 可以从连接工厂对象创建它。

```java
JMSContext context = connectionFactory.createContext();
```

#### <a name="jmscontext-modes"></a>JMSContext 模式

与 **session** 对象一样，可以用 [会话模式](#session-modes)中提到的相同确认模式来创建 JMSContext。

```java
JMSContext context = connectionFactory.createContext(JMSContext.AUTO_ACKNOWLEDGE);
```

如果未指定模式，则默认情况下选取 **JMSContext.AUTO_ACKNOWLEDGE** 。

### <a name="jms-message-producers"></a>JMS 消息创建者

消息创建者是使用 JMSContext 或会话创建的对象，用于将消息发送到目标。

可以将其创建为独立的对象，如下所示： 

```java
JMSProducer producer = context.createProducer();
```

需要发送消息时，或在运行时创建。

```java
context.createProducer().send(destination, message);
```

### <a name="jms-message-consumers"></a>JMS 消息使用者

消息使用方是由 JMSContext 或会话创建的对象，用于接收发送到目标的消息。 可按如下所示创建它-

```java
JMSConsumer consumer = context.createConsumer(dest);
```

#### <a name="synchronous-receives-via-receive-method"></a>通过接收 () 方法进行同步接收

消息使用者提供一种同步方法，通过方法从目标接收消息 `receive()` 。

如果未指定参数/超时，或指定了超时值 "0"，则使用者会无限期阻止，除非消息到达，否则连接会断开 (前面) 。

```java
Message m = consumer.receive();
Message m = consumer.receive(0);
```

如果提供了一个非零的正自变量，则使用者将被阻止，直到该计时器过期。

```java
Message m = consumer.receive(1000); // time out after one second.
```

#### <a name="asynchronous-receives-with-jms-message-listeners"></a>带有 JMS 消息侦听器的异步接收

消息侦听器是用于对目标上的消息进行异步处理的对象。 它实现接口，该 `MessageListener` 接口包含 `onMessage` 特定业务逻辑必须居住的方法。

消息侦听器对象必须使用方法实例化并注册特定消息使用者 `setMessageListener` 。

```java
Listener myListener = new Listener();
consumer.setMessageListener(myListener);
```

### <a name="consuming-from-topics"></a>使用主题

[JMS 消息使用者](#jms-message-consumers) 是针对 [目标](#jms-destination) 创建的，它可能是一个队列或主题。

队列上的使用者只是在会话上下文中生存的客户端对象 (和客户端应用程序与 Azure 服务总线之间的连接) 。

但主题上的使用者有2部分- 
   * 驻留在会话上下文中的 **客户端对象** (或 JMSContext) 和，
   * 作为 Azure 服务总线上的实体的 **订阅** 。

订阅记录在 [此处](java-message-service-20-entities.md#java-message-service-jms-subscriptions) ，可以是以下项之一- 
   * 共享持久订阅
   * 共享非持久订阅
   * 非共享持久订阅
   * 非共享非持久订阅

### <a name="jms-queue-browsers"></a>JMS 队列浏览器

JMS API 提供一个 `QueueBrowser` 对象，该对象允许应用程序浏览队列中的消息，并显示每条消息的标头值。

可以使用 JMSContext 创建队列浏览器，如下所示。

```java
QueueBrowser browser = context.createBrowser(queue);
```

> [!NOTE]
> JMS API 未提供用于浏览主题的 API。
>
> 这是因为主题本身并不存储这些消息。 一旦将消息发送到主题，就会将其转发到相应的订阅。
>

### <a name="jms-message-selectors"></a>JMS 消息选择器

接收应用程序可使用消息选择器来筛选收到的消息。 使用消息选择器时，接收应用程序会将筛选消息的工作卸载到 JMS 提供程序 (在这种情况下，Azure 服务总线) ，而不是承担这种责任。

创建以下任何使用者时，可以使用选择器- 
   * 共享持久订阅
   * 非共享持久订阅
   * 共享的非持久订阅
   * 非持久订阅
   * 队列浏览器


## <a name="summary"></a>总结

本开发人员指南展示了如何使用 Java 消息服务 (JMS) 使用 java 客户端应用程序连接到 Azure 服务总线。

## <a name="next-steps"></a>后续步骤

若要详细了解 Azure 服务总线以及 Java 消息服务 (JMS) 实体，请查看以下链接 - 
* [服务总线 - 队列、主题和订阅](service-bus-queues-topics-subscriptions.md)
* [服务总线 - Java 消息服务实体](service-bus-queues-topics-subscriptions.md#java-message-service-jms-20-entities)
* [Azure 服务总线中的 AMQP 1.0 支持](service-bus-amqp-overview.md)
* [服务总线 AMQP 1.0 开发人员指南](service-bus-amqp-dotnet.md)
* [服务总线队列入门](service-bus-dotnet-get-started-with-queues.md)
* [Java 消息服务 API（外部 Oracle 文档）](https://docs.oracle.com/javaee/7/api/javax/jms/package-summary.html)
* [了解如何从 ActiveMQ 迁移到服务总线](migrate-jms-activemq-to-servicebus.md)
