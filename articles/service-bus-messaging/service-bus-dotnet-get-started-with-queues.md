---
title: Azure 服务总线队列入门 (Azure.Messaging.ServiceBus)
description: 在本教程中，你将创建 .NET Core C# 应用程序，用于向/从服务总线队列发送/接收消息。
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 08/16/2021
ms.custom: contperf-fy21q4
ms.openlocfilehash: b84ff908404c2d18f86ddd63fa14a9854fdf72d6
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2021
ms.locfileid: "129079326"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-net"></a>向/从服务总线队列发送/接收消息 (.NET)
本快速入门指南显示如何使用 [Azure.Messaging.ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus/) .NET 库向服务总线队列发送消息和接收来自该队列的消息。

> [!NOTE]
> 可以在 [GitHub 上用于 .NET 的 Azure SDK 存储库中](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples)找到更多 Azure 服务总线的 .NET 示例。

## <a name="prerequisites"></a>先决条件

- **Azure 订阅**。 若要使用 Azure 服务（包括 Azure 服务总线），需要一个订阅。 如果没有现成的 Azure 帐户，可以注册[免费试用版](https://azure.microsoft.com/free/)。
- Microsoft Visual Studio 2019。 Azure 服务总线客户端库利用 C# 8.0 中引入的新功能。  你仍可使用以前的 C# 语言版本的库，但新语法将不可用。 若要使用完整语法，建议使用 .NET Core SDK 3.0 或更高版本进行编译，并将语言版本设置为 `latest`。 如果使用 Visual Studio，Visual Studio 2019 以前的版本与生成 C# 8.0 项目时所需的工具将不兼容。
- **创建服务总线命名空间和队列**。 按照[使用 Azure 门户创建服务总线队列](service-bus-quickstart-portal.md)一文的步骤创建服务总线命名空间和队列。 

    > [!IMPORTANT]
    > 记下服务总线命名空间的 [**主连接字符串**](./service-bus-quickstart-topics-subscriptions-portal.md#get-the-connection-string)以及创建的队列的名称。 本教程后面会用到它们。 


## <a name="send-messages"></a>发送消息
本部分介绍如何创建一个向服务总线队列发送消息的 .NET Core 控制台应用程序。 

### <a name="create-a-console-application"></a>创建控制台应用程序

1. 启动 Visual Studio 2019。 
1. 选择“创建新项目”。 
1. 在“创建新项目”对话框中执行以下步骤：如果看不到此对话框，请在菜单中选择“文件”，然后依次选择“新建”、“项目”。   
    1. 选择“C#”作为编程语言。
    1. 选择“控制台”作为应用程序类型。 
    1. 从结果列表中选择“控制台应用程序”。 
    1. 然后，选择“下一步”  。 

        :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/new-send-project.png" alt-text="显示使用 C# 和所选的控制台创建新项目对话框的图像":::
1. 输入 **QueueSender** 作为项目名称，输入 **ServiceBusQueueQuickStart** 作为解决方案名称，然后选择 **下一步**。 

    :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/project-solution-names.png" alt-text="在“配置你的新项目对话框”中显示解决方案和项目名称的图像 ":::
1. 在 **其他信息** 页面，选择 **创建** 来创建解决方案和项目。 

### <a name="add-the-service-bus-nuget-package"></a>添加服务总线 NuGet 包

1. 在菜单中选择“工具” > “NuGet 包管理器” > “包管理器控制台”。 
1. 运行以下命令安装 **Azure.Messaging.ServiceBus**  NuGet 包：

    ```cmd
    Install-Package Azure.Messaging.ServiceBus
    ```

### <a name="add-code-to-send-messages-to-the-queue"></a>添加将消息发送到队列的代码

1. 将 Program.cs 中的代码替换为以下代码。 下面是代码中的重要步骤。  
    1. 使用命名空间的主连接字符串创建 [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) 对象。 
    1. 对 [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) 对象调用 [CreateSender](/dotnet/api/azure.messaging.servicebus.servicebusclient.createsender) 方法，从而为特定的“服务总线”队列创建 [ServiceBusSender](/dotnet/api/azure.messaging.servicebus.servicebussender) 对象。     
    1. 使用 [ServiceBusSender.CreateMessageBatchAsync](/dotnet/api/azure.messaging.servicebus.servicebussender.createmessagebatchasync) 方法创建 [ServiceBusMessageBatch](/dotnet/api/azure.messaging.servicebus.servicebusmessagebatch) 对象。
    1. 使用 [ServiceBusMessageBatch.TryAddMessage](/dotnet/api/azure.messaging.servicebus.servicebusmessagebatch.tryaddmessage) 将消息添加到该批次。 
    1. 使用 [ServiceBusSender.SendMessagesAsync](/dotnet/api/azure.messaging.servicebus.servicebussender.sendmessagesasync) 方法将批量消息发送到“服务总线”队列。
    
        有关更多信息，请参见代码注释。
    
        ```csharp
        using System;
        using System.Threading.Tasks;
        using Azure.Messaging.ServiceBus;
        
        namespace QueueSender
        {
            class Program
            {
                // connection string to your Service Bus namespace
                static string connectionString = "<NAMESPACE CONNECTION STRING>";
    
                // name of your Service Bus queue
                static string queueName = "<QUEUE NAME>";
        
                // the client that owns the connection and can be used to create senders and receivers
                static ServiceBusClient client;
        
                // the sender used to publish messages to the queue
                static ServiceBusSender sender;
        
                // number of messages to be sent to the queue
                private const int numOfMessages = 3;
        
                static async Task Main()
                {
                    // The Service Bus client types are safe to cache and use as a singleton for the lifetime
                    // of the application, which is best practice when messages are being published or read
                    // regularly.
                    //
                    // Create the clients that we'll use for sending and processing messages.
                    client = new ServiceBusClient(connectionString);
                    sender = client.CreateSender(queueName);
        
                    // create a batch 
                    using ServiceBusMessageBatch messageBatch = await sender.CreateMessageBatchAsync();
        
                    for (int i = 1; i <= numOfMessages; i++)
                    {
                        // try adding a message to the batch
                        if (!messageBatch.TryAddMessage(new ServiceBusMessage($"Message {i}")))
                        {
                            // if it is too large for the batch
                            throw new Exception($"The message {i} is too large to fit in the batch.");
                        }
                    }
        
                    try
                    {
                        // Use the producer client to send the batch of messages to the Service Bus queue
                        await sender.SendMessagesAsync(messageBatch);
                        Console.WriteLine($"A batch of {numOfMessages} messages has been published to the queue.");
                    }
                    finally
                    {
                        // Calling DisposeAsync on client types is required to ensure that network
                        // resources and other unmanaged objects are properly cleaned up.
                        await sender.DisposeAsync();
                        await client.DisposeAsync();
                    }
        
                    Console.WriteLine("Press any key to end the application");
                    Console.ReadKey();
                }
            }
        }   
        ``` 
1. 将 `<NAMESPACE CONNECTION STRING>` 替换为服务总线命名空间的主连接字符串。 并将 `<QUEUE NAME>` 替换为你的队列的名称。
1. 生成项目并确保没有错误。 
1. 运行程序并等待出现确认消息。
    
    ```bash
    A batch of 3 messages has been published to the queue
    ```
1. 在 Azure 门户中按照以下步骤操作：
    1. 导航到服务总线命名空间。 
    1. 在“概述”页上，在底部中间的窗格中选择队列。 
    
        :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/select-queue.png" alt-text="按照所选队列在 Azure 门户中显示服务总线命名空间页面的图像。" lightbox="./media/service-bus-dotnet-get-started-with-queues/select-queue.png":::
    1. 请注意“必备”部分中的值。

    :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/sent-messages-essentials.png" alt-text="显示接收的消息数量和队列大小的图像" lightbox="./media/service-bus-dotnet-get-started-with-queues/sent-messages-essentials.png":::

    请注意以下值：
    - 队列的 **活动** 消息计数值现在为 **3**。 每次运行此发件人应用而不检索消息时，该值会增加 3。
    - 每次应用向队列添加消息时，队列的 **当前大小** 都会递增。
    - 在 **消息** 图表中的底部 **指标** 部分中，可以看到队列有三条传入的消息。 

## <a name="receive-messages"></a>接收消息
在本节中，你将会创建一个 .NET Core 控制台应用，用于接收来自队列的消息。 

### <a name="create-a-project-for-the-receiver"></a>为接收器创建项目

1. 在“解决方案资源管理器”窗口中，右键单击 **ServiceBusQueueQuickStart** 解决方案，指向 **添加**，然后选择 **新建项目**。 
1. 选择 **控制台应用程序**，然后选择 **下一步**。 
1. 输入 **QueueReceiver** 作为 **项目名称**，然后选择 **创建**。 
1. 在 **解决方案资源管理器** 窗口中，右键单击 **QueueReceiver**，然后选择 **设为启动项目**。 

### <a name="add-the-service-bus-nuget-package"></a>添加服务总线 NuGet 包

1. 在菜单中选择“工具” > “NuGet 包管理器” > “包管理器控制台”。 
1. 在 **程序包管理器控制台** 窗口中，确认 **QueueReceiver** 选定用于 **默认项目**。 如果不是，请使用下拉列表选择 **QueueReceiver**。

    :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/package-manager-console.png" alt-text="显示在“包管理器”控制台中选择了 QueueReceiver 项目的屏幕截图":::
1. 运行以下命令安装 **Azure.Messaging.ServiceBus**  NuGet 包：

    ```cmd
    Install-Package Azure.Messaging.ServiceBus
    ```

### <a name="add-the-code-to-receive-messages-from-the-queue"></a>添加从队列接收消息的代码
在本部分中，你将添加从队列检索消息的代码。

1. 将 Program.cs 中的代码替换为以下代码。 下面是代码中的重要步骤。
    下面是代码中的重要步骤：
    1. 使用命名空间的主连接字符串创建 [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) 对象。 
    1. 对 [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) 对象调用 [CreateProcessor](/dotnet/api/azure.messaging.servicebus.servicebusclient.createprocessor) 方法，从而为指定的“服务总线”队列创建 [ServiceBusProcessor](/dotnet/api/azure.messaging.servicebus.servicebusprocessor) 对象。 
    1. 为 [ServiceBusProcessor](/dotnet/api/azure.messaging.servicebus.servicebusprocessor) 对象的 [ProcessMessageAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processmessageasync) 和 [ProcessErrorAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processerrorasync) 事件指定处理程序。 
    1. 通过对 [ServiceBusProcessor](/dotnet/api/azure.messaging.servicebus.servicebusprocessor) 对象调用 [StartProcessingAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.startprocessingasync) 以开始处理消息。 
    1. 当用户按下某个键结束处理时，将对 [ServiceBusProcessor](/dotnet/api/azure.messaging.servicebus.servicebusprocessor) 对象调用 [StopProcessingAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.stopprocessingasync)。 

        有关更多信息，请参阅代码注释。

        ```csharp
        using System;
        using System.Threading.Tasks;
        using Azure.Messaging.ServiceBus;
        
        namespace QueueReceiver
        {
            class Program
            {
                // connection string to your Service Bus namespace
                static string connectionString = "<NAMESPACE CONNECTION STRING>";
        
                // name of your Service Bus queue
                static string queueName = "<QUEUE NAME>";
        
        
                // the client that owns the connection and can be used to create senders and receivers
                static ServiceBusClient client;
        
                // the processor that reads and processes messages from the queue
                static ServiceBusProcessor processor;
        
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
        
                static async Task Main()
                {
                    // The Service Bus client types are safe to cache and use as a singleton for the lifetime
                    // of the application, which is best practice when messages are being published or read
                    // regularly.
                    //
        
                    // Create the client object that will be used to create sender and receiver objects
                    client = new ServiceBusClient(connectionString);
        
                    // create a processor that we can use to process the messages
                    processor = client.CreateProcessor(queueName, new ServiceBusProcessorOptions());
        
                    try
                    {
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
                    finally
                    {
                        // Calling DisposeAsync on client types is required to ensure that network
                        // resources and other unmanaged objects are properly cleaned up.
                        await processor.DisposeAsync();
                        await client.DisposeAsync();
                    }
                }
            }
        }
        ```
1. 将 `<NAMESPACE CONNECTION STRING>` 替换为服务总线命名空间的主连接字符串。 并将 `<QUEUE NAME>` 替换为你的队列的名称。 
1. 生成项目并确保没有错误。
1. 运行接收器应用程序。 你应该会看到接收的消息。 按任意键来停止使用接收器和应用程序。 

    ```console
    Wait for a minute and then press any key to end the processing
    Received: Message 1
    Received: Message 2
    Received: Message 3
    
    Stopping the receiver...
    Stopped receiving messages
    ```
1. 再次检查门户。 等待几分钟，如果未看到 **活动** 消息的 `0`，请刷新页面。 

    - **活动** 消息计数和 **当前大小** 值现为 **0**。
    - 在“消息”图表中的底部“指标”部分中，可以看到队列有八条传入消息和八条传出消息 。 
    
        :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/queue-messages-size-final.png" alt-text="接收后的活动消息和大小" lightbox="./media/service-bus-dotnet-get-started-with-queues/queue-messages-size-final.png":::


## <a name="next-steps"></a>后续步骤
请参阅以下文档和示例：

- [适用于 .NET 的 Azure 服务总线客户端库 - 自述文件](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus)
- [GitHub 上的示例](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples)
- [.NET API 参考](/dotnet/api/azure.messaging.servicebus)
