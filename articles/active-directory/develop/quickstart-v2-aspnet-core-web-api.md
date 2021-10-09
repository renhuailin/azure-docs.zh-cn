---
title: 快速入门：使用 Microsoft 标识平台保护 ASP.NET Core Web API | Azure
titleSuffix: Microsoft identity platform
description: 在本快速入门中，你将下载并修改一个代码示例，该示例演示如何通过使用 Microsoft 标识平台进行授权来保护 ASP.NET Core Web API。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/22/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: f70c35f55256ef98dd261a13058f1b6ad5330d9f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128616795"
---
# <a name="quickstart-protect-an-aspnet-core-web-api-with-the-microsoft-identity-platform"></a>快速入门：使用 Microsoft 标识平台保护 ASP.NET Core Web API

在本快速入门中，我们将下载一个 ASP.NET Core Web API 代码示例并查看其对资源的访问权限限制为仅授权帐户的方式。 该示例支持对任何 Azure Active Directory (Azure AD) 组织中的个人 Microsoft 帐户和帐户进行授权。

> [!div renderon="docs"]
> ## <a name="prerequisites"></a>先决条件
>
> - 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
> - [Azure Active Directory 租户](quickstart-create-new-tenant.md)
> - [.NET Core SDK 3.1+](https://dotnet.microsoft.com/)
> - [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) 或 [Visual Studio Code](https://code.visualstudio.com/)
>
> ## <a name="step-1-register-the-application"></a>步骤 1：注册应用程序
>
> 首先，在 Azure AD 租户中注册 Web API，并通过执行以下步骤来添加范围：
>
> 1. 登录 <a href="https://portal.azure.com/" target="_blank">Azure 门户</a>。
> 1. 如果有权访问多个租户，请使用顶部菜单中的“目录 + 订阅”筛选器 :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::，以切换到要在其中注册应用程序的租户。
> 1. 搜索并选择“Azure Active Directory”。
> 1. 在“管理”下，选择“应用注册” > “新建注册”  。
> 1. 对于“名称”，请输入应用程序名称。 例如，输入 **AspNetCoreWebApi-Quickstart**。 应用的用户会看到此名称，你稍后可对其进行更改。
> 1. 选择“注册”  。
> 1. 在“管理”下，选择“公开 API” > “添加范围”  。 针对 **应用程序 ID URI**，通过选择 **保存并继续** 来接受默认，然后输入以下信息：
>    - **范围名称**：`access_as_user`
>    - **谁能同意？** ：管理员和用户
>    - **管理员许可显示名称**：`Access AspNetCoreWebApi-Quickstart`
>    - **管理员许可说明**：`Allows the app to access AspNetCoreWebApi-Quickstart as the signed-in user.`
>    - **用户同意显示名称**：`Access AspNetCoreWebApi-Quickstart`
>    - **用户同意说明**：`Allow the application to access AspNetCoreWebApi-Quickstart on your behalf.`
>    - **状态**：**已启用**
> 1. 选择“添加范围”以完成范围添加。

## <a name="step-2-download-the-aspnet-core-project"></a>步骤 2：下载 ASP.NET Core 项目

> [!div renderon="docs"]
> 从 GitHub [下载 ASP.NET Core 解决方案](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/archive/aspnetcore3-1.zip)。

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div renderon="docs"]
> ## <a name="step-3-configure-the-aspnet-core-project"></a>步骤 3：配置 ASP.NET Core 项目
>
> 在此步骤中，将示例代码配置为使用之前创建的应用注册。
>
> 1. 将 .zip 存档提取到驱动器根附近的文件夹中。 例如，解压到 C:\Azure-Samples。
>
>    建议将存档解压到驱动器根附近的目录中，以避免在 Windows 上出现路径长度限制导致的错误。
>
> 1. 在代码编辑器的 webapi 文件夹中打开解决方案。
> 1. 打开 appsettings.json 文件，并修改以下代码：
>
>    ```json
>    "ClientId": "Enter_the_Application_Id_here",
>    "TenantId": "Enter_the_Tenant_Info_Here"
>    ```
>
>    - 将 `Enter_the_Application_Id_here` 替换为在 Azure 门户中注册的应用程序的应用程序（客户端）ID。 可以在应用的“概述”页上找到应用程序（客户端）ID。
>    - 将 `Enter_the_Tenant_Info_Here` 替换为以下其中一项：
>       - 如果应用程序支持“仅限此组织目录中的帐户”，请将此值替换为目录（租户）ID（GUID）或租户名称（例如，`contoso.onmicrosoft.com`）。 你可以在应用的“概述”页上找到目录（租户）ID。
>       - 如果应用程序支持“任何组织目录中的帐户”，请将该值替换为 `organizations`。
>       - 如果应用程序支持“所有 Microsoft 帐户用户”，请将该值保留为 `common`。
>
> 在此快速入门中，请不要更改 appsettings.json 文件中的任何其他值。

## <a name="how-the-sample-works"></a>示例工作原理

Web API 从客户端应用程序接收令牌，Web API 中的代码验证该令牌。 [方案：受保护的 Web API](scenario-protected-web-api-overview.md) 中详细说明了此方案。

### <a name="startup-class"></a>Startup 类

*Microsoft.AspNetCore.Authentication* 中间件使用托管进程启动时执行的 `Startup` 类。 在其 `ConfigureServices` 方法中，调用了 Microsoft.Identity.Web 提供的 `AddMicrosoftIdentityWebApi` 扩展方法。

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
                .AddMicrosoftIdentityWebApi(Configuration, "AzureAd");
    }
