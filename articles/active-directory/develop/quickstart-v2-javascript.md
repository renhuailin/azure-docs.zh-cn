---
title: 快速入门：在 JavaScript 单页应用中登录用户 | Azure
titleSuffix: Microsoft identity platform
description: 本快速入门介绍 JavaScript 应用如何调用 API，该 API 需要 Microsoft 标识平台颁发的访问令牌。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 04/11/2019
ms.author: marsma
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript, devx-track-js
ms.openlocfilehash: d49ab26695a061f47fd7e077fb9c609055998286
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129232577"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-javascript-spa"></a>快速入门：在 JavaScript SPA 中登录用户并获得访问令牌

在本快速入门中，你将下载并运行一个代码示例，该示例演示 JavaScript 单页应用程序 (SPA) 如何让用户登录并调用 Microsoft Graph。 此代码示例还演示如何获取访问令牌来调用 Microsoft Graph API 或任何 Web API。

有关说明，请参阅[示例工作原理](#how-the-sample-works)。

## <a name="prerequisites"></a>先决条件

* 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download)（用于编辑项目文件）

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>注册并下载快速入门应用程序
> 若要启动快速入门应用程序，请使用以下选项之一。
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>选项 1（快速）：注册并自动配置应用，然后下载代码示例
>
> 1. 转到 <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs" target="_blank">Azure 门户 - 应用注册</a>快速入门体验。
> 1. 输入应用程序的名称。
> 1. 在“支持的帐户类型”下，选择“任何组织目录中的帐户和个人 Microsoft 帐户”。 
> 1. 选择“注册”  。
> 1. 遵照说明下载内容，系统会自动配置新应用程序。
>
> ### <a name="option-2-manual-register-and-manually-configure-your-application-and-code-sample"></a>选项 2（手动）：注册并手动配置应用程序和代码示例
>
> #### <a name="step-1-register-your-application"></a>步骤 1：注册应用程序
>
> 1. 登录 <a href="https://portal.azure.com/" target="_blank">Azure 门户</a>。
> 1. 如果有权访问多个租户，请使用顶部菜单中的“目录 + 订阅”筛选器:::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::，选择要在其中注册应用程序的租户。
> 1. 搜索并选择“Azure Active Directory”  。
> 1. 在“管理”下，选择“应用注册” > “新建注册”  。
> 1. 输入应用程序的 **名称**。 应用的用户可能会看到此名称，你稍后可对其进行更改。
> 1. 在“支持的帐户类型”下，选择“任何组织目录中的帐户和个人 Microsoft 帐户”。 
> 1. 选择“注册”  。 在应用的“概述”页上，记下“应用程序(客户端) ID”值，供稍后使用 。
> 1. 本快速入门要求启用[隐式授权流](v2-oauth2-implicit-grant-flow.md)。 在“管理”下，选择“身份验证”。 
> 1. 在“平台配置” > “添加平台”下 。 选择“Web”。
> 1. 将“重定向 URI”值设为 `http://localhost:3000/`
> 1. 在“隐式授权和混合流”下，选择“访问令牌”和“ID 令牌”  。
> 1. 选择“配置” 。

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>步骤 1：在 Azure 门户中配置应用程序
> 为使本快速入门的代码示例正常运行，请添加重定向 URI `http://localhost:3000/` 并启用“隐式授权” 。
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [为我进行这些更改]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![已配置](media/quickstart-v2-javascript/green-check.png) 应用程序已使用这些属性进行配置。

#### <a name="step-2-download-the-project"></a>步骤 2：下载项目

> [!div renderon="docs"]
> 若要使用 Node.js 在 Web 服务器中运行项目，请[下载核心项目文件](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)。

