---
title: 快速入门 - 加入 Teams 会议
author: agiurg
ms.author: agiurg
ms.date: 07/20/2021
ms.topic: include
ms.service: azure-communication-services
ms.openlocfilehash: 07a8af0c389b0b5c2216f45e0a3fb0ba053e5690
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114442873"
---
本快速入门介绍如何使用适用于 Android 的 Azure 通信服务聊天 SDK 在 Teams 会议中聊天。

## <a name="prerequisites"></a>先决条件 

-  [部署 Teams](/deployoffice/teams-install)。 
- [通话应用](../../voice-video-calling/get-started-teams-interop.md)正常运行。 

## <a name="enable-teams-interoperability"></a>启用 Teams 互操作性 

以来宾用户身份加入 Teams 会议的通信服务用户仅在加入 Teams 会议通话后才能访问会议的聊天。 请参阅 [Teams 互操作](../../voice-video-calling/get-started-teams-interop.md)文档，了解如何将通信服务用户添加到 Teams 会议通话。

你需要是拥有这两个实体的组织的成员才能使用此功能。

## <a name="joining-the-meeting-chat"></a>加入会议聊天 

启用 Teams 互操作性后，通信服务用户可以使用通话 SDK 以外部用户身份加入 Teams 通话。 用户加入通话时还会以参与者身份加入会议聊天，在聊天中用户可以与通话中的其他用户发送和接收消息。 用户将无法访问加入通话前发送的聊天消息。 若要加入会议并开始聊天，可以执行后续步骤。

## <a name="add-chat-to-the-teams-calling-app"></a>将聊天添加到 Teams 通话应用

在模块级别中，build.gradle 可在聊天 SDK 上添加依赖项。

> [!IMPORTANT]
> 已知问题：在同一应用程序中同时使用 Android 聊天和调用 SDK 时，聊天 SDK 的实时通知功能不起作用。 你将遇到依赖项解析问题。 在我们寻找解决方案期间，你可以通过将以下排除项添加到应用程序 `build.gradle` 文件中的聊天 SDK 依赖项来关闭实时通知功能：
> 
> ```groovy
> implementation ("com.azure.android:azure-communication-chat:1.0.0") {
>     exclude group: 'com.microsoft', module: 'trouter-client-android'
> }
> ```


## <a name="add-the-teams-ui-layout"></a>添加 Teams UI 布局

将 activity_main.xml 中的代码替换为以下代码片段。 此操作会添加用于线程 id 和发送消息的输入，以及用于发送类型化消息和基本聊天布局的按钮。

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <EditText
        android:id="@+id/teams_meeting_thread_id"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginHorizontal="20dp"
        android:layout_marginTop="128dp"
        android:ems="10"
        android:hint="Meeting Thread Id"
        android:inputType="textUri"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <EditText
        android:id="@+id/teams_meeting_link"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginHorizontal="20dp"
        android:layout_marginTop="64dp"
        android:ems="10"
        android:hint="Teams meeting link"
        android:inputType="textUri"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <LinearLayout
        android:id="@+id/button_layout"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="30dp"
        android:gravity="center"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintHorizontal_bias="0.0"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/teams_meeting_thread_id">

        <Button
            android:id="@+id/join_meeting_button"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Join Meeting" />

        <Button
            android:id="@+id/hangup_button"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Hangup" />

    </LinearLayout>

    <TextView
        android:id="@+id/call_status_bar"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="40dp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent" />

    <TextView
        android:id="@+id/recording_status_bar"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="20dp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent" />

    <ScrollView
        android:id="@+id/chat_box"
        android:layout_width="374dp"
        android:layout_height="294dp"
        android:layout_marginTop="40dp"
        android:layout_marginBottom="20dp"
        app:layout_constraintBottom_toTopOf="@+id/send_message_button"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/button_layout"
        android:orientation="vertical"
        android:gravity="bottom"
        android:layout_gravity="bottom"
        android:fillViewport="true">

        <LinearLayout
            android:id="@+id/chat_box_layout"
            android:orientation="vertical"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent"
            android:gravity="bottom"
            android:layout_gravity="top"
            android:layout_alignParentBottom="true"/>
    </ScrollView>

    <EditText
        android:id="@+id/message_body"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginHorizontal="20dp"
        android:layout_marginTop="588dp"
        android:ems="10"
        android:inputType="textUri"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        tools:text="Type your message here..."
        tools:visibility="invisible" />

    <Button
        android:id="@+id/send_message_button"
        android:layout_width="138dp"
        android:layout_height="45dp"
        android:layout_marginStart="133dp"
        android:layout_marginTop="48dp"
        android:layout_marginEnd="133dp"
        android:text="Send Message"
        android:visibility="invisible"
        app:layout_constraintBottom_toTopOf="@+id/recording_status_bar"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintHorizontal_bias="0.428"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/chat_box" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

