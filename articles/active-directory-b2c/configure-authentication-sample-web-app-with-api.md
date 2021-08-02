---
title: 在使用 Azure Active Directory B2C 调用 Web API 的示例的 Web 应用中配置身份验证
description: 使用 Azure Active Directory B2C 在调用 Web API 的 ASP.NET Web 应用中登录和注册用户。
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
ms.openlocfilehash: fab89113665b8b9534118fc443474349f4e9c3b6
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2021
ms.locfileid: "112072957"
---
# <a name="configure-authentication-in-a-sample-web-application-that-calls-a-web-api-using-azure-active-directory-b2c"></a>在使用 Azure Active Directory B2C 调用 Web API 的示例的 Web 应用中配置身份验证

本文使用一个示例 ASP.NET Web 应用，该示例通过调用 Web API 来说明如何将 Azure Active Directory B2C (Azure AD B2C) 身份验证添加到 Web 应用中。

> [!IMPORTANT]
> 本文中引用的示例 ASP.NET Web 应用用于调用带有持有者令牌的 Web API。 对于不调用 Web API 的 Web 应用，请参阅[使用 Azure Active Directory B2C 在示例 Web 应用中配置身份验证](configure-authentication-sample-web-app.md)。  

## <a name="overview"></a>概述

OpenID Connect (OIDC) 是基于 OAuth 2.0 构建的身份验证协议，用于确保用户安全地登录到应用程序。 此 Web 应用示例使用 [Microsoft Identity Web](https://www.nuget.org/packages/Microsoft.Identity.Web)。 Microsoft Identity Web 是一组 ASP.NET Core 库，可简化向 Web 应用（该应用可调用安全的 Web API）添加身份验证和授权支持的过程。 

登录流涉及以下步骤：

1. 用户导航到 Web 应用并选择“登录”。
1. 该应用发起身份验证请求，并将用户重定向到 Azure AD B2C。
1. 用户[注册或登录](add-sign-up-and-sign-in-policy.md)、[重置密码](add-password-reset-policy.md)或使用[社交帐户](add-identity-provider.md)登录。
1. 成功登录后，Azure AD B2C 向应用返回一个授权代码。
1. 应用执行下列操作
    1. 将此授权代码交换为 ID 令牌、访问令牌和刷新令牌。
    1. 读取 ID 令牌声明，并保留应用程序授权 Cookie。
    1. 将刷新令牌存储在内存中的缓存中供以后使用。

### <a name="app-registration-overview"></a>应用注册概述

若要让应用能够使用 Azure AD B2C 登录并调用 Web API，你需要在 Azure AD B2C 目录中注册两个应用程序。  

- Web 应用注册能让你的应用使用 Azure AD B2C 登录。 在应用注册过程中，请指定重定向 URI。 重定向 URI 是用户在使用 Azure AD B2C 完成身份验证后，Azure AD B2C 将用户重定向到的终结点。 此应用注册过程会生成应用程序 ID（也称为“客户端 ID”），作为该应用的唯一标识。  你还将创建一个客户端密码，你的应用程序可使用该密码安全地获取令牌。

- Web API 注册可使应用调用安全的 Web API。 注册内容包括 Web API 的作用域。 作用域提供了一种方法来管理受保护资源（例如你的 Web API）的访问权限。 你可以向 Web API 的作用域授予 Web 应用访问权限。 在请求访问令牌时，你的应用需要在请求的作用域参数中指定所需的访问权限。  

以下关系图描述了应用注册和应用程序体系结构。

![使用 Web API 调用注册和令牌的 Web 应用](./media/configure-authentication-sample-web-app-with-api/web-app-with-api-architecture.png) 

### <a name="call-to-a-web-api"></a>调用 Web API

[!INCLUDE [active-directory-b2c-app-integration-call-api](../../includes/active-directory-b2c-app-integration-call-api.md)]

### <a name="sign-out"></a>注销

[!INCLUDE [active-directory-b2c-app-integration-sign-out-flow](../../includes/active-directory-b2c-app-integration-sign-out-flow.md)]

## <a name="prerequisites"></a>先决条件

运行以下两者之一的计算机： 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 具有“ASP.NET 和 Web 开发”工作负载的 [Visual Studio 2019 16.8 或更高版本](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)
* [.NET 5.0 SDK](https://dotnet.microsoft.com/download/dotnet)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [Visual Studio Code](https://code.visualstudio.com/download)
* [用于 Visual Studio Code 的 C#（最新版本）](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET 5.0 SDK](https://dotnet.microsoft.com/download/dotnet)

---

## <a name="step-1-configure-your-user-flow"></a>步骤 1：配置用户流

[!INCLUDE [active-directory-b2c-app-integration-add-user-flow](../../includes/active-directory-b2c-app-integration-add-user-flow.md)]

## <a name="step-2-register-web-applications"></a>步骤 2：注册 Web 应用

在此步骤中，你将创建 Web 应用和 Web API 应用程序注册，并指定 Web API 的作用域。

### <a name="21-register-the-web-api-app"></a>2.1 注册 Web API 应用

[!INCLUDE [active-directory-b2c-app-integration-register-api](../../includes/active-directory-b2c-app-integration-register-api.md)]

### <a name="22-configure-web-api-app-scopes"></a>2.2 配置 Web API 应用的作用域

[!INCLUDE [active-directory-b2c-app-integration-api-scopes](../../includes/active-directory-b2c-app-integration-api-scopes.md)]


### <a name="23-register-the-web-app"></a>2.3 注册 Web 应用

请按照以下步骤创建 Web 应用注册：

1. 选择“应用注册”，然后选择“新建注册” 。
1. 输入应用程序的“名称”。 例如，“webapp1”。
1. 在“支持的帐户类型”下，选择“任何标识提供者或组织目录中的帐户(用于通过用户流对用户进行身份验证)” 。 
1. 在“重定向 URI”下，选择“Web”，然后在 URL 文本框中输入 `https://localhost:5000/signin-oidc` 。
1. 在“权限”下，选中“向 OpenID 和脱机访问权限授予管理员同意”复选框。
1. 选择“注册”。
1. 完成应用注册后，选择“概述”。
1. 记录“应用程序（客户端）ID”，以便在稍后配置 Web 应用程序时使用。

    ![获取你的 Web 应用 ID](./media/configure-authentication-sample-web-app-with-api/get-azure-ad-b2c-app-id.png)  

### <a name="24-create-a-web-app-client-secret"></a>2.4 创建 Web 应用的客户端密码

[!INCLUDE [active-directory-b2c-app-integration-client-secret](../../includes/active-directory-b2c-app-integration-client-secret.md)]


### <a name="25-grant-the-web-app-permissions-for-the-web-api"></a>2.5 为 Web API 授予 Web 应用权限

[!INCLUDE [active-directory-b2c-app-integration-grant-permissions](../../includes/active-directory-b2c-app-integration-grant-permissions.md)]

## <a name="step-3-get-the-web-app-sample"></a>步骤 3：获取 Web 应用示例

[下载 zip 文件](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/refs/heads/master.zip)，或从 GitHub 克隆示例 Web 应用。 

```bash
git clone https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2
```

将示例文件提取到文件夹，该文件夹路径的总字符长度须不得超过 260 个字符。

## <a name="step-4-configure-the-sample-web-api"></a>步骤 4：配置示例 Web API

在示例文件夹中的 `4-WebApp-your-API/4-2-B2C/TodoListService` 文件夹下，使用 Visual Studio 或 Visual Studio Code 打开 TodoListService 项目。 

在项目根文件夹下，打开 `appsettings.json` 文件。 此文件包含有关 Azure AD B2C 标识提供者的信息。 Web API 应用使用此信息来验证访问令牌，该访问令牌由 Web 应用当作持有者令牌传递。 更新应用设置的以下属性：

|部分  |键  |Value  |
|---------|---------|---------|
|AzureAdB2C|实例| Azure AD B2C [租户名称](tenant-management.md#get-your-tenant-name)的第一部分。 例如 `https://contoso.b2clogin.com`。|
|AzureAdB2C|域| Azure AD B2C 租户的完整[租户名称](tenant-management.md#get-your-tenant-name)。 例如 `contoso.onmicrosoft.com`。|
|AzureAdB2C|ClientId| [步骤 2.1](#21-register-the-web-api-app) 中的 Web API 应用 ID。|
|AzureAdB2C|SignUpSignInPolicyId|用户流，或在[步骤 1](#step-1-configure-your-user-flow) 中创建的自定义策略。|

你的最终配置文件应类似于下面的 JSON：

```json
{
  "AzureAdB2C": {
    "Instance": "https://contoso.b2clogin.com",
    "Domain": "contoso.onmicrosoft.com",
    "ClientId": "<web-api-app-application-id>",
    "SignedOutCallbackPath": "/signout/<your-sign-up-in-policy>",
    "SignUpSignInPolicyId": "<your-sign-up-in-policy>"
  },
  // More setting here
}
```

### <a name="41-set-the-permission-policy"></a>4.1 设置权限策略

Web API 将验证用户是否已使用持有者令牌进行身份验证，以及持有者令牌是否已配置接受的作用域。 如果持有者令牌不具有其中任何一个接受的作用域，则 Web API 将返回 HTTP 状态代码 403（已禁止）并向响应正文写入一条消息，告知令牌中应配置哪些作用域。 

若要配置接受的作用域，请打开 `Controller/TodoListController.cs` 类，然后设置作用域名称。 作用域名称，不含完整 URI。

```csharp
[RequiredScope("tasks.read")]
```

### <a name="42-run-the-sample-web-api-app"></a>4.2 运行示例 Web API 应用

若要允许 Web 应用调用 Web API 示例，请按照以下步骤运行 Web API：

1. 如果已请求，则还原依赖项。
1. 生成并运行该项目。
1. 生成项目后，Visual Studio 或 Visual Studio Code 将使用以下地址 https://localhost:44332 在浏览器中启动 Web API。

## <a name="step-5-configure-the-sample-web-app"></a>步骤 5：配置示例 Web 应用

在示例文件夹中的 `4-WebApp-your-API/4-2-B2C/Client` 文件夹下，使用 Visual Studio 或 Visual Studio Code 打开 TodoListClient.csproj 项目。 

在项目根文件夹下，打开 `appsettings.json` 文件。 此文件包含有关 Azure AD B2C 标识提供者的信息。 Web 应用使用此信息与 Azure AD B2C 建立信任关系、允许用户登录和注销、获取令牌和验证令牌。 更新应用设置的以下属性：

|部分  |键  |Value  |
|---------|---------|---------|
|AzureAdB2C|实例| Azure AD B2C [租户名称](tenant-management.md#get-your-tenant-name)的第一部分。 例如 `https://contoso.b2clogin.com`。|
|AzureAdB2C|域| Azure AD B2C 租户的完整[租户名称](tenant-management.md#get-your-tenant-name)。 例如 `contoso.onmicrosoft.com`。|
|AzureAdB2C|ClientId| [步骤 2.1](#21-register-the-web-api-app) 中的 Web 应用 ID。|
|AzureAdB2C | ClientSecret | [步骤 2.4](#24-create-a-web-app-client-secret) 中的 Web 应用密码。 | 
|AzureAdB2C|SignUpSignInPolicyId|用户流，或在[步骤 1](#step-1-configure-your-user-flow) 中创建的自定义策略。|
| TodoList | TodoListScope | [步骤 2.5](#25-grant-the-web-app-permissions-for-the-web-api) 中的作用域。|
| TodoList | TodoListBaseAddress | Web API 的基 URI，例如 `https://localhost:44332`|

你的最终配置文件应类似于下面的 JSON：

```JSon
{
  "AzureAdB2C": {
    "Instance": "https://contoso.b2clogin.com",
    "Domain": "contoso.onmicrosoft.com",
    "ClientId": "<web-app-application-id>",
    "ClientSecret": "<web-app-application-secret>",  
    "SignedOutCallbackPath": "/signout/<your-sign-up-in-policy>",
    "SignUpSignInPolicyId": "<your-sign-up-in-policy>"
  },
  "TodoList": {
    "TodoListScope": "https://contoso.onmicrosoft.com/api/demo.read",
    "TodoListBaseAddress": "https://localhost:44332"
  }
}
```


## <a name="step-6-run-the-sample-web-app"></a>步骤 6：运行示例 Web 应用

1. 生成并运行该项目。
1. 浏览到 https://localhost:5000。 
1. 完成注册或登录过程。

身份验证成功后，你会在导航栏中看到显示名称。 若要查看 Azure AD B2C 令牌返回到应用的声明，请选择“TodoList”。

![Web 应用令牌声明](./media/configure-authentication-sample-web-app-with-api/web-api-to-do-list.png)


## <a name="deploy-your-application"></a>部署应用程序

在生产应用程序中，应用注册重定向 URI 通常是运行应用的可公开访问的终结点，比如 `https://contoso.com/signin-oidc`。 

可以随时在注册的应用程序中添加和修改重定向 URI。 重定向 URI 存在以下限制：

* 回复 URL 必须以方案 `https` 开头。
* 回复 URL 区分大小写。 其大小写必须与正在运行的应用程序的 URL 路径的大小写匹配。 

### <a name="token-cache-for-a-web-app"></a>Web 应用的令牌缓存

Web 应用示例在内存中使用令牌缓存序列化。 此实现在示例中非常有用。 如果你不介意令牌缓存在 Web 应用重启后会丢失这一情况，那么它也适用于生产应用程序。 

对于生产环境，建议使用分布式内存缓存。 例如，Redis 缓存、NCache 缓存或 SQL Server 缓存。 有关分布式内存缓存实现的详细信息，请参阅 [Web 应用的令牌缓存](../active-directory/develop/msal-net-token-cache-serialization.md#token-cache-for-a-web-app-confidential-client-application)。


## <a name="next-steps"></a>后续步骤

* 了解[有关代码示例](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-5-B2C#about-the-code)的详细信息
* 了解如何[使用 Azure AD B2C 在自己的 Web 应用中启用身份验证](enable-authentication-web-application.md)
