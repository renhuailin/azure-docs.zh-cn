---
title: 教程 - 使用 Azure Web PubSub 服务创建聊天应用
description: 演示如何使用 Azure Web PubSub 服务创建聊天应用的教程
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: tutorial
ms.date: 08/16/2021
ms.openlocfilehash: cf3f6f174cc5302b4215db21ec7362401b3454e9
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829728"
---
# <a name="tutorial-create-a-chat-app-with-azure-web-pubsub-service"></a>教程：使用 Azure Web PubSub 服务创建聊天应用

[发布和订阅消息教程](./tutorial-pub-sub-messages.md)介绍了使用 Azure Web PubSub 发布和订阅消息的基础知识。 本教程将介绍 Azure Web PubSub 的事件系统，以便用它来生成具有实时通信功能的完整 Web 应用程序。 

在本教程中，你将了解如何：

> [!div class="checklist"]
> * 创建 Web PubSub 服务实例
> * 配置 Azure Web PubSub 的事件处理程序设置
> * 生成实时聊天应用

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

# <a name="java"></a>[Java](#tab/java)

- [Java 开发工具包 (JDK)](/java/azure/jdk/) 8 或更高版本
- [Apache Maven](https://maven.apache.org/download.cgi)

---

## <a name="create-the-application"></a>创建应用程序

Azure Web PubSub 中有两个角色：服务器和客户端。 此概念类似于 Web 应用程序中的服务器和客户端角色。 服务器负责管理客户端、侦听和响应客户端消息，客户端的角色则是将用户消息发送到服务器、从服务器接收消息并向最终用户直观显示这些消息。

在本教程中，我们将生成一个实时聊天 Web 应用程序。 在真实的 Web 应用程序中，服务器的职责还包括对客户端进行身份验证，以及为应用程序 UI 提供静态网页。 

# <a name="c"></a>[C#](#tab/csharp)

我们将使用 [ASP.NET Core](/aspnet/core) 来托管网页并处理传入的请求。

首先，让我们创建一个空的 ASP.NET Core 应用。

1.  创建 Web 应用

    ```bash
    dotnet new web
    dotnet add package Azure.Messaging.WebPubSub --prerelease
    ```

2.  然后在 `Startup.cs` 中的 `app.UseRouting();` 前面添加 `app.UseStaticFiles();`，以支持静态文件。 删除 `app.UseEndpoints` 中的默认 `endpoints.MapGet`。

    ```csharp
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
        });
    }
    ```

3.  另外，创建一个 HTML 文件并将其保存为 `wwwroot/index.html`，我们稍后会在聊天应用的 UI 中使用它。

    ```html
    <html>
    <body>
      <h1>Azure Web PubSub Chat</h1>
    </body>

    </html>
    ```

可以通过运行 `dotnet run --urls http://localhost:8080` 来测试服务器，并在浏览器中访问 http://localhost:8080/index.html 。

你可能还记得，在[发布和订阅消息教程](./tutorial-pub-sub-messages.md)中，订阅者使用 Web PubSub SDK 中的 API 根据连接字符串生成访问令牌，并使用它连接到服务。 在真实的应用程序中，这种做法通常不太安全，因为连接字符串具有很高的权限，可对服务执行任何操作，因此你不想与任何客户端共享它。 让我们将此访问令牌生成过程更改为服务器端的 REST API。这样一来，客户端每次需要连接服务时，都可以调用此 API 来请求访问令牌，而无需保存连接字符串。

1.  安装 Microsoft.Extensions.Azure

    ```bash
    dotnet add package Microsoft.Extensions.Azure
    ```
2. 在 `ConfigureServices` 内添加服务客户端，不要忘记将 `<connection_string>` 替换为你的服务之一。

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddAzureClients(builder =>
        {
            builder.AddWebPubSubServiceClient("<connection_string>", "chat");
        });
    }
    ```
2.  在 `app.UseEndpoints` 中向服务器添加 `/negotiate` API，以生成令牌

    ```csharp
    app.UseEndpoints(endpoints =>
    {
        endpoints.MapGet("/negotiate", async context =>
        {
            var id = context.Request.Query["id"];
            if (id.Count != 1)
            {
                context.Response.StatusCode = 400;
                await context.Response.WriteAsync("missing user id");
                return;
            }
            var serviceClient = context.RequestServices.GetRequiredService<WebPubSubServiceClient>();
            await context.Response.WriteAsync(serviceClient.GenerateClientAccessUri(userId: id).AbsoluteUri);
        });
    });
    ```

    此令牌生成代码类似于我们在[发布和订阅消息教程](./tutorial-pub-sub-messages.md)中使用的代码，其不同之处在于，我们在生成令牌时多传递了一个参数 (`userId`)。 用户 ID 可用于识别客户端的身份，因此，当你收到消息时，你知道消息来自何处。

    你可以通过运行 `dotnet run --urls http://localhost:8080` 并访问 `http://localhost:8080/negotiate?id=<user-id>` 来测试此 API，它将为你提供 Azure Web PubSub 的完整 URL 和访问令牌。

