---
title: 有关在 .NET Core 应用中使用功能标志的教程 | Microsoft Docs
description: 本教程介绍如何在 .NET Core 应用中实施功能标志。
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 09/17/2020
ms.author: alkemper
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 5f15d016c890ec214fe6b6a3d8af3138d202408b
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2021
ms.locfileid: "122970331"
---
# <a name="tutorial-use-feature-flags-in-an-aspnet-core-app"></a>教程：在 ASP.NET Core 应用中使用功能标志

.NET Core 功能管理库能够顺畅地支持在 .NET 或 ASP.NET Core 应用程序中实施功能标志。 使用这些库能够以声明方式将功能标志添加到代码中，这样就不必手动编写代码来使用 `if` 语句启用或禁用功能。

功能管理库还会在幕后管理功能标志生命周期。 例如，这些库可刷新和缓存标志状态，或者保证标志状态在请求调用期间保持不变。 此外，ASP.NET Core 库提供现成的集成，包括 MVC 控制器操作、视图、路由和中间件。

[“将功能标志添加到 ASP.NET Core 应用”快速入门](./quickstart-feature-flag-aspnet-core.md)介绍了一个如何在 ASP.NET Core 应用程序中使用功能标志的简单示例。 本教程介绍了功能管理库的其他设置选项和功能。 可以使用在快速入门中创建的示例应用来试用本教程中所示的示例代码。 

有关 ASP.NET Core 功能管理 API 参考文档，请参阅 [Microsoft.FeatureManagement 命名空间](/dotnet/api/microsoft.featuremanagement)。

在本教程中，您将学习如何执行以下操作：

> [!div class="checklist"]
> * 在应用程序的关键组件中添加功能标志，以控制功能可用性。
> * 使用应用程序配置来管理功能标志时，与应用程序配置相集成。

## <a name="set-up-feature-management"></a>设置功能管理

若要访问 .NET Core 功能管理器，应用必须具有对 `Microsoft.FeatureManagement.AspNetCore` NuGet 包的引用。

.NET Core 功能管理器是通过框架的本机配置系统配置的。 因此，可以使用 .NET Core 支持的任何配置源（包括本地 appsettings.json 文件或环境变量）来定义应用程序的功能标志设置。

默认情况下，功能管理器从 .NET Core 配置数据的 `"FeatureManagement"` 节检索功能标志配置。 若要使用默认配置位置，请调用传入到 Startup 类的 ConfigureServices 方法的 IServiceCollection 的 [AddFeatureManagement](/dotnet/api/microsoft.featuremanagement.servicecollectionextensions.addfeaturemanagement) 方法。


```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
        services.AddFeatureManagement();
    }
}
```

可以通过调用 [Configuration.GetSection](/dotnet/api/microsoft.web.administration.configuration.getsection) 并传入所需节的名称来指定应从其他配置节中检索功能管理配置。 以下示例告知功能管理器要从名为 `"MyFeatureFlags"` 的另一个节读取数据：

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
        services.AddFeatureManagement(Configuration.GetSection("MyFeatureFlags"));
    }
}
```


如果在功能标志中使用筛选器，则必须包含 [Microsoft.FeatureManagement.FeatureFilters](/dotnet/api/microsoft.featuremanagement.featurefilters) 命名空间，添加对 [AddFeatureFilters](/dotnet/api/microsoft.featuremanagement.ifeaturemanagementbuilder.addfeaturefilter) 的调用，并指定要用作方法泛型类型的筛选器的类型名称。 若要详细了解如何使用功能筛选器来动态启用和禁用功能，请参阅[为目标受众启用功能的分阶段推出](./howto-targetingfilter-aspnet-core.md)。

以下示例演示如何使用名为 `PercentageFilter` 的内置功能筛选器：



```csharp
using Microsoft.FeatureManagement;
using Microsoft.FeatureManagement.FeatureFilters;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
        services.AddFeatureManagement()
                .AddFeatureFilter<PercentageFilter>();
    }
}
```

建议不要将功能标志硬编码到应用程序中，而应将功能标志保留在应用程序外部并单独对其进行管理。 这样便可以随时修改标志状态，并使这些更改在应用程序中立即生效。 Azure 应用配置服务提供了一个专用门户 UI，用于管理所有功能标志。 Azure 应用配置服务还直接通过其 .NET Core 客户端库将功能标志传送到应用程序。

将 ASP.NET Core 应用程序连接到应用配置的最简单方法是使用 `Microsoft.Azure.AppConfiguration.AspNetCore` NuGet 包中包含的配置提供程序。 在添加对此 NuGet 包的引用后，请按照以下步骤使用此包。

1. 打开 *Program.cs* 文件，并添加以下代码。
    > [!IMPORTANT]
    > `CreateHostBuilder` 替换 .NET Core 3.x 中的 `CreateWebHostBuilder`。 根据环境选择正确的语法。

    ### <a name="net-5x"></a>[.NET 5.x](#tab/core5x)
    
    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration(config =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags());
                }).UseStartup<Startup>());
    ```

    ### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)
    
    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;

    public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration(config =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options =>
                    options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags());
            }).UseStartup<Startup>());
    ```
        
    ### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)
    
    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                    .ConfigureAppConfiguration(config =>
                    {
                        var settings = config.Build();
                        config.AddAzureAppConfiguration(options =>
                            options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags());
                    }).UseStartup<Startup>();
    ```
    ---

