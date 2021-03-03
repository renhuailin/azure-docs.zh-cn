---
title: 教程 - 为 Azure 通信服务准备 Web 应用 (Node.js)
titleSuffix: An Azure Communication Services tutorial
description: 了解如何创建支持 Azure 通信服务的基线 Web 应用
author: nmurav
services: azure-communication-services
ms.author: nmurav
ms.date: 01/03/2012
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: d682524ae3ff5b82233a69959a309a7495e30bed
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101658057"
---
# <a name="tutorial-prepare-a-web-app-for-azure-communication-services-nodejs"></a>Tutorial:为 Azure 通信服务准备 Web 应用 (Node.js)

使用 Azure 通信服务可向应用程序添加实时通信。 在本教程中，你将了解如何设置支持 Azure 通信服务的 Web 应用。 本教程旨在介绍知识，适合想要开始使用实时通信的新的开发人员。

在本教程结束时，你将拥有一个配置了 Azure 通信服务客户端库的基线 Web 应用，你可用它开始构建自己的实时通信解决方案。

欢迎随时访问 [Azure 通信服务 GitHub](https://github.com/Azure/communication) 页面提供反馈。

本教程介绍如何执行下列操作：
> [!div class="checklist"]
> * 配置开发环境
> * 设置本地 Web 服务器
> * 将 Azure 通信服务包添加到你的网站
> * 将你的网站发布到 Azure 静态网站

## <a name="prerequisites"></a>必备条件

- 具有活动订阅的 Azure 帐户。 有关详细信息，请参阅[创建免费账户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 请注意，免费帐户为你提供了价值 200 美元的 Azure 赠金，你可用它来试用任何服务组合。
- [Visual Studio Code](https://code.visualstudio.com/)：我们将使用它在本地开发环境中编辑代码。
- [webpack](https://webpack.js.org/)：这将用于捆绑并本地托管代码。
- [Node.js](https://nodejs.org/en/)：这将用于安装和管理依赖项，例如 Azure 通信服务客户端库和 webpack。
- [nvm 和 npm](/windows/nodejs/setup-on-windows)，用于处理版本控制。
- 适用于 Visual Studio Code 的 [Azure 存储扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage)。 若要在 Azure 存储中发布应用程序，则需要使用此扩展。 [详细了解如何在 Azure 存储中托管静态网站](../../storage/blobs/storage-blob-static-website.md)
- [Azure 应用服务扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)。 可通过该扩展部署网站（与前述类似），也可选择配置完全托管的持续集成和持续交付 (CI/CD)。
- [Azure 函数扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)，用于构建你自己的无服务器应用程序。 例如，你可在 Azure 函数中托管身份验证应用程序。
- 活动的通信服务资源和连接字符串。 [创建通信服务资源](../quickstarts/create-communication-resource.md)。
- 用户访问令牌。 有关说明，请参阅[访问令牌快速入门](../quickstarts/access-tokens.md?pivots=programming-language-javascript)或[受信任的服务](./trusted-service-tutorial.md)教程。


## <a name="configure-your-development-environment"></a>配置开发环境

本地开发环境将配置如下：

:::image type="content" source="./media/step-one-pic-one.png" alt-text="开发人员环境体系结构":::


### <a name="install-nodejs-nvm-and-npm"></a>安装 Node.js、nvm 和 npm

我们将使用 Node.js 来下载和安装客户端应用程序所需的各种依赖项。 我们将用它来生成静态文件，然后在 Azure 中托管这些文件，因此你无需在你的服务器上配置它。

Windows 开发人员可按照[此 NodeJS 教程](/windows/nodejs/setup-on-windows)来配置 Node、nvm 和 npm。

我们使用 LTS 12.20.0 版本测试了本教程。 安装 nvm 后，请使用以下 PowerShell 命令部署要使用的版本：

```PowerShell
nvm list available
nvm install 12.20.0
nvm use 12.20.0
```

:::image type="content" source="./media/step-one-pic-two.png" alt-text="使用 nvm 部署 Node.js":::

### <a name="configure-visual-studio-code"></a>配置 Visual Studio Code

可在某一[受支持的平台](https://code.visualstudio.com/docs/supporting/requirements#_platforms)上下载 [Visual Studio Code](https://code.visualstudio.com/)。

### <a name="create-a-workspace-for-your-azure-communication-services-projects"></a>为 Azure 通信服务项目创建一个工作区

创建一个新的文件夹用来存储项目文件，如下所示：`C:\Users\Documents\ACS\CallingApp`。 在 Visual Studio Code 中，依次单击“文件”和“将文件夹添加到工作区”，然后将文件夹添加到工作区。

:::image type="content" source="./media/step-one-pic-three.png" alt-text="创建新工作区":::

转到 Visual Studio Code 左窗格中的“资源管理器”，你将在“无标题”工作区中看到“CallingApp”文件夹。

:::image type="content" source="./media/step-one-pic-four.png" alt-text="资源管理器":::

随时都可更新工作区的名称。 可右键单击“CallingApp”文件夹，再选择“在集成终端中打开”来验证 Node.js 版本。

:::image type="content" source="./media/step-one-pic-five.png" alt-text="打开终端":::

在终端中，键入以下命令来验证在上一步中安装的 node.js 版本：

```JavaScript
node --version
```

:::image type="content" source="./media/step-one-pic-six.png" alt-text="验证 Node.js 版本":::

### <a name="install-azure-extensions-for-visual-studio-code"></a>安装适用于 Visual Studio Code 的 Azure 扩展

通过 Visual Studio 应用商店或使用 Visual Studio Code（“视图”>“扩展”>“Azure 存储”）安装 [Azure 存储扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage)。

:::image type="content" source="./media/step-one-pic-seven.png" alt-text="安装 Azure 存储扩展 1":::

对 [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) 和 [Azure 应用服务](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)扩展执行相同的步骤。


## <a name="set-up-a-local-webserver"></a>设置本地 Web 服务器

### <a name="create-a-new-npm-package"></a>创建新的 npm 包

在终端中，从工作区文件夹的路径键入：

``` console
npm init -y
```

此命令会初始化一个新的 npm 包，并将 `package.json` 添加到项目的根文件夹中。

:::image type="content" source="./media/step-one-pic-eight.png" alt-text="包 JSON":::

可在[此处](https://docs.npmjs.com/cli/v6/commands/npm-init)找到其他有关 npm init 命令的文档

### <a name="install-webpack"></a>安装 webpack

通过 [webpack](https://webpack.js.org/)，可将代码捆绑到可部署到 Azure 的静态文件中。 它还有一个开发服务器，我们将对其进行配置来与通话示例一起使用。

在终端中键入以下内容来安装 webpack：

``` Console
npm install webpack@4.42.0 webpack-cli@3.3.11 webpack-dev-server@3.10.3 --save-dev
```

本教程已使用上面指定版本进行了测试。 如果指定 `-dev`，则包管理器将知道此依赖项用于开发目的，且不得包含在部署到 Azure 的代码中。

你将看到两个新包作为“devDependencies”添加到 `package.json` 文件中。 这些包将安装到 `./CallingApp/node_modules/` 目录中。

:::image type="content" source="./media/step-one-pic-ten.png" alt-text="webpack 配置":::

### <a name="configure-the-development-server"></a>配置开发服务器

如果从浏览器运行静态应用程序（如 `index.html` 文件），会使用 `file://` 协议。 为了使 npm 模块正常工作，我们需要将 webpack 用作本地开发服务器来使用 HTTP 协议。

我们将创建两个配置：一个用于开发，另一个用于生产。 为生产准备的文件将缩小，这意味着我们将删除未使用的空格和字符。 这适用于应尽量降低延迟或应对代码进行模糊化处理的生产方案。

我们将使用 `webpack-merge` 工具来处理 [webpack 的不同配置文件](https://webpack.js.org/guides/production/)

让我们从开发环境开始。 首先，需要安装 `webpack merge`。 在终端中，运行以下命令：

```Console
npm install --save-dev webpack-merge
```

在 `package.json` 文件中，可看到“devDependencies”中又添加了一个依赖项。

在下一步中，我们需要创建一个新文件 `webpack.common.js`，并添加以下代码：

```JavaScript
const path = require('path');
module.exports ={
    entry: './app.js',
    output: {
        filename:'app.js',
        path: path.resolve(__dirname, 'dist'),
    }
}
```

然后再添加两个文件，每个文件对应一个配置：

* webpack.dev.js
* webpack.prod.js

在下一步中，需要修改 `webpack.dev.js` 文件。 将以下代码添加到该文件：

```JavaScript
const { merge } = require('webpack-merge');
const common = require('./webpack.common.js');

module.exports = merge(common, {
    mode: 'development',
    devtool: 'inline-source-map',
});
```
在此配置中，我们会从 `webpack.common.js` 导入通用参数、合并这两个文件、将模式设置为“开发”，并将 SourceMap 配置为“inline-source-map”。

开发模式会指示 webpack 不要缩小文件，也不要生成优化的生产文件。 可在[此处](https://webpack.js.org/configuration/mode/)找到有关 webpack 模式的详细文档。

[此处](https://webpack.js.org/configuration/devtool/#root)列出了源映射选项。 通过设置源映射，可更轻松地通过浏览器进行调试。

:::image type="content" source="./media/step-one-pic-11.png" alt-text="配置 webpack":::

若要运行开发服务器，请转到 `package.json`，并在脚本下添加以下代码：

```JavaScript
    "build:dev": "webpack-dev-server --config webpack.dev.js"
```

文件现在应如下所示：

```JavaScript
{
  "name": "CallingApp",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build:dev": "webpack-dev-server --config webpack.dev.js"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "webpack": "^4.42.0",
    "webpack-cli": "^3.3.11",
    "webpack-dev-server": "^3.10.3"
  }
}
```

你添加了可从 npm 使用的命令。

:::image type="content" source="./media/step-one-pic-12.png" alt-text="修改 package.json":::

### <a name="testing-the-development-server"></a>测试开发服务器

 在 Visual Studio Code 中，在项目下创建 3 个文件：

* `index.html`
* `app.js`
* `app.css`（可选，这使你能够设定应用的风格）

将其粘贴到 `index.html`：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My first ACS application</title>
    <link rel="stylesheet" href="./app.css"/>
    <script src="./app.js" defer></script>
</head>
<body>
    <h1>Hello from ACS!</h1>
</body>
</html>
```
:::image type="content" source="./media/step-one-pic-13.png" alt-text="HTML 文件":::

将以下代码添加到 `app.js`：

```JavaScript
alert('Hello world alert!');
console.log('Hello world console!');
```
将以下代码添加到 `app.css`：

```CSS
html {
    font-family: sans-serif;
  }
```
别忘记保存！ 文件资源管理器中的文件名旁边的白色点指示文件未保存。

 :::image type="content" source="./media/step-one-pic-14.png" alt-text="包含 JS 代码的 App.js 文件":::

打开此页面时，应会看到浏览器的控制台中除了显示警报，还显示了消息。

:::image type="content" source="./media/step-one-pic-15.png" alt-text="App.css 文件":::

使用以下终端命令测试开发配置：

```Console
npm run build:dev
```

控制台将显示服务器的运行位置。 该名称默认为 `http://localhost:8080`。 build:dev 命令是我们之前添加到 `package.json` 的命令。

 :::image type="content" source="./media/step-one-pic-16.png" alt-text="启动开发服务器":::

 导航到浏览器中的地址，你会看到在前面的步骤中配置的页面和警报。

  :::image type="content" source="./media/step-one-pic-17.png" alt-text="Html 页面":::


服务器运行时，可更改代码和服务器，HTML 页面也会自动重新加载。

接下来，转到 Visual Studio Code 中的 `app.js` 文件并删除 `alert('Hello world alert!');`。 保存文件并验证浏览器中是否不再显示警报。

若要停止服务器，可在终端中运行 `Ctrl+C`。 可随时键入 `npm run build:dev` 来启动服务器。

## <a name="add-the-azure-communication-services-packages"></a>添加 Azure 通信服务包

使用 `npm install` 命令安装适用于 JavaScript 的 Azure 通信服务呼叫客户端库。

```Console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```

此操作将添加 Azure 通信服务的常用包和通话包作为你的包的依赖项。 你将看到两个新包添加到 `package.json` 文件中。 可在[此处](https://docs.npmjs.com/cli/v6/commands/npm-install)详细了解 `npm install`。

:::image type="content" source="./media/step-one-pic-nine.png" alt-text="安装 Azure 通信服务包":::

这些包由 Azure 通信服务团队提供，包括身份验证和通话库。 “--save”命令表示我们的应用程序依赖于这些包来进行生产，它们将包含在 `package.json` 文件的 `dependencies` 中。 当我们构建用于生产方案的应用程序时，这些包将包含在我们的生产代码中。


## <a name="publish-your-website-to-azure-static-websites"></a>将你的网站发布到 Azure 静态网站

### <a name="create-a-configuration-for-production-deployment"></a>创建用于生产部署的配置

将以下代码添加到 `webpack.prod.js`：

```JavaScript
const { merge } = require('webpack-merge');
 const common = require('./webpack.common.js');

 module.exports = merge(common, {
   mode: 'production',
 });
 ```

请注意，此配置将与 webpack.common.js（我们在这里指定了输入文件和结果存储位置）合并，并将模式设置为“生产”。

在 `package.json` 中添加以下代码：

```JavaScript
"build:prod": "webpack --config webpack.prod.js"
```

你的文件应如下所示：

```JavaScript
{
  "name": "CallingApp",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build:dev": "webpack-dev-server --config webpack.dev.js",
    "build:prod": "webpack --config webpack.prod.js"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "@azure/communication-calling": "^1.0.0-beta.6",
    "@azure/communication-common": "^1.0.0"
  },
  "devDependencies": {
    "webpack": "^4.42.0",
    "webpack-cli": "^3.3.11",
    "webpack-dev-server": "^3.10.3",
    "webpack-merge": "^5.7.3"
  }
}
```

 :::image type="content" source="./media/step-one-pic-20.png" alt-text="已配置的文件":::


在终端中，运行：

```Console
npm run build:prod
```

该命令将在其中创建一个 `dist` 文件夹和生产就绪的 `app.js` 静态文件。

 :::image type="content" source="./media/step-one-pic-21.png" alt-text="生产版本":::


### <a name="deploy-your-app-to-azure-storage"></a>将应用部署到 Azure 存储

将 `index.html` 和 `app.css` 复制到 `dist` 文件夹。

在 `dist` 文件夹中，创建一个新文件并将其命名为 `404.html`。 将以下标记复制到该文件中：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="./app.css"/>
    <title>Document</title>
</head>
<body>
    <h1>The page does not exists.</h1>
</body>
</html>
```

保存文件 (Ctrl + S)。

右键单击并选择通过 Azure 存储部署到静态网站。

:::image type="content" source="./media/step-one-pic-22.png" alt-text="开始部署到 Azure":::

在 `Select subscription` 字段中，选择“登录到 Azure”（如果尚未创建订阅，请选择“创建免费的 Azure 帐户”）

:::image type="content" source="./media/step-one-pic-23.png" alt-text="登录到 Azure":::

选择 `Create new Storage Account` > `Advanced`：

 :::image type="content" source="./media/step-one-pic-24.png" alt-text="创建存储帐户组":::

 提供存储组的名称：

 :::image type="content" source="./media/step-one-pic-25.png" alt-text="添加帐户的名称":::

如果需要，请创建新的资源组：

  :::image type="content" source="./media/step-one-pic-26.png" alt-text="创建新组":::

  对于“你想启用静态网站托管吗?”，请回答“是”

  :::image type="content" source="./media/step-one-pic-27.png" alt-text="选择用于启用静态网站托管的选项":::

创建文件 `index.html` 时，请在“输入索引文档名”中接受默认文件名。

对于“输入 404 错误文档路径”，请键入 `404.html`。

选择应用程序的位置。 选择的位置将定义在组通话中今后的通话应用程序将使用的媒体处理器。

Azure 通信服务会根据应用程序位置来选择媒体处理器。

:::image type="content" source="./media/step-one-pic-28.png" alt-text="选择位置":::

请等待资源和网站创建完毕。

单击“浏览到网站”：

:::image type="content" source="./media/step-one-pic-29.png" alt-text="部署已完成":::

通过浏览器的开发工具，可检查源代码并查看我们为生产准备的文件。

:::image type="content" source="./media/step-one-pic-30.png" alt-text="网站":::

转到 [Azure 门户](https://portal.azure.com/#home)，选择你资源组，再选择所创建的应用程序，然后导航到 `Settings` > `Static website`。 你可看到已启用静态网站，请注意主要终结点、索引文档和错误路径文档文件。

:::image type="content" source="./media/step-one-pic-31.png" alt-text="静态网站选择":::

在“Blob 服务”下选择“容器”，你将看到创建了两个容器，一个用于日志 ($logs)，一个用于网站内容 ($web)

:::image type="content" source="./media/step-one-pic-32.png" alt-text="容器配置":::

如果转到 `$web`，你将看到在 Visual Studio 中创建了文件，且这些文件已部署到 Azure。

:::image type="content" source="./media/step-one-pic-33.png" alt-text="部署":::

可随时从 Visual Studio Code 重新部署应用程序。

你现可构建你的第一个 Azure 通信服务 Web 应用了。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [添加对应用的音频呼叫](../quickstarts/voice-video-calling/getting-started-with-calling.md)

你可能还想要：

- [向应用添加聊天](../quickstarts/chat/get-started.md)
- [创建用户访问令牌](../quickstarts/access-tokens.md)
- [了解客户端和服务器体系结构](../concepts/client-and-server-architecture.md)
- [了解身份验证](../concepts/authentication.md)