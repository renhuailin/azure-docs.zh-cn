---
title: 如何开始与 Azure Web PubSub 建立 WebSocket 连接
description: 了解如何在不同的语言中开始与 Azure Web PubSub 服务建立 WebSocket 连接。
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: how-to
ms.date: 08/26/2021
ms.openlocfilehash: 02e7afa9b8c454b3520db933d76149973b50c699
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129275157"
---
#  <a name="start-a-websocket-connection-to-azure-web-pubsub"></a>开始与 Azure Web PubSub 建立 WebSocket 连接

客户端使用标准 [WebSocket](https://tools.ietf.org/html/rfc6455) 协议连接到 Azure Web PubSub 服务。 可以使用 WebSocket 客户端支持的语言为服务编写客户端。 在本文中，你将看到几个不同语言的 WebSocket 客户端示例。

## <a name="authorization"></a>授权

Web PubSub 使用 [JSON Web 令牌 (JWT)](https://tools.ietf.org/html/rfc7519.html) 来验证和授权客户端。 客户端可将该令牌放在 `access_token` 查询参数中，或者在连接到服务时将该令牌放在 `Authorization` 头中。

通常，客户端先与其应用服务器通信，以获取服务和令牌的 URL。 然后，客户端使用收到的 URL 和令牌打开与服务的 WebSocket 连接。

该门户还提供了一个工具，用于以动态方式生成带令牌的客户端 URL。 此工具可用于快速测试。

:::image type="content" source="./media/howto-websocket-connect/generate-client-url.png" alt-text="显示在何处找到客户端 URL 生成器的屏幕截图。":::

> [!NOTE]
> 生成令牌时，请确保仅包括必要的角色。
>

为了简化示例工作流，在以下各部分，我们将使用门户中临时生成的此 URL 供客户端连接。 `<Client_URL_From_Portal>` 用于表示值。 生成的令牌默认会在 60 分钟后过期，因此请记得在令牌过期后重新生成令牌。

该服务支持两种类型的 WebSocket 客户端：一种是简单 WebSocket 客户端，另一种是 PubSub WebSocket 客户端。 下面介绍这两种客户端如何连接到该服务。 有关这些客户端的详细信息，请参阅[适用于 Azure Web PubSub 的 WebSocket 客户端协议](./concept-client-protocols.md)。

## <a name="dependency"></a>依赖项

# <a name="in-browser"></a>[在浏览器中](#tab/browser)
大多数新式浏览器原生就能支持 `WebSocket` API。 

# <a name="nodejs"></a>[Node.js](#tab/javascript)

* [Node.js 12.x 或更高版本](https://nodejs.org)
* `npm install ws`

# <a name="python"></a>[Python](#tab/python)
* [Python](https://www.python.org/)
* `pip install websockets`

# <a name="c"></a>[C#](#tab/csharp)

* [.NET Core 2.1 或更高版本](https://dotnet.microsoft.com/download)
* `dotnet add package Websocket.Client`
    * [Websocket.Client](https://github.com/Marfusios/websocket-client) 是提供内置重新连接和错误处理的第三方 WebSocket 客户端。

# <a name="java"></a>[Java](#tab/java)
- [Java 开发工具包 (JDK)](/java/azure/jdk/) 版本 8 或更高版本。
- [Apache Maven](https://maven.apache.org/download.cgi)。

---

## <a name="simple-websocket-client"></a>简单 WebSocket 客户端

# <a name="in-browser"></a>[在浏览器中](#tab/browser)

在 HTML 页面的 `script` 块中：
```html
<script>
    // Don't forget to replace this <Client_URL_From_Portal> with the value fetched from the portal
    let ws = new WebSocket("<Client_URL_From_Portal>");
    ws.onopen = () => {
        // Do things when the WebSocket connection is established
    };

    ws.onmessage = event => {
        // Do things when messages are received.
    };
</script>
```

# <a name="nodejs"></a>[Node.js](#tab/javascript)

```js
const WebSocket = require('ws');
// Don't forget to replace this <Client_URL_From_Portal> with the value fetched from the portal
const client = new WebSocket("<Client_URL_From_Portal>");
client.on('open', () => {
     // Do things when the WebSocket connection is established
});
client.on('message', msg => {
     // Do things when messages are received.
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import asyncio
import websockets

async def hello():
    # Don't forget to replace this <Client_URL_From_Portal> with the value fetched from the portal
    uri = '<Client_URL_From_Portal>'
    async with websockets.connect(uri) as ws:
        while True:
            await ws.send('hello')
            greeting = await ws.recv()
            print(greeting)

asyncio.get_event_loop().run_until_complete(hello())
```

# <a name="c"></a>[C#](#tab/csharp)

```csharp
using System;
using System.IO;
using System.Text;
using System.Threading.Tasks;
using Websocket.Client;

namespace subscriber
{
    class Program
    {
        static async Task Main(string[] args)
        {
            // Don't forget to replace this <Client_URL_From_Portal> with the value fetched from the portal
            using (var client = new WebsocketClient(new Uri("<Client_URL_From_Portal>")))
            {
                // Disable the auto disconnect and reconnect because the sample would like the client to stay online even if no data comes in
                client.ReconnectTimeout = null;
                client.MessageReceived.Subscribe(msg => Console.WriteLine($"Message received: {msg}"));
                await client.Start();
                Console.WriteLine("Connected.");
                Console.Read();
            }
        }
    }
}
```

# <a name="java"></a>[Java](#tab/java)

```java
package client;

import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.WebSocket;
import java.util.concurrent.CompletionStage;

/**
 * A simple WebSocket Client.
 *
 */
public final class SimpleClient {
    private SimpleClient() {
    }

    /**
     * Starts a simple WebSocket connection.
     * @param args The arguments of the program.
     */
    public static void main(String[] args) throws Exception {
        // Don't forget to replace this <Client_URL_From_Portal> with the value fetched from the portal
        WebSocket ws = HttpClient.newHttpClient().newWebSocketBuilder()
                .buildAsync(URI.create("<Client_URL_From_Portal>"), new WebSocketClient()).join();
        System.in.read();
    }

    private static final class WebSocketClient implements WebSocket.Listener {
        private WebSocketClient() {
        }

        @Override
        public void onOpen(WebSocket webSocket) {
            System.out.println("onOpen using subprotocol " + webSocket.getSubprotocol());
            WebSocket.Listener.super.onOpen(webSocket);
        }

        @Override
        public CompletionStage<?> onText(WebSocket webSocket, CharSequence data, boolean last) {
            System.out.println("onText received " + data);
            return WebSocket.Listener.super.onText(webSocket, data, last);
        }

        @Override
        public void onError(WebSocket webSocket, Throwable error) {
            System.out.println("Bad day! " + webSocket.toString());
            WebSocket.Listener.super.onError(webSocket, error);
        }
    }
}

```


---

## <a name="pubsub-websocket-client"></a>PubSub WebSocket 客户端

# <a name="in-browser"></a>[在浏览器中](#tab/browser)

在 HTML 页面的 `script` 块中：
```html
<script>
    // Don't forget to replace this <Client_URL_From_Portal> with the value fetched from the portal
    let ws = new WebSocket("<Client_URL_From_Portal>", 'json.webpubsub.azure.v1');
    ws.onopen = () => {
        // Do things when the WebSocket connection is established
    };

    ws.onmessage = event => {
        // Do things when messages are received.
    };
</script>
```

# <a name="nodejs"></a>[Node.js](#tab/javascript)

```js
const WebSocket = require('ws');
// Don't forget to replace this <Client_URL_From_Portal> with the value fetched from the portal
const client = new WebSocket("<Client_URL_From_Portal>", "json.webpubsub.azure.v1");
client.on('open', () => {
     // Do things when the WebSocket connection is established
});
client.on('message', msg => {
     // Do things when messages are received.
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import asyncio
import websockets

async def join_group():
    # Don't forget to replace this <Client_URL_From_Portal> with the value fetched from the portal
    uri = '<Client_URL_From_Portal>'
    async with websockets.connect(uri, subprotocols=['json.webpubsub.azure.v1']) as ws:
        await ws.send('{"type":"joinGroup","ackId":1,"group":"group1"}')
        return await ws.recv()

print(asyncio.get_event_loop().run_until_complete(join_group()))
```

# <a name="c"></a>[C#](#tab/csharp)

```csharp
using System;
using System.IO;
using System.Net.WebSockets;
using System.Text;
using System.Threading.Tasks;
namespace subscriber
{
    class Program
    {
        static async Task Main(string[] args)
        {
            // Don't forget to replace this <Client_URL_From_Portal> with the value fetched from the portal
            using (var client = new WebsocketClient(new Uri("<Client_URL_From_Portal>"), () =>
            {
                var inner = new ClientWebSocket();
                inner.Options.AddSubProtocol("json.webpubsub.azure.v1");
                return inner;
            }))
            {
                // Disable the auto disconnect and reconnect because the sample would like the client to stay online even if no data comes in
                client.ReconnectTimeout = null;
                client.MessageReceived.Subscribe(msg => Console.WriteLine($"Message received: {msg}"));
                await client.Start();
                Console.WriteLine("Connected.");
                Console.Read();
            }
        }
    }
}
```

# <a name="java"></a>[Java](#tab/java)

```java
package client;

import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.WebSocket;
import java.util.concurrent.CompletionStage;

/**
 * A PubSub WebSocket Client.
 *
 */
public final class SubprotocolClient {
    private SubprotocolClient() {
    }

    /**
     * Starts a PubSub WebSocket connection.
     * @param args The arguments of the program.
     */
    public static void main(String[] args) throws Exception {
        // Don't forget to replace this <Client_URL_From_Portal> with the value fetched from the portal
        WebSocket ws = HttpClient.newHttpClient().newWebSocketBuilder().subprotocols("json.webpubsub.azure.v1")
                .buildAsync(URI.create("<Client_URL_From_Portal>"), new WebSocketClient()).join();

        ws.sendText("{\"type\":\"joinGroup\",\"ackId\":1,\"group\":\"group1\"}", true);
        System.in.read();
    }

    private static final class WebSocketClient implements WebSocket.Listener {
        private WebSocketClient() {
        }

        @Override
        public void onOpen(WebSocket webSocket) {
            System.out.println("onOpen using subprotocol " + webSocket.getSubprotocol());
            WebSocket.Listener.super.onOpen(webSocket);
        }

        @Override
        public CompletionStage<?> onText(WebSocket webSocket, CharSequence data, boolean last) {
            System.out.println("onText received " + data);
            return WebSocket.Listener.super.onText(webSocket, data, last);
        }

        @Override
        public void onError(WebSocket webSocket, Throwable error) {
            System.out.println("Bad day! " + webSocket.toString());
            WebSocket.Listener.super.onError(webSocket, error);
        }
    }
}
```

---

## <a name="next-steps"></a>后续步骤

本文介绍了如何使用门户中生成的 URL 连接到服务。 若要了解客户端如何与应用服务器通信以获取实际应用程序中的 URL，请参阅以下教程并查看示例。

> [!div class="nextstepaction"]
> [教程：使用 Azure Web PubSub 创建聊天室](./tutorial-build-chat.md)

> [!div class="nextstepaction"]
> [教程：使用服务支持的子协议进行客户端流式传输](./tutorial-subprotocol.md)

> [!div class="nextstepaction"]
> [了解更多 Azure Web PubSub 示例](https://aka.ms/awps/samples)

