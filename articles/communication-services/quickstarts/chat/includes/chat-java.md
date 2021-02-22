---
title: include 文件
description: include 文件
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 9/1/2020
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 6a075ae721d767faf25e4774dd545d36eedfaef4
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100379640"
---
## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Java 开发工具包 (JDK)](/java/azure/jdk/?preserve-view=true&view=azure-java-stable) 8 或更高版本。
- [Apache Maven](https://maven.apache.org/download.cgi)。
- 已部署的通信服务资源和连接字符串。 [创建通信服务资源](../../create-communication-resource.md)。
- [用户访问令牌](../../access-tokens.md)。 请确保将范围设置为“聊天”，并记下令牌字符串和 userId 字符串。


## <a name="setting-up"></a>设置

### <a name="create-a-new-java-application"></a>创建新的 Java 应用程序

打开终端或命令窗口，并导航到要在其中创建 Java 应用程序的目录。 运行以下命令以从 maven-archetype-quickstart 模板生成 Java 项目。

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

你会注意到，“生成”目标创建了与 artifactId 名称相同的目录。 在此目录下，`src/main/java directory` 包含项目源代码，`src/test/java` 目录包含测试源，pom.xml 文件是项目的项目对象模型 ([POM](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html))。

将应用程序的 POM 文件更新为使用 Java 8 或更高版本：

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
</properties>
```

### <a name="add-the-package-references-for-the-chat-client-library"></a>添加聊天客户端库的包引用

在 POM 文件中，使用聊天 API 引用 `azure-communication-chat` 包：

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-chat</artifactId>
    <version>1.0.0-beta.4</version> 
</dependency>
```

若要进行身份验证，客户端需引用 `azure-communication-common` 包：

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-common</artifactId>
    <version>1.0.0-beta.4</version> 
</dependency>
```

## <a name="object-model"></a>对象模型

以下类和接口处理适用于 Java 的 Azure 通信服务聊天客户端库的某些主要功能。

| 名称                                  | 说明                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | 聊天功能需要此类。 使用订阅信息实例化此类，并使用它来创建、获取和删除会话。 |
| ChatAsyncClient | 异步聊天功能需要此类。 使用订阅信息实例化此类，并使用它来创建、获取和删除会话。 |
| ChatThreadClient | 聊天会话功能需要此类。 通过 ChatClient 获取实例，并使用它来发送/接收/更新/删除消息、添加/删除/获取用户、发送键入通知和阅读回执。 |
| ChatThreadAsyncClient | 异步聊天会话功能需要此类。 通过 ChatAsyncClient 获取实例，并使用它来发送/接收/更新/删除消息、添加/删除/获取用户、发送键入通知和阅读回执。 |

## <a name="create-a-chat-client"></a>创建聊天客户端
若要创建聊天客户端，需使用通信服务终结点以及在前提步骤中生成的访问令牌。 使用用户访问令牌可以生成直接对 Azure 通信服务进行身份验证的客户端应用程序。 在服务器上生成这些令牌后，将它们传回客户端设备。 需要使用 Common 客户端库中的 CommunicationTokenCredential 类将令牌传递到聊天客户端。 

添加导入语句时，请确保仅从 com.azure.communication.chat 和 com.azure.communication.chat.models 命名空间中添加，而不要从 com.azure.communication.chat.implementation 命名空间中添加。 在通过 Maven 生成的 App.java 文件中，可以使用以下代码开始操作：

```Java
import com.azure.communication.chat.*;
import com.azure.communication.chat.models.*;
import com.azure.communication.common.*;
import com.azure.core.http.HttpClient;

import java.io.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - Chat Quickstart");
        
        // Your unique Azure Communication service endpoint
        String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";

        // Create an HttpClient builder of your choice and customize it
        // Use com.azure.core.http.netty.NettyAsyncHttpClientBuilder if that suits your needs
        NettyAsyncHttpClientBuilder yourHttpClientBuilder = new NettyAsyncHttpClientBuilder();
        HttpClient httpClient = yourHttpClientBuilder.build();

        // User access token fetched from your trusted service
        String userAccessToken = "<USER_ACCESS_TOKEN>";

        // Create a CommunicationTokenCredential with the given access token, which is only valid until the token is valid
        CommunicationTokenCredential userCredential = new CommunicationTokenCredential(userAccessToken);

        // Initialize the chat client
        final ChatClientBuilder builder = new ChatClientBuilder();
        builder.endpoint(endpoint)
            .credential(userCredential)
            .httpClient(httpClient);
        ChatClient chatClient = builder.buildClient();
    }
}
```


## <a name="start-a-chat-thread"></a>启动聊天会话

使用 `createChatThread` 方法创建聊天会话。
`createChatThreadOptions` 用于描述会话请求。

- 使用 `topic` 为该聊天提供一个主题；在创建聊天会话后可使用 `UpdateThread` 函数更新主题。
- 使用 `participants` 列出要添加到会话的会话参与者。 `ChatParticipant` 使用你在[用户访问令牌](../../access-tokens.md)快速入门中创建的用户。

响应 `chatThreadClient` 用于对创建的聊天会话执行操作：向聊天会话添加参与者、发送消息、删除消息等。它包含 `chatThreadId` 属性（聊天会话的唯一 ID）。 可以通过公共方法 .getChatThreadId() 访问该属性。

```Java
List<ChatParticipant> participants = new ArrayList<ChatParticipant>();

ChatParticipant firstThreadParticipant = new ChatParticipant()
    .setUser(firstUser)
    .setDisplayName("Participant Display Name 1");
    
ChatParticipant secondThreadParticipant = new ChatParticipant()
    .setUser(secondUser)
    .setDisplayName("Participant Display Name 2");

participants.add(firstThreadParticipant);
participants.add(secondThreadParticipant);

