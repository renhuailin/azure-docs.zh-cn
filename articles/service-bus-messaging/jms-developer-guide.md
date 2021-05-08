---
title: Azure 服务总线 JMS 2.0 开发人员指南
description: 如何使用 Java 消息服务 (JMS) 2.0 API 与 Azure 服务总线进行通信
ms.topic: article
ms.date: 01/17/2021
ms.openlocfilehash: 492da46a5ce4f5955a72972d91a8c56d54c385bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "101726948"
---
# <a name="azure-service-bus-jms-20-developer-guide"></a>Azure 服务总线 JMS 2.0 开发人员指南

本指南包含的详细信息可帮助你成功使用 Java 消息服务 (JMS) 2.0 API 与 Azure 服务总线进行通信。

作为 Java 开发人员，如果你不熟悉 Azure 服务总线，请参阅以下文章。

| 入门 | 概念 |
|----------------|-------|
| <ul> <li> [什么是 Azure 服务总线](service-bus-messaging-overview.md) </li> <li> [队列、主题和订阅](service-bus-queues-topics-subscriptions.md) </li> </ul> | <ul> <li> [Azure 服务总线 - 高级层](service-bus-premium-messaging.md) </li> </ul> |

## <a name="java-message-service-jms-programming-model"></a>Java 消息服务 (JMS) 编程模型

Java 消息服务 API 编程模型如下所示 - 

> [!NOTE]
>
>Azure 服务总线高级层支持 JMS 1.1 和 JMS 2.0。
> <br> <br>
> Azure 服务总线 - 标准层支持有限 JMS 1.1 功能。 有关详细信息，请参阅此[文档](service-bus-java-how-to-use-jms-api-amqp.md)。
>

# <a name="jms-20-programming-model"></a>[JMS 2.0 编程模型](#tab/JMS-20)

:::image type="content" source="./media/jms-developer-guide/java-message-service-20-programming-model.png "alt-text="显示 JMS 2.0 编程模型的关系图。" border="false":::

# <a name="jms-11-programming-model"></a>[JMS 1.1 编程模型](#tab/JMS-11)

:::image type="content" source="./media/jms-developer-guide/java-message-service-11-programming-model.png "alt-text="显示 JMS 1.1 编程模型的关系图。" border="false":::

---

## <a name="jms---building-blocks"></a>JMS - 构建基块

以下构建基块可用于与 JMS 应用程序进行通信。

