---
title: 使用 azure-messaging-servicebus 将消息发送到 Azure 服务总线主题
description: 本快速入门介绍如何使用 azure-messaging-servicebus 包将消息发送到 Azure 服务总线主题。
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 03/16/2021
ms.custom: contperf-fy21q3
ms.openlocfilehash: 7cc854c850b02674151da7c88d94a2800b6572f5
ms.sourcegitcommit: 2f322df43fb3854d07a69bcdf56c6b1f7e6f3333
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2021
ms.locfileid: "108015962"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-net"></a>向 Azure 服务总线主题发送消息，并从该主题的订阅接收消息 (.NET)
在本教程中，将创建一个 C# 应用程序来执行以下任务：

1. 向服务总线主题发送消息。 

    服务总线主题提供了一个端点，以供发送方应用程序用来发送消息。 一个主题可以有一个或多个订阅。 主题的每个订阅都将获取发送到主题的消息的副本。 有关“服务总线”主题的详细信息，请参阅[什么是 Azure 服务总线？](service-bus-messaging-overview.md)。 
1. 接收来自主题订阅的消息。 

    :::image type="content" source="./media/service-bus-messaging-overview/about-service-bus-topic.png" alt-text="服务总线主题和订阅":::

    > [!Important]
    > 本快速入门使用新的 Azure.Messaging.ServiceBus 包。 如果你在使用旧的 Microsoft.Azure.ServiceBus 包，请参阅[使用 Microsoft.Azure.ServiceBus 包发送和接收消息](service-bus-dotnet-how-to-use-topics-subscriptions-legacy.md)。

## <a name="prerequisites"></a>先决条件

- Azure 订阅。 要完成本教程，需要一个 Azure 帐户。 可以激活 [Visual Studio 或 MSDN 订阅者权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF)或注册[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)。
- 遵循这一[快速入门](service-bus-quickstart-topics-subscriptions-portal.md)中的步骤，创建一个“服务总线”主题和多个对该主题的订阅。 

    > [!NOTE]
    > 在本教程中，你将使用连接字符串连接到命名空间、主题名称和某个订阅的名称。  
