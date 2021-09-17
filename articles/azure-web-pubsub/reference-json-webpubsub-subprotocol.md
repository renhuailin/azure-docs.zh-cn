---
title: 参考文章 - Azure Web PubSub 支持的 JSON WebSocket 子协议 `json.webpubsub.azure.v1`
description: 本参考文章介绍 Azure Web PubSub 支持的 WebSocket 子协议 `json.webpubsub.azure.v1`
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 08/16/2021
ms.openlocfilehash: 724ffa23cf533133603db717b4d01ebbd81894a1
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446609"
---
#  <a name="azure-web-pubsub-supported-json-websocket-subprotocol"></a>Azure Web PubSub 支持的 JSON WebSocket 子协议
     
本文档介绍子协议 `json.webpubsub.azure.v1`。

当客户端使用此子协议时，传出数据帧和传入数据帧都应为 JSON 有效负载。

## <a name="overview"></a>概述

子协议 `json.webpubsub.azure.v1` 使客户端能够直接发布/订阅，而不是往返于上游服务器。 我们使用 PubSub WebSocket 客户端支持的 `json.webpubsub.azure.v1` 子协议来调用 WebSocket 连接。

例如，在 JS 中，可以使用以下方式来创建 PubSub WebSocket 客户端：
```js
// PubSub WebSocket client
var pubsub = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'json.webpubsub.azure.v1');
```
对于简单的 WebSocket 客户端，*服务器* 是“必备”角色，用于处理来自客户端的事件。 简单的 WebSocket 连接在发送消息时始终会触发 `message` 事件，并且始终依赖于服务器端处理消息以及执行其他操作。 借助 `json.webpubsub.azure.v1` 子协议，授权客户端可以使用[联接请求](#join-groups)加入组，并可使用[发布请求](#publish-messages)直接息发布到组。 该子协议还可以使用 [事件请求 *来自定义消息所属于的* 事件](#send-custom-events)，从而将消息路由到不同的上游（事件处理程序）。

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

### <a name="join-groups"></a>加入组

格式：

```json
{
    "type": "joinGroup",
    "group": "<group_name>",
    "ackId" : 1 // optional
}
```

* `ackId` 是可选的，表示此命令消息的增量整数。 指定 `ackId` 时，服务在执行命令后将[确认响应消息](#ack-response)发送回客户端。

### <a name="leave-groups"></a>退出组

格式：

```json
{
    "type": "leaveGroup",
    "group": "<group_name>",
    "ackId" : 1 // optional
}
```

* `ackId` 是可选的，表示此命令消息的增量整数。 指定 `ackId` 时，服务在执行命令后将[确认响应消息](#ack-response)发送回客户端。

### <a name="publish-messages"></a>发布消息

格式：

```json
{
    "type": "sendToGroup",
    "group": "<group_name>",
    "ackId" : 1, // optional
    "dataType" : "json|text|binary",
    "data": {}, // data can be string or valid json token depending on the dataType 
}
```

* `ackId` 是可选的，表示此命令消息的增量整数。 指定 `ackId` 时，服务在执行命令后将[确认响应消息](#ack-response)发送回客户端。

`dataType` 可以是 `json`、`text` 或 `binary` 之一：
* `json`：`data` 可以是 JSON 支持的任何类型，并且将按原样发布；如果未指定 `dataType`，则默认为 `json`。
* `text`：`data` 应为字符串格式，并且将发布字符串数据；
* `binary`：`data` 应采用 base64 格式，并且将发布二进制数据；

#### <a name="case-1-publish-text-data"></a>案例 1：发布文本数据：
```json
{
    "type": "sendToGroup",
    "group": "<group_name>",
    "dataType" : "text",
    "data": "text data" 
}
```

* `<group_name>` 组中的子协议客户端将收到哪些内容：
```json
{
    "type": "message",
    "from": "group",
    "group": "<group_name>",
    "dataType" : "text",
    "data" : "text data"
}
```
* `<group_name>` 组中的原始客户端收到的是字符串数据 `text data`。

#### <a name="case-2-publish-json-data"></a>案例 2：发布 JSON 数据：
```json
{
    "type": "sendToGroup",
    "group": "<group_name>",
    "dataType" : "json",
    "data": {
        "hello": "world"
    }
}
```

* `<group_name>` 组中的子协议客户端将收到哪些内容：
```json
{
    "type": "message",
    "from": "group",
    "group": "<group_name>",
    "dataType" : "json",
    "data" : {
        "hello": "world"
    }
}
```
* `<group_name>` 组中的原始客户端收到的是序列化字符串数据 `{"hello": "world"}`。


#### <a name="case-3-publish-binary-data"></a>案例 3：发布二进制数据：
```json
{
    "type": "sendToGroup",
    "group": "<group_name>",
    "dataType" : "binary",
    "data": "<base64_binary>"
}
```

* `<group_name>` 组中的子协议客户端将收到哪些内容：
```json
{
    "type": "message",
    "from": "group",
    "group": "<group_name>",
    "dataType" : "binary",
    "data" : "<base64_binary>", 
}
```
* `<group_name>` 组中的原始客户端收到的是二进制帧格式的 **二进制** 数据。

### <a name="send-custom-events"></a>发送自定义事件

格式：

```json
{
    "type": "event",
    "event": "<event_name>",
    "dataType" : "json|text|binary",
    "data": {}, // data can be string or valid json token depending on the dataType 
}
```

`dataType` 可以是 `text`、`binary` 或 `json` 之一：
* `json`：数据可以是 JSON 支持的任何类型，并且将按原样发布；如果未指定 `dataType`，则默认为 `json`。
* `text`：数据应为字符串格式，并且将发布字符串数据；
* `binary`：数据应采用 base64 格式，并且将发布二进制数据；

#### <a name="case-1-send-event-with-text-data"></a>案例 1：发送包含文本数据的事件：
```json
{
    "type": "event",
    "event": "<event_name>",
    "dataType" : "text",
    "data": "text data", 
}
```

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

#### <a name="case-2-send-event-with-json-data"></a>案例 2：发送包含 JSON 数据的事件：
```json
{
    "type": "event",
    "event": "<event_name>",
    "dataType" : "json",
    "data": {
        "hello": "world"
    }, 
}
```

上游事件处理程序的内容如下所示，对于 `dataType`=`json`，CloudEvents HTTP 请求的 `Content-Type` 为 `application/json`

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

{
    "hello": "world"
}

```

#### <a name="case-3-send-event-with-binary-data"></a>案例 3：发送包含二进制数据的事件：
```json
{
    "type": "event",
    "event": "<event_name>",
    "dataType" : "binary",
    "data": "base64_binary", 
}
```

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

binary

```

对于文本消息帧，WebSocket 帧可以是 `text` 格式，对于 `binary` 消息帧，可以是 UTF8 编码的二进制数据。

如果消息与所述格式不匹配，服务将拒绝客户端。

## <a name="responses"></a>响应

客户端接收的消息可以有以下几种类型：`ack`、`message` 和 `system`： 

### <a name="ack-response"></a>确认响应

如果请求包含 `ackId`，则服务将为此请求返回确认响应。 客户端实现应处理此确认机制，包括等待 `async` `await` 操作的确认响应，并且如果在特定时间段内没有收到确认响应，则会进行超时检查。

格式：
```json
{
    "type": "ack",
    "ackId": 1, // The ack id for the request to ack
    "success": false, // true or false
    "error": {
        "name": "NotFound|Forbidden|Timeout|InternalServerError",
        "message": "<error_detail>"
    }
}
```

客户端实现应始终首先检查 `success` 是 `true` 还是 `false`。 仅当 `success` 为 `false` 时，客户端才会从 `error` 读取。

### <a name="message-response"></a>消息响应

客户端可以从以下对象中接收消息：客户端加入的某个组；服务器将消息发送到特定客户端或特定用户的服务器管理角色。

1. 当消息来自组时

    ```json
    {
        "type": "message",
        "from": "group",
        "group": "<group_name>",
        "dataType": "json|text|binary",
        "data" : {} // The data format is based on the dataType
    }
    ```

1. 当消息来自服务器时。

    ```json
    {
        "type": "message",
        "from": "server",
        "dataType": "json|text|binary",
        "data" : {} // The data format is based on the dataType
    }
    ```

#### <a name="case-1-sending-data-hello-world-to-the-connection-through-rest-api-with-content-typetextplain"></a>案例 1：通过 REST API 使用 `Content-Type`=`text/plain` 将数据 `Hello World` 发送到连接 
* 简单的 WebSocket 客户端收到的是包含数据的文本 WebSocket 帧：`Hello World`；
* PubSub WebSocket 客户端收到的内容如下所示：
    ```json
    {
        "type": "message",
        "from": "server",
        "dataType" : "text",
        "data": "Hello World", 
    }
    ```

#### <a name="case-2-sending-data--hello--world-to-the-connection-through-rest-api-with-content-typeapplicationjson"></a>案例 2：通过 REST API 使用 `Content-Type`=`application/json` 将数据 `{ "Hello" : "World"}` 发送到连接
* 简单的 WebSocket 客户端收到的是包含字符串式数据的文本 WebSocket 帧：`{ "Hello" : "World"}`；
* PubSub WebSocket 客户端收到的内容如下所示：
    ```json
    {
        "type": "message",
        "from": "server",
        "dataType" : "json",
        "data": {
            "Hello": "World"
        }
    }
    ```

如果 REST API 使用 `application/json` 内容类型发送一个字符串 `Hello World`，那么简单的 WebSocket 客户端接收的是一个 JSON 字符串，也就是 `"Hello World"`（为字符串加上了 `"`）。

#### <a name="case-3-sending-binary-data-to-the-connection-through-rest-api-with-content-typeapplicationoctet-stream"></a>案例 3：通过 REST API 使用 `Content-Type`=`application/octet-stream` 将二进制数据发送到连接
* 简单的 WebSocket 客户端收到的是包含二进制数据的二进制 WebSocket 帧。
* PubSub WebSocket 客户端收到的内容如下所示：
    ```json
    {
        "type": "message",
        "from": "server",
        "dataType" : "binary",
        "data": "<base64_binary>"
    }
    ```

### <a name="system-response"></a>系统响应

Web PubSub 服务还可以向客户端发送与系统相关的响应。 

#### <a name="connected"></a>已连接

当连接连接到服务时。

```json
{
    "type": "system",
    "event": "connected",
    "userId": "user1",
    "connectionId": "abcdefghijklmnop",
}
```

#### <a name="disconnected"></a>已断开连接

当服务器关闭连接时，或当服务拒绝客户端时。

```json
{
    "type": "system",
    "event": "disconnected",
    "message": "reason"
}
```

## <a name="next-steps"></a>后续步骤

[!INCLUDE [next step](includes/include-next-step.md)]