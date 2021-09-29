---
title: Tutorial:在 Node.js 控制台应用中调用 Microsoft Graph | Azure
titleSuffix: Microsoft identity platform
description: 在本教程中，你将构建一个控制台应用，用于将 Microsoft Graph 调用到 Node.js 控制台应用。
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: v-doeris
ms.openlocfilehash: 94e5675a4710e1d90ca053727c32219bb98d80c1
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124786325"
---
# <a name="tutorial-call-the-microsoft-graph-api-in-a-nodejs-console-app"></a>Tutorial:在 Node.js 控制台应用中调用 Microsoft Graph API

在本教程中，你将构建一个控制台应用，该应用使用其自己的身份调用 Microsoft Graph API。 构建的控制台应用使用[适用于 Node.js 的 Microsoft 身份验证库 (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node)。

请按照本教程中的步骤进行操作：

> [!div class="checklist"]
> - 在 Azure 门户中注册应用程序
> - 创建 Node.js 控制台应用项目
> - 向应用添加身份验证逻辑
> - 添加应用注册详细信息
> - 添加方法来调用 web API
> - 测试应用程序

## <a name="prerequisites"></a>先决条件

- [Node.js](https://nodejs.org/en/download/)
- [Visual Studio Code](https://code.visualstudio.com/download) 或其他代码编辑器

## <a name="register-the-application"></a>注册应用程序

首先，请完成[向 Microsoft 标识平台注册应用程序](quickstart-register-app.md)中的步骤来注册你的应用。

对于应用注册，请使用以下设置：

- 名称：`NodeConsoleApp`（建议）
- 支持的帐户类型：**仅此组织目录中的帐户**
- API 权限：**Microsoft API** > **Microsoft Graph** > **应用程序权限** > `User.Read.All`
- 客户端机密：`*********`（记录此值以便在后面的步骤中使用 - 它只显示一次）

## <a name="create-the-project"></a>创建项目

创建文件夹来承载你的应用程序，例如 NodeConsoleApp。

1. 首先，在终端中更改到你的项目目录，然后运行以下NPM 命令：

```console
    npm init -y
    npm install --save dotenv yargs axios @azure/msal-node
```

2. 接下来，创建一个名为 bin 的文件夹。 然后，在此文件夹中创建名为 index.js 的文件，并添加以下代码：

```JavaScript
#!/usr/bin/env node

// read in env settings
require('dotenv').config();

const yargs = require('yargs');

const fetch = require('./fetch');
const auth = require('./auth');

const options = yargs
    .usage('Usage: --op <operation_name>')
    .option('op', { alias: 'operation', describe: 'operation name', type: 'string', demandOption: true })
    .argv;

async function main() {
    console.log(`You have selected: ${options.op}`);

    switch (yargs.argv['op']) {
        case 'getUsers':

            try {
                // here we get an access token
                const authResponse = await auth.getToken(auth.tokenRequest);

                // call the web API with the access token
                const users = await fetch.callApi(auth.apiConfig.uri, authResponse.accessToken);

                // display result
                console.log(users);
            } catch (error) {
                console.log(error);
            }

            break;
        default:
            console.log('Select a Graph operation first');
            break;
    }
};

main();
```

此文件引用两个其他节点模块：auth.js 和 fetch.js，前者包含用于获取访问令牌的 MSAL 节点的实现，后者包含用于发出 HTTP 请求以使用访问令牌 Microsoft Graph API 的方法 。 完成本教程的其余部分之后，项目的文件和文件夹结构应类似于下面这样：

```
NodeConsoleApp/
├── bin
│   ├── auth.js
│   ├── fetch.js
│   ├── index.js
├── package.json
└── .env
```

## <a name="add-authentication-logic"></a>添加身份验证逻辑

在 bin 文件夹中，创建另一个名为 auth.js 的文件，并添加以下代码，以便获取调用 Microsoft Graph API 时要提供的访问令牌 。

```JavaScript
const msal = require('@azure/msal-node');

/**
 * Configuration object to be passed to MSAL instance on creation.
 * For a full list of MSAL Node configuration parameters, visit:
 * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/configuration.md
 */
const msalConfig = {
    auth: {
        clientId: process.env.CLIENT_ID,
        authority: process.env.AAD_ENDPOINT + process.env.TENANT_ID,
        clientSecret: process.env.CLIENT_SECRET,
    }
};

/**
 * With client credentials flows permissions need to be granted in the portal by a tenant administrator.
 * The scope is always in the format '<resource>/.default'. For more, visit:
 * https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow
 */
const tokenRequest = {
    scopes: [process.env.GRAPH_ENDPOINT + '/.default'],
};

const apiConfig = {
    uri: process.env.GRAPH_ENDPOINT + '/v1.0/users',
};

/**
 * Initialize a confidential client application. For more info, visit:
 * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md
 */
const cca = new msal.ConfidentialClientApplication(msalConfig);

/**
 * Acquires token with client credentials.
 * @param {object} tokenRequest
 */
async function getToken(tokenRequest) {
    return await cca.acquireTokenByClientCredential(tokenRequest);
}

module.exports = {
    apiConfig: apiConfig,
    tokenRequest: tokenRequest,
    getToken: getToken
};
```

在上述代码片段中，我们首先创建一个 (msalConfig) 的配置对象，并将其传递给 MSAL [ConfidentialClientApplication](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md)。 接下来，我们创建一个方法，用于通过客户端凭据获取令牌，并最终公开此模块以便通过 main.js 进行访问。 此模块中的配置参数是从环境文件中提取的，我们将在下一步中创建该文件。

## <a name="add-app-registration-details"></a>添加应用注册详细信息

创建一个环境文件，以存储在获取令牌时将使用的应用注册详细信息。 为此，请在示例 (NodeConsoleApp) 的根文件夹中创建一个名为 .env 的文件，并添加以下代码 ：

```
# Credentials
TENANT_ID=Enter_the_Tenant_Id_Here
CLIENT_ID=Enter_the_Application_Id_Here
CLIENT_SECRET=Enter_the_Client_Secret_Here

# Endpoints
AAD_ENDPOINT=Enter_the_Cloud_Instance_Id_Here
GRAPH_ENDPOINT=Enter_the_Graph_Endpoint_Here
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
- `Enter_the_Graph_Endpoint_Here` 是应用程序应与之通信的 Microsoft Graph API 实例。
  - 对于全球 Microsoft Graph API 终结点，请将此字符串的两个实例都替换为 `https://graph.microsoft.com`。
  - 对于国家/地区云部署中的终结点，请参阅 Microsoft Graph 文档中的[国家/地区云部署](/graph/deployments)。

## <a name="add-a-method-to-call-a-web-api"></a>添加方法来调用 Web API

在 bin 文件夹中创建另一个名为 fetch.js 的文件，并添加以下代码以对 Microsoft Graph API 进行 REST 调用 ：

```javascript
const axios = require('axios');

/**
 * Calls the endpoint with authorization bearer token.
 * @param {string} endpoint
 * @param {string} accessToken
 */
async function callApi(endpoint, accessToken) {

    const options = {
        headers: {
            Authorization: `Bearer ${accessToken}`
        }
    };

    console.log('request made to web API at: ' + new Date().toString());

    try {
        const response = await axios.default.get(endpoint, options);
        return response.data;
    } catch (error) {
        console.log(error)
        return error;
    }
};

module.exports = {
    callApi: callApi
};
```

此处，使用 `callApi` 方法对需要令牌的受保护资源发出 HTTP `GET` 请求。 然后，该请求将内容返回给调用方。 此方法可在 HTTP 授权标头中添加获取的令牌。 此处的受保护资源是 Microsoft Graph API [用户终结点](/graph/api/user-list)，该终结点显示在其中注册了此应用的租户中的用户。

## <a name="test-the-app"></a>测试应用

你已完成应用程序的创建，现在即可测试应用的功能。

从项目文件夹的根目录中运行以下命令，启动 Node.js 控制台应用：

```console
node . --op getUsers
```

这会导致某些来自 Microsoft Graph API 的 JSON 响应，你应在控制台中看到用户对象的数组：

```console
You have selected: getUsers
request made to web API at: Fri Jan 22 2021 09:31:52 GMT-0800 (Pacific Standard Time)
{
    '@odata.context': 'https://graph.microsoft.com/v1.0/$metadata#users',
    value: [
        {
            displayName: 'Adele Vance'
            givenName: 'Adele',
            jobTitle: 'Retail Manager',
            mail: 'AdeleV@msaltestingjs.onmicrosoft.com',
            mobilePhone: null,
            officeLocation: '18/2111',
            preferredLanguage: 'en-US',
            surname: 'Vance',
            userPrincipalName: 'AdeleV@msaltestingjs.onmicrosoft.com',
            id: 'a6a218a5-f5ae-462a-acd3-581af4bcca00'
        }
    ]
}
```
:::image type="content" source="media/tutorial-v2-nodejs-console/screenshot.png" alt-text="显示 Graph 响应的命令行接口":::

## <a name="how-the-application-works"></a>应用程序的工作原理

此应用程序使用 [OAuth 2.0 客户端凭据授权](./v2-oauth2-client-creds-grant-flow.md)。 这种授予通常用于必须在后台运行的服务器间交互，不需要立即与用户交互。 凭据授权流允许 Web 服务（机密客户端）在调用其他 Web 服务时使用它自己的凭据（而不是模拟用户）进行身份验证。 此身份验证模型支持的应用程序类型通常是守护程序或服务帐户 。

请求客户端凭据流时，其作用域是资源的名称后跟 `/.default`。 此表示法告知 Azure Active Directory (Azure AD) 使用在应用程序注册过程中静态声明的应用程序级权限。 另外，这些 API 权限必须由租户管理员授予。

## <a name="next-steps"></a>后续步骤

如果你想要更深入了解 Microsoft 标识平台上的 Node.js 控制台应用程序开发，请参阅由多部分组成的方案系列：

> [!div class="nextstepaction"]
> [场景：守护程序应用程序](scenario-daemon-overview.md)
