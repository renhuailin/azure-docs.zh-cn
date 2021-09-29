---
title: Azure 队列存储入门（使用 .NET）- Azure 存储
description: Azure 队列存储用于在应用程序组件之间进行可靠的异步消息传送。 应用程序组件可以利用云消息传送进行独立缩放。
author: normesta
ms.author: normesta
ms.reviewer: dineshm
ms.date: 10/08/2020
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-csharp
ms.openlocfilehash: 4dc434d8387a065f60405e1c0937c7bb61ef9833
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128562450"
---
# <a name="get-started-with-azure-queue-storage-using-net"></a>Azure 队列存储入门（使用 .NET）

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>概述

Azure 队列存储用于在应用程序组件之间进行云消息传送。 设计可缩放的应用程序时，应用程序组件通常是分离的，各组件可以独立缩放。 队列存储在应用程序组件之间提供异步消息传送，无论这些组件是运行在云中、桌面上、本地服务器上还是移动设备上。 队列存储还支持管理异步任务以及构建过程工作流。

### <a name="about-this-tutorial"></a>关于本教程

本教程展示了如何针对某些使用 Azure 队列存储的常见情形编写 .NET 代码。 涉及的方案包括创建和删除队列、添加、读取和删除队列消息。

**估计完成时间：** 45 分钟

### <a name="prerequisites"></a>先决条件

