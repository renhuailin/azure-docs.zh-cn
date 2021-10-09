---
title: 快速入门：从 Node.js 桌面应用调用 Microsoft Graph | Azure
titleSuffix: Microsoft identity platform
description: 在本快速入门中，你将了解 Node.js Electron 桌面应用程序如何让用户登录并获取访问令牌以调用受 Microsoft 标识平台终结点保护的 API
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.date: 02/17/2021
ms.author: v-doeris
ms.openlocfilehash: 8e791392386f6a2f74f6d59e7999afeb272249aa
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128605746"
---
# <a name="quickstart-acquire-an-access-token-and-call-the-microsoft-graph-api-from-an-electron-desktop-app"></a>快速入门：获取访问令牌并从 Electron 桌面应用调用 Microsoft Graph API

在本快速入门中将下载并运行一个代码示例，该示例演示 Electron 桌面应用程序如何让用户登录并获取访问令牌来调用 Microsoft Graph API。

本快速入门通过[使用 PKCE 的身份验证代码流](v2-oauth2-auth-code-flow.md)使用[适用于 Node.js 的 Microsoft 身份验证库 (MSAL Node)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node)。

## <a name="prerequisites"></a>先决条件

* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) 或其他代码编辑器

> [!div renderon="docs"]
> ## <a name="register-and-download-the-sample-application"></a>注册并下载示例应用程序
>
> 请按照以下步骤开始使用。
>
> #### <a name="step-1-register-the-application"></a>步骤 1：注册应用程序
> 若要手动注册应用程序并将应用的注册信息添加到解决方案，请执行以下步骤：
>
> 1. 登录 <a href="https://portal.azure.com/" target="_blank">Azure 门户</a>。
> 1. 如果有权访问多个租户，请使用顶部菜单中的“目录 + 订阅”筛选器 :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::，以切换到要在其中注册应用程序的租户。
> 1. 搜索并选择“Azure Active Directory”。
> 1. 在“管理”下，选择“应用注册” > “新建注册”  。
> 1. 输入应用程序的名称（例如 `msal-node-desktop`）。 应用的用户可能会看到此名称，你稍后可对其进行更改。
> 1. 选择“注册”以创建应用程序。
> 1. 在“管理”下，选择“身份验证”。 
> 1. 选择“添加平台” > “移动和桌面应用程序” 。
> 1. 在“重定向 URI”部分中，输入 `msal://redirect`。
> 1. 选择“配置” 。

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-the-application-in-azure-portal"></a>步骤 1：在 Azure 门户中配置应用程序
> 要使此快速入门的代码示例正常运行，需要将答复 URL 添加为 msal://redirect。
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [执行此更改]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![已配置](media/quickstart-v2-windows-desktop/green-check.png) 应用程序已使用这些属性进行配置。

#### <a name="step-2-download-the-electron-sample-project"></a>步骤 2：下载 Electron 示例项目

