---
title: 使用 Azure Active Directory B2C 在示例单页应用程序中配置身份验证
description: 本文会讨论如何在单页应用程序中使用 Azure Active Directory B2C 执行用户登录和注册。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/15/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 1ea2ae3644ae22fd06a5bae650fa3f5beb955aba
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128572504"
---
# <a name="configure-authentication-in-a-sample-single-page-application-by-using-azure-ad-b2c"></a>使用 Azure AD B2C 在示例单页应用程序中配置身份验证

本文使用一个示例 JavaScript 单页应用程序 (SPA)，说明如何向 SPA 添加 Azure Active Directory B2C (Azure AD B2C) 身份验证。

## <a name="overview"></a>概述

OpenID Connect (OIDC) 是在 OAuth 2.0 上构建的身份验证协议。 你可以借此安全地将用户登录到应用程序。 此单页应用程序示例使用了 [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser) 和 OIDC PKCE 流。 MSAL.js 是 Microsoft 提供的库，可简化向 SPA 添加身份验证和授权支持的过程。

### <a name="sign-in-flow"></a>登录流

登录流涉及以下步骤：

1. 用户前往 Web 应用，并选择“登录”。
1. 应用发起身份验证请求，并将用户重定向到 Azure AD B2C。
1. 用户[注册或登录并](add-sign-up-and-sign-in-policy.md)[重置密码](add-password-reset-policy.md)。 或者，用户也可以使用[社交帐户](add-identity-provider.md)登录。
1. 用户成功登录后，Azure AD B2C 向应用返回一个授权代码。
1. 单页应用程序验证 ID 令牌，读取声明，进而允许用户调用受保护的资源和 API。

### <a name="app-registration-overview"></a>应用注册概述

若要让应用能够使用 Azure AD B2C 登录并调用 Web API，你需要在 Azure AD B2C 目录中注册两个应用程序。  

- Web 应用注册能让你的应用使用 Azure AD B2C 登录。 在注册过程中，你需要指定重定向 URI。 重定向 URI 是用户在使用 Azure AD B2C 完成身份验证后，Azure AD B2C 将用户重定向到的终结点。 此应用注册过程会生成应用程序 ID（也称为“客户端 ID”），将其作为该应用的唯一标识。 

- Web API 注册可使应用调用安全的 Web API。 注册内容包括 Web API 的作用域。 作用域提供了一种方法来管理受保护资源（例如你的 Web API）的访问权限。 你可以向 Web API 的作用域授予 Web 应用访问权限。 在请求访问令牌时，你的应用需要在请求的作用域参数中指定所需的访问权限。  

下图演示了应用体系结构和注册：

![关系图：使用 Web API 调用注册和令牌的 Web 应用。](./media/configure-authentication-sample-spa-app/spa-app-with-api-architecture.png) 

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

在此步骤中，你将创建 SPA 和 Web API 应用程序注册，并指定 Web API 的作用域。

### <a name="step-21-register-the-web-api-application"></a>步骤 2.1：注册 Web API 应用程序

[!INCLUDE [active-directory-b2c-app-integration-register-api](../../includes/active-directory-b2c-app-integration-register-api.md)]

### <a name="step-22-configure-scopes"></a>步骤 2.2：配置作用域

[!INCLUDE [active-directory-b2c-app-integration-api-scopes](../../includes/active-directory-b2c-app-integration-api-scopes.md)]

### <a name="step-23-register-the-spa"></a>步骤 2.3：注册 SPA

要创建 SPA 注册，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 搜索并选择“Azure AD B2C”。
1. 选择“应用注册”，然后选择“新建注册” 。
1. 输入应用程序的名称（例如 MyApp）。
1. 在“支持的帐户类型”下，选择“任何标识提供者或组织目录中的帐户(用于通过用户流对用户进行身份验证)” 。 
1. 在“重定向 URI”下，选择“单页应用程序 (SPA)”，然后在 URL 框中输入 `http://localhost:6420`。 
1. 在“权限”下，选中“授予对 OpenID 和脱机访问权限的管理员许可”复选框。 
1. 选择“注册”  。

### <a name="step-24-enable-the-implicit-grant-flow"></a>步骤 2.4：启用隐式授权流

接下来，启用隐式授权流：

1. 在“管理”下，选择“身份验证”。 

1. 选择“尝试新体验”（如果已显示）。

1. 在“隐式授权”下，选中“ID 令牌”复选框。 

1. 选择“保存”。

   记录“应用程序（客户端）ID”，以便在稍后配置 Web 应用程序时使用。

    ![屏幕截图：用于记录 Web 应用程序 ID 的 Web 应用概述页。](./media/configure-authentication-sample-web-app/get-azure-ad-b2c-app-id.png)  

### <a name="step-25-grant-permissions"></a>步骤 2.5：授予权限