```

`AddAuthentication()` 方法配置服务以添加基于 JwtBearer 的身份验证。

包含 `.AddMicrosoftIdentityWebApi` 的行会向 Web API 添加 Microsoft 标识平台授权。 然后对其进行配置，使其根据 appsettings.json 配置文件的 `AzureAD` 部分中的信息来验证 Microsoft 标识平台颁发的访问令牌：

| appsettings.json 密钥 | 说明                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ClientId`             | Azure 门户中注册的应用程序的“应用程序(客户端) ID”。                                                                                       |
| `Instance`             | 用户进行身份验证时使用的安全令牌服务 (STS) 终结点。 此值通常为 `https://login.microsoftonline.com/`，指示 Azure 公有云。 |
| `TenantId`             | 租户的名称或其租户 ID (GUID)，或使用工作帐户或学校帐户或 Microsoft 个人帐户进行用户登录时常用的名称。                             |

`Configure()` 方法包含两个重要的方法 `app.UseAuthentication()` 和 `app.UseAuthorization()`，这些方法实现了命名功能：

```csharp
// The runtime calls this method. Use this method to configure the HTTP request pipeline.
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // more code
    app.UseAuthentication();
    app.UseAuthorization();
    // more code
}
```

### <a name="protecting-a-controller-a-controllers-method-or-a-razor-page"></a>保护控制器、控制器的方法或 Razor 页

可以使用 `[Authorize]` 属性保护控制器或控制器方法。 此属性只允许经过身份验证的用户，从而限制对控制器或方法的访问。 如果用户尚未通过身份验证，可以启动身份验证质询来访问控制器。

```csharp
namespace webapi.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
```

### <a name="validation-of-scope-in-the-controller"></a>验证控制器中的范围

API 中的代码通过使用 `HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);` 来验证令牌中是否涵盖了所需范围：

```csharp
namespace webapi.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
    {
        // The web API will only accept tokens 1) for users, and 2) having the "access_as_user" scope for this API
        static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

        [HttpGet]
        public IEnumerable<WeatherForecast> Get()
        {
            HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);

            // some code here
        }
    }
}
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>后续步骤

包含此 ASP.NET Core Web API 代码示例的 GitHub 存储库包含说明和更多代码示例，这些示例向你展示如何：

- 向新的 ASP.NET Core Web API 添加身份验证。
- 从桌面应用程序调用 Web API。
- 调用下游 API，如 Microsoft Graph 和其他 Microsoft API。

> [!div class="nextstepaction"]
> [GitHub 上的 ASP.NET Core Web API 教程](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2)
