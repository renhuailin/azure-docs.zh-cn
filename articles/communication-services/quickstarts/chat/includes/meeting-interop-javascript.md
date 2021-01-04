---
title: 快速入门 - 加入 Teams 会议
author: askaur
ms.author: askaur
ms.date: 12/08/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 0c41771af81989ff965098a762338216db54fd27
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97577988"
---
## <a name="join-the-meeting-chat"></a>加入会议聊天 

启用 Teams 互操作性后，通信服务用户可以使用调用客户端库以来宾用户身份加入 Teams 通话。 用户加入通话时还会以参与者身份加入会议聊天，在聊天中用户可以与通话中的其他用户发送和接收消息。 用户将无法访问加入通话前发送的聊天消息。 

## <a name="get-a-teams-meeting-chat-thread-for-a-communication-services-user"></a>获取通信服务用户的 Teams 会议聊天线程

首先，实例化会议聊天线程的 `ChatThreadClient`。 分析会议链接或使用 Graph API 与会议 ID 来获取线程 ID。 

- Teams 会议链接如下所示：`https://teams.microsoft.com/l/meetup-join/meeting_chat_thread_id/1606337455313?context=some_context_here`。 线程 ID 将位于 `meeting_chat_thread_id` 在该链接中的位置。 
- 如果你有会议 ID，可以使用 [Graph API](https://docs.microsoft.com/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta) 来获取线程 ID。 [GET API](https://docs.microsoft.com/graph/api/onlinemeeting-get?view=graph-rest-beta&tabs=http%22%20%5C) 响应将获得包含 `threadID` 的 `chatInfo` 对象。 

获得聊天线程 ID 后，你可以使用 JavaScript 聊天客户端库获取聊天线程客户端： 

```javascript
let chatThreadClient = await chatClient.getChatThreadClient(threadId); 

console.log(`Chat Thread client for threadId:${chatThreadClient.threadId}`); 
```
  
可以使用 `chatThreadClient` 列出聊天线程中的成员，获取聊天记录以及发送消息。  

## <a name="send-and-receive-messages"></a>发送和接收消息  

使用 `SendMessage` 将消息发送到会议聊天。 对于接收传入的邮件，不支持订阅 `chatMessageReceived` 事件，因为此方案中尚未启用实时信令。 若要获取最新消息，可以轮询 `ListMessages` API。 对于互操作性方案，`ListMessages` API 现支持返回三种新的消息类型：
- `RichText/HTML`
- `ThreadActivity/MemberJoined`
- `ThreadActivity/MemberLeft` </br>

有关消息类型的更多信息，请参阅[此处](../../../concepts/chat/concepts.md)。 

**注意** - 对于 Teams 中的互操作性方案，当前仅支持发送和接收消息。 尚不支持其他功能，例如键入指示符，以及通信服务用户从 Teams 会议添加或删除其他用户。  

 
