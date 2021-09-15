---
title: 参考 - 适用于 Azure Web PubSub 的 CloudEvents 扩展
description: 本参考介绍为 Azure Web PubSub 服务定义的 CloudEvents 扩展
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 08/16/2021
ms.openlocfilehash: a132fdf16fa62360827a516f034bcd37c38b2928
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446596"
---
#  <a name="cloudevents-extension-for-azure-web-pubsub"></a>适用于 Azure Web PubSub 的 CloudEvents 扩展

服务使用 [CloudEvents HTTP 协议](https://github.com/cloudevents/spec/blob/v1.0.1/http-protocol-binding.md)将客户端事件传送到上游 webhook。

从服务发送到服务器的数据将始终采用 CloudEvents `binary` 格式。

- [Webhook 验证](#protection)
- [Web PubSub CloudEvents 属性扩展](#extension)
- [事件](#events)
    - 阻塞事件
        - [系统 `connect` 事件](#connect)
        - [用户事件](#message)
    - 非阻塞事件
        - [系统 `connected` 事件](#connected)
        - [系统 `disconnected` 事件](#disconnected)

## <a name="webhook-validation"></a>Webhook 验证

<a name="protection"></a>

Webhook 验证遵循 [CloudEvents](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection)。 请求的标头中始终包含 `WebHook-Request-Origin: xxx.webpubsub.azure.com`。

当且仅当传送目标允许传送事件时，在答复请求时必须包含 `WebHook-Allowed-Origin` 标头，例如：

`WebHook-Allowed-Origin: *`

或：

`WebHook-Allowed-Origin: xxx.webpubsub.azure.com`

目前，不支持 [WebHook-Request-Rate](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#414-webhook-request-rate) 和 [WebHook-Request-Callback](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#413-webhook-request-callback)。


## <a name="web-pubsub-cloudevents-attribute-extension"></a>Web PubSub CloudEvents 属性扩展
<a name="extension"></a>

> 另请注意，HTTP 规范现在遵循类似的模式，不再建议扩展 HTTP 标头以 X- 作为前缀。

此扩展定义了 Web PubSub 针对其生成的每个事件所使用的属性。

### <a name="attributes"></a>属性

| 名称 | 类型 | 描述 | 示例|
|--|--|--|--|
| `userId` | `string` | 连接通过身份验证的用户 | |
| `hub` | `string` | 连接所属于的中心 | |
| `connectionId` | `string` | connectionId 对于客户端连接是唯一的 | |
| `eventName` | `string` | 不带前缀的事件名称 | |
| `subprotocol` | `string` | 客户端使用的子协议（如果有） | |
| `signature` | `string` | 上游 Webhook 的签名，用于验证传入请求是否来自预期来源。 该服务在计算值时使用主访问密钥和辅助访问密钥作为 HMAC 密钥：`Hex_encoded(HMAC_SHA256(accessKey, connectionId))`。 上游应在处理请求之前检查请求是否有效。 | |

## <a name="events"></a>事件

有两种类型的事件，一种是 *阻塞* 事件，服务等待这种事件的响应以继续后续步骤。 一种是 *非阻塞* 事件，服务在处理下一条消息之前，不会等待此类事件的响应。

- 阻塞事件
    - [系统 `connect` 事件](#connect)
    - [用户事件](#message)
- 非阻塞事件
    - [系统 `connected` 事件](#connected)
    - [系统 `disconnected` 事件](#disconnected)

### <a name="system-connect-event"></a>系统 `connect` 事件
<a name="connect"></a>

* `ce-type`: `azure.webpubsub.sys.connect`
* `Content-Type`: `application/json`

#### <a name="request-format"></a>请求格式：

```HTTP
POST /upstream HTTP/1.1
Host: xxxxxx
WebHook-Request-Origin: xxx.webpubsub.azure.com
Content-Type: application/json; charset=utf-8
Content-Length: nnnn
ce-specversion: 1.0
ce-type: azure.webpubsub.sys.connect
ce-source: /hubs/{hub}/client/{connectionId}
ce-id: {eventId}
ce-time: 2021-01-01T00:00:00Z
ce-signature: sha256={connection-id-hash-primary},sha256={connection-id-hash-secondary}
ce-userId: {userId}
ce-connectionId: {connectionId}
ce-hub: {hub}
ce-eventName: connect

{
    "claims": {},
    "query": {},
    "headers": {},
    "subprotocols": [],
    "clientCertificates": [
        {
            "thumbprint": "ABC"
        }
    ]
}

```

#### <a name="success-response-format"></a>成功响应格式：

* `204`：成功，无内容。
* `200`：成功后，内容应为 JSON 格式，允许以下属性：

```HTTP
HTTP/1.1 200 OK

{
    "groups": [],
    "userId": "",
    "roles": [],
    "subprotocol": ""
}

```

* `subprotocols`

    `connect` 事件将子协议和身份验证信息从客户端转发到 Upstream。 Azure SignalR 服务使用状态码来确定请求是否将升级为 WebSocket 协议。

    如果请求包含 `subprotocols` 属性，则服务器应返回它支持的一个子协议。 如果服务器不想使用任何子协议，则不应在响应中发送 `subprotocol` 属性。 [发送空白标头无效](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API/Writing_WebSocket_servers#Subprotocols)。

* `userId`: `{auth-ed user ID}`

    由于服务允许匿名连接，因此 `connect` 事件负责向服务告知客户端连接的用户 ID。 服务会从响应有效负载 `userId` 中读取用户 ID（如果存在）。 如果从请求声明和 `connect` 事件的响应有效负载中均无法读取用户 ID，连接将被删除。

<a name="connect_response_header_group"></a>
 
* `groups`: `{groups to join}`

    此属性为用户提供了一种将此连接添加到一个或多个组的简便方法。 这样，在将此连接添加到某个组时无需再进行另一次调用。

* `roles`: `{roles the client has}`
    
    此属性为 Upstream 提供了一种授权客户端的方法。 不同的角色定义客户端拥有的不同初始权限，如果客户端是 PubSub WebSocket 客户端，则非常有用。 有关权限的详细信息，请参阅[客户端权限](./concept-client-protocols.md#permissions)。

#### <a name="error-response-format"></a>错误响应格式：

* `4xx`：错误，来自 Upstream 的响应将返回为客户端请求的响应。

```HTTP
HTTP/1.1 401 Unauthorized
```

### <a name="system-connected-event"></a>系统 `connected` 事件
<a name="connected"></a> 当客户端完成 WebSocket 握手并成功连接时，服务将调用 Upstream。

* `ce-type`: `azure.webpubsub.sys.connected`
* `Content-Type`: `application/json`

请求正文为空 JSON。

#### <a name="request-format"></a>请求格式：

```HTTP
POST /upstream HTTP/1.1
Host: xxxxxx
WebHook-Request-Origin: xxx.webpubsub.azure.com
Content-Type: application/json; charset=utf-8
Content-Length: nnnn
ce-specversion: 1.0
ce-type: azure.webpubsub.sys.connected
ce-source: /hubs/{hub}/client/{connectionId}
ce-id: {eventId}
ce-time: 2021-01-01T00:00:00Z
ce-signature: sha256={connection-id-hash-primary},sha256={connection-id-hash-secondary}
ce-userId: {userId}
ce-connectionId: {connectionId}
ce-hub: {hub}
ce-eventName: connect
ce-subprotocol: abc

{}

```

#### <a name="response-format"></a>响应格式：

`2xx`：成功响应。

`connected` 是一个异步事件，当响应状态代码为“not success”，服务将记录一条错误。

```HTTP
HTTP/1.1 200 OK
```


### <a name="system-disconnected-event"></a>系统 `disconnected` 事件
如果 connect 事件返回了 `2xx` 状态代码，则在客户端请求完成时，将始终会触发 <a name="disconnected"></a>
`disconnected` 事件。 

* `ce-type`: `azure.webpubsub.sys.disconnected`
* `Content-Type`: `application/json`

#### <a name="request-format"></a>请求格式：

```HTTP
POST /upstream HTTP/1.1
Host: xxxxxx
WebHook-Request-Origin: xxx.webpubsub.azure.com
Content-Type: application/json; charset=utf-8
Content-Length: nnnn
ce-specversion: 1.0
ce-type: azure.webpubsub.sys.disconnected
ce-source: /hubs/{hub}/client/{connectionId}
ce-id: {eventId}
ce-time: 2021-01-01T00:00:00Z
ce-signature: sha256={connection-id-hash-primary},sha256={connection-id-hash-secondary}
ce-userId: {userId}
ce-connectionId: {connectionId}
ce-hub: {hub}
ce-eventName: disconnect
ce-subprotocol: abc

{
    "reason": "{Reason}"
}

```

* `reason`

    `reason` 描述了客户端断开连接的原因。


#### <a name="response-format"></a>响应格式：

`2xx`：成功响应。

`disconnected` 是一个异步事件，当响应状态代码表示未成功时，服务将记录一条错误。

```HTTP
HTTP/1.1 200 OK
```


### <a name="user-event-message-for-the-simple-websocket-clients"></a>简单 WebSocket 客户端的用户事件 `message`
<a name="message"></a> 该服务针对每个 WebSocket 消息帧在上游调用事件处理程序。

* `ce-type`: `azure.webpubsub.user.message`
* `Content-Type`: `application/octet-stream` 表示二进制帧；`text/plain` 表示文本帧； 

UserPayload 是客户端发送的内容。

#### <a name="request-format"></a>请求格式：

```HTTP
POST /upstream HTTP/1.1
Host: xxxxxx
WebHook-Request-Origin: xxx.webpubsub.azure.com
Content-Type: application/octet-stream | text/plain | application/json
Content-Length: nnnn
ce-specversion: 1.0
ce-type: azure.webpubsub.user.message
ce-source: /hubs/{hub}/client/{connectionId}
ce-id: {eventId}
ce-time: 2021-01-01T00:00:00Z
ce-signature: sha256={connection-id-hash-primary},sha256={connection-id-hash-secondary}
ce-userId: {userId}
ce-connectionId: {connectionId}
ce-hub: {hub}
ce-eventName: message

UserPayload

```

#### <a name="success-response-format"></a>成功响应格式

* 状态代码
    * `204`：成功，无内容。
    * `200`：成功，`UserResponsePayload` 的格式取决于响应的 `Content-Type`。
* `Content-Type`: `application/octet-stream` 表示二进制帧；`text/plain` 表示文本帧； 

当 `Content-Type` 为 `application/octet-stream` 时，服务使用 `binary` WebSocket 帧向客户端发送 `UserResponsePayload`。 当 `Content-Type` 为 `text/plain` 时，服务使用 `text` WebSocket 帧向客户端发送 `UserResponsePayload`。 

```HTTP
HTTP/1.1 200 OK
Content-Type: application/octet-stream (for binary frame) or text/plain (for text frame)
Content-Length: nnnn

UserResponsePayload
```

#### <a name="error-response-format"></a>错误响应格式
当状态代码表示未成功时，将其被视为错误响应。 如果 `message` 响应状态代码表示未成功时，连接将被删除。

### <a name="user-custom-event-custom_event-for-pubsub-websocket-clients"></a>PubSub WebSocket 客户端的用户自定义事件 `{custom_event}`
<a name="custom_event"></a>

服务针对每个有效的自定义事件消息调用事件处理程序 Webhook。

#### <a name="case-1-send-event-with-text-data"></a>案例 1：发送包含文本数据的事件：
```json
{
    "type": "event",
    "event": "<event_name>",
    "dataType" : "text",
    "data": "text data"
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
ce-subprotocol: json.webpubsub.azure.v1

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
ce-subprotocol: json.webpubsub.azure.v1

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
    "data": "aGVsbG8gd29ybGQ=" // base64 encoded binary
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
ce-subprotocol: json.webpubsub.azure.v1

<binary data>

```

#### <a name="success-response-format"></a>成功响应格式

```HTTP
HTTP/1.1 200 OK
Content-Type: application/octet-stream | text/plain | application/json
Content-Length: nnnn

UserResponsePayload
```
* 状态代码
    * `204`：成功，无内容。
    * `200`：成功，发送到 PubSub WebSocket 客户端的数据依赖于 `Content-Type`； 

* 当 `Content-Type` 为 `application/octet-stream` 时，服务在将 `UserResponsePayload` 发送回客户端时将使用 `dataType` 作为 `binary`，并且有效负载采用 base64 编码。 示例响应：
    ```json
    {
        "type": "message",
        "from": "server",
        "dataType": "binary",
        "data" : "aGVsbG8gd29ybGQ="
    }
    ```
* 当 `Content-Type` 为 `text/plain` 时，服务在将 `UserResponsePayload` 发送到客户端时将使用 `dataType` 作为 `text`，并且有效负载为字符串。
* 当 `Content-Type` 为 `application/json` 时，服务在将 `UserResponsePayload` 发送到客户端时将使用 `dataType`=`json`，后者以 `data` 值令牌作为响应有效负载正文。


#### <a name="error-response-format"></a>错误响应格式
当状态代码表示未成功时，将其被视为错误响应。 如果 `{custom_event}` 响应状态代码表示未成功时，连接将被删除。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [next step](includes/include-next-step.md)]
