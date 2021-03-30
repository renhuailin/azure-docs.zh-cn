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
ms.openlocfilehash: 2d1c3d3be412f6f11f9d2e300b3a97cf5634f5e4
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2021
ms.locfileid: "103495372"
---
## <a name="prerequisites"></a>先决条件
在开始之前，请务必：

- 创建活动订阅的 Azure 帐户。 有关详细信息，请参阅[创建免费账户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [安装 Python](https://www.python.org/downloads/)
- 创建 Azure 通信服务资源。 有关详细信息，请参阅[创建 Azure 通信服务资源](../../create-communication-resource.md)。 为完成此快速入门，需要记录资源终结点。
- [用户访问令牌](../../access-tokens.md)。 请确保将范围设置为“聊天”，并记下令牌字符串和 userId 字符串。

## <a name="setting-up"></a>设置

### <a name="create-a-new-python-application"></a>创建新的 Python 应用程序

打开终端或命令窗口，为应用创建一个新目录，并导航到该目录。

```console
mkdir chat-quickstart && cd chat-quickstart
```

使用文本编辑器在项目根目录中创建名为“start-chat.py”的文件，并添加程序的结构，包括基本异常处理。 将在以下部分中将此快速入门的所有源代码添加到此文件。

```python
import os
# Add required client library components from quickstart here

try:
    print('Azure Communication Services - Chat Quickstart')
    # Quickstart code goes here
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="install-client-library"></a>安装客户端库

```console

pip install azure-communication-chat

```

## <a name="object-model"></a>对象模型

以下类和接口处理适用于 Python 的 Azure 通信服务聊天客户端库的某些主要功能。

| 名称                                  | 说明                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | 聊天功能需要此类。 使用订阅信息实例化此类，并使用它来创建、获取和删除会话。 |
| ChatThreadClient | 聊天会话功能需要此类。 通过 ChatClient 获取实例，并使用它来发送/接收/更新/删除消息、添加/删除/获取用户、发送键入通知和阅读回执。 |

## <a name="create-a-chat-client"></a>创建聊天客户端

若要创建聊天客户端，需使用通信服务终结点以及在前提步骤中生成 `Access Token`。 详细了解[用户访问令牌](../../access-tokens.md)。

本快速入门不介绍如何创建服务层来管理聊天应用程序的令牌，尽管我们建议你这样做。 有关详细信息，请参阅以下文档：[聊天体系结构](../../../concepts/chat/concepts.md)

```console
pip install azure-communication-identity
```

```python
from azure.communication.chat import ChatClient
from azure.communication.identity._shared.user_credential import CommunicationTokenCredential
from azure.communication.chat._shared.user_token_refresh_options import CommunicationTokenRefreshOptions

endpoint = "https://<RESOURCE_NAME>.communication.azure.com"
refresh_options = CommunicationTokenRefreshOptions(<Access Token>)
chat_client = ChatClient(endpoint, CommunicationTokenCredential(refresh_options))
```

## <a name="start-a-chat-thread"></a>启动聊天会话

使用 `create_chat_thread` 方法创建聊天会话。

- 使用 `topic` 提供一个会话主题；在创建聊天会话后可使用 `update_thread` 函数更新主题。
- 使用 `thread_participants` 列出要添加到聊天会话中的 `ChatThreadParticipant`，`ChatThreadParticipant` 采用 `CommunicationUserIdentifier` 类型作为 `user`，这是通过[创建用户](../../access-tokens.md#create-an-identity)创建后得到的
- 使用 `repeatability_request_id` 来指示该请求是可重复的请求。 客户端可以使用同一个 Repeatability-Request-ID 多次发出请求，并获得相应的响应，无需服务器多次执行该请求。

`CreateChatThreadResult` 是创建线程后返回的结果，可以使用它来提取已创建的聊天线程的 `id`。 然后，可以使用此 `id` 通过 `ChatThreadClient` 方法提取 `get_chat_thread_client` 对象。 `ChatThreadClient` 可用于对此聊天线程执行其他聊天操作。

#### <a name="without-repeatability-request-id"></a>无 Repeatability-Request-ID
```python
from datetime import datetime
from azure.communication.chat import ChatThreadParticipant

# from azure.communication.identity import CommunicationIdentityClient
# 
# # create an user
# identity_client = CommunicationIdentityClient.from_connection_string('<connection_string>')
# user = identity_client.create_user()
# 
# ## OR pass existing user
# # from azure.communication.identity import CommunicationUserIdentifier
# # user_id = 'some_user_id'
# # user = CommunicationUserIdentifier(user_id)

topic="test topic"
participants = [ChatThreadParticipant(
    user=user,
    display_name='name',
    share_history_time=datetime.utcnow()
)]

create_chat_thread_result = chat_client.create_chat_thread(topic)
chat_thread_client = chat_client.get_chat_thread_client(create_chat_thread_result.chat_thread.id)
```

#### <a name="with-repeatability-request-id"></a>有 Repeatability-Request-ID
```python
from datetime import datetime
from azure.communication.chat import ChatThreadParticipant

# from azure.communication.identity import CommunicationIdentityClient
# 
# # create an user
# identity_client = CommunicationIdentityClient.from_connection_string('<connection_string>')
# user = identity_client.create_user()
# 
# ## OR pass existing user
# # from azure.communication.identity import CommunicationUserIdentifier
# # user_id = 'some_user_id'
# # user = CommunicationUserIdentifier(user_id)

topic="test topic"
participants = [ChatThreadParticipant(
    user=user,
    display_name='name',
    share_history_time=datetime.utcnow()
)]

repeatability_request_id = 'b66d6031-fdcc-41df-8306-e524c9f226b8' # some unique identifier
chat_thread_client = chat_client.create_chat_thread(topic, 
                                                    thread_participants=participants, 
                                                    repeatability_request_id=repeatability_request_id)
```

## <a name="get-a-chat-thread-client"></a>获取聊天会话客户端
`get_chat_thread_client` 方法返回某个已存在的会话的会话客户端。 可使用该方法在创建的会话上执行操作：添加参与者、发送消息，等等。thread_id 是现有聊天会话的唯一 ID。

`ChatThreadClient` 可用于对此聊天线程执行其他聊天操作。

```python
thread_id = create_chat_thread_result.chat_thread.id
chat_thread_client = chat_client.get_chat_thread_client(thread_id)
```

## <a name="get-a-chat-thread"></a>获取聊天线程

使用 `get_chat_thread` 方法从服务中检索 `ChatThread`；`thread_id` 是线程的唯一 ID。
- 使用必需的 `thread_id` 指定线程的唯一 ID。 
```python
chat_thread = chat_client.get_chat_thread(thread_id=thread_id)
```

## <a name="list-all-chat-threads"></a>列出所有聊天会话
`list_chat_threads` 方法返回 `ChatThreadInfo` 类型的迭代器。 它可用于列出所有聊天会话。

- 使用 `start_time` 指定最早的时间点，以便获取该时间点之前的聊天会话。
- 使用 `results_per_page` 指定每页返回的聊天会话的最大数量。

`[ChatThreadInfo]` 的迭代器是列出线程后返回的响应

```python
from datetime import datetime, timedelta

start_time = datetime.utcnow() - timedelta(days=2)

chat_thread_infos = chat_client.list_chat_threads(results_per_page=5, start_time=start_time)
for chat_thread_info_page in chat_thread_infos.by_page():
    for chat_thread_info in chat_thread_info_page:
        print(chat_thread_info)
```

## <a name="delete-a-chat-thread"></a>删除聊天会话
`delete_chat_thread` 用于删除聊天会话。

- 使用 `thread_id` 指定需要删除的现有聊天会话的 thread_id

```python
thread_id = create_chat_thread_result.chat_thread.id
chat_client.delete_chat_thread(thread_id=thread_id)
```

## <a name="send-a-message-to-a-chat-thread"></a>向聊天会话发送消息

使用 `send_message` 方法将消息发送到刚刚创建的聊天会话（由 thread_id 标识）。

- 使用 `content` 提供聊天消息内容；
- 使用 `chat_message_type` 指定消息内容类型。 可能的值为 'text' 和 'html'；如果未指定，则分配默认值 'text'。
- 使用 `sender_display_name` 指定发送方的显示名称；

响应是一个类型为 `str` 的“ID”，这是该消息的唯一 ID。

#### <a name="message-type-not-specified"></a>未指定消息类型
```python
topic = "test topic"
create_chat_thread_result = chat_client.create_chat_thread(topic)
thread_id = create_chat_thread_result.chat_thread.id
chat_thread_client = chat_client.get_chat_thread_client(create_chat_thread_result.chat_thread.id)

content='hello world'

send_message_result_id = chat_thread_client.send_message(content)
```

#### <a name="message-type-specified"></a>指定了消息类型
```python
from azure.communication.chat import ChatMessageType

topic = "test topic"
create_chat_thread_result = chat_client.create_chat_thread(topic)
thread_id = create_chat_thread_result.chat_thread.id
chat_thread_client = chat_client.get_chat_thread_client(create_chat_thread_result.chat_thread.id)


content='hello world'
sender_display_name='sender name'

# specify chat message type with pre-built enumerations
send_message_result_id_w_enum = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name, chat_message_type=ChatMessageType.TEXT)
print("Message sent: id: ", send_message_result_id_w_enum)

# specify chat message type as string
send_message_result_id_w_str = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name, chat_message_type='text')
print("Message sent: id: ", send_message_result_id_w_str)
```

## <a name="get-a-specific-chat-message-from-a-chat-thread"></a>从聊天会话获取特定的聊天消息
`get_message` 函数可用于检索特定消息（由 message_id 标识）

- 使用 `message_id` 指定消息 ID。

类型为 `ChatMessage` 的响应包含与单条消息相关的所有信息。

```python
message_id = send_message_result_id
chat_message = chat_thread_client.get_message(message_id)
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>从聊天会话接收聊天消息

