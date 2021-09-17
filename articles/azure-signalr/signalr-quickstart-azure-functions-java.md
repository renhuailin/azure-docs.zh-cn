---
title: 通过 Java 使用 Azure Functions 和 SignalR 服务创建聊天室
description: 介绍如何使用 Java 通过 Azure SignalR 服务和 Azure Functions 来创建一个应用（用于显示 GitHub 星数）的快如入门。
author: sffamily
ms.author: zhshang
ms.date: 06/09/2021
ms.topic: quickstart
ms.service: signalr
ms.devlang: java
ms.custom:
- devx-track-java
- mode-api
ms.openlocfilehash: c5f363ce78724ed569ec3ab24cd6c1fa640954e2
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122444427"
---
# <a name="quickstart-use-java-to-create-an-app-showing-github-star-count-with-azure-functions-and-signalr-service"></a>快速入门：使用 Java 通过 Azure SignalR 服务和 Azure Functions 来创建一个应用，用于显示 GitHub 星数

Azure SignalR 服务可让你轻松地向应用程序添加实时功能，而 Azure Functions 是一个无服务器平台，可让你无需管理任何基础结构即可运行代码。 在本快速入门中，了解如何使用 Java 通过 SignalR 服务和 Azure Functions 来构建无服务器应用程序，以将消息广播到客户端。

