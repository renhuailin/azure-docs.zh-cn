---
title: 配置调用 Web API 的 Web 应用 | Azure
titleSuffix: Microsoft identity platform
description: 了解如何配置调用 Web API 的 Web 应用的代码
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/25/2020
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: e9a5b288976d375d9f773fee3dc1ea671ed902b4
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124786515"
---
# <a name="a-web-app-that-calls-web-apis-code-configuration"></a>调用 Web API 的 Web 应用：代码配置

如[用于登录用户的 Web 应用](scenario-web-app-sign-user-overview.md)方案中所述，Web 应用使用 [OAuth 2.0 授权代码流](v2-oauth2-auth-code-flow.md)来登录用户。 此流有两个步骤：

1. 请求获取授权代码。 此部分将私密对话和用户一起委派给 Microsoft 标识平台。 在此对话期间，用户登录，并同意使用 Web API。 当此私密对话成功结束时，Web 应用会在其重定向 URI 中收到一个授权代码。
1. 通过兑换授权代码来请求获取 API 的访问令牌。

[用于登录用户的 Web 应用](scenario-web-app-sign-user-overview.md)方案只涉及第一步。 在本文中，你将学习如何修改 Web 应用，使其不仅能登录用户，而且现在还能调用 Web API。

## <a name="microsoft-libraries-supporting-web-apps"></a>支持 Web 应用的 Microsoft 库

以下 Microsoft 库支持 Web 应用：

[!INCLUDE [active-directory-develop-libraries-webapp](../../../includes/active-directory-develop-libraries-webapp.md)]

选择你感兴趣的平台的选项卡：

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="client-secrets-or-client-certificates"></a>客户端密码或客户端证书

鉴于 Web 应用现在调用下游的 Web API，请提供 appsettings.json 文件中客户端密码或客户端证书。 你还可以添加一个节来指定：

- 下游 Web API 的 URL
- 调用 API 所需的范围

在下面的示例中，`GraphBeta` 节指定了这些设置。

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-app-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common"

   // To call an API
   "ClientSecret": "[Copy the client secret added to the app from the Azure portal]",
   "ClientCertificates": [
  ]
 },
 "GraphBeta": {
    "BaseUrl": "https://graph.microsoft.com/beta",
    "Scopes": "user.read"
    }
}
```

你可以提供客户端证书，而不是客户端密码。 以下代码片段演示如何使用存储在 Azure Key Vault 中的证书。

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-app-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common"

   // To call an API
   "ClientCertificates": [
      {
        "SourceType": "KeyVault",
        "KeyVaultUrl": "https://msidentitywebsamples.vault.azure.net",
        "KeyVaultCertificateName": "MicrosoftIdentitySamplesCert"
      }
   ]
  },
  "GraphBeta": {
    "BaseUrl": "https://graph.microsoft.com/beta",
    "Scopes": "user.read"
  }
}
```

