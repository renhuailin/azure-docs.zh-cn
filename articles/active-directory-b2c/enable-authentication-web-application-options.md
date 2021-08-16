---
title: 使用 Azure Active Directory B2C 启用 Web 应用程序选项
description: 通过多种方式启用 Web 应用程序选项。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 05/25/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 3335e035a2d36cc7830d8bc93db82a7d318d26b3
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110481951"
---
# <a name="configure-authentication-in-a-sample-web-application-using-azure-active-directory-b2c-options"></a>使用 Azure Active Directory B2C 选项在示例 Web 应用程序中配置身份验证

本文介绍如何自定义和增强 Web 应用程序的 Azure Active Directory B2C (Azure AD B2C) 身份验证体验。 在开始之前，请先熟悉以下文章：[在示例 Web 应用程序中配置身份验证](configure-authentication-sample-web-app.md)或[在你自己的 Web 应用程序中启用身份验证](enable-authentication-web-application.md)。

## <a name="use-a-custom-domain"></a>使用自定义域

在应用程序的重定向 URL 中使用[自定义域](custom-domain.md)可提供更加优质的无缝用户体验。 从用户角度来看，用户会在登录过程中保留在域中，而不是重定向到 Azure AD B2C 默认域 .b2clogin.com。

若要使用自定义域，请遵循[启用自定义域](custom-domain.md)中的指导。 在项目根文件夹下，打开 `appsettings.json` 文件。 此文件包含有关 Azure AD B2C 标识提供者的信息。 将 `Instance` 条目更新为你的自定义域。

以下 JSON 显示了更改前的应用设置： 

```JSon
"AzureAdB2C": {
  "Instance": "https://contoso.b2clogin.com",
  ...
}
```  

以下 JSON 显示了更改后的应用设置： 

```JSon
"AzureAdB2C": {
  "Instance": "https://login.contoso.com",
  ...
}
``` 

## <a name="use-your-tenant-id"></a>使用你的租户 ID

可以将 URL 中的 B2C 租户名称替换为你的租户 ID GUID，以便删除对 URL 中“b2c”的所有引用。  例如，可以将 `https://account.contosobank.co.uk/contosobank.onmicrosoft.com/` 更改为 `https://account.contosobank.co.uk/<tenant ID GUID>/`

