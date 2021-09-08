---
title: 使用 .NET 通过事件中心将消息发布到 Azure Purview 的Atlas Kafka 主题并处理来自这些主题的消息
description: 本文提供创建 .NET Core 应用程序的演练，该应用程序使用最新 Azure.Messaging.EventHubs 包向 Purview 的 Apache Atlas Kafka 主题发送事件或由其接收事件。
ms.topic: quickstart
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.devlang: dotnet
ms.date: 04/15/2021
ms.openlocfilehash: 468c02d9384fb2c8a4723c179fde9a44be76dd50
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2021
ms.locfileid: "123476279"
---
# <a name="publish-messages-to-and-process-messages-from-azure-purviews-atlas-kafka-topics-via-event-hubs-using-net"></a>使用 .NET 通过事件中心将消息发布到 Azure Purview 的Atlas Kafka 主题并处理来自这些主题的消息 
本快速入门介绍如何使用 Azure.Messaging.EventHubs .NET 库通过事件中心向 Azure Purview 的 Atlas Kafka 主题发送事件以及由其接收事件。 

> [!IMPORTANT]
> 创建 Purview 帐户时将创建托管事件中心，详见[监控范围帐户的创建](create-catalog-portal.md)。 你可以将消息发布到事件中心 kafka 主题 ATLAS_HOOK，Purview 使用和处理它。 Purview 将通知对事件中心 kafka 主题 ATLAS_ENTITIES 的实体更改，用户可以使用和处理这些更改。此快速入门使用新的 Azure.Messaging.EventHubs 库。 


## <a name="prerequisites"></a>先决条件
如果不熟悉 Azure 事件中心，请在阅读本快速入门之前参阅[事件中心概述](../event-hubs/event-hubs-about.md)。 

若要完成本快速入门，需要具备以下先决条件：