> [!div renderon="portal" class="sxs-lookup"]
> 使用 Node.js 在 Web 服务器中运行项目

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [下载代码示例](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-javascript-app"></a>步骤 3：配置 JavaScript 应用
>
> 在 JavaScriptSPA 文件夹中，编辑 authConfig.js，并在 `msalConfig` 下设置 `clientID`、`authority` 和 `redirectUri` 值。
>
> ```javascript
>
>  // Config object to be passed to Msal on creation
>  const msalConfig = {
>    auth: {
>      clientId: "Enter_the_Application_Id_Here",
>      authority: "Enter_the_Cloud_Instance_Id_Here/Enter_the_Tenant_Info_Here",
>      redirectUri: "Enter_the_Redirect_Uri_Here",
>    },
>    cache: {
>      cacheLocation: "sessionStorage", // This configures where your cache will be stored
>      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
>    }
>  };
>
>```

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
>
> 其中：
> - `Enter_the_Application_Id_Here` 是已注册应用程序的应用程序（客户端）ID。
>
>    若要查找“应用程序(客户端) ID”的值，请转到 Azure 门户中应用的“概述”页 。
> - `Enter_the_Cloud_Instance_Id_Here` 是 Azure 云的实例。 对于主要或全球 Azure 云，只需输入 `https://login.microsoftonline.com/` 。 对于 **国家** 云（例如“中国”云），请参阅 [国家云](./authentication-national-cloud.md)。
> - `Enter_the_Tenant_info_here` 设置为以下选项之一：
>    - 如果应用程序支持“此组织目录中的帐户”，请将此值替换为“租户 ID”或“租户名称”（例如，contoso.microsoft.com`contoso.microsoft.com`）。
>
>    若要查找“目录(租户) ID”的值，请转到 Azure 门户中应用注册的“概述”页 。
>    - 如果应用程序支持“任何组织目录中的帐户”，请将该值替换为“`organizations`”。
>    - 如果应用支持“任何组织目录中的帐户和个人 Microsoft 帐户”，请将此值替换为“`common`”。 若要限制对“仅限个人 Microsoft 帐户”的支持，请将此值替换为“`consumers`”。
>
>    若要查找“支持的帐户类型”的值，请转到 Azure 门户中应用注册的“概述”页 。
> - `Enter_the_Redirect_Uri_Here` 为 `http://localhost:3000/`。
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>步骤 3：应用已配置并可以运行
> 我们已经为项目配置了应用属性的值。

> [!div renderon="docs"]
>
> 然后，仍在同一文件夹中，编辑 graphConfig.js 文件，以便为 `apiConfig` 对象设置 `graphMeEndpoint` 和 `graphMeEndpoint`。
> ```javascript
>   // Add here the endpoints for MS Graph API services you would like to use.
>   const graphConfig = {
>     graphMeEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me",
>     graphMailEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me/messages"
>   };
>
>   // Add here scopes for access token to be used at MS Graph API endpoints.
>   const tokenRequest = {
>       scopes: ["Mail.Read"]
>   };
> ```
>

> [!div renderon="docs"]
>
> 其中：
> - \<Enter_the_Graph_Endpoint_Here> 是将针对其进行 API 调用的终结点。 对于主要或全局 Microsoft Graph API 服务，只需输入 `https://graph.microsoft.com/`。 有关详细信息，请参阅[国家云部署](/graph/deployments)
>
> #### <a name="step-4-run-the-project"></a>步骤 4：运行项目

使用 [Node.js](https://nodejs.org/en/download/) 在 Web 服务器中运行项目：

1. 若要启动服务器，请从项目目录运行以下命令：
    ```cmd
    npm install
    npm start
    ```
1. 打开 Web 浏览器并转到 `http://localhost:3000/`。

1. 选择“登录”开始登录，然后调用 Microsoft Graph API。

在浏览器加载应用程序后，选择“登录”。 首次登录时，系统会提示你同意允许应用程序访问你的个人资料并登录。 成功登录后，你的用户个人资料信息应会显示在页面上。

## <a name="more-information"></a>详细信息

### <a name="how-the-sample-works"></a>示例工作原理

![示例 JavaScript SPA 的工作原理：1. SPA 启动登录。 2. SPA 从 Microsoft 标识平台获取 ID 令牌。 3. SPA 调用获取令牌。 4. Microsoft 标识平台返回 SPA 的访问令牌。 5. SPA 使用访问令牌向 Microsoft Graph API 发出 HTTP GET 请求。 6. Graph API 向 SPA 返回 HTTP 响应。](media/quickstart-v2-javascript/javascriptspa-intro.svg)

### <a name="msaljs"></a>msal.js

MSAL 库会将登录用户，并请求用于访问受 Microsoft 标识平台保护的 API 的令牌。 快速入门 *index.html* 文件包含对该库的引用：

```html
<script type="text/javascript" src="https://alcdn.msftauth.net/lib/1.2.1/js/msal.js" integrity="sha384-9TV1245fz+BaI+VvCjMYL0YDMElLBwNS84v3mY57pXNOt6xcUYch2QLImaTahcOP" crossorigin="anonymous"></script>
```

可将上述版本替换为 [MSAL.js 版本](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)中列出的最新发布版本。

另外，如果已安装 Node.js，则可通过 Node.js 包管理器 (npm) 下载最新版本：

```cmd
npm install msal
```

### <a name="msal-initialization"></a>MSAL 初始化

快速入门代码还演示了如何初始化 MSAL 库：

```javascript
  // Config object to be passed to Msal on creation
  const msalConfig = {
    auth: {
      clientId: "75d84e7a-40bx-f0a2-91b9-0c82d4c556aa", // this is a fake id
      authority: "https://login.microsoftonline.com/common",
      redirectUri: "http://localhost:3000/",
    },
    cache: {
      cacheLocation: "sessionStorage", // This configures where your cache will be stored
      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
    }
  };

const myMSALObj = new Msal.UserAgentApplication(msalConfig);
```

|Where  | 说明 |
|---------|---------|
|`clientId`     | 在 Azure 门户中注册的应用程序的应用程序 ID|
|`authority`    | （可选）支持帐户类型的颁发机构 URL，如前面的配置部分所述。 默认颁发机构为 `https://login.microsoftonline.com/common`。 |
|`redirectUri`     | 应用程序注册配置的答复/redirectUri。 在本例中为 `http://localhost:3000/`。 |
|`cacheLocation`  | （可选）针对身份验证状态设置浏览器存储。 默认为 sessionStorage。   |
|`storeAuthStateInCookie`  | （可选）用于存储身份验证请求状态的库，验证浏览器 Cookie 中的身份验证流时需要该状态。 此 Cookie 是针对 IE 和 Edge 浏览器设置的，目的是缓解某些[已知问题](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues)。 |

有关可用的可配置选项的详细信息，请阅读[初始化客户端应用程序](msal-js-initializing-client-applications.md)。

### <a name="sign-in-users"></a>用户登录

以下代码片段演示如何进行用户登录：

```javascript
// Add scopes for the id token to be used at Microsoft identity platform endpoints.
const loginRequest = {
    scopes: ["openid", "profile", "User.Read"],
};

myMSALObj.loginPopup(loginRequest)
    .then((loginResponse) => {
    //Login Success callback code here
}).catch(function (error) {
    console.log(error);
});
```

|Where  | 说明 |
|---------|---------|
| `scopes`   | （可选）包含在登录时为了获得用户许可而请求的范围。 例如：`[ "user.read" ]`（针对 Microsoft Graph）或 `[ "<Application ID URL>/scope" ]`（针对自定义 Web API，即 `api://<Application ID>/access_as_user`）。 |

另外，你可能希望使用 `loginRedirect` 方法将当前页重定向到登录页，而不是显示弹出窗口。

### <a name="request-tokens"></a>请求令牌

MSAL 使用三个方法来获取令牌：`acquireTokenRedirect`、`acquireTokenPopup` 和 `acquireTokenSilent`

#### <a name="get-a-user-token-silently"></a>以无提示方式获取用户令牌

`acquireTokenSilent` 方法处理令牌获取和续订，无需进行任何用户交互。 首次执行 `loginRedirect` 或 `loginPopup` 方法后，通常使用 `acquireTokenSilent` 方法获取用于访问受保护资源的令牌，以便进行后续调用。 进行请求或续订令牌的调用时，以静默方式进行。

```javascript

const tokenRequest = {
    scopes: ["Mail.Read"]
};

myMSALObj.acquireTokenSilent(tokenRequest)
    .then((tokenResponse) => {
        // Callback code here
        console.log(tokenResponse.accessToken);
    }).catch((error) => {
        console.log(error);
    });
```

|Where  | 说明 |
|---------|---------|
| `scopes`   | 包含请求的需要在 API 的访问令牌中返回的作用域。 例如：`[ "mail.read" ]`（针对 Microsoft Graph）或 `[ "<Application ID URL>/scope" ]`（针对自定义 Web API，即 `api://<Application ID>/access_as_user`）。|

#### <a name="get-a-user-token-interactively"></a>以交互方式获取用户令牌

在某些情况下，你要强制用户与 Microsoft 标识平台进行交互。 例如：
* 由于密码已过期，用户可能需要重新输入凭据。
* 应用程序正在请求访问用户需要许可的其他资源范围。
* 需要双重身份验证。

建议用于大多数应用程序的常用模式是先调用 `acquireTokenSilent`，然后捕获异常，然后再调用 `acquireTokenPopup`（或 `acquireTokenRedirect`），以便启动交互式请求。

调用 `acquireTokenPopup` 会显示用于登录的弹出窗口。 （调用 `acquireTokenRedirect` 则会将用户重定向到 Microsoft 标识平台。） 在该窗口中，为了进行交互，用户需要确认其凭证、为所需的资源提供许可，或者完成双重身份验证。

```javascript
// Add here scopes for access token to be used at MS Graph API endpoints.
const tokenRequest = {
    scopes: ["Mail.Read"]
};

myMSALObj.acquireTokenPopup(requestObj)
    .then((tokenResponse) => {
        // Callback code here
        console.log(tokenResponse.accessToken);
    }).catch((error) => {
        console.log(error);
    });
```

> [!NOTE]
> 本快速入门对 Microsoft Internet Explorer 使用 `loginRedirect` 和 `acquireTokenRedirect` 方法，因为 Internet Explorer 浏览器处理弹出窗口时会出现一个[已知问题](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues)。

## <a name="next-steps"></a>后续步骤

有关为本快速入门生成应用程序的更详细分步指导，请参阅：

> [!div class="nextstepaction"]
> [教程：从 JavaScript 单页应用程序 (SPA) 将用户登录并调用 Microsoft Graph API](tutorial-v2-javascript-spa.md)