[!INCLUDE [active-directory-b2c-app-integration-grant-permissions](../../includes/active-directory-b2c-app-integration-grant-permissions.md)]

## <a name="step-3-get-the-spa-sample-code"></a>步骤 3：获取 SPA 示例代码

此示例演示单页应用程序如何使用 Azure AD B2C 来实现用户注册和登录。 然后，该应用将获取访问令牌并调用受保护的 Web API。 

若要获取 SPA 示例代码，可以执行以下操作之一： 

* [下载 ZIP 文件](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/archive/main.zip)。 
* 通过运行以下命令，从 GitHub 克隆该示例代码：

    ```bash
    git clone https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa.git
    ```

### <a name="step-31-update-the-spa-sample"></a>步骤 3.1：更新 SPA 示例

获取 SPA 示例后，请使用 Azure AD B2C 和 Web API 值更新代码。 在示例文件夹中，从 `App` 文件夹下，打开下表所列的 JavaScript 文件，然后使用相应的值更新这些文件。  


|文件  |密钥  |值  |
|---------|---------|---------|
|authConfig.js|clientId| [步骤 2.3](#step-23-register-the-spa) 中的 SPA ID。|
|policies.js| 姓名| 在[步骤 1](#step-1-configure-your-user-flow) 中创建的用户流或自定义策略。|
|policies.js|authorities|你的 Azure AD B2C [租户名称](tenant-management.md#get-your-tenant-name)（例如 `contoso.onmicrosoft.com`）。 然后，将其替换为在[步骤 1](#step-1-configure-your-user-flow) 中创建的用户流或自定义策略（例如 `https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>`）。|
|policies.js|authorityDomain|你的 Azure AD B2C [租户名称](tenant-management.md#get-your-tenant-name)（例如 `contoso.onmicrosoft.com`）。|
|apiConfig.js|b2cScopes|你在[步骤 2.2](#step-22-configure-scopes) 中创建的 Web API 作用域（例如 `b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/tasks-api/tasks.read"]`）。|
|apiConfig.js|webApi|Web API 的 URL，即 `http://localhost:5000/tasks`。|
| | | |

最终的代码应类似于以下示例：

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

注册 Web API 并定义其作用域后，请配置 Web API 代码，使其适用于你的 Azure AD B2C 租户。 

若要获取 Web API 示例代码，请执行下列操作之一：

* [下载\* .zip 存档](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip)。

* 通过运行以下命令，从 GitHub 克隆该示例 Web API 项目：

    ```bash
    git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
    ```

* 你还可以直接前往 GitHub 上的 [Azure-Samples/active-directory-b2c-javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) 项目。

### <a name="step-41-update-the-web-api"></a>步骤 4.1：更新 Web API

1. 在代码编辑器中打开 config.json 文件。
1. 使用此前创建的应用程序注册修改变量值。 然后使用作为先决条件的一部分（例如 b2c_1_susi）而创建的用户流更新 `policyName`。
    
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

### <a name="step-42-enable-cors"></a>步骤 4.2：启用 CORS

若要允许单页应用程序调用 Node.js Web API，需要在该 Web API 中启用[跨域资源共享 (CORS)](https://expressjs.com/en/resources/middleware/cors.html)。 在生产应用程序中，密切关注哪个域正在发出请求。 在此示例中，允许来自任何域的请求。

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

1. 若要查看此应用程序，请在浏览器中转到 `http://localhost:6420`。

    ![屏幕截图：显示在浏览器窗口中的 SPA 示例应用程序。](./media/configure-authentication-sample-spa-app/sample-app-sign-in.png)

1. 完成注册或登录过程。 成功登录后，应会看到“用户 \<your username> 已登录”的消息。
1. 选择“调用 API”  按钮。 SPA 会将请求中的访问令牌发送到受保护的 Web API，该 Web API 将返回已登录用户的显示名称：

    ![屏幕截图：浏览器窗口中的 SPA，显示 API 返回的用户名 JSON 结果。](./media/configure-authentication-sample-spa-app/sample-app-result.png)

## <a name="deploy-your-application"></a>部署应用程序 

在生产应用程序中，应用注册重定向 URI 通常是运行应用的可公开访问的终结点，比如 `https://contoso.com/signin-oidc`。 

可以随时在注册的应用程序中添加和修改重定向 URI。 重定向 URI 存在以下限制：

* 回复 URL 必须以方案 `https` 开头。
* 回复 URL 区分大小写。 其大小写必须与正在运行的应用程序的 URL 路径的大小写匹配。 

## <a name="next-steps"></a>后续步骤

有关本文所讨论的概念的详细信息，请参阅：
* [详细了解相关代码示例](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa)。
* [在自己的 SPA 中启用身份验证](enable-authentication-spa-app.md)。
* [配置 SPA 中的身份验证选项](enable-authentication-spa-app-options.md)。
* [在自己的 Web API 中启用身份验证](enable-authentication-web-api.md)。
