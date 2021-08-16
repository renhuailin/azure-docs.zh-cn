---
title: 使用 Azure Active Directory B2C 在示例 SPA 应用程序中配置身份验证
description: 使用 Azure Active Directory B2C 在 SPA 应用程序中登录并注册用户。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/11/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: addf3870c22105a2ff42202e768d1e8cda4ffbde
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2021
ms.locfileid: "112072962"
---
# <a name="configure-authentication-in-a-sample-single-page-application-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 在示例单页应用程序中配置身份验证

本文使用一个示例 JavaScript 单页应用程序，说明如何向 SPA 应用添加 Azure Active Directory B2C (Azure AD B2C) 身份验证。

## <a name="overview"></a>概述

OpenID Connect (OIDC) 是基于 OAuth 2.0 构建的身份验证协议，用于确保用户安全地登录到应用程序。 此单页应用程序示例使用了 [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser) 和 OIDC PKCE 流。 MSAL.js 是 Microsoft 提供的库，可简化向 SPA 应用添加身份验证和授权支持的过程。

### <a name="sign-in-flow"></a>登录流
登录流涉及以下步骤：

1. 用户导航到 Web 应用并选择“登录”。 
1. 该应用发起身份验证请求，并将用户重定向到 Azure AD B2C。
1. 用户[注册或登录](add-sign-up-and-sign-in-policy.md)、[重置密码](add-password-reset-policy.md)或使用[社交帐户](add-identity-provider.md)登录。
1. 成功登录后，Azure AD B2C 向应用返回 ID 令牌。
1. 单页应用程序验证 ID 令牌，读取声明，进而允许用户调用受保护的资源/API。

### <a name="app-registration-overview"></a>应用注册概述

若要让应用能够使用 Azure AD B2C 登录并调用 Web API，你需要在 Azure AD B2C 目录中注册两个应用程序。  

- Web 应用注册能让你的应用使用 Azure AD B2C 登录。 在应用注册过程中，请指定重定向 URI。 重定向 URI 是用户使用 Azure AD B2C 进行身份验证后重定向到的终结点。 应用注册过程将生成一个应用程序 ID，也称为客户端 ID，作为该应用的唯一标识。

- Web API 注册可使应用调用安全的 Web API。 注册内容包括 Web API 的作用域。 作用域提供了一种方法来管理受保护资源（例如你的 Web API）的访问权限。 你可以向 Web API 的作用域授予 Web 应用访问权限。 在请求访问令牌时，你的应用需要在请求的作用域参数中指定所需的访问权限。  

以下关系图描述了应用注册和应用程序体系结构。

![使用 Web API 调用注册和令牌的 Web 应用](./media/configure-authentication-sample-spa-app/spa-app-with-api-architecture.png) 

### <a name="call-to-a-web-api"></a>调用 Web API

[!INCLUDE [active-directory-b2c-app-integration-call-api](../../includes/active-directory-b2c-app-integration-call-api.md)]

### <a name="sign-out-flow"></a>注销流

[!INCLUDE [active-directory-b2c-app-integration-sign-out-flow](../../includes/active-directory-b2c-app-integration-sign-out-flow.md)]

## <a name="prerequisites"></a>先决条件

运行以下内容的计算机：

