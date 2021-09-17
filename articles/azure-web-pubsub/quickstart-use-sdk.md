---
title: 快速入门 - 使用适用于 Azure Web PubSub 实例的服务 SDK 发布消息
description: 演示如何使用服务 SDK 的快速入门
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: quickstart
ms.date: 08/06/2021
ms.openlocfilehash: 42bcaa9697733662b3a5b890461b917fc466c492
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122445170"
---
# <a name="quickstart-publish-messages-using-the-service-sdk-for-the-azure-web-pubsub-instance"></a>快速入门：使用适用于 Azure Web PubSub 实例的服务 SDK 发布消息

本快速入门演示如何使用服务 SDK 将消息发布到客户端。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 本快速入门需要 Azure CLI 版本 2.22.0 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="create-a-resource-group"></a>创建资源组

[!INCLUDE [Create a resource group](includes/cli-rg-creation.md)]

## <a name="create-a-web-pubsub-instance"></a>创建 Web PubSub 实例

[!INCLUDE [Create a Web PubSub instance](includes/cli-awps-creation.md)]

## <a name="get-the-connectionstring-for-future-use"></a>获取 ConnectionString 以供将来使用

[!INCLUDE [Get the connection string](includes/cli-awps-connstr.md)]

复制已提取的 ConnectionString，稍后在使用服务 SDK 时会将其用作 `<connection_string>` 的值。

## <a name="connect-to-the-instance"></a>连接到实例

[!INCLUDE [az webpubsub client](includes/cli-awps-client-connect.md)]

## <a name="publish-messages-using-service-sdk"></a>使用服务 SDK 发布消息

现在，让我们使用 Azure Web PubSub SDK 向连接的客户端发布消息。

### <a name="prerequisites"></a>先决条件

# <a name="c"></a>[C#](#tab/csharp)

* [.NET Core 2.1 或更高版本](https://dotnet.microsoft.com/download)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

* [Node.js 12.x 或更高版本](https://nodejs.org)

# <a name="python"></a>[Python](#tab/python)
* [Python](https://www.python.org/)

# <a name="java"></a>[Java](#tab/java)
- [Java 开发工具包 (JDK)](/java/azure/jdk/) 8 或更高版本。
- [Apache Maven](https://maven.apache.org/download.cgi)。

---

### <a name="set-up-the-project-to-publish-messages"></a>设置项目以发布消息

# <a name="c"></a>[C#](#tab/csharp)

1. 添加新项目 `publisher` 并添加 SDK 包 `package Azure.Messaging.WebPubSub`。

    ```bash
    mkdir publisher
    cd publisher
    dotnet new console
    dotnet add package Azure.Messaging.WebPubSub --prerelease
    ```

2. 更新 `Program.cs` 文件，以使用 `WebPubSubServiceClient` 类并将消息发送到客户端。

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

4. 可以看到以前的 CLI 客户端收到了消息。
   
    ```json
    {"type":"message","from":"server","dataType":"text","data":"Hello World"}
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

4. 可以看到以前的 CLI 客户端收到了消息。
   
    ```json
    {"type":"message","from":"server","dataType":"text","data":"Hello World"}
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

        # Or call .\env\Scripts\activate when you are using CMD

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

    `build_send_to_all_request()` 会生成消息并使用 `send_request()` 调用将消息发送到中心内所有已连接的客户端。

3. 运行以下命令，将 `<connection_string>` 替换为[前面步骤](#get-the-connectionstring-for-future-use)中提取的 ConnectionString：

    ```bash
    python publish.py "<connection_string>" "myHub1" "Hello World"
    ```

4. 可以看到以前的 CLI 客户端收到了消息。
   
    ```json
    {"type":"message","from":"server","dataType":"text","data":"Hello World"}
    ```

# <a name="java"></a>[Java](#tab/java)

1. 首先让我们使用 Maven 创建一个新的控制台应用 `webpubsub-quickstart-publisher`，并切换到 webpubsub-quickstart-publisher 文件夹：
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
    * Quickstart - Publish messages using Azure Web PubSub service SDK
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

4. 导航到包含 pom.xml 文件的目录，并使用以下 `mvn` 命令编译该项目。

    ```console
    mvn compile
    ```
5. 然后生成包

    ```console
    mvn package
    ```
6. 运行以下 `mvn` 命令以执行应用，将 `<connection_string>` 替换为[前面步骤](#get-the-connectionstring-for-future-use)中提取的 ConnectionString：

    ```console
    mvn exec:java -Dexec.mainClass="com.webpubsub.quickstart.App" -Dexec.cleanupDaemonThreads=false -Dexec.args="'<connection_string>' 'myHub1' 'Hello World'"
    ```

7. 可以看到以前的 CLI 客户端收到了消息。
   
    ```json
    {"type":"message","from":"server","dataType":"text","data":"Hello World"}
    ```

---

## <a name="next-steps"></a>后续步骤

本快速入门提供如何连接到 Web PubSub 服务以及如何将消息发布到连接的客户端的基本想法。

在现实应用程序中，你可以使用各种语言的 SDK 来生成自己的应用程序。 我们还提供了函数扩展，供你轻松生成无服务器应用程序。

[!INCLUDE [next step](includes/include-next-step.md)]
