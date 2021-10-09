---
title: 以交互方式获取用于调用 Web API 的令牌（桌面应用）| Azure
titleSuffix: Microsoft identity platform
description: 了解如何构建调用 Web API 以通过交互方式获取应用的令牌的桌面应用
services: active-directory
author: maliksahil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/25/2021
ms.author: sahmalik
ms.custom: aaddev, devx-track-python, has-adal-ref
ms.openlocfilehash: 6cd9e4ff795e1051267aa20e244e03c37c15f831
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124837665"
---
# <a name="desktop-app-that-calls-web-apis-acquire-a-token-interactively"></a>调用 Web API 的桌面应用：以交互方式获取令牌

下面的示例展示了以交互方式获取令牌，以便使用 Microsoft Graph 读取用户配置文件的最少代码。

# <a name="net"></a>[.NET](#tab/dotnet)

### <a name="in-msalnet"></a>在 MSAL.NET 中

```csharp
string[] scopes = new string[] {"user.read"};
var app = PublicClientApplicationBuilder.Create(clientId).Build();
var accounts = await app.GetAccountsAsync();
AuthenticationResult result;
try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
             .ExecuteAsync();
}
catch(MsalUiRequiredException)
{
 result = await app.AcquireTokenInteractive(scopes)
             .ExecuteAsync();
}
```

### <a name="mandatory-parameters"></a>必需参数

`AcquireTokenInteractive` 只有一个必需的参数 ``scopes``，其中包含一个定义需要令牌的范围的字符串枚举。 如果令牌用于 Microsoft Graph，则可以在“权限”一节中每个 Microsoft Graph API 的 API 参考中找到所需范围。 例如，若要[列出用户的联系人](/graph/api/user-list-contacts)，必须使用范围“User.Read”、“Contacts.Read”。 有关详细信息，请参阅 [Microsoft Graph 权限参考](/graph/permissions-reference)。

在 Android 上，还需要按如下所示使用 `.WithParentActivityOrWindow` 指定父活动，以便在交互后令牌返回到该父活动。 如果未指定父活动，则调用 `.ExecuteAsync()` 时会引发异常。

### <a name="specific-optional-parameters-in-msalnet"></a>MSAL.NET 中的特定可选参数

#### <a name="withparentactivityorwindow"></a>WithParentActivityOrWindow

UI 非常重要，因为它是交互式的。 `AcquireTokenInteractive` 提供一个特定的可选参数，该参数可为支持它的平台指定父 UI。 在桌面应用程序中使用时，`.WithParentActivityOrWindow` 根据具体的平台采用不同的类型。 或者，如果你不想控制登录对话框在屏幕上的显示位置，则可以省略可选的父窗口参数来创建窗口。 这适用于基于命令行、用于将调用传递到任何其他后端服务且不需要任何用于用户交互的窗口的应用程序。

```csharp
// net45
WithParentActivityOrWindow(IntPtr windowPtr)
WithParentActivityOrWindow(IWin32Window window)

// Mac
WithParentActivityOrWindow(NSWindow window)

// .NET Standard (this will be on all platforms at runtime, but only on NetStandard at build time)
WithParentActivityOrWindow(object parent).
```

备注：

- 在 .NET Standard 中，预期的 `object` 是 `Activity`（在 Android 上）、`UIViewController`（在 iOS 上）、`NSWindow`（在 Mac 上）和 `IWin32Window` 或 `IntPr`（在 Windows 上）。
- 在 Windows 上，必须从 UI 线程调用 `AcquireTokenInteractive`，以便嵌入性浏览器获取正确的 UI 同步上下文。 不从 UI 线程调用可能会导致无法正常输送消息和 UI 出现死锁的情况。 在尚未进入 UI 线程的情况下，从 UI 线程调用 Microsoft 身份验证库 (MSAL) 的方法之一是使用 WPF 上的 `Dispatcher`。
- 使用 WPF 时，若要从 WPF 控件获取一个窗口，可以使用 `WindowInteropHelper.Handle` 类。 然后从 WPF 控件 (`this`) 发出调用：

  ```csharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>WithPrompt

`WithPrompt()` 用于通过指定提示来控制与用户的交互。 可以使用 [Microsoft.Identity.Client.Prompt](/dotnet/api/microsoft.identity.client.prompt) 结构控制确切的行为。

结构定义以下常量：

- `SelectAccount` 强制 STS 显示帐户选择对话框，其中包含用户已建立会话的帐户。 当应用程序开发人员想要让用户在不同的标识之间选择时，此选项非常有用。 此选项驱动 MSAL 将 `prompt=select_account` 发送到标识提供者。 此选项为默认值。 它能够很好地根据可用的信息（例如帐户和用户会话的存在性）提供尽量最佳的体验。 不要对其进行更改，除非你有充分的理由。
- 应用程序开发人员可以使用 `Consent` 强制要求向用户显示许可提示，即使以前已经授予了许可。 在这种情况下，MSAL 会将 `prompt=consent` 发送到标识提供者。 此选项可用于某些注重安全的应用程序，其中的组织监管机制要求每次使用该应用程序时，都要向用户显示许可对话框。
- 应用程序开发人员可以使用 `ForceLogin` 来让服务向用户显示凭据提示，即使可能不需要这种用户提示。 如果获取令牌失败，可以使用此选项让用户重新登录。 在这种情况下，MSAL 会将 `prompt=login` 发送到标识提供者。 有时，此选项会在某些注重安全的应用程序中使用，其中的组织监管机制要求用户每次在访问应用程序的特定部分时重新登录。
- `Create` 通过向标识提供程序发送 `prompt=create` 触发用于外部标识的注册体验。 不应为 Azure AD B2C 应用发送此提示。 有关详细信息，请参阅[向应用添加自助注册用户流](../external-identities/self-service-sign-up-user-flow.md)。
- `Never`（仅适用于 .NET 4.5 和 WinRT）不会提示用户，而是尝试使用隐藏的嵌入式 Web 视图中存储的 Cookie。 有关详细信息，请参阅“MSAL.NET 中的 Web 视图”。 使用此选项可能会失败。 在这种情况下，`AcquireTokenInteractive` 会引发异常来告知需要 UI 交互。 需要使用另一个 `Prompt` 参数。
- `NoPrompt` 不会向标识提供者发送任何提示，因此将决定向用户提供最佳登录体验（单一登录或选择帐户）。 此选项对于 Azure Active Directory (Azure AD) B2C 编辑配置文件策略也是必需的。 有关详细信息，请参阅 [Azure AD B2C 细节](https://aka.ms/msal-net-b2c-specificities)。

#### <a name="withuseembeddedwebview"></a>WithUseEmbeddedWebView

通过此方法，可以指定是否要强制使用嵌入式 Web 视图或系统 Web 视图（可用时）。 有关详细信息，请参阅 [Web 浏览器的用法](msal-net-web-browsers.md)。

```csharp
var result = await app.AcquireTokenInteractive(scopes)
                    .WithUseEmbeddedWebView(true)
                    .ExecuteAsync();
