---
title: 使用集成 Windows 身份验证获取用于调用 Web API 的令牌（桌面应用）| Azure
titleSuffix: Microsoft identity platform
description: 了解如何使用集成 Windows 身份验证，构建调用 Web API 以获取应用的令牌的桌面应用
services: active-directory
author: maliksahil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/25/2021
ms.author: sahmalik
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 67b2c1ac6b9a73444639bb174eacfbf5d685ce92
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129234825"
---
# <a name="desktop-app-that-calls-web-apis-acquire-a-token-using-integrated-windows-authentication"></a>调用 Web API 的桌面应用：使用集成窗口身份验证获取令牌

若要在域中或加入 Azure AD 的计算机上登录域用户，请使用集成 Windows 身份验证 (IWA)。

## <a name="constraints"></a>约束

- 集成 Windows 身份验证仅可用于“联合 +”用户，即在 Active Directory 中创建并受 Azure AD 支持的用户。 直接在 Azure AD 中创建的但不是由 Active Directory 支持的用户（称为“托管用户”）不能使用此身份验证流。 此项限制不影响用户名和密码流。
- IWA 不会绕过[多重身份验证 (MFA)](../authentication/concept-mfa-howitworks.md)。 如果已配置 MFA，则在需要 MFA 质询的情况下，IWA 可能失败，因为 MFA 需要用户交互。

    IWA 是非交互式的，但 MFA 需要用户交互。 标识提供者何时请求执行 MFA 并不由你控制，而是由租户管理员控制。 根据观察，在以下情况下需要 MFA：当你从不同国家/地区登录时；未通过 VPN 连接到公司网络时；有时甚至通过 VPN 连接也需要 MFA。 规则并不确定。 Azure AD 使用 AI 来持续判断是否需要执行 MFA。 如果 IWA 失败，则回退到用户提示，例如交互式身份验证或设备代码流。

- 在 `PublicClientApplicationBuilder` 中传入的颁发机构需要：
  - 租户化（采用 `https://login.microsoftonline.com/{tenant}/` 格式，其中，`tenant` 是表示租户 ID 或者与该租户关联的域的 GUID）。
  - 对于任何工作和学校帐户：`https://login.microsoftonline.com/organizations/`。
  - 不支持 Microsoft 个人帐户。 不能使用 /common 或 /consumers 租户。

- 因为集成 Windows 身份验证是一个无提示流：
  - 应用程序的用户必须已事先许可使用该应用程序。
  - 或者，租户管理员必须已事先许可租户中的所有用户使用该应用程序。
  - 换句话说：
    - 开发人员已在 Azure 门户中自行选择“授予”按钮。
    - 或者，租户管理员已在应用程序注册的“API 权限”选项卡中选择“授予/撤销 {租户域} 的管理员许可”按钮。  有关详细信息，请参阅[添加访问 Web API 的权限](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api)。
    - 或者，你已提供某种方式让用户许可应用程序。 有关详细信息，请参阅[请求单个用户的许可](./v2-permissions-and-consent.md#requesting-individual-user-consent)。
    - 或者，你已提供某种方式让租户管理员许可应用程序。 有关详细信息，请参阅[管理员许可](./v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)。

- 已针对 .NET Desktop、.NET Core 和 UWP 应用启用此流。

有关许可的详细信息，请参阅 [Microsoft 标识平台的权限和许可](./v2-permissions-and-consent.md)。

## <a name="learn-how-to-use-it"></a>了解其用法

# <a name="net"></a>[.NET](#tab/dotnet)

在 MSAL.NET 中，请使用：

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

通常只需要一个参数 (`scopes`)。 根据 Windows 管理员设置策略的方式，有可能不允许 Windows 计算机上的应用程序查找已登录的用户。 在这种情况下，请使用另一个方法 `.WithUsername()`，并以 UPN 格式（例如 `joe@contoso.com`）传入已登录用户的用户名。

下面的示例展示了最新的情况，并说明了可获取的异常类型以及缓解措施。

```csharp
static async Task GetATokenForGraph()
{
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

有关 AcquireTokenByIntegratedWindowsAuthentication 上可能的修饰符列表，请参阅 [AcquireTokenByIntegratedWindowsAuthParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyintegratedwindowsauthparameterbuilder#methods)。

# <a name="java"></a>[Java](#tab/java)

此代码摘录自 [MSAL Java 开发示例](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/)。

```java
private static IAuthenticationResult acquireTokenIwa() throws Exception {

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

            IntegratedWindowsAuthenticationParameters parameters =
                    IntegratedWindowsAuthenticationParameters
                            .builder(SCOPE, USER_NAME)
                            .build();

            // Try to acquire a IWA. You will need to generate a Kerberos ticket.
            // If successful, you should see the token and account information printed out to
            // console
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

此流不适用于 macOS。

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

MSAL 节点尚不支持此流。

# <a name="python"></a>[Python](#tab/python)

MSAL Python 中尚不支持此流。

---
## <a name="next-steps"></a>后续步骤

转到此方案中的下一篇文章：[从桌面应用调用 Web API](scenario-desktop-call-api.md)。
