---
title: 教程 - 使用 Azure Web PubSub 服务中的子协议在 WebSocket 客户端之间发布和订阅消息
description: 本教程介绍如何使用 Azure Web PubSub 服务及其支持的 WebSocket 子协议在客户端之间进行同步。
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: tutorial
ms.date: 08/16/2021
ms.openlocfilehash: 232489ea06020d5f2f06cfc7d841c888b2b5a5d0
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122773193"
---
# <a name="tutorial-publish-and-subscribe-messages-between-websocket-clients-using-subprotocol"></a>教程：使用子协议在 WebSocket 客户端之间发布和订阅消息

在[构建聊天应用教程](./tutorial-build-chat.md)中，你已了解如何使用 WebSocket API 通过 Azure Web PubSub 发送和接收数据。 客户端与服务进行通信时不需要协议。 例如，你可以使用 `WebSocket.send()` 发送任何数据，服务器将按原样接收数据。 这很容易使用，但功能也很有限。 例如，你不能在向服务器发送事件时指定事件名称，也不能将消息发布到其他客户端，而只能将其发送到服务器。 在本教程中，你将了解如何使用子协议来扩展客户端的功能。

在本教程中，你将了解：

> [!div class="checklist"]
> * 创建 Azure Web PubSub 服务实例
> * 生成完整 URL 以建立 WebSocket 连接
> * 使用子协议在 WebSocket 客户端之间发布消息

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 本设置需要 Azure CLI 版本 2.22.0 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="create-an-azure-web-pubsub-instance"></a>创建 Azure Web PubSub 实例

### <a name="create-a-resource-group"></a>创建资源组

[!INCLUDE [Create a resource group](includes/cli-rg-creation.md)]

### <a name="create-a-web-pubsub-instance"></a>创建 Web PubSub 实例

[!INCLUDE [Create a Web PubSub instance](includes/cli-awps-creation.md)]

### <a name="get-the-connectionstring-for-future-use"></a>获取 ConnectionString 以供将来使用

[!INCLUDE [Get the connection string](includes/cli-awps-connstr.md)]

复制已提取的 ConnectionString，本教程稍后会将其用作 `<connection_string>` 的值。

## <a name="set-up-the-project"></a>设置项目

### <a name="prerequisites"></a>先决条件

# <a name="c"></a>[C#](#tab/csharp)

* [ASP.NET Core 3.1 或更高版本](/aspnet/core)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