```

#### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

此修饰符在高级方案中使用，在该方案中你希望用户提前许可多个资源，并且不想使用增量许可，这种许可通常与 MSAL.NET/Microsoft 标识平台配合使用。 有关详细信息，请参阅[让用户提前许可多个资源](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources)。

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>WithCustomWebUi

Web UI 是一种调用浏览器的机制。 此机制可以是专用的 UI WebBrowser 控件，也可以是委托打开浏览器的方法。
MSAL 为大多数平台提供 Web UI 实现，但有时，你可能需要自行托管浏览器：

- MSAL 未明确涵盖的平台，例如 Blazor、Unity 和桌面上的 Mono。
- 你希望对应用程序进行 UI 测试，并使用可以与 Selenium 配合使用的自动化浏览器。
- 浏览器和运行 MSAL 的应用位于不同的进程中。

##### <a name="at-a-glance"></a>速览

为了实现这一点，请为 MSAL 提供需要显示在所选浏览器中的 `start Url`，方便最终用户输入用户名等项。
身份验证完成后，应用需将 `end Url`（其中包含 Azure AD 提供的代码）传回给 MSAL。
`end Url` 的宿主始终为 `redirectUri`。 若要截获 `end Url`，请执行以下操作之一：

- 监视浏览器重定向，直至进入 `redirect Url`。
- 让浏览器重定向到你监视的 URL。

##### <a name="withcustomwebui-is-an-extensibility-point"></a>WithCustomWebUi 是一个扩展点

`WithCustomWebUi` 是一个扩展点，可用于在公共客户端应用程序中提供你自己的 UI。 还可以让用户通过标识提供者的 /Authorize 终结点，并让其登录和许可。 然后，MSAL.NET 可以兑换身份验证代码并获取令牌。 例如，在 Visual Studio 中使用该参数可让电子应用程序（例如 Visual Studio 反馈）提供 Web 交互，并让 MSAL.NET 完成大部分工作。 如果要提供 UI 自动化，也可以使用它。 在公共客户端应用程序中，MSAL.NET 使用代码交换的证明密钥 (PKCE) 标准来确保遵守规则。 只有 MSAL.NET 才能兑换代码。 有关详细信息，请参阅 [RFC 7636 - OAuth 公共客户端的代码交换证明密钥](https://tools.ietf.org/html/rfc7636)。

  ```csharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="use-withcustomwebui"></a>使用 WithCustomWebUi

