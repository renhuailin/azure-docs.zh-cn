---
title: 通过 Java 使用 Azure 服务总线队列 (azure-messaging-servicebus)
description: 本教程介绍如何使用 Java 向 Azure 服务总线队列发送消息，并从中接收消息。 使用新的 azure-messaging-servicebus 包。
ms.date: 02/13/2021
ms.topic: quickstart
ms.devlang: Java
ms.custom:
- seo-java-july2019
- seo-java-august2019
- seo-java-september2019
- devx-track-java
- mode-api
ms.openlocfilehash: e3998e812bb921ff8bea5e83199bc4e142ed2e83
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533431"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-java"></a>向 Azure 服务总线队列发送消息并从中接收消息 (Java)
在本快速入门中，你将创建一个 Java 应用，以便向 Azure 服务总线队列发送消息，并从中接收消息。 

> [!IMPORTANT]
> 本快速入门使用新的 azure-messaging-servicebus 包。 有关使用旧 azure-servicebus 包的快速入门，请参阅[使用 azure-servicebus 发送和接收消息](service-bus-java-how-to-use-queues-legacy.md)。


## <a name="prerequisites"></a>先决条件
- Azure 订阅。 要完成本教程，需要一个 Azure 帐户。 可以[激活 MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF)或[注册免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)。
- 如果没有可使用的队列，请遵循[使用 Azure 门户创建服务总线队列](service-bus-quickstart-portal.md)一文来创建队列。 记下服务总线命名空间的连接字符串以及创建的队列的名称 。
- 安装 [Azure SDK for Java][Azure SDK for Java]。 如果使用 Eclipse，则可安装 [Azure Toolkit for Eclipse][Azure Toolkit for Eclipse]，其中包含用于 Java 的 Azure SDK。 然后，可将“适用于 Java 的 Microsoft Azure 库”添加到项目  。 如果使用 IntelliJ，请参阅[安装 Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/installation)。 


## <a name="send-messages-to-a-queue"></a>向队列发送消息
在本部分中，你将创建一个 Java 控制台项目，并添加代码以将消息发送到之前创建的队列。 

### <a name="create-a-java-console-project"></a>创建 Java 控制台项目
使用 Eclipse 或所选工具创建 Java 项目。 

### <a name="configure-your-application-to-use-service-bus"></a>配置应用程序以使用服务总线
添加对 Azure Core 和 Azure 服务总线库的引用。 

如果使用的是 Eclipse 并创建了 Java 控制台应用程序，请将 Java 项目转换为 Maven：在“包资源管理器”窗口中右键单击该项目，然后选择“配置” -> “转换为 Maven 项目”  。 然后，将依赖项添加到这两个库，如以下示例所示。

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>org.myorg.sbusquickstarts</groupId>
    <artifactId>sbustopicqs</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <build>
        <sourceDirectory>src</sourceDirectory>
        <plugins>
            <plugin>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.1</version>
                <configuration>
                    <release>15</release>
                </configuration>
            </plugin>
        </plugins>
    </build>
    <dependencies>
        <dependency>
            <groupId>com.azure</groupId>
            <artifactId>azure-core</artifactId>
            <version>1.13.0</version>
        </dependency>
        <dependency>
            <groupId>com.azure</groupId>
            <artifactId>azure-messaging-servicebus</artifactId>
            <version>7.0.2</version>
        </dependency>
    </dependencies>
</project>
```

### <a name="add-code-to-send-messages-to-the-queue"></a>添加将消息发送到队列的代码
1. 将以下 `import` 语句添加到 Java 文件的主题中。 

    ```java
    import com.azure.messaging.servicebus.*;
    
    import java.util.concurrent.CountDownLatch;
    import java.util.concurrent.TimeUnit;
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

    > [!IMPORTANT]
    > 将代码中 `QueueTest::processMessage` 中的 `QueueTest` 替换为你的类的名称。 

    ```java
    // handles received messages
    static void receiveMessages() throws InterruptedException
    {
        CountDownLatch countdownLatch = new CountDownLatch(1);

        // Create an instance of the processor through the ServiceBusClientBuilder
        ServiceBusProcessorClient processorClient = new ServiceBusClientBuilder()
            .connectionString(connectionString)
            .processor()
            .queueName(queueName)
            .processMessage(QueueTest::processMessage)
            .processError(context -> processError(context, countdownLatch))
            .buildProcessorClient();

        System.out.println("Starting the processor");
        processorClient.start();

        TimeUnit.SECONDS.sleep(10);
        System.out.println("Stopping and closing the processor");
        processorClient.close();        
    }   
    ```
