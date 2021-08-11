---
title: 快速入门：向 ASP.NET Core Web 应用添加 Microsoft 标识登录功能 | Azure
titleSuffix: Microsoft identity platform
description: 本快速入门介绍应用如何使用 OpenID Connect 在 ASP.NET Core Web 应用上实现 Microsoft 登录
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/11/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: 56b18d27f3048e0e26737294b861a28f7a2be6e5
ms.sourcegitcommit: 6f21017b63520da0c9d67ca90896b8a84217d3d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114652950"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>快速入门：向 ASP.NET Core Web 应用添加 Microsoft 登录功能

在本快速入门中，你将下载并运行一个代码示例，该示例演示 ASP.NET Core Web 应用如何从任何 Azure Active Directory (Azure AD) 组织中登录用户。  

> [!div renderon="docs"]
> 下图显示了该示例应用的工作原理：
>
> ![示例应用中 Web 浏览器、Web 应用和 Microsoft 标识平台之间的交互关系图。](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.svg)
>
> ## <a name="prerequisites"></a>先决条件
>
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) 或 [Visual Studio Code](https://code.visualstudio.com/)
> * [.NET Core SDK 3.1+](https://dotnet.microsoft.com/download)
>
> ## <a name="register-and-download-the-app"></a>注册和下载应用
> 可以通过两种方法开始生成应用程序：自动或手动配置。
>
> ### <a name="automatic-configuration"></a>自动配置
> 如果要自动配置应用，然后下载代码示例，请执行以下步骤：
>
> 1. 转到 <a href="https://aka.ms/aspnetcore2-1-aad-quickstart-v2/" target="_blank">Azure 门户页面进行应用注册</a>。
> 1. 输入应用程序的名称并选择“注册”。
> 1. 遵循说明下载内容，并一键自动配置新应用程序。
>
> ### <a name="manual-configuration"></a>手动配置
> 如果要手动配置应用程序和代码示例，请执行以下过程。
> #### <a name="step-1-register-your-application"></a>步骤 1：注册应用程序
> 1. 登录 <a href="https://portal.azure.com/" target="_blank">Azure 门户</a>。
> 1. 如果有权访问多个租户，请使用顶部菜单中的“目录 + 订阅”筛选器 :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::，选择要在其中注册应用程序的租户。
> 1. 搜索并选择“Azure Active Directory”  。
> 1. 在“管理”下，选择“应用注册” > “新建注册”  。
> 1. 对于“名称”，请输入应用程序名称。 例如，输入 AspNetCore-Quickstart。 应用的用户会看到此名称，你稍后可对其进行更改。
> 1. 对于“重定向 URI”，请输入 https://localhost:44321/signin-oidc 。
> 1. 选择“注册”  。
> 1. 在“管理”下，选择“身份验证”。 
> 1. 对于“前通道注销 URL”，请输入 https://localhost:44321/signout-oidc 。
> 1. 在“隐式授权和混合流”下，选择“ID 令牌” 。
> 1. 选择“保存”。

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>步骤 1：在 Azure 门户中配置应用程序
> 为使此快速入门中的代码示例正常运行：
> - 对于“重定向 URI”，请输入 https://localhost:44321/ 和 https://localhost:44321/signin-oidc  。
> - 对于“前通道注销 URL”，请输入 https://localhost:44321/signout-oidc 。 
>
> 授权终结点将发出请求 ID 令牌。
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [执行此更改]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![已配置](media/quickstart-v2-aspnet-webapp/green-check.png) 应用程序已使用这些属性进行配置。

#### <a name="step-2-download-the-aspnet-core-project"></a>步骤 2：下载 ASP.NET Core 项目

> [!div renderon="docs"]
> [下载 ASP.NET Core 解决方案](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1.zip)

> [!div renderon="portal" class="sxs-lookup"]
> 运行该项目。

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [下载代码示例](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1.zip)

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>步骤 3：应用已配置并可以运行
> 我们已经为项目配置了应用属性的值，并且该项目已准备好运行。
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`
> [!div renderon="docs"]
> #### <a name="step-3-configure-your-aspnet-core-project"></a>步骤 3：配置 ASP.NET Core 项目
> 1. 将 .zip 存档解压缩到驱动器根附近的本地文件夹中。 例如，解压到 C:\Azure-Samples。
> 
>    建议将存档解压到驱动器根附近的目录中，以避免在 Windows 上出现路径长度限制导致的错误。
> 1. 在 Visual Studio 2019 中打开该解决方案。
> 1. 打开 appsettings.json 文件，并修改以下代码：
>
>    ```json
>    "Domain": "[Enter the domain of your tenant, e.g. contoso.onmicrosoft.com]",
>    "ClientId": "Enter_the_Application_Id_here",
>    "TenantId": "common",
>    ```
>
>    - 将 `Enter_the_Application_Id_here` 替换为在 Azure 门户中注册的应用程序的应用程序（客户端）ID。 可以在应用的“概述”页中找到“应用程序(客户端) ID”值 。
>    - 将 `common` 替换为以下其中一项：
>       - 如果应用程序支持“仅限此组织目录中的帐户”，请将此值替换为目录（租户）ID (GUID) 或租户名称（例如 `contoso.onmicrosoft.com`）。 可以在应用的“概述”页上找到“目录(租户) ID”值 。
>       - 如果应用程序支持“任何组织目录中的帐户”，请将该值替换为 `organizations`。
>       - 如果应用程序支持“所有 Microsoft 帐户用户”，请将该值保留为 `common`。
>
> 在此快速入门中，请不要更改 appsettings.json 文件中的任何其他值。
>
> #### <a name="step-4-build-and-run-the-application"></a>步骤 4：生成并运行应用程序
>
> 通过选择“调试”菜单 >“开始调试”，或按 F5 键在 Visual Studio 中构建和运行应用 。
>
> 系统会提示你输入凭据，然后要求你同意应用所需的权限。 在同意提示上选择“接受”。
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp/webapp-01-consent.png" alt-text="“同意”对话框的屏幕截图，显示应用正在向用户请求的权限。":::
>
> 同意请求的权限后，应用将显示你已使用 Azure Active Directory 凭据成功登录。
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp/webapp-02-signed-in.png" alt-text="Web 浏览器的屏幕截图，显示正在运行的 Web 应用和已登录的用户。":::

## <a name="more-information"></a>详细信息

本部分概述了使用户登录所需的代码。 此概述对于了解代码的工作原理、主要参数是什么，以及如何向现有 ASP.NET Core 应用程序添加登录非常有用。

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="how-the-sample-works"></a>示例工作原理
>
> ![示例应用中 Web 浏览器、Web 应用和 Microsoft 标识平台之间的交互关系图。](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.svg)

### <a name="startup-class"></a>Startup 类

Microsoft.AspNetCore.Authentication 中间件使用托管进程启动时运行的 `Startup` 类：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
        .AddMicrosoftIdentityWebApp(Configuration.GetSection("AzureAd"));

    services.AddControllersWithViews(options =>
    {
        var policy = new AuthorizationPolicyBuilder()
            .RequireAuthenticatedUser()
            .Build();
        options.Filters.Add(new AuthorizeFilter(policy));
    });
   services.AddRazorPages()
        .AddMicrosoftIdentityUI();
}
```

`AddAuthentication()` 方法配置服务以添加基于 cookie 的身份验证。 此身份验证在浏览器方案中使用，并将质询设置为 OpenID 连接。

包含 `.AddMicrosoftIdentityWebApp` 的行可向应用程序添加 Microsoft 标识平台身份验证。 然后对应用程序进行配置，使其根据 appsettings.json 配置文件的 `AzureAD` 部分中的信息登录用户：

| *appsettings.json* 密钥 | 说明                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ClientId`             | Azure 门户中注册的应用程序的“应用程序(客户端) ID”。                                                                                       |
| `Instance`             | 用户进行身份验证时使用的安全令牌服务 (STS) 终结点。 此值通常为 `https://login.microsoftonline.com/`，指示 Azure 公有云。 |
| `TenantId`             | 租户的名称或租户 ID (GUID)，或使用工作帐户或学校帐户或 Microsoft 个人帐户进行用户登录时常用的 `common`。                             |

`Configure()` 方法包含两个重要的方法 `app.UseAuthentication()` 和 `app.UseAuthorization()`，这些方法实现了命名功能。 此外，在 `Configure()` 方法中，必须至少调用一次 `endpoints.MapControllerRoute()` 或 `endpoints.MapControllers()` 来注册 Microsoft 标识 Web 路由：

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
```

### <a name="attribute-for-protecting-a-controller-or-methods"></a>用于保护控制器或方法的属性

可以使用 `[Authorize]` 属性保护控制器或控制器方法。 此属性只允许经过身份验证的用户，从而限制对控制器或方法的访问。 如果用户尚未通过身份验证，可以启动身份验证质询来访问控制器。

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>后续步骤

包含此 ASP.NET Core 教程的 GitHub 存储库包含说明和更多代码示例，这些示例向你展示如何：

- 向新的 ASP.NET Core Web 应用程序添加身份验证。
- 调用 Microsoft Graph、其他 Microsoft API 或你自己的 Web API。
- 添加授权。
- 在国家云中或使用社会标识实现用户登录。

> [!div class="nextstepaction"]
> [GitHub 上的 ASP.NET Core Web 应用教程](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)
