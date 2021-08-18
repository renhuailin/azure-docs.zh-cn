---
title: 将 Java 消息服务 2.0 API 与 Azure 服务总线高级版配合使用
description: 如何将 Java 消息服务 (JMS) 与 Azure 服务总线配合使用
ms.topic: article
ms.date: 07/17/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: b27784e4f70a72ae5b114d2796c2aaeace8068ab
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2021
ms.locfileid: "112414677"
---
# <a name="use-java-message-service-20-api-with-azure-service-bus-premium"></a>将 Java 消息服务 2.0 API 与 Azure 服务总线高级版配合使用

本文介绍如何使用热门的 **Java 消息服务 (JMS) 2.0** API 通过高级消息队列协议 (AMQP 1.0) 协议与 Azure 服务总结交互。

> [!NOTE]
> 仅在 Azure 服务总线高级层支持 Java 消息服务 (JMS) 2.0 API。
>

## <a name="pre-requisites"></a>先决条件

### <a name="get-started-with-service-bus"></a>服务总线入门

此指南假定已有服务总线命名空间。 如果没有，则可以使用 [Azure 经典门户](https://portal.azure.com)[创建命名空间和队列](service-bus-create-namespace-portal.md)。 

若要详细了解如何创建服务总线命名空间和队列，请参阅[通过 Azure 门户开始使用服务总线队列](service-bus-quickstart-portal.md)。

### <a name="set-up-a-java-development-environment"></a>设置 Java 开发环境

若要开发 Java 应用程序，需要设置适当的开发环境 - 
   * 安装 JDK（Java 开发工具包）或 JRE (Java Runtime Environment)。
   * 在生成路径和适当的系统变量中添加 JDK 或 JRE。
   * 安装 Java IDE 以利用 JDK 或 JRE。 例如 Eclipse 或 IntelliJ。

若要详细了解如何在 Azure 上准备适用于 Java 的开发人员环境，请使用[本指南](/azure/developer/java/fundamentals/)。

## <a name="what-jms-features-are-supported"></a>支持哪些 JMS 功能？

[!INCLUDE [service-bus-jms-features-list](./includes/service-bus-jms-feature-list.md)]

## <a name="downloading-the-java-message-service-jms-client-library"></a>下载 Java 消息服务 (JMS) 客户端库

若要利用 Azure 服务总线高级层上提供的所有功能，必须将下面的库添加到项目的生成路径。

[Azure-servicebus-jms](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms)

> [!NOTE]
> 若要将 [Azure-servicebus-jms](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms) 添加到生成路径，请利用项目的首选依赖项管理工具，如 [Maven](https://maven.apache.org/) 或 [Gradle](https://gradle.org/)。
>

## <a name="coding-java-applications"></a>为 Java 应用程序编码

导入依赖项后，可以采用独立于 JMS 提供程序的方式编写 Java 应用程序。

### <a name="connecting-to-azure-service-bus-using-jms"></a>使用 JMS 连接到 Azure 服务总线

若要使用 JMS 客户端与 Azure 服务总线连接，需要连接字符串，该字符串在 [Azure 门户](https://portal.azure.com)中“共享访问策略”中的“主连接字符串”下提供 。

1. 实例化 `ServiceBusJmsConnectionFactorySettings`

    ```java
    ServiceBusJmsConnectionFactorySettings connFactorySettings = new ServiceBusJmsConnectionFactorySettings();
    connFactorySettings.setConnectionIdleTimeoutMS(20000);
    ```
2. 使用相应的 `ServiceBusConnectionString` 实例化 `ServiceBusJmsConnectionFactory`。

    ```java
    String ServiceBusConnectionString = "<SERVICE_BUS_CONNECTION_STRING_WITH_MANAGE_PERMISSIONS>";
    ConnectionFactory factory = new ServiceBusJmsConnectionFactory(ServiceBusConnectionString, connFactorySettings);
    ```

3. 使用 `ConnectionFactory` 创建 `Connection`，然后创建 `Session` 

    ```java
    Connection connection = factory.createConnection();
    Session session = connection.createSession();
    ```
    或者创建 `JMSContext`（用于 JMS 2.0 客户端）

    ```java
    JMSContext jmsContext = factory.createContext();
    ```

    >[!IMPORTANT]
    > 虽然 JMS“会话”和服务总线“会话”的名称相似，但它们完全不相关。
    >
    > 在 JMS 1.1 中，会话是允许创建 MessageProducer、MessageConsumer 和消息本身的 API 的基本构建基块。 有关详细信息，请参阅 [JMS API 编程模型](https://docs.oracle.com/javaee/6/tutorial/doc/bnceh.html)
    >
    > 在服务总线中，[会话](message-sessions.md)是对队列和订阅实现 FIFO 处理的服务和客户端构造。
    >

### <a name="write-the-jms-application"></a>编写 JMS 应用程序

`Session` 或 `JMSContext` 经过实例化后，对于管理和数据这两方面的操作，应用程序都可以使用熟悉的 JMS API 来执行。

请参阅[支持的 JMS 功能](how-to-use-java-message-service-20.md#what-jms-features-are-supported)的列表，以了解哪些 API 受支持。

下面是一些用于开始使用 JMS 的示例代码片段 -

#### <a name="sending-messages-to-a-queue-and-topic"></a>将消息发送到队列和主题

```java
// Create the queue and topic
Queue queue = jmsContext.createQueue("basicQueue");
Topic topic = jmsContext.createTopic("basicTopic");
// Create the message
Message msg = jmsContext.createMessage();

// Create the JMS message producer
JMSProducer producer = jmsContext.createProducer();

// send the message to the queue
producer.send(queue, msg);
// send the message to the topic
producer.send(topic, msg);
```

#### <a name="receiving-messages-from-a-queue"></a>从队列接收消息

```java
// Create the queue
Queue queue = jmsContext.createQueue("basicQueue");

// Create the message consumer
JMSConsumer consumer = jmsContext.createConsumer(queue);

// Receive the message
Message msg = (Message) consumer.receive();
```

#### <a name="receiving-messages-from-a-shared-durable-subscription-on-a-topic"></a>从针对某主题的共享持久订阅接收消息

```java
// Create the topic
Topic topic = jmsContext.createTopic("basicTopic");

// Create a shared durable subscriber on the topic
JMSConsumer sharedDurableConsumer = jmsContext.createSharedDurableConsumer(topic, "sharedDurableConsumer");

// Receive the message
Message msg = (Message) sharedDurableConsumer.receive();
```

## <a name="summary"></a>“摘要”

本指南展示了 Java 客户端应用程序如何使用 Java 消息服务 (JMS) 通过 AMQP 1.0 实现与 Azure 服务总线交互。

也可以通过其他语言（包括 .NET、C、Python 和 PHP）使用 Service Bus AMQP 1.0。 使用这些不同语言构建的组件可以使用服务总线中的 AMQP 1.0 支持可靠且完全无损地交换消息。

## <a name="next-steps"></a>后续步骤

若要详细了解 Azure 服务总线以及 Java 消息服务 (JMS) 实体，请查看以下链接 - 
* [服务总线 - 队列、主题和订阅](service-bus-queues-topics-subscriptions.md)
* [服务总线 - Java 消息服务实体](service-bus-queues-topics-subscriptions.md#java-message-service-jms-20-entities)
* [Azure 服务总线中的 AMQP 1.0 支持](service-bus-amqp-overview.md)
* [服务总线 AMQP 1.0 开发人员指南](service-bus-amqp-dotnet.md)
* [服务总线队列入门](service-bus-dotnet-get-started-with-queues.md)
* [Java 消息服务 API（外部 Oracle 文档）](https://docs.oracle.com/javaee/7/api/javax/jms/package-summary.html)
* [了解如何从 ActiveMQ 迁移到服务总线](migrate-jms-activemq-to-servicebus.md)