---
title: 教程 - 使用 Azure Web PubSub 服务和 Azure Functions 创建无服务器聊天
description: 演示如何使用 Azure Web PubSub 服务和 Azure Functions 生成无服务器应用程序的教程。
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: tutorial
ms.date: 03/11/2021
ms.openlocfilehash: 466da06b470774c67fb93eca5cc027edb16bcbc7
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446162"
---
# <a name="tutorial-create-a-serverless-chat-with-azure-functions-and-azure-web-pubsub-service"></a>教程：使用 Azure Functions 和 Azure Web PubSub 服务创建无服务器聊天

Azure Web PubSub 服务可帮助你轻松地使用 Websocket 和发布-订阅模式生成实时消息传递 Web 应用程序。 Azure Functions 是一个无服务器平台，可让你在不管理任何基础结构的情况下运行代码。 本教程介绍如何使用 Azure Web PubSub 服务和 Azure Functions 来生成具有实时消息传递和发布-订阅模式的无服务器应用程序。

在本教程中，你将了解：

> [!div class="checklist"]
> * 创建 Web PubSub 服务实例
> * 在本地运行示例函数
> * 配置 Web PubSub 事件处理程序以将事件和消息路由到应用程序

## <a name="prerequisites"></a>先决条件

# <a name="javascript"></a>[JavaScript](#tab/javascript)

