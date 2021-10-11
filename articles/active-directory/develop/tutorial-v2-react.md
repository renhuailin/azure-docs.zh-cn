---
title: 教程：创建使用身份验证代码流的 React 单页应用 | Azure
titleSuffix: Microsoft identity platform
description: 在本教程中，你将创建一个 React SPA，它可以使用户登录，并使用身份验证代码流从 Microsoft 标识平台获取访问令牌，并调用 Microsoft Graph API。
services: active-directory
author: j-mantu
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 04/16/2021
ms.author: jamesmantu
ms.custom: aaddev, devx-track-js
ms.openlocfilehash: 3be99d0d9b22a4a52c4e6003d073324b40db3120
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129231779"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-a-react-single-page-app-spa-using-auth-code-flow"></a>教程：使用授权代码流让用户登录并从 React 单页应用 (SPA) 调用 Microsoft Graph API

在本教程中，你将生成一个 React 单页应用程序 (SPA)，其使用 PKCE 的授权代码流来登录用户并调用 Microsoft Graph。 生成的 SPA 使用适用于 React 的 Microsoft 身份验证库 (MSAL)。

在本教程中：
> [!div class="checklist"]
> * 使用 `npm` 创建 React 项目
> * 在 Azure 门户中注册应用程序
> * 添加代码以支持用户登录和注销
> * 添加代码以调用 Microsoft Graph API
> * 测试应用

MSAL React 支持浏览器中的授权代码流，而不是隐式授权流。 MSAL React 不支持隐式流。

## <a name="prerequisites"></a>先决条件

* 用于运行本地 Web 服务器的 [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) 或其他代码编辑器

## <a name="how-the-tutorial-app-works"></a>教程应用的工作方式

:::image type="content" source="media/tutorial-v2-javascript-auth-code/diagram-01-auth-code-flow.png" alt-text="展示单页应用程序中的授权代码流的示意图":::

在本教程中创建的应用程序使 React SPA 可以通过从 Microsoft 标识平台获取安全令牌来查询 Microsoft Graph API。 它使用适用于 React 的 Microsoft 身份验证库 (MSAL) - MSAL.js v2 库的包装器。 MSAL React 使 React 16+ 应用程序能够使用 Azure Active Directory (Azure AD) 对企业用户和具有 Microsoft 帐户和社交标识（如 Facebook、Google 和 LinkedIn）的用户进行身份验证。 使用此库，应用程序还可以获取对 Microsoft 云服务和 Microsoft Graph 的访问权限。

此方案中，用户登录后请求了访问令牌，并在授权标头将其添加到 HTTP 请求。 令牌获取和更新由适用于 React 的 Microsoft 身份验证库 (MSAL React) 处理。

### <a name="libraries"></a>库

本教程使用以下库：

|库|说明|
|---|---|
|[MSAL React](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)|适用于 JavaScript React 的 Microsoft 身份验证库包装器|
|[MSAL 浏览器](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)|“适用于 JavaScript v2 的 Microsoft 身份验证库”浏览器包|

## <a name="get-the-completed-code-sample"></a>获取完整代码示例