Microsoft.Identity.Web 提供了多种通过配置或代码描述证书的方法。 有关详细信息，请参阅 GitHub 上的 [Microsoft.Identity.Web - 使用证书](https://github.com/AzureAD/microsoft-identity-web/wiki/Using-certificates)。

## <a name="startupcs"></a>Startup.cs

Web 应用将需要获取下游 API 的令牌。 可通过在 `.AddMicrosoftIdentityWebApi(Configuration)` 后面添加 `.EnableTokenAcquisitionToCallDownstreamApi()` 行来指定它。 此行公开 `ITokenAcquisition` 服务，可在控制器和页面操作中使用该服务。 不过，正如你将在以下两个选项中看到的那样，可以更简单地执行此操作。 还需要在 Startup.cs 中选择令牌缓存实现，例如 `.AddInMemoryTokenCaches()`：

   ```csharp
   using Microsoft.Identity.Web;

   public class Startup
   {
     // ...
     public void ConfigureServices(IServiceCollection services)
     {
     // ...
     services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
             .AddMicrosoftIdentityWebApp(Configuration, Configuration.GetSection("AzureAd"))
               .EnableTokenAcquisitionToCallDownstreamApi(new string[]{"user.read" })
               .AddInMemoryTokenCaches();
      // ...
     }
     // ...
   }
   ```

传递给 `EnableTokenAcquisitionToCallDownstreamApi` 的范围是可选项，使 Web 应用能够请求范围并在用户登录时向用户征求对这些范围的许可。 如果未指定范围，Microsoft.Identity.Web 会启用增量许可体验。

如果你不想自行获取令牌，可以通过 Microsoft.Identity.Web 提供的两种机制从 Web 应用调用 Web API。 选择哪种机制取决于你是要调用 Microsoft Graph 还是调用另一个 API。

### <a name="option-1-call-microsoft-graph"></a>选项 1：调用 Microsoft Graph

如果要调用 Microsoft Graph，则可通过 Microsoft.Identity.Web 在 API 操作中直接使用 `GraphServiceClient`（由 Microsoft Graph SDK 公开）。 若要公开 Microsoft Graph，请执行以下操作：

1. 将 [Microsoft.Identity.Web.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) NuGet 包添加到项目。
1. 在 Startup.cs 文件的 `.EnableTokenAcquisitionToCallDownstreamApi()` 后面添加 `.AddMicrosoftGraph()`。 `.AddMicrosoftGraph()` 具有多个重写。 使用将配置部分作为参数的重写，代码变为：

   ```csharp
   using Microsoft.Identity.Web;

   public class Startup
   {
     // ...
     public void ConfigureServices(IServiceCollection services)
     {
     // ...
     services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
             .AddMicrosoftIdentityWebApp(Configuration, Configuration.GetSection("AzureAd"))
               .EnableTokenAcquisitionToCallDownstreamApi(new string[]{"user.read" })
                  .AddMicrosoftGraph(Configuration.GetSection("GraphBeta"))
               .AddInMemoryTokenCaches();
      // ...
     }
     // ...
   }
   ```

### <a name="option-2-call-a-downstream-web-api-other-than-microsoft-graph"></a>选项 2：调用下游 Web API 而不是 Microsoft Graph

为了调用 Web API 而不是 Microsoft Graph，Microsoft.Identity.Web 提供了 `.AddDownstreamWebApi()`，它可请求令牌并调用下游 Web API。

   ```csharp
   using Microsoft.Identity.Web;

   public class Startup
   {
     // ...
     public void ConfigureServices(IServiceCollection services)
     {
     // ...
     services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
             .AddMicrosoftIdentityWebApp(Configuration, "AzureAd")
               .EnableTokenAcquisitionToCallDownstreamApi(new string[]{"user.read" })
                  .AddDownstreamWebApi("MyApi", Configuration.GetSection("GraphBeta"))
               .AddInMemoryTokenCaches();
      // ...
     }
     // ...
   }
   ```

### <a name="summary"></a>总结

与 Web API 一样，你可以选择各种令牌缓存实现。 有关详细信息，请参阅 GitHub 上的 [Microsoft.Identity.Web - 令牌缓存序列化](https://aka.ms/ms-id-web/token-cache-serialization)。

下图显示 Microsoft.Identity.Web 的各种可能性及其对 Startup.cs 文件的影响 ：

:::image type="content" source="media/scenarios/microsoft-identity-web-startup-cs.svg" alt-text="此框图显示了 Startup.cs 中的服务配置选项，用于调用 Web API 和指定令牌缓存实现":::

> [!NOTE]
> 若要完全理解以下代码示例，请熟悉 [ASP.NET Core 基础知识](/aspnet/core/fundamentals)，尤其是[依赖项注入](/aspnet/core/fundamentals/dependency-injection)和[选项](/aspnet/core/fundamentals/configuration/options)。

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

由于用户登录已委托给 OpenID Connect (OIDC) 中间件，因此你必须与 OIDC 进程交互。 具体交互方式取决于你使用的框架。

对于 ASP.NET，你将订阅中间件 OIDC 事件：

- 你会让 ASP.NET Core 通过 OpenID Connect 中间件来请求获取授权代码。 ASP.NET 或 ASP.NET Core 会让用户登录和同意。
- 你将订阅 Web 应用来接收授权代码。 此订阅是通过使用 C# 委派来完成的。
- 收到授权代码后，你将使用 MSAL 库来兑换它。 生成的访问令牌和刷新令牌存储在令牌缓存中。 可以在应用的其他部分（如控制器）中使用缓存，从而以静默方式获取其他令牌。

本文以及下一篇文章中的代码示例都是从 [ASP.NET Web 应用示例](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)中提取的。 不妨参阅相应示例来获取完整的实现详细信息。

# <a name="java"></a>[Java](#tab/java)

本文以及下一篇文章中的代码示例都是从使用适用于 Java 的 MSAL 的 Web 应用示例[调用 Microsoft Graph 的 Java Web 应用](https://github.com/Azure-Samples/ms-identity-java-webapp)中提取的。
此示例当前让适用于 Java 的 MSAL 生成授权代码 URL，并处理导航到 Microsoft 标识平台的授权终结点。 也可以使用冲刺 (sprint) 安全机制来登录用户。 不妨参阅相应示例来获取完整的实现详细信息。

# <a name="python"></a>[Python](#tab/python)

本文以及下一篇文章中的代码示例都是从使用 MSAL.Python 的 Web 应用示例[调用 Microsoft Graph 的 Python Web 应用](https://github.com/Azure-Samples/ms-identity-python-webapp)中提取的。
此示例当前让 MSAL.Python 生成授权代码 URL，并处理导航到 Microsoft 标识平台的授权终结点。 不妨参阅相应示例来获取完整的实现详细信息。

---

## <a name="code-that-redeems-the-authorization-code"></a>兑换授权代码的代码

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Microsoft.Identity.Web 通过设置正确的 OpenID Connect 设置、订阅代码接收的事件和兑换代码来简化代码。 兑换授权代码不需要其他代码。 请参阅 [Microsoft.Identity.Web 源代码](https://github.com/AzureAD/microsoft-identity-web/blob/c29f1a7950b940208440bebf0bcb524a7d6bee22/src/Microsoft.Identity.Web/WebAppExtensions/WebAppCallsWebApiAuthenticationBuilderExtensions.cs#L140)，详细了解其原理。

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET 处理代码的方式与 ASP.NET Core 类似，不同之处在于配置 OpenID Connect 和订阅 [App_Start\Startup.Auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs) 文件中发生的 `OnAuthorizationCodeReceived` 事件。 概念也类似于 ASP.NET Core 中的概念，不同之处是在 ASP.NET 中，必须在 [Web.config # L15](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Web.config#L15) 中指定 `RedirectUri`。 此配置的可靠性比 ASP.NET Core 中的低一点，因为在部署应用时需要更改它。

下面是 Startup.Auth.cs 的代码：

```csharp
public partial class Startup
{
  public void ConfigureAuth(IAppBuilder app)
  {
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    // Custom middleware initialization. This is activated when the code obtained from a code_grant is present in the query string (&code=<code>).
    app.UseOAuth2CodeRedeemer(
        new OAuth2CodeRedeemerOptions
        {
            ClientId = AuthenticationConfig.ClientId,
            ClientSecret = AuthenticationConfig.ClientSecret,
            RedirectUri = AuthenticationConfig.RedirectUri
        }
      );

  app.UseOpenIdConnectAuthentication(
      new OpenIdConnectAuthenticationOptions
      {
        // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0.
        Authority = AuthenticationConfig.Authority,
        ClientId = AuthenticationConfig.ClientId,
        RedirectUri = AuthenticationConfig.RedirectUri,
        PostLogoutRedirectUri = AuthenticationConfig.RedirectUri,
        Scope = AuthenticationConfig.BasicSignInScopes + " Mail.Read", // A basic set of permissions for user sign-in and profile access "openid profile offline_access"
        TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = false,
            // In a real application, you would use IssuerValidator for additional checks, such as making sure the user's organization has signed up for your app.
            //     IssuerValidator = (issuer, token, tvp) =>
            //     {
            //        //if(MyCustomTenantValidation(issuer))
            //        return issuer;
            //        //else
            //        //    throw new SecurityTokenInvalidIssuerException("Invalid issuer");
            //    },
            //NameClaimType = "name",
        },
        Notifications = new OpenIdConnectAuthenticationNotifications()
        {
            AuthorizationCodeReceived = OnAuthorizationCodeReceived,
            AuthenticationFailed = OnAuthenticationFailed,
        }
      });
  }

  private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification context)
  {
      // Upon successful sign-in, get the access token and cache it by using MSAL.
      IConfidentialClientApplication clientApp = MsalAppBuilder.BuildConfidentialClientApplication(new ClaimsPrincipal(context.AuthenticationTicket.Identity));
      AuthenticationResult result = await clientApp.AcquireTokenByAuthorizationCode(new[] { "Mail.Read" }, context.Code).ExecuteAsync();
  }

  private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
  {
      notification.HandleResponse();
      notification.Response.Redirect("/Error?message=" + notification.Exception.Message);
      return Task.FromResult(0);
  }
}
```

# <a name="java"></a>[Java](#tab/java)

若要了解 Java 示例如何获取授权代码，请参阅[用于登录用户的 Web 应用：代码配置](scenario-web-app-sign-user-app-configuration.md?tabs=java#initialization-code)。 在应用收到授权代码后，[AuthFilter.java#L51-L56](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java#L51-L56)：

1. 将授权代码委派给 [AuthHelper.java#L67-L97](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L67-L97) 中的 `AuthHelper.processAuthenticationCodeRedirect` 方法。
1. 调用 `getAuthResultByAuthCode`。

```Java
class AuthHelper {
  // Code omitted
  void processAuthenticationCodeRedirect(HttpServletRequest httpRequest, String currentUri, String fullUrl)
            throws Throwable {

  // Code omitted
  AuthenticationResponse authResponse = AuthenticationResponseParser.parse(new URI(fullUrl), params);

  // Code omitted
  IAuthenticationResult result = getAuthResultByAuthCode(
                    httpRequest,
                    oidcResponse.getAuthorizationCode(),
                    currentUri);

// Code omitted
  }
}
```

`getAuthResultByAuthCode` 方法是在 [AuthHelper.java#L176](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L176) 中定义。 它创建 MSAL `ConfidentialClientApplication`，然后使用通过授权代码创建的 `AuthorizationCodeParameters` 来调用 `acquireToken()`。

```Java
   private IAuthenticationResult getAuthResultByAuthCode(
            HttpServletRequest httpServletRequest,
            AuthorizationCode authorizationCode,
            String currentUri) throws Throwable {

        IAuthenticationResult result;
        ConfidentialClientApplication app;
        try {
            app = createClientApplication();

            String authCode = authorizationCode.getValue();
            AuthorizationCodeParameters parameters = AuthorizationCodeParameters.builder(
                    authCode,
                    new URI(currentUri)).
                    build();

            Future<IAuthenticationResult> future = app.acquireToken(parameters);

            result = future.get();
        } catch (ExecutionException e) {
            throw e.getCause();
        }

        if (result == null) {
            throw new ServiceUnavailableException("authentication result was null");
        }

        SessionManagementHelper.storeTokenCacheInSession(httpServletRequest, app.tokenCache().serialize());

        return result;
    }

    private ConfidentialClientApplication createClientApplication() throws MalformedURLException {
        return ConfidentialClientApplication.builder(clientId, ClientCredentialFactory.create(clientSecret)).
                authority(authority).
                build();
    }
```

# <a name="python"></a>[Python](#tab/python)

请求执行授权代码流，如[用于登录用户的 Web 应用：代码配置](scenario-web-app-sign-user-app-configuration.md?tabs=python#initialization-code)中所述。 然后，在 `authorized` 函数上收到代码，Flask 从 `/getAToken` URL 路由此代码。 有关此代码的完整上下文，请参阅 [app.py#L30-L44](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L30-L44)：

```python
 @app.route("/getAToken")  # Its absolute URL must match your app's redirect_uri set in AAD.
def authorized():
    if request.args['state'] != session.get("state"):
        return redirect(url_for("login"))
    cache = _load_cache()
    result = _build_msal_app(cache).acquire_token_by_authorization_code(
        request.args['code'],
        scopes=app_config.SCOPE,  # Misspelled scope would cause an HTTP 400 error here.
        redirect_uri=url_for("authorized", _external=True))
    if "error" in result:
        return "Login failure: %s, %s" % (
            result["error"], result.get("error_description"))
    session["user"] = result.get("id_token_claims")
    _save_cache(cache)
    return redirect(url_for("index"))
```

---

保密客户端应用还可以使用客户端证书或客户端断言（而不是客户端密码）来证明其标识。
使用客户端断言是一种高级方案，[客户端断言](msal-net-client-assertions.md)中对此进行了详细说明。

## <a name="token-cache"></a>令牌缓存

> [!IMPORTANT]
> Web 应用或 Web API 的令牌缓存实现不同于桌面应用的实现，后者通常是[基于文件](msal-net-token-cache-serialization.md)的。
> 出于安全性和性能方面的原因，请务必确保对于 Web 应用和 Web API，每个用户帐户都有一个令牌缓存。 必须为每个帐户序列化令牌缓存。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core 教程使用依赖关系注入，让你能够在应用的 Startup.cs 文件中确定令牌缓存实现。 Microsoft.Identity.Web 随附了[令牌缓存序列化](msal-net-token-cache-serialization.md)中所述的预生成令牌缓存序列化程序。 一个有意思的地方是，可以选择 ASP.NET Core [分布式内存缓存](/aspnet/core/performance/caching/distributed#distributed-memory-cache)：

```csharp
// Use a distributed token cache by adding:
    services.AddMicrosoftIdentityWebAppAuthentication(Configuration, "AzureAd")
            .EnableTokenAcquisitionToCallDownstreamApi(
                initialScopes: new string[] { "user.read" })
            .AddDistributedTokenCaches();

// Then, choose your implementation.
// For instance, the distributed in-memory cache (not cleared when you stop the app):
services.AddDistributedMemoryCache();

// Or a Redis cache:
services.AddStackExchangeRedisCache(options =>
{
 options.Configuration = "localhost";
 options.InstanceName = "SampleInstance";
});

// Or even a SQL Server token cache:
services.AddDistributedSqlServerCache(options =>
{
 options.ConnectionString = _config["DistCache_ConnectionString"];
 options.SchemaName = "dbo";
 options.TableName = "TestCache";
});
```

有关令牌缓存提供程序的详细信息，另请参阅 Microsoft.Identity.Web 的[令牌缓存序列化](https://aka.ms/ms-id-web/token-cache-serialization)一文，以及 Web 应用教程的 [ASP.NET Core Web 应用教程 | 令牌缓存](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache)部分。

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Web 应用或 Web API 的令牌缓存实现不同于桌面应用的实现，后者通常是[基于文件](msal-net-token-cache-serialization.md)的。

Web 应用实现可以使用 ASP.NET 会话或服务器内存。 例如，了解在 [MsalAppBuilder.cs#L39-L51](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/79e3e1f084cd78f9170a8ca4077869f217735a1a/WebApp/Utils/MsalAppBuilder.cs#L57-L58) 中创建 MSAL.NET 应用之后缓存实现是如何挂钩的：


首先，若要使用这些实现，需执行以下操作：
- 添加 Microsoft.Identity.Web NuGet 包。 这些令牌缓存序列化程序不会直接引入 MSAL.NET，从而避免了不需要的依赖项。 除了更高级别的 ASP.NET Core，Microsoft.Identity.Web 还引入了作为 MSAL.NET 的帮助程序的类。 
- 在代码中，使用 Microsoft.Identity.Web 命名空间：

  ```csharp
  #using Microsoft.Identity.Web
  ```
- 生成机密客户端应用程序后，添加选择的令牌缓存序列化。

```csharp
public static class MsalAppBuilder
{
  private static IConfidentialClientApplication clientapp;

  public static IConfidentialClientApplication BuildConfidentialClientApplication()
  {
    if (clientapp == null)
    {
      clientapp = ConfidentialClientApplicationBuilder.Create(AuthenticationConfig.ClientId)
            .WithClientSecret(AuthenticationConfig.ClientSecret)
            .WithRedirectUri(AuthenticationConfig.RedirectUri)
            .WithAuthority(new Uri(AuthenticationConfig.Authority))
            .Build();

      // After the ConfidentialClientApplication is created, we overwrite its default UserTokenCache serialization with our implementation
      clientapp.AddInMemoryTokenCache();
    }
    return clientapp;
  }
```

除了 `clientapp.AddInMemoryTokenCache()`，还可使用更高级的缓存序列化实现，如 Redis、SQL、CosmosDB 或分布式内存。 下面是针对 Redis 的示例：

```csharp
  clientapp.AddDistributedTokenCache(services =>
  {
    services.AddStackExchangeRedisCache(options =>
    {
        options.Configuration = "localhost";
        options.InstanceName = "SampleInstance";
    });
  });
```

有关详细信息，请参阅 [MSAL.NET 中的令牌缓存序列化](./msal-net-token-cache-serialization.md)。

# <a name="java"></a>[Java](#tab/java)

MSAL Java 提供了用于序列化和反序列化令牌缓存的方法。 Java 示例处理来自会话的序列化，如 [AuthHelper.java#L99-L122](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L99-L122) 中的 `getAuthResultBySilentFlow` 方法所示：

```Java
IAuthenticationResult getAuthResultBySilentFlow(HttpServletRequest httpRequest, HttpServletResponse httpResponse)
      throws Throwable {

  IAuthenticationResult result =  SessionManagementHelper.getAuthSessionObject(httpRequest);

  IConfidentialClientApplication app = createClientApplication();

  Object tokenCache = httpRequest.getSession().getAttribute("token_cache");
  if (tokenCache != null) {
      app.tokenCache().deserialize(tokenCache.toString());
  }

  SilentParameters parameters = SilentParameters.builder(
          Collections.singleton("User.Read"),
          result.account()).build();

  CompletableFuture<IAuthenticationResult> future = app.acquireTokenSilently(parameters);
  IAuthenticationResult updatedResult = future.get();

  // Update session with latest token cache.
  SessionManagementHelper.storeTokenCacheInSession(httpRequest, app.tokenCache().serialize());

  return updatedResult;
}
```

[适用于 Java 的 MSAL 示例](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/SessionManagementHelper.java)中提供了 `SessionManagementHelper` 类的详细信息。

# <a name="python"></a>[Python](#tab/python)

在 Python 示例中，通过为每个请求重新创建保密客户端应用，然后在 Flask 会话缓存中序列化它，确保每个帐户都有一个缓存：

```python
from flask import Flask, render_template, session, request, redirect, url_for
from flask_session import Session  # https://pythonhosted.org/Flask-Session
import msal
import app_config


app = Flask(__name__)
app.config.from_object(app_config)
Session(app)

# Code omitted here for simplicity

def _load_cache():
    cache = msal.SerializableTokenCache()
    if session.get("token_cache"):
        cache.deserialize(session["token_cache"])
    return cache

def _save_cache(cache):
    if cache.has_state_changed:
        session["token_cache"] = cache.serialize()

def _build_msal_app(cache=None):
    return msal.ConfidentialClientApplication(
        app_config.CLIENT_ID, authority=app_config.AUTHORITY,
        client_credential=app_config.CLIENT_SECRET, token_cache=cache)
```

---

## <a name="next-steps"></a>后续步骤

此时，当用户登录时，令牌存储在令牌缓存中。 让我们来看看随后是如何在 Web 应用的其他部分中使用它的。

[在全局注销时从缓存中删除帐户](scenario-web-app-call-api-sign-in.md)