## <a name="enable-the-teams-ui-controls"></a>启用 Teams UI 控件

### <a name="import-packages-and-define-state-variables"></a>导入包并定义状态变量

对于 `MainActivity.java` 的内容，请添加以下其他导入：

```
import android.graphics.Typeface;
import android.graphics.Color;
import android.text.Html;
import android.os.Handler;
import android.view.Gravity;
import android.view.View;
import android.widget.LinearLayout;
import java.util.Collections;
import java.util.concurrent.CountDownLatch;
import java.util.concurrent.TimeUnit;
import com.azure.android.communication.chat.ChatThreadAsyncClient;
import com.azure.android.communication.chat.ChatThreadClientBuilder;
import com.azure.android.communication.chat.models.ChatMessage;
import com.azure.android.communication.chat.models.ChatMessageType;
import com.azure.android.communication.chat.models.ChatParticipant;
import com.azure.android.communication.chat.models.ListChatMessagesOptions;
import com.azure.android.communication.chat.models.SendChatMessageOptions;
import com.azure.android.communication.common.CommunicationIdentifier;
import com.azure.android.communication.common.CommunicationUserIdentifier;
import com.azure.android.core.rest.util.paging.PagedAsyncStream;
import com.azure.android.core.util.AsyncStreamHandler;
```

对于 `MainActivity` 类，请添加以下变量：

```
    // InitiatorId is used to differentiate incoming messages from outgoing messages
    private static final String InitiatorId = "<USER_ID>";
    private static final String ResourceUrl = "<COMMUNICATION_SERVICES_RESOURCE_ENDPOINT>";
    private String threadId;
    private ChatThreadAsyncClient chatThreadAsyncClient;
    
    // The list of ids corresponsding to messages which have already been processed
    ArrayList<String> chatMessages = new ArrayList<>();
```

将 `<USER_ID>` 替换为发起聊天的用户的 ID。
将 `<COMMUNICATION_SERVICES_RESOURCE_ENDPOINT>` 替换为通信服务资源的终结点。 

### <a name="initialize-the-chatthreadclient"></a>初始化 ChatThreadClient

加入会议后，将 `ChatThreadClient` 实例化并让聊天组件可见。

用以下代码更新 `MainActivity.joinTeamsMeeting()` 方法的末尾：

```
    private void joinTeamsMeeting() {
        ...
        EditText threadIdView = findViewById(R.id.teams_meeting_thread_id);
        threadId = threadIdView.getText().toString();
        // Initialize Chat Thread Client
        chatThreadAsyncClient = new ChatThreadClientBuilder()
                .endpoint(ResourceUrl)
                .credential(new CommunicationTokenCredential(UserToken))
                .chatThreadId(threadId)
                .buildAsyncClient();
        Button sendMessageButton = findViewById(R.id.send_message_button);
        EditText messageBody = findViewById(R.id.message_body);
        // Register the method for sending messages and toggle the visibility of chat components
        sendMessageButton.setOnClickListener(l -> sendMessage());
        sendMessageButton.setVisibility(View.VISIBLE);
        messageBody.setVisibility(View.VISIBLE);
        
        // Start the polling for chat messages immediately
        handler.post(runnable);
    }
```

