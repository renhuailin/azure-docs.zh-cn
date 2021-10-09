---
title: 快速入门：用于登录用户的 ASP.NET Web 应用
titleSuffix: Microsoft identity platform
description: 下载并运行一个代码示例，该示例演示了 ASP.NET Web 应用如何让 Azure AD 用户登录。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/25/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET, contperf-fy21q1
ms.openlocfilehash: 0c648ae229db1ebe6ae50131c23e292c616b2820
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128633481"
---
# <a name="quickstart-aspnet-web-app-that-signs-in-azure-ad-users"></a>快速入门：用于让 Azure AD 用户登录的 ASP.NET Web 应用

在本快速入门中，你将下载并运行一个代码示例，该示例演示 ASP.NET Web 应用程序如何使用 Azure Active Directory (Azure AD) 帐户让用户登录。

> [!div renderon="docs"]
> 下图显示了该示例应用的工作原理：
>
> ![示例应用中 Web 浏览器、Web 应用和 Microsoft 标识平台之间的交互关系图。](media/quickstart-v2-aspnet-webapp/aspnetwebapp-intro.svg)
>
> ## <a name="prerequisites"></a>先决条件
>
> * 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)
> * [.NET Framework 4.7.2+](https://dotnet.microsoft.com/download/visual-studio-sdks)
>
> ## <a name="register-and-download-the-app"></a>注册和下载应用
> 可以通过两种方法开始生成应用程序：自动或手动配置。
>
> ### <a name="automatic-configuration"></a>自动配置
> 如果要自动配置应用，然后下载代码示例，请执行以下步骤：
>
> 1. 转到 <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs" target="_blank">Azure 门户页面进行应用注册</a>。
> 1. 输入应用程序的名称并选择“注册”。
> 1. 遵循说明下载内容，并一键自动配置新应用程序。
>
> ### <a name="manual-configuration"></a>手动配置
> 如果要手动配置应用程序和代码示例，请执行以下过程。
>
> #### <a name="step-1-register-your-application"></a>步骤 1：注册应用程序
>
> 1. 登录 <a href="https://portal.azure.com/" target="_blank">Azure 门户</a>。
> 1. 如果有权访问多个租户，请使用顶部菜单中的“目录 + 订阅”筛选器:::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::，以切换到要在其中注册应用程序的租户。
> 1. 搜索并选择“Azure Active Directory”。
> 1. 在“管理”下，选择“应用注册” > “新建注册”  。
> 1. 对于“名称”，请输入应用程序名称。 例如，输入 ASPNET-Quickstart。 应用的用户会看到此名称，你稍后可对其进行更改。
> 1. 在“重定向 URI”中添加 https://localhost:44368/ ，然后选择“注册”  。
> 1. 在“管理”下，选择“身份验证”。 
> 1. 在“隐式授权和混合流”部分，选择“ID 令牌” 。
> 1. 选择“保存”。

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>步骤 1：在 Azure 门户中配置应用程序
> 为使此快速入门中的代码示例正常运行，请为“重定向 URI”输入 https://localhost:44368/ 。
>
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [执行此更改]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![已配置](media/quickstart-v2-aspnet-webapp/green-check.png)应用程序已使用此属性进行配置。

#### <a name="step-2-download-the-project"></a>步骤 2：下载项目

> [!div renderon="docs"]
> [下载 Visual Studio 2019 解决方案](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip)

> [!div renderon="portal" class="sxs-lookup"]
> 使用 Visual Studio 2019 运行项目。
> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [下载代码示例](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip)

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>步骤 3：应用已配置并可以运行
> 我们已经为项目配置了应用属性的值。

> [!div renderon="docs"]
> #### <a name="step-3-run-your-visual-studio-project"></a>步骤 3：运行 Visual Studio 项目

1. 将 .zip 文件解压缩到根文件夹附近的本地文件夹中。 例如，解压到 C:\Azure-Samples。
   
   建议将存档解压到驱动器根附近的目录中，以避免在 Windows 上出现路径长度限制导致的错误。
2. 在 Visual Studio 中打开解决方案 (AppModelv2-WebApp-OpenIDConnect-DotNet.sln)。
3. 可能需要右键单击项目“AppModelv2-WebApp-OpenIDConnect-DotNet”，然后选择“还原 NuGet 包”，具体取决于 Visual Studio 的版本 。
4. 通过选择“查看” > “其他 Windows” > “包管理器控制台”，打开包管理器控制台  。 然后运行 `Update-Package Microsoft.CodeDom.Providers.DotNetCompilerPlatform -r`。

> [!div renderon="docs"]
> 5. 编辑“Web.config”，将参数 `ClientId`、`Tenant` 和 `redirectUri` 替换为：
>    ```xml
>    <add key="ClientId" value="Enter_the_Application_Id_here" />
>    <add key="Tenant" value="Enter_the_Tenant_Info_Here" />
>    <add key="redirectUri" value="https://localhost:44368/" />
>    ```
>    在该代码中：
>
>    - `Enter_the_Application_Id_here` 是先前创建的应用注册的应用程序（客户端）ID。 在 Azure 门户的“应用注册”中的应用“概述”页上找到应用程序（客户端）ID 。
>    - `Enter_the_Tenant_Info_Here` 是以下选项之一：
>      - 如果应用程序支持“仅我的组织”，请将该值替换为目录（租户）ID 或租户名称（例如 `contoso.onmicrosoft.com`）。 在 Azure 门户的“应用注册”中的应用“概述”页上找到目录（租户）ID 。
>      - 如果应用程序支持“任何组织目录中的帐户”，请将该值替换为 `organizations`。
>      - 如果应用程序支持“所有 Microsoft 帐户用户”，请将该值替换为 `common`。
>    - `redirectUri` 是你先前在 Azure 门户的“应用注册”中输入的“重定向 URI” 。
>

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

## <a name="more-information"></a>详细信息

本部分概述了使用户登录所需的代码。 此概述对于了解代码的工作原理、主要参数是什么，以及如何向现有 ASP.NET 应用程序添加登录非常有用。

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="how-the-sample-works"></a>示例工作原理
>
> ![示例应用中 Web 浏览器、Web 应用和 Microsoft 标识平台之间的交互关系图。](media/quickstart-v2-aspnet-webapp/aspnetwebapp-intro.svg)

### <a name="owin-middleware-nuget-packages"></a>OWIN 中间件 NuGet 包

可以将 ASP.NET 中的 OpenID Connect 与 OWIN 中间件包配合使用，通过基于 Cookie 的身份验证设置身份验证管道。 可在 Visual Studio 的包管理器控制台中运行以下命令，以便安装这些包：

```powershell
Install-Package Microsoft.Owin.Security.OpenIdConnect
Install-Package Microsoft.Owin.Security.Cookies
Install-Package Microsoft.Owin.Host.SystemWeb
```

### <a name="owin-startup-class"></a>OWIN 启动类

OWIN 中间件使用在托管进程初始化时运行的“启动类”。 在本快速入门中，startup.cs 文件位于根文件夹下。 以下代码显示本快速入门使用的参数：

```csharp
public void Configuration(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());
    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {
            // Sets the client ID, authority, and redirect URI as obtained from Web.config
            ClientId = clientId,
            Authority = authority,
            RedirectUri = redirectUri,
            // PostLogoutRedirectUri is the page that users will be redirected to after sign-out. In this case, it's using the home page
            PostLogoutRedirectUri = redirectUri,
            Scope = OpenIdConnectScope.OpenIdProfile,
            // ResponseType is set to request the code id_token, which contains basic information about the signed-in user
            ResponseType = OpenIdConnectResponseType.CodeIdToken,
            // ValidateIssuer set to false to allow personal and work accounts from any organization to sign in to your application
            // To only allow users from a single organization, set ValidateIssuer to true and the 'tenant' setting in Web.config to the tenant name
            // To allow users from only a list of specific organizations, set ValidateIssuer to true and use the ValidIssuers parameter
            TokenValidationParameters = new TokenValidationParameters()
            {
                ValidateIssuer = false // Simplification (see note below)
            },
            // OpenIdConnectAuthenticationNotifications configures OWIN to send notification of failed authentications to the OnAuthenticationFailed method
            Notifications = new OpenIdConnectAuthenticationNotifications
            {
                AuthenticationFailed = OnAuthenticationFailed
            }
        }
    );
}
```

> |Where  | 说明 |
> |---------|---------|
> | `ClientId`     | Azure 门户中注册的应用程序的应用程序 ID。 |
> | `Authority`    | 用户进行身份验证时使用的安全令牌服务 (STS) 终结点。 对于公共云，通常为 `https://login.microsoftonline.com/{tenant}/v2.0`。 在该 URL 中，{tenant} 是租户名称、租户 ID，或 `common` 是对常用终结点的引用。 （常用终结点用于多租户应用程序。） |
> | `RedirectUri`  | 一个 URL，在通过 Microsoft 标识平台进行身份验证之后，会将用户发送到此 URL。 |
> | `PostLogoutRedirectUri`     | 一个 URL，在注销以后，会将用户发送到此 URL。 |
> | `Scope`     | 请求的作用域的列表，使用空格进行分隔。 |
> | `ResponseType`     | 来自身份验证响应的请求包含授权代码和 ID 令牌。 |
> | `TokenValidationParameters`     | 用于令牌验证的参数列表。 在本示例中，`ValidateIssuer` 设置为 `false`，指示它可以接受来自任何个人、工作或学校帐户类型的登录。 |
> | `Notifications`     | 可以在 `OpenIdConnect` 消息上运行的委托的列表。 |


> [!NOTE]
> 在本快速入门中，设置 `ValidateIssuer = false` 是一种简化操作。 在实际应用程序中验证颁发者。 查看示例，了解如何执行该操作。

### <a name="authentication-challenge"></a>身份验证质询

可以通过在控制器中请求身份验证质询，强制用户登录：

```csharp
public void SignIn()
{
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties{ RedirectUri = "/" },
            OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

> [!TIP]
> 使用此方法请求身份验证质询是可选的。 如果希望已经身份验证和未经身份验证用户都可以访问视图，通常会使用此方法。 或者，可以使用下一节中所述的方法来保护控制器。

### <a name="attribute-for-protecting-a-controller-or-a-controller-actions"></a>用于保护控制器或控制器操作的属性

可以使用 `[Authorize]` 属性保护控制器或控制器操作。 此属性通过仅允许经过身份验证的用户访问控制器中的操作来限制对控制器或操作的访问。 当未经身份验证的用户尝试访问由 `[Authorize]` 属性修饰的某个操作或控制器时，将自动发生身份验证质询。

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>后续步骤

试用 ASP.NET 教程，了解有关构建应用程序和新功能的完整分步指南，包括本快速入门的完整说明。

> [!div class="nextstepaction"]
> [向 ASP.NET Web 应用添加的登录功能](tutorial-v2-asp-webapp.md)
