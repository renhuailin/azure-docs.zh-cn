---
title: 教程 - 使用 Azure Web PubSub 服务和 Azure Functions 创建无服务器通知应用
description: 演示如何使用 Azure Web PubSub 服务和 Azure Functions 生成无服务器通知应用程序的教程。
author: JialinXin
ms.author: jixin
ms.service: azure-web-pubsub
ms.topic: tutorial
ms.date: 08/24/2021
ms.openlocfilehash: 98bb95800596ac54bae01efb501c6016767ca650
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124785090"
---
# <a name="tutorial-create-a-serverless-notification-app-with-azure-functions-and-azure-web-pubsub-service"></a>教程：使用 Azure Functions 和 Azure Web PubSub 服务创建无服务器通知应用

借助 Azure Web PubSub 服务，你可以使用 WebSocket 生成实时消息传递 Web 应用程序。 Azure Functions 是一个无服务器平台，可让你在不管理任何基础结构的情况下运行代码。 本教程介绍如何使用 Azure Web PubSub 服务和 Azure Functions，在通知方案下生成具有实时消息传递功能的无服务器应用程序。 

在本教程中，你将了解如何：

> [!div class="checklist"]
> * 生成无服务器通知应用
> * 使用 Web PubSub 函数输入和输出绑定
> * 在本地运行示例函数
> * 将函数部署到 Azure 函数应用

## <a name="prerequisites"></a>先决条件

# <a name="javascript"></a>[JavaScript](#tab/javascript)

