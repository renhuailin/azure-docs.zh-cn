---
title: include 文件
description: 包含文件
services: azure-communication-services
author: probableprime
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: rifox
ms.openlocfilehash: d61120d3f4e1256b9da50b8128b23fd73b36a5ef
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2021
ms.locfileid: "129406836"
---
## <a name="sample-code"></a>代码示例
在 [GitHub](https://github.com/Azure-Samples/communication-services-ios-quickstarts/tree/main/add-chat) 上查找此快速入门的最终代码。

## <a name="prerequisites"></a>先决条件
在开始之前，请务必：

- 创建活动订阅的 Azure 帐户。 有关详细信息，请参阅[创建免费账户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- 安装 [Xcode](https://developer.apple.com/xcode/) 和 [CocoaPods](https://cocoapods.org/)。 使用 Xcode 创建用于快速入门的 iOS 应用程序，并使用 Cocoapods 来安装依赖项。
- 创建 Azure 通信服务资源。 有关详细信息，请参阅[快速入门：创建和管理通信服务资源](../../create-communication-resource.md)。 为完成此快速入门，需要记录资源终结点。
- 在 Azure 通信服务中创建 2 个用户，并向他们[颁发用户访问令牌](../../access-tokens.md)。 请确保将范围设置为 `chat`，并记下 `token`字符串和 `userId` 字符串。 在本快速入门中，创建一个包含初始参与者的会话，然后将第二个参与者添加到该会话中。

## <a name="setting-up"></a>设置

### <a name="create-a-new-ios-application"></a>创建新的 iOS 应用程序

打开 Xcode，并选择“新建 Xcode 项目”。 然后选择“iOS”作为平台，选择“应用”作为模板 。

在项目名称处，输入 ChatQuickstart。 然后选择“Storyboard”作为界面，“UIKit App Delegate”作为生命周期，“Swift”作为语言  。

选择“下一步”，然后选择要在其中创建项目的目录。

### <a name="install-the-libraries"></a>安装库

使用 Cocoapods 来安装所需的通信服务依赖项。

在命令行中，导航到 `ChatQuickstart` iOS 项目的根目录。 通过以下命令创建 Podfile：`pod init`。

打开 Podfile，并向 `ChatQuickstart` 目标添加以下依赖项：

```
pod 'AzureCommunicationCommon', '~> 1.0.2'
pod 'AzureCommunicationChat', '~> 1.1.0-beta.2'
```

通过以下命令安装依赖项：`pod install`。 请注意，这还会创建 Xcode 工作区。

运行 `pod install` 后，通过选择新建的 `.xcworkspace`以重新在 Xcode 中打开项目。

### <a name="set-up-the-placeholders"></a>设置占位符

在 Xcode 中打开工作区 `ChatQuickstart.xcworkspace`，然后打开 `ViewController.swift`。

在本快速入门中，将代码添加到 `viewController`，并在 Xcode 控制台中查看输出。 本快速入门不涉及在 iOS 中构建用户界面。 

在 `viewController.swift` 的顶部导入 `AzureCommunication` 和 `AzureCommunicatonChat` 库：

```
import AzureCommunicationCommon
import AzureCommunicationChat
```

将以下代码复制到 `ViewController` 的 `viewDidLoad()` 方法中：

```
override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view.

        let semaphore = DispatchSemaphore(value: 0)
        DispatchQueue.global(qos: .background).async {
            do {
                // <CREATE A CHAT CLIENT>
                
                // <CREATE A CHAT THREAD>

                // <LIST ALL CHAT THREADS>

                // <GET A CHAT THREAD CLIENT>

                // <SEND A MESSAGE>

                // <SEND A READ RECEIPT >

                // <RECEIVE MESSAGES>

                // <ADD A USER>
                
                // <LIST USERS>
            } catch {
                print("Quickstart failed: \(error.localizedDescription)")
            }
        }
    }
```

为了便于演示，我们将使用信号灯来同步代码。 在以下步骤中，使用 Azure 通信服务聊天库将占位符替换为示例代码。


### <a name="create-a-chat-client"></a>创建聊天客户端

若要创建聊天客户端，你需要使用通信服务终结点以及在先决条件步骤中生成的访问令牌。

详细了解[用户访问令牌](../../access-tokens.md)。

本快速入门不介绍如何创建服务层来管理聊天应用程序的令牌，尽管我们建议你这样做。 详细了解[聊天体系结构](../../../concepts/chat/concepts.md)

将注释 `<CREATE A CHAT CLIENT>` 替换为以下代码片段：

```
let endpoint = "<ACS_RESOURCE_ENDPOINT>"
let credential =
try CommunicationTokenCredential(
    token: "<ACCESS_TOKEN>"
)
let options = AzureCommunicationChatClientOptions()

let chatClient = try ChatClient(
    endpoint: endpoint,
    credential: credential,
    withOptions: options
)
```

将 `<ACS_RESOURCE_ENDPOINT>` 替换为 Azure 通信服务资源的终结点。 将 `<ACCESS_TOKEN>` 替换为有效的通信服务访问令牌。

## <a name="object-model"></a>对象模型 

以下类和接口用于处理适用于 iOS 的 Azure 通信服务聊天 SDK 的某些主要功能。

| 名称                                   | 说明                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `ChatClient` | 聊天功能需要此类。 你使用订阅信息来实例化此类，使用此类来创建、获取和删除会话，以及订阅聊天事件。 |
| `ChatThreadClient` | 聊天会话功能需要此类。 通过 `ChatClient` 获取实例，并使用它来发送、接收、更新和删除消息。 还可以使用此类来添加、删除和获取用户，发送“正在键入”通知和已读回执。 |

## <a name="start-a-chat-thread"></a>启动聊天会话

`CreateChatThreadResult` 是从创建聊天线程返回的响应。
它包含 `chatThread` 属性（`ChatThreadProperties` 对象）。 此对象包含的 threadId 可用于获取 `ChatThreadClient`，后者用于对创建的会话执行各种操作：添加参与者、发送消息等。

将注释 `<CREATE A CHAT THREAD>` 替换为以下代码片段：

```
let request = CreateChatThreadRequest(
    topic: "Quickstart",
    participants: [
        ChatParticipant(
            id: CommunicationUserIdentifier("<USER_ID>"),
            displayName: "Jack"
        )
    ]
)

var threadId: String?
chatClient.create(thread: request) { result, _ in
    switch result {
    case let .success(result):
        threadId = result.chatThread?.id

    case .failure:
        fatalError("Failed to create thread.")
    }
    semaphore.signal()
}
semaphore.wait()
```

将 `<USER_ID>` 替换为有效的通信服务用户 ID。

在此处使用信号灯等待完成事件处理器，然后再继续。 在后续步骤中，将使用返回到完成事件处理器的响应中的 `threadId`。

## <a name="list-all-chat-threads"></a>列出所有聊天会话

创建聊天会话后，可以通过在 `ChatClient` 上调用 `listChatThreads` 方法来列出所有聊天会话。 使用下面的代码替换 `<LIST ALL CHAT THREADS>` 注释：

```
chatClient.listThreads { result, _ in
    switch result {
    case let .success(threads):
        guard let chatThreadItems = threads.pageItems else {
            print("No threads returned.")
            return
        }

        for chatThreadItem in chatThreadItems {
            print("Thread id: \(chatThreadItem.id)")
        }
    case .failure:
        print("Failed to list threads")
    }
    semaphore.signal()
}
semaphore.wait()
```

## <a name="get-a-chat-thread-client"></a>获取聊天会话客户端

`createClient` 方法返回已存在的会话的 `ChatThreadClient`。 可使用该方法在创建的会话上执行操作：添加参与者、发送消息等，threadId 是现有聊天会话的唯一 ID。

使用下面的代码替换 `<GET A CHAT THREAD CLIENT>` 注释：

```
let chatThreadClient = try chatClient.createClient(forThread: threadId!)
```

## <a name="send-a-message-to-a-chat-thread"></a>向聊天会话发送消息

使用 `send` 方法将消息发送到由 threadId 标识的会话。

`SendChatMessageRequest` 用于描述消息请求：

- 使用 `content` 提供聊天消息内容
- 使用 `senderDisplayName` 指定发送方的显示名称
- 使用 `type` 指定消息类型，例如 'text' 或 'html'
- 可以选择使用 `metadata` 来包含想随消息一起发送的任何附加数据。 此字段为开发人员提供了一种机制，可用于扩展聊天消息功能并为用例添加自定义信息。 例如，在消息中共享文件链接时，你可能希望在元数据中添加“hasAttachment:true”，以便收件人的应用程序可分析并相应显示。

`SendChatMessageResult` 是发送消息后返回的响应，它包含一个 ID（是消息的唯一 ID）。

将注释 `<SEND A MESSAGE>` 替换为以下代码片段：

```
let message = SendChatMessageRequest(
                        content: "Hello!",
                        senderDisplayName: "Jack",
                        type: .text,
                        metadata: [
                            "hasAttachment": "true",
                            "attachmentUrl": "https://contoso.com/files/attachment.docx"
                        ]
                    )

var messageId: String?

chatThreadClient.send(message: message) { result, _ in
    switch result {
    case let .success(result):
        print("Message sent, message id: \(result.id)")
        messageId = result.id
    case .failure:
        print("Failed to send message")
    }
    semaphore.signal()
}
semaphore.wait()
```

## <a name="send-a-read-receipt"></a>发送阅读回执

`sendReadReceipt` 方法可用于代表用户将阅读回执事件发布到会话。
`messageId` 是已读聊天消息的唯一 ID。

将注释 `<SEND A READ RECEIPT>` 替换为以下代码：

```
if let id = messageId {
    chatThreadClient.sendReadReceipt(forMessage: id) { result, _ in
        switch result {
        case .success:
            print("Read receipt sent")
        case .failure:
            print("Failed to send read receipt")
        }
        semaphore.signal()
    }
    semaphore.wait()
} else {
    print("Cannot send read receipt without a message id")
}
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>从聊天会话接收聊天消息

使用实时信令，你可以订阅以侦听新的传入消息并相应更新内存中的当前消息。 Azure 通信服务支持[你可以订阅的事件列表](../../../concepts/chat/concepts.md#real-time-notifications)。

将注释 `<RECEIVE MESSAGES>` 替换为以下代码。 启用通知后，请尝试发送新消息以查看 ChatMessageReceivedEvents。

```
chatClient.startRealTimeNotifications { result in
    switch result {
    case .success:
        print("Real-time notifications started.")
    case .failure:
        print("Failed to start real-time notifications.")
    }
    semaphore.signal()
}
semaphore.wait()

chatClient.register(event: .chatMessageReceived, handler: { response in
    switch response {
    case let .chatMessageReceivedEvent(event):
        print("Received a message: \(event.message)")
    default:
        return
    }
})
```

或者可以通过按指定间隔轮询 `listMessages` 方法来检索聊天消息。 请查看以下适用于 `listMessages` 的代码片段

```
chatThreadClient.listMessages { result, _ in
    switch result {
    case let .success(messagesResult):
        guard let messages = messagesResult.pageItems else {
            print("No messages returned.")
            return
        }

        for message in messages {
            print("Received message with id: \(message.id)")
        }

    case .failure:
        print("Failed to receive messages")
    }
    semaphore.signal()
}
semaphore.wait()
```

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>将用户作为参与者添加到聊天会话

创建会话后，可以在其中添加和删除用户。 通过添加用户，可以向他们授予访问权限，使其能够向会话发送消息，以及添加/删除其他参与者。 在调用 `add` 之前，请确保已获取该用户的新访问令牌和标识。 用户需要使用该访问令牌才能初始化其聊天客户端。

使用 `ChatThreadClient` 的 `add` 方法将一个或多个参与者添加到聊天会话。 下面是对每个会话参与者支持的属性：
- `id`，必需，是会话参与者的标识。
- `displayName`（可选）是会话参与者的显示名称。
- `shareHistoryTime`，可选，是开始与参与者共享聊天历史记录的时间。

使用下面的代码替换 `<ADD A USER>` 注释：

```
let user = ChatParticipant(
    id: CommunicationUserIdentifier("<USER_ID>"),
    displayName: "Jane"
)

chatThreadClient.add(participants: [user]) { result, _ in
    switch result {
    case let .success(result):
        (result.invalidParticipants != nil) ? print("Added participant") : print("Error adding participant")
    case .failure:
        print("Failed to add the participant")
    }
    semaphore.signal()
}
semaphore.wait()
```

将 `<USER_ID>` 替换为要添加的用户的通信服务用户 ID。

## <a name="list-users-in-a-thread"></a>列出会话中的用户

使用 `listParticipants` 方法可获取特定聊天会话的所有参与者。

使用下面的代码替换 `<LIST USERS>` 注释：

```
chatThreadClient.listParticipants { result, _ in
    switch result {
    case let .success(participantsResult):
        guard let participants = participantsResult.pageItems else {
            print("No participants returned.")
            return
        }

        for participant in participants {
            let user = participant.id as! CommunicationUserIdentifier
            print("User with id: \(user.identifier)")
        }
    case .failure:
        print("Failed to list participants")
    }
    semaphore.signal()
}
semaphore.wait()
```

## <a name="run-the-code"></a>运行代码

在 Xcode 中，单击“运行”按钮以生成并运行项目。 在控制台中，可以从代码中查看输出，并从 ChatClient 查看记录器输出。

注意：请将 `Build Settings > Build Options > Enable Bitcode` 设置为 `No`。 目前，适用于 iOS 的 AzureCommunicationChat SDK 不支持启用 Bitcode，以下 [GitHub 问题](https://github.com/Azure/azure-sdk-for-ios/issues/787)跟踪的就是此问题。
