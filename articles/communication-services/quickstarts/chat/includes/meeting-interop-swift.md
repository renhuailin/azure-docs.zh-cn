---
title: 快速入门 - 加入 Teams 会议
author: askaur
ms.author: askaur
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 06d7b776b49411fece14356f24a91f1a5df2b364
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121722544"
---
本快速入门介绍如何使用适用于 iOS 的 Azure 通信服务聊天 SDK 在 Teams 会议中聊天。

## <a name="prerequisites"></a>先决条件 

-  [部署 Teams](/deployoffice/teams-install)。 
- [通话应用](../../voice-video-calling/get-started-teams-interop.md)正常运行。 

## <a name="joining-the-meeting-chat"></a>加入会议聊天 

通信服务用户可以使用调用 SDK 作为匿名用户加入 Teams 会议。 用户加入会议时还会以参与者身份加入会议聊天，在聊天中用户可以与会议中的其他用户发送和接收消息。 用户将不能访问在其加入会议之前发送的聊天消息，且他们将无法在会议结束后发送或接收消息。 若要加入会议并开始聊天，可以执行后续步骤。

## <a name="add-chat-to-the-teams-calling-app"></a>将聊天添加到 Teams 通话应用

删除 `Podfile.lock`，并将 Podfile 的内容替换为以下内容：

```
platform :ios, '13.0'
use_frameworks!

target 'AzureCommunicationCallingSample' do
  pod 'AzureCommunicationCalling', '~> 1.0.0-beta.12'
  pod 'AzureCommunicationChat', '~> 1.0.0-beta.11'
end
```

## <a name="install-the-chat-packages"></a>安装聊天包

运行 `pod install` 以安装 AzureCommunicationChat 包。
安装后，打开 `.xcworkspace` 文件。

## <a name="set-up-the-app-framework"></a>设置应用框架

通过添加以下代码片段，在 `ContentView.swift` 中导入 AzureCommunicationChat 包：

```
import AzureCommunicationChat
```

## <a name="add-the-teams-ui-controls"></a>添加 Teams UI 控件

在 `ContentView.swift` 中，将以下代码片段添加到现有状态变量下方。 

```
    // Chat
    @State var chatClient: ChatClient?
    @State var chatThreadClient: ChatThreadClient?
    @State var chatMessage: String = ""
    @State var meetingMessages: [MeetingMessage] = []

    let displayName: String = "<YOUR_DISPLAY_NAME_HERE>"
```

将 `<YOUR_DISPLAY_NAME_HERE>` 替换为要用于聊天的显示名称。

接下来，将修改窗体 `Section` 以显示聊天消息，并添加用于聊天的 UI 控件。

将以下代码片段添加到现有窗体。 直接添加到“文本”视图 `Text(recordingStatus)` 之后，用于记录状态。

```
VStack(alignment: .leading) {
    ForEach(meetingMessages, id: \.id) { message in
        let currentUser: Bool = (message.displayName == self.displayName)
        let foregroundColor = currentUser ? Color.white : Color.black
        let background = currentUser ? Color.blue : Color(.systemGray6)
        let alignment = currentUser ? HorizontalAlignment.trailing : HorizontalAlignment.leading
        VStack {
            Text(message.displayName).font(Font.system(size: 10))
            Text(message.content)
        }
        .alignmentGuide(.leading) { d in d[alignment] }
        .padding(10)
        .foregroundColor(foregroundColor)
        .background(background)
        .cornerRadius(10)
    }
}.frame(minWidth: 0, maxWidth: .infinity)
TextField("Enter your message...", text: $chatMessage)
Button(action: sendMessage) {
    Text("Send Message")
}.disabled(chatThreadClient == nil)
```

接下来，将标题更改为 `Chat Teams Quickstart`。 修改具有此标题的以下行。

```
.navigationBarTitle("Chat Teams Quickstart")
```

## <a name="enable-the-teams-ui-controls"></a>启用 Teams UI 控件

### <a name="initialize-the-chatclient"></a>初始化 ChatClient

实例化 `ChatClient` 并启用实时通知。 我们将使用实时通知来接收聊天消息。

在 `NavigationView` `.onAppear` 中，在用于初始化 `CallAgent` 的现有代码后面添加以下代码段。

```
// Initialize the ChatClient
do {
    let endpoint = "COMMUNICATION_SERVICES_RESOURCE_ENDPOINT_HERE>"
    let credential = try CommunicationTokenCredential(token: "<USER_ACCESS_TOKEN_HERE>")

    self.chatClient = try ChatClient(
        endpoint: endpoint,
        credential: credential,
        withOptions: AzureCommunicationChatClientOptions()
    )

    self.message = "ChatClient successfully created"

    // Start real-time notifications
    self.chatClient?.startRealTimeNotifications() { result in
        switch result {
        case .success:
            print("Real-time notifications started")
            // Receive chat messages
            self.chatClient?.register(event: ChatEventId.chatMessageReceived, handler: receiveMessage)
        case .failure:
            print("Failed to start real-time notifications")
            self.message = "Failed to enable chat notifications"
        }
    }
} catch {
    print("Unable to create ChatClient")
    self.message = "Please enter a valid endpoint and Chat token in source code"
    return
}
```

将 `<COMMUNICATION_SERVICES_RESOURCE_ENDPOINT_HERE>` 替换为通信服务资源的终结点。
将 `<USER_ACCESS_TOKEN_HERE>` 替换为具有聊天范围的访问令牌。 

详细了解用户访问令牌：[用户访问令牌](../../access-tokens.md)

### <a name="initialize-the-chatthreadclient"></a>初始化 ChatThreadClient