可以通过按指定间隔轮询的 `list_messages` 方法来检索聊天消息。

- 使用 `results_per_page` 指定每页返回的消息的最大数量。
- 使用 `start_time` 指定最早的时间点，以便获取该时间点之前的消息。

`[ChatMessage]` 的迭代器是列出消息后返回的响应

```python
from datetime import datetime, timedelta

start_time = datetime.utcnow() - timedelta(days=1)

chat_messages = chat_thread_client.list_messages(results_per_page=1, start_time=start_time)
for chat_message_page in chat_messages.by_page():
    for chat_message in chat_message_page:
        print("ChatMessage: Id=", chat_message.id, "; Content=", chat_message.content.message)
```

`list_messages` 返回最新版本的消息，包括使用 `update_message` 和 `delete_message` 对消息执行的任何编辑或删除。 对于已删除的消息，`ChatMessage.deleted_on` 返回一个日期/时间值，指示删除该消息的时间。 对于已编辑的消息，`ChatMessage.edited_on` 返回一个日期/时间值，指示编辑该消息的日期/时间。 可以使用 `ChatMessage.created_on` 访问消息创建的原始时间，还可使用它对消息进行排序。

`list_messages` 返回可由 `ChatMessage.type` 标识的不同类型的消息。 这些类型包括：

