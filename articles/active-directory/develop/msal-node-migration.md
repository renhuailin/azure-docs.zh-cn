---
title: 将 Node.js 应用程序从 ADAL 迁移到 MSAL | Azure
titleSuffix: Microsoft identity platform
description: 如何更新现有 Node.js 应用程序，以使用 Microsoft 身份验证库 (MSAL) 而不是 Active Directory 身份验证库 (ADAL) 完成身份验证和授权。
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/26/2021
ms.author: v-doeris
ms.openlocfilehash: 4b23538ed02bd737764c24a8511986966473c7ed
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2021
ms.locfileid: "114402381"
---
# <a name="how-to-migrate-a-nodejs-app-from-adal-to-msal"></a>如何将 Node.js 应用从 ADAL 迁移到 MSAL

若要通过 SDK 为在 Microsoft 身份识别平台上注册的应用程序启用身份验证和授权，我们现在会建议使用[适用于 Node 的 Microsoft 身份验证库](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node)。 本文介绍了将应用从适用于 Node 的 Active Directory 身份验证库 (ADAL Node) 迁移到 MSAL Node 时所需完成的重要步骤。

## <a name="prerequisites"></a>先决条件

- Node 10、12 或 14 版。 请参阅[版本支持说明](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node#node-version-support)

## <a name="update-app-registration-settings"></a>更新应用注册设置

使用 ADAL Node 时，你有可能会用到 Azure AD v1.0 终结点。 从 ADAL 迁移到 MSAL 的应用还应考虑切换至 Azure AD v2.0 终结点。

1. 查看 [v1 与 v2 终结点之间的差异](../azuread-dev/azure-ad-endpoint-comparison.md)
1. 如有必要，请相应更新现有应用的注册设置。

> [!NOTE]
> 为确保后向兼容性，MSAL Node 支持 v1.0 和 v2.0 终结点。

## <a name="install-and-import-msal"></a>安装并导入 MSAL

1. 通过 NPM 安装 MSAL Node 程序包：

```console
npm install @azure/msal-node
```

1. 然后，在代码中导入 MSAL Node：

```javascript
const msal = require('@azure/msal-node');
```

1. 最后，卸载 ADAL Node 程序包并删除代码中的任何引用：

```console
npm uninstall adal-node
```

## <a name="initialize-msal"></a>初始化 MSAL

在 ADAL Node 中，你可以初始化 `AuthenticationContext` 对象，该对象之后会为你显示可以在不同身份验证流中使用的方法 (例如适用于 Web 应用的 `acquireTokenWithAuthorizationCode`)。 初始化时，唯一的必需参数是 authority URI：

```javascript
var adal = require('adal-node');

var authorityURI = "https://login.microsoftonline.com/common";
var authenticationContex = new adal.AuthenticationContext(authorityURI);
```

在 MSAL Node 中，你有两种替代方案可以使用：若要构建移动应用或桌面应用，请实例化 `PublicClientApplication` 对象。 构造函数需要用到[配置对象](#configure-msal)，而且其中至少要包含参数 `clientId`。 如果未指定 authority URI，则 MSAL 会默认将其设置为 `https://login.microsoftonline.com/common`。

```javascript
const msal = require('@azure/msal-node');

const pca = new msal.PublicClientApplication({
        auth: {
            clientId = "YOUR_CLIENT_ID"
        }
    });
```

> [!NOTE]
> 如果在 v2.0 中使用 `https://login.microsoftonline.com/common` authority，你将允许用户使用任何 Azure AD 组织或个人 Microsoft 帐户 (MSA) 进行登录。 在 MSAL Node 中，如果你想要限制用户登录到任何 Azure AD 帐户（该行为同样适用于 ADAL Node），则应改为使用 `https://login.microsoftonline.com/organizations`。

另一方面，若要构建 Web 应用或守护程序应用，可以实例化 `ConfidentialClientApplication` 对象。 对于此类应用，你还需要提供客户端凭据，如客户端密码或证书：

```javascript
const msal = require('@azure/msal-node');

const cca = new msal.ConfidentialClientApplication({
        auth: {
            clientId = "YOUR_CLIENT_ID",
            clientSecret = "YOUR_CLIENT_SECRET"
        }
    });
```

与 ADAL 的 `AuthenticationContext` 不同，`PublicClientApplication` 和 `ConfidentialClientApplication` 都要与客户端 ID 绑定。 这意味着，如果你要在应用程序中使用不同的客户端 ID，则需要为每个 ID 实例化一个新的 MSAL 实例。 有关详细信息，请参阅 [MSAL Node 的初始化](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md)

## <a name="configure-msal"></a>配置 MSAL

在 Microsoft 身份识别平台上构建应用时，应用中将包含许多与身份验证相关的参数。 在 ADAL Node 中，`AuthenticationContext` 对象拥有的可用于实例化其本身的配置参数的数量有限，而剩余的参数则可以在代码中自由挂起（例如 clientSecret）：

```javascript
var adal = require('adal-node');

var authority = "https://login.microsoftonline.com/YOUR_TENANT_ID"
var validateAuthority = true,
var cache = null;

var authenticationContext = new adal.AuthenticationContext(authority, validateAuthority, cache);
```

- `authority`：可识别令牌颁发机构的 URL
- `validateAuthority`：可阻止代码向可能存有恶意的颁发机构请求令牌的一项功能
- `cache`：设置此 AuthenticationContext 实例将使用的令牌缓存。  如果未设置此参数，则使用内存缓存中的默认值

另一方面，MSAL Node 使用[配置](https://azuread.github.io/microsoft-authentication-library-for-js/ref/modules/_azure_msal_node.html#configuration)类型的配置对象。 其中包含以下属性：

```javascript
const msal = require('@azure/msal-node');

const msalConfig = {
    auth: {
        clientId: "YOUR_CLIENT_ID",
        authority: "https://login.microsoftonline.com/YOUR_TENANT_ID",
        clientSecret: "YOUR_TENANT_ID",
        knownAuthorities: [], 
    },
    cache: {
        // your implementation of caching
    },
    system: {
        loggerOptions: { /** logging related options */ }
    }
}


const cca = new msal.ConfidentialClientApplication(msalConfig);
```

有一个显著的区别是，MSAL 不含用于禁用颁发机构验证的标志，而且默认情况下一律要对颁发机构进行验证。 MSAL 会将你请求的颁发机构与 Microsoft 已知的一系列颁发机构或你已经在配置中指定的一系列颁发机构进行比较。 有关详细信息，请参阅[配置选项](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/configuration.md)

## <a name="switch-to-msal-api"></a>切换到 MSAL API

ADAL Node 中的大多数公共方法都可在 MSAL Node 中找到同等方法：

| ADAL                                | MSAL                              | 说明                             |
|-------------------------------------|-----------------------------------|-----------------------------------|
| `acquireToken`                      | `acquireTokenSilent`              | 已重命名，现在需要[帐户](https://azuread.github.io/microsoft-authentication-library-for-js/ref/modules/_azure_msal_common.html#accountinfo)对象 |
| `acquireTokenWithAuthorizationCode` | `acquireTokenByCode`              |                                   |
| `acquireTokenWithClientCredentials` | `acquireTokenByClientCredential` |                                   |
| `acquireTokenWithRefreshToken`      | `acquireTokenByRefreshToken`      | 用于迁移有效的[刷新令牌](#remove-logic-around-refresh-tokens)              |
| `acquireTokenWithDeviceCode`        | `acquireTokenByDeviceCode`        | 现在介绍抽象用户代码的获取方法（见下文） |
| `acquireTokenWithUsernamePassword`  | `acquireTokenByUsernamePassword`  |                                   |

但是，ADAL Node 中的某些方法已经弃用，而 MSAL Node 提供了新的方法：

| ADAL                              | MSAL                            | 说明                             |
|-----------------------------------|---------------------------------|-----------------------------------|
| `acquireUserCode`                   | 不适用                             | 与 `acquireTokeByDeviceCode` 合并（见上文）|
| 不适用                               | `acquireTokenOnBehalfOf`          | 一种用于抽象化 [OBO 流](./v2-oauth2-on-behalf-of-flow.md)的新方法 |
| `acquireTokenWithClientCertificate` | 不适用                             | 不再需要，因为现在系统会在初始化期间分配证书（请参阅[配置选项](#configure-msal)） |
| 不适用                               | `getAuthCodeUrl`                  | 一种用于抽象化[授权终结点](./active-directory-v2-protocols.md#endpoints) URL 构造的新方法 |

## <a name="use-scopes-instead-of-resources"></a>使用作用域而不是资源

v1.0 和 v2.0 终结点之间的一个重要区别就在于资源的访问方式。 在 ADAL Node 中，你要先在应用注册门户中注册权限，然后为资源（如 Microsoft Graph）请求访问令牌，如下所示：

```javascript
authenticationContext.acquireTokenWithAuthorizationCode(
    req.query.code,
    redirectUri,
    resource, // e.g. 'https://graph.microsoft.com'
    clientId,
    clientSecret,
    function (err, response) {
        // do something with the authentication response
    }
);
```

MSAL Node 同时支持 v1.0 和 v2.0 终结点。 v2.0 终结点使用以作用域为中心的模型来访问资源。 因此，你在为资源请求访问令牌时，还需要指定该资源的作用域：

```javascript
const tokenRequest = {
    code: req.query.code,
    scopes: ["https://graph.microsoft.com/User.Read"],
    redirectUri: REDIRECT_URI,
};

pca.acquireTokenByCode(tokenRequest).then((response) => {
    // do something with the authentication response
}).catch((error) => {
    console.log(error);
});
```

以作用域为中心的模型有一个优点，即可以使用动态作用域。 使用 v1.0 构建应用程序时，你需要注册该应用程序所需的完整权限集（称为“静态作用域”），以便用户在登录时同意这些权限。 在 v2.0 中，你可以随时按需使用作用域参数来请求相关权限（因此称为“动态作用域”）。 这样，用户便可以提供对作用域的增量同意。 因此，如果你最初只是希望用户登录到你的应用程序，而不需要任何类型的访问权限，则可以这样做。 如果后来需要读取用户的日历，则可以在 acquireToken 方法中请求日历范围，并获取用户的许可。 有关详细信息，请参阅[资源和作用域](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/resources-and-scopes.md)

## <a name="use-promises-instead-of-callbacks"></a>使用承诺而不是回叫

在 ADAL Node 中，成功完成身份验证后，你可使用回叫执行任何操作并获得响应：

```javascript
var context = new AuthenticationContext(authorityUrl, validateAuthority);

context.acquireTokenWithClientCredentials(resource, clientId, clientSecret, function(err, response) {
    if (err) {
        console.log(err);
    } else {
        // do something with the authentication response
    }
});
```

在 MSAL Node 中，则需改为使用承诺：

```javascript
    const cca = new msal.ConfidentialClientApplication(msalConfig);

    cca.acquireTokenByClientCredential(tokenRequest).then((response) => {
        // do something with the authentication response
    }).catch((error) => {
        console.log(error);
    });
```

还可以使用 ES8 附带的 async/await 语法：

```javascript
    try {
        const authResponse = await cca.acquireTokenByCode(tokenRequest);
    } catch (error) {
        console.log(error);
    }
```

## <a name="enable-logging"></a>启用日志记录

在 ADAL Node 中，你可以在代码中的任何位置单独配置日志记录：

```javascript
var adal = require('adal-node');

//PII or OII logging disabled. Default Logger does not capture any PII or OII.
adal.logging.setLoggingOptions({
  log: function (level, message, error) {
    console.log(message);

    if (error) {
        console.log(error);
    }
  },
  level: logging.LOGGING_LEVEL.VERBOSE, // provide the logging level
  loggingWithPII: false  // Determine if you want to log personal identification information. The default value is false.
});
```

在 MSAL Node 中，日志记录是配置选项的一部分，可通过初始化 MSAL Node 实例进行创建：

```javascript
const msal = require('@azure/msal-node');

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

const cca = new msal.ConfidentialClientApplication(msalConfig);
```

## <a name="enable-token-caching"></a>启用令牌缓存

在 ADAL Node 中，你可以选择导入内存中的令牌缓存。 初始化 `AuthenticationContext` 对象时，令牌缓存可用作参数：

```javascript
var MemoryCache = require('adal-node/lib/memory-cache');

var cache = new MemoryCache();
var authorityURI = "https://login.microsoftonline.com/common";

var context = new AuthenticationContext(authorityURI, true, cache);
```

MSAL Node 默认使用内存中的令牌缓存。 无需显式导入令牌缓存；其会作为 `ConfidentialClientApplication` 和 `PublicClientApplication` 类的一部分予以公开。

```javascript
const msalTokenCache = publicClientApplication.getTokenCache();
```

重要的是，以前在 ADAL Node 中使用的令牌缓存无法转移到 MSAL Node，因为缓存架构不兼容。 但是，你可以使用应用先前通过 MSAL Node 中的 ADAL Node 获取的有效刷新令牌。 有关详细信息，请参阅[刷新令牌](#remove-logic-around-refresh-tokens)部分。

你还可以提供自己的缓存插件，以将缓存写入磁盘。 该缓存插件必须实现接口 [ICachePlugin](https://azuread.github.io/microsoft-authentication-library-for-js/ref/interfaces/_azure_msal_common.icacheplugin.html)。 与日志记录一样，高速缓存也是配置选项的一部分，并且是通过初始化 MSAL Node 实例进行创建：

```javascript
const msal = require('@azure/msal-node');

const msalConfig = {
    auth: {
        // authentication related parameters 
    },
    cache: {
        cachePlugin // your implementation of cache plugin
    },
    system: {
        // logging related options 
    }
}

const msalInstance = new ConfidentialClientApplication(msalConfig);
```

可按下方所示实现高速缓存插件示例：

```javascript
const fs = require('fs');

// Call back APIs which automatically write and read into a .json file - example implementation
const beforeCacheAccess = async (cacheContext) => {
    cacheContext.tokenCache.deserialize(await fs.readFile(cachePath, "utf-8"));
};

const afterCacheAccess = async (cacheContext) => {
    if(cacheContext.cacheHasChanged) {
        await fs.writeFile(cachePath, cacheContext.tokenCache.serialize());
    }
};

// Cache Plugin
const cachePlugin = {
    beforeCacheAccess,
    afterCacheAccess
};
```

若要开发[公共客户端应用程序](./msal-client-applications.md)（如桌面应用），[适用于 Node 的 Microsoft 身份验证扩展](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/extensions/msal-node-extensions)可提供安全机制，以使客户端应用程序能够跨平台执行令牌缓存的序列化和暂留操作。 支持的平台包括 Windows、Mac 和 Linux。

> [!NOTE]
> 若为 Web 应用程序，则不建议使用[适用于 Node 的 Microsoft 身份验证扩展](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/extensions/msal-node-extensions)，因为这可能会导致缩放和性能问题。 相反，我们建议使用 Web 应用将缓存保存到会话中。

## <a name="remove-logic-around-refresh-tokens"></a>删除有关刷新令牌的逻辑

在 ADAL Node 中，如果刷新令牌 (RT) 已公开，则你便可通过高速缓存令牌及使用 `acquireTokenWithRefreshToken` 方法来开发与使用这些令牌有关的解决方案。 与 RT 特别相关的典型方案：

- 长期运行的服务，即当用户不再保持连接时，代表用户执行刷新仪表板等操作。
- WebFarm 方案，允许客户端将 RT 带入 Web 服务（高速缓存是在客户端完成，而不是服务器端，而且 cookie 经加密）。

出于安全原因，MSAL Node 和其他 MSAL 不会公开刷新令牌。 而是由 MSAL 代你处理令牌刷新。 因此，你无需再为此生成逻辑。 但是，可以利用以前从 ADAL Node 的缓存中获得的（仍然有效的）刷新令牌，使用 MSAL Node 获得一组新的令牌。 为此，MSAL Node 提供 `acquireTokenByRefreshToken`，它等同于 ADAL Node 的 `acquireTokenWithRefreshToken` 方法：

```javascript
var msal = require('@azure/msal-node');

const config = {
    auth: {
        clientId: "ENTER_CLIENT_ID",
        authority: "https://login.microsoftonline.com/ENTER_TENANT_ID",
        clientSecret: "ENTER_CLIENT_SECRET"
    }
};

const cca = new msal.ConfidentialClientApplication(config);

const refreshTokenRequest = {
    refreshToken: "", // your previous refresh token here
    scopes: ["user.read"],
};

cca.acquireTokenByRefreshToken(refreshTokenRequest).then((response) => {
    console.log(JSON.stringify(response));
}).catch((error) => {
    console.log(JSON.stringify(error));
});
```

> [!NOTE]
> 建议在利用仍然有效的刷新令牌来使用 MSAL Node 的 `acquireTokenByRefreshToken` 方法获得一组新的令牌（如上所示）后，销毁旧的 ADAL Node 令牌缓存。

## <a name="handle-errors-and-exceptions"></a>处理错误和异常

使用 MSAL Node 时，最常出现的错误类型是 `interaction_required` 错误。 通常只需启动交互式令牌获取提示，就能解决此错误。 例如，使用 `acquireTokenSilent` 时，如果没有缓存的刷新令牌，则 MSAL Node 将无法以静默方式获取访问令牌。 同样地，你尝试访问的 Web API 可能实施了[条件访问](../conditional-access/overview.md)策略，要求用户执行[多重身份验证](../authentication/concept-mfa-howitworks.md) (MFA)。 在这种情况下，通过触发 `acquireTokenByCode` 处理 `interaction_required` 错误时，系统会提示用户执行 MFA，使用户能够履行该策略。

不过，可能出现的另一个常见错误是 `consent_required`。当获取受保护资源访问令牌所需的权限未经用户同意时，就会出现此错误。 与在 `interaction_required` 中一样，`consent_required` 错误的解决方法通常是使用 `acquireTokenByCode` 方法启动交互式令牌获取提示。

## <a name="run-the-app"></a>运行应用

完成更改后，运行应用并测试身份验证方案：

```console
npm start
```

## <a name="example-acquiring-tokens-with-adal-node-vs-msal-node"></a>示例：通过 ADAL Node 和 MSAL Node 获取令牌

下方片段演示了 Express.js 框架中的一款机密客户端 Web 应用。 其会在用户完成身份验证路由 `/auth` 时执行登录，通过 `/redirect` 路由获取 Microsoft Graph 的访问令牌，然后显示上述令牌的内容。


<table>
<tr><td> 使用 ADAL 节点 </td><td> 使用 MSAL 节点 </td></tr>
<tr>
<td>

```javascript
// Import dependencies
var express = require('express');
var crypto = require('crypto');
var adal = require('adal-node');

// Authentication parameters
var clientId = 'Enter_the_Application_Id_Here';
var clientSecret = 'Enter_the_Client_Secret_Here';
var tenant = 'common';
var authorityUrl = 'https://login.microsoftonline.com/' + tenant;
var redirectUri = 'http://localhost:3000/redirect';
var resource = 'https://graph.microsoft.com';

// Configure logging
adal.Logging.setLoggingOptions({
    log: function (level, message, error) {
        console.log(message);
    },
    level: adal.Logging.LOGGING_LEVEL.VERBOSE,
    loggingWithPII: false
});

// Auth code request URL template
var templateAuthzUrl = 'https://login.microsoftonline.com/' 
    + tenant + '/oauth2/authorize?response_type=code&client_id=' 
    + clientId + '&redirect_uri=' + redirectUri 
    + '&state=<state>&resource=' + resource;

// Initialize express
var app = express();

// State variable persists throughout the app lifetime
app.locals.state = "";

app.get('/auth', function(req, res) {

    // Create a random string to use against XSRF
    crypto.randomBytes(48, function(ex, buf) {
        app.locals.state = buf.toString('base64')
            .replace(/\//g, '_')
            .replace(/\+/g, '-');
        
        // Construct auth code request URL
        var authorizationUrl = templateAuthzUrl
            .replace('<state>', app.locals.state);

        res.redirect(authorizationUrl);
    });
});

app.get('/redirect', function(req, res) {
    // Compare state parameter against XSRF
    if (app.locals.state !== req.query.state) {
        res.send('error: state does not match');
    }

    // Initialize an AuthenticationContext object
    var authenticationContext = 
        new adal.AuthenticationContext(authorityUrl);
    
    // Exchange auth code for tokens
    authenticationContext.acquireTokenWithAuthorizationCode(
        req.query.code, 
        redirectUri, 
        resource, 
        clientId, 
        clientSecret,
        function(err, response) {
            res.send(response);
        }
    );
});

app.listen(3000, function() { 
    console.log(`listening on port 3000!`); 
});
```

</td>
<td>

```javascript
// Import dependencies
const express = require("express");
const msal = require('@azure/msal-node');

// Authentication parameters
const config = {
    auth: {
        clientId: "Enter_the_Application_Id_Here",
        authority: "https://login.microsoftonline.com/common",
        clientSecret: "Enter_the_Client_Secret_Here"
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
};

const REDIRECT_URI = "http://localhost:3000/redirect";

// Initialize MSAL Node object using authentication parameters
const cca = new msal.ConfidentialClientApplication(config);

// Initialize express
const app = express();

app.get('/auth', (req, res) => {
    
    // Construct a request object for auth code
    const authCodeUrlParameters = {
        scopes: ["user.read"],
        redirectUri: REDIRECT_URI,
    };

    // Request auth code, then redirect
    cca.getAuthCodeUrl(authCodeUrlParameters)
        .then((response) => {
            res.redirect(response);
        }).catch((error) => res.send(error));
});

app.get('/redirect', (req, res) => {
    
    // Use the auth code in redirect request to construct
    // a token request object
    const tokenRequest = {
        code: req.query.code,
        scopes: ["user.read"],
        redirectUri: REDIRECT_URI,
    };

    // Exchange the auth code for tokens
    cca.acquireTokenByCode(tokenRequest)
        .then((response) => {
            res.send(response);
        }).catch((error) => res.status(500).send(error));
});

app.listen(3000, () => 
    console.log(`listening on port 3000!`));
```

</td>
</tr>
</table>

## <a name="next-steps"></a>后续步骤

- [MSAL Node API 引用](https://azuread.github.io/microsoft-authentication-library-for-js/ref/modules/_azure_msal_node.html)
- [MSAL Node 代码示例](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/msal-node-samples)
