---
title: 使用 .NET 向/从 Azure 事件中心发送/接收事件（最新版）
description: 本文演练如何创建一个可使用最新 Azure.Messaging.EventHubs 包向/从 Azure 事件中心发送/接收事件的 .NET Core 应用程序。
ms.topic: quickstart
ms.date: 06/10/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 948bacd1506bc65c97c84ea5fa9d3f5b4ad95503
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2021
ms.locfileid: "114730254"
---
# <a name="send-events-to-and-receive-events-from-azure-event-hubs---net-azuremessagingeventhubs"></a>向 Azure 事件中心发送事件及从 Azure 事件中心接收事件 - .NET (Azure.Messaging.EventHubs) 
本快速入门介绍如何使用 Azure.Messaging.EventHubs .NET 库向事件中心发送事件以及从事件中心接收事件。 

## <a name="prerequisites"></a>先决条件
如果不熟悉 Azure 事件中心，请在阅读本快速入门之前参阅[事件中心概述](event-hubs-about.md)。 

若要完成本快速入门，需要具备以下先决条件：

- Microsoft Azure 订阅。 若要使用 Azure 服务（包括 Azure 事件中心），需要一个订阅。  如果没有现有的 Azure 帐户，可以注册[免费试用](https://azure.microsoft.com/free/)帐户，或者在[创建帐户](https://azure.microsoft.com)时使用 MSDN 订阅者权益。
- Microsoft Visual Studio 2019。 Azure 事件中心客户端库利用 C# 8.0 中引入的新功能。  你仍可使用以前的 C# 语言版本的库，但新语法将不可用。 若要使用完整语法，建议使用 [.NET Core SDK](https://dotnet.microsoft.com/download) 3.0 或更高版本进行编译，并将[语言版本](/dotnet/csharp/language-reference/configure-language-version#override-a-default) 设置为 `latest`。 如果使用 Visual Studio，Visual Studio 2019 以前的版本与生成 C# 8.0 项目时所需的工具将不兼容。 可在[此处](https://visualstudio.microsoft.com/vs/)下载 Visual Studio 2019（包括免费的 Community Edition）。
- 创建事件中心命名空间和事件中心。 第一步是使用 [Azure 门户](https://portal.azure.com)创建事件中心类型的命名空间，并获取应用程序与事件中心进行通信所需的管理凭据。 要创建命名空间和事件中心，请按照[此文](event-hubs-create.md)中的步骤操作。 然后，按照以下文章中的说明获取事件中心命名空间的连接字符串：[获取连接字符串](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)。 稍后将在本快速入门中使用连接字符串。

## <a name="send-events"></a>发送事件 
本部分介绍如何创建一个向事件中心发送事件的 .NET Core 控制台应用程序。 

### <a name="create-a-console-application"></a>创建控制台应用程序

1. 启动 Visual Studio 2019。 
1. 选择“创建新项目”。 
1. 在“创建新项目”对话框中执行以下步骤：如果看不到此对话框，请在菜单中选择“文件”，然后依次选择“新建”、“项目”。   
    1. 选择“C#”作为编程语言。
    1. 选择“控制台”作为应用程序类型。 
    1. 从结果列表中选择“控制台应用程序”。 
    1. 然后，选择“下一步”  。 

        :::image type="content" source="./media/getstarted-dotnet-standard-send-v2/new-send-project.png" alt-text="显示“新建项目”对话框的插图":::
1. 输入 EventHubsSender 作为项目名称，输入 EventHubsQuickStart 作为解决方案名称，然后选择“确定”以创建项目。 

    :::image type="content" source="./media/getstarted-dotnet-standard-send-v2/project-solution-names.png" alt-text="显示可在其中输入解决方案和项目名称的页面的插图":::

### <a name="add-the-event-hubs-nuget-package"></a>添加事件中心 NuGet 包

1. 在菜单中选择“工具” > “NuGet 包管理器” > “包管理器控制台”。 
1. 运行以下命令安装 Azure.Messaging.EventHubs NuGet 包：

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```


### <a name="write-code-to-send-events-to-the-event-hub"></a>编写代码以将事件发送到事件中心

1. 在 Program.cs 文件顶部添加以下 `using` 语句：

    ```csharp
    using System;
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Producer;
    ```

2. 将事件中心连接字符串和事件中心名称的常量添加到 `Program` 类。  

    ```csharp
        // connection string to the Event Hubs namespace
        private const string connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";

        // name of the event hub
        private const string eventHubName = "<EVENT HUB NAME>";

        // number of events to be sent to the event hub
        private const int numOfEvents = 3;
    ```

    > [!NOTE]
    > 请将占位符值替换为命名空间的连接字符串和事件中心名称。 确保连接字符串是命名空间级别的连接字符串。
3. 将以下静态属性添加到 `Program` 类。 查看代码注释。 

    ```csharp
        // The Event Hubs client types are safe to cache and use as a singleton for the lifetime
        // of the application, which is best practice when events are being published or read regularly.
        static EventHubProducerClient producerClient;    
    ```
1. 将 `Main` 方法替换为以下 `async Main` 方法。 参阅代码注释了解详细信息。 

    ```csharp
        static async Task Main()
        {
            // Create a producer client that you can use to send events to an event hub
            producerClient = new EventHubProducerClient(connectionString, eventHubName);

            // Create a batch of events 
            using EventDataBatch eventBatch = await producerClient.CreateBatchAsync();

            for (int i = 1; i <= numOfEvents; i++)
            {
                if (! eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes($"Event {i}"))))
                {
                    // if it is too large for the batch
                    throw new Exception($"Event {i} is too large for the batch and cannot be sent.");
                }
            }

            try
            {
                // Use the producer client to send the batch of events to the event hub
                await producerClient.SendAsync(eventBatch);
                Console.WriteLine($"A batch of {numEvents} events has been published.");
            }
            finally
            {
                await producerClient.DisposeAsync();
            }
        }
    ```
5. 生成项目并确保没有错误。
6. 运行程序并等待出现确认消息。 

    ```csharp
    A batch of 3 events has been published.
    ```
1. 在 Azure 门户中，可以验证事件中心是否已收到事件。 在“指标”部分切换到“消息”视图。 刷新页面以更新图表。 可能需要在几秒钟后才会显示已收到消息。 

    :::image type="content" source="./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png" alt-text="用于验证事件中心是否已收到事件的 Azure 门户页的插图" lightbox="./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png":::

    > [!NOTE]
    > 有关包含更详细注释的完整源代码，请参阅 [GitHub 上的此文件](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/samples/Sample04_PublishingEvents.md)

## <a name="receive-events"></a>接收事件
本部分介绍如何编写一个使用事件处理程序从事件中心接收事件的 .NET Core 控制台应用程序。 该事件处理器通过从事件中心管理持久检查点和并行接收操作，来简化从这些事件中心接收事件的过程。 事件处理器与特定的事件中心和使用者组相关联。 它从事件中心内的多个分区接收事件，并将其传递给处理程序委托，以使用提供的代码进行处理。 


> [!WARNING]
> 如果在 Azure Stack Hub 上运行此代码，除非将特定的存储 API 版本作为目标，否则会遇到运行时错误。 这是因为事件中心 SDK 使用 Azure 中提供的最新 Azure 存储 API，而此 API 可能在 Azure Stack Hub 平台上不可用。 Azure Stack Hub 支持的存储 Blob SDK 版本可能与 Azure 上通常提供的版本不同。 如果你正在将 Azure Blob 存储用作检查点存储，请查看[Azure Stack Hub 内部版本支持的 Azure 存储 API 版本](/azure-stack/user/azure-stack-acs-differences?#api-version)，并在代码中面向此版本。 
>
> 例如，如果在 Azure Stack Hub 版本 2005 上运行，则存储服务的最高可用版本为版本 2019-02-02。 默认情况下，事件中心 SDK 客户端库使用 Azure 上的最高可用版本（在 SDK 发布时为 2019-07-07）。 在这种情况下，除了执行本部分中的步骤以外，还需要添加相关代码，将存储服务 API 版本 2019-02-02 作为目标。 如需通过示例来了解如何以特定的存储 API 版本为目标，请参阅 [GitHub 上的此示例](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/)。 
 

### <a name="create-an-azure-storage-and-a-blob-container"></a>创建 Azure 存储和 Blob 容器
本快速入门使用 Azure 存储作为检查点存储。 按照以下步骤创建 Azure 存储帐户。 

1. [创建 Azure 存储帐户](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [创建一个 blob 容器](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [获取存储帐户的连接字符串](../storage/common/storage-configure-connection-string.md)

    请记下该连接字符串和容器名称。 稍后要在接收代码中使用这些信息。 


### <a name="create-a-project-for-the-receiver"></a>为接收器创建项目

1. 在“解决方案资源管理器”窗口中，右键单击“EventHubQuickStart”解决方案，指向“添加”，然后选择“新建项目”。 
1. 依次选择“控制台应用程序”、“下一步” 。 
1. 输入 EventHubsReceiver 作为“项目名称”，然后选择“创建”。 
1. 在“解决方案资源管理器”窗口中，右键单击“EventHubsReceiver”并选择“设为启动项目”  。 

### <a name="add-the-event-hubs-nuget-package"></a>添加事件中心 NuGet 包

1. 在菜单中选择“工具” > “NuGet 包管理器” > “包管理器控制台”。 
1. 在“包管理器控制台”窗口中，确认为“默认项目”选择了“EventHubsReceiver”  。 如果不是，请使用下拉列表选择“EventHubsReceiver”。
1. 运行以下命令安装 Azure.Messaging.EventHubs NuGet 包：

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```
1. 运行以下命令安装 Azure.Messaging.EventHubs.Processor NuGet 包：

    ```cmd
    Install-Package Azure.Messaging.EventHubs.Processor
    ```    

