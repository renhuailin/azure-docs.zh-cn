---
title: 教程：在 ASP.NET Core 中使用应用程序配置动态配置
titleSuffix: Azure App Configuration
description: 本教程介绍如何动态更新 ASP.NET Core 应用的配置数据
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 09/1/2020
ms.author: alkemper
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: c6a80a4d17fd5bf9584a6aaa8b50802f5a4ec5a6
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110468842"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>教程：在 ASP.NET Core 应用中使用动态配置

ASP.NET Core 有可插拔的配置系统，可以从各种源读取配置数据。 它可以动态处理更改，而不会导致应用程序重启。 ASP.NET Core 支持将配置设置绑定到强类型 .NET 类。 它通过使用 `IOptionsSnapshot<T>`（它会在基础数据发生变化时自动重载应用程序的配置）将它们注入到代码中。

本教程演示如何在代码中实现动态配置更新。 它建立在快速入门中介绍的 Web 应用之上。 在继续操作之前，请先完成[使用应用程序配置创建 ASP.NET Core 应用](./quickstart-aspnet-core-app.md)。

你可以使用任何代码编辑器执行本教程中的步骤。 [Visual Studio Code](https://code.visualstudio.com/) 是 Windows、macOS 和 Linux 平台上提供的一个卓越选项。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 设置应用程序，使其能够更新配置以响应应用程序配置存储区中的更改。
> * 在应用程序的控制器中注入最新配置。

## <a name="prerequisites"></a>先决条件

若要完成本教程，请安装 [.NET Core SDK](https://dotnet.microsoft.com/download)。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

在继续操作之前，请先完成[使用应用程序配置创建 ASP.NET Core 应用](./quickstart-aspnet-core-app.md)。

## <a name="add-a-sentinel-key"></a>添加 sentinel 键

Sentinel 键是完成所有其他键的更改后更新的特殊键。 应用程序监视 Sentinel 键。 检测到更改时，应用程序刷新所有配置值。 与监视所有键以了解更改情况相比，此方法有助于确保应用程序中配置的一致性，并减少对应用程序配置发出的请求总数。

1. 在 Azure 门户中，选择“配置资源管理器”>“创建”>“键-值”  。
1. 输入 *TestApp:Settings:Sentinel* 作为“键”。   输入 1 作为“值”。 将“标签”和“内容类型”留空   。
1. 选择“应用”。 

> [!NOTE]
> 如果不使用 Sentinel 键，则需手动注册要监视的每个键。

## <a name="reload-data-from-app-configuration"></a>从应用配置重载数据

1. 通过运行以下命令，添加对 `Microsoft.Azure.AppConfiguration.AspNetCore` NuGet 包的引用：

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

1. 打开 *Program.cs*，并更新 `CreateWebHostBuilder` 方法以添加 `config.AddAzureAppConfiguration()` 方法。

   #### <a name="net-5x"></a>[.NET 5.x](#tab/core5x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                    {
                        options.Connect(settings["ConnectionStrings:AppConfig"])
                               .ConfigureRefresh(refresh =>
                                    {
                                        refresh.Register("TestApp:Settings:Sentinel", refreshAll: true)
                                               .SetCacheExpiration(new TimeSpan(0, 5, 0));
                                    });
                    });
                })
            .UseStartup<Startup>());
    ```   
    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                    {
                        options.Connect(settings["ConnectionStrings:AppConfig"])
                               .ConfigureRefresh(refresh =>
                                    {
                                        refresh.Register("TestApp:Settings:Sentinel", refreshAll: true)
                                               .SetCacheExpiration(new TimeSpan(0, 5, 0));
                                    });
                    });
                })
            .UseStartup<Startup>());
    ```
    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                           .ConfigureRefresh(refresh =>
                                {
                                    refresh.Register("TestApp:Settings:Sentinel", refreshAll: true)
                                           .SetCacheExpiration(new TimeSpan(0, 5, 0));
                                });
                });
            })
            .UseStartup<Startup>();
    ```
    ---

    在 `ConfigureRefresh` 方法中，在要监视更改的应用程序配置存储中注册键。 `Register` 方法的 `refreshAll` 参数指示在已注册键更改时应刷新所有配置值。 `SetCacheExpiration` 方法指定向应用程序配发出新请求以检查任何配置更改之前必须经过的最小时间。 在此示例中，替代默认的过期时间（30 秒），改为指定一个 5 分钟的时间。 这会减少对应用程序配置存储发出的潜在请求数。

    > [!NOTE]
    > 出于测试目的，可能需要缩短缓存刷新过期时间。

    若要实际触发一个配置刷新，可使用应用程序配置中间件。 稍后会介绍如何执行此操作。

1. 在控制器目录中添加一个 Settings.cs 文件，用于定义和实现新的 `Settings` 类。 将命名空间替换为项目的名称。 

    ```csharp
    namespace TestAppConfig
    {
        public class Settings
        {
            public string BackgroundColor { get; set; }
            public long FontSize { get; set; }
            public string FontColor { get; set; }
            public string Message { get; set; }
        }
    }
    ```

1. 打开 Startup.cs 并更新 `ConfigureServices` 方法。 调用 `Configure<Settings>`，将配置数据绑定到 `Settings` 类。 调用 `AddAzureAppConfiguration`，将应用程序配置组件添加到应用程序的服务集合。

    #### <a name="net-5x"></a>[.NET 5.x](#tab/core5x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddControllersWithViews();
        services.AddAzureAppConfiguration();
    }
    ```
    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddControllersWithViews();
        services.AddAzureAppConfiguration();
    }
    ```
    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
        services.AddAzureAppConfiguration();
    }
    ```
    ---

