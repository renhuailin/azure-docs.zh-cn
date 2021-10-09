---
title: 使用 Azure Active Directory B2C 在 Web API 中启用身份验证
description: 本文介绍了如何使用 Azure Active Directory B2C 来保护 Web API。
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
ms.openlocfilehash: a67cdd9ba92e3c78c5cb29a827cf537ba2e372ae
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124740202"
---
# <a name="enable-authentication-in-your-own-web-api-by-using-azure-ad-b2c"></a>使用 Azure AD B2C 在你自己的 Web API 中启用身份验证

若要授权访问 Web API，请仅处理包含 Azure Active Directory B2C (Azure AD B2C) 颁发的有效访问令牌的请求。 本文介绍了如何启用对 Web API 的 Azure AD B2C 授权。 完成本文中的步骤后，只有获取了有效访问令牌的用户才有权调用你的 Web API 终结点。  

## <a name="prerequisites"></a>先决条件

在开始操作之前，请阅读以下文章之一，其中介绍了如何为调用 Web API 的应用配置身份验证。 然后，按照本文中的步骤将示例 Web API 替换为你自己的 Web API。   

- [在示例 ASP.NET Core 应用程序中配置身份验证](configure-authentication-sample-web-app-with-api.md)
- [在示例单页应用程序 (SPA) 中配置身份验证](configure-authentication-sample-spa-app.md)

## <a name="overview"></a>概述

基于令牌的身份验证确保对 Web API 的请求附带有效的访问令牌。 

此应用执行以下操作：

1. 通过 Azure AD B2C 对用户进行身份验证。
1. 获取一个具有对 Web API 终结点的必需权限（作用域）的访问令牌。
1. 使用以下格式，在 HTTP 请求的身份验证标头中将该访问令牌作为持有者令牌进行传递： 

    ```http
    Authorization: Bearer <token>
    ```    

Web API 执行下列操作：

1. 从 HTTP 请求中的授权标头读取持有者令牌。

1. 验证该令牌。 
1. 验证令牌中的权限（作用域）。
1. 读取在令牌中编码的声明（可选）。
1. 响应 HTTP 请求。 

### <a name="app-registration-overview"></a>应用注册概述

要使应用能够通过 Azure AD B2C 登录并调用 Web API，必须在 Azure AD B2C 目录中注册两个应用程序。  

- Web 应用程序、移动应用程序或 SPA 应用程序注册使你的应用可以通过 Azure AD B2C 登录。 应用注册过程会生成一个应用程序 ID（也称为客户端 ID），用于唯一标识你的应用程序（例如，应用 ID：1）。  

- Web API 注册使你的应用可以调用受保护的 Web API。 注册将公开 Web API 权限（范围）。 应用注册过程会生成一个应用程序 ID，用于唯一标识你的 Web API（例如，应用 ID：2）。  向应用（应用 ID：1）授予对 Web API 范围（应用 ID：2）的权限。 

下图描绘了应用程序注册和应用程序体系结构：

![图中显示了使用 Web API 的应用的应用程序注册和应用程序体系结构。](./media/enable-authentication-web-api/app-with-api-architecture.png) 

## <a name="prepare-your-development-environment"></a>准备开发环境  

在后续部分，你将创建一个新的 Web API 项目。 选择编程语言：ASP.NET Core 或 Node.js。 确保有一台运行以下任一软件的计算机： 

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)