若要使用 `.WithCustomWebUI`，请执行以下步骤。

  1. 实现 `ICustomWebUi` 接口。 有关详细信息，请参阅[此网站](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70)。 实现一个 `AcquireAuthorizationCodeAsync` 方法，并接受 MSAL.NET 计算的授权代码 URL。 然后，让用户完成与标识提供者的交互，并返回该 URL，标识提供者会结合授权代码一起使用该 URL 回调你的实现。 如果遇到问题，实现应引发 `MsalExtensionException` 异常，以便与 MSAL 进行良好的配合。
  2. 在 `AcquireTokenInteractive` 调用中，使用 `.WithCustomUI()` 修饰符传递自定义 Web UI 的实例。

     ```csharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation-seleniumwebui"></a>测试自动化中 ICustomWebUi 的实现示例：SeleniumWebUI

MSAL.NET 团队已重新编写 UI 测试，以使用此扩展性机制。 如果有兴趣，请查看 MSAL.NET 源代码中的 [SeleniumWebUI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160) 类。

##### <a name="provide-a-great-experience-with-systemwebviewoptions"></a>通过 SystemWebViewOptions 提供极佳体验

在 MSAL.NET 4.1 [`SystemWebViewOptions`](/dotnet/api/microsoft.identity.client.systemwebviewoptions) 中，可以指定：

- 在系统 Web 浏览器中出现登录或许可错误时，要转到的 URI (`BrowserRedirectError`) 或需要显示的 HTML 片段 (`HtmlMessageError`)。
- 在成功完成登录或许可时，要转到的 URI (`BrowserRedirectSuccess`) 或需要显示的 HTML 片段 (`HtmlMessageSuccess`)。
- 启动系统浏览器所需运行的操作。 可以通过设置 `OpenBrowserAsync` 委托来提供自己的实现。 此类还为两个浏览器提供默认的实现：`OpenWithEdgeBrowserAsync` 和 `OpenWithChromeEdgeBrowserAsync`，分别对应于 Microsoft Edge 和 [Chromium 上的 Microsoft Edge](https://www.windowscentral.com/faq-edge-chromium)。

若要使用此结构，请编写如下示例所示的内容：

```csharp
IPublicClientApplication app;
...

options = new SystemWebViewOptions
{
 HtmlMessageError = "<b>Sign-in failed. You can close this tab ...</b>",
 BrowserRedirectSuccess = "https://contoso.com/help-for-my-awesome-commandline-tool.html"
};

var result = app.AcquireTokenInteractive(scopes)
                .WithEmbeddedWebView(false)       // The default in .NET Core
                .WithSystemWebViewOptions(options)
                .Build();
```

#### <a name="other-optional-parameters"></a>其他可选参数

若要详细了解 `AcquireTokenInteractive` 的所有其他可选参数，请参阅 [AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder#methods)。

# <a name="java"></a>[Java](#tab/java)

```java
private static IAuthenticationResult acquireTokenInteractive() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            InteractiveRequestParameters parameters = InteractiveRequestParameters
                    .builder(new URI("http://localhost"))
                    .scopes(SCOPE)
                    .build();

            // Try to acquire a token interactively with system browser. If successful, you should see
            // the token and account information printed out to console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="macos"></a>[macOS](#tab/macOS)

### <a name="in-msal-for-ios-and-macos"></a>在适用于 iOS 和 macOS 的 MSAL 中

Objective-C：

```objc
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopes webviewParameters:[MSALWebviewParameters new]];
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) {
    if (!error)
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;

        NSString *accessToken = result.accessToken;
    }
}];
```

Swift：

```swift
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopes, webviewParameters: MSALWebviewParameters())
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in

    guard let authResult = result, error == nil else {
        print(error!.localizedDescription)
        return
    }

    // Get access token from result
    let accessToken = authResult.accessToken
})
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

在 MSAL 节点中，可以使用代码交换证明密钥 (PKCE) 通过授权代码流获取令牌。 该过程有两个步骤：首先，应用程序获取可用于生成授权代码的 URL。 此 URL 可在所选的浏览器中打开，用户可以在其中输入凭据，并使用授权代码将其重定向回 `redirectUri`（在应用注册期间注册）。 其次，应用程序将接收的授权代码传递到用于将其交换为访问令牌的 `acquireTokenByCode()` 方法。

```javascript
const msal = require("@azure/msal-node");

const msalConfig = {
    auth: {
        clientId: "your_client_id_here",
        authority: "your_authority_here",
    }
};

const pca = new msal.PublicClientApplication(msalConfig);

const {verifier, challenge} = await msal.cryptoProvider.generatePkceCodes();

const authCodeUrlParameters = {
    scopes: ["User.Read"],
    redirectUri: "your_redirect_uri",
    codeChallenge: challenge, // PKCE Code Challenge
    codeChallengeMethod: "S256" // PKCE Code Challenge Method 
};

// get url to sign user in and consent to scopes needed for application
pca.getAuthCodeUrl(authCodeUrlParameters).then((response) => {
    console.log(response);

    const tokenRequest = {
        code: response["authorization_code"],
        codeVerifier: verifier // PKCE Code Verifier 
        redirectUri: "your_redirect_uri",
        scopes: ["User.Read"],
    };

    // acquire a token by exchanging the code
    pca.acquireTokenByCode(tokenRequest).then((response) => {
        console.log("\nResponse: \n:", response);
    }).catch((error) => {
        console.log(error);
    });
}).catch((error) => console.log(JSON.stringify(error)));
```

# <a name="python"></a>[Python](#tab/python)

MSAL Python 1.7+ 提供交互式获取令牌方法。

```python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_interactive(  # It automatically provides PKCE protection
         scopes=config["scope"])
```

---
### <a name="next-steps"></a>后续步骤

转到此方案中的下一篇文章：[从桌面应用调用 Web API](scenario-desktop-call-api.md)。