2. 打开“Startup.cs”并更新 `Configure` 和 `ConfigureServices` 方法，添加名为 `UseAzureAppConfiguration` 的内置中间件。 此中间件允许在 ASP.NET Core Web 应用继续接收请求的同时定期刷新功能标志值。



    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        app.UseAzureAppConfiguration();
    }
    ```

   ```csharp
   public void ConfigureServices(IServiceCollection services)
   {
       services.AddAzureAppConfiguration();
   }
   ```
   
在典型方案中，你将在部署和启用应用程序的不同功能时定期更新功能标志值。 默认情况下，功能标志值缓存 30 秒，因此，在中间件收到请求时触发的刷新操作不会更新该值，直到缓存值过期为止。 以下代码演示如何通过在对 UseFeatureFlags 的调用中设置 [CacheExpirationInterval](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.featuremanagement.featureflagoptions.cacheexpirationinterval)，将缓存到期时间或轮询间隔更改为 5 分钟。


    
```csharp
config.AddAzureAppConfiguration(options =>
    options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags(featureFlagOptions => {
        featureFlagOptions.CacheExpirationInterval = TimeSpan.FromMinutes(5);
    }));
});
```


## <a name="feature-flag-declaration"></a>功能标志声明

每个功能标志声明有两个组成部分：一个名称，以及一个列表，该列表中包含一个或多个用于评估功能状态是否为“开”（即，其值为 `True`）的筛选器。 筛选器定义有关何时应启用某个功能的条件。

如果某个功能标志包含多个筛选器，则会按顺序遍历筛选器列表，直到其中一个筛选器确定应启用该功能。 此时，该功能标志为“打开”，并会跳过所有剩余的筛选器结果。  如果没有任何筛选器指示应启用该功能，则表示该功能标志为“关闭”。 

功能管理器支持将 *appsettings.json* 用作功能标志的配置源。 以下示例演示如何在 JSON 文件中设置功能标志：

```JSON
{"FeatureManagement": {
        "FeatureA": true, // Feature flag set to on
        "FeatureB": false, // Feature flag set to off
        "FeatureC": {
            "EnabledFor": [
                {
                    "Name": "Percentage",
                    "Parameters": {
                        "Value": 50
                    }
                }
            ]
        }
    }
}
```

按照约定，此 JSON 文档的 `FeatureManagement` 节用于功能标志设置。 以上示例演示了三个功能标志，其筛选器已在 `EnabledFor` 属性中定义：

* `FeatureA` 状态为“打开”。 
* `FeatureB` 状态为“关闭”。 
* `FeatureC` 指定包含 `Parameters` 属性的名为 `Percentage` 的筛选器。 `Percentage` 是可配置的筛选器。 在此示例中，`Percentage` 指定打开 `FeatureC` 标志的概率为 50%。  有关如何使用功能筛选器的操作指南，请参阅[使用功能筛选器启用条件功能标志](./howto-feature-filters-aspnet-core.md)。




## <a name="use-dependency-injection-to-access-ifeaturemanager"></a>使用依赖项注入访问 IFeatureManager 

对于某些操作（例如，手动检查功能标志值），需要获取 [IFeatureManager](/dotnet/api/microsoft.featuremanagement.ifeaturemanager?preserve-view=true&view=azure-dotnet-preview) 的实例。 在 ASP.NET Core MVC 中，可以通过依赖项注入访问功能管理器 `IFeatureManager`。 在下面的示例中，`IFeatureManager` 类型的参数将添加到控制器的构造函数的签名中。 运行时在调用构造函数时会自动解析引用并提供接口的一个实例。 如果你使用的应用程序模板中的控制器在构造函数中已有一个或多个依赖项注入参数（例如 `ILogger`），则只需添加 `IFeatureManager` 作为附加参数：

### <a name="net-5x"></a>[.NET 5.x](#tab/core5x)
    
```csharp
using Microsoft.FeatureManagement;

