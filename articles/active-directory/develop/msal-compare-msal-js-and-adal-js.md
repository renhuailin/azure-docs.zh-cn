---
title: 将 JavaScript 应用程序从 ADAL.js 迁移到 MSAL.js | Azure
titleSuffix: Microsoft identity platform
description: 如何更新现有 JavaScript 应用程序，以使用 Microsoft 身份验证库 (MSAL) 而不是 Active Directory 身份验证库 (ADAL) 完成身份验证和授权。
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 07/06/2021
ms.author: v-doeris
ms.custom: has-adal-ref
ms.openlocfilehash: 541200501fd9bd3cc1883283bc308445d0e4115e
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129232236"
---
# <a name="how-to-migrate-a-javascript-app-from-adaljs-to-msaljs"></a>如何将 JavaScript 应用从 ADAL.js 迁移到 MSAL.js

[适用于 JavaScript 的 Microsoft 身份验证库](https://github.com/AzureAD/microsoft-authentication-library-for-js)（MSAL.js，也称为 msal-browser）2.x 是我们建议对 Microsoft 标识平台上的 JavaScript 应用程序使用的身份验证库。 本文重点介绍需要做出哪些更改才能将使用 ADAL.js 的应用迁移到 MSAL.js 2.x

> [!NOTE]
> 强烈建议使用 MSAL.js 2.x 而不是 MSAL.js 1.x。 授权代码授予流更安全，并且可以使单页应用程序保持良好的用户体验（尽管 Safari 等浏览器实施了隐私保护措施来阻止第三方 Cookie），此外，它还具有其他许多优势。

## <a name="prerequisites"></a>先决条件

- 必须在应用注册门户中将“平台” / “回复 URL 类型”设置为“单页应用程序”（如果在应用注册中添加了其他平台，例如“Web”，则需要确保重定向 URI 不重叠   。 请参阅：[重定向 URI 限制](./reply-url.md)）
- 必须为 MSAL.js 依赖的 ES6 功能（例如承诺）提供[填充代码](./msal-js-use-ie-browser.md)，这样才能在 Internet Explorer 中运行你的应用
- 确保已将 Azure AD 应用迁移到 [v2 终结点](../azuread-dev/azure-ad-endpoint-comparison.md)（如果尚未这样做）

## <a name="install-and-import-msal"></a>安装并导入 MSAL

可通过两种方式安装 MSAL.js 2.x 库：

### <a name="via-npm"></a>通过 NPM：

```console
npm install @azure/msal-browser
```

然后，根据所用的模块系统，按如下所示导入该库：

```javascript
import * as msal from "@azure/msal-browser"; // ESM

const msal = require('@azure/msal-browser'); // CommonJS
```

### <a name="via-cdn"></a>通过 CDN：

在 HTML 文档的 header 节中加载以下脚本：

```html
<!DOCTYPE html>
<html>
  <head>
    <script type="text/javascript" src="https://alcdn.msauth.net/browser/2.14.2/js/msal-browser.min.js"></script>
  </head>
</html>
```

有关其他 CDN 链接以及使用 CDN 时的最佳做法，请参阅：[CDN 用法](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/cdn-usage.md)

## <a name="initialize-msal"></a>初始化 MSAL

在 ADAL.js 中实例化 [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-js/wiki/Config-authentication-context#authenticationcontext) 类，然后该类将公开可用于实现身份验证的方法（`login`、`acquireTokenPopup` 等）。 此对象用作应用程序与授权服务器或标识提供者之间的连接的表示形式。 初始化时，唯一的必需参数是 clientId：

```javascript
window.config = {
  clientId: "YOUR_CLIENT_ID"
};

var authContext = new AuthenticationContext(config);
```

在 MSAL.js 中，请改为实例化 [PublicClientApplication](https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal_browser.publicclientapplication.html) 类。 与 ADAL.js 一样，构造函数需要一个至少包含 `clientId` 参数的[配置对象](#configure-msal)。 有关详细信息，请参阅：[初始化 MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/initialization.md)

```javascript
const msalConfig = {
  auth: {
      clientId: 'YOUR_CLIENT_ID'
  }
};

const msalInstance = new msal.PublicClientApplication(msalConfig);
```

在 ADAL.js 和 MSAL.js 中，如果未指定颁发机构 URI，则默认会使用 `https://login.microsoftonline.com/common`。

> [!NOTE]
> 如果在 v2.0 中使用 `https://login.microsoftonline.com/common` authority，你将允许用户使用任何 Azure AD 组织或个人 Microsoft 帐户 (MSA) 进行登录。 在 MSAL.js 中，如果你想要限制用户登录到任何 Azure AD 帐户（该行为同样适用于 ADAL.js），请改用 `https://login.microsoftonline.com/organizations`。

## <a name="configure-msal"></a>配置 MSAL

初始化 [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-js/wiki/Config-authentication-context#authenticationcontext) 时使用的一些 [ADAL.js 中的配置选项](https://github.com/AzureAD/azure-activedirectory-library-for-js/wiki/Config-authentication-context)在 MSAL.js 中已弃用，同时 MSAL.js 中引入了一些新配置选项。 请参阅[可用选项的完整列表](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/configuration.md)。 重要的是，在获取令牌期间可以替代其中的许多选项（`clientId` 除外），因此你可以根据每个请求设置这些选项。 例如，可以使用与初始化期间获取令牌时设置的 URI 不同的颁发机构 URI 或重定向 URI 。

此外，不再需要通过配置选项指定登录体验（即，是要使用弹出窗口还是重定向页面）。 相反，`MSAL.js` 通过 `PublicClientApplication` 实例公开 `loginPopup` 和 `loginRedirect` 方法。

## <a name="enable-logging"></a>启用日志记录

在 ADAL.js 中，你可以在代码中的任何位置单独配置日志记录：

```javascript
window.config = {
  clientId: "YOUR_CLIENT_ID"
};

var authContext = new AuthenticationContext(config);

var Logging = {
  level: 3,
  log: function (message) {
      console.log(message);
  },
  piiLoggingEnabled: false
};


authContext.log(Logging)
```

在 MSAL.js 中，日志记录是配置选项的一部分，将在初始化 `PublicClientApplication` 期间创建：

```javascript
const msalConfig = {
  auth: {
      // authentication related parameters
  },
  cache: {
      // cache related parameters
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
}

const msalInstance = new msal.PublicClientApplication(msalConfig);
```

## <a name="switch-to-msal-api"></a>切换到 MSAL API

ADAL.js 中的某些公共方法在 MSAL.js 中有等效方法：

| ADAL                                | MSAL                              | 说明                                        |
|-------------------------------------|-----------------------------------|----------------------------------------------|
| `acquireToken`                      | `acquireTokenSilent`              | 已重命名，现在需要[帐户](https://azuread.github.io/microsoft-authentication-library-for-js/ref/modules/_azure_msal_common.html#accountinfo)对象 |
| `acquireTokenPopup`                 | `acquireTokenPopup`               | 现在是异步的，并返回承诺              |
| `acquireTokenRedirect`              | `acquireTokenRedirect`            | 现在是异步的，并返回承诺              |
| `handleWindowCallback`              | `handleRedirectPromise`           | 使用重定向体验时需要          |
| `getCachedUser`                     | `getAllAccounts`                  | 已重命名，现在会返回帐户数组。|

其他方法已弃用，不过 MSAL.js 提供了新方法：

| ADAL                              | MSAL                            | 说明                                            |
|-----------------------------------|---------------------------------|--------------------------------------------------|
| `login`                           | 不适用                             | 已弃用。 使用 `loginPopup` 或 `loginRedirect`  |
| `logOut`                          | 空值                             | 已弃用。 使用 `logoutPopup` 或 `logoutRedirect`|
| 空值                               | `loginPopup`                    |                                                  |
| 不可用                               | `loginRedirect`                 |                                                  |
| 不可用                               | `logoutPopup`                   |                                                  |
| 空值                               | `logoutRedirect`                |                                                  |
| 空值                               | `getAccountByHomeId`            | 按主目录 ID（OID + 租户 ID）筛选帐户    |
| 空值                               | `getAccountLocalId`             | 按本地 ID 筛选帐户（对于 ADFS 非常有用）   |
| 空值                               | `getAccountUsername`            | 按用户名（如果存在）筛选帐户         |

此外，与 ADAL.js 不同，MSAL.js 是在 TypeScript 中实现的，因此它会公开各种可在项目中使用的类型和接口。 有关详细信息，请参阅 [MSAL.js API 参考](https://azuread.github.io/microsoft-authentication-library-for-js/ref/)。

## <a name="use-scopes-instead-of-resources"></a>使用作用域而不是资源

Azure AD v1.0 与 v2.0 终结点之间的一个重要区别在于资源的访问方式 。 将 ADAL.js 与 v1.0 终结点配合使用时，首先需要在应用注册门户中注册权限，然后为资源（例如 Microsoft Graph）请求访问令牌，如下所示：

```javascript
authContext.acquireTokenRedirect("https://graph.microsoft.com", function (error, token) {
  // do something with the access token
});
```

MSAL.js 既支持 v1.0 终结点，也支持 v2.0 终结点 。 v2.0 终结点采用以范围为中心的模型来访问资源。 因此，你在为资源请求访问令牌时，还需要指定该资源的作用域：

```javascript
msalInstance.acquireTokenRedirect({
  scopes: ["https://graph.microsoft.com/User.Read"]
});
```

以作用域为中心的模型有一个优点，即可以使用动态作用域。 使用 v1.0 终结点生成应用程序时，需要注册该应用程序所需的完整权限集（称为“静态范围”），以便用户在登录时同意这些权限。 在 v2.0 中，你可以随时按需使用作用域参数来请求相关权限（因此称为“动态作用域”）。 这样，用户便可以提供对作用域的增量同意。 因此，如果你最初只是希望用户登录到你的应用程序，而不需要任何类型的访问权限，则可以这样做。 如果后来需要读取用户的日历，则可以在 acquireToken 方法中请求日历范围，并获取用户的许可。 有关详细信息，请参阅[资源和作用域](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/resources-and-scopes.md)

## <a name="use-promises-instead-of-callbacks"></a>使用承诺而不是回叫

在 ADAL.js 中，回调用于在成功完成身份验证并获取响应后执行的任何操作：

```javascript
authContext.acquireTokenPopup(resource, extraQueryParameter, claims, function (error, token) {
  // do something with the access token
});
```

在 MSAL.js 中，需要改用承诺：

```javascript
msalInstance.acquireTokenPopup({
      scopes: ["User.Read"] // shorthand for https://graph.microsoft.com/User.Read
  }).then((response) => {
      // do something with the auth response
  }).catch((error) => {
      // handle errors
  });
```

还可以使用 ES8 附带的 async/await 语法：

```javascript
const getAccessToken = async() => {
  try {
      const authResponse = await msalInstance.acquireTokenPopup({
          scopes: ["User.Read"]
      });
  } catch (error) {
      // handle errors
  }
}
```

## <a name="cache-and-retrieve-tokens"></a>缓存和检索令牌

与 ADAL.js 一样，MSAL.js 使用 [Web 存储 API](https://developer.mozilla.org/docs/Web/API/Web_Storage_API) 在浏览器存储中缓存令牌和其他身份验证项目。 建议使用 `sessionStorage` 选项（请参阅：[配置](#configure-msal)），因为它可以更安全地存储用户获取的令牌，但使用 `localStorage` 可以在不同的标签页和用户会话中实现[单一登录](./msal-js-sso.md)。

重要的是，最好不要直接访问缓存， 而应该使用适当的 MSAL.js API 来检索访问令牌或用户帐户等身份验证项目。

## <a name="renew-tokens-with-refresh-tokens"></a>使用刷新令牌续订令牌

ADAL.js 使用 [OAuth 2.0 隐式流](./v2-oauth2-implicit-grant-flow.md)，出于安全原因，此流不会返回刷新令牌（刷新令牌的生存期比访问令牌更长，因此一旦落入恶意行动者之手，安全风险就更高）。 因此，ADAL.js 使用隐藏的 Iframe 执行令牌续订，这样可以避免反复提示用户进行身份验证。

凭借支持 PKCE 的授权代码流，使用 MSAL.js 2.x 的应用将获取刷新令牌以及 ID 和访问令牌，而这些又可用于续订它们。 刷新令牌的使用已抽象化，开发人员不应围绕它们构建逻辑。 MSAL 则是自行使用刷新令牌来管理令牌续订。 以前在 ADAL.js 中使用的令牌缓存无法转移到 MSAL.js，因为令牌缓存架构已更改，与 ADAL.js 中使用的架构不兼容。

## <a name="handle-errors-and-exceptions"></a>处理错误和异常

使用 MSAL.js 时，最常出现的错误类型是 `interaction_in_progress` 错误。 如果在调用一个交互式 API（`loginPopup`、`loginRedirect`、`acquireTokenPopup`、`acquireTokenRedirect`）时另一个交互式 API 仍在运行，则会引发此错误。 `login*` 和 `acquireToken*` API 是异步的，因此你需要确保生成的承诺已得到解决，然后才能调用另一个承诺。

另一个常见错误是 `interaction_required`。 通常只需启动交互式令牌获取提示，就能解决此错误。 例如，你尝试访问的 Web API 可能实施了[条件访问](../conditional-access/overview.md)策略，要求用户执行[多重身份验证](../authentication/concept-mfa-howitworks.md) (MFA)。 在这种情况下，通过触发 `acquireTokenPopup` 或 `acquireTokenRedirect` 处理 `interaction_required` 错误时会提示用户执行 MFA，使用户能够履行它。

不过，可能出现的另一个常见错误是 `consent_required`。当获取受保护资源访问令牌所需的权限未经用户同意时，就会出现此错误。 与在 `interaction_required` 中一样，`consent_required` 错误的解决方法通常是使用 `acquireTokenPopup` 或 `acquireTokenRedirect` 启动交互式令牌获取提示。

有关详细信息，请参阅：[常见的 MSAL.js 错误及其处理方法](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/errors.md)

## <a name="use-the-events-api"></a>使用事件 API

MSAL.js (>=v2.4) 引入了可在应用中使用的事件 API。 这些事件与身份验证过程以及 MSAL 在任意时刻执行的操作相关，可用于更新 UI、显示错误消息、检查是否正在进行任何交互，等等。 例如，下面是当登录过程出于任何原因而失败时要调用的事件回调：

```javascript
const callbackId = msalInstance.addEventCallback((message) => {
  // Update UI or interact with EventMessage here
  if (message.eventType === EventType.LOGIN_FAILURE) {
      if (message.error instanceof AuthError) {
          // Do something with the error
      }
    }
});
```

为了保持性能，不再需要事件回调时，应将其取消注册，这一点非常重要。 有关详细信息，请参阅：[MSAL.js 事件 API](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/events.md)

## <a name="handle-multiple-accounts"></a>处理多个帐户

ADAL.js 使用“用户”的概念来表示当前已经过身份验证的实体。 MSAL.js 将“用户”的概念替换成了“帐户”，原因是一个用户可能有多个关联的帐户 。 这也意味着，你现在需要控制多个帐户，并选择使用适当的帐户。 以下代码片段演示了此过程：

```javascript
let homeAccountId = null; // Initialize global accountId (can also be localAccountId or username) used for account lookup later, ideally stored in app state

// This callback is passed into `acquireTokenPopup` and `acquireTokenRedirect` to handle the interactive auth response
function handleResponse(resp) {
  if (resp !== null) {
      homeAccountId = resp.account.homeAccountId; // alternatively: resp.account.homeAccountId or resp.account.username
  } else {
      const currentAccounts = myMSALObj.getAllAccounts();
      if (currentAccounts.length < 1) { // No cached accounts
          return;
      } else if (currentAccounts.length > 1) { // Multiple account scenario
          // Add account selection logic here
      } else if (currentAccounts.length === 1) {
          homeAccountId = currentAccounts[0].homeAccountId; // Single account scenario
      }
  }
}
```

有关详细信息，请参阅：[MSAL.js 中的帐户](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/accounts.md)

## <a name="use-the-wrappers-libraries"></a>使用包装器库

如果你正在进行 Angular 和 React 框架相关的开发，可以分别使用 [MSAL Angular v2](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angular) 和 [MSAL React](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)。 这些包装器公开与 MSAL.js 相同的公共 API，同时还提供特定于框架的方法和组件，以简化身份验证和令牌获取过程。

## <a name="run-the-app"></a>运行应用

完成更改后，运行应用并测试身份验证方案：

```console
npm start
```

## <a name="example-securing-web-apps-with-adal-node-vs-msal-node"></a>示例：通过 ADAL Node 和 MSAL Node 保护 Web 应用

以下代码片段演示了单页应用程序通过 Microsoft 标识平台对用户进行身份验证并依次使用 ADAL.js 和 MSAL.js 获取 Microsoft Graph 访问令牌所需的最少量代码：

<table>
<tr><td> 使用 ADAL.js </td><td> 使用 MSAL.js </td></tr>
<tr>
<td>

```html

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">

  <script 
    type="text/javascript"
    src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.18/js/adal.min.js">
  </script>
</head>

<div>
  <button id="loginButton">Login</button>
  <button id="logoutButton" style="visibility: hidden;">Logout</button>
  <button id="tokenButton" style="visibility: hidden;">Get Token</button>
</div>

<body>
  <script>

    const loginButton = document.getElementById("loginButton");
    const logoutButton = document.getElementById("logoutButton");
    const tokenButton = document.getElementById("tokenButton");

    var authContext = new AuthenticationContext({
        instance: 'https://login.microsoftonline.com/',
        clientId: "ENTER_CLIENT_ID",
        tenant: "ENTER_TENANT_ID",
        cacheLocation: "sessionStorage",
        redirectUri: "http://localhost:3000",
        popUp: true,
        callback: function (errorDesc, token, error, tokenType) {
            console.log('Hello ' + authContext.getCachedUser().profile.upn)

            loginButton.style.visibility = "hidden";
            logoutButton.style.visibility = "visible";
            tokenButton.style.visibility = "visible";
        }
    });

    authContext.log({
        level: 3,
        log: function (message) {
            console.log(message);
        },
        piiLoggingEnabled: false
    });

    loginButton.addEventListener('click', function () {
        authContext.login();
    });

    logoutButton.addEventListener('click', function () {
        authContext.logOut();
    });

    tokenButton.addEventListener('click', () => {
        authContext.acquireTokenPopup(
            "https://graph.microsoft.com", 
            null, null, 
            function (error, token) {
                console.log(error, token);
            }
        )
    });
  </script>
</body>

</html>

```

</td>
<td>

```html

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">

  <script 
    type="text/javascript" 
    src="https://alcdn.msauth.net/browser/2.14.2/js/msal-browser.min.js">
  </script>
</head>

<div>
  <button id="loginButton">Login</button>
  <button id="logoutButton" style="visibility: hidden;">Logout</button>
  <button id="tokenButton" style="visibility: hidden;">Get Token</button>
</div>

<body>
  <script>
    const loginButton = document.getElementById("loginButton");
    const logoutButton = document.getElementById("logoutButton");
    const tokenButton = document.getElementById("tokenButton");

    const pca = new msal.PublicClientApplication({
        auth: {
            clientId: "ENTER_CLIENT_ID",
            authority: "https://login.microsoftonline.com/ENTER_TENANT_ID",
            redirectUri: "http://localhost:3000",
        },
        cache: {
            cacheLocation: "sessionStorage"
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
    });

    loginButton.addEventListener('click', () => {
        pca.loginPopup().then((response) => {
            console.log(`Hello ${response.account.username}!`);

            loginButton.style.visibility = "hidden";
            logoutButton.style.visibility = "visible";
            tokenButton.style.visibility = "visible";
        })
    });

    logoutButton.addEventListener('click', () => {
        pca.logoutPopup().then((response) => {
            window.location.reload();
        })
    });

    tokenButton.addEventListener('click', () => {
        pca.acquireTokenPopup({
            scopes: ["User.Read"]
        }).then((response) => {
            console.log(response);
        })
    });
  </script>
</body>

</html>

```

</td>
</tr>
</table>

## <a name="next-steps"></a>后续步骤

- [MSAL.js API 参考](https://azuread.github.io/microsoft-authentication-library-for-js/ref/)
- [MSAL.js 代码示例](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples)