- [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
- 一个 [Azure 存储帐户](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="set-up-your-development-environment"></a>设置开发环境

接下来在 Visual Studio 中设置开发环境，即可试用本指南中的代码示例。

### <a name="create-a-windows-console-application-project"></a>创建 Windows 控制台应用程序项目

在 Visual Studio 中创建新的 Windows 控制台应用程序。 以下步骤演示了如何在 Visual Studio 2019 中创建控制台应用程序。 在其他版本的 Visual Studio 中，这些步骤是类似的。

1. 选择“文件” > “新建” > “项目”  
2. 选择“平台” > “Windows”
3. 选择“控制台应用(.NET Framework)”
4. 选择“下一步”
5. 在“项目名称”字段中输入应用程序的名称
6. 选择“创建”

本教程中的所有代码示例都可以添加到控制台应用程序的 `Program.cs` 文件的 `Main()` 方法。

可以在任意类型的 .NET 应用程序（包括 Azure 云服务或 Web 应用，以及桌面和移动应用程序）中使用 Azure 存储客户端库。 为简单起见，我们在本指南中使用控制台应用程序。

### <a name="use-nuget-to-install-the-required-packages"></a>使用 NuGet 安装所需包

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

为完成此教程，需要在项目中引用下述四个包：

- [适用于 .NET 的 Azure.Core 库](https://www.nuget.org/packages/azure.core/)：此包为新式 .NET Azure SDK 客户端库提供了共享基元、抽象和帮助程序。
- [适用于 .NET 的 Azure.Storage.Common 客户端库](https://www.nuget.org/packages/azure.storage.common/)：此包提供了由其他 Azure 存储客户端库共享的基础结构。
- [适用于 .NET 的 Azure.Storage.Queues 客户端库](https://www.nuget.org/packages/azure.storage.queues/)：有了此包，就可以使用 Azure 队列存储来存储客户端可能会访问的消息。
- [适用于 .NET 的 System.Configuration.ConfigurationManager 库](https://www.nuget.org/packages/system.configuration.configurationmanager/)：此包提供了对客户端应用程序配置文件的访问权限。

可以使用 NuGet 获取这些包。 执行以下步骤：

1. 在“解决方案资源管理器”中，右键单击项目并选择“管理 NuGet 包”。
1. 选择“浏览”
1. 在线搜索 `Azure.Storage.Queues`，然后选择“安装”以安装 Azure 存储客户端库及其依赖项。 这还会安装 Azure.Storage.Common 和 Azure.Core 库，它们是队列库的依赖项。
1. 在线搜索 `System.Configuration.ConfigurationManager`，然后选择“安装”以安装 Configuration Manager。

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnetv11)

为完成此教程，需要在项目中引用下述三个包：

- [适用于 .NET 的 Microsoft.Azure.Storage.Common 客户端库](https://www.nuget.org/packages/microsoft.azure.storage.common/)：此包提供以编程方式访问存储帐户中数据资源的权限。
- [适用于 .NET 的 Microsoft Azure 队列存储客户端库](https://www.nuget.org/packages/microsoft.azure.storage.queue/)：有了此客户端库，就可以使用 Azure 队列存储来存储客户端可能会访问的消息。
- [适用于 .NET 的 Microsoft Azure 配置管理器库](https://www.nuget.org/packages/microsoft.azure.configurationmanager/)：此包提供用于分析配置文件中连接字符串的类，而不考虑应用程序在何处运行。

可以使用 NuGet 获取这些包。 执行以下步骤：

1. 在“解决方案资源管理器”中，右键单击项目并选择“管理 NuGet 包”。
1. 选择“浏览”
1. 在线搜索 `Microsoft.Azure.Storage.Queue`，然后选择“安装”以安装 Azure 存储客户端库及其依赖项。 这样也会安装 Microsoft.Azure.Storage.Common 库，该库是队列库的依赖项。
1. 在线搜索 `Microsoft.Azure.ConfigurationManager`，然后选择“安装”以安装 Azure Configuration Manager。

---

### <a name="determine-your-target-environment"></a>确定目标环境

可从两个环境中选择用于运行本指南中示例的环境：

- 可针对云中的 Azure 存储帐户运行代码。
- 可针对 Azurite 存储仿真器运行代码。 Azurite 是仿真云中 Azure 存储帐户的本地环境。 当应用程序正在开发时，Azurite 是用于测试和调试代码的免费选项。 模拟器使用已知帐户和密钥。 有关详细信息，请参阅[使用 Azurite 仿真器进行本地 Azure 存储开发和测试](../common/storage-use-azurite.md)。

> [!NOTE]
> 可以指向存储模拟器以避免引发与 Azure 存储有关的任何费用。 但是，如果你确实选择以云中的 Azure 存储帐户为目标，则执行此教程的费用可以忽略不计。

## <a name="get-your-storage-connection-string"></a>获取存储连接字符串

用于 .NET 的 Azure 存储客户端库支持使用存储连接字符串来配置终结点和用于访问存储服务的凭据。 有关详细信息，请参阅[管理存储帐户访问密钥](../common/storage-account-keys-manage.md)。

### <a name="copy-your-credentials-from-the-azure-portal"></a>从 Azure 门户复制凭据

此示例代码需要对存储帐户访问进行授权。 若要授权，请以连接字符串的形式向应用程序提供存储帐户凭据。 若要查看存储帐户凭据，请执行以下操作：

1. 导航到 [Azure 门户](https://portal.azure.com)。
2. 找到自己的存储帐户。
3. 在存储帐户概述的“设置”部分，选择“访问密钥”。  此时会显示帐户访问密钥，以及每个密钥的完整连接字符串。
4. 找到“密钥 1”下面的“连接字符串”值，单击“复制”按钮复制该连接字符串。   下一步需将此连接字符串值添加到某个环境变量。

    ![显示如何从 Azure 门户复制连接字符串的屏幕截图](media/storage-dotnet-how-to-use-queues/portal-connection-string.png)

有关连接字符串的详细信息，请参阅[配置 Azure 存储的连接字符串](../common/storage-configure-connection-string.md)。

> [!NOTE]
> 存储帐户密钥类似于存储帐户的根密码。 始终要小心保护存储帐户密钥。 避免将其分发给其他用户、对其进行硬编码或将其保存在其他人可以访问的纯文本文件中。 如果认为密钥可能已泄漏，请使用 Azure 门户重新生成密钥。

维护存储连接字符串的最佳方法在配置文件中。 若要配置连接字符串，请从 Visual Studio 中的解决方案资源管理器打开 `app.config` 文件。 添加 `<appSettings>` 元素的内容，如此处所示。 将 `connection-string` 替换为你从门户中的存储帐户复制的值：

```xml
<configuration>
    <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.7.2" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="connection-string" />
    </appSettings>
</configuration>
```

例如，配置设置看起来类似于：

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=GMuzNHjlB3S9itqZJHHCnRkrokLkcSyW7yK9BRbGp0ENePunLPwBgpxV1Z/pVo9zpem/2xSHXkMqTHHLcx8XRA==EndpointSuffix=core.windows.net" />
```

若要以 Azurite 存储仿真器为目标，可使用映射到已知帐户名称和密钥的快捷方式。 在这种情况下，连接字符串设置如下所示：

```xml
<add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
```

### <a name="add-using-directives"></a>添加 using 指令

将以下 `using` 指令添加到 `Program.cs` 文件顶部：

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_UsingStatements":::

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnetv11)

```csharp
using System; // Namespace for Console output
using Microsoft.Azure; // Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage; // Namespace for CloudStorageAccount
using Microsoft.Azure.Storage.Queue; // Namespace for Queue storage types
```

---

### <a name="create-the-queue-storage-client"></a>创建队列存储客户端

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

使用 [`QueueClient`](/dotnet/api/azure.storage.queues.queueclient) 类可以检索存储在队列存储中的队列。 下面是创建服务客户端的一种方法：

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_CreateClient":::

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnetv11)

使用 [`CloudQueueClient`](/dotnet/api/microsoft.azure.storage.queue.cloudqueueclient?view=azure-dotnet-legacy&preserve-view=true) 类可以检索存储在队列存储中的队列。 下面是创建服务客户端的一种方法：

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
```

---

现在，你已准备好编写从队列存储读取数据并将数据写入队列存储的代码。

## <a name="create-a-queue"></a>创建队列

此示例演示如何创建队列：

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_CreateQueue":::

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnetv11)

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist
queue.CreateIfNotExists();
```

---

## <a name="insert-a-message-into-a-queue"></a>在队列中插入消息

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

若要在现有队列中插入消息，请调用 [`SendMessage`](/dotnet/api/azure.storage.queues.queueclient.sendmessage) 方法。 消息可以是字符串（UTF-8 格式）或字节数组。 下面的代码将创建一个队列（如果该队列不存在）并插入一条消息：

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_InsertMessage":::

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnetv11)

若要在现有队列中插入消息，请首先创建一个新的 [`CloudQueueMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage?view=azure-dotnet-legacy&preserve-view=true)。 接下来，调用 [`AddMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessage?view=azure-dotnet-legacy&preserve-view=true) 方法。 可以通过字符串（采用 UTF-8 格式）或字节数组创建 `CloudQueueMessage`。 以下代码将创建队列（如果队列不存在）并插入消息：若要在现有队列中插入消息 `Hello, World`，请先创建新的 [`CloudQueueMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage?view=azure-dotnet-legacy&preserve-view=true)。 接下来，调用 [`AddMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessage?view=azure-dotnet-legacy&preserve-view=true) 方法。 可以通过字符串（采用 UTF-8 格式）或字节数组创建 `CloudQueueMessage`。 下面是创建队列（如果不存在）并插入消息“`Hello, World`”的代码：

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist
queue.CreateIfNotExists();

// Create a message and add it to the queue
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
queue.AddMessage(message);
```

---

## <a name="peek-at-the-next-message"></a>扫视下一条消息

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

通过调用 [`PeekMessages`](/dotnet/api/azure.storage.queues.queueclient.peekmessages) 方法，可以速览队列中的消息，不将其从队列中删除。 如果没有为 `maxMessages` 参数传递值，则默认设置是速览一条消息。

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_PeekMessage":::

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnetv11)

通过调用 [`PeekMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.peekmessage?view=azure-dotnet-legacy&preserve-view=true) 方法，可以速览队列前面的消息，不将其从队列中删除。

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Peek at the next message
CloudQueueMessage peekedMessage = queue.PeekMessage();

// Display message.
Console.WriteLine(peekedMessage.AsString);
```

---

## <a name="change-the-contents-of-a-queued-message"></a>更改已排队消息的内容

可以更改队列中现有消息的内容。 如果消息表示工作任务，可使用此功能来更新该工作任务的状态。 以下代码使用新内容更新队列消息，并将可见性超时设置为再延长 60 秒。 这会保存与消息关联的工作的状态，并额外为客户端提供一分钟的时间来继续处理消息。 可使用此方法跟踪队列消息上的多步骤工作流，即使处理步骤因硬件或软件故障而失败，也无需从头开始操作。 通常同时保留重试计数，当消息重试次数超过 *n* 时再删除该消息。 这可避免每次处理某条消息时都触发应用程序错误。

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_UpdateMessage":::

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnetv11)

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Get the message from the queue and update the message contents.
CloudQueueMessage message = queue.GetMessage();
message.SetMessageContent2("Updated contents.", false);
queue.UpdateMessage(message,
    TimeSpan.FromSeconds(60.0),  // Make it invisible for another 60 seconds.
    MessageUpdateFields.Content | MessageUpdateFields.Visibility);
```

---

## <a name="dequeue-the-next-message"></a>取消下一条消息的排队

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

可通过两个步骤取消消息在队列中的排队。 调用 [`ReceiveMessages`](/dotnet/api/azure.storage.queues.queueclient.receivemessages) 时，你会获得队列中的下一条消息。 从 `ReceiveMessages` 返回的消息对于从此队列读取消息的任何其他代码都是不可见的。 默认情况下，此消息持续 30 秒不可见。 若要完成从队列中删除消息的操作，还必须调用 [`DeleteMessage`](/dotnet/api/azure.storage.queues.queueclient.deletemessage)。 此删除消息的两步过程可确保，如果代码因硬件或软件故障而无法处理消息，则代码的其他实例可以获取相同消息并重试。 代码在处理消息后会立即调用 `DeleteMessage`。

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DequeueMessage":::

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnetv11)

代码通过两个步骤来取消对队列中某条消息的排队。 调用 [`GetMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessage?view=azure-dotnet-legacy&preserve-view=true) 时，你会获得队列中的下一条消息。 从 `GetMessage` 返回的消息对于从此队列读取消息的任何其他代码都是不可见的。 默认情况下，此消息持续 30 秒不可见。 若要完成从队列中删除消息的操作，还必须调用 [`DeleteMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessage?view=azure-dotnet-legacy&preserve-view=true)。 此删除消息的两步过程可确保，如果代码因硬件或软件故障而无法处理消息，则代码的其他实例可以获取相同消息并重试。 代码在处理消息后会立即调用 `DeleteMessage`。

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Get the next message
CloudQueueMessage retrievedMessage = queue.GetMessage();

//Process the message in less than 30 seconds, and then delete the message
queue.DeleteMessage(retrievedMessage);
```

---

<!-- docutune:casing "Async-Await pattern" "Async and Await" -->

## <a name="use-the-async-await-pattern-with-common-queue-storage-apis"></a>将 Async-Await 模式与公用队列存储 API 配合使用

此示例展示了如何将 Async-Await 模式与公用队列存储 API 配合使用。 示例调用每个给定方法的异步版本，如每个方法的 `Async` 后缀所示。 使用异步方法时，Async-Await 模式会暂停本地执行，直到调用完成。 此行为允许当前的线程执行其他工作，这有助于避免性能瓶颈并提高应用程序的整体响应能力。 有关在 .NET 中使用 Async-Await 模式的详细信息，请参阅 [Async 和 Await（C# 和 Visual Basic）](/previous-versions/hh191443(v=vs.140))

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_AsyncQueue":::

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnetv11)

```csharp
// Create the queue if it doesn't already exist
if(await queue.CreateIfNotExistsAsync())
{
    Console.WriteLine("Queue '{0}' Created", queue.Name);
}
else
{
    Console.WriteLine("Queue '{0}' Exists", queue.Name);
}

// Create a message to put in the queue
CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

// Async enqueue the message
await queue.AddMessageAsync(cloudQueueMessage);
Console.WriteLine("Message added");

// Async dequeue the message
CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();
Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

// Async delete the message
await queue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Deleted message");
```

---

## <a name="use-additional-options-for-dequeuing-messages"></a>使用用于取消消息排队的其他选项

可通过两种方式自定义队列中消息的检索。 首先，可获取一批消息（最多 32 条）。 其次，可以设置更长或更短的不可见超时时间，从而允许代码使用更多或更少时间来完全处理每个消息。

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

以下代码示例使用 [`ReceiveMessages`](/dotnet/api/azure.storage.queues.queueclient.receivemessages) 方法在一次调用中获取 20 条消息。 然后，使用 `foreach` 循环处理每条消息。 它还将每条消息的不可见超时时间设置为 5 分钟。 请注意，5 分钟超时时间对于所有消息都是同时开始的，因此在调用 `ReceiveMessages` 5 分钟后，尚未删除的任何消息都会再次变得可见。

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DequeueMessages":::

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnetv11)

以下代码示例使用 [`GetMessages`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessages?view=azure-dotnet-legacy&preserve-view=true) 方法在一次调用中获取 20 条消息。 然后，使用 `foreach` 循环处理每条消息。 它还将每条消息的不可见超时时间设置为 5 分钟。 请注意，5 分钟超时时间对于所有消息都是同时开始的，因此在调用 `GetMessages` 5 分钟后，尚未删除的任何消息都会再次变得可见。

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
{
    // Process all messages in less than 5 minutes, deleting each message after processing.
    queue.DeleteMessage(message);
}
```

---

## <a name="get-the-queue-length"></a>获取队列长度

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

可以获取队列中消息的估计数。 [`GetProperties`](/dotnet/api/azure.storage.queues.queueclient.getproperties) 方法返回包括消息计数在内的队列属性。 [`ApproximateMessagesCount`](/dotnet/api/azure.storage.queues.models.queueproperties.approximatemessagescount) 属性包含队列中的大致消息数。 此数字不低于队列中的实际消息数，但可能会更高。

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_GetQueueLength":::

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnetv11)

可以获取队列中消息的估计数。 [`FetchAttributes`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.fetchattributes?view=azure-dotnet-legacy&preserve-view=true) 方法返回包括消息计数在内的队列属性。 [`ApproximateMessageCount`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.approximatemessagecount?view=azure-dotnet-legacy&preserve-view=true) 属性返回 `FetchAttributes` 方法检索到的最后一个值，而不会调用队列存储。

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Fetch the queue attributes.
queue.FetchAttributes();

// Retrieve the cached approximate message count.
int cachedMessageCount = queue.ApproximateMessageCount;

// Display number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

---

## <a name="delete-a-queue"></a>删除队列

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

若要删除队列及其包含的所有消息，请对队列对象调用 [`Delete`](/dotnet/api/azure.storage.queues.queueclient.delete) 方法。

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DeleteQueue":::

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnetv11)

若要删除队列及其包含的所有消息，请对队列对象调用 [`Delete`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.delete?view=azure-dotnet-legacy&preserve-view=true) 方法。

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Delete the queue.
queue.Delete();
```

---

## <a name="next-steps"></a>后续步骤

现在，你已了解了有关队列存储的基础知识，请单击下面的链接来了解更复杂的存储任务。

- 查看队列存储参考文档，了解有关可用 API 的完整详细信息：
  - [用于 .NET 的 Azure 存储客户端库参考](/dotnet/api/overview/azure/storage)
  - [Azure 存储 REST API 参考](/rest/api/storageservices/)
- 查看更多功能指南，以了解在 Azure 中存储数据的其他方式。
  - [Azure 表存储入门（使用 .NET）](../../cosmos-db/tutorial-develop-table-dotnet.md)，介绍如何存储结构化数据。
  - [Azure Blob 存储入门（使用 .NET）](../blobs/storage-quickstart-blobs-dotnet.md)，介绍如何存储非结构化数据。
  - [使用.NET (C#) 连接到 SQL 数据库](../../azure-sql/database/connect-query-dotnet-core.md)，存储关系数据。
- 了解如何通过使用 [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) 简化为使用 Azure 存储而写的代码。