public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(ILogger<HomeController> logger, IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```

### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

```csharp
using Microsoft.FeatureManagement;

public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(ILogger<HomeController> logger, IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```
    
### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

```csharp
using Microsoft.FeatureManagement;

public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```

---

## <a name="feature-flag-references"></a>功能标志引用

将功能标志定义为字符串变量，以便在代码中引用它们：

```csharp
public static class MyFeatureFlags
{
    public const string FeatureA = "FeatureA";
    public const string FeatureB = "FeatureB";
    public const string FeatureC = "FeatureC";
}
```

## <a name="feature-flag-checks"></a>功能标志检查

功能管理的一种常见模式是检查功能标志是否设置为“开”，如果设置为“开”，则运行一段代码。 例如：

```csharp
IFeatureManager featureManager;
...
if (await featureManager.IsEnabledAsync(MyFeatureFlags.FeatureA))
{
    // Run the following code
}
```

## <a name="controller-actions"></a>控制器操作

在 MVC 控制器中，可以使用 `FeatureGate` 属性控制是要启用整个控制器类还是特定的操作。 以下 `HomeController` 控制器要求 `FeatureA` 状态为“打开”，才能执行控制器类包含的任何操作： 

```csharp
using Microsoft.FeatureManagement.Mvc;

[FeatureGate(MyFeatureFlags.FeatureA)]
public class HomeController : Controller
{
    ...
}
```

以下 `Index` 操作要求 `FeatureA` 状态为“打开”才能运行该操作： 

```csharp
using Microsoft.FeatureManagement.Mvc;

[FeatureGate(MyFeatureFlags.FeatureA)]
public IActionResult Index()
{
    return View();
}
```

当由于控制功能标志状态为“关”而阻止了 MVC 控制器或操作时，将调用已注册的 [IDisabledFeaturesHandler](/dotnet/api/microsoft.featuremanagement.mvc.idisabledfeatureshandler?preserve-view=true&view=azure-dotnet-preview) 接口。 默认的 `IDisabledFeaturesHandler` 接口向客户端返回 404 状态代码，但不返回响应正文。

## <a name="mvc-views"></a>MVC 视图

打开 Views 目录中的 _ViewImports.cshtml  ，并添加功能管理器标记帮助器  ：

```html
@addTagHelper *, Microsoft.FeatureManagement.AspNetCore
```

在 MVC 视图中，可以使用 `<feature>` 标记根据是否启用了某个功能标志来呈现内容：

```html
<feature name="FeatureA">
    <p>This can only be seen if 'FeatureA' is enabled.</p>
</feature>
```

要在不满足要求时显示替代内容，可以使用 `negate` 属性。

```html
<feature name="FeatureA" negate="true">
    <p>This will be shown if 'FeatureA' is disabled.</p>
</feature>
```

如果启用了列表中的任何或所有功能，还可以使用 `<feature>` 功能标记显示内容。

```html
<feature name="FeatureA, FeatureB" requirement="All">
    <p>This can only be seen if 'FeatureA' and 'FeatureB' are enabled.</p>
</feature>
<feature name="FeatureA, FeatureB" requirement="Any">
    <p>This can be seen if 'FeatureA', 'FeatureB', or both are enabled.</p>
</feature>
```

## <a name="mvc-filters"></a>MVC 筛选器

可以设置 MVC 筛选器，以根据功能标志的状态激活这些筛选器。 此功能仅限用于实现了 [IAsyncActionFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncactionfilter) 的筛选器。 以下代码添加名为 `ThirdPartyActionFilter` 的 MVC 筛选器。 仅当已启用 `FeatureA` 时，才会在 MVC 管道内部触发此筛选器。  

```csharp
using Microsoft.FeatureManagement.FeatureFilters;

IConfiguration Configuration { get; set;}

public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options => {
        options.Filters.AddForFeature<ThirdPartyActionFilter>(MyFeatureFlags.FeatureA);
    });
}
```

## <a name="middleware"></a>中间件

还可以使用功能标志按条件添加应用程序分支和中间件。 仅当已启用 `FeatureA` 时，以下代码才会在请求管道中插入中间件组件。

```csharp
app.UseMiddlewareForFeature<ThirdPartyMiddleware>(MyFeatureFlags.FeatureA);
```

此代码生成更通用的功能来根据功能标志分支整个应用程序。

```csharp
app.UseForFeature(featureName, appBuilder => {
    appBuilder.UseMiddleware<T>();
});
```

## <a name="next-steps"></a>后续步骤

本教程已介绍如何使用 `Microsoft.FeatureManagement` 库在 ASP.NET Core 应用程序中实施功能标志。 有关 ASP.NET Core 和应用程序配置中的功能管理支持的详细信息，请参阅以下资源：

* [ASP.NET Core 功能标志示例代码](./quickstart-feature-flag-aspnet-core.md)
* [Microsoft.FeatureManagement 文档](/dotnet/api/microsoft.featuremanagement)
* [管理功能标志](./manage-feature-flags.md)