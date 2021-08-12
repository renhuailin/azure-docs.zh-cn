---
title: 包含文件
description: 包含文件
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 73621e7ef9f68747edde9cfb16289fb6dc82695a
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "114201504"
---
## <a name="sample-code"></a>代码示例
在 [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/add-chat) 上查找此快速入门的最终代码。

## <a name="prerequisites"></a>先决条件
在开始之前，请务必：
- 创建活动订阅的 Azure 帐户。 有关详细信息，请参阅[创建免费账户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 安装 [Visual Studio](https://visualstudio.microsoft.com/downloads/)
- 创建 Azure 通信服务资源。 有关详细信息，请参阅[创建 Azure 通信服务资源](../../create-communication-resource.md)。 需要为此快速入门记录资源终结点。
- [用户访问令牌](../../access-tokens.md)。 请确保将范围设置为“聊天”，并记下令牌字符串和 userId 字符串。

## <a name="setting-up"></a>设置

### <a name="create-a-new-c-application"></a>新建 C# 应用程序

在控制台窗口（例如 cmd、PowerShell 或 Bash）中，使用 `dotnet new` 命令创建名为 `ChatQuickstart` 的新控制台应用。 此命令将创建包含单个源文件的简单“Hello World”C# 项目：**Program.cs**。

```console
dotnet new console -o ChatQuickstart
```

将目录更改为新创建的应用文件夹，并使用 `dotnet build` 命令编译应用程序。

```console
cd ChatQuickstart
dotnet build
```

### <a name="install-the-package"></a>安装包

安装适用于 .NET 的 Azure 通信聊天 SDK

```PowerShell
dotnet add package Azure.Communication.Chat --version 1.0.0
```

## <a name="object-model"></a>对象模型

以下类用于处理适用于 C# 的 Azure 通信服务聊天 SDK 的某些主要功能。

| 名称                                  | 说明                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | 聊天功能需要此类。 使用订阅信息实例化此类，并使用它来创建、获取和删除会话。 |
| ChatThreadClient | 聊天会话功能需要此类。 通过 ChatClient 获取实例，并使用它来发送/接收/更新/删除消息、添加/删除/获取参与者、发送键入通知和阅读回执。 |

## <a name="create-a-chat-client"></a>创建聊天客户端

若要创建聊天客户端，你需要使用通信服务终结点以及在先决条件步骤中生成的访问令牌。 你需要使用标识 SDK 中的 `CommunicationIdentityClient` 类来创建用户，并颁发要传递到聊天客户端的令牌。

详细了解[用户访问令牌](../../access-tokens.md)。

本快速入门不介绍如何创建服务层来管理聊天应用程序的令牌，尽管我们建议你这样做。 详细了解[聊天体系结构](../../../concepts/chat/concepts.md)

复制以下代码片段，并将其粘贴到源文件 Program.cs 中
```csharp
using Azure;
using Azure.Communication;
using Azure.Communication.Chat;
using System;

namespace ChatQuickstart
{
    class Program
    {
        static async System.Threading.Tasks.Task Main(string[] args)
        {
            // Your unique Azure Communication service endpoint
            Uri endpoint = new Uri("https://<RESOURCE_NAME>.communication.azure.com");

            CommunicationTokenCredential communicationTokenCredential = new CommunicationTokenCredential(<Access_Token>);
            ChatClient chatClient = new ChatClient(endpoint, communicationTokenCredential);
        }
    }
}
```

## <a name="start-a-chat-thread"></a>启动聊天会话

在 chatClient 上使用 `createChatThread` 方法创建聊天会话
- 使用 `topic` 为该聊天提供一个主题；在创建聊天会话后可使用 `UpdateTopic` 函数更新主题。
- 使用 `participants` 属性传递要添加到聊天会话的 `ChatParticipant` 对象的列表。 使用 `CommunicationIdentifier` 对象初始化 `ChatParticipant` 对象。 `CommunicationIdentifier` 可以是 `CommunicationUserIdentifier`、`MicrosoftTeamsUserIdentifier` 或 `PhoneNumberIdentifier` 类型。 例如，若要获取 `CommunicationIdentifier` 对象，需要传递一个已按照[创建用户](../../access-tokens.md#create-an-identity)中的说明创建的访问 ID

`createChatThread` 方法的 response 对象包含 `chatThread` 详细信息。 若要与聊天会话操作（例如添加参与者、发送消息、删除消息，等等）进行交互，需要在 `ChatClient` 客户端上使用 `GetChatThreadClient` 方法将 `chatThreadClient` 客户端实例实例化。

```csharp
var chatParticipant = new ChatParticipant(identifier: new CommunicationUserIdentifier(id: "<Access_ID>"))
{
    DisplayName = "UserDisplayName"
};
CreateChatThreadResult createChatThreadResult = await chatClient.CreateChatThreadAsync(topic: "Hello world!", participants: new[] { chatParticipant });
ChatThreadClient chatThreadClient = chatClient.GetChatThreadClient(threadId: createChatThreadResult.ChatThread.Id);
string threadId = chatThreadClient.Id;
```

## <a name="get-a-chat-thread-client"></a>获取聊天会话客户端
`GetChatThreadClient` 方法返回某个已存在的会话的会话客户端。 它可用于在创建的会话上执行操作：添加成员、发送消息等，threadId 是现有聊天会话的唯一 ID。

```csharp
string threadId = "<THREAD_ID>";
ChatThreadClient chatThreadClient = chatClient.GetChatThreadClient(threadId: threadId);
```

## <a name="list-all-chat-threads"></a>列出所有聊天会话
使用 `GetChatThreads` 可检索用户所属的所有聊天线程。

```csharp
AsyncPageable<ChatThreadItem> chatThreadItems = chatClient.GetChatThreadsAsync();
await foreach (ChatThreadItem chatThreadItem in chatThreadItems)
{
    Console.WriteLine($"{ chatThreadItem.Id}");
}
```

## <a name="send-a-message-to-a-chat-thread"></a>向聊天会话发送消息

使用 `SendMessage` 向会话发送消息。

- 使用 `content` 提供消息的内容，它是必需的。
- 使用 `type` 表示消息的内容类型，例如 'Text' 或 'Html'。 如果未指定，则会将其设置为 'Text'。
- 使用 `senderDisplayName` 指定发送方的显示名称。 如果未指定，则会将其设置为空字符串。

```csharp
SendChatMessageResult sendChatMessageResult = await chatThreadClient.SendMessageAsync(content:"hello world", type: ChatMessageType.Text);
string messageId = sendChatMessageResult.Id;
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>从聊天会话接收聊天消息

可以通过按指定间隔在聊天会话客户端上轮询 `GetMessages` 方法来检索聊天消息。

```csharp
AsyncPageable<ChatMessage> allMessages = chatThreadClient.GetMessagesAsync();
await foreach (ChatMessage message in allMessages)
{
    Console.WriteLine($"{message.Id}:{message.Content.Message}");
}
```

`GetMessages` 采用可选的 `DateTimeOffset` 参数。 如果指定了该偏移量，你将收到在其之后接收、更新或删除的消息。 请注意，还会返回在偏移时间之前收到但在该时间之后编辑过或删除的消息。

`GetMessages` 返回最新版本的消息，包括使用 `UpdateMessage` 和 `DeleteMessage` 对消息执行的任何编辑或删除。 对于已删除的消息，`chatMessage.DeletedOn` 返回日期/时间值，该值指示该消息删除的时间。 对于已编辑的消息，`chatMessage.EditedOn` 返回一个日期/时间值，指示编辑该消息的时间。 可以使用 `chatMessage.CreatedOn` 访问消息创建的原始时间，还可使用它对消息进行排序。

`GetMessages` 返回可使用 `chatMessage.Type` 标识的不同类型的消息。 这些类型包括：

- `Text`：会话成员发送的普通聊天消息。

- `Html`：格式化文本消息。 请注意，通信服务用户当前无法发送 RichText 消息。 在 Teams Interop 方案中，此消息类型支持将消息从 Teams 用户传递到通信服务用户。

- `TopicUpdated`：指示主题已更新的系统消息。 （只读）

- `ParticipantAdded`：系统消息，指示一个或多个参与者已添加到聊天会话。（只读）

- `ParticipantRemoved`：指示已从聊天会话中删除参与者的系统消息。

有关详细信息，请参阅[消息类型](../../../concepts/chat/concepts.md#message-types)。

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>将用户作为参与者添加到聊天会话

创建会话后，可以在其中添加和删除用户。 通过添加用户，可以向他们授予访问权限，使其能够向会话发送消息，以及添加/删除其他参与者。 在调用 `AddParticipants` 之前，请确保已获取该用户的新访问令牌和标识。 用户需要使用该访问令牌才能初始化其聊天客户端。

使用 `AddParticipants` 将一个或多个参与者添加到聊天会话。 下面是对每个会话参与者支持的属性：
- `communicationUser`，必需，是会话参与者的标识。
- `displayName`（可选）是会话参与者的显示名称。
- `shareHistoryTime`，可选，是开始与参与者共享聊天历史记录的时间。

```csharp
var josh = new CommunicationUserIdentifier(id: "<Access_ID_For_Josh>");
var gloria = new CommunicationUserIdentifier(id: "<Access_ID_For_Gloria>");
var amy = new CommunicationUserIdentifier(id: "<Access_ID_For_Amy>");

var participants = new[]
{
    new ChatParticipant(josh) { DisplayName = "Josh" },
    new ChatParticipant(gloria) { DisplayName = "Gloria" },
    new ChatParticipant(amy) { DisplayName = "Amy" }
};

await chatThreadClient.AddParticipantsAsync(participants: participants);
```

## <a name="get-thread-participants"></a>获取会话参与者

使用 `GetParticipants` 检索聊天会话的参与者。

```csharp
AsyncPageable<ChatParticipant> allParticipants = chatThreadClient.GetParticipantsAsync();
await foreach (ChatParticipant participant in allParticipants)
{
    Console.WriteLine($"{((CommunicationUserIdentifier)participant.User).Id}:{participant.DisplayName}:{participant.ShareHistoryTime}");
}
```

## <a name="send-read-receipt"></a>发送阅读回执

使用 `SendReadReceipt` 通知其他参与者：用户已阅读该消息。

```csharp
await chatThreadClient.SendReadReceiptAsync(messageId: messageId);
```

## <a name="run-the-code"></a>运行代码

从应用程序目录使用 `dotnet run` 命令运行应用程序。

```console
dotnet run
```
