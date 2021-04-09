---
title: 快速入门：创建 Node.js Web 应用
description: 在数分钟内将第一个 Node.js Hello World 部署到 Azure 应用服务。
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 08/01/2020
ms.custom: mvc, devcenter, seodec18
zone_pivot_groups: app-service-platform-windows-linux
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 7cd9add699d9bd4a3eff9cdcf1e65af0d754b70a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "101747322"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>在 Azure 中创建 Node.js Web 应用

Windows Server 2019 <abbr title="一项基于 HTTP 的服务，用于托管 Web 应用程序、REST API 和移动后端应用程序。">Azure 应用服务</abbr> 方法是使用 Visual Studio Code 在本地创建 Node.js/Express 应用并将其部署到 Azure 云。 你使用的是 <abbr title="在 Azure 应用服务中，则是基本层，你的应用在该层上与其他应用（包括其他客户的应用）在相同的 VM 上运行。 此层级用于开发和测试。">免费层</abbr>，因此可免费完成本快速入门。

## <a name="1-prepare-your-environment"></a>1.准备环境

- Azure 帐户，具有有效 <abbr title="Azure 订阅是用于在 Azure 中预置资源的逻辑容器。 它包含你的所有资源（如虚拟机 (VM)、数据库等）的详细信息。">订阅</abbr>. [免费创建帐户](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension)。
- <a href="https://git-scm.com/" target="_blank">安装 Git</a>
- [Node.js 和 npm](https://nodejs.org)。 运行命令 `node --version` 验证是否已安装 Node.js。
- [Visual Studio Code](https://code.visualstudio.com/)。
- Visual Studio Code 的 [Azure 应用服务扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)。

[报告问题](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&prepare-your-environment)




<br>
<hr/>

## <a name="2-clone-and-run-a-local-nodejs-application"></a>2.克隆并运行本地 Node.js 应用程序

1. 在本地计算机上，打开终端并克隆示例存储库：

    ```bash
    git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
    ```

1. 导航到新应用所在的文件夹：

    ```bash
    cd nodejs-docs-hello-world
    ```

1. 安装依赖项：

    ```bash
    npm install
    ```

1. 启动该应用以在本地进行测试：

    ```bash
    npm start
    ```
    
1. 打开浏览器并导航到 `http://localhost:1337`。 浏览器应会显示“Hello World!”。

1. 在终端中按 <kbd>Ctrl</kbd> + <kbd>C</kbd> 停止服务器。

[报告问题](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&prepare-your-environment)


<br>
<hr/>




<!-- VS Code extension works differently for Windows/Linus - Step 3 -->

::: zone pivot="platform-windows"  

[!INCLUDE [Windows](./includes/quickstart-nodejs-uiex-windows.md)]


::: zone-end

::: zone pivot="platform-linux"  

[!INCLUDE [Windows](./includes/quickstart-nodejs-uiex-linux.md)]

::: zone-end


## <a name="4-viewing-logs-from-visual-studio-code"></a>4.从 Visual Studio Code 查看日志

查看 <abbr title="对应用中的 `console.log` 发出的任何调用将显示在 Visual Studio Code 的输出窗口中。">log</abbr> 正在运行的应用服务应用的日志。

1. 在“Azure 应用服务”资源管理器中找到该应用，右键单击该应用的名称，然后选择“启动流日志” 。

1. 此时会打开 Visual Studio Code 输出窗口。

    ![查看流日志](./media/quickstart-nodejs/view-logs.png)

    :::image type="content" source="./media/quickstart-nodejs/enable-restart.png" alt-text="屏幕截图，VS Code 提示启用文件日志记录并重启 Web 应用，已选中“是”按钮。":::

1. 几秒钟后，你将看到一条消息，指出已连接到日志流服务。 
1. 请多次刷新页面以查看更多活动。

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    2020-09-20 20:37:39.574 INFO  - Initiating warmup request to container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node
    2020-09-20 20:37:55.011 INFO  - Waiting for response to warmup request for container msdocs-vscode-node_2_00ac292a. Elapsed time = 15.4373071 sec
    2020-09-20 20:38:08.233 INFO  - Container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node initialized successfully and is ready to serve requests.
    2020-09-20T20:38:21  Startup Request, url: /Default.cshtml, method: GET, type: request, pid: 61,1,7, SCM_SKIP_SSL_VALIDATION: 0, SCM_BIN_PATH: /opt/Kudu/bin, ScmType: None
    </pre>

<br>

[报告问题](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&prepare-your-environment)

<br>
<hr/>

## <a name="5-clean-up-resources"></a>5.清理资源

在“Azure 应用服务”资源管理器中找到该应用，右键单击该应用的名称，然后选择“删除” 。 

:::image type="content" source="./media/quickstart-nodejs/delete-resource-ieux.png" alt-text="在“Azure 应用服务”资源管理器中找到该应用，右键单击该应用的名称，然后选择“删除”****":::

## <a name="next-steps"></a>后续步骤

祝贺你，你现已成功完成本快速入门！ 可以使用相同的过程来部署对此应用所做的更改，并选择现有应用而不是创建新应用。

接下来请查看其他 Azure 扩展。

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Docker 工具](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Azure CLI 工具](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Azure 资源管理器工具](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

或安装 [Node Pack for Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) 扩展包获取所有这些工具。