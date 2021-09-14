---
title: 参考 - Azure Web PubSub 支持的 Protobuf WebSocket 子协议 `protobuf.webpubsub.azure.v1`
description: 本参考文章介绍 Azure Web PubSub 支持的 WebSocket 子协议 `protobuf.webpubsub.azure.v1`
author: chenyl
ms.author: chenyl
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 08/31/2021
ms.openlocfilehash: 9b54f219f52c9fa19134841488fcdf7f2bbc844d
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123439819"
---
#  <a name="azure-web-pubsub-supported-protobuf-websocket-subprotocol"></a>Azure Web PubSub 支持的 Protobuf WebSocket 子协议
     
本文档介绍子协议 `protobuf.webpubsub.azure.v1`。

当客户端使用此子协议时，传出数据帧和传入数据帧都应为 protobuf 有效负载。

## <a name="overview"></a>概述

子协议 `protobuf.webpubsub.azure.v1` 使客户端能够直接发布/订阅，而不是往返于上游服务器。 我们使用 PubSub WebSocket 客户端支持的 `protobuf.webpubsub.azure.v1` 子协议来调用 WebSocket 连接。

例如，在 JS 中，可以通过以下代码创建使用 Protobuf 子协议的 PubSub WebSocket 客户端：
```js
// PubSub WebSocket client
var pubsub = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'protobuf.webpubsub.azure.v1');
```
对于简单的 WebSocket 客户端，*服务器* 是“必备”角色，用于处理来自客户端的事件。 简单的 WebSocket 连接在发送消息时始终会触发 `message` 事件，并且始终依赖于服务器端处理消息以及执行其他操作。 借助 `protobuf.webpubsub.azure.v1` 子协议，授权客户端可以使用[联接请求](#join-groups)加入组，并可使用[发布请求](#publish-messages)直接息发布到组。 该子协议还可以使用 [事件请求 *来自定义消息所属于的* 事件](#send-custom-events)，从而将消息路由到不同的上游（事件处理程序）。

> [!NOTE]
> 目前，WebPubSub 服务仅支持 [proto3](https://developers.google.com/protocol-buffers/docs/proto3)。

## <a name="permissions"></a>权限

你可能已注意到，当我们描述 PubSub WebSocket 客户端时，客户端只能在 *获得授权* 时发布到其他客户端。 客户端的`role`确定了客户端具有的 *初始* 权限：

| 角色 | 权限 |
|---|---|
| 未指定 | 客户端可以发送事件请求。
| `webpubsub.joinLeaveGroup` | 客户端可以加入/退出任何组。
| `webpubsub.sendToGroup` | 客户端可以将消息发布到任何组。
| `webpubsub.joinLeaveGroup.<group>` | 客户端可以加入/退出 `<group>` 组。
| `webpubsub.sendToGroup.<group>` | 客户端可以将消息发布到 `<group>` 组。

服务器端还可通过 REST API 或服务器 SDK 动态授予或撤销客户端的权限。

## <a name="requests"></a>请求

所有请求消息都遵循以下 protobuf 格式。

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

* `ackId` 是可选的，表示此命令消息的增量整数。 指定 `ackId` 时，服务在执行命令后将[确认响应消息](#ack-response)发送回客户端。

### <a name="leave-groups"></a>退出组

格式：

将 `leave_group_message.group` 设置为组名。

* `ackId` 是可选的，表示此命令消息的增量整数。 指定 `ackId` 时，服务在执行命令后将[确认响应消息](#ack-response)发送回客户端。

### <a name="publish-messages"></a>发布消息

格式：

* `ackId` 是可选的，表示此命令消息的增量整数。 指定 `ackId` 时，服务在执行命令后将[确认响应消息](#ack-response)发送回客户端。

有一个隐式 `dataType`，它可以是 `protobuf`、`text` 或 `binary`，具体取决于设置的 `MessageData` 中的 `data`。 接收方客户端可以利用 `dataType` 来正确处理内容。

* `protobuf`：如果设置 `send_to_group_message.data.protobuf_data`，则隐式类型为 `protobuf`。 `protobuf_data` 可以是[任意](https://developers.google.com/protocol-buffers/docs/proto3#any)类型。 所有其他客户端将接收可由 protobuf SDK 反序列化的 protobuf 编码二进制文件。 在仅支持基于文本的内容（例如 `json.webpubsub.azure.v1`）的客户端中，它们将接收 base64 编码的二进制文件；

* `text`：如果设置 `send_to_group_message.data.text_data`，则隐式类型为 `text`。 `text_data` 应为字符串。 使用其他协议的所有客户端将接收 UTF-8 编码的字符串；

* `binary`：如果设置 `send_to_group_message.data.binary_data`，则隐式类型为 `binary`。 `binary_data` 应为字节数组。 使用其他协议的所有客户端将接收未经过 protobuf 编码的原始二进制文件。 在仅支持基于文本的内容（例如 `json.webpubsub.azure.v1`）的客户端中，它们将接收 base64 编码的二进制文件；

#### <a name="case-1-publish-text-data"></a>案例 1：发布文本数据：

将`send_to_group_message.group`设置为`group`，将`send_to_group_message.data.text_data`设置为`"text data"`。

* 此 `group` 组中的哪个 protobuf 子协议客户端接收二进制帧，并可以使用 [DownstreamMessage](#responses) 进行反序列化。

* 此 `group` 组中的 json 子协议客户端接收哪些内容：

```json
{
    "type": "message",
    "from": "group",
    "group": "group",
    "dataType" : "text",
    "data" : "text data"
}
```

* `group` 组中的原始客户端收到的是字符串数据 `text data`。

#### <a name="case-2-publish-protobuf-data"></a>案例 2：发布 protobuf 数据：

假设你有一条客户消息：

```
message MyMessage {
    int32 value = 1;
}
```

使用 `value = 1` 将 `send_to_group_message.group` 设置为 `group` 并将 `send_to_group_message.data.protobuf_data` 设置为 `Any.pack(MyMessage)`

* 此 `group` 组中的哪个 protobuf 子协议客户端接收二进制帧，并可以使用 [DownstreamMessage](#responses) 进行反序列化。

* `group` 组中的子协议客户端将收到哪些内容：

```json
{
    "type": "message",
    "from": "group",
    "group": "G",
    "dataType" : "protobuf",
    "data" : "Ci90eXBlLmdvb2dsZWFwaXMuY29tL2F6dXJlLndlYnB1YnN1Yi5UZXN0TWVzc2FnZRICCAE=" // Base64 encoded bytes
}
```

请注意，数据是 base64 编码的可反序列化 protobuf 二进制文件。 可以使用以下 protobuf 定义，并使用 `Any.unpack()` 将其反序列化：

```protobuf
syntax = "proto3";

message MyMessage {
    int32 value = 1;
}
```

* 此 `group` 组中的原始客户端接收的是二进制帧。

```
# Show in Hex
0A 2F 74 79 70 65 2E 67 6F 6F 67 6C 65 61 70 69 73 2E 63 6F 6D 2F 61 7A 75 72 65 2E 77 65 62 70 75 62 73 75 62 2E 54 65 73 74 4D 65 73 73 61 67 65 12 02 08 01
```

#### <a name="case-3-publish-binary-data"></a>案例 3：发布二进制数据：

将`send_to_group_message.group`设置为`group`，将`send_to_group_message.data.binary_data`设置为`[1, 2, 3]`。

* 此 `group` 组中的哪个 protobuf 子协议客户端接收二进制帧，并可以使用 [DownstreamMessage](#responses) 进行反序列化。

* 此 `group` 组中的 json 子协议客户端接收哪些内容：

```json
{
    "type": "message",
    "from": "group",
    "group": "group",
    "dataType" : "binary",
    "data" : "AQID", // Base64 encoded [1,2,3]
}
```

由于 json 子协议客户端仅支持基于文本的消息，因此二进制文件始终使用 base64 进行编码。

* `group` 组中的原始客户端收到的是二进制帧格式的 **二进制** 数据。

```
# Show in Hex
01 02 03
```

### <a name="send-custom-events"></a>发送自定义事件

有一个隐式 `dataType`，它可以是 `protobuf`、`text` 或 `binary`，具体取决于设置的 dataType。 接收方客户端可以利用 `dataType` 来正确处理内容。

* `protobuf`：如果设置 `event_message.data.protobuf_data`，则隐式类型为 `protobuf`。 `protobuf_data` 可以是任何受支持的 protobuf 类型。 事件处理程序将接收可由任何 protobuf SDK 反序列化的 protobuf 编码二进制文件。

* `text`：如果设置 `event_message.data.text_data`，则隐式类型为 `text`。 `text_data` 应为字符串。 事件处理程序将接收 UTF-8 编码的字符串；

* `binary`：如果设置 `event_message.data.binary_data`，则隐式类型为 `binary`。 `binary_data` 应为字节数组。 事件处理程序将接收原始二进制帧

#### <a name="case-1-send-event-with-text-data"></a>案例 1：发送包含文本数据的事件：

将 `event_message.data.text_data` 设置为 `"text data"`。

上游事件处理程序的内容如下所示，对于 `dataType`=`text`，CloudEvents HTTP 请求的 `Content-Type` 为 `text/plain`

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

#### <a name="case-2-send-event-with-protobuf-data"></a>案例 2：发送具有 protobuf 数据的事件：

假设你有一条客户消息：

```
message MyMessage {
    int32 value = 1;
}
```

使用 `value = 1` 将 `event_message.data.protobuf_data` 设置为 `any.pack(MyMessage)`

上游事件处理程序接收的内容如下所示，请注意，对于 `dataType`=`protobuf`，CloudEvents HTTP 请求的 `Content-Type` 为 `application/x-protobuf`

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

// Just show in hex, you need to read as binary
0A 2F 74 79 70 65 2E 67 6F 6F 67 6C 65 61 70 69 73 2E 63 6F 6D 2F 61 7A 75 72 65 2E 77 65 62 70 75 62 73 75 62 2E 54 65 73 74 4D 65 73 73 61 67 65 12 02 08 01
```

数据是有效的 protobuf 二进制文件。 可以使用以下 `proto` 和 `any.unpack()` 将其反序列化：

```protobuf
syntax = "proto3";

message MyMessage {
    int32 value = 1;
}
```

#### <a name="case-3-send-event-with-binary-data"></a>案例 3：发送包含二进制数据的事件：

将 `send_to_group_message.binary_data` 设置为 `[1, 2, 3]`。

上游事件处理程序的内容如下所示，对于 `dataType`=`binary`，CloudEvents HTTP 请求的 `Content-Type` 为 `application/octet-stream`

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

// Just show in hex, you need to read as binary
01 02 03 
```

对于文本消息帧，WebSocket 帧可以是 `text` 格式，对于 `binary` 消息帧，可以是 UTF8 编码的二进制数据。

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

客户端接收的消息可以有以下几种类型：`ack`、`message` 和 `system`： 

### <a name="ack-response"></a>确认响应

如果请求包含 `ackId`，则服务将为此请求返回确认响应。 客户端实现应处理此确认机制，包括等待 `async` `await` 操作的确认响应，并且如果在特定时间段内没有收到确认响应，则会进行超时检查。

客户端实现应始终首先检查 `success` 是 `true` 还是 `false`。 仅当 `success` 为 `false` 时，客户端才会从 `error` 读取。

### <a name="message-response"></a>消息响应

客户端可以从以下对象中接收消息：客户端加入的某个组；服务器将消息发送到特定客户端或特定用户的服务器管理角色。

你始终将获取 `DownstreamMessage.DataMessage`

- 当消息来自组时，`from` 将是 `group`。 当消息来自服务器时， `from` 将是 `server`

- 当消息来自组时，`group` 将是组名。

- 发送方的 `dateType` 将导致设置其中一个消息。 如果 `dateType` 为 `text`，则应使用 `message_response_message.data.text_data`。 如果 `dateType` 为 `binary`，则应使用 `message_response_message.data.binary_data`。 如果 `dateType` 为 `protobuf`，则应使用 `message_response_message.data.protobuf_data`。 如果 `dateType` 为 `json`，则应使用 `message_response_message.data.text_data`，内容将是序列化的 json 字符串。

### <a name="system-response"></a>系统响应

Web PubSub 服务还可以向客户端发送与系统相关的响应。 

#### <a name="connected"></a>已连接

当连接连接到服务时。 你将接收 `DownstreamMessage.SystemMessage.ConnectedMessage`

#### <a name="disconnected"></a>已断开连接

当服务器关闭连接时，或当服务拒绝客户端时。 你将接收 `DownstreamMessage.SystemMessage.DisconnectedMessage`

## <a name="next-steps"></a>后续步骤

[!INCLUDE [next step](includes/include-next-step.md)]