2. 添加 `processMessage` 方法以处理从服务总线订阅接收的消息。 

    ```java
    private static void processMessage(ServiceBusReceivedMessageContext context) {
        ServiceBusReceivedMessage message = context.getMessage();
        System.out.printf("Processing message. Session: %s, Sequence #: %s. Contents: %s%n", message.getMessageId(),
            message.getSequenceNumber(), message.getBody());
    }    
    ```
3. 添加 `processError` 方法以处理错误消息。

    ```java
    private static void processError(ServiceBusErrorContext context, CountDownLatch countdownLatch) {
        System.out.printf("Error when receiving messages from namespace: '%s'. Entity: '%s'%n",
            context.getFullyQualifiedNamespace(), context.getEntityPath());

        if (!(context.getException() instanceof ServiceBusException)) {
            System.out.printf("Non-ServiceBusException occurred: %s%n", context.getException());
            return;
        }

        ServiceBusException exception = (ServiceBusException) context.getException();
        ServiceBusFailureReason reason = exception.getReason();

        if (reason == ServiceBusFailureReason.MESSAGING_ENTITY_DISABLED
            || reason == ServiceBusFailureReason.MESSAGING_ENTITY_NOT_FOUND
            || reason == ServiceBusFailureReason.UNAUTHORIZED) {
            System.out.printf("An unrecoverable error occurred. Stopping processing with reason %s: %s%n",
                reason, exception.getMessage());

            countdownLatch.countDown();
        } else if (reason == ServiceBusFailureReason.MESSAGE_LOCK_LOST) {
            System.out.printf("Message lock lost for message: %s%n", context.getException());
        } else if (reason == ServiceBusFailureReason.SERVICE_BUSY) {
            try {
                // Choosing an arbitrary amount of time to wait until trying again.
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                System.err.println("Unable to sleep for period of time");
            }
        } else {
            System.out.printf("Error source %s, reason %s, message: %s%n", context.getErrorSource(),
                reason, context.getException());
        }
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
Processing message. Session: 88d961dd801f449e9c3e0f8a5393a527, Sequence #: 1. Contents: Hello, World!
Processing message. Session: e90c8d9039ce403bbe1d0ec7038033a0, Sequence #: 2. Contents: First message
Processing message. Session: 311a216a560c47d184f9831984e6ac1d, Sequence #: 3. Contents: Second message
Processing message. Session: f9a871be07414baf9505f2c3d466c4ab, Sequence #: 4. Contents: Third message
Stopping and closing the processor
```

在 Azure 门户中的服务总线命名空间的“概述”页上，可看到传入和传出消息计数  。 可能需要等待一分钟左右，然后刷新页面才会看到最新值。 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="传入和传出消息计数" lightbox="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png":::

在此“概述”页上选择队列，导航到“服务总线队列”页面 。 还可在此页上看到传入和传出消息计数 。 还可看到其他信息，如队列的当前大小、最大大小和活动消息计数等  。 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/queue-details.png" alt-text="队列详细信息" lightbox="./media/service-bus-java-how-to-use-queues/queue-details.png":::



## <a name="next-steps"></a>后续步骤
请参阅以下文档和示例：

- [适用于 Java 的 Azure 服务总线客户端库 - 自述文件](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/servicebus/azure-messaging-servicebus/README.md)
- [GitHub 上的示例](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Java API 参考](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-messaging-servicebus/7.0.0/index.html)

[Azure SDK for Java]: /azure/developer/java/sdk/get-started
[Azure Toolkit for Eclipse]: /azure/developer/java/toolkit-for-eclipse/installation
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
