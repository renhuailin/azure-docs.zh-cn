---
title: 快速入门：创建 Node.js Web 应用 - Linux
description: 在数分钟内将第一个 Node.js Hello World 部署到 Azure 应用服务。
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 08/01/2020
ms.custom: mvc, devcenter, seodec18
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: a6c580f7d6bc03f298621b1a33fcb9f3f461e802
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101747323"
---
<!-- default for linux -->

## <a name="3-deploy-to-azure-app-service-from-visual-studio-code"></a>3.通过 Visual Studio Code 部署到 Azure 应用服务

1. 在 Visual Studio Code 中打开应用程序文件夹。

    ```bash
    code .
    ```

1. 在“Azure 应用服务”资源管理器中，选择“登录到 Azure...”，然后按照说明进行操作 。 登录后，资源管理器应会显示 Azure 订阅的名称。

    ![登录到 Azure](../media/quickstart-nodejs/sign-in.png)
    <br>
    <details>
    <summary>排查 Azure 登录问题</summary>
    
    如果在登录到 Azure 时出现错误“找不到名为 [订阅 ID] 的订阅”，原因可能是你使用了代理，因此无法访问 Azure API。 在终端中使用 `export` 以代理信息配置 `HTTP_PROXY` 和 `HTTPS_PROXY` 环境变量。
    
    ```bash
    export HTTPS_PROXY=https://username:password@proxy:8080
    export HTTP_PROXY=http://username:password@proxy:8080
    ```

    [报告问题](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)


1. 在“AZURE 应用服务”资源管理器中，选择蓝色的向上箭头图标，将应用部署到 Azure。  

    :::image type="content" source="../media/quickstart-nodejs/deploy.png" alt-text="VS Code 中的 Azure 应用服务的屏幕截图，显示已选中蓝色箭头图标。":::

1. 选择当前已打开的目录 `nodejs-docs-hello-world`。

1. 选择“新建新的 Web 应用”，  此选项默认部署到 Linux 上的应用服务。

1. 为 Web 应用键入全局唯一的 <abbr title="应用名称的有效字符为“a-z”、“0-9”和“-”。">name</abbr> ，然后按 Enter。 

1. 选择 **Node.js 版本**，建议使用“LTS”。

    通知通道将显示正在为应用创建 Azure 资源。

1. 当系统提示你更新配置以在目标服务器上运行 `npm install` 时，选择“是”。  随后将部署应用。

    :::image type="content" source="../media/quickstart-nodejs/server-build.png" alt-text="屏幕截图，显示提示更新目标服务器上的配置，已选中“是”按钮。":::

1. 系统提示更新工作区时选择“确定”，使之后的部署自动面向相同的应用服务 Web 应用。 

    :::image type="content" source="../media/quickstart-nodejs/save-configuration.png" alt-text="屏幕截图，显示提示更新工作区，已选中“是”按钮。":::




1. 部署完成后，选择提示中的“浏览网站”以查看全新部署的 Web 应用。 

<br>
<details>
<summary>疑难解答</summary>
* 确保应用程序正在侦听 PORT 环境变量 `process.env.PORT` 提供的端口。
* 如果看到错误“你无权查看此目录或页面”，则表示应用程序可能无法正常启动。  查看日志输出以查找并修复错误。 

</details>

<br>

[报告问题](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&prepare-your-environment)


<br>
<hr/>