3.  然后更新 `index.html`，使其包含以下脚本，以从服务器获取令牌并连接到服务
 
    ```html
    <html>
      <body>
        <h1>Azure Web PubSub Chat</h1>
      </body>
  
      <script>
        (async function () {
          let id = prompt('Please input your user name');
          let res = await fetch(`/negotiate?id=${id}`);
          let url = await res.text();
          let ws = new WebSocket(url);
          ws.onopen = () => console.log('connected');
        })();
      </script>
    </html>
    ```

    如果使用的是 Chrome，可以通过打开主页并输入用户名来测试它。 按 F12 打开“开发人员工具”窗口，切换到“控制台”表，你会看到浏览器控制台中正在打印 `connected`。


# <a name="javascript"></a>[JavaScript](#tab/javascript)

我们将使用 [express.js](https://expressjs.com/)（一种流行的 node.js Web 框架）来完成这项工作。

首先创建一个空的 express 应用。

1.  安装 express.js

    ```bash
    npm init -y
    npm install --save express
    ```

2.  然后创建一个 express 服务器并将其保存为 `server.js`

    ```javascript
    const express = require('express');

    const app = express();
    app.use(express.static('public'));
    app.listen(8080, () => console.log('server started'));
    ```

3.  另外，创建一个 HTML 文件并将其保存为 `public/index.html`，我们稍后会在聊天应用的 UI 中使用它。

    ```html
    <html>

    <body>
      <h1>Azure Web PubSub Chat</h1>
    </body>

    </html>
    ```

可以通过运行 `node server` 来测试服务器，并在浏览器中访问 http://localhost:8080 。


你可能还记得，在[发布和订阅消息教程](./tutorial-pub-sub-messages.md)中，订阅者使用 Web PubSub SDK 中的 API 根据连接字符串生成访问令牌，并使用它连接到服务。 在真实的应用程序中，这种做法通常不太安全，因为连接字符串具有很高的权限，可对服务执行任何操作，因此你不想与任何客户端共享它。 让我们将此访问令牌生成过程更改为服务器端的 REST API。这样一来，客户端每次需要连接服务时，都可以调用此 API 来请求访问令牌，而无需保存连接字符串。

1.  安装 Azure Web PubSub SDK

    ```bash
    npm install --save @azure/web-pubsub
    ```

2.  向服务器添加 `/negotiate` API 以生成令牌

    ```javascript
    const express = require('express');
    const { WebPubSubServiceClient } = require('@azure/web-pubsub');

    const app = express();
    const hubName = 'chat';

    let serviceClient = new WebPubSubServiceClient(process.argv[2], hubName);

    app.get('/negotiate', async (req, res) => {
      let id = req.query.id;
      if (!id) {
        res.status(400).send('missing user id');
        return;
      }
      let token = await serviceClient.getAuthenticationToken({ userId: id });
      res.json({
        url: token.url
      });
    });

    app.use(express.static('public'));
    app.listen(8080, () => console.log('server started'));
    ```

    此令牌生成代码类似于我们在[发布和订阅消息教程](./tutorial-pub-sub-messages.md)中使用的代码，其不同之处在于，我们在生成令牌时多传递了一个参数 (`userId`)。 用户 ID 可用于识别客户端的身份，因此，当你收到消息时，你知道消息来自何处。

    你可以通过运行 `node server "<connection-string>"` 并访问 `http://localhost:8080/negotiate?id=<user-id>` 来测试此 API，它将为你提供 Azure Web PubSub 的完整 URL 和访问令牌。

3.  然后使用以下脚本更新 `index.html`，以从服务器获取令牌并连接到服务
 
    ```html

    <html>
      <body>
        <h1>Azure Web PubSub Chat</h1>
      </body>
  
      <script>
        (async function () {
          let id = prompt('Please input your user name');
          let res = await fetch(`/negotiate?id=${id}`);
          let data = await res.json();
          let ws = new WebSocket(data.url);
          ws.onopen = () => console.log('connected');
        })();
      </script>
    </html>
    ```

    如果使用的是 Chrome，可以通过打开主页并输入用户名来测试它。 按 F12 打开“开发人员工具”窗口，切换到“控制台”表，你会看到浏览器控制台中正在打印 `connected`。

# <a name="java"></a>[Java](#tab/java)

我们将使用 [Javalin](https://javalin.io/) Web 框架来托管网页并处理传入的请求。

1. 首先，让我们使用 Maven 创建一个新应用 `webpubsub-tutorial-chat`，并切换到 webpubsub-tutorial-chat 文件夹：

    ```console
    mvn archetype:generate --define interactiveMode=n --define groupId=com.webpubsub.tutorial --define artifactId=webpubsub-tutorial-chat --define archetypeArtifactId=maven-archetype-quickstart --define archetypeVersion=1.4
    cd webpubsub-tutorial-chat
    ```

2. 让我们将 `javalin` Web 框架依赖项添加到 `pom.xml` 的 `dependencies` 节点中：

    * `javalin`：适用于 Java 的简单 Web 框架
    * `slf4j-simple`：适用于 Java 的记录器

    ```xml
    <!-- https://mvnrepository.com/artifact/io.javalin/javalin -->
    <dependency>
        <groupId>io.javalin</groupId>
        <artifactId>javalin</artifactId>
        <version>3.13.6</version>
    </dependency>

    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-simple</artifactId>
        <version>1.7.30</version>
    </dependency>
    ```

3. 让我们导航到 /src/main/java/com/webpubsub/tutorial 目录，在编辑器中打开 App.java 文件，使用 `Javalin.create` 提供静态文件：

    ```java
    package com.webpubsub.tutorial;
    
    import io.javalin.Javalin;
    
    public class App {
        public static void main(String[] args) {
            // start a server
            Javalin app = Javalin.create(config -> {
                config.addStaticFiles("public");
            }).start(8080);
        }
    }
    ```

    根据你的设置，你可能需要将语言级别显式设置为 Java 8。 此操作可以在 pom.xml 中完成。 添加以下代码片段：
    ```xml
    <build>
        <plugins>
            <plugin>
              <artifactId>maven-compiler-plugin</artifactId>
              <version>3.8.0</version>
              <configuration>
                <source>1.8</source>
                <target>1.8</target>
              </configuration>
            </plugin>
        </plugins>
    </build>
    ```

4. 让我们创建一个 HTML 文件并将其保存到 /src/main/resources/public/index.html 中。 稍后我们将在聊天应用的 UI 中使用它。

    ```html
    <html>
    <body>
      <h1>Azure Web PubSub Chat</h1>
    </body>

    </html>
    ```

可以通过在包含 pom.xml 文件的目录下运行以下命令来测试服务器，并在浏览器中访问 http://localhost:8080 。

```console
mvn compile & mvn package & mvn exec:java -Dexec.mainClass="com.webpubsub.tutorial.App" -Dexec.cleanupDaemonThreads=false
```

你可能还记得，在[发布和订阅消息教程](./tutorial-pub-sub-messages.md)中，订阅者使用 Web PubSub SDK 中的 API 根据连接字符串生成访问令牌，并使用它连接到服务。 在真实的应用程序中，这种做法通常不太安全，因为连接字符串具有很高的权限，可对服务执行任何操作，因此你不想与任何客户端共享它。 让我们将此访问令牌生成过程更改为服务器端的 REST API。这样一来，客户端每次需要连接服务时，都可以调用此 API 来请求访问令牌，而无需保存连接字符串。

1. 将 Azure Web PubSub SDK 依赖项添加到 `pom.xml` 的 `dependencies` 节点中：

    ```xml
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-webpubsub</artifactId>
        <version>1.0.0-beta.2</version>
    </dependency>
    ```

2. 向 `App.java` 文件添加 `/negotiate` API 以生成令牌：

    ```java
    package com.webpubsub.tutorial;
    
    import com.azure.messaging.webpubsub.WebPubSubClientBuilder;
    import com.azure.messaging.webpubsub.WebPubSubServiceClient;
    import com.azure.messaging.webpubsub.models.GetAuthenticationTokenOptions;
    import com.azure.messaging.webpubsub.models.WebPubSubAuthenticationToken;
    
    import io.javalin.Javalin;
    
    public class App {
        public static void main(String[] args) {
            
            if (args.length != 1) {
                System.out.println("Expecting 1 arguments: <connection-string>");
                return;
            }
    
            // create the service client
            WebPubSubServiceClient client = new WebPubSubClientBuilder()
                    .connectionString(args[0])
                    .hub("chat")
                    .buildClient();
    
            // start a server
            Javalin app = Javalin.create(config -> {
                config.addStaticFiles("public");
            }).start(8080);
    
            
            // Handle the negotiate request and return the token to the client
            app.get("/negotiate", ctx -> {
                String id = ctx.queryParam("id");
                if (id == null) {
                    ctx.status(400);
                    ctx.result("missing user id");
                    return;
                }
                GetAuthenticationTokenOptions option = new GetAuthenticationTokenOptions();
                option.setUserId(id);
                WebPubSubAuthenticationToken token = client.getAuthenticationToken(option);
                ctx.result(token.getUrl());
                return;
            });
        }
    }
    ```

    此令牌生成代码类似于我们在[发布和订阅消息教程](./tutorial-pub-sub-messages.md)中使用的代码，其不同之处在于，我们在生成令牌时调用 `setUserId` 方法来设置用户 ID。 用户 ID 可用于识别客户端的身份，因此，当你收到消息时，你知道消息来自何处。

    你可以通过运行以下命令（将 `<connection_string>` 替换为[前面步骤](#get-the-connectionstring-for-future-use)中提取的 ConnectionString）并访问 `http://localhost:8080/negotiate?id=<user-id>` 来测试此 API，它将为你提供 Azure Web PubSub 的完整 URL 和访问令牌。

    ```console
    mvn compile & mvn package & mvn exec:java -Dexec.mainClass="com.webpubsub.tutorial.App" -Dexec.cleanupDaemonThreads=false -Dexec.args="'<connection_string>'"
    ```

3. 然后使用以下脚本更新 `index.html`，以从服务器获取令牌并连接到服务。

    ```html
    <html>
      <body>
        <h1>Azure Web PubSub Chat</h1>
      </body>
  
      <script>
        (async function () {
          let id = prompt('Please input your user name');
          let res = await fetch(`/negotiate?id=${id}`);
          let url = await res.text();
          let ws = new WebSocket(url);
          ws.onopen = () => console.log('connected');
        })();
      </script>
    </html>
    ```

    如果使用的是 Chrome，可以通过打开主页并输入用户名来测试它。 按 F12 打开“开发人员工具”窗口，切换到“控制台”表，你会看到浏览器控制台中正在打印 `connected`。

---

## <a name="handle-events"></a>处理事件

在 Azure Web PubSub 中，当客户端发生某些活动时（例如客户端连接或断开连接），服务会向服务器发送通知，以便服务器可以对这些事件做出响应。

事件以 Webhook 的形式传送到服务器。 Webhook 由应用程序服务器提供和公开，并在 Azure Web PubSub 服务端注册。 每当发生事件时，服务都会调用 Webhook。

Azure Web PubSub 遵循 [CloudEvents](./reference-cloud-events.md) 来描述事件数据。 

# <a name="c"></a>[C#](#tab/csharp)
目前，你需要用 C# 自行实现事件处理程序，步骤直接遵循[协议规范](./reference-cloud-events.md)，如下所示。

1. 在 `UseEndpoints` 中添加事件处理程序。 指定事件的终结点路径，比如 `/eventhandler`。 

2. 首先，我们想处理 abuse protection OPTIONS 请求，我们检查标头中是否包含 `WebHook-Request-Origin` 标头，然后返回标头 `WebHook-Allowed-Origin`。 为了简化演示，我们返回 `*` 以允许所有来源。
    ```csharp
    app.UseEndpoints(endpoints =>
    {
        // abuse protection
        endpoints.Map("/eventhandler", async context =>
        {
            if (context.Request.Method == "OPTIONS")
            {
                if (context.Request.Headers["WebHook-Request-Origin"].Count > 0)
                {
                    context.Response.Headers["WebHook-Allowed-Origin"] = "*";
                    context.Response.StatusCode = 200;
                    return;
                }
            }
        });
    });
    ```

3. 然后，我们想检查传入的请求是否就是我们预期的事件。 比方说，我们现在关心的是系统 `connected` 事件，它应该包含标头 `ce-type` 作为 `azure.webpubsub.sys.connected`。 我们在 abuse protection 后面添加逻辑：
    ```csharp
    app.UseEndpoints(endpoints =>
    {
        // abuse protection
        endpoints.Map("/eventhandler", async context =>
        {
            if (context.Request.Method == "OPTIONS")
            {
                if (context.Request.Headers["WebHook-Request-Origin"].Count > 0)
                {
                    context.Response.Headers["WebHook-Allowed-Origin"] = "*";
                    context.Response.StatusCode = 200;
                    return;
                }
            }
            else if (context.Request.Method == "POST")
            {
                // get the userId from header
                var userId = context.Request.Headers["ce-userId"];
                if (context.Request.Headers["ce-type"] == "azure.webpubsub.sys.connected")
                {
                    // the connected event
                    Console.WriteLine($"{userId} connected");
                    context.Response.StatusCode = 200;
                    return;
                }
            }
        });
    });
    ```

在上面的代码中，我们在连接客户端时直接向控制台打印一条消息。 你可以看到，我们使用了 `context.Request.Headers["ce-userId"]`，因此我们可以看到所连接客户端的标识。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

如果使用 Web PubSub SDK，则有现成的实现来分析和处理 CloudEvents 架构，因此无需处理这些细节。

添加以下代码，在 `/eventhandler` 处公开 REST API（此操作由 Web PubSub SDK 提供的 express 中间件完成）以处理“客户端已连接”事件：

```bash
npm install --save @azure/web-pubsub-express
```

```javascript
const { WebPubSubEventHandler } = require('@azure/web-pubsub-express');

let handler = new WebPubSubEventHandler(hubName, ['*'], {
  path: '/eventhandler',
  onConnected: async req => {
    console.log(`${req.context.userId} connected`);
  }
});

app.use(handler.getMiddleware());
```

在上面的代码中，我们在连接客户端时直接向控制台打印一条消息。 你可以看到，我们使用了 `req.context.userId`，因此我们可以看到所连接客户端的标识。

# <a name="java"></a>[Java](#tab/java)
目前，你需要用 Java 自行实现事件处理程序，步骤直接遵循[协议规范](./reference-cloud-events.md)，如下所示。

1. 为事件处理程序路径添加 HTTP 处理程序，比如 `/eventhandler`。 

2. 首先，我们想处理 abuse protection OPTIONS 请求，我们检查标头中是否包含 `WebHook-Request-Origin` 标头，然后返回标头 `WebHook-Allowed-Origin`。 为了简化演示，我们返回 `*` 以允许所有来源。
    ```java
    
    // validation: https://azure.github.io/azure-webpubsub/references/protocol-cloudevents#validation
    app.options("/eventhandler", ctx -> {
        ctx.header("WebHook-Allowed-Origin", "*");
    });
    ```

3. 然后，我们想检查传入的请求是否就是我们预期的事件。 比方说，我们现在关心的是系统 `connected` 事件，它应该包含标头 `ce-type` 作为 `azure.webpubsub.sys.connected`。 我们在 abuse protection 后面添加逻辑：
    ```java
    // validation: https://azure.github.io/azure-webpubsub/references/protocol-cloudevents#validation
    app.options("/eventhandler", ctx -> {
        ctx.header("WebHook-Allowed-Origin", "*");
    });

    // handle events: https://azure.github.io/azure-webpubsub/references/protocol-cloudevents#events
    app.post("/eventhandler", ctx -> {
        String event = ctx.header("ce-type");
        if ("azure.webpubsub.sys.connected".equals(event)) {
            String id = ctx.header("ce-userId");
            System.out.println(id + " connected.");
        }
        ctx.status(200);
    });

    ```

在上面的代码中，我们在连接客户端时直接向控制台打印一条消息。 你可以看到，我们使用了 `ctx.header("ce-userId")`，因此我们可以看到所连接客户端的标识。

---

## <a name="set-up-the-event-handler"></a>设置事件处理程序

### <a name="expose-localhost"></a>公开 localhost

接下来，我们需要在服务中设置 Webhook URL，以便在有新事件时，它可以知道在哪里调用。 但是，这里有一个问题，我们的服务器在 localhost 上运行，因此没有可访问 Internet 的终结点。 在这里，我们使用 [ngrok](https://ngrok.com/) 向 Internet 公开 localhost。

1.  首先，从 https://ngrok.com/download 下载 ngrok，将可执行文件提取到本地文件夹或系统 bin 文件夹中。
2.  启动 ngrok
    
    ```bash
    ngrok http 8080
    ```

ngrok 将打印可从 Internet 访问的 URL (`https://<domain-name>.ngrok.io`)。

### <a name="set-event-handler"></a>设置事件处理程序

接下来，我们更新服务事件处理程序并设置 Webhook URL。

使用 Azure CLI [az webpubsub event-handler hub](/cli/azure/webpubsub/event-handler/hub) 命令更新事件处理程序设置：

  > [!Important]
  > 将 &lt;your-unique-resource-name&gt; 替换为在前面的步骤中创建的 Web PubSub 资源的名称。
  > 将 &lt;domain-name&lt; 替换为 ngrok 打印的名称。

```azurecli-interactive
az webpubsub event-handler hub update -n "<your-unique-resource-name>" -g "myResourceGroup" --hub-name chat --template url-template="https://<domain-name>.ngrok.io/eventHandler" user-event-pattern="*" system-event-pattern="connected"
```


完成更新后，打开主页 http://localhost:8080/index.html ，输入用户名，你将看到服务器控制台中打印的 connected 消息。

## <a name="handle-message-events"></a>处理 Message 事件

除了 `connected` 或 `disconnected` 等系统事件外，客户端还可以通过 WebSocket 连接来发送消息。这些消息将作为一种特殊类型的事件传送到服务器，称为 `message` 事件。 我们可以使用此事件从一个客户端接收消息，并将其广播到所有客户端，以便它们可以相互对话。

# <a name="c"></a>[C#](#tab/csharp)

`message` 事件的 `ce-type` 始终为 `azure.webpubsub.user.message`，详情请参阅[事件消息](./reference-cloud-events.md#message)。

1. 处理 message 事件

    ```csharp
    app.UseEndpoints(endpoints =>
    {
        // abuse protection
        endpoints.Map("/eventhandler", async context =>
        {
            var serviceClient = context.RequestServices.GetRequiredService<WebPubSubServiceClient>();
            if (context.Request.Method == "OPTIONS")
            {
                if (context.Request.Headers["WebHook-Request-Origin"].Count > 0)
                {
                    context.Response.Headers["WebHook-Allowed-Origin"] = "*";
                    context.Response.StatusCode = 200;
                    return;
                }
            }
            else if (context.Request.Method == "POST")
            {
                // get the userId from header
                var userId = context.Request.Headers["ce-userId"];
                if (context.Request.Headers["ce-type"] == "azure.webpubsub.sys.connected")
                {
                    // the connected event
                    Console.WriteLine($"{userId} connected");
                    context.Response.StatusCode = 200;
                    return;
                }
                else if (context.Request.Headers["ce-type"] == "azure.webpubsub.user.message")
                {
                    using var stream = new StreamReader(context.Request.Body);
                    await serviceClient.SendToAllAsync($"[{userId}] {await stream.ReadToEndAsync()}");
                    context.Response.StatusCode = 200;
                    return;
                }
            }
        });
    });
    ```

    此事件处理程序使用 `WebPubSubServiceClient.SendToAllAsync()` 将收到的消息广播到所有客户端。

2.  更新 `index.html`，以添加从用户向服务器发送消息并在页面中显示收到的消息的逻辑。

    ```html
    <html>

    <body>
      <h1>Azure Web PubSub Chat</h1>
      <input id="message" placeholder="Type to chat...">
      <div id="messages"></div>
      <script>
        (async function () {
          let id = prompt('Please input your user name');
          let res = await fetch(`/negotiate?id=${id}`);
          let url = await res.text();
          let ws = new WebSocket(url);
          ws.onopen = () => console.log('connected');

          let messages = document.querySelector('#messages');
          ws.onmessage = event => {
            let m = document.createElement('p');
            m.innerText = event.data;
            messages.appendChild(m);
          };

          let message = document.querySelector('#message');
          message.addEventListener('keypress', e => {
            if (e.charCode !== 13) return;
            ws.send(message.value);
            message.value = '';
          });
        })();
      </script>
    </body>

    </html>
    ```

    你可以从上面的代码中看到，我们使用 `WebSocket.send()` 发送消息，使用 `WebSocket.onmessage` 侦听来自服务的消息。

3.  最后，更新 `onConnected` 处理程序以将 connected 事件广播到所有客户端，以便它们可以看到谁加入了聊天室。

    ```csharp
    app.UseEndpoints(endpoints =>
    {
        // abuse protection
        endpoints.Map("/eventhandler", async context =>
        {
            if (context.Request.Method == "OPTIONS")
            {
                if (context.Request.Headers["WebHook-Request-Origin"].Count > 0)
                {
                    context.Response.Headers["WebHook-Allowed-Origin"] = "*";
                    context.Response.StatusCode = 200;
                    return;
                }
            }
            else if (context.Request.Method == "POST")
            {
                // get the userId from header
                var userId = context.Request.Headers["ce-userId"];
                if (context.Request.Headers["ce-type"] == "azure.webpubsub.sys.connected")
                {
                    // the connected event
                    Console.WriteLine($"{userId} connected");
                    await serviceClient.SendToAllAsync($"[SYSTEM] {userId} joined.");
                    context.Response.StatusCode = 200;
                    return;
                }
            }
        });
    });
    ```

现在，使用 `dotnet run --urls http://localhost:8080` 运行服务器，并打开多个浏览器实例以访问 http://localhost:8080/index.html ，然后就可以相互聊天了。

可以在[此处][code-csharp]找到本教程的完整代码示例。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

1. 添加新的 `handleUserEvent` 处理程序

    ```javascript
    let handler = new WebPubSubEventHandler(hubName, ['*'], {
      path: '/eventhandler',
      onConnected: async req => {
        console.log(`${req.context.userId} connected`);
      },
      handleUserEvent: async (req, res) => {
        if (req.context.eventName === 'message') 
            await serviceClient.sendToAll(`[${req.context.userId}] ${req.data}`, { contentType: 'text/plain' });
        res.success();
      }
    });
    ```

    此事件处理程序使用 `WebPubSubServiceClient.sendToAll()` 将收到的消息广播到所有客户端。

    你可以看到 `handleUserEvent` 还有一个 `res` 对象，你可以在其中将消息发送回事件发送方。 在这里，我们只需调用 `res.success()`，使 WebHook 返回 200（请注意，即使你不想向客户端返回任何内容，也需要执行此调用，否则 WebHook 永远不会返回并且客户端连接将关闭）。

2.  更新 `index.html`，以添加从用户向服务器发送消息并在页面中显示收到的消息的逻辑。

    ```html
    <html>

      <body>
        <h1>Azure Web PubSub Chat</h1>
        <input id="message" placeholder="Type to chat...">
        <div id="messages"></div>
        <script>
          (async function () {
            let id = prompt('Please input your user name');
            let res = await fetch(`/negotiate?id=${id}`);
            let data = await res.json();
            let ws = new WebSocket(data.url);
            ws.onopen = () => console.log('connected');
  
            let messages = document.querySelector('#messages');
            ws.onmessage = event => {
              let m = document.createElement('p');
              m.innerText = event.data;
              messages.appendChild(m);
            };
  
            let message = document.querySelector('#message');
            message.addEventListener('keypress', e => {
              if (e.charCode !== 13) return;
              ws.send(message.value);
              message.value = '';
            });
          })();
        </script>
      </body>
  
      </html>
    ```

    你可以从上面的代码中看到，我们使用 `WebSocket.send()` 发送消息，使用 `WebSocket.onmessage` 侦听来自服务的消息。

3. `sendToAll` 接受对象作为输入并向客户端发送 JSON 文本。 在实际场景中，我们可能需要复杂的对象来携带有关消息的更多信息。 最后，更新处理程序，以将 JSON 对象广播到所有客户端：

    ```javascript
    let handler = new WebPubSubEventHandler(hubName, ['*'], {
      path: '/eventhandler',
      onConnected: async req => {
        console.log(`${req.context.userId} connected`);
        await serviceClient.sendToAll({
          type: "system",
          message: `${req.context.userId} joined`
        });
      },
      handleUserEvent: async (req, res) => {
        if (req.context.eventName === 'message') {
          await serviceClient.sendToAll({
            from: req.context.userId,
            message: req.data
          });
        }
        res.success();
      }
    });
    ```

4. 更新客户端以分析 JSON 数据：
    ```html
    <html>

      <body>
        <h1>Azure Web PubSub Chat</h1>
        <input id="message" placeholder="Type to chat...">
        <div id="messages"></div>
        <script>
          (async function () {
            let id = prompt('Please input your user name');
            let res = await fetch(`/negotiate?id=${id}`);
            let data = await res.json();
            let ws = new WebSocket(data.url);
            ws.onopen = () => console.log('connected');
  
            let messages = document.querySelector('#messages');
            
            ws.onmessage = event => {
              let m = document.createElement('p');
              let data = JSON.parse(event.data);
              m.innerText = `[${data.type || ''}${data.from || ''}] ${data.message}`;
              messages.appendChild(m);
            };
  
            let message = document.querySelector('#message');
            message.addEventListener('keypress', e => {
              if (e.charCode !== 13) return;
              ws.send(message.value);
              message.value = '';
            });
          })();
        </script>
      </body>
  
    </html>
    ```

现在，运行服务器并打开多个浏览器实例，然后就可以相互聊天了。

可以在[此处][code-js]找到本教程的完整代码示例。

# <a name="java"></a>[Java](#tab/java)

`message` 事件的 `ce-type` 始终为 `azure.webpubsub.user.message`，详情请参阅[事件消息](./reference-cloud-events.md#message)。

1. 处理 message 事件

    ```java
    // handle events: https://azure.github.io/azure-webpubsub/references/protocol-cloudevents#events
    app.post("/eventhandler", ctx -> {
        String event = ctx.header("ce-type");
        if ("azure.webpubsub.sys.connected".equals(event)) {
            String id = ctx.header("ce-userId");
            System.out.println(id + " connected.");
        } else if ("azure.webpubsub.user.message".equals(event)) {
            String id = ctx.header("ce-userId");
            String message = ctx.body();
            client.sendToAll(String.format("[%s] %s", id, message), WebPubSubContentType.TEXT_PLAIN);
        }
        ctx.status(200);
    });
    ```

    此事件处理程序使用 `client.sendToAll()` 将收到的消息广播到所有客户端。

2.  更新 `index.html`，以添加从用户向服务器发送消息并在页面中显示收到的消息的逻辑。

    ```html
    <html>

    <body>
      <h1>Azure Web PubSub Chat</h1>
      <input id="message" placeholder="Type to chat...">
      <div id="messages"></div>
      <script>
        (async function () {
          let id = prompt('Please input your user name');
          let res = await fetch(`/negotiate?id=${id}`);
          let url = await res.text();
          let ws = new WebSocket(url);
          ws.onopen = () => console.log('connected');

          let messages = document.querySelector('#messages');
          ws.onmessage = event => {
            let m = document.createElement('p');
            m.innerText = event.data;
            messages.appendChild(m);
          };

          let message = document.querySelector('#message');
          message.addEventListener('keypress', e => {
            if (e.charCode !== 13) return;
            ws.send(message.value);
            message.value = '';
          });
        })();
      </script>
    </body>

    </html>
    ```

    你可以从上面的代码中看到，我们使用 `WebSocket.send()` 发送消息，使用 `WebSocket.onmessage` 侦听来自服务的消息。

3.  最后，更新 `connected` 事件处理程序以将 connected 事件广播到所有客户端，以便它们可以看到谁加入了聊天室。

    ```java

    // handle events: https://azure.github.io/azure-webpubsub/references/protocol-cloudevents#events
    app.post("/eventhandler", ctx -> {
        String event = ctx.header("ce-type");
        if ("azure.webpubsub.sys.connected".equals(event)) {
            String id = ctx.header("ce-userId");
            client.sendToAll(String.format("[SYSTEM] %s joined", id), WebPubSubContentType.TEXT_PLAIN);
        } else if ("azure.webpubsub.user.message".equals(event)) {
            String id = ctx.header("ce-userId");
            String message = ctx.body();
            client.sendToAll(String.format("[%s] %s", id, message), WebPubSubContentType.TEXT_PLAIN);
        }
        ctx.status(200);
    });

    ```

现在，使用以下命令运行服务器并打开多个浏览器实例，然后就可以相互聊天了。

```console
mvn compile & mvn package & mvn exec:java -Dexec.mainClass="com.webpubsub.tutorial.App" -Dexec.cleanupDaemonThreads=false -Dexec.args="'<connection_string>'"
```

可以在[此处][code-java]找到本教程的完整代码示例。

---

## <a name="next-steps"></a>后续步骤

本教程大致介绍了事件系统在 Azure Web PubSub 服务中的工作原理。

请查看其他教程，进一步深入了解如何使用该服务。

> [!div class="nextstepaction"]
> [了解更多 Azure Web PubSub 示例](https://aka.ms/awps/samples)


[code-js]: https://github.com/Azure/azure-webpubsub/tree/main/samples/javascript/chatapp/
[code-java]: https://github.com/Azure/azure-webpubsub/tree/main/samples/java/chatapp/
[code-csharp]: https://github.com/Azure/azure-webpubsub/tree/main/samples/csharp/chatapp/
