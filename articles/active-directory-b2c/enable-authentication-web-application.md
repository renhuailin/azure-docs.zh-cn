---
title: 使用 Azure Active Directory B2C 构建基块在 Web 应用中启用身份验证
description: 本文讨论如何在 ASP.NET Web 应用中使用 Azure Active Directory B2C 的生成块来执行用户登录和注册。
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
ms.openlocfilehash: 0fc28097220918f8d9cde8f44c156a5de82a5e50
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2021
ms.locfileid: "123469655"
---
# <a name="enable-authentication-in-your-own-web-app-by-using-azure-ad-b2c"></a>使用 Azure AD B2C 在自己的 Web 应用中启用身份验证

本文演示如何将 Azure Active Directory B2C (Azure AD B2C) 身份验证添加到你自己的 ASP.NET Web 应用程序。 了解如何通过使用 [OpenID Connect](openid-connect.md) 协议的 ASP.NET Core 中间件创建 ASP.NET Core Web 应用程序。 

将本文与[在示例 Web 应用中配置身份验证](configure-authentication-sample-web-app.md)结合使用，用你自己的 Web 应用来替换示例 Web 应用。

## <a name="prerequisites"></a>先决条件

要查看先决条件和集成说明，请参阅[在示例 Web 应用程序中配置身份验证](configure-authentication-sample-web-app.md)。

## <a name="step-1-create-a-web-app-project"></a>步骤 1：创建 Web 应用项目

可以使用现有 ASP.NET 模型视图控制器 (MVC) Web 应用项目或创建新项目。 若要创建新项目，请打开命令行界面，然后输入以下命令：

```dotnetcli
dotnet new mvc -o mywebapp
```

上述命令执行以下操作：

* 创建新 MVC Web 应用。  
* `-o mywebapp` 参数使用应用的源文件创建名为 mywebapp 的目录。

## <a name="step-2-add-the-authentication-libraries"></a>步骤 2：添加身份验证库

添加 Microsoft Identity Web 库，这是一组 ASP.NET Core 库，简化了为 Web 应用添加 Azure AD B2C 身份验证和授权支持的过程。 Microsoft 标识 Web 库使用基于 Cookie 的身份验证设置身份验证管道。 它负责发送和接收 HTTP 身份验证消息、令牌验证、声明提取等。

若要添加 Microsoft 标识 Web 库，请运行以下命令来安装包： 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```dotnetcli
dotnet add package Microsoft.Identity.Web
dotnet add package Microsoft.Identity.Web.UI
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
Install-Package Microsoft.Identity.Web
Install-Package Microsoft.Identity.Web.UI 
```

---


## <a name="step-3-initiate-the-authentication-libraries"></a>步骤 3：启动身份验证库

Microsoft 标识 Web 中间件使用在托管进程启动时运行的启动类。 在此步骤中，添加启动身份验证库所需的代码。

打开 Startup.cs，然后在类的开头添加以下 `using` 声明：

```csharp
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Authentication.OpenIdConnect;
using Microsoft.Identity.Web;
using Microsoft.Identity.Web.UI;
```

由于 Microsoft 标识 Web 使用基于 Cookie 的身份验证来保护 Web 应用，因此以下代码将设置 SameSite Cookie 设置。 然后，它读取 `AzureAdB2C` 应用程序设置，并启动中间件控制器及其视图。 

将 `ConfigureServices(IServiceCollection services)` 函数替换为以下代码片段： 

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<CookiePolicyOptions>(options =>
    {
        // This lambda determines whether user consent for non-essential cookies is needed for a given request.
        options.CheckConsentNeeded = context => true;
        options.MinimumSameSitePolicy = SameSiteMode.Unspecified;
        // Handling SameSite cookie according to https://docs.microsoft.com/en-us/aspnet/core/security/samesite?view=aspnetcore-3.1
        options.HandleSameSiteCookieCompatibility();
    });

    // Configuration to sign-in users with Azure AD B2C
    services.AddMicrosoftIdentityWebAppAuthentication(Configuration, "AzureAdB2C");

    services.AddControllersWithViews()
        .AddMicrosoftIdentityUI();

    services.AddRazorPages();

    //Configuring appsettings section AzureAdB2C, into IOptions
    services.AddOptions();
    services.Configure<OpenIdConnectOptions>(Configuration.GetSection("AzureAdB2C"));
}
```

以下代码添加 Cookie 策略，并使用身份验证模型。 将 `Configure` 函数替换为以下代码片段： 

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
        // The default HSTS value is 30 days. You may want to change this for production scenarios, see https://aka.ms/aspnetcore-hsts.
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();

    // Add the Microsoft Identity Web cookie policy
    app.UseCookiePolicy();
    app.UseRouting();
    // Add the ASP.NET Core authentication service
    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllerRoute(
            name: "default",
            pattern: "{controller=Home}/{action=Index}/{id?}");
        
        // Add endpoints for Razor pages
        endpoints.MapRazorPages();
    });
};
```

## <a name="step-4-add-the-ui-elements"></a>步骤 4：添加 UI 元素

若要添加用户界面元素，请使用分部视图，其中包含用于检查用户是否已登录的逻辑。 如果用户未登录，分部视图将呈现登录按钮。 如果用户已登录，则会显示用户的显示名称和注销按钮。
  
使用以下代码段在 /Views/Shared 文件夹中创建新文件 \_LoginPartial.cshtml ：