### <a name="enable-sending-messages"></a>启用消息发送

将 `sendMessage()` 方法添加到 `MainActivity`。 该方法将使用 `ChatThreadClient` 代表用户发送消息。

```
    private void sendMessage() {
        // Retrieve the typed message content
        EditText messageBody = findViewById(R.id.message_body);
        // Set request options and send message
        SendChatMessageOptions options = new SendChatMessageOptions();
        options.setContent(messageBody.getText().toString());
        options.setSenderDisplayName("ACS User");
        chatThreadAsyncClient.sendMessage(options);
        // Clear the text box
        messageBody.setText("");
    }
```

### <a name="enable-polling-for-messages-and-rendering-them-in-the-application"></a>启用消息轮询并在应用程序中呈现消息

> [!IMPORTANT]
> 已知问题：由于聊天 SDK 的实时通知功能无法与调用 SDK 配合使用，我们必须按照预定义的间隔轮询 `GetMessages` API。 我们将在示例中使用 3 秒间隔。

我们可以从 `GetMessages` API 返回的消息列表中获取以下数据： 
 - 自联接后线程上的 `text` 和 `html` 消息
 - 对线程名册做出的更改
 - 对线程主题做出的更新


对于 `MainActivity` 类，添加一个处理程序和一个将以 3 秒间隔运行的可运行任务：

```
    private Handler handler = new Handler();
    private Runnable runnable = new Runnable() {
        @Override
        public void run() {
            try {
                retrieveMessages();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            // Repeat every 3 seconds
            handler.postDelayed(runnable, 3000);
        }
    };
```

请注意，该任务已在初始化步骤中更新 `MainActivity.joinTeamsMeeting()` 方法的末尾启动。

最后，我们将添加该方法，以便查询线程上的所有可访问消息、按消息类型对其进行分析，并显示 `html` 和 `text` 消息：

```
    private void retrieveMessages() throws InterruptedException {
        // Initialize the list of messages not yet processed
        ArrayList<ChatMessage> newChatMessages = new ArrayList<>();
        
        // Retrieve all messages accessible to the user
        PagedAsyncStream<ChatMessage> messagePagedAsyncStream
                = this.chatThreadAsyncClient.listMessages(new ListChatMessagesOptions(), null);
        // Set up a lock to wait until all returned messages have been inspected
        CountDownLatch latch = new CountDownLatch(1);
        // Traverse the returned messages
        messagePagedAsyncStream.forEach(new AsyncStreamHandler<ChatMessage>() {
            @Override
            public void onNext(ChatMessage message) {
                // Messages that should be displayed in the chat
                if ((message.getType().equals(ChatMessageType.TEXT)
                    || message.getType().equals(ChatMessageType.HTML))
                    && !chatMessages.contains(message.getId())) {
                    newChatMessages.add(message);
                    chatMessages.add(message.getId());
                }
                if (message.getType().equals(ChatMessageType.PARTICIPANT_ADDED)) {
                    // Handle participants added to chat operation
                    List<ChatParticipant> participantsAdded = message.getContent().getParticipants();
                    CommunicationIdentifier participantsAddedBy = message.getContent().getInitiatorCommunicationIdentifier();
                }
                if (message.getType().equals(ChatMessageType.PARTICIPANT_REMOVED)) {
                    // Handle participants removed from chat operation
                    List<ChatParticipant> participantsRemoved = message.getContent().getParticipants();
                    CommunicationIdentifier participantsRemovedBy = message.getContent().getInitiatorCommunicationIdentifier();
                }
                if (message.getType().equals(ChatMessageType.TOPIC_UPDATED)) {
                    // Handle topic updated
                    String newTopic = message.getContent().getTopic();
                    CommunicationIdentifier topicUpdatedBy = message.getContent().getInitiatorCommunicationIdentifier();
                }
            }
            @Override
            public void onError(Throwable throwable) {
                latch.countDown();
            }
            @Override
            public void onComplete() {
                latch.countDown();
            }
        });
        // Wait until the operation completes
        latch.await(1, TimeUnit.MINUTES);
        // Returned messages should be ordered by the createdOn field to be guaranteed a proper chronological order
        // For the purpose of this demo we will just reverse the list of returned messages
        Collections.reverse(newChatMessages);
        for (ChatMessage chatMessage : newChatMessages)
        {
            LinearLayout chatBoxLayout = findViewById(R.id.chat_box_layout);
            // For the purpose of this demo UI, we don't need to use HTML formatting for displaying messages
            // The Teams client always sends html messages in meeting chats 
            String message = Html.fromHtml(chatMessage.getContent().getMessage(), Html.FROM_HTML_MODE_LEGACY).toString().trim();
            TextView messageView = new TextView(this);
            messageView.setText(message);
            // Compare with sender identifier and align LEFT/RIGHT accordingly
            // ACS users are of type CommunicationUserIdentifier
            CommunicationIdentifier senderId = chatMessage.getSenderCommunicationIdentifier();
            if (senderId instanceof CommunicationUserIdentifier
                && InitiatorId.equals(((CommunicationUserIdentifier) senderId).getId())) {
                messageView.setTextColor(Color.GREEN);
                messageView.setGravity(Gravity.RIGHT);
            } else {
                messageView.setTextColor(Color.BLUE);
                messageView.setGravity(Gravity.LEFT);
            }
            // Note: messages with the deletedOn property set to a timestamp, should be marked as deleted
            // Note: messages with the editedOn property set to a timestamp, should be marked as edited
            messageView.setTypeface(Typeface.SANS_SERIF, Typeface.BOLD);
            chatBoxLayout.addView(messageView);
        }
    }
```