- Microsoft Azure 订阅。 若要使用 Azure 服务（包括 Azure 事件中心），需要一个订阅。  如果没有现有的 Azure 帐户，可以注册[免费试用](https://azure.microsoft.com/free/)帐户，或者在[创建帐户](https://azure.microsoft.com)时使用 MSDN 订阅者权益。
- Microsoft Visual Studio 2019。 Azure 事件中心客户端库利用 C# 8.0 中引入的新功能。  你仍可使用以前的 C# 语言版本的库，但新语法将不可用。 若要使用完整语法，建议使用 [.NET Core SDK](https://dotnet.microsoft.com/download) 3.0 或更高版本进行编译，并将[语言版本](/dotnet/csharp/language-reference/configure-language-version#override-a-default) 设置为 `latest`。 如果使用 Visual Studio，Visual Studio 2019 以前的版本与生成 C# 8.0 项目时所需的工具将不兼容。 可在[此处](https://visualstudio.microsoft.com/vs/)下载 Visual Studio 2019（包括免费的 Community Edition）。

## <a name="publish-messages-to-purview"></a>将消息发布到 Purview 
本部分介绍如何创建 .NET Core 控制台应用程序，通过事件中心 kafka 主题 ATLAS_HOOK 向 Purview 发送事件。 

## <a name="create-a-visual-studio-project"></a>创建 Visual Studio 项目

接下来，在 Visual Studio 中创建 C# 控制台应用程序：

1. 启动 **Visual Studio**。
2. 在“开始”窗口中，选择“创建新项目” > “控制台应用(.NET Framework)” 。 需要 .NET 4.5.2 或更高版本。
3. 在“项目名称”中，输入“PurviewKafkaProducer”。 
4. 选择“创建”来创建项目。

### <a name="create-a-console-application"></a>创建控制台应用程序

1. 启动 Visual Studio 2019。 
1. 选择“创建新项目”。 
1. 在“创建新项目”对话框中执行以下步骤：如果看不到此对话框，请在菜单中选择“文件”，然后依次选择“新建”、“项目”。   
    1. 选择“C#”作为编程语言。
    1. 选择“控制台”作为应用程序类型。 
    1. 从结果列表中选择“控制台应用(.NET Core)”。 
    1. 然后，选择“下一步”。 


### <a name="add-the-event-hubs-nuget-package"></a>添加事件中心 NuGet 包

1. 在菜单中选择“工具” > “NuGet 包管理器” > “包管理器控制台”。 
1. 运行以下命令以安装 Azure.Messaging.EventHubs NuGet 包和 Azure.Messaging.EventHubs.Producer NuGet 包： 

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```
    
    ```cmd
    Install-Package Azure.Messaging.EventHubs.Producer
    ```    

### <a name="write-code-to-send-messages-to-the-event-hub"></a>编写代码以将消息发送到事件中心

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
    private const string connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
    private const string eventHubName = "<EVENT HUB NAME>";
    ```

    可以通过在 Purview 帐户的“属性”选项卡中查看 Atlas Kafka 终结点主/辅助连接字符串来获取与 Purview 帐户关联的事件中心命名空间。

    :::image type="content" source="media/manage-eventhub-kafka-dotnet/properties.png" alt-text="事件中心命名空间":::

    事件中心名称应为 ATLAS_HOOK，以便将消息发送到 Purview。

3. 将 `Main` 方法替换为以下 `async Main` 方法，并添加 `async ProduceMessage` 以将消息推送到 Purview。 参阅代码注释了解详细信息。 

    ```csharp
        static async Task Main()
        {
            // Read from the default consumer group: $Default
            string consumerGroup = EventHubConsumerClient.DefaultConsumerGroupName;
            
            / Create an event producer client to add events in the event hub
            EventHubProducerClient producer = new EventHubProducerClient(ehubNamespaceConnectionString, eventHubName);
            
            await ProduceMessage(producer);
        }
        
        static async Task ProduceMessage(EventHubProducerClient producer)
     
        {
            // Create a batch of events 
            using EventDataBatch eventBatch = await producerClient.CreateBatchAsync();

            // Add events to the batch. An event is a represented by a collection of bytes and metadata. 
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("<First event>")));
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("<Second event>")));
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("<Third event>")));

            // Use the producer client to send the batch of events to the event hub
            await producerClient.SendAsync(eventBatch);
            Console.WriteLine("A batch of 3 events has been published.");
             
        }
    ```
5. 生成项目并确保没有错误。
6. 运行程序并等待出现确认消息。 

    > [!NOTE]
    > 有关包含更详细注释的完整源代码，请参阅 [GitHub 上的此文件](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/samples/Sample04_PublishingEvents.md)

### <a name="sample-create-entity-json-message-to-create-a-sql-table-with-two-columns"></a>用于创建包含两个列的 sql 表的示例创建实体 JSON 消息。

```json
    
    {
    "msgCreatedBy": "nayenama",
    "message": {
    "entities": {
    "referredEntities": {
        "-1102395743156037": {
            "typeName": "azure_sql_column",
            "attributes": {
                "owner": null,
                "userTypeId": 61,
                "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable#OrderID",
                "precision": 23,
                "length": 8,
                "description": "Sales Order ID",
                "scale": 3,
                "name": "OrderID",
                "data_type": "int",
                "table": {
                    "guid": "-1102395743156036",
                    "typeName": "azure_sql_table",
                    "entityStatus": "ACTIVE",
                    "displayText": "SalesOrderTable",
                    "uniqueAttributes": {
                                "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable"
                    }
            }
            },
            "guid": "-1102395743156037",
            "version": 2
        },
        "-1102395743156038": {
         "typeName": "azure_sql_column",
            "attributes": {
                "owner": null,
                "userTypeId": 61,
                "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable#OrderDate",
                "description": "Sales Order Date",
                "scale": 3,
                "name": "OrderDate",
                "data_type": "datetime",
                "table": {
                    "guid": "-1102395743156036",
                    "typeName": "azure_sql_table",
                    "entityStatus": "ACTIVE",
                    "displayText": "SalesOrderTable",
                    "uniqueAttributes": {
                                "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable"
                    }
            }
            },
            "guid": "-1102395743156038",
            "status": "ACTIVE",
            "createdBy": "ServiceAdmin",
            "version": 0
        }
        },
        "entity": 
                {
                    "typeName": "azure_sql_table",
                    "attributes": {
                        "owner": "admin",
                        "temporary": false,
                        "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable",
                        "name" : "SalesOrderTable",
                        "description": "Sales Order Table added via Kafka",
                        "columns": [
                            {
                                "guid": "-1102395743156037",
                                "typeName": "azure_sql_column",
                                "uniqueAttributes": {
                                    "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable#OrderID"
                                }
                            },
                            {
                                "guid": "-1102395743156038",
                                "typeName": "azure_sql_column",
                                "uniqueAttributes": {
                                    "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable#OrderDate"
                                }
                            }
                        ]
                        },
                        "guid": "-1102395743156036",
                    "version": 0                
                    }
                },
        "type": "ENTITY_CREATE_V2",
        "user": "admin"
    },
    "version": {
        "version": "1.0.0"
    },
    "msgCompressionKind": "NONE",
    "msgSplitIdx": 1,
    "msgSplitCount": 1
}

``` 

## <a name="consume-messages-from-purview"></a>使用来自 Purview 的消息
本部分介绍如何编写一个使用事件处理器从事件中心接收消息的 .NET Core 控制台应用程序。 需要使用 ATLAS_ENTITIES 事件中心接收来自 Purview 的消息。事件处理器通过从事件中心管理持久检查点和并行接收来简化从这些事件中心接收事件。 

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
1. 依次选择“控制台应用(.NET Core)”、“下一步”。 
1. 输入 PurviewKafkaConsumer 作为“项目名称”，然后选择“创建”。   

### <a name="add-the-event-hubs-nuget-package"></a>添加事件中心 NuGet 包

1. 在菜单中选择“工具” > “NuGet 包管理器” > “包管理器控制台”。 
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
    
    可以通过在 Purview 帐户的“属性”选项卡中查看 Atlas Kafka 终结点主/辅助连接字符串来获取与 Purview 帐户关联的事件中心命名空间。

    :::image type="content" source="media/manage-eventhub-kafka-dotnet/properties.png" alt-text="事件中心命名空间":::

    事件中心名称应为 ATLAS_ENTITIES，以便将消息发送到 Purview。

3. 将 `Main` 方法替换为以下 `async Main` 方法。 参阅代码注释了解详细信息。 

    ```csharp
        static async Task Main()
        {
            // Read from the default consumer group: $Default
            string consumerGroup = EventHubConsumerClient.DefaultConsumerGroupName;

            // Create a blob container client that the event processor will use 
            BlobContainerClient storageClient = new BlobContainerClient(blobStorageConnectionString, blobContainerName);

            // Create an event processor client to process events in the event hub
            EventProcessorClient processor = new EventProcessorClient(storageClient, consumerGroup, ehubNamespaceConnectionString, eventHubName);

            // Register handlers for processing events and handling errors
            processor.ProcessEventAsync += ProcessEventHandler;
            processor.ProcessErrorAsync += ProcessErrorHandler;

            // Start the processing
            await processor.StartProcessingAsync();

            // Wait for 10 seconds for the events to be processed
            await Task.Delay(TimeSpan.FromSeconds(10));

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

### <a name="sample-message-received-from-purview"></a>从 Purview 收到的示例消息

```json
{
    "version":
        {"version":"1.0.0",
         "versionParts":[1]
        },
         "msgCompressionKind":"NONE",
         "msgSplitIdx":1,
         "msgSplitCount":1,
         "msgSourceIP":"10.244.155.5",
         "msgCreatedBy":
         "",
         "msgCreationTime":1618588940869,
         "message":{
            "type":"ENTITY_NOTIFICATION_V2",
            "entity":{
                "typeName":"azure_sql_table",
                    "attributes":{
                        "owner":"admin",
                        "createTime":0,
                        "qualifiedName":"mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable",
                        "name":"SalesOrderTable",
                        "description":"Sales Order Table"
                        },
                        "guid":"ead5abc7-00a4-4d81-8432-d5f6f6f60000",
                        "status":"ACTIVE",
                        "displayText":"SalesOrderTable"
                    },
                    "operationType":"ENTITY_UPDATE",
                    "eventTime":1618588940567
                }
}
```

> [!IMPORTANT]
> Atlas 当前支持下列操作类型：ENTITY_CREATE_V2、ENTITY_PARTIAL_UPDATE_V2、ENTITY_FULL_UPDATE_V2、ENTITY_DELETE_V2。    当前默认启用将消息推送到 Purview。 如果方案涉及从 Purview 读取内容，请联系我们，因为此行为需要加入允许列表。 （提供 Purview 帐户的订阅 id 和名称）。


## <a name="next-steps"></a>后续步骤
查看 GitHub 上的示例。 

- [GitHub 上的事件中心示例](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples)
- [GitHub 上的事件处理器示例](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples)
- [Atlas 通知简介](https://atlas.apache.org/2.0.0/Notifications.html)
