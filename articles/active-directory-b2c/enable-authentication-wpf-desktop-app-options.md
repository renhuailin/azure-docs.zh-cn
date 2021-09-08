---
title: 使用 Azure Active Directory B2C 启用 WPF 桌面应用程序选项
description: 通过多种方式启用 WPF 桌面应用程序选项。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/04/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: bbb0b9d29b2551a6c733d0df8b62505efe68514f
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123220258"
---
# <a name="enable-authentication-options-in-a-wpf-desktop-app-by-using-azure-ad-b2c"></a>使用 Azure AD B2C 在 WPF 桌面应用中启用验证选项 

本文介绍如何自定义和增强 Windows Presentation Foundation (WPF) 桌面应用程序的 Azure Active Directory B2C (Azure AD B2C) 身份验证体验。 

在开始之前，请先熟悉[使用 Azure AD B2C 在示例 WPF 桌面应用中配置身份验证](configure-authentication-sample-wpf-desktop-app.md)一文。


[!INCLUDE [active-directory-b2c-app-integration-login-hint](../../includes/active-directory-b2c-app-integration-login-hint.md)]

1. 如果使用的是自定义策略，请按照[设置直接登录](direct-signin.md#prepopulate-the-sign-in-name)中的说明添加所需的输入声明。 
1. 查找 Microsoft 身份验证库 (MSAL) 配置对象，然后添加带有登录提示的 `withLoginHint()` 方法。

```csharp
authResult = await app.AcquireTokenInteractive(App.ApiScopes)
    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
    .WithLoginHint("bob@contoso.com")
    .ExecuteAsync();
```

[!INCLUDE [active-directory-b2c-app-integration-domain-hint](../../includes/active-directory-b2c-app-integration-domain-hint.md)]

1. 检查外部标识提供者的域名。 有关详细信息，请参阅[将登录重定向到社交服务提供商](direct-signin.md#redirect-sign-in-to-a-social-provider)。 
1. 创建或使用现有的 `Dictionary` 对象来存储额外的查询参数。
1. 将具有相应域名的 `domain_hint` 参数添加到字典中（如 `facebook.com`）。
1. 将额外的查询参数对象传递到 MSAL 配置对象的 `WithExtraQueryParameters` 方法中。

```csharp
Dictionary<string, string> extraQueryParameters = new Dictionary<string, string>();
extraQueryParameters.Add("domain_hint", "facebook.com");

authResult = await app.AcquireTokenInteractive(App.ApiScopes)
    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
    .WithExtraQueryParameters(extraQueryParameters)
    .ExecuteAsync();
```

[!INCLUDE [active-directory-b2c-app-integration-ui-locales](../../includes/active-directory-b2c-app-integration-ui-locales.md)]

1. [配置语言自定义](language-customization.md)。
1. 创建或使用现有的 `Dictionary` 对象来存储额外的查询参数。
1. 将具有相应语言代码的 `ui_locales` 参数添加到字典中（如 `en-us`）。
1. 将额外的查询参数对象传递到 MSAL 配置对象的 `WithExtraQueryParameters` 方法中。

```csharp
Dictionary<string, string> extraQueryParameters = new Dictionary<string, string>();
extraQueryParameters.Add("ui_locales", "en-us");

authResult = await app.AcquireTokenInteractive(App.ApiScopes)
    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
    .WithExtraQueryParameters(extraQueryParameters)
    .ExecuteAsync();
```

[!INCLUDE [active-directory-b2c-app-integration-custom-parameters](../../includes/active-directory-b2c-app-integration-custom-parameters.md)]

1. 配置 [ContentDefinitionParameters](customize-ui-with-html.md#configure-dynamic-custom-page-content-uri) 元素。
1. 创建或使用现有的 `Dictionary` 对象来存储额外的查询参数。
1. 添加自定义查询字符串参数（如 `campaignId`）。 设置参数值（如 `germany-promotion`）。
1. 将额外的查询参数对象传递到 MSAL 配置对象的 `WithExtraQueryParameters` 方法中。

```csharp
Dictionary<string, string> extraQueryParameters = new Dictionary<string, string>();
extraQueryParameters.Add("campaignId", "germany-promotion");

authResult = await app.AcquireTokenInteractive(App.ApiScopes)
    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
    .WithExtraQueryParameters(extraQueryParameters)
    .ExecuteAsync();
```

[!INCLUDE [active-directory-b2c-app-integration-id-token-hint](../../includes/active-directory-b2c-app-integration-id-token-hint.md)]

1. 在自定义策略中，定义 [ID 令牌提示技术配置文件](id-token-hint.md)。
1. 在代码中，生成或获取 ID 令牌，然后将令牌设置为变量（如 `idToken`）。 
1. 创建或使用现有的 `Dictionary` 对象来存储额外的查询参数。
1. 使用可存储 ID 令牌的相应变量添加 `id_token_hint` 参数。
1. 将额外的查询参数对象传递到 MSAL 配置对象的 `extraQueryParameters` 属性中。

```csharp
Dictionary<string, string> extraQueryParameters = new Dictionary<string, string>();
extraQueryParameters.Add("id_token_hint", idToken);

authResult = await app.AcquireTokenInteractive(App.ApiScopes)
    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
    .WithExtraQueryParameters(extraQueryParameters)
    .ExecuteAsync();
```


[!INCLUDE [active-directory-b2c-app-integration-logging](../../includes/active-directory-b2c-app-integration-logging.md)]


下面的代码片段演示如何配置 MSAL 日志记录：

```csharp
PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
    .WithB2CAuthority(AuthoritySignUpSignIn)
    .WithRedirectUri(RedirectUri)
    .WithLogging(Log, LogLevel.Info, false) // don't log P(ersonally) I(dentifiable) I(nformation) details on a regular basis
    .Build();
```

## <a name="configure-the-redirect-uri"></a>配置重定向 URI

在[桌面应用注册](configure-authentication-sample-wpf-desktop-app.md#step-23-register-the-desktop-app)过程中，在选择重定向 URI 时，请记住以下重要注意事项：

* **开发**：对于桌面应用中的开发用途，可将重定向 URI 设置为 `http://localhost`，Azure AD B2C 将遵循请求中的任何端口。 如果注册的 URI 包含端口，Azure AD B2C 将仅使用该端口。 例如，如果注册的重定向 URI 为 `http://localhost`，则请求中的重定向 URI 可为 `http://localhost:<randomport>`。 如果注册的重定向 URI 为 `http://localhost:8080`，则请求中的重定向 URI 必须为 `http://localhost:8080`。
* **唯一**：每个应用程序的重定向 URI 的方案必须是唯一的。 在示例 `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` 中，`com.onmicrosoft.contosob2c.exampleapp` 为方案。 应遵循此模式。 如果两个应用程序共享同一方案，则用户可选择应用程序。 如果用户选择错误，登录会失败。
* **完整**：重定向 URI 必须同时包含方案和路径。 路径必须在域之后包含至少一个斜杠字符。 例如，`//oauth/` 有效，而 `//oauth` 会失败。 请勿在 URI 中包含特殊字符。 例如，不允许包含下划线字符 (_)。

## <a name="next-steps"></a>后续步骤

- 若要了解详细信息，请参阅[适用于 .NET、UWP、NetCore 和 Xamarin 的 MSAL 配置选项](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)。