聊天线程参与者的显示名称不由 Teams 客户端设置。 在 `participantsAdded` 事件和 `participantsRemoved` 事件当中，这些名称将在列出参与者的 API 中作为 null 返回。 可以从 `call` 对象的 `remoteParticipants` 字段检索聊天参与者的显示名称。

## <a name="get-a-teams-meeting-chat-thread-for-a-communication-services-user"></a>获取通信服务用户的 Teams 会议聊天线程

可以使用图形 API 来检索 Teams 会议链接和聊天，详情请参见[图形文档](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true)。 通信服务呼叫 SDK 接受完整的 Teams 会议链接。 此链接作为 `onlineMeeting` 资源的一部分返回，可在具有[图像 API](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true) 的 [`joinWebUrl` 属性](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true)下访问，也可获取 `threadId`。 响应将获得包含 `threadID` 的 `chatInfo` 对象。 

还可以从 Teams 会议邀请本身的“加入会议”URL 中获取所需的会议信息和线程 ID。
Teams 会议链接如下所示：`https://teams.microsoft.com/l/meetup-join/meeting_chat_thread_id/1606337455313?context=some_context_here`。 `threadId` 将为 `meeting_chat_thread_id` 在链接中的位置。 确保在使用 `meeting_chat_thread_id` 之前未对其进行转义。 该值应采用以下格式：`19:meeting_ZWRhZDY4ZGUtYmRlNS00OWZaLTlkZTgtZWRiYjIxOWI2NTQ4@thread.v2`


## <a name="run-the-code"></a>运行代码

现在可以使用工具栏上的“运行应用”按钮 (Shift+F10) 启动应用。

若要加入 Teams 会议和聊天，请在 UI 中输入团队的会议链接和线程 ID。

加入 Team 会议后，需要在 Team 客户端中允许用户加入会议。 允许用户加入聊天后，就可以发送和接收消息了。

:::image type="content" source="../join-teams-meeting-chat-quickstart-android.png" alt-text="已完成的 Android 应用程序的屏幕截图。":::

> [!NOTE] 
> 对于 Teams 中的互操作性方案，当前仅支持发送、接收和编辑消息。 尚不支持其他功能，例如键入指示符，以及通信服务用户从 Teams 会议添加或删除其他用户。
