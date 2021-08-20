---
title: 快速入门 - 加入 Teams 会议
author: askaur
ms.author: askaur
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 04ee9f19f23d16af9070d3366981b0690ba9ef27
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "113113081"
---
本快速入门介绍如何使用适用于 JavaScript 的 Azure 通信服务聊天 SDK 在 Teams 会议中聊天。

## <a name="sample-code"></a>代码示例
在 [GitHub](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/join-chat-to-teams-meeting) 上查找此快速入门的最终代码。

## <a name="prerequisites"></a>先决条件 

*  [部署 Teams](/deployoffice/teams-install)。 
* [聊天应用](../get-started.md)正常运行。 

## <a name="enable-teams-interoperability"></a>启用 Teams 互操作性 

以来宾用户身份加入 Teams 会议的通信服务用户仅在加入 Teams 会议通话后才能访问会议的聊天。 请参阅 [Teams 互操作](../../voice-video-calling/get-started-teams-interop.md)文档，了解如何将通信服务用户添加到 Teams 会议通话。

你需要是拥有这两个实体的组织的成员才能使用此功能。

## <a name="joining-the-meeting-chat"></a>加入会议聊天 

启用 Teams 互操作性后，通信服务用户可以使用通话 SDK 以外部用户身份加入 Teams 通话。 用户加入通话时还会以参与者身份加入会议聊天，在聊天中用户可以与通话中的其他用户发送和接收消息。 用户将无法访问加入通话前发送的聊天消息。 若要加入会议并开始聊天，可以执行后续步骤。

## <a name="create-a-new-nodejs-application"></a>创建新的 Node.js 应用程序

打开终端或命令窗口，为应用创建一个新目录，并导航到该目录。

```console
mkdir chat-interop-quickstart && cd chat-interop-quickstart
```

运行 `npm init -y` 以使用默认设置创建 package.json 文件。

```console
npm init -y
```

## <a name="install-the-chat-packages"></a>安装聊天包

使用 `npm install` 命令安装适用于 JavaScript 的必要通信服务 SDK。

```console
npm install @azure/communication-common --save

npm install @azure/communication-identity --save

npm install @azure/communication-signaling --save

npm install @azure/communication-chat --save

npm install @azure/communication-calling --save
```

`--save` 选项将该库作为 package.json 文件中的依赖项列出。

## <a name="set-up-the-app-framework"></a>设置应用框架

此快速入门使用 webpack 捆绑应用程序资产。 运行以下命令以安装 webpack、 webpack-cli 和 webpack-dev-server npm 包，并将它们作为 package.json 中的开发依赖项列出：

```console
npm install webpack@4.42.0 webpack-cli@3.3.11 webpack-dev-server@3.10.3 --save-dev
```

在项目的根目录中创建一个 index.html 文件。 我们将使用此文件来配置可让用户加入会议并开始聊天。

## <a name="add-the-teams-ui-controls"></a>添加 Teams UI 控件

将 index.html 中的代码替换为以下代码片段。
页面顶部的文本框将用于输入 Teams 会议上下文和会议线程 ID。 使用“加入 Teams 会议”按钮加入指定会议。
页面底部将弹出一个聊天窗口。 它可用于在会议线程上发送消息，并且它将在通信服务用户为成员时实时显示在该线程上发送的任何消息。

