---
title: 如何通过 Java 使用队列存储 - Azure 存储
description: 了解如何使用队列存储来创建和删除队列。 了解如何使用用于 Java 的 Azure 存储客户端库来插入、速览、获取和删除消息。
author: normesta
ms.author: normesta
ms.reviewer: dineshm
ms.date: 08/19/2020
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-java
ms.openlocfilehash: 25734b7f4073895da832b3d020679c6f7b36a080
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128665846"
---
# <a name="how-to-use-queue-storage-from-java"></a>如何通过 Java 使用队列存储

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>概述

本指南演示了如何为使用 Azure 队列存储服务的常见方案编写代码。 这些示例用 Java 编写并使用[用于 Java 的 Azure 存储 SDK](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage)。 方案包括 **插入**、**速览**、**获取** 和 **删除** 队列消息。 还介绍了用于 **创建** 和 **删除** 队列的代码。 有关队列的详细信息，请参阅[后续步骤](#next-steps)部分。

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>创建 Java 应用程序

# <a name="java-v12-sdk"></a>[Java v12 SDK](#tab/java)

首先，验证你的开发系统是否满足[用于 Java 的 Azure 队列存储客户端库 v12](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue)中列出的先决条件。

创建名为 `queues-how-to-v12` 的 Java 应用程序：

1. 在控制台窗口（例如 cmd、PowerShell 或 Bash）中，使用 Maven 创建名为 blob-quickstart-v12 的新控制台应用`queues-how-to-v12`。 键入以下 `mvn` 命令，创建“hello world”Java 项目。

   ```bash
    mvn archetype:generate \
        --define interactiveMode=n \
        --define groupId=com.queues.howto \
        --define artifactId=queues-howto-v12 \
        --define archetypeArtifactId=maven-archetype-quickstart \
        --define archetypeVersion=1.4
   ```

   ```powershell
   mvn archetype:generate `
       --define interactiveMode=n `
       --define groupId=com.queues.howto `
       --define artifactId=queues-howto-v12 `
       --define archetypeArtifactId=maven-archetype-quickstart `
       --define archetypeVersion=1.4
   ```

1. 生成项目的输出应如下所示：

   ```console
   [INFO] Scanning for projects...
   [INFO]
   [INFO] ------------------< org.apache.maven:standalone-pom >-------------------
   [INFO] Building Maven Stub Project (No POM) 1
   [INFO] --------------------------------[ pom ]---------------------------------
   [INFO]
   [INFO] >>> maven-archetype-plugin:3.1.2:generate (default-cli) > generate-sources @ standalone-pom >>>
   [INFO]
   [INFO] <<< maven-archetype-plugin:3.1.2:generate (default-cli) < generate-sources @ standalone-pom <<<
   [INFO]
   [INFO]
   [INFO] --- maven-archetype-plugin:3.1.2:generate (default-cli) @ standalone-pom ---
   [INFO] Generating project in Batch mode
   [INFO] ----------------------------------------------------------------------------
   [INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
   [INFO] ----------------------------------------------------------------------------
   [INFO] Parameter: groupId, Value: com.queues.howto
   [INFO] Parameter: artifactId, Value: queues-howto-v12
   [INFO] Parameter: version, Value: 1.0-SNAPSHOT
   [INFO] Parameter: package, Value: com.queues.howto
   [INFO] Parameter: packageInPathFormat, Value: com/queues/howto
   [INFO] Parameter: version, Value: 1.0-SNAPSHOT
   [INFO] Parameter: package, Value: com.queues.howto
   [INFO] Parameter: groupId, Value: com.queues.howto
   [INFO] Parameter: artifactId, Value: queues-howto-v12
   [INFO] Project created from Archetype in dir: C:\queues\queues-howto-v12
   [INFO] ------------------------------------------------------------------------
   [INFO] BUILD SUCCESS
   [INFO] ------------------------------------------------------------------------
   [INFO] Total time:  6.775 s
   [INFO] Finished at: 2020-08-17T15:27:31-07:00
   [INFO] ------------------------------------------------------------------------
   ```

1. 切换到新创建的 `queues-howto-v12` 目录。

   ```console
   cd queues-howto-v12
   ```

### <a name="install-the-package"></a>安装包

在文本编辑器中打开 pom.xml 文件`pom.xml`。 将以下依赖项元素添加到依赖项组。

```xml
<dependency>
  <groupId>com.azure</groupId>
  <artifactId>azure-storage-queue</artifactId>
  <version>12.6.0</version>
</dependency>
```

# <a name="java-v8-sdk"></a>[Java v8 SDK](#tab/java8)

首先，验证你的开发系统是否满足[用于 Java v8 的 Azure 存储 SDK](https://github.com/azure/azure-storage-java) 中列出的先决条件。 按照有关下载和安装用于 Java 的 Azure 存储库的说明进行操作。 然后，可以使用本文中的示例创建 Java 应用程序。

---

## <a name="configure-your-application-to-access-queue-storage"></a>配置应用程序以访问队列存储

将下列 import 语句添加到需要在其中使用 Azure 存储 API 来访问队列的 Java 文件的顶部：

# <a name="java-v12-sdk"></a>[Java v12 SDK](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_ImportStatements":::

# <a name="java-v8-sdk"></a>[Java v8 SDK](#tab/java8)

```java
// Include the following imports to use queue APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.queue.*;
```

---

## <a name="set-up-an-azure-storage-connection-string"></a>设置 Azure 存储连接字符串

Azure 存储客户端使用存储连接字符串来访问数据管理服务。 获取 [Azure 门户](https://portal.azure.com)中列出的你的存储帐户的名称和主访问密钥。 将它们用作连接字符串中的 `AccountName` 和 `AccountKey` 值。 此示例演示如何声明一个静态字段以保存连接字符串：

# <a name="java-v12-sdk"></a>[Java v12 SDK](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_ConnectionString":::

# <a name="java-v8-sdk"></a>[Java v8 SDK](#tab/java8)

```java
// Define the connection-string with your values.
final String storageConnectionString =
    "DefaultEndpointsProtocol=https;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```

可以将此字符串存储在名为 `ServiceConfiguration.cscfg` 的服务配置文件中。 对于以 Microsoft Azure 角色运行的应用，请通过调用 `RoleEnvironment.getConfigurationSettings` 来访问连接字符串。 下面是从名为 `StorageConnectionString` 的 `Setting` 元素获取连接字符串的示例：

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

---

以下示例假设你有一个包含存储连接字符串的 `String` 对象。

## <a name="how-to-create-a-queue"></a>如何：创建队列

# <a name="java-v12-sdk"></a>[Java v12 SDK](#tab/java)

`QueueClient` 对象包含用于与队列进行交互的操作。 以下代码创建 `QueueClient` 对象。 使用该 `QueueClient` 对象创建要使用的队列。

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_CreateQueue":::

# <a name="java-v8-sdk"></a>[Java v8 SDK](#tab/java8)

利用 `CloudQueueClient` 对象，可以获取队列的引用对象。 以下代码可创建一个 `CloudQueueClient` 对象，该对象提供对要使用的队列的引用。 如果队列不存在，可以创建它。

> [!NOTE]
> 可通过其他方法来创建 `CloudStorageAccount` 对象。 有关详细信息，请参阅 [Azure 存储客户端 SDK 引用](https://azure.github.io/azure-sdk-for-java/storage.html)中的 `CloudStorageAccount`。

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

   // Create the queue client.
   CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

   // Retrieve a reference to a queue.
   CloudQueue queue = queueClient.getQueueReference("myqueue");

   // Create the queue if it doesn't already exist.
   queue.createIfNotExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-add-a-message-to-a-queue"></a>如何：向队列添加消息

# <a name="java-v12-sdk"></a>[Java v12 SDK](#tab/java)

若要在现有队列中插入消息，请调用 `sendMessage` 方法。 消息可以是字符串（UTF-8 格式）或字节数组。 下面是将字符串消息发送到队列的代码。

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_AddMessage":::

# <a name="java-v8-sdk"></a>[Java v8 SDK](#tab/java8)

要将消息插入现有队列，请先创建一个新的 `CloudQueueMessage`。 接下来，调用 `addMessage` 方法。 可从字符串（UTF-8 格式）或字节数组创建 `CloudQueueMessage`。 以下代码创建队列（如果该队列不存在）并插入消息 `Hello, World`。

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Create the queue if it doesn't already exist.
    queue.createIfNotExists();

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.addMessage(message);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-peek-at-the-next-message"></a>如何：扫视下一条消息

通过调用 `peekMessage`，可以速览队列前面的消息，而不会从队列中删除它。

# <a name="java-v12-sdk"></a>[Java v12 SDK](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_PeekMessage":::

# <a name="java-v8-sdk"></a>[Java v8 SDK](#tab/java8)

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Peek at the next message.
    CloudQueueMessage peekedMessage = queue.peekMessage();

    // Output the message value.
    if (peekedMessage != null)
    {
      System.out.println(peekedMessage.getMessageContentAsString());
   }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-change-the-contents-of-a-queued-message"></a>如何：更改已排队消息的内容

可以更改队列中现有消息的内容。 如果消息表示一个工作任务，则可以使用此功能来更新状态。 以下代码使用新内容更新队列消息，并将可见性超时设置为再延长 30 秒。 延长可见性超时会再给客户端 30 秒时间来继续处理该消息。 你还可以保留重试计数。 如果消息重试了 n 次以上，则你会将其删除。 此方案可避免每次处理某条消息时都触发应用程序错误。

# <a name="java-v12-sdk"></a>[Java v12 SDK](#tab/java)

下面的代码示例将在消息队列中进行搜索，查找与搜索字符串匹配的第一个消息内容，对消息内容进行修改，然后退出。

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_UpdateSearchMessage":::

# <a name="java-v8-sdk"></a>[Java v8 SDK](#tab/java8)

下面的代码示例将在消息队列中进行搜索，查找与 `Hello, world` 匹配的第一个消息内容，对消息内容进行修改，然后退出。

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // The maximum number of messages that can be retrieved is 32.
    final int MAX_NUMBER_OF_MESSAGES_TO_PEEK = 32;

    // Loop through the messages in the queue.
    for (CloudQueueMessage message : queue.retrieveMessages(MAX_NUMBER_OF_MESSAGES_TO_PEEK,1,null,null))
    {
        // Check for a specific string.
        if (message.getMessageContentAsString().equals("Hello, World"))
        {
            // Modify the content of the first matching message.
            message.setMessageContent("Updated contents.");
            // Set it to be visible in 30 seconds.
            EnumSet<MessageUpdateFields> updateFields =
                EnumSet.of(MessageUpdateFields.CONTENT,
                MessageUpdateFields.VISIBILITY);
            // Update the message.
            queue.updateMessage(message, 30, updateFields, null, null);
            break;
        }
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

以下代码示例只更新队列中的第一个可见消息。

# <a name="java-v12-sdk"></a>[Java v12 SDK](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_UpdateFirstMessage":::

# <a name="java-v8-sdk"></a>[Java v8 SDK](#tab/java8)

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve the first visible message in the queue.
    CloudQueueMessage message = queue.retrieveMessage();

    if (message != null)
    {
        // Modify the message content.
        message.setMessageContent("Updated contents.");
        // Set it to be visible in 60 seconds.
        EnumSet<MessageUpdateFields> updateFields =
            EnumSet.of(MessageUpdateFields.CONTENT,
            MessageUpdateFields.VISIBILITY);
        // Update the message.
        queue.updateMessage(message, 60, updateFields, null, null);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-get-the-queue-length"></a>如何：获取队列长度

可以获取队列中消息的估计数。

# <a name="java-v12-sdk"></a>[Java v12 SDK](#tab/java)

`getProperties` 方法可返回多个值，包括队列中的当前消息数。 此计数仅为近似值，因为可能会在请求后添加或删除消息。 `getApproximateMessageCount` 方法可返回通过调用 `getProperties` 检索到的最后一个值，而不会调用队列存储。

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_GetQueueLength":::

# <a name="java-v8-sdk"></a>[Java v8 SDK](#tab/java8)

`downloadAttributes` 方法可检索多个值，包括队列中的当前消息数。 此计数仅为近似值，因为可能会在请求后添加或删除消息。 `getApproximateMessageCount` 方法可返回通过调用 `downloadAttributes` 检索到的最后一个值，而不会调用队列存储。

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

   // Download the approximate message count from the server.
    queue.downloadAttributes();

    // Retrieve the newly cached approximate message count.
    long cachedMessageCount = queue.getApproximateMessageCount();

    // Display the queue length.
    System.out.println(String.format("Queue length: %d", cachedMessageCount));
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-dequeue-the-next-message"></a>如何：取消对下一条消息的排队

# <a name="java-v12-sdk"></a>[Java v12 SDK](#tab/java)

代码通过两个步骤来取消对队列中某条消息的排队。 调用 `receiveMessage` 时，会获得队列中的下一条消息。 从 `receiveMessage` 返回的消息对于从此队列读取消息的任何其他代码都是不可见的。 默认情况下，此消息持续 30 秒不可见。 若要完成从队列中删除消息，还必须调用 `deleteMessage`。 如果你的代码未能处理消息，此两步过程可确保你可以获取同一消息并重试。 代码在处理消息后会立即调用 `deleteMessage`。

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_DequeueMessage":::

# <a name="java-v8-sdk"></a>[Java v8 SDK](#tab/java8)

代码通过两个步骤来取消对队列中某条消息的排队。 调用 `retrieveMessage` 时，会获得队列中的下一条消息。 从 `retrieveMessage` 返回的消息对于从此队列读取消息的任何其他代码都是不可见的。 默认情况下，此消息持续 30 秒不可见。 若要完成从队列中删除消息，还必须调用 `deleteMessage`。 如果你的代码未能处理消息，此两步过程可确保你可以获取同一消息并重试。 代码在处理消息后会立即调用 `deleteMessage`。

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve the first visible message in the queue.
    CloudQueueMessage retrievedMessage = queue.retrieveMessage();

    if (retrievedMessage != null)
    {
        // Process the message in less than 30 seconds, and then delete the message.
        queue.deleteMessage(retrievedMessage);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="additional-options-for-dequeuing-messages"></a>用于取消对消息进行排队的其他选项

可以通过两种方式自定义队列中的消息检索。 首先，获取一批消息（最多 32 条）。 其次，设置更长或更短的不可见超时时间，从而允许代码使用更多或更少的时间来完全处理每个消息。

# <a name="java-v12-sdk"></a>[Java v12 SDK](#tab/java)

以下代码示例使用 `receiveMessages` 方法在一个调用中获取 20 条消息。 然后，使用 `for` 循环处理每条消息。 它还将每条消息的不可见超时设置为 5 分钟（300 秒）。 超时同时针对所有消息启动。 自调用 `receiveMessages` 起经过五分钟后，未删除的任何消息都将再次变得可见。

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_DequeueMessages":::

# <a name="java-v8-sdk"></a>[Java v8 SDK](#tab/java8)

以下代码示例使用 `retrieveMessages` 方法在一个调用中获取 20 条消息。 然后，使用 `for` 循环处理每条消息。 它还将每条消息的不可见超时设置为 5 分钟（300 秒）。 超时同时针对所有消息启动。 自调用 `retrieveMessages` 起经过五分钟后，未删除的任何消息都将再次变得可见。

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
    for (CloudQueueMessage message : queue.retrieveMessages(20, 300, null, null)) {
        // Do processing for all messages in less than 5 minutes,
        // deleting each message after processing.
        queue.deleteMessage(message);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-list-the-queues"></a>如何：列出队列

# <a name="java-v12-sdk"></a>[Java v12 SDK](#tab/java)

若要获取当前队列的列表，请调用 `QueueServiceClient.listQueues()` 方法，它将返回 `QueueItem` 对象的集合。

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_ListQueues":::

# <a name="java-v8-sdk"></a>[Java v8 SDK](#tab/java8)

若要获取当前队列的列表，请调用 `CloudQueueClient.listQueues()` 方法，它将返回 `CloudQueue` 对象的集合。

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient =
        storageAccount.createCloudQueueClient();

    // Loop through the collection of queues.
    for (CloudQueue queue : queueClient.listQueues())
    {
        // Output each queue name.
        System.out.println(queue.getName());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-delete-a-queue"></a>如何：删除队列

# <a name="java-v12-sdk"></a>[Java v12 SDK](#tab/java)

若要删除队列及其包含的所有消息，请对 `QueueClient` 对象调用 `delete` 方法。

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_DeleteMessageQueue":::

# <a name="java-v8-sdk"></a>[Java v8 SDK](#tab/java8)

若要删除队列及其包含的所有消息，请对 `CloudQueue` 对象调用 `deleteIfExists` 方法。

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Delete the queue if it exists.
    queue.deleteIfExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="next-steps"></a>后续步骤

现在，你已了解了有关队列存储的基础知识，请单击下面的链接来了解更复杂的存储任务。

- [Azure Storage SDK for Java](https://github.com/Azure/Azure-SDK-for-Java)
- [Azure 存储客户端 SDK 参考](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage)
- [Azure 存储服务 REST API](/rest/api/storageservices/)
- [Azure 存储团队博客](https://techcommunity.Microsoft.com/t5/Azure-storage/bg-p/azurestorageblog)
