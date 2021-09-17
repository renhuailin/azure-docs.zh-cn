---
title: Azure SignalR 服务无服务器快速入门 - C#
description: 本快速入门介绍如何使用 C# 通过 Azure SignalR 服务和 Azure Functions 创建一个可显示 GitHub 星数的应用。
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.date: 06/09/2021
ms.author: zhshang
ms.openlocfilehash: 3a3fa958ac6ad1cb440f30b5c680ae3f9139d29a
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122444919"
---
# <a name="quickstart-create-an-app-showing-github-star-count-with-azure-functions-and-signalr-service-using-c"></a>快速入门：使用 C\# 通过 Azure Functions 和 SignalR 服务创建一个可显示 GitHub 星数的应用

使用 Azure SignalR 服务可以轻松地将实时功能添加到应用程序。 Azure Functions 是一个无服务器平台，可让你在不管理任何基础结构的情况下运行代码。 在本快速入门中，了解如何使用 C# 通过 SignalR 服务和 Azure Functions 生成一个无服务器应用程序，用于将消息广播到客户端。

> [!NOTE]
> 可以从 [GitHub](https://github.com/aspnet/AzureSignalR-samples/tree/main/samples/QuickStartServerless/csharp) 获取本文中提到的所有代码

## <a name="prerequisites"></a>必备条件

如果你还没有安装 Visual Studio Code，可以免费下载和使用它 (https://code.visualstudio.com/Download) )。

你还可以使用 [Azure Functions Core Tools](../azure-functions/functions-run-local.md?tabs=windows%2Ccsharp%2Cbash#v2) 在命令行（macOS、Windows 或 Linux）上运行本教程的代码。 此外，也可以在 [.NET Core SDK](https://dotnet.microsoft.com/download) 和你喜爱的代码编辑器上运行。

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/dotnet)。

遇到问题？ 尝试使用[故障排除指南](signalr-howto-troubleshoot-guide.md)或[通知我们](https://aka.ms/asrs/qscsharp)。

## <a name="log-in-to-azure-and-create-signalr-service-instance"></a>登录到 Azure 并创建 SignalR 服务实例

使用 Azure 帐户登录到 <https://portal.azure.com/> 的 Azure 门户。

遇到问题？ 尝试使用[故障排除指南](signalr-howto-troubleshoot-guide.md)或[通知我们](https://aka.ms/asrs/qscsharp)。

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

遇到问题？ 尝试使用[故障排除指南](signalr-howto-troubleshoot-guide.md)或[通知我们](https://aka.ms/asrs/qscsharp)。

## <a name="setup-and-run-the-azure-function-locally"></a>在本地安装和运行 Azure Functions

1. 确保已安装 Azure Functions Core Tools。 创建一个空目录并使用命令行导航到该目录。

    ```bash
    # Initialize a function project
    func init --worker-runtime dotnet
    
    # Add SignalR Service package reference to the project
    dotnet add package Microsoft.Azure.WebJobs.Extensions.SignalRService
    ```

2. 初始化项目后， 创建一个名为 Function.cs 的新文件。 将以下代码添加到 Function.cs。
   
    ```csharp
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Http;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Extensions.Http;
    using Microsoft.Azure.WebJobs.Extensions.SignalRService;
    using Newtonsoft.Json;
    
    namespace CSharp
    {
        public static class Function
        {
            private static HttpClient httpClient = new HttpClient();
    
            [FunctionName("index")]
            public static IActionResult Index([HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req, ExecutionContext context)
            {
                var path = Path.Combine(context.FunctionAppDirectory, "content", "index.html");
                return new ContentResult
                {
                    Content = File.ReadAllText(path),
                    ContentType = "text/html",
                };
            }
    
            [FunctionName("negotiate")]
            public static SignalRConnectionInfo Negotiate( 
                [HttpTrigger(AuthorizationLevel.Anonymous)] HttpRequest req,
                [SignalRConnectionInfo(HubName = "serverlessSample")] SignalRConnectionInfo connectionInfo)
            {
                return connectionInfo;
            }
    
            [FunctionName("broadcast")]
            public static async Task Broadcast([TimerTrigger("*/5 * * * * *")] TimerInfo myTimer,
            [SignalR(HubName = "serverlessSample")] IAsyncCollector<SignalRMessage> signalRMessages)
            {
                var request = new HttpRequestMessage(HttpMethod.Get, "https://api.github.com/repos/azure/azure-signalr");
                request.Headers.UserAgent.ParseAdd("Serverless");
                var response = await httpClient.SendAsync(request);
                var result = JsonConvert.DeserializeObject<GitResult>(await response.Content.ReadAsStringAsync());
                await signalRMessages.AddAsync(
                    new SignalRMessage
                    {
                        Target = "newMessage",
                        Arguments = new[] { $"Current star count of https://github.com/Azure/azure-signalr is: {result.StarCount}" }
                    });
            }
    
            private class GitResult
            {
                [JsonRequired]
                [JsonProperty("stargazers_count")]
                public string StarCount { get; set; }
            }
        }
    }
    ```
    这些代码有三个函数。 `Index` 用作客户端来获取网站。 `Negotiate` 用于为客户端获取访问令牌。 `Broadcast` 定期从 GitHub 获取星数，并向所有客户端广播消息。

3. 本示例的客户端界面是网页。 考虑到我们是在 `GetHomePage` 函数中从 `content/index.html` 读取 HTML 内容，因此需要在项目根目录下的 `content` 目录中创建一个新文件 `index.html`。 然后复制以下内容。
    ```html
    <html>
    
    <body>
      <h1>Azure SignalR Serverless Sample</h1>
      <div id="messages"></div>
      <script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.7/signalr.min.js"></script>
      <script>
        let messages = document.querySelector('#messages');
        const apiBaseUrl = window.location.origin;
        const connection = new signalR.HubConnectionBuilder()
            .withUrl(apiBaseUrl + '/api')
            .configureLogging(signalR.LogLevel.Information)
            .build();
          connection.on('newMessage', (message) => {
            document.getElementById("messages").innerHTML = message;
          });
    
          connection.start()
            .catch(console.error);
      </script>
    </body>
    
    </html>
    ```

4. 更新 `*.csproj` 以在生成输出文件夹中制作内容页面。

    ```html
    <ItemGroup>
      <None Update="content/index.html">
        <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
      </None>
    </ItemGroup>
    ```

5. 现在就快完成了。 最后一步是将 SignalR 服务的连接字符串设置为 Azure Functions 设置。

    1. 在打开 Azure门户的浏览器中，通过在门户顶部的搜索框中搜索先前部署的 SignalR 服务实例的名称，确认该实例已成功创建。 选择该实例以将其打开。

        ![搜索 SignalR 服务实例](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

    2. 选择“密钥”以查看 SignalR 服务实例的连接字符串。
    
        ![屏幕截图突出显示了主连接字符串。](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

    3. 复制主连接字符串。 然后执行以下命令。
    
        ```bash
        func settings add AzureSignalRConnectionString "<signalr-connection-string>"
        ```
    
6. 在本地运行 Azure Functions：

    ```bash
    func start
    ```

    在本地运行 Azure Functions 后， 使用浏览器访问 `http://localhost:7071/api/index`，随后可以看到当前的星数。 如果你在 GitHub 中进行星标或取消星标，星数会每隔几秒刷新一次。

    > [!NOTE]
    > SignalR 绑定需要 Azure 存储，但是在本地运行 Azure Functions 时，可以使用本地存储仿真器。
    > 如果收到一些类似 `There was an error performing a read operation on the Blob Storage Secret Repository. Please ensure the 'AzureWebJobsStorage' connection string is valid.` 的错误，则需要下载并启用[存储仿真器](../storage/common/storage-use-emulator.md)

遇到问题？ 尝试使用[故障排除指南](signalr-howto-troubleshoot-guide.md)或[通知我们](https://aka.ms/asrs/qscsharp)

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

遇到问题？ 尝试使用[故障排除指南](signalr-howto-troubleshoot-guide.md)或[通知我们](https://aka.ms/asrs/qspython)。

## <a name="next-steps"></a>后续步骤

在本快速入门中，你在本地生成并运行了一个实时的无服务器应用程序。 详细了解了如何将 SignalR 服务绑定用于 Azure Functions。
接着，详细了解了如何通过 SignalR 服务在客户端与 Azure Functions 之间进行双向通信。

> [!div class="nextstepaction"]
> [Azure Functions 的 SignalR Service 绑定](../azure-functions/functions-bindings-signalr-service.md)

> [!div class="nextstepaction"]
> [无服务器双向通信](https://github.com/aspnet/AzureSignalR-samples/tree/main/samples/BidirectionChat)

> [!div class="nextstepaction"]
> [使用 Visual Studio 部署 Azure 函数应用](../azure-functions/functions-develop-vs.md#publish-to-azure)

