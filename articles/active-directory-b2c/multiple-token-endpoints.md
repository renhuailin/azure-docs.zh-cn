---
title: 将基于 OWIN 的 Web API 迁移到 b2clogin.com 或自定义域
titleSuffix: Azure AD B2C
description: 了解如何在将应用程序迁移到 b2clogin.com 时启用 .NET Web API 以支持多个令牌颁发者颁发的令牌。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/15/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 860f167913211ee7c511e515937f29ba5bf954cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "103491539"
---
# <a name="migrate-an-owin-based-web-api-to-b2clogincom-or-a-custom-domain"></a>将基于 OWIN 的 Web API 迁移到 b2clogin.com 或自定义域

本文介绍一种技术，该技术在实现[用于 .NET (OWIN) 的开放 Web 界面](http://owin.org/)的 Web API 中实现对多个令牌发行者的支持。 将 Azure Active Directory B2C (Azure AD B2C) API 及其应用程序从一个域迁移到另一个域时，支持多个令牌终结点非常有用。 例如，从 login.microsoftonline.com 迁移到 b2clogin.com 或[自定义域](custom-domain.md)。

通过在 API 中添加对接受 b2clogin.com、login.microsoftonline.com 或自定义域颁发的令牌的支持，你可以分阶段迁移 Web 应用程序，然后从 API 中删除对 login.microsoftonline.com 颁发的令牌支持。

以下部分演示了如何在使用 [Microsoft OWIN][katana] 中间件组件 (Katana) 的 Web API 中启用多个颁发者的示例。 虽然代码示例特定于 Microsoft OWIN 中间件，但常规技术应该适用于其他 OWIN 库。

## <a name="prerequisites"></a>先决条件

在继续执行本文中的步骤以前，需要准备好以下 Azure AD B2C 资源：

* 在租户中创建的[用户流](tutorial-create-user-flows.md)或[自定义策略](custom-policy-get-started.md)

## <a name="get-token-issuer-endpoints"></a>获取令牌颁发者终结点

首先需要获取要在 API 中支持的每个颁发者的令牌颁发者终结点 URI。 若要获取 Azure AD B2C 租户支持的 b2clogin.com 和 login.microsoftonline.com 终结点，请在 Azure 门户中使用以下过程。

首先选择一个现有用户流：

1. 在 [Azure 门户](https://portal.azure.com)中导航到你的 Azure AD B2C 租户
1. 在“策略”下，选择“用户流(策略)” 
1. 选择现有的策略（例如 B2C_1_signupsignin1），然后选择“运行用户流”
1. 在页面顶部附近的“运行用户流”标题下，选择超链接以导航到该用户流的 OpenID Connect 发现终结点。

    ![Azure 门户的“立即运行”页中的“已知 URI”超链接](media/multi-token-endpoints/portal-01-policy-link.png)

1. 在浏览器中打开的页上，记下 `issuer` 值，例如：

    `https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

1. 使用“选择域”下拉列表选择其他域，然后再次执行前两个步骤，并记录其 `issuer` 值。

现在，你应记录了两个类似于以下内容的 URI：

```
https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
```

### <a name="custom-policies"></a>自定义策略

如果你有自定义策略而不是用户流，则可以使用类似的过程来获取颁发者 URI。

1. 导航到 Azure AD B2C 租户
1. 选择“标识体验框架”
1. 选择其中一种信赖方策略，例如 B2C_1A_signup_signin
1. 使用“选择域”下拉列表选择域，例如 yourtenant.b2clogin.com
1. 选择“OpenID Connect 发现终结点”下显示的超链接
1. 记录该 `issuer` 值
1. 为另一个域（例如 login.microsoftonline.com）执行步骤 4-6

## <a name="get-the-sample-code"></a>获取示例代码

现在，你已拥有两个令牌终结点 URI，你需要更新代码，将这两个终结点指定为有效颁发者。 若要浏览示例，请下载或克隆示例应用程序，然后更新示例，以便支持将这两个终结点作为有效的颁发者。

下载存档：[active-directory-b2c-dotnet-webapp-and-webapi-master.zip][sample-archive]

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

## <a name="enable-multiple-issuers-in-web-api"></a>在 Web API 中启用多个颁发者

在此部分，更新代码以将两个令牌颁发者终结点指定为有效。

1. 在 Visual Studio 中打开 B2C-WebAPI-DotNet.sln 解决方案
1. 在 TaskService 项目中，在编辑器中打开 TaskService\\App_Start\\Startup.Auth.cs 文件 **** **
1. 将下面的 `using` 指令添加到文件的顶部：

    `using System.Collections.Generic;`
1. 将 [`ValidIssuers`][validissuers] 属性添加到 [`TokenValidationParameters`][tokenvalidationparameters] 定义，并指定上一部分记录的两个 URI：

    ```csharp
    TokenValidationParameters tvps = new TokenValidationParameters
    {
        // Accept only those tokens where the audience of the token is equal to the client ID of this app
        ValidAudience = ClientId,
        AuthenticationType = Startup.DefaultPolicy,
        ValidIssuers = new List<string> {
            "https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/",
            "https://{your-b2c-tenant}.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/"//,
            //"https://your-custom-domain/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/"
        }
    };
    ```

`TokenValidationParameters` 由 MSAL.NET 提供，由 OWIN 中间件在 Startup.Auth.cs 中代码的下一部分中使用。 指定多个有效颁发者后，OWIN 应用程序管道就会注意到这两个令牌终结点均为有效颁发者。

```csharp
app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
{
    // This SecurityTokenProvider fetches the Azure AD B2C metadata &  from the OpenID Connect metadata endpoint
    AccessTokenFormat = new JwtFormat(tvps, new tCachingSecurityTokenProvider(String.Format(AadInstance, ultPolicy)))
});
```

如前所述，其他 OWIN 库通常提供类似的功能来支持多个颁发者。 虽然为每个库都提供示例超出了本文范围，但你可以对大多数库使用类似技术。

## <a name="switch-endpoints-in-web-app"></a>切换 Web 应用中的终结点

Web API 现支持两个 URI，你现在需要更新 Web 应用程序，以便从 b2clogin.com 终结点检索令牌。

例如，可以通过修改 TaskWebApp 项目中 TaskWebApp\\Web.config 文件的 `ida:AadInstance` 值，将示例 Web 应用程序配置为使用新的终结点。

更改 TaskWebApp 的 Web.config 中的 `ida:AadInstance` 值，使其引用 `{your-b2c-tenant-name}.b2clogin.com` 而不是 `login.microsoftonline.com`。

早于:

```xml
<!-- Old value -->
<add key="ida:AadInstance" value="https://login.microsoftonline.com/tfp/{0}/{1}" />
```

晚于（将 `{your-b2c-tenant}` 替换为你的 B2C 租户名称）：

```xml
<!-- New value -->
<add key="ida:AadInstance" value="https://{your-b2c-tenant}.b2clogin.com/tfp/{0}/{1}" />
```

在 Web 应用的执行过程中构造终结点字符串时，将在请求令牌时使用基于 b2clogin.com 的终结点。

使用自定义域时：

```xml
<!-- Custom domain -->
<add key="ida:AadInstance" value="https://custom-domain/{0}/{1}" />
```

## <a name="next-steps"></a>后续步骤

本文介绍了一种配置 Web API（该 API 实现 Microsoft OWIN 中间件 (Katana)）的方法，以接受来自多个颁发者终结点的令牌。 你可能会注意到，如果希望针对自己的租户构建并运行这些项目，则需要更改 TaskService 和 TaskWebApp 项目的 Web.Config 文件中的其他几个字符串。 如果希望看到项目实际运行，欢迎对其进行适当地修改，但此操作的完整演练超出了本文的范围。

有关 Azure AD B2C 发出的不同类型的安全令牌的详细信息，请参阅 [Azure Active Directory B2C 中的令牌概述](tokens-overview.md)。

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip
[sample-repo]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi

<!-- LINKS - Internal -->
[katana]: /aspnet/aspnet/overview/owin-and-katana/
[validissuers]: /dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters.validissuers
[tokenvalidationparameters]: /dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters
