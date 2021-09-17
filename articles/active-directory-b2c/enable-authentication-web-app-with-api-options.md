---
title: 启用使用 Azure Active Directory B2C 调用 Web API 选项的 Web 应用程序
description: 本文介绍如何通过多种方式来支持使用调用 Web API 选项的 Web 应用程序。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/05/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 7bc5982d6f0ce4631ba1225964efb4ac2bc9fda4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122638125"
---
# <a name="configure-authentication-options-in-a-web-app-that-calls-a-web-api-by-using-azure-ad-b2c"></a>在使用 Azure AD B2C 来调用 Web API 的 Web 应用程序中配置身份验证选项 

本文介绍如何自定义和增强调用 Web API 的 Web 应用程序的 Azure Active Directory B2C (Azure AD B2C) 身份验证体验。 在开始之前，请先熟悉以下文章： 
* [在示例 Web 应用程序中配置身份验证](configure-authentication-sample-web-app-with-api.md)
* [在自己的 Web 应用程序中启用身份验证](enable-authentication-web-app-with-api.md)。

[!INCLUDE [active-directory-b2c-app-integration-custom-domain](../../includes/active-directory-b2c-app-integration-custom-domain.md)] 

若要在身份验证 URL 中使用自定义域和租户 ID，请按照[启用自定义域](custom-domain.md)中的指南操作。 在项目根文件夹下，打开 `appsettings.json` 文件。 此文件包含有关 Azure AD B2C 标识提供者的信息。 

