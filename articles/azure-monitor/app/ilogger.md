---
title: 使用 .NET 进行 Application Insights 日志记录
description: 了解如何使用 .NET 中的 ILogger 接口使用 Application Insights。
ms.topic: conceptual
ms.date: 05/20/2021
ms.openlocfilehash: 274fa5a9ab3fef94f892ed75fd69bc6cca6cccc9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128617785"
---
# <a name="application-insights-logging-with-net"></a>使用 .NET 进行 Application Insights 日志记录

本文介绍如何使用多个 NuGet 包在 .NET 应用中通过 Application Insights 捕获日志：

- **核心程序包：**
  - [`Microsoft.Extensions.Logging.ApplicationInsights`][nuget-ai]
- **工作负载包：**
  - [`Microsoft.ApplicationInsights.AspNetCore`][nuget-ai-anc]
  - [`Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel`][nuget-ai-ws-tc]

[nuget-ai]: https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights
[nuget-ai-anc]: https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore
[nuget-ai-ws]: https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService
[nuget-ai-ws-tc]: https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel

> [!TIP]
> 本文不介绍 [`Microsoft.ApplicationInsights.WorkerService`][nuget-ai-ws]NuGet 包。 它可用于为后台服务启用 Application Insights。 有关详细信息，请参阅 [Application Insights for Worker Service 应用](./worker-service.md)。

可以通过多种方式注册 `ApplicationInsightsLoggerProvider`，具体取决于你使用的 Application Insights 日志记录包。 `ApplicationInsightsLoggerProvider` 是 <xref:Microsoft.Extensions.Logging.ILoggerProvider> 的实现，负责提供 <xref:Microsoft.Extensions.Logging.ILogger> 和 <xref:Microsoft.Extensions.Logging.ILogger%601> 实现。

## <a name="aspnet-core-applications"></a>ASP.NET Core 应用程序

若要将 Application Insights 遥测添加到 ASP.NET Core 应用程序，请使用 NuGet `Microsoft.ApplicationInsights.AspNetCore` 包。 可以通过[用作连接服务的 Visual Studio](/visualstudio/azure/azure-app-insights-add-connected-service) 完成此配置，或者手动进行配置。

