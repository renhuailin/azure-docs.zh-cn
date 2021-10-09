---
title: 配置 Node.js 应用
description: 了解如何在原生 Windows 实例、预构建的 Linux 容器或 Azure 应用服务中配置 Node.js 应用。 本文介绍最常见的配置任务。
ms.custom: devx-track-js, devx-track-azurecli
ms.devlang: nodejs
ms.topic: article
ms.date: 04/23/2021
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 8f65fff40419ef11dcb5d90c670d10573a114fd4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128657056"
---
# <a name="configure-a-nodejs-app-for-azure-app-service"></a>为 Azure 应用服务配置 Node.js 应用

Node.js 应用必须与所有必需的 NPM 依赖项一起部署。 当你在[启用了生成自动化](deploy-zip.md#enable-build-automation-for-zip-deploy)的情况下部署 [Git 存储库](deploy-local-git.md)或 [Zip 包](deploy-zip.md)时，应用服务部署引擎会自动为你运行 `npm install --production`。 但是，如果使用 [FTP/S](deploy-ftp.md) 部署你的文件，则需手动上传所需的包。

本指南为在应用服务中进行部署的 Node.js 开发人员提供了重要概念和说明。 若从未使用过 Azure 应用服务，则首先应按照 [Node.js 快速入门](quickstart-nodejs.md)以及[将 Node.js 与 MongoDB 配合使用的教程](tutorial-nodejs-mongodb-app.md)进行操作。

## <a name="show-nodejs-version"></a>显示 Node.js 版本

::: zone pivot="platform-windows"  

若要显示当前的 Node.js 版本，请在 [Cloud Shell](https://shell.azure.com) 中运行以下命令：

```azurecli-interactive
az webapp config appsettings list --name <app-name> --resource-group <resource-group-name> --query "[?name=='WEBSITE_NODE_DEFAULT_VERSION'].value"
```

要显示所有受支持的 Node.js 版本，请导航到 `https://<sitename>.scm.azurewebsites.net/api/diagnostics/runtime` 或在 [Cloud Shell](https://shell.azure.com) 中运行以下命令：

```azurecli-interactive
az webapp list-runtimes | grep node
```

::: zone-end

::: zone pivot="platform-linux"

若要显示当前的 Node.js 版本，请在 [Cloud Shell](https://shell.azure.com) 中运行以下命令：

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

要显示所有受支持的 Node.js 版本，请在 [Cloud Shell](https://shell.azure.com) 中运行以下命令：

```azurecli-interactive
az webapp list-runtimes --linux | grep NODE
```

::: zone-end

## <a name="set-nodejs-version"></a>设置 Node.js 版本

::: zone pivot="platform-windows"  

若要将应用设置为某个[受支持的 Node.js 版本](#show-nodejs-version)，请在 [Cloud Shell](https://shell.azure.com) 中运行以下命令，以将 `WEBSITE_NODE_DEFAULT_VERSION` 设置为受支持的版本：

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_NODE_DEFAULT_VERSION="10.15"
```

此设置指定在运行时以及在自动生成应用服务期间自动还原程序包时要使用的 Node.js 版本。 此设置仅识别主要的次要版本，不支持 LTS 名字对象。

> [!NOTE]
> 应在项目的 `package.json` 中设置 Node.js 版本。 部署引擎在一个单独的进程中运行，该进程包含所有受支持的 Node.js 版本。

::: zone-end

::: zone pivot="platform-linux"

若要将应用设置为某个[受支持的 Node.js 版本](#show-nodejs-version)，请在 [Cloud Shell](https://shell.azure.com) 中运行以下命令：

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "NODE|10.14"
```

此设置指定在运行时以及在 Kudu 中自动还原程序包时要使用的 Node.js 版本。

> [!NOTE]
> 应在项目的 `package.json` 中设置 Node.js 版本。 部署引擎在一个单独的容器中运行，该容器包含所有受支持的 Node.js 版本。

::: zone-end

## <a name="get-port-number"></a>获取端口号

Node.js 应用需要侦听正确的端口才能接收传入的请求。

::: zone pivot="platform-windows"  

在 Windows 上的应用服务中，Node.js 应用是通过 [IISNode](https://github.com/Azure/iisnode)托管的，你的 Node.js 应用应侦听 `process.env.PORT` 变量中指定的端口。 以下示例演示如何在一个简单的快速应用中执行此操作：

::: zone-end

::: zone pivot="platform-linux"  

应用服务在 Node.js 容器中设置环境变量 `PORT`，并在该端口号将传入请求转发到你的容器。 若要接收请求，你的应用应该使用 `process.env.PORT` 侦听该端口。 以下示例演示如何在一个简单的快速应用中执行此操作：

::: zone-end

```javascript
const express = require('express')
const app = express()
const port = process.env.PORT || 3000

app.get('/', (req, res) => {
  res.send('Hello World!')
})

app.listen(port, () => {
  console.log(`Example app listening at http://localhost:${port}`)
})
```

::: zone pivot="platform-linux"

## <a name="customize-build-automation"></a>自定义生成自动化

如果在[启用了生成自动化](deploy-zip.md#enable-build-automation-for-zip-deploy)的情况下使用 Git 或 zip 包部署应用，应用服务生成自动化将按以下顺序完成各个步骤：

1. 运行 `PRE_BUILD_SCRIPT_PATH` 指定的自定义脚本。
1. 在没有任何标志的情况下运行 `npm install`，这将包括 npm `preinstall` 和 `postinstall` 脚本并将安装 `devDependencies`。
1. 如果在 package.json中指定了生成脚本，请运行 `npm run build`。
1. 如果在 package.json中指定了 build:azure 脚本，请运行 `npm run build:azure`。
1. 运行 `POST_BUILD_SCRIPT_PATH` 指定的自定义脚本。

> [!NOTE]
> 如 [npm 文档](https://docs.npmjs.com/misc/scripts)所述，名为 `prebuild` 和 `postbuild` 的脚本分别在 `build` 之前和之后运行（如果已指定）。 `preinstall` 和 `postinstall` 分别在 `install` 之前和之后运行。

`PRE_BUILD_COMMAND` 和 `POST_BUILD_COMMAND` 是默认为空的环境变量。 若要运行生成前命令，请定义 `PRE_BUILD_COMMAND`。 若要运行生成后命令，请定义 `POST_BUILD_COMMAND`。

以下示例在一系列命令中指定两个以逗号分隔的变量。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

有关用于自定义生成自动化的其他环境变量，请参阅 [Oryx 配置](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md)。

有关应用服务如何在 Linux 中运行和生成 Node.js 应用的详细信息，请参阅 [Oryx 文档：如何检测和生成 Node.js 应用](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/nodejs.md)。

## <a name="configure-nodejs-server"></a>配置 Node.js 服务器

Node.js 容器附带了 [PM2](https://pm2.keymetrics.io/)（一个生产流程管理器）。 你可以将应用配置为以 PM2、NPM 或自定义命令启动。

|工具|目的|
|--|--|
|[通过 PM2 运行](#run-with-pm2)|推荐 - 在生产或过渡期间使用。 PM2 提供了全方位服务的应用管理平台。|
|[运行 npm start](#run-npm-start)|仅在开发期间使用。|
|[运行自定义命令](#run-custom-command)|在开发或过渡期间使用。|


### <a name="run-with-pm2"></a>通过 PM2 运行

在你的项目中找到常用的 Node.js 文件之一时，容器会自动通过 PM2 启动你的应用：

- *bin/www*
- *server.js*
- *app.js*
- *index.js*
- *hostingstart.js*
- 下述 [PM2 文件](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file)之一：process.json 和 ecosystem.config.js

你还可以配置具有以下扩展名的自定义启动文件：

- .js 文件
- 扩展名为 *.json*、 *.config.js*、 *.yaml* 或 *.yml* 的 [PM2 文件](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file)

若要添加自定义启动文件，请在 [Cloud Shell](https://shell.azure.com) 中运行以下命令：

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filname-with-extension>"
```

### <a name="run-custom-command"></a>运行自定义命令

“应用服务”可以使用自定义命令（例如，run.sh 之类的可执行文件）启动应用。例如，要运行 `npm run start:prod`，请在 [Cloud Shell](https://shell.azure.com) 中运行以下命令：

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "npm run start:prod"
```

### <a name="run-npm-start"></a>运行 npm start

若要使用 `npm start` 启动应用，只需确保 `start` 脚本位于 package.json 文件中即可。 例如：

```json
{
  ...
  "scripts": {
    "start": "gulp",
    ...
  },
  ...
}
```

若要在项目中使用自定义 package.json，请在 [Cloud Shell](https://shell.azure.com) 中运行以下命令：

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filename>.json"
```


## <a name="debug-remotely"></a>远程调试

> [!NOTE]
> 远程调试功能当前为预览版。

如果你将 Node.js 应用配置为[通过 PM2 运行](#run-with-pm2)，则可以在 [Visual Studio Code](https://code.visualstudio.com/) 中远程调试该应用，但使用 *.config.js、*.yml 或 .yaml 运行它的情况除外。

在大多数情况下，你的应用不需要进行额外配置。 如果你的应用通过 process.json 文件（默认的或自定义的）运行，则它必须在 JSON 根中具有 `script` 属性。 例如：

```json
{
  "name"        : "worker",
  "script"      : "./index.js",
  ...
}
```

若要设置用于远程调试的 Visual Studio Code，请安装[应用服务扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)。 按照扩展页上的说明进行操作，在 Visual Studio Code 中登录到 Azure。

在 Azure 资源管理器中，找到要调试的应用，右键单击该应用，然后选择“启动远程调试”。 单击“是”为你的应用启用该功能。 应用服务会启动一个隧道代理并附加调试器。 然后，你可以向应用发出请求，并会看到调试器在断点处暂停。

完成调试后，通过选择“断开连接”来停止调试器。 出现提示时，应单击“是”以禁用远程调试。 若要在以后禁用它，请在 Azure 资源管理器中再次右键单击你的应用，然后选择“禁用远程调试”。

::: zone-end

## <a name="access-environment-variables"></a>访问环境变量

在应用服务中，可以在应用代码外部[设置应用设置](configure-common.md)。 然后，可以使用标准的 Node.js 模式访问这些设置。 例如，若要访问名为 `NODE_ENV` 的应用设置，请使用以下代码：

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>运行 Grunt/Bower/Gulp

默认情况下，当识别出 Node.js 应用是在[启用了生成自动化](deploy-zip.md#enable-build-automation-for-zip-deploy)的情况下通过 Git 或 Zip 部署进行部署时，应用服务生成自动化会运行 `npm install --production`。 如果你的应用需要任何常用的自动化工具（例如 Grunt、Bower 或 Gulp），你需要提供[自定义部署脚本](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)才能运行该应用。

若要使你的存储库能够运行这些工具，需要将它们添加到 package.json 中的依赖项。 例如：

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

在本地终端窗口中，将目录更改到你的存储库根目录，并运行以下命令：

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

你的存储库根目录中现在有两个额外的文件：.deployment 和 deploy.sh。

打开 deploy.sh 并找到 `Deployment` 节，该节如下所示：

```bash
##################################################################################################################################
# Deployment
# ----------
```

该节在末尾处运行 `npm install --production`。 在 `Deployment` 节的末尾添加运行必需工具所需的代码节：

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

请参阅 [MEAN.js 示例中的示例](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135)，其中的部署脚本也运行自定义 `npm install` 命令。

### <a name="bower"></a>Bower

此代码片段运行 `bower install`。

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

此代码片段运行 `gulp imagemin`。

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

此代码片段运行 `grunt`。

```bash
if [ -e "$DEPLOYMENT_TARGET/Gruntfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/grunt
  exitWithMessageOnError "Grunt failed"
  cd - > /dev/null
fi
```

## <a name="detect-https-session"></a>检测 HTTPS 会话

在应用服务中，[TLS/SSL 终止](https://wikipedia.org/wiki/TLS_termination_proxy)在网络负载均衡器上发生，因此所有 HTTPS 请求将以未加密的 HTTP 请求形式访问你的应用。 如果应用逻辑需要检查用户请求是否已加密，可以检查 `X-Forwarded-Proto` 标头。

使用常用 Web 框架可以访问采用标准应用模式的 `X-Forwarded-*` 信息。 在 [Express](https://expressjs.com/) 中，你可以使用[信任代理](https://expressjs.com/guide/behind-proxies.html)。 例如：

```javascript
app.set('trust proxy', 1)
...
if (req.secure) {
  // Do something when HTTPS is used
}
```

## <a name="access-diagnostic-logs"></a>访问诊断日志

::: zone pivot="platform-windows"  

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

::: zone-end


::: zone pivot="platform-linux"

## <a name="monitor-with-application-insights"></a>使用 Application Insights 进行监视

利用 Application Insights，可以监视应用程序的性能、异常和使用情况，而无需进行任何代码更改。 若要附加 App Insights 代理，请转到门户中的 Web 应用并选择“设置”下的“Application Insights”，然后选择“打开 Application Insights”  。 接下来，请选择现有的或创建一个新的 App Insights 资源。 最后，选择底部的“应用”。 若要使用 PowerShell 来检测 Web 应用，请参阅[这些说明](../azure-monitor/app/azure-web-apps-nodejs.md#enable-through-powershell)

此代理将会监视服务器端 Node.js 应用程序。 若要监视客户端 JavaScript，请[将 JavaScript SDK 添加到项目](../azure-monitor/app/javascript.md)。 

有关详细信息，请参阅 [Application Insights 扩展发行说明](../azure-monitor/app/web-app-extension-release-notes.md)。

::: zone-end

## <a name="troubleshooting"></a>疑难解答

如果运行中的 Node.js 应用在应用服务中的行为不同或有错误，请尝试执行以下操作：

- [访问日志流](#access-diagnostic-logs)。
- 在生产模式下，在本地测试应用。 应用服务在生产模式下运行 Node.js 应用，因此需要确保项目在生产模式下按预期在本地运行。 例如：
    - 可以为生产模式安装不同的程序包（`dependencies` 与 `devDependencies`），具体取决于你的 package.json。
    - 某些 Web 框架可以在生产模式下通过各种方式部署静态文件。
    - 在生产模式下运行时，某些 Web 框架可能会使用自定义的启动脚本。
- 在开发模式下，在应用服务中运行你的应用。 例如，在 [MEAN.js](https://meanjs.org/) 中，可以通过[设置 `NODE_ENV` 应用设置](configure-common.md)在运行时中将应用设置为开发模式。

::: zone pivot="platform-windows"

#### <a name="you-do-not-have-permission-to-view-this-directory-or-page"></a>你无权查看此目录或页面

将 Node.js 代码部署到应用服务中的本机 Windows 应用后，导航到应用的 URL 时，可能会在浏览器中看到消息 `You do not have permission to view this directory or page.`。 这很可能是因为没有 web.config 文件（请参阅[模板](https://github.com/projectkudu/kudu/blob/master/Kudu.Core/Scripts/iisnode.config.template)和[示例](https://github.com/Azure-Samples/nodejs-docs-hello-world/blob/master/web.config)）。

如果你使用 Git 部署文件，或者使用[启用了生成自动化](deploy-zip.md#enable-build-automation-for-zip-deploy)的 ZIP 部署，那么在满足以下条件之一时，部署引擎会在应用的 Web 根目录 (`%HOME%\site\wwwroot`) 中自动生成 web.config：

- 你的项目根具有一个 package.json，它定义包含 JavaScript 文件路径的 `start` 脚本。
- 项目根具有 server.js 或 app.js。

生成的 web.config 是根据检测到的启动脚本定制的。 对于其他部署方法，请手动添加此 web.config。 请确保文件格式正确。 

如果使用 [ZIP 部署](deploy-zip.md)（例如，通过 Visual Studio Code），请务必[启用生成自动化](deploy-zip.md#enable-build-automation-for-zip-deploy)，因为默认情况下不启用。 [`az webapp up`](/cli/azure/webapp#az_webapp_up) 使用启用了生成自动化的 ZIP 部署。

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

::: zone-end

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：使用 MongoDB 的 Node.js 应用](tutorial-nodejs-mongodb-app.md)

::: zone pivot="platform-linux"

> [!div class="nextstepaction"]
> [应用服务 Linux 常见问题解答](faq-app-service-linux.yml)

::: zone-end

或者参阅其他某些资源：

[环境变量和应用设置参考](reference-app-settings.md)