- [Visual Studio 2019](https://www.visualstudio.com/vs)。 
 
## <a name="send-messages-to-a-topic"></a>将消息发送到主题
在本部分中，你将在 Visual Studio 中创建 .NET Core 控制台应用程序，并添加代码以将消息发送到创建的“服务总线”主题。 

### <a name="create-a-console-application"></a>创建控制台应用程序
使用 Visual Studio 创建 .NET Core 控制台应用程序。 

1. 启动 Visual Studio。  
1. 如果看到“入门”页，请选择“创建新项目”。 
1. 在“创建新项目”页上，遵循以下步骤： 
    1. 对于编程语言，请选择“C#”。 
    1. 对于项目类型，选择“控制台”。 
    1. 从模板列表中选择“控制台应用 (.NET Core)”。 
    1. 然后，选择“下一步”  。 
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-console-project.png" alt-text="创建控制台应用项目":::
1. 在“配置新项目”页上，遵循以下步骤： 
    1. 对于“项目名称”，请输入项目的名称。 
    1. 对于“位置”，请选择项目和解决方案文件的位置。 
    1. 对于“解决方案名称”，请输入解决方案的名称。 一个 Visual Studio 解决方案可以包含一个或多个项目。 在这一快速入门中，解决方案将只有一个项目。 
    1. 选择“创建”来创建项目。 
            
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-console-project-2.png" alt-text="输入项目和解决方案的名称和位置":::    


### <a name="add-the-service-bus-nuget-package"></a>添加服务总线 NuGet 包
1. 右键单击新创建的项目，并选择“管理 NuGet 包” 。

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/manage-nuget-packages-menu.png" alt-text="“管理 NuGet 包”菜单":::        
1. 切换到“浏览”选项卡。
1. 搜索并选择 [Azure.Messaging.ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus/)。
1. 选择“安装”即可完成安装。

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/select-service-bus-package.png" alt-text="“选择服务总线 NuGet”包。":::
5. 如果看到“预览更改”对话框，请选择“确定”以继续。 
1. 如果看到“许可证接受”页，请选择“我接受”以继续。 
    

### <a name="add-code-to-send-messages-to-the-topic"></a>添加将消息发送到主题的代码 

1. 在“解决方案资源管理器”窗口中，双击“Program.cs”以在编辑器中打开该文件。 
1. 将以下 `using` 语句添加到命名空间定义顶部，位于类声明之前：
   
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using Azure.Messaging.ServiceBus;
    ```
1. 在 `Program` 类中，`Main` 函数的上方，声明以下变量：

    ```csharp
        static string connectionString = "<NAMESPACE CONNECTION STRING>";
        static string topicName = "<SERVICE BUS TOPIC NAME>";
        static string subscriptionName = "<SERVICE BUS - TOPIC SUBSCRIPTION NAME>";
    ```

    请替换以下值：
    - 将 `<NAMESPACE CONNECTION STRING>` 替换为服务总线命名空间的连接字符串
    - 将 `<TOPIC NAME>` 替换为主题名称
    - 将 `<SUBSCRIPTION NAME>` 替换为订阅的名称

### <a name="send-a-single-message-to-the-topic"></a>向主题发送一条消息
向 `Program` 类中添加一个名为 `SendMessageToTopicAsync` 的方法（可添加在 `Main` 方法的上方或下方）。 这个方法可将单条消息发送到主题。

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

该方法将执行以下步骤： 
1. 使用命名空间的连接字符串创建 [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) 对象。 
1. 使用 `ServiceBusClient` 对象为指定的“服务总线”主题创建 [ServiceBusSender](/dotnet/api/azure.messaging.servicebus.servicebussender) 对象。 这一步使用 [ServiceBusClient. CreateSender](/dotnet/api/azure.messaging.servicebus.servicebusclient.createsender) 方法。
1. 然后，这个方法使用 [ServiceBusSender. SendMessageAsync](/dotnet/api/azure.messaging.servicebus.servicebussender.sendmessageasync) 方法将一条测试消息发送到“服务总线”主题。 

### <a name="send-a-batch-of-messages-to-the-topic"></a>向主题发送一批消息
1. 添加一个名为 `CreateMessages` 的方法，以创建指向 `Program` 类的消息队列（.NET 队列，并非服务总线队列）。 通常，可以从应用程序的不同部分获得这些消息。 在这里，我们将创建一个示例消息队列。 如果不熟悉 .NET 队列，请参阅 [Queue.Enqueue](/dotnet/api/system.collections.queue.enqueue)。

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

    下面是代码中的重要步骤：
    1. 使用命名空间的连接字符串创建 [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) 对象。 
    1. 对 `ServiceBusClient` 对象调用 [CreateSender](/dotnet/api/azure.messaging.servicebus.servicebusclient.createsender) 方法，从而为指定的“服务总线”主题创建 [ServiceBusSender](/dotnet/api/azure.messaging.servicebus.servicebussender) 对象。 
    1. 调用帮助器方法 `CreateMessages` 以获取要发送到“服务总线”主题的消息队列。 
    1. 使用 [ServiceBusSender.CreateMessageBatchAsync](/dotnet/api/azure.messaging.servicebus.servicebussender.createmessagebatchasync) 创建 [ServiceBusMessageBatch](/dotnet/api/azure.messaging.servicebus.servicebusmessagebatch)。
    1. 使用 [ServiceBusMessageBatch.TryAddMessage](/dotnet/api/azure.messaging.servicebus.servicebusmessagebatch.tryaddmessage) 将消息添加到该批次。 在消息被添加到批次后，将从 .NET 队列中删除这些消息。 
    1. 使用 [ServiceBusSender.SendMessagesAsync](/dotnet/api/azure.messaging.servicebus.servicebussender.sendmessagesasync) 方法将批量消息发送到“服务总线”主题。

### <a name="update-the-main-method"></a>更新 Main 方法
将 `Main()` 方法替换为以下 **async** `Main` 方法。 它同时调用发送方法，将单个消息和一批消息发送到主题。  

```csharp
    static async Task Main()
    {
        // send a single message to the topic
        await SendMessageToTopicAsync();

        // send a batch of messages to the topic
        await SendMessageBatchToTopicAsync();
    }
```

### <a name="test-the-app-to-send-messages-to-the-topic"></a>测试用于将消息发送到主题的应用
1. 运行该应用程序。 应该会看到以下输出：

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

    下面是代码中的重要步骤：
    1. 使用命名空间的连接字符串创建 [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) 对象。 
    1. 对 `ServiceBusClient` 对象调用 [CreateProcessor](/dotnet/api/azure.messaging.servicebus.servicebusclient.createprocessor) 方法，从而为指定的“服务总线”主题和订阅组合创建 [ServiceBusProcessor](/dotnet/api/azure.messaging.servicebus.servicebusprocessor) 对象。 
    1. 为 `ServiceBusProcessor` 对象的 [ProcessMessageAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processmessageasync) 和 [ProcessErrorAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processerrorasync) 事件指定处理程序。 
    1. 通过对 `ServiceBusProcessor` 对象调用 [StartProcessingAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.startprocessingasync) 以开始处理消息。 
    1. 当用户按下某个键结束处理时，将对 `ServiceBusProcessor` 对象调用 [StopProcessingAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.stopprocessingasync)。 
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
- [GitHub 上的 Azure 服务总线的 .NET 示例](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples)
- [.NET API 参考](/dotnet/api/azure.messaging.servicebus?preserve-view=true)