* [Node.js 12.x 或更高版本](https://nodejs.org)

# <a name="python"></a>[Python](#tab/python)
* [Python](https://www.python.org/)

---

## <a name="using-a-subprotocol"></a>使用子协议

客户端可以使用特定的[子协议](https://datatracker.ietf.org/doc/html/rfc6455#section-1.9)启动 WebSocket 连接。 Azure Web PubSub 服务支持名为 `json.webpubsub.azure.v1` 的子协议，使客户端能够直接进行发布/订阅，而不是往返于上游服务器。 查看 [Azure Web PubSub 支持的 JSON WebSocket 子协议](./reference-json-webpubsub-subprotocol.md)，了解该子协议的详细信息。

> 如果使用其他协议名称，这些名称将被服务忽略，并在连接事件处理程序中传递到服务器，因此你可以构建自己的协议。

使用 `json.webpubsub.azure.v1` 子协议创建 Web 应用程序。

1.  安装依赖项

    # <a name="c"></a>[C#](#tab/csharp)
    ```bash
    mkdir logstream
    cd logstream
    dotnet new web
    dotnet add package Microsoft.Extensions.Azure
    dotnet add package Azure.Messaging.WebPubSub --prerelease
    ```
    
    # <a name="javascript"></a>[JavaScript](#tab/javascript)
    
    ```bash
    mkdir logstream
    cd logstream
    npm init -y
    npm install --save express
    npm install --save ws
    npm install --save node-fetch
    npm install --save @azure/web-pubsub
    ```

    # <a name="python"></a>[Python](#tab/python)
    
    ```bash
    mkdir logstream
    cd logstream

    # Create venv
    python -m venv env

    # Active venv
    ./env/Scripts/activate

    # Or call .\env\Scripts\activate when you are using CMD under Windows

    pip install azure-messaging-webpubsubservice
    ```
    
    ---
    
2.  创建服务器端来托管 `/negotiate` API 和 Web 页面。

    # <a name="c"></a>[C#](#tab/csharp)

    使用以下代码更新 `Startup.cs`。
    - 更新 `ConfigureServices` 方法以添加服务客户端，并从配置中读取连接字符串。 
    - 更新 `Configure` 方法，在 `app.UseRouting();` 之前添加 `app.UseStaticFiles();`，以支持静态文件。 
    - 并使用 `/negotiate` 请求更新 `app.UseEndpoints`，以生成客户端访问令牌。

    ```csharp
    using Azure.Messaging.WebPubSub;

    using Microsoft.AspNetCore.Builder;
    using Microsoft.AspNetCore.Hosting;
    using Microsoft.AspNetCore.Http;
    using Microsoft.Extensions.Azure;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Hosting;
    
    namespace logstream
    {
        public class Startup
        {
            public Startup(IConfiguration configuration)
            {
                Configuration = configuration;
            }
    
            public IConfiguration Configuration { get; }
    
            public void ConfigureServices(IServiceCollection services)
            {
                services.AddAzureClients(builder =>
                {
                    builder.AddWebPubSubServiceClient(Configuration["Azure:WebPubSub:ConnectionString"], "stream");
                });
            }
    
            public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
            {
                if (env.IsDevelopment())
                {
                    app.UseDeveloperExceptionPage();
                }
    
                app.UseStaticFiles();
    
                app.UseRouting();
    
                app.UseEndpoints(endpoints =>
                {
                    endpoints.MapGet("/negotiate", async context =>
                    {
                        var serviceClient = context.RequestServices.GetRequiredService<WebPubSubServiceClient>();
                        var response = new
                        {
                            url = serviceClient.GenerateClientAccessUri(roles: new string[] { "webpubsub.sendToGroup.stream", "webpubsub.joinLeaveGroup.stream" }).AbsoluteUri
                        };
                        await context.Response.WriteAsJsonAsync(response);
                    });
                });
            }
        }
    }
    
    ```
    
    # <a name="javascript"></a>[JavaScript](#tab/javascript)

    创建 `server.js` 并添加以下代码：

    ```javascript
    const express = require('express');
    const { WebPubSubServiceClient } = require('@azure/web-pubsub');

    let endpoint = new WebPubSubServiceClient(process.argv[2], 'stream');
    const app = express();

    app.get('/negotiate', async (req, res) => {
      let token = await endpoint.getAuthenticationToken({
        roles: ['webpubsub.sendToGroup.stream', 'webpubsub.joinLeaveGroup.stream']
      });
      res.send({
        url: token.url
      });
    });

    app.use(express.static('public'));
    app.listen(8080, () => console.log('server started'));
    ```

    # <a name="python"></a>[Python](#tab/python)
    
    创建 `server.py` 以托管 `/negotiate` API 和 Web 页面。

    ```python
    import json
    import sys
    from http.server import HTTPServer, SimpleHTTPRequestHandler
    from azure.messaging.webpubsubservice import (
        build_authentication_token
    )

    class Resquest(SimpleHTTPRequestHandler):
        def do_GET(self):
            if self.path == '/':
                self.path = 'public/index.html'
                return SimpleHTTPRequestHandler.do_GET(self)
            elif self.path == '/negotiate':
                token = build_authentication_token(sys.argv[1], 'stream', roles=['webpubsub.sendToGroup.stream', 'webpubsub.joinLeaveGroup.stream'])
                print(token)
                self.send_response(200)
                self.send_header('Content-Type', 'application/json')
                self.end_headers()
                self.wfile.write(json.dumps({
                    'url': token['url']
                }).encode())

    if __name__ == '__main__':
        if len(sys.argv) != 2:
            print('Usage: python server.py <connection-string>')
            exit(1)

        server = HTTPServer(('localhost', 8080), Resquest)
        print('server started')
        server.serve_forever()
    ```

    ---
    
3.  创建网页

    # <a name="c"></a>[C#](#tab/csharp)
    使用以下内容创建一个 HTML 页面，并将其另存为 `wwwroot/index.html`：
    
    # <a name="javascript"></a>[JavaScript](#tab/javascript)

    使用以下内容创建一个 HTML 页面，并将其另存为 `public/index.html`：

    # <a name="python"></a>[Python](#tab/python)

    使用以下内容创建一个 HTML 页面，并将其另存为 `public/index.html`：
    
    ---
    
    ```html
    <html>

    <body>
      <div id="output"></div>
      <script>
        (async function () {
          let res = await fetch('/negotiate')
          let data = await res.json();
          let ws = new WebSocket(data.url, 'json.webpubsub.azure.v1');
          ws.onopen = () => {
            console.log('connected');
          };

          let output = document.querySelector('#output');
          ws.onmessage = event => {
            let d = document.createElement('p');
            d.innerText = event.data;
            output.appendChild(d);
          };
        })();
      </script>
    </body>

    </html>
    ```

    它只连接到服务，并将收到的任何消息打印到页面上。 主要的变化是在创建 WebSocket 连接时指定了子协议。

4. 运行服务器
    # <a name="c"></a>[C#](#tab/csharp)
    使用适用于 .NET Core 的[机密管理器](/aspnet/core/security/app-secrets#secret-manager)工具来设置连接字符串。 运行以下命令，将 `<connection_string>` 替换为在[上一步](#get-the-connectionstring-for-future-use)中提取的连接字符串，并在浏览器中打开 http://localhost:5000/index.html ：

    ```bash
    dotnet user-secrets init
    dotnet user-secrets set Azure:WebPubSub:ConnectionString "<connection-string>"
    dotnet run
    ```
    
    # <a name="javascript"></a>[JavaScript](#tab/javascript)
    
    运行以下命令，将 `<connection-string>` 替换为在[上一步](#get-the-connectionstring-for-future-use)中提取的 ConnectionString，并在浏览器中打开 http://localhost:8080：

    ```bash
    
    node server "<connection-string>"
    ```
    
    # <a name="python"></a>[Python](#tab/python)

    运行以下命令，将 `<connection-string>` 替换为在[上一步](#get-the-connectionstring-for-future-use)中提取的 ConnectionString，并在浏览器中打开 http://localhost:8080：

    ```bash
    python server.py "<connection-string>"
    ```
    ---

    如果你使用的是 Chrome，可以按“F12”或单击右键 ->“检查” -> “开发人员工具”，并选择“网络”选项卡  。加载网页，你可以看到 WebSocket 连接已经建立。 单击以检查 WebSocket 连接，可以看到客户端中收到以下 `connected` 事件消息。 可以发现，你能够获得为该客户端生成的 `connectionId`。
    
    ```json
    {"type":"system","event":"connected","userId":null,"connectionId":"<the_connection_id>"}
    ```

可以发现，借助子协议，可在连接为 `connected` 时获取连接的某些元数据。

另请注意，客户端现在接收到的不是纯文本，而是包含更多信息的 JSON 消息（例如消息类型和来源）。 因此，你可以使用此信息对消息进行更多的处理（例如，如果消息来自不同的来源，则以不同的样式显示消息），可以在后面的部分中了解这些信息。

## <a name="publish-messages-from-client"></a>从客户端发布消息

在[构建聊天应用](./tutorial-build-chat.md)教程中，当客户端通过 WebSocket 连接发送消息时，它将在服务器端触发用户事件。 使用子协议，客户端将通过发送 JSON 消息获得更多的功能。 例如，你可以从客户端将消息直接发布到其他客户端。

如果要将大量数据实时流式传输到其他客户端，这很有用。 使用此功能来构建日志流式处理应用程序，该应用程序可实时将控制台日志流式传输到浏览器。

1. 创建流式处理程序
 
    # <a name="c"></a>[C#](#tab/csharp)
    创建 `stream` 程序：
    ```bash
    mkdir stream
    cd stream
    dotnet new console
    ```
    
    使用以下内容更新 `Program.cs`：
    ```csharp
    using System;
    using System.Net.Http;
    using System.Net.WebSockets;
    using System.Text;
    using System.Text.Json;
    using System.Threading.Tasks;
    
    namespace stream
    {
        class Program
        {
            private static readonly HttpClient http = new HttpClient();
            static async Task Main(string[] args)
            {
                // Get client url from remote
                var stream = await http.GetStreamAsync("http://localhost:5000/negotiate");
                var url = (await JsonSerializer.DeserializeAsync<ClientToken>(stream)).url;
                var client = new ClientWebSocket();
                client.Options.AddSubProtocol("json.webpubsub.azure.v1");
    
                await client.ConnectAsync(new Uri(url), default);
    
                Console.WriteLine("Connected.");
                var streaming = Console.ReadLine();
                while (streaming != null)
                {
                    if (!string.IsNullOrEmpty(streaming))
                    {
                        var message = JsonSerializer.Serialize(new
                        {
                            type = "sendToGroup",
                            group = "stream",
                            data = streaming + Environment.NewLine,
                        });
                        Console.WriteLine("Sending " + message);
                        await client.SendAsync(Encoding.UTF8.GetBytes(message), WebSocketMessageType.Text, true, default);
                    }
    
                    streaming = Console.ReadLine();
                }
    
                await client.CloseAsync(WebSocketCloseStatus.NormalClosure, null, default);
            }
    
            private sealed class ClientToken
            {
                public string url { get; set; }
            }
        }
    }
    
    ```

    # <a name="javascript"></a>[JavaScript](#tab/javascript)
    使用以下内容创建 `stream.js`。
    
    ```javascript
    const WebSocket = require('ws');
    const fetch = require('node-fetch');

    async function main() {
      let res = await fetch(`http://localhost:8080/negotiate`);
      let data = await res.json();
      let ws = new WebSocket(data.url, 'json.webpubsub.azure.v1');
      ws.on('open', () => {
        process.stdin.on('data', data => {
          ws.send(JSON.stringify({
            type: 'sendToGroup',
            group: 'stream',
            dataType: 'text',
            data: data.toString()
          }));
          process.stdout.write(data);
        });
      });
      process.stdin.on('close', () => ws.close());
    }

    main();
    ```

    以上代码创建与服务的 WebSocket 连接，然后每当它接收到某些数据时，就使用 `ws.send()` 发布数据。 若要发布到其他连接，只需要将 `type` 设置为 `sendToGroup`，并在消息中指定组名。

    # <a name="python"></a>[Python](#tab/python)

    打开 `stream` 程序的另一个 bash 窗口，并安装 `websockets` 依赖项：

    ```bash
    mkdir stream
    cd stream

    # Create venv
    python -m venv env

    # Active venv
    ./env/Scripts/activate

    # Or call .\env\Scripts\activate when you are using CMD under Windows
    pip install websockets
    ```

    使用以下内容创建 `stream.py`。

    ```python
    import asyncio
    import sys
    import threading
    import time
    import websockets
    import requests
    import json


    async def connect(url):
        async with websockets.connect(url, subprotocols=['json.webpubsub.azure.v1']) as ws:
            print('connected')
            id = 1
            while True:
                data = input()
                payload = {
                    'type': 'sendToGroup',
                    'group': 'stream',
                    'dataType': 'text',
                    'data': str(data + '\n'),
                    'ackId': id
                }
                id = id + 1
                await ws.send(json.dumps(payload))
                await ws.recv()

    res = requests.get('http://localhost:8080/negotiate').json()

    try:
        asyncio.get_event_loop().run_until_complete(connect(res['url']))
    except KeyboardInterrupt:
        pass

    ```

    以上代码创建与服务的 WebSocket 连接，然后每当它接收到某些数据时，就使用 `ws.send()` 发布数据。 若要发布到其他连接，只需要将 `type` 设置为 `sendToGroup`，并在消息中指定组名。
    
    ---
    
    可以看到此处有一个新的概念“组”。 组是中心中的逻辑概念，你可以在中心内将消息发布到一组连接。 在中心中，可以有多个组，一个客户端可以同时订阅多个组。 使用子协议时，只能发布到组，而不是广播到整个中心。 有关术语的详细信息，请查看[基本概念](./key-concepts.md)。

2.  由于我们在此处使用组，因此在 `ws.onopen` 回调内建立 WebSocket 连接时，还需要更新 `index.html` 网页以加入组。
    
    ```javascript
    ws.onopen = () => {
      console.log('connected');
      ws.send(JSON.stringify({
        type: 'joinGroup',
        group: 'stream'
      }));
    };
    ```

    可以发现，客户端通过发送 `joinGroup` 类型的消息来加入该组。

3.  此外，稍微更新 `ws.onmessage` 回调逻辑，以分析 JSON 响应并只打印来自 `stream` 组中的消息，使其充当实时流打印机。

    ```javascript
    ws.onmessage = event => {
      let message = JSON.parse(event.data);
      if (message.type === 'message' && message.group === 'stream') {
        let d = document.createElement('span');
        d.innerText = message.data;
        output.appendChild(d);
        window.scrollTo(0, document.body.scrollHeight);
      }
    };
    ```

4.  出于安全考虑，默认情况下，客户端不能自行发布或订阅组。 因此，你可能已注意到，我们在生成令牌时为客户端设置了 `roles`：

    # <a name="c"></a>[C#](#tab/csharp)
    在 `Startup.cs` 中执行 `GenerateClientAccessUri` 时，按如下所示设置 `roles`：
    ```csharp
    serviceClient.GenerateClientAccessUri(roles: new string[] { "webpubsub.sendToGroup.stream", "webpubsub.joinLeaveGroup.stream" })
    ```

    # <a name="javascript"></a>[JavaScript](#tab/javascript)

    在 `server.js` 中执行 `getAuthenticationToken` 时，按如下所示添加 `roles`：

    ```javascript
    app.get('/negotiate', async (req, res) => {
      let token = await endpoint.getAuthenticationToken({
        roles: ['webpubsub.sendToGroup.stream', 'webpubsub.joinLeaveGroup.stream']
      });
      ...
    });
    
    ```
    
    # <a name="python"></a>[Python](#tab/python)
    
    在 `server.py` 中执行 `build_authentication_token` 时，更新令牌生成代码，为客户端提供此类 `roles`：

    ```python
    token = build_authentication_token(sys.argv[1], 'stream', roles=['webpubsub.sendToGroup.stream', 'webpubsub.joinLeaveGroup.stream'])
    
    ```
    ---
    

5.  最后，还要对 `index.html` 应用一些样式，使它得到更好的显示。

    ```html
    <html>

      <head>
        <style>
          #output {
            white-space: pre;
            font-family: monospace;
          }
        </style>
      </head>
    ```

现在，运行以下代码，键入任意文本，这些文本将在浏览器中实时显示：

# <a name="c"></a>[C#](#tab/csharp)

```bash
ls -R | dotnet run

# Or call `dir /s /b | dotnet run` when you are using CMD under Windows

```

或者，你可以让它的速度放慢，以便可以看到数据实时流式传输到浏览器：

```bash
for i in $(ls -R); do echo $i; sleep 0.1; done | dotnet run
```

可以在[此处][code-csharp]找到本教程的完整代码示例。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`node stream`

或者，你也可以使用此应用将来自另一个控制台应用的任何输出通过管道流式传输到浏览器。 例如：

```bash
ls -R | node stream

# Or call `dir /s /b | node stream` when you are using CMD under Windows
```

或者，你可以让它的速度放慢，以便可以看到数据实时流式传输到浏览器：

```bash
for i in $(ls -R); do echo $i; sleep 0.1; done | node stream
```

可以在[此处][code-js]找到本教程的完整代码示例。

# <a name="python"></a>[Python](#tab/python)

现在，你可以运行 `python stream.py`，键入任意文本，这些文本将在浏览器中实时显示。

或者，你也可以使用此应用将来自另一个控制台应用的任何输出通过管道流式传输到浏览器。 例如：

```bash
ls -R | python stream.py

# Or call `dir /s /b | python stream.py` when you are using CMD under Windows
```

或者，你可以让它的速度放慢，以便可以看到数据实时流式传输到浏览器：

```bash
for i in $(ls -R); do echo $i; sleep 0.1; done | python stream.py
```

可以在[此处][code-python]找到本教程的完整代码示例。


---


## <a name="next-steps"></a>后续步骤

本教程提供如何连接到 Web PubSub 服务以及如何使用子协议将消息发布到连接的客户端的基本概念。

查看其他教程，进一步深入了解如何使用该服务。

> [!div class="nextstepaction"]
> [了解更多 Azure Web PubSub 示例](https://aka.ms/awps/samples)

[code-csharp]: https://github.com/Azure/azure-webpubsub/tree/main/samples/csharp/logstream/

[code-js]: https://github.com/Azure/azure-webpubsub/tree/main/samples/javascript/logstream/

[code-python]: https://github.com/Azure/azure-webpubsub/tree/main/samples/python/logstream/