```html
<!DOCTYPE html>
<html>
   <head>
      <title>Communication Client - Calling and Chat Sample</title>
      <style>
         body {box-sizing: border-box;}
         /* The popup chat - hidden by default */
         .chat-popup {
         display: none;
         position: fixed;
         bottom: 0;
         left: 15px;
         border: 3px solid #f1f1f1;
         z-index: 9;
         }
         .message-box {
         display: none;
         position: fixed;
         bottom: 0;
         left: 15px;
         border: 3px solid #FFFACD;
         z-index: 9;
         }
         .form-container {
         max-width: 300px;
         padding: 10px;
         background-color: white;
         }
         .form-container textarea {
         width: 90%;
         padding: 15px;
         margin: 5px 0 22px 0;
         border: none;
         background: #e1e1e1;
         resize: none;
         min-height: 50px;
         }
         .form-container .btn {
         background-color: #4CAF40;
         color: white;
         padding: 14px 18px;
         margin-bottom:10px;
         opacity: 0.6;
         border: none;
         cursor: pointer;
         width: 100%;
         }
         .container {
         border: 1px solid #dedede;
         background-color: #F1F1F1;
         border-radius: 3px;
         padding: 8px;
         margin: 8px 0;
         }
         .darker {
         border-color: #ccc;
         background-color: #ffdab9;
         margin-left: 25px;
         margin-right: 3px;
         }
         .lighter {
         margin-right: 20px;
         margin-left: 3px;
         }
         .container::after {
         content: "";
         clear: both;
         display: table;
         }
      </style>
   </head>
   <body>
      <h4>Azure Communication Services</h4>
      <h1>Calling and Chat Quickstart</h1>
          <input id="teams-link-input" type="text" placeholder="Teams meeting link"
        style="margin-bottom:1em; width: 300px;" />
          <input id="thread-id-input" type="text" placeholder="Chat thread id"
        style="margin-bottom:1em; width: 300px;" />
        <p>Call state <span style="font-weight: bold" id="call-state">-</span></p>
      <div>
        <button id="join-meeting-button" type="button">
            Join Teams Meeting
        </button>
        <button id="hang-up-button" type="button" disabled="true">
            Hang Up
        </button>
      </div>
      <div class="chat-popup" id="chat-box">
         <div id="messages-container"></div>
         <form class="form-container">
            <textarea placeholder="Type message.." name="msg" id="message-box" required></textarea>
            <button type="button" class="btn" id="send-message">Send</button>
         </form>
      </div>
      <script src="./bundle.js"></script>
   </body>
</html>
```

## <a name="enable-the-teams-ui-controls"></a>启用 Teams UI 控件

将 client.js 文件的内容替换为以下代码片段。

在代码片段中，将 
- `SECRET_CONNECTION_STRING` 替换为通信服务的连接字符串 
- `ENDPOINT_URL` 替换为通信服务的终结点 URL

```javascript
import { CallClient, CallAgent } from "@azure/communication-calling";
import { AzureCommunicationTokenCredential } from "@azure/communication-common";
import { CommunicationIdentityClient } from "@azure/communication-identity";
import { ChatClient } from "@azure/communication-chat";

let call;
let callAgent;
let chatClient;
let chatThreadClient;

const meetingLinkInput = document.getElementById('teams-link-input');
const threadIdInput = document.getElementById('thread-id-input');
const callButton = document.getElementById("join-meeting-button");
const hangUpButton = document.getElementById("hang-up-button");
const callStateElement = document.getElementById('call-state');

const messagesContainer = document.getElementById("messages-container");
const chatBox = document.getElementById("chat-box");
const sendMessageButton = document.getElementById("send-message");
const messagebox = document.getElementById("message-box");

var userId = '';
var messages = '';

async function init() {
    const connectionString = "<SECRET_CONNECTION_STRING>";
    const endpointUrl = "<ENDPOINT_URL>";

    const identityClient = new CommunicationIdentityClient(connectionString);

    let identityResponse = await identityClient.createUser();
    userId = identityResponse.communicationUserId;
    console.log(`\nCreated an identity with ID: ${identityResponse.communicationUserId}`);

    let tokenResponse = await identityClient.getToken(identityResponse, [
        "voip",
        "chat",
    ]);

    const { token, expiresOn } = tokenResponse;
    console.log(`\nIssued an access token that expires at: ${expiresOn}`);
    console.log(token);

    const callClient = new CallClient();
    const tokenCredential = new AzureCommunicationTokenCredential(token);
    callAgent = await callClient.createCallAgent(tokenCredential);
    callButton.disabled = false;

    chatClient = new ChatClient(
        endpointUrl,
        new AzureCommunicationTokenCredential(token)
    );

    console.log('Azure Communication Chat client created!');
}

init();

callButton.addEventListener("click", async () => {
    // join with meeting link
    call = callAgent.join({meetingLink: meetingLinkInput.value}, {});

    call.on('stateChanged', () => {
        callStateElement.innerText = call.state;
    })
    // toggle button and chat box states
    chatBox.style.display = "block";
    hangUpButton.disabled = false;
    callButton.disabled = true;

    messagesContainer.innerHTML = messages;

    console.log(call);

    // open notifications channel
    await chatClient.startRealtimeNotifications();

    // subscribe to new message notifications
    chatClient.on("chatMessageReceived", (e) => {
        console.log("Notification chatMessageReceived!");

      // check whether the notification is intended for the current thread
        if (threadIdInput.value != e.threadId) {
            return;
        }

        if (e.sender.communicationUserId != userId) {
           renderReceivedMessage(e.message);
        }
        else {
           renderSentMessage(e.message);
        }
    });

    chatThreadClient = await chatClient.getChatThreadClient(threadIdInput.value);
});

async function renderReceivedMessage(message) {
    messages += '<div class="container lighter">' + message + '</div>';
    messagesContainer.innerHTML = messages;
}

async function renderSentMessage(message) {
    messages += '<div class="container darker">' + message + '</div>';
    messagesContainer.innerHTML = messages;
}

hangUpButton.addEventListener("click", async () => 
    {
        // end the current call
        await call.hangUp();

        // toggle button states
        hangUpButton.disabled = true;
        callButton.disabled = false;
        callStateElement.innerText = '-';

        // toggle chat states
        chatBox.style.display = "none";
        messages = "";
    });

sendMessageButton.addEventListener("click", async () =>
    {
        let message = messagebox.value;

        let sendMessageRequest = { content: message };
        let sendMessageOptions = { senderDisplayName : 'Jack' };
        let sendChatMessageResult = await chatThreadClient.sendMessage(sendMessageRequest, sendMessageOptions);
        let messageId = sendChatMessageResult.id;

        messagebox.value = '';
        console.log(`Message sent!, message id:${messageId}`);
    });
```