> [!NOTE]
> 可以从 [GitHub](https://github.com/aspnet/AzureSignalR-samples/tree/main/samples/QuickStartServerless/java) 获取本文中提到的所有代码

## <a name="prerequisites"></a>先决条件

- 代码编辑器，如 [Visual Studio Code](https://code.visualstudio.com/)
- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing)。 用于在本地运行 Azure Functions 应用。

   > [!NOTE]
   > Java 中所需的 SignalR 服务绑定仅在 Azure Function Core Tools 版本2.4.419（主机版本 2.0.12332）或更高版本中受支持。

   > [!NOTE]
   > 若要安装扩展，Azure Functions Core Tools 需要安装 [.NET Core SDK](https://dotnet.microsoft.com/download)。 但是，构建 JavaScript Azure Function 应用不需要了解 .NET。

- [Java 开发人员工具包](https://www.azul.com/downloads/zulu/)版本 11
- [Apache Maven](https://maven.apache.org) 版本 3.0 或更高版本

> [!NOTE]
> 本快速入门可以在 macOS、Windows 或 Linux 上运行。

遇到问题？ 尝试使用[故障排除指南](signalr-howto-troubleshoot-guide.md)或[通知我们](https://aka.ms/asrs/qsjava)。

## <a name="log-in-to-azure"></a>登录 Azure

使用 Azure 帐户登录到 <https://portal.azure.com/> 的 Azure 门户。

遇到问题？ 尝试使用[故障排除指南](signalr-howto-troubleshoot-guide.md)或[通知我们](https://aka.ms/asrs/qsjava)。

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

遇到问题？ 尝试使用[故障排除指南](signalr-howto-troubleshoot-guide.md)或[通知我们](https://aka.ms/asrs/qsjava)。


## <a name="configure-and-run-the-azure-function-app"></a>配置和运行 Azure 函数应用

1. 确保已经安装了 Azure Functions Core Tools、Java（示例中为版本 11）和 maven。
    
    ```bash
    mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype -DjavaVersion=11
    ```

    Maven 会要求你提供所需的值来完成项目的生成操作。 可以提供以下值。

    | Prompt | 值 | 说明 |
    | ------ | ----- | ----------- |
    | **groupId** | `com.signalr` | 一个值，用于按照 Java 的[包命名规则](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7)在所有项目中标识你的项目。 |
    | **artifactId** | `java` | 一个值，该值是 jar 的名称，没有版本号。 |
    | **version** | `1.0-SNAPSHOT` | 选择默认值。 |
    | **package** | `com.signalr` | 一个值，该值是所生成函数代码的 Java 包。 使用默认值。 |   

2. 初始化项目后。 转到 `src/main/java/com/signalr` 文件夹，将以下代码复制到 `Function.java`

    ```java
    package com.signalr;
  
    import com.google.gson.Gson;
    import com.microsoft.azure.functions.ExecutionContext;
    import com.microsoft.azure.functions.HttpMethod;
    import com.microsoft.azure.functions.HttpRequestMessage;
    import com.microsoft.azure.functions.HttpResponseMessage;
    import com.microsoft.azure.functions.HttpStatus;
    import com.microsoft.azure.functions.annotation.AuthorizationLevel;
    import com.microsoft.azure.functions.annotation.FunctionName;
    import com.microsoft.azure.functions.annotation.HttpTrigger;
    import com.microsoft.azure.functions.annotation.TimerTrigger;
    import com.microsoft.azure.functions.signalr.*;
    import com.microsoft.azure.functions.signalr.annotation.*;
    
    import org.apache.commons.io.IOUtils;
    
    
    import java.io.IOException;
    import java.io.InputStream;
    import java.net.URI;
    import java.net.http.HttpClient;
    import java.net.http.HttpRequest;
    import java.net.http.HttpResponse;
    import java.net.http.HttpResponse.BodyHandlers;
    import java.nio.charset.StandardCharsets;
    import java.util.Optional;
    
    public class Function {
        @FunctionName("index")
        public HttpResponseMessage run(
                @HttpTrigger(
                    name = "req",
                    methods = {HttpMethod.GET},
                    authLevel = AuthorizationLevel.ANONYMOUS)HttpRequestMessage<Optional<String>> request,
                final ExecutionContext context) throws IOException {
            
            InputStream inputStream = getClass().getClassLoader().getResourceAsStream("content/index.html");
            String text = IOUtils.toString(inputStream, StandardCharsets.UTF_8.name());
            return request.createResponseBuilder(HttpStatus.OK).header("Content-Type", "text/html").body(text).build();
        }
  
        @FunctionName("negotiate")
        public SignalRConnectionInfo negotiate(
                @HttpTrigger(
                    name = "req",
                    methods = { HttpMethod.POST },
                    authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> req,
                @SignalRConnectionInfoInput(
                    name = "connectionInfo",
                    hubName = "serverless") SignalRConnectionInfo connectionInfo) {
                    
            return connectionInfo;
        }
    
        @FunctionName("broadcast")
        @SignalROutput(name = "$return", hubName = "serverless")
        public SignalRMessage broadcast(
            @TimerTrigger(name = "timeTrigger", schedule = "*/5 * * * * *") String timerInfo) throws IOException, InterruptedException {
            
            HttpClient client = HttpClient.newHttpClient();
            HttpRequest req = HttpRequest.newBuilder().uri(URI.create("https://api.github.com/repos/azure/azure-signalr")).header("User-Agent", "serverless").build();
            HttpResponse<String> res = client.send(req, BodyHandlers.ofString());
            Gson gson = new Gson();
            GitResult result = gson.fromJson(res.body(), GitResult.class);
            return new SignalRMessage("newMessage", "Current star count of https://github.com/Azure/azure-signalr is:".concat(result.stargazers_count));
        }
    
        class GitResult {
            public String stargazers_count;
        }
    }
    ```

3. 需要更新某些依赖项。 因此，请打开 `pom.xml` 并添加代码中使用的一些依赖项。

    ```xml
    <dependency>
        <groupId>com.microsoft.azure.functions</groupId>
        <artifactId>azure-functions-java-library-signalr</artifactId>
        <version>1.0.0</version>
    </dependency>
    <dependency>
        <groupId>commons-io</groupId>
        <artifactId>commons-io</artifactId>
        <version>2.4</version>
    </dependency>
    <dependency>
        <groupId>com.google.code.gson</groupId>
        <artifactId>gson</artifactId>
        <version>2.8.7</version>
    </dependency>
    ```

4. 此示例的客户端界面为网页。 考虑到我们在 `index` 函数中从 `content/index.html` 中读取 HTML 内容，因此要在 `resources` 目录中创建一个新文件 `content/index.html`。 目录应类似于如下所示。
    
    ```
    FunctionsProject
     | - src
     | | - main
     | | | - java
     | | | | - com
     | | | | | - signalr 
     | | | | | | - Function.java
     | | | - resources
     | | | | - content
     | | | | | - index.html
     | - pom.xml
     | - host.json
     | - local.settings.json
    ```

    打开 `index.html` 并复制以下内容。

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

5. 现在即将完成了。 最后一步是将 SignalR 服务的连接字符串设置为 Azure Functions 设置。

    1. 在打开 Azure门户的浏览器中，通过在门户顶部的搜索框中搜索先前部署的 SignalR 服务实例的名称，确认该实例已成功创建。 选择该实例以将其打开。

        ![搜索 SignalR 服务实例](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

    1. 选择“密钥”以查看 SignalR 服务实例的连接字符串。
    
        ![屏幕截图突出显示了主连接字符串。](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

    1. 复制主连接字符串。 然后执行以下命令。
    
        ```bash
        func settings add AzureSignalRConnectionString "<signalr-connection-string>"
        # Also we need to set AzureWebJobsStorage as Azure Function's requirement
        func settings add AzureWebJobsStorage "UseDevelopmentStorage=true"
        ```
    
6. 在本地运行 Azure Functions：

    ```bash
    mvn clean package
    mvn azure-functions:run
    ```

    在本地运行 Azure Functions 后， 使用浏览器访问 `http://localhost:7071/api/index`，随后可以看到当前的星数。 如果你在 GitHub 中进行星标或取消星标，星数会每隔几秒刷新一次。

    > [!NOTE]
    > SignalR 绑定需要 Azure 存储，但是在本地运行 Azure Functions 时，可以使用本地存储仿真器。
    > 如果收到一些类似 `There was an error performing a read operation on the Blob Storage Secret Repository. Please ensure the 'AzureWebJobsStorage' connection string is valid.` 的错误，则需要下载并启用[存储仿真器](../storage/common/storage-use-emulator.md)
    
遇到问题？ 尝试使用[故障排除指南](signalr-howto-troubleshoot-guide.md)或[通知我们](https://aka.ms/asrs/qsjava)。


[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

遇到问题？ 尝试使用[故障排除指南](signalr-howto-troubleshoot-guide.md)或[通知我们](https://aka.ms/asrs/qsjava)。

## <a name="next-steps"></a>后续步骤

在本快速入门中，你在本地生成并运行了一个实时的无服务器应用程序。 详细了解了如何将 SignalR 服务绑定用于 Azure Functions。
接着，详细了解了如何通过 SignalR 服务在客户端与 Azure Functions 之间进行双向通信。

> [!div class="nextstepaction"]
> [Azure Functions 的 SignalR Service 绑定](../azure-functions/functions-bindings-signalr-service.md)

> [!div class="nextstepaction"]
> [无服务器的双向通信](https://github.com/aspnet/AzureSignalR-samples/tree/main/samples/BidirectionChat)

> [!div class="nextstepaction"]
> [使用 Java 和 Maven 创建第一个函数](../azure-functions/create-first-function-cli-csharp.md?pivots=programming-language-java%2cprogramming-language-java)
