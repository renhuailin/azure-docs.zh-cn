---
title: 使用 Azure Active Directory B2C 启用 Web 应用验证选项
description: 本文介绍了几种启用 Web 应用验证选项的方法。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/12/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 50cdb5f171614c138427b358f2418b8b81751457
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123220344"
---
# <a name="enable-authentication-options-in-a-web-app-by-using-azure-ad-b2c"></a>使用 Azure AD B2C 在 Web 应用中启用验证选项 

本文介绍如何启用、自定义和增强 Web 应用程序的 Azure Active Directory B2C (Azure AD B2C) 身份验证体验。 

在开始之前，请务必先熟悉以下文章： 
* [在示例 Web 应用中配置身份验证](configure-authentication-sample-web-app.md)
* [在自己的 Web 应用中启用身份验证](enable-authentication-web-application.md)。

[!INCLUDE [active-directory-b2c-app-integration-custom-domain](../../includes/active-directory-b2c-app-integration-custom-domain.md)]

若要在身份验证 URL 中使用自定义域和租户 ID，请按照[启用自定义域](custom-domain.md)中的指南进行操作。 在项目根文件夹下，打开 appsettings.json 文件。 此文件包含有关 Azure AD B2C 标识提供者的信息。

在 appsettings.json 文件中，执行以下操作：

- 将 `Instance` 条目更新为你的自定义域。
- 将 `Domain` 条目更新为你的[租户 ID](tenant-management.md#get-your-tenant-id)。 有关详细信息，请参阅[使用租户 ID](custom-domain.md#optional-use-tenant-id)。

以下 JSON 显示了更改前的应用设置： 

```JSON
"AzureAdB2C": {
  "Instance": "https://contoso.b2clogin.com",
  "Domain": "tenant-name.onmicrosoft.com",
  ...
}
```  

以下 JSON 显示了更改后的应用设置： 

```JSON
"AzureAdB2C": {
  "Instance": "https://login.contoso.com",
  "Domain": "00000000-0000-0000-0000-000000000000",
  ...
}
``` 

## <a name="support-advanced-scenarios"></a>支持高级方案

Microsoft 标识平台 API 中的 `AddMicrosoftIdentityWebAppAuthentication` 方法允许开发人员为高级身份验证方案添加代码或订阅 OpenIdConnect 事件。 例如，你可以订阅 OnRedirectToIdentityProvider，它可用于自定义应用发送到 Azure AD B2C 的身份验证请求。

若要支持高级方案，请打开 Startup.cs 文件，然后在 `ConfigureServices` 函数中，将 `AddMicrosoftIdentityWebAppAuthentication` 替换为以下代码片段： 

```csharp
// Configuration to sign-in users with Azure AD B2C

//services.AddMicrosoftIdentityWebAppAuthentication(Configuration, "AzureAdB2C");

services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
        .AddMicrosoftIdentityWebApp(options =>
{
    Configuration.Bind("AzureAdB2C", options);
    options.Events ??= new OpenIdConnectEvents();
    options.Events.OnRedirectToIdentityProvider += OnRedirectToIdentityProviderFunc;
});
```

前面的代码添加了 OnRedirectToIdentityProvider 事件以及对 `OnRedirectToIdentityProviderFunc` 方法的引用。 将以下代码片段添加到 `Startup.cs` 类。

```csharp
private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
{
    // Custom code here
    
    // Don't remove this line
    await Task.CompletedTask.ConfigureAwait(false);
}
```

可以使用上下文参数在控制器和 `OnRedirectToIdentityProvider` 函数之间传递参数。 

[!INCLUDE [active-directory-b2c-app-integration-login-hint](../../includes/active-directory-b2c-app-integration-login-hint.md)]

1. 如果使用的是自定义策略，请按照[设置直接登录](direct-signin.md#prepopulate-the-sign-in-name)中的说明添加所需的输入声明。 
1. 完成[支持高级方案](#support-advanced-scenarios)过程。
1. 将以下代码行添加到 `OnRedirectToIdentityProvider` 函数：
    
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
1. 在 `OnRedirectToIdentityProviderFunc` 函数中，将以下代码行添加到 `OnRedirectToIdentityProvider` 函数：
    
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
1. 将以下代码行添加到 `OnRedirectToIdentityProvider` 函数：

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
1. 将以下代码行添加到 `OnRedirectToIdentityProvider` 函数：
    
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
1. 将以下代码行添加到 `OnRedirectToIdentityProvider` 函数：
    
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

如果想自定义登录、注册或注销操作，建议创建自己的控制器  。 拥有自己的控制器后，就可以在控制器和身份验证库之间传递参数。 `AccountController` 是 `Microsoft.Identity.Web.UI` NuGet 包的一部分，用于处理登录和注销操作。 你可以在 [Microsoft 标识 Web 库](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.UI/Areas/MicrosoftIdentity/Controllers/AccountController.cs)中找到它的实现。 

### <a name="add-the-account-controller"></a>添加帐户控制器

在 Visual Studio 项目中，右键单击“Controllers”文件夹，然后添加一个新的控制器。 选择“MVC - 空控制器”，然后提供名称“MyAccountController.cs” 。

以下代码片段使用 SignIn 操作演示了自定义 `MyAccountController`。

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
            return Challenge(properties, scheme);
        }

    }
}
```

在 _LoginPartial.cshtml 视图中，将登录链接更改为你的控制器。

```html
<form method="get" asp-area="MicrosoftIdentity" asp-controller="MyAccount" asp-action="SignIn">
```

### <a name="pass-the-azure-ad-b2c-policy-id"></a>传递 Azure AD B2C 策略 ID

以下代码片段使用 SignIn 和 SignUp 操作演示了自定义 `MyAccountController` 。 该操作将名为 `policy` 的参数传递到身份验证库。 这使你能够为特定操作提供正确的 Azure AD B2C 策略 ID。

```csharp
public IActionResult SignIn([FromRoute] string scheme)
{
    scheme ??= OpenIdConnectDefaults.AuthenticationScheme;
    var redirectUrl = Url.Content("~/");
    var properties = new AuthenticationProperties { RedirectUri = redirectUrl };
    properties.Items["policy"] = "B2C_1_SignIn";
    return Challenge(properties, scheme);
}