在现有的 `joinTeamsMeeting()` 函数中，我们将在用户加入会议后初始化 `ChatThreadClient`。

在调用 `self.callAgent?.join()` 的完成处理程序中，在注释 `// Initialize the ChatThreadClient` 下面添加代码。 完整代码如下所示。

```
self.callAgent?.join(with: teamsMeetingLinkLocator, joinCallOptions: joinCallOptions) { (call, error) in
    if (error == nil) {
        self.call = call
        self.callObserver = CallObserver(self)
        self.call!.delegate = self.callObserver
        self.message = "Teams meeting joined successfully"

        // Initialize the ChatThreadClient
        do {
            guard let threadId = getThreadId(from: self.meetingLink) else {
                self.message = "Failed to join meeting chat"
                return
            }
            self.chatThreadClient = try chatClient?.createClient(forThread: threadId)
            self.message = "Joined meeting chat successfully"
        } catch {
            print("Failed to create ChatThreadClient")
            self.message = "Failed to join meeting chat"
            return
        }
    } else {
        print("Failed to join Teams meeting")
    }
}
```

向 `ContentView` 添加以下 helper 函数，该函数用于从团队的会议链接中检索聊天线程 ID。

```
func getThreadId(from meetingLink: String) -> String? {
    if let range = self.meetingLink.range(of: "meetup-join/") {
        let thread = self.meetingLink[range.upperBound...]
        if let endRange = thread.range(of: "/")?.lowerBound {
            return String(thread.prefix(upTo: endRange))
        }
    }
    return nil
}
```

### <a name="enable-sending-messages"></a>启用消息发送

添加 `sendMessage()` 函数到 `ContentView`。 此函数将使用 `ChatThreadClient` 发送来自用户的消息。

```
func sendMessage() {
    let message = SendChatMessageRequest(
        content: self.chatMessage,
        senderDisplayName: self.displayName
    )
    
    self.chatThreadClient?.send(message: message) { result, _ in
        switch result {
        case .success:
            print("Chat message sent")
        case .failure:
            print("Failed to send chat message")
        }

        self.chatMessage = ""
    }
}
```

### <a name="enable-receiving-messages"></a>启用消息接收

若要接收消息，我们将实现 `ChatMessageReceived` 事件的处理程序。 将新消息发送到线程时，此处理程序会将消息添加到 `meetingMessages` 变量中，以便可以在 UI 中显示这些消息。

首先，将以下结构添加到 `ContentView.swift`。 UI 将使用结构中的数据来显示聊天消息。

```
struct MeetingMessage {
    let id: String
    let content: String
    let displayName: String
}
```

然后将 `receiveMessage()` 函数添加到 `ContentView`。 这是每次发生 `ChatMessageReceived` 事件时调用的处理程序。

```
func receiveMessage(response: Any, eventId: ChatEventId) {
    let chatEvent: ChatMessageReceivedEvent = response as! ChatMessageReceivedEvent

    let displayName: String = chatEvent.senderDisplayName ?? "Unknown User"
    let content: String = chatEvent.message.replacingOccurrences(of: "<[^>]+>", with: "", options: String.CompareOptions.regularExpression)

    self.meetingMessages.append(
        MeetingMessage(
            id: chatEvent.id,
            content: content,
            displayName: displayName
        )
    )
}
```

### <a name="leave-the-chat"></a>离开聊天

当用户离开团队会议时，我们将从 UI 中清除聊天。 完整代码如下所示。

```
func leaveMeeting() {
    if let call = call {
        call.hangUp(options: nil, completionHandler: { (error) in
            if error == nil {
                self.message = "Leaving Teams meeting was successful"
                // Clear the chat
                self.meetingMessages.removeAll()
            } else {
                self.message = "Leaving Teams meeting failed"
            }
        })
    } else {
        self.message = "No active call to hanup"
    }
}
```

## <a name="get-a-teams-meeting-chat-thread-for-a-communication-services-user"></a>获取通信服务用户的 Teams 会议聊天线程

可以使用图形 API 来检索 Teams 会议链接和聊天，详情请参见[图形文档](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true)。 通信服务呼叫 SDK 接受完整的 Teams 会议链接。 此链接作为 `onlineMeeting` 资源的一部分返回，可在具有[图像 API](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true) 的 [`joinWebUrl` 属性](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true)下访问，也可获取 `threadId`。 响应将获得包含 `threadID` 的 `chatInfo` 对象。 

还可以从 Teams 会议邀请本身的“加入会议”URL 中获取所需的会议信息和线程 ID。
Teams 会议链接如下所示：`https://teams.microsoft.com/l/meetup-join/meeting_chat_thread_id/1606337455313?context=some_context_here`。 `threadId` 将为 `meeting_chat_thread_id` 在链接中的位置。 确保在使用 `meeting_chat_thread_id` 之前未对其进行转义。 该值应采用以下格式：`19:meeting_ZWRhZDY4ZGUtYmRlNS00OWZaLTlkZTgtZWRiYjIxOWI2NTQ4@thread.v2`


## <a name="run-the-code"></a>运行代码

运行该应用程序。 

若要加入 Teams 会议，请在 UI 中输入团队的会议链接。

加入 Team 会议后，需要在 Team 客户端中允许用户加入会议。 允许用户加入聊天后，就可以发送和接收消息了。

:::image type="content" source="../join-teams-meeting-chat-quickstart-ios.png" alt-text="已完成的 iOS 应用程序的屏幕截图。":::

> [!NOTE] 
> 对于 Teams 中的互操作性方案，当前仅支持发送、接收和编辑消息。 尚不支持其他功能，例如键入指示符，以及通信服务用户从 Teams 会议添加或删除其他用户。