安装代码编辑器（如 [Visual Studio Code](https://code.visualstudio.com/)）以及库 [Node.js](https://nodejs.org/en/download/) 版本 10.x

   > [!NOTE]
   > 有关支持的 Node.js 版本的详细信息，请参阅 [Azure Functions 运行时版本文档](../azure-functions/functions-versions.md#languages)。

安装 [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing)（2.7.1505 或更高版本）以在本地运行 Azure Functions 应用。

# <a name="c"></a>[C#](#tab/csharp)

安装代码编辑器，如 [Visual Studio Code](https://code.visualstudio.com/)。

安装 [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing)（版本 3 或更高版本）以在本地运行 Azure Functions 应用。

---

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [create-instance-portal](includes/create-instance-portal.md)]

## <a name="clone-the-sample-application"></a>克隆示例应用程序

在部署该服务时，让我们切换到使用代码。 首先，[从 GitHub 克隆示例应用](https://github.com/Azure/azure-webpubsub/tree/main/samples/functions/js/simplechat)。

1. 打开 git 终端窗口。 导航到要克隆示例项目的文件夹。

1. 运行下列命令，克隆示例存储库。 此命令在计算机上创建示例应用程序的副本。

    ```bash
    git clone https://github.com/Azure/azure-webpubsub.git
    ```

## <a name="configure-and-run-the-azure-function-app"></a>配置和运行 Azure 函数应用

- 在浏览器中，打开“Azure 门户”，确认已成功创建前面部署的 Web PubSub 服务实例。 导航到该实例。
- 选择“密钥”并复制连接字符串。

:::image type="content" source="media/quickstart-serverless/copy-connection-string.png" alt-text="复制 Web PubSub 连接字符串的屏幕截图。":::

# <a name="javascript"></a>[JavaScript](#tab/javascript)

- 更新函数配置。

  打开克隆的存储库中的 /samples/functions/js/simplechat 文件夹。 编辑 local.settings.json 以添加服务连接字符串。
  在 local.settings.json 中需要进行这些更改，然后保存该文件。
    - 将占位符 `<connection-string>` 替换为从 Azure 门户中复制的值，以便进行 `WebPubSubConnectionString` 设置。 
    - `AzureWebJobsStorage` 设置是必需的，因为 [Azure Functions 需要 Azure 存储帐户](../azure-functions/storage-considerations.md)。
        - 如果 [Azure 存储模拟器](https://go.microsoft.com/fwlink/?linkid=717179&clcid=0x409)在本地运行，请保留“UseDevelopmentStorage=true”原始设置。
        - 如果有 Azure 存储连接字符串，请用它来替换值。
 
- JavaScript 函数将组织到文件夹中。 每个文件夹中有两个文件：`function.json` 定义函数中使用的绑定，`index.js` 是函数的主体。 此函数应用中有几个 HTTP 触发的函数：

    - login：此函数是 HTTP 触发的函数。 它使用 webPubSubConnection 输入绑定生成并返回有效的服务连接信息。
    - messages：此函数是 `WebPubSubTrigger` 触发的函数。 在请求正文中接收聊天消息，并使用 `WebPubSub` 输出绑定将消息广播到所有连接的客户端应用程序。
    - connect 和 connected：这些函数是 `WebPubSubTrigger` 触发的函数 。 处理 connect 事件和 connected 事件。

- 在终端中，确保自己位于 /samples/functions/js/simplechat 文件夹中。 安装扩展并运行函数应用。

    ```bash
    func extensions install

    func start
    ```

# <a name="c"></a>[C#](#tab/csharp)

- 更新函数配置。

  打开克隆的存储库中的 /samples/functions/csharp/simplechat 文件夹。 编辑 local.settings.json 以添加服务连接字符串。
  在 local.settings.json 中需要进行这些更改，然后保存该文件。
    - 将占位符 `<connection-string>` 替换为从 Azure 门户中复制的值，以便进行 `WebPubSubConnectionString` 设置。 
    - `AzureWebJobsStorage` 设置是必需的，因为 [Azure Functions 需要 Azure 存储帐户](../azure-functions/storage-considerations.md)。
        - 如果 [Azure 存储模拟器](https://go.microsoft.com/fwlink/?linkid=717179&clcid=0x409)在本地运行，请保留“UseDevelopmentStorage=true”原始设置。
        - 如果有 Azure 存储连接字符串，请用它来替换值。

- C# 函数是由文件 Functions.cs 组织的。 此函数应用中有几个 HTTP 触发的函数：

    - login：此函数是 HTTP 触发的函数。 它使用 webPubSubConnection 输入绑定生成并返回有效的服务连接信息。
    - connected：此函数是 `WebPubSubTrigger` 触发的函数。 在请求正文中接收聊天消息，并通过多个任务将消息广播到所有连接的客户端应用程序。
    - broadcast：此函数是 `WebPubSubTrigger` 触发的函数。 在请求正文中接收聊天消息，并通过单个任务将消息广播到所有连接的客户端应用程序。
    - connect 和 disconnect - 这些函数是 `WebPubSubTrigger` 触发的函数。 处理 connect 事件和 disconnect 事件。

- 在终端中，确保自己位于 /samples/functions/csharp/simplechat 文件夹中。 安装扩展并运行函数应用。

    ```bash
    func extensions install

    func start
    ```

---

- 本地函数将使用在 `local.settings.json` 文件中定义的端口。 若要使其在公用网络中可用，需要使用 [ngrok](https://ngrok.com) 来公开此终结点。 运行下面的命令，将获得一个转发终结点，例如： http://{ngrok-id}.ngrok.io -> http://localhost:7071 。

    ```bash
    ngrok http 7071
    ``` 

- 在 Azure Web PubSub 服务中设置 `Event Handler`。 转到“Azure 门户”>“查找 Web PubSub 资源”>“设置” 。 将新的中心设置映射添加到使用中的一个函数，如下所示。 将 {ngrok-id} 替换为自己的值。

   - 中心名称：`simplechat`
   - URL 模板： http://{ngrok-id}.ngrok.io/runtime/webhooks/webpubsub
   - 用户事件模式：*
   - 系统事件：connect、connected、disconnected。

:::image type="content" source="media/quickstart-serverless/set-event-handler.png" alt-text="设置事件处理程序的屏幕截图。":::

## <a name="run-the-web-application"></a>运行 Web 应用程序

1. 若要简化客户端测试，请打开浏览器进入示例[单页 Web 应用程序](http://jialinxin.github.io/webpubsub/)。 

1. 输入函数应用基 URL 作为本地 URL：`http://localhost:7071`。

1. 输入用户名。

1. Web 应用程序调用函数应用中的 login 函数来检索连接信息以连接到 Azure Web PubSub 服务。 `Client websocket opened.` 出现时，表示已建立连接。 

1. 键入消息，然后按 Enter。 应用程序将该消息发送到 Azure 函数应用中的 messages 函数，然后后者使用 Web PubSub 输出绑定将该消息广播到所有连接的客户端。 如果一切正常工作，该消息将出现在应用程序中。

1. 在另一个浏览器窗口中打开另一个 Web 应用程序实例。 你将看到发送的任何消息都将出现在应用程序的所有实例中。

## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用此应用，请按照以下步骤删除本文档中创建的所有资源，以免产生任何费用：

1. 在 Azure 门户的最左侧选择“资源组”，，然后选择创建的资源组。 可以改用搜索框按名称查找资源组。

1. 在打开的窗口中选择资源组，然后选择“删除资源组”。

1. 在新窗口中键入要删除的资源组的名称，然后选择“删除”。

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已了解如何运行无服务器聊天应用程序。 现在，可以开始构建自己的应用程序。 

> [!div class="nextstepaction"]
> [快速入门：使用 Azure Web PubSub 创建简单的聊天室](https://azure.github.io/azure-webpubsub/getting-started/create-a-chat-app/js-handle-events)

> [!div class="nextstepaction"]
> [适用于 Azure Functions 的 Azure Web PubSub 绑定](https://azure.github.io/azure-webpubsub/references/functions-bindings)

> [!div class="nextstepaction"]
> [了解更多 Azure Web PubSub 示例](https://github.com/Azure/azure-webpubsub/tree/main/samples)