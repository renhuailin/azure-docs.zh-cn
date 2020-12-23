---
title: 通过 Java 使用 Azure 服务总线主题和订阅 (azure-messaging-servicebus)
description: 在本快速入门中，你将使用 azure-messaging-servicebus package 包编写 Java 代码，以将消息发送到 Azure 服务总线主题，然后从该主题的订阅接收消息。
ms.devlang: Java
ms.topic: quickstart
ms.date: 11/09/2020
ms.openlocfilehash: 011dbfef1155b82daca216e9519db07188260130
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96489541"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-java"></a>向 Azure 服务总线主题发送消息，并从该主题的订阅接收消息 (Java)
在本快速入门中，你将使用 azure-messaging-servicebus package 包编写 Java 代码，以将消息发送到 Azure 服务总线主题，然后从该主题的订阅接收消息。

> [!IMPORTANT]
> 本快速入门使用新的 azure-messaging-servicebus 包。 有关使用旧 azure-servicebus 包的快速入门，请参阅[使用 azure-servicebus 发送和接收消息](service-bus-java-how-to-use-topics-subscriptions-legacy.md)。

## <a name="prerequisites"></a>先决条件

- Azure 订阅。 要完成本教程，需要一个 Azure 帐户。 可以激活 [Visual Studio 或 MSDN 订阅者权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF)或注册[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)。
- 遵循[快速入门：使用 Azure 门户创建一个服务总线主题和多个对该主题的订阅](service-bus-quickstart-topics-subscriptions-portal.md)。 记下连接字符串、主题名称和订阅名称。 本快速入门仅需使用一个订阅。 
- 安装 [Azure SDK for Java][Azure SDK for Java]。 如果使用 Eclipse，则可安装 [Azure Toolkit for Eclipse][Azure Toolkit for Eclipse]，其中包含用于 Java 的 Azure SDK。 然后，可将“适用于 Java 的 Microsoft Azure 库”添加到项目  。 如果使用 IntelliJ，请参阅[安装 Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/installation)。 


## <a name="send-messages-to-a-topic"></a>将消息发送到主题
在本部分中，你将创建一个 Java 控制台项目，并添加代码以将消息发送到创建的主题。 

### <a name="create-a-java-console-project"></a>创建 Java 控制台项目
使用 Eclipse 或所选工具创建 Java 项目。 

