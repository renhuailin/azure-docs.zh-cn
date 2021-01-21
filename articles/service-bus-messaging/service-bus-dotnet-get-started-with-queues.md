---
title: Azure 服务总线队列入门 (Azure.Messaging.ServiceBus)
description: 在本教程中，你将创建 .NET Core C# 应用程序，用于向/从服务总线队列发送/接收消息。
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 11/13/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: ec3f53e6f69614028c013efa5f0e6852cbc3f8ae
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2021
ms.locfileid: "98631634"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-net"></a>向/从服务总线队列发送/接收消息 (.NET)
在本教程中，你将使用 Azure.Messaging.ServiceBus 包创建 .NET Core C# 控制台应用程序，用于向/从 Azure 服务总线队列发送/接收消息。 

> [!Important]
> 本快速入门使用新的 Azure.Messaging.ServiceBus 包。 有关使用旧的 Microsoft.Azure.ServiceBus 包的快速入门，请参阅[使用 Microsoft.Azure.ServiceBus 包发送和接收事件](service-bus-dotnet-get-started-with-queues-legacy.md)。

## <a name="prerequisites"></a>先决条件

- [Visual Studio 2019](https://www.visualstudio.com/vs)
- Azure 订阅。 若要完成本教程，需要一个 Azure 帐户。 可以[激活 MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF)或[注册免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)。
- 如果没有可使用的队列，请遵循[使用 Azure 门户创建服务总线队列](service-bus-quickstart-portal.md)一文来创建队列。 记下服务总线命名空间的连接字符串以及创建的队列的名称 。

## <a name="send-messages-to-a-queue"></a>向队列发送消息
在此快速入门中，你将创建 C# .Net Core 控制台应用程序，以将消息发送到队列。

### <a name="create-a-console-application"></a>创建控制台应用程序
启动 Visual Studio 并创建新的用于 C# 的 **控制台应用 (.NET Core)** 项目。 

### <a name="add-the-service-bus-nuget-package"></a>添加服务总线 NuGet 包

1. 右键单击新创建的项目，并选择“管理 NuGet 包” 。
1. 选择“浏览”。 搜索并选择 [Azure.Messaging.ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus/)。
1. 选择“安装”以完成安装，然后关闭“NuGet 包管理器”。

### <a name="add-code-to-send-messages-to-the-queue"></a>添加将消息发送到队列的代码

1. 在 *Program.cs* 中将以下 `using` 语句添加到命名空间定义顶部，位于类声明之前：

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    
    using Azure.Messaging.ServiceBus;
    ```

1. 在 `Program` 类中声明以下变量：

    ```csharp
        static string connectionString = "<NAMESPACE CONNECTION STRING>";
        static string queueName = "<QUEUE NAME>";
    ```

    以 `connectionString` 变量的形式输入命名空间的连接字符串。 输入队列名称。

1. 紧跟在 `Main()` 方法的后面，添加以下 `SendMessagesAsync()` 方法来发送消息：

    ```csharp
        static async Task SendMessageAsync()
        {
            // create a Service Bus client 
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a sender for the queue 
                ServiceBusSender sender = client.CreateSender(queueName);

                // create a message that we can send
                ServiceBusMessage message = new ServiceBusMessage("Hello world!");

                // send the message
                await sender.SendMessageAsync(message);
                Console.WriteLine($"Sent a single message to the queue: {queueName}");
            }
        }
    ```
1. 添加一个名为 `CreateMessages` 的方法，以创建指向 `Program` 类的消息队列（.NET 队列）。 通常，可以从应用程序的不同部分获得这些消息。 在这里，我们将创建一个示例消息队列。

    ```csharp
        static Queue<ServiceBusMessage> CreateMessages()
        {
            // create a queue containing the messages and return it to the caller
            Queue<ServiceBusMessage> messages = new Queue<ServiceBusMessage>();
            messages.Enqueue(new ServiceBusMessage("First message in the batch"));
            messages.Enqueue(new ServiceBusMessage("Second message in the batch"));
            messages.Enqueue(new ServiceBusMessage("Third message in the batch"));
            return messages;
        }
    ```
1. 将名为 `SendMessageBatchAsync` 的方法添加到 `Program` 类，并添加以下代码。 此方法使用消息队列，并准备一个或多个要发送到服务总线队列的批处理。 

    ```csharp
        static async Task SendMessageBatchAsync()
        {
            // create a Service Bus client 
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a sender for the queue 
                ServiceBusSender sender = client.CreateSender(queueName);

                // get the messages to be sent to the Service Bus queue
                Queue<ServiceBusMessage> messages = CreateMessages();

                // total number of messages to be sent to the Service Bus queue
                int messageCount = messages.Count;

                // while all messages are not sent to the Service Bus queue
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

                Console.WriteLine($"Sent a batch of {messageCount} messages to the topic: {queueName}");
            }
        }
    ```
1. 将 `Main()` 方法替换为以下 **async** `Main` 方法。 它同时调用发送方法，将单个消息和一批消息发送到队列。 

    ```csharp
        static async Task Main()
        {
            // send a message to the queue
            await SendMessageAsync();

            // send a batch of messages to the queue
            await SendMessageBatchAsync();
        }
    ```
5. 运行该应用程序。 你应该会看到以下消息。 

    ```console
    Sent a single message to the queue: myqueue
    Sent a batch of messages to the queue: myqueue
    ```       
1. 在 Azure 门户中按照以下步骤操作：
    1. 导航到服务总线命名空间。 
    1. 在“概述”页上，在底部中间的窗格中选择队列。 
    1. 请注意“必备”部分中的值。

    :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/sent-messages-essentials.png" alt-text="收到的消息，包含计数和大小" lightbox="./media/service-bus-dotnet-get-started-with-queues/sent-messages-essentials.png":::

    请注意以下值：
    - 队列的“活动消息计数”值现在为 4 。 每次运行此发件人应用而不检索消息时，该值会增加 4。
    - 每次该应用将消息添加到队列，队列的当前大小就会递增，增量为“基本信息”中的“当前”值。 
    - 在“消息”图表中的底部“指标”部分中，可以看到队列有四条传入的消息 。 

## <a name="receive-messages-from-a-queue"></a>从队列接收消息
在本部分中，你将添加从队列检索消息的代码。

1. 将以下方法添加到处理消息和任何错误的 `Program` 类中。 

    ```csharp
        // handle received messages
        static async Task MessageHandler(ProcessMessageEventArgs args)
        {
            string body = args.Message.Body.ToString();
            Console.WriteLine($"Received: {body}");

            // complete the message. messages is deleted from the queue. 
            await args.CompleteMessageAsync(args.Message);
        }

        // handle any errors when receiving messages
        static Task ErrorHandler(ProcessErrorEventArgs args)
        {
            Console.WriteLine(args.Exception.ToString());
            return Task.CompletedTask;
        }
    ```
1. 将名为 `ReceiveMessagesAsync` 的方法添加到 `Program` 类中，并添加以下代码来接收消息。 

    ```csharp
        static async Task ReceiveMessagesAsync()
        {
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a processor that we can use to process the messages
                ServiceBusProcessor processor = client.CreateProcessor(queueName, new ServiceBusProcessorOptions());

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
1. 从 `ReceiveMessagesAsync` 方法中添加对 `Main` 方法的调用。 如果只想对接收消息进行测试，请注释禁止 `SendMessagesAsync` 方法。 如果不这样做，则会看到发送到队列的其他四条消息。 

    ```csharp
        static async Task Main()
        {
            // send a message to the queue
            await SendMessageAsync();

            // send a batch of messages to the queue
            await SendMessageBatchAsync();

            // receive message from the queue
            await ReceiveMessagesAsync();
        }
    ```

## <a name="run-the-app"></a>运行应用
运行该应用程序。 等待一分钟，然后按任意键停止接收消息。 你应该会看到以下输出（密钥的空格）。 

```console
Sent a single message to the queue: myqueue
Sent a batch of messages to the queue: myqueue
Wait for a minute and then press any key to end the processing
Received: Hello world!
Received: First message in the batch
Received: Second message in the batch
Received: Third message in the batch
Received: Hello world!
Received: First message in the batch
Received: Second message in the batch
Received: Third message in the batch

Stopping the receiver...
Stopped receiving messages
```

再次检查门户。 

- “活动消息计数”和“当前”值现在为 **0**。
- 在“消息”图表中的底部“指标”部分中，可以看到队列有八条传入消息和八条传出消息 。 

    :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/queue-messages-size-final.png" alt-text="接收后的活动消息和大小" lightbox="./media/service-bus-dotnet-get-started-with-queues/queue-messages-size-final.png":::

## <a name="next-steps"></a>后续步骤
请参阅以下文档和示例：

- [适用于 .NET 的 Azure 服务总线客户端库 - 自述文件](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus)
- [GitHub 上的示例](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples)
- [.NET API 参考](/dotnet/api/azure.messaging.servicebus?preserve-view=true)