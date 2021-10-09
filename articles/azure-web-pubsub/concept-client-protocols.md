---
title: Azure Web PubSub 的 WebSocket 客户端协议
description: 本文概述了 Azure Web PubSub 的客户端协议。
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 08/16/2021
ms.openlocfilehash: 8f78dbcdecc552e94c3d871f610ef227a1795f8e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128576253"
---
#  <a name="websocket-client-protocols-for-azure-web-pubsub"></a>Azure Web PubSub 的 WebSocket 客户端协议

客户端通过标准 [WebSocket](https://datatracker.ietf.org/doc/html/rfc6455) 协议连接到 Azure Web PubSub。

## <a name="service-endpoints"></a>服务终结点
Web PubSub 服务提供两种类型的终结点供客户端与之连接：
* `/client/hubs/{hub}`
* `/client/?hub={hub}`

`{hub}` 是必需参数，充当各种应用程序的隔离项。 可以在路径或查询中设置它。

## <a name="authorization"></a>授权

客户端使用 JSON Web 令牌 (JWT) 连接到服务。 可以将令牌以 `/client/?hub={hub}&access_token={token}` 形式置于查询字符串中，或以 `Authorization: Bearer {token}` 形式置于 `Authorization` 标头中。

下面是常规授权工作流：

1. 客户端与应用程序服务器协商。 应用程序服务器包含的授权中间件可处理客户端请求，并对 JWT 进行签名，以使客户端连接到服务。
1. 应用程序服务器将 JWT 和服务 URL 返回到客户端。
1. 客户端尝试使用从应用程序服务器返回的 URL 和 JWT 连接到 Web PubSub 服务。

<a name="simple_client"></a>

## <a name="the-simple-websocket-client"></a>简单的 WebSocket 客户端

顾名思义，简单的 WebSocket 客户端就是一个简单的 WebSocket 连接。 它还可以有自己的自定义子协议。 

例如，在 JavaScript 中，可以使用以下代码创建简单的 WebSocket 客户端：

```js
// simple WebSocket client1
var client1 = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1');

// simple WebSocket client2 with some custom subprotocol
var client2 = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'custom.subprotocol')
```

## <a name="the-pubsub-websocket-client"></a>PubSub WebSocket 客户端

PubSub WebSocket 客户端支持两个子协议：
* `json.webpubsub.azure.v1`
* `protobuf.webpubsub.azure.v1`

#### <a name="the-json-subprotocol"></a>JSON 子协议

例如，在 JavaScript 中，可以通过以下代码创建使用 JSON 子协议的 PubSub WebSocket 客户端：

```js
// PubSub WebSocket client
var pubsub = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'json.webpubsub.azure.v1');
```

#### <a name="the-protobuf-subprotocol"></a>protobuf 子协议

协议缓冲区 (protobuf) 是一种语言中性、平台中性的基于二进制的协议，可简化二进制数据的发送。 Protobuf 提供的工具可为 Java、Python、Objective-C、C# 和 C++ 等多种语言生成客户端。 [详细了解 protobuf](https://developers.google.com/protocol-buffers)。

例如，在 JavaScript 中，可以通过以下代码创建使用 protobuf 子协议的 PubSub WebSocket 客户端：

```js
// PubSub WebSocket client
var pubsub = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'protobuf.webpubsub.azure.v1');
```

当客户端使用子协议时，传出和传入数据帧都应为 JSON 有效负载。 经授权的客户端可直接通过 Azure Web PubSub 服务向其他客户端发布消息。

### <a name="permissions"></a>权限

你可能已在前面的 PubSub WebSocket 客户端介绍中注意到，一个客户端只有在获得授权时才能发布到其他客户端。 当客户端建立连接或在连接的生存期内时，可以对其授予权限。

| 角色 | 权限 |
|---|---|
| 未指定 | 客户端可以发送事件请求。 |
| `webpubsub.joinLeaveGroup` | 客户端可以加入或退出任何组。 |
| `webpubsub.sendToGroup` | 客户端可以向任何组发布消息。 |
| `webpubsub.joinLeaveGroup.<group>` | 客户端可以加入或退出 `<group>` 组。 |
| `webpubsub.sendToGroup.<group>` | 客户端可以向 `<group>` 组发布消息。 |
| | |

有关 JSON 子协议的详细信息，请参阅 [JSON 子协议](./reference-json-webpubsub-subprotocol.md)。

有关 protobuf 子协议的详细信息，请参阅 [Protobuf 子协议](./reference-protobuf-webpubsub-subprotocol.md)。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [next step](includes/include-next-step.md)]