### <a name="configure-your-application-to-use-service-bus"></a>配置应用程序以使用服务总线
添加对 Azure 服务总线库的引用。 [Maven 中心存储库](https://search.maven.org/search?q=a:azure-messaging-servicebus)中提供了服务总线的 Java 客户端库。 可使用 Maven 项目文件中的以下依赖项声明引用此库：

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-servicebus</artifactId>
    <version>7.0.0</version>
</dependency>
```

### <a name="add-code-to-send-messages-to-the-topic"></a>添加将消息发送到主题的代码
1. 将以下 `import` 语句添加到 Java 文件的主题中。 

    ```java
    import com.azure.messaging.servicebus.*;
    import com.azure.messaging.servicebus.models.*;
    import java.util.concurrent.TimeUnit;
    import java.util.function.Consumer;
    import java.util.Arrays;
    import java.util.List;
    ```    
5. 在类中，定义用于保存连接字符串和主题名称的变量，如下所示： 

    ```java
    static String connectionString = "<NAMESPACE CONNECTION STRING>";
    static String topicName = "<TOPIC NAME>";    
    static String subName = "<SUBSCRIPTION NAME>";
    ```

    将 `<NAMESPACE CONNECTION STRING>` 替换为服务总线命名空间的连接字符串。 并将 `<TOPIC NAME>` 替换为主题名称。
3. 在类中添加一个名为 `sendMessage` 的方法，以向主题发送一条消息。 

    ```java
        static void sendMessage()
    {
        // create a Service Bus Sender client for the queue 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .topicName(topicName)
                .buildClient();
        
        // send one message to the topic
        senderClient.sendMessage(new ServiceBusMessage("Hello, World!"));
        System.out.println("Sent a single message to the topic: " + topicName);        
    }
    ```
1. 在类中添加一个名为 `createMessages` 的方法，以创建消息列表。 通常，可以从应用程序的不同部分获得这些消息。 在这里，我们将创建一个示例消息列表。

    ```java
    static List<ServiceBusMessage> createMessages()
    {
        // create a list of messages and return it to the caller
        ServiceBusMessage[] messages = {
                new ServiceBusMessage("First message"),
                new ServiceBusMessage("Second message"),
                new ServiceBusMessage("Third message")
        };
        return Arrays.asList(messages);
    }
    ```
1. 添加一个名为 `sendMessageBatch` 方法的方法，以将消息发送到你创建的主题。 此方法为主题创建 `ServiceBusSenderClient`，调用 `createMessages` 方法来获取消息列表，准备一个或多个批处理，并将批处理发送到主题。 

```java
    static void sendMessageBatch()
    {
        // create a Service Bus Sender client for the topic 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .topicName(topicName)
                .buildClient();

        // Creates an ServiceBusMessageBatch where the ServiceBus.
        ServiceBusMessageBatch messageBatch = senderClient.createMessageBatch();        
        
        // create a list of messages
        List<ServiceBusMessage> listOfMessages = createMessages();
        
        // We try to add as many messages as a batch can fit based on the maximum size and send to Service Bus when
        // the batch can hold no more messages. Create a new batch for next set of messages and repeat until all
        // messages are sent.        
        for (ServiceBusMessage message : listOfMessages) {
            if (messageBatch.tryAddMessage(message)) {
                continue;
            }

            // The batch is full, so we create a new batch and send the batch.
            senderClient.sendMessages(messageBatch);
            System.out.println("Sent a batch of messages to the topic: " + topicName);
            
            // create a new batch
            messageBatch = senderClient.createMessageBatch();

            // Add that message that we couldn't before.
            if (!messageBatch.tryAddMessage(message)) {
                System.err.printf("Message is too large for an empty batch. Skipping. Max size: %s.", messageBatch.getMaxSizeInBytes());
            }
        }

        if (messageBatch.getCount() > 0) {
            senderClient.sendMessages(messageBatch);
            System.out.println("Sent a batch of messages to the topic: " + topicName);
        }

        //close the client
        senderClient.close();
    }
```

## <a name="receive-messages-from-a-subscription"></a>从订阅接收消息
在本部分中，你将添加代码以从主题的订阅中检索消息。 

1. 添加名为 `receiveMessages` 的方法，以从订阅检索消息。 此方法通过指定用于处理消息的处理程序和用于处理错误的另一个处理程序来为订阅创建 `ServiceBusProcessorClient`。 然后，它将启动处理器，等待几秒钟，输出接收的消息，然后停止和关闭处理器。

    ```java
    // handles received messages
    static void receiveMessages() throws InterruptedException
    {
        // Consumer that processes a single message received from Service Bus
        Consumer<ServiceBusReceivedMessageContext> messageProcessor = context -> {
            ServiceBusReceivedMessage message = context.getMessage();
            System.out.println("Received message: " + message.getBody().toString() + " from the subscription: " + subName);
        };

        // Consumer that handles any errors that occur when receiving messages
        Consumer<Throwable> errorHandler = throwable -> {
            System.out.println("Error when receiving messages: " + throwable.getMessage());
            if (throwable instanceof ServiceBusReceiverException) {
                ServiceBusReceiverException serviceBusReceiverException = (ServiceBusReceiverException) throwable;
                System.out.println("Error source: " + serviceBusReceiverException.getErrorSource());
            }
        };

        // Create an instance of the processor through the ServiceBusClientBuilder
        ServiceBusProcessorClient processorClient = new ServiceBusClientBuilder()
            .connectionString(connectionString)
            .processor()
            .topicName(topicName)
            .subscriptionName(subName)
            .processMessage(messageProcessor)
            .processError(errorHandler)
            .buildProcessorClient();

        System.out.println("Starting the processor");
        processorClient.start();

        TimeUnit.SECONDS.sleep(10);
        System.out.println("Stopping and closing the processor");
        processorClient.close();        
    }
    ```
2. 更新 `main` 方法以调用 `sendMessage`、`sendMessageBatch` 和 `receiveMessages` 方法，并引发 `InterruptedException`。     

    ```java
    public static void main(String[] args) throws InterruptedException {        
        sendMessage();
        sendMessageBatch();
        receiveMessages();
    }   
    ```

## <a name="run-the-app"></a>运行应用
运行程序，可以看到类似于以下输出的输出：

```console
Sent a batch of messages to the topic: mytopic
Starting the processor
Received message: First message from the subscription: mysub
Received message: Second message from the subscription: mysub
Received message: Third message from the subscription: mysub
Stopping and closing the processor
```

在 Azure 门户中的服务总线命名空间的“概述”页上，可看到传入和传出消息计数  。 可能需要等待一分钟左右，然后刷新页面才会看到最新值。 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="传入和传出消息计数" lightbox="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png":::

切换到窗格偏中下位置的“主题”选项卡，然后选择主题以查看该主题的“服务总线主题”页 。 在此页上，“消息”图表中应显示四条传入消息和四条传出消息。 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/topic-page-portal.png" alt-text="传入和传出消息" lightbox="./media/service-bus-java-how-to-use-topics-subscriptions/topic-page-portal.png":::

如果注释掉 `main` 方法中的 `receiveMessages` 调用，并再次运行应用，则在“服务总线主题”页上，除 4 条传出消息外，还会看到 8 条传入消息（4 条新消息）。 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/updated-topic-page.png" alt-text="已更新的主题页" lightbox="./media/service-bus-java-how-to-use-topics-subscriptions/updated-topic-page.png":::

在此页上，如果选择一个订阅，则将转到“服务总线订阅”页。 可以在此页上查看活动消息计数、死信消息计数等。 在此示例中，还有四条活动消息未被接收器接收。 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/active-message-count.png" alt-text="活动消息计数" lightbox="./media/service-bus-java-how-to-use-topics-subscriptions/active-message-count.png":::

## <a name="next-steps"></a>后续步骤
请参阅以下文档和示例：

- [适用于 Java 的 Azure 服务总线客户端库 - 自述文件](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/servicebus/azure-messaging-servicebus/README.md)
- [GitHub 上的示例](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/servicebus/azure-messaging-servicebus/src/samples)
- [Java API 参考](/java/api/overview/azure/servicebus?preserve-view=true&view=azure-java-preview)


[Azure SDK for Java]: /java/api/overview/azure/
[Azure Toolkit for Eclipse]: /azure/developer/java/toolkit-for-eclipse/installation
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /dotnet/api/microsoft.azure.servicebus.sqlfilter
[SqlFilter.SqlExpression]: /dotnet/api/microsoft.azure.servicebus.sqlfilter.sqlexpression
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage