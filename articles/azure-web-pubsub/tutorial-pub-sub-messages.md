---
title: 教程 - 使用 WebSocket API 和 Azure Web PubSub 服务 SDK 发布和订阅消息
description: 演示如何使用 Azure Web PubSub 服务和 Azure Functions 生成无服务器应用程序的教程。
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: tutorial
ms.date: 08/16/2021
ms.openlocfilehash: b0027bfd1f214ecba347652ce37009103b76ff00
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123255430"
---
# <a name="tutorial-publish-and-subscribe-messages-using-websocket-api-and-azure-web-pubsub-service-sdk"></a>教程：使用 WebSocket API 和 Azure Web PubSub 服务 SDK 发布和订阅消息

Azure Web PubSub 服务可帮助你轻松地使用 Websocket 和发布-订阅模式生成实时消息传递 Web 应用程序。 本教程介绍如何使用 WebSocket API 订阅服务，以及如何使用服务 SDK 发布消息。

在本教程中，你将了解如何：

> [!div class="checklist"]
> * 创建 Web PubSub 服务实例
> * 生成完整的 URL，以建立 WebSocket 连接
> * 使用标准 WebSocket 协议订阅消息
> * 使用服务 SDK 发布消息

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

* [.NET Core 2.1 或更高版本](https://dotnet.microsoft.com/download)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

* [Node.js 12.x 或更高版本](https://nodejs.org)

# <a name="python"></a>[Python](#tab/python)
* [Python](https://www.python.org/)

# <a name="java"></a>[Java](#tab/java)
- [Java 开发工具包 (JDK)](/java/azure/jdk/) 8 或更高版本
- [Apache Maven](https://maven.apache.org/download.cgi)

---

## <a name="set-up-the-subscriber"></a>设置订阅者

客户端使用 [JSON Web 令牌 (JWT)](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims) 身份验证通过标准 WebSocket 协议连接到 Azure Web PubSub 服务。 服务 SDK 提供帮助程序方法来生成令牌。 在本教程中，订阅者直接根据 ConnectionString 生成令牌。 在真实的应用程序中，我们通常使用服务器端应用程序来处理身份验证/授权工作流。 请尝试学习[生成聊天应用](./tutorial-build-chat.md)教程，更好地了解该工作流。

# <a name="c"></a>[C#](#tab/csharp)

1. 首先，让我们为此项目创建一个新文件夹 `subscribe`，并安装所需的依赖项：
    * [Websocket.Client](https://github.com/Marfusios/websocket-client) 包是支持 WebSocket 连接的第三方包。 你可以使用任何支持 WebSocket 的 API/库来执行此操作。
    * SDK 包 `Azure.Messaging.WebPubSub` 可帮助生成 JWT 令牌。 

    ```bash
    mkdir subscriber
    cd subscriber
    dotnet new console
    dotnet add package Websocket.Client --version 4.3.30
    dotnet add package Azure.Messaging.WebPubSub --prerelease
    ```

2. 更新 `Program.cs` 文件以连接到服务：

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.Messaging.WebPubSub;
    using Websocket.Client;

    namespace subscriber
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                if (args.Length != 2)
                {
                    Console.WriteLine("Usage: subscriber <connectionString> <hub>");
                    return;
                }
                var connectionString = args[0];
                var hub = args[1];

                // Either generate the URL or fetch it from server or fetch a temp one from the portal
                var serviceClient = new WebPubSubServiceClient(connectionString, hub);
                var url = serviceClient.GenerateClientAccessUri();

                using (var client = new WebsocketClient(url))
                {
                    // Disable the auto disconnect and reconnect because the sample would like the client to stay online even no data comes in
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

    上面的代码将创建一个 WebSocket 连接，连接到 Azure Web PubSub 中的中心。 中心是 Azure Web PubSub 中的逻辑单元，你可以在其中向一组客户端发布消息。 [关键概念](./key-concepts.md)包含有关 Azure Web PubSub 中使用的术语的详细说明。
    
    Azure Web PubSub 服务使用 [JSON Web 令牌 (JWT)](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims) 身份验证，因此在代码示例中，我们使用 Web PubSub SDK 中的 `WebPubSubServiceClient.GenerateClientAccessUri()` 来生成包含完整 URL 和有效访问令牌的服务的 URL。
    
    建立连接后，你将通过 WebSocket 连接接收消息。 因此，我们使用 `client.MessageReceived.Subscribe(msg => ...));` 来侦听传入的消息。

3. 运行以下命令，将 `<connection_string>` 替换为[前面步骤](#get-the-connectionstring-for-future-use)中提取的 ConnectionString：

    ```bash
    dotnet run "<connection_string>" "myHub1"
    ```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

1. 首先，让我们为此项目创建一个新文件夹 `subscriber`，并安装所需的依赖项：

    ```bash
    mkdir subscriber
    cd subscriber
    npm init -y
    npm install --save ws
    npm install --save @azure/web-pubsub

    ```
2. 然后使用 WebSocket API 连接到服务。 使用以下代码创建一个 `subscribe.js` 文件：

    ```javascript
    const WebSocket = require('ws');
    const { WebPubSubServiceClient } = require('@azure/web-pubsub');

    async function main() {
      if (process.argv.length !== 4) {
        console.log('Usage: node subscribe <connection-string> <hub-name>');
        return 1;
      }

      let serviceClient = new WebPubSubServiceClient(process.argv[2], process.argv[3]);
      let token = await serviceClient.getAuthenticationToken();
      let ws = new WebSocket(token.url);
      ws.on('open', () => console.log('connected'));
      ws.on('message', data => console.log('Message received: %s', data));
    }

    main();
    ```
    
    上面的代码将创建一个 WebSocket 连接，连接到 Azure Web PubSub 中的中心。 中心是 Azure Web PubSub 中的逻辑单元，你可以在其中向一组客户端发布消息。 [关键概念](./key-concepts.md)包含有关 Azure Web PubSub 中使用的术语的详细说明。
    
    Azure Web PubSub 服务使用 [JSON Web 令牌 (JWT)](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims) 身份验证，因此在代码示例中，我们使用 Web PubSub SDK 中的 `WebPubSubServiceClient.getAuthenticationToken()` 来生成包含完整 URL 和有效访问令牌的服务的 URL。
    
    建立连接后，你将通过 WebSocket 连接接收消息。 因此，我们使用 `WebSocket.on('message', ...)` 来侦听传入的消息。
    
3. 运行以下命令，将 `<connection_string>` 替换为[前面步骤](#get-the-connectionstring-for-future-use)中提取的 ConnectionString：

    ```bash
    node subscribe "<connection_string>" "myHub1"
    ```

# <a name="python"></a>[Python](#tab/python)

1. 首先，让我们为此项目创建一个新文件夹 `subscriber`，并安装所需的依赖项：

    ```bash
    mkdir subscriber
    cd subscriber
    # Create venv
    python -m venv env

    # Active venv
    ./env/Scripts/activate

    # Or call .\env\Scripts\activate when you are using CMD under Windows

    pip install azure-messaging-webpubsubservice
    pip install websockets

    ```

2. 然后使用 WebSocket API 连接到服务。 使用以下代码创建一个 `subscribe.py` 文件：

    ```python
    import asyncio
    import sys
    import websockets
    from azure.messaging.webpubsubservice import (
        build_authentication_token
    )

    async def connect(url):
        async with websockets.connect(url) as ws:
            print('connected')
            while True:
                print('Message received: ' + await ws.recv())

    if len(sys.argv) != 3:
        print('Usage: python subscribe.py <connection-string> <hub-name>')
        exit(1)

    connection_string = sys.argv[1]
    hub_name = sys.argv[2]

    token = build_authentication_token(connection_string, hub_name)

    try:
        asyncio.get_event_loop().run_until_complete(connect(token['url']))
    except KeyboardInterrupt:
        pass

    ```

    上面的代码将创建一个 WebSocket 连接，连接到 Azure Web PubSub 中的中心。 中心是 Azure Web PubSub 中的逻辑单元，你可以在其中向一组客户端发布消息。 [关键概念](./key-concepts.md)包含有关 Azure Web PubSub 中使用的术语的详细说明。
    
    Azure Web PubSub 服务使用 [JSON Web 令牌 (JWT)](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims) 身份验证，因此在代码示例中，我们使用 Web PubSub SDK 中的 `build_authentication_token()` 来生成包含完整 URL 和有效访问令牌的服务的 URL。
    
    建立连接后，你将通过 WebSocket 连接接收消息。 因此，我们使用 `await ws.recv()` 来侦听传入的消息。

3. 运行以下命令，将 `<connection_string>` 替换为[前面步骤](#get-the-connectionstring-for-future-use)中提取的 ConnectionString：

    ```bash
    python subscribe.py "<connection_string>" "myHub1"
    ```

# <a name="java"></a>[Java](#tab/java)
1. 首先，让我们为本教程创建一个新文件夹 `pubsub`
    ```cmd
    mkdir pubsub
    cd pubsub
    ```

1. 然后在此 `pubsub` 文件夹中，让我们使用 Maven 创建一个新的控制台应用 `webpubsub-quickstart-subscriber`，并切换到 webpubsub-quickstart-subscriber 文件夹：

    ```console
    mvn archetype:generate --define interactiveMode=n --define groupId=com.webpubsub.quickstart --define artifactId=webpubsub-quickstart-subscriber --define archetypeArtifactId=maven-archetype-quickstart --define archetypeVersion=1.4
    cd webpubsub-quickstart-subscriber
    ```

2. 让我们将 WebSocket 和 Azure Web PubSub SDK 依赖项添加到 `pom.xml` 的 `dependencies` 节点中：

    * `azure-messaging-webpubsub`：适用于 Java 的 Web PubSub 服务 SDK
    * `Java-WebSocket`：适用于 Java 的 WebSocket 客户端 SDK

    ```xml
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-webpubsub</artifactId>
        <version>1.0.0-beta.2</version>
    </dependency>

    <dependency>
        <groupId>org.java-websocket</groupId>
        <artifactId>Java-WebSocket</artifactId>
        <version>1.5.1</version>
    </dependency>

    ```

3. 在 Azure Web PubSub 中，你可以通过 WebSocket 连接连接到服务并订阅消息。 WebSocket 是一个全双工信道，因此，服务可以将消息实时推送到客户端。 你可以使用任何支持 WebSocket 的 API/库来执行此操作。 在本示例中，我们使用 [Java-WebSocket](https://github.com/TooTallNate/Java-WebSocket) 包。 让我们导航到 /src/main/java/com/webpubsub/quickstart 目录，在编辑器中打开 App.java 文件，并将代码替换为以下内容：

    ```java
    
    package com.webpubsub.quickstart;

    import com.azure.messaging.webpubsub.*;
    import com.azure.messaging.webpubsub.models.*;
    
    import org.java_websocket.client.WebSocketClient;
    import org.java_websocket.handshake.ServerHandshake;

    import java.io.IOException;
    import java.net.URI;
    import java.net.URISyntaxException;

    /**
    * Connect to Azure Web PubSub service using WebSocket protocol
    *
    */
    public class App 
    {
        public static void main( String[] args ) throws IOException, URISyntaxException
        {
            if (args.length != 2) {
                System.out.println("Expecting 2 arguments: <connection-string> <hub-name>");
                return;
            }

            WebPubSubServiceClient client = new WebPubSubClientBuilder()
                .connectionString(args[0])
                .hub(args[1])
                .buildClient();

            WebPubSubAuthenticationToken token = client.getAuthenticationToken(new GetAuthenticationTokenOptions());

            WebSocketClient webSocketClient = new WebSocketClient(new URI(token.getUrl())) {
                @Override
                public void onMessage(String message) {
                    System.out.println(String.format("Message received: %s", message));
                }

                @Override
                public void onClose(int arg0, String arg1, boolean arg2) {
                    // TODO Auto-generated method stub
                }

                @Override
                public void onError(Exception arg0) {
                    // TODO Auto-generated method stub
                }

                @Override
                public void onOpen(ServerHandshake arg0) {
                    // TODO Auto-generated method stub
                }
                
            };

            webSocketClient.connect();
            System.in.read();
        }
    }

    ```

    上面的代码将创建一个 WebSocket 连接，连接到 Azure Web PubSub 中的中心。 中心是 Azure Web PubSub 中的逻辑单元，你可以在其中向一组客户端发布消息。 [关键概念](./key-concepts.md)包含有关 Azure Web PubSub 中使用的术语的详细说明。
    
    Azure Web PubSub 服务使用 [JSON Web 令牌 (JWT)](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims) 身份验证，因此在代码示例中，我们使用 Web PubSub SDK 中的 `WebPubSubServiceClient.getAuthenticationToken(new GetAuthenticationTokenOptions())` 来生成包含完整 URL 和有效访问令牌的服务的 URL。
    
    建立连接后，你将通过 WebSocket 连接接收消息。 因此，我们使用 `onMessage(String message)` 来侦听传入的消息。

4. 导航到包含 pom.xml 文件的目录，使用以下代码运行应用，并将 `<connection_string>` 替换为[前面步骤](#get-the-connectionstring-for-future-use)中提取的 ConnectionString：

    ```console
    mvn compile & mvn package & mvn exec:java -Dexec.mainClass="com.webpubsub.quickstart.App" -Dexec.cleanupDaemonThreads=false -Dexec.args="'<connection_string>' 'myHub1'"
    ```

---

## <a name="publish-messages-using-service-sdk"></a>使用服务 SDK 发布消息

现在，让我们使用 Azure Web PubSub SDK 向连接的客户端发布消息。

# <a name="c"></a>[C#](#tab/csharp)

1. 首先，让我们为此项目创建一个新文件夹 `publisher`，并安装所需的依赖项：

    ```bash
    mkdir publisher
    cd publisher
    dotnet new console
    dotnet add package Azure.Messaging.WebPubSub --prerelease
    ```

2. 现在，让我们更新 `Program.cs` 文件，以使用 `WebPubSubServiceClient` 类并将消息发送到客户端。

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.Messaging.WebPubSub;

    namespace publisher
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                if (args.Length != 3) {
                    Console.WriteLine("Usage: publisher <connectionString> <hub> <message>");
                    return;
                }
                var connectionString = args[0];
                var hub = args[1];
                var message = args[2];

                var serviceClient = new WebPubSubServiceClient(connectionString, hub);
                
                // Send messages to all the connected clients
                // You can also try SendToConnectionAsync to send messages to the specific connection
                await serviceClient.SendToAllAsync(message);
            }
        }
    }
    ```

    `SendToAllAsync()` 调用会直接将消息发送到中心内所有已连接的客户端。

3. 运行以下命令，将 `<connection_string>` 替换为[前面步骤](#get-the-connectionstring-for-future-use)中提取的 ConnectionString：

    ```bash
    dotnet run "<connection_string>" "myHub1" "Hello World"
    ```

4. 你可以看到之前的订阅者收到了以下消息：
   
    ```
    Message received: Hello World
    ```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

1. 首先，让我们为此项目创建一个新文件夹 `publisher`，并安装所需的依赖项：

    ```bash
    mkdir publisher
    cd publisher
    npm init -y
    npm install --save @azure/web-pubsub

    ```
2. 现在，让我们使用 Azure Web PubSub SDK 将消息发布到服务。 使用以下代码创建一个 `publish.js` 文件：

    ```javascript
    const { WebPubSubServiceClient } = require('@azure/web-pubsub');

    if (process.argv.length !== 5) {
    console.log('Usage: node publish <connection-string> <hub-name> <message>');
    return 1;
    }

    let serviceClient = new WebPubSubServiceClient(process.argv[2], process.argv[3]);

    // by default it uses `application/json`, specify contentType as `text/plain` if you want plain-text
    serviceClient.sendToAll(process.argv[4], { contentType: "text/plain" });
    ```

    `sendToAll()` 调用会直接将消息发送到中心内所有已连接的客户端。

3. 运行以下命令，将 `<connection_string>` 替换为[前面步骤](#get-the-connectionstring-for-future-use)中提取的 ConnectionString：

    ```bash
    node publish "<connection_string>" "myHub1" "Hello World"
    ```

4. 你可以看到之前的订阅者收到了以下消息：
   
    ```
    Message received: Hello World
    ```

# <a name="python"></a>[Python](#tab/python)

1. 首先，让我们为此项目创建一个新文件夹 `publisher`，并安装所需的依赖项：
    * 使用 bash 时
        ```bash
        mkdir publisher
        cd publisher
        # Create venv
        python -m venv env

        # Active venv
        ./env/Scripts/activate

        # Or call .\env\Scripts\activate when you are using CMD under windows

        pip install azure-messaging-webpubsubservice

        ```
2. 现在，让我们使用 Azure Web PubSub SDK 将消息发布到服务。 使用以下代码创建一个 `publish.py` 文件：

    ```python
    import sys
    from azure.messaging.webpubsubservice import (
        WebPubSubServiceClient
    )
    from azure.messaging.webpubsubservice.rest import *

    if len(sys.argv) != 4:
        print('Usage: python publish.py <connection-string> <hub-name> <message>')
        exit(1)

    connection_string = sys.argv[1]
    hub_name = sys.argv[2]
    message = sys.argv[3]

    service_client = WebPubSubServiceClient.from_connection_string(connection_string)
    res = service_client.send_request(build_send_to_all_request(hub_name, content=message, content_type='text/plain'))
    # res should be <HttpResponse: 202 Accepted>
    print(res)

    ```

    `build_send_to_all_request()` 可生成消息，`send_request()` 可将消息发送到中心内所有已连接的客户端。

3. 运行以下命令，将 `<connection_string>` 替换为[前面步骤](#get-the-connectionstring-for-future-use)中提取的 ConnectionString：

    ```bash
    python publish.py "<connection_string>" "myHub1" "Hello World"
    ```

4. 你可以看到之前的订阅者收到了以下消息：
   
    ```
    Received message: Hello World
    ```

# <a name="java"></a>[Java](#tab/java)

1.  让我们使用另一个终端，返回到 `pubsub` 文件夹以创建发布者控制台应用 `webpubsub-quickstart-publisher`，并切换到 webpubsub-quickstart-publisher 文件夹：
    ```console
    mvn archetype:generate --define interactiveMode=n --define groupId=com.webpubsub.quickstart --define artifactId=webpubsub-quickstart-publisher --define archetypeArtifactId=maven-archetype-quickstart --define archetypeVersion=1.4
    cd webpubsub-quickstart-publisher
    ```

2. 让我们将 Azure Web PubSub SDK 依赖项添加到 `pom.xml` 的 `dependencies` 节点中：

    ```xml
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-webpubsub</artifactId>
        <version>1.0.0-beta.2</version>
    </dependency>
    ```

3. 现在，让我们使用 Azure Web PubSub SDK 将消息发布到服务。 让我们导航到 /src/main/java/com/webpubsub/quickstart 目录，在编辑器中打开 App.java 文件，并将代码替换为以下内容：

    ```java
    package com.webpubsub.quickstart;

    import com.azure.messaging.webpubsub.*;
    import com.azure.messaging.webpubsub.models.*;

    /**
    * Publish messages using Azure Web PubSub service SDK
    *
    */
    public class App 
    {
        public static void main( String[] args )
        {
            if (args.length != 3) {
                System.out.println("Expecting 3 arguments: <connection-string> <hub-name> <message>");
                return;
            }

            WebPubSubServiceClient client = new WebPubSubClientBuilder()
                .connectionString(args[0])
                .hub(args[1])
                .buildClient();
            client.sendToAll(args[2], WebPubSubContentType.TEXT_PLAIN);
        }
    }

    ```

    `sendToAll()` 调用会直接将消息发送到中心内所有已连接的客户端。

4. 导航到包含 pom.xml 文件的目录，使用以下命令运行项目，并将 `<connection_string>` 替换为[前面步骤](#get-the-connectionstring-for-future-use)中提取的 ConnectionString：

    ```console
    mvn compile & mvn package & mvn exec:java -Dexec.mainClass="com.webpubsub.quickstart.App" -Dexec.cleanupDaemonThreads=false -Dexec.args="'<connection_string>' 'myHub1' 'Hello World'"
    ```

5. 你可以看到之前的订阅者收到了以下消息：
   
    ```
    Message received: Hello World
    ```

---

## <a name="next-steps"></a>后续步骤

本教程大致介绍了如何连接到 Web PubSub 服务以及如何将消息发布到连接的客户端。

请查看其他教程，进一步深入了解如何使用该服务。

> [!div class="nextstepaction"]
> [教程：使用 Azure Web PubSub 创建聊天室](./tutorial-build-chat.md)

> [!div class="nextstepaction"]
> [了解更多 Azure Web PubSub 示例](https://aka.ms/awps/samples)
