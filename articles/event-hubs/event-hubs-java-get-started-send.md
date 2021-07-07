---
title: 使用 Java 向/从 Azure 事件中心发送/接收事件（最新版）
description: 本文演练如何创建一个可使用最新 azure-messaging-eventhubs 包向/从 Azure 事件中心发送/接收事件的 Java 应用程序。
ms.topic: quickstart
ms.date: 04/30/2021
ms.custom: devx-track-java
ms.openlocfilehash: e5d9df512c2066603ec8dcbcb876da1969a43161
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2021
ms.locfileid: "108752986"
---
# <a name="use-java-to-send-events-to-or-receive-events-from-azure-event-hubs-azure-messaging-eventhubs"></a>使用 Java 向/从 Azure 事件中心 (azure-messaging-eventhubs) 发送/接收事件

本快速入门介绍如何使用 **azure-messaging-eventhubs** Java 包向事件中心发送事件以及从事件中心接收事件。

> [!IMPORTANT]
> 本快速入门使用新的 **azure-messaging-eventhubs** 库。 有关使用旧的 **azure-eventhubs** 和 **azure-eventhubs-eph** 包的快速入门，请参阅 [使用 azure-eventhubs 和 azure-eventhubs-eph 发送和接收事件](event-hubs-java-get-started-send-legacy.md)。


## <a name="prerequisites"></a>先决条件

如果不熟悉 Azure 事件中心，请在阅读本快速入门之前参阅[事件中心概述](event-hubs-about.md)。

若要完成本快速入门，需要具备以下先决条件：