public IActionResult SignUp([FromRoute] string scheme)
{
    scheme ??= OpenIdConnectDefaults.AuthenticationScheme;
    var redirectUrl = Url.Content("~/");
    var properties = new AuthenticationProperties { RedirectUri = redirectUrl };
    properties.Items["policy"] = "B2C_1_SignUp";
    return Challenge(properties, scheme);
}
```

在 _LoginPartial.cshtml 视图中，将任何其他身份验证链接（例如注册或编辑个人资料）的 `asp-controller` 值更改为 `MyAccountController`。

### <a name="pass-custom-parameters"></a>传递自定义参数

以下代码片段使用 SignIn 操作演示了自定义 `MyAccountController`。 该操作将名为 `campaign_id` 的参数传递到身份验证库。

```csharp
public IActionResult SignIn([FromRoute] string scheme)
{
    scheme ??= OpenIdConnectDefaults.AuthenticationScheme;
    var redirectUrl = Url.Content("~/");
    var properties = new AuthenticationProperties { RedirectUri = redirectUrl };
    properties.Items["policy"] = "B2C_1_SignIn";
    properties.Items["campaign_id"] = "1234";
    return Challenge(properties, scheme);
}
```

完成“[支持高级方案](#support-advanced-scenarios)”过程，然后在 `OnRedirectToIdentityProvider` 方法中读取自定义参数：

```csharp
private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
{
    // Read the custom parameter
    var campaign_id = context.Properties.Items.FirstOrDefault(x => x.Key == "campaign_id").Value;

    // Add your custom code here
    if (campaign_id != null)
    {
        // Send parameter to authentication request
        context.ProtocolMessage.SetParameter("campaign_id", campaign_id);
    }
    
    await Task.CompletedTask.ConfigureAwait(false);
}
```

## <a name="secure-your-logout-redirect"></a>保护注销重定向

注销后，用户将重定向到 `post_logout_redirect_uri` 参数中指定的 URI，而不管为应用程序指定的回复 URL 为何。 但是，如果传递了有效的 `id_token_hint` 并启用了“注销请求中需要 ID 令牌”，则在执行重定向之前，Azure AD B2C 将验证 `post_logout_redirect_uri` 的值是否与应用程序的某个已配置重定向 URI 相匹配。 如果没有为应用程序配置匹配的回复 URL，则会显示一条错误消息，而用户不会重定向。

若要在应用程序中支持安全的注销重定向，请首先按照[帐户控制器](enable-authentication-web-application-options.md#add-the-account-controller)和[支持高级方案](#support-advanced-scenarios)部分中的步骤进行操作。 然后按照以下步骤进行操作：

1. 在 `MyAccountController.cs` 控制器中，使用以下代码段添加 SignOut 操作：

    ```csharp
    [HttpGet("{scheme?}")]
    public async Task<IActionResult> SignOutAsync([FromRoute] string scheme)
    {
        scheme ??= OpenIdConnectDefaults.AuthenticationScheme;

        //obtain the id_token
        var idToken = await HttpContext.GetTokenAsync("id_token");
        //send the id_token value to the authentication middleware
        properties.Items["id_token_hint"] = idToken;            

        return SignOut(properties,CookieAuthenticationDefaults.AuthenticationScheme,scheme);
    }
    ```

1. 在 Startup.cs 类中，解析 `id_token_hint` 值并将该值附加到身份验证请求。 以下代码片段演示如何将 `id_token_hint` 值传递给身份验证请求：

    ```csharp
    private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
    {
        var id_token_hint = context.Properties.Items.FirstOrDefault(x => x.Key == "id_token_hint").Value;
        if (id_token_hint != null)
        {
            // Send parameter to authentication request
            context.ProtocolMessage.SetParameter("id_token_hint", id_token_hint);
        }

        await Task.CompletedTask.ConfigureAwait(false);
    }
    ```

1. 在 `ConfigureServices` 函数中，为控制器添加 `SaveTokens` 选项可以访问 `id_token` 值： 

    ```csharp
    services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
        .AddMicrosoftIdentityWebApp(options =>
        {
            Configuration.Bind("AzureAdB2C", options);
            options.Events ??= new OpenIdConnectEvents();        
            options.Events.OnRedirectToIdentityProvider += OnRedirectToIdentityProviderFunc;
            options.SaveTokens = true;
        });
    ```

1. 在 appsettings.json 配置文件中，将注销重定向 URI 路径添加到 `SignedOutCallbackPath` 密钥。

    ```json
    "AzureAdB2C": {
      "Instance": "https://<your-tenant-name>.b2clogin.com",
      "ClientId": "<web-app-application-id>",
      "Domain": "<your-b2c-domain>",
      "SignedOutCallbackPath": "/signout/<your-sign-up-in-policy>",
      "SignUpSignInPolicyId": "<your-sign-up-in-policy>"
    }
    ```

在以上示例中，传递给注销请求的 post_logout_redirect_uri 的格式为：`https://your-app.com/signout/<your-sign-up-in-policy>`。 必须将此 URL 添加到应用程序注册的答复 URL。