* [Visual Studio Code](https://code.visualstudio.com/) 或其他代码编辑器。
* [Node.js 运行时](https://nodejs.org/en/download/)

## <a name="step-1-configure-your-user-flow"></a>步骤 1：配置用户流

[!INCLUDE [active-directory-b2c-app-integration-add-user-flow](../../includes/active-directory-b2c-app-integration-add-user-flow.md)]

## <a name="step-2-register-your-spa-and-api"></a>步骤 2：注册 SPA 和 API

在此步骤中，你将创建 SPA 应用和 Web API 应用程序注册，并指定 Web API 的作用域。

### <a name="21-register-the-web-api-application"></a>2.1 注册 Web API 应用程序

[!INCLUDE [active-directory-b2c-app-integration-register-api](../../includes/active-directory-b2c-app-integration-register-api.md)]

### <a name="22-configure-scopes"></a>2.2 配置作用域

[!INCLUDE [active-directory-b2c-app-integration-api-scopes](../../includes/active-directory-b2c-app-integration-api-scopes.md)]

### <a name="23-register-the-client-app"></a>2.3 注册客户端应用

按照以下步骤创建应用注册：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在门户工具栏中选择“目录 + 订阅”图标，然后选择包含 Azure AD B2C 租户的目录。
1. 在 Azure 门户中，搜索并选择“Azure AD B2C”。
1. 选择“应用注册”，然后选择“新建注册” 。
1. 输入应用程序的“名称”。 例如，MyApp。
1. 在“支持的帐户类型”下，选择“任何标识提供者或组织目录中的帐户(用于通过用户流对用户进行身份验证)” 。 
1. 在“重定向 URI”下，选择“单页应用程序(SPA)”，然后在 URL 文本框中输入 `http://localhost:6420` 。
1. 在“权限”下，选中“向 OpenID 和脱机访问权限授予管理员同意”复选框。
1. 选择“注册”。

接下来，启用隐式授权流：

1. 在“管理”下，选择“身份验证”。 
1. 选择“尝试新体验”（如果已显示）。
1. 在隐式授权下，选中“ID 令牌”复选框。
1. 选择“保存”。

记录“应用程序（客户端）ID”，以便在稍后配置 Web 应用程序时使用。
    ![获取应用程序 ID](./media/configure-authentication-sample-web-app/get-azure-ad-b2c-app-id.png)  

### <a name="25-grant-permissions"></a>2.5 授予权限

[!INCLUDE [active-directory-b2c-app-integration-grant-permissions](../../includes/active-directory-b2c-app-integration-grant-permissions.md)]

## <a name="step-3-get-the-spa-sample-code"></a>步骤 3：获取 SPA 示例代码

该示例演示了单页应用程序如何使用 Azure AD B2C 执行用户注册和登录，以及如何调用受保护的 Web API。 下载以下示例代码：

  从 GitHub [下载 zip 文件](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/archive/main.zip)或克隆该示例：

  ```
  git clone https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa.git
  ```

### <a name="31-update-the-spa-sample"></a>3.1 更新 SPA 示例

获得示例后，请使用 Azure AD B2C 租户名称和步骤 2.3 中记录的 myApp 应用程序 ID 来更新代码。

打开 App 文件夹中的 authConfig.js 文件 。
1. 在 `msalConfig` 对象中，查找 `clientId` 的赋值，并将其替换为步骤 2.3 中记录的应用程序（客户端） ID。

打开 `policies.js` 文件。
1. 在 `names` 下找到条目，并将其分配替换为在上一步中创建的用户流的名称，例如 `b2c_1_susi`。
1. 在 `authorities` 下找到条目，并将其相应替换为在上一步中创建的用户流的名称，例如 `https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>`。
1. 找到 `authorityDomain` 的分配，并将其替换为 `<your-tenant-name>.b2clogin.com`。

打开 `apiConfig.js` 文件。
1. 找到 `b2cScopes` 的分配，并将 URL 替换为你为 Web API 创建的范围 URL，例如 `b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/tasks-api/tasks.read"]`。
1. 查找 `webApi` 的赋值，并将当前 URL 替换为 `http://localhost:5000/tasks`。


生成的代码应类似于以下示例：

authConfig.js：

```javascript
const msalConfig = {
  auth: {
    clientId: "<your-MyApp-application-ID>"
    authority: b2cPolicies.authorities.signUpSignIn.authority,
    knownAuthorities: [b2cPolicies.authorityDomain],
  },
  cache: {
    cacheLocation: "localStorage",
    storeAuthStateInCookie: true
  }
};

const loginRequest = {
  scopes: ["openid", "profile"],
};

const tokenRequest = {
  scopes: apiConfig.b2cScopes
};
```

policies.js：

```javascript
const b2cPolicies = {
    names: {
        signUpSignIn: "b2c_1_susi",
        forgotPassword: "b2c_1_reset",
        editProfile: "b2c_1_edit_profile"
    },
    authorities: {
        signUpSignIn: {
            authority: "https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/b2c_1_susi",
        },
        forgotPassword: {
            authority: "https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/b2c_1_reset",
        },
        editProfile: {
            authority: "https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/b2c_1_edit_profile"
        }
    },
    authorityDomain: "your-tenant-name.b2clogin.com"
}
```

apiConfig.js：

```javascript
const apiConfig = {
  b2cScopes: ["https://your-tenant-name.onmicrosoft.com/tasks-api/tasks.read"],
  webApi: "http://localhost:5000/tasks"
};
```

## <a name="step-4-get-the-web-api-sample-code"></a>步骤 4：获取 Web API 示例代码

注册 Web API 并定义其作用域后，请配置 Web API 代码，使其适用于你的 Azure AD B2C 租户。 下载以下示例代码：

[下载 \*.zip 存档](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip)，或者从 GitHub 克隆示例 Web API 项目。 还可以直接浏览到 GitHub 上的 [Azure-Samples/active-directory-b2c-javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) 项目。

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="41-update-the-web-api"></a>4.1 更新 Web API

1. 在代码编辑器中打开 config.json 文件。
1. 使用此前创建的应用程序注册修改变量值。 另外，使用作为先决条件的一部分创建的用户流更新 `policyName`。 例如，b2c_1_susi。
    
    ```json
    "credentials": {
        "tenantName": "<your-tenant-name>",
        "clientID": "<your-webapi-application-ID>"
    },
    "policies": {
        "policyName": "b2c_1_susi"
    },
    "resource": {
        "scope": ["tasks.read"] 
    },
    ```

### <a name="42-enable-cors"></a>4.2 启用 CORS

若要允许单页应用程序调用 Node.js Web API，需要在该 Web API 中启用 [CORS](https://expressjs.com/en/resources/middleware/cors.html)。 在生产应用程序中，应密切关注哪个域正在发出请求。 在此示例中，允许来自任何域的请求。

若要启用 CORS，请使用以下中间件。 在已下载的 Node.js Web API 代码示例中，此中间件已添加到 index.js 文件中。

```javascript
app.use((req, res, next) => {
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "Authorization, Origin, X-Requested-With, Content-Type, Accept");
    next();
});
```

## <a name="step-5-run-the-spa-and-web-api"></a>步骤 5：运行 SPA 和 Web API

现已准备好测试单页应用程序在限定作用域内对 API 的访问。 在本地计算机上同时运行 Node.js Web API 和示例 JavaScript 单页应用程序。 然后登录到该单页应用程序，并选择“调用 API”按钮向受保护的 API 发起请求。 

### <a name="run-the-nodejs-web-api"></a>运行 Node.js Web API

1. 打开控制台窗口，切换到包含 Node.js Web API 示例的目录。 例如：

    ```console
    cd active-directory-b2c-javascript-nodejs-webapi
    ```

1. 运行以下命令：

    ```console
    npm install && npm update
    node index.js
    ```

    控制台窗口将显示托管该应用程序的端口号。

    ```console
    Listening on port 5000...
    ```

### <a name="run-the-single-page-app"></a>运行单页应用

1. 打开另一个控制台窗口，并切换到包含 JavaScript SPA 示例的目录。 例如：

    ```console
    cd ms-identity-b2c-javascript-spa
    ```

1. 运行以下命令：

    ```console
    npm install && npm update
    npm start
    ```

    控制台窗口将显示该应用程序所在的端口号。

    ```console
    Listening on port 6420...
    ```

1. 在浏览器中导航到 `http://localhost:6420`，查看此应用程序。

    ![浏览器中显示的单页应用程序示例应用](./media/configure-authentication-sample-spa-app/sample-app-sign-in.png)

1. 使用在[上一篇教程](tutorial-single-page-app.md)中所用的电子邮件地址和密码登录。 成功登录后，应会看到 `User 'Your Username' logged-in` 消息。
1. 选择“调用 API”  按钮。 SPA 会将请求中的访问令牌发送到受保护的 Web API，该 Web API 将返回已登录用户的显示名称：

    ![浏览器中的单页应用程序，其中显示了 API 返回的用户名 JSON 结果](./media/configure-authentication-sample-spa-app/sample-app-result.png)

## <a name="deploy-your-application"></a>部署应用程序 

在生产应用程序中，应用注册重定向 URI 通常是运行应用的可公开访问的终结点，比如 `https://contoso.com/signin-oidc`。 

可以随时在注册的应用程序中添加和修改重定向 URI。 重定向 URI 存在以下限制：

* 回复 URL 必须以方案 `https` 开头。
* 回复 URL 区分大小写。 其大小写必须与正在运行的应用程序的 URL 路径的大小写匹配。 

## <a name="next-steps"></a>后续步骤

* 了解[有关代码示例](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa)的详细信息
* 了解如何[使用 Azure AD B2C 在自己的 SPA 应用程序中启用身份验证选项](enable-authentication-spa-app-options.md)