聊天线程参与者的显示名称不由 Teams 客户端设置。 在 `participantsAdded` 事件和 `participantsRemoved` 事件当中，这些名称将在列出参与者的 API 中作为 null 返回。 可以从 `call` 对象的 `remoteParticipants` 字段检索聊天参与者的显示名称。 收到有关名册更改的通知后，可以使用此代码来检索已添加或已删除的用户名称：

```
var displayName = call.remoteParticipants.find(p => p.identifier.communicationUserId == '<REMOTE_USER_ID>').displayName;
```

## <a name="get-a-teams-meeting-chat-thread-for-a-communication-services-user"></a>获取通信服务用户的 Teams 会议聊天线程

可以使用图形 API 来检索 Teams 会议链接和聊天，详情请参见[图形文档](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true)。 通信服务呼叫 SDK 接受完整的 Teams 会议链接。 此链接作为 `onlineMeeting` 资源的一部分返回，可在具有[图像 API](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true) 的 [`joinWebUrl` 属性](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true)下访问，也可获取 `threadId`。 响应将获得包含 `threadID` 的 `chatInfo` 对象。 

还可以从 Teams 会议邀请本身的“加入会议”URL 中获取所需的会议信息和线程 ID。
Teams 会议链接如下所示：`https://teams.microsoft.com/l/meetup-join/meeting_chat_thread_id/1606337455313?context=some_context_here`。 `threadId` 将为 `meeting_chat_thread_id` 在链接中的位置。 确保在使用 `meeting_chat_thread_id` 之前未对其进行转义。 该值应采用以下格式：`19:meeting_ZWRhZDY4ZGUtYmRlNS00OWZaLTlkZTgtZWRiYjIxOWI2NTQ4@thread.v2`


## <a name="run-the-code"></a>运行代码

Webpack 用户可以使用 `webpack-dev-server` 生成并运行应用。 运行以下命令，在本地 Web 服务器上捆绑应用程序主机：

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

打开浏览器并导航到 http://localhost:8080/。 应该看到以下内容：

:::image type="content" source="../join-teams-meeting-chat-quickstart.png" alt-text="已完成的 JavaScript 应用程序的屏幕截图。":::

在文本框中插入 Teams 会议链接和线程 ID。 按“加入 Teams 会议”来加入 Teams 会议。 在通信服务用户获批进入会议后，可以在通信服务应用程序中进行聊天。 导航到页面底部的框以开始聊天。

> [!NOTE] 
> 对于 Teams 中的互操作性方案，当前仅支持发送、接收和编辑消息以及发送键入通知。 尚不支持其他功能，例如阅读回执，以及通信服务用户从 Teams 会议中添加或删除其他用户。
