---
title: 为 Azure 静态 Web 应用设置本地开发
description: 了解如何为 Azure 静态 Web 应用设置本地开发环境
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: how-to
ms.date: 04/02/2021
ms.author: cshoe
ms.custom: devx-track-js
ms.openlocfilehash: a959a7b424a855f47a2e128b5c77727d21a9e0aa
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114449770"
---
# <a name="set-up-local-development-for-azure-static-web-apps"></a>为 Azure 静态 Web 应用设置本地开发

发布到云时，Azure Static Web Apps 站点会将许多协同工作的服务链接在一起，就像它们是同一个应用程序一样。 这些服务包括：

- 静态 Web 应用
- Azure Functions API
- 身份验证和授权服务
- 路由和配置服务

这些服务必须相互通信，Azure Static Web Apps 则在云中处理此集成。

但是，这些在本地运行的服务不会自动绑定在一起。

为提供与在 Azure 中相似的体验，[Azure Static Web Apps CLI](https://github.com/Azure/static-web-apps-cli) 提供以下服务：

- 本地静态站点服务器
- 前端框架开发服务器代理
- API 终结点代理 - 通过 Azure Functions Core Tools 提供
- 模拟身份验证和授权服务器
- 强制执行本地路由和配置设置

> [!NOTE]
> 使用前端框架构建的站点通常需要代理配置设置来正确处理 `api` 路由下的请求。 使用 Azure Static Web Apps CLI 时，代理位置值为 `/api`，不使用 CLI 时，代理位置值则为 `http://localhost:7071/api`。

## <a name="how-it-works"></a>工作原理

下图显示了在本地处理请求的方式。

:::image type="content" source="media/local-development/cli-conceptual.png" alt-text="Azure Static Web App CLI 请求和响应流":::

> [!IMPORTANT]
> 导航到 `http://localhost:4280` 以访问 CLI 提供服务的应用程序。

- 通过端口 `4280` 的请求将根据请求的类型转发到相应的服务器。

- 静态内容请求（如 HTML 或 CSS）由内部 CLI 静态内容服务器或前端框架服务器处理，以便进行调试。

- 身份验证和授权请求由仿真器处理，该仿真器为应用提供虚构的身份配置文件。

- Functions Core Tools 运行时处理对站点 API 的请求。

- 来自所有服务的响应都返回到浏览器，就像它们都是同一个应用程序一样。

## <a name="prerequisites"></a>先决条件

- 现有 Azure Static Web Apps 站点：如果你没有此站点，请从 [vanilla-api](https://github.com/staticwebdev/vanilla-api/generate?return_to=/staticwebdev/vanilla-api/generate) 入门应用开始。
- 使用 npm 的 [Node.js](https://nodejs.org)：运行 [Node.js LTS](https://nodejs.org) 版本，其中包括对 [npm](https://www.npmjs.com/) 的访问权限。
- [Visual Studio Code](https://code.visualstudio.com/)：用于调试 API 应用程序，但 CLI 不需要使用。

> [!NOTE]
> 若要[在本地使用 API](add-api.md#run-the-frontend-and-api-locally) 运行 `swa`，需要 Azure Functions Core Tools。

## <a name="get-started"></a>入门

打开终端，进入现有 Azure Static Web Apps 站点的根文件夹。

1. 安装 CLI。

    `npm install -g @azure/static-web-apps-cli`

1. 如果应用程序需要，请创建应用。

    为项目运行 `npm run build` 或等效命令。

1. 更改到应用的输出目录。 输出文件夹通常命名为 build 或类似名称。

1. 启动 CLI。

    `swa start`

1. 导航到 `http://localhost:4280`，在浏览器中查看应用。

### <a name="other-ways-to-start-the-cli"></a>启动 CLI 的其他方法

| 说明 | 命令 |
|--- | --- |
| 提供特定文件夹 | `swa start ./output-folder` |
| 使用正在运行的框架开发服务器 | `swa start http://localhost:3000` |
| 在文件夹中启动函数应用 | `swa start ./output-folder --api ./api` |
| 使用正在运行的函数应用 | `swa start ./output-folder --api http://localhost:7071` |

## <a name="authorization-and-authentication-emulation"></a>授权和身份验证仿真

Static Web Apps CLI 仿真在 Azure 中实现的[安全流](./authentication-authorization.md)。 用户登录时，你可以定义返回给应用的虚构身份配置文件。

例如，当你尝试导航到 `/.auth/login/github` 时，将返回一个页面，可供你定义身份配置文件。

> [!NOTE]
> 仿真器可与任何安全提供程序配合使用，而不只是 GitHub。

:::image type="content" source="media/local-development/auth-emulator.png" alt-text="本地身份验证和授权仿真器":::

仿真器提供了一个页面，可供你提供以下[客户端主体](./user-information.md#client-principal-data)值：

| Value | 说明 |
| --- | --- |
| **用户名** | 与安全提供程序关联的帐户名称。 该值在客户端主体中显示为 `userDetails` 属性，如果未提供，则自动生成该值。 |
| **用户 ID** | CLI 自动生成的值。  |
| **角色** | 角色名称列表，其中每个名称都位于新的一行。  |

登录后：

- 可使用 `/.auth/me` 终结点或函数终结点来检索用户的[客户端主体](./user-information.md)。

- 导航到 `/.auth/logout` 将清除客户端主体并注销模拟用户。

## <a name="debugging"></a>调试

一个 static web app 中有两个调试上下文。 第一个用于静态内容站点，第二个用于 API 函数。 通过允许 Static Web Apps CLI 使用开发服务器处理其中一个或全部两个上下文，即可进行本地调试。

以下步骤显示了使用开发服务器处理这两个调试上下文的常用方案。

1. 启动静态站点开发服务器。 此命令专用于你正在使用的前端框架，但通常采用的命令形式如 `npm run build`、`npm start` 或 `npm run dev`。

1. 在 Visual Studio Code 中打开 API 应用程序文件夹，然后启动调试会话。

1. 通过按顺序列出静态服务器和 API 服务器的地址，将这些地址传递给 `swa start` 命令。

    `swa start http://localhost:<DEV-SERVER-PORT-NUMBER> --api=http://localhost:7071`

以下屏幕截图显示了典型调试方案的终端：

通过 `npm run dev` 运行静态内容站点。

:::image type="content" source="media/local-development/run-dev-static-server.png" alt-text="静态站点开发服务器":::

Azure Functions API 应用程序在 Visual Studio Code 中运行调试会话。

:::image type="content" source="media/local-development/visual-studio-code-debugging.png" alt-text="Visual Studio Code API 调试":::

Static Web Apps CLI 已启动，使用了两个开发服务器。

:::image type="content" source="media/local-development/static-web-apps-cli-terminal.png" alt-text="Azure Static Web Apps CLI 终端":::

现在通过端口 `4280` 的请求将路由到静态内容开发服务器或 API 调试会话。

有关不同调试方案的详细信息，以及有关如何自定义端口和服务器地址的指导，请参阅 [Azure Static Web Apps CLI 存储库](https://github.com/Azure/static-web-apps-cli)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [配置应用程序](configuration.md)
