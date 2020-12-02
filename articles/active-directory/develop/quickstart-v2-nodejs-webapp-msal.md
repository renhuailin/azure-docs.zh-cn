---
title: 快速入门：通过 MSAL Node 添加对 Node Web 应用的身份验证 | Azure
titleSuffix: Microsoft identity platform
description: 本快速入门介绍如何实现对 Node.js Web 应用和适用于 Node.js 的 Microsoft 身份验证库 (MSAL) 的身份验证。
services: active-directory
author: amikuma
manager: saeeda
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/22/2020
ms.author: amikuma
ms.custom: aaddev, scenarios:getting-started, languages:js, devx-track-js
ms.openlocfilehash: ac400efe66ab740f5fb87977ac531cbe465e3f91
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2020
ms.locfileid: "95995143"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-node-web-app-using-the-auth-code-flow"></a>快速入门：使用授权代码流在 Node Web 应用中让用户登录并获取访问令牌

在本快速入门中，你将下载并运行一个代码示例，该示例演示 Node.js Web 应用如何通过使用授权代码流让用户登录。 此代码示例还演示如何获取访问令牌来调用 Microsoft Graph API。 

有关说明，请参阅[示例工作原理](#how-the-sample-works)。

本快速入门通过身份验证代码流使用适用于 Node.js 的 Microsoft 身份验证库 (MSAL Node)。

> [!IMPORTANT]
> MSAL Node [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建 Azure 订阅](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) 或其他代码编辑器

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>注册并下载快速入门应用程序
>
> #### <a name="step-1-register-your-application"></a>步骤 1：注册应用程序
>
> 1. 登录 [Azure 门户](https://portal.azure.com)。
> 1. 如果你的帐户有权访问多个租户，请在右上角选择该帐户，然后将门户会话设置为要使用的 Azure AD 租户。
> 1. 选择[“应用注册”](https://go.microsoft.com/fwlink/?linkid=2083908)。
> 1. 选择“新注册”。
> 1. “注册应用程序”页显示后，请输入应用程序的名称。
> 1. 在“支持的帐户类型”下，选择“任何组织目录中的帐户和个人 Microsoft 帐户”。 
> 1. 将“重定向 URI”值设为 `http://localhost:3000/redirect`
> 1. 选择“注册”。 
> 1. 在应用的“概述”页上，记下“应用程序(客户端) ID”值，供稍后使用 。
> 1. 在“证书和机密”下，选择“新建客户端密码” 。  将说明留空并保留默认过期时间，然后单击“添加”。
> 1. 记下“客户端密码”的值以供稍后使用 。

#### <a name="step-2-download-the-project"></a>步骤 2：下载项目

> [!div renderon="docs"]
> 若要使用 Node.js 在 Web 服务器中运行项目，请[下载核心项目文件](https://github.com/Azure-Samples/ms-identity-node/archive/main.zip)。

> [!div renderon="portal" class="sxs-lookup"]
> 使用 Node.js 在 Web 服务器中运行项目

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [下载代码示例](https://github.com/Azure-Samples/ms-identity-node/archive/main.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-node-app"></a>步骤 3：配置 Node 应用
>
> 提取项目，打开文件夹“ms-identity-node-main”，然后打开“index.js”文件 。
> 将 `clientID` 设置为“应用程序(客户端) ID”。
> 将 `clientSecret` 设置为“客户端密码”的值 。
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
> 修改 `config` 部分中的值，如下所述：
>
> - `Enter_the_Application_Id_Here` 是已注册应用程序的应用程序（客户端）ID。
> - `Enter_the_Client_Secret_Here` 是注册的应用程序的“客户端密码”的值 。
>
> 默认 `authority` 值表示主要（全局）Azure 云：
>
> ```javascript
> authority: "https://login.microsoftonline.com/common",
> ```
>
> > [!TIP]
> > 若要查找“应用程序(客户端) ID”的值，请转到 Azure 门户中应用注册的“概览”页面 。 转到“证书和机密”下，以检索或生成新的客户端密码 。
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>步骤 3：应用已配置并可以运行
>
> [!div renderon="docs"]
>
> #### <a name="step-4-run-the-project"></a>步骤 4：运行项目

使用 Node.js 运行项目：

1. 若要启动服务器，请从项目目录中运行以下命令：
    ```console
    npm install
    npm start
    ```
1. 浏览到 `http://localhost:3000/`。

1. 选择“登录”启动登录过程。

    首次登录时，系统会提示你允许应用程序访问你的个人资料并将你登录。 成功登录后，你将在命令行中看到一条日志消息。

## <a name="more-information"></a>详细信息

### <a name="how-the-sample-works"></a>示例工作原理

该示例在运行时将在 localhost 端口 3000 上托管 Web 服务器。 当 Web 浏览器访问此站点时，该示例会立即将用户重定向到 Microsoft 身份验证页面。 因此，该示例不包含任何 html，也不显示元素。 身份验证成功后会显示消息“成功”。

### <a name="msal-node"></a>MSAL Node

MSAL Node 库会让用户登录，并请求用于访问受 Microsoft 标识平台保护的 API 的令牌。 可通过 Node.js 包管理器 (npm) 下载最新版本：

```console
npm install @azure/msal-node
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [向现有 Web 应用添加身份验证 - GitHub 代码示例 >](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/msal-node-samples/standalone-samples/auth-code)
