---
title: 将公共客户端应用程序迁移到 MSAL.NET
titleSuffix: Microsoft identity platform
description: 了解如何将公共客户端应用程序从适用于 .NET 的 Azure Active Directory 身份验证库迁移到适用于 .NET 的 Microsoft 身份验证库。
services: active-directory
author: sahmalik
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 08/31/2021
ms.author: sahmalik
ms.reviewer: saeeda, shermanouko, jmprieur
ms.custom: devx-track-csharp, aaddev, has-adal-ref
ms.openlocfilehash: 1ccd2acaaec8c49de761511ebd3d4a5ad86f4095
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129233647"
---
# <a name="migrate-public-client-applications-from-adalnet-to-msalnet"></a>将公共客户端应用程序从 ADAL.NET 迁移到 MSAL.NET

本文介绍如何将公共客户端应用程序从适用于 .NET 的 Azure Active Directory 身份验证库 (ADAL.NET) 迁移到适用于 .NET 的 Microsoft 身份验证库 (MSAL.NET)。 公共客户端应用程序是桌面应用，包括代表用户调用其他服务的 Win32、WPF、UWP 和移动应用。 有关公共客户端应用程序的详细信息，请参阅[身份验证流和应用程序方案](authentication-flows-app-scenarios.md)。

## <a name="migration-steps"></a>迁移步骤

1. 在应用中使用 ADAL.NET 查找代码。

   公共客户端应用程序中使用 ADAL 的代码将会通过以下参数实例化 `AuthenticationContext` 并调用 `AcquireTokenAsync` 的重写：

   - `resourceId` 字符串。 此变量是要调用的 Web API 的应用 ID URI。
   - 一个 `clientId`，即应用程序的标识符，也称为应用 ID。

