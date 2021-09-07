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
ms.openlocfilehash: b5ea5a90a6ad39404208bb9c353bb33e86201299
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2021
ms.locfileid: "122967867"
---
## <a name="sample-code"></a>代码示例
在 [GitHub](https://github.com/Azure-Samples/communication-services-java-quickstarts/tree/main/chat-quickstart-java) 上查找此快速入门的最终代码。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Java 开发工具包 (JDK)](/azure/developer/java/fundamentals/java-jdk-install) 8 或更高版本。
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

### <a name="add-the-package-references-for-the-chat-sdk"></a>添加聊天 SDK 的包引用

在 POM 文件中，使用聊天 API 引用 `azure-communication-chat` 包：

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-chat</artifactId>
    <version><!-- Please refer to https://search.maven.org/artifact/com.azure/azure-communication-chat for the latest version --></version>
</dependency>
```

若要进行身份验证，客户端需引用 `azure-communication-common` 包：

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-common</artifactId>
    <version><!-- Please refer to https://search.maven.org/artifact/com.azure/azure-communication-common for the latest version --></version>
</dependency>
```

## <a name="object-model"></a>对象模型

以下类和接口用于处理适用于 Java 的 Azure 通信服务聊天 SDK 的某些主要功能。

| 名称                                  | 说明                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | 聊天功能需要此类。 使用订阅信息实例化此类，并使用它来创建、获取和删除会话。 |
| ChatAsyncClient | 异步聊天功能需要此类。 使用订阅信息实例化此类，并使用它来创建、获取和删除会话。 |
| ChatThreadClient | 聊天会话功能需要此类。 通过 ChatClient 获取实例，并使用它来发送/接收/更新/删除消息、添加/删除/获取用户、发送键入通知和阅读回执。 |
| ChatThreadAsyncClient | 异步聊天会话功能需要此类。 通过 ChatAsyncClient 获取实例，并使用它来发送/接收/更新/删除消息、添加/删除/获取用户、发送键入通知和阅读回执。 |

## <a name="create-a-chat-client"></a>创建聊天客户端
若要创建聊天客户端，需使用通信服务终结点以及在前提步骤中生成的访问令牌。 使用用户访问令牌可以生成直接对 Azure 通信服务进行身份验证的客户端应用程序。 在服务器上生成这些令牌后，将它们传回客户端设备。 你需要使用 Common SDK 中的 CommunicationTokenCredential 类将令牌传递到聊天客户端。

详细了解[聊天体系结构](../../../concepts/chat/concepts.md)

添加导入语句时，请确保仅从 com.azure.communication.chat 和 com.azure.communication.chat.models 命名空间中添加，而不要从 com.azure.communication.chat.implementation 命名空间中添加。 在通过 Maven 生成的 App.java 文件中，可以使用以下代码开始操作：

```Java
package com.communication.quickstart;

import com.azure.communication.chat.*;
import com.azure.communication.chat.models.*;
import com.azure.communication.common.*;
import com.azure.core.http.rest.PagedIterable;

import java.io.*;
import java.util.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - Chat Quickstart");

        // Your unique Azure Communication service endpoint
        String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";

        // User access token fetched from your trusted service
        String userAccessToken = "<USER_ACCESS_TOKEN>";

        // Create a CommunicationTokenCredential with the given access token, which is only valid until the token is valid
        CommunicationTokenCredential userCredential = new CommunicationTokenCredential(userAccessToken);

        // Initialize the chat client
        final ChatClientBuilder builder = new ChatClientBuilder();
        builder.endpoint(endpoint)
            .credential(userCredential);
        ChatClient chatClient = builder.buildClient();
    }
}
```

## <a name="start-a-chat-thread"></a>启动聊天会话

使用 `createChatThread` 方法创建聊天会话。
`createChatThreadOptions` 用于描述会话请求。

- 使用构造函数的 `topic` 参数提供此聊天主题；在使用 `UpdateThread` 函数创建聊天会话后主题将会更新。
- 使用 `participants` 列出要添加到会话的会话参与者。 `ChatParticipant` 使用你在[用户访问令牌](../../access-tokens.md)快速入门中创建的用户。

`CreateChatThreadResult` 是从创建聊天线程返回的响应。
它包含一个返回 `ChatThread` 对象的 `getChatThread()` 方法，该对象可从你获取 `ChatThreadClient` 的位置得到会话客户端以便操作所创建的会：添加参与者、发送消息等。`ChatThread` 对象还包含用于检索会员唯一 ID 的 `getId()` 方法。

```Java
CommunicationUserIdentifier identity1 = new CommunicationUserIdentifier("<USER_1_ID>");
CommunicationUserIdentifier identity2 = new CommunicationUserIdentifier("<USER_2_ID>");

ChatParticipant firstThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(identity1)
    .setDisplayName("Participant Display Name 1");

ChatParticipant secondThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(identity2)
    .setDisplayName("Participant Display Name 2");

CreateChatThreadOptions createChatThreadOptions = new CreateChatThreadOptions("Topic")
    .addParticipant(firstThreadParticipant)
    .addParticipant(secondThreadParticipant);

CreateChatThreadResult result = chatClient.createChatThread(createChatThreadOptions);
String chatThreadId = result.getChatThread().getId();
```

## <a name="list-chat-threads"></a>列出聊天会话

使用 `listChatThreads` 方法检索现有聊天会话的列表。

```java
PagedIterable<ChatThreadItem> chatThreads = chatClient.listChatThreads();

chatThreads.forEach(chatThread -> {
    System.out.printf("ChatThread id is %s.\n", chatThread.getId());
});
```

## <a name="get-a-chat-thread-client"></a>获取聊天会话客户端

`getChatThreadClient` 方法返回某个已存在的会话的会话客户端。 可使用该方法对创建的会话执行操作：添加参与者、发送消息等。`chatThreadId` 是现有聊天会话的唯一 ID。

```Java
ChatThreadClient chatThreadClient = chatClient.getChatThreadClient(chatThreadId);
```

## <a name="send-a-message-to-a-chat-thread"></a>在聊天会话中发送消息

使用 `sendMessage` 方法将消息发送到刚刚创建的由 chatThreadId 标识的会话。
`sendChatMessageOptions` 用于描述聊天消息请求。

- 使用 `content` 提供聊天消息内容。
- 使用 `type` 指定聊天消息内容类型（文本或 HTML）。
- 使用 `senderDisplayName` 指定发送方的显示名称。
- 可以选择使用 `metadata` 来包含想随消息一起发送的任何附加数据。 此字段为开发人员提供了一种机制，可用于扩展聊天消息功能并为用例添加自定义信息。 例如，在消息中共享文件链接时，你可能希望在元数据中添加“hasAttachment:true”，以便收件人的应用程序可分析并相应显示。

响应 `sendChatMessageResult` 包含 `id`（消息的唯一 ID）。

```Java
Map<String, String> metadata = new HashMap<String, String>();
metadata.put("hasAttachment", "true");
metadata.put("attachmentUrl", "https://contoso.com/files/attachment.docx");

SendChatMessageOptions sendChatMessageOptions = new SendChatMessageOptions()
    .setContent("Please take a look at the attachment")
    .setType(ChatMessageType.TEXT)
    .setSenderDisplayName("Sender Display Name")
    .setMetadata(metadata);

SendChatMessageResult sendChatMessageResult = chatThreadClient.sendMessage(sendChatMessageOptions);
String chatMessageId = sendChatMessageResult.getId();
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>从聊天会话接收聊天消息

可以通过按指定间隔在聊天会话客户端上轮询 `listMessages` 方法来检索聊天消息。

```Java
chatThreadClient.listMessages().forEach(message -> {
    System.out.printf("Message id is %s.\n", message.getId());
});
```

`listMessages` 返回最新版本的消息，包括使用 .editMessage() 和 .deleteMessage() 对消息执行的任何编辑或删除操作。 对于已删除的消息，`chatMessage.getDeletedOn()` 返回一个日期/时间值，指示删除该消息的时间。 对于已编辑的消息，`chatMessage.getEditedOn()` 返回一个日期/时间值，指示编辑该消息的时间。 可以使用 `chatMessage.getCreatedOn()` 访问消息创建的原始时间，还可使用它对消息进行排序。

单击此处了解有关消息类型的详细信息：[消息类型](../../../concepts/chat/concepts.md#message-types)。

## <a name="send-read-receipt"></a>发送阅读回执

`sendReadReceipt` 方法可用于代表用户将阅读回执事件发布到会话。
`chatMessageId` 是已读聊天消息的唯一 ID。

```Java
String chatMessageId = message.getId();
chatThreadClient.sendReadReceipt(chatMessageId);
```

## <a name="list-chat-participants"></a>列出聊天参与者

使用 `listParticipants` 检索包含由 chatThreadId 标识的聊天线程参与者的分页集合。

```Java
PagedIterable<ChatParticipant> chatParticipantsResponse = chatThreadClient.listParticipants();
chatParticipantsResponse.forEach(chatParticipant -> {
    System.out.printf("Participant id is %s.\n", ((CommunicationUserIdentifier) chatParticipant.getCommunicationIdentifier()).getId());
});
```

## <a name="add-a-user-as-participant-to-the-chat-thread"></a>将用户作为参与者添加到聊天会话

创建聊天会话后，可以在其中添加和删除用户。 通过添加用户，可以向他们授予访问权限，使其能够在聊天会话中发送消息，以及添加/删除其他参与者。 首先需要获取该用户的新访问令牌和标识。 在调用 addParticipants 方法之前，请确保已获取该用户的新访问令牌和标识。 用户需要使用该访问令牌才能初始化聊天客户端。

使用 `addParticipants` 方法将参与者添加到该会话中。

- `communicationIdentifier`，必需，是你已在[用户访问令牌](../../access-tokens.md)快速入门中通过 CommunicationIdentityClient 创建的 CommunicationIdentifier。
- `displayName`（可选）是会话参与者的显示名称。
- `shareHistoryTime`（可选）是开始与参与者共享聊天历史记录的时间。 若要在聊天会话开始后共享历史记录，请将此属性设置为等于或小于会话创建时间的任何日期。 若在添加参与者之前不共享任何历史记录，请将其设置为当前日期。 若要共享部分历史记录，请将其设置为所需日期。

```Java
List<ChatParticipant> participants = new ArrayList<ChatParticipant>();

CommunicationUserIdentifier identity3 = new CommunicationUserIdentifier("<USER_3_ID>");
CommunicationUserIdentifier identity4 = new CommunicationUserIdentifier("<USER_4_ID>");

ChatParticipant thirdThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(identity3)
    .setDisplayName("Display Name 3");

ChatParticipant fourthThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(identity4)
    .setDisplayName("Display Name 4");

participants.add(thirdThreadParticipant);
participants.add(fourthThreadParticipant);

chatThreadClient.addParticipants(participants);
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