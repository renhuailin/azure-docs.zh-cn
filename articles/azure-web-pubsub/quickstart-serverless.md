---
title: 教程 - 构建带客户端身份验证的无服务器实时聊天应用
description: 演示如何使用 Azure Web PubSub 服务和 Azure Functions 构建带客户端身份验证的无服务器聊天应用的教程。
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: tutorial
ms.date: 03/11/2021
ms.openlocfilehash: 787a0e4990988f35ca8e2c98eab1d9c77bea9c1b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128576328"
---
# <a name="tutorial-create-a-serverless-real-time-chat-app-with-azure-functions-and-azure-web-pubsub-service"></a>教程：使用 Azure Functions 和 Azure Web PubSub 服务创建无服务器实时聊天应用

Azure Web PubSub 服务可帮助你轻松地使用 Websocket 和发布-订阅模式生成实时消息传递 Web 应用程序。 Azure Functions 是一个无服务器平台，可让你在不管理任何基础结构的情况下运行代码。 本教程介绍如何使用 Azure Web PubSub 服务和 Azure Functions 来生成具有实时消息传递和发布-订阅模式的无服务器应用程序。

在本教程中，你将了解如何：

> [!div class="checklist"]
> * 构建无服务器实时聊天应用
> * 使用 Web PubSub 函数触发器绑定和输出绑定
> * 将函数部署到 Azure 函数应用
> * 配置 Azure 身份验证
> * 配置 Web PubSub 事件处理程序以将事件和消息路由到应用程序

## <a name="prerequisites"></a>先决条件

# <a name="javascript"></a>[JavaScript](#tab/javascript)