- `ChatMessageType.TEXT`：会话参与者发送的普通聊天消息。

- `ChatMessageType.HTML`：会话参与者发送的 HTML 聊天消息。

- `ChatMessageType.TOPIC_UPDATED`：指示主题已更新的系统消息。

- `ChatMessageType.PARTICIPANT_ADDED`：指示一个或多个参与者已添加到聊天会话的系统消息。

- `ChatMessageType.PARTICIPANT_REMOVED`：指示已从聊天会话中删除参与者的系统消息。

有关详细信息，请参阅[消息类型](../../../concepts/chat/concepts.md#message-types)。

## <a name="update-topic-of-a-chat-thread"></a>更新聊天会话的主题
可以使用 `update_topic` 方法更新聊天会话的主题

```python
topic = "updated thread topic"
chat_thread_client.update_topic(topic=topic)

chat_thread = chat_client.get_chat_thread(chat_thread_client.thread_id)
updated_topic = chat_thread.topic

print('Updated topic: ', updated_topic)
```

## <a name="update-a-message"></a>更新消息
可以使用 `update_message` 方法更新现有消息（由 message_id 标识）的内容。

- 使用必需的 `message_id` 指定消息的唯一 ID。
- 使用可选的 `content` 指定要更新的消息内容；如果不指定，则分配空内容

```python
content = 'Hello world!'
send_message_result_id = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name)

content = 'Hello! I am updated content'
chat_thread_client.update_message(message_id=send_message_result_id, content=content)

chat_message = chat_thread_client.get_message(send_message_result_id)
print('Updated message content: ', chat_message.content.message)
```

## <a name="send-read-receipt-for-a-message"></a>发送消息的阅读回执
`send_read_receipt` 方法可用于代表用户将阅读回执事件发布到会话。

- 使用 `message_id` 指定当前用户阅读的最新消息的 ID。

```python
message_id=send_message_result_id
chat_thread_client.send_read_receipt(message_id=message_id)
```

## <a name="list-read-receipts-for-a-chat-thread"></a>列出聊天会话的阅读回执
`list_read_receipts` 方法可用于获取会话的阅读回执。

- 使用 `results_per_page` 指定每页返回的聊天消息阅读回执的最大数量。
- 使用 `skip` 指定跳过响应中指定位置之前的聊天消息阅读回执。

`[ChatMessageReadReceipt]` 的迭代器是列出阅读回执后返回的响应

```python
read_receipts = chat_thread_client.list_read_receipts(results_per_page=5, skip=0)

for read_receipt_page in read_receipts.by_page():
    for read_receipt in read_receipt_page:
        print('ChatMessageReadReceipt: ', read_receipt)
        print('Sender', read_receipt.sender)
        print('Message Id', read_receipt.chat_message_id)
        print('Read On Timestamp', read_receipt.read_on)
```

## <a name="send-typing-notification"></a>发送键入通知
`send_typing_notification` 方法可用于代表用户将键入事件发布到会话。

```python
chat_thread_client.send_typing_notification()
```

## <a name="delete-message"></a>删除消息
`delete_message` 方法可用于删除由 message_id 标识的消息

- 使用 `message_id` 指定 message_id

```python
message_id=send_message_result_id
chat_thread_client.delete_message(message_id=message_id)
```

## <a name="add-a-user-as-participant-to-the-chat-thread"></a>将用户作为参与者添加到聊天会话

创建聊天会话后，可以在其中添加和删除用户。 通过添加用户，可以向他们授予访问权限，使其能够向聊天会话发送消息，以及添加/删除其他参与者。 在调用 `add_participant` 方法之前，请确保已获取该用户的新访问令牌和标识。 用户需要使用该访问令牌才能初始化其聊天客户端。

使用 `add_participant` 方法将会话参与者添加到由 thread_id 标识的会话中。

- 使用 `thread_participant` 指定要添加到聊天会话的参与者；
- `user`（必需）是通过[创建用户](../../access-tokens.md#create-an-identity)中的 `CommunicationIdentityClient` 创建的 `CommunicationUserIdentifier`
- `display_name`（可选）是会话参与者的显示名称。
- `share_history_time`（可选）是开始与参与者共享聊天历史记录的时间。 若要在聊天会话开始后共享历史记录，请将此属性设置为等于或小于会话创建时间的任何日期。 若在添加参与者之前不共享任何历史记录，请将其设置为当前日期。 若要共享部分历史记录，请将其设置为中间日期。

如果已成功添加参与者，则不会引发错误。 如果在添加参与者时遇到错误，则会引发 `RuntimeError`

```python
from azure.communication.identity import CommunicationIdentityClient
from azure.communication.chat import ChatThreadParticipant
from datetime import datetime

# create an user
identity_client = CommunicationIdentityClient.from_connection_string('<connection_string>')
new_user = identity_client.create_user()

# # conversely, you can also add an existing user to a chat thread; provided the user_id is known
# from azure.communication.identity import CommunicationUserIdentifier
#
# user_id = 'some user id'
# user_display_name = "Wilma Flinstone"
# new_user = CommunicationUserIdentifier(user_id)
# participant = ChatThreadParticipant(
#     user=new_user,
#     display_name=user_display_name,
#     share_history_time=datetime.utcnow())

def decide_to_retry(error, **kwargs):
    """
    Insert some custom logic to decide if retry is applicable based on error
    """
    return True

participant = ChatThreadParticipant(
    user=new_user,
    display_name='Fred Flinstone',
    share_history_time=datetime.utcnow())

try:
    chat_thread_client.add_participant(thread_participant=participant)
except RuntimeError as e:
    if e is not None and decide_to_retry(error=e):
        chat_thread_client.add_participant(thread_participant=participant)
```

还可以使用 `add_participants` 方法将多个用户添加到聊天会话，前提是新的访问令牌和标识可用于所有用户。

将返回 `list(tuple(ChatThreadParticipant, CommunicationError))`。 如果已成功添加参与者，则预期会提供一个空列表。 如果在添加参与者时遇到错误，则会在该列表中填充未能添加的参与者以及遇到的错误。

```python
from azure.communication.identity import CommunicationIdentityClient
from azure.communication.chat import ChatThreadParticipant
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
# participant = ChatThreadParticipant(
#     user=new_user,
#     display_name=user_display_name,
#     share_history_time=datetime.utcnow())

participants = []
for _user in new_users:
  chat_thread_participant = ChatThreadParticipant(
    user=_user,
    display_name='Fred Flinstone',
    share_history_time=datetime.utcnow()
  ) 
  participants.append(chat_thread_participant) 

response = chat_thread_client.add_participants(thread_participants=participants)

def decide_to_retry(error, **kwargs):
    """
    Insert some custom logic to decide if retry is applicable based on error
    """
    return True

# verify if all users has been successfully added or not
# in case of partial failures, you can retry to add all the failed participants 
retry = [p for p, e in response if decide_to_retry(e)]
if len(retry) > 0:
    chat_thread_client.add_participants(retry)
```


## <a name="remove-user-from-a-chat-thread"></a>从聊天会话中删除用户

与添加参与者类似，你也可以从会话中删除参与者。 若要删除，需要跟踪已添加的参与者的 ID。

使用 `remove_participant` 方法将会话参与者从由 threadId 标识的会话中删除。
- `user` 是要从会话中删除的 `CommunicationUserIdentifier`。

```python
chat_thread_client.remove_participant(user=new_user)

# # converesely you can also do the following; provided the user_id is known
# from azure.communication.identity import CommunicationUserIdentifier
# 
# user_id = 'some user id'
# chat_thread_client.remove_participant(user=CommunincationUserIdentfier(new_user))
```

## <a name="run-the-code"></a>运行代码

从应用程序目录使用 `python` 命令运行应用程序。

```console
python start-chat.py
```
