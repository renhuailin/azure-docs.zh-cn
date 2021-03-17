---
title: include 文件
description: include 文件
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: dedea2a622cb0eece92bb8b57871c76daa05fb68
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2021
ms.locfileid: "103495373"
---
## <a name="prerequisites"></a>先决条件
在开始之前，请务必：

- 创建活动订阅的 Azure 帐户。 有关详细信息，请参阅[创建免费账户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- 安装 [Xcode](https://developer.apple.com/xcode/) 和 [Cocoapods](https://cocoapods.org/)，我们将使用 Xcode 创建用于快速入门的 iOS 应用程序，并使用 Cocoapods 来安装依赖项。
- 创建 Azure 通信服务资源。 有关详细信息，请参阅[创建 Azure 通信服务资源](../../create-communication-resource.md)。 为完成此快速入门，需要记录资源终结点。
- 创建 2 个 ACS 用户，并向他们颁发用户访问令牌[用户访问令牌](../../access-tokens.md)。 请确保将范围设置为“聊天”，并记下令牌字符串和 userId 字符串 。 在本快速入门中，我们将创建一个包含初始参与者的会话，然后将第二个参与者添加到该会话。

## <a name="setting-up"></a>设置

### <a name="create-a-new-ios-application"></a>创建新的 iOS 应用程序

打开 Xcode，并选择 `Create a new Xcode project`。

在下一个窗口中，选择 `iOS` 作为平台，选择 `App` 作为模板。

选择选项时，输入 `ChatQuickstart` 作为项目名称。 选择 `Storyboard` 作为界面，`UIKit App Delegate` 作为生命周期，`Swift` 作为语言。

单击“下一步”，然后选择要在其中创建项目的目录。

### <a name="install-the-libraries"></a>安装库

我们将使用 Cocoapods 来安装所需的通信服务依赖项。

在命令行中，导航到 `ChatQuickstart` iOS 项目的根目录。

创建 Podfile：`pod init`

打开 Podfile，并向 `ChatQuickstart` 目标添加以下依赖项：
```
pod 'AzureCommunication', '~> 1.0.0-beta.9'
pod 'AzureCommunicationChat', '~> 1.0.0-beta.9'
```

安装依赖项，这还将创建 Xcode 工作区：`pod install`

**运行 pod install 后，通过选择新创建的 `.xcworkspace` 来重新在 Xcode 中打开项目。**

### <a name="setup-the-placeholders"></a>设置占位符

在 Xcode 中打开工作区 `ChatQuickstart.xcworkspace`，然后打开 `ViewController.swift`。

在本快速入门中，我们要将代码添加到 `viewController`，并在 Xcode 控制台中查看输出。 此快速入门不涉及在 iOS 中构建 UI。 

在 `viewController.swift` 的顶部导入 `AzureCommunication` 和 `AzureCommunicatonChat` 库：

```
import AzureCommunication
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
                
                // <CREATE A CHAT THREAD CLIENT>
                
                // <SEND A MESSAGE>
                
                // <ADD A USER>
                
                // <LIST USERS>
                
                // <REMOVE A USER>
            } catch {
                print("Quickstart failed: \(error.localizedDescription)")
            }
        }
    }
```

为了便于演示，我们将使用信号灯来同步代码。 在以下步骤中，我们将使用 Azure 通信服务聊天库将占位符替换为示例代码。


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

将 `<ACS_RESOURCE_ENDPOINT>` 替换为 ACS 资源的终结点。
将 `<ACCESS_TOKEN>` 替换为有效的 ACS 访问令牌。

本快速入门不介绍如何创建服务层来管理聊天应用程序的令牌，尽管我们建议你这样做。 有关详细信息，请参阅以下文档：[聊天体系结构](../../../concepts/chat/concepts.md)

详细了解[用户访问令牌](../../access-tokens.md)。

## <a name="object-model"></a>对象模型 
以下类和接口处理适用于 JavaScript 的 Azure 通信服务聊天客户端库的某些主要功能。

| 名称                                   | 说明                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient | 聊天功能需要此类。 使用订阅信息实例化此类，并使用它来创建、获取和删除会话。 |
| ChatThreadClient | 聊天会话功能需要此类。 通过 ChatClient 获取实例，并使用它来发送/接收/更新/删除消息、添加/删除/获取用户、发送键入通知以及阅读回执和订阅聊天事件。 |

## <a name="start-a-chat-thread"></a>启动聊天会话

现在，我们将使用 `ChatClient` 来创建包含一个初始用户的新会话。

使用下面的代码替换 `<CREATE A CHAT THREAD>` 注释：

```
let request = CreateThreadRequest(
    topic: "Quickstart",
    participants: [
        Participant(
            id: CommunicationUserIdentifier("<USER_ID>"),
            displayName: "Jack"
        )
    ]
)

var threadId: String?
chatClient.create(thread: request) { result, _ in
    switch result {
    case let .success(result):
        threadId = result.thread?.id

    case .failure:
        fatalError("Failed to create thread.")
    }
    semaphore.signal()
}
semaphore.wait()
```

将 `<USER_ID>` 替换为有效的通信服务用户 ID。

我们将在此处使用信号灯等待完成事件处理器，然后再继续。 在后续步骤中，我们将使用返回到完成事件处理器的响应中的 `threadId`。

## <a name="get-a-chat-thread-client"></a>获取聊天会话客户端

现在我们已经创建了聊天会话，接下来将获得一个 `ChatThreadClient`，用于在会话内执行操作。

使用下面的代码替换 `<CREATE A CHAT THREAD CLIENT>` 注释：

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

chatThreadClient.send(message: message) { result, _ in
    switch result {
    case let .success(result):
        print("Message sent, message id: \(result.id)")
    case .failure:
        print("Failed to send message")
    }
    semaphore.signal()
}
semaphore.wait()
```

首先构造 `SendChatMessageRequest`，其中包含内容和发送方显示名称（还可选择包含共享历史记录时间）。 返回到完成事件处理器的响应包含已发送消息的 ID。

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>将用户作为参与者添加到聊天会话

使用下面的代码替换 `<ADD A USER>` 注释：

```
let user = Participant(
    id: CommunicationUserIdentifier("<USER_ID>"),
    displayName: "Jane"
)

chatThreadClient.add(participants: [user]) { result, _ in
    switch result {
    case let .success(result):
        (result.errors != nil) ? print("Added participant") : print("Error adding participant")
    case .failure:
        print("Failed to list participants")
    }
    semaphore.signal()
}
semaphore.wait()
```

将 `<USER_ID>` 替换为要添加的用户的 ACS 用户 ID。

向会话添加参与者时，返回完成的响应可能包含错误。 这些错误表示未能添加特定参与者。

## <a name="list-users-in-a-thread"></a>列出会话中的用户

使用下面的代码替换 `<LIST USERS>` 注释：

```
chatThreadClient.listParticipants { result, _ in
    switch result {
    case let .success(participants):
        var iterator = participants.syncIterator
        while let participant = iterator.next() {
            let user = participant.id as! CommunicationUserIdentifier
            print(user.identifier)
        }
    case .failure:
        print("Failed to list participants")
    }
    semaphore.signal()
}
semaphore.wait()
```


## <a name="remove-user-from-a-chat-thread"></a>从聊天会话中删除用户

使用下面的代码替换 `<REMOVE A USER>` 注释：

```
chatThreadClient
    .remove(
        participant: CommunicationUserIdentifier("<USER_ID>")
    ) { result, _ in
        switch result {
        case .success:
            print("Removed user from the thread.")
        case .failure:
            print("Failed to remove user from the thread.")
        }
    }
```

将 `<USER ID>` 替换为要删除的参与者的通信服务用户 ID。

## <a name="run-the-code"></a>运行代码

在 Xcode 中，单击“运行”按钮以生成并运行项目。 在控制台中，可以从代码中查看输出，并从 ChatClient 查看记录器输出。


