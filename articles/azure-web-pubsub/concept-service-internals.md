---
title: Azure Web PubSub 服务内部情况
description: 了解 Azure Web PubSub 服务内部机制、体系结构、连接以及数据的传输方式。
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 08/18/2021
ms.openlocfilehash: a7adef1e705d14578cdec9bec7a947cc9be0db20
ms.sourcegitcommit: 5d605bb65ad2933e03b605e794cbf7cb3d1145f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2021
ms.locfileid: "122598305"
---
#  <a name="azure-web-pubsub-service-internals"></a>Azure Web PubSub 服务内部情况

借助 Azure Web PubSub 服务，你可以通过使用简单的 [WebSocket](https://tools.ietf.org/html/rfc6455) 连接来轻松发布/订阅消息。

- 可以支持 WebSocket 的任何语言编写客户端
- 一个连接中同时支持文本消息和二进制消息
- 客户端可使用简单协议直接发布客户端-客户端消息
- 该服务可管理你的 WebSocket 连接

## <a name="terms"></a>术语
* 服务：Azure Web PubSub 服务。

[!INCLUDE [Terms](includes/terms.md)]

* 客户端连接和 ConnectionId：客户端成功连接到 `/client` 终结点时，服务将生成唯一的 `connectionId` 作为客户端连接的唯一标识。 然后，用户可以使用此 `connectionId` 管理客户端连接。 详情请参阅[客户端协议](#client_protocol)部分。

* 客户端事件：系统会在客户端连接生命周期内创建事件。 例如，某个简单的 WebSocket 客户端连接在尝试连接到服务时会创建 `connect` 事件，在成功连接到服务时会创建 `connected` 事件，在向服务发送消息时会创建 `message` 事件，在从服务断开连接时会创建 `disconnected` 事件。 有关客户端事件的详细信息，请参阅[客户端协议](#client_protocol)部分。

* 事件处理程序：事件处理程序包含用于处理客户端事件的逻辑。 通过门户或 Azure CLI 在服务中预先注册和配置事件处理程序。 详情请参阅[事件处理程序](#event_handler)部分。 用于托管事件处理程序逻辑的位置将被视为服务器端。

* 服务器：服务器可以处理客户端事件、管理客户端连接并将消息发布到组。 与客户端相比，服务器是值得信赖的。 有关服务器的详细信息，请参阅[服务器协议](#server_protocol)部分。

<a name="workflow"></a>

## <a name="workflow"></a>工作流

![显示 Web PubSub 服务工作流的关系图。](./media/concept-service-internals/workflow.png)

如上面的工作流图所示：
1. 客户端使用 WebSocket 传输连接到服务的 `/client` 终结点。 服务会将 WebSocket 的每一帧转接到配置上游（服务器）。 WebSocket 连接可以连接到任何自定义子协议以供服务器处理，也可以与服务支持的子协议 `json.webpubsub.azure.v1`（允许客户端直接执行发布/订阅操作）进行连接。 详情请参阅[客户端协议](#client_protocol)。
2. 服务在不同的客户端事件上使用 CloudEvents HTTP 协议调用服务器。 [CloudEvents](https://github.com/cloudevents/spec/blob/v1.0.1/http-protocol-binding.md) 指与协议无关的标准定义，用于说明云原生计算基金会 (CNCF) 托管的事件的结构和元数据。 详情请参阅[服务器协议](#server_protocol)。
3. 服务器可以使用 REST API 调用服务，以将消息发送到客户端或管理已连接的客户端。 详情请参阅[服务器协议](#server_protocol)

<a name="client_protocol"></a>

## <a name="client-protocol"></a>客户端协议

客户端连接使用 [WebSocket 协议](https://tools.ietf.org/html/rfc6455)连接到服务的 `/client` 终结点。 WebSocket 协议通过单个 TCP 连接提供全双工信道，并于 2011 年由 IETF 标准化为 RFC 6455。 大多数语言都提供本机支持来启动 WebSocket 连接。 

我们的服务支持两种类型的客户端：
- 其一称为[简单的 WebSocket 客户端](#simple_client)
- 其二称为 [PubSub WebSocket 客户端](#pubsub_client)

<a name="simple_client"></a>

### <a name="the-simple-websocket-client"></a>简单的 WebSocket 客户端
顾名思义，简单的 WebSocket 客户端就是一个简单的 WebSocket 连接。 此客户端还有自定义子协议。 

例如，在 JS 中，可以使用以下内容创建简单的 WebSocket 客户端：
```js
// simple WebSocket client1
var client1 = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1');

// simple WebSocket client2 with some custom subprotocol
var client2 = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'custom.subprotocol')

```

简单的 WebSocket 客户端遵循客户端<->服务器体系结构，如下方序列图所示：![显示客户端连接序列的关系图。](./media/concept-service-internals/simple-client-sequence.png)


1. 当客户端启动 WebSocket 握手时，服务会尝试调用 `connect` 事件处理程序（服务器）执行 WebSocket 握手。 用户可以使用此处理程序来处理 WebSocket 握手，确定要使用的子协议，对客户端进行身份验证，以及将客户端加入某些组。
2. 成功连接客户端后，服务将调用 `connected` 事件处理程序。 其可用作一些通知，并且不会阻止客户端发送消息。 用户可以使用此处理程序执行某些数据存储，并可以通过向客户端发送消息来进行响应。
2. 客户端发送消息时，服务将在事件处理程序（服务器）中触发 `message` 事件以处理发送的消息。 此事件属于常规事件，包含 WebSocket 帧中发送的消息。 用户需要在此事件处理程序中自行调度消息。
3. 当客户端断开连接时，服务会在检测到连接中断问题时尝试在事件处理程序（服务器）中触发 `disconnected` 事件。

事件可分为两类：
* 同步事件（阻止）同步事件会阻止客户端工作流。 当此类事件触发失败时，服务将删除客户端连接。
    * `connect`
    * `message`
* 异步事件（不阻止）异步事件不会阻止客户端工作流，其可在上游事件处理程序中用作一些通知。 当此类事件触发失败时，服务会记录错误详细信息。
    * `connected`
    * `disconnected`
    
#### <a name="scenarios"></a>方案：
此类连接可用于典型的客户端-服务器体系结构，即客户端向服务器发送消息，服务器使用[事件处理程序](#event_handler)处理传入的消息。 当客户在应用程序逻辑中应用现有[子协议](https://www.iana.org/assignments/websocket/websocket.xml)时，也可使用此类连接。

<a name="pubsub_client"></a>

### <a name="the-pubsub-websocket-client"></a>PubSub WebSocket 客户端
服务还支持名为 `json.webpubsub.azure.v1` 的特定子协议，该子协议允许客户端直接执行发布/订阅操作，而不是往返于上游服务器。 我们使用 PubSub WebSocket 客户端支持的 `json.webpubsub.azure.v1` 子协议来调用 WebSocket 连接。

例如，在 JS 中，可以使用以下内容创建 PubSub WebSocket 客户端：
```js
// PubSub WebSocket client
var pubsub = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'json.webpubsub.azure.v1');
```

PubSub WebSocket 客户端可以：
* 加入组，例如：
    ```json
    {
        "type": "joinGroup",
        "group": "<group_name>"
    }
    ```
* 退出组，例如：
    ```json
    {
        "type": "leaveGroup",
        "group": "<group_name>"
    }
    ```
* 将消息发布到组，例如：
    ```json
    {
        "type": "sendToGroup",
        "group": "<group_name>",
        "data": { "hello": "world" }
    }
    ```
* 将自定义事件发送到上游服务器，例如：

    ```json
    {
        "type": "event",
        "event": "<event_name>",
        "data": { "hello": "world" }
    }
    ```

[PubSub WebSocket 子协议](./reference-json-webpubsub-subprotocol.md)包含 `json.webpubsub.azure.v1` 子协议的详细信息。

你可能已经留意到，对于[简单的 WebSocket 客户端](#simple_client)，服务器是处理客户端事件时的“必备”角色。 简单的 WebSocket 连接在发送消息时始终会触发 `message` 事件，并且始终依赖于服务器端处理消息以及执行其他操作。 借助 `json.webpubsub.azure.v1` 子协议，授权客户端可以加入组，并可直接发布消息到组。 该子协议还可以自定义消息所属的事件，从而将消息路由到不同的上游（事件处理程序）。 

#### <a name="scenarios"></a>方案：
当客户端想要相互对话时，可以使用此类客户端。 若客户端享有相关权限，则可从 `client1` 向服务发送消息，服务可直接将消息传送给 `client2`。

客户端 1：

```js
var client1 = new WebSocket("wss://xxx.webpubsub.azure.com/client/hubs/hub1", "json.webpubsub.azure.v1");
client1.onmessage = e => {
    if (e.data) {
        var message = JSON.parse(e.data);
        if (message.type === "message" 
        && message.group === "Group1"){
            // Only print messages from Group1
            console.log(message.data);
        }
    }
};

client1.onopen = e => {
    client1.send(JSON.stringify({
        type: "joinGroup",
        group: "Group1"
    }));
};
```

客户端 2：

```js
var client2 = new WebSocket("wss://xxx.webpubsub.azure.com/client/hubs/hub1", "json.webpubsub.azure.v1");
client2.onopen = e => {
    client2.send(JSON.stringify({
        type: "sendToGroup",
        group: "Group1",
        data: "Hello Client1"
    });
};
```

如上例所示，`client2` 通过向 `client1` 所在的 `Group1` 发布消息，来直接向 `client1` 发送数据。

<a name="client_message_limit"></a>

### <a name="client-message-limit"></a>客户端消息限制
WebSocket 每一帧允许的最大消息大小为 1MB。

<a name="client_auth"></a>

### <a name="client-auth"></a>客户端身份验证

#### <a name="auth-workflow"></a>身份验证工作流

客户端使用签名的 JWT 令牌连接到服务。 上游还可以拒绝作为传入客户端的 `connect` 事件处理程序的客户端。 事件处理程序通过指定客户端在 Webhook 响应中使用的 `userId` 和 `role` 来对客户端进行身份验证，或者拒绝包含 401 错误的客户端。 详情请参阅[事件处理程序](#event_handler)部分。

工作流如下图所示：

![显示客户端身份验证工作流的关系图。](./media/concept-service-internals/client-connect-workflow.png)

当我们介绍 PubSub WebSocket 客户端时，你可能已注意到，客户端仅在获得授权时，才能向其他客户端发布消息。 客户端的 `role` 可确定客户端拥有的初始权限：

| 角色 | 权限 |
|---|---|
| 未指定 | 客户端可以发送事件。
| `webpubsub.joinLeaveGroup` | 客户端可以加入/退出任何组。
| `webpubsub.sendToGroup` | 客户端可以向任何组发布消息。
| `webpubsub.joinLeaveGroup.<group>` | 客户端可以加入/退出 `<group>` 组。
| `webpubsub.sendToGroup.<group>` | 客户端可以向 `<group>` 组发布消息。

服务器端也可以通过[服务器协议](#connection_manager)动态授予或撤消客户端的权限，如后面部分中所示。

<a name="server_protocol"></a>

## <a name="server-protocol"></a>服务器协议

服务器协议为服务器提供管理客户端连接和组的功能。

服务器协议通常包含两种角色：
1. [事件处理程序](#event_handler)
2. [“ODBC 目标编辑器”](#connection_manager)

<a name="event_handler"></a>

### <a name="event-handler"></a>事件处理程序
事件处理程序可处理传入的客户端事件。 通过门户或 Azure CLI 在服务中预先注册和配置事件处理程序，以便在触发客户端事件时，服务可以确定是否要处理事件。 对于公共预览版，我们使用 `PUSH` 模式来调用事件处理程序：事件处理程序是服务器端，会公开在触发事件时要调用的服务的公共可访问终结点。 其可用作 Webhook。 

服务使用 [CloudEvents HTTP 协议](https://github.com/cloudevents/spec/blob/v1.0.1/http-protocol-binding.md)将客户端事件传送到上游 Webhook。

它将为每个事件构建一个 HTTP POST 请求并发送到注册的上游，并需要 HTTP 响应。 

从服务发送到服务器的数据一律采用 CloudEvents `binary` 格式。

![显示 Web PubSub 服务事件推送模式的关系图。](./media/concept-service-internals/event-push.png)

#### <a name="upstream-and-validation"></a>上游和验证

需要通过门户或 Azure CLI 在服务中预先注册和配置事件处理程序，以便在触发客户端事件时，服务可以确定是否要处理事件。 对于公共预览版，我们使用 `PUSH` 模式来调用事件处理程序：事件处理程序是服务器端，会公开在触发事件时要调用的服务的公共可访问终结点。 其可用作 Webhook 上游 。 

配置 Webhook 终结点时，URL 可以使用 `{event}` 参数定义 URL 模板。 当客户端请求进入时，服务会动态计算 Webhook URL 的值。 例如，当请求 `/client/hubs/chat` 传入时，为中心 `http://host.com/api/{event}` 配置事件处理程序 URL 模式 `chat`；当连接客户端时，则率先发送到以下 URL：`http://host.com/api/connect`。 这可能适用于 PubSub WebSocket 客户端发送自定义事件的情况，其中事件处理程序可帮助将不同的事件调度到不同的上游。 请注意，URL 域名中不允许使用 `{event}` 参数。

通过 Azure 门户或 CLI 设置事件处理程序上游时，服务遵循 [CloudEvents 滥用保护措施](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection)来验证上游 Webhook。 `WebHook-Request-Origin` 请求头设置为服务域名 `xxx.webpubsub.azure.com`，并且其要求含标头 `WebHook-Allowed-Origin` 的响应包含此域名。

执行验证时，`{event}` 参数将解析为 `validate`。 例如，当尝试将 URL 设置为 `http://host.com/api/{event}` 时，服务会尝试向 `http://host.com/api/validate` 请求执行“OPTIONS”操作，并且仅当响应有效时才可成功设置配置。

目前，我们不支持 [WebHook-Request-Rate](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#414-webhook-request-rate) 和 [WebHook-Request-Callback](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#413-webhook-request-callback)。

#### <a name="authentication-between-service-and-webhook"></a>服务和 Webhook 之间的身份验证
- 匿名模式
- 通过配置的 Webhook URL 提供 `code` 的简单身份验证。
- AAD 身份验证。 
   - 在 AAD 的 [应用注册] 中添加客户端密码，并通过门户/CLI 向 Azure Web PubSub 提供 [客户端密码]。
   - 通过门户/CLI 向 Azure Web PubSub 提供[身份标识](/azure/app-service/overview-managed-identity?tabs=dotnet)

<a name="connection_manager"></a>

### <a name="connection-manager"></a>“ODBC 源编辑器”

服务器本质上就是获授权用户。 借助事件处理程序角色，服务器可知晓客户端的元数据（例如 `connectionId` 和 `userId`），以便其：
   - 关闭客户端连接
   - 向客户端发送消息
   - 将消息发送到属于同一用户的客户端
   - 将客户端添加到组
   - 将通过身份验证的客户端作为同一用户添加到组
   - 从组中删除客户端
   - 从组中删除作为同一用户通过身份验证的客户端
   - 将消息发布到组

其还可以授予或撤消对 PubSub 客户端的发布/订阅权限：
   - 向某些特定组或所有组授予加入/发布权限
   - 撤消某些特定组或所有组的加入/发布权限
   - 检查客户端是否有权加入/发布到某些特定组或所有组
   
对于公共预览版，服务为服务器提供 REST API 以便管理连接：

![显示 Web PubSub service 连接管理器工作流的关系图。](./media/concept-service-internals/manager-rest.png)

[此处][rest]定义了详细的 REST API 协议。

### <a name="summary"></a>“摘要”
你可能已经注意到，事件处理程序角色处理从服务到服务器的通信，而管理员角色处理从服务器到服务的通信。 因此，同时使用这两个角色使用 HTTP 协议时，服务与服务器之间的数据流将如下所示：

![显示 Web PubSub 服务双向工作流的关系图。](./media/concept-service-internals/http-service-server.png)

[rest]: /rest/api/webpubsub/

## <a name="next-steps"></a>后续步骤

[!INCLUDE [next step](includes/include-next-step.md)]
