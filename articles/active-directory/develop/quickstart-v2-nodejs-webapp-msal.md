---
title: 快速入门：通过 MSAL Node 添加对 Node.js Web 应用的身份验证 | Azure
titleSuffix: Microsoft identity platform
description: 本快速入门介绍如何实现对 Node.js Web 应用和适用于 Node.js 的 Microsoft 身份验证库 (MSAL) 的身份验证。
services: active-directory
author: mmacy
manager: celested
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/22/2020
ms.author: marsma
ms.custom: aaddev, scenarios:getting-started, languages:js, devx-track-js
ms.openlocfilehash: 3421c519c4ab059cda1d413db3906b3f1ed76ca0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128594681"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-node-web-app-using-the-auth-code-flow"></a>快速入门：使用授权代码流在 Node Web 应用中让用户登录并获取访问令牌

在本快速入门中，你将下载并运行一个代码示例，该示例演示 Node.js Web 应用如何使用授权代码流来让用户登录。 此代码示例还演示如何获取访问令牌来调用 Microsoft Graph API。

有关说明，请参阅[示例工作原理](#how-the-sample-works)。

本快速入门通过身份验证代码流使用适用于 Node.js 的 Microsoft 身份验证库 (MSAL Node)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 [免费创建 Azure 订阅](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) 或其他代码编辑器

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>注册并下载快速入门应用程序
>
> #### <a name="step-1-register-your-application"></a>步骤 1：注册应用程序
>
> 1. 登录 <a href="https://portal.azure.com/" target="_blank">Azure 门户</a>。
> 1. 如果有权访问多个租户，请使用顶部菜单中的“目录 + 订阅”筛选器 :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::，以切换到要在其中注册应用程序的租户。
> 1. 在“管理”下，选择“应用注册” > “新建注册”  。
> 1. 输入应用程序的 **名称**。 应用的用户可能会看到此名称，你稍后可对其进行更改。
> 1. 在“支持的帐户类型”下，选择“任何组织目录中的帐户和个人 Microsoft 帐户”。 
> 1. 将“重定向 URI”值设为 `http://localhost:3000/redirect`
> 1. 选择“注册”。
> 1. 在应用的“概述”页上，记下“应用程序(客户端) ID”值，供稍后使用 。
> 1. 在“管理”下，选择“证书和机密” > “新建客户端机密”  。  将说明留空并保留默认过期时间，然后选择“添加”。
> 1. 记下客户端密码的值供稍后使用。

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-the-application-in-azure-portal"></a>步骤 1：在 Azure 门户中配置应用程序
> 要使此快速入门的代码示例正常运行，需要创建一个客户端密码，并添加以下答复 URL：`http://localhost:3000/redirect`。
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [执行此更改]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![已配置](media/quickstart-v2-windows-desktop/green-check.png) 应用程序已使用这些属性进行配置。

#### <a name="step-2-download-the-project"></a>步骤 2：下载项目

> [!div renderon="docs"]
> 若要使用 Node.js 在 Web 服务器中运行项目，请[下载核心项目文件](https://github.com/Azure-Samples/ms-identity-node/archive/main.zip)。

> [!div renderon="portal" class="sxs-lookup"]
> 使用 Node.js 在 Web 服务器中运行项目。

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [下载代码示例](https://github.com/Azure-Samples/ms-identity-node/archive/main.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-node-app"></a>步骤 3：配置 Node 应用
>
> 提取项目，打开 ms-identity-node-main 文件夹，然后打开 index.js 文件 。
>
> 将 `clientID` 值设置为应用程序（客户端）ID，然后将 `clientSecret` 值设置为客户端密码。
>
>```javascript
>const config = {
>    auth: {
>        clientId: "Enter_the_Application_Id_Here",
>        authority: "https://login.microsoftonline.com/common",
>        clientSecret: "Enter_the_Client_Secret_Here"
>    },
>    system: {
>        loggerOptions: {
>            loggerCallback(loglevel, message, containsPii) {
>                console.log(message);
>            },
>            piiLoggingEnabled: false,
>            logLevel: msal.LogLevel.Verbose,
>        }
>    }
>};
> ```

> [!div renderon="docs"]
>
> 修改 `config` 部分中的值：
>
> - `Enter_the_Application_Id_Here` 是已注册的应用程序的应用程序（客户端）ID。
>
>    若要查找应用程序（客户端）ID，请转到 Azure 门户中应用注册的概述页面。
> - `Enter_the_Client_Secret_Here` 是注册的应用程序的客户端密码。
>
>    若要检索客户端密码或生成新的客户端密码，请在“管理”下选择“证书和机密” 。
>
> 默认 `authority` 值表示主要（全局）Azure 云：
>
> ```javascript
> authority: "https://login.microsoftonline.com/common",
> ```
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>步骤 3：应用已配置并可以运行
>
> [!div renderon="docs"]
>
> #### <a name="step-4-run-the-project"></a>步骤 4：运行项目

使用 Node.js 运行项目。

1. 若要启动服务器，请从项目目录中运行以下命令：

    ```console
    npm install
    npm start
    ```

1. 转到 `http://localhost:3000/`。

1. 选择“登录”启动登录过程。

    首次登录时，系统会提示你允许应用程序访问你的个人资料并将你登录。 成功登录后，你将在命令行中看到一条日志消息。

## <a name="more-information"></a>详细信息

### <a name="how-the-sample-works"></a>示例工作原理

该示例在 localhost 端口 3000 上托管 Web 服务器。 当 Web 浏览器访问此站点时，该示例会立即将用户重定向到 Microsoft 身份验证页面。 因此，该示例不包含任何 HTML，也不显示元素。 身份验证成功后会显示消息“成功”。

### <a name="msal-node"></a>MSAL Node

MSAL Node 库会让用户登录，并请求用于访问受 Microsoft 标识平台保护的 API 的令牌。 可通过 Node.js 包管理器 (npm) 下载最新版本：

```console
npm install @azure/msal-node
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [向现有 Web 应用添加身份验证 - GitHub 代码示例 >](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/msal-node-samples/auth-code)
