---
title: Tutorial:在 Node.js 和 Express Web 应用中让用户登录 | Azure
titleSuffix: Microsoft identity platform
description: 在本教程中，你将在 Web 应用中添加对登录用户的支持。
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: v-doeris
ms.openlocfilehash: 3f1f26acbba0f5830421e760d6a68a11f618fa85
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "101648984"
---
# <a name="tutorial-sign-in-users-in-a-nodejs--express-web-app"></a>Tutorial:在 Node.js 和 Express web 应用中让用户登录

在本教程中，你将构建一个登录用户的 Web 应用。 构建的 Web 应用使用[适用于 Node.js 的 Microsoft 身份验证库 (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node)。

请按照本教程中的步骤进行操作：

> [!div class="checklist"]
> - 在 Azure 门户中注册应用程序
> - 创建 Express web 应用项目
> - 安装身份验证库包
> - 添加应用注册详细信息
> - 为用户登录添加代码
> - 测试应用程序

## <a name="prerequisites"></a>先决条件

- [Node.js](https://nodejs.org/en/download/)
- [Visual Studio Code](https://code.visualstudio.com/download) 或其他代码编辑器

## <a name="register-the-application"></a>注册应用程序

首先，请完成[向 Microsoft 标识平台注册应用程序](quickstart-register-app.md)中的步骤来注册你的应用。

对于应用注册，请使用以下设置：

- 名称：`ExpressWebApp`（建议）
- 支持的帐户类型：任何组织目录中的帐户（任何 Azure AD 目录 - 多租户）和个人 Microsoft 帐户（例如 Skype、Xbox）
- 平台类型：**Web**
- 重定向 URI：`http://localhost:3000/redirect`
- 客户端机密：`*********`（记录此值以便在后面的步骤中使用 - 它只显示一次）

## <a name="create-the-project"></a>创建项目

创建文件夹来承载你的应用程序，例如 ExpressWebApp。

1. 首先，在终端中更改到你的项目目录，然后运行以下 `npm` 命令：

```console
    npm init -y
    npm install --save express
```

2. 接下来，创建名为 index.js 的文件并添加以下代码：

```JavaScript
    const express = require("express");
    const msal = require('@azure/msal-node');

    const SERVER_PORT = process.env.PORT || 3000;

    // Create Express App and Routes
    const app = express();

    app.listen(SERVER_PORT, () => console.log(`Msal Node Auth Code Sample app listening on port ${SERVER_PORT}!`))
```

现在，在端口 3000 上运行了一个简单的 Web 服务器。 项目的文件和文件夹结构应类似于下面这样：

```
ExpressWebApp/
├── index.js
└── package.json
```

## <a name="install-the-auth-library"></a>安装身份验证库

在终端中找到项目目录的根，并通过 NPM 安装 MSAL 节点包。

```console
    npm install --save @azure/msal-node
```

## <a name="add-app-registration-details"></a>添加应用注册详细信息

在之前创建的 index.js 文件中，添加以下代码：

```JavaScript
    // Before running the sample, you will need to replace the values in the config,
    // including the clientSecret
    const config = {
        auth: {
            clientId: "Enter_the_Application_Id",
            authority: "Enter_the_Cloud_Instance_Id_Here/Enter_the_Tenant_Id_here",
            clientSecret: "Enter_the_Client_secret"
        },
        system: {
            loggerOptions: {
                loggerCallback(loglevel, message, containsPii) {
                    console.log(message);
                },
                piiLoggingEnabled: false,
                logLevel: msal.LogLevel.Verbose,
            }
        }
    };
```

使用从 Azure 应用注册门户获取的值填写以下详细信息：

- `Enter_the_Tenant_Id_here` 应是以下各项之一：
  - 如果应用程序支持“此组织目录中的帐户”，请将此值替换为“租户 ID”或“租户名称”。 例如，`contoso.microsoft.com`。
  - 如果应用程序支持“任何组织目录中的帐户”，请将该值替换为“`organizations`”。
  - 如果应用支持“任何组织目录中的帐户和个人 Microsoft 帐户”，请将此值替换为“`common`”。
  - 若要限制对“仅限个人 Microsoft 帐户”的支持，请将此值替换为“`consumers`”。
- `Enter_the_Application_Id_Here`：已注册应用程序的应用程序（客户端）ID。
- `Enter_the_Cloud_Instance_Id_Here`：在其中注册应用程序的 Azure 云实例。
  - 对于主要（或全球）Azure 云，请输入 `https://login.microsoftonline.com`。
  - 对于国家/地区云（例如中国云），可以在[国家/地区云](authentication-national-cloud.md)中找到相应值。
- `Enter_the_Client_secret`：将此值替换为先前创建的客户端机密。 若要生成新密钥，请在 Azure 门户的应用注册设置中使用“证书和密码”。

> [!WARNING]
> 源代码中的任何纯文本机密都会增加安全风险。 本文使用纯文本客户端机密只是为了简单起见。 在机密客户端应用程序中使用[证书凭据](active-directory-certificate-credentials.md)（而不是客户端机密），尤其是计划部署到生产环境的应用。

## <a name="add-code-for-user-login"></a>为用户登录添加代码

在之前创建的 index.js 文件中，添加以下代码：

```JavaScript
    // Create msal application object
    const cca = new msal.ConfidentialClientApplication(config);

    app.get('/', (req, res) => {
        const authCodeUrlParameters = {
            scopes: ["user.read"],
            redirectUri: "http://localhost:3000/redirect",
        };

        // get url to sign user in and consent to scopes needed for application
        cca.getAuthCodeUrl(authCodeUrlParameters).then((response) => {
            res.redirect(response);
        }).catch((error) => console.log(JSON.stringify(error)));
    });

    app.get('/redirect', (req, res) => {
        const tokenRequest = {
            code: req.query.code,
            scopes: ["user.read"],
            redirectUri: "http://localhost:3000/redirect",
        };

        cca.acquireTokenByCode(tokenRequest).then((response) => {
            console.log("\nResponse: \n:", response);
            res.sendStatus(200);
        }).catch((error) => {
            console.log(error);
            res.status(500).send(error);
        });
    });
```

## <a name="test-sign-in"></a>测试登录

你已完成应用程序的创建，现在即可测试应用的功能。

1. 从项目文件夹的根目录中运行以下命令，启动 Node.js 控制台应用：

```console
   node index.js
```

2. 打开浏览器窗口并导航到 `http://localhost:3000`。 应该会出现登录屏幕：

:::image type="content" source="media/tutorial-v2-nodejs-webapp-msal/sign-in-screen.png" alt-text="显示 Azure AD 登录屏幕":::

3. 输入凭据后，你应该会看到一个同意屏幕，要求你批准应用的权限。

:::image type="content" source="media/tutorial-v2-nodejs-webapp-msal/consent-screen.png" alt-text="显示 Azure AD 许可屏幕":::

## <a name="how-the-application-works"></a>应用程序的工作原理

在本教程中，通过将 MSAL Node [ConfidentialClientApplication](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md) 对象的配置对象传递给它，将其初始化 (MsalConfig)，其中包含 Azure 门户上 Azure AD 应用注册获得的参数。 你创建的 Web 应用使用 [OAuth 2.0 授权代码授予流](./v2-oauth2-auth-code-flow.md)来登录用户并获取 ID 和访问令牌。

## <a name="next-steps"></a>后续步骤

如果你想要更深入了解 Microsoft 标识平台上的 Node.js & Express web 应用程序开发，请参阅由多部分组成的方案系列：

> [!div class="nextstepaction"]
> [场景：可将用户登录的 Web 应用](scenario-web-app-sign-user-overview.md)