### <a name="update-the-main-method"></a>更新 Main 方法 

1. 在 Program.cs 文件顶部添加以下 `using` 语句。

    ```csharp
    using System;
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Storage.Blobs;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Consumer;
    using Azure.Messaging.EventHubs.Processor;
    ```
1. 将事件中心连接字符串和事件中心名称的常量添加到 `Program` 类。 请将括号中的占位符替换为在创建事件中心时获取的适当值。 请将括号中的占位符替换为创建事件中心和存储帐户时获取的适当值（访问密钥 - 主连接字符串）。 请确保 `{Event Hubs namespace connection string}` 是命名空间级别的连接字符串，而不是事件中心字符串。

    ```csharp
        private const string ehubNamespaceConnectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
        private const string eventHubName = "<EVENT HUB NAME>";
        private const string blobStorageConnectionString = "<AZURE STORAGE CONNECTION STRING>";
        private const string blobContainerName = "<BLOB CONTAINER NAME>";
    ```
3. 将以下静态属性添加到 `Program` 类。 

    ```csharp
        static BlobContainerClient storageClient;

        // The Event Hubs client types are safe to cache and use as a singleton for the lifetime
        // of the application, which is best practice when events are being published or read regularly.        
        static EventProcessorClient processor;    
    ```
1. 将 `Main` 方法替换为以下 `async Main` 方法。 参阅代码注释了解详细信息。 

    ```csharp
        static async Task Main()
        {
            // Read from the default consumer group: $Default
            string consumerGroup = EventHubConsumerClient.DefaultConsumerGroupName;

            // Create a blob container client that the event processor will use 
            storageClient = new BlobContainerClient(blobStorageConnectionString, blobContainerName);

            // Create an event processor client to process events in the event hub
            processor = new EventProcessorClient(storageClient, consumerGroup, ehubNamespaceConnectionString, eventHubName);

            // Register handlers for processing events and handling errors
            processor.ProcessEventAsync += ProcessEventHandler;
            processor.ProcessErrorAsync += ProcessErrorHandler;

            // Start the processing
            await processor.StartProcessingAsync();

            // Wait for 30 seconds for the events to be processed
            await Task.Delay(TimeSpan.FromSeconds(30));

            // Stop the processing
            await processor.StopProcessingAsync();
        }
    ```
