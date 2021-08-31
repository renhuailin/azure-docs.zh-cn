---
title: 使用 Azure Active Directory B2C 构建基块在 SPA 应用程序中启用身份验证
description: 本文介绍 Azure Active Directory B2C 的构建基块，用于在 SPA 应用程序中注册和登录用户。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/25/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: e3d46a2843f84fa13c0d80288ccf43e1e474e2b5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121723220"
---
# <a name="enable-authentication-in-your-own-single-page-application-by-using-azure-ad-b2c"></a>使用 Azure AD B2C 在自己的单页应用程序中启用身份验证

本文介绍如何将 Azure Active Directory B2C (Azure AD B2C) 身份验证添加到自己的单页应用程序 (SPA)。 了解如何使用适用于 JavaScript 的 [Microsoft 身份验证库 (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js) 创建 SPA 应用程序。 

请将本文与[在示例 SPA 应用程序中配置身份验证](./configure-authentication-sample-spa-app.md)结合使用，并将示例 SPA 应用替换为你自己的 SPA 应用。

## <a name="overview"></a>概述

本文使用 Node.js 和 [Express](https://expressjs.com/) 创建基本的 Node.js Web 应用。 Express 是一个精简且灵活的 Node.js Web 应用框架，用于为 Web 应用程序和移动应用程序提供一系列功能。

[MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js) 身份验证库是 Microsoft 提供的库，可简化向 SPA 应用添加身份验证和授权支持的过程。

> [!TIP]
> 整个 MSAL.js 代码在客户端上运行。 可将 Node.js 和 Express 服务器端代码替换为其他解决方案，例如 .NET Core、Java 和 Hypertext Preprocessor (PHP) 脚本语言。

## <a name="prerequisites"></a>先决条件

要查看先决条件和集成说明，请参阅[在示例 SPA 应用程序中配置身份验证](configure-authentication-sample-spa-app.md)。

## <a name="step-1-create-an-spa-app-project"></a>步骤 1：创建 SPA 应用项目

可以使用现有 SPA 应用项目或创建新项目。 要创建新项目，请执行以下操作：

1. 打开命令行界面，并创建新的目录（如 myApp）。 此目录将包含你的应用代码、用户界面和配置文件。

1. 进入创建的目录。

1. 使用 `npm init` 命令为你的应用创建 `package.json` 文件。 此命令将提示有关应用的信息（例如，应用的名称和版本，以及初始入口点的名称，即 index.js 文件）。 运行以下命令，并接受默认值：

```
npm init
```

## <a name="step-2-install-the-dependencies"></a>步骤 2：安装依赖项

要安装 Express 包，请在命令行界面中运行以下命令：

```
npm install express
```

为了查找应用的静态文件，服务器端代码将使用 [Path](https://www.npmjs.com/package/path) 包。 

要安装 Path 包，请在命令行界面中运行以下命令：

```
npm install path
```

## <a name="step-3-configure-your-web-server"></a>步骤 3：配置 Web 服务器

在 myApp 文件夹中，创建名为 index.js 的文件，其中包含以下代码：

```javascript
// Initialize express
const express = require('express');
const app = express();

// The port to listen to incoming HTTP requests
const port = 6420;

// Initialize path
const path = require('path');

// Set the front-end folder to serve public assets.
app.use(express.static('App'));

// Set up a route for the index.html
app.get('*', (req, res) => {
    res.sendFile(path.join(__dirname + '/index.html'));
});

// Start the server, and listen for HTTP requests
app.listen(port, () => {
  console.log(`Listening on http://localhost:${port}`);
});
```

## <a name="step-4-create-the-spa-user-interface"></a>步骤 4：创建 SPA 用户界面

添加 SAP 应用 `index.html` 文件。 此文件实现通过 Bootstrap 框架生成的用户界面，并导入用于配置、身份验证和 Web API 调用的脚本文件。

index.html 文件引用的资源详见下表： 

|参考 |定义|
|---|---|
|MSAL.js 库| MSAL.js 身份验证 JavaScript 库 [CDN 路径](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/cdn-usage.md)。|
|[Bootstrap 样式表](https://getbootstrap.com/) | 一个免费的前端框架，用于更快且更轻松地进行 Web 开发。 该框架包括基于 HTML 和基于 CSS 的设计模板。 |
|[policies.js](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/blob/main/App/policies.js) | 包含 Azure AD B2C 自定义策略和用户流。 |
|[authConfig.js](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/blob/main/App/authConfig.js) | 包含身份验证配置参数。|
|[authRedirect.js](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/blob/main/App/authRedirect.js) | 包含身份验证逻辑。 |
|[apiConfig.js](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/blob/main/App/apiConfig.js) | 包含 Web API 范围和 API 终结点位置。 |
|[api.js](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/blob/main/App/api.js) | 定义用于调用 API 并处理其响应的方法。|
|[ui.js](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/blob/main/App/ui.js) | 控制 UI 元素。 |
| | |

为了呈现 SPA 索引文件，在 myApp 文件夹中创建名为 index.html 的文件，其中包含以下 HTML 片段。

```html
<!DOCTYPE html>
<html>
    <head>
        <title>My Azure AD B2C test app</title>
    </head>
    <body>
        <h2>My Azure AD B2C test app</h2>
        <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous" />
        <button type="button" id="signIn" class="btn btn-secondary" onclick="signIn()">Sign-in</button>
        <button type="button" id="signOut" class="btn btn-success d-none" onclick="signOut()">Sign-out</button>
        <h5 id="welcome-div" class="card-header text-center d-none"></h5>
        <br />
        <!-- Content -->
        <div class="card">
            <div class="card-body text-center">
                <pre id="response" class="card-text"></pre>
                <button type="button" id="callApiButton" class="btn btn-primary d-none" onclick="passTokenToApi()">Call API</button>
            </div>
        </div>
        <script src="https://alcdn.msauth.net/browser/2.14.2/js/msal-browser.min.js" integrity="sha384-ggh+EF1aSqm+Y4yvv2n17KpurNcZTeYtUZUvhPziElsstmIEubyEB6AIVpKLuZgr" crossorigin="anonymous"></script>

        <!-- Importing app scripts (load order is important) -->
        <script type="text/javascript" src="./apiConfig.js"></script>
        <script type="text/javascript" src="./policies.js"></script>
        <script type="text/javascript" src="./authConfig.js"></script>
        <script type="text/javascript" src="./ui.js"></script>

        <!-- <script type="text/javascript" src="./authRedirect.js"></script>   -->
        <!-- uncomment the above line and comment the line below if you would like to use the redirect flow -->
        <script type="text/javascript" src="./authRedirect.js"></script>
        <script type="text/javascript" src="./api.js"></script>
    </body>
</html>
 ```

## <a name="step-5-configure-the-authentication-library"></a>步骤 5：配置身份验证库

配置 MSAL.js 库与 Azure AD B2C 集成的方式。 MSAL.js 库使用常见配置对象连接到 Azure AD B2C 租户身份验证终结点。

要配置身份验证库，请执行以下操作：

1. 在 myApp 文件夹中 ，创建名为 App 的新文件夹 。 
1. 在 App 文件夹中，创建名为 authConfig.js 的新文件 。
1. 将以下 JavaScript 代码添加到 authConfig.js 文件中：

    ```javascript
    const msalConfig = {
        auth: {
        clientId: "<Application-ID>", 
        authority: b2cPolicies.authorities.signUpSignIn.authority, 
        knownAuthorities: [b2cPolicies.authorityDomain], 
        redirectUri: "http://localhost:6420",
        },
        cache: {
        cacheLocation: "localStorage", .
        storeAuthStateInCookie: false, 
        }
    };

    const loginRequest = {
    scopes: ["openid", ...apiConfig.b2cScopes],
    };

    const tokenRequest = {
    scopes: [...apiConfig.b2cScopes],
    forceRefresh: false
    };
    ```

1. 将 `<Application-ID>` 替换为你的应用注册应用程序 ID。 有关详细信息，请参阅[在示例 SPA 应用程序中配置身份验证](./configure-authentication-sample-spa-app.md#step-23-register-the-spa)。

> [!TIP]
> 有关更多的 MSAL 对象配置选项，请参阅[身份验证选项](./enable-authentication-spa-app-options.md)文章。

## <a name="step-6-specify-your-azure-ad-b2c-user-flows"></a>步骤 6：指定 Azure AD B2C 用户流

创建 policies.js 文件，该文件提供了关于 Azure AD B2C 环境的信息。 MSAL.js 库使用此信息来创建对 Azure AD B2C 的身份验证请求。

要指定 Azure AD B2C 用户流，请执行以下操作：

1. 在 App 文件夹中，创建名为 policies.js 的新文件 。
1. 将以下代码添加到 policies.js 文件中：

    ```javascript
    const b2cPolicies = {
        names: {
            signUpSignIn: "B2C_1_SUSI",
            editProfile: "B2C_1_EditProfile"
        },
        authorities: {
            signUpSignIn: {
                authority: "https://contoso.b2clogin.com/contoso.onmicrosoft.com/Your-B2C-SignInOrSignUp-Policy-Id",
            },
            editProfile: {
                authority: "https://contoso.b2clogin.com/contoso.onmicrosoft.com/Your-B2C-EditProfile-Policy-Id"
            }
        },
        authorityDomain: "contoso.b2clogin.com"
    }
    ```

1. 将 `B2C_1_SUSI` 替换为你的“登录”Azure AD B2C 策略名称。
1. 将 `B2C_1_EditProfile` 替换为你的“编辑配置文件”Azure AD B2C 策略名称。
1. 将 `contoso` 的所有实例替换为你的 [Azure AD B2C 租户名称](./tenant-management.md#get-your-tenant-name)。

## <a name="step-7-use-the-msal-to-sign-in-the-user"></a>步骤 7：使用 MSAL 登录用户

在此步骤中实现初始化登录流、获取 API 访问令牌和注销的方法。 

有关详细信息，请参阅文章 [MSAL PublicClientApplication 类参考](https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal_browser.publicclientapplication.html)和[使用 Microsoft 身份验证库 (MSAL) 将用户登录](../active-directory/develop/tutorial-v2-javascript-spa.md#use-the-microsoft-authentication-library-msal-to-sign-in-the-user)。

要登录用户，请执行以下操作：

1. 在 App 文件夹中，创建名为 authRedirect.js 的新文件 。
1. 在 authRedirect.js 中，复制并粘贴以下代码：

    ```javascript
    // Create the main myMSALObj instance
    // configuration parameters are located at authConfig.js
    const myMSALObj = new msal.PublicClientApplication(msalConfig);

    let accountId = "";
    let idTokenObject = "";
    let accessToken = null;

    myMSALObj.handleRedirectPromise()
        .then(response => {
            if (response) {
                /**
                 * For the purpose of setting an active account for UI update, we want to consider only the auth response resulting
                 * from SUSI flow. "tfp" claim in the id token tells us the policy (NOTE: legacy policies may use "acr" instead of "tfp").
                 * To learn more about B2C tokens, visit https://docs.microsoft.com/en-us/azure/active-directory-b2c/tokens-overview
                 */
                if (response.idTokenClaims['tfp'].toUpperCase() === b2cPolicies.names.signUpSignIn.toUpperCase()) {
                    handleResponse(response);
                }
            }
        })
        .catch(error => {
            console.log(error);
        });


    function setAccount(account) {
        accountId = account.homeAccountId;
        idTokenObject = account.idTokenClaims;
        myClaims= JSON.stringify(idTokenObject);
        welcomeUser(myClaims);
    }

    function selectAccount() {

        /**
         * See here for more information on account retrieval: 
         * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
         */

        const currentAccounts = myMSALObj.getAllAccounts();

        if (currentAccounts.length < 1) {
            return;
        } else if (currentAccounts.length > 1) {
        
            /**
             * Due to the way MSAL caches account objects, the auth response from initiating a user-flow
             * is cached as a new account, which results in more than one account in the cache. Here we make
             * sure we are selecting the account with homeAccountId that contains the sign-up/sign-in user-flow, 
             * as this is the default flow the user initially signed-in with.
             */
            const accounts = currentAccounts.filter(account =>
                account.homeAccountId.toUpperCase().includes(b2cPolicies.names.signUpSignIn.toUpperCase())
                &&
                account.idTokenClaims.iss.toUpperCase().includes(b2cPolicies.authorityDomain.toUpperCase())
                &&
                account.idTokenClaims.aud === msalConfig.auth.clientId 
                );

            if (accounts.length > 1) {
                // localAccountId identifies the entity for which the token asserts information.
                if (accounts.every(account => account.localAccountId === accounts[0].localAccountId)) {
                    // All accounts belong to the same user
                    setAccount(accounts[0]);
                } else {
                    // Multiple users detected. Logout all to be safe.
                    signOut();
                };
            } else if (accounts.length === 1) {
                setAccount(accounts[0]);
            }

        } else if (currentAccounts.length === 1) {
            setAccount(currentAccounts[0]);
        }
    }

    // in case of page refresh
    selectAccount();

    async function handleResponse(response) {

        if (response !== null) {
            setAccount(response.account);
        } else {
            selectAccount();
        }
    }

    function signIn() {
        myMSALObj.loginRedirect(loginRequest);
    }

    function signOut() {
        const logoutRequest = {
            postLogoutRedirectUri: msalConfig.auth.redirectUri,
        };

        myMSALObj.logoutRedirect(logoutRequest);
    }

    function getTokenRedirect(request) {
        request.account = myMSALObj.getAccountByHomeId(accountId); 

        return myMSALObj.acquireTokenSilent(request)
            .then((response) => {
                // In case the response from B2C server has an empty accessToken field
                // throw an error to initiate token acquisition
                if (!response.accessToken || response.accessToken === "") {
                    throw new msal.InteractionRequiredAuthError;
                } else {
                    console.log("access_token acquired at: " + new Date().toString());
                    accessToken = response.accessToken;
                    passTokenToApi();
                }
            }).catch(error => {
                console.log("Silent token acquisition fails. Acquiring token using popup. \n", error);
                if (error instanceof msal.InteractionRequiredAuthError) {
                    // fallback to interaction when silent call fails
                    return myMSALObj.acquireTokenRedirect(request);
                } else {
                    console.log(error);   
                }
        });
    }
    
    // Acquires and access token and then passes it to the API call
    function passTokenToApi() {
        if (!accessToken) {
            getTokenRedirect(tokenRequest);
        } else {
            try {
                callApi(apiConfig.webApi, accessToken);
            } catch(error) {
                console.log(error); 
            }
        }
    }

    function editProfile() {


        const editProfileRequest = b2cPolicies.authorities.editProfile;
        editProfileRequest.loginHint = myMSALObj.getAccountByHomeId(accountId).username;

        myMSALObj.loginRedirect(editProfileRequest);
    }
    ```

## <a name="step-8-configure-the-web-api-location-and-scope"></a>步骤 8：配置 Web API 位置和作用域

为了使 SPA 应用能够调用 Web API，请提供 Web API 终结点位置和用于授权访问 Web API 的[作用域](./configure-authentication-sample-spa-app.md#app-registration-overview)。

要配置 Web API 位置和作用域，请执行以下操作：

1. 在 App 文件夹中，创建名为 apiConfig.js 的新文件 。
1. 在 apiConfig.js 中，复制并粘贴以下代码：

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
        b2cScopes: ["https://contoso.onmicrosoft.com/tasks/tasks.read"],
        webApi: "https://mydomain.azurewebsites.net/tasks"
    };
    ```

1. 将 `contoso` 替换为你的租户名称。 可以按照[配置范围](./configure-authentication-sample-spa-app.md#step-22-configure-scopes)一文中所述找到所需的范围名称。
1. 将 `webApi` 的值替换为你的 Web API 终结点位置。

## <a name="step-9-call-your-web-api"></a>步骤 9：调用 Web API

定义对 API 终结点的 HTTP 请求。 HTTP 请求配置为将使用 MSAL.js 获取的访问令牌传入请求中的 `Authorization` HTTP 标头。

以下代码定义了对 API 终结点的 HTTP `GET` 请求，并将访问令牌传入 `Authorization` HTTP 标头。 API 位置由 apiConfig.js 中的 `webApi` 键定义。 

要使用获取的令牌调用 Web API，请执行以下操作：

1. 在 App 文件夹中，创建名为 api.js 的新文件 。
1. 将以下代码添加到 api.js 文件中：

    ```javascript
    function callApi(endpoint, token) {
        
        const headers = new Headers();
        const bearer = `Bearer ${token}`;
    
        headers.append("Authorization", bearer);
    
        const options = {
            method: "GET",
            headers: headers
        };
    
        logMessage('Calling web API...');
        
        fetch(endpoint, options)
        .then(response => response.json())
        .then(response => {

            if (response) {
            logMessage('Web API responded: ' + response.name);
            }
            
            return response;
        }).catch(error => {
            console.error(error);
        });
    }
    ```

## <a name="step-10-add-the-ui-elements-reference"></a>步骤 10：添加 UI 元素引用

SPA 应用使用 JavaScript 来控制 UI 元素。 例如，它显示登录和注销按钮，并在屏幕上呈现用户 ID 令牌声明。

要添加 UI 元素引用，请执行以下操作：

1. 在 App 文件夹中，创建名为 ui.js 的文件。
1. 将以下代码添加到 ui.js 文件中：

    ```javascript
    // Select DOM elements to work with
    const signInButton = document.getElementById('signIn');
    const signOutButton = document.getElementById('signOut')
    const titleDiv = document.getElementById('title-div');
    const welcomeDiv = document.getElementById('welcome-div');
    const tableDiv = document.getElementById('table-div');
    const tableBody = document.getElementById('table-body-div');
    const editProfileButton = document.getElementById('editProfileButton');
    const callApiButton = document.getElementById('callApiButton');
    const response = document.getElementById("response");
    const label = document.getElementById('label');

    function welcomeUser(claims) {
        welcomeDiv.innerHTML = `Token claims: </br></br> ${claims}!`

        signInButton.classList.add('d-none');
        signOutButton.classList.remove('d-none');
        welcomeDiv.classList.remove('d-none');
        callApiButton.classList.remove('d-none');
    }

    function logMessage(s) {
        response.appendChild(document.createTextNode('\n' + s + '\n'));
    }
    ```
  
## <a name="step-11-run-your-spa-application"></a>步骤 11：运行 SPA 应用程序

在命令 shell 中运行以下命令：

``` powershell
npm install  
npm ./index.js
```

1. 转到 https://localhost:6420。 
1. 选择“登录”。
1. 完成注册或登录过程。

成功通过身份验证后，屏幕上会显示经过分析的 ID 令牌。 选择 `Call API` 调用 API 终结点。

## <a name="next-steps"></a>后续步骤

* 了解有关[代码示例](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa)的详细信息。
* [使用 Azure AD B2C 在自己的 SPA 应用程序中配置身份验证选项](enable-authentication-spa-app-options.md)。
* [在自己的 Web API 中启用身份验证](enable-authentication-web-api.md)。
