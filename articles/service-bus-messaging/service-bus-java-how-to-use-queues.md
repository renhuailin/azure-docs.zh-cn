---
title: 通过 Java 使用 Azure 服务总线队列 (azure-messaging-servicebus)
description: 本教程介绍如何使用 Java 向 Azure 服务总线队列发送消息，并从中接收消息。 使用新的 azure-messaging-servicebus 包。
ms.devlang: Java
ms.topic: quickstart
ms.date: 11/09/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: a91ed2a358a9595a4d22dd629b8d470423b786d6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "95909520"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-java"></a>向 Azure 服务总线队列发送消息并从中接收消息 (Java)
在本快速入门中，你将创建一个 Java 应用，以便向 Azure 服务总线队列发送消息，并从中接收消息。 

> [!IMPORTANT]
> 本快速入门使用处于预览阶段的新 azure-messaging-servicebus 包。 有关使用当前正式发布 (GA) 的 azure-servicebus 包的快速入门，请参阅[使用 azure-servicebus 发送和接收消息](service-bus-java-how-to-use-queues-legacy.md)。

## <a name="prerequisites"></a>先决条件
- Azure 订阅。 若要完成本教程，需要一个 Azure 帐户。 可以[激活 MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF)或[注册免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)。
- 如果没有可使用的队列，请遵循[使用 Azure 门户创建服务总线队列](service-bus-quickstart-portal.md)一文来创建队列。 记下服务总线命名空间的连接字符串以及创建的队列的名称 。
- 安装 [Azure SDK for Java][Azure SDK for Java]。 如果使用 Eclipse，则可安装 [Azure Toolkit for Eclipse][Azure Toolkit for Eclipse]，其中包含用于 Java 的 Azure SDK。 然后，可将“适用于 Java 的 Microsoft Azure 库”添加到项目  。 如果使用 IntelliJ，请参阅[安装 Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/installation)。 


## <a name="send-messages-to-a-queue"></a>向队列发送消息
在本部分中，你将创建一个 Java 控制台项目，并添加代码以将消息发送到之前创建的队列。 

### <a name="create-a-java-console-project"></a>创建 Java 控制台项目
使用 Eclipse 或所选工具创建 Java 项目。 

