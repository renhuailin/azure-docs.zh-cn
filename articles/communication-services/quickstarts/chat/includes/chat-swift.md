---
title: include 文件
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
ms.openlocfilehash: 50c040b7c958d0398dba8026f6086b6f392870db
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122262911"
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
pod 'AzureCommunicationCommon', '~> 1.0'
pod 'AzureCommunicationChat', '~> 1.0.1'
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

使用下面的代码替换 `<CREATE A CHAT CLIENT>` 注释：

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

本快速入门不介绍如何创建服务层来管理聊天应用程序的令牌，尽管我们建议你这样做。 有关详细信息，请参阅[聊天概念](../../../concepts/chat/concepts.md)的“聊天体系结构”部分。

有关用户访问令牌的详细信息，请参阅[快速入门：创建和管理访问令牌](../../access-tokens.md)。

## <a name="object-model"></a>对象模型 

以下类和接口用于处理适用于 JavaScript 的 Azure 通信服务聊天 SDK 的某些主要功能。

| 名称                                   | 说明                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `ChatClient` | 聊天功能需要此类。 你使用订阅信息来实例化此类，使用此类来创建、获取和删除会话，以及订阅聊天事件。 |
| `ChatThreadClient` | 聊天会话功能需要此类。 通过 `ChatClient` 获取实例，并使用它来发送、接收、更新和删除消息。 还可以使用此类来添加、删除和获取用户，发送“正在键入”通知和已读回执。 |

## <a name="start-a-chat-thread"></a>启动聊天会话

现在使用自己的 `ChatClient` 来创建包含一个初始用户的新会话。

使用下面的代码替换 `<CREATE A CHAT THREAD>` 注释：

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

现在已经创建了聊天会话，接下来能够获得一个 `ChatThreadClient`，用于在会话内执行操作。

使用下面的代码替换 `<GET A CHAT THREAD CLIENT>` 注释：

```
let chatThreadClient = try chatClient.createClient(forThread: threadId!)
```

## <a name="send-a-message-to-a-chat-thread"></a>向聊天会话发送消息

使用下面的代码替换 `<SEND A MESSAGE>` 注释：

```
let message = SendChatMessageRequest(
    content: "Hello!",
    senderDisplayName: "Jack"
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

首先，构造 `SendChatMessageRequest`，其中包含内容和发送方显示名称。 如果需要的话，该请求还可包含共享历史记录时间。 返回到完成事件处理器的响应包含已发送消息的 ID。


## <a name="send-a-read-receipt"></a>发送阅读回执

可通过调用 `ChatThreadClients` 的 `sendReadReceipt` 方法发送特定消息的阅读回执。 使用下面的代码替换 `<SEND A READ RECEIPT>` 注释：

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

可通过调用 `ChatThreadClient` 中的 `listMessages()` 方法来从聊天会话接收消息。 列出的消息包括系统消息和用户发送的消息。 有关可接收消息的类型的详细信息，请参阅[消息类型](../../../concepts/chat/concepts.md#message-types)

使用下面的代码替换 `<RECEIVE MESSAGES>` 注释：

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

向会话添加参与者时，返回的响应可能包含错误。 这些错误表示未能添加特定参与者。

## <a name="list-users-in-a-thread"></a>列出会话中的用户

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

注意：请将 `Build Settings > Build Options > Enable Bitcode` 设置为 `No`。 目前，适用于 iOS 的 AzureCommunicationChat SDK 不支持启用 Bitcode，以下 [Github 问题](https://github.com/Azure/azure-sdk-for-ios/issues/787)正在跟踪此问题。