2. 确定有应用使用 ADAL.NET 后，安装 MSAL.NET NuGet 包 [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) 并更新项目库引用。 有关详细信息，请参阅[安装 NuGet 包](https://www.bing.com/search?q=install+nuget+package)。

3. 根据公共客户端应用程序方案更新代码。 有些步骤是通用的，适用于所有公共客户端方案。 其他步骤对每种场景具有唯一性。 

   公共客户端方案包括：

   - [Web 身份验证管理器](scenario-desktop-acquire-token-wam.md)，Windows 上首选的基于代理的身份验证。
   - [交互式身份验证](scenario-desktop-acquire-token-interactive.md)，其中会向用户显示一个基于 Web 的界面用于完成登录过程。
   - [集成 Windows 身份验证](scenario-desktop-acquire-token-integrated-windows-authentication.md)，用户使用他们在登录到 Windows 域时所用的相同标识进行登录（适用于已加入域或已加入 Azure AD 的计算机）。
   - [用户名/密码](scenario-desktop-acquire-token-username-password.md)，通过提供用户名/密码凭据进行登录。
   - [设备代码流](scenario-desktop-acquire-token-device-code-flow.md)，在其中，UX 受限的设备会显示一个设备代码，以便在备用设备上完成身份验证流。


## <a name="interactive"></a>[交互式](#tab/interactive)

在交互式方案中，公共客户端应用程序将显示托管在浏览器中的登录用户界面，并要求用户以交互方式登录。

#### <a name="find-out-if-your-code-uses-interactive-scenarios"></a>确定代码是否使用交互式方案

在使用交互式身份验证的公共客户端应用程序中，应用的 ADAL 代码将会通过以下参数实例化 `AuthenticationContext` 并包含对 `AcquireTokenAsync` 的调用。
 - 一个 `clientId`，即表示应用程序注册的 GUID
 - 一个 `resourceUrl`，表示要请求其令牌的资源
 - 一个表示回复 URL 的 URI
 - `PlatformParameters` 对象。 

 #### <a name="update-the-code-for-interactive-scenarios"></a>更新交互式方案的代码

 [!INCLUDE [Common steps](includes/msal-net-adoption-steps-public-clients.md)]

在这种情况下，我们使用对 `IPublicClientApplication.AcquireTokenInteractive` 的调用替换对 `AuthenticationContext.AcquireTokenAsync` 的调用。

下面是交互式方案的 ADAL.NET 和 MSAL.NET 代码的比较：

:::row:::
:::column span="":::
    ADAL
:::column-end:::
:::column span="":::
    MSAL
:::column-end:::
:::row-end:::
:::row:::
:::column span="":::
      
```csharp
var ac = new AuthenticationContext("https://login.microsoftonline.com/<tenantId>");
AuthenticationResult result;
result = await ac.AcquireTokenAsync("<clientId>",
                                    "https://resourceUrl",
                                    new Uri("https://ClientReplyUrl"),
                                    new PlatformParameters(PromptBehavior.Auto));
```
:::column-end:::   
:::column span="":::
```csharp
// 1. Configuration - read below about redirect URI
var pca = PublicClientApplicationBuilder.Create("client_id")
              .WithBroker()
              .Build();

// Add a token cache, see https://docs.microsoft.com/en-us/azure/active-directory/develop/msal-net-token-cache-serialization?tabs=desktop

// 2. GetAccounts
var accounts = await pca.GetAccountsAsync();
var accountToLogin = // choose an account, or null, or use PublicClientApplication.OperatingSystemAccount for the default OS account

try
{
    // 3. AcquireTokenSilent 
    var authResult = await pca.AcquireTokenSilent(new[] { "User.Read" }, accountToLogin)
                              .ExecuteAsync();
}
catch (MsalUiRequiredException) // no change in the pattern
{
    // 4. Specific: Switch to the UI thread for next call . Not required for console apps.
    await SwitchToUiThreadAsync(); // not actual code, this is different on each platform / tech

    // 5. AcquireTokenInteractive
    var authResult = await pca.AcquireTokenInteractive(new[] { "User.Read" })
                              .WithAccount(accountToLogin)  // this already exists in MSAL, but it is more important for WAM
                              .WithParentActivityOrWindow(myWindowHandle) // to be able to parent WAM's windows to your app (optional, but highly recommended; not needed on UWP)
                              .ExecuteAsync();
}
```
   :::column-end:::
:::row-end:::

上面所示的 MSAL 代码使用 WAM（Web 身份验证管理器），这是建议的方法。 如果你想要使用交互式身份验证但不使用 WAM，请参阅[交互式身份验证](scenario-desktop-acquire-token-interactive.md)。

## <a name="integrated-windows-authentication"></a>[集成 Windows 身份验证](#tab/iwa)

在集成 Windows 身份验证中，公共客户端应用程序使用登录到 Windows 域时所用的相同标识进行登录（适用于已加入域或已加入 Azure AD 的计算机）。

#### <a name="find-out-if-your-code-uses-integrated-windows-authentication"></a>确定代码是否使用集成 Windows 身份验证

如果应用的 ADAL 代码通过以下参数包含了可用作 `AuthenticationContextIntegratedAuthExtensions` 类的扩展方法的 `AcquireTokenAsync` 调用，则表示该代码使用了集成 Windows 身份验证方案：

- 一个 `resource`，表示要请求其令牌的资源
- 一个 `clientId`，即表示应用程序注册的 GUID
- 一个 `UserCredential` 对象，表示你尝试为其请求令牌的用户。

#### <a name="update-the-code-for-integrated-windows-authentication-scenarios"></a>更新集成 Windows 身份验证方案的代码

 [!INCLUDE [Common steps](includes/msal-net-adoption-steps-public-clients.md)]

在这种情况下，我们使用对 `IPublicClientApplication.AcquireTokenByIntegratedWindowsAuth` 的调用替换对 `AuthenticationContext.AcquireTokenAsync` 的调用。

下面是集成 Windows 身份验证方案的 ADAL.NET 和 MSAL.NET 代码的比较：

:::row:::
:::column span="":::
    ADAL
:::column-end:::
:::column span="":::
    MSAL
:::column-end:::
:::row-end:::
:::row:::
:::column span="":::
      
```csharp
var ac = new AuthenticationContext("https://login.microsoftonline.com/<tenantId>");
AuthenticationResult result;
result = await context.AcquireTokenAsync(resource, clientId,
                                         new UserCredential("john@contoso.com"));
```
:::column-end:::   
:::column span="":::
```csharp
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app = PublicClientApplicationBuilder
      .Create(clientId)
      .WithAuthority(authority)
      .Build();

 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
      .ExecuteAsync();
 }
 else
 {
  try
  {
   result = await app.AcquireTokenByIntegratedWindowsAuth(scopes)
      .ExecuteAsync(CancellationToken.None);
  }
  catch (MsalUiRequiredException ex)
  {
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'.Send an interactive authorization request for this user and resource.

   // you need to get user consent first. This can be done, if you are not using .NET Core (which does not have any Web UI)
   // by doing (once only) an AcquireToken interactive.

   // If you are using .NET core or don't want to do an AcquireTokenInteractive, you might want to suggest the user to navigate
   // to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read

   // AADSTS50079: The user is required to use multi-factor authentication.
   // There is no mitigation - if MFA is configured for your tenant and AAD decides to enforce it,
   // you need to fallback to an interactive flows such as AcquireTokenInteractive or AcquireTokenByDeviceCode
   }
   catch (MsalServiceException ex)
   {
    // Kind of errors you could have (in ex.Message)

    // MsalServiceException: AADSTS90010: The grant type is not supported over the /common or /consumers endpoints. Please use the /organizations or tenant-specific endpoint.
    // you used common.
    // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted or otherwise organizations

    // MsalServiceException: AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
    // Explanation: this can happen if your application was not registered as a public client application in Azure AD
    // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   }
   catch (MsalClientException ex)
   {
      // Error Code: unknown_user Message: Could not identify logged in user
      // Explanation: the library was unable to query the current Windows logged-in user or this user is not AD or AAD
      // joined (work-place joined users are not supported).

      // Mitigation 1: on UWP, check that the application has the following capabilities: Enterprise Authentication,
      // Private Networks (Client and Server), User Account Information

      // Mitigation 2: Implement your own logic to fetch the username (e.g. john@contoso.com) and use the
      // AcquireTokenByIntegratedWindowsAuth form that takes in the username

      // Error Code: integrated_windows_auth_not_supported_managed_user
      // Explanation: This method relies on an a protocol exposed by Active Directory (AD). If a user was created in Azure
      // Active Directory without AD backing ("managed" user), this method will fail. Users created in AD and backed by
      // AAD ("federated" users) can benefit from this non-interactive method of authentication.
      // Mitigation: Use interactive authentication
   }
 }

 Console.WriteLine(result.Account.Username);
}
```
   :::column-end:::
:::row-end:::

## <a name="username-password"></a>[用户名密码](#tab/up)

在用户名密码身份验证中，将通过提供用户名/密码凭据进行登录。
#### <a name="find-out-if-your-code-uses-username-password-authentication"></a>确定代码是否使用用户名密码身份验证

如果应用的 ADAL 代码通过以下参数包含了对作为 `AuthenticationContextIntegratedAuthExtensions` 类扩展方法提供的 `AcquireTokenAsync` 的调用，则表示该代码使用了用户名密码身份验证方案：

- 一个 `resource`，表示要请求其令牌的资源
- 一个 `clientId`，即表示应用程序注册的 GUID
- 一个 `UserPasswordCredential` 对象，其中包含你尝试为其请求令牌的用户的用户名和密码。

#### <a name="update-the-code-for-username-password-auth-scenarios"></a>更新用户名密码身份验证方案的代码

在这种情况下，我们使用对 `IPublicClientApplication.AcquireTokenByUsernamePassword` 的调用替换对 `AuthenticationContext.AcquireTokenAsync` 的调用。

下面是用户名密码方案的 ADAL.NET 和 MSAL.NET 代码的比较：

 [!INCLUDE [Common steps](includes/msal-net-adoption-steps-public-clients.md)]

:::row:::
:::column span="":::
    ADAL
:::column-end:::
:::column span="":::
    MSAL
:::column-end:::
:::row-end:::
:::row:::
:::column span="":::
      
```csharp
var ac = new AuthenticationContext("https://login.microsoftonline.com/<tenantId>");
AuthenticationResult result;
result = await context.AcquireTokenAsync(
   resource, clientId, 
   new UserPasswordCredential("john@contoso.com", johnsPassword));

```
:::column-end:::   
:::column span="":::
```csharp
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuilder.Create(clientId)
       .WithAuthority(authority)
       .Build();
 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                    .ExecuteAsync();
 }
 else
 {
  try
  {
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password
    securePassword.AppendChar(c);  // keystroke by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                     securePassword)
                      .ExecuteAsync();
  }
  catch(MsalException)
  {
   // See details below
  }
 }
 Console.WriteLine(result.Account.Username);
```
   :::column-end:::
:::row-end:::

## <a name="device-code"></a>[设备代码](#tab/devicecode)

在设备代码流身份验证中，UX 受限的设备会显示一个设备代码，以便在备用设备上完成身份验证流。

#### <a name="find-out-if-your-code-uses-device-code-flow-authentication"></a>确定代码是否使用设备代码流身份验证

如果应用的 ADAL 代码通过以下参数包含了对 `AuthenticationContext.AcquireTokenByDeviceCodeAsync` 的调用，则表示该代码使用了设备代码流方案：
- 一个 `DeviceCodeResult` 对象实例，该实例是使用你要请求其令牌的资源的 `resourceID` 实例化的；一个 `clientId`，即表示应用程序的 GUID。

#### <a name="update-the-code-for-device-code-flow-scenarios"></a>更新设备代码流方案的代码

 [!INCLUDE [Common steps](includes/msal-net-adoption-steps-public-clients.md)]

在这种情况下，我们使用对 `IPublicClientApplication.AcquireTokenWithDeviceCode` 的调用替换对 `AuthenticationContext.AcquireTokenAsync` 的调用。

下面是设备代码流方案的 ADAL.NET 和 MSAL.NET 代码的比较：

:::row:::
:::column span="":::
    ADAL
:::column-end:::
:::column span="":::
    MSAL
:::column-end:::
:::row-end:::
:::row:::
:::column span="":::
      
```csharp
static async Task<AuthenticationResult> GetTokenViaCode(AuthenticationContext ctx)
{
 AuthenticationResult result = null;
 try
 {
  result = await ac.AcquireTokenSilentAsync(resource, clientId);
 }
 catch (AdalException adalException)
 {
  if (adalException.ErrorCode == AdalError.FailedToAcquireTokenSilently
   || adalException.ErrorCode == AdalError.InteractionRequired)
  {
  try
  {
   DeviceCodeResult codeResult = await ctx.AcquireDeviceCodeAsync(resource, clientId);
   Console.WriteLine("You need to sign in.");
   Console.WriteLine("Message: " + codeResult.Message + "\n");
   result = await ctx.AcquireTokenByDeviceCodeAsync(codeResult);
  }
  catch (Exception exc)
  {
   Console.WriteLine("Something went wrong.");
   Console.WriteLine("Message: " + exc.Message + "\n");
  }
 }
 return result;
}

```
:::column-end:::   
:::column span="":::
```csharp
private const string ClientId = "<client_guid>";
private const string Authority = "https://login.microsoftonline.com/contoso.com";
private readonly string[] scopes = new string[] { "user.read" };

static async Task<AuthenticationResult> GetATokenForGraph()
{
    IPublicClientApplication pca = PublicClientApplicationBuilder
            .Create(ClientId)
            .WithAuthority(Authority)
            .WithDefaultRedirectUri()
            .Build();

    var accounts = await pca.GetAccountsAsync();

    // All AcquireToken* methods store the tokens in the cache, so check the cache first
    try
    {
        return await pca.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
            .ExecuteAsync();
    }
    catch (MsalUiRequiredException ex)
    {
        // No token found in the cache or AAD insists that a form interactive auth is required (e.g. the tenant admin turned on MFA)
        // If you want to provide a more complex user experience, check out ex.Classification

        return await AcquireByDeviceCodeAsync(pca);
    }
}

private static async Task<AuthenticationResult> AcquireByDeviceCodeAsync(IPublicClientApplication pca)
{
    try
    {
        var result = await pca.AcquireTokenWithDeviceCode(scopes,
            deviceCodeResult =>
            {
                    // This will print the message on the console which tells the user where to go sign-in using
                    // a separate browser and the code to enter once they sign in.
                    // The AcquireTokenWithDeviceCode() method will poll the server after firing this
                    // device code callback to look for the successful login of the user via that browser.
                    // This background polling (whose interval and timeout data is also provided as fields in the
                    // deviceCodeCallback class) will occur until:
                    // * The user has successfully logged in via browser and entered the proper code
                    // * The timeout specified by the server for the lifetime of this code (typically ~15 minutes) has been reached
                    // * The developing application calls the Cancel() method on a CancellationToken sent into the method.
                    //   If this occurs, an OperationCanceledException will be thrown (see catch below for more details).
                    Console.WriteLine(deviceCodeResult.Message);
                return Task.FromResult(0);
            }).ExecuteAsync();

        Console.WriteLine(result.Account.Username);
        return result;
    }

    // TODO: handle or throw all these exceptions depending on your app
    catch (MsalServiceException ex)
    {
        // Kind of errors you could have (in ex.Message)

        // AADSTS50059: No tenant-identifying information found in either the request or implied by any provided credentials.
        // Mitigation: as explained in the message from Azure AD, the authoriy needs to be tenanted. you have probably created
        // your public client application with the following authorities:
        // https://login.microsoftonline.com/common or https://login.microsoftonline.com/organizations

        // AADSTS90133: Device Code flow is not supported under /common or /consumers endpoint.
        // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted

        // AADSTS90002: Tenant <tenantId or domain you used in the authority> not found. This may happen if there are
        // no active subscriptions for the tenant. Check with your subscription administrator.
        // Mitigation: if you have an active subscription for the tenant this might be that you have a typo in the
        // tenantId (GUID) or tenant domain name.
    }
    catch (OperationCanceledException ex)
    {
        // If you use a CancellationToken, and call the Cancel() method on it, then this *may* be triggered
        // to indicate that the operation was cancelled.
        // See https://docs.microsoft.com/dotnet/standard/threading/cancellation-in-managed-threads
        // for more detailed information on how C# supports cancellation in managed threads.
    }
    catch (MsalClientException ex)
    {
        // Possible cause - verification code expired before contacting the server
        // This exception will occur if the user does not manage to sign-in before a time out (15 mins) and the
        // call to `AcquireTokenWithDeviceCode` is not cancelled in between
    }
}
```
   :::column-end:::
:::row-end:::

---
### <a name="msal-benefits"></a>MSAL 优势

对于应用来说，MSAL.NET 的主要优势包括：

- **复原能力**。 MSAL.NET 可通过以下方面帮助应用实现可复原性：

   - Azure AD 缓存凭据服务 (CCS) 优势。 CCS 以 Azure AD 备份的形式操作。
   - 如果调用的 API 通过[连续访问评估](app-resilience-continuous-access-evaluation.md)启用了长效令牌，则主动更新令牌。

### <a name="troubleshooting"></a>故障排除

以下故障排除信息提出两种假设： 

- 你的 ADAL.NET 代码正常运行。
- 你通过保留同一客户端 ID 迁移到 MSAL。

如果通过以下任一消息遇到异常： 

> `AADSTS90002: Tenant 'cf61953b-e41a-46b3-b500-663d279ea744' not found. This may happen if there are no active`
> `subscriptions for the tenant. Check to make sure you have the correct tenant ID. Check with your subscription`
> `administrator.`

可以使用以下步骤对异常进行故障排除：

1. 确认使用的是最新版本的 MSAL.NET。
1. 确认在生产机密客户端应用程序时设置的授权主机与使用 ADAL 时使用的授权主机类似。 具体而言，这是否为同一种[云](msal-national-cloud.md)（Azure 政府、Azure 中国世纪互联或 Azure 德国）？

## <a name="next-steps"></a>后续步骤

详细了解 [ADAL.NET 与 MSAL.NET 应用之间的差异](msal-net-differences-adal-net.md)。
详细了解 [MSAL.NET 中的令牌缓存序列化](msal-net-token-cache-serialization.md)
