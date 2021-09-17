---
title: Azure Web PubSub 的 WebSocket 客户端协议
description: Azure Web PubSub 的客户端协议概述
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 08/16/2021
ms.openlocfilehash: e08f595e2dc80abe06fa68d1a3e30ac68ff0097c
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123426339"
---
#  <a name="websocket-client-protocols-for-azure-web-pubsub"></a>Azure Web PubSub 的 WebSocket 客户端协议

客户端通过标准 [WebSocket](https://datatracker.ietf.org/doc/html/rfc6455) 协议连接到 Azure Web PubSub。

## <a name="service-endpoint"></a>服务终结点
该服务提供两种类型的终结点供客户端与之连接：
1. `/client/hubs/{hub}`
2. `/client/?hub={hub}`

`{hub}` 是必需参数，充当不同应用程序的隔离项。 可以在路径或查询中设置此参数。

## <a name="auth"></a>Auth
1. 客户端通过 JWT 令牌连接服务

### <a name="1-the-client-connects-using-the-jwt-token"></a>1. 客户端使用 JWT 令牌进行连接

JWT 令牌可用于查询字符串 `/client/?hub={hub}&access_token={token}` 或 `Authorization` 标头：`Authorization: Bearer {token}`。

一般工作流如下：
1. 客户端与应用程序服务器协商。 应用程序服务器使用身份验证中间件处理客户端请求，并对 JWT 令牌签名，以使客户端连接到服务。
2. 应用程序服务器将 JWT 令牌和服务 URL 返回到客户端
3. 客户端尝试使用应用程序服务器返回的 URL 和 JWT 令牌连接到 Web PubSub 服务

<a name="simple_client"></a>

## <a name="the-simple-websocket-client"></a>简单的 WebSocket 客户端
顾名思义，简单的 WebSocket 客户端就是一个简单的 WebSocket 连接。 此客户端还有自定义子协议。 

例如，在 JS 中，可以使用以下内容创建简单的 WebSocket 客户端：
```js
// simple WebSocket client1
var client1 = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1');

// simple WebSocket client2 with some custom subprotocol
var client2 = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'custom.subprotocol')

```

## <a name="the-pubsub-websocket-client"></a>PubSub WebSocket 客户端

PubSub WebSocket 客户端支持两个子协议 `json.webpubsub.azure.v1` 和 `protobuf.webpubsub.azure.v1`

#### <a name="json-subprotocol"></a>Json 子协议

例如，在 JS 中，可以通过以下代码创建使用 json 子协议的 PubSub WebSocket 客户端：
```js
// PubSub WebSocket client
var pubsub = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'json.webpubsub.azure.v1');
```

#### <a name="protobuf-subprotocol"></a>Protobuf 子协议

Protobuf 是一种语言中立、平台中立的基于二进制的协议，它非常适合用于发送二进制数据。 Protobuf 提供的工具可为 Java、Python、Objective-C、C# 和 C++ 等多种语言生成客户端。 [详细了解 protobuf](https://developers.google.com/protocol-buffers)。

例如，在 JS 中，可以通过以下代码创建使用 protobuf 子协议的 PubSub WebSocket 客户端：
```js
// PubSub WebSocket client
var pubsub = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'protobuf.webpubsub.azure.v1');
```

当客户端使用此子协议时，传出数据帧和传入数据帧都应为 JSON 有效负载。 经过身份验证的客户端可直接通过 Azure Web PubSub 服务向其他客户端发布消息。

### <a name="permissions"></a>权限

当我们介绍 PubSub WebSocket 客户端时，你可能已注意到，客户端仅在获得授权时，才能向其他客户端发布消息。 当客户端已建立连接或在连接的生存期内时，才能对其授予权限。

| 角色 | 权限 |
|---|---|
| 未指定 | 客户端可以发送事件请求。
| `webpubsub.joinLeaveGroup` | 客户端可以加入/退出任何组。
| `webpubsub.sendToGroup` | 客户端可以向任何组发布消息。
| `webpubsub.joinLeaveGroup.<group>` | 客户端可以加入/退出 `<group>` 组。
| `webpubsub.sendToGroup.<group>` | 客户端可以向 `<group>` 组发布消息。

* 有关 Json 子协议的详细信息，请参阅 [JSON 子协议](./reference-json-webpubsub-subprotocol.md)。
* 有关 Protobuf 子协议的详细信息，请参阅 [Protobuf 子协议](./reference-protobuf-webpubsub-subprotocol.md)。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [next step](includes/include-next-step.md)]