> [!NOTE]
> 以下指南从 [Oracle Java EE 6 Java 消息服务 (JMS) 教程](https://docs.oracle.com/javaee/6/tutorial/doc/bnceh.html)改写而来
>
> 建议参考本教程，更好地了解 Java 消息服务 (JMS)。
>

### <a name="connection-factory"></a>连接工厂
客户端使用连接工厂对象与 JMS 提供程序连接。 连接工厂用于封装由管理员定义的一组连接配置参数。

每个连接工厂是 `ConnectionFactory`、`QueueConnectionFactory` 或 `TopicConnectionFactory` 接口的实例。

为了简化与 Azure 服务总线的连接，这些接口分别通过 `ServiceBusJmsConnectionFactory`、`ServiceBusJmsQueueConnectionFactory` 和 `ServiceBusJmsTopicConnectionFactory` 来实现。 可以使用以下参数实例化连接工厂 - 
   * 连接字符串 - 用于Azure 服务总线高级层命名空间的连接字符串。
   * ServiceBusJmsConnectionFactorySettings 属性包，其中包含
      * connectionIdleTimeoutMS - 空闲连接超时（以毫秒为单位）。
      * traceFrames - 收集 AMQP 跟踪帧用于调试的布尔型标志。
      * *其他配置参数*

可以按如下所示创建工厂。 连接字符串是必需参数，而其他属性是可选参数。

```java
ConnectionFactory factory = new ServiceBusJmsConnectionFactory(SERVICE_BUS_CONNECTION_STRING, null);
```

> [!IMPORTANT]
> 利用 JMS 2.0 API 的 Java 应用程序只能使用连接字符串连接到 Azure 服务总线。 目前，仅支持使用连接字符串对 JMS 客户端进行身份验证。
>
> 当前不支持 Azure Active Directory (AAD) 支持的身份验证。
>

### <a name="jms-destination"></a>JMS 目标

“目标”是客户端用来指定其生成的消息目标以及其使用的消息源的对象。

目标映射到 Azure 服务总线中的实体 - 队列（点到点方案中）和主题（发布-订阅方案中）。

### <a name="connections"></a>连接

连接封装与 JMS 提供程序的虚拟连接。 对于 Azure 服务总线，这表示应用程序与 Azure 服务总线之间通过 AMQP 建立的有状态连接。

从连接工厂创建连接，如下所示。

```java
Connection connection = factory.createConnection();
```

### <a name="sessions"></a>会话

会话是一种用于生成和使用消息的单线程上下文。 它可以用来创建消息、消息生成方和使用者，但它还提供事务上下文以允许将发送和接收分组到一个原子工作单元中。

可以从连接对象创建会话，如下所示。

```java
Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
```

#### <a name="session-modes"></a>会话模式

可以使用以下任一模式创建会话。

| 会话模式 | 行为 |
| ----------------- | -------- |
|**Session.AUTO_ACKNOWLEDGE** | 当会话从接收调用成功返回，或当会话调用以处理消息的消息侦听器成功返回时，会话会自动确认客户端接收到消息。|
|**Session.CLIENT_ACKNOWLEDGE** |客户端通过调用消息的确认方法来确认已使用的消息。|
|**Session.DUPS_OK_ACKNOWLEDGE**|此确认模式指示会话延迟确认消息送达。| 
|**Session.SESSION_TRANSACTED**|此值可以作为参数传递给连接对象上的方法 createSession (int sessionMode)，以指定会话应使用本地事务。| 

如果未指定会话模式，则默认选取“Session.AUTO_ACKNOWLEDGE”。

### <a name="jmscontext"></a>JMSContext

> [!NOTE]
> JMSContext 定义为 JMS 2.0 规范的一部分。
>

JMSContext 合并连接对象和会话对象提供的功能。 可以从连接工厂对象创建它。

```java
JMSContext context = connectionFactory.createContext();
```

#### <a name="jmscontext-modes"></a>JMSContext 模式

与会话对象一样，可以使用[会话模式](#session-modes)中提到的同一确认模式来创建 JMSContext。

```java
JMSContext context = connectionFactory.createContext(JMSContext.AUTO_ACKNOWLEDGE);
```

如果未指定模式，则默认选取“JMSContext.AUTO_ACKNOWLEDGE”。

### <a name="jms-message-producers"></a>JMS 消息生成方

消息生成方是使用 JMSContext 或会话创建的对象，用于将消息发送到目标。

可以将其创建为独立的对象，如下所示 - 

```java
JMSProducer producer = context.createProducer();
```

也可以在需要发送消息的运行时创建。

```java
context.createProducer().send(destination, message);
```

### <a name="jms-message-consumers"></a>JMS 消息使用者

消息使用者是由 JMSContext 或会话创建的对象，用于接收发送到目标的消息。 可按如下所示创建它 -

```java
JMSConsumer consumer = context.createConsumer(dest);
```

#### <a name="synchronous-receives-via-receive-method"></a>通过 receive() 方法进行同步接收

消息使用者通过 `receive()` 方法以同步方式从目标接收消息。

如果未指定参数/超时，或指定超时值为“0”，则除非消息到达或连接断开（取两者之中较早发生的情况），会无限期阻止使用者。

```java
Message m = consumer.receive();
Message m = consumer.receive(0);
```

如果提供非零正参数，则计时器过期之前会一直阻止使用者。

```java
Message m = consumer.receive(1000); // time out after one second.
```

#### <a name="asynchronous-receives-with-jms-message-listeners"></a>使用 JMS 消息侦听器进行异步接收

消息侦听器是用于对目标上的消息进行异步处理的对象。 它实现 `MessageListener` 接口，该接口包含必须存在特定业务逻辑的 `onMessage` 方法。

必须使用 `setMessageListener` 方法将消息侦听器对象实例化并针对特定消息使用者进行注册。

```java
Listener myListener = new Listener();
consumer.setMessageListener(myListener);
```

### <a name="consuming-from-topics"></a>使用主题

[JMS 消息使用者](#jms-message-consumers)根据[目标](#jms-destination)创建，而目标可能是一个队列或主题。

队列使用者只是客户端应用程序与 Azure 服务总线之间的会话（和连接）上下文中存在的客户端对象。

但主题使用者则包含 2 个部分 - 
   * 会话（或 JMSContext）上下文中存在的客户端对象，
   * Azure 服务总线中的订阅实体。

[此处](java-message-service-20-entities.md#java-message-service-jms-subscriptions)介绍了订阅，它可以是以下内容之一 - 
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
> JMS API 不提供用于浏览主题的 API。
>
> 这是因为主题本身并不存储消息。 一旦将消息发送到主题，就会将其转发到相应的订阅。
>

### <a name="jms-message-selectors"></a>JMS 消息选择器

接收应用程序可使用消息选择器来筛选接收到的消息。 借助消息选择器，接收应用程序会将消息筛选卸载到 JMS 提供程序（此时为 Azure 服务总线），而自身不会负责这项工作。

创建以下任一使用者时，可以使用选择器 - 
   * 共享持久订阅
   * 非共享持久订阅
   * 共享非持久订阅
   * 非共享非持久订阅
   * 队列浏览器


## <a name="summary"></a>摘要

本开发人员指南展示了 Java 客户端应用程序如何使用 Java 消息服务 (JMS) 与 Azure 服务总线连接。

## <a name="next-steps"></a>后续步骤

若要详细了解 Azure 服务总线以及 Java 消息服务 (JMS) 实体，请查看以下链接 - 
* [服务总线 - 队列、主题和订阅](service-bus-queues-topics-subscriptions.md)
* [服务总线 - Java 消息服务实体](service-bus-queues-topics-subscriptions.md#java-message-service-jms-20-entities)
* [Azure 服务总线中的 AMQP 1.0 支持](service-bus-amqp-overview.md)
* [服务总线 AMQP 1.0 开发人员指南](service-bus-amqp-dotnet.md)
* [服务总线队列入门](service-bus-dotnet-get-started-with-queues.md)
* [Java 消息服务 API（外部 Oracle 文档）](https://docs.oracle.com/javaee/7/api/javax/jms/package-summary.html)
* [了解如何从 ActiveMQ 迁移到服务总线](migrate-jms-activemq-to-servicebus.md)
