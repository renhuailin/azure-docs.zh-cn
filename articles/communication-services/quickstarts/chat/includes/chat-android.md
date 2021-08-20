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
ms.openlocfilehash: 41a25c5b92bfc91379ecf9c869a6c1dbbb7c9680
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "114201378"
---
## <a name="sample-code"></a>代码示例
在 [GitHub](https://github.com/Azure-Samples/communication-services-android-quickstarts/tree/main/Add-chat) 上查找此快速入门的最终代码。

## <a name="prerequisites"></a>先决条件

在开始之前，请务必：

- 创建活动订阅的 Azure 帐户。 有关详细信息，请参阅[创建免费账户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 安装 [Android Studio](https://developer.android.com/studio)，我们将使用 Android Studio 为快速入门创建一个 Android 应用程序以安装依赖项。
- 创建 Azure 通信服务资源。 有关详细信息，请参阅[创建 Azure 通信服务资源](../../create-communication-resource.md)。 为完成此快速入门，需要记录资源终结点。
- 创建 2 个通信服务用户，并向他们颁发用户访问令牌[用户访问令牌](../../access-tokens.md)。 请确保将范围设置为“聊天”，并记下令牌字符串和 userId 字符串 。 在本快速入门中，我们将创建一个包含初始参与者的会话，然后将第二个参与者添加到该会话中。

## <a name="setting-up"></a>设置

### <a name="create-a-new-android-application"></a>创建新的 Android 应用程序

1. 打开 Android Studio，然后选择 `Create a new project`。 
2. 在下一个窗口中，选择 `Empty Activity` 作为项目模板。
3. 在选择选项时，输入 `ChatQuickstart` 作为项目名称。
4. 单击“下一步”，然后选择要在其中创建项目的目录。

### <a name="install-the-libraries"></a>安装库

我们将使用 Gradle 来安装所需的通信服务依赖项。 在命令行中，导航到 `ChatQuickstart` 项目的根目录。 打开应用的 build.gradle 文件，并将以下依赖项添加到 `ChatQuickstart` 目标中：

```
implementation 'com.azure.android:azure-communication-common:1.0.1'
implementation 'com.azure.android:azure-communication-chat:1.0.0'
implementation 'org.slf4j:slf4j-log4j12:1.7.29'
```

#### <a name="exclude-meta-files-in-packaging-options-in-root-buildgradle"></a>在 root build.gradle 的打包选项中排除元文件
```
android {
   ...
    packagingOptions {
        exclude 'META-INF/DEPENDENCIES'
        exclude 'META-INF/LICENSE'
        exclude 'META-INF/license'
        exclude 'META-INF/NOTICE'
        exclude 'META-INF/notice'
        exclude 'META-INF/ASL2.0'
        exclude("META-INF/*.md")
        exclude("META-INF/*.txt")
        exclude("META-INF/*.kotlin_module")
    }
}
```

#### <a name="alternative-to-install-libraries-through-maven"></a>（替代方案）通过 Maven 安装库
若要使用 [Maven](https://maven.apache.org/) 生成系统将库导入项目，请将其添加到应用的 `pom.xml` 文件的 `dependencies` 部分，同时指定你要使用的项目 ID 和版本：

```xml
<dependency>
  <groupId>com.azure.android</groupId>
  <artifactId>azure-communication-chat</artifactId>
  <version>1.0.0</version>
</dependency>
```


### <a name="setup-the-placeholders"></a>设置占位符

打开并编辑文件 `MainActivity.java`。 在本快速入门中，我们要将代码添加到 `MainActivity`，并在控制台中查看输出。 本快速入门不涉及生成 UI。 在文件顶部，导入 `Communication common`、`Communication chat` 和其他系统库：

```
import com.azure.android.communication.chat.*;
import com.azure.android.communication.chat.models.*;
import com.azure.android.communication.common.*;

import android.os.Bundle;
import android.util.Log;
import android.widget.Toast;

import com.jakewharton.threetenabp.AndroidThreeTen;

import java.util.ArrayList;
import java.util.List;
```

将以下代码复制到文件 `MainActivity.java` 中的类 `MainActivity` 中：

```java
    private String endpoint = "https://<resource>.communication.azure.com";
    private String firstUserId = "<first_user_id>";
    private String secondUserId = "<second_user_id>";
    private String firstUserAccessToken = "<first_user_access_token>";
    private String threadId = "<thread_id>";
    private String chatMessageId = "<chat_message_id>";
    private final String sdkVersion = "1.0.0";
    private static final String APPLICATION_ID = "Chat Quickstart App";
    private static final String SDK_NAME = "azure-communication-com.azure.android.communication.chat";
    private static final String TAG = "Chat Quickstart App";

    private void log(String msg) {
        Log.i(TAG, msg);
        Toast.makeText(this, msg, Toast.LENGTH_LONG).show();
    }
    
   @Override
    protected void onStart() {
        super.onStart();
        try {
            AndroidThreeTen.init(this);

            // <CREATE A CHAT CLIENT>

            // <CREATE A CHAT THREAD>

            // <CREATE A CHAT THREAD CLIENT>

            // <SEND A MESSAGE>
            
            // <RECEIVE CHAT MESSAGES>

            // <ADD A USER>

            // <LIST USERS>

            // <REMOVE A USER>
            
            // <<SEND A TYPING NOTIFICATION>>
            
            // <<SEND A READ RECEIPT>>
               
            // <<LIST READ RECEIPTS>>
        } catch (Exception e){
            System.out.println("Quickstart failed: " + e.getMessage());
        }
    }
```

1. 将 `<resource>` 替换为通信服务资源。
2. 将 `<first_user_id>` 和 `<second_user_id>` 替换为在必要步骤中生成的有效通信服务用户 ID。
3. 将 `<first_user_access_token>` 替换为在必要步骤中生成的 `<first_user_id>` 的通信服务访问令牌。

在以下步骤中，我们将使用 Azure 通信服务聊天库将占位符替换为示例代码。


### <a name="create-a-chat-client"></a>创建聊天客户端

将注释 `<CREATE A CHAT CLIENT>` 替换为以下代码（将 import 语句放在文件顶部）：

```java
import com.azure.android.core.http.policy.UserAgentPolicy;

ChatAsyncClient chatAsyncClient = new ChatClientBuilder()
    .endpoint(endpoint)
    .credential(new CommunicationTokenCredential(firstUserAccessToken))
    .addPolicy(new UserAgentPolicy(APPLICATION_ID, SDK_NAME, sdkVersion))
    .buildAsyncClient();

```

## <a name="object-model"></a>对象模型
以下类和接口用于处理适用于 JavaScript 的 Azure 通信服务聊天 SDK 的某些主要功能。

| 名称                                   | 说明                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient/ChatAsyncClient | 聊天功能需要此类。 你使用订阅信息来实例化此类，使用此类来创建、获取和删除会话，以及订阅聊天事件。 |
| ChatThreadClient/ChatThreadAsyncClient | 聊天会话功能需要此类。 通过 ChatClient 获取实例，并使用它来发送/接收/更新/删除消息、添加/删除/获取用户、发送键入通知和阅读回执。 |

## <a name="start-a-chat-thread"></a>启动聊天会话

我们将使用自己的 `ChatAsyncClient` 来创建包含一个初始用户的新会话。

使用下面的代码替换 `<CREATE A CHAT THREAD>` 注释：

```java
// A list of ChatParticipant to start the thread with.
List<ChatParticipant> participants = new ArrayList<>();
// The display name for the thread participant.
String displayName = "initial participant";
participants.add(new ChatParticipant()
    .setCommunicationIdentifier(new CommunicationUserIdentifier(firstUserId))
    .setDisplayName(displayName));

// The topic for the thread.
final String topic = "General";
// Optional, set a repeat request ID.
final String repeatabilityRequestID = "";
// Options to pass to the create method.
CreateChatThreadOptions createChatThreadOptions = new CreateChatThreadOptions()
    .setTopic(topic)
    .setParticipants(participants)
    .setIdempotencyToken(repeatabilityRequestID);

CreateChatThreadResult createChatThreadResult =
    chatAsyncClient.createChatThread(createChatThreadOptions).get();
ChatThreadProperties chatThreadProperties = createChatThreadResult.getChatThreadProperties();
threadId = chatThreadProperties.getId();

```

## <a name="get-a-chat-thread-client"></a>获取聊天会话客户端

现在我们已经创建了聊天会话，接下来将获得一个 `ChatThreadAsyncClient`，用于在会话内执行操作。 使用下面的代码替换 `<CREATE A CHAT THREAD CLIENT>` 注释：

```
ChatThreadAsyncClient chatThreadAsyncClient = new ChatThreadClientBuilder()
    .endpoint(endpoint)
    .credential(new CommunicationTokenCredential(firstUserAccessToken))
    .addPolicy(new UserAgentPolicy(APPLICATION_ID, SDK_NAME, sdkVersion))
    .chatThreadId(threadId)
    .buildAsyncClient();

```

## <a name="send-a-message-to-a-chat-thread"></a>向聊天会话发送消息

现在将消息发送到该会话。

使用下面的代码替换 `<SEND A MESSAGE>` 注释：

```java
// The chat message content, required.
final String content = "Test message 1";
// The display name of the sender, if null (i.e. not specified), an empty name will be set.
final String senderDisplayName = "An important person";
SendChatMessageOptions chatMessageOptions = new SendChatMessageOptions()
    .setType(ChatMessageType.TEXT)
    .setContent(content)
    .setSenderDisplayName(senderDisplayName);

// A string is the response returned from sending a message, it is an id, which is the unique ID of the message.
chatMessageId = chatThreadAsyncClient.sendMessage(chatMessageOptions).get().getId();

```

## <a name="receive-chat-messages-from-a-chat-thread"></a>从聊天会话接收聊天消息
使用实时信令，可以订阅新的传入消息并相应更新内存中的当前消息。 Azure 通信服务支持[你可以订阅的事件列表](../../../concepts/chat/concepts.md#real-time-notifications)。

将注释 `<RECEIVE CHAT MESSAGES>` 替换为以下代码（将 import 语句放在文件顶部）：

```java

// Start real time notification
chatAsyncClient.startRealtimeNotifications(firstUserAccessToken, getApplicationContext());

// Register a listener for chatMessageReceived event
chatAsyncClient.addEventHandler(ChatEventType.CHAT_MESSAGE_RECEIVED, (ChatEvent payload) -> {
    ChatMessageReceivedEvent chatMessageReceivedEvent = (ChatMessageReceivedEvent) payload;
    // You code to handle chatMessageReceived event
    
});

```

> [!IMPORTANT]
> 已知问题：在同一应用程序中同时使用 Android 聊天和调用 SDK 时，聊天 SDK 的实时通知功能不起作用。 可能会遇到依赖项解析问题。
> 虽然正在研究解决方案，但可通过在应用的 build.gradle 文件中添加以下依赖项信息来关闭实时通知功能，并轮询 GetMessages API 以向用户显示传入消息。 
> 
> ```
> implementation ("com.azure.android:azure-communication-chat:1.0.0") {
>     exclude group: 'com.microsoft', module: 'trouter-client-android'
> }
> implementation 'com.azure.android:azure-communication-calling:1.0.0'
> ```
> 
> 对于上面的更新，请注意，如果应用程序尝试触摸任何通知 API（如 `chatAsyncClient.startRealtimeNotifications()` 或 `chatAsyncClient.addEventHandler()`），则会出现运行时错误。


## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>将用户作为参与者添加到聊天会话

使用下面的代码替换 `<ADD A USER>` 注释：

```java
// The display name for the thread participant.
String secondUserDisplayName = "a new participant";
ChatParticipant participant = new ChatParticipant()
    .setCommunicationIdentifier(new CommunicationUserIdentifier(secondUserId))
    .setDisplayName(secondUserDisplayName);
        
chatThreadAsyncClient.addParticipant(participant);

```


## <a name="list-users-in-a-thread"></a>列出会话中的用户

将注释 `<LIST USERS>` 替换为以下代码（将 import 语句放在文件顶部）：

```java
import com.azure.android.core.rest.util.paging.PagedAsyncStream;
import com.azure.android.core.util.RequestContext;

// The maximum number of participants to be returned per page, optional.
int maxPageSize = 10;

// Skips participants up to a specified position in response.
int skip = 0;

// Options to pass to the list method.
ListParticipantsOptions listParticipantsOptions = new ListParticipantsOptions()
    .setMaxPageSize(maxPageSize)
    .setSkip(skip);

PagedAsyncStream<ChatParticipant> participantsPagedAsyncStream =
      chatThreadAsyncClient.listParticipants(listParticipantsOptions, RequestContext.NONE);

participantsPagedAsyncStream.forEach(chatParticipant -> {
    // You code to handle participant
});

```


## <a name="remove-user-from-a-chat-thread"></a>从聊天会话中删除用户

现在从该会话中删除第二个用户。

使用下面的代码替换 `<REMOVE A USER>` 注释：

```java
// Using the unique ID of the participant.
chatThreadAsyncClient.removeParticipant(new CommunicationUserIdentifier(secondUserId)).get();

```

## <a name="send-a-typing-notification"></a>发送键入通知

使用下面的代码替换 `<SEND A TYPING NOTIFICATION>` 注释：

```java
chatThreadAsyncClient.sendTypingNotification().get();
```

## <a name="send-a-read-receipt"></a>发送阅读回执

将为上面发送的消息发送阅读回执。

使用下面的代码替换 `<SEND A READ RECEIPT>` 注释：

```java
chatThreadAsyncClient.sendReadReceipt(chatMessageId).get();
```

## <a name="list-read-receipts"></a>列出阅读回执

使用下面的代码替换 `<READ RECEIPTS>` 注释：

```java
// The maximum number of participants to be returned per page, optional.
maxPageSize = 10;
// Skips participants up to a specified position in response.
skip = 0;
// Options to pass to the list method.
ListReadReceiptOptions listReadReceiptOptions = new ListReadReceiptOptions()
    .setMaxPageSize(maxPageSize)
    .setSkip(skip);

PagedAsyncStream<ChatMessageReadReceipt> readReceiptsPagedAsyncStream =
      chatThreadAsyncClient.listReadReceipts(listReadReceiptOptions, RequestContext.NONE);

readReceiptsPagedAsyncStream.forEach(readReceipt -> {
    // You code to handle readReceipt
});

```

## <a name="run-the-code"></a>运行代码

在 Android Studio 中，点击“运行”按钮以生成并运行项目。 在控制台中，可以查看代码的输出和 ChatClient 的记录器输出。
