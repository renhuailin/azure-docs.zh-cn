---
title: Azure SignalR 服务无服务器快速入门 - Python
description: 有关如何使用 Azure SignalR 服务和 Azure Functions 通过 Python 创建显示 GitHub 星数的应用的快速入门。
author: anthonychu
ms.author: antchu
ms.date: 06/09/2021
ms.topic: quickstart
ms.service: signalr
ms.devlang: python
ms.custom:
- devx-track-python
- mode-api
ms.openlocfilehash: 46c15f932f55883be66745d415820767089ae0f1
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2021
ms.locfileid: "112461976"
---
# <a name="quickstart-create-an-app-showing-github-star-count-with-azure-functions-and-signalr-service-using-python"></a>快速入门：使用 Azure Functions 和 SignalR 服务通过 Python 创建显示 GitHub 星数的应用

使用 Azure SignalR 服务可以轻松地将实时功能添加到应用程序。 Azure Functions 是一个无服务器平台，可让你在不管理任何基础结构的情况下运行代码。 本快速入门介绍如何使用 SignalR 服务和 Azure Functions 通过 Python 构建无服务器应用程序，以将消息广播到客户端。

> [!NOTE]
> 可以从 [GitHub](https://github.com/aspnet/AzureSignalR-samples/tree/main/samples/QuickStartServerless/python) 获取本文中提到的所有代码

## <a name="prerequisites"></a>先决条件

本快速入门可以在 macOS、Windows 或 Linux 上运行。

确保已安装了代码编辑器，例如 [Visual Studio Code](https://code.visualstudio.com/)。

安装 [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing)（版本 2.7.1505 或更高版本），以便在本地运行 Python Azure Functions 应用。

Azure Functions 需要 [Python 3.6+](https://www.python.org/downloads/)。 （请参阅[支持的 Python 版本](../azure-functions/functions-reference-python.md#python-version)）

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

遇到问题？ 尝试使用[故障排除指南](signalr-howto-troubleshoot-guide.md)或[通知我们](https://aka.ms/asrs/qspython)。

## <a name="log-in-to-azure"></a>登录 Azure

使用 Azure 帐户登录到 <https://portal.azure.com/> 的 Azure 门户。

遇到问题？ 尝试使用[故障排除指南](signalr-howto-troubleshoot-guide.md)或[通知我们](https://aka.ms/asrs/qspython)。

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

遇到问题？ 尝试使用[故障排除指南](signalr-howto-troubleshoot-guide.md)或[通知我们](https://aka.ms/asrs/qspython)。


## <a name="setup-and-run-the-azure-function-locally"></a>在本地安装和运行 Azure Functions

1. 确保已安装 Azure Functions Core Tools。 创建一个空白目录并使用命令行导航到该目录。

    ```bash
    # Initialize a function project
    func init --worker-runtime python
    ```

2. 初始化项目后，需要创建函数。 在此示例中，我们需要创建 3 个函数。

    1. 运行以下命令以创建 `index` 函数，该函数将托管客户端网页。

        ```bash
        func new -n index -t HttpTrigger
        ```
        
        打开 `index/__init__.py` 并复制以下代码。

        ```javascript
        import os
    
        import azure.functions as func
        
        
        def main(req: func.HttpRequest) -> func.HttpResponse:
            f = open(os.path.dirname(os.path.realpath(__file__)) + '/../content/index.html')
            return func.HttpResponse(f.read(), mimetype='text/html')
        ```
    
    2. 为客户端创建 `negotiate` 函数以获取访问令牌。
    
        ```bash
        func new -n negotiate -t SignalRNegotiateHTTPTrigger
        ```
        
        打开 `negotiate/function.json` 并复制以下 json 代码：
    
        ```json
        {
          "scriptFile": "__init__.py",
          "bindings": [
            {
              "authLevel": "function",
              "type": "httpTrigger",
              "direction": "in",
              "name": "req",
              "methods": [
                "post"
              ]
            },
            {
              "type": "http",
              "direction": "out",
              "name": "$return"
            },
            {
              "type": "signalRConnectionInfo",
              "name": "connectionInfo",
              "hubName": "serverless",
              "connectionStringSetting": "AzureSignalRConnectionString",
              "direction": "in"
            }
          ]
        }
        ```

        然后打开 `negotiate/__init__.py` 并复制以下代码：

        ```python
        import azure.functions as func
    
        
        def main(req: func.HttpRequest, connectionInfo) -> func.HttpResponse:
            return func.HttpResponse(connectionInfo)
        ```
    
    3. 创建 `broadcast` 函数，以将消息广播到所有客户端。 在此示例中，我们使用时间触发器来定期广播消息。
    
        ```bash
        func new -n broadcast -t TimerTrigger
        # install requests
        pip install requests
        ```
    
        打开 `broadcast/function.json` 并复制以下代码。
    
        ```json
        {
          "scriptFile": "__init__.py",
          "bindings": [
            {
              "name": "myTimer",
              "type": "timerTrigger",
              "direction": "in",
              "schedule": "*/5 * * * * *"
            },
            {
              "type": "signalR",
              "name": "signalRMessages",
              "hubName": "serverless",
              "connectionStringSetting": "AzureSignalRConnectionString",
              "direction": "out"
            }
          ]
        }
        ```
    
        打开 `broadcast/__init__.py` 并复制以下代码。
    
        ```python
        import requests
        import json
        
        import azure.functions as func
        
        
        def main(myTimer: func.TimerRequest, signalRMessages: func.Out[str]) -> None:
            headers = {'User-Agent': 'serverless'}
            res = requests.get('https://api.github.com/repos/azure/azure-signalr', headers=headers)
            jres = res.json()
        
            signalRMessages.set(json.dumps({
                'target': 'newMessage',
                'arguments': [ 'Current star count of https://github.com/Azure/azure-signalr is: ' + str(jres['stargazers_count']) ]
            }))
        ```

3. 此示例的客户端界面为网页。 考虑到我们在 `index` 函数中从 `content/index.html` 读取 HTML 内容，因此要在 `content` 目录中创建一个新文件 `index.html`。 然后复制以下内容。

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
    
4. 现在即将完成了。 最后一步是将 SignalR 服务的连接字符串设置为 Azure Functions 设置。

    1. 在打开 Azure门户的浏览器中，通过在门户顶部的搜索框中搜索先前部署的 SignalR 服务实例的名称，确认该实例已成功创建。 选择该实例以将其打开。

        ![搜索 SignalR 服务实例](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

    1. 选择“密钥”以查看 SignalR 服务实例的连接字符串。
    
        ![屏幕截图突出显示了主连接字符串。](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

    1. 复制主连接字符串。 然后执行以下命令。
    
        ```bash
        func settings add AzureSignalRConnectionString '<signalr-connection-string>'
        ```
    
5. 在本地运行 Azure Functions：

    ```bash
    func start
    ```

    在本地运行 Azure Functions 后， 使用浏览器访问 `http://localhost:7071/api/index`，随后可以查看当前星数。 如果在 GitHub 中进行星标或取消星标，星数会每隔几秒刷新一次。

    > [!NOTE]
    > SignalR 绑定需要 Azure 存储，但是在本地运行 Azure Functions 时，可以使用本地存储仿真器。
    > 如果收到类似`There was an error performing a read operation on the Blob Storage Secret Repository. Please ensure the 'AzureWebJobsStorage' connection string is valid.`的错误，则需要下载并启用[存储仿真器](../storage/common/storage-use-emulator.md)

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

遇到问题？ 尝试使用[故障排除指南](signalr-howto-troubleshoot-guide.md)或[通知我们](https://aka.ms/asrs/qspython)。

## <a name="next-steps"></a>后续步骤

在本快速入门中，你在本地生成并运行了一个实时无服务器应用程序。 详细了解如何将 SignalR 服务绑定用于 Azure Functions。
接下来，详细了解如何通过 SignalR 服务在客户端与 Azure Functions 之间进行双向通信。

> [!div class="nextstepaction"]
> [Azure Functions 的 SignalR Service 绑定](../azure-functions/functions-bindings-signalr-service.md)

> [!div class="nextstepaction"]
> [无服务器双向通信](https://github.com/aspnet/AzureSignalR-samples/tree/main/samples/BidirectionChat)

> [!div class="nextstepaction"]
> [使用 VS Code 部署 Azure Functions](/azure/developer/javascript/tutorial-vscode-serverless-node-01)