1. 更新 `Configure` 方法，并添加对 `UseAzureAppConfiguration` 的调用。 它使应用程序能够使用应用程序配置中间件自动为你处理配置更新。

    #### <a name="net-5x"></a>[.NET 5.x](#tab/core5x)

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
                // The default HSTS value is 30 days. You may want to change this for production scenarios, see https://aka.ms/aspnetcore-hsts.
                app.UseHsts();
            }

            // Add the following line:
            app.UseAzureAppConfiguration();

            app.UseHttpsRedirection();
            
            app.UseStaticFiles();

            app.UseRouting();

            app.UseAuthorization();

            app.UseEndpoints(endpoints =>
            {
                endpoints.MapControllerRoute(
                    name: "default",
                    pattern: "{controller=Home}/{action=Index}/{id?}");
            });
    }
    ```
    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
                // The default HSTS value is 30 days. You may want to change this for production scenarios, see https://aka.ms/aspnetcore-hsts.
                app.UseHsts();
            }

            // Add the following line:
            app.UseAzureAppConfiguration();

            app.UseHttpsRedirection();
            
            app.UseStaticFiles();

            app.UseRouting();

            app.UseAuthorization();

            app.UseEndpoints(endpoints =>
            {
                endpoints.MapControllerRoute(
                    name: "default",
                    pattern: "{controller=Home}/{action=Index}/{id?}");
            });
    }
    ```
    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseAzureAppConfiguration();

        services.Configure<CookiePolicyOptions>(options =>
        {
            options.CheckConsentNeeded = context => true;
            options.MinimumSameSitePolicy = SameSiteMode.None;
        });

        app.UseMvc();
    }
    ```
    ---
    
    > [!NOTE]
    > 应用程序配置中间件监视 Sentinel 键或你在上一步骤的 `ConfigureRefresh` 调用中注册以进行刷新的其他任何键。 每次向应用程序传入请求时，都会触发中间件。 但是，当设置的缓存过期时间已过去，中间件只会发送请求以检查应用程序配置中的值。 检测到更改时，如果使用 Sentinel 键，它将更新所有配置，或仅更新已注册键的值。
    > - 如果向应用程序配置的更改检测请求失败，则应用程序将继续使用缓存的配置。 当配置的缓存过期时间再次过去，并且有新的请求传入应用程序时，将进行另一次检查。
    > - 配置刷新与应用程序传入请求的处理异步进行。 它不会阻止或减缓触发刷新的传入请求。 触发刷新的请求可能无法获取更新的配置值，但后续请求会获得。
    > - 若要确保中间件被触发，请在请求管道中尽早调用 `app.UseAzureAppConfiguration()`，使其他中间件不会在应用程序中将其短路。

## <a name="use-the-latest-configuration-data"></a>使用最新的配置数据

1. 打开 Controllers 目录中的 *HomeController.cs*，并添加对 `Microsoft.Extensions.Options` 包的引用。

    ```csharp
    using Microsoft.Extensions.Options;
    ```

2. 更新 `HomeController` 类，通过依赖项注入接收 `Settings` 并利用其值。

    #### <a name="net-5x"></a>[.NET 5.x](#tab/core5x)

    ```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        private readonly ILogger<HomeController> _logger;

        public HomeController(ILogger<HomeController> logger, IOptionsSnapshot<Settings> settings)
        {
            _logger = logger;
            _settings = settings.Value;
        }

        public IActionResult Index()
        {
            ViewData["BackgroundColor"] = _settings.BackgroundColor;
            ViewData["FontSize"] = _settings.FontSize;
            ViewData["FontColor"] = _settings.FontColor;
            ViewData["Message"] = _settings.Message;

            return View();
        }

        // ...
    }
    ```
    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        private readonly ILogger<HomeController> _logger;

        public HomeController(ILogger<HomeController> logger, IOptionsSnapshot<Settings> settings)
        {
            _logger = logger;
            _settings = settings.Value;
        }

        public IActionResult Index()
        {
            ViewData["BackgroundColor"] = _settings.BackgroundColor;
            ViewData["FontSize"] = _settings.FontSize;
            ViewData["FontColor"] = _settings.FontColor;
            ViewData["Message"] = _settings.Message;

            return View();
        }

        // ...
    }
    ```
    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        public HomeController(IOptionsSnapshot<Settings> settings)
        {
            _settings = settings.Value;
        }

        public IActionResult Index()
        {
            ViewData["BackgroundColor"] = _settings.BackgroundColor;
            ViewData["FontSize"] = _settings.FontSize;
            ViewData["FontColor"] = _settings.FontColor;
            ViewData["Message"] = _settings.Message;

            return View();
        }
    }
    ```
    ---
    > [!Tip]
    > 若要了解有关读取配置值时的选项模式的详细信息，请参阅 [ASP.NET Core 中的选项模式](/aspnet/core/fundamentals/configuration/options)。

3. 在“视图”>“主页”目录中打开 *Index.cshtml*，并将其内容替换为以下脚本：

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <style>
        body {
            background-color: @ViewData["BackgroundColor"]
        }
        h1 {
            color: @ViewData["FontColor"];
            font-size: @ViewData["FontSize"]px;
        }
    </style>
    <head>
        <title>Index View</title>
    </head>
    <body>
        <h1>@ViewData["Message"]</h1>
    </body>
    </html>
    ```

