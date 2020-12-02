---
title: 使用 azure-messaging-servicebus 将消息发送到 Azure 服务总线主题
description: 本快速入门介绍如何使用 azure-messaging-servicebus 包将消息发送到 Azure 服务总线主题。
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 11/13/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 5dfdf846410e9b622f3ef1e9006aa9846de9979c
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498704"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-net"></a>向 Azure 服务总线主题发送消息，并从该主题的订阅接收消息 (.NET)
本教程介绍如何创建 .NET Core 控制台应用，该应用将消息发送到服务总线主题，并从该主题的订阅接收消息。 

> [!Important]
> 本快速入门使用新的 Azure.Messaging.ServiceBus 包。 有关使用旧的 Microsoft.Azure.ServiceBus 包的快速入门，请参阅[使用 Microsoft.Azure.ServiceBus 包发送和接收消息](service-bus-dotnet-how-to-use-topics-subscriptions-legacy.md)。

## <a name="prerequisites"></a>先决条件

- [Visual Studio 2019](https://www.visualstudio.com/vs)
- Azure 订阅。 要完成本教程，需要一个 Azure 帐户。 可以激活 [Visual Studio 或 MSDN 订阅者权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF)或注册[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)。
- 遵循[快速入门：使用 Azure 门户创建一个服务总线主题和多个对该主题的订阅](service-bus-quickstart-topics-subscriptions-portal.md)。 记下连接字符串、主题名称和订阅名称。 本快速入门仅需使用一个订阅。 

## <a name="send-messages-to-a-topic"></a>将消息发送到主题
在本部分中，你将在 Visual Studio 中创建 .NET Core 控制台应用程序，并添加代码以将消息发送到创建的主题。 

### <a name="create-a-console-application"></a>创建控制台应用程序
启动 Visual Studio 并创建新的用于 C# 的 **控制台应用 (.NET Core)** 项目。 

### <a name="add-the-service-bus-nuget-package"></a>添加服务总线 NuGet 包

1. 右键单击新创建的项目，并选择“管理 NuGet 包” 。
1. 选择“浏览”。 搜索并选择 [Azure.Messaging.ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus/)。
1. 选择“安装”以完成安装，然后关闭“NuGet 包管理器”。

### <a name="add-code-to-send-messages-to-the-topic"></a>添加将消息发送到主题的代码 

1. 在 Program.cs 中将以下 `using` 语句添加到命名空间定义顶部，位于类声明之前：
   
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using Azure.Messaging.ServiceBus;
    ```
1. 在 `Program` 类中声明以下变量：

    ```csharp
        static string connectionString = "<NAMESPACE CONNECTION STRING>";
        static string topicName = "<TOPIC NAME>";
        static string subscriptionName = "<SUBSCRIPTION NAME>";
    ```

    请替换以下值：
    - 将 `<NAMESPACE CONNECTION STRING>` 替换为服务总线命名空间的连接字符串
    - 将 `<TOPIC NAME>` 替换为主题名称
    - 将 `<SUBSCRIPTION NAME>` 替换为订阅的名称
2. 添加名为 `SendMessageToTopicAsync` 的方法，该方法向主题发送一条消息。 

    ```csharp
        static async Task SendMessageToTopicAsync()
        {
            // create a Service Bus client 
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a sender for the topic
                ServiceBusSender sender = client.CreateSender(topicName);
                await sender.SendMessageAsync(new ServiceBusMessage("Hello, World!"));
                Console.WriteLine($"Sent a single message to the topic: {topicName}");
            }
        }
    ```
1. 添加一个名为 `CreateMessages` 的方法，以创建指向 `Program` 类的消息队列（.NET 队列）。 通常，可以从应用程序的不同部分获得这些消息。 在这里，我们将创建一个示例消息队列。

    ```csharp
        static Queue<ServiceBusMessage> CreateMessages()
        {
            // create a queue containing the messages and return it to the caller
            Queue<ServiceBusMessage> messages = new Queue<ServiceBusMessage>();
            messages.Enqueue(new ServiceBusMessage("First message"));
            messages.Enqueue(new ServiceBusMessage("Second message"));
            messages.Enqueue(new ServiceBusMessage("Third message"));
            return messages;
        }
    ```
1. 将名为 `SendMessageBatchAsync` 的方法添加到 `Program` 类，并添加以下代码。 此方法使用消息队列，并准备一个或多个要发送到服务总线主题的批处理。 

    ```csharp
        static async Task SendMessageBatchToTopicAsync()
        {
            // create a Service Bus client 
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {

                // create a sender for the topic 
                ServiceBusSender sender = client.CreateSender(topicName);

                // get the messages to be sent to the Service Bus topic
                Queue<ServiceBusMessage> messages = CreateMessages();

                // total number of messages to be sent to the Service Bus topic
                int messageCount = messages.Count;

                // while all messages are not sent to the Service Bus topic
                while (messages.Count > 0)
                {
                    // start a new batch 
                    using ServiceBusMessageBatch messageBatch = await sender.CreateMessageBatchAsync();

                    // add the first message to the batch
                    if (messageBatch.TryAddMessage(messages.Peek()))
                    {
                        // dequeue the message from the .NET queue once the message is added to the batch
                        messages.Dequeue();
                    }
                    else
                    {
                        // if the first message can't fit, then it is too large for the batch
                        throw new Exception($"Message {messageCount - messages.Count} is too large and cannot be sent.");
                    }

                    // add as many messages as possible to the current batch
                    while (messages.Count > 0 && messageBatch.TryAddMessage(messages.Peek()))
                    {
                        // dequeue the message from the .NET queue as it has been added to the batch
                        messages.Dequeue();
                    }

                    // now, send the batch
                    await sender.SendMessagesAsync(messageBatch);

                    // if there are any remaining messages in the .NET queue, the while loop repeats 
                }

                Console.WriteLine($"Sent a batch of {messageCount} messages to the topic: {topicName}");
            }
        }
    ```
1. 将 `Main()` 方法替换为以下 **async** `Main` 方法。 它同时调用发送方法，将单个消息和一批消息发送到主题。  

    ```csharp
        static async Task Main()
        {
            // send a message to the topic
            await SendMessageToTopicAsync();

            // send a batch of messages to the topic
            await SendMessageBatchToTopicAsync();
        }
    ```
5. 运行该应用程序。 应该会看到以下输出：

    ```console
    Sent a single message to the topic: mytopic
    Sent a batch of 3 messages to the topic: mytopic
    ```
1. 在 Azure 门户中按照以下步骤操作：
    1. 导航到服务总线命名空间。 
    1. 在“概述”页底部居中位置的窗格中，切换到“主题”选项卡，然后选择“服务总线”主题 。 下面的示例中采用的是 `mytopic`。
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/select-topic.png" alt-text="选择主题":::
    1. 在“服务总线主题”页的“消息”图表中的底部“指标”部分中，可以看到主题有四条传入的消息  。 如果未看到该值，请等待几分钟，然后刷新页面以查看更新后的图表。 

        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/sent-messages-essentials.png" alt-text="发送到主题的消息" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/sent-messages-essentials.png":::
    4. 在底部窗格中选择订阅。 下面的示例中采用的是 S1。 在“服务总线订阅”页面上，你会看到“活动消息计数”为 4  。 订阅已接收你发送到主题的四条消息，但接收方尚未选择它们。 
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page.png" alt-text="订阅中接收的消息" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page.png":::
    

## <a name="receive-messages-from-a-subscription"></a>从订阅接收消息

1. 将以下方法添加到处理消息和任何错误的 `Program` 类中。 

    ```csharp
        static async Task MessageHandler(ProcessMessageEventArgs args)
        {
            string body = args.Message.Body.ToString();
            Console.WriteLine($"Received: {body} from subscription: {subscriptionName}");

            // complete the message. messages is deleted from the queue. 
            await args.CompleteMessageAsync(args.Message);
        }

        static Task ErrorHandler(ProcessErrorEventArgs args)
        {
            Console.WriteLine(args.Exception.ToString());
            return Task.CompletedTask;
        }
    ```
1. 将以下方法 `ReceiveMessagesFromSubscriptionAsync` 添加到 `Program` 类。

    ```csharp
        static async Task ReceiveMessagesFromSubscriptionAsync()
        {
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a processor that we can use to process the messages
                ServiceBusProcessor processor = client.CreateProcessor(topicName, subscriptionName, new ServiceBusProcessorOptions());

                // add handler to process messages
                processor.ProcessMessageAsync += MessageHandler;

                // add handler to process any errors
                processor.ProcessErrorAsync += ErrorHandler;

                // start processing 
                await processor.StartProcessingAsync();

                Console.WriteLine("Wait for a minute and then press any key to end the processing");
                Console.ReadKey();

                // stop processing 
                Console.WriteLine("\nStopping the receiver...");
                await processor.StopProcessingAsync();
                Console.WriteLine("Stopped receiving messages");
            }
        }
    ```
1. 将对 `ReceiveMessagesFromSubscriptionAsync` 方法的调用添加到 `Main` 方法。 如果只想对接收消息进行测试，请注释禁止 `SendMessagesToTopicAsync` 方法。 如果不这样做，则会看到发送到主题的其他四条消息。 

    ```csharp
        static async Task Main()
        {
            // send a message to the topic
            await SendMessageToTopicAsync();

            // send a batch of messages to the topic
            await SendMessageBatchToTopicAsync();

            // receive messages from the subscription
            await ReceiveMessagesFromSubscriptionAsync();
        }
    ```
## <a name="run-the-app"></a>运行应用
运行该应用程序。 等待一分钟，然后按任意键停止接收消息。 你应该会看到以下输出（密钥的空格）。 

```console
Sent a single message to the topic: mytopic
Sent a batch of 3 messages to the topic: mytopic
Wait for a minute and then press any key to end the processing
Received: Hello, World! from subscription: mysub
Received: First message from subscription: mysub
Received: Second message from subscription: mysub
Received: Third message from subscription: mysub
Received: Hello, World! from subscription: mysub
Received: First message from subscription: mysub
Received: Second message from subscription: mysub
Received: Third message from subscription: mysub

Stopping the receiver...
Stopped receiving messages
```

再次检查门户。 

- 在“服务总线主题”页面上的“消息”图表中，你会看到八条传入消息和八条传出消息 。 如果未看到这些值，请等待几分钟，然后刷新页面以查看更新后的图表。 

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/messages-size-final.png" alt-text="发送和接收的消息" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/messages-size-final.png":::
- 在“服务总线订阅”页面上，你会看到“活动消息计数”为零 。 这是因为接收方已接收并已完成来自此订阅的消息。 

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page-final.png" alt-text="末尾订阅中的活动消息计数" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page-final.png":::
    


## <a name="next-steps"></a>后续步骤
请参阅以下文档和示例：

- [适用于 .NET 的 Azure 服务总线客户端库 - 自述文件](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus)
- [GitHub 上的示例](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples)
- [.NET API 参考](/dotnet/api/azure.messaging.servicebus?preserve-view=true&view=azure-dotnet-preview)