---
title: 令牌缓存序列化 (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: 了解如何使用适用于 .NET 的 Microsoft 身份验证库 (MSAL.NET) 对令牌缓存进行序列化和自定义序列化。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2021
ms.author: jmprieur
ms.reviewer: mmacy
ms.custom: devx-track-csharp, aaddev, has-adal-ref
ms.openlocfilehash: 216fd3f132464b9866bc1f3b1b61b143de117019
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2021
ms.locfileid: "129083460"
---
# <a name="token-cache-serialization-in-msalnet"></a>MSAL.NET 中的令牌缓存序列化

[获取令牌](msal-acquire-cache-tokens.md)后，Microsoft 身份验证库 (MSAL) 将缓存该令牌。 公共客户端应用程序（桌面/移动应用）应该先尝试从缓存中获取令牌，然后再通过其他方法获取令牌。 机密客户端应用程序中的令牌获取方法可以自行管理缓存。 本文介绍 MSAL.NET 中令牌缓存的默认序列化和自定义序列化。

## <a name="quick-summary"></a>快速摘要

建议如下：
- 在 Web 应用和 Web API 中，使用[“Microsoft.Identity.Web”中的令牌缓存序列化程序](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization)。 它们甚至提供分布式数据库或缓存系统来存储令牌。
  - 在 ASP.NET Core [Web 应用](scenario-web-app-call-api-overview.md)和 [Web API](scenario-web-api-call-api-overview.md) 中，使用 Microsoft.Identity.Web 作为 ASP.NET Core 中的较高级 API。
  - 在 ASP.NET 经典版、.NET Core 和 .NET Framework 中，直接将 MSAL.NET 与 Microsoft.Identity.Web 中提供的[适用于 MSAL 的令牌缓存序列化适配器]()配合使用。 
- 在桌面应用程序（可以使用文件系统来存储令牌）中，将 [Microsoft.Identity.Client.Extensions.Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/wiki/Cross-platform-Token-Cache) 与 MSAL.Net 配合使用。
- 在移动应用程序（Xamarin.iOS、Xamarin.Android、通用 Windows 平台）中，不要执行任何操作，因为 MSAL.NET 会为你处理缓存：这些平台具有安全的存储。

## <a name="aspnet-core-web-apps-and-web-apis"></a>[ASP.NET Core Web 应用和 Web API](#tab/aspnetcore)

[Microsoft.Identity.Web](https://github.com/AzureAD/microsoft-identity-web) 库提供了包含令牌缓存序列化的 NuGet 包 [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web)：

| 扩展方法 | 说明  |
| ---------------- | ------------ |
| `AddInMemoryTokenCaches` | 内存中令牌缓存序列化。 此实现在示例中非常有用。 如果你不介意令牌缓存在 Web 应用重启后会丢失这一情况，那么它也适用于生产应用程序。 `AddInMemoryTokenCaches` 采用类型为 `MsalMemoryTokenCacheOptions` 的可选参数，该参数用于指定缓存项在未使用的情况下过期前的持续时间。
| `AddSessionTokenCaches` | 令牌缓存将绑定到用户会话。 如果 ID 令牌包含许多声明，则此选项不是理想的选择，因为 Cookie 会变得太大。
| `AddDistributedTokenCaches` | 令牌缓存是针对 ASP.NET Core `IDistributedCache` 实现的适配器，因此允许你在分布式内存缓存、Redis 缓存、分布式 NCache 或 SQL Server 缓存之间进行选择。 有关 `IDistributedCache` 实现的详细信息，请参阅[分布式内存缓存](/aspnet/core/performance/caching/distributed)。


以下代码示例在 ASP.NET Core 应用程序的 [Startup](/aspnet/core/fundamentals/startup) 类的 [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) 方法中使用内存中缓存：

```CSharp
#using Microsoft.Identity.Web
```

```CSharp
using Microsoft.Identity.Web;

public class Startup
{
 const string scopesToRequest = "user.read";
  
  public void ConfigureServices(IServiceCollection services)
  {
   // code before
   services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
           .AddMicrosoftIdentityWebApp(Configuration)
             .EnableTokenAcquisitionToCallDownstreamApi(new string[] { scopesToRequest })
                .AddInMemoryTokenCaches();
   // code after
  }
  // code after
}
```

从缓存的角度看，在 ASP.NET Core Web API 中可以运行与上面类似的代码


下面是可能的分布式缓存示例：

```C#
// or use a distributed Token Cache by adding
   services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
           .AddMicrosoftIdentityWebApp(Configuration)
             .EnableTokenAcquisitionToCallDownstreamApi(new string[] { scopesToRequest }
               .AddDistributedTokenCaches();

// and then choose your implementation of distributed cache

// For instance the distributed in memory cache (not cleared when you stop the app)
services.AddDistributedMemoryCache();

// Or a Redis cache
// Requires the Microsoft.Extensions.Caching.StackExchangeRedis NuGet package
services.AddStackExchangeRedisCache(options =>
{
 options.Configuration = "localhost";
 options.InstanceName = "SampleInstance";
});

// Or even a SQL Server token cache
// Requires the Microsoft.Extensions.Caching.SqlServer NuGet package
services.AddDistributedSqlServerCache(options =>
{
 options.ConnectionString = _config["DistCache_ConnectionString"];
 options.SchemaName = "dbo";
 options.TableName = "TestCache";
});

// Or a Cosmos DB cache
// Requires the Microsoft.Extensions.Caching.Cosmos NuGet package
services.AddCosmosCache((CosmosCacheOptions cacheOptions) =>
{
    cacheOptions.ContainerName = Configuration["CosmosCacheContainer"];
    cacheOptions.DatabaseName = Configuration["CosmosCacheDatabase"];
    cacheOptions.ClientBuilder = new CosmosClientBuilder(Configuration["CosmosConnectionString"]);
    cacheOptions.CreateIfNotExists = true;
});
```

它们的用法在 [ASP.NET Core Web 应用教程](/aspnet/core/tutorials/first-mvc-app/)的 [2-2 令牌缓存](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache)阶段中进行了说明。

## <a name="non-aspnet-core-web-apps-and-web-apis"></a>[非 ASP.NET Core Web 应用和 Web API](#tab/aspnet)

即使使用的是 MSAL.NET，也能够受益于 Microsoft.Identity.Web 中引入的令牌缓存序列化 

### <a name="referencing-the-nuget-package"></a>引用 NuGet 包

除 MSAL.NET 以外，再将 [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web) NuGet 包添加到你的项目

### <a name="configuring-the-token-cache"></a>配置令牌缓存

以下代码演示如何将适当分区的内存中令牌缓存添加到应用。

```CSharp
using Microsoft.Identity.Web;
using Microsoft.Identity.Client;
using Microsoft.Extensions.DependencyInjection;
```

```CSharp

public static async Task<AuthenticationResult> GetTokenAsync(string clientId, X509Certificate cert, string authority, string[] scopes)
 {
     // Create the confidential client application
     app= ConfidentialClientApplicationBuilder.Create(clientId)
       // Alternatively to the certificate you can use .WithClientSecret(clientSecret)
       .WithCertificate(cert)
       .WithLegacyCacheCompatibility(false)
       .WithAuthority(authority)
       .Build();

     // Add a static in-memory token cache. Other options available: see below
     app.AddInMemoryTokenCache();  // Microsoft.Identity.Web 1.16+
   
     // Make the call to get a token for client_credentials flow (app to app scenario) 
     return await app.AcquireTokenForClient(scopes).ExecuteAsync();
     
     // OR Make the call to get a token for OBO (web api scenario)
     return await app.AcquireTokenOnBehalfOf(scopes, userAssertion).ExecuteAsync();
     
     // OR Make the call to get a token via auth code (web app scenario)
     return await app.AcquireTokenByAuthorizationCode(scopes, authCode);    
     
     // OR, when the user has previously logged in, get a token silently
     var homeAccountId = GetHomeAccountIdFromClaimsPrincipal(); // uid and utid claims
     var account = await app.GetAccountAsync(homeAccountId);
     try
     {
          return await app.AcquireTokenSilent(scopes, account).ExecuteAsync();; 
     } 
     catch (MsalUiRequiredException)
     {
        // cannot get a token silently, so redirect the user to be challenged 
     }
  }
```

### <a name="available-caching-technologies"></a>可用缓存技术

你可以使用不同的缓存技术，包括 .NET 提供的分布式令牌缓存，而不是使用 `app.AddInMemoryTokenCache();`。

#### <a name="in-memory-token-cache"></a>内存中令牌缓存

示例的内存中令牌缓存序列化非常有用。 如果你不介意令牌缓存在 Web 应用重启后会丢失这一情况，那么它也适用于生产应用程序。

```CSharp 
     // Add an in-memory token cache
     app.AddInMemoryTokenCache();
```

#### <a name="distributed-caches"></a>分布式缓存

如果使用 `app.AddDistributedTokenCache`，则令牌缓存是针对 .NET `IDistributedCache` 实现的适配器，因此允许在分布式内存缓存、Redis 缓存、CosmosDb 或 SQL Server 缓存之间进行选择。 有关 `IDistributedCache` 实现的详细信息，请参阅[分布式内存缓存](/aspnet/core/performance/caching/distributed)。

##### <a name="distributed-in-memory-token-cache"></a>分布式内存中令牌缓存

```CSharp 
     // In memory distributed token cache
     app.AddDistributedTokenCache(services =>
     {
       // In net462/net472, requires to reference Microsoft.Extensions.Caching.Memory
       services.AddDistributedMemoryCache();
     });
```

##### <a name="sql-server"></a>SQL Server

```CSharp 
     // SQL Server token cache
     app.AddDistributedTokenCache(services =>
     {
      services.AddDistributedSqlServerCache(options =>
      {
       // In net462/net472, requires to reference Microsoft.Extensions.Caching.Memory

       // Requires to reference Microsoft.Extensions.Caching.SqlServer
       options.ConnectionString = @"Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=TestCache;Integrated Security=True;Connect Timeout=30;Encrypt=False;TrustServerCertificate=False;ApplicationIntent=ReadWrite;MultiSubnetFailover=False";
       options.SchemaName = "dbo";
       options.TableName = "TestCache";

       // You don't want the SQL token cache to be purged before the access token has expired. Usually
       // access tokens expire after 1 hour (but this can be changed by token lifetime policies), whereas
       // the default sliding expiration for the distributed SQL database is 20 mins. 
       // Use a value which is above 60 mins (or the lifetime of a token in case of longer lived tokens)
       options.DefaultSlidingExpiration = TimeSpan.FromMinutes(90);
      });
     });
```

##### <a name="redis-cache"></a>Redis 缓存

```CSharp 
     // Redis token cache
     app.AddDistributedTokenCache(services =>
     {
       // Requires to reference Microsoft.Extensions.Caching.StackExchangeRedis
       services.AddStackExchangeRedisCache(options =>
       {
         options.Configuration = "localhost";
         options.InstanceName = "Redis";
       });
      });
```

如果发现令牌获取偶尔需要花费与 Redis 缓存超时一样长的时间，请参阅[禁用缓存同步](#disabling-cache-synchronization)。 

##### <a name="cosmos-db"></a>Cosmos DB

```CSharp 
      // Cosmos DB token cache
      app.AddDistributedTokenCache(services =>
      {
        // Requires to reference Microsoft.Extensions.Caching.Cosmos
        services.AddCosmosCache((CosmosCacheOptions cacheOptions) =>
        {
          cacheOptions.ContainerName = Configuration["CosmosCacheContainer"];
          cacheOptions.DatabaseName = Configuration["CosmosCacheDatabase"];
          cacheOptions.ClientBuilder = new CosmosClientBuilder(Configuration["CosmosConnectionString"]);
          cacheOptions.CreateIfNotExists = true;
        });
       });
```

### <a name="disabling-legacy-token-cache"></a>禁用旧式令牌缓存

MSAL 专门提供一些内部代码来实现与旧式 ADAL 缓存的交互。 未同时使用 MSAL 和 ADAL（因而未使用旧式缓存）时，相关的旧式缓存代码不是必要的。 MSAL [4.25.0](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/releases/tag/4.25.0) 增加了禁用旧式 ADAL 缓存代码的功能，并提高了缓存使用性能。 有关禁用旧式缓存之前和之后的性能比较，请参阅拉取请求 [#2309](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/pull/2309)。 按如下所示针对应用程序生成器调用 `.WithLegacyCacheCompatibility(false)`。

```csharp
var app = ConfidentialClientApplicationBuilder
    .Create(clientId)
    .WithClientSecret(clientSecret)
    .WithLegacyCacheCompatibility(false)
    .Build();
```

### <a name="disabling-cache-synchronization"></a>禁用缓存同步

默认情况下，MSAL 将在每次缓存读取和缓存写入之间锁定机密客户端应用程序级别的缓存访问。 如果缓存序列化程序需要很长时间才会发生超时（Redis 缓存可能会出现这种情况），此锁定可能会成为一个问题。 可以将 `WithCacheSynchronization` 标志设置为 false，以启用乐观缓存锁定策略，这可能会导致更好的性能，尤其是在跨请求重用 ConfidentialClientApplication 对象的情况下。 

```csharp
var app = ConfidentialClientApplicationBuilder
    .Create(clientId)
    .WithClientSecret(clientSecret)
    .WithCacheSynchronization(false)
    .Build();
```

### <a name="monitor-cache-hit-ratios-and-cache-performance"></a>监视缓存命中率和缓存性能

MSAL 公开重要指标作为 [AuthenticationResult.AuthenticationResultMetadata](/dotnet/api/microsoft.identity.client.authenticationresultmetadata) 对象的一部分： 

| 指标       | 含义     | 何时触发警报？    |
| :-------------: | :----------: | :-----------: |
|  `DurationTotalInMs` | MSAL 中花费的总时间，包括网络调用和缓存   | 针对整体高延迟(> 1s)的警报。 值取决于令牌源。 从缓存：一个缓存访问。 从 AAD：两个缓存访问 + 一个 HTTP 调用。 第一次调用（每进程）需要更长的时间，因为有一个额外的 HTTP 调用。 |
|  `DurationInCacheInMs` | 加载或保存令牌缓存所用的时间（由应用开发人员自定义）（例如，保存到 Redis）。| 对高峰发出警报。 |
|  `DurationInHttpInMs`| 向 AAD 发出 HTTP 调用所花的时间。  | 对高峰发出警报。|
|  `TokenSource` | 指示令牌的源。 从缓存中检索令牌的速度要快得多（例如，~100 ms 与 ~700 ms）。 可用于监视缓存命中率并发出警报。 | 与 `DurationTotalInMs` 结合使用 |

### <a name="samples"></a>示例

- [ConfidentialClientTokenCache](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/tree/master/ConfidentialClientTokenCache) 示例中演示了如何在 .NET Framework 和 .NET Core 应用程序中使用令牌缓存序列化程序 
- 以下示例是使用相同技术的 ASP.NET Web 应用： https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect (参阅 [WebApp/Utils/MsalAppBuilder.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Utils/MsalAppBuilder.cs)

## <a name="desktop-apps"></a>[桌面应用](#tab/desktop)

在桌面应用程序中，建议使用跨平台令牌缓存。

#### <a name="cross-platform-token-cache-msal-only"></a>跨平台令牌缓存（仅适用于 MSAL）

MSAL.NET 单独在名为 Microsoft.Identity.Client.Extensions.Msal 的库中提供了跨平台令牌缓存，在 https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet 中可找到其源代码。

##### <a name="referencing-the-nuget-package"></a>引用 NuGet 包

将 [Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/) NuGet 包添加到你的项目。

##### <a name="configuring-the-token-cache"></a>配置令牌缓存

有关详细信息，请参阅https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/wiki/Cross-platform-Token-Cache。 下面是跨平台令牌缓存的用法示例。

```csharp
 var storageProperties =
     new StorageCreationPropertiesBuilder(Config.CacheFileName, Config.CacheDir)
     .WithLinuxKeyring(
         Config.LinuxKeyRingSchema,
         Config.LinuxKeyRingCollection,
         Config.LinuxKeyRingLabel,
         Config.LinuxKeyRingAttr1,
         Config.LinuxKeyRingAttr2)
     .WithMacKeyChain(
         Config.KeyChainServiceName,
         Config.KeyChainAccountName)
     .Build();

 IPublicClientApplication pca = PublicClientApplicationBuilder.Create(clientId)
    .WithAuthority(Config.Authority)
    .WithRedirectUri("http://localhost")  // make sure to register this redirect URI for the interactive login 
    .Build();
    

// This hooks up the cross-platform cache into MSAL
var cacheHelper = await MsalCacheHelper.CreateAsync(storageProperties );
cacheHelper.RegisterCache(pca.UserTokenCache);
         
```

## <a name="mobile-apps"></a>[移动应用](#tab/mobile)

在 MSAL.NET 中，默认会提供内存中令牌缓存。 对于可提供安全存储供用户使用的平台，默认会提供序列化：通用 Windows 平台 (UWP)、Xamarin.iOS 和 Xamarin.Android。

## <a name="write-your-own-cache"></a>[编写自己的缓存](#tab/custom)

如果你确实想要编写自己的令牌缓存序列化程序，可以使用 MSAL.NET 在 .NET Framework 和 .NET Core 子平台中提供的自定义令牌缓存序列化。 访问缓存时会触发事件，应用可以选择是对缓存进行序列化还是反序列化。 在处理用户的机密客户端应用程序（登录用户并调用 Web API 的 Web 应用程序，以及调用下游 Web API 的 Web API）中，可能会有许多用户，并且会对这些用户进行并行处理。 出于安全和性能方面的原因，我们建议为每个用户序列化一个缓存。 序列化事件基于已处理用户的标识来计算缓存密钥，并对该用户的令牌缓存进行序列化/反序列化操作。

请记住，自定义序列化不适用于移动平台（UWP、Xamarin.iOS 和 Xamarin.Android）。 MSAL 已经为这些平台定义了安全且高效的序列化机制。 但是，.NET 桌面和 .NET Core 应用程序使用不同的体系结构，而 MSAL 无法实现通用的序列化机制。 例如，网站可能会选择在 Redis 缓存中存储令牌，而桌面应用在加密的文件中存储令牌。 因此，提供的序列化方法并不是按原样使用的。 若要在 .NET 桌面或 .NET Core 中使用持久的令牌缓存应用程序，请自定义序列化。

令牌缓存序列化中使用以下类和接口：

- `ITokenCache`，定义用于订阅令牌缓存序列化请求的事件，以及用于序列化或反序列化采用各种格式的缓存的方法（ADAL v3.0、MSAL 2.x 和 MSAL 3.x = ADAL v5.0）。
- `TokenCacheCallback` 是传递给事件的回调，可让你处理序列化。 将结合 `TokenCacheNotificationArgs` 类型的参数调用它们。
- `TokenCacheNotificationArgs` 仅提供应用程序的 `ClientId`，是对该令牌适用的用户的引用。

  ![类图](media/msal-net-token-cache-serialization/class-diagram.png)

> [!IMPORTANT]
> MSAL.NET 将为你创建令牌缓存，当你调用应用程序的 `UserTokenCache` 和 `AppTokenCache` 属性时，它会提供 `IToken` 缓存。 最好是不要自行实现接口。 实现自定义令牌缓存序列化时，你的责任是：
> - 回应 `BeforeAccess` 和 `AfterAccess`“事件”（或其异步风格）。 `BeforeAccess` 委托负责反序列化缓存，而 `AfterAccess` 负责序列化缓存。
> - 其中的一部分事件存储或加载 Blob，这些 Blob 将通过事件参数传递到所需的任何存储。

所用的策略会有所不同，具体取决于是针对[公共客户端应用程序](msal-client-applications.md)（桌面）还是[机密客户端应用程序](msal-client-applications.md)（Web 应用/ Web API、守护程序应用程序）编写令牌缓存序列化。

### <a name="custom-token-cache-for-a-web-app-or-web-api-confidential-client-application"></a>Web 应用或 Web API（机密客户端应用程序）的自定义令牌缓存

在 Web 应用或 Web API 中，缓存可以使用会话、Redis 缓存、SQL 数据库或 Cosmos DB 数据库。 在 Web 应用或 Web API 中，将为每个帐户保留一个令牌缓存： 
- 对于 Web 应用，令牌缓存应使用帐户 ID 进行键控。
- 对于 Web API，帐户应使用用于调用该 API 的令牌的哈希值进行键控。

[Microsoft.Identity.Web/TokenCacheProviders](https://github.com/AzureAD/microsoft-identity-web/tree/master/src/Microsoft.Identity.Web/TokenCacheProviders) 中提供了令牌缓存序列化程序的示例。

### <a name="custom-token-cache-for-a-desktop-or-mobile-app-public-client-application"></a>桌面或移动应用（公共客户端应用程序）的自定义令牌缓存

从 MSAL.NET v2.x 开始，可以使用多个选项来序列化公共客户端的令牌缓存。 只能以 MSAL.NET 格式序列化缓存（统一格式的缓存在不同的 MSAL 和平台中是通用的）。  还可以支持 ADAL V3 的[旧式](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization)令牌缓存序列化。

以下示例说明了如何自定义令牌缓存序列化，以在 ADAL.NET 3.x、ADAL.NET 5.x 与 MSAL.NET 之间共享单一登录状态：[active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2)。

> [!Note]
> MSAL 2.x 不再支持 MSAL.NET 1.1.4-preview 令牌缓存格式。 如果应用程序利用 MSAL.NET 1.x，则用户必须重新登录。 也支持从 ADAL 4.x（和 3.x）迁移。

#### <a name="simple-token-cache-serialization-msal-only"></a>简单令牌缓存序列化（仅限 MSAL）

下面是适用于桌面应用程序的令牌缓存的自定义序列化的简单实现示例。 此处，用户令牌缓存是应用程序所在的同一文件夹中的某个文件。

生成应用程序后，通过调用 `TokenCacheHelper.EnableSerialization()` 方法并传递应用程序 `UserTokenCache` 来启用序列化。

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

`TokenCacheHelper` 帮助器类定义为：

```csharp
static class TokenCacheHelper
 {
  public static void EnableSerialization(ITokenCache tokenCache)
  {
   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Path to the token cache. Note that this could be something different for instance for MSIX applications:
  /// private static readonly string CacheFilePath =
  /// $"{Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData)}\{AppName}\msalcache.bin";
  /// </summary>
  public static readonly string CacheFilePath = System.Reflection.Assembly.GetExecutingAssembly().Location + ".msalcache.bin3";

  private static readonly object FileLock = new object();


  private static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeMsalV3(File.Exists(CacheFilePath)
            ? ProtectedData.Unprotect(File.ReadAllBytes(CacheFilePath),
                                      null,
                                      DataProtectionScope.CurrentUser)
            : null);
   }
  }

  private static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     // reflect changesgs in the persistent store
     File.WriteAllBytes(CacheFilePath,
                         ProtectedData.Protect(args.TokenCache.SerializeMsalV3(),
                                                 null,
                                                 DataProtectionScope.CurrentUser)
                         );
    }
   }
  }
 }
```

[Microsoft.Identity.Client.Extensions.Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) 开源库中提供了适用于公共客户端应用程序（适用于 Windows、Mac 和 Linux 上运行的桌面应用程序）的基于产品质量令牌缓存文件的序列化程序。 可以通过以下 NuGet 包将此程序包含在应用程序中：[Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/)。

#### <a name="dual-token-cache-serialization-msal-unified-cache-and-adal-v3"></a>双令牌缓存序列化（MSAL 统一缓存和 ADAL v3）

若要在同一个平台上同时使用统一缓存格式（在 ADAL.NET 4.x 和 MSAL.NET 2.x 中通用）和同一代的或更低版本的其他 MSAL 实现令牌缓存序列化，请查看以下代码：

```csharp
string appLocation = Path.GetDirectoryName(Assembly.GetEntryAssembly().Location;
string cacheFolder = Path.GetFullPath(appLocation) + @"..\..\..\..");
string adalV3cacheFileName = Path.Combine(cacheFolder, "cacheAdalV3.bin");
string unifiedCacheFileName = Path.Combine(cacheFolder, "unifiedCache.bin");

IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
                                    .Build();
FilesBasedTokenCacheHelper.EnableSerialization(app.UserTokenCache,
                                               unifiedCacheFileName,
                                               adalV3cacheFileName);

```

在此情况下，帮助器类定义为：

```csharp
using System;
using System.IO;
using System.Security.Cryptography;
using Microsoft.Identity.Client;

namespace CommonCacheMsalV3
{
 /// <summary>
 /// Simple persistent cache implementation of the dual cache serialization (ADAL V3 legacy
 /// and unified cache format) for a desktop applications (from MSAL 2.x)
 /// </summary>
 static class FilesBasedTokenCacheHelper
 {
  /// <summary>
  /// Enables the serialization of the token cache
  /// </summary>
  /// <param name="adalV3CacheFileName">File name where the cache is serialized with the
  /// ADAL V3 token cache format. Can
  /// be <c>null</c> if you don't want to implement the legacy ADAL V3 token cache
  /// serialization in your MSAL 2.x+ application</param>
  /// <param name="unifiedCacheFileName">File name where the cache is serialized
  /// with the Unified cache format, common to
  /// ADAL V4 and MSAL V2 and above, and also across ADAL/MSAL on the same platform.
  ///  Should not be <c>null</c></param>
  /// <returns></returns>
  public static void EnableSerialization(ITokenCache tokenCache, string unifiedCacheFileName, string adalV3CacheFileName)
  {
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// File path where the token cache is serialized with the unified cache format
  /// (ADAL.NET V4, MSAL.NET V3)
  /// </summary>
  public static string UnifiedCacheFileName { get; private set; }

  /// <summary>
  /// File path where the token cache is serialized with the legacy ADAL V3 format
  /// </summary>
  public static string AdalV3CacheFileName { get; private set; }

  private static readonly object FileLock = new object();

  public static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeAdalV3(ReadFromFileIfExists(AdalV3CacheFileName));
    try
    {
     args.TokenCache.DeserializeMsalV3(ReadFromFileIfExists(UnifiedCacheFileName));
    }
    catch(Exception ex)
    {
     // Compatibility with the MSAL v2 cache if you used one
     args.TokenCache.DeserializeMsalV2(ReadFromFileIfExists(UnifiedCacheFileName));
    }
   }
  }

  public static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     WriteToFileIfNotNull(UnifiedCacheFileName, args.TokenCache.SerializeMsalV3());
     if (!string.IsNullOrWhiteSpace(AdalV3CacheFileName))
     {
      WriteToFileIfNotNull(AdalV3CacheFileName, args.TokenCache.SerializeAdalV3());
     }
    }
   }
  }

  /// <summary>
  /// Read the content of a file if it exists
  /// </summary>
  /// <param name="path">File path</param>
  /// <returns>Content of the file (in bytes)</returns>
  private static byte[] ReadFromFileIfExists(string path)
  {
   byte[] protectedBytes = (!string.IsNullOrEmpty(path) && File.Exists(path))
       ? File.ReadAllBytes(path) : null;
   byte[] unprotectedBytes = encrypt ?
       ((protectedBytes != null) ? ProtectedData.Unprotect(protectedBytes, null, DataProtectionScope.CurrentUser) : null)
       : protectedBytes;
   return unprotectedBytes;
  }

  /// <summary>
  /// Writes a blob of bytes to a file. If the blob is <c>null</c>, deletes the file
  /// </summary>
  /// <param name="path">path to the file to write</param>
  /// <param name="blob">Blob of bytes to write</param>
  private static void WriteToFileIfNotNull(string path, byte[] blob)
  {
   if (blob != null)
   {
    byte[] protectedBytes = encrypt
      ? ProtectedData.Protect(blob, null, DataProtectionScope.CurrentUser)
      : blob;
    File.WriteAllBytes(path, protectedBytes);
   }
   else
   {
    File.Delete(path);
   }
  }

  // Change if you want to test with an un-encrypted blob (this is a json format)
  private static bool encrypt = true;
 }
}
```

---

## <a name="plain-text-fallback-mode"></a>纯文本回退模式

MSAL 允许以明文形式存储未加密的令牌。 这仅适用于在开发环境中用于调试目的。 可以通过以下代码模式使用纯文本回退模式。

```csharp
storageProperties =
    new StorageCreationPropertiesBuilder(
        Config.CacheFileName + ".plaintext",
        Config.CacheDir)
    .WithUnprotectedFile()
    .Build();

var cacheHelper = await MsalCacheHelper.CreateAsync(storageProperties).ConfigureAwait(false);
```

## <a name="next-steps"></a>后续步骤

以下示例演示了令牌缓存序列化。

| 示例 | 平台 | 说明|
| ------ | -------- | ----------- |
|[active-directory-dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | 桌面 (WPF) | 调用 Microsoft Graph API 的 Windows 桌面 .NET (WPF) 应用程序。 ![该示意图显示了一个拓扑，其中的桌面应用 WPF TodoListClient 通过以交互方式获取令牌来流向 Azure AD，然后流向 Microsoft Graph。](media/msal-net-token-cache-serialization/topology.png)|
|[active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2) | 桌面（控制台） | 一组 Visual Studio 解决方案，阐释了如何将 Azure AD v1.0 应用程序（使用 ADAL.NET）迁移到 Microsoft 标识平台应用程序（使用 MSAL.NET）。 具体而言，请参阅[令牌缓存迁移](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/blob/master/TokenCacheMigration/README.md)和[机密客户端令牌缓存](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/tree/master/ConfidentialClientTokenCache) |
[ms-identity-aspnet-webapp-openidconnect](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect) | ASP.NET (net472) | ASP.NET MVC 应用程序中的令牌缓存序列化的示例（使用 MSAL.NET）。 尤其请参阅 [MsalAppBuilder](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Utils/MsalAppBuilder.cs)
