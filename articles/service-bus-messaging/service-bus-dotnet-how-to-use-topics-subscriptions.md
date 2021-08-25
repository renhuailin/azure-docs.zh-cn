---
title: Azure 服务总线主题和订阅入门
description: 本快速入门介绍如何使用 azure-messaging-servicebus 包将消息发送到 Azure 服务总线主题。
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 06/29/2021
ms.custom: contperf-fy21q3
ms.openlocfilehash: 8a53a8099df37ec3f24489cccaf91af2d53c97ce
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747126"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-its-subscriptions-net"></a>向 Azure 服务总线主题发送消息，并从其订阅接收消息 (.NET)
本快速入门介绍如何使用 [Azure.Messaging.ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus/) .NET 库向服务总线主题发送消息和接收有关该主题订阅的消息。

## <a name="prerequisites"></a>先决条件
如果你是首次使用该服务，请在使用本快速入门之前先参阅[服务总线概述](service-bus-messaging-overview.md)。 

- **Azure 订阅**。 若要使用 Azure 服务（包括 Azure 服务总线），需要一个订阅。  如果没有现有的 Azure 帐户，可以注册[免费试用](https://azure.microsoft.com/free/)帐户，或者在[创建帐户](https://azure.microsoft.com)时使用 MSDN 订阅者权益。
- Microsoft Visual Studio 2019。 Azure 服务总线客户端库利用 C# 8.0 中引入的新功能。  你仍可使用以前的 C# 语言版本的库，但新语法将不可用。 若要使用完整语法，建议使用 [.NET Core SDK](https://dotnet.microsoft.com/download) 3.0 或更高版本进行编译，并将[语言版本](/dotnet/csharp/language-reference/configure-language-version#override-a-default)设置为 `latest`。 如果使用 Visual Studio，Visual Studio 2019 以前的版本与生成 C# 8.0 项目时所需的工具将不兼容。 可在[此处](https://visualstudio.microsoft.com/vs/)下载 Visual Studio 2019（包括免费的 Community Edition）。
- 遵循这一[快速入门](service-bus-quickstart-topics-subscriptions-portal.md)中的步骤，创建一个“服务总线”主题和多个对该主题的订阅。 

    > [!IMPORTANT]
    > 记录到命名空间的连接字符串、主题名称和某个主题订阅的名称。 本教程后面会用到它们。
 
## <a name="send-messages-to-a-topic"></a>将消息发送到主题
本部分介绍如何创建一个向服务总线主题发送消息的 .NET Core 控制台应用程序。 

### <a name="create-a-console-application"></a>创建控制台应用程序

1. 启动 Visual Studio 2019。 
1. 选择“创建新项目”。 
1. 在“创建新项目”对话框中执行以下步骤：如果看不到此对话框，请在菜单中选择“文件”，然后依次选择“新建”、“项目”。   
    1. 选择“C#”作为编程语言。
    1. 选择“控制台”作为应用程序类型。 
    1. 从结果列表中选择“控制台应用程序”。 
    1. 然后，选择“下一步”  。 

        :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/new-send-project.png" alt-text="显示使用 C# 和所选的控制台创建新项目对话框的图像":::
1. 输入 TopicSender 作为项目名称，输入 ServiceBusTopicQuickStart 作为解决方案名称，然后选择“下一步”。 
1. 在“其他信息”页面，选择“创建”来创建解决方案和项目。

### <a name="add-the-service-bus-nuget-package"></a>添加服务总线 NuGet 包

1. 在菜单中选择“工具” > “NuGet 包管理器” > “包管理器控制台”。 
1. 运行以下命令安装 Azure.Messaging.ServiceBus NuGet 包：

    ```cmd
    Install-Package Azure.Messaging.ServiceBus
    ```

### <a name="add-code-to-send-messages-to-the-topic"></a>添加将消息发送到主题的代码 

1. 将 Program.cs 中的代码替换为以下代码。 下面是代码中的重要步骤。  
    1. 使用命名空间的连接字符串创建 [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) 对象。 
    1. 对 `ServiceBusClient` 对象调用 `CreateSender` 方法，从而为特定的“服务总线”主题创建 [ServiceBusSender](/dotnet/api/azure.messaging.servicebus.servicebussender) 对象。     
    1. 使用 `ServiceBusSender.CreateMessageBatchAsync` 方法创建 `ServiceBusMessageBatch` 对象。
    1. 使用 `ServiceBusMessageBatch.TryAddMessage` 方法将消息添加到该批。 
    1. 使用 `ServiceBusSender.SendMessagesAsync` 方法将批量消息发送到“服务总线”主题。
    
        有关更多信息，请参阅代码注释。
        ```csharp
        using System;
        using System.Threading.Tasks;
        using Azure.Messaging.ServiceBus;
        
        namespace TopicSender
        {
            class Program
            {
                // connection string to your Service Bus namespace
                static string connectionString = "<NAMESPACE CONNECTION STRING>";
        
                // name of your Service Bus topic
                static string topicName = "<TOPIC NAME>";
        
                // the client that owns the connection and can be used to create senders and receivers
                static ServiceBusClient client;
        
                // the sender used to publish messages to the topic
                static ServiceBusSender sender;
        
                // number of messages to be sent to the topic
                private const int numOfMessages = 3;
        
                static async Task Main()
                {
                    // The Service Bus client types are safe to cache and use as a singleton for the lifetime
                    // of the application, which is best practice when messages are being published or read
                    // regularly.
                    //
                    // Create the clients that we'll use for sending and processing messages.
                    client = new ServiceBusClient(connectionString);
                    sender = client.CreateSender(topicName);
        
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
                        // Use the producer client to send the batch of messages to the Service Bus topic
                        await sender.SendMessagesAsync(messageBatch);
                        Console.WriteLine($"A batch of {numOfMessages} messages has been published to the topic.");
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
1. 将 `<NAMESPACE CONNECTION STRING>` 替换为服务总线命名空间的连接字符串。 此外，将 `<TOPIC NAME>` 替换为你的服务总线主题的名称。 
1. 生成项目并确保没有错误。 
1. 运行程序并等待出现确认消息。
    
    ```bash
    A batch of 3 messages has been published to the topic
    ```
1. 在 Azure 门户中按照以下步骤操作：
    1. 导航到服务总线命名空间。 
    1. 在“概述”页底部居中位置的窗格中，切换到“主题”选项卡，然后选择“服务总线”主题 。 下面的示例中采用的是 `mytopic`。
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/select-topic.png" alt-text="选择主题":::
    1. 在“服务总线主题”页的“消息”图表中的底部“指标”部分中，可以看到主题有三条传入的消息。 如果未看到该值，请等待几分钟，然后刷新页面以查看更新后的图表。 

        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/sent-messages-essentials.png" alt-text="发送到主题的消息" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/sent-messages-essentials.png":::
    4. 在底部窗格中选择订阅。 下面的示例中采用的是 S1。 在“服务总线订阅”页面上，你会看到“活动消息计数”为 3。 订阅已接收你发送到主题的三条消息，但接收方尚未选择它们。 
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page.png" alt-text="订阅中接收的消息" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page.png":::
    
## <a name="receive-messages-from-a-subscription"></a>从订阅接收消息
在本节中，你将会创建一个 .NET Core 控制台应用程序，用于接收来自服务总线主题订阅的消息。 

### <a name="create-a-project-for-the-receiver"></a>为接收器创建项目

1. 在“解决方案资源管理器”窗口中，右键单击“ServiceBusTopicQuickStart”解决方案，指向“添加”，然后选择“新建项目”。 
1. 选择“控制台应用程序”，然后选择“下一步”。 
1. 输入 SubscriptionReceiver 作为“项目名称”，然后选择“下一步”。 
1. 在“其他信息”页上，选择“创建”。 
1. 在“解决方案资源管理器”窗口中，右键单击“SubscriptionReceiver”，然后选择“设为启动项目”。 

### <a name="add-the-service-bus-nuget-package"></a>添加服务总线 NuGet 包

1. 在菜单中选择“工具” > “NuGet 包管理器” > “包管理器控制台”。 
1. 在“包管理器控制台”窗口中，确认“SubscriptionReceiver”已选定为“默认项目”。 如果不是，请使用下拉列表选择“SubscriptionReceiver”。
1. 运行以下命令安装 Azure.Messaging.ServiceBus NuGet 包：

    ```cmd
    Install-Package Azure.Messaging.ServiceBus
    ```

### <a name="add-code-to-receive-messages-from-the-subscription"></a>添加代码以从订阅接收消息
1. 将 Program.cs 中的代码替换为以下代码。 下面是代码中的重要步骤。
    下面是代码中的重要步骤：
    1. 使用命名空间的连接字符串创建 [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) 对象。 
    1. 对 `ServiceBusClient` 对象调用 `CreateProcessor` 方法，从而为指定的“服务总线”队列创建 [ServiceBusProcessor](/dotnet/api/azure.messaging.servicebus.servicebusprocessor) 对象。 
    1. 为 `ServiceBusProcessor` 对象的 `ProcessMessageAsync` 和 `ProcessErrorAsync` 事件指定处理程序。 
    1. 通过对 `ServiceBusProcessor` 对象调用 `StartProcessingAsync` 以开始处理消息。 
    1. 当用户按下某个键结束处理时，将对 `ServiceBusProcessor` 对象调用 `StopProcessingAsync`。 

        有关更多信息，请参阅代码注释。

        ```csharp
        using System;
        using System.Threading.Tasks;
        using Azure.Messaging.ServiceBus;
        
        namespace SubscriptionReceiver
        {
            class Program
            {
                // connection string to your Service Bus namespace
                static string connectionString = "<NAMESPACE CONNECTION STRING>";
        
                // name of the Service Bus topic
                static string topicName = "<SERVICE BUS TOPIC NAME>";
            
                // name of the subscription to the topic
                static string subscriptionName = "<SERVICE BUS - TOPIC SUBSCRIPTION NAME>";
        
                // the client that owns the connection and can be used to create senders and receivers
                static ServiceBusClient client;
        
                // the processor that reads and processes messages from the subscription
                static ServiceBusProcessor processor;
        
                // handle received messages
                static async Task MessageHandler(ProcessMessageEventArgs args)
                {
                    string body = args.Message.Body.ToString();
                    Console.WriteLine($"Received: {body} from subscription: {subscriptionName}");
        
                    // complete the message. messages is deleted from the subscription. 
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
                    // Create the clients that we'll use for sending and processing messages.
                    client = new ServiceBusClient(connectionString);
        
                    // create a processor that we can use to process the messages
                    processor = client.CreateProcessor(topicName, subscriptionName, new ServiceBusProcessorOptions());
        
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
1. 将占位符替换为正确的值：
    - 将 `<NAMESPACE CONNECTION STRING>` 替换为服务总线命名空间的连接字符串
    - 将 `<TOPIC NAME>` 替换为你的服务总线主题的名称
    - 将 `<SERVICE BUS - TOPIC SUBSCRIPTION NAME>` 替换为主题的订阅名称。 
1. 生成项目并确保没有错误。
1. 运行接收器应用程序。 你应该会看到接收的消息。 按任意键来停止使用接收器和应用程序。 

    ```console
    Wait for a minute and then press any key to end the processing
    Received: Message 1 from subscription: S1
    Received: Message 2 from subscription: S1
    Received: Message 3 from subscription: S1
    
    Stopping the receiver...
    Stopped receiving messages
    ```
1. 再次检查门户。 
    - 在“服务总线主题”页上的“消息”图表中，你会看到三条传入消息和三条传出消息。 如果未看到这些值，请等待几分钟，然后刷新页面以查看更新后的图表。 
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/messages-size-final.png" alt-text="发送和接收的消息" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/messages-size-final.png":::
    - 在“服务总线订阅”页面上，你会看到“活动消息计数”为零 。 这是因为接收方已接收并已完成来自此订阅的消息。 
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page-final.png" alt-text="末尾订阅中的活动消息计数" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page-final.png":::
        


## <a name="next-steps"></a>后续步骤
请参阅以下文档和示例：

- [适用于 .NET 的 Azure 服务总线客户端库 - 自述文件](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus)
- [GitHub 上的 Azure 服务总线的 .NET 示例](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples)
- [.NET API 参考](/dotnet/api/azure.messaging.servicebus?preserve-view=true)
