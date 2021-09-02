---
title: 将 Azure Cosmos DB 用作 ASP.NET 会话状态和缓存提供程序
description: 了解如何将 Azure Cosmos DB 用作 ASP.NET 会话状态和缓存提供程序
author: StefArroyo
ms.author: esarroyo
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/15/2021
ms.openlocfilehash: 9a7b7ad15d1d4850e910010e4fdc195e474d10af
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123113360"
---
# <a name="use-azure-cosmos-db-as-an-aspnet-session-state-and-caching-provider"></a>将 Azure Cosmos DB 用作 ASP.NET 会话状态和缓存提供程序
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

通过 Azure Cosmos DB 会话和缓存提供程序，可以使用 Azure Cosmos DB，并利用它的低延迟和全局缩放功能来存储会话状态数据，并将其用作应用程序内的分布式缓存。

## <a name="what-is-session-state"></a>什么是会话状态？

[会话状态](/aspnet/core/fundamentals/app-state?view=aspnetcore-5.0#configure-session-state&preserve-view=true)是用户数据，用于在同一浏览器中跟踪用户在某个时间段内浏览 Web 应用程序的情况。 会话状态会过期，并且仅限于特定浏览器中的交互，而不会扩展到其他浏览器。 它被视为临时数据，如果不存在，也不会破坏应用程序。 但是，如果存在，它会使用户的体验更快，因为 Web 应用程序不需要对同一用户的每个浏览器请求获取它。

它通常由某些存储机制提供支持，在某些情况下，这些存储机制可以位于当前 Web 服务器外部，并将同一浏览器的请求负载均衡分布到多个 Web 服务器上，以实现更高的可伸缩性。

最简单的会话状态提供程序是内存中提供程序，它只将数据存储在本地 web 服务器内存中，并要求应用程序使用[应用程序请求路由](/iis/extensions/planning-for-arr/using-the-application-request-routing-module)。 这会使浏览器会话粘滞在特定的 web 服务器（该浏览器的所有请求都需要始终驻留在同一 Web 服务器上）。 该提供程序非常适用于简单的场景，但是当 Web 应用程序扩展时，粘滞要求可能会带来负载均衡问题。

有很多外部存储提供程序可用，这些提供程序不要求会话粘滞，以多个 Web 服务器可读取和访问的方式存储会话数据，因此可实现更高的可伸缩性。

## <a name="session-state-scenarios"></a>会话状态场景

可通过扩展包 [Microsoft.Extensions.Caching.Cosmos](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Cosmos) 用作会话状态提供程序的 Cosmos DB 使用 [Azure Cosmos DB .NET SDK](sql-api-sdk-dotnet-standard.md)，以将容器用作基于键/值方法的有效会话存储，其中键是会话标识符。

添加包后，可以将 `AddCosmosCache` 用作启动过程的一部分（services.AddSession 和 app.UseSession 是任何会话状态提供程序都需要的[常见初始化](/aspnet/core/fundamentals/app-state?view=aspnetcore-5.0#configure-session-stat&preserve-view=true)步骤）：

```csharp
public void ConfigureServices(IServiceCollection services)
{
  /* Other service configurations */
  services.AddCosmosCache((CosmosCacheOptions cacheOptions) =>
  {
      CosmosClientBuilder clientBuilder = new CosmosClientBuilder("myConnectionString")
        .WithApplicationRegion("West US");
      cacheOptions.ContainerName = "myContainer";
      cacheOptions.DatabaseName = "myDatabase";
      cacheOptions.ClientBuilder = clientBuilder;
      /* Creates the container if it does not exist */
      cacheOptions.CreateIfNotExists = true; 
  });

  services.AddSession(options =>
  {
      options.IdleTimeout = TimeSpan.FromSeconds(3600);
      options.Cookie.IsEssential = true;
  });
  /* Other service configurations */
}

public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    /* Other configurations */

    app.UseSession();

    /* app.UseEndpoints and other configurations */
}
```

在这些步骤中，你将指定要存储会话状态的数据库和容器，如果不存在，则创建它们。

你可以使用 `CosmosClientBuilder` 自定义 SDK 客户端配置，或者如果你的应用程序已经使用 `CosmosClient` 来执行 Cosmos DB 的其他操作，也可以将其注入提供程序：

```csharp
services.AddCosmosCache((CosmosCacheOptions cacheOptions) =>
{
    cacheOptions.ContainerName = "myContainer";
    cacheOptions.DatabaseName = "myDatabase";
    cacheOptions.CosmosClient = preExistingClient;
    /* Creates the container if it does not exist */
    cacheOptions.CreateIfNotExists = true; 
});
```

此后，你可以像使用任何其他提供程序一样使用 ASP.NET Core 会话，并使用 HttpContext.Session 对象。 记住，请始终尝试按照 [ASP.NET 建议](/aspnet/core/fundamentals/app-state?view=aspnetcore-5.0#load-session-state-asynchronously&preserve-view=true)异步加载会话信息。

##  <a name="distributed-cache-scenarios"></a>分布式缓存场景

假设 Cosmos DB 提供程序实现了 [IDistributedCache 接口以充当分布式缓存提供程序](/aspnet/core/performance/caching/distributed?view=aspnetcore-5.0&preserve-view=true)，因此它也可用于需要分布式缓存的任何应用程序，而不仅仅适用于需要高性能和分布式会话状态提供程序的 Web 应用程序。

分布式缓存需要数据一致性以提供能够共享缓存数据的独立实例。 使用 Cosmos DB 提供程序时，可以：

- 如果可以启用[应用程序请求路由](/iis/extensions/planning-for-arr/using-the-application-request-routing-module)并将请求粘滞到特定实例，请在会话一致性中使用 Cosmos DB 帐户。
- 在有限过期或强一致性中使用 Cosmos DB 帐户，而无需请求粘滞。 就不同实例的负载分布而言，这提供了最大的可伸缩性。

若要将 Cosmos DB 提供程序用作分布式缓存，需要使用 `services.AddCosmosCache` 调用在 `ConfiguredService` 中注册它。 完成后，应用程序中的任何构造函数都可以通过引用 `IDistributedCache` 来请求缓存，并且它将接收由要使用的[依赖项注入](/dotnet/core/extensions/dependency-injection)注入的实例：

```csharp
public class MyBusinessClass
{
    private readonly IDistributedCache cache;

    public MyBusinessClass(IDistributedCache cache)
    {
        this.cache = cache;
    }
    
    public async Task SomeOperationAsync()
    {
        string someCachedValue = await this.cache.GetStringAsync("someKey");
        /* Use the cache */
    }
}
```

## <a name="troubleshooting-and-diagnosing"></a>故障排除和诊断
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

由于 Cosmos DB 提供程序在底层使用 .NET SDK，因此所有现有的[性能指南](performance-tips-dotnet-sdk-v3-sql.md)和[故障排除指南](troubleshoot-dot-net-sdk.md)都可用于了解任何潜在问题。 请注意，有一种独特的方法可通过基础 Cosmos DB 操作访问诊断，因为它们无法通过 IDistributedCache API 公开。

通过注册可选的诊断委托，可以捕获和有条件地记录任何诊断，以排除高延迟之类的故障：

```csharp
void captureDiagnostics(CosmosDiagnostics diagnostics)
{
    if (diagnostics.GetClientElapsedTime() > SomePredefinedThresholdTime)
    {
        Console.WriteLine(diagnostics.ToString());
    }
}

services.AddCosmosCache((CosmosCacheOptions cacheOptions) =>
{
    cacheOptions.DiagnosticsHandler = captureDiagnostics;
    /* other options */
});
```

## <a name="next-steps"></a>后续步骤
- 若要查找有关 Azure Cosmos DB 会话和缓存提供程序的详细信息，请参阅[GitHub 上的源代码](https://github.com/Azure/Microsoft.Extensions.Caching.Cosmos/)。
- 通过浏览“探索 ASP.NET Core Web 应用程序”的示例，[试用](https://github.com/Azure/Microsoft.Extensions.Caching.Cosmos/tree/master/sample)Azure Cosmos DB 的会话和缓存提供程序。
- 有关详细信息，请参阅 .NET 中的[分布式缓存](/aspnet/core/performance/caching/distributed?view=aspnetcore-5.0&preserve-view=true)。