想要改为下载本教程的完整示例项目？ 若要使用本地 Web 服务器（如 Node.js）运行项目，请克隆 [ms-identity-javascript-react-spa](https://github.com/Azure-Samples/ms-identity-javascript-react-spa) 存储库：

`git clone https://github.com/Azure-Samples/ms-identity-javascript-react-spa`

然后，若要在执行代码示例之前对其进行配置，请跳到[配置步骤](#register-your-application)。

若要继续学习本教程并自行生成应用程序，请前进到下一节[先决条件](#prerequisites)。

## <a name="create-your-project"></a>创建项目

安装 [Node.js](https://nodejs.org/en/download/) 后，打开终端窗口，然后运行以下命令：

```console
npx create-react-app msal-react-tutorial # Create a new React app
cd msal-react-tutorial # Change to the app directory
npm install @azure/msal-browser @azure/msal-react # Install the MSAL packages
npm install react-bootstrap bootstrap # Install Bootstrap for styling
```

现在，你已使用[创建 React 应用](https://create-react-app.dev/docs/getting-started)启动了一个小型 React 项目。 这将是本教程其余部分所依据的起始点。 若要在学习本教程时查看对应用所做的更改，可以运行以下命令： 

```console
npm start
```

应会自动打开到应用的浏览器窗口。 如果没有，请打开浏览器并导航到 http://localhost:3000 。 每次使用更新的代码保存文件时，页面都会重新加载以反映更改。

## <a name="register-your-application"></a>注册应用程序

按照[单页应用程序：应用注册](./scenario-spa-app-registration.md)中的步骤使用 Azure 门户为 SPA 创建应用注册。

在[重定向 URI：具有授权代码流的 MSAL.js 2.0](scenario-spa-app-registration.md#redirect-uri-msaljs-20-with-auth-code-flow) 步骤中，输入 `http://localhost:3000`（create-react-app 将为应用程序提供服务的默认位置）。

### <a name="configure-your-javascript-spa"></a>配置 JavaScript SPA

1. 在“src”文件夹中创建名为“authConfig.js”的文件来包含用于身份验证的配置参数，然后添加以下代码 ：

    ```javascript
    export const msalConfig = {
      auth: {
        clientId: "Enter_the_Application_Id_Here",
        authority: "Enter_the_Cloud_Instance_Id_Here/Enter_the_Tenant_Info_Here",
        redirectUri: "Enter_the_Redirect_Uri_Here",
      },
      cache: {
        cacheLocation: "sessionStorage", // This configures where your cache will be stored
        storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
      }
    };
    
    // Add scopes here for ID token to be used at Microsoft identity platform endpoints.
    export const loginRequest = {
     scopes: ["User.Read"]
    };
    
    // Add the endpoints here for Microsoft Graph API services you'd like to use.
    export const graphConfig = {
        graphMeEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me"
    };
    ```

1. 修改 `msalConfig` 部分中的值，如下所述：
    
    |值名称| 关于|
    |----------|------|
    |`Enter_the_Application_Id_Here`| 已注册应用程序的应用程序（客户端）ID。|
    |`Enter_the_Cloud_Instance_Id_Here`| 在其中注册应用程序的 Azure 云实例。 对于主要（或全球）Azure 云，请输入 `https://login.microsoftonline.com`。 对于国家/地区云（例如中国云），可以在[国家/地区云](authentication-national-cloud.md)中找到相应值。
    |`Enter_the_Tenant_Info_Here`| 设置为以下选项之一：如果应用程序支持此组织目录中的帐户，请将此值替换为目录（租户）ID 或租户名称（例如 contoso.microsoft.com）。 如果应用程序支持“任何组织目录中的帐户”，请将此值替换为 **organizations**。 如果应用程序支持“任何组织目录中的帐户和个人 Microsoft 帐户”，请将此值替换为 **common**。 若要限制对“仅限个人 Microsoft 帐户”的支持，请将此值替换为 **consumers**。 |
    |`Enter_the_Redirect_Uri_Here`|替换为 **http://localhost:3000** 。|
    |`Enter_the_Graph_Endpoint_Here`| 应用程序应与之通信的 Microsoft Graph API 实例。 对于全球 Microsoft Graph API 终结点，请将此字符串的两个实例都替换为 `https://graph.microsoft.com`。 对于国家/地区云部署中的终结点，请参阅 Microsoft Graph 文档中的[国家/地区云部署](/graph/deployments)。|
    
    有关可用的可配置选项的详细信息，请阅读[初始化客户端应用程序](msal-js-initializing-client-applications.md)。

1. 打开“src/index.js”文件并添加以下导入语句：

    ```javascript
    import "bootstrap/dist/css/bootstrap.min.css";
    import { PublicClientApplication } from "@azure/msal-browser";
    import { MsalProvider } from "@azure/msal-react";
    import { msalConfig } from "./authConfig";
    ```

1. 在“src/index.js”中的导入语句下，使用步骤 1 中的配置创建 `PublicClientApplication` 实例。

    ```javascript
    const msalInstance = new PublicClientApplication(msalConfig);
    ``` 

1. 在“src/index.js”中查找 `<App />` 组件，将其包装在 `MsalProvider` 组件中。 呈现函数类似于以下所示：

    ```jsx
    ReactDOM.render(
        <React.StrictMode>
            <MsalProvider instance={msalInstance}>
                <App />
            </MsalProvider>
        </React.StrictMode>,
        document.getElementById("root")
    );
    ``` 


## <a name="sign-in-users"></a>用户登录

在“src”中创建名为“components”的文件夹，并在此文件夹中创建名为“SignInButton.jsx”的文件  。 添加以下任一节中的代码，以使用弹出窗口或全帧重定向来调用登录：

### <a name="sign-in-using-popups"></a>使用弹出窗口登录

将以下代码添加到“src/components/SignInButton.jsx”以创建按钮组件，该组件将在被选中时调用弹出登录：

```jsx
import React from "react";
import { useMsal } from "@azure/msal-react";
import { loginRequest } from "../authConfig";
import Button from "react-bootstrap/Button";

function handleLogin(instance) {
    instance.loginPopup(loginRequest).catch(e => {
        console.error(e);
    });
}

/**
 * Renders a button which, when selected, will open a popup for login
 */
export const SignInButton = () => {
    const { instance } = useMsal();

    return (
        <Button variant="secondary" className="ml-auto" onClick={() => handleLogin(instance)}>Sign in using Popup</Button>
    );
}
```

### <a name="sign-in-using-redirects"></a>使用重定向登录

将以下代码添加到“src/components/SignInButton.jsx”以创建按钮组件，该组件将在选中时调用重定向登录：

```jsx
import React from "react";
import { useMsal } from "@azure/msal-react";
import { loginRequest } from "../authConfig";
import Button from "react-bootstrap/Button";

function handleLogin(instance) {
    instance.loginRedirect(loginRequest).catch(e => {
        console.error(e);
    });
}

/**
 * Renders a button which, when selected, will redirect the page to the login prompt
 */
export const SignInButton = () => {
    const { instance } = useMsal();

    return (
        <Button variant="secondary" className="ml-auto" onClick={() => handleLogin(instance)}>Sign in using Redirect</Button>
    );
}
```

### <a name="add-the-sign-in-button"></a>添加登录按钮

1. 在名为“PageLayout.jsx”的“components”文件夹中创建另一个文件 ，并添加以下代码以创建包含刚创建的登录按钮的导航栏组件：

    ```jsx
    import React from "react";
    import Navbar from "react-bootstrap/Navbar";
    import { useIsAuthenticated } from "@azure/msal-react";
    import { SignInButton } from "./SignInButton";
    
    /**
     * Renders the navbar component with a sign-in button if a user is not authenticated
     */
    export const PageLayout = (props) => {
        const isAuthenticated = useIsAuthenticated();
    
        return (
            <>
                <Navbar bg="primary" variant="dark">
                    <a className="navbar-brand" href="/">MSAL React Tutorial</a>
                    { isAuthenticated ? <span>Signed In</span> : <SignInButton /> }
                </Navbar>
                <h5><center>Welcome to the Microsoft Authentication Library For React Tutorial</center></h5>
                <br />
                <br />
                {props.children}
            </>
        );
    };
    ```

2. 现在打开“src/App.js”并添加，将现有内容替换为以下代码： 

    ```jsx
    import React from "react";
    import { PageLayout } from "./components/PageLayout";
    
    function App() {
      return (
          <PageLayout>
              <p>This is the main app content!</p>
          </PageLayout>
      );
    }
    
    export default App;
    ```

应用现在有了一个登录按钮，该按钮只向未经身份验证的用户显示！

当用户首次选择“使用弹出窗口登录”或“使用重定向登录”按钮时，`onClick` 处理程序会调用 `loginPopup`（或 `loginRedirect`）来登录用户 。 `loginPopup` 方法会打开一个包含 Microsoft 标识平台终结点的弹出窗口，以提示输入并验证用户的凭据。 成功登录后，msal.js 将启动[授权代码流](v2-oauth2-auth-code-flow.md)。

此时，会将受 PKCE 保护的授权代码发送到受 CORS 保护的令牌终结点，并使用该代码来交换令牌。 应用程序将会收到 ID 令牌、访问令牌和刷新令牌，msal.js 将处理这些令牌，令牌中包含的信息将会被缓存。

## <a name="sign-users-out"></a>注销用户

在“src/components”中创建一个名为“SignOutButton.jsx”的文件 。 添加以下任一节中的代码，以使用弹出窗口或全帧重定向来调用注销：

### <a name="sign-out-using-popups"></a>使用弹出窗口注销

将以下代码添加到“src/components/SignOutButton.jsx”以创建按钮组件，该组件将在被选中时调用弹出注销：

```jsx
import React from "react";
import { useMsal } from "@azure/msal-react";
import Button from "react-bootstrap/Button";

function handleLogout(instance) {
    instance.logoutPopup().catch(e => {
        console.error(e);
    });
}

/**
 * Renders a button which, when selected, will open a popup for logout
 */
export const SignOutButton = () => {
    const { instance } = useMsal();

    return (
        <Button variant="secondary" className="ml-auto" onClick={() => handleLogout(instance)}>Sign out using Popup</Button>
    );
}
```

### <a name="sign-out-using-redirects"></a>使用重定向注销

将以下代码添加到“src/components/SignOutButton.jsx”以创建按钮组件，该组件将在被选中时调用重定向注销：

```jsx
import React from "react";
import { useMsal } from "@azure/msal-react";
import Button from "react-bootstrap/Button";

function handleLogout(instance) {
    instance.logoutRedirect().catch(e => {
        console.error(e);
    });
}

/**
 * Renders a button which, when selected, will redirect the page to the logout prompt
 */
export const SignOutButton = () => {
    const { instance } = useMsal();

    return (
        <Button variant="secondary" className="ml-auto" onClick={() => handleLogout(instance)}>Sign out using Redirect</Button>
    );
}
```

### <a name="add-the-sign-out-button"></a>添加注销按钮

更新“src/components/PageLayout.jsx”中的 `PageLayout` 组件，以便为经过身份验证的用户呈现新 `SignOutButton` 组件。 代码应如下所示：

```jsx
import React from "react";
import Navbar from "react-bootstrap/Navbar";
import { useIsAuthenticated } from "@azure/msal-react";
import { SignInButton } from "./SignInButton";
import { SignOutButton } from "./SignOutButton";

/**
 * Renders the navbar component with a sign-in button if a user is not authenticated
 */
export const PageLayout = (props) => {
    const isAuthenticated = useIsAuthenticated();

    return (
        <>
            <Navbar bg="primary" variant="dark">
                <a className="navbar-brand" href="/">MSAL React Tutorial</a>
                { isAuthenticated ? <SignOutButton /> : <SignInButton /> }
            </Navbar>
            <h5><center>Welcome to the Microsoft Authentication Library For React Tutorial</center></h5>
            <br />
            <br />
            {props.children}
        </>
    );
};
```

## <a name="conditionally-render-components"></a>有条件地呈现组件

为了仅为经过身份验证或未经身份验证的用户呈现某些组件，请使用 `AuthenticateTemplate` 和/或 `UnauthenticatedTemplate`，如下所示。

1. 将以下导入语句添加到“src/App.js”：

    ```javascript
    import { AuthenticatedTemplate, UnauthenticatedTemplate } from "@azure/msal-react";
    ```
    
1. 为了仅为经过身份验证的用户呈现某些组件，请用以下代码更新“src/App.js”中的 `App` 函数： 

    ```jsx
    function App() {
        return (
            <PageLayout>
                <AuthenticatedTemplate>
                    <p>You are signed in!</p>
                </AuthenticatedTemplate>
            </PageLayout>
        );
    }
    ```

1. 若要仅为未经过身份验证的用户呈现某些组件，如建议登录，请用以下代码更新“src/App.js”中的 `App` 函数： 

    ```jsx
    function App() {
        return (
            <PageLayout>
                <AuthenticatedTemplate>
                    <p>You are signed in!</p>
                </AuthenticatedTemplate>
                <UnauthenticatedTemplate>
                    <p>You are not signed in! Please sign in.</p>
                </UnauthenticatedTemplate>
            </PageLayout>
        );
    }
    ```

## <a name="acquire-a-token"></a>获取令牌

1. 在调用 API（如 Microsoft Graph）之前，需要获取访问令牌。 使用以下代码将新组件添加到名为 `ProfileContent` 的“src/App.js”：

    ```jsx
    function ProfileContent() {
        const { instance, accounts, inProgress } = useMsal();
        const [accessToken, setAccessToken] = useState(null);
    
        const name = accounts[0] && accounts[0].name;
    
        function RequestAccessToken() {
            const request = {
                ...loginRequest,
                account: accounts[0]
            };
    
            // Silently acquires an access token which is then attached to a request for Microsoft Graph data
            instance.acquireTokenSilent(request).then((response) => {
                setAccessToken(response.accessToken);
            }).catch((e) => {
                instance.acquireTokenPopup(request).then((response) => {
                    setAccessToken(response.accessToken);
                });
            });
        }
    
        return (
            <>
                <h5 className="card-title">Welcome {name}</h5>
                {accessToken ? 
                    <p>Access Token Acquired!</p>
                    :
                    <Button variant="secondary" onClick={RequestAccessToken}>Request Access Token</Button>
                }
            </>
        );
    };
    ```

1. 更新“src/App.js”中的导入语句，以匹配以下内容：

    ```js
    import React, { useState } from "react";
    import { PageLayout } from "./components/PageLayout";
    import { AuthenticatedTemplate, UnauthenticatedTemplate, useMsal } from "@azure/msal-react";
    import { loginRequest } from "./authConfig";
    import Button from "react-bootstrap/Button";
    ```

1. 最后，在“src/App.js”的 `App` 组件中将新 `ProfileContent` 组件添加为 `AuthenticatedTemplate` 的子组件。 `App` 组件应该如下所示：

    ```javascript
    function App() {
      return (
          <PageLayout>
              <AuthenticatedTemplate>
                  <ProfileContent />
              </AuthenticatedTemplate>
              <UnauthenticatedTemplate>
                  <p>You are not signed in! Please sign in.</p>
              </UnauthenticatedTemplate>
          </PageLayout>
      );
    }
    ```

上面的代码将为已登录的用户呈现一个按钮，以便在选择该按钮时，用户可以为 Microsoft Graph 请求访问令牌。

在用户登录后，应用不应要求用户在每次需要访问受保护资源时重新进行身份验证（即请求令牌）。 若要防止此类重新验证请求，请调用 `acquireTokenSilent`，该调用会首先查找缓存的未过期访问令牌，并在需要时使用刷新令牌获取新的访问令牌。 但在某些情况下，可能需要强制用户与 Microsoft 标识平台交互。 例如：

- 由于会话已过期，用户需要重新输入其凭据。
- 刷新令牌已过期。
- 应用程序正在请求访问资源，这需要用户的许可。
- 需要双重身份验证。

调用 `acquireTokenPopup` 会打开一个弹出窗口（也可以调用 `acquireTokenRedirect` 将用户重定向到 Microsoft 标识平台）。 在该窗口中，为了进行交互，用户需要确认其凭据、为所需的资源提供许可，或者完成双重身份验证。

> [!NOTE]
> 如果使用 Internet Explorer，我们建议使用 `loginRedirect` 和 `acquireTokenRedirect` 方法，因为 Internet Explorer 和弹出窗口存在一个[已知问题](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/internet-explorer.md#popups)。

## <a name="call-the-microsoft-graph-api"></a>调用 Microsoft Graph API

1. 在“src”文件夹中创建名为“graph.js”的文件，并添加以下用于对 Microsoft Graph API 进行 REST 调用的代码 ：

    ```javascript
    import { graphConfig } from "./authConfig";
    
    /**
     * Attaches a given access token to a Microsoft Graph API call. Returns information about the user
     */
    export async function callMsGraph(accessToken) {
        const headers = new Headers();
        const bearer = `Bearer ${accessToken}`;
    
        headers.append("Authorization", bearer);
    
        const options = {
            method: "GET",
            headers: headers
        };
    
        return fetch(graphConfig.graphMeEndpoint, options)
            .then(response => response.json())
            .catch(error => console.log(error));
    }
    ```

1. 接下来，在“src/components”中创建一个名为“ProfileData.jsx”的文件，并添加以下代码 ：

    ```javascript
    import React from "react";
    
    /**
     * Renders information about the user obtained from Microsoft Graph
     */
    export const ProfileData = (props) => {
        return (
            <div id="profile-div">
                <p><strong>First Name: </strong> {props.graphData.givenName}</p>
                <p><strong>Last Name: </strong> {props.graphData.surname}</p>
                <p><strong>Email: </strong> {props.graphData.userPrincipalName}</p>
                <p><strong>Id: </strong> {props.graphData.id}</p>
            </div>
        );
    };
    ```

1. 接下来，打开“src/App.js”并将以下语句添加到导入语句：
    
    ```javascript
    import { ProfileData } from "./components/ProfileData";
    import { callMsGraph } from "./graph";
    ```

1. 最后，在“src/App.js”中更新 `ProfileContent` 组件以调用 Microsoft Graph，并在获取令牌后显示配置文件数据。 `ProfileContent` 组件应类似于以下所示：

    ```javascript
    function ProfileContent() {
        const { instance, accounts } = useMsal();
        const [graphData, setGraphData] = useState(null);
    
        const name = accounts[0] && accounts[0].name;
    
        function RequestProfileData() {
            const request = {
                ...loginRequest,
                account: accounts[0]
            };
    
            // Silently acquires an access token which is then attached to a request for Microsoft Graph data
            instance.acquireTokenSilent(request).then((response) => {
                callMsGraph(response.accessToken).then(response => setGraphData(response));
            }).catch((e) => {
                instance.acquireTokenPopup(request).then((response) => {
                    callMsGraph(response.accessToken).then(response => setGraphData(response));
                });
            });
        }
    
        return (
            <>
                <h5 className="card-title">Welcome {name}</h5>
                {graphData ? 
                    <ProfileData graphData={graphData} />
                    :
                    <Button variant="secondary" onClick={RequestProfileData}>Request Profile Information</Button>
                }
            </>
        );
    };
    ```

在上面所做的更改中，使用 `callMSGraph()` 方法对需要令牌的受保护资源发出 HTTP `GET` 请求。 然后，该请求将内容返回给调用方。 此方法可在 HTTP 授权标头中添加获取的令牌。 在本教程创建的示例应用程序中，受保护资源是 Microsoft Graph API me 终结点，它显示已登录用户个人资料信息。

## <a name="test-your-application"></a>测试应用程序

你已完成应用程序创建，现在可以启动 Web 服务器并测试应用的功能。

1. 通过从项目文件夹的根目录中运行以下命令，为应用提供服务：

   ```console
   npm start
   ```
1. 应会自动打开到应用的浏览器窗口。 如果没有，请打开浏览器并导航到 `http://localhost:3000`。 应会看到如下所示的页面。

    :::image type="content" source="media/tutorial-v2-react/react-01-unauthenticated.png" alt-text="显示登录对话框的 Web 浏览器":::

1. 选择登录按钮进行登录。

### <a name="provide-consent-for-application-access"></a>许可应用程序访问

首次登录到应用程序时，系统会提示你授予其访问你的个人资料的权限，并将你登录：

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-02-consent-dialog.png" alt-text="显示在 Web 浏览器中的内容对话框":::

如果你同意请求的权限，Web 应用程序会显示名称，表示登录成功：

:::image type="content" source="media/tutorial-v2-react/react-02-authenticated.png" alt-text="Web 浏览器中的登录成功结果":::

### <a name="call-the-graph-api"></a>调用图形 API

登录之后，选择“查看个人资料”，以查看在调用 Microsoft Graph API 的响应中返回的用户个人资料信息：

:::image type="content" source="media/tutorial-v2-react/react-03-graph-data.png" alt-text="浏览器中显示的 Microsoft Graph 中的个人资料信息":::

### <a name="more-information-about-scopes-and-delegated-permissions"></a>有关作用域和委派权限的详细信息

Microsoft Graph API 需要 *user.read* 作用域来读取用户的个人资料。 默认情况下，此作用域会自动添加到在 Azure 门户中注册的每个应用程序。 Microsoft Graph 的其他 API 以及后端服务器的自定义 API 可能需要其他作用域。 例如，Microsoft Graph API 需要使用 Mail.Read 作用域来列出用户的电子邮件。

添加作用域时，可能会提示用户为添加的作用域提供额外许可。

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>后续步骤

如果你想要更深入了解 Microsoft 标识平台上的 JavaScript 单页应用程序开发，请参阅由多部分组成的方案系列：

> [!div class="nextstepaction"]
> [方案：单页应用程序](scenario-spa-overview.md)