### <a name="configure-your-application-to-use-service-bus"></a>配置应用程序以使用服务总线
添加对 Azure 服务总线库的引用。 [Maven 中心存储库](https://search.maven.org/search?q=a:azure-messaging-servicebus)中提供了服务总线的 Java 客户端库。 可使用 Maven 项目文件中的以下依赖项声明引用此库：

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-servicebus</artifactId>
    <version>7.0.0-beta.7</version>
</dependency>
```

### <a name="add-code-to-send-messages-to-the-queue"></a>添加将消息发送到队列的代码
1. 将以下 `import` 语句添加到 Java 文件的主题中。 

    ```java
    import com.azure.messaging.servicebus.*;
    import com.azure.messaging.servicebus.models.*;
    import java.util.concurrent.TimeUnit;
    import java.util.function.Consumer;
    import java.util.Arrays;
    import java.util.List;
    ```    
5. 在类中，定义用于保存连接字符串和队列名称的变量，如下所示： 

    ```java
    static String connectionString = "<NAMESPACE CONNECTION STRING>";
    static String queueName = "<QUEUE NAME>";    
    ```

    将 `<NAMESPACE CONNECTION STRING>` 替换为服务总线命名空间的连接字符串。 并将 `<QUEUE NAME>` 替换为该队列的名称。
3. 在类中添加一个名为 `sendMessage` 的方法，以向队列发送一条消息。 

    ```java
    static void sendMessage()
    {
        // create a Service Bus Sender client for the queue 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .queueName(queueName)
                .buildClient();
        
        // send one message to the queue
        senderClient.sendMessage(new ServiceBusMessage("Hello, World!"));
        System.out.println("Sent a single message to the queue: " + queueName);        
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
1. 添加一个名为 `sendMessageBatch` 方法的方法，以将消息发送到你创建的队列。 此方法为队列创建 `ServiceBusSenderClient`调用 `createMessages` 方法来获取消息列表，准备一个或多个批处理，并将批处理发送到队列。 

```java
    static void sendMessageBatch()
    {
        // create a Service Bus Sender client for the queue 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .queueName(queueName)
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
            System.out.println("Sent a batch of messages to the queue: " + queueName);
            
            // create a new batch
            messageBatch = senderClient.createMessageBatch();

            // Add that message that we couldn't before.
            if (!messageBatch.tryAddMessage(message)) {
                System.err.printf("Message is too large for an empty batch. Skipping. Max size: %s.", messageBatch.getMaxSizeInBytes());
            }
        }
        
        if (messageBatch.getCount() > 0) {
            senderClient.sendMessages(messageBatch);
            System.out.println("Sent a batch of messages to the queue: " + queueName);
        }

        //close the client
        senderClient.close();
    }
```

## <a name="receive-messages-from-a-queue"></a>从队列接收消息
在本部分中，你将添加从队列检索消息的代码。 

1. 添加名为 `receiveMessages` 的方法，以从队列检索消息。 此方法通过指定用于处理消息的处理程序和用于处理错误的另一个处理程序来为队列创建 `ServiceBusProcessorClient`。 然后，它将启动处理器，等待几秒钟，输出接收的消息，然后停止和关闭处理器。

    ```java
    // handles received messages
    static void receiveMessages() throws InterruptedException
    {
        // consumer that processes a single message received from Service Bus
        Consumer<ServiceBusReceivedMessageContext> messageProcessor = context -> {
            ServiceBusReceivedMessage message = context.getMessage();
            System.out.println("Received message: " + message.getBody().toString());
        };

        // handles any errors that occur when receiving messages
        Consumer<Throwable> errorHandler = throwable -> {
            System.out.println("Error when receiving messages: " + throwable.getMessage());
            if (throwable instanceof ServiceBusReceiverException) {
                ServiceBusReceiverException serviceBusReceiverException = (ServiceBusReceiverException) throwable;
                System.out.println("Error source: " + serviceBusReceiverException.getErrorSource());
            }
        };

        // create an instance of the processor through the ServiceBusClientBuilder
        ServiceBusProcessorClient processorClient = new ServiceBusClientBuilder()
            .connectionString(connectionString)
            .processor()
            .queueName(queueName)
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
运行应用程序时，会在控制台窗口中看到以下消息。 

```console
Sent a single message to the queue: myqueue
Sent a batch of messages to the queue: myqueue
Starting the processor
Received message: Hello, World!
Received message: First message in the batch
Received message: Second message in the batch
Received message: Three message in the batch
Stopping and closing the processor
```

在 Azure 门户中的服务总线命名空间的“概述”页上，可看到传入和传出消息计数  。 可能需要等待一分钟左右，然后刷新页面才会看到最新值。 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="传入和传出消息计数" lightbox="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png":::

在此“概述”页上选择队列，导航到“服务总线队列”页面 。 还可在此页上看到传入和传出消息计数 。 还可看到其他信息，如队列的当前大小、最大大小和活动消息计数等  。 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/queue-details.png" alt-text="队列详细信息" lightbox="./media/service-bus-java-how-to-use-queues/queue-details.png":::



## <a name="next-steps"></a>后续步骤
请参阅以下文档和示例：

- [适用于 Java 的 Azure 服务总线客户端库 - 自述文件](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/servicebus/azure-messaging-servicebus/README.md)
- [GitHub 上的示例](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/servicebus/azure-messaging-servicebus/src/samples)
- [Java API 参考](https://docs.microsoft.com/java/api/overview/azure/servicebus?view=azure-java-preview&preserve-view=true)

请参阅 [GitHub 上的更多示例](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/servicebus/azure-messaging-servicebus)。 

[Azure SDK for Java]: /azure/developer/java/sdk/java-sdk-azure-get-started
[Azure Toolkit for Eclipse]: /azure/developer/java/toolkit-for-eclipse/installation
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