- Microsoft Azure 订阅。 若要使用 Azure 服务（包括 Azure 事件中心），需要一个订阅。  如果没有现有的 Azure 帐户，可以注册[免费试用](https://azure.microsoft.com/free/)帐户，或者在[创建帐户](https://azure.microsoft.com)时使用 MSDN 订阅者权益。
- Java 开发环境。 本快速入门使用 [Eclipse](https://www.eclipse.org/)。 需要 Java 开发工具包 (JDK) 版本 8 或更高版本。
- **创建事件中心命名空间和事件中心**。 第一步是使用 [Azure 门户](https://portal.azure.com)创建事件中心类型的命名空间，并获取应用程序与事件中心进行通信所需的管理凭据。 要创建命名空间和事件中心，请按照[此文](event-hubs-create.md)中的步骤操作。 然后，按照以下文章中的说明获取事件中心命名空间的连接字符串：[获取连接字符串](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)。 稍后将在本快速入门中使用连接字符串。

## <a name="send-events"></a>发送事件

本部分介绍如何创建一个向事件中心发送事件的 Java 应用程序。

### <a name="add-reference-to-azure-event-hubs-library"></a>将引用添加到 Azure 事件中心库

[Maven 中心存储库](https://search.maven.org/search?q=a:azure-messaging-eventhubs)中提供了事件中心的 Java 客户端库。 可使用 Maven 项目文件中的以下依赖项声明引用此库：

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-eventhubs</artifactId>
    <version>5.7.0</version>
</dependency>
```

> [!NOTE]
> 将版本更新为发布到 Maven 存储库的最新版本。

### <a name="write-code-to-send-messages-to-the-event-hub"></a>编写代码以将消息发送到事件中心

对于以下示例，请首先在你最喜欢的 Java 开发环境中为控制台/shell 应用程序创建一个新的 Maven 项目。 添加一个名为 `Sender` 的类，并向该类中添加以下代码：

> [!IMPORTANT]
> 将 `<Event Hubs namespace connection string>` 更新为事件中心命名空间的连接字符串。 将 `<Event hub name>` 更新为命名空间中的事件中心名称。

```java
import com.azure.messaging.eventhubs.*;
import java.util.Arrays;
import java.util.List;

public class Sender {
    private static final String connectionString = "<Event Hubs namespace connection string>";
    private static final String eventHubName = "<Event hub name>";

    public static void main(String[] args) {
        publishEvents();
    }
}
```

### <a name="add-code-to-publish-events-to-the-event-hub"></a>添加代码以将事件发布到事件中心

将名为 `publishEvents` 的方法添加到 `Sender` 类：

```java
    /**
     * Code sample for publishing events.
     * @throws IllegalArgumentException if the EventData is bigger than the max batch size.
     */
    public static void publishEvents() {
        // create a producer client
        EventHubProducerClient producer = new EventHubClientBuilder()
            .connectionString(connectionString, eventHubName)
            .buildProducerClient();

        // sample events in an array
        List<EventData> allEvents = Arrays.asList(new EventData("Foo"), new EventData("Bar"));

        // create a batch
        EventDataBatch eventDataBatch = producer.createBatch();

        for (EventData eventData : allEvents) {
            // try to add the event from the array to the batch
            if (!eventDataBatch.tryAdd(eventData)) {
                // if the batch is full, send it and then create a new batch
                producer.send(eventDataBatch);
                eventDataBatch = producer.createBatch();

                // Try to add that event that couldn't fit before.
                if (!eventDataBatch.tryAdd(eventData)) {
                    throw new IllegalArgumentException("Event is too large for an empty batch. Max size: "
                        + eventDataBatch.getMaxSizeInBytes());
                }
            }
        }
        // send the last batch of remaining events
        if (eventDataBatch.getCount() > 0) {
            producer.send(eventDataBatch);
        }
        producer.close();
    }
```

生成程序，并确保没有引发任何错误。 将在运行接收器程序后运行此程序。

## <a name="receive-events"></a>接收事件

本教程中的代码基于 [GitHub 上的 EventProcessorClient 示例](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorBlobCheckpointStoreSample.java)，可检查该代码以查看完整的工作应用程序。

> [!WARNING]
> 如果在 Azure Stack Hub 上运行此代码，除非将特定的存储 API 版本作为目标，否则会遇到运行时错误。 这是因为事件中心 SDK 使用 Azure 中提供的最新 Azure 存储 API，而此 API 可能在 Azure Stack Hub 平台上不可用。 Azure Stack Hub 支持的 Azure Blob 存储 SDK 版本可能与 Azure 上通常提供的版本不同。 如果你正在将 Azure Blob 存储用作检查点存储，请查看[Azure Stack Hub 内部版本支持的 Azure 存储 API 版本](/azure-stack/user/azure-stack-acs-differences?#api-version)，并在代码中面向此版本。
>
> 例如，如果在 Azure Stack Hub 版本 2005 上运行，则存储服务的最高可用版本为版本 2019-02-02。 默认情况下，事件中心 SDK 客户端库使用 Azure 上的最高可用版本（在 SDK 发布时为 2019-07-07）。 在这种情况下，除了执行本部分中的步骤以外，还需要添加相关代码，将存储服务 API 版本 2019-02-02 作为目标。 如需通过示例来了解如何以特定的存储 API 版本为目标，请参阅 [GitHub 上的此示例](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithCustomStorageVersion.java)。


### <a name="create-an-azure-storage-and-a-blob-container"></a>创建 Azure 存储和 Blob 容器

本快速入门将使用 Azure 存储（特别是 Blob 存储）作为检查点存储。 标记检查点是一个进程，被事件处理器用来标记或提交分区中最后一个成功处理的事件的位置。 标记检查点通常在处理事件的函数中进行。 了解有关检查点的更多信息，请参阅[事件处理器](event-processor-balance-partition-load.md)。

按照以下步骤创建 Azure 存储帐户。

1. [创建 Azure 存储帐户](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [创建一个 blob 容器](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [获取存储帐户的连接字符串](../storage/common/storage-configure-connection-string.md)

    请记下该连接字符串和容器名称 。 稍后要在接收代码中使用这些信息。

### <a name="add-event-hubs-libraries-to-your-java-project"></a>将事件中心库添加到 Java 项目

在 pom.xml 文件中添加以下依赖项。

- [azure-messaging-eventhubs](https://search.maven.org/search?q=a:azure-messaging-eventhubs)
- [azure-messaging-eventhubs-checkpointstore-blob](https://search.maven.org/search?q=a:azure-messaging-eventhubs-checkpointstore-blob)

```xml
<dependencies>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-eventhubs</artifactId>
        <version>5.7.0</version>
    </dependency>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-eventhubs-checkpointstore-blob</artifactId>
        <version>1.6.0</version>
    </dependency>
</dependencies>
```

1. 将以下“导入”语句添加到 Java 文件顶部。

    ```java
    import com.azure.messaging.eventhubs.*;
    import com.azure.messaging.eventhubs.checkpointstore.blob.BlobCheckpointStore;
    import com.azure.messaging.eventhubs.models.*;
    import com.azure.storage.blob.*;
    import java.util.function.Consumer;
    ```
2. 创建一个名为 `Receiver` 的类，并向该类中添加以下字符串变量。 将占位符替换为正确的值。

    > [!IMPORTANT]
    > 将占位符替换为正确的值。
    > - `<Event Hubs namespace connection string>` 更新为事件中心命名空间的连接字符串。 更新
    > - `<Event hub name>` 更新为命名空间中的事件中心名称。
    > - `<Storage connection string>` 更新为 Azure 存储帐户的连接字符串。
    > - `<Storage container name>` 更新为 Azure Blob 存储中的容器名称。

    ```java
    private static final String connectionString = "<Event Hubs namespace connection string>";
    private static final String eventHubName = "<Event hub name>";
    private static final String storageConnectionString = "<Storage connection string>";
    private static final String storageContainerName = "<Storage container name>";
    ```
3. 将下面的 `main` 方法添加到该类。

    ```java
    public static void main(String[] args) throws Exception {
        // Create a blob container client that you use later to build an event processor client to receive and process events
        BlobContainerAsyncClient blobContainerAsyncClient = new BlobContainerClientBuilder()
            .connectionString(storageConnectionString)
            .containerName(storageContainerName)
            .buildAsyncClient();

        // Create a builder object that you will use later to build an event processor client to receive and process events and errors.
        EventProcessorClientBuilder eventProcessorClientBuilder = new EventProcessorClientBuilder()
            .connectionString(connectionString, eventHubName)
            .consumerGroup(EventHubClientBuilder.DEFAULT_CONSUMER_GROUP_NAME)
            .processEvent(PARTITION_PROCESSOR)
            .processError(ERROR_HANDLER)
            .checkpointStore(new BlobCheckpointStore(blobContainerAsyncClient));

        // Use the builder object to create an event processor client
        EventProcessorClient eventProcessorClient = eventProcessorClientBuilder.buildEventProcessorClient();

        System.out.println("Starting event processor");
        eventProcessorClient.start();

        System.out.println("Press enter to stop.");
        System.in.read();

        System.out.println("Stopping event processor");
        eventProcessorClient.stop();
        System.out.println("Event processor stopped.");

        System.out.println("Exiting process");
    }
    ```
4. 将处理事件和错误的两个帮助程序方法（`PARTITION_PROCESSOR` 和 `ERROR_HANDLER`）添加到 `Receiver` 类中。

    ```java
    public static final Consumer<EventContext> PARTITION_PROCESSOR = eventContext -> {
        PartitionContext partitionContext = eventContext.getPartitionContext();
        EventData eventData = eventContext.getEventData();

        System.out.printf("Processing event from partition %s with sequence number %d with body: %s%n",
            partitionContext.getPartitionId(), eventData.getSequenceNumber(), eventData.getBodyAsString());

        // Every 10 events received, it will update the checkpoint stored in Azure Blob Storage.
        if (eventData.getSequenceNumber() % 10 == 0) {
            eventContext.updateCheckpoint();
        }
    };

    public static final Consumer<ErrorContext> ERROR_HANDLER = errorContext -> {
        System.out.printf("Error occurred in partition processor for partition %s, %s.%n",
            errorContext.getPartitionContext().getPartitionId(),
            errorContext.getThrowable());
    };
    ```

5. 生成程序，并确保没有引发任何错误。

## <a name="run-the-applications"></a>运行应用程序

1. 先运行接收器应用程序。
1. 然后运行发送器应用程序。
1. 在“接收器”应用程序窗口中，确认已看到发送器应用程序发布的事件。

    ```cmd
    Starting event processor
    Press enter to stop.
    Processing event from partition 0 with sequence number 331 with body: Foo
    Processing event from partition 0 with sequence number 332 with body: Bar
    ```
    
1. 在接收器应用程序窗口中按 **ENTER** 停止该应用程序。

    ```cmd
    Starting event processor
    Press enter to stop.
    Processing event from partition 0 with sequence number 331 with body: Foo
    Processing event from partition 0 with sequence number 332 with body: Bar

    Stopping event processor
    Event processor stopped.
    Exiting process
    ```

## <a name="next-steps"></a>后续步骤

在 GitHub 上参阅以下示例：

- [azure-messaging-eventhubs 示例](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)
- [azure-messaging-eventhubs-checkpointstore-blob 示例](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob).