## <a name="build-and-run-the-app-locally"></a>在本地生成并运行应用

1. 要通过使用 .NET Core CLI 生成应用，请在命令行界面中执行以下命令：

    ```console
        dotnet build
    ```

1. 生成成功完成后，请运行以下命令以在本地运行 Web 应用：

    ```console
        dotnet run
    ```

1. 打开浏览器窗口，访问 `dotnet run` 输出中显示的 URL。

    ![在本地启动快速入门应用](./media/quickstarts/aspnet-core-app-launch-local-before.png)

1. 登录 [Azure 门户](https://portal.azure.com)。 选择“所有资源”，然后选择在快速入门中创建的应用程序配置存储区实例  。

1. 选择“配置资源管理器”并更新以下键的值。 最后记得更新 Sentinel 键。

    | 密钥 | 值 |
    |---|---|
    | TestApp:Settings:BackgroundColor | green |
    | TestApp:Settings:FontColor | lightGray |
    | TestApp:Settings:Message | Azure 应用配置中的数据 - 现可实时更新！ |
    | TestApp:Settings:Sentinel | 2 |

1. 刷新浏览器页面，查看新的配置设置。 可能需要多次刷新，系统才能反映所做的更改，也可以将缓存过期时间更改为小于 5 分钟。 

    ![在本地启动更新的快速入门应用](./media/quickstarts/aspnet-core-app-launch-local-after.png)

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>后续步骤

在本教程中，你使得 ASP.NET Core Web 应用能够从应用程序配置中动态刷新配置设置。 若要了解如何使用 Azure 托管标识来简化对应用程序配置的访问，请继续学习下一篇教程。

> [!div class="nextstepaction"]
> [托管标识集成](./howto-integrate-azure-managed-service-identity.md)