若要使用租户 ID，请遵循[启用自定义域](custom-domain.md#optional-use-tenant-id)中的指导。 在项目根文件夹下，打开 `appsettings.json` 文件。 此文件包含有关 Azure AD B2C 标识提供者的信息。 将 `Domain` 条目更新为你的自定义域。

以下 JSON 演示了更改前的应用设置： 

```JSon
"AzureAdB2C": {
  "Domain": "tenant-name.onmicrosoft.com",
  ...
}
```  

以下 JSON 演示了更改后的应用设置：

```JSon
"AzureAdB2C": {
  "Domain": "00000000-0000-0000-0000-000000000000",
  ...
}
``` 

## <a name="support-advanced-scenarios"></a>支持高级方案

Microsoft 标识平台 API 中的 `AddMicrosoftIdentityWebAppAuthentication` 方法允许开发人员为高级身份验证方案添加代码或订阅 OpenIdConnect 事件。 例如，你可以订阅 OnRedirectToIdentityProvider，它可用于自定义应用发送到 Azure AD B2C 的身份验证请求。

若要支持高级方案，请打开 `Startup.cs`，然后在 `ConfigureServices` 函数中，将 `AddMicrosoftIdentityWebAppAuthentication` 替换为以下代码片段： 

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


## <a name="prepopulate-the-sign-in-name"></a>预填充登录名

在登录用户旅程中，你的应用可以针对特定用户。 当针对用户时，应用程序可以在授权请求中使用用户登录名指定 `login_hint` 查询参数。 Azure AD B2C 自动填充登录名，用户只需提供密码。 

若要预填充登录名，请执行以下步骤：

1. 完成[支持高级方案](#support-advanced-scenarios)过程。
1. 如果使用的是自定义策略，请按照[设置直接登录](direct-signin.md#prepopulate-the-sign-in-name)中的说明添加所需的输入声明。 
1. 将以下代码行添加到 OnRedirectToIdentityProvider 函数：
    
    ```csharp
    private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
    {
      context.ProtocolMessage.LoginHint = "emily@contoso.com";
      
      // More code
      await Task.CompletedTask.ConfigureAwait(false);
    }
    ```

## <a name="redirect-sign-in-to-an-external-identity-provider"></a>将登录重定向到外部标识提供者

如果已将应用程序的登录旅程配置为包括社交帐户（如 Facebook、LinkedIn 或 Google），则可以指定 `domain_hint` 参数。 此查询参数向 Azure AD B2C 提供有关应该用于登录的社交标识提供者的提示。 例如，如果应用程序指定 `domain_hint=facebook.com`，登录流会直接转到 Facebook 登录页。 

若要将登录重定向到外部标识提供者，请执行以下步骤：

1. 完成[支持高级方案](#support-advanced-scenarios)过程。
1. 检查外部标识提供者的域名。 有关详细信息，请参阅[将登录重定向到社交服务提供商](direct-signin.md#redirect-sign-in-to-a-social-provider)。 
1. 在 OnRedirectToIdentityProviderFunc 函数中，将以下代码行添加到 OnRedirectToIdentityProvider 函数：
    
    ```csharp
    private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
    {
      context.ProtocolMessage.DomainHint = "facebook.com";
      
      // More code
      await Task.CompletedTask.ConfigureAwait(false);
    }
    ```

## <a name="specify-the-ui-language"></a>指定 UI 语言

用户流可以使用 Azure AD B2C 中的语言自定义来适应不同的语言，从而满足客户需求。 有关详细信息，请参阅[语言自定义](language-customization.md)。

若要设置首选语言，请执行以下步骤：

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

## <a name="pass-a-custom-query-string-parameter"></a>传递自定义查询字符串参数

你使用可以自定义策略来传递自定义查询字符串参数，例如，当你想要[动态更改页面内容](customize-ui-with-html.md?pivots=b2c-custom-policy#configure-dynamic-custom-page-content-uri)时。


若要传递自定义查询字符串参数，请执行以下步骤：

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

## <a name="pass-id-token-hint"></a>传递 ID 令牌提示

通过 Azure AD B2C，信赖方应用可将入站 JWT 作为 OAuth2 授权请求的一部分发送。 JWT 令牌可以由信赖方应用或标识提供者颁发，并且可以传递有关用户或授权请求的提示。 Azure AD B2C 验证签名、颁发者名称和令牌受众，并从入站令牌中提取声明。

若要在身份验证请求中包含 ID 令牌提示，请执行以下步骤： 

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

如果你想自定义 **登录**、**注册** 或 **注销** 操作，建议你创建自己的控制器。 拥有自己的控制器后，就可以在控制器和身份验证库之间传递参数。 `AccountController` 是 `Microsoft.Identity.Web.UI` NuGet 包的一部分，用于处理登录和注销操作。 你可以在 [Microsoft 标识 Web 库](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.UI/Areas/MicrosoftIdentity/Controllers/AccountController.cs)中找到它的实现。 

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

借助 [ASP.NET Core 中的授权](/aspnet/core/security/authorization/introduction)，你可以使用[基于角色的授权](/aspnet/core/security/authorization/roles)、[基于声明的授权](/aspnet/core/security/authorization/claims)或[基于策略的授权](/aspnet/core/security/authorization/policies)来检查用户是否有权访问受保护的资源。

在 ConfigureServices 方法中添加 AddAuthorization 方法，以添加授权模型。 以下示例将创建一个名为 `EmployeeOnly` 的策略。 该策略检查 `EmployeeNumber` 声明是否存在。 声明的值必须是以下 ID 之一：1、2、3、4 或 5。

```csharp
services.AddAuthorization(options =>
    {
        options.AddPolicy("EmployeeOnly", policy =>
              policy.RequireClaim("EmployeeNumber", "1", "2", "3", "4", "5"));
    });
```

ASP.NET Core 中的授权由 [AuthorizeAttribute](/aspnet/core/security/authorization/simple) 及其各种参数控制。 在其最基本的形式中，通过向控制器、操作或 Razor 页面应用 `[Authorize]` 属性，可限制为仅允许该组件的已验证用户访问。

策略通过具有策略名称的 `[Authorize]` 属性应用到控制器。 以下代码将 `Claims` 操作限制为仅允许由 `EmployeeOnly` 策略授权的用户访问：

```csharp
[Authorize(Policy = "EmployeeOnly")]
public IActionResult Claims()
{
    return View();
}
```

## <a name="next-steps"></a>后续步骤

- 了解详细信息：[ASP.NET Core 中的授权简介](/aspnet/core/security/authorization/introduction)