* 代码编辑器，如 [Visual Studio Code](https://code.visualstudio.com/)

* [Node.js](https://nodejs.org/en/download/) 版本 10.x。
   > [!NOTE]
   > 有关支持的 Node.js 版本的详细信息，请参阅 [Azure Functions 运行时版本文档](../azure-functions/functions-versions.md#languages)。
* [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing)（首选 v3 或更高版本），用于在本地运行 Azure 函数应用并部署到 Azure。

* [Azure 命令行接口 (Azure CLI)](/cli/azure)，用于管理 Azure 资源。

* （可选）[ngrok](https://ngrok.com/download) 将本地函数公开为 Web PubSub 服务的事件处理程序。 此可选选项仅可用于在本地运行函数应用。

# <a name="c"></a>[C#](#tab/csharp)

* 代码编辑器，如 [Visual Studio Code](https://code.visualstudio.com/)。

* [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing)（首选 v3 或更高版本），用于在本地运行 Azure 函数应用并部署到 Azure。

* [Azure 命令行接口 (Azure CLI)](/cli/azure)，用于管理 Azure 资源。

* （可选）[ngrok](https://ngrok.com/download) 将本地函数公开为 Web PubSub 服务的事件处理程序。 此可选选项仅可用于在本地运行函数应用。

---

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [create-instance-portal](includes/create-instance-portal.md)]

## <a name="create-the-functions"></a>创建函数

1. 确保已安装 [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing)。 然后为项目创建一个空目录。 在此工作目录下运行命令。

    # <a name="javascript"></a>[JavaScript](#tab/javascript)
    ```bash
    func init --worker-runtime javascript
    ```

    # <a name="c"></a>[C#](#tab/csharp)
    ```bash
    func init --worker-runtime dotnet
    ```

1. 显式安装 `Microsoft.Azure.WebJobs.Extensions.WebPubSub` 函数扩展包。

   a. 删除 `extensionBundle` 中的 `host.json` 节，以便在下一步中安装特定扩展包。 或者让 host json 变得像下面这样简单。
    ```json
    {
        "version": "2.0"
    }
    ```
   b. 运行命令以安装特定函数扩展包。
    ```bash
    func extensions install --package Microsoft.Azure.WebJobs.Extensions.WebPubSub --version 1.0.0-beta.3
    ```

1. 创建 `index` 函数，为客户端读取和托管静态网页。
    ```bash
    func new -n index -t HttpTrigger
    ```
   # <a name="javascript"></a>[JavaScript](#tab/javascript)
   - 更新 `index/function.json` 并复制以下 json 代码。
        ```json
        {
            "bindings": [
                {
                    "authLevel": "anonymous",
                    "type": "httpTrigger",
                    "direction": "in",
                    "name": "req",
                    "methods": [
                      "get",
                      "post"
                    ]
                },
                {
                    "type": "http",
                    "direction": "out",
                    "name": "res"
                }
            ]
        }
        ```
   - 更新 `index/index.js` 并复制以下代码。
        ```js
        var fs = require('fs');
        module.exports = function (context, req) {
            fs.readFile('index.html', 'utf8', function (err, data) {
                if (err) {
                    console.log(err);
                    context.done(err);
                }
                context.res = {
                    status: 200,
                    headers: {
                        'Content-Type': 'text/html'
                    },
                    body: data
                };
                context.done();
            });
        }
        ```

   # <a name="c"></a>[C#](#tab/csharp)
   - 更新 `index.cs` 并将 `Run` 函数替换为以下代码。
        ```c#
        [FunctionName("index")]
        public static IActionResult Run([HttpTrigger(AuthorizationLevel.Anonymous)] HttpRequest req)
        {
            return new ContentResult
            {
                Content = File.ReadAllText("index.html"),
                ContentType = "text/html",
            };
        }
        ```

1. 创建 `negotiate` 函数，以帮助客户端使用访问令牌获取服务连接 URL。
    ```bash
    func new -n negotiate -t HttpTrigger
    ```
    > [!NOTE]
    > 本示例使用 [AAD](../app-service/configure-authentication-user-identities.md) 用户标识标头 `x-ms-client-principal-name` 来检索 `userId`。 这在本地函数中不起作用。 在本地运行时，可以将其清空或改为使用其他方式获取或生成 `userId`。 例如，让客户端键入用户名，并在调用 `negotiate` 函数以获取服务连接 URL 时将其传递到 `?user={$username}` 之类的查询中。 在 `negotiate` 函数中，将 `userId` 设置为值 `{query.user}`。
    
    # <a name="javascript"></a>[JavaScript](#tab/javascript)
   - 更新 `negotiate/function.json` 并复制以下 json 代码。
        ```json
        {
            "bindings": [
                {
                    "authLevel": "anonymous",
                    "type": "httpTrigger",
                    "direction": "in",
                    "name": "req"
                },
                {
                    "type": "http",
                    "direction": "out",
                    "name": "res"
                },
                {
                    "type": "webPubSubConnection",
                    "name": "connection",
                    "hub": "simplechat",
                    "userId": "{headers.x-ms-client-principal-name}",
                    "direction": "in"
                }
            ]
        }
        ```
   - 更新 `negotiate/index.js` 并复制以下代码。
        ```js
        module.exports = function (context, req, connection) {
            context.res = { body: connection };
            context.done();
        };
        ```
   # <a name="c"></a>[C#](#tab/csharp)
   - 更新 `negotiate.cs` 并将 `Run` 函数替换为以下代码。
        ```c#
        [FunctionName("negotiate")]
        public static WebPubSubConnection Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req,
            [WebPubSubConnection(Hub = "simplechat", UserId = "{headers.x-ms-client-principal-name}")] WebPubSubConnection connection,
            ILogger log)
        {
            log.LogInformation("Connecting...");
            return connection;
        }
        ```

2. 创建 `message` 函数以通过服务广播客户端消息。
   ```bash
   func new -n message -t HttpTrigger
   ```

   > [!NOTE]
   > 此函数实际上是使用 `WebPubSubTrigger`。 但是，由于该服务仍处于预览阶段，因此 `WebPubSubTrigger` 未集成到函数模板中。 我们使用 `HttpTrigger` 来初始化函数模板并在代码中更改触发器类型。

   # <a name="javascript"></a>[JavaScript](#tab/javascript)
   - 更新 `message/function.json` 并复制以下 json 代码。
        ```json
        {
            "bindings": [
                {
                    "type": "webPubSubTrigger",
                    "direction": "in",
                    "name": "message",
                    "dataType": "binary",
                    "hub": "simplechat",
                    "eventName": "message",
                    "eventType": "user"
                },
                {
                    "type": "webPubSub",
                    "name": "webPubSubEvent",
                    "hub": "simplechat",
                    "direction": "out"
                }
            ]
        }
        ```
   - 更新 `message/index.js` 并复制以下代码。
        ```js
        module.exports = async function (context, message) {
            context.bindings.webPubSubEvent = {
                "operationKind": "sendToAll",
                "message": `[${context.bindingData.connectionContext.userId}] ${message}`,
                "dataType": context.bindingData.dataType
            };
            // MessageResponse directly return to caller
            var response = { 
                "message": '[SYSTEM] ack.',
                "dataType" : "text"
            };
            return response;
        };
        ```

   # <a name="c"></a>[C#](#tab/csharp)
   - 更新 `message.cs` 并将 `Run` 函数替换为以下代码。
        ```c#
        [FunctionName("message")]
        public static async Task<MessageResponse> Run(
            [WebPubSubTrigger(WebPubSubEventType.User, "message")] ConnectionContext context,
            BinaryData message,
            MessageDataType dataType,
            [WebPubSub(Hub = "simplechat")] IAsyncCollector<WebPubSubOperation> operations)
        {
            await operations.AddAsync(new SendToAll
            {
                Message = BinaryData.FromString($"[{context.UserId}] {message.ToString()}"),
                DataType = dataType
            });
            return new MessageResponse
            {
                Message = BinaryData.FromString("[SYSTEM] ack"),
                DataType = MessageDataType.Text
            };
        }
        ```

3. 在项目根文件夹中添加客户端单页 `index.html`，并复制如下所示的内容。
    ```html
    <html>
        <body>
            <h1>Azure Web PubSub Serverless Chat App</h1>
            <div id="login"></div>
            <p></p>
            <input id="message" placeholder="Type to chat...">
            <div id="messages"></div>
            <script>
                (async function () {
                    let authenticated = window.location.href.includes('?authenticated=true');
                    if (!authenticated) {
                        // auth
                        let login = document.querySelector("#login");
                        let link = document.createElement('a');
                        link.href = `${window.location.origin}/.auth/login/aad?post_login_redirect_url=/api/index?authenticated=true`;
                        link.text = "login";
                        login.appendChild(link);
                    }
                    else {
                        // negotiate
                        let messages = document.querySelector('#messages');
                        let res = await fetch(`${window.location.origin}/api/negotiate`, {
                            credentials: "include"
                        });
                        let url = await res.json();
                        // connect
                        let ws = new WebSocket(url.url);
                        ws.onopen = () => console.log('connected');
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
                    }
                })();
            </script>
        </body>
    </html>
    ```

    # <a name="javascript"></a>[JavaScript](#tab/javascript)

    # <a name="c"></a>[C#](#tab/csharp)
    由于 C# 项目会将文件编译到不同的输出文件夹，因此你需要更新 `*.csproj`，使内容页与其保持一致。
    ```xml
    <ItemGroup>
        <None Update="index.html">
            <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
        </None>
    </ItemGroup>
    ``

## Create and Deploy the Azure Function App

Before you can deploy your function code to Azure, you need to create 3 resources:
* A resource group, which is a logical container for related resources.
* A storage account, which is used to maintain state and other information about your functions.
* A function app, which provides the environment for executing your function code. A function app maps to your local function project and lets you group functions as a logical unit for easier management, deployment and sharing of resources.

Use the following commands to create these items. 

1. If you haven't done so already, sign in to Azure:

    ```bash
    az login
    ```

1. 创建资源组，或者可以通过重用某个 Azure Web PubSub 服务来跳过此步骤：

    ```bash
    az group create -n WebPubSubFunction -l <REGION>
    ```

1. 在资源组和区域中创建常规用途存储帐户：

    ```bash
    az storage account create -n <STORAGE_NAME> -l <REGION> -g WebPubSubFunction
    ```

1. 在 Azure 中创建函数应用：

    # <a name="javascript"></a>[JavaScript](#tab/javascript)

    ```bash
    az functionapp create --resource-group WebPubSubFunction --consumption-plan-location <REGION> --runtime node --runtime-version 12 --functions-version 3 --name <FUNCIONAPP_NAME> --storage-account <STORAGE_NAME>
    ```

    # <a name="c"></a>[C#](#tab/csharp)

    ```bash
    az functionapp create --resource-group WebPubSubFunction --consumption-plan-location <REGION> --runtime dotnet --functions-version 3 --name <FUNCIONAPP_NAME> --storage-account <STORAGE_NAME>
    ```

1. 将函数项目部署到 Azure：

    在 Azure 中成功创建函数应用后，便可以使用 [func azure functionapp publish](./../azure-functions/functions-run-local.md) 命令部署本地函数项目。

    ```bash
    func azure functionapp publish <FUNCIONAPP_NAME>
    ```
1. 配置函数应用的 `WebPubSubConnectionString`：

   首先，从“Azure 门户”中找到你的 Web PubSub 资源，并复制出“密钥”下的连接字符串 。 然后，导航到“Azure 门户” -> “设置” -> “配置”中的函数应用设置  。 并在“应用程序设置”下添加一个新项目，其名称等于 `WebPubSubConnectionString`，值为你的 Web PubSub 资源连接字符串。

## <a name="configure-the-web-pubsub-service-event-handler"></a>配置 Web PubSub 服务 `Event Handler`

本示例使用 `WebPubSubTrigger` 侦听服务上游消息请求。 因此 Web PubSub 需要知道函数的终结点信息才能发送目标客户端请求。 并且 Azure 函数应用需要一个系统密钥，以确保有关特定于扩展的 Webhook 方法的安全性。 在上一步中，使用 `message` 函数部署函数应用后，我们可以获取系统密钥。

转到“Azure 门户”-> 找到你的函数应用资源 ->“应用密钥” -> “系统密钥” -> “`webpubsub_extension`”   。 将该值复制为 `<APP_KEY>`。

:::image type="content" source="media/quickstart-serverless/func-keys.png" alt-text="获取函数系统密钥的屏幕截图。":::

在 Azure Web PubSub 服务中设置 `Event Handler`。 转到“Azure 门户”>“查找 Web PubSub 资源”>“设置” 。 将新的中心设置映射添加到使用中的一个函数，如下所示。 将 `<FUNCTIONAPP_NAME>` 和 `<APP_KEY>` 替换为你自己的值。

   - 中心名称：`simplechat`
   - URL 模板： https://<FUNCTIONAPP_NAME>.azurewebsites.net/runtime/webhooks/webpubsub?code=<APP_KEY>
   - 用户事件模式：*
   - 系统事件：-（本示例中无需配置）

:::image type="content" source="media/quickstart-serverless/set-event-handler.png" alt-text="设置事件处理程序的屏幕截图。":::

> [!NOTE]
> 如果在本地运行函数： 可以在函数启动后通过命令 `ngrok http 7071` 使用 [ngrok](https://ngrok.com/download) 公开函数 URL。 并使用 URL `https://<NGROK_ID>.ngrok.io/runtime/webhooks/webpubsub` 配置 Web PubSub 服务 `Event Handler`。 

## <a name="configure-to-enable-client-authentication"></a>配置以启用客户端身份验证

转到“Azure 门户”-> 找到你的函数应用资源 ->“身份验证” 。 单击“`Add identity provider`”  。 将“应用服务身份验证设置”设置为“允许未经身份验证的访问”，以便匿名用户可以在重定向以进行身份验证之前访问你的客户端索引页 。 然后“保存”。

此处选择 `Microsoft` 作为标识提供者，它将在 `negotiate` 函数中使用 `x-ms-client-principal-name` 作为 `userId`。 此外，可以按照以下链接配置其他标识提供者，并且不要忘记相应地更新 `negotiate` 函数中的 `userId` 值。

* [Microsoft(Azure AD)](../app-service/configure-authentication-provider-aad.md)
* [Facebook](../app-service/configure-authentication-provider-facebook.md)
* [Google](../app-service/configure-authentication-provider-google.md)
* [Twitter](../app-service/configure-authentication-provider-twitter.md)

## <a name="try-the-application"></a>尝试运行应用程序

现在，可以从你的函数应用测试页面：`https://<FUNCTIONAPP_NAME>.azurewebsites.net/api/index`。 请参阅下面的快照。
1. 单击 `login` 进行身份验证。
2. 在输入框中键入消息进行聊天。

在消息函数中，我们会将调用方的消息广播给所有客户端，并向调用方返回消息 `[SYSTEM] ack`。 我们可以在下面的示例聊天快照中了解，前 4 条消息来自当前客户端，后 2 条消息来自其他客户端。

:::image type="content" source="media/quickstart-serverless/chat-sample.png" alt-text="聊天示例的屏幕截图。":::

## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用此应用，请按照以下步骤删除本文档中创建的所有资源，以免产生任何费用：

1. 在 Azure 门户的最左侧选择“资源组”，，然后选择创建的资源组。 可以改用搜索框按名称查找资源组。

1. 在打开的窗口中选择资源组，然后选择“删除资源组”。

1. 在新窗口中键入要删除的资源组的名称，然后选择“删除”。

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何运行无服务器聊天应用程序。 现在，可以开始构建自己的应用程序。 

> [!div class="nextstepaction"]
> [适用于 Azure Functions 的 Azure Web PubSub 绑定](https://azure.github.io/azure-webpubsub/references/functions-bindings)

> [!div class="nextstepaction"]
> [快速入门：使用 Azure Web PubSub 创建简单的聊天室](https://azure.github.io/azure-webpubsub/getting-started/create-a-chat-app/js-handle-events)

> [!div class="nextstepaction"]
> [了解更多 Azure Web PubSub 示例](https://github.com/Azure/azure-webpubsub/tree/main/samples)