默认情况下，如果通过[代码](./asp-net-core.md)或[无代码](./azure-web-apps-net-core.md#enable-agent-based-monitoring)方法配置 ASP.NET Core 应用程序，则会为其注册 Application Insights 日志记录提供程序。 注册的提供程序配置为自动捕获严重级别为 <xref:Microsoft.Extensions.Logging.LogLevel.Warning?displayProperty=nameWithType> 或更高的日志事件。 可以自定义严重性和类别。 有关详细信息，请参阅[日志记录级别](#logging-level)。

1. 确保已安装 NuGet 包：

   ```xml
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.17.0" />
    </ItemGroup>
   ```

1. 确保 `Startup.ConfigureServices` 方法调用 `services.AddApplicationInsightsTelemetry`：

    ```csharp
    using Microsoft.AspNetCore.Builder;
    using Microsoft.AspNetCore.Hosting;
    using Microsoft.AspNetCore.Http;
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Hosting;
    using Microsoft.Extensions.Configuration;
    
    namespace WebApplication
    {
        public class Startup
        {
            public Startup(IConfiguration configuration)
            {
                Configuration = configuration;
            }

            public IConfiguration Configuration { get; }

            public void ConfigureServices(IServiceCollection services)
            {
                services.AddApplicationInsightsTelemetry();
                // Configure the Connection String/Instrumentation key in appsettings.json
            }

            public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
            {
                // omitted for brevity
            }
        }
    }
    ```

安装 NuGet 包，并且使用依赖项注入注册提供程序后，应用即可进行记录。 使用构造函数注入时，需要 <xref:Microsoft.Extensions.Logging.ILogger> 或泛型替代接口 <xref:Microsoft.Extensions.Logging.ILogger%601>。 解析这些实现后，`ApplicationInsightsLoggerProvider` 将提供它们。 记录的消息或异常将发送到 Application Insights。 

考虑以下示例控制器：

```csharp
public class ValuesController : ControllerBase
{
    private readonly ILogger _logger;

    public ValuesController(ILogger<ValuesController> logger)
    {
        _logger = logger;
    }

    [HttpGet]
    public ActionResult<IEnumerable<string>> Get()
    {
        _logger.LogWarning("An example of a Warning trace..");
        _logger.LogError("An example of an Error level message");

        return new string[] { "value1", "value2" };
    }
}
```

有关详细信息，请参阅 [Logging in ASP.NET Core](/aspnet/core/fundamentals/logging)（ASP.NET Core 中的日志记录）。

### <a name="capture-logs-within-aspnet-core-startup-code"></a>在 ASP.NET Core 启动代码中捕获日志

某些方案要求在执行应用启动例程的过程中捕获日志，然后请求-响应管道即可接受请求。 但是，无法从“Program.cs”和“Startup.cs”中的依赖项注入中轻松获得 `ILogger` 实现。 有关详细信息，请参阅 [.NET 中的日志记录：在 Main 中创建日志](/dotnet/core/extensions/logging?tabs=command-line#create-logs-in-main)。

从 Program.cs 和 Startup.cs 记录日志时有几个限制 ：

* 遥测数据是通过 [InMemoryChannel](./telemetry-channels.md) 遥测通道发送的。
* 没有[采样](./sampling.md)应用于遥测。
* 标准[遥测初始化表达式或处理器](./api-filtering-sampling.md)不可用。

以下示例通过显式实例化并配置 Program.cs 和 Startup.cs 演示了此问题 。

#### <a name="example-programcs"></a>示例 Program.cs

```csharp
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;
using Microsoft.Extensions.Logging.ApplicationInsights;

namespace WebApplication
{
    public class Program
    {
        public static void Main(string[] args)
        {
            var host = CreateHostBuilder(args).Build();

            var logger = host.Services.GetRequiredService<ILogger<Program>>();
            logger.LogInformation("From Program, running the host now.");

            host.Run();
        }

        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                })
                .ConfigureLogging((context, builder) =>
                {
                    // Providing an instrumentation key is required if you're using the
                    // standalone Microsoft.Extensions.Logging.ApplicationInsights package,
                    // or when you need to capture logs during application startup, such as
                    // in Program.cs or Startup.cs itself.
                    builder.AddApplicationInsights(
                        context.Configuration["APPINSIGHTS_CONNECTIONSTRING"]);

                    // Capture all log-level entries from Program
                    builder.AddFilter<ApplicationInsightsLoggerProvider>(
                        typeof(Program).FullName, LogLevel.Trace);

                    // Capture all log-level entries from Startup
                    builder.AddFilter<ApplicationInsightsLoggerProvider>(
                        typeof(Startup).FullName, LogLevel.Trace);
                });
    }
}
```

在上面的代码中，为 `ApplicationInsightsLoggerProvider` 配置了 `"APPINSIGHTS_CONNECTIONSTRING"` 连接字符串。 应用了一个筛选器用于将日志级别设置为 <xref:Microsoft.Extensions.Logging.LogLevel.Trace?displayProperty=nameWithType>。

> [!IMPORTANT]
> 建议使用[连接字符串](./sdk-connection-string.md?tabs=net)替代检测密钥。 新的 Azure 区域要求使用连接字符串而不是检测密钥。 
>
> 连接字符串标识要与遥测数据关联的资源。 它还允许你修改可供你的资源将其用作遥测目标的终结点。 你需要复制连接字符串，并将其添加到应用程序的代码或环境变量中。

#### <a name="example-startupcs"></a>示例 Startup.cs

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;

namespace WebApplication
{
    public class Startup
    {
        public Startup(IConfiguration configuration)
        {
            Configuration = configuration;
        }

        public IConfiguration Configuration { get; }

        public void ConfigureServices(IServiceCollection services)
        {
            services.AddApplicationInsightsTelemetry();
            // Configure the Connection String/Instrumentation key in appsettings.json
        }

        // The ILogger<Startup> is resolved by dependency injection
        // and available in Startup.Configure.
        public void Configure(
            IApplicationBuilder app, IWebHostEnvironment env, ILogger<Startup> logger)
        {
            logger.LogInformation(
                "Configuring for {Environment} environment",
                env.EnvironmentName);

            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }

            app.UseRouting();
            app.UseEndpoints(endpoints =>
            {
                endpoints.MapGet("/", async context =>
                {
                    await context.Response.WriteAsync("Hello World!");
                });
            });
        }
    }
}
```

## <a name="console-application"></a>控制台应用程序

安装的包如下：

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="5.0.0" />
  <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.17.0"/>
</ItemGroup>
```

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;
using Microsoft.Extensions.Logging.ApplicationInsights;
using System;
using System.Threading.Tasks;

namespace ConsoleApp
{
    class Program
    {
        static async Task Main(string[] args)
        {
            using var channel = new InMemoryChannel();

            try
            {
                IServiceCollection services = new ServiceCollection();
                services.Configure<TelemetryConfiguration>(config => config.TelemetryChannel = channel);
                services.AddLogging(builder =>
                {
                    // Only Application Insights is registered as a logger provider
                    builder.AddApplicationInsights("<YourInstrumentationKey>");
                });

                IServiceProvider serviceProvider = services.BuildServiceProvider();
                ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

                logger.LogInformation("Logger is working...");
            }
            finally
            {
                // Explicitly call Flush() followed by Delay, as required in console apps.
                // This ensures that even if the application terminates, telemetry is sent to the back end.
                channel.Flush();

                await Task.Delay(TimeSpan.FromMilliseconds(1000));
            }
        }
    }
}

```

前面的示例使用的是 `Microsoft.Extensions.Logging.ApplicationInsights` 包。 默认情况下，此配置使用“绝对最小值”`TelemetryConfiguration` 设置将数据发送到 Application Insights：`InMemoryChannel` 通道。 没有采样，也没有标准的 `TelemetryInitializer` 实例。 可为控制台应用程序重写此行为，如以下示例所示。

安装此附加包：

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.17.0" />
```

以下部分介绍如何使用 <xref:Microsoft.Extensions.Options.ConfigureOptions%601.Configure(%600)> 方法替代默认的 `TelemetryConfiguration` 设置。 此示例设置 `ServerTelemetryChannel` 和采样。 它将自定义 `TelemetryInitializer` 实例添加到 `TelemetryConfiguration`。

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;
using Microsoft.Extensions.Logging.ApplicationInsights;
using System;
using System.Threading.Tasks;

namespace ConsoleApp
{
    class Program
    {
        static async Task Main(string[] args)
        {
            using var channel = new ServerTelemetryChannel();

            try
            {
                IServiceCollection services = new ServiceCollection();
                services.Configure<TelemetryConfiguration>(
                    config =>
                    {
                        config.TelemetryChannel = channel;

                        // Optional: implement your own TelemetryInitializer instance and configure it here
                        // config.TelemetryInitializers.Add(new MyTelemetryInitializer());

                        config.DefaultTelemetrySink.TelemetryProcessorChainBuilder.UseSampling(5);
                        channel.Initialize(config);
                    });

                services.AddLogging(builder =>
                {
                    // Only Application Insights is registered as a logger provider
                    builder.AddApplicationInsights("<YourInstrumentationKey>");
                });

                IServiceProvider serviceProvider = services.BuildServiceProvider();
                ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

                logger.LogInformation("Logger is working...");
            }
            finally
            {
                // Explicitly call Flush() followed by Delay, as required in console apps.
                // This ensures that even if the application terminates, telemetry is sent to the back end.
                channel.Flush();

                await Task.Delay(TimeSpan.FromMilliseconds(1000));
            }
        }
    }
}
```

## <a name="logging-level"></a>日志记录级别

`ILogger` 实现具有内置机制，可应用[日志筛选](/dotnet/core/extensions/logging#how-filtering-rules-are-applied)。 通过此筛选可以控制发送到每个已注册的提供程序（包括 Application Insights 提供程序）的日志。 可以在配置中（例如，使用 appsettings.json 文件）或者在代码中使用此筛选。

以下示例说明如何对 `ApplicationInsightsLoggerProvider` 应用筛选规则。

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>使用 appsettings.json 在配置中创建筛选规则

`ApplicationInsightsLoggerProvider` 的别名为“ApplicationInsights”。 以下 appsettings.json 节替代 Application Insights 的默认 <xref:Microsoft.Extensions.Logging.LogLevel.Warning?displayProperty=nameWithType> 日志级别，以便记录以“Microsoft”开头的、级别为 <xref:Microsoft.Extensions.Logging.LogLevel.Error?displayProperty=nameWithType> 和更高级别的类别。

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    },
    "ApplicationInsights": {
      "LogLevel": {
        "Microsoft": "Error"
      }
    }
  }
}
```

### <a name="create-filter-rules-in-code"></a>在代码中创建筛选规则

以下代码段为这些项配置要发送到 `ApplicationInsightsLoggerProvider` 的日志：

- 所有类别中的 <xref:Microsoft.Extensions.Logging.LogLevel.Warning?displayProperty=nameWithType> 及更高级别
- 以“Microsoft”开头的类别中的 <xref:Microsoft.Extensions.Logging.LogLevel.Error?displayProperty=nameWithType> 及更高级别

```csharp
Host.CreateDefaultBuilder(args)
    .UseStartup<Startup>()
    .ConfigureLogging(builder =>
    {
        builder.AddFilter<ApplicationInsightsLoggerProvider>("", LogLevel.Warning);
        builder.AddFilter<ApplicationInsightsLoggerProvider>("Microsoft", LogLevel.Error);
    });
```


## <a name="logging-scopes"></a>日志记录范围

`ApplicationInsightsLoggingProvider` 支持[日志范围](/dotnet/core/extensions/logging#log-scopes)。 默认启用范围。 

如果范围的类型为 `IReadOnlyCollection<KeyValuePair<string,object>>`，则集合中的每个键值对都将作为自定义属性添加到 Application Insights 遥测数据中。 在以下示例中，日志将作为 `TraceTelemetry` 捕获，其属性中将包含 `("MyKey", "MyValue")`。

```csharp
using (_logger.BeginScope(new Dictionary<string, object> { ["MyKey"] = "MyValue" }))
{
    _logger.LogError("An example of an Error level message");
}
```

如果将任何其他类型用作范围，则它将存储在 Application Insights 遥测中的属性 `Scope` 下。 在下面的示例中，`TraceTelemetry` 将具有名为 `Scope` 的属性，其中包含范围。

```csharp
    using (_logger.BeginScope("hello scope"))
    {
        _logger.LogError("An example of an Error level message");
    }
```

## <a name="frequently-asked-questions"></a>常见问题

### <a name="what-are-the-old-and-new-versions-of-applicationinsightsloggerprovider"></a>ApplicationInsightsLoggerProvider 的旧版本和新版本是什么？

[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 包含一个内置的 `ApplicationInsightsLoggerProvider` (`Microsoft.ApplicationInsights.AspNetCore.Logging.ApplicationInsightsLoggerProvider`) 实例，该实例通过 `ILoggerFactory` 扩展方法启用。 从版本 2.7.1 开始，此提供程序已标记为过时。 计划在下一次主要版本更改中彻底删除此提供程序。 

[Microsoft.ApplicationInsights.AspNetCore 2.6.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 包本身尚未过时。 对请求和依赖项等项启用监视需要用到它。

建议的替代方法是安装新的独立包 [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights)，其中包含改进的 `ApplicationInsightsLoggerProvider` 实例 (`Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider`)，以及 `ILoggerBuilder` 中用于启用该提供程序的扩展方法。

[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 版本 2.7.1 依赖于新包，可自动启用 `ILogger` 捕获。

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>为何某些 ILogger 日志在 Application Insights 中会显示两次？

如果通过对 `ILoggerFactory` 调用 `AddApplicationInsights` 来启用旧版（现在过时）`ApplicationInsightsLoggerProvider`，则可能会出现重复项。 检查 `Configure` 方法是否包含以下代码，如有则将其删除：

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

如果在 Visual Studio 中调试时看到重复的日志，请在启用 Application Insights 的代码中将 `EnableDebugLogger` 设置为 `false`，如下所示。 这种重复现象和修复方法仅与调试应用程序时的情况相关。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var options = new ApplicationInsightsServiceOptions
    {
        EnableDebugLogger = false
    }
    services.AddApplicationInsightsTelemetry(options);
    // ...
}
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdk-version-271-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>我已更新到 Microsoft.ApplicationInsights.AspNet SDK 版本 2.7.1，现在可以自动捕获 ILogger 中的日志。 如何完全禁用此功能？

请参阅[日志记录级别](#logging-level)部分有关如何筛选日志的一般信息。 若要关闭 `ApplicationInsightsLoggerProvider`，请在调用中使用 `LogLevel.None` 来配置日志记录。 在以下命令中，`builder` 为 <xref:Microsoft.Extensions.Logging.ILoggingBuilder>。

```csharp
builder.AddFilter<ApplicationInsightsLoggerProvider>("", LogLevel.None);
```

以下是 appsettings.json 文件中的更改：

```json
{
    "Logging": {
        "ApplicationInsights": {
            "LogLevel": {
                "Default": "None"
            }
        }
    }
}
```

### <a name="why-do-some-ilogger-logs-not-have-the-same-properties-as-others"></a>为何某些 ILogger 日志包含的属性与其他日志不同？

Application Insights 使用对其他每项遥测功能所用的相同 `TelemetryConfiguration` 来捕获和发送 `ILogger` 日志。 但存在一种例外情况。 默认情况下，在从 Program.cs 或 Startup.cs 记录日志时，不会完全设置 `TelemetryConfiguration` 。 来自这些位置的日志不采用默认配置，因此它们不会运行所有的 `TelemetryInitializer` 和 `TelemetryProcessor` 实例。

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>我正在使用独立包 Microsoft.Extensions.Logging.ApplicationInsights，并想要手动记录一些附加的自定义遥测数据。 如何做到这一点？

使用独立包时，`TelemetryClient` 不会注入到依赖项注入 (DI) 容器。 你需要创建 `TelemetryClient` 的新实例，并使用记录器提供程序所用的相同配置，如以下代码所示。 这可以确保对所有自定义遥测数据以及来自 `ILogger` 的遥测数据使用相同的配置。

```csharp
public class MyController : ApiController
{
   // This TelemetryClient instance can be used to track additional telemetry through the TrackXXX() API.
   private readonly TelemetryClient _telemetryClient;
   private readonly ILogger _logger;

   public MyController(IOptions<TelemetryConfiguration> options, ILogger<MyController> logger)
   {
        _telemetryClient = new TelemetryClient(options.Value);
        _logger = logger;
   }  
}
```

> [!NOTE]
> 如果使用 `Microsoft.ApplicationInsights.AspNetCore` 包启用 Application Insights，请修改此代码，以便在构造函数中直接获取 `TelemetryClient`。 有关示例，请参阅[此常见问题解答](./asp-net-core.md#frequently-asked-questions)。

### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-where-can-i-see-ilogger-logs-in-application-insights"></a>将从 ILogger 日志生成哪种类型的 Application Insights 遥测数据？ 可以在何处查看 Application Insights 中的 ILogger 日志？

`ApplicationInsightsLoggerProvider` 捕获 `ILogger` 日志并从中创建 `TraceTelemetry`。 如果将 `Exception` 对象传递给 `ILogger` 上的 `Log` 方法，则会创建 `ExceptionTelemetry` 而不是 `TraceTelemetry`。 

在显示 Application Insights 的任何其他 `TraceTelemetry` 或 `ExceptionTelemetry` 项的位置（包括 Azure 门户、Analytics 或 Visual Studio 本地调试器），都可以找到这些遥测项。

若要始终发送 `TraceTelemetry`，请使用此代码片段：

```csharp
builder.AddApplicationInsights(
    options => options.TrackExceptionsAsExceptionTelemetry = false);
```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>我没有安装 SDK，而是使用 Azure Web 应用扩展来为 ASP.NET Core 应用程序启用 Application Insights。 如何使用新的提供程序？ 

Azure Web 应用中的 Application Insights 扩展使用新提供程序。 可以在应用程序的 *appsettings.json* 文件中修改筛选规则。

## <a name="next-steps"></a>后续步骤

* [.NET 中的日志记录](/dotnet/core/extensions/logging)
* [ASP.NET Core 中的日志记录](/aspnet/core/fundamentals/logging)
* [Application Insights 中的 .NET 跟踪日志](./asp-net-trace-logs.md)