* [Visual Studio Code](https://code.visualstudio.com/download)
* [C# for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)（最新版本）
* [.NET 5.0 SDK](https://dotnet.microsoft.com/download/dotnet)

# <a name="nodejs"></a>[Node.js](#tab/nodejsgeneric)

* [Visual Studio Code](https://code.visualstudio.com/) 或其他代码编辑器
* [Node.js 运行时](https://nodejs.org/en/download/)

---

## <a name="step-1-create-a-protected-web-api"></a>步骤 1：创建受保护的 Web API

创建一个新的 Web API 项目。 首先，选择要使用的编程语言：ASP.NET Core 或 Node.js。 

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)

使用 [`dotnet new`](/dotnet/core/tools/dotnet-new) 命令。 `dotnet new` 命令创建一个名为 TodoList 的新文件夹，其中包含 Web API 项目资产。 打开目录，然后打开 [Visual Studio Code](https://code.visualstudio.com/)。 

```dotnetcli
dotnet new webapi -o TodoList
cd TodoList
code . 
```

系统提示你“将所需资产添加到项目”时，请选择“是”。

# <a name="nodejs"></a>[Node.js](#tab/nodejsgeneric)

使用适用于 [Node.js](https://nodejs.org/) 的 [Express](https://expressjs.com/) 生成 Web API。 若要创建 Web API，请执行以下操作：

1. 创建名为 TodoList 的新文件夹。 
1. 在 TodoList 文件夹下，创建一个名为 app.js 的文件。
1. 在命令 shell 中，运行 `npm init -y`。 此命令为 Node.js 项目创建默认的 package.json 文件。
1. 在命令 shell 中，运行 `npm install express`。 此命令安装 Express 框架。

--- 

## <a name="step-2-install-the-dependencies"></a>步骤 2：安装依赖项

在你的 Web API 项目中添加身份验证库。 身份验证库将分析 HTTP 身份验证头，验证令牌，并提取声明。 有关详细信息，请查看该库的文档。


# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)

若要添加身份验证库，请运行以下命令安装相应的包：

```dotnetcli
dotnet add package Microsoft.Identity.Web
```

# <a name="nodejs"></a>[Node.js](#tab/nodejsgeneric)

若要添加身份验证库，请运行以下命令安装相应的包：

```
npm install passport
npm install passport-azure-ad
npm install morgan
```
 
[morgen 包](https://www.npmjs.com/package/morgan)是适用于 Node.js 的 HTTP 请求记录器中间件。

---

## <a name="step-3-initiate-the-authentication-library"></a>步骤 3：启动身份验证库

添加所需的代码来启动身份验证库。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)

打开 Startup.cs，然后在类的开头添加以下 `using` 声明：

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
using Microsoft.Identity.Web;
```

找到 `ConfigureServices(IServiceCollection services)` 函数。 然后在 `services.AddControllers();` 代码行的前面添加以下代码片段：


```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Adds Microsoft Identity platform (Azure AD B2C) support to protect this Api
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddMicrosoftIdentityWebApi(options =>
    {
        Configuration.Bind("AzureAdB2C", options);

        options.TokenValidationParameters.NameClaimType = "name";
    },
    options => { Configuration.Bind("AzureAdB2C", options); });
    // End of the Microsoft Identity platform block    

    services.AddControllers();
}
```

找到 `Configure` 函数。 然后，紧接在 `app.UseRouting();` 代码行的后面添加以下代码片段：


```csharp
app.UseAuthentication();
```

更改后，你的代码应如以下代码片段所示：

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }

    app.UseHttpsRedirection();

    app.UseRouting();
    
    // Add the following line 
    app.UseAuthentication();
    // End of the block you add
    
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });
}
```

# <a name="nodejs"></a>[Node.js](#tab/nodejsgeneric)

将以下 JavaScript 代码添加到 app.js 文件。

```javascript
// Import the required libraries
const express = require('express');
const morgan = require('morgan');
const passport = require('passport');
const config = require('./config.json');

// Import the passport Azure AD library
const BearerStrategy = require('passport-azure-ad').BearerStrategy;

// Set the Azure AD B2C options
const options = {
    identityMetadata: `https://${config.credentials.tenantName}.b2clogin.com/${config.credentials.tenantName}.onmicrosoft.com/${config.policies.policyName}/${config.metadata.version}/${config.metadata.discovery}`,
    clientID: config.credentials.clientID,
    audience: config.credentials.clientID,
    issuer: config.credentials.issuer,
    policyName: config.policies.policyName,
    isB2C: config.settings.isB2C,
    scope: config.resource.scope,
    validateIssuer: config.settings.validateIssuer,
    loggingLevel: config.settings.loggingLevel,
    passReqToCallback: config.settings.passReqToCallback
}

// Instantiate the passport Azure AD library with the Azure AD B2C options
const bearerStrategy = new BearerStrategy(options, (token, done) => {
        // Send user info using the second argument
        done(null, { }, token);
    }
);

// Use the required libraries
const app = express();

app.use(morgan('dev'));

app.use(passport.initialize());

passport.use(bearerStrategy);

//enable CORS (for testing only -remove in production/deployment)
app.use((req, res, next) => {
    res.header('Access-Control-Allow-Origin', '*');
    res.header('Access-Control-Allow-Headers', 'Authorization, Origin, X-Requested-With, Content-Type, Accept');
    next();
});
```
--- 

## <a name="step-4-add-the-endpoints"></a>步骤 4：添加终结点

向 Web API 中添加两个终结点：

- 匿名的 `/public` 终结点。 此终结点返回当前日期和时间。 使用它通过匿名调用来调试 Web API。
- 受保护的 `/hello` 终结点。 此终结点返回访问令牌中的 `name` 声明值。

**若要添加匿名终结点，请执行以下操作：**

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)

在 /Controllers 文件夹下，添加 PublicController.cs 文件，然后向其中添加以下代码片段：

```csharp
using System;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;

namespace TodoList.Controllers
{
    [ApiController]
    [Route("[controller]")]
    public class PublicController : ControllerBase
    {
        private readonly ILogger<PublicController> _logger;

        public PublicController(ILogger<PublicController> logger)
        {
            _logger = logger;
        }

        [HttpGet]
        public ActionResult Get()
        {
            return Ok( new {date = DateTime.UtcNow.ToString()});
        }
    }
}
```

# <a name="nodejs"></a>[Node.js](#tab/nodejsgeneric)

在 app.js 文件中，添加以下 JavaScript 代码：


```javascript
// API anonymous endpoint
app.get('/public', (req, res) => res.send( {'date': new Date() } ));
```

--- 

**若要添加受保护的终结点，请执行以下操作：**

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)

在 /Controllers 文件夹下，添加 HelloController.cs 文件，然后向其中添加以下代码：

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;
using Microsoft.Identity.Web.Resource;

namespace TodoList.Controllers
{
    [Authorize]
    [RequiredScope("tasks.read")]
    [ApiController]
    [Route("[controller]")]
    public class HelloController : ControllerBase
    {

        private readonly ILogger<HelloController> _logger;
        private readonly IHttpContextAccessor _contextAccessor;

        public HelloController(ILogger<HelloController> logger, IHttpContextAccessor contextAccessor)
        {
            _logger = logger;
            _contextAccessor = contextAccessor;
        }

        [HttpGet]
        public ActionResult Get()
        {
            return Ok( new { name = User.Identity.Name});
        }
    }
}
```

`HelloController` 控制器由 [AuthorizeAttribute](/aspnet/core/security/authorization/simple) 修饰，这将仅向已通过身份验证的用户授予访问权限。 

该控制器还使用 `[RequiredScope("tasks.read")]` 进行修饰。 [RequiredScopeAttribute](/dotnet/api/microsoft.identity.web.resource.requiredscopeattribute.-ctor) 验证 Web API 是否是使用正确的范围 `tasks.read` 调用的。 

# <a name="nodejs"></a>[Node.js](#tab/nodejsgeneric)

在 app.js 文件中，添加以下 JavaScript 代码： 

```javascript
// API protected endpoint
app.get('/hello',
    passport.authenticate('oauth-bearer', {session: false}),
    (req, res) => {
        console.log('Validated claims: ', req.authInfo);
        
        // Service relies on the name claim.  
        res.status(200).json({'name': req.authInfo['name']});
    }
);
```

`/hello` 终结点首先调用 `passport.authenticate()` 函数。 该身份验证函数仅向已通过身份验证的用户授予访问权限。 

该身份验证函数还会验证 Web API 是否是使用正确的范围调用的。 [配置文件](#step-6-configure-the-web-api)中指定了允许的范围。 

--- 

## <a name="step-5-configure-the-web-server"></a>步骤 5：配置 Web 服务器

在开发环境中，将 Web API 设置为侦听传入的 HTTP 请求端口号。 此示例使用 HTTP 端口 6000。 Web API 的基 URI 将为：<'http://localhost:6000 '>

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)

将以下 JSON 代码片段添加到 appsettings.json 文件。 

```json
"Kestrel": {
    "EndPoints": {
      "Http": {
        "Url": "http://localhost:6000"
      }
    }
  }
```

# <a name="nodejs"></a>[Node.js](#tab/nodejsgeneric)

将以下 JavaScript 代码添加到 app.js 文件。 

```javascript
// Starts listening on port 6000
const port = process.env.PORT || 6000;

app.listen(port, () => {
    console.log('Listening on port ' + port);
});
```

---

## <a name="step-6-configure-the-web-api"></a>步骤 6：配置 Web API

向配置文件中添加配置。 该文件包含有关 Azure AD B2C 标识提供者的信息。 Web API 应用使用此信息来验证访问令牌，该访问令牌由 Web 应用当作持有者令牌传递。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)

在项目根文件夹下，打开 appsettings.json 文件，然后添加以下设置：

```json
{
  "AzureAdB2C": {
    "Instance": "https://contoso.b2clogin.com",
    "Domain": "contoso.onmicrosoft.com",
    "ClientId": "<web-api-app-application-id>",
    "SignedOutCallbackPath": "/signout/<your-sign-up-in-policy>",
    "SignUpSignInPolicyId": "<your-sign-up-in-policy>"
  },
  // More settings here
}
```

在 appsettings.json 文件中，更新以下属性： 

|部分  |密钥  |值  |
|---------|---------|---------|
|AzureAdB2C|实例| Azure AD B2C [租户名称](tenant-management.md#get-your-tenant-name)的第一部分（例如 `https://contoso.b2clogin.com`）。|
|AzureAdB2C|域| Azure AD B2C 租户的完整[租户名称](tenant-management.md#get-your-tenant-name)（例如 `contoso.onmicrosoft.com`）。|
|AzureAdB2C|ClientId| Web API 应用程序 ID。 在[上面的示意图](#app-registration-overview)中，它是标有“应用 ID: 2”的应用程序。 若要了解如何获取 Web API 应用程序注册 ID，请参阅[先决条件](#prerequisites)。 |
|AzureAdB2C|SignUpSignInPolicyId|用户流或自定义策略。 若要了解如何获取用户流或策略，请参阅[先决条件](#prerequisites)。  |

# <a name="nodejs"></a>[Node.js](#tab/nodejsgeneric)

在项目根文件夹下创建 config.json 文件，然后向其中添加以下 JSON 代码片段：  

```json
{
    "credentials": {
        "tenantName": "<your-tenant-name>",
        "clientID": "<your-webapi-application-ID>",
        "issuer": "https://<your-tenant-name>.b2clogin.com/<your-tenant-ID>/v2.0/"
    },
    "policies": {
        "policyName": "b2c_1_susi"
    },
    "resource": {
        "scope": ["tasks.read"]
    },
    "metadata": {
        "discovery": ".well-known/openid-configuration",
        "version": "v2.0"
    },
    "settings": {
        "isB2C": true,
        "validateIssuer": true,
        "passReqToCallback": false,
        "loggingLevel": "info"
    }
}
```

在 config.json 文件中，更新以下属性：

|部分  |密钥  |值  |
|---------|---------|---------|
| 凭据 | tenantName | Azure AD B2C [租户名称](tenant-management.md#get-your-tenant-name)的第一部分（例如 `contoso`）。|
| 凭据 |clientID | Web API 应用程序 ID。 在[上面的示意图](#app-registration-overview)中，它是标有“应用 ID: 2”的应用程序。 若要了解如何获取 Web API 应用程序注册 ID，请参阅[先决条件](#prerequisites)。 |
| 凭据 | 颁发者| 令牌颁发者 `iss` 声明值。 默认情况下，Azure AD B2C 采用以下格式返回令牌：`https://<your-tenant-name>.b2clogin.com/<your-tenant-ID>/v2.0/`。 请将 `<your-tenant-name>` 替换为你的 Azure AD B2C [租户名称](tenant-management.md#get-your-tenant-name)的第一部分。 将 `<your-tenant-ID>` 替换为你的 [Azure AD B2C 租户 ID](tenant-management.md#get-your-tenant-id)。 |
| 策略 | policyName | 用户流或自定义策略。 若要了解如何获取用户流或策略，请参阅[先决条件](#prerequisites)。|
| resource | scope | Web API 应用程序注册的范围。 若要了解如何获取 Web API 范围，请参阅[先决条件](#prerequisites)。|

---

## <a name="step-7-run-and-test-the-web-api"></a>步骤 7：运行并测试 Web API

最后，使用你的 Azure AD B2C 环境设置运行该 Web API。 

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)

在命令 shell 中，通过运行以下命令来启动 Web 应用：

```bush
 dotnet run
```

你应会看到以下输出，这意味着你的应用已启动并正在运行，并已做好接收请求的准备。

```
Now listening on: http://localhost:6000
```

若要停止该程序，请在命令 shell 中选择“Ctrl+C”。 可以使用 `node app.js` 命令重新运行应用。

> [!TIP]
> 若要运行 `dotnet run` 命令，还可以使用 [Visual Studio Code 调试程序](https://code.visualstudio.com/docs/editor/debugging)。 Visual Studio Code 的内置调试程序有助于加速编辑、编译和调试循环。

打开浏览器并转到 `http://localhost:6000/public`。 在浏览器窗口中，应会看到显示了以下文本以及当前日期和时间。

# <a name="nodejs"></a>[Node.js](#tab/nodejsgeneric)

在命令 shell 中，通过运行以下命令来启动 Web 应用：

```bush
node app.js
```

你应会看到以下输出，这意味着你的应用已启动并正在运行，并已做好接收请求的准备。

```
Example app listening on port 6000!
```

若要停止该程序，请在命令 shell 中选择“Ctrl+C”。 可以使用 `node app.js` 命令重新运行应用。

> [!TIP]
> 或者，若要运行 `node app.js` 命令，可以使用 [Visual Studio Code 调试程序](https://code.visualstudio.com/docs/editor/debugging)。 Visual Studio Code 的内置调试程序有助于加速编辑、编译和调试循环。

打开浏览器并转到 `http://localhost:6000/public`。 在浏览器窗口中，应会看到显示了以下文本以及当前日期和时间。

---

## <a name="step-8-call-the-web-api-from-your-app"></a>步骤 8：从应用中调用 Web API

尝试在不使用访问令牌的情况下调用受保护的 Web API 终结点。 打开浏览器并转到 `http://localhost:6000/hello`。 API 将返回“未授权”HTTP 错误消息，此时可以确认该 Web API 受持有者令牌的保护。

继续配置应用以调用 Web API。 有关指导，请参阅[先决条件](#prerequisites)部分。

观看此视频，了解将 Azure AD B2C 与 API 集成时的一些最佳做法。

>[!Video https://www.youtube.com/embed/wuUu71RcsIo]

## <a name="next-steps"></a>后续步骤

获取 GitHub 中的完整示例：

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)
* 使用 [Microsoft 标识库](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-2-B2C/TodoListService)获取 Web API。

# <a name="nodejs"></a>[Node.js](#tab/nodejsgeneric)
* 使用 [Passport.js 库](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi)获取 Web API。