* 代码编辑器，如 [Visual Studio Code](https://code.visualstudio.com/)

* [Node.js](https://nodejs.org/en/download/) 版本 10.x。
   > [!NOTE]
   > 有关支持的 Node.js 版本的详细信息，请参阅 [Azure Functions 运行时版本文档](../azure-functions/functions-versions.md#languages)。

* [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing)（首选 v3 或更高版本），用于在本地运行 Azure 函数应用并部署到 Azure。

* [Azure 命令行接口 (Azure CLI)](/cli/azure)，用于管理 Azure 资源。

# <a name="c"></a>[C#](#tab/csharp)

* 代码编辑器，如 [Visual Studio Code](https://code.visualstudio.com/)。

* [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing)（首选 v3 或更高版本），用于在本地运行 Azure 函数应用并部署到 Azure。

* [Azure 命令行接口 (Azure CLI)](/cli/azure)，用于管理 Azure 资源。

---

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [create-instance-portal](includes/create-instance-portal.md)]

## <a name="create-and-run-the-functions-locally"></a>在本地创建和运行函数

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

2. 创建 `negotiate` 函数，以帮助客户端使用访问令牌获取服务连接 URL。
    ```bash
    func new -n negotiate -t HttpTrigger
    ```
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
                    "hub": "notification",
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
            [WebPubSubConnection(Hub = "notification")] WebPubSubConnection connection,
            ILogger log)
        {
            log.LogInformation("Connecting...");

            return connection;
        }
        ```

3. 创建 `notification` 函数，以使用 `TimerTrigger` 生成通知。
   ```bash
    func new -n notification -t TimerTrigger
    ```
    # <a name="javascript"></a>[JavaScript](#tab/javascript)
   - 更新 `notification/function.json` 并复制以下 json 代码。
        ```json
        {
            "bindings": [
                {
                "name": "myTimer",
                "type": "timerTrigger",
                "direction": "in",
                "schedule": "*/10 * * * * *"
                },
                {
                "type": "webPubSub",
                "name": "webPubSubOperation",
                "hub": "notification",
                "direction": "out"
                }
            ]
        }
        ```
   - 更新 `notification/index.js` 并复制以下代码。
        ```js
        module.exports = function (context, myTimer) {
            context.bindings.webPubSubOperation = {
                "operationKind": "sendToAll",
                "message": `[DateTime: ${new Date()}] Temperature: ${getValue(22, 1)}\xB0C, Humidity: ${getValue(40, 2)}%`,
                "dataType": "text"
            }
            context.done();
        };

        function getValue(baseNum, floatNum) {
            return (baseNum + 2 * floatNum * (Math.random() - 0.5)).toFixed(3);
        }
        ```
   # <a name="c"></a>[C#](#tab/csharp)
   - 更新 `notification.cs` 并将 `Run` 函数替换为以下代码。
        ```c#
        [FunctionName("notification")]
        public static async Task Run([TimerTrigger("*/10 * * * * *")]TimerInfo myTimer, ILogger log,
            [WebPubSub(Hub = "notification")] IAsyncCollector<WebPubSubOperation> operations)
        {
            await operations.AddAsync(new SendToAll
            {
                Message = BinaryData.FromString($"[DateTime: {DateTime.Now}] Temperature: {GetValue(23, 1)}{'\xB0'}C, Humidity: {GetValue(40, 2)}%"),
                DataType = MessageDataType.Text
            });
        }

        private static string GetValue(double baseNum, double floatNum)
        {
            var rng = new Random();
            var value = baseNum + floatNum * 2 * (rng.NextDouble() - 0.5);
            return value.ToString("0.000");
        }
        ``` 

4. 在项目根文件夹中添加客户端单页 `index.html`，并复制如下所示的内容。
    ```html
    <html>
        <body>
        <h1>Azure Web PubSub Notification</h1>
        <div id="messages"></div>
        <script>
            (async function () {
                let messages = document.querySelector('#messages');
                let res = await fetch(`${window.location.origin}/api/negotiate`);
                let url = await res.json();
                let ws = new WebSocket(url.url);
                ws.onopen = () => console.log('connected');

                ws.onmessage = event => {
                    let m = document.createElement('p');
                    m.innerText = event.data;
                    messages.appendChild(m);
                };
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
    ```

5. 配置和运行 Azure 函数应用

    - 在浏览器中，打开“Azure 门户”，确认已成功创建前面部署的 Web PubSub 服务实例。 导航到该实例。
    - 选择“密钥”并复制连接字符串。

    :::image type="content" source="media/quickstart-serverless/copy-connection-string.png" alt-text="复制 Web PubSub 连接字符串的屏幕截图。":::

    在函数文件夹中运行以下命令，以设置服务连接字符串。 根据需要将 `<connection-string`> 替换为你的值。

    ```bash
    func settings add WebPubSubConnectionString "<connection-string>"
    ```

    > [!NOTE]
    > 示例中使用的 `TimerTrigger` 依赖于 Azure 存储，但是在本地运行 Azure 函数应用时，可以使用本地存储模拟器。 如果收到一些类似 `There was an error performing a read operation on the Blob Storage Secret Repository. Please ensure the 'AzureWebJobsStorage' connection string is valid.` 的错误，则需要下载并启用[存储模拟器](../storage/common/storage-use-emulator.md)。

    现在，可以通过以下命令运行本地函数。

    ```bash
    func start
    ```

    若要检查正在运行的日志，可以通过访问 `https://localhost:7071/api/index` 来访问本地主机静态页面。

## <a name="deploy-function-app-to-azure"></a>将函数应用部署到 Azure

在将函数代码部署到 Azure 之前，需要创建三个资源：
* 一个资源组：相关资源的逻辑容器。
* 一个存储帐户：用于维护有关函数的状态和其他信息。
* 一个函数应用：提供用于执行函数代码的环境。 函数应用映射到本地函数项目，并允许你将函数分组为一个逻辑单元，以便更轻松地管理、部署和共享资源。

使用以下命令创建这些项。 

1. 请登录到 Azure（如果尚未这样做）：

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

    在 Azure 中成功创建函数应用后，便可以使用 [func azure functionapp publish](../azure-functions/functions-run-local.md) 命令部署本地函数项目。

    ```bash
    func azure functionapp publish <FUNCIONAPP_NAME> --publish-local-settings
    ```

    > [!NOTE]
    > 在这里，我们将本地设置 `local.settings.json` 与命令参数 `--publish-local-settings` 一起部署。 如果你使用的是 Microsoft Azure 存储模拟器，可以在出现以下提示消息后键入 `no`，跳过在 Azure 上覆盖此值的步骤：`App setting AzureWebJobsStorage is different between azure and local.settings.json, Would you like to overwrite value in azure? [yes/no/show]`。 除此之外，你还可以在“Azure 门户” -> “设置” -> “配置”中更新函数应用设置。

1. 现在，你可以通过导航到 URL (`https://<FUNCIONAPP_NAME>.azurewebsites.net/api/index`)，从 Azure 函数应用检查你的站点。

## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用此应用，请按照以下步骤删除本文档中创建的所有资源，以免产生任何费用：

1. 在 Azure 门户的最左侧选择“资源组”，，然后选择创建的资源组。 可以改用搜索框按名称查找资源组。

1. 在打开的窗口中选择资源组，然后选择“删除资源组”。

1. 在新窗口中键入要删除的资源组的名称，然后选择“删除”。

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何运行无服务器聊天应用程序。 现在，可以开始构建自己的应用程序。 

> [!div class="nextstepaction"]
> [教程：使用 Azure Web PubSub 创建简单的聊天室](https://azure.github.io/azure-webpubsub/getting-started/create-a-chat-app/js-handle-events)

> [!div class="nextstepaction"]
> [适用于 Azure Functions 的 Azure Web PubSub 绑定](https://azure.github.io/azure-webpubsub/references/functions-bindings)

> [!div class="nextstepaction"]
> [了解更多 Azure Web PubSub 示例](https://github.com/Azure/azure-webpubsub/tree/main/samples)