CreateChatThreadOptions createChatThreadOptions = new CreateChatThreadOptions()
    .setTopic("Topic")
    .setParticipants(participants);
ChatThreadClient chatThreadClient = chatClient.createChatThread(createChatThreadOptions);
String chatThreadId = chatThreadClient.getChatThreadId();
```

## <a name="send-a-message-to-a-chat-thread"></a>在聊天会话中发送消息

使用 `sendMessage` 方法将消息发送到刚刚创建的由 chatThreadId 标识的会话。
`sendChatMessageOptions` 用于描述聊天消息请求。

- 使用 `content` 提供聊天消息内容。
- 使用 `type` 指定聊天消息内容类型（文本或 HTML）。
- 使用 `senderDisplayName` 指定发送方的显示名称。

响应 `sendChatMessageResult` 包含 `id`（消息的唯一 ID）。

```Java
SendChatMessageOptions sendChatMessageOptions = new SendChatMessageOptions()
    .setContent("Message content")
    .setType(ChatMessageType.TEXT)
    .setSenderDisplayName("Sender Display Name");

SendChatMessageResult sendChatMessageResult = chatThreadClient.sendMessage(sendChatMessageOptions);
String chatMessageId = sendChatMessageResult.getId();
```


## <a name="get-a-chat-thread-client"></a>获取聊天会话客户端

`getChatThreadClient` 方法返回某个已存在的会话的会话客户端。 可使用该方法对创建的会话执行操作：添加参与者、发送消息等。`chatThreadId` 是现有聊天会话的唯一 ID。

```Java
String chatThreadId = "Id";
ChatThread chatThread = chatClient.getChatThread(chatThreadId);
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>从聊天会话接收聊天消息

可以通过按指定间隔在聊天会话客户端上轮询 `listMessages` 方法来检索聊天消息。

```Java
chatThreadClient.listMessages().iterableByPage().forEach(resp -> {
    System.out.printf("Response headers are %s. Url %s  and status code %d %n", resp.getHeaders(),
        resp.getRequest().getUrl(), resp.getStatusCode());
    resp.getItems().forEach(message -> {
        System.out.printf("Message id is %s.", message.getId());
    });
});
```

`listMessages` 返回最新版本的消息，包括使用 .editMessage() 和 .deleteMessage() 对消息执行的任何编辑或删除操作。 对于已删除的消息，`chatMessage.getDeletedOn()` 返回一个日期/时间值，指示删除该消息的时间。 对于已编辑的消息，`chatMessage.getEditedOn()` 返回一个日期/时间值，指示编辑该消息的时间。 可以使用 `chatMessage.getCreatedOn()` 访问消息创建的原始时间，还可使用它对消息进行排序。

`listMessages` 返回可使用 `chatMessage.getType()` 标识的不同类型的消息。 这些类型包括：

- `Text`：会话参与者发送的普通聊天消息。

- `ThreadActivity/TopicUpdate`：指示主题已更新的系统消息。

- `ThreadActivity/AddMember`：指示已在聊天会话中添加一个或多个成员的系统消息。

- `ThreadActivity/DeleteMember`：指示已从聊天会话中删除某个成员的系统消息。

有关详细信息，请参阅[消息类型](../../../concepts/chat/concepts.md#message-types)。

## <a name="add-a-user-as-participant-to-the-chat-thread"></a>将用户作为参与者添加到聊天会话

创建聊天会话后，可以在其中添加和删除用户。 通过添加用户，可以向他们授予访问权限，使其能够在聊天会话中发送消息，以及添加/删除其他参与者。 首先需要获取该用户的新访问令牌和标识。 在调用 addParticipants 方法之前，请确保已获取该用户的新访问令牌和标识。 用户需要使用该访问令牌才能初始化聊天客户端。

使用 `addParticipants` 方法将参与者添加到由 threadId 标识的会话中。

- 使用 `listParticipants` 列出要添加到聊天会话的参与者。
- `user`（必需）是[用户访问令牌](../../access-tokens.md)快速入门中通过 CommunicationIdentityClient 创建的 CommunicationUserIdentifier。
- `display_name`（可选）是会话参与者的显示名称。
- `share_history_time`（可选）是开始与参与者共享聊天历史记录的时间。 若要在聊天会话开始后共享历史记录，请将此属性设置为等于或早于会话创建时间的任何日期。 若在添加参与者之前不共享任何历史记录，请将其设置为当前日期。 若要共享部分历史记录，请将其设置为所需日期。

```Java
List<ChatParticipant> participants = new ArrayList<ChatParticipant>();

ChatParticipant firstThreadParticipant = new ChatParticipant()
    .setUser(user1)
    .setDisplayName("Display Name 1");

ChatParticipant secondThreadParticipant = new ChatParticipant()
    .setUser(user2)
    .setDisplayName("Display Name 2");

participants.add(firstThreadParticipant);
participants.add(secondThreadParticipant);

AddChatParticipantsOptions addChatParticipantsOptions = new AddChatParticipantsOptions()
    .setParticipants(participants);
chatThreadClient.addParticipants(addChatParticipantsOptions);
```

## <a name="remove-user-from-a-chat-thread"></a>从聊天会话中删除用户

与将用户添加到会话类似，可以从聊天会话中删除用户。 若要执行此操作，需要跟踪已添加的参与者的用户标识。

使用 `removeParticipant`，其中 `user` 是已创建的 CommunicationUserIdentifier。

```Java
chatThreadClient.removeParticipant(user);
```

## <a name="run-the-code"></a>运行代码

导航到包含 pom.xml 文件的目录，并使用以下 `mvn` 命令编译该项目。

```console
mvn compile
```

然后，生成包。

```console
mvn package
```

运行以下 `mvn` 命令以执行应用。

```console
mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false
```