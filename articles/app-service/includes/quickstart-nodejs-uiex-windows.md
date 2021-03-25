---
title: 快速入门：创建 Node.js Web 应用 - Windows
description: 只需几分钟即可针对 Windows 平台将你的首个 Node.js Hello World 部署到 Azure 应用服务。
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 08/01/2020
ms.custom: mvc, devcenter, seodec18
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 324919c1d085302e675e0bc9a370ce35f52a0f1d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102109398"
---
<!-- advanced for windows -->

## <a name="3-deploy-to-azure-app-service-from-visual-studio-code"></a>3.通过 Visual Studio Code 部署到 Azure 应用服务

1. 在 Visual Studio Code 中打开应用程序文件夹。

    ```bash
    code .
    ```

1. 在“Azure 应用服务”资源管理器中，选择“登录到 Azure...”，然后按照说明进行操作 。 登录后，资源管理器应会显示 Azure 订阅的名称。

    ![登录 Azure](../media/quickstart-nodejs/sign-in.png)

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

1. 选择“创建新 Web 应用...高级”，部署到 Windows 上的应用服务。

1. 为 Web 应用键入全局唯一的 <abbr title="应用名称的有效字符为“a-z”、“0-9”和“-”。">name</abbr> ，然后按 Enter。 
1. 选择“创建新的资源组”，然后输入资源组的名称，例如`AppServiceQS-rg`。
1. 选择 **Node.js 版本**，建议使用“LTS”。

    通知通道将显示正在为应用创建 Azure 资源。
1. 选择 **Windows** 作为操作系统。
1. 选择“创建新的应用服务计划”，输入该计划的名称（例如 `AppServiceQS-plan`），然后选择“F1 免费”作为定价层。 
1. 当系统提示 Application Insights 时，选择“立即跳过”。
1. 选择你附近或想要访问的资源附近的区域。

1. 系统提示更新工作区时选择“确定”，使之后的部署自动面向相同的应用服务 Web 应用。 

    :::image type="content" source="../media/quickstart-nodejs/save-configuration.png" alt-text="屏幕截图，显示提示更新工作区，已选中“是”按钮。":::

1. 再次右键单击应用服务的节点，然后选择“部署到 Web 应用”。

1. 再次右键单击应用服务的节点，并选择“浏览网站”。

    [报告问题](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

1. 部署完成后，选择提示中的“浏览网站”以查看全新部署的 Web 应用。 

<br/>
<details>
<summary><strong>故障排除</strong></summary>

如果无法完成这些步骤，请检查以下内容：

* 确保应用程序正在侦听 PORT 环境变量 `process.env.PORT` 提供的端口。

* 如果看到错误“你无权查看此目录或页面”，则表示应用程序可能无法正常启动。  查看日志输出以查找并修复错误。 

</details>

<br>

[报告问题](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&prepare-your-environment)


<br/>
<hr/>