1. 现在，将以下事件和错误处理程序方法添加到类中。 

    ```csharp
        static async Task ProcessEventHandler(ProcessEventArgs eventArgs)
        {
            // Write the body of the event to the console window
            Console.WriteLine("\tReceived event: {0}", Encoding.UTF8.GetString(eventArgs.Data.Body.ToArray()));

            // Update checkpoint in the blob storage so that the app receives only new events the next time it's run
            await eventArgs.UpdateCheckpointAsync(eventArgs.CancellationToken);
        }

        static Task ProcessErrorHandler(ProcessErrorEventArgs eventArgs)
        {
            // Write details about the error to the console window
            Console.WriteLine($"\tPartition '{ eventArgs.PartitionId}': an unhandled exception was encountered. This was not expected to happen.");
            Console.WriteLine(eventArgs.Exception.Message);
            return Task.CompletedTask;
        }    
    ```
1. 生成项目并确保没有错误。

    > [!NOTE]
    > 有关包含更详细注释的完整源代码，请参阅 [GitHub 上的此文件](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample01_HelloWorld.md)。
6. 运行接收器应用程序。 
1. 应会看到一条消息，指出已收到事件。 

    ```bash
    Received event: Event 1
    Received event: Event 2
    Received event: Event 3    
    ```
    这些事件是前面通过运行发送器程序发送到事件中心的三个事件。 


## <a name="next-steps"></a>后续步骤
查看 GitHub 上的示例。 

- [GitHub 上的事件中心示例](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples)
- [GitHub 上的事件处理器示例](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples)
- [Azure 基于角色的访问控制 (Azure RBAC) 示例](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)
