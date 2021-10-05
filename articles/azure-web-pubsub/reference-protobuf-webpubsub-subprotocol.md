---
title: 参考 - Azure Web PubSub 支持的 Protobuf WebSocket 子协议 `protobuf.webpubsub.azure.v1`
description: 本参考文章介绍 Azure Web PubSub 支持的 WebSocket 子协议 `protobuf.webpubsub.azure.v1`。
author: chenyl
ms.author: chenyl
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 08/31/2021
ms.openlocfilehash: 045d7946a94ba9658dcdc235e1d30e36b4c4e09b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128626981"
---
#  <a name="the-azure-web-pubsub-supported-protobuf-websocket-subprotocol"></a>Azure Web PubSub 支持的 Protobuf WebSocket 子协议
     
本文档介绍子协议 `protobuf.webpubsub.azure.v1`。

当客户端使用此子协议时，传出数据帧和传入数据帧都应为协议缓冲区 (protobuf) 有效负载。

## <a name="overview"></a>概述

子协议 `protobuf.webpubsub.azure.v1` 使客户端能够直接执行发布/订阅 (PubSub)，而不是执行与上游服务器之间的往返访问。 使用 `protobuf.webpubsub.azure.v1` 子协议的 WebSocket 连接称作 PubSub WebSocket 客户端。

例如，在 JavaScript 中，可以通过以下代码创建使用 protobuf 子协议的 PubSub WebSocket 客户端：

```js
// PubSub WebSocket client
var pubsub = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'protobuf.webpubsub.azure.v1');
```