- 将 `Instance` 条目更新为你的自定义域。
- 将 `Domain` 条目更新为你的[租户 ID](tenant-management.md#get-your-tenant-id)。 有关详细信息，请参阅[使用租户 ID](custom-domain.md#optional-use-tenant-id)。

以下 JSON 代码中显示了更改 *前* 的应用设置： 

```json
"AzureAdB2C": {
  "Instance": "https://contoso.b2clogin.com",
  "Domain": "tenant-name.onmicrosoft.com",
  ...
}
```  

以下 JSON 代码中显示了更改 *后* 的应用设置： 

```json
"AzureAdB2C": {
  "Instance": "https://login.contoso.com",
  "Domain": "00000000-0000-0000-0000-000000000000",
  ...
}
``` 

## <a name="support-advanced-scenarios"></a>支持高级方案

Microsoft 标识平台 API 中的 `AddMicrosoftIdentityWebAppAuthentication` 方法允许开发人员为高级身份验证方案添加代码或订阅 OpenIdConnect 事件。 例如，可以订阅 OnRedirectToIdentityProvider，可以借助后者来自定义应用发送到 Azure AD B2C 的身份验证请求。

若要支持高级方案，请打开 Startup.cs 文件，然后在 `ConfigureServices` 函数中，将 `AddMicrosoftIdentityWebAppAuthentication` 替换为以下代码片段： 

```csharp
// Configuration to sign in users with Azure AD B2C

//services.AddMicrosoftIdentityWebAppAuthentication(Configuration, "AzureAdB2C");

services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
        .AddMicrosoftIdentityWebApp(options =>
{
    Configuration.Bind("AzureAdB2C", options);
    options.Events ??= new OpenIdConnectEvents();
    options.Events.OnRedirectToIdentityProvider += OnRedirectToIdentityProviderFunc;
});
```

上面的代码添加了 OnRedirectToIdentityProvider 事件，并引用了 *OnRedirectToIdentityProviderFunc* 方法。 将以下代码片段添加到 `Startup.cs` 类。

```csharp
private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
{
    // Custom code here
    
    // Don't remove this line
    await Task.CompletedTask.ConfigureAwait(false);
}
```

可以使用上下文参数在控制器和 OnRedirectToIdentityProvider 函数之间传递参数。 


[!INCLUDE [active-directory-b2c-app-integration-login-hint](../../includes/active-directory-b2c-app-integration-login-hint.md)]

1. 如果使用的是自定义策略，请按照[设置直接登录](direct-signin.md#prepopulate-the-sign-in-name)中的说明添加所需的输入声明。 

1. 完成[支持高级方案](#support-advanced-scenarios)过程。

1. 将以下代码行添加到 OnRedirectToIdentityProvider 函数：
    
    ```csharp
    private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
    {
      context.ProtocolMessage.LoginHint = "emily@contoso.com";
      
      // More code
      await Task.CompletedTask.ConfigureAwait(false);
    }
    ```

[!INCLUDE [active-directory-b2c-app-integration-domain-hint](../../includes/active-directory-b2c-app-integration-domain-hint.md)]

1. 检查外部标识提供者的域名。 有关详细信息，请参阅[将登录重定向到社交服务提供商](direct-signin.md#redirect-sign-in-to-a-social-provider)。
1. 完成[支持高级方案](#support-advanced-scenarios)过程。 
1. 在 OnRedirectToIdentityProviderFunc 函数中，将以下代码行添加到 OnRedirectToIdentityProvider 函数：
    
    ```csharp
    private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
    {
      context.ProtocolMessage.DomainHint = "facebook.com";
      
      // More code
      await Task.CompletedTask.ConfigureAwait(false);
    }
    ```

[!INCLUDE [active-directory-b2c-app-integration-ui-locales](../../includes/active-directory-b2c-app-integration-ui-locales.md)]

1. [配置语言自定义](language-customization.md)。
1. 完成[支持高级方案](#support-advanced-scenarios)过程。
1. 将以下代码行添加到 OnRedirectToIdentityProvider 函数：

    ```csharp
    private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
    {
      context.ProtocolMessage.UiLocales = "es";

      // More code
      await Task.CompletedTask.ConfigureAwait(false);
    }
    ```

[!INCLUDE [active-directory-b2c-app-integration-custom-parameters](../../includes/active-directory-b2c-app-integration-custom-parameters.md)]

1. 配置 [ContentDefinitionParameters](customize-ui-with-html.md#configure-dynamic-custom-page-content-uri) 元素。
1. 完成[支持高级方案](#support-advanced-scenarios)过程。
1. 将以下代码行添加到 OnRedirectToIdentityProvider 函数：
    
    ```csharp
    private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
    {
      context.ProtocolMessage.Parameters.Add("campaignId", "123");

      // More code
      await Task.CompletedTask.ConfigureAwait(false);
    }
    ```

[!INCLUDE [active-directory-b2c-app-integration-id-token-hint](../../includes/active-directory-b2c-app-integration-id-token-hint.md)]
 
1. 完成[支持高级方案](#support-advanced-scenarios)过程。
1. 在自定义策略中，定义 [ID 令牌提示技术配置文件](id-token-hint.md)。
1. 将以下代码行添加到 OnRedirectToIdentityProvider 函数：
    
    ```csharp
    private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
    {
      // The idTokenHint variable holds your ID token 
      context.ProtocolMessage.IdTokenHint = idTokenHint

      // More code
      await Task.CompletedTask.ConfigureAwait(false);
    }
    ```
    
## <a name="account-controller"></a>帐户控制器

如果你想自定义 *登录*、*注册* 或 *注销* 操作，我们鼓励你创建自己的控制器。 拥有自己的控制器后，就可以在控制器和身份验证库之间传递参数。 `AccountController` 是 `Microsoft.Identity.Web.UI` NuGet 包的一部分，用于处理登录和注销操作。 你可以在 [Microsoft 标识 Web 库](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.UI/Areas/MicrosoftIdentity/Controllers/AccountController.cs)中找到它的实现。 

以下代码片段使用 SignIn 操作演示了自定义 `MyAccountController`。 该操作将名为 `campaign_id` 的参数传递到身份验证库。

```csharp
using System;
using System.Collections.Generic;
using System.Diagnostics;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.OpenIdConnect;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;


namespace mywebapp.Controllers
{
    [AllowAnonymous]
    [Area("MicrosoftIdentity")]
    [Route("[area]/[controller]/[action]")]
    public class MyAccountController : Controller
    {

        [HttpGet("{scheme?}")]
        public IActionResult SignIn([FromRoute] string scheme)
        {
            scheme ??= OpenIdConnectDefaults.AuthenticationScheme;
            var redirectUrl = Url.Content("~/");
            var properties = new AuthenticationProperties { RedirectUri = redirectUrl };
            properties.Items["campaign_id"] = "1234";
            return Challenge(properties, scheme);
        }

    }
}

```

在 `_LoginPartial.cshtml` 视图中，将登录链接更改为你的控制器

```
<form method="get" asp-area="MicrosoftIdentity" asp-controller="MyAccount" asp-action="SignIn">
```

在 `Startup.cs` 调用的 `OnRedirectToIdentityProvider` 中，可以读取自定义参数：

```csharp
private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
{
    // Read the custom parameter
    var campaign_id = (context.Properties.Items.ContainsKey("campaign_id"))
    
    // Add your custom code here
    
    await Task.CompletedTask.ConfigureAwait(false);
}
```

## <a name="role-based-access-control"></a>基于角色的访问控制

借助 [ASP.NET Core 中的授权](/aspnet/core/security/authorization/introduction)，可以使用[基于角色的授权](/aspnet/core/security/authorization/roles)、[基于声明的授权](/aspnet/core/security/authorization/claims)或[基于策略的授权](/aspnet/core/security/authorization/policies)来检查用户是否有权访问受保护的资源。

在 ConfigureServices 方法中添加 AddAuthorization 方法，以添加授权模型。 以下示例将创建一个名为 `EmployeeOnly` 的策略。 该策略会检查声明 `EmployeeNumber` 是否存在。 声明的值必须是以下 ID 之一：1、2、3、4 或 5。

```csharp
services.AddAuthorization(options =>
    {
        options.AddPolicy("EmployeeOnly", policy =>
              policy.RequireClaim("EmployeeNumber", "1", "2", "3", "4", "5"));
    });
```

可以使用 [AuthorizeAttribute](/aspnet/core/security/authorization/simple) 及其各种参数控制 ASP.NET Core 中的授权。 在将 `[Authorize]`属性的最基本形式应用于控制器、操作或 Razor 页面时，可限制为仅允许该组件的已验证用户访问。

可以使用具有策略名称的 `[Authorize]` 属性将策略应用到控制器。 以下代码将 `Claims` 操作限制为仅允许由 `EmployeeOnly` 策略授权的用户访问：

```csharp
[Authorize(Policy = "EmployeeOnly")]
public IActionResult Claims()
{
    return View();
}
```

## <a name="next-steps"></a>后续步骤

若要了解详细信息，请参阅 [ASP.NET Core 中的授权简介](/aspnet/core/security/authorization/introduction)。