> [!div renderon="docs"]
> [下载代码示例](https://github.com/azure-samples/ms-identity-javascript-nodejs-desktop/archive/main.zip)

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [下载代码示例](https://github.com/azure-samples/ms-identity-javascript-nodejs-desktop/archive/main.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-electron-sample-project"></a>步骤 3：配置 Electron 示例项目
>
> 1. 将 zip 文件提取到靠近磁盘根目录的本地文件夹，例如 C:/Azure-Samples。
> 1. 编辑 .env，将字段 `TENANT_ID` 和 `CLIENT_ID` 的值替换为以下代码片段：
>
>    ```
>    "TENANT_ID": "Enter_the_Tenant_Id_Here",
>    "CLIENT_ID": "Enter_the_Application_Id_Here"
>    ```
>    其中：
>    - `Enter_the_Application_Id_Here` - 是已注册应用程序的 **应用程序（客户端）ID**。
>    - `Enter_the_Tenant_Id_Here` - 将此值替换为 **租户 ID** 或 **租户名称**（例如 contoso.microsoft.com）
>
> > [!TIP]
> > 若要查找“应用程序(客户端) ID”、“目录(租户) ID”的值，请转到 Azure 门户中应用的“概览”页。  

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>步骤 4：运行应用程序

> [!div renderon="docs"]
> #### <a name="step-4-run-the-application"></a>步骤 4：运行应用程序

需要安装一次此示例的依赖项：

```console
npm install
```

然后，通过命令提示符或控制台运行应用程序：

```console
npm start
```

应该会看到应用程序的 UI，其中包含一个“登录”按钮。

## <a name="about-the-code"></a>关于代码

下面讨论了示例应用程序的一些重要方面。

### <a name="msal-node"></a>MSAL Node

[MSAL Node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) 是一个用于登录用户和请求令牌的库，此类令牌用于访问受 Microsoft 标识平台保护的 API。 若要详细了解如何搭配使用 MSAL Node 和桌面应用，请参阅[本文](scenario-desktop-overview.md)。

可通过运行以下 npm 命令安装 MSAL Node。

```console
npm install @azure/msal-node --save
```

### <a name="msal-initialization"></a>MSAL 初始化

可通过添加以下代码为 MSAL Node 添加引用：

```javascript
const { PublicClientApplication } = require('@azure/msal-node');
```

然后，使用以下代码对 MSAL 进行初始化：

```javascript
const MSAL_CONFIG = {
    auth: {
        clientId: "Enter_the_Application_Id_Here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_Id_Here",
    },
};

const pca = new PublicClientApplication(MSAL_CONFIG);
```

> | 其中： |说明 |
> |---------|---------|
> | `clientId` | 是在 Azure 门户中注册的应用程序的 **应用程序(客户端) ID**。 可以在 Azure 门户的应用的“概览”页中找到此值。 |
> | `authority`    | 用户要进行身份验证的 STS 终结点。 对于公有云，通常为 `https://login.microsoftonline.com/{tenant}`，其中 {tenant} 是租户名称或租户 ID。|

### <a name="requesting-tokens"></a>请求令牌

在使用 PKCE 的授权代码流的第一个阶段，准备并发送具有相应参数的授权代码请求。 然后，在流的第二个阶段侦听授权代码响应。 获取代码后，将其交换以获取令牌。

```javascript
// The redirect URI you setup during app registration with a custom file protocol "msal"
const redirectUri = "msal://redirect";

const cryptoProvider = new CryptoProvider();

const pkceCodes = {
    challengeMethod: "S256", // Use SHA256 Algorithm
    verifier: "", // Generate a code verifier for the Auth Code Request first
    challenge: "" // Generate a code challenge from the previously generated code verifier
};

/**
 * Starts an interactive token request
 * @param {object} authWindow: Electron window object
 * @param {object} tokenRequest: token request object with scopes
 */
async function getTokenInteractive(authWindow, tokenRequest) {

    /**
     * Proof Key for Code Exchange (PKCE) Setup
     *
     * MSAL enables PKCE in the Authorization Code Grant Flow by including the codeChallenge and codeChallengeMethod
     * parameters in the request passed into getAuthCodeUrl() API, as well as the codeVerifier parameter in the
     * second leg (acquireTokenByCode() API).
     */

    const {verifier, challenge} = await cryptoProvider.generatePkceCodes();

    pkceCodes.verifier = verifier;
    pkceCodes.challenge = challenge;

    const authCodeUrlParams = {
        redirectUri: redirectUri
        scopes: tokenRequest.scopes,
        codeChallenge: pkceCodes.challenge, // PKCE Code Challenge
        codeChallengeMethod: pkceCodes.challengeMethod // PKCE Code Challenge Method
    };

    const authCodeUrl = await pca.getAuthCodeUrl(authCodeUrlParams);

    // register the custom file protocol in redirect URI
    protocol.registerFileProtocol(redirectUri.split(":")[0], (req, callback) => {
        const requestUrl = url.parse(req.url, true);
        callback(path.normalize(`${__dirname}/${requestUrl.path}`));
    });

    const authCode = await listenForAuthCode(authCodeUrl, authWindow); // see below

    const authResponse = await pca.acquireTokenByCode({
        redirectUri: redirectUri,
        scopes: tokenRequest.scopes,
        code: authCode,
        codeVerifier: pkceCodes.verifier // PKCE Code Verifier
    });

    return authResponse;
}

/**
 * Listens for auth code response from Azure AD
 * @param {string} navigateUrl: URL where auth code response is parsed
 * @param {object} authWindow: Electron window object
 */
async function listenForAuthCode(navigateUrl, authWindow) {

    authWindow.loadURL(navigateUrl);

    return new Promise((resolve, reject) => {
        authWindow.webContents.on('will-redirect', (event, responseUrl) => {
            try {
                const parsedUrl = new URL(responseUrl);
                const authCode = parsedUrl.searchParams.get('code');
                resolve(authCode);
            } catch (err) {
                reject(err);
            }
        });
    });
}
```

> |其中：| 说明 |
> |---------|---------|
> | `authWindow` | 当前 Electron 窗口正在运行中。 |
> | `tokenRequest` | 包含所请求的范围，例如 `"User.Read"`（针对 Microsoft Graph）或 `"api://<Application ID>/access_as_user"`（针对自定义 Web API）。 |

## <a name="next-steps"></a>后续步骤

若要详细了解如何通过 MSAL Node 进行 Electron 桌面应用开发，请参阅教程：

> [!div class="nextstepaction"]
> [教程：在 Electron 桌面应用中让用户登录并调用 Microsoft Graph API](tutorial-v2-nodejs-desktop.md)