对于简单的 WebSocket 客户端，服务器具有所需的角色，可以处理来自客户端的事件。 简单的 WebSocket 连接在发送消息时始终会触发 `message` 事件，并且始终依赖于服务器端处理消息以及执行其他操作。 借助 `protobuf.webpubsub.azure.v1` 子协议，授权客户端可以使用[加入请求](#join-groups)加入组，并可使用[发布请求](#publish-messages)直接将消息发布到组。 客户端还可以使用[事件请求](#send-custom-events)将消息路由到各种上游事件处理程序，以自定义消息所属的事件。

> [!NOTE]
> 目前，Web PubSub 服务仅支持 [proto3](https://developers.google.com/protocol-buffers/docs/proto3)。

## <a name="permissions"></a>权限

在前面的 PubSub WebSocket 客户端介绍中，你可能已注意到，一个客户端只有在获得授权时才能发布到其他客户端。 客户端的角色决定了它的初始权限，下表列出了这些权限：

| 角色 | 权限 |
|---|---|
| 未指定 | 客户端可以发送事件请求。 |
| `webpubsub.joinLeaveGroup` | 客户端可以加入或退出任何组。 |
| `webpubsub.sendToGroup` | 客户端可以向任何组发布消息。 |
| `webpubsub.joinLeaveGroup.<group>` | 客户端可以加入或退出组 `<group>`。 |
| `webpubsub.sendToGroup.<group>` | 客户端可以向 `<group>` 组发布消息。 |
| | |

服务器端还可通过 REST API 或服务器 SDK 动态授予或撤销客户端的权限。

## <a name="requests"></a>请求

所有请求消息遵循以下 protobuf 格式：

```protobuf
syntax = "proto3";

import "google/protobuf/any.proto";

message UpstreamMessage {
    oneof message {
        SendToGroupMessage send_to_group_message = 1;
        EventMessage event_message = 5;
        JoinGroupMessage join_group_message = 6;
        LeaveGroupMessage leave_group_message = 7;
    }

    message SendToGroupMessage {
        string group = 1;
        optional int32 ack_id = 2;
        MessageData data = 3;
    }

    message EventMessage {
        string event = 1;
        MessageData data = 2;
    }
    
    message JoinGroupMessage {
        string group = 1;
        optional int32 ack_id = 2;
    }

    message LeaveGroupMessage {
        string group = 1;
        optional int32 ack_id = 2;
    }
}

message MessageData {
    oneof data {
        string text_data = 1;
        bytes binary_data = 2;
        google.protobuf.Any protobuf_data = 3;
    }
}
```

### <a name="join-groups"></a>加入组

格式：

将 `join_group_message.group` 设置为组名。

* `ackId` 是可选项。 它是此命令消息的增量整数。 如果指定 `ackId`，则执行命令后，服务会将[确认响应消息](#ack-response)发送回客户端。

### <a name="leave-groups"></a>退出组

格式：

将 `leave_group_message.group` 设置为组名。

* `ackId` 是可选项。 它是此命令消息的增量整数。 如果指定 `ackId`，则执行命令后，服务会将[确认响应消息](#ack-response)发送回客户端。

### <a name="publish-messages"></a>发布消息

格式：

* `ackId` 是可选项。 它是此命令消息的增量整数。 如果指定 `ackId`，则执行命令后，服务会将[确认响应消息](#ack-response)发送回客户端。

有一个隐式 `dataType`，它可以是 `protobuf`、`text` 或 `binary`，具体取决于设置的 `MessageData` 中的 `data`。 接收方客户端可以使用 `dataType` 来正确处理内容。

* `protobuf`：如果设置 `send_to_group_message.data.protobuf_data`，则隐式 `dataType` 为 `protobuf`。 `protobuf_data` 的类型可以是[任意](https://developers.google.com/protocol-buffers/docs/proto3#any)消息类型。 所有其他客户端将接收 protobuf 编码的二进制文件，该文件可由 protobuf SDK 反序列化。 仅支持基于文本的内容（例如 `json.webpubsub.azure.v1`）的客户端将接收 Base64 编码的二进制文件。

* `text`：如果设置 `send_to_group_message.data.text_data`，则隐式 `dataType` 为 `text`。 `text_data` 应为字符串。 使用其他协议的所有客户端将接收 UTF-8 编码的字符串。

* `binary`：如果设置 `send_to_group_message.data.binary_data`，则隐式 `dataType` 为 `binary`。 `binary_data` 应为字节数组。 使用其他协议的所有客户端将接收未经过 protobuf 编码的原始二进制文件。 仅支持基于文本的内容（例如 `json.webpubsub.azure.v1`）的客户端将接收 Base64 编码的二进制文件。

#### <a name="case-1-publish-text-data"></a>案例 1：发布文本数据

将 `send_to_group_message.group` 设置为 `group`，将 `send_to_group_message.data.text_data` 设置为 `"text data"`。

* `group` 组中的 protobuf 子协议客户端接收二进制帧，并可以使用 [DownstreamMessage](#responses) 将该二进制帧反序列化。

* `group` 组中的 JSON 子协议客户端将接收：

    ```json
    {
        "type": "message",
        "from": "group",
        "group": "group",
        "dataType" : "text",
        "data" : "text data"
    }
    ```

* `group` 组中的原始客户端接收字符串 `text data`。

#### <a name="case-2-publish-protobuf-data"></a>案例 2：发布 protobuf 数据

假设你有一个客户消息：

```
message MyMessage {
    int32 value = 1;
}
```

使用 `value = 1` 将 `send_to_group_message.group` 设置为 `group` 并将 `send_to_group_message.data.protobuf_data` 设置为 `Any.pack(MyMessage)`。

* `group` 组中的 protobuf 子协议客户端接收二进制帧，并可以使用 [DownstreamMessage](#responses) 将该二进制帧反序列化。

* `group` 组中的子协议客户端将接收：

    ```json
    {
        "type": "message",
        "from": "group",
        "group": "G",
        "dataType" : "protobuf",
        "data" : "Ci90eXBlLmdvb2dsZWFwaXMuY29tL2F6dXJlLndlYnB1YnN1Yi5UZXN0TWVzc2FnZRICCAE=" // Base64-encoded bytes
    }
    ```

    > [!NOTE]
    > 数据是 Base64 编码的可反序列化 protobuf 二进制文件。 

可以使用以下 protobuf 定义，并使用 `Any.unpack()` 将其反序列化：

```protobuf
syntax = "proto3";

message MyMessage {
    int32 value = 1;
}
```

* `group` 组中的原始客户端接收二进制帧：

    ```
    # Show in hexadecimal
    0A 2F 74 79 70 65 2E 67 6F 6F 67 6C 65 61 70 69 73 2E 63 6F 6D 2F 61 7A 75 72 65 2E 77 65 62 70 75 62 73 75 62 2E 54 65 73 74 4D 65 73 73 61 67 65 12 02 08 01
    ```

#### <a name="case-3-publish-binary-data"></a>案例 3：发布二进制数据

将 `send_to_group_message.group` 设置为 `group`，将 `send_to_group_message.data.binary_data` 设置为 `[1, 2, 3]`。

* `group` 组中的 protobuf 子协议客户端接收二进制帧，并可以使用 [DownstreamMessage](#responses) 将该二进制帧反序列化。

* `group` 组中的 JSON 子协议客户端将接收：

    ```json
    {
        "type": "message",
        "from": "group",
        "group": "group",
        "dataType" : "binary",
        "data" : "AQID", // Base64-encoded [1,2,3]
    }
    ```

    由于 JSON 子协议客户端仅支持基于文本的消息，因此该二进制文件始终是 Base64 编码的文件。

* `group` 组中的原始客户端接收二进制帧中的二进制数据：

    ```
    # Show in hexadecimal
    01 02 03
    ```

### <a name="send-custom-events"></a>发送自定义事件

有一个隐式 `dataType`，它可以是 `protobuf`、`text` 或 `binary`，具体取决于设置的 `dataType`。 接收方客户端可以使用 `dataType` 来正确处理内容。

* `protobuf`：如果设置 `event_message.data.protobuf_data`，则隐式 `dataType` 为 `protobuf`。 `protobuf_data` 可以是任何受支持的 protobuf 类型。 事件处理程序接收 protobuf 编码的二进制文件，该文件可由任何 protobuf SDK 反序列化。

* `text`：如果设置 `event_message.data.text_data`，则隐式类型为 `text`。 `text_data` 应为字符串。 事件处理程序接收 UTF-8 编码的字符串；

* `binary`：如果设置 `event_message.data.binary_data`，则隐式类型为 `binary`。 `binary_data` 应为字节数组。 事件处理程序接收原始二进制帧。

#### <a name="case-1-send-an-event-with-text-data"></a>案例 1：发送包含文本数据的事件

将 `event_message.data.text_data` 设置为 `"text data"`。

上游事件处理程序接收类似于以下内容的请求。 请注意，CloudEvents HTTP 请求的 `Content-Type` 为 `text/plain`，其中 `dataType`=`text`。

```HTTP
POST /upstream HTTP/1.1
Host: xxxxxx
WebHook-Request-Origin: xxx.webpubsub.azure.com
Content-Type: text/plain
Content-Length: nnnn
ce-specversion: 1.0
ce-type: azure.webpubsub.user.<event_name>
ce-source: /client/{connectionId}
ce-id: {eventId}
ce-time: 2021-01-01T00:00:00Z
ce-signature: sha256={connection-id-hash-primary},sha256={connection-id-hash-secondary}
ce-userId: {userId}
ce-connectionId: {connectionId}
ce-hub: {hub_name}
ce-eventName: <event_name>

text data

```

#### <a name="case-2-send-an-event-with-protobuf-data"></a>案例 2：发送包含 protobuf 数据的事件

假设你收到了以下客户消息：

```
message MyMessage {
    int32 value = 1;
}
```

使用 `value = 1` 将 `event_message.data.protobuf_data` 设置为 `any.pack(MyMessage)`

上游事件处理程序接收类似于以下内容的请求。 请注意，CloudEvents HTTP 请求的 `Content-Type` 为 `application/x-protobuf`，其中 `dataType`=`protobuf`。

```HTTP
POST /upstream HTTP/1.1
Host: xxxxxx
WebHook-Request-Origin: xxx.webpubsub.azure.com
Content-Type: application/json
Content-Length: nnnn
ce-specversion: 1.0
ce-type: azure.webpubsub.user.<event_name>
ce-source: /client/{connectionId}
ce-id: {eventId}
ce-time: 2021-01-01T00:00:00Z
ce-signature: sha256={connection-id-hash-primary},sha256={connection-id-hash-secondary}
ce-userId: {userId}
ce-connectionId: {connectionId}
ce-hub: {hub_name}
ce-eventName: <event_name>

// Just show in hexadecimal; read it as binary
0A 2F 74 79 70 65 2E 67 6F 6F 67 6C 65 61 70 69 73 2E 63 6F 6D 2F 61 7A 75 72 65 2E 77 65 62 70 75 62 73 75 62 2E 54 65 73 74 4D 65 73 73 61 67 65 12 02 08 01
```

数据是有效的 protobuf 二进制文件。 可以使用以下 `proto` 和 `any.unpack()` 将其反序列化：

```protobuf
syntax = "proto3";

message MyMessage {
    int32 value = 1;
}
```

#### <a name="case-3-send-an-event-with-binary-data"></a>案例 3：发送包含二进制数据的事件

将 `send_to_group_message.binary_data` 设置为 `[1, 2, 3]`。

上游事件处理程序接收类似于以下内容的请求。 对于 `dataType`=`binary`，CloudEvents HTTP 请求的 `Content-Type` 为 `application/octet-stream`。 

```HTTP
POST /upstream HTTP/1.1
Host: xxxxxx
WebHook-Request-Origin: xxx.webpubsub.azure.com
Content-Type: application/octet-stream
Content-Length: nnnn
ce-specversion: 1.0
ce-type: azure.webpubsub.user.<event_name>
ce-source: /client/{connectionId}
ce-id: {eventId}
ce-time: 2021-01-01T00:00:00Z
ce-signature: sha256={connection-id-hash-primary},sha256={connection-id-hash-secondary}
ce-userId: {userId}
ce-connectionId: {connectionId}
ce-hub: {hub_name}
ce-eventName: <event_name>

// Just show in hexadecimal; you need to read it as binary
01 02 03 
```

对于文本消息帧，WebSocket 帧可以采用 `text` 格式；对于 `binary` 消息帧，它可以是 UTF-8 编码的二进制数据。

如果消息与所述格式不匹配，服务将拒绝客户端。

## <a name="responses"></a>响应

所有响应消息遵循以下 protobuf 格式：

```protobuf
message DownstreamMessage {
    oneof message {
        AckMessage ack_message = 1;
        DataMessage data_message = 2;
        SystemMessage system_message = 3;
    }
    
    message AckMessage {
        int32 ack_id = 1;
        bool success = 2;
        optional ErrorMessage error = 3;
    
        message ErrorMessage {
            string name = 1;
            string message = 2;
        }
    }

    message DataMessage {
        string from = 1;
        optional string group = 2;
        MessageData data = 3;
    }

    message SystemMessage {
        oneof message {
            ConnectedMessage connected_message = 1;
            DisconnectedMessage disconnected_message = 2;
        }
    
        message ConnectedMessage {
            string connection_id = 1;
            string user_id = 2;
        }

        message DisconnectedMessage {
            string reason = 2;
        }
    }
}
```

客户端接收的消息的类型可以是以下三种类型中的任何一种：`ack`、`message` 或 `system`。 

### <a name="ack-response"></a>确认响应

如果请求包含 `ackId`，则服务将为此请求返回确认响应。 客户端实现应处理此确认机制，包括：
* 等待 `async` `await` 操作的确认响应。 
* 如果在特定时间段内未收到确认响应，则进行超时检查。

客户端实现应始终首先检查 `success` 状态是 `true` 还是 `false`。 当 `success` 状态为 `false` 时，客户端可以从错误详细信息的 `error` 属性中读取数据。

### <a name="message-response"></a>消息响应

客户端可以接收客户端加入到的组发布的消息。 或者，当服务器将消息发送到特定客户端或特定用户时，客户端可以从服务器管理角色接收消息。

在以下情况下，你始终会收到 `DownstreamMessage.DataMessage` 消息：

- 当消息来自组时，`from` 为 `group`。 当消息来自服务器时，`from` 为 `server`。
- 当消息来自组时，`group` 是组名。

发送方的 `dataType` 将导致发送以下消息之一： 
* 如果 `dataType` 为 `text`，则使用 `message_response_message.data.text_data`。 
* 如果 `dataType` 为 `binary`，则使用 `message_response_message.data.binary_data`。 
* 如果 `dataType` 为 `protobuf`，则使用 `message_response_message.data.protobuf_data`。 
* 如果 `dataType` 为 `json`，则使用 `message_response_message.data.text_data`，并且内容是序列化的 JSON 字符串。

### <a name="system-response"></a>系统响应

Web PubSub 服务还可以向客户端发送与系统相关的响应。 

#### <a name="connected"></a>已连接

当客户端连接到服务时，你将收到 `DownstreamMessage.SystemMessage.ConnectedMessage` 消息。

#### <a name="disconnected"></a>已断开连接

当服务器关闭连接或服务拒绝客户端时，你将收到 `DownstreamMessage.SystemMessage.DisconnectedMessage` 消息。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [next step](includes/include-next-step.md)]