## <a name="role-based-access-control"></a>基于角色的访问控制

借助 [ASP.NET Core 中的授权](/aspnet/core/security/authorization/introduction)，可以使用以下方法之一来检查用户是否有权访问受保护的资源： 
* [基于角色的授权](/aspnet/core/security/authorization/roles) 
* [基于声明的授权](/aspnet/core/security/authorization/claims) 
* [基于策略的授权](/aspnet/core/security/authorization/policies)

在 `ConfigureServices` 方法中添加 `AddAuthorization` 方法，以添加授权模型。 以下示例将创建一个名为 `EmployeeOnly` 的策略。 该策略会检查以验证声明 `EmployeeNumber` 是否存在。 声明的值必须是以下 ID 之一：1、2、3、4 或 5。

```csharp
services.AddAuthorization(options =>
    {
        options.AddPolicy("EmployeeOnly", policy =>
              policy.RequireClaim("EmployeeNumber", "1", "2", "3", "4", "5"));
    });
```

可以使用 [AuthorizeAttribute](/aspnet/core/security/authorization/simple) 及其各种参数控制 ASP.NET Core 中的授权。 在其最基本的形式中，通过向控制器、操作或 Razor 页面应用 `Authorize` 属性，可限制为仅允许该组件的已验证用户访问。

可以使用具有策略名称的 `Authorize` 属性将策略应用到控制器。 以下代码将 `Claims` 操作限制为仅允许由 `EmployeeOnly` 策略授权的用户访问：

```csharp
[Authorize(Policy = "EmployeeOnly")]
public IActionResult Claims()
{
    return View();
}
```

## <a name="next-steps"></a>后续步骤

- 若要详细了解授权，请参阅 [ASP.NET Core 中的授权简介](/aspnet/core/security/authorization/introduction)。
