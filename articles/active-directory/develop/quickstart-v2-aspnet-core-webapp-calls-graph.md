---
title: 快速入门：实现用户登录并调用 Microsoft Graph 的 ASP.NET Core Web 应用 | Azure
titleSuffix: Microsoft identity platform
description: 本快速入门介绍应用如何利用 Microsoft.Identity.Web 通过 OpenID Connect 在 ASP.NET Core Web 应用中实现 Microsoft 登录，并调用 Microsoft Graph
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 05/17/2021
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: bc5983ebafe39b7b0595106ec280cc81291163ac
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128573784"
---
# <a name="quickstart-aspnet-core-web-app-that-signs-in-users-and-calls-microsoft-graph-on-their-behalf"></a>快速入门：实现用户登录并代表用户调用 Microsoft Graph 的 ASP.NET Core Web 应用

在本快速入门中，你将下载并运行一个代码示例，该示例演示 ASP.NET Core Web 应用如何从任何 Azure Active Directory (Azure AD) 组织中实现用户登录，并调用 Microsoft Graph。

有关说明，请参阅[示例工作原理](#how-the-sample-works)。

> [!div renderon="docs"]
> ## <a name="prerequisites"></a>先决条件
>
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) 或 [Visual Studio Code](https://code.visualstudio.com/)
> * [.NET Core SDK 3.1+](https://dotnet.microsoft.com/download)
>
>
> ## <a name="step-1-register-your-application"></a>步骤 1：注册应用程序
> 若要手动注册应用程序并将应用的注册信息添加到解决方案，请执行以下步骤：
>
> 1. 登录 <a href="https://portal.azure.com/" target="_blank">Azure 门户</a>。
> 1. 如果有权访问多个租户，请使用顶部菜单中的“目录 + 订阅”筛选器 :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::，以切换到要在其中注册应用程序的租户。
> 1. 搜索并选择“Azure Active Directory”。
> 1. 在“管理”下，选择“应用注册” > “新建注册”  。
> 1. 输入应用程序的名称（例如 `AspNetCoreWebAppCallsGraph-Quickstart`）。 应用的用户可能会看到此名称，你稍后可对其进行更改。
> 1. 输入 `https://localhost:44321/signin-oidc` 的“重定向 URI”。
> 1. 选择“注册”  。
> 1. 在“管理”下，选择“身份验证”。 
> 1. 输入前向通道注销 URL `https://localhost:44321/signout-oidc`。
> 1. 选择“保存”。
> 1. 在“管理”下，选择“证书和机密” > “新建客户端机密”  。
> 1. 输入描述，例如 `clientsecret1`。
> 1. 对于密码到期时间，选择“1 年”。
> 1. 选择“添加”并立即记录密码值，以供后续使用 。 此密码值不会重新显示，也无法通过任何其他方式检索。 将此值记录在安全位置，就像记录任何密码一样。

> [!div class="sxs-lookup" renderon="portal"]
> ## <a name="step-1-configure-your-application-in-the-azure-portal"></a>步骤 1：在 Azure 门户中配置应用程序
>
> 要使本快速入门的示例代码正常运行，请在注册应用时提供重定向 URI `https://localhost:44321/signin-oidc` 和前向通道注销 URL `https://localhost:44321/signout-oidc` 。
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [执行此更改]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![已配置](media/quickstart-v2-aspnet-webapp/green-check.png) 应用程序已使用这些属性进行配置。

## <a name="step-2-download-the-aspnet-core-project"></a>步骤 2：下载 ASP.NET Core 项目

> [!div renderon="docs"]
> [下载 ASP.NET Core 解决方案](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1-callsgraph.zip)

> [!div renderon="portal" class="sxs-lookup"]
> 运行该项目。

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [下载代码示例](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1-callsgraph.zip)

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div class="sxs-lookup" renderon="portal"]
> ## <a name="step-3-your-app-is-configured-and-ready-to-run"></a>步骤 3：应用已配置并可以运行
>
> 我们已经为项目配置了应用属性的值，并且该项目已准备好运行。
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`
> [!div renderon="docs"]
>
> ## <a name="step-3-configure-your-aspnet-core-project"></a>步骤 3：配置 ASP.NET Core 项目
> 1. 将 .zip 存档解压缩到驱动器根附近的本地文件夹中。 例如，解压缩到 C:\Azure-Samples。
> 1. 在 Visual Studio 2019 中打开该解决方案。
> 1. 打开 appsettings.json 文件，并修改以下内容：
>
>    ```json
>    "ClientId": "Enter_the_Application_Id_here",
>    "TenantId": "common",
>    "clientSecret": "Enter_the_Client_Secret_Here"
>    ```
>
>    - 将 `Enter_the_Application_Id_here` 替换为在 Azure 门户中注册的应用程序的“应用程序(客户端) ID”。 可以在应用的“概览”页中找到“应用程序(客户端) ID”。
>    - 将 `common` 替换为以下其中一项：
>       - 如果应用程序支持“仅限此组织目录中的帐户”，请将此值替换为“目录(租户) ID”(GUID) 或“租户名称”（例如 `contoso.onmicrosoft.com`）  。 你可以在应用的”概述”页上找到“目录(租户) ID” 。
>       - 如果应用程序支持“任何组织目录中的帐户”，请将该值替换为`organizations`
>       - 如果应用程序支持“所有 Microsoft 帐户用户”，请将该值保留为 `common`
>    - 将 `Enter_the_Client_Secret_Here` 替换为在先前步骤中创建并记录的客户端机密。
>
> 在此快速入门中，请不要更改 appsettings.json 文件中的任何其他值。
>
> ## <a name="step-4-build-and-run-the-application"></a>步骤 4：生成并运行应用程序
>
> 通过选择“调试”菜单 >“开始调试”，或按 `F5` 键在 Visual Studio 中构建和运行应用 。
>
> 系统会提示你输入凭据，然后要求你同意应用所需的权限。 在同意提示上选择“接受”。
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp-calls-graph/webapp-01-consent.png" alt-text="“同意”对话框，显示应用从 > 用户请求的权限":::
>
> 同意请求的权限后，应用将显示你已使用 Azure Active Directory 凭据成功登录，并且你会在页面的“API 结果”部分中看到你的电子邮件地址。 它是使用 Microsoft Graph 提取的。
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp-calls-graph/webapp-02-signed-in.png" alt-text="Web 浏览器显示正在运行的 web 应用和登录的用户":::

## <a name="about-the-code"></a>关于代码

本部分概述了实现用户登录并代表用户调用 Microsoft Graph API 所需的代码。 阅读本概述对于了解代码的工作原理、主要参数非常有用，如果你想要将登录功能添加到现有 ASP.NET Core 应用程序并调用 Microsoft Graph，阅读本概述也非常有用。 它使用了 [Microsoft.Identity.Web](microsoft-identity-web.md)，这是 [MSAL.NET](msal-overview.md) 的包装器。

### <a name="how-the-sample-works"></a>示例工作原理

![显示本快速入门生成的示例应用的工作原理](media/quickstart-v2-aspnet-core-webapp-calls-graph/aspnetcorewebapp-intro.svg)

### <a name="startup-class"></a>Startup 类

Microsoft.AspNetCore.Authentication 中间件使用主机进程初始化时执行的 `Startup` 类：

```csharp
  // Get the scopes from the configuration (appsettings.json)
  var initialScopes = Configuration.GetValue<string>("DownstreamApi:Scopes")?.Split(' ');

  public void ConfigureServices(IServiceCollection services)
  {
      // Add sign-in with Microsoft
      services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
        .AddMicrosoftIdentityWebApp(Configuration.GetSection("AzureAd"))

            // Add the possibility of acquiring a token to call a protected web API
            .EnableTokenAcquisitionToCallDownstreamApi(initialScopes)

                // Enables controllers and pages to get GraphServiceClient by dependency injection
                // And use an in memory token cache
                .AddMicrosoftGraph(Configuration.GetSection("DownstreamApi"))
                .AddInMemoryTokenCaches();

      services.AddControllersWithViews(options =>
      {
          var policy = new AuthorizationPolicyBuilder()
              .RequireAuthenticatedUser()
              .Build();
          options.Filters.Add(new AuthorizeFilter(policy));
      });

      // Enables a UI and controller for sign in and sign out.
      services.AddRazorPages()
          .AddMicrosoftIdentityUI();
  }
```

方法 `AddAuthentication()` 配置该服务以添加基于 Cookie 的身份验证，此身份验证在浏览器方案中使用，并用于设置 OpenID Connect 质询。

包含 `.AddMicrosoftIdentityWebApp` 的行可向应用程序添加 Microsoft 标识平台身份验证。 这是由 [Microsoft.Identity.Web](microsoft-identity-web.md) 提供的。 然后对其进行配置，使其根据 appsettings.json 配置文件的 `AzureAD` 部分中的信息，使用 Microsoft 标识平台登录：

| *appsettings.json* 密钥 | 说明                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ClientId`             | Azure 门户中注册的应用程序的“应用程序(客户端) ID”。                                                                                       |
| `Instance`             | 用户进行身份验证时使用的安全令牌服务 (STS) 终结点。 此值通常为 `https://login.microsoftonline.com/`，指示 Azure 公有云。 |
| `TenantId`             | 租户的名称或其租户 ID (GUID)，或使用工作帐户或学校帐户或 Microsoft 个人帐户进行用户登录时常用的名称。                             |

应用程序使用 `EnableTokenAcquisitionToCallDownstreamApi` 方法可以获取令牌来调用受保护的 Web API。 控制器或 Razor 页使用 `AddMicrosoftGraph` 可直接（通过依赖项注入）让 `GraphServiceClient` 受益，而应用使用 `AddInMemoryTokenCaches` 方法可受益于令牌缓存。

`Configure()` 方法包含两个重要的方法 `app.UseAuthentication()` 和 `app.UseAuthorization()`，这些方法实现了命名功能。 此外，在 `Configure()` 方法中，必须至少调用一次 `endpoints.MapControllerRoute()` 或调用 `endpoints.MapControllers()`来注册 Microsoft Identity Web 的路由。

```csharp
app.UseAuthentication();
app.UseAuthorization();

app.UseEndpoints(endpoints =>
{

    endpoints.MapControllerRoute(
        name: "default",
        pattern: "{controller=Home}/{action=Index}/{id?}");
    endpoints.MapRazorPages();
});

// endpoints.MapControllers(); // REQUIRED if MapControllerRoute() isn't called.
```

### <a name="protect-a-controller-or-a-controllers-method"></a>保护控制器或控制器的方法

可以通过将 `[Authorize]` 属性应用于控制器的类或其一个或多个方法来保护控制器或其方法。 此 `[Authorize]` 属性只允许通过身份验证的用户，从而限制了访问。 如果用户尚未通过身份验证，可以启动身份验证质询来访问控制器。 本快速入门从配置文件中读取范围：

```csharp
[AuthorizeForScopes(ScopeKeySection = "DownstreamApi:Scopes")]
public async Task<IActionResult> Index()
{
    var user = await _graphServiceClient.Me.Request().GetAsync();
    ViewData["ApiResult"] = user.DisplayName;

    return View();
}
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>后续步骤

包含本快速入门中引用的 ASP.NET Core 代码示例的 GitHub 存储库包含说明和更多代码示例，这些示例向你展示如何：

- 向新的 ASP.NET Core Web 应用程序添加身份验证
- 调用 Microsoft Graph、其他 Microsoft API 或你自己的 Web API
- 添加授权
- 在国家云中或使用社会标识实现用户登录

> [!div class="nextstepaction"]
> [GitHub 上的 ASP.NET Core Web 应用教程](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)
