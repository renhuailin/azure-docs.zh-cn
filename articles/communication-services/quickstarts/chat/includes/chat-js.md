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
ms.openlocfilehash: e8afed5b318a3a6601d90fcd235476174e40e358
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2021
ms.locfileid: "122967864"
---
## <a name="sample-code"></a>代码示例
在 [GitHub](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/add-chat) 上查找此快速入门的最终代码。

## <a name="prerequisites"></a>先决条件
在开始之前，请务必：

- 创建活动订阅的 Azure 帐户。 有关详细信息，请参阅[创建免费账户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 安装 [Node.js](https://nodejs.org/en/download/) Active LTS 和 Maintenance LTS 版本。
- 创建 Azure 通信服务资源。 有关详细信息，请参阅[创建 Azure 通信服务资源](../../create-communication-resource.md)。 为完成此快速入门，需要记录资源终结点。
- 创建 3 个 ACS 用户，并向他们颁发用户访问令牌[用户访问令牌](../../access-tokens.md)。 请确保将范围设置为“聊天”，并记下令牌字符串和 userId 字符串 。 完整的演示将创建一个具有两个初始参与者的会话，然后将第三个参与者添加到该会话。

## <a name="setting-up"></a>设置

### <a name="create-a-new-web-application"></a>新建 Web 应用程序

首先，打开终端或命令窗口，为应用创建一个新目录，并导航到该目录。

```console
mkdir chat-quickstart && cd chat-quickstart
```

运行 `npm init -y` 以使用默认设置创建 package.json 文件。

```console
npm init -y
```

### <a name="install-the-packages"></a>安装包

使用 `npm install` 命令安装适用于 JavaScript 的以下通信服务 SDK。

```console
npm install @azure/communication-common --save

npm install @azure/communication-identity --save

npm install @azure/communication-signaling --save

npm install @azure/communication-chat --save

```

`--save` 选项将该库作为 package.json 文件中的依赖项列出。

### <a name="set-up-the-app-framework"></a>设置应用框架

此快速入门使用 webpack 捆绑应用程序资产。 运行以下命令以安装 webpack、 webpack-cli 和 webpack-dev-server npm 包，并将它们作为 package.json 中的开发依赖项列出：

```console
npm install webpack webpack-cli webpack-dev-server --save-dev
```

在根目录中创建一个 `webpack.config.js` 文件。 将以下配置复制到此文件中：

```
module.exports = {
  entry: "./client.js",
  output: {
    filename: "bundle.js"
  },
  devtool: "inline-source-map",
  mode: "development"
}
```

向 `package.json` 添加 `start` 脚本，我们将使用它来运行应用程序。 在“`package.json`”的“`scripts`”部分添加以下内容：

```
"scripts": {
  "start": "webpack serve --config ./webpack.config.js"
}
```

在项目的根目录中创建一个 index.html 文件。 我们将使用此文件作为模板，使用适用于 JavaScript 的 Azure 通信聊天 SDK 添加聊天功能。

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Communication Client - Chat Sample</title>
  </head>
  <body>
    <h4>Azure Communication Services</h4>
    <h1>Chat Quickstart</h1>
    <script src="./bundle.js"></script>
  </body>
</html>
```

在名为 client.js 的项目的根目录中创建一个文件，以包含此快速入门的应用程序逻辑。

### <a name="create-a-chat-client"></a>创建聊天客户端

若要在 Web 应用中创建聊天客户端，需使用通信服务终结点和在前提步骤中生成的访问令牌 。

用户访问令牌使你可以构建直接对 Azure 通信服务进行身份验证的客户端应用程序。 本快速入门不介绍如何创建服务层来管理聊天应用程序的令牌。 有关聊天体系结构的详细信息，请参阅[聊天概念](../../../concepts/chat/concepts.md)，有关访问令牌的详细信息，请参阅[用户访问令牌](../../access-tokens.md)。

在 client.js 内，使用下面代码中的终结点和访问令牌，以及适用于 JavaScript 的 Azure 通信聊天 SDK 添加聊天功能。

```JavaScript

import { ChatClient } from '@azure/communication-chat';
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

// Your unique Azure Communication service endpoint
let endpointUrl = 'https://<RESOURCE_NAME>.communication.azure.com';
// The user access token generated as part of the pre-requisites
let userAccessToken = '<USER_ACCESS_TOKEN>';

let chatClient = new ChatClient(endpointUrl, new AzureCommunicationTokenCredential(userAccessToken));
console.log('Azure Communication Chat client created!');
```
- 将 endpointUrl 替换为通信服务资源终结点，如果尚未执行此操作，请参阅[创建 Azure 通信服务资源](../../create-communication-resource.md)。
- 将 userAccessToken 替换为你颁发的令牌。


### <a name="run-the-code"></a>运行代码

运行以下命令，在本地 webserver 上捆绑应用程序主机：
```console
npm run start
```
打开浏览器并导航到 http://localhost:8080/。
在浏览器中的“开发人员工具”控制台中，应会显示以下内容：

```console
Azure Communication Chat client created!
```

## <a name="object-model"></a>对象模型
以下类和接口用于处理适用于 JavaScript 的 Azure 通信服务聊天 SDK 的某些主要功能。

| 名称                                   | 说明                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient | 聊天功能需要此类。 你使用订阅信息来实例化此类，使用此类来创建、获取和删除会话，并且订阅到聊天事件。 |
| ChatThreadClient | 聊天会话功能需要此类。 通过 ChatClient 获取实例，并使用它来发送/接收/更新/删除消息、添加/删除/获取用户、发送键入通知和阅读回执。 |


## <a name="start-a-chat-thread"></a>启动聊天会话

使用 `createThread` 方法创建聊天会话。

`createThreadRequest` 用于描述会话请求：

- 使用 `topic` 为此聊天指定主题。 在创建聊天会话后可使用 `UpdateThread` 函数更新主题。
- 使用 `participants` 列出要添加到聊天会话的参与者。

解析时，`createChatThread` 方法返回 `CreateChatThreadResult`。 此模型包含 `chatThread` 属性，可在其中访问新创建会话的 `id`。 然后可使用 `id` 获取 `ChatThreadClient` 的实例。 接下来可使用 `ChatThreadClient` 在会话中执行操作，如发送消息或列出参与者。

```JavaScript
async function createChatThread() {
  const createChatThreadRequest = {
    topic: "Hello, World!"
  };
  const createChatThreadOptions = {
    participants: [
      {
        id: { communicationUserId: '<USER_ID>' },
        displayName: '<USER_DISPLAY_NAME>'
      }
    ]
  };
  const createChatThreadResult = await chatClient.createChatThread(
    createChatThreadRequest,
    createChatThreadOptions
  );
  const threadId = createChatThreadResult.chatThread.id;
  return threadId;
}

createChatThread().then(async threadId => {
  console.log(`Thread created:${threadId}`);
  // PLACEHOLDERS
  // <CREATE CHAT THREAD CLIENT>
  // <RECEIVE A CHAT MESSAGE FROM A CHAT THREAD>
  // <SEND MESSAGE TO A CHAT THREAD>
  // <LIST MESSAGES IN A CHAT THREAD>
  // <ADD NEW PARTICIPANT TO THREAD>
  // <LIST PARTICIPANTS IN A THREAD>
  // <REMOVE PARTICIPANT FROM THREAD>
  });
```

刷新浏览器选项卡时，控制台中应会显示以下内容：
```console
Thread created: <thread_id>
```

## <a name="get-a-chat-thread-client"></a>获取聊天会话客户端

`getChatThreadClient` 方法返回已存在的会话的 `chatThreadClient`。 可使用该方法在创建的会话上执行操作：添加参与者、发送消息等，threadId 是现有聊天会话的唯一 ID。

```JavaScript
let chatThreadClient = chatClient.getChatThreadClient(threadId);
console.log(`Chat Thread client for threadId:${threadId}`);

```
添加此代码以代替 client.js 中的 `<CREATE CHAT THREAD CLIENT>` 注释，刷新浏览器选项卡并检查控制台，你应该会看到：
```console
Chat Thread client for threadId: <threadId>
```

## <a name="list-all-chat-threads"></a>列出所有聊天会话

`listChatThreads` 方法将返回 `ChatThreadItem` 类型的 `PagedAsyncIterableIterator` 对象。 它可用于列出所有聊天会话。
`[ChatThreadItem]` 的迭代器是列出线程后返回的响应

```JavaScript
const threads = chatClient.listChatThreads();
for await (const thread of threads) {
   // your code here
}
```

## <a name="send-a-message-to-a-chat-thread"></a>向聊天会话发送消息

使用 `sendMessage` 方法将消息发送到由 threadId 标识的会话。

`sendMessageRequest` 用于描述消息请求：

- 使用 `content` 提供聊天消息内容；

`sendMessageOptions` 用于描述操作可选参数：

- 使用 `senderDisplayName` 指定发送方的显示名称；
- 使用 `type` 指定消息类型，例如“text”或“html”；
- 可以选择使用 `metadata` 来包含想随消息一起发送的任何附加数据。 此字段为开发人员提供了一种机制，可用于扩展聊天消息功能并为用例添加自定义信息。 例如，在消息中共享文件链接时，你可能希望在元数据中添加“hasAttachment:true”，以便收件人的应用程序可分析并相应显示。

`SendChatMessageResult` 是发送消息后返回的响应，它包含一个 ID（是消息的唯一 ID）。

```JavaScript
const sendMessageRequest =
{
  content: 'Please take a look at the attachment'
};
let sendMessageOptions =
{
  senderDisplayName : 'Jack',
  type: 'text',
  metadata: {
    'hasAttachment': 'true',
    'attachmentUrl': 'https://contoso.com/files/attachment.docx'
  }
};
const sendChatMessageResult = await chatThreadClient.sendMessage(sendMessageRequest, sendMessageOptions);
const messageId = sendChatMessageResult.id;
console.log(`Message sent!, message id:${messageId}`);
```

添加此代码以代替 client.js 中的 `<SEND MESSAGE TO A CHAT THREAD>` 注释，刷新浏览器选项卡并检查控制台。
```console
Message sent!, message id:<number>
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>从聊天会话接收聊天消息

使用实时信令，你可以订阅以侦听新的传入消息并相应更新内存中的当前消息。 Azure 通信服务支持[你可以订阅的事件列表](../../../concepts/chat/concepts.md#real-time-notifications)。

```JavaScript
// open notifications channel
await chatClient.startRealtimeNotifications();
// subscribe to new notification
chatClient.on("chatMessageReceived", (e) => {
  console.log("Notification chatMessageReceived!");
  // your code here
});

```
添加此代码以代替 client.js 中的 `<RECEIVE A CHAT MESSAGE FROM A CHAT THREAD>` 注释。
刷新浏览器选项卡，控制台中应会显示消息 `Notification chatMessageReceived`；

或者可以通过按指定间隔轮询 `listMessages` 方法来检索聊天消息。

```JavaScript

const messages = chatThreadClient.listMessages();
for await (const message of messages) {
   // your code here
}

```
添加此代码以代替 client.js 中的 `<LIST MESSAGES IN A CHAT THREAD>` 注释。
刷新选项卡，应该可以在控制台中找到在这个聊天会话中发送的消息列表。

`listMessages` 返回可由 `chatMessage.type` 标识的不同类型的消息。 

有关详细信息，请参阅[消息类型](../../../concepts/chat/concepts.md#message-types)。

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>将用户作为参与者添加到聊天会话

创建聊天会话后，可以在其中添加和删除用户。 通过添加用户，可以向他们授予访问权限，使其能够在聊天会话中发送消息，以及添加/删除其他参与者。

在调用 `addParticipants` 方法之前，请确保已获取该用户的新访问令牌和标识。 用户需要使用该访问令牌才能初始化其聊天客户端。

`addParticipantsRequest` 描述请求对象，其中 `participants` 列出要添加到聊天会话的参与者；
- `id`（必需）是要添加到聊天会话的通信标识符。
- `displayName`（可选）是会话参与者的显示名称。
- `shareHistoryTime`（可选）是开始与参与者共享聊天历史记录的时间。 若要在聊天会话开始后共享历史记录，请将此属性设置为等于或小于会话创建时间的任何日期。 若在添加参与者之前不共享任何历史记录，请将其设置为当前日期。 若要共享部分历史记录，请将其设置为所选日期。

```JavaScript

const addParticipantsRequest =
{
  participants: [
    {
      id: { communicationUserId: '<NEW_PARTICIPANT_USER_ID>' },
      displayName: 'Jane'
    }
  ]
};

await chatThreadClient.addParticipants(addParticipantsRequest);

```
将 NEW_PARTICIPANT_USER_ID 替换为[新用户 ID](../../access-tokens.md)。在 client.js 中添加此代码以代替 `<ADD NEW PARTICIPANT TO THREAD>` 注释 

## <a name="list-users-in-a-chat-thread"></a>列出聊天会话中的用户
```JavaScript
const participants = chatThreadClient.listParticipants();
for await (const participant of participants) {
   // your code here
}
```
添加此代码以代替 client.js 中的 `<LIST PARTICIPANTS IN A THREAD>` 注释，刷新浏览器选项卡并检查控制台，你应该会看到会话中用户的信息。

## <a name="remove-user-from-a-chat-thread"></a>从聊天会话中删除用户

与添加参与者类似，可从聊天会话中删除参与者。 若要删除，需要跟踪已添加的参与者的 ID。

使用 `removeParticipant` 方法，其中 `participant` 是要从会话中删除的通信用户。

```JavaScript

await chatThreadClient.removeParticipant({ communicationUserId: <PARTICIPANT_ID> });
await listParticipants();
```
将 PARTICIPANT_ID 替换为在上一步中使用的用户 ID (<NEW_PARTICIPANT_USER_ID>)。
添加此代码以代替 client.js 中的 `<REMOVE PARTICIPANT FROM THREAD>` 注释，
