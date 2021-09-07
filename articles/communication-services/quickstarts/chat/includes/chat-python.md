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
ms.openlocfilehash: b22cee560284a5a185e6b3ba560531d43d37ca67
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2021
ms.locfileid: "122967865"
---
## <a name="sample-code"></a>代码示例
在 [GitHub](https://github.com/Azure-Samples/communication-services-python-quickstarts/tree/main/add-chat) 上查找此快速入门的最终代码。

## <a name="prerequisites"></a>先决条件
在开始之前，请务必：

- 创建活动订阅的 Azure 帐户。 有关详细信息，请参阅[创建免费账户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 安装 [Python](https://www.python.org/downloads/)。
- 创建 Azure 通信服务资源。 有关详细信息，请参阅[快速入门：创建和管理通信服务资源](../../create-communication-resource.md)。 为完成此快速入门，需要记录资源终结点。
- 一个[用户访问令牌](../../access-tokens.md)。 请确保将范围设置为 `chat`，并记下 `token`字符串和 `userId` 字符串。

## <a name="setting-up"></a>设置

### <a name="create-a-new-python-application"></a>创建新的 Python 应用程序

打开终端或命令窗口，为应用创建一个新目录，并转到该目录。

```console
mkdir chat-quickstart && cd chat-quickstart
```

使用文本编辑器在项目根目录中创建一个名为 start-chat.py 的文件。 为程序添加结构，包括基本的异常处理。 在以下部分，将此快速入门的所有源代码添加到此文件。

```python
import os
# Add required SDK components from quickstart here

try:
    print('Azure Communication Services - Chat Quickstart')
    # Quickstart code goes here
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="install-sdk"></a>安装 SDK

使用以下命令安装 SDK：

```console

pip install azure-communication-chat

```

## <a name="object-model"></a>对象模型

以下类和接口用于处理适用于 Python 的 Azure 通信服务聊天 SDK 的某些主要功能。

| 名称                                  | 说明                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| `ChatClient` | 聊天功能需要此类。 使用订阅信息实例化此类，并使用它来创建、获取和删除会话。 |
| `ChatThreadClient` | 聊天会话功能需要此类。 通过 `ChatClient` 获取实例，并使用它来发送、接收、更新和删除消息。 此外，可用它来添加、删除和获取用户，还可发送键入通知和已读回执。 |

## <a name="create-a-chat-client"></a>创建聊天客户端

为创建聊天客户端，请使用通信服务终结点和在前提步骤期间生成的访问令牌。

```console
pip install azure-communication-identity
```

```python
from azure.communication.chat import ChatClient, CommunicationTokenCredential

endpoint = "https://<RESOURCE_NAME>.communication.azure.com"
chat_client = ChatClient(endpoint, CommunicationTokenCredential("<Access Token>"))
```
本快速入门不介绍如何创建服务层来管理聊天应用程序的令牌，尽管我们建议你这样做。 有关详细信息，请参阅[聊天概念](../../../concepts/chat/concepts.md)的“聊天体系结构”部分。

## <a name="start-a-chat-thread"></a>启动聊天会话

使用 `create_chat_thread` 方法创建聊天会话。

- 使用 `topic` 指定一个会话主题。 使用 `update_thread` 函数创建聊天会话后，可更新主题。
- 使用 `thread_participants` 列出要添加到聊天会话的 `ChatParticipant`。 `ChatParticipant` 将 `CommunicationUserIdentifier` 类型视为 `user`。

`CreateChatThreadResult` 是创建会话后返回的结果。 可用它来提取所创建的聊天会话的 `id`。 然后，可使用此 `id` 通过 `get_chat_thread_client` 方法提取 `ChatThreadClient` 对象。 可使用 `ChatThreadClient` 对此聊天会话执行其他聊天操作。

```python
topic="test topic"

create_chat_thread_result = chat_client.create_chat_thread(topic)
chat_thread_client = chat_client.get_chat_thread_client(create_chat_thread_result.chat_thread.id)
```

## <a name="get-a-chat-thread-client"></a>获取聊天会话客户端

`get_chat_thread_client` 方法返回某个已存在的会话的会话客户端。 可用它来对创建的会话执行操作。 例如，可添加参与者和发送消息。 `thread_id` 是现有聊天会话的唯一 ID。

可使用 `ChatThreadClient` 对此聊天会话执行其他聊天操作。

```python
thread_id = create_chat_thread_result.chat_thread.id
chat_thread_client = chat_client.get_chat_thread_client(thread_id)
```

## <a name="list-all-chat-threads"></a>列出所有聊天会话

`list_chat_threads` 方法返回 `ChatThreadItem` 类型的迭代器。

- 使用 `start_time` 指定要获取聊天会话的最早时间点。
- 使用 `results_per_page` 指定每页返回的聊天会话的最大数量。

`[ChatThreadItem]` 的迭代器是列出会话后返回的响应。

```python
from datetime import datetime, timedelta

start_time = datetime.utcnow() - timedelta(days=2)

chat_threads = chat_client.list_chat_threads(results_per_page=5, start_time=start_time)
for chat_thread_item_page in chat_threads.by_page():
    for chat_thread_item in chat_thread_item_page:
        print(chat_thread_item)
        print('Chat Thread Id: ', chat_thread_item.id)
```


## <a name="send-a-message-to-a-chat-thread"></a>向聊天会话发送消息

使用 `send_message` 方法将消息发送到刚刚创建的由 `thread_id` 标识的聊天会话。

- 使用 `content` 提供聊天消息内容。
- 使用 `chat_message_type` 指定消息内容类型。 可能的值为 `text` 和 `html`。 如果未指定值，则默认值为 `text`。
- 使用 `sender_display_name` 指定发送方的显示名称。
- 可以选择使用 `metadata` 来包含你希望随消息一起发送的任何附加数据。 此字段为开发人员提供了一种机制，以扩展聊天消息功能并为用例添加自定义信息。 例如，在消息中共享文件链接时，你可能希望在元数据中添加“hasAttachment:true”，以便收件人的应用程序可进行分析并相应地显示。

`SendChatMessageResult` 是发送消息后返回的响应。 它包含一个 ID，这是消息的唯一 ID。

```python
from azure.communication.chat import ChatMessageType

topic = "test topic"
create_chat_thread_result = chat_client.create_chat_thread(topic)
thread_id = create_chat_thread_result.chat_thread.id
chat_thread_client = chat_client.get_chat_thread_client(create_chat_thread_result.chat_thread.id)


content='Please take a look at the attachment'
sender_display_name='sender name'
metadata={
    'hasAttachment': 'true',
    'attachmentUrl': 'https://contoso.com/files/attachment.docx'
}

# specify chat message type with pre-built enumerations
send_message_result_w_enum = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name, chat_message_type=ChatMessageType.TEXT, metadata=metadata)
print("Message sent: id: ", send_message_result_w_enum.id)
```


## <a name="receive-chat-messages-from-a-chat-thread"></a>从聊天会话接收聊天消息

可以通过按指定间隔轮询的 `list_messages` 方法来检索聊天消息。

- 使用 `results_per_page` 指定每页返回的消息的最大数量。
- 使用 `start_time` 指定要获取消息的最早时间点。

`[ChatMessage]` 的迭代器是列出消息后返回的响应。

```python
from datetime import datetime, timedelta

start_time = datetime.utcnow() - timedelta(days=1)

chat_messages = chat_thread_client.list_messages(results_per_page=1, start_time=start_time)
for chat_message_page in chat_messages.by_page():
    for chat_message in chat_message_page:
        print("ChatMessage: Id=", chat_message.id, "; Content=", chat_message.content.message)
```

`list_messages` 返回最新版本的消息，包括使用 `update_message` 和 `delete_message` 对消息执行的任何编辑或删除。 对于已删除的消息，`ChatMessage.deleted_on` 返回一个 `datetime` 值，它指示删除该消息的时间。 对于已编辑的消息，`ChatMessage.edited_on` 返回一个 `datetime` 值，它指示编辑该消息的时间。 可使用 `ChatMessage.created_on` 访问消息创建的原始时间，还可用它来对消息进行排序。

`list_messages` 返回可使用 `ChatMessage.type` 标识的不同类型的消息。 

有关详细信息，请查看[消息类型](../../../concepts/chat/concepts.md#message-types)。

## <a name="send-read-receipt"></a>发送阅读回执

使用 `send_read_receipt` 方法可代表用户将已读回执事件发布到会话。

- 使用 `message_id` 指定当前用户阅读的最新消息的 ID。

```python
content='hello world'

send_message_result = chat_thread_client.send_message(content)
chat_thread_client.send_read_receipt(message_id=send_message_result.id)
```


## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>将用户作为参与者添加到聊天会话

创建聊天会话后，可在其中添加和删除用户。 通过添加用户，可向他们授予访问权限，使其能够向聊天会话发送消息，以及添加或删除其他参与者。 在调用 `add_participants` 方法之前，请确保已获取该用户的新访问令牌和标识。 用户需要访问令牌才能初始化聊天客户端。

可使用 `add_participants` 方法将一或多名用户添加到聊天会话，前提是新的访问令牌和标识可用于所有用户。

将返回 `list(tuple(ChatParticipant, CommunicationError))`。 如果已成功添加参与者，则预期会提供一个空列表。 如果在添加参与者时遇到错误，则会在该列表中填充未能添加的参与者以及遇到的错误。

```python
from azure.communication.identity import CommunicationIdentityClient
from azure.communication.chat import ChatParticipant
from datetime import datetime

# create 2 users
identity_client = CommunicationIdentityClient.from_connection_string('<connection_string>')
new_users = [identity_client.create_user() for i in range(2)]

# # conversely, you can also add an existing user to a chat thread; provided the user_id is known
# from azure.communication.identity import CommunicationUserIdentifier
#
# user_id = 'some user id'
# user_display_name = "Wilma Flinstone"
# new_user = CommunicationUserIdentifier(user_id)
# participant = ChatParticipant(
#     identifier=new_user,
#     display_name=user_display_name,
#     share_history_time=datetime.utcnow())

participants = []
for _user in new_users:
  chat_thread_participant = ChatParticipant(
    identifier=_user,
    display_name='Fred Flinstone',
    share_history_time=datetime.utcnow()
  ) 
  participants.append(chat_thread_participant) 

response = chat_thread_client.add_participants(participants)

def decide_to_retry(error, **kwargs):
    """
    Insert some custom logic to decide if retry is applicable based on error
    """
    return True

# verify if all users has been successfully added or not
# in case of partial failures, you can retry to add all the failed participants 
retry = [p for p, e in response if decide_to_retry(e)]
if retry:
    chat_thread_client.add_participants(retry)
```


## <a name="list-thread-participants-in-a-chat-thread"></a>列出聊天会话中的会话参与者

与添加参与者类似，你也可以列出会话中的参与者。

使用 `list_participants` 检索聊天会话的参与者。 下面两个命令都是可选的：

- `results_per_page` 用于指定每页返回的参与者人数上限。
- `skip` 用于跳过响应中的参与者，直到指定位置为止。

`[ChatParticipant]` 的迭代器是列出参与者后返回的响应。

```python
chat_thread_participants = chat_thread_client.list_participants()
for chat_thread_participant_page in chat_thread_participants.by_page():
    for chat_thread_participant in chat_thread_participant_page:
        print("ChatParticipant: ", chat_thread_participant)
```

## <a name="run-the-code"></a>运行代码

从应用程序目录使用 `python` 命令运行应用程序。

```console
python start-chat.py
```