```razor
@using System.Security.Principal
@if (User.Identity.IsAuthenticated)
{
    <ul class="nav navbar-nav navbar-right">
        <li class="navbar-text">Hello @User.Identity.Name</li>
        <!-- The Account controller is not defined in this project. Instead, it is part of Microsoft.Identity.Web.UI nuget package and
            it defines some well known actions such as SignUp/In, SignOut and EditProfile-->
        <li class="navbar-btn">
            <form method="get" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="EditProfile">
                <button type="submit" class="btn btn-primary" style="margin-right:5px">Edit Profile</button>
            </form>
        </li>
        <li class="navbar-btn">
            <form method="get" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignOut">
                <button type="submit" class="btn btn-primary">Sign Out</button>
            </form>
        </li>
    </ul>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li class="navbar-btn">
            <form method="get" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignIn">
                <button type="submit" class="btn btn-primary">Sign Up/In</button>
            </form>
        </li>
    </ul>
}
```

修改 /Views/Shared_Layout.cshtml 文件以包含你添加的 _LoginPartial.cshtml 文件 。 _Layout.cshtml 文件是一个通用布局，可为用户提供一致的页面浏览体验。 该布局包括常见的用户界面元素，如应用的页眉和页脚。

> [!NOTE]
> 根据你运行的 .NET Core 版本以及是否要将登录添加到现有应用，UI 元素的外观可能会有所不同。 如果是这样，请确保在页面布局的适当位置包含 _LoginPartial。

打开 /Views/Shared/_Layout.cshtml 文件，然后添加以下 `div` 元素。

```razor
<div class="navbar-collapse collapse">
...
</div>
```

将此元素替换为以下 Razor 代码：

```razor
<div class="navbar-collapse collapse">
  <ul class="nav navbar-nav">
    <li><a asp-area="" asp-controller="Home" asp-action="Index">Home</a></li>
    <li><a asp-area="" asp-controller="Home" asp-action="Claims">Claims</a></li>
  </ul>
  <partial name="_LoginPartial" />
</div>
```

前面的 Razor 代码包含指向将在后续步骤中创建的 `Claims` 操作。

## <a name="step-5-add-the-claims-view"></a>步骤 5：添加声明视图

要查看 ID 令牌声明，请在 /Views/Home 文件夹下添加 Claims.cshtml 视图 。

```razor
@using System.Security.Claims

@{
  ViewData["Title"] = "Claims";
}
<h2>@ViewData["Title"].</h2>

<table class="table-hover table-condensed table-striped">
  <tr>
    <th>Claim Type</th>
    <th>Claim Value</th>
  </tr>

  @foreach (Claim claim in User.Claims)
  {
    <tr>
      <td>@claim.Type</td>
      <td>@claim.Value</td>
    </tr>
  }
</table>
```

在此步骤中，添加 `Claims` 操作，将 Claims.cshtml 视图链接到主控制器。 `Claims` 操作使用 `Authorize` 属性，该属性将对该操作的访问权限限制为经过身份验证的用户。  

在 /Controllers/HomeController.cs 中添加以下操作：

```csharp
[Authorize]
public IActionResult Claims()
{
    return View();
}
```

在类的开头，添加以下 `using` 声明：

```csharp
using Microsoft.AspNetCore.Authorization;
```

## <a name="step-6-add-the-app-settings"></a>步骤 6：添加应用设置

Azure AD B2C 标识提供者设置存储在 appsettings.json 文件中。 打开 appsettings.json，然后添加以下设置：

```JSon
"AzureAdB2C": {
  "Instance": "https://<your-tenant-name>.b2clogin.com",
  "ClientId": "<web-app-application-id>",
  "Domain": "<your-b2c-domain>",
  "SignedOutCallbackPath": "/signout/<your-sign-up-in-policy>",
  "SignUpSignInPolicyId": "<your-sign-up-in-policy>"
}
```

[在示例 Web 应用中配置身份验证](configure-authentication-sample-web-app.md)一文介绍了所需信息。 使用以下设置：

* **实例**：将 `<your-tenant-name>` 替换为 Azure AD B2C [租户名称](tenant-management.md#get-your-tenant-name)的第一部分（例如 `https://contoso.b2clogin.com`）。
* **域**：将 `<your-b2c-domain>` 替换为 Azure AD B2C 完整[租户名称](tenant-management.md#get-your-tenant-name)（例如 `contoso.onmicrosoft.com`）。
* **客户端 ID**：将 `<web-app-application-id>` 替换为[步骤 2](configure-authentication-sample-web-app.md#step-2-register-a-web-application) 中的应用程序 ID。
* **策略名称**：将 `<your-sign-up-in-policy>` 替换为在[步骤 1](configure-authentication-sample-web-app.md#step-1-configure-your-user-flow) 中创建的用户流。

## <a name="step-7-run-your-application"></a>步骤 7：运行应用程序

1. 生成并运行该项目。
1. 转到  `https://localhost:5001` 。 
1. 选择“注册/登录”。
1. 完成注册或登录过程。

成功进行身份验证后，导航栏中会显示显示名称。 若要查看 Azure AD B2C 令牌返回到应用的声明，请选择“声明”。

## <a name="next-steps"></a>后续步骤
* 了解如何[自定义和增强 Web 应用的 Azure AD B2C 身份验证体验](enable-authentication-web-application-options.md).